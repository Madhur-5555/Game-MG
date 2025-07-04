<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Mini Subway Surfer</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { overflow: hidden; background: #222; font-family: sans-serif; }
    canvas { display: block; margin: auto; background: linear-gradient(#555, #222); }
    .controls {
      position: absolute;
      bottom: 20px;
      left: 50%;
      transform: translateX(-50%);
      display: flex;
      gap: 10px;
      z-index: 10;
    }
    .btn {
      padding: 15px 20px;
      background: #00c3ff;
      border: none;
      color: white;
      font-size: 18px;
      border-radius: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.5);
      cursor: pointer;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>
  <div class="controls">
    <button class="btn" id="leftBtn">◀️</button>
    <button class="btn" id="jumpBtn">⬆️</button>
    <button class="btn" id="rightBtn">▶️</button>
  </div>
  <audio id="jumpSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_0d6ed3f62b.mp3?filename=jump-144349.mp3"></audio>  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const jumpSound = document.getElementById('jumpSound');

    const playerImg = new Image();
    playerImg.src = "https://i.ibb.co/sFdtYYM/runner.png"; // cartoon style runner

    const player = { x: 180, y: 500, width: 40, height: 60, vy: 0, jump: false };
    let obstacles = [];
    let score = 0;
    let gravity = 0.5;
    let gameSpeed = 3;
    let gameOver = false;

    function drawPlayer() {
      ctx.drawImage(playerImg, player.x, player.y, player.width, player.height);
    }

    function drawObstacles() {
      ctx.fillStyle = 'crimson';
      for (let obs of obstacles) {
        ctx.fillRect(obs.x, obs.y, obs.width, obs.height);
      }
    }

    function spawnObstacle() {
      const x = Math.floor(Math.random() * 3) * 130 + 10;
      obstacles.push({ x: x, y: -50, width: 40, height: 40 });
    }

    function updateObstacles() {
      for (let obs of obstacles) {
        obs.y += gameSpeed;
        if (
          obs.x < player.x + player.width &&
          obs.x + obs.width > player.x &&
          obs.y < player.y + player.height &&
          obs.y + obs.height > player.y
        ) {
          gameOver = true;
        }
      }
      obstacles = obstacles.filter(o => o.y < canvas.height);
    }

    function drawScore() {
      ctx.fillStyle = 'white';
      ctx.font = '20px Arial';
      ctx.fillText('Score: ' + score, 10, 30);
    }

    function gameLoop() {
      if (gameOver) {
        ctx.fillStyle = 'black';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = 'red';
        ctx.font = '40px Arial';
        ctx.fillText('Game Over!', 100, 250);
        ctx.fillStyle = 'white';
        ctx.font = '20px Arial';
        ctx.fillText('Score: ' + score, 150, 300);
        return;
      }

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      player.vy += gravity;
      player.y += player.vy;

      if (player.y > 500) {
        player.y = 500;
        player.jump = false;
      }

      drawPlayer();
      drawObstacles();
      updateObstacles();
      drawScore();

      score++;
      if (score % 50 === 0) spawnObstacle();

      requestAnimationFrame(gameLoop);
    }

    function moveLeft() {
      if (player.x > 10) player.x -= 130;
    }
    function moveRight() {
      if (player.x < 260) player.x += 130;
    }
    function jump() {
      if (!player.jump) {
        player.vy = -10;
        player.jump = true;
        jumpSound.currentTime = 0;
        jumpSound.play();
      }
    }

    // Button controls
    document.getElementById('leftBtn').addEventListener('click', moveLeft);
    document.getElementById('rightBtn').addEventListener('click', moveRight);
    document.getElementById('jumpBtn').addEventListener('click', jump);

    // Keyboard controls
    document.addEventListener('keydown', (e) => {
      if (e.key === 'ArrowLeft') moveLeft();
      if (e.key === 'ArrowRight') moveRight();
      if (e.key === ' ') jump();
    });

    gameLoop();
  </script></body>
</html>
