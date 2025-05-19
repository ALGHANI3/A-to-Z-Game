<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Snake ABC Game - Stages & Themes</title>
  <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
  <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
  <style>
    /* Basic reset and global styles */
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      font-family: 'Press Start 2P', cursive;
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      padding: 10px;
      text-align: center;
      transition: background-color 0.5s ease; /* Theme transition */
    }

    h1 {
      font-size: 2rem;
      margin-bottom: 15px;
      text-shadow: 3px 3px #ff00ff;
      color: #00ffff;
    }

    .game-container {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 100%;
      max-width: 420px;
      padding: 15px;
      border-radius: 15px;
      border: 3px solid #00ffff;
      box-shadow: 0 0 20px #00ffff;
      transition: background-color 0.5s ease; /* Theme transition */
    }

    .settings-panel {
      background-color: rgba(0, 0, 0, 0.3);
      padding: 10px;
      border-radius: 10px;
      margin-bottom: 15px;
      width: 100%;
    }

    .settings-panel h3 {
      font-size: 0.9rem;
      margin-bottom: 8px;
      color: #00ffff; /* Cyan title for settings */
    }

    .settings-panel div {
      margin-bottom: 10px;
    }
    .settings-panel div:last-child {
      margin-bottom: 0;
    }

    .setting-btn {
      font-family: 'Press Start 2P', cursive;
      padding: 8px 12px;
      font-size: 0.75rem;
      margin: 3px;
      cursor: pointer;
      background-color: #444;
      color: #fff;
      border: 2px solid #666;
      border-radius: 5px;
      transition: background-color 0.2s, border-color 0.2s;
    }

    .setting-btn.active {
      background-color: #00ffff; /* Cyan for active */
      color: #0a0a23;
      border-color: #00dddd;
    }
    .setting-btn:hover:not(.active) {
      background-color: #555;
    }


    .controls, .score-board {
      margin: 10px 0;
      padding: 10px;
      background-color: rgba(0, 0, 0, 0.5);
      border-radius: 10px;
      width: 100%;
    }

    .score-board p {
      margin: 5px 0;
      font-size: 0.9rem;
    }

    #gameCanvas {
      border: 2px solid #00ffff;
      border-radius: 8px;
      width: 100%;
      height: auto;
      aspect-ratio: 1 / 1;
      max-width: 400px;
      display: block;
      transition: background-color 0.5s ease; /* Theme transition */
    }

    button#startGameBtn, button#restartBtn { /* Target only main game buttons */
      padding: 12px 20px;
      font-size: 1rem;
      font-weight: bold;
      margin: 8px;
      cursor: pointer;
      background: linear-gradient(45deg, #ff00ff, #00ffff);
      color: #0a0a23;
      border: none;
      border-radius: 8px;
      box-shadow: 0 4px #990099;
      transition: all 0.1s ease;
    }

    button#startGameBtn:active, button#restartBtn:active {
      box-shadow: 0 2px #990099;
      transform: translateY(2px);
    }

    button#startGameBtn:hover, button#restartBtn:hover {
      filter: brightness(1.2);
    }

    button#restartBtn {
      background: linear-gradient(45deg, #ff6600, #ffff00);
      box-shadow: 0 4px #cc5200;
    }
    button#restartBtn:active {
      box-shadow: 0 2px #cc5200;
      transform: translateY(2px);
    }

    @media (max-width: 480px) {
      h1 {
        font-size: 1.5rem;
      }
      button#startGameBtn, button#restartBtn {
        font-size: 0.9rem;
        padding: 10px 15px;
      }
      .score-board p {
        font-size: 0.8rem;
      }
      .settings-panel h3 {
        font-size: 0.8rem;
      }
      .setting-btn {
        font-size: 0.7rem;
        padding: 6px 10px;
      }
    }
  </style>
</head>
<body>
  <h1>🐍 Snake ABC Hunter 🕹️</h1>

  <div class="game-container">
    <div class="settings-panel">
      <div>
        <h3>Difficulty:</h3>
        <button class="setting-btn difficulty-btn active" data-difficulty="normal">Normal</button>
        <button class="setting-btn difficulty-btn" data-difficulty="easy">Easy</button>
        <button class="setting-btn difficulty-btn" data-difficulty="hard">Hard</button>
      </div>
      <div>
        <h3>Theme:</h3>
        <button class="setting-btn theme-btn active" data-theme="default">Classic</button>
        <button class="setting-btn theme-btn" data-theme="jungle">Jungle</button>
        <button class="setting-btn theme-btn" data-theme="desert">Desert</button>
      </div>
    </div>

    <div class="controls">
      <button id="startGameBtn" onclick="startGame()">Start Game</button>
    </div>

    <canvas id="gameCanvas"></canvas>

    <div class="score-board">
      <p>Score: <span id="score">0</span></p>
      <p>Best Score: <span id="best">0</span></p>
      <button id="restartBtn" onclick="startGame()" style="display:none;">Try Again</button>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const scoreDisplay = document.getElementById('score');
    const bestDisplay = document.getElementById('best');
    const restartBtn = document.getElementById('restartBtn');
    const startGameBtn = document.getElementById('startGameBtn');
    const gameContainer = document.querySelector('.game-container');

    // Game constants
    const gridSize = 20;
    let canvasSize = 400;
    let tileCount;

    // Game state variables
    const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
    let letterIndex = 0;
    let snake, food, dx, dy, score, bestScore, speed, gameLoopInterval, currentLetter;
    let gameRunning = false;
    let currentDifficulty = 'normal'; // Default difficulty
    let currentTheme = 'default';     // Default theme

    // Theme settings
    const themeSettings = {
        default: {
            bodyBg: '#0a0a23',
            containerBg: '#1c1c3c',
            canvasBg: '#001f3f',
            snakeHead: '#00ff00',
            snakeBody: '#00cc00',
            foodText: '#ffffff',
            messageText: '#ffffff',
            gameOverText: '#ff0000',
            borderColor: '#00ffff',
            h1Color: '#00ffff',
            h1Shadow: '#ff00ff',
            settingsTitleColor: '#00ffff',
            activeButtonBg: '#00ffff',
            activeButtonColor: '#0a0a23'
        },
        jungle: {
            bodyBg: '#228B22',
            containerBg: '#556B2F',
            canvasBg: '#8FBC8F',
            snakeHead: '#FFD700',
            snakeBody: '#DAA520',
            foodText: '#1A1A1A',
            messageText: '#1A1A1A',
            gameOverText: '#8B0000',
            borderColor: '#32CD32',
            h1Color: '#ADFF2F',
            h1Shadow: '#228B22',
            settingsTitleColor: '#ADFF2F',
            activeButtonBg: '#ADFF2F',
            activeButtonColor: '#228B22'
        },
        desert: {
            bodyBg: '#F4A460',
            containerBg: '#D2B48C',
            canvasBg: '#DEB887',
            snakeHead: '#8B4513',
            snakeBody: '#A0522D',
            foodText: '#000000',
            messageText: '#000000',
            gameOverText: '#8B0000',
            borderColor: '#CD853F',
            h1Color: '#8B4513',
            h1Shadow: '#D2691E',
            settingsTitleColor: '#8B4513',
            activeButtonBg: '#8B4513',
            activeButtonColor: '#FFFFFF'
        }
    };

    // Sound synthesis
    let eatSynth, gameOverSynth;

    function setupSounds() {
        if (typeof Tone !== 'undefined') {
            eatSynth = new Tone.Synth({ oscillator: { type: 'sine' }, envelope: { attack: 0.01, decay: 0.1, sustain: 0.01, release: 0.1 } }).toDestination();
            gameOverSynth = new Tone.MonoSynth({ oscillator: { type: 'fmsquare' }, envelope: { attack: 0.05, decay: 0.2, sustain: 0.1, release: 0.5 }, filterEnvelope: { attack: 0.05, decay: 0.1, sustain: 0.05, release: 0.2, baseFrequency: 300, octaves: 2 } }).toDestination();
        } else {
            console.warn("Tone.js not loaded.");
        }
    }

    function applyTheme(themeName) {
        const theme = themeSettings[themeName];
        document.body.style.backgroundColor = theme.bodyBg;
        gameContainer.style.backgroundColor = theme.containerBg;
        gameContainer.style.borderColor = theme.borderColor;
        gameContainer.style.boxShadow = `0 0 20px ${theme.borderColor}`;

        document.querySelector('h1').style.color = theme.h1Color;
        document.querySelector('h1').style.textShadow = `3px 3px ${theme.h1Shadow}`;
        
        document.querySelectorAll('.settings-panel h3').forEach(h3 => h3.style.color = theme.settingsTitleColor);
        document.querySelectorAll('.setting-btn.active').forEach(btn => {
            btn.style.backgroundColor = theme.activeButtonBg;
            btn.style.color = theme.activeButtonColor;
        });
        canvas.style.borderColor = theme.borderColor;


        if (!gameRunning) {
            resizeCanvas(); 
        }
    }


    window.onload = () => {
        setupSounds();
        loadBestScore();
        applyTheme(currentTheme); 
        resizeCanvas();
        window.addEventListener('resize', resizeCanvas);
        setupSettingButtons();
    };

    function setupSettingButtons() {
        // Difficulty buttons
        document.querySelectorAll('.difficulty-btn').forEach(button => {
            button.addEventListener('click', () => {
                if (gameRunning) return; 
                currentDifficulty = button.dataset.difficulty; // Store selected difficulty
                document.querySelectorAll('.difficulty-btn').forEach(btn => btn.classList.remove('active'));
                button.classList.add('active');
                applyTheme(currentTheme); 
            });
        });

        // Theme buttons
        document.querySelectorAll('.theme-btn').forEach(button => {
            button.addEventListener('click', () => {
                if (gameRunning) return;
                currentTheme = button.dataset.theme;
                document.querySelectorAll('.theme-btn').forEach(btn => btn.classList.remove('active'));
                button.classList.add('active');
                applyTheme(currentTheme);
            });
        });
    }


    function resizeCanvas() {
        const containerWidth = canvas.parentElement.clientWidth - 30;
        canvasSize = Math.min(containerWidth, 400);
        canvas.width = canvasSize;
        canvas.height = canvasSize;
        tileCount = Math.floor(canvasSize / gridSize); 

        if (gameRunning) {
            drawGameElements();
        } else {
            const theme = themeSettings[currentTheme];
            ctx.fillStyle = theme.canvasBg;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = theme.messageText;
            ctx.font = `${canvasSize / 20}px 'Press Start 2P'`;
            ctx.textAlign = 'center';
            ctx.fillText('Press Start!', canvas.width / 2, canvas.height / 2);
        }
    }

    function startGame() {
        // Ensure Tone.js context is started by user gesture
        if (typeof Tone !== 'undefined' && Tone.context.state !== 'running') {
            Tone.start().then(setupSounds);
        }

        clearInterval(gameLoopInterval);
        gameRunning = true;
        document.querySelectorAll('.setting-btn').forEach(btn => btn.disabled = true); // Disable setting buttons during game

        // Set speed based on the stored currentDifficulty
        // This is where the selected difficulty impacts game speed
        if (currentDifficulty === 'easy') {
            speed = 200; // Slower
        } else if (currentDifficulty === 'hard') {
            speed = 80;  // Faster
        } else { // 'normal' or default
            speed = 130; // Medium
        }

        dx = gridSize;
        dy = 0;
        let startX = Math.floor(tileCount / 2 - 2) * gridSize;
        let startY = Math.floor(tileCount / 2) * gridSize;
        startX = Math.max(0, Math.min(startX, (tileCount -1) * gridSize));
        startY = Math.max(0, Math.min(startY, (tileCount -1) * gridSize));
        snake = [{ x: startX, y: startY }];

        score = 0;
        scoreDisplay.textContent = score;
        letterIndex = 0;

        restartBtn.style.display = 'none';
        startGameBtn.textContent = 'Restart Game';

        currentLetter = getNextLetter();
        food = getRandomFood();
        gameLoopInterval = setInterval(gameLoop, speed); // Game loop uses the speed set above
    }

    function getNextLetter() {
      const letter = alphabet[letterIndex % alphabet.length];
      letterIndex++;
      return letter;
    }

    function gameLoop() {
      if (!gameRunning) return;
      updateSnakePosition();
      if (checkGameOver()) {
        gameOver();
        return;
      }
      checkFoodCollision();
      drawGameElements();
    }

    function updateSnakePosition() {
      const head = { x: snake[0].x + dx, y: snake[0].y + dy };
      snake.unshift(head);
    }

    function checkFoodCollision() {
      if (snake[0].x === food.x && snake[0].y === food.y) {
        if (eatSynth) eatSynth.triggerAttackRelease('C5', '8n', Tone.now());
        score++;
        scoreDisplay.textContent = score;
        currentLetter = getNextLetter();
        food = getRandomFood();
      } else {
        snake.pop();
      }
    }

    function checkGameOver() {
      const head = snake[0];
      if (head.x < 0 || head.x >= tileCount * gridSize || head.y < 0 || head.y >= tileCount * gridSize) {
        return true;
      }
      for (let i = 1; i < snake.length; i++) {
        if (snake[i].x === head.x && snake[i].y === head.y) {
          return true;
        }
      }
      return false;
    }

    function drawGameElements() {
      const theme = themeSettings[currentTheme];
      ctx.fillStyle = theme.canvasBg;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      snake.forEach((part, index) => {
        ctx.fillStyle = index === 0 ? theme.snakeHead : theme.snakeBody;
        ctx.fillRect(part.x, part.y, gridSize, gridSize);
        ctx.strokeStyle = theme.canvasBg; 
        ctx.strokeRect(part.x, part.y, gridSize, gridSize);
      });

      ctx.font = `${gridSize * 0.8}px 'Press Start 2P'`;
      ctx.fillStyle = theme.foodText;
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.fillText(currentLetter, food.x + gridSize / 2, food.y + gridSize / 2 + 2);
    }

    function getRandomFood() {
      let newFoodPosition;
      while (true) {
          newFoodPosition = {
            x: Math.floor(Math.random() * tileCount) * gridSize,
            y: Math.floor(Math.random() * tileCount) * gridSize
          };
          let collisionWithSnake = false;
          for (const part of snake) {
              if (part.x === newFoodPosition.x && part.y === newFoodPosition.y) {
                  collisionWithSnake = true;
                  break;
              }
          }
          if (!collisionWithSnake && (newFoodPosition.x < tileCount*gridSize && newFoodPosition.y < tileCount*gridSize)) break;
      }
      return newFoodPosition;
    }

    function gameOver() {
      gameRunning = false;
      clearInterval(gameLoopInterval);
      if (gameOverSynth) gameOverSynth.triggerAttackRelease('C3', '0.5n', Tone.now());
      document.querySelectorAll('.setting-btn').forEach(btn => btn.disabled = false); // Re-enable setting buttons


      updateBestScore();
      restartBtn.style.display = 'inline-block';
      startGameBtn.textContent = 'Start Game';

      const theme = themeSettings[currentTheme];
      ctx.fillStyle = 'rgba(0, 0, 0, 0.65)'; 
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.font = `${canvasSize / 12}px 'Press Start 2P'`;
      ctx.fillStyle = theme.gameOverText;
      ctx.textAlign = 'center';
      ctx.fillText('GAME OVER!', canvas.width / 2, canvas.height / 2 - canvasSize / 20);
      ctx.font = `${canvasSize / 20}px 'Press Start 2P'`;
      ctx.fillStyle = theme.messageText;
      ctx.fillText(`Score: ${score}`, canvas.width / 2, canvas.height / 2 + canvasSize / 20);
    }

    function loadBestScore() {
      bestScore = parseInt(localStorage.getItem('snakeABCBestScore')) || 0;
      bestDisplay.textContent = bestScore;
    }

    function updateBestScore() {
      if (score > bestScore) {
        bestScore = score;
        localStorage.setItem('snakeABCBestScore', bestScore);
        bestDisplay.textContent = bestScore;
      }
    }

    document.addEventListener('keydown', e => {
      if (!gameRunning && (e.key.includes('Arrow') || ['w', 'a', 's', 'd'].includes(e.key.toLowerCase()))) return;

      const goingUp = dy === -gridSize;
      const goingDown = dy === gridSize;
      const goingLeft = dx === -gridSize;
      const goingRight = dx === gridSize;

      switch (e.key.toLowerCase()) {
        case 'arrowup': case 'w': if (!goingDown) { dx = 0; dy = -gridSize; } break;
        case 'arrowdown': case 's': if (!goingUp) { dx = 0; dy = gridSize; } break;
        case 'arrowleft': case 'a': if (!goingRight) { dx = -gridSize; dy = 0; } break;
        case 'arrowright': case 'd': if (!goingLeft) { dx = gridSize; dy = 0; } break;
        case ' ': if (!gameRunning) { startGame(); } break;
      }
    });

    let touchStartX = 0, touchStartY = 0;
    canvas.addEventListener('touchstart', function(event) {
        if (!gameRunning) return;
        touchStartX = event.changedTouches[0].screenX;
        touchStartY = event.changedTouches[0].screenY;
        event.preventDefault();
    }, { passive: false });

    canvas.addEventListener('touchend', function(event) {
        if (!gameRunning) return;
        const touchEndX = event.changedTouches[0].screenX;
        const touchEndY = event.changedTouches[0].screenY;
        handleSwipe(touchEndX, touchEndY);
        event.preventDefault();
    }, { passive: false });

    function handleSwipe(touchEndX, touchEndY) {
        const deltaX = touchEndX - touchStartX;
        const deltaY = touchEndY - touchStartY;
        const absDeltaX = Math.abs(deltaX);
        const absDeltaY = Math.abs(deltaY);

        const goingUp = dy === -gridSize;
        const goingDown = dy === gridSize;
        const goingLeft = dx === -gridSize;
        const goingRight = dx === gridSize;

        if (absDeltaX > absDeltaY) {
            if (deltaX > 0 && !goingLeft) { dx = gridSize; dy = 0; }
            else if (deltaX < 0 && !goingRight) { dx = -gridSize; dy = 0; }
        } else if (absDeltaY > absDeltaX) {
            if (deltaY > 0 && !goingUp) { dx = 0; dy = gridSize; }
            else if (deltaY < 0 && !goingDown) { dx = 0; dy = -gridSize; }
        }
    }
  </script>
</body>
</html>
