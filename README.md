<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Farm Adventure</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1 id="title">Farm Adventure</h1>
    <div class="image-gallery">
      <!-- Add image slots -->
      <div class="image-slot" id="slot1"></div>
      <div class="image-slot" id="slot2"></div>
      <div class="image-slot" id="slot3"></div>
      <div class="image-slot" id="slot4"></div>
      <div class="image-slot" id="slot5"></div>
      <div class="image-slot" id="slot6"></div>
      <div class="image-slot" id="slot7"></div>
      <div class="image-slot" id="slot8"></div>
      <div class="image-slot" id="slot9"></div>
      <div class="image-slot" id="slot10"></div>
    </div>
    <canvas id="gameCanvas"></canvas>
  </div>
  <script src="script.js"></script>
</body>
</html>
body {
  margin: 0;
  padding: 0;
  background-color: pink;
  font-family: Arial, sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.container {
  width: 100%;
  max-width: 800px;
}

#title {
  text-align: center;
  font-size: 2em;
  margin: 20px 0;
  color: white;
}

.image-gallery {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 10px;
  margin-bottom: 20px;
}

.image-slot {
  width: 100%;
  padding-top: 100%; /* 1:1 aspect ratio */
  background-color: white;
  border: 2px dashed gray;
}

#gameCanvas {
  display: block;
  margin: 20px auto;
  background: url('./farm-background.jpeg') no-repeat center center / cover;
  width: 100%;
  max-width: 800px;
  height: 400px;
  border: 2px solid black;
}
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Canvas dimensions
canvas.width = 800;
canvas.height = 400;

// Game variables
const cowImages = {
  brown: new Image(),
  white: new Image()
};
cowImages.brown.src = "./brown-cow.png"; // Brown cow image
cowImages.white.src = "./white-cow.png"; // White cow image

let brownCow = { x: 50, y: 300, width: 50, height: 50, speed: 5 };
let whiteCow = { x: 100, y: 300, width: 50, height: 50, speed: 5 };

let obstacles = [];
let gameRunning = true;

// Key bindings
let keys = {};

// Create an obstacle
function createObstacle() {
  const obstacle = {
    x: canvas.width,
    y: 350,
    width: 50,
    height: 50,
    speed: 4
  };
  obstacles.push(obstacle);
}

// Draw cow
function drawCow(cow, image) {
  ctx.drawImage(image, cow.x, cow.y, cow.width, cow.height);
}

// Draw obstacles
function drawObstacles() {
  obstacles.forEach(obstacle => {
    ctx.fillStyle = "green";
    ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
    obstacle.x -= obstacle.speed;
  });
}

// Detect collisions
function detectCollisions(cow) {
  for (let obstacle of obstacles) {
    if (
      cow.x < obstacle.x + obstacle.width &&
      cow.x + cow.width > obstacle.x &&
      cow.y < obstacle.y + obstacle.height &&
      cow.y + cow.height > obstacle.y
    ) {
      return true;
    }
  }
  return false;
}

// Game loop
function gameLoop() {
  if (!gameRunning) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // Draw cows
  drawCow(brownCow, cowImages.brown);
  drawCow(whiteCow, cowImages.white);

  // Draw obstacles
  drawObstacles();

  // Move cows
  if (keys["ArrowUp"] && brownCow.y > 0) brownCow.y -= brownCow.speed;
  if (keys["ArrowDown"] && brownCow.y < canvas.height - brownCow.height)
    brownCow.y += brownCow.speed;
  if (keys["w"] && whiteCow.y > 0) whiteCow.y -= whiteCow.speed;
  if (keys["s"] && whiteCow.y < canvas.height - whiteCow.height)
    whiteCow.y += whiteCow.speed;

  // Check collisions
  if (detectCollisions(brownCow) || detectCollisions(whiteCow)) {
    gameRunning = false;
    alert("Game Over! Refresh to play again.");
  }

  // Remove off-screen obstacles
  obstacles = obstacles.filter(obstacle => obstacle.x + obstacle.width > 0);

  requestAnimationFrame(gameLoop);
}

// Event listeners
document.addEventListener("keydown", e => (keys[e.key] = true));
document.addEventListener("keyup", e => (keys[e.key] = false));

// Start the game
setInterval(createObstacle, 2000);
gameLoop();
