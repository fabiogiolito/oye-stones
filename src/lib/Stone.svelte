<script>
  import { emotions } from "$lib/emotions";

  export let emotion = 'passion';
  $: currentEmotion = emotions[emotion];

  export let primaryOrbCount = 1;
  export let secondaryOrbCount = 1;
  export let orbsBlur = 25;

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

  function randomOrb() {
    const size = Math.random() * (60 - 20) + 20;
    const x = Math.random() * (100 - size);
    const y = Math.random() * (100 - size);
    const duration = Math.random() * (12 - 6) + 6;
    const direction = Math.round(Math.random()) ? 'alternate' : 'alternate-reverse';
    return { size, x, y, duration, direction }
  }

</script>

<div class="stone-container"
  style="
    --baseColor: {`hsl(${currentEmotion.color[0]}, ${currentEmotion.color[1]}%, ${currentEmotion.color[2]}%)`};
    --blur: {orbsBlur}px;
    --mask: url('/v3/mask_{currentEmotion.name}.svg');
  "
>
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
  <img src="/v3/shader_{currentEmotion.name}.png" class="shader" alt="shader" />
</div>

<style>
  .stone-container {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    filter: drop-shadow(8px 8px 40px var(--baseColor));
  }

  .stone {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: var(--baseColor);
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
