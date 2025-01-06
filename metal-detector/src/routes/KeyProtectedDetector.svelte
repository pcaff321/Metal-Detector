<script lang="ts">
  import { onMount } from "svelte";
  import Detector from "./Detector.svelte";

    let keyGiven = $state(false);
    let typedKey = $state("");

    const KEY = "kinderbueno"

    onMount(() => typedKey = "")

    $effect(() => {
        if ((encodeURIComponent(typedKey.replace(/\s/g,'').toLowerCase())) === KEY) {
            keyGiven = true;
        }
    });
</script>

<style>
    .password-entry{
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        height: 100vh;
        overflow: hidden;
    }

    .hint-for-key {
        height: 40px;
        padding: 12px;
    }

    .incorrect-key {
        color: red;
        text-align: center;
        font-weight: 700;
    }
</style>

{#if keyGiven}
    <Detector />
{:else}
<div class="password-entry">
    <h1>Heeeelllllllloooooooooooooo please enter correct password:</h1>
    <input type="text" class="password-input" bind:value={typedKey} placeholder={"ENTER KEY"} />
    <div class="hint-for-key">
        {#if typedKey !== ""}
        <span class="incorrect-key">THAT IS FUCKING INCORRECT</span>
        {/if}
    </div>
</div>
{/if}
