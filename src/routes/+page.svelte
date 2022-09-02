<script>

  let orbs = [
    "#DBCBED",
    "#8963B5",
    "#9B4C47",
    "#FD6259",
    "#DBCBED",
    "#8963B5",
    "#9B4C47",
    "#FD6259",
    "#DBCBED",
    "#8963B5",
    "#9B4C47",
    "#FD6259",
    "#DBCBED",
    "#8963B5",
    "#9B4C47",
    "#FD6259",
  ];

  function randomOrb() {
    const size = Math.random() * (320 - 100) + 100;
    const x = Math.random() * (400 - 60) + 60;
    const y = Math.random() * (480 - 140) + 140;
    const duration = Math.random() * (10 - 4) + 4;
    const direction = Math.round(Math.random()) ? 'alternate' : 'alternate-reverse';
    return { size, x, y, duration, direction }
  }
  
</script>

<div class="canvas" style="
  --startColor: #FFD8D3;
  --endColor: #FFD8D3;
  --baseColor: #F9A5A0;
  --blur: {20}px;
">

  <svg viewBox="0 0 480 640" fill="none" xmlns="http://www.w3.org/2000/svg">
    <clipPath id="stonesMask">
      <path fill="white" d="M329.092 415.505C304.425 488.505 265.592 499.172 212.592 447.505C159.592 395.839 115.092 335.505 79.0919 266.505C43.0919 197.505 67.4253 157.339 152.092 146.005C236.759 134.672 293.592 158.505 322.592 217.505C351.592 276.505 353.759 342.505 329.092 415.505Z" />
    </clipPath>
  </svg>

  <div class="stone-container">
    <div class="stone">
      {#each orbs as orb}
        <div class="orb" style="
          --size: {randomOrb().size}px;
          --x: {randomOrb().x}px;
          --y: {randomOrb().y}px;
          --duration: {randomOrb().duration}s;
          --direction: {randomOrb().direction};
          --color: {orb};
        " />
      {/each}
    </div>
  </div>

  <img src="/stones_3_shade.png" class="shader" alt="shader" />

  <div class="grain" />

</div>


<style>
  .canvas {
    background: linear-gradient(to bottom, var(--startColor), var(--endColor));
  }

  .shader {
    position: absolute;
    inset: 0;
    z-index: 10;
    mix-blend-mode: overlay;
  }

  .stone-container {
    filter: drop-shadow(8px 8px 40px var(--baseColor));
    mix-blend-mode: multiply;
  }

  .stone {
    background: var(--baseColor);
    position: absolute;
    inset: 0;
    clip-path: url(#stonesMask);
    mix-blend-mode: multiply;
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
  }

  .grain {
    position: absolute;
    inset: 0;
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
