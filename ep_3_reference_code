Final implementation, reference code for video: https://www.youtube.com/watch?v=JWTn6_juiTQ
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spaceship Arcade Game</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: black;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }

        #game-container {
            position: relative;
            width: 800px;
            height: 600px;
            border: 2px solid white;
            background: url('./images/background_image_level_1.webp') no-repeat center center/cover;
            /* Background image */
            overflow: hidden;
        }


        #spaceship {
            position: absolute;
            bottom: 30px;
            left: 50%;
            width: 40px;
            height: 40px;
            background: url('https://cdn-icons-png.flaticon.com/128/1985/1985789.png') no-repeat center center/contain;
            transform: translateX(-50%);
        }

        #projectiles-container {
            position: absolute;
            bottom: 0;
        }

        #asteroids-container {
            position: absolute;
            top: 0;
        }

        .projectile {
            position: absolute;
            width: 5px;
            height: 10px;
            background-color: red;
        }

        .asteroid {
            position: absolute;
            border-radius: 50%;
            background: url('https://cdn-icons-png.flaticon.com/128/13404/13404254.png') no-repeat center center/contain;
        }
    </style>
</head>

<body>
    <div id="game-container">
        <div id="spaceship"></div>
        <div id="asteroids-container"></div>
        <div id="projectiles-container"></div>
    </div>

    <script>
        const spaceship = document.getElementById('spaceship');
        const gameContainer = document.getElementById('game-container');
        const projectilesContainer = document.getElementById('projectiles-container');
        const asteroidsContainer = document.getElementById('asteroids-container');

        let spaceshipPosition = { x: 380, y: 30 };
        const spaceshipSpeed = 5;

        let projectiles = [];
        let asteroids = [];

        let movingLeft = false;
        let movingRight = false;
        let movingUp = false;
        let movingDown = false;

        function moveSpaceship() {
            if (movingLeft) spaceshipPosition.x = Math.max(0, spaceshipPosition.x - spaceshipSpeed);
            if (movingRight) spaceshipPosition.x = Math.min(gameContainer.clientWidth - 40, spaceshipPosition.x + spaceshipSpeed);
            if (movingUp) spaceshipPosition.y = Math.min(gameContainer.clientHeight - 40, spaceshipPosition.y + spaceshipSpeed);
            if (movingDown) spaceshipPosition.y = Math.max(0, spaceshipPosition.y - spaceshipSpeed);

            spaceship.style.left = `${spaceshipPosition.x}px`;
            spaceship.style.bottom = `${spaceshipPosition.y}px`;

            requestAnimationFrame(moveSpaceship);
        }

        function handleKeydown(event) {
            switch (event.key) {
                case 'ArrowLeft':
                    movingLeft = true;
                    break;
                case 'ArrowRight':
                    movingRight = true;
                    break;
                case 'ArrowUp':
                    movingUp = true;
                    break;
                case 'ArrowDown':
                    movingDown = true;
                    break;
                case ' ':
                    shootProjectile();
                    break;
            }
        }

        function handleKeyup(event) {
            switch (event.key) {
                case 'ArrowLeft':
                    movingLeft = false;
                    break;
                case 'ArrowRight':
                    movingRight = false;
                    break;
                case 'ArrowUp':
                    movingUp = false;
                    break;
                case 'ArrowDown':
                    movingDown = false;
                    break;
            }
        }

        function handleTouchstart(event) {
            const touch = event.touches[0];
            const touchX = touch.clientX;
            const touchY = touch.clientY;

            if (touchX < spaceshipPosition.x) movingLeft = true;
            if (touchX > spaceshipPosition.x) movingRight = true;
            if (touchY < spaceshipPosition.y) movingDown = true;
            if (touchY > spaceshipPosition.y) movingUp = true;

            shootProjectile();
        }

        function handleTouchmove(event) {
            const touch = event.touches[0];
            const touchX = touch.clientX;
            const touchY = touch.clientY;

            movingLeft = touchX < spaceshipPosition.x;
            movingRight = touchX > spaceshipPosition.x;
            movingDown = touchY < spaceshipPosition.y;
            movingUp = touchY > spaceshipPosition.y;
        }

        function handleTouchend(event) {
            movingLeft = false;
            movingRight = false;
            movingUp = false;
            movingDown = false;
        }

        function shootProjectile() {
            const projectile = document.createElement('div');
            projectile.classList.add('projectile');
            projectile.style.left = `${spaceshipPosition.x + 18}px`;
            projectile.style.bottom = `${spaceshipPosition.y + 40}px`;

            projectiles.push(projectile);
            projectilesContainer.appendChild(projectile);

            function moveProjectile() {
                const projectileRect = projectile.getBoundingClientRect();
                if (projectileRect.top <= 0) {
                    projectilesContainer.removeChild(projectile);
                    projectiles = projectiles.filter(p => p !== projectile);
                    return;
                }

                projectile.style.bottom = `${parseInt(projectile.style.bottom) + 5}px`;

                asteroids.forEach(asteroid => {
                    checkCollisions(projectile, asteroid);
                });

                requestAnimationFrame(moveProjectile);
            }

            moveProjectile();
        }

        function spawnAsteroid() {
            const asteroid = document.createElement('div');
            asteroid.classList.add('asteroid');
            const asteroidSize = Math.random() * (40 - 20) + 20;
            asteroid.style.left = `${Math.random() * (gameContainer.clientWidth - asteroidSize)}px`;
            asteroid.style.top = '0px';
            asteroid.style.width = `${asteroidSize}px`;
            asteroid.style.height = `${asteroidSize}px`;

            asteroids.push(asteroid);
            asteroidsContainer.appendChild(asteroid);

            function moveAsteroid() {
                const asteroidRect = asteroid.getBoundingClientRect();
                if (asteroidRect.top >= gameContainer.clientHeight) {
                    asteroidsContainer.removeChild(asteroid);
                    asteroids = asteroids.filter(a => a !== asteroid);
                    return;
                }

                asteroid.style.top = `${parseInt(asteroid.style.top) + 1}px`;

                projectiles.forEach(projectile => {
                    checkCollisions(projectile, asteroid);
                });

                requestAnimationFrame(moveAsteroid);
            }

            moveAsteroid();
        }

        function checkCollisions(projectile, asteroid) {
            const pRect = projectile.getBoundingClientRect();
            const aRect = asteroid.getBoundingClientRect();

            if (pRect.left < aRect.right &&
                pRect.right > aRect.left &&
                pRect.top < aRect.bottom &&
                pRect.bottom > aRect.top) {
                asteroidsContainer.removeChild(asteroid);
                asteroids = asteroids.filter(a => a !== asteroid);

                projectilesContainer.removeChild(projectile);
                projectiles = projectiles.filter(p => p !== projectile);
            }
        }

        setInterval(spawnAsteroid, 2000);

        document.addEventListener('keydown', handleKeydown);
        document.addEventListener('keyup', handleKeyup);

        gameContainer.addEventListener('touchstart', handleTouchstart);
        gameContainer.addEventListener('touchmove', handleTouchmove);
        gameContainer.addEventListener('touchend', handleTouchend);

        moveSpaceship();
    </script>
</body>

</html>
```
