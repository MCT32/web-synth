<svelte:options accessors={true} />


<script lang="ts">
    import * as Tone from 'tone';

    import LargeKnob from "./LargeKnob.svelte";
    import Module from "./Module.svelte";
    import SmallKnob from "./SmallKnob.svelte";


    const types: BiquadFilterType[] = [
        "lowpass",
        "bandpass",
        "highpass"
    ]


    export let filter: Tone.Filter | undefined = undefined;
    let detune_adder: Tone.Add;
    export let env_cv: Tone.Scale | undefined = undefined;

    let detune: number;
    let type: number;
    let env_amt: number;

    function onChangeDetune() {
        detune_adder.addend.set({
            value: detune
        })
    }

    function onChangeType() {
        if (!filter) return;

        filter.set({
            type: types[type]
        })
    }

    function onChangeEnvAmt() {
        if (!env_cv) return;

        env_cv.set({
            max: env_amt
        })
    }


    export function start() {
        filter = new Tone.Filter(Tone.Frequency("C4").toFrequency(), "lowpass");
        detune_adder = new Tone.Add().connect(filter.detune);
        env_cv = new Tone.Scale(0, 2400).connect(detune_adder);
        detune_adder.addend.set({
            value: detune
        })
    }
</script>


<Module name="Filter">
    <LargeKnob label="Cutoff" min={-4800} max={4800} defaultValue={2400} bind:value={detune} on:change={onChangeDetune} />
    <SmallKnob label="Type" min={0} max={2} defaultValue={0} round={true} bind:value={type} on:change={onChangeType} />
    <SmallKnob label="Env Amt" min={0} max={4800} defaultValue={2400} bind:value={env_amt} on:change={onChangeEnvAmt} />
</Module>
