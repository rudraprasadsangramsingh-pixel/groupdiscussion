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

    // Spaceship
    const ship = { x: 225, y: 550, width: 50, height: 30, speed: 5, health: 100 };

    // Bullets & Aliens
    let bullets = [];
    let aliens = [];

    // Controls
    let keys = {};
    document.addEventListener("keydown", e => keys[e.key] = true);
    document.addEventListener("keyup", e => keys[e.key] = false);

    // Fire bullets
    document.addEventListener("keydown", e => {
      if (e.key === " ") {
        bullets.push({ x: ship.x + ship.width/2 - 2, y: ship.y, width: 4, height: 10, speed: 7 });
      }
    });

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

      // Collision detection (bullet vs alien)
      bullets.forEach((b, bi) => {
        aliens.forEach((a, ai) => {
          if (b.x < a.x + a.width && b.x + b.width > a.x &&
              b.y < a.y + a.height && b.y + b.height > a.y) {
            bullets.splice(bi, 1);
            aliens.splice(ai, 1);
          }
        });
      });

      // Collision detection (alien vs ship)
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

      // Draw health bar
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
