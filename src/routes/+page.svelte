<script lang="ts">
    import { onMount } from 'svelte';
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
    let keyboardOctave = 4;

    function onMIDIMessage(event: Event) {
        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10010000) {
            play(Tone.Frequency((event as MIDIMessageEvent).data[1], 'midi').toNote());
        }

        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10000000) {
            stop(Tone.Frequency((event as MIDIMessageEvent).data[1], 'midi').toNote());
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
        const keys = {
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
            play(keyboardToNote(ev.key));
        }

        if (ev.key == "x") {
            keyboardOctave++;
        }

        if (ev.key == "z") {
            keyboardOctave--;
        }
    }

    function onKeyUp(ev: KeyboardEvent) {
        stop(keyboardToNote(ev.key));
    }

    function play(note: string) {
        currentKey = note;
        osc.osc.set({
            frequency: new Tone.Frequency(note)
        });
        env.env.triggerAttack();
        filter_env.env.triggerAttack();
    }

    function stop(note: string) {
        if (currentKey == note) {
            env.env.triggerRelease();
            filter_env.env.triggerRelease();
        }
    }

    function start() {
        osc.start();
        env.start();
        filter_env.start();
        filter.start();

        osc.osc.connect(env.env);
        env.env.connect(filter.filter);
        filter_env.env.connect(filter.env_cv);
        filter.filter.toDestination();

        console.log(osc.osc)
    }
</script>


<button on:click={start}>play</button>

<Frame>
    <Oscillator bind:this={osc} />
    <Divider />
    <Envelope bind:this={env} />
    <Divider />
    <Filter bind:this={filter} />
    <Divider />
    <FilterEnvelope bind:this={filter_env} />
</Frame>


<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />


<style>
    :global(body) {
        background-color: #27272a;
        min-height: 100%;
    }
</style>
