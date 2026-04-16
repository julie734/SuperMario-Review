# SuperMario-Review
Questions review games
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>LIN-NEI JHS Vocabulary Race</title>
    <style>
        :root {
            --mario-red: #e74c3c;
            --luigi-green: #2ecc71;
            --track-gray: #34495e;
            --gold: #f1c40f;
        }

        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            font-family: 'Arial Black', sans-serif;
        }

        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background: #222;
        }

        /* Screens */
        #start-screen, #win-screen {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9);
            z-index: 100;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            color: white;
            text-align: center;
        }

        .btn-main {
            padding: 30px 60px;
            font-size: 3.5rem;
            background: var(--gold);
            border: none;
            border-radius: 20px;
            cursor: pointer;
            box-shadow: 0 10px #c29d0b;
        }

        /* Layout */
        .game-wrapper {
            display: flex;
            width: 100%;
            height: 100%;
        }

        .player-side {
            flex: 1;
            height: 100%;
            display: flex;
            flex-direction: column;
            border: 4px solid #000;
            position: relative;
            background: #fff;
        }

        /* Race Track Area */
        .track-container {
            height: 25%;
            background: linear-gradient(to bottom, #87CEEB 70%, #5d4037 70%); /* Sky and Dirt */
            position: relative;
            border-bottom: 10px solid #333;
            overflow: hidden;
        }

        .finish-line {
            position: absolute;
            right: 0;
            top: 0;
            bottom: 0;
            width: 60px;
            background: repeating-conic-gradient(#fff 0 25%, #000 0 50%) 0 0/30px 30px;
        }

        .character {
            position: absolute;
            bottom: 10px;
            left: 0%;
            width: 100px;
            height: 100px;
            font-size: 80px;
            display: flex;
            justify-content: center;
            align-items: center;
            transition: left 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            z-index: 5;
        }

        /* Quiz Area */
        .quiz-area {
            flex: 1;
            padding: 20px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }

        .question-box {
            background: #f9f9f9;
            padding: 20px;
            border-radius: 15px;
            border: 5px solid #ddd;
            font-size: 2.2rem;
            text-align: center;
            min-height: 150px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 10px;
        }

        .option-btn {
            padding: 40px 10px;
            font-size: 2rem;
            border: 5px solid #333;
            border-radius: 15px;
            background: #eee;
            cursor: pointer;
            transition: transform 0.1s;
        }

        .p1-btn:active { background: var(--mario-red); color: white; }
        .p2-btn:active { background: var(--luigi-green); color: white; }

        /* Freeze System */
        .freeze-overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.85);
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 50;
            color: #ff4757;
        }

        .bowser { font-size: 150px; margin-bottom: 20px; }
        .freeze-text { font-size: 5rem; font-weight: 900; }

        .hidden { display: none !important; }
    </style>
</head>
<body>

    <div id="start-screen">
        <h1 style="font-size: 4rem; margin-bottom: 10px;">LIN-NEI JHS</h1>
        <h2 style="font-size: 2.5rem; color: var(--gold); margin-bottom: 40px;">Vocabulary Race: Mario vs Luigi</h2>
        <button class="btn-main" onclick="startGame()">START GAME</button>
    </div>

    <div id="win-screen" class="hidden">
        <h1 id="winner-text" style="font-size: 6rem;"></h1>
        <button class="btn-main" onclick="location.reload()">REPLAY</button>
    </div>

    <div class="game-wrapper">
        <div class="player-side" id="side-p1">
            <div class="track-container">
                <div class="finish-line"></div>
                <div id="char-p1" class="character">🏃‍♂️</div> <div style="position:absolute; bottom:0; left:10px; color:white; font-size:1.5rem;">MARIO</div>
            </div>
            <div class="quiz-area">
                <div id="q-p1" class="question-box"></div>
                <div id="opts-p1" class="options-grid"></div>
            </div>
            <div id="freeze-p1" class="freeze-overlay">
                <div class="bowser">🦖</div>
                <div class="freeze-text">FREEZE!</div>
            </div>
        </div>

        <div class="player-side" id="side-p2">
            <div class="track-container">
                <div class="finish-line"></div>
                <div id="char-p2" class="character">🏃</div> <div style="position:absolute; bottom:0; left:10px; color:white; font-size:1.5rem;">LUIGI</div>
            </div>
            <div class="quiz-area">
                <div id="q-p2" class="question-box"></div>
                <div id="opts-p2" class="options-grid"></div>
            </div>
            <div id="freeze-p2" class="freeze-overlay">
                <div class="bowser">🦖</div>
                <div class="freeze-text">FREEZE!</div>
            </div>
        </div>
    </div>

    <script>
        const questions = [
            { q: "Please recycle the ________ after drinking water.", options: ["brush", "newspaper", "bottle cap", "button"], a: "bottle cap" },
            { q: "Old ________ can be recycled into new paper.", options: ["mask", "brush", "newspaper", "pot"], a: "newspaper" },
            { q: "We can reuse a ________ to plant small flowers.", options: ["bottle cap", "mask", "pot", "button"], a: "pot" },
            { q: "People sit on the ________ in the park and enjoy nature.", options: ["newspaper", "bench", "basket", "brush"], a: "bench" },
            { q: "Please wear a ________ to protect yourself and others.", options: ["bench", "button", "mask", "pot"], a: "mask" },
            { q: "Use a ________ when you go shopping to reduce plastic bags.", options: ["newspaper", "basket", "mask", "bottle cap"], a: "basket" },
            { q: "She uses a ________ to clean reusable items.", options: ["brush", "bench", "newspaper", "button"], a: "brush" },
            { q: "The old shirt lost a ________, but we can fix it and reuse it.", options: ["basket", "button", "pot", "mask"], a: "button" },
            { q: "The kids were amazed by the ______ robot at the museum.", options: ["noisy", "lucky", "giant", "oil"], a: "giant" },
            { q: "My stomach hurts a lot. It’s ______ me.", options: ["tricking", "killing", "hiding", "oiling"], a: "killing" },
            { q: "Don’t ______ people. It’s not nice.", options: ["hide", "chance", "trick", "kill"], a: "trick" },
            { q: "This is a great ______ to learn something new.", options: ["trick", "giant", "classmate", "chance"], a: "chance" },
            { q: "Could you please close the window? It’s really ______ outside.", options: ["lucky", "noisy", "giant", "somewhere"], a: "noisy" },
            { q: "Richard and I are ______. We go to school together every morning.", options: ["tricks", "giants", "classmates", "chances"], a: "classmates" },
            { q: "Judy was so ______. She fell but didn’t get hurt.", options: ["noisy", "somewhere", "trick", "lucky"], a: "lucky" }
        ];

        let gameState = {
            p1: { idx: 0, frozen: false },
            p2: { idx: 0, frozen: false }
        };

        function startGame() {
            document.getElementById('start-screen').classList.add('hidden');
            render('p1');
            render('p2');
        }

        function render(player) {
            const data = gameState[player];
            if (data.idx >= questions.length) {
                victory(player);
                return;
            }

            const qObj = questions[data.idx];
            document.getElementById(`q-${player}`).innerText = qObj.q;
            
            const container = document.getElementById(`opts-${player}`);
            container.innerHTML = '';

            qObj.options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = `option-btn ${player === 'p1' ? 'p1-btn' : 'p2-btn'}`;
                btn.innerText = opt;
                
                // Use pointerdown for immediate touch response on TVs/iPads
                btn.onpointerdown = (e) => {
                    e.preventDefault();
                    handleAnswer(player, opt);
                };
                container.appendChild(btn);
            });

            // Update Character Movement
            const progress = (data.idx / questions.length) * 85; 
            document.getElementById(`char-${player}`).style.left = progress + "%";
        }

        function handleAnswer(player, choice) {
            if (gameState[player].frozen) return;

            if (choice === questions[gameState[player].idx].a) {
                gameState[player].idx++;
                render(player);
            } else {
                triggerFreeze(player);
            }
        }

        function triggerFreeze(player) {
            gameState[player].frozen = true;
            const overlay = document.getElementById(`freeze-${player}`);
            overlay.style.display = 'flex';
            
            setTimeout(() => {
                overlay.style.display = 'none';
                gameState[player].frozen = false;
            }, 3000);
        }

        function victory(player) {
            const screen = document.getElementById('win-screen');
            const text = document.getElementById('winner-text');
            const name = player === 'p1' ? 'MARIO' : 'LUIGI';
            const color = player === 'p1' ? 'var(--mario-red)' : 'var(--luigi-green)';
            
            screen.classList.remove('hidden');
            text.innerText = `${name} WINS!`;
            text.style.color = color;
        }
    </script>
</body>
</html>
