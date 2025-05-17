<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>A to Z Fun Catch - Learn with Urdu MP3</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        /* Custom styles can go here if needed, but prefer Tailwind utilities */
        body {
            font-family: 'Arial', sans-serif;
        }
        #gameArea {
            position: relative;
            width: 100%; /* Full width of its container */
            height: 70vh; /* Adjusted height for better mobile viewing */
            max-width: 800px; /* Max width for larger screens */
            margin-left: auto; /* Center the game area */
            margin-right: auto; /* Center the game area */
            overflow: hidden;
            background: linear-gradient(to bottom, #a0d8f1, #ffffff);
            border: 2px solid #333;
            border-radius: 8px; /* Added rounded corners */
        }
        .item {
            position: absolute;
            width: 60px; /* Slightly smaller items for mobile */
            height: 60px;
            cursor: pointer;
            transition: transform 0.1s ease-out;
            z-index: 10;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 5px; /* Increased padding */
            text-align: center;
            background-color: white; /* Added background for items */
            border-radius: 8px; /* Rounded corners for items */
            box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2); /* Added subtle shadow */
        }
         /* Increase item size on larger screens */
        @media (min-width: 768px) {
            .item {
                width: 80px;
                height: 80px;
                padding: 8px; /* Increased padding for larger screens */
            }
        }

        .item img {
            width: 100%;
            height: 100%;
            object-fit: contain;
            pointer-events: none;
            margin-bottom: 3px; /* Adjusted space */
        }
         @media (min-width: 768px) {
            .item img {
                 margin-bottom: 5px;
            }
         }

        .item .letter {
            font-size: 1rem; /* Adjusted font size */
            font-weight: bold;
            color: #333;
            background-color: rgba(255, 255, 255, 0.8); /* Temporary background for visibility */
            padding: 0 2px; /* Add a little padding around the letter */
            border-radius: 3px;
        }
         @media (min-width: 768px) {
            .item .letter {
                font-size: 1.2rem;
            }
         }

        .correct {
            border: 3px solid green;
        }
        .incorrect {
            border: 3px solid red;
        }

        /* Styles for screens (start, game, end) */
        #startScreen, #gameScreen, #endScreen {
             /* Ensure screens take full height to center content vertically */
            min-height: calc(100vh - 64px); /* Adjust based on header/footer if any */
            display: flex; /* Use flexbox for centering */
            flex-direction: column; /* Stack content vertically */
            align-items: center; /* Center horizontally */
            justify-content: center; /* Center vertically */
        }

         /* Hide screens by default, controlled by JS */
        #gameScreen, #endScreen {
            display: none;
        }
    </style>
</head>
<body class="bg-gradient-to-t from-blue-200 to-white text-center p-4 min-h-screen flex flex-col">
    <h1 class="text-3xl font-bold mb-4">A to Z Fun Catch - Learn with Urdu MP3</h1>

    <div id="startScreen" class="flex flex-col items-center justify-center flex-grow">
        <h2 class="text-2xl mb-4">Choose Difficulty</h2>
        <div class="flex flex-col space-y-4 md:flex-row md:space-y-0 md:space-x-4">
            <button class="btn bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded shadow text-lg">Easy</button>
            <button class="btn bg-yellow-500 hover:bg-yellow-600 text-white font-bold py-2 px-4 rounded shadow text-lg">Normal</button>
            <button class="btn bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded shadow text-lg">Hard</button>
        </div>
        <p id="bestScoreDisplayStart" class="mt-4 text-gray-700 text-lg"></p>
        <p class="mt-2 text-gray-700 text-base">Instructions: Catch the item that matches the displayed letter and word.</p>
    </div>

    <div id="gameScreen" class="hidden flex-grow">
        <div id="wordDisplay" class="text-2xl font-semibold my-4 text-gray-800"></div>
        <div id="imageDisplay" class="w-24 h-24 md:w-32 md:h-32 mx-auto my-4">
            </div>
        <div id="gameArea"></div>
        <div id="scoreBoard" class="text-xl my-4 text-gray-700"></div>
        <div id="timerDisplay" class="text-xl my-4 text-gray-700"></div>
    </div>

    <div id="endScreen" class="hidden flex-col items-center justify-center flex-grow">
        <h2 class="text-2xl mb-4">Game Over!</h2>
        <p id="finalScore" class="text-xl mb-4 text-gray-800"></p>
        <p id="bestScoreDisplayEnd" class="text-xl mb-4 text-gray-800"></p>
        <button class="btn bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-4 rounded shadow text-lg" onclick="location.reload()">Play Again</button>
    </div>

    <audio id="audioPlayer"></audio>

    <script>
        // Expanded items list for A-Z with actual images (using placeholder sounds for now)
        const items = {
            A: ['Apple', 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/15/Red_Apple.jpg/1200px-Red_Apple.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'],
            B: ['Ball', 'https://upload.wikimedia.org/wikipedia/commons/thumb/d/d3/Soccerball.svg/1024px-Soccerball.svg.png', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3'],
            C: ['Cat', 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Cat03.jpg/1200px-Cat03.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3'],
            D: ['Dog', 'https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Collage_of_Nine_Dogs.jpg/1200px-Collage_of_Nine_Dogs.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3'],
            E: ['Elephant', 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/African_Bush_Elephant.jpg/1200px-African_Bush_Elephant.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3'],
            F: ['Fish', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b0/Light_sussex_hen_and_chick.jpg/1200px-Light_sussex_hen_and_chick.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3'], // Using Chicken as a placeholder for F for now
            G: ['Grape', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/bb/Table_grapes_on_branch.jpg/1200px-Table_grapes_on_branch.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-7.mp3'],
            H: ['House', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b2/Haus_Seestra%C3%9Fe_10_in_Prien.jpg/1200px-Haus_Seestra%C3%9Fe_10_in_Prien.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-8.mp3'],
            I: ['Ice Cream', 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/30/Ice_cream_with_strawberry_sauce%2C_pistachios_and_mint.jpg/1200px-Ice_cream_with_strawberry_sauce%2C_pistachios_and_mint.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-9.mp3'],
            J: ['Jellyfish', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Aurelia_aurita_with_two_shrimp.jpg/1200px-Aurelia_aurita_with_two_shrimp.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-10.mp3'],
            K: ['Kite', 'https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/Red_Delta_Kite.jpg/1200px-Red_Delta_Kite.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'],
            L: ['Lion', 'https://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Lion_waiting_in_Namibia.jpg/1200px-Lion_waiting_in_Namibia.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3'],
            M: ['Monkey', 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/0b/Macaque_de_Berb%C3%A9rie_-_Barbary_macaque.jpg/1200px-Macaque_de_Berb%C3%A9rie_-_Barbary_macaque.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3'],
            N: ['Nest', 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Bird_nest_with_eggs_in_a_tree.jpg/1200px-Bird_nest_with_eggs_in_a_tree.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3'],
            O: ['Orange', 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c4/Oranges-Plateselber.jpg/1200px-Oranges-Plateselber.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3'],
            P: ['Penguin', 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/07/Emperor_Penguin_Manchot_empereur.jpg/1200px-Emperor_Penguin_Manchot_empereur.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3'],
            Q: ['Queen', 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/17/Queen_Elizabeth_II_in_Canada_1957.jpg/1200px-Queen_Elizabeth_II_in_Canada_1957.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-7.mp3'], // Using Queen Elizabeth II as an example
            R: ['Rabbit', 'https://upload.wikimedia.org/wikipedia/commons/thumb/1/1f/Oryctolagus_cuniculus_cottontail_rabbit.jpg/1200px-Oryctolagus_cuniculus_cottontail_rabbit.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-8.mp3'],
            S: ['Sun', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b4/The_Sun_by_the_Atmospheric_Imaging_Assembly_of_NASA%27s_Solar_Dynamics_Observatory_-_20100819.jpg/1200px-The_Sun_by_the_Atmospheric_Imaging_Assembly_of_NASA%27s_Solar_Dynamics_Observatory_-_20100819.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-9.mp3'],
            T: ['Tiger', 'https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tigre_dans_la_jungle_-_Tiger_in_the_jungle_%285216515081%29.jpg/1200px-Tigre_dans_la_jungle_-_Tiger_in_the_jungle_%285216515081%29.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-10.mp3'],
            U: ['Umbrella', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b9/Umbrella_transparent_background.png/1024px-Umbrella_transparent_background.png', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3'],
            V: ['Violin', 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/c0/Violin_by_Antonio_Stradivari%2C_1721_-_Symphony_Hall%2C_Phoenix%2C_Arizona_-_DSC07621.JPG/1200px-Violin_by_Antonio_Stradivari%2C_1721_-_Symphony_Hall%2C_Arizona_-_DSC07621.JPG', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3'],
            W: ['Wolf', 'https://upload.wikimedia.org/wikipedia/commons/thumb/5/5f/Kolm%C3%A5rden_Wolf.jpg/1200px-Kolm%C3%A5rden_Wolf.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3'],
            X: ['Xylophone', 'https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/Xylophone_with_mallets.jpg/1200px-Xylophone_with_mallets.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3'],
            Y: ['Yacht', 'https://upload.wikimedia.org/wikipedia/commons/thumb/b/b9/Sailing_yacht_Adix_in_Falmouth_Harbour.jpg/1200px-Sailing_yacht_Adix_in_Falmouth_Harbour.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3'],
            Z: ['Zebra', 'https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Zebra_Kenya_-_05.jpg/1200px-Zebra_Kenya_-_05.jpg', 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3'],
        };

        let score = 0;
        let mode = 'easy';
        let currentLetterIndex = 0;
        let gameInterval;
        let itemFallIntervals = []; // To keep track of falling item intervals
        const letters = Object.keys(items);
        let maxLettersToPlay = 0;
        let fallSpeed = 2; // Pixels per interval (Adjusted default speed)
        let fallIntervalTime = 30; // Milliseconds per interval

        // Get DOM elements
        const startScreen = document.getElementById('startScreen');
        const gameScreen = document.getElementById('gameScreen');
        const endScreen = document.getElementById('endScreen');
        const wordDisplay = document.getElementById('wordDisplay');
        const imageDisplay = document.getElementById('imageDisplay');
        const gameArea = document.getElementById('gameArea');
        const scoreBoard = document.getElementById('scoreBoard');
        const timerDisplay = document.getElementById('timerDisplay');
        const audioPlayer = document.getElementById('audioPlayer');
        const finalScore = document.getElementById('finalScore');
        const bestScoreDisplayStart = document.getElementById('bestScoreDisplayStart');
        const bestScoreDisplayEnd = document.getElementById('bestScoreDisplayEnd');

        const BEST_SCORE_STORAGE_KEY = 'aToZCatchBestScore';

        // Function to get best score from local storage
        function getBestScore() {
            const bestScore = localStorage.getItem(BEST_SCORE_STORAGE_KEY);
            return bestScore ? parseInt(bestScore, 10) : 0;
        }

        // Function to set best score in local storage
        function setBestScore(newBestScore) {
            localStorage.setItem(BEST_SCORE_STORAGE_KEY, newBestScore);
        }

        // Display best score on start screen when the page loads
        document.addEventListener('DOMContentLoaded', () => {
            displayBestScore();
             // Display start screen initially (handled by CSS now, but good to keep for clarity if needed)
            // startScreen.style.display = 'flex';
        });


        // Function to display the best score on both start and end screens
        function displayBestScore() {
            const bestScore = getBestScore();
            bestScoreDisplayStart.textContent = `Best Score: ${bestScore}`;
            bestScoreDisplayEnd.textContent = `Best Score: ${bestScore}`;
        }

        function playAudio(url) {
            audioPlayer.src = url;
            audioPlayer.play();
        }

        // Add event listeners to difficulty buttons
        document.querySelectorAll('.btn').forEach(button => {
            button.addEventListener('click', function() {
                startGame(this.textContent.toLowerCase());
            });
        });


        function startGame(selectedMode) {
            mode = selectedMode;
            score = 0;
            currentLetterIndex = 0;
            itemFallIntervals = []; // Clear previous intervals

            // Set game parameters based on difficulty
            if (mode === 'easy') {
                maxLettersToPlay = 5; // Play with first 5 letters
                fallSpeed = 2;
                fallIntervalTime = 30;
            } else if (mode === 'normal') {
                maxLettersToPlay = 10; // Play with first 10 letters
                fallSpeed = 3;
                fallIntervalTime = 25;
            } else if (mode === 'hard') {
                maxLettersToPlay = letters.length; // Play with all letters
                fallSpeed = 4;
                fallIntervalTime = 20;
            }

            startScreen.style.display = 'none';
            gameScreen.style.display = 'flex'; // Show game screen
            endScreen.style.display = 'none'; // Hide end screen

            scoreBoard.textContent = `Score: ${score}`;
            timerDisplay.textContent = `Time: --`; // Placeholder for timer

            runGame();
        }

        function runGame() {
            // Clear any existing items and their fall intervals
            gameArea.innerHTML = '';
            itemFallIntervals.forEach(clearInterval);
            itemFallIntervals = [];

            if (currentLetterIndex >= maxLettersToPlay) {
                endGame();
                return;
            }

            const currentLetter = letters[currentLetterIndex];
            const [correctWord, correctImg, correctSound] = items[currentLetter];

            wordDisplay.textContent = `${currentLetter} for ${correctWord}`;
            imageDisplay.innerHTML = `<img src="${correctImg}" alt="${correctWord}" class="rounded-md shadow-md" onerror="this.onerror=null; this.src='https://placehold.co/80x80/CCCCCC/000000?text=Error';" />`; // Add error handling for images

            const options = [[currentLetter, correctWord, correctImg, correctSound, true]]; // Added currentLetter to options

            // Add incorrect options
            while (options.length < 3) { // Always show 3 options
                const randomLetter = letters[Math.floor(Math.random() * letters.length)];
                const [word, img, mp3] = items[randomLetter];
                 // Ensure the random word is not already in options and is not the correct word
                if (!options.find(opt => opt[1] === word) && word !== correctWord) { // Check against word
                    options.push([randomLetter, word, img, mp3, false]); // Added randomLetter
                }
            }

            shuffle(options);

            // Create and animate falling items
            options.forEach(([letter, word, img, mp3, isCorrect]) => { // Destructure to get letter
                const el = document.createElement('div');
                el.className = `item bg-white rounded-md shadow-md`; // Removed flex/center classes from here
                // Calculate random left position, ensuring item stays within bounds
                const maxLeft = gameArea.offsetWidth - 60; // Use item width (60px)
                el.style.left = `${Math.random() * maxLeft}px`;
                el.style.top = '0px';
                // Include both image and letter
                el.innerHTML = `
                    <img src="${img}" alt="${word}" title="${word}" class="rounded-md" onerror="this.onerror=null; this.src='https://placehold.co/60x60/CCCCCC/000000?text=Error';" /> <span class="letter">${letter}</span>
                `;

                gameArea.appendChild(el);

                // Animate fall
                const fallInterval = setInterval(() => {
                    let top = parseInt(el.style.top, 10);
                    top += fallSpeed;
                    el.style.top = `${top}px`;

                    // Remove item if it falls off the screen
                    if (top > gameArea.offsetHeight) {
                        clearInterval(fallInterval);
                        el.remove();
                        // Optional: Penalize for missing the correct item
                        if (isCorrect) {
                           // Handle miss penalty if desired
                        }
                    }
                }, fallIntervalTime);
                itemFallIntervals.push(fallInterval); // Store interval ID

                // Click handler for the item
                el.onclick = () => {
                    // Clear all fall intervals for this round's items
                    itemFallIntervals.forEach(clearInterval);
                    itemFallIntervals = [];
                    gameArea.innerHTML = ''; // Remove all items immediately

                    if (isCorrect) {
                        score++;
                        scoreBoard.textContent = `Score: ${score}`;
                        playAudio(mp3);
                        // Add visual feedback (optional, as items are removed quickly)
                        // el.classList.add('correct');
                    } else {
                        playAudio('https://www.soundhelix.com/examples/mp3/SoundHelix-Song-10.mp3'); // Incorrect sound
                        // Add visual feedback (optional)
                         // el.classList.add('incorrect');
                         // Optional: Penalize score for incorrect catch
                         // score = Math.max(0, score - 1);
                         // scoreBoard.textContent = `Score: ${score}`;
                    }

                    // Move to the next letter after a short delay
                    setTimeout(() => {
                        currentLetterIndex++;
                        runGame();
                    }, 500); // Short delay to show feedback
                }
            });
        }

        function endGame() {
            clearInterval(gameInterval); // Clear the main game interval if used
            itemFallIntervals.forEach(clearInterval); // Clear any remaining fall intervals
            itemFallIntervals = [];
            gameArea.innerHTML = ''; // Clear game area

            // Check and update best score
            const currentBestScore = getBestScore();
            if (score > currentBestScore) {
                setBestScore(score);
            }

            // Display final score and updated best score
            gameScreen.style.display = 'none';
            endScreen.style.display = 'flex'; // Show end screen
            finalScore.textContent = `You scored ${score} out of ${maxLettersToPlay}`;
            displayBestScore(); // Update best score display on end screen
        }

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
        }

        // Optional: Implement a game timer
        // function startTimer(durationInSeconds) {
        //     let timer = durationInSeconds;
        //     const countdown = setInterval(() => {
        //         const minutes = Math.floor(timer / 60);
        //         const seconds = timer % 60;
        //         timerDisplay.textContent = `Time: ${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
        //
        //         if (--timer < 0) {
        //             clearInterval(countdown);
        //             endGame(); // End game when timer runs out
        //         }
        //     }, 1000);
        //     // Store timer interval if needed for clearing on game end
        // }

    </script>
</body>
</html>
