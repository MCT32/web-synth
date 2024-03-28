<script lang="ts">
    import Knob from "./Knob.svelte";
    import { createEventDispatcher } from "svelte";

    export let label: string;

    export let min: number = 0;
    export let max: number = 100;
    export let defaultValue: number = 50;
    export let value: number = defaultValue;
    export let round: boolean = false;


    const dispatch = createEventDispatcher();

    function onChange() {
        dispatch('change');
    }
</script>


<div class="container">
    <span class="label">{label}</span>

    <Knob min={min} max={max} defaultValue={defaultValue} bind:value={value} bind:round={round} on:change={onChange} diameter={50} />
</div>


<style>
    .container {
        width: fit-content;
    }

    .label {
        display: block;
        color: white;
        font-family: Arial, Helvetica, sans-serif;
        font-size: 16px;
        font-weight: bold;
        text-align: center;
        width: 100%;
    }
</style>
