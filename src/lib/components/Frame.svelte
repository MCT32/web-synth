<script lang="ts">
    export let ready: boolean;
</script>


<div class="frame" class:hide={!ready}>
    <slot />
</div>


<style>
    .frame {
        display: flex;
        align-items: stretch;
        flex-flow: row;
        gap: 10px;
        width: fit-content;
        height: fit-content;
        background-color: #1c1917;
        padding: 10px;
        border-radius: 15px;
        border: 4px solid #facc15;
        box-shadow: 0px 0px 20px #1c1917;

        transition: filter 500ms;
    }

    .hide {
        filter: brightness(0.2);
    }
</style>
