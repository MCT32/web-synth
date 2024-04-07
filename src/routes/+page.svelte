<script lang="ts">
    import { SvelteComponent, onMount } from 'svelte';
    import * as Tone from 'tone';

    import Frame from '$lib/components/Frame.svelte';
    import Oscillator from '$lib/components/Oscillator.svelte';
    import Divider from '$lib/components/Divider.svelte';
    import Envelope from '$lib/components/Envelope.svelte';
    import Filter from '$lib/components/Filter.svelte';
    import FilterEnvelope from '$lib/components/FilterEnvelope.svelte';


    let osc: Oscillator;
    let env: Envelope;
    let filter_env: FilterEnvelope;
    let filter: Filter;

    let currentKey = "";
    let keyboardOctave = 2;

    let ready = false;

    function onMIDIMessage(event: Event) {
        const midi_event = event as MIDIMessageEvent;

        if (midi_event.data == null) return;

        if ((midi_event.data[0] & 0b11110000) == 0b10010000) {
            play(Tone.Frequency(midi_event.data[1], 'midi').toNote());
        }

        if ((midi_event.data[0] & 0b11110000) == 0b10000000) {
            stop(Tone.Frequency(midi_event.data[1], 'midi').toNote());
        }
    }

    onMount(async () => {
        let midi: MIDIAccess;

        await navigator.requestMIDIAccess().then((midiAccess) => {
            midi = midiAccess;

            midi.inputs.forEach((entry) => {
                entry.onmidimessage = onMIDIMessage;
                console.log(entry.name);
            })
        });
    });

    function keyboardToNote(key: string) {
        const keys: {[index: string]: string} = {
            "a": "C",
            "w": "C#",
            "s": "D",
            "e": "D#",
            "d": "E",
            "f": "F",
            "t": "F#",
            "g": "G",
            "y": "G#",
            "h": "A",
            "u": "A#",
            "j": "B"
        }

        const note = keys[key];

        if (note != undefined) {
            return note + keyboardOctave;
        } else {
            return undefined;
        }
    }

    function onKeyDown(ev: KeyboardEvent) {
        if (!ev.repeat) {
            const note = keyboardToNote(ev.key);

            if (note) play(note);
        }

        if (ev.key == "x") {
            keyboardOctave++;
        }

        if (ev.key == "z") {
            keyboardOctave--;
        }
    }

    function onKeyUp(ev: KeyboardEvent) {
        const note = keyboardToNote(ev.key);

        if (note) stop(note);
    }

    function play(note: string) {
        currentKey = note;

        if (osc.osc && env.env && filter_env.env) {
            osc.osc.set({
                frequency: Tone.Frequency(note).toFrequency()
            });
            env.env.triggerAttack();
            filter_env.env.triggerAttack();
        }
    }

    function stop(note: string) {
        if (env.env && filter_env.env && currentKey == note) {
            env.env.triggerRelease();
            filter_env.env.triggerRelease();
        }
    }

    function start() {
        osc.start();
        env.start();
        filter_env.start();
        filter.start();

        if (!osc.osc || !env.env || !filter.filter || !filter.env_cv || !filter_env.env) return;

        osc.osc.connect(env.env);
        env.env.connect(filter.filter);
        filter_env.env.connect(filter.env_cv);
        filter.filter.toDestination();

        ready = true;
    }
</script>


<div class="container">
    {#if !ready}
        <button on:click={start}>PLAY</button>
    {/if}

    <Frame bind:ready={ready}>
        <Oscillator bind:this={osc} />
        <Divider />
        <Envelope bind:this={env} />
        <Divider />
        <Filter bind:this={filter} />
        <Divider />
        <FilterEnvelope bind:this={filter_env} />
    </Frame>
</div>


<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />


<style>
    :global(body) {
        background-color: #27272a;
        min-height: 100vh;
        margin: 0;
    }

    .container {
        width: fit-content;
        height: fit-content;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
    }

    button {
        z-index: 10;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);

        font-size: xx-large;
        font-weight: bold;
        color: black;
        border: 5px solid white;
        background-color: white;
        padding: 10px;

        transition: background-color 200ms;
    }

    button:hover {
        background-color: transparent;
        color: white;
    }
</style>
