<svelte:options accessors={true} />


<script lang="ts">
    import * as Tone from 'tone';

    import LargeKnob from "./LargeKnob.svelte";
    import Module from "./Module.svelte";
    import SmallKnob from "./SmallKnob.svelte";


    const forms: Array<string> = [
        "sine",
        "triangle",
        "sawtooth",
        "pulse",
    ]


    export let osc: Tone.OmniOscillator = undefined;

    let form: number;
    let pw: number;
    let detune: number;


    export function start() {
        osc = new Tone.OmniOscillator({
            type: "sawtooth"
        });

        osc.volume.value = -6;

        osc.start();
    }

    function onChangeForm() {
        osc.set({
            type: forms[form]
        });
    }

    function onChangePW() {
        osc.set({
            width: pw
        });
    }

    function onChangeDetune() {
        osc.set({
            detune: detune
        });
    }
</script>


<Module name="Oscillator">
    <LargeKnob label="Form" min={0} max={3} defaultValue={2} round={true} bind:value={form} on:change={onChangeForm} />
    <SmallKnob label="PW" min={-1} max={1} defaultValue={0} bind:value={pw} on:change={onChangePW} />
    <SmallKnob label="Detune" min={-1200} max={1200} defaultValue={0} bind:value={detune} on:change={onChangeDetune} />
</Module>