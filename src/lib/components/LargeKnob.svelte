<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import Knob from "./Knob.svelte";

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

    <Knob min={min} max={max} defaultValue={defaultValue} bind:value={value} bind:round={round} on:change={onChange} />
</div>


<style>
    .container {
        width: fit-content;
        margin-left: 50px;
        margin-right: 50px;
    }

    .label {
        display: block;
        color: white;
        font-family: Arial, Helvetica, sans-serif;
        font-size: 24px;
        font-weight: bold;
        text-align: center;
        width: 100%;
    }
</style>
