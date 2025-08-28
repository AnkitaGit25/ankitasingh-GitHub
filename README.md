# ankitasingh-GitHub
This is my first Git repository.
<br>
Author - Ankita Singh
<br>
GitHub user
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Pong Game</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="scoreboard">
    <span id="playerScore">0</span> : <span id="computerScore">0</span>
  </div>
  <canvas id="pong" width="800" height="400"></canvas>
  <script src="game.js"></script>
</body>
body {
  background: #222;
  color: #fff;
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0;
}

.scoreboard {
  font-size: 2em;
  margin: 30px 0 10px 0;
}

canvas {
  background: #000;
  display: block;
  border: 2px solid #fff;
  box-shadow: 0 0 30px #000;
}
const canvas = document.getElementById('pong');
const ctx = canvas.getContext('2d');

const WIDTH = canvas.width;
const HEIGHT = canvas.height;

const paddleWidth = 12;
const paddleHeight = 80;
const paddleMargin = 16;
let leftPaddleY = HEIGHT / 2 - paddleHeight / 2;
let rightPaddleY = HEIGHT / 2 - paddleHeight / 2;
let leftPaddleSpeed = 0;

const ballSize = 14;
let ballX = WIDTH / 2 - ballSize / 2;
let ballY = HEIGHT / 2 - ballSize / 2;
let ballSpeedX = 5 * (Math.random() < 0.5 ? 1 : -1);
let ballSpeedY = (Math.random() - 0.5) * 6;

let playerScore = 0;
let computerScore = 0;

const playerScoreEl = document.getElementById('playerScore');
const computerScoreEl = document.getElementById('computerScore');

// Mouse control
canvas.addEventListener('mousemove', (e) => {
  const rect = canvas.getBoundingClientRect();
  leftPaddleY = e.clientY - rect.top - paddleHeight / 2;
  leftPaddleY = Math.max(Math.min(leftPaddleY, HEIGHT - paddleHeight), 0);
});

// Keyboard control
document.addEventListener('keydown', (e) => {
  if (e.key === 'ArrowUp') leftPaddleSpeed = -6;
  if (e.key === 'ArrowDown') leftPaddleSpeed = 6;
});
document.addEventListener('keyup', (e) => {
  if (e.key === 'ArrowUp' || e.key === 'ArrowDown') leftPaddleSpeed = 0;
});

function drawRect(x, y, w, h, color = '#fff') {
  ctx.fillStyle = color;
  ctx.fillRect(x, y, w, h);
}

function drawCircle(x, y, r, color = '#fff') {
  ctx.fillStyle = color;
  ctx.beginPath();
  ctx.arc(x, y, r, 0, Math.PI * 2);
  ctx.fill();
}

function drawNet() {
  ctx.strokeStyle = '#fff';
  ctx.setLineDash([8, 12]);
  ctx.beginPath();
  ctx.moveTo(WIDTH / 2, 0);
  ctx.lineTo(WIDTH / 2, HEIGHT);
  ctx.stroke();
  ctx.setLineDash([]);
}

function resetBall() {
  ballX = WIDTH / 2 - ballSize / 2;
  ballY = HEIGHT / 2 - ballSize / 2;
  ballSpeedX = 5 * (Math.random() < 0.5 ? 1 : -1);
  ballSpeedY = (Math.random() - 0.5) * 6;
}

function update() {
  // Keyboard paddle
  leftPaddleY += leftPaddleSpeed;
  leftPaddleY = Math.max(Math.min(leftPaddleY, HEIGHT - paddleHeight), 0);

  // Computer paddle AI
  let target = ballY - (paddleHeight / 2);
  let delta = target - rightPaddleY;
  rightPaddleY += Math.sign(delta) * Math.min(Math.abs(delta), 4);
  rightPaddleY = Math.max(Math.min(rightPaddleY, HEIGHT - paddleHeight), 0);

  // Ball movement
  ballX += ballSpeedX;
  ballY += ballSpeedY;

  // Wall collision
  if (ballY <= 0) {
    ballY = 0;
    ballSpeedY *= -1;
  }
  if (ballY + ballSize >= HEIGHT) {
    ballY = HEIGHT - ballSize;
    ballSpeedY *= -1;
  }

  // Paddle collision (left)
  if (
    ballX <= paddleMargin + paddleWidth &&
    ballY + ballSize >= leftPaddleY &&
    ballY <= leftPaddleY + paddleHeight
  ) {
    ballX = paddleMargin + paddleWidth;
    ballSpeedX *= -1;
    // Add some spin
    let hitPoint = (ballY + ballSize / 2) - (leftPaddleY + paddleHeight / 2);
    ballSpeedY = hitPoint * 0.2;
  }

  // Paddle collision (right)
  if (
    ballX + ballSize >= WIDTH - paddleMargin - paddleWidth &&
    ballY + ballSize >= rightPaddleY &&
    ballY <= rightPaddleY + paddleHeight
  ) {
    ballX = WIDTH - paddleMargin - paddleWidth - ballSize;
    ballSpeedX *= -1;
    // Add some spin
    let hitPoint = (ballY + ballSize / 2) - (rightPaddleY + paddleHeight / 2);
    ballSpeedY = hitPoint * 0.2;
  }

  // Score
  if (ballX < 0) {
    computerScore++;
    computerScoreEl.textContent = computerScore;
    resetBall();
  }
  if (ballX + ballSize > WIDTH) {
    playerScore++;
    playerScoreEl.textContent = playerScore;
    resetBall();
  }
}

function draw() {
  ctx.clearRect(0, 0, WIDTH, HEIGHT);

  drawNet();

  // Left paddle
  drawRect(paddleMargin, leftPaddleY, paddleWidth, paddleHeight, '#0af');
  // Right paddle
  drawRect(WIDTH - paddleMargin - paddleWidth, rightPaddleY, paddleWidth, paddleHeight, '#fa0');
  // Ball
  drawCircle(ballX + ballSize / 2, ballY + ballSize / 2, ballSize / 2, '#fff');
}

function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

loop();
</html>

