<svelte:options accessors={true} />


<script lang="ts">
    import * as Tone from 'tone';

    import Module from "./Module.svelte";
    import Slider from "./Slider.svelte";
    import SmallKnob from "./SmallKnob.svelte";


    export let env: Tone.Envelope | undefined = undefined;


    let attack: number = 0.1;
    let decay: number = 0.2;
    let sustain: number = 1.0;
    let release: number = 0.8;

    function onChangeAttack() {
        if (!env) return;

        env.set({
            attack: attack
        })
    }

    function onChangeDecay() {
        if (!env) return;

        env.set({
            decay: decay
        })
    }

    function onChangeSustain() {
        if (!env) return;

        env.set({
            sustain: sustain
        })
    }

    function onChangeRelease() {
        if (!env) return;

        env.set({
            release: release
        })
    }


    export function start() {
        env = new Tone.Envelope({
            attack: 0.1,
            decay: 0.2,
            sustain: 1.0,
            release: 0.8,
            attackCurve: "linear",
            decayCurve: "linear",
            releaseCurve: "linear",
        });
    }
</script>


<Module name="Filter Env">
    <div class="container">
        <div class="knob-container">
            <SmallKnob label="Attack" min={0.0} max={1.0} defaultValue={0.1} bind:value={attack} on:change={onChangeAttack} />
            <SmallKnob label="Decay" min={0.0} max={1.0} defaultValue={0.2} bind:value={decay} on:change={onChangeDecay} />
            <SmallKnob label="Release" min={0.0} max={1.0} defaultValue={0.8} bind:value={release} on:change={onChangeRelease} />
        </div>

        <Slider label="Sustain" min={0.0} max={1.0} step="any" bind:value={sustain} on:change={onChangeSustain} />
    </div>
</Module>


<style>
    .container {
        display: flex;
        flex-flow: row;
        align-items: center;
        gap: 10px;
    }

    .knob-container {
        display: flex;
        flex-flow: column;
        align-items: center;
        gap: 10px;
    }
</style>
