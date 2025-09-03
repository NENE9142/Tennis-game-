# Tennis-gama


<!DOCTYPE html>
<html lang="he">
<head>
  <meta charset="UTF-8">
  <title>משחק טניס 🎾</title>
  <style>
    body { margin: 0; background: black; display: flex; flex-direction: column; align-items: center; }
    canvas { background: #111; display: block; margin-top: 20px; }
    #menu, #difficultyMenu { margin-top: 20px; text-align: center; }
    button { padding: 10px 20px; margin: 5px; font-size: 18px; cursor: pointer; border-radius: 10px; border: none; }
    h1, h2 { color: white; }
  </style>
</head>
<body>
  <h1>🎾 משחק טניס</h1>
  <div id="menu">
    <button onclick="showDifficultyMenu()">שחק נגד מחשב</button>
    <button onclick="startGame('2p')">שני שחקנים</button>
  </div>

  <div id="difficultyMenu" style="display:none;">
    <h2>בחר רמת קושי:</h2>
    <button onclick="startGame('ai','easy')">קל</button>
    <button onclick="startGame('ai','medium')">בינוני</button>
    <button onclick="startGame('ai','hard')">קשה</button>
  </div>

  <canvas id="gameCanvas" width="800" height="500"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    let upPressed = false, downPressed = false;
    let wPressed = false, sPressed = false;

    const paddleHeight = 80, paddleWidth = 10;
    let leftY = canvas.height/2 - paddleHeight/2;
    let rightY = canvas.height/2 - paddleHeight/2;

    let ballX = canvas.width/2, ballY = canvas.height/2;
    let ballDX = 4, ballDY = 4, ballSize = 10;

    let gameMode = null; // "ai" or "2p"
    let aiSpeed = 3;     // מהירות ברירת מחדל של המחשב
    let scoreLeft = 0, scoreRight = 0;

    document.addEventListener("keydown", keyDownHandler);
    document.addEventListener("keyup", keyUpHandler);

    function keyDownHandler(e) {
      if (e.key === "ArrowUp") upPressed = true;
      if (e.key === "ArrowDown") downPressed = true;
      if (e.key === "w") wPressed = true;
      if (e.key === "s") sPressed = true;
    }
    function keyUpHandler(e) {
      if (e.key === "ArrowUp") upPressed = false;
      if (e.key === "ArrowDown") downPressed = false;
      if (e.key === "w") wPressed = false;
      if (e.key === "s") sPressed = false;
    }

    function showDifficultyMenu() {
      document.getElementById("menu").style.display = "none";
      document.getElementById("difficultyMenu").style.display = "block";
    }

    function startGame(mode, difficulty=null) {
      gameMode = mode;
      if (mode === "ai") {
        if (difficulty === "easy") aiSpeed = 2;
        if (difficulty === "medium") aiSpeed = 4;
        if (difficulty === "hard") aiSpeed = 6;
      }
      document.getElementById("menu").style.display = "none";
      document.getElementById("difficultyMenu").style.display = "none";
      resetBall();
      setInterval(draw, 1000/60);
    }

    function resetBall() {
      ballX = canvas.width/2;
      ballY = canvas.height/2;
      ballDX = (Math.random() > 0.5 ? 4 : -4);
      ballDY = (Math.random() > 0.5 ? 4 : -4);
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // ציור מחבטים
      ctx.fillStyle = "white";
      ctx.fillRect(20, leftY, paddleWidth, paddleHeight);
      ctx.fillRect(canvas.width-30, rightY, paddleWidth, paddleHeight);

      // ציור כדור
      ctx.beginPath();
      ctx.arc(ballX, ballY, ballSize, 0, Math.PI*2);
      ctx.fill();

      // ציור תוצאה
      ctx.font = "24px Arial";
      ctx.fillText(scoreLeft, canvas.width/4, 30);
      ctx.fillText(scoreRight, canvas.width*3/4, 30);

      // תנועה שחקן שמאל
      if (wPressed && leftY > 0) leftY -= 5;
      if (sPressed && leftY < canvas.height-paddleHeight) leftY += 5;

      // תנועה שחקן ימין
      if (gameMode === "2p") {
        if (upPressed && rightY > 0) rightY -= 5;
        if (downPressed && rightY < canvas.height-paddleHeight) rightY += 5;
      } else if (gameMode === "ai") {
        if (ballY < rightY + paddleHeight/2) rightY -= aiSpeed;
        else if (ballY > rightY + paddleHeight/2) rightY += aiSpeed;
      }

      // תנועה כדור
      ballX += ballDX;
      ballY += ballDY;

      // התנגשויות בקירות
      if (ballY - ballSize < 0 || ballY + ballSize > canvas.height) ballDY *= -1;

      // התנגשויות במחבטים
      if (ballX - ballSize < 30 && ballY > leftY && ballY < leftY + paddleHeight) ballDX *= -1;
      if (ballX + ballSize > canvas.width-30 && ballY > rightY && ballY < rightY + paddleHeight) ballDX *= -1;

      // נקודות
      if (ballX < 0) { scoreRight++; resetBall(); }
      if (ballX > canvas.width) { scoreLeft++; resetBall(); }
    }
  </script>
</body>
</html>






