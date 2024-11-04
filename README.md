# number-sytem
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #282c34;
            color: white;
            font-family: 'Arial', sans-serif;
        }

        h1 {
            margin-bottom: 10px;
        }

        canvas {
            background-color: #000;
            border: 1px solid #fff;
        }

        .score-board {
            margin-bottom: 20px;
            font-size: 18px;
        }
    </style>
</head>
<body>

    <h1>Snake Game</h1>
    <div class="score-board">
        <p>Score: <span id="score">0</span> | High Score: <span id="highScore">0</span></p>
    </div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const box = 20; // Size of each box
        let snake = [{ x: 9 * box, y: 9 * box }]; // Initial position of the snake
        let direction = 'RIGHT'; // Initial direction of the snake
        let food = { x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box }; // Random food position
        let score = 0; // Initial score
        let highScore = localStorage.getItem('highScore') || 0; // High score from local storage
        document.getElementById("highScore").innerText = highScore; // Display high score

        // Function to draw everything on the canvas
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear canvas

            // Draw snake
            for (let i = 0; i < snake.length; i++) {
                ctx.fillStyle = (i === 0) ? 'lime' : 'green'; // Head is lime, body is green
                ctx.fillRect(snake[i].x, snake[i].y, box, box);
            }

            // Draw food
            ctx.fillStyle = 'red';
            ctx.fillRect(food.x, food.y, box, box);
            
            // Old head position
            let snakeX = snake[0].x;
            let snakeY = snake[0].y;

            // Direction control
            if (direction === 'LEFT') snakeX -= box;
            if (direction === 'UP') snakeY -= box;
            if (direction === 'RIGHT') snakeX += box;
            if (direction === 'DOWN') snakeY += box;

            // Check if snake eats the food
            if (snakeX === food.x && snakeY === food.y) {
                score++;
                document.getElementById("score").innerText = score; // Update score
                food = { x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box }; // New food position
            } else {
                // Remove the last part of the snake
                snake.pop();
            }

            // Add new head
            const newHead = { x: snakeX, y: snakeY };

            // Game over conditions
            if (snakeX < 0 || snakeY < 0 || snakeX >= canvas.width || snakeY >= canvas.height || collision(newHead, snake)) {
                clearInterval(game); // Stop the game
                alert("Game Over! Your score: " + score); // Alert the player
                if (score > highScore) {
                    highScore = score;
                    localStorage.setItem('highScore', highScore); // Update high score in local storage
                    document.getElementById("highScore").innerText = highScore; // Update displayed high score
                }
                // Prompt to restart the game
                document.getElementById("score").innerText += " - Press SPACE to restart.";
                return; // Exit the draw function on game over
            }

            snake.unshift(newHead); // Add new head to the snake
        }

        // Check collision with itself
        function collision(head, array) {
            for (let i = 0; i < array.length; i++) {
                if (head.x === array[i].x && head.y === array[i].y) {
                    return true; // Collision detected
                }
            }
            return false; // No collision
        }

        // Control the snake direction
        document.addEventListener("keydown", directionControl);
        function directionControl(event) {
            if (event.keyCode === 37 && direction !== 'RIGHT') direction = 'LEFT';
            if (event.keyCode === 38 && direction !== 'DOWN') direction = 'UP';
            if (event.keyCode === 39 && direction !== 'LEFT') direction = 'RIGHT';
            if (event.keyCode === 40 && direction !== 'UP') direction = 'DOWN';
            // Restart game with SPACE
            if (event.keyCode === 32) {
                initGame(); // Restart the game
            }
        }

        // Initialize game
        function initGame() {
            snake = [{ x: 9 * box, y: 9 * box }]; // Reset snake position
            direction = 'RIGHT'; // Reset direction
            food = { x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box }; // Reset food position
            score = 0; // Reset score
            document.getElementById("score").innerText = score; // Update displayed score
            clearInterval(game); // Clear any existing game interval
            game = setInterval(draw, 100); // Start game loop
        }

        // Start the game for the first time
        let game = setInterval(draw, 100); // Initialize the game loop
    </script>

</body>
</html>
