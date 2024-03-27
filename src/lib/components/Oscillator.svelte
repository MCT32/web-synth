<svelte:options accessors={true} />


<script lang="ts">
    import * as Tone from 'tone';

    import LargeKnob from "./LargeKnob.svelte";
    import Module from "./Module.svelte";
    import SmallKnob from "./SmallKnob.svelte";


    export let osc: Tone.PolySynth = undefined;
    let detune: number;


    export function start() {
        osc = new Tone.PolySynth();
    }

    function onChange() {
        osc.set({
            detune: detune
        });
    }
</script>


<Module name="Oscillator">
    <LargeKnob label="Freq" min={-1200} max={1200} defaultValue={0} bind:value={detune} on:change={onChange} />
    <SmallKnob label="Form" />
    <SmallKnob label="PW" />
</Module>