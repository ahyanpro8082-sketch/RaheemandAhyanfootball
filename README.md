<!DOCTYPE html>
<html>
<head>
  <title>Raheem vs Ahyan ⚽</title>
  <style>
    body { margin: 0; background: #222; font-family: Arial; }
    canvas { display: block; margin: auto; background: green; }
    #menu { text-align: center; color: white; margin-top: 20px; }
    #startBtn { padding: 10px 20px; font-size: 16px; }
  </style>
</head>
<body>

<div id="menu">
  Player 1 Name: <input id="player1Name" value="Raheem"> 
  Player 2 Name: <input id="player2Name" value="Ahyan">
  <button id="startBtn">Start Game</button>
</div>

<canvas id="game" width="800" height="500" style="display:none;"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const menu = document.getElementById("menu");
const startBtn = document.getElementById("startBtn");

let player1Name = "Raheem";
let player2Name = "Ahyan";

let raheem, ahyan, ball, leftGoal, rightGoal;
let raheemScore = 0, ahyanScore = 0;

let keys = {};
let maxScore = 5;
let gameOver = false;
let winner = "";

// Start game
startBtn.addEventListener("click", () => {
  player1Name = document.getElementById("player1Name").value || "Raheem";
  player2Name = document.getElementById("player2Name").value || "Ahyan";
  initGame();
});

function initGame() {
  raheem = { x: 100, y: 250, color: "red", name: player1Name };
  ahyan = { x: 700, y: 250, color: "blue", name: player2Name };
  ball = { x: 400, y: 250, vx: 0, vy: 0 };

  leftGoal = { x: 0, y: 175, width: 20, height: 150 };
  rightGoal = { x: 780, y: 175, width: 20, height: 150 };

  raheemScore = 0;
  ahyanScore = 0;
  gameOver = false;

  canvas.style.display = "block";
  menu.style.display = "none";

  requestAnimationFrame(gameLoop);
}

// Controls
document.addEventListener("keydown", e => keys[e.key] = true);
document.addEventListener("keyup", e => keys[e.key] = false);

// Restart
document.addEventListener("keydown", e => {
  if (e.key === "r" && gameOver) {
    raheemScore = 0;
    ahyanScore = 0;
    gameOver = false;
    winner = "";
    resetBall();
  }
});

// Game update
function update() {
  if (gameOver) return;

  // Movement
  if (keys["ArrowUp"]) raheem.y -= 4;
  if (keys["ArrowDown"]) raheem.y += 4;
  if (keys["ArrowLeft"]) raheem.x -= 4;
  if (keys["ArrowRight"]) raheem.x += 4;

  if (keys["w"]) ahyan.y -= 4;
  if (keys["s"]) ahyan.y += 4;
  if (keys["a"]) ahyan.x -= 4;
  if (keys["d"]) ahyan.x += 4;

  // Ball physics
  ball.x += ball.vx;
  ball.y += ball.vy;
  ball.vx *= 0.98;
  ball.vy *= 0.98;

  // Kick logic
  [raheem, ahyan].forEach(p => {
    const dx = ball.x - p.x;
    const dy = ball.y - p.y;
    const dist = Math.sqrt(dx*dx + dy*dy);
    if (dist < 25) {
      ball.vx = dx * 0.3;
      ball.vy = dy * 0.3;
    }
  });

  // Keep players inside
  raheem.x = Math.max(0, Math.min(raheem.x, 780));
  raheem.y = Math.max(0, Math.min(raheem.y, 480));
  ahyan.x = Math.max(0, Math.min(ahyan.x, 780));
  ahyan.y = Math.max(0, Math.min(ahyan.y, 480));

  // Goals
  if (ball.x - 10 <= leftGoal.x + leftGoal.width &&
      ball.y > leftGoal.y &&
      ball.y < leftGoal.y + leftGoal.height) {
    ahyanScore++;
    checkWinner();
    resetBall();
  }

  if (ball.x + 10 >= rightGoal.x &&
      ball.y > rightGoal.y &&
      ball.y < rightGoal.y + rightGoal.height) {
    raheemScore++;
    checkWinner();
    resetBall();
  }
}

function checkWinner() {
  if (raheemScore >= maxScore) {
    gameOver = true;
    winner = raheem.name;
  }
  if (ahyanScore >= maxScore) {
    gameOver = true;
    winner = ahyan.name;
  }
}

function resetBall() {
  ball.x = 400;
  ball.y = 250;
  ball.vx = 0;
  ball.vy = 0;
}

// Draw
function draw() {
  ctx.clearRect(0,0,800,500);

  // Field
  ctx.fillStyle = "green";
  ctx.fillRect(0,0,800,500);

  // Goals
  ctx.fillStyle = "yellow";
  ctx.fillRect(leftGoal.x, leftGoal.y, leftGoal.width, leftGoal.height);
  ctx.fillRect(rightGoal.x, rightGoal.y, rightGoal.width, rightGoal.height);

  // Players
  ctx.fillStyle = raheem.color;
  ctx.fillRect(raheem.x, raheem.y, 20,20);

  ctx.fillStyle = ahyan.color;
  ctx.fillRect(ahyan.x, ahyan.y, 20,20);

  // Names
  ctx.fillStyle = "white";
  ctx.font = "14px Arial";
  ctx.fillText(raheem.name, raheem.x-10, raheem.y-10);
  ctx.fillText(ahyan.name, ahyan.x-10, ahyan.y-10);

  // Ball with border
  ctx.fillStyle = "white";
  ctx.beginPath();
  ctx.arc(ball.x, ball.y, 10, 0, Math.PI*2);
  ctx.fill();
  ctx.lineWidth = 2;
  ctx.strokeStyle = "black";
  ctx.stroke();

  // Score
  ctx.fillStyle = "white";
  ctx.font = "20px Arial";
  ctx.fillText(`${raheem.name}: ${raheemScore}`, 50, 30);
  ctx.fillText(`${ahyan.name}: ${ahyanScore}`, 600, 30);

  // Winner screen
  if (gameOver) {
    ctx.fillStyle = "white";
    ctx.font = "40px Arial";
    ctx.fillText(winner + " WINS!", 250, 250);

    ctx.font = "20px Arial";
    ctx.fillText("Press R to Restart", 280, 300);
  }
}

// Loop
function gameLoop() {
  update();
  draw();
  requestAnimationFrame(gameLoop);
}
</script>

</body>
</html>
