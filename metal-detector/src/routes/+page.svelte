<style>
    h1 {
        height: 20px;
    }

    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        height: 100vh;
        overflow: hidden;
        cursor: none;
    }

    .heart {
        background-color: red;
        position: absolute;
        height: 20px;
        min-height: 15px;
        width: 20px;
        min-width: 15px;
        opacity: 0.2;
        -webkit-mask-image: url('data:image/svg+xml;charset=UTF-8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path fill="currentColor" d="M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z"></path></svg>');
                mask-image: url('data:image/svg+xml;charset=UTF-8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path fill="currentColor" d="M462.3 62.6C407.5 15.9 326 24.3 275.7 76.2L256 96.5l-19.7-20.3C186.1 24.3 104.5 15.9 49.7 62.6c-62.8 53.6-66.1 149.8-9.9 207.9l193.5 199.8c12.5 12.9 32.8 12.9 45.3 0l193.5-199.8c56.3-58.1 53-154.3-9.8-207.9z"></path></svg>');
        -webkit-mask-repeat: no-repeat;
                mask-repeat: no-repeat;
        -webkit-mask-position: bottom;
                mask-position: bottom;
        animation: floating-heart 10s infinite cubic-bezier(0.5, 0.5, 0.5, 0.5);
        animation-direction: alternate;
        animation-timing-function: ease-in-out;

        --low-opacity: 0.1;
        --high-opacity: 0.5;
        --start-left: 50%;
        --start-top: 50%;
        --end-left: 100%;
        --end-top: 90%;
        --animation-duration-var: 10s;

        animation-duration: var(--animation-duration-var);
        }

    @keyframes floating-heart {
        0% {
            opacity: var(--low-opacity);
            left: var(--start-left);
            top: var(--start-top);
        }
        10% {
            opacity: var(--high-opacity);
        }
        50% {
            left: calc((var(--start-left) + var(--end-left)) / 2);
            top: calc((var(--start-top) + var(--end-top)) / 2);
        }
        90% {
            opacity: var(--low-opacity);
        }
        100% {
            opacity: var(--high-opacity);
            left: var(--end-left);
            top: var(--end-top);
        }
    }

    .image {
        position: absolute;
        width: 250px;
        height: 444px;
        background-color: red;
        border-radius: 10%;
        border: 2px solid black;
    }

    .shakeContainerExtreme {
        animation: containerShakeExtreme 0.3s infinite ease-in-out;
    }

    .detector {
        position: absolute;
        display: block;
        /* background: red; */
        /* border: 1px solid black; */
        /* box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19); */
        z-index: 100;
        /* border-radius: 100%; */
        /* height: 12px;
        width: 12px; */
        margin: 0;

        --rotation: 40deg;

        transform: rotate(var(--rotation));
    }

    @keyframes containerShakeExtreme {
        0% {
            transform: translateY(0) translateX(0);
        }
        25% {
            transform: translateY(15px) translateX(-15px);
        }
        50% {
            transform: translateY(-15px) translateX(15px);
        }
        75% {
            transform: translateY(0) translateX(-15px);
        }
        100% {
            transform: translateY(15px) translateX(0);
        }
    }
</style>

<script lang="ts">
    import explosion from '$lib/assets/explosion.mp3';
    import BrokenMachineContainer from './BrokenMachineContainer.svelte';
    import Needle from './Needle.svelte'
    import { onMount } from 'svelte';
    
    const getRandomInteger = (min: number, max: number) => {
        min = Math.ceil(min)
        max = Math.max(min, Math.floor(max))
        
        return Math.floor(Math.random() * (max - min)) + min
    }

    const distance = (x1: number, y1: number, x2: number, y2: number) => Math.hypot(x2 - x1, y2 - y1); 

    let explosionAudio: any;

    let innerWidth = $state(0)
    let innerHeight = $state(0)

    // Keep up to date with css
    const divWidth = $state(250);
    const divHeight = $state(444);

    const MIN_INTERVAL = 120;
    const MAX_INTERVAL = 1200;

    const divLeft = $derived(getRandomInteger(0, innerWidth - divWidth));
    const divTop = $derived(getRandomInteger(20, innerHeight - divHeight));

    let cursorTop = $state(-50);
    let cursorLeft = $state(-50);

    let beep_interval = $state(500);    

    const degree_of_closeness = $derived((beep_interval - MIN_INTERVAL) / (MAX_INTERVAL - MIN_INTERVAL))

    const MAX_FREQUENCY = 600;
    const MIN_FREQUENCY = 400;
    const FREQUENCY_RANGE_DIFFERENCE = MAX_FREQUENCY - MIN_FREQUENCY;

    function getFrequency() {
        return Math.min(MIN_FREQUENCY + (FREQUENCY_RANGE_DIFFERENCE * (1 - degree_of_closeness)), MAX_FREQUENCY);
    }

    $effect(() => {
        const hearts = document.getElementsByClassName('heart');

        for (let i = 0; i < hearts.length; i++) {
            const heart = hearts[i] as HTMLElement;

            const minusMultiplyer = (i % 2 == 0 ? -1 : 1);
            const startDifference = 150 * minusMultiplyer;
            const endDifference = 300 * minusMultiplyer;

            heart.style.setProperty('--animation-duration-var', `${Math.max(3, Math.random() * 10)}s`);
            heart.style.setProperty('--low-opacity', `${Math.random() / 2}`);
            heart.style.setProperty('--high-opacity', `${Math.random() / 2}`);
            heart.style.setProperty('--start-left', `${(Math.random() * innerWidth) - startDifference}px`);
            heart.style.setProperty('--start-top', `${(Math.random() * innerHeight) - startDifference}px`);
            heart.style.setProperty('--end-left', `${(Math.random() * innerWidth) + endDifference}px`);
            heart.style.setProperty('--end-top', `${(Math.random() * innerHeight) + endDifference}px`);
        };
    });

    const playBeep = () => {
        if (exploded){
            return;
        }
        const audioCtx = new AudioContext();
        const oscillator = audioCtx.createOscillator();
        const gainNode = audioCtx.createGain();

        oscillator.type = "sine";
        oscillator.frequency.setValueAtTime(getFrequency(), audioCtx.currentTime); // A4 note
        oscillator.connect(gainNode);
        gainNode.connect(audioCtx.destination);

        oscillator.connect(gainNode);
        gainNode.connect(audioCtx.destination);

        gainNode.gain.setValueAtTime(withinImage ?  1 : Math.max(0.6, Math.min(degree_of_closeness * 1.5, 0.9)), audioCtx.currentTime);

        oscillator.start();
        setTimeout(() => {oscillator.stop(); audioCtx.close()}, MIN_INTERVAL - 20);
    };

    let lastBeepTime = 0;
    let animationFrameId: number;

    let extremeBeepDuration = $state(0)
    let exploded = $state(false);

    const tick = $derived((currentTime: number) => {

    const timeSinceLastBeep = currentTime - lastBeepTime

    if (timeSinceLastBeep >= beep_interval) {
      playBeep();
      lastBeepTime = currentTime;
      if (withinImage){
        extremeBeepDuration += timeSinceLastBeep;
      } else {
        extremeBeepDuration = 0;
      }
    }

    animationFrameId = requestAnimationFrame(tick); // Keep the loop running
  });
  
  const EXTREME_BEEP_DURATION_THRESHOLD = 5000;

  $effect(() => {
    if (extremeBeepDuration >= EXTREME_BEEP_DURATION_THRESHOLD && !exploded){
        exploded = true;
        explosionAudio?.play()
        cancelAnimationFrame(animationFrameId);
    }
  })



    // $effect(() => {
    //     const id = setInterval(() => {
    //         if (audio !== undefined){ 
    //             // audio.play();
    //             // playBeep();
    //             // audio.currentTime = 0;
    //             // audio.play();
    //         }
    //     }, 100);

    //     return () => {
    //         clearInterval(id);
    //     };
    // });
    
    function setBeepInterval(interval: number){
        if (withinImage){
            beep_interval = MIN_INTERVAL;
        } else {
            beep_interval = Math.floor(Math.max(MIN_INTERVAL + 20, Math.min(MAX_INTERVAL, interval)))
        }
    }

    function isWithinImage(cursorLeft: number, cursorTop: number, divHeight: number, divWidth: number, divTop: number, divLeft: number){
        const leftDiff = cursorLeft - divLeft;
        const topDiff = cursorTop - divTop;
        return ((0 < leftDiff) && (leftDiff < divWidth) && (0 < topDiff) && (topDiff < divHeight));
    }

    const withinImage = $derived(isWithinImage(cursorLeft, cursorTop, divHeight, divWidth, divTop, divLeft))

    const calculateRotation = (mouseX: number, mouseY: number, targetX: number, targetY: number) => {
        const dx = mouseX - targetX;
        const dy = targetY - mouseY;
        const angleInRadians = Math.atan2(dy, dx); // Angle in radians
        return angleInRadians * (180 / Math.PI); // Convert to degrees
    };

    let distanceVal = $state(10000)

    function handleMousemove(e: MouseEvent) {
        const centerOfImageX = divLeft + divWidth / 2;
        const centerOfImageY = divTop + divHeight / 2;
        const clientX = e.clientX;
        const clientY = e.clientY
        distanceVal = distance(clientX, clientY, centerOfImageX, centerOfImageY)

        cursorLeft = clientX;
        cursorTop = clientY;

        const cursorElement = document.getElementsByClassName("detector")[0] as HTMLElement;

        if (cursorElement) {
            cursorElement.style.setProperty("--rotation", `${calculateRotation(clientY, clientX, centerOfImageY, centerOfImageX)}deg`)
        }



        setBeepInterval(distanceVal)
    }

    onMount(() => {
        lastBeepTime = performance.now();
        animationFrameId = requestAnimationFrame(tick);

        return () => {
        cancelAnimationFrame(animationFrameId);
        };
    });
</script>

<svelte:window bind:innerWidth bind:innerHeight />

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_mouse_events_have_key_events -->
<!-- svelte-ignore event_directive_deprecated -->
{#if exploded}
    <BrokenMachineContainer />
{:else}
<div class="container {withinImage ? 'shakeContainerExtreme' : ''}" on:mousemove|preventDefault={handleMousemove}>
    <h1>
        Cuteness Detector
    </h1>
    <Needle needle_intensity={(1 - degree_of_closeness) * 100} withinImage={withinImage} />
    {#if innerWidth > 0}
    {#await import(`$lib/assets/sevgi${getRandomInteger(1, 7)}.png`) then { default: src }}
    <!-- svelte-ignore a11y_img_redundant_alt -->
    <img class="image" style:top={`${divTop}px`} style:left={`${divLeft}px`} {src} alt="Image" />
    {/await}
    <!-- <div class="image" style:top={`${divTop}px`} style:left={`${divLeft}px`} style:height={`${divHeight}px`} style:width={`${divWidth}px`}>IMAGE</div> -->
    {/if}

    {#each { length: 169 }}
        <div class="heart"></div>
    {/each}

    <div class="detector {withinImage ? 'shakeContainerExtreme' : ''}" style:top={`${cursorTop}px`} style:left={`${cursorLeft - 20}px`} ><svg height="40" width="40" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" xml:space="preserve">
        <path d="M418.254 179.2c-9.387 0-18.773 3.413-25.6 8.533V179.2c0-23.893-18.773-42.667-42.667-42.667-9.387 0-18.773 3.413-25.6 8.533 0-23.893-18.773-42.667-42.667-42.667-9.387 0-18.773 3.413-25.6 8.533V42.667C256.121 18.773 237.347 0 213.454 0s-42.667 18.773-42.667 42.667v193.707c-18.773-32.427-62.293-48.64-95.573-36.693-4.267.853-7.68 3.413-11.947 5.973-12.8 8.533-11.947 19.627-11.947 23.04-.853 5.12 0 5.973 11.947 19.627 14.507 17.067 46.08 52.907 65.707 88.747 1.707 1.707 24.747 39.253 50.347 67.413v22.187c0 46.933 38.4 85.333 85.333 85.333h85.333c36.693 0 75.093-30.72 83.627-66.56 2.56-10.24 5.12-17.92 8.533-21.333 8.533-10.24 18.773-34.133 18.773-65.707V221.867c.001-23.894-18.773-42.667-42.666-42.667zm-67.413 315.733h-85.333c-37.547 0-68.267-30.72-68.267-68.267v-6.767c2.875 2.339 5.76 4.61 8.533 6.767 3.413 3.413 7.68 6.827 11.947 10.24 1.707.853 3.413 1.707 5.12 1.707 2.56 0 5.12-.853 6.827-3.413 3.413-3.413 2.56-8.533-.853-11.947-4.267-3.413-8.533-6.827-12.8-10.24-7.494-5.829-14.983-11.664-21.684-18.285-25.475-26.456-49.996-66.195-49.996-66.195-20.48-36.693-52.907-73.387-68.267-91.307-2.56-3.413-5.973-7.68-7.68-9.387 0-2.56 0-4.267 5.973-7.68 3.413-1.707 5.973-3.413 8.533-4.267 22.187-7.68 58.88 2.56 75.093 30.72l13.191 19.786c.109.523.26 1.041.462 1.547l8.533 17.067c1.707 3.413 10.24 5.12 11.093 3.413 3.413-1.707 5.12-6.827 3.413-11.093l-6.02-12.039c.028-.253.046-.507.046-.761V42.667c0-14.507 11.093-25.6 25.6-25.6s25.6 11.093 25.6 25.6V284.16c0 5.12 3.413 8.533 8.533 8.533s8.533-3.413 8.533-8.533V145.067c0-14.507 11.093-25.6 25.6-25.6s25.6 11.093 25.6 25.6v130.56c0 5.12 3.413 8.533 8.533 8.533s8.533-3.413 8.533-8.533V179.2c0-14.507 11.093-25.6 25.6-25.6 14.507 0 25.6 11.093 25.6 25.6V284.16c0 5.12 3.413 8.533 8.533 8.533s8.533-3.413 8.533-8.533v-62.293c0-14.507 11.093-25.6 25.6-25.6 14.507 0 25.6 11.093 25.6 25.6V358.4c0 15.106-2.777 27.428-6.161 36.621-1.015.932-1.826 2.12-2.372 3.485-4.267 12.8-16.213 17.067-37.547 23.04-4.267.853-6.827 5.973-5.973 10.24 1.707 3.413 5.12 5.973 8.533 5.973.853 0 1.707 0 1.707.853 5.648-1.521 11.128-3.098 16.289-4.954-.659 2.39-1.258 4.895-1.782 7.514-5.97 29.015-37.543 53.761-66.556 53.761z"/>
      </svg>
      </div>
</div>
{/if}

<audio src={explosion} preload="auto" bind:this={explosionAudio}></audio>

{#await import(`$lib/assets/sevgi${getRandomInteger(1, 7)}.png`) then { default: src }}
  <img {src} alt="Cutie Pie" />
{/await}