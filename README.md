<!DOCTYPE html>
<html>
<head>
  <title>Space Shooter Game</title>
  <style>
    body {
      margin: 0;
      background: black;
      overflow: hidden;
    }
    canvas {
      display: block;
      margin: auto;
      background: #111;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="500" height="600"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Spaceship object
    const ship = {
      x: 225,
      y: 550,
      width: 50,
      height: 30,
      speed: 5,
      health: 100
    };

    let bullets = [];
    let aliens = [];
    let keys = {};
    let firing = false; // track if fire button is pressed

    // Key events
    document.addEventListener("keydown", e => {
      keys[e.key] = true;
      if (e.key === " ") firing = true; // start firing
    });
    document.addEventListener("keyup", e => {
      keys[e.key] = false;
      if (e.key === " ") firing = false; // stop firing
    });

    // Fire bullets continuously while space is held
    setInterval(() => {
      if (firing) {
        bullets.push({
          x: ship.x + ship.width/2 - 2,
          y: ship.y,
          width: 4,
          height: 10,
          speed: 7
        });
      }
    }, 200); // fire rate (every 200ms)

    // Spawn aliens continuously
    setInterval(() => {
      aliens.push({ x: Math.random() * 450, y: 0, width: 40, height: 30, speed: 2 });
    }, 1000);

    function update() {
      // Move ship left/right only
      if (keys["ArrowLeft"] && ship.x > 0) ship.x -= ship.speed;
      if (keys["ArrowRight"] && ship.x < canvas.width - ship.width) ship.x += ship.speed;

      // Move bullets
      bullets.forEach(b => b.y -= b.speed);
      bullets = bullets.filter(b => b.y > 0);

      // Move aliens
      aliens.forEach(a => a.y += a.speed);

      // Bullet vs Alien collision
      bullets.forEach((b, bi) => {
        aliens.forEach((a, ai) => {
          if (b.x < a.x + a.width && b.x + b.width > a.x &&
              b.y < a.y + a.height && b.y + b.height > a.y) {
            bullets.splice(bi, 1);
            aliens.splice(ai, 1);
          }
        });
      });

      // Alien vs Ship collision
      aliens.forEach((a, ai) => {
        if (ship.x < a.x + a.width && ship.x + ship.width > a.x &&
            ship.y < a.y + a.height && ship.y + ship.height > a.y) {
          aliens.splice(ai, 1);
          ship.health -= 1; // reduce health by 1%
        }
      });
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Health display
      ctx.fillStyle = "white";
      ctx.font = "20px Arial";
      ctx.fillText("Health: " + ship.health + "%", 10, 25);

      // Draw ship
      ctx.fillStyle = "cyan";
      ctx.fillRect(ship.x, ship.y, ship.width, ship.height);

      // Draw bullets
      ctx.fillStyle = "yellow";
      bullets.forEach(b => ctx.fillRect(b.x, b.y, b.width, b.height));

      // Draw aliens
      ctx.fillStyle = "red";
      aliens.forEach(a => ctx.fillRect(a.x, a.y, a.width, a.height));
    }

    function gameLoop() {
      update();
      draw();

      if (ship.health > 0) {
        requestAnimationFrame(gameLoop);
      } else {
        ctx.fillStyle = "white";
        ctx.font = "40px Arial";
        ctx.fillText("GAME OVER", canvas.width/2 - 100, canvas.height/2);
      }
    }

    gameLoop();
  </script>
</body>
</html>

