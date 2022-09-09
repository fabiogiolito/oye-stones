<script>
  import { emotions } from "$lib/emotions";
  import Stone from "$lib/Stone.svelte";

  let stonesCount = 3;
  let positionVariant = 0;

  let bgColor = emotions['anger'].color;

  const possiblePositions = {
    3: [
      [
        {
          rotation: Math.round(Math.random() * 360),
          scale: 247 / 480 * 100,
          x: 199 / 480 * 100,
          y: 1 / 480 * 100,
        },
        {
          rotation: Math.round(Math.random() * 360),
          scale: 247 / 480 * 100,
          x: 9 / 480 * 100,
          y: 142 / 480 * 100,
        },
        {
          rotation: Math.round(Math.random() * 360),
          scale: 247 / 480 * 100,
          x: 226 / 480 * 100,
          y: 235 / 480 * 100,
        },
      ]
    ]
  }

  $: stones = [...Array(stonesCount)].map( (i, index) => {
    const emotionsArr = Object.keys(emotions);
    return {
      emotion: emotionsArr[Math.floor(Math.random() * emotionsArr.length)],
      primaryOrbCount: Math.round(Math.random() * (4 - 2) + 2),
      secondaryOrbCount: Math.round(Math.random()),
    }
  });

  $: positions = possiblePositions[stonesCount][positionVariant];

  // ===========================
  // Functions

  function randomizeLightness(hsl = primaryColor, rangeStart = 10, rangeEnd = 90) {
    let lightness = Math.random() * (rangeEnd - rangeStart) + rangeStart;
    return `hsl(${hsl[0]}, ${hsl[1]}%, ${Math.round(lightness)}%)`;
  }
</script>


<div class="canvas" style="
  --bgStart: {randomizeLightness(bgColor, 90, 95)};
  --bgEnd: {randomizeLightness(bgColor, 70, 90)};
">

  {#each stones as stone, index}
    <div class="stone-position" style="
      --rotation: {positions[index].rotation};
      --scale: {positions[index].scale};
      --x: {positions[index].x};
      --y: {positions[index].y};
    ">
      <Stone {...stone} />
    </div>
  {/each}

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

  .stone-position {
    width: calc(100% * (var(--scale, 100) / 100));
    height: calc(100% * (var(--scale, 1) / 100));
    position: absolute;
    top: calc(var(--y) * 1%);
    left: calc(var(--x) * 1%);
    transform: rotate(calc(1deg * var(--rotation, 1)));
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

</style>
