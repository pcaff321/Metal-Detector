<script>
    import sevgiGobshite from '$lib/assets/sevgiGobshite.png'
    import Explosion from './Explosion.svelte';
</script>

<style>
    .container {
        display: flex;
        flex-direction: column;
        background-color: black;
        height: 100vh;
        width: 100vw;
        justify-content: center;
        align-items: center;
    }

    .broken-machine-message {
        color: white;
    }

    .sevgiGobshite {
        width: 35%;
    }
</style>
  
<div class="container">
    <Explosion />
    <h1 class="broken-machine-message">YOU BROKE THE FUCKING MACHINE</h1>
    <!-- svelte-ignore a11y_missing_attribute -->
    <img class="sevgiGobshite" src={sevgiGobshite} />
</div>