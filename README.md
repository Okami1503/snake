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
    font-family: 'Arial Black', sans-serif;
    color: white;
  }
  canvas {
    display: block;
    margin: auto;
    background: black;
  }
  #message {
    position: absolute;
    top: 20px;
    width: 100%;
    text-align: center;
    font-size: 24px;
    color: white;
    pointer-events: none;
  }
</style>
</head>
<body>
<canvas id="game"></canvas>
<div id="message"></div>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const WIDTH = 600;
const HEIGHT = 600;
const CELL = 20;
const SPEED = 120;

canvas.width = WIDTH;
canvas.height = HEIGHT;

let snake = [{x: 300, y: 300}];
let direction = {x: CELL, y: 0};
let food = {};
let gameOver = false;

const messages = [
  "IL TE REGARDE",
  "TU NE JOUE PAS SEUL",
  "POURQUOI TU CONTINUES",
  "IL EST TROP TARD",
  "ÇA NE SERA JAMAIS FINI",
  "TU AS DÉJÀ PERDU"
];
let currentMessage = "";
let messageTimer = 0;

// ======================
// Génération de la nourriture
// ======================
function generateFood() {
  const available = [];
  for(let x = 0; x < WIDTH; x += CELL){
    for(let y = 0; y < HEIGHT; y += CELL){
      if(!snake.some(s => s.x === x && s.y === y)){
        available.push({x, y});
      }
    }
  }
  food = available[Math.floor(Math.random() * available.length)];
}

// ======================
// Contrôles
// ======================
document.addEventListener("keydown", (e) => {
  if(e.key === "ArrowLeft" && direction.x === 0) direction = {x: -CELL, y: 0};
  if(e.key === "ArrowRight" && direction.x === 0) direction = {x: CELL, y: 0};
  if(e.key === "ArrowUp" && direction.y === 0) direction = {x: 0, y: -CELL};
  if(e.key === "ArrowDown" && direction.y === 0) direction = {x: 0, y: CELL};
  
  if(gameOver){
    if(e.key.toLowerCase() === "r") restart();
    if(e.key.toLowerCase() === "q") window.close();
  }
});

// ======================
// Dessin
// ======================
function drawGradient() {
  for(let i = 0; i < HEIGHT; i++){
    let ratio = i / HEIGHT;
    let r = Math.floor(80 * (1 - ratio));
    ctx.strokeStyle = `rgb(${r},0,0)`;
    ctx.beginPath();
    ctx.moveTo(0,i);
    ctx.lineTo(WIDTH,i);
    ctx.stroke();
  }
}

function draw(){
  ctx.clearRect(0,0,WIDTH,HEIGHT);
  drawGradient();
  
  // Brouillard
  ctx.fillStyle = "rgba(0,0,0,0.5)";
  ctx.fillRect(0,0,WIDTH,HEIGHT);

  // Nourriture
  ctx.fillStyle = "#3b0000";
  ctx.fillRect(food.x, food.y, CELL, CELL);

  // Snake
  ctx.fillStyle = "#0a550a";
  snake.forEach(s => ctx.fillRect(s.x, s.y, CELL, CELL));

  // Message
  if(messageTimer > 0){
    document.getElementById("message").textContent = currentMessage;
  } else {
    document.getElementById("message").textContent = "";
  }

  if(gameOver){
    ctx.fillStyle = "red";
    ctx.font = "20px Arial Black";
    ctx.textAlign = "center";
    ctx.fillText("TU N'AS PAS ÉCHAPPÉ AU JEU", WIDTH/2, HEIGHT/2 - 20);
    ctx.fillText("R : REJOUER   Q : QUITTER", WIDTH/2, HEIGHT/2 + 20);
  }
}

// ======================
// Mise à jour du jeu
// ======================
function update(){
  if(gameOver){
    draw();
    return;
  }

  const head = {...snake[snake.length-1]};
  head.x += direction.x;
  head.y += direction.y;

  // Collisions
  if(head.x < 0 || head.y < 0 || head.x >= WIDTH || head.y >= HEIGHT || snake.some(s => s.x === head.x && s.y === head.y)){
    gameOver = true;
    draw();
    return;
  }

  snake.push(head);

  if(head.x === food.x && head.y === food.y){
    generateFood();
    currentMessage = messages[Math.floor(Math.random() * messages.length)];
    messageTimer = 60; // frames
  } else {
    snake.shift();
  }

  if(messageTimer > 0) messageTimer--;
  draw();
}

// ======================
// Restart
// ======================
function restart(){
  snake = [{x:300, y:300}];
  direction = {x:CELL, y:0};
  generateFood();
  gameOver = false;
  currentMessage = "";
}

// ======================
// Lancement
// ======================
generateFood();
setInterval(update, SPEED);
</script>
</body>
</html>


