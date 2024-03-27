<script lang="ts">
    import { onMount } from 'svelte';
    import * as Tone from 'tone';


    let synth: Tone.Synth;

    const keys: Array<string> = [
        "C",
        "C#",
        "D",
        "D#",
        "E",
        "F",
        "F#",
        "G",
        "G#",
        "A",
        "A#",
        "B",
    ]

    function onMIDIMessage(event: Event) {
        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10010000) {
            const octave = Math.floor((event as MIDIMessageEvent).data[1] as number / 12) - 2;
            const key = keys[(event as MIDIMessageEvent).data[1] as number % 12];

            synth.triggerAttack(key + octave);
        }

        if (((event as MIDIMessageEvent).data[0] & 0b11110000) == 0b10000000) {
            synth.triggerRelease();
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
        const filter = new Tone.Filter(500, "lowpass").toDestination();
        synth = new Tone.Synth().connect(filter);
        
        synth.oscillator.type = 'square';
    }
</script>


<button on:click={start}>play</button>
