<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>TerraMaze: The Ceremonial Labyrinth</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no">
  <style>
    html, body {
      height: 100%;
      margin: 0;
      padding: 0;
      background: linear-gradient(140deg, #281c09 0%, #a67c00 100%);
      color: #191607;
      font-family: 'Segoe UI', Cambria, 'Georgia', serif;
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      box-sizing: border-box;
    }

    #game-container {
      display: flex;
      flex-direction: column;
      align-items: center;
      height: 100vh;
      justify-content: center;
      min-height: 100vh;
      position: relative;
    }

    h1 {
      margin-top: 12px;
      margin-bottom: 0;
      font-size: 2.2rem;
      font-weight: bold;
      letter-spacing: 2px;
      color: #ffd700;
      text-shadow: 0 2px 5px #5b4700bb;
      font-family: 'Georgia', serif;
    }

    .maze-container {
      position: relative;
      margin: 12px 0 0 0;
      background: #333111;
      border-radius: 16px;
      border: 3px solid #a67c00;
      box-shadow: 0 6px 40px #3d3300cc;
      display: flex;
      align-items: center;
      justify-content: center;
      width: 520px;
      height: 520px;
      max-width: 95vw;
      max-height: 65vw;
      touch-action: none;
      user-select: none;
    }

    #maze-canvas {
      display: block;
      width: 500px;
      height: 500px;
      max-width: 92vw;
      max-height: 58vw;
      background: #222113;
      border-radius: 12px;
      margin: 0 auto;
      box-shadow: 0 3px 18px #4b3c05aa;
      outline: none;
    }

    /* ===== Onscreen D-Pad Controls ===== */
    #controls {
      user-select: none;
      -webkit-user-select: none;
      margin: 26px auto 0 auto;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 8px;
    }
    .dpad-row {
      display: flex;
      flex-direction: row;
      gap: 12px;
    }
    .dpad-button {
      width: 64px;
      height: 64px;
      border-radius: 18px;
      background: linear-gradient(145deg, #ffe96a, #ffd700 60%, #b49a31 100%);
      border: 3px solid #a67c00;
      margin: 0;
      outline: none;
      font-size: 1.8rem;
      font-weight: bold;
      color: #1c1605;
      box-shadow: 0 1.5px 10px #aaa32633, 0 3px 1px #b49e42bb;
      cursor: pointer;
      transition: box-shadow 0.12s, background 0.09s;
      display: flex;
      align-items: center;
      justify-content: center;
      touch-action: manipulation;
      position: relative;
      z-index: 10;
    }
    .dpad-button:active, .dpad-button.pressed {
      background: linear-gradient(145deg, #ffd700 70%, #ffea80 100%);
      box-shadow: 0 2px 3px #a67c0088, 0 0.9px 12px #d6be5e99;
    }
    .dpad-button:focus {
      outline: 3px solid #aaa400;
      outline-offset: 2px;
    }
    /* Visually hidden but still accessible to screen readers */
    .sr-only {
      position: absolute !important;
      left: -9999px;
      width: 1px;
      height: 1px;
      overflow: hidden;
    }

    /* ===== Victory Overlay ===== */
    #victory-overlay {
      position: absolute;
      inset: 0;
      width: 100vw;
      min-width: 300px;
      min-height: 340px;
      height: 100vh;
      background: #ffed5f;
      background: linear-gradient(100deg, #fff8d4 0%, #ffe25a 80%, #fff3a1 100%);
      opacity: 0;
      pointer-events: none;
      z-index: 1000;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      transition: opacity 1s ease;
      /* For fade-in via JS/CSS class toggle */
      visibility: hidden;
    }
    #victory-overlay.show {
      opacity: 1;
      visibility: visible;
      pointer-events: auto;
      transition: opacity 1.1s cubic-bezier(.24, .68, 0, 1);
    }
    #victory-overlay h1 {
      color: #181818;
      font-size: 3.3rem;
      font-weight: bold;
      margin: 0 0 1rem 0;
      text-shadow:
        0 1.8px 0 #ffe25a88,
        0 0.2em 0 #ffe89f80,
        0 6px 12px #f9e38340;
      letter-spacing: 3px;
      font-family: 'Georgia', serif;
    }
    #victory-overlay .ritual-line {
      width: 92px;
      height: 6px;
      border-radius: 5px;
      background: linear-gradient(-(5deg), #ffee87 0%, #b49e42 100%);
      margin: 0 0 1.3em 0;
      box-shadow: 0 .5px 6px #c2a62766;
    }
    #victory-overlay p {
      font-size: 1.9rem;
      color: #291e01;
      margin: 8px 0 28px 0;
      font-weight: 500;
      letter-spacing: 2px;
    }

    #play-again-btn {
      margin-top: 14px;
      font-size: 1.4rem;
      font-weight: bold;
      padding: 18px 44px;
      border-radius: 24px;
      color: #fffbe9;
      background: linear-gradient(95deg, #ffd700 8%, #eebb22 60%, #f9e383 100%);
      border: none;
      box-shadow: 0 3px 0 #ad8d11, 0 7px 24px #fff7b433;
      letter-spacing: 1px;
      text-shadow: 0 2px 5px #b49e42bb;
      cursor: pointer;
      outline: none;
      transition: background 0.18s, box-shadow 0.18s;
      display: inline-block;
      position: relative;
    }
    #play-again-btn:active, #play-again-btn.pressed {
      background: linear-gradient(96deg, #f9e383 4%, #ffd700 80%);
      box-shadow: 0 2px 1px #fffde4, 0 3.8px 24px #bea72833;
      color: #ad8d23;
    }
    #play-again-btn:focus {
      outline: 3px solid #ffe176;
      outline-offset: 3px;
    }

    /* Responsive Scaling for Mobile */
    @media (max-width: 800px) {
      .maze-container {
        width: 98vw;
        height: 98vw;
        min-width: 300px;
        min-height: 340px;
        max-width: 98vw;
        max-height: 98vw;
      }
      #maze-canvas {
        width: 94vw;
        height: 94vw;
        min-width: 260px;
        min-height: 280px;
        max-width: 94vw;
        max-height: 94vw;
      }
      #controls .dpad-button { width: 56px; height: 56px; font-size: 1.4rem; }
      #controls { margin: 14px 0 0 0; }
    }
    @media (max-width: 520px) {
      .maze-container, #maze-canvas {
        width: 98vw;
        height: 90vw;
        max-width: 99vw;
        max-height: 76vw;
      }
      #victory-overlay h1 { font-size: 2.2rem; }
      #play-again-btn { font-size: 1rem; padding: 10px 22px; }
      #victory-overlay p { font-size: 1.2rem; }
    }
  </style>
</head>
<body>
<div id="game-container" aria-live="polite">
  <h1>By Empire XTerra<h1>
  </h1>
  <div class="maze-container">
    <canvas id="maze-canvas" width="500" height="500" tabindex="0" aria-label="Labyrinth maze"></canvas>
    <div id="victory-overlay" aria-modal="true" role="dialog">
      <h1>You Win!<h1>
      </h1>TerraMaze By Empire XTerra</h1>
      <div class="ritual-line"></div>
      <p class="sr-only">Congratulations, you solved the ceremonial maze!</p>
      <button id="play-again-btn" aria-label="Play Again">Play Again</button>
    </div>
  </div>
  <div id="controls" aria-label="Direction controls">
    <div class="dpad-row" style="justify-content:center;">
      <button class="dpad-button" id="btn-up" aria-label="Move Up" tabindex="0">&#8593;</button>
    </div>
    <div class="dpad-row">
      <button class="dpad-button" id="btn-left" aria-label="Move Left" tabindex="0">&#8592;</button>
      <div style="width:48px; min-width:12px; opacity:0;"></div>
      <button class="dpad-button" id="btn-right" aria-label="Move Right" tabindex="0">&#8594;</button>
    </div>
    <div class="dpad-row" style="justify-content:center;">
      <button class="dpad-button" id="btn-down" aria-label="Move Down" tabindex="0">&#8595;</button>
    </div>
  </div>
</div>
<script>
/*------------------------------------------------------------------
  MAZE DATA STRUCTURE & GENERATION
-------------------------------------------------------------------*/
const MAZE_ROWS = 25
const MAZE_COLS = 25
let mazeGrid, player, goal, mazeStack;
const WALL_TOP = 0, WALL_RIGHT = 1, WALL_BOTTOM = 2, WALL_LEFT = 3;

/** Cell: Store per-cell info for recursive generator and traversal */
function Cell(row, col) {
  this.row = row;
  this.col = col;
  this.walls = [true, true, true, true]; // top, right, bottom, left
  this.visited = false;
}
/** Utility for random shuffle */
function shuffle(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}
/** Transform (row,col) to flat index for grid */
function cellIndex(row, col) {
  if (row < 0 || col < 0 || row >= MAZE_ROWS || col >= MAZE_COLS) return -1;
  return row * MAZE_COLS + col;
}
/** Return neighbor cells for recursive-backtracking */
function cellNeighbors(cell) {
  const dirs = [
    {dr:-1,dc:0,w0:WALL_TOP,w1:WALL_BOTTOM},
    {dr:0,dc:1,w0:WALL_RIGHT,w1:WALL_LEFT},
    {dr:1,dc:0,w0:WALL_BOTTOM,w1:WALL_TOP},
    {dr:0,dc:-1,w0:WALL_LEFT,w1:WALL_RIGHT}
  ];
  let neighbors = [];
  for (let d = 0; d < dirs.length; ++d) {
    const nr = cell.row + dirs[d].dr, nc = cell.col + dirs[d].dc;
    if (nr >= 0 && nc >= 0 && nr < MAZE_ROWS && nc < MAZE_COLS) {
      const neighbor = mazeGrid[cellIndex(nr, nc)];
      if (!neighbor.visited) neighbors.push({cell: neighbor, dir: d});
    }
  }
  return shuffle(neighbors);
}

/** -- MAZE GENERATION: Recursive Backtracking -- */
function generateMaze() {
  // Step 1: initialize grid
  mazeGrid = [];
  for (let r = 0; r < MAZE_ROWS; ++r)
    for (let c = 0; c < MAZE_COLS; ++c)
      mazeGrid.push(new Cell(r, c));
  // Step 2: recursive backtracking stack
  mazeStack = [];
  let current = mazeGrid[0];
  current.visited = true;
  mazeStack.push(current);
  while (mazeStack.length > 0) {
    let top = mazeStack[mazeStack.length - 1];
    let neighbors = cellNeighbors(top);
    if (neighbors.length > 0) {
      const {cell: next, dir} = neighbors[0];
      // Remove walls between current and next
      top.walls[dir] = false;
      next.walls[[2,3,0,1][dir]] = false; // Opposite wall
      next.visited = true;
      mazeStack.push(next);
    } else {
      mazeStack.pop();
    }
  }
}

/*------------------------------------------------------------------
  PLAYER, GOAL & MOVEMENT LOGIC
-------------------------------------------------------------------*/
function resetPlayerAndGoal() {
  player = {row: 0, col: 0}; // ceremonial: always at start
  goal = {row: MAZE_ROWS - 1, col: MAZE_COLS - 1};
}
/** Movement: (dir = 0=up,1=right,2=down,3=left) */
function movePlayer(dir) {
  if (victoryActive) return;
  const cell = mazeGrid[cellIndex(player.row, player.col)];
  if (cell.walls[dir] === false) {
    let [dRow, dCol] = [[-1,0],[0,1],[1,0],[0,-1]][dir];
    let newRow = player.row + dRow, newCol = player.col + dCol;
    if (newRow >= 0 && newRow < MAZE_ROWS && newCol >= 0 && newCol < MAZE_COLS) {
      player.row = newRow; player.col = newCol;
      drawMaze();
      if (player.row === goal.row && player.col === goal.col) {
        setTimeout(triggerVictory, 250);
      }
    }
  } else {
    // optional: small feedback for wall bump (e.g. flash cell)
  }
}

/*------------------------------------------------------------------
  RENDERING CEREMONIAL MAZE & PIECES
-------------------------------------------------------------------*/
const canvas = document.getElementById('maze-canvas');
const ctx = canvas.getContext('2d');

function drawMaze() {
  // Responsive: match canvas size to parent (.maze-container)
  let size = Math.min(canvas.parentElement.offsetWidth, canvas.parentElement.offsetHeight);
  canvas.width = canvas.height = size;
  // draw
  ctx.clearRect(0,0,size,size);
  ctx.save();
  ctx.translate(8, 8); // padding for visibility
  let cellSize = (size - 16) / MAZE_ROWS;
  // Walls and floor
  ctx.strokeStyle = '#d2ba69';
  ctx.lineWidth = 5;
  ctx.shadowColor = "#94893d";
  ctx.shadowBlur = 2.3;
  for (let r = 0; r < MAZE_ROWS; ++r) for (let c = 0; c < MAZE_COLS; ++c) {
    let cell = mazeGrid[cellIndex(r, c)];
    let x = c * cellSize, y = r * cellSize;
    ctx.beginPath();
    // Each wall only drawn if present
    if (cell.walls[WALL_TOP]) {
      ctx.moveTo(x, y); ctx.lineTo(x + cellSize, y);
    }
    if (cell.walls[WALL_RIGHT]) {
      ctx.moveTo(x + cellSize, y); ctx.lineTo(x + cellSize, y + cellSize);
    }
    if (cell.walls[WALL_BOTTOM]) {
      ctx.moveTo(x + cellSize, y + cellSize); ctx.lineTo(x, y + cellSize);
    }
    if (cell.walls[WALL_LEFT]) {
      ctx.moveTo(x, y + cellSize); ctx.lineTo(x, y);
    }
    ctx.stroke();
  }
  // Goal cell: strong gold
  let goalX = goal.col * cellSize, goalY = goal.row * cellSize;
  ctx.save();
  ctx.globalAlpha = 0.95;
  ctx.fillStyle = "linear-gradient(145deg, #ffe96a 10%, #ffc107 70%, #b49a31 100%)";
  ctx.beginPath();
  ctx.arc(goalX + cellSize/2, goalY + cellSize/2, cellSize*0.32, 0, Math.PI*2);
  ctx.fillStyle = "#ffe358";
  ctx.shadowColor = "#fcf177aa";
  ctx.shadowBlur = 15;
  ctx.fill();
  ctx.restore();

  // Player: ceremonial blue orb with radiant stroke
  let px = player.col * cellSize, py = player.row * cellSize;
  ctx.save();
  ctx.globalAlpha = 1.0;
  ctx.beginPath();
  ctx.arc(px + cellSize/2, py + cellSize/2, cellSize*0.28, 0, Math.PI*2);
  ctx.fillStyle = "radial-gradient(circle at 70% 35%, #82c4ee 45%, #14426e 98%)";
  // fallback for browsers: base blue if no gradient
  ctx.fillStyle = "#2690e0";
  ctx.shadowColor = "#4a82e1bb";
  ctx.shadowBlur = 11;
  ctx.fill();
  // Ceremonial bright aura
  ctx.save();
  ctx.globalAlpha = 0.25;
  ctx.beginPath();
  ctx.arc(px + cellSize/2, py + cellSize/2, cellSize*0.46, 0, Math.PI*2);
  ctx.fillStyle = "#c8e3fa";
  ctx.fill();
  ctx.restore();

  ctx.restore();
  ctx.restore();
}

/*------------------------------------------------------------------
  TOUCH, KEYBOARD, & VICTORY SCREEN LOGIC
-------------------------------------------------------------------*/
let victoryActive = false;

function triggerVictory() {
  victoryActive = true;
  showVictoryOverlay();
}

function showVictoryOverlay() {
  const overlay = document.getElementById('victory-overlay');
  overlay.classList.add('show');
  // Focus 'Play Again' for keyboard accessibility after fade-in
  setTimeout(() => {
    document.getElementById('play-again-btn').focus();
  }, 1100);
}
function hideVictoryOverlay() {
  const overlay = document.getElementById('victory-overlay');
  overlay.classList.remove('show');
  // Accessibility: Restore maze canvas focus
  setTimeout(() => {
    canvas.focus();
  }, 300);
}
function resetMaze() {
  victoryActive = false;
  generateMaze();
  resetPlayerAndGoal();
  drawMaze();
  hideVictoryOverlay();
}

/* Keyboard Controls (Support Arrow + WASD) */
function onKeyDown(evt) {
  if (victoryActive) {
    // Space or Enter on Play Again
    if (evt.key === "Enter" || evt.key === " " || evt.key === "Spacebar") {
      if (document.activeElement.id === "play-again-btn") {
        evt.preventDefault(); resetMaze();
      }
    }
    return;
  }
  let key = evt.key.toLowerCase();
  if (key === "arrowup" || key === "w") { evt.preventDefault(); movePlayer(WALL_TOP);}
  else if (key === "arrowright" || key === "d") { evt.preventDefault(); movePlayer(WALL_RIGHT);}
  else if (key === "arrowdown" || key === "s") { evt.preventDefault(); movePlayer(WALL_BOTTOM);}
  else if (key === "arrowleft" || key === "a") { evt.preventDefault(); movePlayer(WALL_LEFT);}
}
canvas.addEventListener('keydown', onKeyDown, false);
canvas.setAttribute("tabindex","0");

/* Touch / Click / Tap Controls */
function makeButtonHandler(dir) {
  return function(e) {
    if (!victoryActive) movePlayer(dir);
    // For visual feedback
    e.currentTarget.classList.add('pressed');
    setTimeout(() => e.currentTarget.classList.remove('pressed'), 110);
    e.preventDefault && e.preventDefault();
  };
}
document.getElementById('btn-up').addEventListener('touchstart', makeButtonHandler(WALL_TOP), {passive:false});
document.getElementById('btn-up').addEventListener('mousedown', makeButtonHandler(WALL_TOP));
document.getElementById('btn-up').addEventListener('click', makeButtonHandler(WALL_TOP));

document.getElementById('btn-right').addEventListener('touchstart', makeButtonHandler(WALL_RIGHT), {passive:false});
document.getElementById('btn-right').addEventListener('mousedown', makeButtonHandler(WALL_RIGHT));
document.getElementById('btn-right').addEventListener('click', makeButtonHandler(WALL_RIGHT));

document.getElementById('btn-down').addEventListener('touchstart', makeButtonHandler(WALL_BOTTOM), {passive:false});
document.getElementById('btn-down').addEventListener('mousedown', makeButtonHandler(WALL_BOTTOM));
document.getElementById('btn-down').addEventListener('click', makeButtonHandler(WALL_BOTTOM));

document.getElementById('btn-left').addEventListener('touchstart', makeButtonHandler(WALL_LEFT), {passive:false});
document.getElementById('btn-left').addEventListener('mousedown', makeButtonHandler(WALL_LEFT));
document.getElementById('btn-left').addEventListener('click', makeButtonHandler(WALL_LEFT));

['btn-up','btn-down','btn-left','btn-right'].forEach(id => {
  const btn = document.getElementById(id);
  // Keyboard navigation: Space/Enter triggers button
  btn.addEventListener('keydown', function(e) {
    if (e.key === " " || e.key === "Enter" || e.key === "Spacebar") {
      e.preventDefault(); this.classList.add('pressed');
      setTimeout(() => this.classList.remove('pressed'), 90);
      makeButtonHandler(
        { 'btn-up': WALL_TOP, 'btn-down': WALL_BOTTOM, 'btn-left': WALL_LEFT, 'btn-right': WALL_RIGHT }[id]
      )(e);
    }
  });
});

/* Hide scrolling when pressing D-pad on mobile */
document.getElementById("controls").addEventListener('touchmove',function(e){e.preventDefault();},{passive:false});

/* Play Again button */
const playAgainBtn = document.getElementById('play-again-btn');
playAgainBtn.addEventListener('touchend', function(e){ e.preventDefault(); resetMaze(); });
playAgainBtn.addEventListener('click', resetMaze);
playAgainBtn.addEventListener('keydown', function(e) {
  if (e.key === " " || e.key === "Enter" || e.key === "Spacebar") {
    e.preventDefault(); resetMaze();
  }
});
/*------------------------------------------------------------------
  INIT
-------------------------------------------------------------------*/
// Window resize: redraw with new scale
window.addEventListener('resize', drawMaze);
// Initial generation & render
generateMaze();
resetPlayerAndGoal();
drawMaze();
// Focus canvas for keyboard listeners
canvas.focus();
</script>
</body>
</html>

