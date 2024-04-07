<script lang="ts">
    import { createEventDispatcher } from "svelte";


    export let label: string;
    export let min: number = 0;
    export let max: number = 100;
    export let value: number = 50;
    export let step: any = 1;


    const dispatch = createEventDispatcher();

    function onChange() {
        dispatch('change');
        console.log("afsadf");
    }


    const notypecheck = (x:any)=>x;
</script>


<div class="container">
    <span class="label">{label}</span>
    <input class="slider" type="range" {...notypecheck({orient: "vertical"})} min={min} max={max} step={step} bind:value={value} on:input={onChange}>
</div>


<style>
    .container {
        display: flex;
        flex-flow: column;
        align-items: center;
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

    .slider {
        appearance: none;
        width: 6px;
        height: 200px;
        border-radius: 3px;
        background-color: #facc15;
    }

    .slider::-moz-range-thumb {
        width: 20px;
        height: 10px;
        border-radius: 3px;
        background-color: #a1a1aa;
    }
</style>
