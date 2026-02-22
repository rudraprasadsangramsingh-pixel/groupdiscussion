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
    const ship = { x: 225, y: 550, width: 50, height: 30, speed: 5 };

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
      // Move ship
      if (keys["ArrowLeft"] && ship.x > 0) ship.x -= ship.speed;
      if (keys["ArrowRight"] && ship.x < canvas.width - ship.width) ship.x += ship.speed;

      // Move bullets
      bullets.forEach(b => b.y -= b.speed);
      bullets = bullets.filter(b => b.y > 0);

      // Move aliens
      aliens.forEach(a => a.y += a.speed);

      // Collision detection
      bullets.forEach((b, bi) => {
        aliens.forEach((a, ai) => {
          if (b.x < a.x + a.width && b.x + b.width > a.x &&
              b.y < a.y + a.height && b.y + b.height > a.y) {
            bullets.splice(bi, 1);
            aliens.splice(ai, 1);
          }
        });
      });
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

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
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
