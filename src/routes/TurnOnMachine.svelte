<script lang="ts">
  import Detector from "./Detector.svelte";

    let activated = $state(false);
</script>


<style>
    .container {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        width: 100vw;
        height: 100vh;
    }

    .turn-on-button {
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 16px;
        border-radius: 20px;
        height: 120px;
        width: 120px;
        background-color: rgb(244, 84, 84);
        border: 1px solid black;
        font-weight: 700;
        box-shadow: 10px 10px;
        transition: transform 1s;
    }

    .turn-on-button:hover {
        filter: brightness(85%);
    }

    .turn-on-button:active {
        filter: brightness(60%) !important;
        transform: scale(1.2);
    }
</style>

{#if activated}
    <Detector />
{:else}
    <div class="container">
        <!-- svelte-ignore a11y_click_events_have_key_events -->
        <!-- svelte-ignore a11y_no_static_element_interactions -->
        <div class="turn-on-button" onclick={() => activated = true}>ACTIVATE</div>
    </div>
{/if}