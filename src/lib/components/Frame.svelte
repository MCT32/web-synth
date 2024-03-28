<div class="frame">
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
        position: absolute;
        margin: auto;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
    }
</style>
