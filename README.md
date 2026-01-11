<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>SNAKE — TU N'ES PAS SEUL</title>
<style>
  body {
    margin: 0;
    background: black;
    overflow: hidden;
  }
  canvas {
    display: block;
    margin: 0 auto;
    background: black;
  }
</style>
</head>
<body>
<canvas id="game"></canvas>

<script>
const WIDTH = 600;
const HEIGHT = 600;
const CELL = 20;
const SPEED = 120;

const canvas = document.getElementById("game");
canvas.width = WIDTH;
canvas.height = HEIGHT;
const ctx = canvas.getContext("2d");

let snake = [{x: 300, y: 300}];
let direction = {x: CELL, y: 0}; // avance vers la droite
let food = null;
let gameOver = false;
let currentMessage = "";
let messageTimer = 0;

const messages = [
  "IL TE REGARDE",
  "TU NE JOUE PAS SEUL",
  "POURQUOI TU CONTINUES",
  "IL EST TROP TARD",
  "ÇA NE SERA JAMAIS FINI",
  "TU AS DÉJÀ PERDU"
];

// ======================
// Génération nourriture hors serpent
// ======================
function generateFood() {
  while (true) {
    let x = Math.floor(Math.random() * (WIDTH / CELL)) * CELL;
    let y = Math.floor(Math.random() * (HEIGHT / CELL)) * CELL;
    if (!snake.some(segment => segment.x === x && segment.y === y)) {
      return {x, y};
    }
  }
}

food = generateFood();

// ======================
// Contrôles
// ======================
document.addEventListener("keydown", e => {
  if (e.key === "ArrowLeft") direction = {x: -CELL, y: 0};
  if (e.key === "ArrowRight") direction = {x: CELL, y: 0};
  if (e.key === "ArrowUp") direction = {x: 0, y: -CELL};
  if (e.key === "ArrowDown") direction = {x: 0, y: CELL};

  if (gameOver && e.key.toLowerCase() === "r") restart();
  if (gameOver && e.key.toLowerCase() === "q") window.close();
});

// ======================
// Dessin du gradient
// ======================
function drawGradient() {
  const grad = ctx.createLinearGradient(0, 0, 0, HEIGHT);
  grad.addColorStop(0, "rgb(80,0,0)");
  grad.addColorStop(1, "rgb(0,0,0)");
  ctx.fillStyle = grad;
  ctx.fillRect(0, 0, WIDTH, HEIGHT);
}

// ======================
// Dessin du jeu
// ======================
function draw() {
  ctx.clearRect(0, 0, WIDTH, HEIGHT);

  // Dégradé sombre
  drawGradient();

  // Brouillard oppressant
  ctx.fillStyle = "rgba(0,0,0,0.5)";
  ctx.fillRect(0, 0, WIDTH, HEIGHT);

  // Nourriture
  ctx.fillStyle = "#3b0000";
  ctx.fillRect(food.x, food.y, CELL, CELL);

  // Serpent
  ctx.fillStyle = "#0a550a";
  snake.forEach(segment => {
    ctx.fillRect(segment.x, segment.y, CELL, CELL);
  });

  // Message dérangeant
  if (messageTimer > 0) {
    ctx.fillStyle = "white";
    ctx.font = "bold 16px Arial";
    ctx.textAlign = "center";
    ctx.fillText(currentMessage, WIDTH / 2, 30);
  }

  // Game over
  if (gameOver) {
    ctx.fillStyle = "red";
    ctx.font = "bold 20px Arial";
    ctx.textAlign = "center";
    ctx.fillText("TU N'AS PAS ÉCHAPPÉ AU JEU", WIDTH / 2, HEIGHT / 2 - 20);
    ctx.fillText("R : REJOUER   Q : QUITTER", WIDTH / 2, HEIGHT / 2 + 20);
  }
}

// ======================
// Boucle de jeu
// ======================
function update() {
  if (gameOver) {
    draw();
    return;
  }

  if (direction.x !== 0 || direction.y !== 0) {
    let head = snake[snake.length - 1];
    let newHead = {x: head.x + direction.x, y: head.y + direction.y};

    // Collisions
    if (
      newHead.x < 0 || newHead.y < 0 ||
      newHead.x >= WIDTH || newHead.y >= HEIGHT ||
      snake.some(seg => seg.x === newHead.x && seg.y === newHead.y)
    ) {
      gameOver = true;
      draw();
      return;
    }

    snake.push(newHead);

    // Manger la nourriture
    if (newHead.x === food.x && newHead.y === food.y) {
      food = generateFood();
      currentMessage = messages[Math.floor(Math.random() * messages.length)];
      messageTimer = 120;
    } else {
      snake.shift();
    }
  }

  draw();

  if (messageTimer > 0) messageTimer--;

  setTimeout(update, SPEED);
}

// ======================
// Restart
// ======================
function restart() {
  snake = [{x: 300, y: 300}];
  direction = {x: CELL, y: 0};
  food = generateFood();
  gameOver = false;
  currentMessage = "";
  messageTimer = 0;
  update();
}

// ======================
// Lancement
// ======================
update();
</script>
</body>
</html>

