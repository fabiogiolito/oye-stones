<script>

  let emotions = {
    anger: {
      name: 'anger',
      color: [360, 57, 60],
      sibling: 'balance',
      mask: "M17.5258 80.5715C13.0077 66.7917 38.8895 22.8975 46.0966 20.822C53.3036 18.7465 91.5797 12.3616 100.702 18.8094C109.824 25.2571 133.558 65.5653 130.839 76.5333C128.12 87.5012 82.4983 132.687 72.7976 133.328C63.0968 133.969 22.044 94.3513 17.5258 80.5715Z",
    },
    balance: {
      name: 'balance',
      color: [277, 48, 83],
      sibling: 'anger',
      mask: "M35.7149 108.032C31.3012 88.061 36.3785 57.8974 40.041 47.4571C54.4023 6.51861 91.6297 4.52182 106.939 47.4572C110.025 56.1111 117.259 89.0612 113.592 108.032C106.5 144.721 42.7027 139.65 35.7149 108.032Z",
    },
    
    fear: {
      name: 'fear',
      color: [209, 65, 71],
      sibling: 'passion',
      mask: "M24 77.803C24 67.719 44.7314 27.3344 54.2565 23.4536C63.7817 19.5727 111.243 23.4524 120.933 35.2209C127.854 43.6259 127.457 101.337 120.933 109.18C113.649 117.937 69.3848 131.546 58.739 127.11C48.0931 122.674 24 87.8869 24 77.803Z",
    },
    passion: {
      name: 'passion',
      color: [13, 100, 70],
      sibling: 'fear',
      mask: "M49.8027 23.8479C62.0094 18.7646 102.796 37.3433 115.975 58.0293C133.711 85.8667 125.621 93.4359 102.475 99.5293C90.1838 102.765 48.7247 138.791 35.4753 122.029C12.1859 92.5663 28.117 32.8787 49.8027 23.8479Z",
    },
    
    shame: {
      name: 'shame',
      color: [156, 42, 79],
      sibling: 'tranquility',
      mask: "M26.9622 36.1405C44.2375 16.1514 99.8089 19.7989 119.534 32.014C140.475 44.9821 100.616 127.247 73.1194 126.838C47.6222 126.458 13.7332 51.4475 26.9622 36.1405Z",
    },
    tranquility: {
      name: 'tranquility',
      color: [38, 61, 71],
      sibling: 'shame',
      mask: "M91.0426 19.8085C116.009 25.4246 109.12 49.3549 107.917 72.9523C107.008 90.7785 115.083 126.515 99.2798 130.055C80.8512 134.183 51.1656 106.955 42.4613 77.7451C30.1091 36.2942 67.0496 14.4112 91.0426 19.8085Z",
    },

    sadness: {
      name: 'sadness',
      color: [195, 61, 72],
      sibling: 'happiness',
      mask: "M21.0981 121.236C12.1761 104.522 60.5525 20 60.5525 20C60.5525 20 139.213 95.4505 129.103 112.946C120.143 128.45 28.7492 135.569 21.0981 121.236Z",
    },
    happiness: {
      name: 'happiness',
      color: [40, 100, 61],
      sibling: 'sadness',
      mask: "M24.0871 49.4813C34.1714 37.8499 57.3107 44.2993 68.4269 42.5654C80.0425 40.7537 116.916 19.5965 126.919 26.3122C136.922 33.0279 110.701 121.66 76.9068 124.002C43.1123 126.345 7.8225 68.2413 24.0871 49.4813Z",
    },

    apathy: {
      name: 'apathy',
      color: [183, 90, 75],
      sibling: 'vitality',
      mask: "M26.9622 36.1405C44.2375 16.1514 99.8089 19.7989 119.534 32.014C140.475 44.9821 100.616 127.247 73.1194 126.838C47.6222 126.458 13.7332 51.4475 26.9622 36.1405Z",
    },
    vitality: {
      name: 'vitality',
      color: [48, 100, 73],
      sibling: 'apathy',
      mask: "M24.4139 38.4584C36.1703 28.5201 58.0392 38.4584 69.2898 38.4584C81.0459 38.4585 120.739 23.2366 129.588 31.4137C138.437 39.5908 98.8698 123.123 65.118 120.229C31.3663 117.336 5.45249 54.4877 24.4139 38.4584Z",
    }, 
  };

  let orbsBlur = 25;

  let stonesCount = 3;
  let stonesVariant = 1;

  $: stones = [...Array(stonesCount)].map( (i, index) => {
    return {
      emotion: Object.keys(emotions)[index],
      primaryOrbCount: Math.round(Math.random() * (4 - 2) + 2),
      secondaryOrbCount: Math.round(Math.random()),
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
        --mask: {`url("/v5/mask_${stonesCount}_${stonesVariant}_${index + 1}.svg")`};
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

  <img src="/v5/shader_{stonesCount}_{stonesVariant}.png" class="shader" alt="shader" />

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
