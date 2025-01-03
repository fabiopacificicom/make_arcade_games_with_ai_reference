This is the reference code as shown at the end of the episode: 
https://youtu.be/ai4iQyvDGKU

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spaceship Arcade Game</title>
    <style>
        /* style.css */
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
            overflow: hidden;
        }

        #spaceship {
            position: absolute;
            bottom: 30px;
            left: 50%;
            width: 40px;
            height: 40px;
            background-color: white;
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
            background-color: gray;
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
        // script.js

        const spaceship = document.getElementById('spaceship');
        const gameContainer = document.getElementById('game-container');
        const projectilesContainer = document.getElementById('projectiles-container');
        const asteroidsContainer = document.getElementById('asteroids-container');

        let spaceshipPosition = { x: 380, y: 530 }; // Starting position
        const spaceshipSpeed = 5; // Adjusted speed
        let projectiles = [];
        let asteroids = [];

        let movingLeft = false;
        let movingRight = false;
        let movingUp = false;
        let movingDown = false;

        function moveSpaceship() {
            // Update the spaceship position
            if (movingLeft) spaceshipPosition.x = Math.max(0, spaceshipPosition.x - spaceshipSpeed);
            if (movingRight) spaceshipPosition.x = Math.min(gameContainer.clientWidth - 40, spaceshipPosition.x + spaceshipSpeed);
            if (movingUp) spaceshipPosition.y = Math.min(gameContainer.clientHeight - 40, spaceshipPosition.y + spaceshipSpeed);
            if (movingDown) spaceshipPosition.y = Math.max(0, spaceshipPosition.y - spaceshipSpeed);

            // Apply the new position
            spaceship.style.left = `${spaceshipPosition.x}px`;
            spaceship.style.bottom = `${spaceshipPosition.y}px`;

            // Request the next frame for smooth animation
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

        function shootProjectile() {
            const projectile = document.createElement('div');
            projectile.classList.add('projectile');
            projectile.style.position = 'absolute';
            projectile.style.left = `${spaceshipPosition.x + 18}px`;  // Center of spaceship
            projectile.style.bottom = `${spaceshipPosition.y + 40}px`;  // Right above the spaceship

            projectiles.push(projectile);
            projectilesContainer.appendChild(projectile);

            // Move the projectile upwards
            function moveProjectile() {
                const projectileRect = projectile.getBoundingClientRect();
                if (projectileRect.top <= 0) {
                    // Remove projectile if it's off-screen
                    projectile.remove();
                    projectiles = projectiles.filter(p => p !== projectile); // Clean up the array
                    return;
                }

                // Move projectile upwards
                projectile.style.bottom = `${parseInt(projectile.style.bottom) + 10}px`;

                // Check for collisions with asteroids
                checkCollisions(projectile);

                requestAnimationFrame(moveProjectile);
            }

            moveProjectile();
        }

        function spawnAsteroid() {
            const asteroid = document.createElement('div');
            asteroid.classList.add('asteroid');
            const asteroidSize = Math.random() * (40 - 20) + 20;
            asteroid.style.position = 'absolute';
            asteroid.style.left = `${Math.random() * (gameContainer.clientWidth - asteroidSize)}px`;
            asteroid.style.top = '0px';
            asteroid.style.width = `${asteroidSize}px`;
            asteroid.style.height = `${asteroidSize}px`;

            asteroids.push(asteroid);
            asteroidsContainer.appendChild(asteroid);

            // Move the asteroid downwards
            function moveAsteroid() {
                const asteroidRect = asteroid.getBoundingClientRect();
                if (asteroidRect.top >= gameContainer.clientHeight) {
                    asteroid.remove();
                    asteroids = asteroids.filter(a => a !== asteroid); // Clean up the array
                    return;
                }
                asteroid.style.top = `${parseInt(asteroid.style.top) + 2}px`;

                // Check for collisions with projectiles
                checkCollisions(null, asteroid);

                requestAnimationFrame(moveAsteroid);
            }

            moveAsteroid();

        }

        function checkCollisions(projectile = null, asteroid = null) {
            if (projectile && asteroid) {
                const pRect = projectile.getBoundingClientRect();
                const aRect = asteroid.getBoundingClientRect();

                // Check for intersection between projectile and asteroid
                if (pRect.left < aRect.right &&
                    pRect.right > aRect.left &&
                    pRect.top < aRect.bottom &&
                    pRect.bottom > aRect.top) {
                    // Collision detected
                    asteroid.remove();
                    projectile.remove();
                    asteroids = asteroids.filter(a => a !== asteroid); // Clean up the array
                    projectiles = projectiles.filter(p => p !== projectile); // Clean up the array
                }
            }
        }

        // Spawn asteroids every 2 seconds
        setInterval(spawnAsteroid, 2000);


        // Add event listeners for keydown and keyup
        document.addEventListener('keydown', handleKeydown);
        document.addEventListener('keyup', handleKeyup);

        // Start the spaceship movement loop
        moveSpaceship();
    </script>
</body>

</html>
```
