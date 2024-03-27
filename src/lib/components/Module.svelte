<script lang="ts">
    export let name: string;
</script>


<div class="container">
    <h2>{name}</h2>
    <slot />
</div>


<style>
    .container {
        display: flex;
        flex-flow: column;
        align-items: center;
        gap: 10px;
    }

    h2 {
        color: white;
        font-family: Arial, Helvetica, sans-serif;
        font-weight: bold;
        font-size: 32px;
        text-align: center;
        margin: 0px;
    }
</style>
