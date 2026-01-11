<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<title>SNAKE — TU N'ES PAS SEUL</title>
<style>
    body {
        margin: 0;
        background: black;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        color: white;
        font-family: Arial Black, Arial, sans-serif;
    }
    canvas {
        border: none;
    }
</style>
</head>
<body>

<canvas id="game" width="600" height="600"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const W = 600, H = 600;
const CELL = 20;
let speed = 120;

let snake = [{x:300, y:300}];
let direction = {x:0, y:0};
let food = randomPos();
let gameOver = false;

const messages = [
    "IL TE REGARDE",
    "TU NE JOUE PAS SEUL",
    "POURQUOI TU CONTINUES",
    "IL EST TROP TARD",
    "ÇA NE FINIRA PAS",
    "TU AS DÉJÀ PERDU"
];

let currentMessage = "";
let messageTimer = 0;

// =====================
// CONTRÔLES (NORMAUX)
// =====================
document.addEventListener("keydown", e => {
    if (e.key === "ArrowLeft") direction = {x:-CELL, y:0};
    if (e.key === "ArrowRight") direction = {x:CELL, y:0};
    if (e.key === "ArrowUp") direction = {x:0, y:-CELL};
    if (e.key === "ArrowDown") direction = {x:0, y:CELL};

    if (gameOver && e.key.toLowerCase() === "r") restart();
});

// =====================
// DÉGRADÉ ROUGE → NOIR
// =====================
function drawGradient() {
    const grad = ctx.createLinearGradient(0, 0, 0, H);
    grad.addColorStop(0, "#8b0000");
    grad.addColorStop(1, "#000000");
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, W, H);
}

// =====================
// DESSIN
// =====================
function draw() {
    drawGradient();

    // Nourriture
    ctx.fillStyle = "#5a0000";
    ctx.fillRect(food.x, food.y, CELL, CELL);

    // Snake
    ctx.fillStyle = "#0a550a";
    snake.forEach(p => ctx.fillRect(p.x, p.y, CELL, CELL));

    // Message dérangeant
    if (messageTimer > 0) {
        ctx.fillStyle = "white";
        ctx.font = "16px Arial Black";
        ctx.textAlign = "center";
        ctx.fillText(currentMessage, W/2, 30);
        messageTimer--;
    }

    // Brouillard
    ctx.fillStyle = "rgba(0,0,0,0.6)";
    ctx.fillRect(0, 0, W, H);

    if (gameOver) {
        ctx.fillStyle = "red";
        ctx.font = "22px Arial Black";
        ctx.textAlign = "center";
        ctx.fillText("TU N'AS PAS ÉCHAPPÉ AU JEU", W/2, H/2 - 20);
        ctx.fillText("R : REJOUER", W/2, H/2 + 20);
    }
}

// =====================
// LOGIQUE
// =====================
function update() {
    if (gameOver) {
        draw();
        return;
    }

    if (direction.x !== 0 || direction.y !== 0) {
        const head = snake[snake.length - 1];
        const newHead = {
            x: head.x + direction.x,
            y: head.y + direction.y
        };

        if (
            newHead.x < 0 || newHead.y < 0 ||
            newHead.x >= W || newHead.y >= H ||
            snake.some(p => p.x === newHead.x && p.y === newHead.y)
        ) {
            gameOver = true;
        } else {
            snake.push(newHead);

            if (newHead.x === food.x && newHead.y === food.y) {
                food = randomPos();
                currentMessage = messages[Math.floor(Math.random()*messages.length)];
                messageTimer = 120;
            } else {
                snake.shift();
            }
        }
    }

    draw();
    setTimeout(update, speed);
}

// =====================
// OUTILS
// =====================
function randomPos() {
    return {
        x: Math.floor(Math.random() * (W / CELL)) * CELL,
        y: Math.floor(Math.random() * (H / CELL)) * CELL
    };
}

function restart() {
    snake = [{x:300, y:300}];
    direction = {x:0, y:0};
    food = randomPos();
    gameOver = false;
    currentMessage = "";
    update();
}

// =====================
// LANCEMENT
// =====================
update();
</script>

</body>
</html>
