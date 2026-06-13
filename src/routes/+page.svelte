<script>
  let mx = $state(0.5);
  let my = $state(0.5);
  let trailDots = $state([]);
  let tid = 0;

  function handleMove(e) {
    mx = e.clientX / window.innerWidth;
    my = e.clientY / window.innerHeight;
    tid++;
    trailDots = [...trailDots.slice(-10), { x: e.clientX, y: e.clientY, id: tid }];
  }

  // Equalizer bars
  const eqBars = Array.from({ length: 32 }, (_, i) => ({
    id: i,
    delay: Math.random() * 2,
    duration: 0.4 + Math.random() * 1.2,
    height: 30 + Math.random() * 70,
  }));

  // City buildings
  const buildings = Array.from({ length: 40 }, (_, i) => ({
    id: i,
    left: i * 2.5,
    height: 8 + Math.random() * 55,
    width: 2 + Math.random() * 4,
    neon: Math.random() > 0.7,
  }));

  // Fireworks — 4 bursts, 24 particles each
  const particleCount = 24;
  const fireworkColors = [
    ['#ff2d55', '#ff5e5e', '#ffaaaa'],
    ['#0ff', '#5effff', '#aaffff'],
    ['#f5c842', '#ffdd66', '#ffee99'],
    ['#ff2d95', '#ff66bb', '#ffaadd'],
  ];
  const fireworks = Array.from({ length: 4 }, (_, bi) => ({
    id: bi,
    left: 15 + Math.random() * 70,
    top: 10 + Math.random() * 45,
    delay: bi * 2.5 + Math.random() * 3,
    particles: Array.from({ length: particleCount }, (_, pi) => {
      const angle = (pi / particleCount) * 360 + Math.random() * 15;
      const rad = (angle * Math.PI) / 180;
      const dist = 60 + Math.random() * 100;
      return {
        id: pi,
        tx: Math.cos(rad) * dist,
        ty: Math.sin(rad) * dist,
        delay: Math.random() * 0.3,
        color: fireworkColors[bi][pi % 3],
      };
    }),
  }));

  // Stars
  const stars = Array.from({ length: 30 }, (_, i) => ({
    id: i,
    x: Math.random() * 100,
    y: Math.random() * 60,
  }));

  // Hovercars — light trails in the city
  const hovercars = Array.from({ length: 12 }, (_, i) => ({
    id: i,
    top: 68 + Math.random() * 24,
    delay: i * 1.2 + Math.random() * 3,
    duration: 3 + Math.random() * 5,
    flip: i % 2 === 0,
  }));
</script>

<svelte:window onmousemove={handleMove} />

<div class="stage" style="--mx: {mx}; --my: {my}">
  <!-- Mouse glow -->
  <div class="mouse-glow" style="--mx: {mx}; --my: {my}"></div>

  <!-- Mouse trail -->
  {#each trailDots as dot (dot.id)}
    <div
      class="trail-dot"
      style="left: {dot.x}px; top: {dot.y}px; animation-delay: 0s"
    ></div>
  {/each}

  <!-- Scanlines overlay -->
  <div class="scanlines"></div>

  <!-- Chromatic fringe -->
  <div class="chroma"></div>

  <!-- Stars -->
  {#each stars as star (star.id)}
    <div class="star" style="left: {star.x}%; top: {star.y}%; animation-delay: {Math.random() * 4}s"></div>
  {/each}

  <!-- Hovercars -->
  {#each hovercars as car (car.id)}
    <div
      class="hovercar {car.flip ? 'hovercar--rev' : ''}"
      style="
        top: {car.top}%;
        animation-delay: {car.delay}s;
        animation-duration: {car.duration}s;
      "
    ></div>
  {/each}

  <!-- Fireworks -->
  {#each fireworks as burst (burst.id)}
    <div
      class="firework"
      style="left: {burst.left}%; top: {burst.top}%"
    >
      {#each burst.particles as p (p.id)}
        <div
          class="fw-particle"
          style="
            --tx: {p.tx}px;
            --ty: {p.ty}px;
            --color: {p.color};
            animation-delay: {burst.delay + p.delay}s;
          "
        ></div>
      {/each}
    </div>
  {/each}

  <!-- Vaporwave sun -->
  <div class="sun">
    <div class="sun__segment sun__seg--1"></div>
    <div class="sun__segment sun__seg--2"></div>
    <div class="sun__segment sun__seg--3"></div>
    <div class="sun__segment sun__seg--4"></div>
    <div class="sun__segment sun__seg--5"></div>
  </div>

  <!-- RAM RGB sticks -->
  <div class="ram-stick ram-stick--1"></div>
  <div class="ram-stick ram-stick--2"></div>
  <div class="ram-stick ram-stick--3"></div>
  <div class="ram-stick ram-stick--4"></div>

  <!-- Synthwave grid floor -->
  <div class="grid-floor"></div>

  <!-- City skyline -->
  <div class="skyline">
    {#each buildings as b (b.id)}
      <div
        class="building {b.neon ? 'building--neon' : ''}"
        style="
          left: {b.left}%;
          height: {b.height}px;
          width: {b.width}px;
        "
      ></div>
    {/each}
  </div>

  <!-- Fog / smog layers -->
  <div class="fog fog--1"></div>
  <div class="fog fog--2"></div>

  <!-- Searchlights -->
  <div class="searchlight searchlight--1"></div>
  <div class="searchlight searchlight--2"></div>
  <div class="searchlight searchlight--3"></div>

  <!-- Glitch title -->
  <div class="title-block">
    <h1 class="glitch" data-text="ASS CITY">ASS CITY</h1>
  </div>

  <!-- Player card -->
  <div class="player">
    <div class="player__header">
      <span class="player__live-badge">● LIVE</span>
      <span class="player__viewers">777 watching</span>
    </div>
    <div class="player__screen">
      <!-- Equalizer inside screen -->
      <div class="equalizer">
        {#each eqBars as bar (bar.id)}
          <div
            class="eq-bar"
            style="
              animation-delay: {bar.delay}s;
              animation-duration: {bar.duration}s;
              --h: {bar.height}%;
            "
          ></div>
        {/each}
      </div>
      <!-- Spinning record -->
      <div class="record">
        <div class="record__label"></div>
      </div>
    </div>
    <!-- Progress bar -->
    <div class="progress">
      <div class="progress__fill"></div>
      <div class="progress__thumb"></div>
    </div>
    <div class="player__time">
      <span>00:00</span>
      <span>∞:∞</span>
    </div>
  </div>

  <!-- Action bar -->
  <div class="actions">
    <button class="btn btn--like">
      <span class="btn__icon">▲</span>
      <span>523K</span>
    </button>
    <button class="btn btn--dislike">
      <span class="btn__icon">▼</span>
    </button>
    <button class="btn btn--share">SHARE</button>
  </div>

  <!-- RGB LED strip -->
  <div class="led-strip"></div>

  <!-- Sun halo ring -->
  <div class="sun-halo"></div>

  <!-- CRT vignette -->
  <div class="crt-vignette"></div>
</div>

<style>
  /* ══════════════════════════════════════
     FIREWORKS — radial particle bursts
     ══════════════════════════════════════ */
  .firework {
    position: absolute;
    z-index: 6;
    pointer-events: none;
    width: 0;
    height: 0;
  }

  .fw-particle {
    position: absolute;
    width: 3px;
    height: 3px;
    border-radius: 50%;
    background: var(--color, #0ff);
    box-shadow:
      0 0 6px var(--color, #0ff),
      0 0 12px var(--color, #0ff),
      0 0 20px rgba(255, 255, 255, 0.3);
    animation: fwBurst 6s ease-out infinite;
    opacity: 0;
  }

  @keyframes fwBurst {
    0%, 55% {
      transform: translate(0, 0) scale(0);
      opacity: 0;
    }
    58% {
      transform: translate(0, 0) scale(1.2);
      opacity: 1;
    }
    68% {
      opacity: 1;
      transform: translate(
        calc(var(--tx, 50px) * 0.35),
        calc(var(--ty, -50px) * 0.35)
      ) scale(1.1);
    }
    100% {
      transform: translate(var(--tx, 80px), var(--ty, -80px)) scale(0);
      opacity: 0;
    }
  }

  /* ══════════════════════════════════════
     STAGE
     ══════════════════════════════════════ */
  .stage {
    min-height: 100vh;
    background: #06071a;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start;
    gap: 16px;
    overflow: hidden;
    position: relative;
    padding: 28px 20px 40px;
    isolation: isolate;
    font-family: 'Noto Sans KR', 'Courier New', monospace;
  }

  /* ══════════════════════════════════════
     SCANLINES — VHS/CRT effect
     ══════════════════════════════════════ */
  .scanlines {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 20;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(0, 0, 0, 0.15) 2px,
      rgba(0, 0, 0, 0.15) 4px
    );
    animation: scanMove 3s linear infinite;
    opacity: 0.8;
  }

  @keyframes scanMove {
    0% { transform: translateY(0); }
    100% { transform: translateY(4px); }
  }

  /* ══════════════════════════════════════
     MOUSE GLOW — radial aura follows cursor
     ══════════════════════════════════════ */
  .mouse-glow {
    position: fixed;
    pointer-events: none;
    z-index: 15;
    width: 300px;
    height: 300px;
    border-radius: 50%;
    background: radial-gradient(circle, rgba(0, 240, 255, 0.06) 0%, rgba(255, 45, 149, 0.03) 40%, transparent 70%);
    transform: translate(-50%, -50%);
    left: calc(var(--mx, 0.5) * 100vw);
    top: calc(var(--my, 0.5) * 100vh);
    filter: blur(20px);
    transition: left 0.4s ease-out, top 0.4s ease-out;
  }

  /* ══════════════════════════════════════
     MOUSE TRAIL — fading dots
     ══════════════════════════════════════ */
  .trail-dot {
    position: fixed;
    pointer-events: none;
    z-index: 14;
    width: 5px;
    height: 5px;
    border-radius: 50%;
    background: #0ff;
    box-shadow:
      0 0 8px rgba(0, 240, 255, 0.9),
      0 0 16px rgba(0, 240, 255, 0.5),
      0 0 24px rgba(255, 45, 149, 0.3);
    transform: translate(-50%, -50%);
    animation: trailFade 0.6s ease-out forwards, trailHue 1.5s linear infinite;
  }

  @keyframes trailFade {
    0% { opacity: 1; transform: translate(-50%, -50%) scale(1.2); }
    100% { opacity: 0; transform: translate(-50%, -50%) scale(0); }
  }

  @keyframes trailHue {
    0%, 100% { background: #0ff; box-shadow: 0 0 8px #0ff, 0 0 16px rgba(0,240,255,0.5), 0 0 24px rgba(255,45,149,0.3); }
    33% { background: #ff2d95; box-shadow: 0 0 8px #ff2d95, 0 0 16px rgba(255,45,149,0.5), 0 0 24px rgba(0,240,255,0.3); }
    66% { background: #f5c842; box-shadow: 0 0 8px #f5c842, 0 0 16px rgba(245,200,66,0.5), 0 0 24px rgba(255,45,149,0.3); }
  }

  /* Chromatic aberration fringe */
  .chroma {
    position: absolute;
    inset: -5px;
    pointer-events: none;
    z-index: 19;
    background: transparent;
    animation: chromaCycle 4s linear infinite;
    opacity: 0.7;
  }

  @keyframes chromaCycle {
    0% { box-shadow: inset 0 0 80px rgba(255,0,80,0.12), inset 0 0 40px rgba(0,200,255,0.08); }
    33% { box-shadow: inset 0 0 80px rgba(0,255,80,0.12), inset 0 0 40px rgba(255,0,200,0.08); }
    66% { box-shadow: inset 0 0 80px rgba(0,100,255,0.12), inset 0 0 40px rgba(255,200,0,0.08); }
    100% { box-shadow: inset 0 0 80px rgba(255,0,80,0.12), inset 0 0 40px rgba(0,200,255,0.08); }
  }

  /* ══════════════════════════════════════
     STARS
     ══════════════════════════════════════ */
  .star {
    position: absolute;
    width: 1.5px;
    height: 1.5px;
    border-radius: 50%;
    background: #fff;
    pointer-events: none;
    z-index: 0;
    animation: starTwinkle 3s infinite alternate ease-in-out;
    box-shadow: 0 0 2px rgba(255, 255, 255, 0.8);
  }

  .star {
    animation: starTwinkle 2s infinite alternate ease-in-out, starHue 4s linear infinite;
  }

  @keyframes starTwinkle {
    0% { opacity: 0.3; transform: scale(1); }
    100% { opacity: 1; transform: scale(2); }
  }

  @keyframes starHue {
    0%, 100% { background: #fff; box-shadow: 0 0 3px rgba(255,255,255,0.8); }
    33% { background: #0ff; box-shadow: 0 0 4px rgba(0,255,255,1); }
    66% { background: #ff2d95; box-shadow: 0 0 4px rgba(255,45,149,1); }
  }

  /* ══════════════════════════════════════
     VAPORWAVE SUN — segmented
     ══════════════════════════════════════ */
  .sun {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translate(
      calc(-50% + (var(--mx, 0.5) - 0.5) * 20px),
      calc(-50% + (var(--my, 0.5) - 0.5) * 15px)
    );
    width: 260px;
    height: 260px;
    pointer-events: none;
    z-index: 0;
    animation: sunPulse 6s infinite alternate ease-in-out;
    transition: transform 0.6s ease-out;
  }

  @keyframes sunPulse {
    0% { filter: brightness(0.85) hue-rotate(0deg); }
    100% { filter: brightness(1.2) hue-rotate(15deg); }
  }

  /* Sun halo ring */
  .sun-halo {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 300px;
    height: 300px;
    border-radius: 50%;
    pointer-events: none;
    z-index: 0;
    border: 2px solid transparent;
    border-top-color: rgba(255, 45, 149, 0.4);
    border-right-color: rgba(245, 200, 66, 0.3);
    animation: haloSpin 8s linear infinite, haloPulse 2s infinite alternate ease-in-out;
  }

  @keyframes haloSpin {
    0% { transform: translate(-50%, -50%) rotate(0deg); }
    100% { transform: translate(-50%, -50%) rotate(360deg); }
  }

  @keyframes haloPulse {
    0% { opacity: 0.4; border-width: 2px; }
    100% { opacity: 0.9; border-width: 3px; }
  }

  /* LED strip bottom */
  .led-strip {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 2px;
    pointer-events: none;
    z-index: 6;
    background: linear-gradient(
      90deg,
      #ff2d55, #ff5e3a, #f5c842, #0f0, #0ff, #f0f, #ff2d55, #ff5e3a, #f5c842, #0ff,
      #ff2d55, #ff5e3a, #f5c842, #0f0, #0ff, #f0f, #ff2d55, #ff5e3a, #f5c842, #0ff
    );
    background-size: 200% 100%;
    animation: ledFlow 1s linear infinite;
    box-shadow:
      0 0 8px rgba(255, 45, 85, 0.6),
      0 0 16px rgba(0, 240, 255, 0.4),
      0 -4px 12px rgba(255, 45, 149, 0.3);
  }

  @keyframes ledFlow {
    0% { background-position: 0% 0; }
    100% { background-position: 200% 0; }
  }

  .sun__segment {
    position: absolute;
    inset: 0;
    border-radius: 50%;
    mask: radial-gradient(circle, #000 0%, #000 100%);
    -webkit-mask: radial-gradient(circle, #000 0%, #000 100%);
  }

  .sun__seg--1 {
    background: #ff2d55;
    clip-path: polygon(50% 0%, 100% 0%, 100% 15%, 50% 15%);
  }

  .sun__seg--2 {
    background: #ff5e3a;
    clip-path: polygon(50% 15%, 100% 15%, 100% 35%, 50% 35%);
  }

  .sun__seg--3 {
    background: #ff8c2d;
    clip-path: polygon(50% 35%, 100% 35%, 100% 55%, 50% 55%);
  }

  .sun__seg--4 {
    background: #ffb82e;
    clip-path: polygon(50% 55%, 100% 55%, 100% 78%, 50% 78%);
  }

  .sun__seg--5 {
    background: #f5c842;
    clip-path: polygon(50% 78%, 100% 78%, 100% 100%, 50% 100%);
  }

  /* ══════════════════════════════════════
     SYNTHWAVE GRID FLOOR
     ══════════════════════════════════════ */
  .grid-floor {
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(
        calc(-50% + (var(--mx, 0.5) - 0.5) * 30px)
      )
      perspective(400px)
      rotateX(60deg);
    width: 200%;
    height: 500px;
    transition: transform 0.8s ease-out;
    pointer-events: none;
    z-index: 0;
    background:
      linear-gradient(0deg, transparent 0%, rgba(6,7,26,0.8) 60%, #06071a 100%),
      repeating-linear-gradient(
        90deg,
        rgba(255, 45, 149, 0.25) 0px,
        transparent 1px,
        transparent 70px,
        rgba(255, 45, 149, 0.25) 71px
      ),
      repeating-linear-gradient(
        0deg,
        rgba(0, 240, 255, 0.2) 0px,
        transparent 1px,
        transparent 40px,
        rgba(0, 240, 255, 0.2) 41px
      );
    animation: gridPulse 2s infinite alternate ease-in-out, gridHue 5s linear infinite;
    transform-origin: bottom center;
  }

  @keyframes gridPulse {
    0% { opacity: 0.6; }
    100% { opacity: 1; }
  }

  @keyframes gridHue {
    0%, 100% { filter: hue-rotate(0deg); }
    50% { filter: hue-rotate(30deg); }
  }

  /* ══════════════════════════════════════
     CITY SKYLINE
     ══════════════════════════════════════ */
  .skyline {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 200px;
    pointer-events: none;
    z-index: 1;
    transform: translateX(calc((var(--mx, 0.5) - 0.5) * -10px));
    transition: transform 0.6s ease-out;
  }

  .building {
    position: absolute;
    bottom: 0;
    background: #0a0b24;
    border-top: 1px solid rgba(0, 240, 255, 0.15);
    animation: buildingFlicker 5s infinite alternate ease-in-out;
  }

  .building--neon {
    border-top-color: rgba(255, 45, 149, 0.5);
    box-shadow: 0 0 8px rgba(255, 45, 149, 0.2);
  }

  @keyframes buildingFlicker {
    0%, 95%, 100% { opacity: 1; }
    96% { opacity: 0.7; }
    97% { opacity: 0.95; }
  }

  /* ══════════════════════════════════════
     FOG / SMOG — drifting city haze
     ══════════════════════════════════════ */
  .fog {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 120px;
    pointer-events: none;
    z-index: 2;
  }

  .fog--1 {
    background: linear-gradient(0deg, rgba(255, 45, 149, 0.06) 0%, transparent 70%);
    animation: fogDrift 14s infinite alternate ease-in-out;
  }

  .fog--2 {
    height: 80px;
    background: linear-gradient(0deg, rgba(0, 240, 255, 0.04) 0%, transparent 60%);
    animation: fogDrift 10s infinite alternate-reverse ease-in-out;
  }

  @keyframes fogDrift {
    0% { transform: translateX(-2%); opacity: 0.6; }
    100% { transform: translateX(2%); opacity: 1; }
  }

  /* ══════════════════════════════════════
     RAM RGB STICKS — gaming component glow
     ══════════════════════════════════════ */
  .ram-stick {
    position: absolute;
    pointer-events: none;
    z-index: 3;
    height: 3px;
    border-radius: 2px;
    background: linear-gradient(
      90deg,
      #ff2d55, #ff5e3a, #f5c842, #0ff, #ff2d95, #0ff, #f5c842, #ff5e3a, #ff2d55
    );
    background-size: 300% 100%;
    animation: ramFlow 1.5s linear infinite;
    box-shadow:
      0 0 10px rgba(255, 45, 149, 0.7),
      0 0 20px rgba(0, 240, 255, 0.4),
      0 0 30px rgba(245, 200, 66, 0.2);
  }

  .ram-stick--1 {
    top: 55%;
    left: 5%;
    width: 120px;
    animation-delay: 0s;
  }

  .ram-stick--2 {
    top: 58%;
    right: 5%;
    width: 90px;
    animation-delay: -1s;
  }

  .ram-stick--3 {
    top: 52%;
    left: 15%;
    width: 60px;
    animation-delay: -2s;
    opacity: 0.6;
  }

  .ram-stick--4 {
    top: 60%;
    right: 12%;
    width: 80px;
    animation-delay: -0.5s;
    opacity: 0.7;
  }

  @keyframes ramFlow {
    0% { background-position: 200% 0; }
    100% { background-position: 0% 0; }
  }

  /* ══════════════════════════════════════
     SEARCHLIGHTS — sweeping beams
     ══════════════════════════════════════ */
  .searchlight {
    position: absolute;
    bottom: 0;
    width: 3px;
    height: 350px;
    pointer-events: none;
    z-index: 3;
    transform-origin: bottom center;
    background: linear-gradient(
      0deg,
      rgba(0, 240, 255, 0.15) 0%,
      rgba(0, 240, 255, 0.04) 50%,
      transparent 100%
    );
  }

  .searchlight--1 {
    left: 20%;
    background: linear-gradient(0deg, rgba(0,240,255,0.2) 0%, rgba(0,240,255,0.05) 50%, transparent 100%);
    animation: sweepA 7s infinite alternate ease-in-out, lightColor1 4s linear infinite;
  }

  .searchlight--2 {
    left: 50%;
    background: linear-gradient(0deg, rgba(255,45,149,0.2) 0%, rgba(255,45,149,0.05) 50%, transparent 100%);
    animation: sweepB 8s infinite alternate ease-in-out, lightColor2 5s linear infinite;
    animation-delay: -2s, 0s;
  }

  .searchlight--3 {
    left: 75%;
    background: linear-gradient(0deg, rgba(245,200,66,0.2) 0%, rgba(245,200,66,0.05) 50%, transparent 100%);
    animation: sweepA 9s infinite alternate ease-in-out, lightColor3 3.5s linear infinite;
    animation-delay: -4s, 0s;
  }

  @keyframes sweepA {
    0% { transform: rotate(-35deg); opacity: 0.3; }
    50% { opacity: 0.9; }
    100% { transform: rotate(35deg); opacity: 0.3; }
  }

  @keyframes sweepB {
    0% { transform: rotate(25deg); opacity: 0.25; }
    50% { opacity: 0.85; }
    100% { transform: rotate(-30deg); opacity: 0.25; }
  }

  @keyframes lightColor1 {
    0%, 100% { background: linear-gradient(0deg, rgba(0,240,255,0.2) 0%, rgba(0,240,255,0.05) 50%, transparent 100%); }
    50% { background: linear-gradient(0deg, rgba(255,45,149,0.2) 0%, rgba(255,45,149,0.05) 50%, transparent 100%); }
  }

  @keyframes lightColor2 {
    0%, 100% { background: linear-gradient(0deg, rgba(255,45,149,0.2) 0%, rgba(255,45,149,0.05) 50%, transparent 100%); }
    50% { background: linear-gradient(0deg, rgba(245,200,66,0.2) 0%, rgba(245,200,66,0.05) 50%, transparent 100%); }
  }

  @keyframes lightColor3 {
    0%, 100% { background: linear-gradient(0deg, rgba(245,200,66,0.2) 0%, rgba(245,200,66,0.05) 50%, transparent 100%); }
    50% { background: linear-gradient(0deg, rgba(0,240,255,0.2) 0%, rgba(0,240,255,0.05) 50%, transparent 100%); }
  }

  /* ══════════════════════════════════════
     HOVERCARS — light trails
     ══════════════════════════════════════ */
  .hovercar {
    position: absolute;
    left: -40px;
    z-index: 3;
    pointer-events: none;
    width: 24px;
    height: 2px;
    background: linear-gradient(90deg, transparent, #0ff, #f5c842, transparent);
    border-radius: 1px;
    box-shadow:
      0 0 6px rgba(0, 240, 255, 0.9),
      0 0 16px rgba(0, 240, 255, 0.5),
      0 0 24px rgba(245, 200, 66, 0.3);
    animation: carCross linear infinite, carHue 2s linear infinite;
  }

  .hovercar--rev {
    left: auto;
    right: -40px;
    animation-name: carCrossRev, carHue;
  }

  @keyframes carCross {
    0% { transform: translateX(0); opacity: 0; }
    5% { opacity: 1; }
    90% { opacity: 0.9; }
    100% { transform: translateX(110vw); opacity: 0; }
  }

  @keyframes carCrossRev {
    0% { transform: translateX(0); opacity: 0; }
    5% { opacity: 1; }
    90% { opacity: 0.9; }
    100% { transform: translateX(-110vw); opacity: 0; }
  }

  @keyframes carHue {
    0%, 100% { filter: hue-rotate(0deg); }
    50% { filter: hue-rotate(180deg); }
  }

  /* ══════════════════════════════════════
     GLITCH TITLE
     ══════════════════════════════════════ */
  .title-block {
    position: relative;
    z-index: 5;
    text-align: center;
    margin-top: 8px;
  }

  .glitch {
    font-family: 'Courier New', monospace;
    font-size: clamp(32px, 8vw, 56px);
    font-weight: 700;
    letter-spacing: 0.15em;
    color: #0ff;
    text-shadow:
      0 0 10px rgba(0, 255, 255, 0.8),
      0 0 40px rgba(0, 255, 255, 0.5),
      0 0 80px rgba(0, 255, 255, 0.3),
      0 0 120px rgba(255, 45, 149, 0.2);
    position: relative;
    animation: glitchSkew 4s infinite steps(1), titleHue 6s linear infinite;
  }

  @keyframes titleHue {
    0%, 100% { color: #0ff; text-shadow: 0 0 10px #0ff, 0 0 40px rgba(0,255,255,0.5), 0 0 80px rgba(0,255,255,0.3), 0 0 120px rgba(255,45,149,0.2); }
    25% { color: #ff2d95; text-shadow: 0 0 10px #ff2d95, 0 0 40px rgba(255,45,149,0.5), 0 0 80px rgba(255,45,149,0.3), 0 0 120px rgba(0,255,255,0.2); }
    50% { color: #f5c842; text-shadow: 0 0 10px #f5c842, 0 0 40px rgba(245,200,66,0.5), 0 0 80px rgba(245,200,66,0.3), 0 0 120px rgba(255,45,149,0.2); }
    75% { color: #0ff; text-shadow: 0 0 10px #0ff, 0 0 40px rgba(0,255,255,0.5), 0 0 80px rgba(0,255,255,0.3), 0 0 120px rgba(245,200,66,0.2); }
  }

  .glitch::before,
  .glitch::after {
    content: attr(data-text);
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0.7;
  }

  .glitch::before {
    color: #f0f;
    animation: glitchBefore 3s infinite steps(2);
    clip-path: polygon(0 20%, 100% 20%, 100% 35%, 0 35%);
    text-shadow: 2px 0 #f0f;
  }

  .glitch::after {
    color: #0ff;
    animation: glitchAfter 2.7s infinite steps(2);
    clip-path: polygon(0 65%, 100% 65%, 100% 80%, 0 80%);
    text-shadow: -2px 0 #0ff;
  }

  @keyframes glitchSkew {
    0%, 92%, 100% { transform: skew(0deg); }
    93% { transform: skew(1deg, 0.5deg); }
    94% { transform: skew(-0.5deg); }
    95% { transform: skew(0deg); }
  }

  @keyframes glitchBefore {
    0%, 90%, 100% { transform: translate(0); }
    91% { transform: translate(-4px, 2px); }
    93% { transform: translate(3px, -1px); }
  }

  @keyframes glitchAfter {
    0%, 88%, 100% { transform: translate(0); }
    89% { transform: translate(3px, -2px); }
    91% { transform: translate(-2px, 1px); }
  }

  /* ══════════════════════════════════════
     PLAYER CARD
     ══════════════════════════════════════ */
  .player {
    position: relative;
    z-index: 5;
    width: 100%;
    max-width: 480px;
    background: rgba(10, 11, 36, 0.85);
    border: 2px solid transparent;
    border-radius: 8px;
    overflow: hidden;
    box-shadow:
      0 0 30px rgba(0, 240, 255, 0.15),
      0 0 60px rgba(255, 45, 149, 0.1),
      0 8px 32px rgba(0, 0, 0, 0.5);
    animation: playerBorder 3s linear infinite;
  }

  @keyframes playerBorder {
    0%, 100% { border-color: #0ff; box-shadow: 0 0 30px rgba(0,240,255,0.15), 0 0 60px rgba(255,45,149,0.1), 0 8px 32px rgba(0,0,0,0.5); }
    33% { border-color: #ff2d95; box-shadow: 0 0 30px rgba(255,45,149,0.15), 0 0 60px rgba(0,240,255,0.1), 0 8px 32px rgba(0,0,0,0.5); }
    66% { border-color: #f5c842; box-shadow: 0 0 30px rgba(245,200,66,0.15), 0 0 60px rgba(255,45,149,0.1), 0 8px 32px rgba(0,0,0,0.5); }
  }

  .player__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 12px;
    border-bottom: 1px solid rgba(0, 240, 255, 0.1);
  }

  .player__live-badge {
    font-size: 10px;
    color: #ff2d55;
    letter-spacing: 0.1em;
    animation: livePulse 0.5s infinite alternate ease-in-out, liveHue 2s linear infinite;
  }

  @keyframes livePulse {
    0% { opacity: 0.5; }
    100% { opacity: 1; text-shadow: 0 0 10px rgba(255,45,85,0.8), 0 0 20px rgba(0,240,255,0.4); }
  }

  @keyframes liveHue {
    0%, 100% { color: #ff2d55; }
    33% { color: #f5c842; }
    66% { color: #0ff; }
  }

  .player__viewers {
    font-size: 10px;
    color: rgba(255, 255, 255, 0.4);
    letter-spacing: 0.05em;
  }

  .player__screen {
    position: relative;
    height: 120px;
    background: linear-gradient(180deg, rgba(6,7,26,0.9), rgba(10,11,36,0.95));
    display: flex;
    align-items: flex-end;
    justify-content: center;
    overflow: hidden;
    padding: 0 16px;
  }

  /* Equalizer */
  .equalizer {
    display: flex;
    align-items: flex-end;
    gap: 2px;
    height: 100%;
    padding-bottom: 16px;
    flex: 1;
  }

  .eq-bar {
    flex: 1;
    min-width: 2px;
    background: linear-gradient(0deg, #ff2d55, #ff8c2d, #f5c842, #0ff, #ff2d95);
    background-size: 100% 200%;
    border-radius: 1px 1px 0 0;
    animation: eqBounce ease-in-out infinite alternate, eqRainbow 2s linear infinite;
    height: var(--h, 50%);
    opacity: 0.9;
    box-shadow:
      0 0 6px rgba(255, 45, 149, 0.6),
      0 0 12px rgba(0, 240, 255, 0.3);
  }

  @keyframes eqRainbow {
    0% { background-position: 0% 0%; }
    100% { background-position: 0% 200%; }
  }

  @keyframes eqBounce {
    0% { height: calc(var(--h, 50%) * 0.2); }
    100% { height: var(--h, 50%); }
  }

  /* Spinning record */
  .record {
    position: absolute;
    right: -20px;
    bottom: -20px;
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background: conic-gradient(
      #1a1a2e 0deg 5deg, #222 5deg 10deg, #1a1a2e 10deg 15deg, #222 15deg 20deg,
      #1a1a2e 20deg 25deg, #222 25deg 30deg, #1a1a2e 30deg 35deg, #222 35deg 40deg,
      #1a1a2e 40deg 45deg, #222 45deg 50deg, #1a1a2e 50deg 55deg, #222 55deg 60deg,
      #1a1a2e 60deg 65deg, #222 65deg 70deg, #1a1a2e 70deg 75deg, #222 75deg 80deg,
      #1a1a2e 80deg 85deg, #222 85deg 90deg, #1a1a2e 90deg 95deg, #222 95deg 100deg,
      #1a1a2e 100deg 105deg, #222 105deg 110deg, #1a1a2e 110deg 115deg, #222 115deg 120deg,
      #1a1a2e 120deg 125deg, #222 125deg 130deg, #1a1a2e 130deg 135deg, #222 135deg 140deg,
      #1a1a2e 140deg 145deg, #222 145deg 150deg, #1a1a2e 150deg 155deg, #222 155deg 160deg,
      #1a1a2e 160deg 165deg, #222 165deg 170deg, #1a1a2e 170deg 175deg, #222 175deg 180deg,
      #1a1a2e 180deg 185deg, #222 185deg 190deg, #1a1a2e 190deg 195deg, #222 195deg 200deg,
      #1a1a2e 200deg 205deg, #222 205deg 210deg, #1a1a2e 210deg 215deg, #222 215deg 220deg,
      #1a1a2e 220deg 225deg, #222 225deg 230deg, #1a1a2e 230deg 235deg, #222 235deg 240deg,
      #1a1a2e 240deg 245deg, #222 245deg 250deg, #1a1a2e 250deg 255deg, #222 255deg 260deg,
      #1a1a2e 260deg 265deg, #222 265deg 270deg, #1a1a2e 270deg 275deg, #222 275deg 280deg,
      #1a1a2e 280deg 285deg, #222 285deg 290deg, #1a1a2e 290deg 295deg, #222 295deg 300deg,
      #1a1a2e 300deg 305deg, #222 305deg 310deg, #1a1a2e 310deg 315deg, #222 315deg 320deg,
      #1a1a2e 320deg 325deg, #222 325deg 330deg, #1a1a2e 330deg 335deg, #222 335deg 340deg,
      #1a1a2e 340deg 345deg, #222 345deg 350deg, #1a1a2e 350deg 355deg, #222 355deg 360deg
    );
    animation: recordSpin 4s linear infinite;
    opacity: 0.3;
    filter: blur(1px);
  }

  .record__label {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 30px;
    height: 30px;
    border-radius: 50%;
    background: #ff2d55;
    box-shadow: 0 0 12px rgba(255, 45, 85, 0.4);
  }

  @keyframes recordSpin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

  /* Progress bar */
  .progress {
    position: relative;
    height: 3px;
    background: rgba(255, 255, 255, 0.1);
    margin: 0;
  }

  .progress__fill {
    height: 100%;
    width: 42%;
    background: #ff2d55;
    box-shadow: 0 0 6px rgba(255, 45, 85, 0.6);
    animation: progressPulse 3s infinite alternate ease-in-out;
  }

  @keyframes progressPulse {
    0% { width: 38%; }
    100% { width: 46%; }
  }

  .progress__thumb {
    position: absolute;
    top: 50%;
    left: 42%;
    transform: translate(-50%, -50%);
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: #ff2d55;
    box-shadow: 0 0 8px rgba(255, 45, 85, 0.8);
  }

  .player__time {
    display: flex;
    justify-content: space-between;
    padding: 4px 12px;
    font-size: 9px;
    color: rgba(255, 255, 255, 0.3);
    letter-spacing: 0.05em;
  }

  /* ══════════════════════════════════════
     ACTION BUTTONS
     ══════════════════════════════════════ */
  .actions {
    position: relative;
    z-index: 5;
    display: flex;
    align-items: center;
    gap: 8px;
    flex-wrap: wrap;
    justify-content: center;
  }

  .btn {
    background: rgba(255, 255, 255, 0.05);
    border: 1px solid rgba(255, 255, 255, 0.1);
    color: rgba(255, 255, 255, 0.7);
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 11px;
    letter-spacing: 0.06em;
    cursor: pointer;
    transition: all 0.3s;
    display: flex;
    align-items: center;
    gap: 5px;
    font-family: inherit;
  }

  .btn:hover {
    background: rgba(255, 255, 255, 0.1);
    border-color: rgba(255, 255, 255, 0.25);
  }

  .btn--like {
    background: rgba(0, 240, 255, 0.08);
    border-color: rgba(0, 240, 255, 0.25);
    color: #0ff;
    animation: likeGlow 1.5s infinite alternate ease-in-out, likeHue 3s linear infinite;
  }

  @keyframes likeGlow {
    0% { box-shadow: 0 0 4px rgba(0, 240, 255, 0.3); }
    100% { box-shadow: 0 0 20px rgba(0, 240, 255, 0.6); }
  }

  @keyframes likeHue {
    0%, 100% { color: #0ff; border-color: rgba(0,240,255,0.4); }
    33% { color: #ff2d95; border-color: rgba(255,45,149,0.4); }
    66% { color: #f5c842; border-color: rgba(245,200,66,0.4); }
  }

  .btn__icon {
    font-size: 10px;
  }

  /* ══════════════════════════════════════
     CRT VIGNETTE
     ══════════════════════════════════════ */
  .crt-vignette {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 18;
    background: radial-gradient(
      ellipse at 50% 50%,
      transparent 55%,
      rgba(0, 0, 0, 0.5) 85%,
      rgba(0, 0, 0, 0.85) 100%
    );
  }
</style>
