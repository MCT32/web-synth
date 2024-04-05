<svelte:options accessors={true} />


<script lang="ts">
    import * as Tone from 'tone';

    import LargeKnob from "./LargeKnob.svelte";
    import Module from "./Module.svelte";
    import SmallKnob from "./SmallKnob.svelte";


    const types = [
        "lowpass",
        "bandpass",
        "highpass"
    ]


    export let filter: Tone.Filter;
    let detune_adder: Tone.Add;
    export let env_cv: Tone.Scale;

    let detune: number;
    let type: number;

    function onChangeDetune() {
        detune_adder.addend.set({
            value: detune
        })
    }

    function onChangeType() {
        filter.set({
            type: types[type]
        })
    }


    export function start() {
        filter = new Tone.Filter(Tone.Frequency("C4").toFrequency(), "lowpass");
        detune_adder = new Tone.Add().connect(filter.detune);
        env_cv = new Tone.Scale(0, 4800).connect(detune_adder);
        detune_adder.addend.set({
            value: detune
        })
    }
</script>


<Module name="Filter">
    <LargeKnob label="Cutoff" min={-4800} max={4800} defaultValue={2400} bind:value={detune} on:change={onChangeDetune} />
    <SmallKnob label="Type" min={0} max={2} defaultValue={0} round={true} bind:value={type} on:change={onChangeType} />
</Module>


<style>
    .horizontal {
        display: flex;
        flex-flow: row;
        gap: 10px;
    }
</style>
