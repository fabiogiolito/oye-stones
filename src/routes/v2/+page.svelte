<script>

  let container;
  let scale = 1;

  let primaryOrbCount = 1;
  let secondaryOrbCount = 1;
  let primaryColor = [ 4, 100, 75 ];
  let secondaryColor = [ 200, 100, 90 ];
  let orbBlur = 30;

  $: primaryOrbs = colorsArray(primaryColor, primaryOrbCount);
  $: secondaryOrbs = colorsArray(secondaryColor, secondaryOrbCount, 60, 70);

  $: if (container) resizeVibra();

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
    const size = Math.random() * (250 - 150) + 150;
    const x = Math.random() * (480 - size);
    const y = Math.random() * (640 - size);
    const duration = Math.random() * (10 - 4) + 4;
    const direction = Math.round(Math.random()) ? 'alternate' : 'alternate-reverse';
    return { size, x, y, duration, direction }
  }

  function resizeVibra() {
    const expected = 480;
    scale = container.offsetWidth / expected;
  }
  
</script>

<svelte:window on:resize={resizeVibra} />

<div class="mx-auto px-2 max-w-[480px]">

  {#key [primaryColor, secondaryColor]}

    <div class="canvas-container" bind:this={container}>

      <div class="canvas" style="
        --bgStart: {randomizeLightness(primaryColor, 90, 95)};
        --bgEnd: {randomizeLightness(primaryColor, 70, 90)};
        --baseColor: {`hsl(${primaryColor[0]}, ${primaryColor[1]}%, ${primaryColor[2]}%)`};
        --blur: {orbBlur}px;
        --scale: {scale};
      ">

        <svg viewBox="0 0 480 640" fill="none" xmlns="http://www.w3.org/2000/svg">
          <clipPath id="stonesMask">
            <path fill="white" d="M329.092 415.505C304.425 488.505 265.592 499.172 212.592 447.505C159.592 395.839 115.092 335.505 79.0919 266.505C43.0919 197.505 67.4253 157.339 152.092 146.005C236.759 134.672 293.592 158.505 322.592 217.505C351.592 276.505 353.759 342.505 329.092 415.505Z" />
          </clipPath>
        </svg>

        <div class="stone-container">
          <div class="stone">
            
            <!-- Secondary colors -->
            {#each secondaryOrbs as color}
              {@const orb = randomOrb()}
              <div class="orb" style="
                --size: {orb.size}px;
                --x: {orb.x}px;
                --y: {orb.y}px;
                --duration: {orb.duration}s;
                --direction: {orb.direction};
                --color: {color};
              " />
            {/each}

            <!-- Primary colors -->
            {#each primaryOrbs as color}
              {@const orb = randomOrb()}
              <div class="orb" style="
                --size: {orb.size}px;
                --x: {orb.x}px;
                --y: {orb.y}px;
                --duration: {orb.duration}s;
                --direction: {orb.direction};
                --color: {color};
              " />
            {/each}

          </div>
        </div>

        <img src="/stones_3_shade.png" class="shader" alt="shader" />

        <div class="grain" />

      </div>
    </div>

  {/key}

  <div class="w-[480px] max-w-full mx-auto my-4 text-xs border border-black border-opacity-10 rounded-lg">
    <p class="font-semibold p-4 pb-2">
      Primary
    </p>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-20">Color <br /> (H, S, L)</p>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={primaryColor[0]}>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={primaryColor[1]}>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={primaryColor[2]}>
    </div>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-20">Orb Count</p>
      <input class="flex-1 min-w-[1px]" type="range" bind:value={primaryOrbCount} min="0" max="20">
      <span>{primaryOrbCount}</span>
    </div>
    <p class="font-semibold p-4 pb-2 mt-2 border-t border-inherit">
      Secondary
    </p>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-20">Color <br /> (H, S, L)</p>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={secondaryColor[0]}>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={secondaryColor[1]}>
      <input class="flex-1 min-w-[1px]" type="number" bind:value={secondaryColor[2]}>
    </div>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-20">Orb count</p>
      <input class="flex-1 min-w-[1px]" type="range" bind:value={secondaryOrbCount} min="0" max="20">
      <span>{secondaryOrbCount}</span>
    </div>
    <p class="font-semibold p-4 pb-2 mt-2 border-t border-inherit">
      Options
    </p>
    <div class="flex space-x-2 px-4 pb-2">
      <p class="w-20">Orbs blur</p>
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
  }

  .stone-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    filter: drop-shadow(8px 8px 40px var(--baseColor));
  }

  .stone {
    background: var(--baseColor);
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    clip-path: url(#stonesMask);
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
      transform: translateY(-100px);
    }
    to {
      transform: translateY(100px);
    }
  }

</style>
