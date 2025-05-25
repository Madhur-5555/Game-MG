<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mini Fighter Game</title>
  <style>
    body { margin: 0; background: #111; color: white; text-align: center; font-family: sans-serif; }
    canvas { background: #222; display: block; margin: 20px auto; }
    #startBtn, #restartBtn {
      padding: 10px 20px; background: limegreen; border: none; font-size: 18px; cursor: pointer;
    }
    #endScreen { display: none; position: absolute; top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.8); justify-content: center; align-items: center; flex-direction: column; }
  </style>
</head>
<body>
  <h1>Mini Fighter Game</h1>
  <button id="startBtn">Start Game</button>
  <div id="endScreen">
    <h2 id="resultText"></h2>
    <button id="restartBtn">Restart</button>
  </div>
  <canvas id="gameCanvas" width="600" height="300"></canvas>
  <audio id="hitSound" src="https://cdn.pixabay.com/download/audio/2023/03/06/audio_4c3f8c3387.mp3?filename=punch-140236.mp3"></audio>
  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const startBtn = document.getElementById('startBtn');
    const endScreen = document.getElementById('endScreen');
    const resultText = document.getElementById('resultText');
    const restartBtn = document.getElementById('restartBtn');
    const hitSound = document.getElementById('hitSound');let gameRunning = false;
const p1 = { x: 50, y: 250, color: 'red', health: 100 };
const p2 = { x: 500, y: 250, color: 'blue', health: 100 };

function drawPlayers() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = p1.color;
  ctx.fillRect(p1.x, p1.y - 50, 30, 50);
  ctx.fillStyle = p2.color;
  ctx.fillRect(p2.x, p2.y - 50, 30, 50);
}

function drawHealthBars() {
  ctx.fillStyle = 'lime';
  ctx.fillRect(20, 20, p1.health * 2, 10);
  ctx.fillStyle = 'aqua';
  ctx.fillRect(350, 20, p2.health * 2, 10);
}

function checkWinner() {
  if (p1.health <= 0 || p2.health <= 0) {
    gameRunning = false;
    resultText.textContent = p1.health <= 0 ? "Player 2 Wins!" : "Player 1 Wins!";
    endScreen.style.display = 'flex';
  }
}

function attack(attacker, defender) {
  if (Math.abs(attacker.x - defender.x) < 50) {
    defender.health -= 10;
    hitSound.currentTime = 0;
    hitSound.play();
  }
}

function gameLoop() {
  if (!gameRunning) return;
  drawPlayers();
  drawHealthBars();
  checkWinner();
  requestAnimationFrame(gameLoop);
}

document.addEventListener('keydown', (e) => {
  if (!gameRunning) return;
  if (e.key === 'a') p1.x -= 10;
  if (e.key === 'd') p1.x += 10;
  if (e.key === 'w') attack(p1, p2);
  if (e.key === 'ArrowLeft') p2.x -= 10;
  if (e.key === 'ArrowRight') p2.x += 10;
  if (e.key === 'ArrowUp') attack(p2, p1);
});

startBtn.onclick = () => {
  startBtn.style.display = 'none';
  gameRunning = true;
  p1.health = 100;
  p2.health = 100;
  p1.x = 50;
  p2.x = 500;
  gameLoop();
};

restartBtn.onclick = () => {
  endScreen.style.display = 'none';
  startBtn.click();
};

  </script>
</body>
</html>
