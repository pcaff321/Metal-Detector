<script lang="ts">
    import { onMount } from "svelte";
  
    // Number of particles in the explosion
    const PARTICLE_COUNT = 100;

    const randomColor = () => {
        const category = Math.floor(Math.random() * 3); // 0 = red, 1 = grey, 2 = yellow

        if (category === 0) {
            return `hsl(${Math.random() * 10}, 100%, ${Math.random() * 50 + 50}%)`; // Red
        } else if (category === 1) {
            const lightness = Math.random() * 50 + 30; // Grey
            return `hsl(0, 0%, ${lightness}%)`;
        } else {
            return `hsl(${Math.random() * 10 + 50}, 100%, ${Math.random() * 50 + 50}%)`; // Yellow
        }
    };
  
    // Array to store particle data
    let particles = Array.from({ length: PARTICLE_COUNT }, () => ({
      x: 0, // Starting x position (center)
      y: 0, // Starting y position (center)
      angle: Math.random() * Math.PI * 2, // Random direction
      speed: Math.random() * 5 + 2, // Random speed
      size: Math.random() * 4 + 2, // Random size
      color: randomColor(),
    }));
  
    // Update particles over time
    const updateParticles = () => {
        particles = particles.map((particle) => ({
            ...particle,
            x: particle.x + Math.cos(particle.angle) * particle.speed,
            y: particle.y + Math.sin(particle.angle) * particle.speed,
            speed: Math.max(particle.speed * 0.98, 0.5), // Minimum speed to ensure particles keep moving
        })).filter(particle => 
            // Keep particles until they are completely outside the viewport
            particle.x > -window.innerWidth && 
            particle.x < window.innerWidth && 
            particle.y > -window.innerHeight && 
            particle.y < window.innerHeight
        );
    };
  
    let animationFrameId: number;
  
    onMount(() => {
      const animate = () => {
        updateParticles();
        animationFrameId = requestAnimationFrame(animate);
      };
  
      animationFrameId = requestAnimationFrame(animate);
  
      return () => {
        cancelAnimationFrame(animationFrameId);
      };
    });
  </script>
  
  <style>
    .explosion {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
      overflow: hidden;
    }
  
    .particle {
      position: absolute;
      border-radius: 50%;
      will-change: transform;
    }
  </style>
  
  <div class="explosion">
    {#each particles as { x, y, size, color }}
      <div
        class="particle"
        style="
          background-color: {color};
          width: {size}px;
          height: {size}px;
          transform: translate(calc(50vw + {x}px), calc(50vh + {y}px));
        "
      ></div>
    {/each}
  </div>
  