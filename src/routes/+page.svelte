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

  let selectedEmotion = 'passion';
  $: currentEmotion = emotions[selectedEmotion];

  let primaryOrbCount = 1;
  let secondaryOrbCount = 1;
  let orbBlur = 25;

  $: primaryOrbs = colorsArray(currentEmotion.color, primaryOrbCount);
  $: secondaryOrbs = colorsArray(emotions[currentEmotion.sibling].color, secondaryOrbCount, 60, 70);


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

<div class="mx-auto px-2 max-w-[480px]">
  
  <div class="canvas-container">

    <div class="canvas" style="
      --bgStart: {randomizeLightness(currentEmotion.color, 90, 95)};
      --bgEnd: {randomizeLightness(currentEmotion.color, 70, 90)};
      --baseColor: {`hsl(${currentEmotion.color[0]}, ${currentEmotion.color[1]}%, ${currentEmotion.color[2]}%)`};
      --blur: {orbBlur}px;
      --mask: {`url("/v3/mask_${currentEmotion.name}.svg")`};
    ">

      <div class="stone-container">
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

      <img src="/v3/shader_{currentEmotion.name}.png" class="shader" alt="shader" />

      <div class="grain" />

    </div>
  </div> 

  <div class="w-[480px] max-w-full mx-auto my-4 py-2 text-xs border border-black border-opacity-10 rounded-lg">
    <div class="flex space-x-2 px-4 pb-2 pt-2">
      <p class="w-24">Emotion</p>
      <select bind:value={selectedEmotion}>
        {#each Object.keys(emotions) as emotion}
          <option value={emotion}>{emotions[emotion].name}</option>
        {/each}
      </select>
    </div>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-24">Primary Orbs</p>
      <input class="flex-1 min-w-[1px]" type="range" bind:value={primaryOrbCount} min="0" max="20">
      <span>{primaryOrbCount}</span>
    </div>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-24">Secondary Orbs</p>
      <input class="flex-1 min-w-[1px]" type="range" bind:value={secondaryOrbCount} min="0" max="20">
      <span>{secondaryOrbCount}</span>
    </div>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-24">Orbs blur</p>
      <input class="flex-1 min-w-[1px]" type="range" bind:value={orbBlur} min="0" max="40">
      <span>{orbBlur}</span>
    </div>
  </div>

</div>

<style>

  .canvas-container {
    overflow: hidden;
  }

  .canvas {
    width: 100%;
    aspect-ratio: 1;
    transform: scale(var(--scale));
    transform-origin: top left;
    background: linear-gradient(to bottom, var(--bgStart), var(--bgEnd));
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
