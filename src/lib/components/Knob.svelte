<script lang="ts">
    import { createEventDispatcher } from "svelte";


    export let defaultValue = 50;
    export let value = defaultValue;
    export let min = 0;
    export let max = 100;

    export let diameter: number = 100;

    export let rotRange = 2 * Math.PI * 0.75;
    export let rotStart = -Math.PI * 0.75;

    export let pixelRange = 200;


    const dispatch = createEventDispatcher();
    
    let startY: number, startValue: number;
    $: valueRange = max - min;
    $: rotation = rotStart + (value - min) / valueRange * rotRange;


	function clamp(num: number, min: number, max: number) {
		return Math.max(min, Math.min(num, max));
	}

    function pointerMove(event: PointerEvent) {
        const { clientY } = event;

        const valueDiff = valueRange * (clientY - startY) / pixelRange;
        value = clamp(startValue - valueDiff, min, max)

        dispatch('change');
    }

    function pointerUp() {
        window.removeEventListener("pointermove", pointerMove);
        window.removeEventListener("pointerup", pointerUp);
    }

    function pointerDown(event: PointerEvent) {
        event.preventDefault();

        const { clientY } = event;

        startY = clientY;
        startValue = value;
        window.addEventListener('pointermove', pointerMove);
        window.addEventListener('pointerup', pointerUp);
    }

    function dblClick() {
        value = defaultValue;

        dispatch('change');
    }
</script>


<!-- svelte-ignore a11y-no-static-element-interactions -->
<div class="large-knob" style="--rotation: {rotation}; --diameter: {diameter + 'px'}" on:pointerdown={pointerDown} on:dblclick={dblClick}>
    <div class="notch"></div>
</div>


<style>
    .large-knob {
        width: var(--diameter);
        height: var(--diameter);
        border-radius: 50%;
        background-color: #facc15;

        transform: rotate(calc(var(--rotation) * 1rad));
    }

    .notch {
        width: calc(var(--diameter) * 0.05);
        height: calc(var(--diameter) * 0.25);
        margin-left: auto;
        margin-right: auto;
        background-color: #a1a1aa;
    }
</style>
