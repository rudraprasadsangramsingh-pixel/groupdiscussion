<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Arpita's Adventure</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; margin: auto; }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    let levelIndex = 0;
    let lives = 3;

    // Levels inspired by Mario
    const levels = [
      {
        background: "#87CEEB", // sky blue
        platforms: [{ x: 0, y: 350, width: 800, height: 50 }],
        enemies: [{ x: 400, y: 310, width: 40, height: 40, dir: 1 }]
      },
      {
        background: "#444", // cave
        platforms: [{ x: 0, y: 350, width: 800, height: 50 }, { x: 300, y: 280, width: 100, height: 20 }],
        enemies: [{ x: 200, y: 310, width: 40, height: 40, dir: -1 }]
      }
    ];

    // Character
    const arpita = { x: 50, y: 300, width: 40, height: 40, dy: 0, jumpPower: -12, gravity: 0.6, speed: 3 };

    let keys = {};
    document.addEventListener("keydown", e => keys[e.code] = true);
    document.addEventListener("keyup", e => keys[e.code] = false);

    function resetGame() {
      lives = 3;
      levelIndex = 0;
      arpita.x = 50; arpita.y = 300;
    }

    function update() {
      const level = levels[levelIndex];

      // Movement
      if (keys["ArrowRight"]) arpita.x += arpita.speed;
      if (keys["ArrowLeft"]) arpita.x -= arpita.speed;
      if (keys["ArrowUp"] && arpita.dy === 0) arpita.dy = arpita.jumpPower;
      if (keys["ArrowDown"]) arpita.dy += 0.5;

      // Gravity
      arpita.dy += arpita.gravity;
      arpita.y += arpita.dy;

      // Platform collision
      level.platforms.forEach(p => {
        if (arpita.x < p.x + p.width &&
            arpita.x + arpita.width > p.x &&
            arpita.y < p.y + p.height &&
            arpita.y + arpita.height > p.y) {
          arpita.y = p.y - arpita.height;
          arpita.dy = 0;
        }
      });

      // Enemy movement + collision
      level.enemies.forEach(e => {
        e.x += e.dir * 2;
        if (e.x < 0 || e.x + e.width > canvas.width) e.dir *= -1;

        // Collision with Arpita
        if (arpita.x < e.x + e.width &&
            arpita.x + arpita.width > e.x &&
            arpita.y < e.y + e.height &&
            arpita.y + arpita.height > e.y) {
          // Jumping on enemy
          if (arpita.dy > 0 && arpita.y + arpita.height - 5 < e.y) {
            level.enemies.splice(level.enemies.indexOf(e), 1);
            arpita.dy = arpita.jumpPower; // bounce
          } else {
            lives--;
            arpita.x = 50; arpita.y = 300;
            if (lives <= 0) resetGame();
          }
        }
      });

      // Next level trigger
      if (arpita.x > canvas.width) {
        levelIndex = (levelIndex + 1) % levels.length;
        arpita.x = 50; arpita.y = 300;
      }
    }

    function draw() {
      const level = levels[levelIndex];
      ctx.fillStyle = level.background;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      // Draw Arpita
      ctx.fillStyle = "red";
      ctx.fillRect(arpita.x, arpita.y, arpita.width, arpita.height);

      // Draw platforms
      ctx.fillStyle = "green";
      level.platforms.forEach(p => ctx.fillRect(p.x, p.y, p.width, p.height));

      // Draw enemies
      ctx.fillStyle = "brown";
      level.enemies.forEach(e => ctx.fillRect(e.x, e.y, e.width, e.height));

      // Draw lives
      ctx.fillStyle = "black";
      ctx.font = "20px Arial";
      ctx.fillText("Lives: " + lives, 10, 20);
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
