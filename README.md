<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Arpita's Adventure</title>
  <style>
    body { background: #87CEEB; margin: 0; overflow: hidden; }
    canvas { display: block; margin: auto; background: #cceeff; }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Character
    const arpita = { 
      x: 50, y: 300, width: 40, height: 40, 
      dy: 0, jumpPower: -12, gravity: 0.6, speed: 3 
    };

    // Platforms
    const platforms = [
      { x: 0, y: 350, width: 800, height: 50 },
      { x: 200, y: 280, width: 100, height: 20 },
      { x: 400, y: 220, width: 100, height: 20 }
    ];

    let keys = {};

    document.addEventListener("keydown", e => keys[e.code] = true);
    document.addEventListener("keyup", e => keys[e.code] = false);

    function update() {
      // Horizontal movement
      if (keys["ArrowRight"]) arpita.x += arpita.speed;
      if (keys["ArrowLeft"]) arpita.x -= arpita.speed;

      // Jump (Up arrow)
      if (keys["ArrowUp"] && arpita.dy === 0) arpita.dy = arpita.jumpPower;

      // Drop down faster (Down arrow)
      if (keys["ArrowDown"]) arpita.dy += 0.5;

      // Gravity
      arpita.dy += arpita.gravity;
      arpita.y += arpita.dy;

      // Collision with platforms
      platforms.forEach(p => {
        if (arpita.x < p.x + p.width &&
            arpita.x + arpita.width > p.x &&
            arpita.y < p.y + p.height &&
            arpita.y + arpita.height > p.y) {
          arpita.y = p.y - arpita.height;
          arpita.dy = 0;
        }
      });

      // Prevent falling off screen
      if (arpita.y + arpita.height > canvas.height) {
        arpita.y = canvas.height - arpita.height;
        arpita.dy = 0;
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw Arpita
      ctx.fillStyle = "red";
      ctx.fillRect(arpita.x, arpita.y, arpita.width, arpita.height);

      // Draw platforms
      ctx.fillStyle = "green";
      platforms.forEach(p => ctx.fillRect(p.x, p.y, p.width, p.height));
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
