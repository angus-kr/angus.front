<script>
  import { onMount } from 'svelte';

  let daysSince = $state(0);
  let canvasEl = $state(null);
  let portraitReady = $state(false);

  $effect(() => {
    const past = new Date(2009, 4, 23);
    daysSince = Math.floor((new Date() - past) / (1000 * 60 * 60 * 24));
  });

  // ── Canvas portrait renderer ──
  onMount(() => {
    const W = 38, H = 48, PX = 5;
    const grid = new Array(W * H).fill(null);

    function plot(x, y, c) { if (x >= 0 && x < W && y >= 0 && y < H) grid[y * W + x] = c; }

    function fillOval(cx, cy, rx, ry, color) {
      for (let y = cy - ry; y <= cy + ry; y++)
        for (let x = cx - rx; x <= cx + rx; x++) {
          const dx = (x - cx) / rx, dy = (y - cy) / ry;
          if (dx * dx + dy * dy <= 1) plot(x, y, color);
        }
    }

    function fillCircle(cx, cy, r, color) { fillOval(cx, cy, r, r, color); }

    function fillRect(x1, y1, x2, y2, color) {
      for (let y = y1; y <= y2; y++)
        for (let x = x1; x <= x2; x++)
          plot(x, y, color);
    }

    function strokeOval(cx, cy, rx, ry, color, t = 1) {
      for (let y = cy - ry - t; y <= cy + ry + t; y++)
        for (let x = cx - rx - t; x <= cx + rx + t; x++) {
          const d = Math.pow((x - cx) / (rx + t * 0.5), 2) + Math.pow((y - cy) / (ry + t * 0.5), 2);
          const i = Math.pow((x - cx) / rx, 2) + Math.pow((y - cy) / ry, 2);
          if (d <= 1 && i > 1) plot(x, y, color);
        }
    }

    function hLine(x1, x2, y, color) { fillRect(x1, y, x2, y, color); }

    // Palette
    const C = {
      skin:    '#e8c9a0',
      skinD:   '#d4ad80',
      skinD2:  '#c09068',
      skinL:   '#f0dabb',
      hair:    '#c8c8c8',
      hairD:   '#999',
      hairL:   '#e0e0e0',
      scalp:   '#e0c8a8',
      brow:    '#888',
      glass:   '#2a2a2a',
      lens:    '#dcc8b4',
      lensG:   '#f0e8e0',
      eye:     '#2a1810',
      pupil:   '#0a0a0a',
      eyeW:    '#f8f4f0',
      mouth:   '#c08068',
      mouthL:  '#a06050',
      tooth:   '#f8f0e8',
      suit:    '#1a1a2e',
      shirt:   '#f0ece8',
      tie:     '#283050',
    };

    // ── Draw ──

    // Neck
    fillRect(14, 42, 23, 47, C.skinD2);
    fillRect(15, 40, 22, 43, C.skinD);

    // Head base
    fillOval(18, 24, 13, 18, C.skin);
    fillOval(14, 22, 6, 12, C.skinL);   // left highlight
    fillOval(24, 24, 5, 14, C.skinD);   // right shadow

    // Scalp
    fillOval(18, 12, 8, 6, C.scalp);
    fillOval(18, 10, 6, 4, C.scalp);

    // Hair
    fillOval(7, 20, 4, 12, C.hairD);
    fillOval(8, 18, 4, 11, C.hair);
    fillOval(29, 20, 4, 12, C.hairD);
    fillOval(28, 18, 4, 11, C.hair);
    fillOval(10, 11, 5, 5, C.hairL);
    fillOval(26, 11, 5, 5, C.hairL);
    fillOval(10, 9, 4, 4, C.hair);
    fillOval(26, 9, 4, 4, C.hair);
    fillOval(6, 16, 3, 6, C.hairD);
    fillOval(30, 16, 3, 6, C.hairD);

    // Forehead
    fillOval(18, 17, 8, 4, C.skin);

    // Eyebrows
    fillOval(13, 24, 4, 1.5, C.brow);
    fillOval(13, 23, 3.5, 1, C.brow);
    fillOval(23, 24, 4, 1.5, C.brow);
    fillOval(23, 23, 3.5, 1, C.brow);

    // Glasses frames
    strokeOval(13, 29, 6, 5.5, C.glass, 1.8);
    strokeOval(23, 29, 6, 5.5, C.glass, 1.8);
    // Bridge
    hLine(18, 20, 28, C.glass);
    hLine(18, 20, 29, C.glass);

    // Lens tint
    fillOval(13, 29, 5, 4.5, C.lens);
    fillOval(23, 29, 5, 4.5, C.lens);
    // Lens glint
    fillOval(11, 27, 1.5, 1, C.lensG);
    fillOval(21, 27, 1.5, 1, C.lensG);

    // Eyes
    fillOval(13, 29, 2.5, 2, C.eyeW);
    fillOval(23, 29, 2.5, 2, C.eyeW);
    fillCircle(13, 29, 1.5, C.eye);
    fillCircle(23, 29, 1.5, C.eye);
    fillCircle(13, 29, 0.8, C.pupil);
    fillCircle(23, 29, 0.8, C.pupil);

    // Nose
    fillOval(18, 34, 3, 2.5, '#d0a880');
    fillOval(18, 35, 2.5, 2, '#d0a880');
    plot(16, 35, C.skinD);
    plot(20, 35, C.skinD);

    // Under-nose
    fillOval(18, 37, 2, 1, C.skinD);

    // Mouth
    fillOval(18, 39, 4, 2, C.mouth);
    fillRect(16, 38, 20, 39, C.tooth);
    fillOval(18, 39, 3, 1.2, C.mouthL);

    // Smile lines
    fillOval(13, 38, 1.5, 2.5, C.skinD);
    fillOval(23, 38, 1.5, 2.5, C.skinD);

    // Ears
    fillOval(6, 26, 3, 5, C.skinD);
    fillOval(6, 26, 2.5, 4, C.skin);
    fillOval(30, 26, 3, 5, C.skinD);
    fillOval(30, 26, 2.5, 4, C.skin);

    // Cheeks
    fillOval(11, 33, 3, 3, C.skinL);
    fillOval(25, 33, 3, 3, C.skinL);

    // Chin
    fillOval(18, 40, 5, 3, C.skin);
    fillOval(18, 41, 4, 2, C.skinD);

    // Suit
    fillRect(4, 44, 32, 47, C.suit);
    fillRect(3, 45, 6, 47, C.suit);
    fillRect(30, 45, 33, 47, C.suit);

    // Shirt collar
    fillRect(15, 43, 21, 44, C.shirt);
    fillRect(14, 44, 22, 44, C.shirt);
    fillRect(16, 42, 17, 42, C.shirt);
    fillRect(19, 42, 20, 42, C.shirt);
    plot(15, 42, C.shirt);
    plot(21, 42, C.shirt);

    // Tie
    fillRect(17, 43, 19, 47, C.tie);
    fillRect(18, 42, 18, 43, C.tie);
    fillRect(17, 42, 19, 43, C.tie);

    // ── Render to canvas ──
    const scale = 5;
    const canvas = canvasEl;
    if (!canvas) return;
    canvas.width = W * scale;
    canvas.height = H * scale;
    const ctx = canvas.getContext('2d');
    ctx.imageSmoothingEnabled = false;

    for (let y = 0; y < H; y++) {
      for (let x = 0; x < W; x++) {
        const color = grid[y * W + x];
        if (color) {
          ctx.fillStyle = color;
          ctx.fillRect(x * scale, y * scale, scale, scale);
        }
      }
    }

    // Add subtle rounded corners by drawing over corner pixels
    // (Skip - pixel art looks fine crisp)

    portraitReady = true;
  });

  // Owls
  const owls = Array.from({ length: 4 }, (_, i) => ({
    id: i,
    top: 15 + Math.random() * 65,
    delay: i * 4 + Math.random() * 2,
    duration: 14 + Math.random() * 12,
    scale: 0.5 + Math.random() * 0.7,
    flip: i % 2 === 0
  }));
</script>

<div class="stage">
  <div class="nebula nebula--1"></div>
  <div class="nebula nebula--2"></div>
  <div class="stars"></div>

  <!-- God rays -->
  {#each Array.from({ length: 12 }, (_, i) => i) as i}
    <div
      class="ray"
      style="--angle: {(i / 12) * 360}deg; animation-delay: {Math.random() * 5}s"
    ></div>
  {/each}

  <!-- Flying owls -->
  {#each owls as owl (owl.id)}
    <div
      class="owl {owl.flip ? 'owl--flip' : ''}"
      style="
        top: {owl.top}%;
        animation-delay: {owl.delay}s;
        animation-duration: {owl.duration}s;
        --s: {owl.scale};
      "
    >
      <div class="owl__body">
        <div class="owl__wing owl__wing--l"></div>
        <div class="owl__wing owl__wing--r"></div>
        <div class="owl__head">
          <div class="owl__eye owl__eye--l"></div>
          <div class="owl__eye owl__eye--r"></div>
          <div class="owl__beak"></div>
        </div>
        <div class="owl__tail"></div>
      </div>
    </div>
  {/each}

  <!-- Portrait -->
  <div class="portrait-frame">
    <canvas bind:this={canvasEl} class="portrait-canvas"></canvas>
  </div>

  <!-- Text -->
  <div class="text-area">
    <h1 class="title">바보 노무현</h1>
    <p class="counter">우리 곁을 떠난 지 <em>{daysSince.toLocaleString()}</em>일</p>
    <p class="url"><a href="https://mc.angus.kr">mc.angus.kr</a></p>
  </div>
</div>

<style>
  .stage {
    min-height: 100vh;
    background: #0a0806;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 24px;
    overflow: hidden;
    position: relative;
    padding: 40px 24px;
  }

  /* nebula */
  .nebula { position: absolute; inset: 0; pointer-events: none; }
  .nebula--1 {
    background: radial-gradient(ellipse at 50% 28%, rgba(245,200,66,0.05) 0%, transparent 50%),
                radial-gradient(ellipse at 30% 60%, rgba(200,150,40,0.025) 0%, transparent 40%),
                radial-gradient(ellipse at 70% 50%, rgba(245,180,50,0.03) 0%, transparent 45%);
    animation: drift 20s infinite alternate ease-in-out;
  }
  .nebula--2 {
    background: radial-gradient(circle at 50% 22%, rgba(245,200,66,0.06) 0%, transparent 35%);
    animation: pulse 8s infinite alternate ease-in-out;
  }
  @keyframes drift {
    0% { transform: translate(0,0) scale(1); }
    50% { transform: translate(12px,-8px) scale(1.04); }
    100% { transform: translate(-8px,6px) scale(0.98); }
  }
  @keyframes pulse {
    0% { opacity: 0.5; }
    100% { opacity: 1; }
  }

  /* stars */
  .stars {
    position: absolute; inset: 0; pointer-events: none;
    width: 2px; height: 2px; border-radius: 50%; background: #fff;
    animation: twinkle 4s infinite alternate ease-in-out;
    box-shadow:
      100px 40px 0 rgba(255,255,255,0.8), 300px 100px 0 rgba(255,255,255,0.6),
      520px 60px 0 rgba(255,255,255,0.7), 750px 180px 0 rgba(255,255,255,0.5),
      860px 50px 0 rgba(255,255,255,0.9), 1000px 260px 0 rgba(255,255,255,0.4),
      40px 260px 0 rgba(255,255,255,0.6), 200px 380px 0 rgba(255,255,255,0.7),
      380px 320px 0 rgba(255,255,255,0.5), 620px 400px 0 rgba(255,255,255,0.8),
      880px 360px 0 rgba(255,255,255,0.6), 1030px 420px 0 rgba(255,255,255,0.4),
      60px 500px 0 rgba(255,255,255,0.5), 280px 540px 0 rgba(255,255,255,0.7),
      520px 580px 0 rgba(255,255,255,0.6), 720px 510px 0 rgba(255,255,255,0.8),
      960px 560px 0 rgba(255,255,255,0.5), 140px 660px 0 rgba(255,255,255,0.4),
      400px 700px 0 rgba(255,255,255,0.6), 650px 660px 0 rgba(255,255,255,0.7),
      840px 680px 0 rgba(255,255,255,0.5), 980px 640px 0 rgba(255,255,255,0.8),
      180px 50px 0 rgba(255,255,255,0.5), 460px 25px 0 rgba(255,255,255,0.7),
      680px 140px 0 rgba(255,255,255,0.4), 90px 140px 0 rgba(255,255,255,0.6),
      360px 190px 0 rgba(255,255,255,0.8), 580px 240px 0 rgba(255,255,255,0.5),
      810px 150px 0 rgba(255,255,255,0.7), 930px 220px 0 rgba(255,255,255,0.6),
      50px 340px 0 rgba(255,255,255,0.4), 250px 300px 0 rgba(255,255,255,0.7),
      480px 270px 0 rgba(255,255,255,0.5), 700px 340px 0 rgba(255,255,255,0.8),
      870px 280px 0 rgba(255,255,255,0.6), 110px 600px 0 rgba(255,255,255,0.7),
      340px 640px 0 rgba(255,255,255,0.5), 490px 610px 0 rgba(255,255,255,0.8),
      720px 630px 0 rgba(255,255,255,0.6), 850px 580px 0 rgba(255,255,255,0.4),
      950px 610px 0 rgba(255,255,255,0.7), 640px 35px 0 rgba(255,255,255,0.8),
      920px 35px 0 rgba(255,255,255,0.5), 35px 85px 0 rgba(255,255,255,0.6),
      510px 95px 0 rgba(255,255,255,0.4), 55px 520px 0 rgba(255,255,255,0.7);
  }
  @keyframes twinkle {
    0% { opacity: 0.5; }
    50% { opacity: 0.85; }
    100% { opacity: 0.4; }
  }

  /* god rays */
  .ray {
    position: absolute; top: 50%; left: 50%;
    width: 2px; height: 600px;
    margin-left: -1px; margin-top: -320px;
    background: linear-gradient(0deg, rgba(245,200,66,0.12) 0%, rgba(245,200,66,0.03) 60%, transparent 100%);
    transform-origin: bottom center;
    animation: raySpin 30s linear infinite, rayPulse 3s infinite alternate ease-in-out;
    pointer-events: none; z-index: 0;
  }
  @keyframes raySpin {
    0% { transform: rotate(var(--angle)) scaleY(1); }
    100% { transform: rotate(calc(var(--angle) + 360deg)) scaleY(1); }
  }
  @keyframes rayPulse {
    0% { opacity: 0.3; }
    100% { opacity: 0.7; }
  }

  /* ── OWL ── */
  .owl {
    position: absolute;
    left: -80px;
    z-index: 4;
    pointer-events: none;
    animation: owlFly linear infinite;
    transform: scale(var(--s, 1));
    opacity: 0.6;
  }

  .owl--flip {
    left: auto;
    right: -80px;
    animation-name: owlFlyReverse;
  }

  @keyframes owlFly {
    0%   { transform: translateX(0) translateY(0) scale(var(--s,1)); opacity: 0; }
    10%  { opacity: 0.6; }
    25%  { transform: translateX(25vw) translateY(-15px) scale(var(--s,1)); }
    50%  { transform: translateX(50vw) translateY(-30px) scale(var(--s,1)); }
    75%  { transform: translateX(75vw) translateY(-15px) scale(var(--s,1)); }
    90%  { opacity: 0.5; }
    100% { transform: translateX(105vw) translateY(0) scale(var(--s,1)); opacity: 0; }
  }

  @keyframes owlFlyReverse {
    0%   { transform: translateX(0) translateY(0) scale(var(--s,1)); opacity: 0; }
    10%  { opacity: 0.6; }
    25%  { transform: translateX(-25vw) translateY(-15px) scale(var(--s,1)); }
    50%  { transform: translateX(-50vw) translateY(-30px) scale(var(--s,1)); }
    75%  { transform: translateX(-75vw) translateY(-15px) scale(var(--s,1)); }
    90%  { opacity: 0.5; }
    100% { transform: translateX(-105vw) translateY(0) scale(var(--s,1)); opacity: 0; }
  }

  .owl__body {
    position: relative;
    width: 36px;
    height: 32px;
    background: #3a3028;
    border-radius: 50%;
    animation: owlBob 0.6s infinite alternate ease-in-out;
  }

  @keyframes owlBob {
    0% { transform: translateY(0); }
    100% { transform: translateY(-3px); }
  }

  .owl__wing {
    position: absolute;
    top: 2px;
    width: 18px;
    height: 22px;
    background: #4a3d32;
    border-radius: 50% 50% 30% 30%;
    animation: wingFlap 0.3s infinite alternate ease-in-out;
    transform-origin: top center;
  }

  .owl__wing--l {
    left: -10px;
    animation-name: wingFlapL;
  }

  .owl__wing--r {
    right: -10px;
    animation-name: wingFlapR;
  }

  @keyframes wingFlapL {
    0% { transform: rotate(15deg); }
    100% { transform: rotate(-30deg); }
  }

  @keyframes wingFlapR {
    0% { transform: rotate(-15deg); }
    100% { transform: rotate(30deg); }
  }

  .owl__head {
    position: absolute;
    top: -10px;
    left: 50%;
    transform: translateX(-50%);
    width: 26px;
    height: 24px;
    background: #3a3028;
    border-radius: 50%;
  }

  /* ear tufts */
  .owl__head::before,
  .owl__head::after {
    content: '';
    position: absolute;
    top: -5px;
    width: 5px;
    height: 8px;
    background: #4a3d32;
    clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
  }

  .owl__head::before { left: 2px; }
  .owl__head::after { right: 2px; }

  .owl__eye {
    position: absolute;
    top: 6px;
    width: 8px;
    height: 8px;
    background: #f5c842;
    border-radius: 50%;
    box-shadow: 0 0 3px rgba(245,200,66,0.8);
    animation: owlBlink 4s infinite;
  }

  @keyframes owlBlink {
    0%, 90%, 100% { transform: scaleY(1); }
    95% { transform: scaleY(0.1); }
  }

  .owl__eye--l { left: 3px; }
  .owl__eye--r { right: 3px; }

  .owl__beak {
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    width: 5px;
    height: 5px;
    background: #c8a050;
    clip-path: polygon(50% 100%, 0% 0%, 100% 0%);
  }

  .owl__tail {
    position: absolute;
    bottom: -4px;
    left: 50%;
    transform: translateX(-50%);
    width: 10px;
    height: 7px;
    background: #2a2218;
    border-radius: 0 0 4px 4px;
  }

  /* ── PORTRAIT ── */
  .portrait-frame {
    position: relative;
    z-index: 5;
    filter: drop-shadow(0 0 40px rgba(245,200,66,0.15))
            drop-shadow(0 8px 32px rgba(0,0,0,0.5));
    animation: portraitFloat 4s infinite alternate ease-in-out;
  }

  @keyframes portraitFloat {
    0% { transform: translateY(0); }
    100% { transform: translateY(-6px); }
  }

  .portrait-canvas {
    display: block;
    image-rendering: pixelated;
    border-radius: 12px;
  }

  /* ── TEXT ── */
  .text-area {
    position: relative;
    z-index: 5;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 10px;
  }

  .title {
    font-family: 'Noto Serif KR', serif;
    font-size: 40px;
    font-weight: 700;
    letter-spacing: 0.08em;
    background: linear-gradient(180deg, #f5c842 0%, #d4a830 40%, #b8922a 100%);
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    filter: drop-shadow(0 0 20px rgba(245,200,66,0.3));
    animation: titleGlow 2s infinite alternate ease-in-out;
  }

  @keyframes titleGlow {
    0% { filter: drop-shadow(0 0 18px rgba(245,200,66,0.25)); }
    100% { filter: drop-shadow(0 0 32px rgba(245,200,66,0.45)); }
  }

  .counter {
    font-size: 14px;
    color: #8a8070;
    letter-spacing: 0.04em;
  }

  .counter em {
    font-style: normal;
    color: #c4a830;
    font-weight: 500;
  }

  .url { margin-top: 8px; }

  .url a {
    font-size: 11px;
    color: #4a4030;
    text-decoration: none;
    letter-spacing: 0.08em;
    transition: color 0.3s;
  }

  .url a:hover { color: #8a7a50; }
</style>
