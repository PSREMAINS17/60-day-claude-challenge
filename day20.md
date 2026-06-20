Day 20 of the claude challenge 

Deployed a face puzzle game with the help of claude AI.
These were the following details that the gave should have contained.
Allow webcam permissions when prompted.
Capture your photo using the camera.
Choose a puzzle difficulty (3×3, 4×4, or 5×5).
Play the puzzle game.
Test drag-and-drop and touch interactions.
Verify timer, move counter, and leaderboard functionality.

Here are some screenshots of the puzzle game:
<img width="1912" height="747" alt="Screenshot 2026-06-20 230533" src="https://github.com/user-attachments/assets/c0193f5d-14be-4ad1-a854-0d4dfd6704df" />
<img width="1915" height="967" alt="Screenshot 2026-06-20 230440" src="https://github.com/user-attachments/assets/5e1d5ce6-ba49-460c-b55a-bf3f92edefed" />
<img width="761" height="633" alt="Screenshot 2026-06-20 231112" src="https://github.com/user-attachments/assets/4c139d5b-c386-4c88-b3ec-77dc9317ec1d" />



[face-puzzle-game.html](https://github.com/user-attachments/files/29165308/face-puzzle-game.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>FacePuzzle</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=Space+Mono:wght@400;700&display=swap');

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --bg: #0a0a0f;
    --surface: #13131a;
    --surface2: #1c1c28;
    --border: #2a2a3d;
    --accent: #7c6aff;
    --accent2: #ff6a9b;
    --accent3: #6affd4;
    --text: #e8e8f0;
    --text2: #8888aa;
    --correct: #6affd4;
    --drag: #ff6a9b;
    --radius: 12px;
    --font: 'Space Grotesk', sans-serif;
    --mono: 'Space Mono', monospace;
  }

  html, body {
    height: 100%;
    background: var(--bg);
    color: var(--text);
    font-family: var(--font);
    overflow-x: hidden;
  }

  body {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }

  /* ─── HEADER ─── */
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 16px 24px;
    border-bottom: 1px solid var(--border);
    background: var(--surface);
    position: sticky;
    top: 0;
    z-index: 100;
  }

  .logo {
    font-size: 1.25rem;
    font-weight: 700;
    letter-spacing: -0.5px;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .logo span { font-weight: 300; }

  /* ─── MAIN ─── */
  main {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 24px 16px 40px;
    gap: 24px;
  }

  /* ─── SCREENS ─── */
  .screen {
    display: none;
    flex-direction: column;
    align-items: center;
    gap: 20px;
    width: 100%;
    max-width: 560px;
  }

  .screen.active { display: flex; }

  /* ─── CAMERA SCREEN ─── */
  .camera-wrap {
    position: relative;
    width: 100%;
    max-width: 480px;
    border-radius: var(--radius);
    overflow: hidden;
    background: #000;
    aspect-ratio: 4/3;
    border: 1px solid var(--border);
  }

  #video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
    transform: scaleX(-1);
  }

  #canvas-snap {
    display: none;
  }

  .camera-overlay {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 12px;
    background: rgba(10,10,15,0.85);
    backdrop-filter: blur(4px);
  }

  .camera-overlay .icon {
    font-size: 3rem;
    line-height: 1;
  }

  .camera-overlay p {
    color: var(--text2);
    font-size: 0.875rem;
    text-align: center;
    padding: 0 20px;
  }

  .face-guide {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
  }

  .face-guide-oval {
    width: 55%;
    height: 80%;
    border: 2px dashed rgba(124,106,255,0.5);
    border-radius: 50%;
    animation: guidePulse 2.5s ease-in-out infinite;
  }

  @keyframes guidePulse {
    0%, 100% { border-color: rgba(124,106,255,0.3); }
    50% { border-color: rgba(124,106,255,0.7); }
  }

  /* ─── DIFFICULTY SCREEN ─── */
  .difficulty-preview {
    width: 100%;
    max-width: 320px;
    border-radius: var(--radius);
    overflow: hidden;
    border: 1px solid var(--border);
  }

  #canvas-preview {
    width: 100%;
    display: block;
  }

  .diff-label {
    font-size: 0.8rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: var(--text2);
    margin-bottom: 4px;
  }

  .diff-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 100%;
  }

  .diff-btn {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 18px 12px;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    transition: all 0.18s ease;
    color: var(--text);
  }

  .diff-btn:hover, .diff-btn.selected {
    border-color: var(--accent);
    background: rgba(124,106,255,0.12);
    transform: translateY(-2px);
  }

  .diff-btn .grid-icon {
    font-size: 1.5rem;
  }

  .diff-btn .grid-name {
    font-size: 0.85rem;
    font-weight: 600;
  }

  .diff-btn .grid-pieces {
    font-size: 0.7rem;
    color: var(--text2);
  }

  /* ─── BUTTONS ─── */
  .btn {
    font-family: var(--font);
    font-size: 0.9rem;
    font-weight: 600;
    border: none;
    border-radius: 8px;
    padding: 12px 24px;
    cursor: pointer;
    transition: all 0.18s ease;
    display: inline-flex;
    align-items: center;
    gap: 8px;
    letter-spacing: 0.01em;
    white-space: nowrap;
  }

  .btn-primary {
    background: var(--accent);
    color: #fff;
    box-shadow: 0 0 20px rgba(124,106,255,0.3);
  }

  .btn-primary:hover:not(:disabled) {
    background: #9483ff;
    box-shadow: 0 0 30px rgba(124,106,255,0.45);
    transform: translateY(-1px);
  }

  .btn-secondary {
    background: var(--surface2);
    color: var(--text);
    border: 1px solid var(--border);
  }

  .btn-secondary:hover:not(:disabled) {
    border-color: var(--accent);
    color: var(--accent);
    transform: translateY(-1px);
  }

  .btn-accent2 {
    background: rgba(255,106,155,0.15);
    color: var(--accent2);
    border: 1px solid rgba(255,106,155,0.3);
  }

  .btn-accent2:hover:not(:disabled) {
    background: rgba(255,106,155,0.25);
    transform: translateY(-1px);
  }

  .btn:disabled {
    opacity: 0.4;
    cursor: not-allowed;
  }

  .btn-row {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    justify-content: center;
  }

  /* ─── PUZZLE SCREEN ─── */
  #puzzle-screen {
    max-width: 680px;
  }

  .puzzle-hud {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 100%;
  }

  .hud-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 12px 14px;
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .hud-label {
    font-size: 0.65rem;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text2);
  }

  .hud-value {
    font-family: var(--mono);
    font-size: 1.1rem;
    font-weight: 700;
    color: var(--text);
  }

  .hud-value.accent { color: var(--accent3); }

  #puzzle-container {
    position: relative;
    user-select: none;
    touch-action: none;
    border-radius: var(--radius);
    overflow: hidden;
    background: var(--surface);
    border: 1px solid var(--border);
  }

  /* ─── PIECES ─── */
  .piece {
    position: absolute;
    cursor: grab;
    transition: box-shadow 0.15s ease;
    background-size: cover;
    background-repeat: no-repeat;
    will-change: transform;
  }

  .piece.dragging {
    cursor: grabbing;
    z-index: 999;
    box-shadow: 0 0 0 3px var(--drag), 0 12px 32px rgba(0,0,0,0.6);
    border-radius: 4px;
  }

  .piece.correct {
    box-shadow: 0 0 0 2px var(--correct);
    border-radius: 2px;
  }

  /* ─── WIN OVERLAY ─── */
  #win-overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(10,10,15,0.92);
    backdrop-filter: blur(8px);
    z-index: 500;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }

  #win-overlay.show { display: flex; }

  .win-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 20px;
    padding: 32px 28px;
    width: 100%;
    max-width: 460px;
    display: flex;
    flex-direction: column;
    gap: 24px;
    position: relative;
    overflow: hidden;
  }

  .win-card::before {
    content: '';
    position: absolute;
    top: -60px;
    left: 50%;
    transform: translateX(-50%);
    width: 220px;
    height: 220px;
    background: radial-gradient(circle, rgba(106,255,212,0.15) 0%, transparent 70%);
    pointer-events: none;
  }

  .win-title {
    text-align: center;
    font-size: 1.8rem;
    font-weight: 700;
    background: linear-gradient(135deg, var(--accent3), var(--accent));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .win-emoji {
    text-align: center;
    font-size: 3rem;
    line-height: 1;
  }

  .win-stats {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
  }

  .win-stat {
    background: var(--surface2);
    border-radius: 10px;
    padding: 14px 10px;
    text-align: center;
    display: flex;
    flex-direction: column;
    gap: 4px;
  }

  .win-stat-label {
    font-size: 0.65rem;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text2);
  }

  .win-stat-value {
    font-family: var(--mono);
    font-size: 1.15rem;
    font-weight: 700;
    color: var(--text);
  }

  /* ─── LEADERBOARD ─── */
  .leaderboard {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  .lb-title {
    font-size: 0.75rem;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--text2);
    padding-bottom: 4px;
    border-bottom: 1px solid var(--border);
  }

  .lb-entry {
    display: grid;
    grid-template-columns: 24px 1fr auto auto auto;
    gap: 8px 12px;
    align-items: center;
    padding: 8px 10px;
    background: var(--surface2);
    border-radius: 8px;
    font-size: 0.8rem;
  }

  .lb-entry.highlight {
    background: rgba(124,106,255,0.15);
    border: 1px solid rgba(124,106,255,0.3);
  }

  .lb-rank {
    font-family: var(--mono);
    font-weight: 700;
    color: var(--text2);
    text-align: center;
  }

  .lb-rank.gold { color: #ffd700; }
  .lb-rank.silver { color: #c0c0c0; }
  .lb-rank.bronze { color: #cd7f32; }

  .lb-date { color: var(--text2); }
  .lb-time { font-family: var(--mono); font-weight: 700; color: var(--accent3); }
  .lb-moves { color: var(--text2); font-family: var(--mono); }
  .lb-diff { color: var(--accent); font-weight: 600; font-size: 0.7rem; }

  .lb-empty {
    text-align: center;
    color: var(--text2);
    font-size: 0.85rem;
    padding: 12px;
  }

  /* ─── CONFETTI ─── */
  #confetti-canvas {
    position: fixed;
    inset: 0;
    pointer-events: none;
    z-index: 490;
  }

  /* ─── SCROLLBAR ─── */
  ::-webkit-scrollbar { width: 6px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }

  /* ─── RESPONSIVE ─── */
  @media (max-width: 480px) {
    .puzzle-hud { grid-template-columns: repeat(3, 1fr); gap: 6px; }
    .hud-card { padding: 8px 10px; }
    .hud-value { font-size: 0.9rem; }
    .win-stats { grid-template-columns: repeat(3, 1fr); gap: 8px; }
    .lb-entry { grid-template-columns: 20px 1fr auto auto; font-size: 0.75rem; gap: 6px 8px; }
    .lb-diff { display: none; }
  }

  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
  }
</style>
</head>
<body>

<header>
  <div class="logo">Face<span>Puzzle</span></div>
  <div id="header-actions" style="display:flex;gap:8px;"></div>
</header>

<main>

  <!-- CAMERA SCREEN -->
  <div id="camera-screen" class="screen active">
    <div class="camera-wrap" id="camera-wrap">
      <video id="video" autoplay playsinline muted></video>
      <div class="face-guide" id="face-guide">
        <div class="face-guide-oval"></div>
      </div>
      <div class="camera-overlay" id="camera-overlay" style="display:none;">
        <div class="icon">📷</div>
        <p id="overlay-msg">Requesting camera access…</p>
        <button class="btn btn-secondary" id="retry-camera-btn" style="display:none;" onclick="initCamera()">Try Again</button>
      </div>
    </div>
    <button class="btn btn-primary" id="snap-btn" disabled onclick="takePhoto()">
      📸 Take Photo
    </button>
    <p style="color:var(--text2);font-size:0.8rem;text-align:center;">Center your face inside the oval, then tap Take Photo</p>
  </div>

  <!-- DIFFICULTY SCREEN -->
  <div id="difficulty-screen" class="screen">
    <div class="difficulty-preview">
      <canvas id="canvas-preview"></canvas>
    </div>
    <div style="width:100%;">
      <p class="diff-label">Choose Difficulty</p>
      <div class="diff-grid">
        <button class="diff-btn" onclick="selectDiff(3,this)">
          <span class="grid-icon">⬛</span>
          <span class="grid-name">Easy</span>
          <span class="grid-pieces">3×3 · 9 pieces</span>
        </button>
        <button class="diff-btn" onclick="selectDiff(4,this)">
          <span class="grid-icon">🟪</span>
          <span class="grid-name">Medium</span>
          <span class="grid-pieces">4×4 · 16 pieces</span>
        </button>
        <button class="diff-btn" onclick="selectDiff(5,this)">
          <span class="grid-icon">🟦</span>
          <span class="grid-name">Hard</span>
          <span class="grid-pieces">5×5 · 25 pieces</span>
        </button>
      </div>
    </div>
    <div class="btn-row">
      <button class="btn btn-secondary" onclick="goToCamera()">↩ Retake Photo</button>
      <button class="btn btn-primary" id="start-btn" disabled onclick="startPuzzle()">Start Puzzle ▶</button>
    </div>
  </div>

  <!-- PUZZLE SCREEN -->
  <div id="puzzle-screen" class="screen">
    <div class="puzzle-hud">
      <div class="hud-card">
        <span class="hud-label">Time</span>
        <span class="hud-value accent" id="hud-time">00:00.0</span>
      </div>
      <div class="hud-card">
        <span class="hud-label">Moves</span>
        <span class="hud-value" id="hud-moves">0</span>
      </div>
      <div class="hud-card">
        <span class="hud-label">Placed</span>
        <span class="hud-value" id="hud-placed">0/<span id="hud-total">9</span></span>
      </div>
    </div>
    <div id="puzzle-container"></div>
    <div class="btn-row">
      <button class="btn btn-accent2" onclick="goToCamera()">📷 New Photo</button>
      <button class="btn btn-secondary" onclick="goToDifficulty()">↩ Change Size</button>
    </div>
  </div>

</main>

<!-- WIN OVERLAY -->
<div id="win-overlay">
  <div class="win-card">
    <div class="win-emoji" id="win-emoji">🎉</div>
    <div class="win-title">Puzzle Complete!</div>
    <div class="win-stats">
      <div class="win-stat">
        <span class="win-stat-label">Time</span>
        <span class="win-stat-value" id="win-time">—</span>
      </div>
      <div class="win-stat">
        <span class="win-stat-label">Moves</span>
        <span class="win-stat-value" id="win-moves">—</span>
      </div>
      <div class="win-stat">
        <span class="win-stat-label">Grid</span>
        <span class="win-stat-value" id="win-diff">—</span>
      </div>
    </div>
    <div class="leaderboard" id="leaderboard-wrap">
      <p class="lb-title">🏆 Best Times</p>
      <div id="leaderboard-list"></div>
    </div>
    <div class="btn-row">
      <button class="btn btn-secondary" onclick="playAgain()">🔄 Play Again</button>
      <button class="btn btn-accent2" onclick="goToCamera()">📷 New Photo</button>
    </div>
  </div>
</div>

<!-- CONFETTI -->
<canvas id="confetti-canvas"></canvas>

<!-- HIDDEN SNAPSHOT CANVAS -->
<canvas id="canvas-snap" style="display:none;"></canvas>

<script>
(function () {
  'use strict';

  /* ════════════════════════════════════════
     STATE
  ════════════════════════════════════════ */
  const S = {
    stream: null,
    photo: null,       // ImageBitmap or null
    photoDataURL: null,
    gridN: 0,
    pieces: [],        // [{ id, correctPos, currentPos, el, x, y }]
    pieceSize: 0,
    containerSize: 0,
    timerStart: null,
    timerRAF: null,
    timerStopped: null,
    moves: 0,
    newHighlight: null, // index of newly set record
    drag: null,
  };

  /* ════════════════════════════════════════
     UTILS
  ════════════════════════════════════════ */
  const $ = id => document.getElementById(id);
  function showScreen(id) {
    ['camera-screen','difficulty-screen','puzzle-screen'].forEach(s => {
      $(s).classList.toggle('active', s === id);
    });
    updateHeaderActions(id);
  }

  function updateHeaderActions(screenId) {
    const wrap = $('header-actions');
    wrap.innerHTML = '';
    if (screenId === 'puzzle-screen') {
      const b = document.createElement('button');
      b.className = 'btn btn-secondary';
      b.style.padding = '8px 14px';
      b.style.fontSize = '0.8rem';
      b.textContent = '🏆 Leaderboard';
      b.onclick = showLeaderboardOnly;
      wrap.appendChild(b);
    }
  }

  function fmtTime(ms) {
    const total = Math.floor(ms / 100);
    const tenths = total % 10;
    const secs = Math.floor(total / 10) % 60;
    const mins = Math.floor(total / 600);
    return `${String(mins).padStart(2,'0')}:${String(secs).padStart(2,'0')}.${tenths}`;
  }

  /* ════════════════════════════════════════
     CAMERA
  ════════════════════════════════════════ */
  async function initCamera() {
    const overlay = $('camera-overlay');
    const msg = $('overlay-msg');
    const retryBtn = $('retry-camera-btn');
    const snapBtn = $('snap-btn');
    const faceGuide = $('face-guide');

    overlay.style.display = 'flex';
    msg.textContent = 'Requesting camera access…';
    retryBtn.style.display = 'none';
    snapBtn.disabled = true;

    if (S.stream) { S.stream.getTracks().forEach(t => t.stop()); S.stream = null; }

    try {
      const stream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: 'user', width: { ideal: 640 }, height: { ideal: 480 } },
        audio: false
      });
      S.stream = stream;
      const video = $('video');
      video.srcObject = stream;
      await new Promise(res => { video.onloadedmetadata = res; });
      await video.play();
      overlay.style.display = 'none';
      faceGuide.style.display = 'flex';
      snapBtn.disabled = false;
    } catch (err) {
      faceGuide.style.display = 'none';
      overlay.style.display = 'flex';
      if (err.name === 'NotAllowedError' || err.name === 'PermissionDeniedError') {
        msg.textContent = 'Camera permission denied. Please allow camera access in your browser settings, then try again.';
      } else if (err.name === 'NotFoundError') {
        msg.textContent = 'No camera found on this device.';
      } else {
        msg.textContent = `Camera error: ${err.message}`;
      }
      retryBtn.style.display = 'inline-flex';
    }
  }

  function takePhoto() {
    const video = $('video');
    const canvas = $('canvas-snap');
    const size = Math.min(video.videoWidth, video.videoHeight);
    const sx = (video.videoWidth - size) / 2;
    const sy = (video.videoHeight - size) / 2;
    canvas.width = 480;
    canvas.height = 480;
    const ctx = canvas.getContext('2d');
    ctx.save();
    ctx.translate(480, 0);
    ctx.scale(-1, 1);
    ctx.drawImage(video, sx, sy, size, size, 0, 0, 480, 480);
    ctx.restore();
    S.photoDataURL = canvas.toDataURL('image/jpeg', 0.9);

    const previewCanvas = $('canvas-preview');
    previewCanvas.width = 480;
    previewCanvas.height = 480;
    previewCanvas.getContext('2d').drawImage(canvas, 0, 0);

    goToDifficulty(true);
  }

  /* ════════════════════════════════════════
     NAVIGATION
  ════════════════════════════════════════ */
  function goToCamera() {
    stopTimer();
    $('win-overlay').classList.remove('show');
    stopConfetti();
    showScreen('camera-screen');
  }

  function goToDifficulty(fromSnap = false) {
    if (!S.photoDataURL && !fromSnap) return;
    S.gridN = 0;
    $('start-btn').disabled = true;
    document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('selected'));
    showScreen('difficulty-screen');
  }

  function selectDiff(n, btn) {
    S.gridN = n;
    document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('selected'));
    btn.classList.add('selected');
    $('start-btn').disabled = false;
  }

  /* ════════════════════════════════════════
     PUZZLE GENERATION
  ════════════════════════════════════════ */
  function startPuzzle() {
    if (!S.gridN) return;
    stopTimer();
    buildPuzzle();
    showScreen('puzzle-screen');
    startTimer();
  }

  function playAgain() {
    $('win-overlay').classList.remove('show');
    stopConfetti();
    buildPuzzle();
    showScreen('puzzle-screen');
    startTimer();
  }

  function buildPuzzle() {
    S.moves = 0;
    S.timerStopped = null;
    $('hud-moves').textContent = '0';
    $('hud-time').textContent = '00:00.0';
    $('hud-total').textContent = S.gridN * S.gridN;

    const container = $('puzzle-container');
    container.innerHTML = '';

    // Responsive size
    const maxW = Math.min(window.innerWidth - 32, 560);
    const size = Math.floor(maxW / S.gridN) * S.gridN;
    S.containerSize = size;
    S.pieceSize = size / S.gridN;

    container.style.width = size + 'px';
    container.style.height = size + 'px';

    // Create image tiles from photoDataURL
    const img = new Image();
    img.src = S.photoDataURL;

    img.onload = function () {
      const N = S.gridN;
      const ps = S.pieceSize;

      // Generate solved order
      const solvedOrder = Array.from({ length: N * N }, (_, i) => i);

      // Scramble (solvable guaranteed via shuffle with parity check for even grids)
      const scrambled = solvableShuffle(solvedOrder.slice(), N);

      S.pieces = [];

      scrambled.forEach((correctId, currentPos) => {
        const col = currentPos % N;
        const row = Math.floor(currentPos / N);
        const x = col * ps;
        const y = row * ps;

        const srcCol = correctId % N;
        const srcRow = Math.floor(correctId / N);

        // Draw piece to offscreen canvas
        const offscreen = document.createElement('canvas');
        offscreen.width = ps;
        offscreen.height = ps;
        const ctx = offscreen.getContext('2d');
        ctx.drawImage(img, srcCol * (img.width / N), srcRow * (img.height / N), img.width / N, img.height / N, 0, 0, ps, ps);
        const dataURL = offscreen.toDataURL('image/jpeg', 0.9);

        const el = document.createElement('div');
        el.className = 'piece';
        el.style.cssText = `width:${ps}px;height:${ps}px;left:${x}px;top:${y}px;background-image:url('${dataURL}');background-size:${ps}px ${ps}px;`;

        container.appendChild(el);

        const piece = { id: correctId, correctPos: correctId, currentPos, el, x, y };
        S.pieces.push(piece);

        attachDrag(el, piece);
        updatePieceCorrect(piece);
      });

      updateHUDPlaced();
    };
  }

  /* ─── Solvable shuffle ─── */
  function solvableShuffle(arr, N) {
    // Fisher-Yates
    for (let i = arr.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
    // For any puzzle (not blank-tile), all permutations are reachable via swaps.
    // We just need to make sure it's not already solved.
    const solved = arr.every((v, i) => v === i);
    if (solved && arr.length > 1) { [arr[0], arr[1]] = [arr[1], arr[0]]; }
    return arr;
  }

  /* ════════════════════════════════════════
     DRAG & DROP
  ════════════════════════════════════════ */
  function attachDrag(el, piece) {
    el.addEventListener('mousedown', e => { e.preventDefault(); dragStart(e.clientX, e.clientY, piece); });
    el.addEventListener('touchstart', e => { e.preventDefault(); const t = e.touches[0]; dragStart(t.clientX, t.clientY, piece); }, { passive: false });
  }

  function dragStart(cx, cy, piece) {
    if (S.drag) return;
    const container = $('puzzle-container');
    const rect = container.getBoundingClientRect();

    S.drag = {
      piece,
      offsetX: cx - rect.left - piece.x,
      offsetY: cy - rect.top - piece.y,
      startX: piece.x,
      startY: piece.y,
    };

    piece.el.classList.add('dragging');
    piece.el.classList.remove('correct');

    const onMove = e => {
      const pos = e.touches ? e.touches[0] : e;
      dragMove(pos.clientX, pos.clientY, rect);
    };
    const onEnd = () => {
      dragEnd();
      document.removeEventListener('mousemove', onMove);
      document.removeEventListener('mouseup', onEnd);
      document.removeEventListener('touchmove', onMove);
      document.removeEventListener('touchend', onEnd);
    };

    document.addEventListener('mousemove', onMove);
    document.addEventListener('mouseup', onEnd);
    document.addEventListener('touchmove', onMove, { passive: false });
    document.addEventListener('touchend', onEnd);
  }

  function dragMove(cx, cy, rect) {
    if (!S.drag) return;
    const { piece, offsetX, offsetY } = S.drag;
    const ps = S.pieceSize;
    const cs = S.containerSize;
    const x = Math.max(0, Math.min(cs - ps, cx - rect.left - offsetX));
    const y = Math.max(0, Math.min(cs - ps, cy - rect.top - offsetY));
    piece.el.style.left = x + 'px';
    piece.el.style.top = y + 'px';
  }

  function dragEnd() {
    if (!S.drag) return;
    const { piece } = S.drag;
    const ps = S.pieceSize;
    const N = S.gridN;
    const el = piece.el;

    // Snap to nearest cell
    const curLeft = parseFloat(el.style.left);
    const curTop = parseFloat(el.style.top);
    const col = Math.round(curLeft / ps);
    const row = Math.round(curTop / ps);
    const clampedCol = Math.max(0, Math.min(N - 1, col));
    const clampedRow = Math.max(0, Math.min(N - 1, row));
    const targetPos = clampedRow * N + clampedCol;

    el.classList.remove('dragging');

    if (targetPos !== piece.currentPos) {
      // Find piece at target
      const targetPiece = S.pieces.find(p => p.currentPos === targetPos);

      if (targetPiece) {
        // Swap positions
        const fromPos = piece.currentPos;
        const fromX = piece.x;
        const fromY = piece.y;

        piece.currentPos = targetPos;
        piece.x = clampedCol * ps;
        piece.y = clampedRow * ps;
        piece.el.style.left = piece.x + 'px';
        piece.el.style.top = piece.y + 'px';

        targetPiece.currentPos = fromPos;
        targetPiece.x = fromX;
        targetPiece.y = fromY;
        targetPiece.el.style.left = fromX + 'px';
        targetPiece.el.style.top = fromY + 'px';

        S.moves++;
        $('hud-moves').textContent = S.moves;

        updatePieceCorrect(piece);
        updatePieceCorrect(targetPiece);
        updateHUDPlaced();
        checkWin();
      } else {
        // Snap back
        el.style.left = piece.x + 'px';
        el.style.top = piece.y + 'px';
        updatePieceCorrect(piece);
      }
    } else {
      el.style.left = piece.x + 'px';
      el.style.top = piece.y + 'px';
      updatePieceCorrect(piece);
    }

    S.drag = null;
  }

  /* ════════════════════════════════════════
     CORRECT DETECTION
  ════════════════════════════════════════ */
  function updatePieceCorrect(piece) {
    const correct = piece.currentPos === piece.correctPos;
    piece.el.classList.toggle('correct', correct);
  }

  function countCorrect() {
    return S.pieces.filter(p => p.currentPos === p.correctPos).length;
  }

  function updateHUDPlaced() {
    $('hud-placed').innerHTML = countCorrect() + '/<span id="hud-total">' + (S.gridN * S.gridN) + '</span>';
  }

  /* ════════════════════════════════════════
     WIN
  ════════════════════════════════════════ */
  function checkWin() {
    if (countCorrect() < S.gridN * S.gridN) return;
    stopTimer();

    const elapsed = S.timerStopped - S.timerStart;
    const timeStr = fmtTime(elapsed);

    $('win-time').textContent = timeStr;
    $('win-moves').textContent = S.moves;
    $('win-diff').textContent = `${S.gridN}×${S.gridN}`;

    S.newHighlight = saveScore(timeStr, S.moves, S.gridN, elapsed);
    renderLeaderboard();

    setTimeout(() => {
      $('win-overlay').classList.add('show');
      launchConfetti();
    }, 300);
  }

  /* ════════════════════════════════════════
     TIMER
  ════════════════════════════════════════ */
  function startTimer() {
    stopTimer();
    S.timerStart = performance.now();
    S.timerStopped = null;

    function tick() {
      const elapsed = performance.now() - S.timerStart;
      $('hud-time').textContent = fmtTime(elapsed);
      S.timerRAF = requestAnimationFrame(tick);
    }
    S.timerRAF = requestAnimationFrame(tick);
  }

  function stopTimer() {
    if (S.timerRAF) { cancelAnimationFrame(S.timerRAF); S.timerRAF = null; }
    if (S.timerStart && !S.timerStopped) { S.timerStopped = performance.now(); }
  }

  /* ════════════════════════════════════════
     LEADERBOARD / LOCALSTORAGE
  ════════════════════════════════════════ */
  const LS_KEY = 'facepuzzle_scores_v2';

  function loadScores() {
    try { return JSON.parse(localStorage.getItem(LS_KEY)) || []; } catch { return []; }
  }

  function saveScore(timeStr, moves, gridN, elapsedMs) {
    const scores = loadScores();
    const entry = {
      timeStr,
      moves,
      diff: `${gridN}×${gridN}`,
      elapsedMs,
      date: new Date().toLocaleDateString(undefined, { month: 'short', day: 'numeric' })
    };
    scores.push(entry);
    scores.sort((a, b) => a.elapsedMs - b.elapsedMs);
    const top5 = scores.slice(0, 5);
    try { localStorage.setItem(LS_KEY, JSON.stringify(top5)); } catch {}
    return top5.indexOf(entry);
  }

  function renderLeaderboard() {
    const list = $('leaderboard-list');
    const scores = loadScores();

    if (!scores.length) {
      list.innerHTML = '<p class="lb-empty">No scores yet — be the first!</p>';
      return;
    }

    const rankEmojis = ['🥇', '🥈', '🥉'];
    const rankClasses = ['gold', 'silver', 'bronze'];

    list.innerHTML = scores.map((s, i) => `
      <div class="lb-entry ${i === S.newHighlight ? 'highlight' : ''}">
        <span class="lb-rank ${rankClasses[i] || ''}">${rankEmojis[i] || (i + 1)}</span>
        <span class="lb-date">${s.date}</span>
        <span class="lb-time">${s.timeStr}</span>
        <span class="lb-moves">${s.moves}mv</span>
        <span class="lb-diff">${s.diff}</span>
      </div>
    `).join('');
  }

  function showLeaderboardOnly() {
    S.newHighlight = -1;
    renderLeaderboard();
    $('win-title') && ($('win-title').textContent = 'Leaderboard');
    $('win-emoji') && ($('win-emoji').textContent = '🏆');
    $('win-overlay').classList.add('show');
    // Hide stats, show only leaderboard
    const stats = document.querySelector('.win-stats');
    if (stats) stats.style.display = 'none';
    $('win-overlay').addEventListener('click', function closer(e) {
      if (e.target === $('win-overlay')) {
        $('win-overlay').classList.remove('show');
        if (stats) stats.style.display = '';
        const title = $('win-title');
        if (title) title.textContent = 'Puzzle Complete!';
        const emoji = $('win-emoji');
        if (emoji) emoji.textContent = '🎉';
        $('win-overlay').removeEventListener('click', closer);
      }
    });
  }

  /* ════════════════════════════════════════
     CONFETTI
  ════════════════════════════════════════ */
  let confettiRAF = null;
  let confettiParticles = [];

  function launchConfetti() {
    const canvas = $('confetti-canvas');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    const ctx = canvas.getContext('2d');
    const colors = ['#7c6aff','#ff6a9b','#6affd4','#ffd700','#ff9f43','#48dbfb'];

    confettiParticles = Array.from({ length: 120 }, () => ({
      x: Math.random() * canvas.width,
      y: -10 - Math.random() * 100,
      vx: (Math.random() - 0.5) * 3,
      vy: 2 + Math.random() * 4,
      rot: Math.random() * 360,
      vrot: (Math.random() - 0.5) * 6,
      w: 8 + Math.random() * 8,
      h: 4 + Math.random() * 4,
      color: colors[Math.floor(Math.random() * colors.length)],
      alpha: 1,
      gravity: 0.08 + Math.random() * 0.05,
    }));

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      let alive = false;
      confettiParticles.forEach(p => {
        p.x += p.vx;
        p.y += p.vy;
        p.vy += p.gravity;
        p.vx *= 0.99;
        p.rot += p.vrot;
        if (p.y > canvas.height * 0.8) p.alpha = Math.max(0, p.alpha - 0.02);
        if (p.alpha > 0) {
          alive = true;
          ctx.save();
          ctx.globalAlpha = p.alpha;
          ctx.translate(p.x, p.y);
          ctx.rotate(p.rot * Math.PI / 180);
          ctx.fillStyle = p.color;
          ctx.fillRect(-p.w / 2, -p.h / 2, p.w, p.h);
          ctx.restore();
        }
      });
      if (alive) confettiRAF = requestAnimationFrame(draw);
      else ctx.clearRect(0, 0, canvas.width, canvas.height);
    }
    if (confettiRAF) cancelAnimationFrame(confettiRAF);
    confettiRAF = requestAnimationFrame(draw);
  }

  function stopConfetti() {
    if (confettiRAF) { cancelAnimationFrame(confettiRAF); confettiRAF = null; }
    const canvas = $('confetti-canvas');
    if (canvas) canvas.getContext('2d').clearRect(0, 0, canvas.width, canvas.height);
  }

  /* ════════════════════════════════════════
     INIT
  ════════════════════════════════════════ */
  window.addEventListener('resize', () => {
    if ($('puzzle-screen').classList.contains('active') && S.pieces.length) {
      buildPuzzle();
    }
  });

  // Expose globals
  window.initCamera = initCamera;
  window.takePhoto = takePhoto;
  window.goToCamera = goToCamera;
  window.goToDifficulty = goToDifficulty;
  window.selectDiff = selectDiff;
  window.startPuzzle = startPuzzle;
  window.playAgain = playAgain;
  window.showLeaderboardOnly = showLeaderboardOnly;

  initCamera();
})();
</script>
</body>
</html>
