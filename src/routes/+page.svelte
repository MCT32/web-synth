<script lang="ts">
    import { onMount } from 'svelte';
    import * as Tone from 'tone';

    import Frame from '$lib/components/Frame.svelte';
    import Oscillator from '$lib/components/Oscillator.svelte';
    import Knob from '$lib/components/Knob.svelte';


    let osc: Oscillator;

    function onMIDIMessage(event: Event) {
        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10010000) {
            osc.osc.triggerAttack(Tone.Frequency((event as MIDIMessageEvent).data[1], 'midi').toFrequency());
        }

        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10000000) {
            osc.osc.triggerRelease(Tone.Frequency((event as MIDIMessageEvent).data[1], 'midi').toFrequency());
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
            osc.osc?.triggerAttack(Tone.Frequency(keyboardToNote(ev.key)));
        }
    }

    function onKeyUp(ev: KeyboardEvent) {
        osc.osc?.triggerRelease(Tone.Frequency(keyboardToNote(ev.key)));
    }

    function start() {
        osc.start();

        osc.osc.toDestination();

        console.log(osc.osc.get())
    }
</script>


<button on:click={start}>play</button>

<Frame>
    <Oscillator bind:this={osc} />
</Frame>


<svelte:window on:keydown={onKeyDown} on:keyup={onKeyUp} />


<style>
    :global(body) {
        background-color: #27272a;
        min-height: 100%;
    }
</style>
