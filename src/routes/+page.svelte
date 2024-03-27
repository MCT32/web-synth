<script lang="ts">
    import { onMount } from 'svelte';
    import * as Tone from 'tone';

    import Frame from '$lib/components/Frame.svelte';
    import Oscillator from '$lib/components/Oscillator.svelte';


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
