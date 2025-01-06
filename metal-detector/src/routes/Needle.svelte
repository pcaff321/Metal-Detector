<script lang="ts">  
    const { needle_intensity = 0, withinImage = false } = $props();
    const intensity: number = $derived(Math.max(0, Math.min(needle_intensity, 100)))
  
    const needleRotation = $derived(intensity * 1.8 - 90); // Map intensity (0-100) to degrees (-90 to 90)

    const shakeContainer = $derived(intensity > 60);
    const shakeContainerExtreme = $derived(intensity > 90);
  </script>
  
  <style>
    .container {
      position: absolute;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      bottom: 0;
      z-index: 50;
    }

    .shakeContainer {
        animation: containerShake 0.3s infinite ease-in-out;
    }

    @keyframes containerShake {
        0% {
            transform: translateX(0);
        }
        25% {
            transform: translateX(-5px);
        }
        50% {
            transform: translateX(5px);
        }
        75% {
            transform: translateX(-5px);
        }
        100% {
            transform: translateX(0);
        }
    }

    .shakeContainerExtreme {
        animation: containerShakeExtreme 0.3s infinite ease-in-out;
    }

    @keyframes containerShakeExtreme {
        0% {
            transform: rotate(5deg) translateX(0);
        }
        25% {
            transform: translateX(-5px);
        }
        50% {
            transform: rotate(-5deg) translateX(15px);
        }
        75% {
            transform: translateX(-5px);
        }
        100% {
            transform: rotate(5deg) translateX(0);
        }
    }
  
    .gauge {
      position: relative;
      width: 200px;
      height: 100px;
      overflow: hidden;
      background: #fff;
      border-radius: 100px 100px 0 0;
      border: 2px solid #000000;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
      background-color: bisque;
    }

    .extremeGauge {
        background-color: crimson !important;
    }
  
    .needle {
      position: absolute;
      bottom: 0;
      left: 50%;
      width: 2px;
      height: 90px;
      background: rgb(233, 131, 131);
      transform-origin: bottom center;
      transform: rotate(-90deg);
      transition: transform 0.2s ease-in-out;
      border: 1px solid black;

      animation: shake 0.1s infinite ease-in-out;
    }

    .needle:after {
        left: auto;
        right: 0;
        border-radius: 0 0 0 10em;
    }

    @keyframes shake {
        0% {
            transform: rotate(calc(var(--rotation) - var(--shake))) translateX(0px);
        }
        50% {
            transform: rotate(calc(var(--rotation) + var(--shake))) translateX(0px);
        }
        100% {
            transform: rotate(calc(var(--rotation) - var(--shake))) translateX(0px);
        }
    }
  
    .ticks {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 180px;
      height: 90px;
    }
  
    .tick {
      position: absolute;
      width: 2px;
      height: 10px;
      background: #888;
      transform-origin: bottom center;
    }
  
    .labels {
      position: absolute;
      top: 65%;
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      justify-content: center;
      width: 160px;
      font-size: 12px;
      font-weight: 700;
    }
  
    .slider {
      margin-top: 20px;
      width: 80%;
      max-width: 400px;
    }
  </style>
  
  <div class={`container ${shakeContainer ? 'shakeContainer' : ''} ${shakeContainerExtreme ? 'shakeContainerExtreme' : ''}`}>
    <div class="gauge {withinImage ? 'extremeGauge' : '' }">
      <div
        class="needle"
        style="
            --rotation: {needleRotation}deg;
            --shake: {intensity / 20}deg;
        "
      >
    </div>
      <div class="labels">
        <span>CUTENESS {shakeContainerExtreme ? '(DANGER)' : '' }</span>
      </div>
    </div>
  </div>
  