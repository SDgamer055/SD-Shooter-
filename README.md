# SD-Shooter-
 "A simple shooting game where the player controls a spaceship to shoot incoming enemies." 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shooting Game</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #000;
        }
        canvas {
            display: block;
            margin: 0 auto;
            background-color: #111;
        }
        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 50px;
            color: white;
            display: none;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div id="gameOver">Game Over</div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Player properties
        const player = {
            x: canvas.width / 2 - 25,
            y: canvas.height - 60,
            width: 50,
            height: 50,
            speed: 5,
            dx: 0
        };

        // Bullet properties
        const bullets = [];
        const bulletSpeed = 5;

        // Enemy properties
        const enemies = [];
        const enemySpeed = 2;

        let gameOver = false;

        // Control player movement
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft') player.dx = -player.speed;
            if (e.key === 'ArrowRight') player.dx = player.speed;
            if (e.key === ' ') shootBullet();
        });
        document.addEventListener('keyup', () => player.dx = 0);

        // Draw player on canvas
        function drawPlayer() {
            ctx.fillStyle = 'green';
            ctx.fillRect(player.x, player.y, player.width, player.height);
        }

        // Move player
        function movePlayer() {
            player.x += player.dx;

            if (player.x < 0) player.x = 0;
            if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
        }

        // Draw bullets
        function drawBullets() {
            ctx.fillStyle = 'red';
            bullets.forEach((bullet, index) => {
                bullet.y -= bulletSpeed;
                ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);

                // Remove bullets that are off-screen
                if (bullet.y < 0) bullets.splice(index, 1);
            });
        }

        // Shoot bullet
        function shootBullet() {
            bullets.push({ x: player.x + player.width / 2 - 5, y: player.y, width: 10, height: 20 });
        }

        // Generate enemies
        function generateEnemies() {
            if (Math.random() < 0.02) {
                const enemyWidth = 50;
                const enemyHeight = 50;
                const x = Math.random() * (canvas.width - enemyWidth);
                const y = -enemyHeight;
                enemies.push({ x, y, width: enemyWidth, height: enemyHeight });
            }
        }

        // Draw enemies
        function drawEnemies() {
            ctx.fillStyle = 'blue';
            enemies.forEach((enemy, index) => {
                enemy.y += enemySpeed;
                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);

                // Remove enemies that are off-screen
                if (enemy.y > canvas.height) enemies.splice(index, 1);
            });
        }

        // Check for collision between bullet and enemy
        function checkCollisions() {
            bullets.forEach((bullet, bulletIndex) => {
                enemies.forEach((enemy, enemyIndex) => {
                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y) {
                        bullets.splice(bulletIndex, 1);
                        enemies.splice(enemyIndex, 1);
                    }
                });
            });
        }

        // Display Game Over
        function gameOverDisplay() {
            document.getElementById('gameOver').style.display = 'block';
            gameOver = true;
        }

        // Game loop
        function gameLoop() {
            if (gameOver) return;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            drawPlayer();
            movePlayer();
            drawBullets();
            generateEnemies();
            drawEnemies();
            checkCollisions();

            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>
