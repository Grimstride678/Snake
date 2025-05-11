<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Snake Game</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(to bottom, #111, #222);
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    h1 {
      margin-top: 40px;
      color: #00ff66;
      text-shadow: 0 0 5px #00ff66;
    }

    canvas {
      border: 2px solid #00ff66;
      background-color: #111;
      display: block;
      margin: 20px auto;
      box-shadow: 0 0 15px #00ff66;
      border-radius: 8px;
    }

    #retry {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 10px;
      background: #00ff66;
      color: #000;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      display: none;
    }

    #retry:hover {
      background: #00cc55;
    }

    #scores {
      margin-top: 10px;
      font-size: 18px;
    }
  </style>
</head>
<body>

  <h1>Snake Game</h1>
  <div id="scores">Score: 0 | High Score: 0</div>
  <canvas id="gameCanvas" width="400" height="400"></canvas>
  <button id="retry">Retry</button>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const gridSize = 20;
    let snake = [{ x: 10, y: 10 }];
    let food = { x: 15, y: 15 };
    let dx = 0;
    let dy = 0;
    let score = 0;
    let highScore = 0;
    let gameInterval;

    const scoreDisplay = document.getElementById('scores');
    const retryBtn = document.getElementById('retry');

    function drawSnake() {
      ctx.fillStyle = '#00ff66';
      snake.forEach(segment => {
        ctx.fillRect(segment.x * gridSize, segment.y * gridSize, gridSize, gridSize);
      });
    }

    function drawFood() {
      ctx.fillStyle = '#ff3333';
      ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize, gridSize);
    }

    function moveSnake() {
      const head = { x: snake[0].x + dx, y: snake[0].y + dy };
      snake.unshift(head);
      if (head.x === food.x && head.y === food.y) {
        score++;
        generateFood();
      } else {
        snake.pop();
      }
    }

    function generateFood() {
      food = {
        x: Math.floor(Math.random() * (canvas.width / gridSize)),
        y: Math.floor(Math.random() * (canvas.height / gridSize))
      };
    }

    function clearCanvas() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    function checkCollision() {
      const head = snake[0];
      return (
        head.x < 0 ||
        head.x >= canvas.width / gridSize ||
        head.y < 0 ||
        head.y >= canvas.height / gridSize ||
        snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y)
      );
    }

    function updateScores() {
      if (score > highScore) highScore = score;
      scoreDisplay.textContent = `Score: ${score} | High Score: ${highScore}`;
    }

    function gameOver() {
      clearInterval(gameInterval);
      retryBtn.style.display = 'inline-block';
      alert('Game Over! Your score: ' + score);
    }

    function main() {
      if (checkCollision()) {
        gameOver();
        return;
      }
      clearCanvas();
      moveSnake();
      drawSnake();
      drawFood();
      updateScores();
    }

    function startGame() {
      snake = [{ x: 10, y: 10 }];
      dx = 0;
      dy = 0;
      score = 0;
      updateScores();
      generateFood();
      retryBtn.style.display = 'none';
      clearCanvas();
      drawSnake();
      drawFood();
      clearInterval(gameInterval);
      gameInterval = setInterval(main, 100);
    }

    document.addEventListener('keydown', event => {
      const key = event.key.toLowerCase();
      if ((key === 'arrowup' || key === 'w') && dy === 0) {
        dy = -1;
        dx = 0;
      }
      if ((key === 'arrowdown' || key === 's') && dy === 0) {
        dy = 1;
        dx = 0;
      }
      if ((key === 'arrowleft' || key === 'a') && dx === 0) {
        dy = 0;
        dx = -1;
      }
      if ((key === 'arrowright' || key === 'd') && dx === 0) {
        dy = 0;
        dx = 1;
      }
    });

    retryBtn.addEventListener('click', startGame);

    startGame(); // Start the game on load
  </script>
</body>
</html>
