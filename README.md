<!DOCTYPE html>
<html>
<head>
    <title>Car Racing Game</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #222;
            font-family: Arial, sans-serif;
        }
        
        #gameContainer {
            position: relative;
            width: 400px;
            height: 600px;
            background: linear-gradient(90deg, #333 10%, #555 50%, #333 90%);
            border: 3px solid #fff;
            overflow: hidden;
        }
        
        #car {
            position: absolute;
            bottom: 20px;
            left: 175px;
            width: 50px;
            height: 70px;
            background: red;
            border: 2px solid yellow;
            border-radius: 5px;
        }
        
        .enemy {
            position: absolute;
            width: 50px;
            height: 70px;
            background: blue;
            border: 2px solid white;
            border-radius: 5px;
        }
        
        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            font-size: 20px;
            z-index: 10;
        }
        
        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.9);
            color: white;
            padding: 30px;
            border-radius: 10px;
            text-align: center;
            display: none;
            z-index: 20;
        }
        
        button {
            background: green;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            margin-top: 10px;
        }
        
        button:hover {
            background: darkgreen;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <div id="score">Score: 0</div>
        <div id="car"></div>
        <div id="gameOver">
            <h2>Game Over!</h2>
            <p id="finalScore">Final Score: 0</p>
            <button onclick="location.reload()">Play Again</button>
        </div>
    </div>

    <script>
        const gameContainer = document.getElementById('gameContainer');
        const car = document.getElementById('car');
        const scoreDisplay = document.getElementById('score');
        const gameOverScreen = document.getElementById('gameOver');
        const finalScoreDisplay = document.getElementById('finalScore');

        let score = 0;
        let carX = 175;
        let gameRunning = true;
        let enemies = [];

        // Car movement
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft' && carX > 20) carX -= 30;
            if (e.key === 'ArrowRight' && carX < 330) carX += 30;
            car.style.left = carX + 'px';
        });

        // Touch controls for phone
        let touchStartX = 0;
        gameContainer.addEventListener('touchstart', (e) => {
            touchStartX = e.touches[0].clientX;
        });

        gameContainer.addEventListener('touchmove', (e) => {
            let touchX = e.touches[0].clientX;
            if (touchX < touchStartX - 30 && carX > 20) {
                carX -= 30;
                touchStartX = touchX;
            }
            if (touchX > touchStartX + 30 && carX < 330) {
                carX += 30;
                touchStartX = touchX;
            }
            car.style.left = carX + 'px';
        });

        // Create enemy cars
        function createEnemy() {
            const enemyX = Math.random() * 350;
            const enemy = document.createElement('div');
            enemy.className = 'enemy';
            enemy.style.left = enemyX + 'px';
            enemy.style.top = '-70px';
            gameContainer.appendChild(enemy);
            
            enemies.push({
                element: enemy,
                x: enemyX,
                y: -70
            });
        }

        // Move enemies down
        function moveEnemies() {
            enemies.forEach((enemy, index) => {
                enemy.y += 5;
                enemy.element.style.top = enemy.y + 'px';

                // Check collision
                if (
                    enemy.x < carX + 50 &&
                    enemy.x + 50 > carX &&
                    enemy.y < 570 &&
                    enemy.y + 70 > 550
                ) {
                    endGame();
                }

                // Remove enemy if off screen
                if (enemy.y > 600) {
                    enemy.element.remove();
                    enemies.splice(index, 1);
                    score += 10;
                    scoreDisplay.textContent = 'Score: ' + score;
                }
            });
        }

        function endGame() {
            gameRunning = false;
            gameOverScreen.style.display = 'block';
            finalScoreDisplay.textContent = 'Final Score: ' + score;
        }

        // Game loop
        setInterval(() => {
            if (gameRunning) {
                moveEnemies();
            }
        }, 30);

        // Create enemies every 1.5 seconds
        setInterval(() => {
            if (gameRunning) {
                createEnemy();
            }
        }, 1500);
    </script>
</body>
</html>
