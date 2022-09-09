<script>

  let emotions = {
    anger: {
      name: 'anger',
      color: [360, 57, 60],
      sibling: 'balance',
    },
    balance: {
      name: 'balance',
      color: [277, 48, 83],
      sibling: 'anger',
    },
    
    fear: {
      name: 'fear',
      color: [209, 65, 71],
      sibling: 'passion',
    },
    passion: {
      name: 'passion',
      color: [13, 100, 70],
      sibling: 'fear',
    },
    
    shame: {
      name: 'shame',
      color: [156, 42, 79],
      sibling: 'tranquility',
    },
    tranquility: {
      name: 'tranquility',
      color: [38, 61, 71],
      sibling: 'shame',
    },

    sadness: {
      name: 'sadness',
      color: [195, 61, 72],
      sibling: 'happiness',
    },
    happiness: {
      name: 'happiness',
      color: [40, 100, 61],
      sibling: 'sadness',
    },

    apathy: {
      name: 'apathy',
      color: [183, 90, 75],
      sibling: 'vitality',
    },
    vitality: {
      name: 'vitality',
      color: [48, 100, 73],
      sibling: 'apathy',
    }, 
  };

  let orbsBlur = 25;

  let stonesCount = 3;

  $: stones = [...Array(stonesCount)].map( (i, index) => {
    return {
      emotion: Object.keys(emotions)[index],
      primaryOrbCount: Math.round(Math.random() * (4 - 2) + 2),
      secondaryOrbCount: Math.round(Math.random() * (4 - 2) + 2),
    }
  });


  // ===========================
  // Functions

  function colorsArray(baseColor, count, min = 20, max = 90) {
    return [...Array(count)].map((_, index) => {
      const lightness = count == 1 ? 40 : (((max - min) / (count - 1)) * index) + min;
      return `hsl(${baseColor[0]}, ${baseColor[1]}%, ${Math.round(lightness)}%)`
    })
  }

  function randomizeLightness(hsl = primaryColor, rangeStart = 10, rangeEnd = 90) {
    let lightness = Math.random() * (rangeEnd - rangeStart) + rangeStart;
    return `hsl(${hsl[0]}, ${hsl[1]}%, ${Math.round(lightness)}%)`;
  }

  function randomOrb() {
    const size = Math.random() * (60 - 20) + 20;
    const x = Math.random() * (100 - size);
    const y = Math.random() * (100 - size);
    const duration = Math.random() * (12 - 6) + 6;
    const direction = Math.round(Math.random()) ? 'alternate' : 'alternate-reverse';
    return { size, x, y, duration, direction }
  }

</script>

<input type="range" bind:value={stonesCount} min="2" max="5" class="block my-10 mx-auto" />


<div class="canvas" style="
  --bgStart: {randomizeLightness(emotions[stones[stones.length - 1].emotion].color, 90, 95)};
  --bgEnd: {randomizeLightness(emotions[stones[stones.length - 1].emotion].color, 70, 90)};
">

  {#each stones as stone, index}
    {@const primaryOrbs = colorsArray(emotions[stone.emotion].color, stone.primaryOrbCount)}
    {@const secondaryOrbs = colorsArray(emotions[emotions[stone.emotion].sibling].color, stone.secondaryOrbCount, 60, 70)}

    <div class="stone-container"
      style="
        --blur: {orbsBlur}px;
        --baseColor: {`hsl(${emotions[stone.emotion].color[0]}, ${emotions[stone.emotion].color[1]}%, ${emotions[stone.emotion].color[2]}%)`};
        --mask: {`url("/v4/mask_${stones.length}_${index + 1}.svg")`};
    ">
      <div class="stone">

        <!-- Secondary colors -->
        {#each secondaryOrbs as color}
          {@const orb = randomOrb()}
          <div class="orb" style="
            --size: {orb.size}%;
            --x: {orb.x}%;
            --y: {orb.y}%;
            --duration: {orb.duration}s;
            --direction: {orb.direction};
            --color: {color};
          " />
        {/each}

        <!-- Primary colors -->
        {#each primaryOrbs as color}
          {@const orb = randomOrb()}
          <div class="orb" style="
            --size: {orb.size}%;
            --x: {orb.x}%;
            --y: {orb.y}%;
            --duration: {orb.duration}s;
            --direction: {orb.direction};
            --color: {color};
          " />
        {/each}

      </div>
    </div>
  {/each}

  <img src="/v4/shader_{stones.length}.png" class="shader" alt="shader" />

  <div class="grain" />

</div>

<style>

  .canvas {
    width: 100%;
    max-width: 480px;
    aspect-ratio: 1;
    transform-origin: top left;
    background: linear-gradient(to bottom, var(--bgStart), var(--bgEnd));
  }

  .stone-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    filter: drop-shadow(8px 8px 40px var(--baseColor));
    mix-blend-mode: multiply;
  }

  .stone {
    background: var(--baseColor);
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    mask-image: var(--mask);
    mask-size: contain;
    mask-repeat: no-repeat;
    mask-position: center;
  }

  .shader {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 10;
    mix-blend-mode: overlay;
    object-fit: contain;
  }

  .orb {
    filter: blur(var(--blur));
    position: absolute;
    top: var(--y);
    left: var(--x);
    width: var(--size);
    height: var(--size);
    background: var(--color);
    border-radius: 100%;
    animation: movement var(--duration) ease-in-out infinite var(--direction);
    opacity: 0.5;
    /* mix-blend-mode: overlay; */
  }

  .grain {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: url("/grain.png");
    border-radius: inherit;
  }

  @keyframes movement {
    from {
      transform: translateY(-100%);
    }
    to {
      transform: translateY(100%);
    }
  }

</style>
