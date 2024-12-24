# Javascript - Play

## Snake

### 1 Versione

#### snake.js

```python
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

canvas.width = 400;
canvas.height = 400;

const box = 20; // Dimensione di ogni segmento del serpente
let snake = [{ x: 200, y: 200 }]; // Posizione iniziale del serpente
let direction = "RIGHT"; // Direzione iniziale
let food = { x: getRandomPosition(), y: getRandomPosition() }; // Posizione iniziale del cibo
let score = 0;
let time = 0;

// Funzione per generare una posizione casuale
function getRandomPosition() {
    return Math.floor(Math.random() * (canvas.width / box)) * box;
}

// Assegna una nuova direzione al movimento
document.addEventListener("keydown", (event) => {
    if (event.key === "ArrowUp" && direction !== "DOWN") direction = "UP";
    if (event.key === "ArrowDown" && direction !== "UP") direction = "DOWN";
    if (event.key === "ArrowLeft" && direction !== "RIGHT") direction = "LEFT";
    if (event.key === "ArrowRight" && direction !== "LEFT") direction = "RIGHT";
});

// Funzione per disegnare il cibo
function drawFood() {
    ctx.fillStyle = "red";
    ctx.fillRect(food.x, food.y, box, box);
}

// Funzione per disegnare il serpente
function drawSnake() {
    ctx.fillStyle = "lime";
    snake.forEach((segment) => {
        ctx.fillRect(segment.x, segment.y, box, box);
    });
}

// Funzione principale per aggiornare il gioco
function updateGame() {
    // Pulisce l'area di gioco
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Muove il serpente
    let head = { ...snake[0] };
    if (direction === "UP") head.y -= box;
    if (direction === "DOWN") head.y += box;
    if (direction === "LEFT") head.x -= box;
    if (direction === "RIGHT") head.x += box;

    // Aggiunge la nuova testa
    snake.unshift(head);

    // Controlla se il serpente mangia il cibo
    if (head.x === food.x && head.y === food.y) {
        score++;
        food = { x: getRandomPosition(), y: getRandomPosition() }; // Genera nuovo cibo
    } else {
        snake.pop(); // Rimuove la coda se non mangia
    }

    // Controlla collisioni con bordi o se stesso
    if (
        head.x < 0 ||
        head.y < 0 ||
        head.x >= canvas.width ||
        head.y >= canvas.height ||
        snake.slice(1).some((segment) => segment.x === head.x && segment.y === head.y)
    ) {
        clearInterval(gameInterval);
        alert("Game Over! Punteggio: " + score);
        document.location.reload();
    }

    // Aggiorna il contatore del punteggio
    document.getElementById("score").textContent = `Cibo mangiato: ${score}`;

    // Disegna il serpente e il cibo
    drawSnake();
    drawFood();
}

// Aggiorna il tempo ogni secondo
setInterval(() => {
    time++;
    document.getElementById("time").textContent = `Tempo: ${time}s`;
}, 1000);

// Avvia il gioco
const gameInterval = setInterval(updateGame, 100);

```

#### styles.css

```python
body {
    margin: 0;
    font-family: Arial, sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    background-color: #222;
    color: #fff;
}

#game-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

#score-container {
    margin-bottom: 20px;
    font-size: 20px;
}

#game {
    border: 5px solid #fff;
    background-color: #000;
    display: block;
}

```

#### index.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="game-container">
        <div id="score-container">
            <span id="score">Cibo mangiato: 0</span>
            <span id="time">Tempo: 0s</span>
        </div>
        <canvas id="game"></canvas>
    </div>
    <script src="snake.js"></script>
</body>
</html>

```

### 2 Versione

#### snake.js

```python
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

canvas.width = 400;
canvas.height = 400;

const box = 20; // Dimensione di ogni segmento del serpente
let snake = [{ x: 200, y: 200 }]; // Posizione iniziale del serpente
let direction = "RIGHT"; // Direzione iniziale
let food = { x: getRandomPosition(), y: getRandomPosition() }; // Posizione iniziale del cibo
let score = 0;
let time = 0;

// Posizioni iniziali per il rilevamento del tocco
let touchStartX = 0;
let touchStartY = 0;
let touchEndX = 0;
let touchEndY = 0;

// Funzione per generare una posizione casuale
function getRandomPosition() {
    return Math.floor(Math.random() * (canvas.width / box)) * box;
}

// Rilevamento eventi touch per controllare il movimento
canvas.addEventListener("touchstart", (event) => {
    const touch = event.touches[0];
    touchStartX = touch.clientX;
    touchStartY = touch.clientY;
});

canvas.addEventListener("touchend", (event) => {
    const touch = event.changedTouches[0];
    touchEndX = touch.clientX;
    touchEndY = touch.clientY;

    handleSwipe(); // Gestisci il movimento basato sullo swipe
});

// Funzione per gestire il gesto swipe
function handleSwipe() {
    const diffX = touchEndX - touchStartX;
    const diffY = touchEndY - touchStartY;

    if (Math.abs(diffX) > Math.abs(diffY)) {
        if (diffX > 0 && direction !== "LEFT") direction = "RIGHT";
        if (diffX < 0 && direction !== "RIGHT") direction = "LEFT";
    } else {
        if (diffY > 0 && direction !== "UP") direction = "DOWN";
        if (diffY < 0 && direction !== "DOWN") direction = "UP";
    }
}

// Assegna una nuova direzione al movimento con la tastiera
document.addEventListener("keydown", (event) => {
    if (event.key === "ArrowUp" && direction !== "DOWN") direction = "UP";
    if (event.key === "ArrowDown" && direction !== "UP") direction = "DOWN";
    if (event.key === "ArrowLeft" && direction !== "RIGHT") direction = "LEFT";
    if (event.key === "ArrowRight" && direction !== "LEFT") direction = "RIGHT";
});

// Funzione per disegnare il cibo
function drawFood() {
    ctx.fillStyle = "red";
    ctx.fillRect(food.x, food.y, box, box);
}

// Funzione per disegnare il serpente
function drawSnake() {
    ctx.fillStyle = "lime";
    snake.forEach((segment) => {
        ctx.fillRect(segment.x, segment.y, box, box);
    });
}

// Funzione principale per aggiornare il gioco
function updateGame() {
    // Pulisce l'area di gioco
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Muove il serpente
    let head = { ...snake[0] };
    if (direction === "UP") head.y -= box;
    if (direction === "DOWN") head.y += box;
    if (direction === "LEFT") head.x -= box;
    if (direction === "RIGHT") head.x += box;

    // Aggiunge la nuova testa
    snake.unshift(head);

    // Controlla se il serpente mangia il cibo
    if (head.x === food.x && head.y === food.y) {
        score++;
        food = { x: getRandomPosition(), y: getRandomPosition() }; // Genera nuovo cibo
    } else {
        snake.pop(); // Rimuove la coda se non mangia
    }

    // Controlla collisioni con bordi o se stesso
    if (
        head.x < 0 ||
        head.y < 0 ||
        head.x >= canvas.width ||
        head.y >= canvas.height ||
        snake.slice(1).some((segment) => segment.x === head.x && segment.y === head.y)
    ) {
        clearInterval(gameInterval);
        alert("Game Over! Punteggio: " + score);
        document.location.reload();
    }

    // Aggiorna il contatore del punteggio
    document.getElementById("score").textContent = `Cibo mangiato: ${score}`;

    // Disegna il serpente e il cibo
    drawSnake();
    drawFood();
}

// Aggiorna il tempo ogni secondo
setInterval(() => {
    time++;
    document.getElementById("time").textContent = `Tempo: ${time}s`;
}, 1000);

// Avvia il gioco
const gameInterval = setInterval(updateGame, 100);

```

#### styles.css

```python
body {
    margin: 0;
    font-family: Arial, sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    background-color: #222;
    color: #fff;
}

#game-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

#score-container {
    margin-bottom: 20px;
    font-size: 20px;
}

#game {
    border: 5px solid #fff;
    background-color: #000;
    display: block;
}

```


#### index.html

```python
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="game-container">
        <div id="score-container">
            <span id="score">Cibo mangiato: 0</span>
            <span id="time">Tempo: 0s</span>
        </div>
        <canvas id="game"></canvas>
    </div>
    <script src="snake.js"></script>
</body>
</html>

```