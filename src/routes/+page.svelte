<script lang="ts">
    import { onMount } from 'svelte';
    import * as Tone from 'tone';

    import Frame from '$lib/components/Frame.svelte';
    import Oscillator from '$lib/components/Oscillator.svelte';
    import Divider from '$lib/components/Divider.svelte';
    import Envelope from '$lib/components/Envelope.svelte';


    let osc: Oscillator;
    let env: Envelope;

    let currentKey = "";

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
            return note + "4";
        } else {
            return undefined;
        }
    }

    function onKeyDown(ev: KeyboardEvent) {
        if (!ev.repeat) {
            play(keyboardToNote(ev.key));
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
    }

    function stop(note: string) {
        if (currentKey == note) {
            env.env.triggerRelease();
        }
    }

    function start() {
        osc.start();
        env.start();

        osc.osc.connect(env.env);
        env.env.toDestination();

        console.log(osc.osc)
    }
</script>


<button on:click={start}>play</button>

<Frame>
    <Oscillator bind:this={osc} />
    <Divider />
    <Envelope bind:this={env} />
</Frame>


<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />


<style>
    :global(body) {
        background-color: #27272a;
        min-height: 100%;
    }
</style>