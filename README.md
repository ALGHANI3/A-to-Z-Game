# A-to-Z-Game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>A to Z Fun Catch - Learn with Play</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for the game */
        body {
            /* Updated background for a more vibrant look */
            background: linear-gradient(to bottom right, #4facfe, #00f2fe);
            overflow: hidden;
            text-align: center;
            font-family: "Inter", sans-serif; /* Use Inter font */
            color: #333; /* Default text color */
            margin: 0;
            padding: 20px; /* Add some padding to the body */
        }
        /* Style for the main game title */
        h1 {
            color: #000000; /* Set the color to black */
            margin-bottom: 1rem; /* Keep some margin below the title */
            font-size: 2rem; /* Basic font size */
            font-weight: bold;
        }
        #gameArea {
            position: relative;
            width: 95%; /* Make game area responsive */
            max-width: 800px; /* Limit max width */
            height: 70vh; /* Use viewport height */
            border: 2px solid #333;
            margin: 20px auto; /* Center the game area */
            overflow: hidden;
            /* Background for game area - Note: This image is an external URL and will not load offline */
            background-image: url('https://i.postimg.cc/4xbdPk6k/a-z-worksheet-32.jpg');
            background-size: cover; /* Cover the entire area */
            background-position: center; /* Center the background image */
            background-repeat: no-repeat; /* Do not repeat the image */
            /* Add a semi-transparent overlay to ensure readability of falling items */
            background-color: rgba(255, 255, 255, 0.5); /* Adjust opacity as needed */
            background-blend-mode: overlay; /* Blend the overlay with the image */

            border-radius: 10px; /* Add rounded corners */
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3); /* Add a stronger shadow */
        }
        .item {
            position: absolute;
            font-size: 1.2rem; /* Adjust font size */
            cursor: pointer;
            padding: 8px 12px; /* Add padding */
            background-color: rgba(255, 255, 255, 0.95); /* More opaque background */
            border-radius: 8px; /* Rounded corners for items */
            box-shadow: 0 3px 6px rgba(0, 0, 0, 0.2); /* Item shadow */
            transition: transform 0.1s ease; /* Add a slight transition on hover/click */
            z-index: 5; /* Ensure items are above game area background but below screens */
            display: flex; /* Use flexbox to align image and text */
            flex-direction: column; /* Stack image and text vertically */
            align-items: center; /* Center content horizontally */
            text-align: center;
        }
        .item img {
            width: 60px; /* Set image width */
            height: 60px; /* Set image height */
            object-fit: cover; /* Ensure image covers the area without distortion */
            margin-bottom: 5px; /* Space between image and text */
            border-radius: 4px; /* Rounded corners for images */
             /* Placeholder image fallback - Note: This is also an external URL */
            content: url('https://placehold.co/60x60/cccccc/000000?text=Image'); /* Fallback if actual image fails */
        }
         .item span {
            font-weight: bold;
            color: #333;
        }
        .item:hover {
            transform: scale(1.08); /* Slightly larger scale on hover */
        }
        #scoreBoard {
            font-size: 1.4rem; /* Slightly larger score font */
            margin-top: 15px;
            font-weight: bold;
            color: #004085; /* Darker blue color */
        }
        #currentLetter {
            font-size: 2.5rem; /* Larger current letter font */
            margin-bottom: 25px;
            color: #0056b3;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1); /* Add text shadow */
        }
         /* Style for the countdown text */
        #countdown {
            font-size: 3em; /* Large font size for countdown */
            font-weight: bold;
            color: #dc3545; /* Red color for urgency */
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
        }
        #startScreen, #endScreen { /* Added endScreen back */
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            /* Background image for the start screen - Note: This image is an external URL and will not load offline */
            background-image: url('https://postimg.cc/LJmw5J9H');
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            /* Added a semi-transparent white background to ensure text is readable */
            background-color: rgba(255, 255, 255, 0.8); /* Adjust opacity as needed */
            background-blend-mode: overlay; /* Blend the overlay with the image */

            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10; /* Ensure screens are on top */
        }
        #endScreen {
             display: none; /* Hide end screen initially */
        }
        .btn {
            padding: 14px 28px; /* Even larger buttons */
            margin: 12px;
            font-size: 1.2rem;
            background-color: #28a745; /* Green color */
            color: white;
            border: none;
            cursor: pointer;
            border-radius: 5px; /* Rounded buttons */
            transition: background-color 0.3s ease, transform 0.1s ease; /* Smooth transition */
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Stronger button shadow */
        }
        .btn:hover {
            background-color: #218838; /* Darker green on hover */
            transform: translateY(-3px); /* More pronounced lift effect */
        }
        .btn:active {
            transform: translateY(0); /* Press effect */
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        }
        /* Message box for feedback */
        #messageBox {
            position: fixed;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background-color: rgba(0, 0, 0, 0.8); /* Slightly darker background */
            color: white;
            padding: 10px 20px;
            border-radius: 5px;
            z-index: 20;
            display: none; /* Hidden by default */
            font-size: 1.1rem;
        }
         /* Styles for game screen elements */
        #gameScreen {
            display: none; /* Hidden by default */
        }
        #wordDisplay {
             font-size: 2rem;
             font-weight: bold;
             margin-bottom: 10px;
             color: #0056b3;
        }
        #imageDisplay img {
             width: 100px;
             height: 100px;
             object-fit: contain;
             margin-bottom: 10px;
        }
        /* Style for the final score on the end screen */
        #finalScore {
            font-size: 1.8rem; /* Larger font for final score */
            font-weight: bold;
            color: #004085; /* Match score board color */
            margin-bottom: 20px;
        }
         /* Style for the "Play Again" button on the end screen */
        #endScreen .btn {
            background-color: #007bff; /* Blue color for Play Again */
            margin-top: 20px;
        }
         #endScreen .btn:hover {
            background-color: #0056b3; /* Darker blue on hover */
        }
    </style>
</head>
<body>
    <h1 class="text-2xl font-bold my-4">A to Z Fun Catch - Learn with Play</h1>

    <div id="startScreen">
        <h2 class="text-xl font-semibold mb-4">Choose Difficulty</h2>
        <button class="btn" onclick="startGame('easy')">Easy</button>
        <button class="btn" onclick="startGame('normal')">Normal</button>
        <button class="btn" onclick="startGame('hard')">Hard</button>
    </div>

    <div id="gameScreen">
        <div id="wordDisplay"></div>
        <div id="imageDisplay"></div>
        <div id="gameArea"></div>
        <div id="scoreBoard"></div>
        </div>


    <div id="endScreen">
        <h2 class="text-2xl font-semibold mb-4">Game Over!</h2>
        <p id="finalScore" class="mb-4"></p>
        <button class="btn" onclick="location.reload()">Play Again</button>
    </div>

    <div id="messageBox"></div>
    <div id="countdown"></div>

    <audio id="audioPlayer"></audio> <script>
        // Add a global error handler for debugging
        window.onerror = function(message, source, lineno, colno, error) {
            console.error("An error occurred:", message, "Source:", source, "Line:", lineno, "Column:", colno, "Error object:", error);
            showMessage("An error occurred. Check the console for details.", 5000);
            return true; // Prevent default error handling
        };

        // Mapping of letters to items with word, image URL, and sound URL
        // Note: Image and sound URLs are external and will not load if the game is run offline without access to these URLs.
        // To make the game fully offline with images/sounds, you would need to replace these URLs with local file paths or data URIs.
        const items = {
            A: { word: 'Apple', image: 'vecteezy_a-for-apple-english-word-with-alphabet-for-kids_22600819.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3' }, // Using the uploaded Apple image filename
            B: { word: 'Ball', image: 'b-5776838_1280.png', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3' }, // Using the uploaded Ball image file name
            C: { word: 'Cat', image: 'https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3' },
            D: { word: 'Dog', image: 'https://upload.wikimedia.org/wikipedia/commons/d/d9/Collage_of_Nine_Dogs.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3' },
            E: { word: 'Elephant', image: 'https://upload.wikimedia.org/wikipedia/commons/3/37/African_Bush_Elephant.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3' },
            F: { word: 'Fish', image: 'https://upload.wikimedia.org/wikipedia/commons/1/13/Goldfish3.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3' },
            G: { word: 'Grapes', image: 'https://upload.wikimedia.org/wikipedia/commons/1/16/Table_grapes_on_white.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-7.mp3' },
            H: { word: 'Hat', image: 'https://upload.wikimedia.org/wikipedia/commons/4/4e/Hat01.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-8.mp3' },
            I: { word: 'Icecream', image: 'https://upload.wikimedia.org/wikipedia/commons/b/b1/Ice_Cream_dessert_02.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-9.mp3' },
            J: { word: 'Jug', image: 'https://upload.wikimedia.org/wikipedia/commons/4/42/Glass_Jug.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-10.mp3' },
            K: { word: 'Kite', image: 'https://upload.wikimedia.org/wikipedia/commons/5/5c/Kite_flying.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3' },
            L: { word: 'Lion', image: 'https://upload.wikimedia.org/wikipedia/commons/7/73/Lion_waiting_in_Namibia.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3' },
            M: { word: 'Mango', image: 'https://upload.wikimedia.org/wikipedia/commons/9/90/Hapus_Mango.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3' },
            N: { word: 'Nest', image: 'https://upload.wikimedia.org/wikipedia/commons/e/ef/Bird_Nest.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3' },
            O: { word: 'Orange', image: 'https://upload.wikimedia.org/wikipedia/commons/c/c4/Orange-Fruit-Pieces.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3' },
            P: { word: 'Parrot', image: 'https://upload.wikimedia.org/wikipedia/commons/d/d6/African_Grey_Parrot.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3' },
            Q: { word: 'Queen', image: 'https://upload.wikimedia.org/wikipedia/commons/b/b6/Queen_Elizabeth_II_1959.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-7.mp3' },
            R: { word: 'Rabbit', image: 'https://upload.wikimedia.org/wikipedia/commons/5/55/Oryctolagus_cuniculus_Tasmania_2.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-8.mp3' },
            S: { word: 'Sun', image: 'https://upload.wikimedia.org/wikipedia/commons/c/c3/Solar_sys8.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-9.mp3' },
            T: { word: 'Tiger', image: 'https://upload.wikimedia.org/wikipedia/commons/5/56/Tiger.50.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-10.mp3' },
            U: { word: 'Umbrella', image: 'https://upload.wikimedia.org/wikipedia/commons/e/e3/Umbrella_open_blue.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3' },
            V: { word: 'Van', image: 'https://upload.wikimedia.org/wikipedia/commons/f/f9/2006_Ford_Transit_280_SWB_2.2_TDCi_85.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3' },
            W: { word: 'Watch', image: 'https://upload.wikimedia.org/wikipedia/commons/7/71/Wrist_watch.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3' },
            X: { word: 'Xylophone', image: 'https://upload.wikimedia.org/wikipedia/commons/4/48/Xylophone_picture.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-4.mp3' },
            Y: { word: 'Yak', image: 'https://youtu.be/hq3yfQnllfQ?si=dJyOsnz7IBcccCt3', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-5.mp3' }, // This URL might not display an image correctly.
            Z: { word: 'Zebra', image: 'https://upload.wikimedia.org/wikipedia/commons/0/0e/Plains_Zebra_Equus_quagga.jpg', sound: 'https://www.soundhelix.com/examples/mp3/SoundHelix-Song-6.mp3' }
        };


        // Game state variables
        let score = 0;
        let incorrectAttempts = 0; // Variable to track incorrect attempts
        const maxIncorrectAttempts = 3; // Maximum allowed incorrect attempts
        let mode = 'easy';
        let currentLetter = '';
        let letterIndex = 0;
        let roundTimeout; // Timeout for each letter round
        let itemFallSpeed = 2; // Default fall speed
        let roundDuration = 5000; // Default duration for each letter round (in ms)
        let itemFallIntervals = []; // To keep track of falling item intervals


        // Array of letters from the items object
        const letters = Object.keys(items);

        // Speech synthesis API (still available but audio player is preferred for sounds)
        const voices = window.speechSynthesis;

        // Get game elements
        const startScreen = document.getElementById('startScreen');
        const gameScreen = document.getElementById('gameScreen'); // Get the game screen div
        const gameArea = document.getElementById('gameArea');
        const scoreBoard = document.getElementById('scoreBoard');
        const endScreen = document.getElementById('endScreen'); // Get the end screen element
        const finalScoreDisplay = document.getElementById('finalScore');
        const currentLetterDisplay = document.getElementById('currentLetter'); // Still keeping this for countdown/initial display
        const messageBox = document.getElementById('messageBox');
        const countdownDisplay = document.getElementById('countdown'); // Get the countdown element
        const wordDisplay = document.getElementById('wordDisplay'); // Get the word display element
        const imageDisplay = document.getElementById('imageDisplay'); // Get the image display element
        const audioPlayer = document.getElementById('audioPlayer'); // Get the audio player element


        console.log("Script loaded. Showing start screen.");
        // Show the start screen initially
        startScreen.style.display = 'flex';

        // Function to speak a word (using SpeechSynthesis - can be used for countdown numbers)
        function speak(word) {
            // Cancel any ongoing speech before speaking a new word
            if (voices.speaking) {
                voices.cancel();
            }
            const utter = new SpeechSynthesisUtterance(word);
            voices.speak(utter);
        }

         // Function to play audio from a URL
        function playAudio(url) {
            // Stop any currently playing audio
            if (!audioPlayer.paused) {
                audioPlayer.pause();
                audioPlayer.currentTime = 0;
            }
            audioPlayer.src = url;
            audioPlayer.play().catch(error => {
                console.error("Audio playback failed:", error);
                // Handle potential autoplay policy issues
                showMessage("Audio playback blocked. Please interact with the page first.", 3000);
            });
        }


        // Function to show a temporary message box
        function showMessage(text, duration = 1500) {
            messageBox.textContent = text;
            messageBox.style.display = 'block';
            setTimeout(() => {
                messageBox.style.display = 'none';
            }, duration);
        }

        // Function to start the game
        function startGame(selectedMode) {
            console.log("startGame called with mode:", selectedMode);
            mode = selectedMode;
            score = 0; // Reset score
            incorrectAttempts = 0; // Reset incorrect attempts
            letterIndex = 0; // Reset letter index
            itemFallIntervals = []; // Clear previous intervals

            startScreen.style.display = 'none';
            // Initially hide game screen elements until countdown is done
            gameScreen.style.display = 'none';
            scoreBoard.textContent = `Score: ${score}`; // Initialize score display

            // Set difficulty parameters
            if (mode === 'easy') {
                itemFallSpeed = 2;
                roundDuration = 6000; // Longer duration for easy
            } else if (mode === 'normal') {
                itemFallSpeed = 3;
                roundDuration = 5000; // Normal duration
            } else if (mode === 'hard') {
                itemFallSpeed = 4;
                roundDuration = 4000; // Shorter duration for hard
            }
            console.log(`Difficulty set: Fall Speed = ${itemFallSpeed}, Round Duration = ${roundDuration}`);

            // Start the countdown before the first round
            startCountdown(3); // Start countdown from 3
        }

        // Function to start the countdown
        function startCountdown(count) {
            countdownDisplay.style.display = 'block'; // Show countdown display
            // Hide game elements during countdown
            gameScreen.style.display = 'none';
            scoreBoard.style.display = 'none';


            let currentCount = count;
            countdownDisplay.textContent = currentCount;
            speak(currentCount);

            const countdownInterval = setInterval(() => {
                currentCount--;
                if (currentCount > 0) {
                    countdownDisplay.textContent = currentCount;
                    speak(currentCount);
                } else {
                    clearInterval(countdownInterval);
                    countdownDisplay.textContent = 'Go!';
                    speak('Go!');
                    setTimeout(() => {
                        countdownDisplay.style.display = 'none'; // Hide countdown
                        gameScreen.style.display = 'block'; // Show game screen
                        scoreBoard.style.display = 'block'; // Show score board
                        runGame(); // Start the first round after countdown
                    }, 500); // Short delay after "Go!"
                }
            }, 1000); // Update every 1 second
        }


        // Function to run a single round (for one letter)
        function runGame() {
            console.log("runGame called. Current letterIndex:", letterIndex);

            // Check if maximum incorrect attempts reached
            if (incorrectAttempts >= maxIncorrectAttempts) {
                 console.log("Max incorrect attempts reached. Ending game.");
                 endGame();
                 return;
            }

            // Check if we have gone through all letters
            if (letterIndex >= letters.length) {
                console.log("Completed all letters. Ending game.");
                endGame();
                return;
            }


            // Clear previous items and any ongoing fall animations
            clearItems();
            console.log("Items cleared for new round.");

            // Set the current letter, word, image, and sound
            currentLetter = letters[letterIndex];
            const correctItemData = items[currentLetter];
            wordDisplay.textContent = `${currentLetter} for ${correctItemData.word}`; // Display letter and word
            imageDisplay.innerHTML = `<img src="${correctItemData.image}" alt="${correctItemData.word}" class="rounded-md shadow-md" onerror="this.onerror=null; this.src='https://placehold.co/100x100/CCCCCC/000000?text=Image';" />`; // Display image with error handling
            playAudio(correctItemData.sound); // Play the sound for the correct item


            const optionsData = [correctItemData];

            // Add incorrect options
            while (optionsData.length < 3) {
                const randomLetter = letters[Math.floor(Math.random() * letters.length)];
                const randomItemData = items[randomLetter];
                // Ensure the random item is not already in the options and is not the correct item
                if (!optionsData.some(option => option.word === randomItemData.word)) {
                    optionsData.push(randomItemData);
                }
            }

            // Shuffle the options
            shuffle(optionsData);
            console.log("Options data for round:", optionsData);

            // Create and add item elements to the game area
            optionsData.forEach((itemData) => {
                const el = document.createElement('div');
                el.className = 'item'; // Basic item class
                // Store the item data on the element for easy access in the click handler
                el.dataset.word = itemData.word;
                el.dataset.image = itemData.image;
                el.dataset.sound = itemData.sound; // Store sound URL
                el.dataset.isCorrect = (itemData.word === correctItemData.word); // Store if this is the correct item

                const imgEl = document.createElement('img');
                imgEl.src = itemData.image;
                 // Add onerror to use a fallback placeholder if the image fails to load
                imgEl.onerror = function() {
                    this.src = 'https://placehold.co/60x60/cccccc/000000?text=Image';
                };
                imgEl.alt = itemData.word; // Add alt text for accessibility

                const textEl = document.createElement('span');
                textEl.textContent = itemData.word;

                el.appendChild(imgEl);
                el.appendChild(textEl);

                // Position items randomly at the top
                // Ensure items are within bounds based on gameArea width
                const gameAreaWidth = gameArea.offsetWidth;
                const itemWidth = 120; // Approximate item width, might need adjustment based on styling
                const maxLeft = gameAreaWidth - itemWidth;
                 // Ensure maxLeft is not negative
                el.style.left = `${Math.random() * Math.max(0, maxLeft)}px`;
                el.style.top = '0px';
                gameArea.appendChild(el);
                console.log("Created item element for:", itemData.word);

                // Animate the item falling
                animateFall(el);

                // Add click event listener
                el.onclick = () => {
                    console.log("Item clicked:", el.dataset.word);
                    // Stop the round timeout
                    clearTimeout(roundTimeout);
                    // Stop all falling animations for this round
                    stopAllFallAnimations();
                    // Process the click
                    handleItemClick(el.dataset.isCorrect === 'true', el.dataset.word, el.dataset.sound);
                };
            });

            // Set a timeout for the current round
            roundTimeout = setTimeout(() => {
                console.log("Round timeout reached for letter:", currentLetter);
                // If the timeout occurs, it means the correct item wasn't caught in time
                showMessage(`Time out! The item for ${currentLetter} was not caught.`);
                // Increment incorrect attempts on timeout
                incorrectAttempts++;
                console.log("Incorrect attempts:", incorrectAttempts);

                // Stop all falling animations
                stopAllFallAnimations();

                // Check if max incorrect attempts reached
                if (incorrectAttempts >= maxIncorrectAttempts) {
                    console.log("Max incorrect attempts reached after timeout. Ending game.");
                    clearTimeout(roundTimeout); // Clear the round timeout
                    stopAllFallAnimations(); // Stop other falling items
                    endGame();
                } else {
                     // Move to the next letter and start the next round after a short delay
                     clearTimeout(roundTimeout); // Clear the round timeout
                     stopAllFallAnimations(); // Stop other falling items
                     letterIndex++;
                     setTimeout(runGame, 1000);
                }
            }, roundDuration);
            console.log("Round timeout set for", roundDuration, "ms.");
        }

        // Function to handle item clicks
        function handleItemClick(isCorrect, clickedWord, clickedSound) {
            console.log("handleItemClick called. Is Correct:", isCorrect, "Clicked Item Word:", clickedWord);
            clearItems(); // Clear all items from the screen immediately

            if (isCorrect) {
                score++;
                scoreBoard.textContent = `Score: ${score}`;
                playAudio(clickedSound); // Play the sound for the clicked item
                showMessage('Correct!');
                console.log("Correct catch. Score:", score);
            } else {
                // Increment incorrect attempts on incorrect click
                incorrectAttempts++;
                console.log("Incorrect catch. Incorrect attempts:", incorrectAttempts);
                // Optional: Play an incorrect sound
                // playAudio('path/to/incorrect_sound.mp3');
                showMessage(`Try again! Attempts left: ${maxIncorrectAttempts - incorrectAttempts}`);
                // Optional: Penalize score here if desired
                // score = Math.max(0, score - 1);
                // scoreBoard.textContent = `Score: ${score}`;
            }

            // Check if max incorrect attempts reached
            if (incorrectAttempts >= maxIncorrectAttempts) {
                console.log("Max incorrect attempts reached after click. Ending game.");
                endGame();
            } else {
                 // Move to the next letter and start the next round after a short delay
                letterIndex++;
                console.log("Moving to next letter. New letterIndex:", letterIndex);
                setTimeout(runGame, 1000);
            }
        }

        // Function to clear all items from the game area and stop their fall animations
        function clearItems() {
             console.log("Clearing items.");
             stopAllFallAnimations(); // Stop all fall animations first
             gameArea.innerHTML = ''; // Then clear the HTML
             console.log("Items cleared.");
        }

        // Function to stop all active falling animations
        function stopAllFallAnimations() {
             console.log("Stopping all fall animations.");
             itemFallIntervals.forEach(clearInterval);
             itemFallIntervals = []; // Clear the array
             console.log("All fall animations stopped.");
        }

        // Function to end the game
        function endGame() {
            console.log("Ending game.");
            clearTimeout(roundTimeout); // Clear the final round timeout
            stopAllFallAnimations(); // Ensure all fall animations are stopped
            clearItems(); // Clear any remaining items
            gameScreen.style.display = 'none'; // Hide game screen
            scoreBoard.style.display = 'none'; // Hide score board
            currentLetterDisplay.classList.add('hidden'); // Hide current letter display (if still visible)
            endScreen.style.display = 'flex'; // Show end screen
            // Updated text to clearly show the final score
            finalScoreDisplay.textContent = `Your Final Score: ${score}`;
            console.log("Game over screen shown. Final score:", score);
        }


        // Function to shuffle an array (Fisher-Yates Algorithm)
        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]]; // Swap elements
            }
            return array; // Return the shuffled array
        }

        // Function to animate the falling of an element
        function animateFall(el) {
            let top = 0;
            const gameAreaHeight = gameArea.offsetHeight; // Get the height of the game area

            // Store the interval ID on the element itself
            const fallInterval = setInterval(() => {
                top += itemFallSpeed; // Use the dynamic fall speed
                el.style.top = `${top}px`;

                // Check if the item has reached the bottom
                if (top + el.offsetHeight > gameAreaHeight) {
                    console.log("Item reached bottom:", el.dataset.word);
                    clearInterval(fallInterval); // Stop falling animation
                    // If the correct item is missed (reaches the bottom)
                    if (el.dataset.isCorrect === 'true') {
                         console.log("Correct item missed.");
                         // Increment incorrect attempts on missing the correct item
                        incorrectAttempts++;
                        console.log("Incorrect attempts:", incorrectAttempts);
                        showMessage(`Missed ${el.dataset.word}! Attempts left: ${maxIncorrectAttempts - incorrectAttempts}`);

                        // Check if max incorrect attempts reached
                        if (incorrectAttempts >= maxIncorrectAttempts) {
                            console.log("Max incorrect attempts reached after missing item. Ending game.");
                            clearTimeout(roundTimeout); // Clear the round timeout
                            stopAllFallAnimations(); // Stop other falling items
                            endGame();
                        } else {
                             // Move to the next letter and start the next round after a short delay
                             clearTimeout(roundTimeout); // Clear the round timeout
                             stopAllFallAnimations(); // Stop other falling items
                             letterIndex++;
                             setTimeout(runGame, 1000);
                        }
                    }
                     // Remove the item from the DOM once it's off-screen
                    el.remove();
                }
            }, 30); // Animation frame interval
             itemFallIntervals.push(fallInterval); // Add interval ID to the array
        }

        // Ensure game area dimensions are updated on window resize
        window.addEventListener('resize', () => {
             console.log("Window resized.");
             // No need to re-run the game, the animateFall function gets the height dynamically
             // and the game area itself is responsive via CSS.
        });

    </script>
</body>
</html>
