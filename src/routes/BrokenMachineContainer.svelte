<script lang="ts">
    import sevgiGobshite from '$lib/assets/sevgiGobshite.png'
    import Explosion from './Explosion.svelte';
    import drill from '$lib/assets/drill.mp3';
    import hammering from '$lib/assets/hammering-nail.mp3';
    import handGesture from '$lib/assets/hand_gesture.jpeg';

    let drillAudio: any;
    let hammeringAudio: any;

    let { repair } = $props();

    let repairing = $state(false);

    const onclick = (e: Event) => {
        
        if (drillAudio){
            repairing = true;
            hammeringAudio.play();
            drillAudio.play();
            drillAudio.addEventListener('ended', () => {
                repair(e);
                repairing = false;
            }, { once: true });
        } else {
            repair(e);
        }
    };

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
        padding: 16px;
        text-align: center;
    }

    .hand-gesture {
        width: 300px
    }

    @media only screen and (max-width: 600px) {
        .hand-gesture {
            width: 80%;
        }
    }

    .sevgiGobshite {
        width: 35%;
    }

    @media only screen and (max-width: 600px) {
        .sevgiGobshite {
            width: 80%;
        }
    }

    .heart {
        display: flex;
        padding: 20px;
        justify-content: center;
        align-items: center;
        position: absolute;
        right: 20px;
        bottom: 40px;
        background-color: red;
        aspect-ratio: 1 / 1;
        height: 120px;
        width: 120px;
        -webkit-mask-image: url('data:image/svg+xml;charset=UTF-8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path fill="currentColor" d="M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z"></path></svg>');
                mask-image: url('data:image/svg+xml;charset=UTF-8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path fill="currentColor" d="M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z"></path></svg>');
        -webkit-mask-repeat: no-repeat;
                mask-repeat: no-repeat;
        -webkit-mask-position: bottom;
                mask-position: bottom;
    }

    .heart:hover {
        filter: brightness(85%);
        animation: 3s ease 0s infinite beat;
    }

    .heart:active {
        filter: brightness(60%) !important;
    }

    @keyframes beat {
        0%, 50%, 100% { transform: scale(1, 1); }
        30%, 80% { transform: scale(0.92, 0.95); }
    }

    .repair-text {
        color: #fff;
        font-weight: 700;
        text-align: center;
        padding: 20px;
    }

    .cost {
        position: absolute;
        bottom: 20px;
        right: 20px;
        color: #fff;
        width: 120px;
        padding: 0 20px;
        height: 20px;
        text-align: center;
    }

    
</style>
  
<div class="container">
{#if !repairing}
    <Explosion />
    <h1 class="broken-machine-message">YOU BROKE THE FUCKING MACHINE</h1>
    <!-- svelte-ignore a11y_missing_attribute -->
    <img class="sevgiGobshite" src={sevgiGobshite} />
    <!-- svelte-ignore a11y_consider_explicit_label -->
    <!-- svelte-ignore a11y_click_events_have_key_events -->
    <!-- svelte-ignore a11y_no_static_element_interactions -->
    <div class="repair-button heart" {onclick}><span class="repair-text">REPAIR</span></div>
    <div class="cost">COST: 5 KISSES</div>
{:else}
<div class="broken-machine-message" style="font-size: 60px;">Cleaning up your mess...</div>
<img class="hand-gesture" src={handGesture} alt="hand" />
{/if}
</div>

<audio src={drill} preload="auto" bind:this={drillAudio}></audio>
<audio src={hammering} preload="auto" bind:this={hammeringAudio}></audio>