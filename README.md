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
      overflow: hidden; /* Prevent scrollbars from minor animation overflows or layout shifts */
    }

    h1 {
      font-size: 2rem; /* Responsive base */
      margin-bottom: 15px;
      text-shadow: 3px 3px #ff00ff;
      color: #00ffff;
    }

    .game-container {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 100%; /* Take full available width */
      max-width: 420px; /* Max width for larger screens */
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
      width: 100%; /* Occupy full width of its parent container */
      max-width: 400px; /* Absolute maximum physical width */
      max-height: 65vh; /* Max height relative to viewport, good for landscape */
      height: auto; /* Let aspect-ratio determine height based on width */
      aspect-ratio: 1 / 1; /* Maintain square shape */
      display: block; /* Remove extra space below */
      margin-left: auto; /* Center canvas if constrained by max-width/max-height */
      margin-right: auto;
      transition: background-color 0.5s ease, border-color 0.5s ease;
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

    /* Responsive adjustments for smaller screens */
    @media (max-width: 480px) {
      h1 {
        font-size: 1.5rem; /* Smaller H1 on small screens */
      }
      .game-container {
        padding: 10px; /* Reduce padding on game container */
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
    @media (max-width: 360px) { /* Even smaller screens */
        h1 {
            font-size: 1.3rem;
        }
        .settings-panel h3 {
            font-size: 0.75rem;
        }
        .setting-btn {
            font-size: 0.65rem;
            padding: 5px 8px;
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
    const gridSize = 20; // Size of each grid cell in the game logic
    let canvasSize; // Actual pixel size of the canvas (determined by CSS and viewport)
    let tileCount;  // Number of grid cells that fit into the canvas

    // Game state variables
    const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
    let letterIndex = 0;
    let snake, food, dx, dy, score, bestScore, speed, gameLoopInterval, currentLetter;
    let gameRunning = false;
    let currentDifficulty = 'normal';
    let currentTheme = 'default';

    // Background animation particles
    let particles = [];
    const MAX_PARTICLES = 30;


    // Theme settings
    const themeSettings = {
        default: {
            bodyBg: '#0a0a23',
            containerBg: '#1c1c3c',
            canvasBg: '#001f3f',
            snakeHead: '#00ff00',
            snakeBody: '#00cc00',
            snakeEyes: '#ffffff',
            foodText: '#ffffff',
            messageText: '#ffffff',
            gameOverText: '#ff0000',
            borderColor: '#00ffff',
            h1Color: '#00ffff',
            h1Shadow: '#ff00ff',
            settingsTitleColor: '#00ffff',
            activeButtonBg: '#00ffff',
            activeButtonColor: '#0a0a23',
            particleColor: 'rgba(0, 255, 255, 0.5)',
            particleSpeedY: 0.5,
            particleSpeedX: 0
        },
        jungle: {
            bodyBg: '#228B22',
            containerBg: '#556B2F',
            canvasBg: '#8FBC8F',
            snakeHead: '#FFD700',
            snakeBody: '#DAA520',
            snakeEyes: '#000000',
            foodText: '#1A1A1A',
            messageText: '#1A1A1A',
            gameOverText: '#8B0000',
            borderColor: '#32CD32',
            h1Color: '#ADFF2F',
            h1Shadow: '#228B22',
            settingsTitleColor: '#ADFF2F',
            activeButtonBg: '#ADFF2F',
            activeButtonColor: '#228B22',
            particleColor: 'rgba(46, 139, 87, 0.7)',
            particleSpeedY: 0.7,
            particleSpeedX: 0.2
        },
        desert: {
            bodyBg: '#F4A460',
            containerBg: '#D2B48C',
            canvasBg: '#DEB887',
            snakeHead: '#8B4513',
            snakeBody: '#A0522D',
            snakeEyes: '#FFFFFF',
            foodText: '#000000',
            messageText: '#000000',
            gameOverText: '#8B0000',
            borderColor: '#CD853F',
            h1Color: '#8B4513',
            h1Shadow: '#D2691E',
            settingsTitleColor: '#8B4513',
            activeButtonBg: '#8B4513',
            activeButtonColor: '#FFFFFF',
            particleColor: 'rgba(210, 180, 140, 0.6)',
            particleSpeedY: 0.3,
            particleSpeedX: 0.5
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
    
    function initParticles() {
        particles = [];
        if (!canvasSize) return; // Don't init if canvasSize isn't set yet
        for (let i = 0; i < MAX_PARTICLES; i++) {
            particles.push({
                x: Math.random() * canvasSize,
                y: Math.random() * canvasSize,
                size: Math.random() * 3 + 1,
            });
        }
    }

    function drawAndUpdateParticles() {
        if (!canvasSize) return; // Don't draw if canvasSize isn't set
        const theme = themeSettings[currentTheme];
        ctx.fillStyle = theme.particleColor;
        particles.forEach(p => {
            ctx.beginPath();
            ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
            ctx.fill();

            p.y += theme.particleSpeedY * (p.size / 2) ; 
            p.x += (Math.random() - 0.5) * theme.particleSpeedX;

            if (p.y > canvasSize + p.size) {
                p.y = -p.size;
                p.x = Math.random() * canvasSize;
            }
            if (p.x > canvasSize + p.size) p.x = -p.size;
            else if (p.x < -p.size) p.x = canvasSize + p.size;
        });
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
        // Particles will be re-initialized by resizeCanvas, which is called after applyTheme or if game not running
        
        if (!gameRunning) {
            resizeCanvas(); // This will redraw canvas with new theme and particles
        } else {
             initParticles(); // If game is running, just re-init particles for next draw cycle
        }
    }

    window.onload = () => {
        setupSounds();
        loadBestScore();
        // resizeCanvas will be called, which then calls applyTheme elements for the first time
        resizeCanvas(); // Initial sizing and theme application through its logic
        window.addEventListener('resize', resizeCanvas);
        setupSettingButtons();
        applyTheme(currentTheme); // Explicitly apply initial theme settings to all elements
    };

    function setupSettingButtons() {
        document.querySelectorAll('.difficulty-btn').forEach(button => {
            button.addEventListener('click', () => {
                if (gameRunning) return; 
                currentDifficulty = button.dataset.difficulty;
                document.querySelectorAll('.difficulty-btn').forEach(btn => btn.classList.remove('active'));
                button.classList.add('active');
                // Re-apply theme to update active button colors if they depend on theme
                const activeThemeBtn = document.querySelector('.theme-btn.active');
                applyTheme(activeThemeBtn ? activeThemeBtn.dataset.theme : currentTheme);
            });
        });

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
        // Get the actual computed size of the canvas element after CSS styling
        // This considers width:100%, max-width, max-height, aspect-ratio
        canvasSize = canvas.clientWidth; 

        // Set the internal drawing buffer resolution to match the displayed size
        canvas.width = canvasSize;
        canvas.height = canvasSize;

        // Calculate how many grid cells fit into the new canvas size
        tileCount = Math.floor(canvasSize / gridSize); 
        
        initParticles(); // Re-initialize particles to fit new canvas size and theme

        if (gameRunning) {
            drawGameElements(); // If game is running, redraw it with new dimensions
        } else {
            // If game is not running, draw the initial "Press Start" message or theme background
            const theme = themeSettings[currentTheme]; // Ensure theme is current
            ctx.fillStyle = theme.canvasBg;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            drawAndUpdateParticles(); // Draw particles on initial screen
            ctx.fillStyle = theme.messageText;
            // Ensure font size is not too small, scaled with canvas size
            ctx.font = `${Math.max(10, Math.floor(canvasSize / 22))}px 'Press Start 2P'`; 
            ctx.textAlign = 'center';
            ctx.fillText('Press Start!', canvas.width / 2, canvas.height / 2);
        }
    }

    function startGame() {
        if (typeof Tone !== 'undefined' && Tone.context.state !== 'running') {
            Tone.start().then(setupSounds);
        }

        clearInterval(gameLoopInterval);
        gameRunning = true;
        document.querySelectorAll('.setting-btn').forEach(btn => btn.disabled = true);

        if (currentDifficulty === 'easy') speed = 200;
        else if (currentDifficulty === 'hard') speed = 80;
        else speed = 130;

        dx = gridSize;
        dy = 0;
        // Ensure snake starts within the potentially smaller tileCount & on the grid
        let startX = Math.floor(tileCount / 2 - 2) * gridSize;
        let startY = Math.floor(tileCount / 2) * gridSize;
        startX = Math.max(0, Math.min(startX, (tileCount - 1) * gridSize));
        startY = Math.max(0, Math.min(startY, (tileCount - 1) * gridSize));

        snake = [{ x: startX, y: startY }];

        score = 0;
        scoreDisplay.textContent = score;
        letterIndex = 0;

        restartBtn.style.display = 'none';
        startGameBtn.textContent = 'Restart Game';

        currentLetter = getNextLetter();
        food = getRandomFood();
        // Particles should be initialized by resizeCanvas or applyTheme already
        gameLoopInterval = setInterval(gameLoop, speed);
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
      // Check wall collision based on the actual game grid area
      if (head.x < 0 || head.x >= tileCount * gridSize || head.y < 0 || head.y >= tileCount * gridSize) {
        return true;
      }
      // Check self-collision
      for (let i = 1; i < snake.length; i++) {
        if (snake[i].x === head.x && snake[i].y === head.y) {
          return true;
        }
      }
      return false;
    }

    function drawSnakeEyes(head, theme) {
        ctx.fillStyle = theme.snakeEyes;
        const eyeSize = Math.max(2, gridSize / 5); // Ensure eyes are visible
        const eyeOffset = Math.max(2, gridSize / 4);

        if (dx > 0) { // Moving Right
            ctx.fillRect(head.x + gridSize - eyeOffset - eyeSize, head.y + eyeOffset, eyeSize, eyeSize);
            ctx.fillRect(head.x + gridSize - eyeOffset - eyeSize, head.y + gridSize - eyeOffset - eyeSize, eyeSize, eyeSize);
        } else if (dx < 0) { // Moving Left
            ctx.fillRect(head.x + eyeOffset, head.y + eyeOffset, eyeSize, eyeSize);
            ctx.fillRect(head.x + eyeOffset, head.y + gridSize - eyeOffset - eyeSize, eyeSize, eyeSize);
        } else if (dy > 0) { // Moving Down
            ctx.fillRect(head.x + eyeOffset, head.y + gridSize - eyeOffset - eyeSize, eyeSize, eyeSize);
            ctx.fillRect(head.x + gridSize - eyeOffset - eyeSize, head.y + gridSize - eyeOffset - eyeSize, eyeSize, eyeSize);
        } else if (dy < 0) { // Moving Up
            ctx.fillRect(head.x + eyeOffset, head.y + eyeOffset, eyeSize, eyeSize);
            ctx.fillRect(head.x + gridSize - eyeOffset - eyeSize, head.y + eyeOffset, eyeSize, eyeSize);
        }
    }

    function drawGameElements() {
      const theme = themeSettings[currentTheme];
      ctx.fillStyle = theme.canvasBg;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      drawAndUpdateParticles();

      snake.forEach((part, index) => {
        ctx.fillStyle = index === 0 ? theme.snakeHead : theme.snakeBody;
        ctx.fillRect(part.x, part.y, gridSize, gridSize);
        // Stroke for slight separation, using canvas background for subtlety
        ctx.strokeStyle = theme.canvasBg; 
        ctx.strokeRect(part.x, part.y, gridSize, gridSize);
        if (index === 0) {
            drawSnakeEyes(part, theme);
        }
      });

      ctx.font = `${Math.max(8, Math.floor(gridSize * 0.8))}px 'Press Start 2P'`; // Ensure food letter is visible
      ctx.fillStyle = theme.foodText;
      ctx.textAlign = 'center';
      ctx.textBaseline = 'middle';
      ctx.strokeStyle = theme.canvasBg; 
      ctx.lineWidth = 2; // Adjust outline thickness as needed
      ctx.strokeText(currentLetter, food.x + gridSize / 2, food.y + gridSize / 2 + Math.floor(gridSize/10)); // Minor y-offset for better centering
      ctx.fillText(currentLetter, food.x + gridSize / 2, food.y + gridSize / 2 + Math.floor(gridSize/10));
    }

    function getRandomFood() {
      let newFoodPosition;
      // Ensure tileCount is valid before trying to place food
      if (tileCount <= 0) { 
          console.error("tileCount is invalid, cannot place food.");
          // Default to a safe off-screen position or handle error appropriately
          return {x: -gridSize, y: -gridSize}; 
      }
      while (true) {
          newFoodPosition = {
            x: Math.floor(Math.random() * tileCount) * gridSize,
            y: Math.floor(Math.random() * tileCount) * gridSize
          };
          let collisionWithSnake = false;
          if(snake) { // Ensure snake exists before checking collision
            for (const part of snake) {
                if (part.x === newFoodPosition.x && part.y === newFoodPosition.y) {
                    collisionWithSnake = true;
                    break;
                }
            }
          }
          // Ensure food is within the visible grid defined by tileCount * gridSize
          if (!collisionWithSnake && 
              newFoodPosition.x >= 0 && newFoodPosition.x < tileCount * gridSize &&
              newFoodPosition.y >= 0 && newFoodPosition.y < tileCount * gridSize) {
            break;
          }
      }
      return newFoodPosition;
    }

    function gameOver() {
      gameRunning = false;
      clearInterval(gameLoopInterval);
      if (gameOverSynth) gameOverSynth.triggerAttackRelease('C3', '0.5n', Tone.now());
      document.querySelectorAll('.setting-btn').forEach(btn => btn.disabled = false);

      updateBestScore();
      restartBtn.style.display = 'inline-block';
      startGameBtn.textContent = 'Start Game';

      const theme = themeSettings[currentTheme];
      ctx.fillStyle = 'rgba(0, 0, 0, 0.65)'; 
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      drawAndUpdateParticles(); 
      ctx.font = `${Math.max(12, Math.floor(canvasSize / 12))}px 'Press Start 2P'`;
      ctx.fillStyle = theme.gameOverText;
      ctx.textAlign = 'center';
      ctx.fillText('GAME OVER!', canvas.width / 2, canvas.height / 2 - Math.floor(canvasSize / 20));
      ctx.font = `${Math.max(10, Math.floor(canvasSize / 22))}px 'Press Start 2P'`;
      ctx.fillStyle = theme.messageText;
      ctx.fillText(`Score: ${score}`, canvas.width / 2, canvas.height / 2 + Math.floor(canvasSize / 20));
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

        if (absDeltaX > absDeltaY) { // Horizontal swipe
            if (deltaX > 0 && !goingLeft) { dx = gridSize; dy = 0; }
            else if (deltaX < 0 && !goingRight) { dx = -gridSize; dy = 0; }
        } else if (absDeltaY > absDeltaX) { // Vertical swipe
            if (deltaY > 0 && !goingUp) { dx = 0; dy = gridSize; }
            else if (deltaY < 0 && !goingDown) { dx = 0; dy = -gridSize; }
        }
    }
  </script>
</body>
</html>
