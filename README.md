<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MindVault - Brain Games</title>
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#0f0c29">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <link href="https://cdn.jsdelivr.net/fontsource/css/inter@latest/index.css" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
            min-height: 100vh;
            color: #fff;
            overflow-x: hidden;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        /* Header */
        .header {
            text-align: center;
            padding: 40px 20px;
            position: relative;
        }

        .header h1 {
            font-size: 3rem;
            background: linear-gradient(90deg, #f093fb, #f5576c, #4facfe);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 10px;
            animation: glow 3s ease-in-out infinite;
        }

        .header p {
            color: #a0a0c0;
            font-size: 1.1rem;
        }

        @keyframes glow {
            0%, 100% { filter: brightness(1); }
            50% { filter: brightness(1.3); }
        }

        /* Navigation */
        .nav-bar {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-bottom: 30px;
            flex-wrap: wrap;
        }

        .nav-btn {
            padding: 12px 24px;
            border: 2px solid rgba(255,255,255,0.2);
            background: rgba(255,255,255,0.05);
            color: #fff;
            border-radius: 12px;
            cursor: pointer;
            font-size: 1rem;
            font-family: 'Inter', sans-serif;
            transition: all 0.3s ease;
            backdrop-filter: blur(10px);
        }

        .nav-btn:hover, .nav-btn.active {
            background: linear-gradient(135deg, #667eea, #764ba2);
            border-color: transparent;
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(102, 126, 234, 0.4);
        }

        /* Game Sections */
        .game-section {
            display: none;
            animation: fadeIn 0.5s ease;
        }

        .game-section.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Home Section */
        .games-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 25px;
            padding: 20px 0;
        }

        .game-card {
            background: rgba(255,255,255,0.05);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 20px;
            padding: 30px;
            text-align: center;
            cursor: pointer;
            transition: all 0.4s ease;
            backdrop-filter: blur(10px);
            position: relative;
            overflow: hidden;
        }

        .game-card::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle, rgba(255,255,255,0.1) 0%, transparent 70%);
            opacity: 0;
            transition: opacity 0.4s;
        }

        .game-card:hover::before {
            opacity: 1;
        }

        .game-card:hover {
            transform: translateY(-8px) scale(1.02);
            border-color: rgba(255,255,255,0.3);
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
        }

        .game-card .icon {
            font-size: 4rem;
            margin-bottom: 15px;
            display: block;
        }

        .game-card h3 {
            font-size: 1.4rem;
            margin-bottom: 10px;
            color: #fff;
        }

        .game-card p {
            color: #a0a0c0;
            font-size: 0.9rem;
            line-height: 1.5;
        }

        /* Sudoku */
        .sudoku-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .sudoku-board {
            display: grid;
            grid-template-columns: repeat(9, 1fr);
            gap: 1px;
            background: rgba(255,255,255,0.3);
            border: 3px solid rgba(255,255,255,0.5);
            border-radius: 10px;
            overflow: hidden;
            max-width: 450px;
            width: 100%;
            aspect-ratio: 1;
        }

        .sudoku-cell {
            aspect-ratio: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(15, 12, 41, 0.9);
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
        }

        .sudoku-cell:hover {
            background: rgba(102, 126, 234, 0.3);
        }

        .sudoku-cell.selected {
            background: rgba(102, 126, 234, 0.5);
            box-shadow: inset 0 0 10px rgba(102, 126, 234, 0.5);
        }

        .sudoku-cell.given {
            color: #4facfe;
            cursor: default;
        }

        .sudoku-cell.user-input {
            color: #f5576c;
        }

        .sudoku-cell.error {
            color: #ff4444;
            animation: shake 0.3s ease;
        }

        .sudoku-cell.highlight {
            background: rgba(102, 126, 234, 0.15);
        }

        /* 3x3 box borders */
        .sudoku-cell:nth-child(3n) {
            border-right: 2px solid rgba(255,255,255,0.4);
        }
        .sudoku-cell:nth-child(9n) {
            border-right: none;
        }
        .sudoku-cell:nth-child(n+19):nth-child(-n+27),
        .sudoku-cell:nth-child(n+46):nth-child(-n+54) {
            border-bottom: 2px solid rgba(255,255,255,0.4);
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-3px); }
            75% { transform: translateX(3px); }
        }

        .number-pad {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 8px;
            max-width: 350px;
            width: 100%;
        }

        .num-btn {
            padding: 15px;
            border: 2px solid rgba(255,255,255,0.2);
            background: rgba(255,255,255,0.05);
            color: #fff;
            border-radius: 10px;
            cursor: pointer;
            font-size: 1.2rem;
            font-weight: 600;
            font-family: 'Inter', sans-serif;
            transition: all 0.2s;
        }

        .num-btn:hover {
            background: linear-gradient(135deg, #667eea, #764ba2);
            border-color: transparent;
            transform: scale(1.05);
        }

        .game-controls {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .ctrl-btn {
            padding: 10px 20px;
            border: 2px solid rgba(255,255,255,0.2);
            background: rgba(255,255,255,0.05);
            color: #fff;
            border-radius: 10px;
            cursor: pointer;
            font-size: 0.9rem;
            font-family: 'Inter', sans-serif;
            transition: all 0.3s;
        }

        .ctrl-btn:hover {
            background: rgba(255,255,255,0.15);
            transform: translateY(-2px);
        }

        .ctrl-btn.primary {
            background: linear-gradient(135deg, #667eea, #764ba2);
            border-color: transparent;
        }

        /* Memory Game */
        .memory-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .memory-stats {
            display: flex;
            gap: 30px;
            font-size: 1.1rem;
        }

        .memory-stats span {
            color: #a0a0c0;
        }

        .memory-stats strong {
            color: #4facfe;
        }

        .memory-board {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
            max-width: 400px;
            width: 100%;
        }

        .memory-card {
            aspect-ratio: 1;
            perspective: 600px;
            cursor: pointer;
        }

        .memory-card-inner {
            width: 100%;
            height: 100%;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.5s;
            border-radius: 12px;
        }

        .memory-card.flipped .memory-card-inner {
            transform: rotateY(180deg);
        }

        .memory-card.matched .memory-card-inner {
            transform: rotateY(180deg);
            box-shadow: 0 0 20px rgba(76, 175, 80, 0.5);
        }

        .memory-card-front, .memory-card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
        }

        .memory-card-front {
            background: linear-gradient(135deg, #667eea, #764ba2);
            border: 2px solid rgba(255,255,255,0.2);
        }

        .memory-card-front::after {
            content: '?';
            font-size: 2rem;
            color: rgba(255,255,255,0.5);
        }

        .memory-card-back {
            background: rgba(255,255,255,0.1);
            border: 2px solid rgba(255,255,255,0.3);
            transform: rotateY(180deg);
        }

        .memory-card.matched .memory-card-back {
            background: rgba(76, 175, 80, 0.2);
            border-color: rgba(76, 175, 80, 0.5);
        }

        /* Keram - Pattern Sequence Game */
        .keram-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 25px;
        }

        .keram-status {
            font-size: 1.2rem;
            color: #a0a0c0;
            text-align: center;
            min-height: 30px;
        }

        .keram-board {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            max-width: 350px;
            width: 100%;
        }

        .keram-btn {
            aspect-ratio: 1;
            border-radius: 20px;
            border: 3px solid rgba(255,255,255,0.2);
            cursor: pointer;
            transition: all 0.2s;
            opacity: 0.6;
            position: relative;
            overflow: hidden;
        }

        .keram-btn.active {
            opacity: 1;
            transform: scale(1.05);
            box-shadow: 0 0 30px currentColor;
        }

        .keram-btn.green {
            background: linear-gradient(135deg, #00b09b, #96c93d);
            color: #00b09b;
        }
        .keram-btn.red {
            background: linear-gradient(135deg, #f5576c, #ff6b6b);
            color: #f5576c;
        }
        .keram-btn.yellow {
            background: linear-gradient(135deg, #f7971e, #ffd200);
            color: #ffd200;
        }
        .keram-btn.blue {
            background: linear-gradient(135deg, #4facfe, #00f2fe);
            color: #4facfe;
        }

        .keram-btn:hover {
            opacity: 0.8;
        }

        .keram-score {
            font-size: 1.5rem;
            color: #4facfe;
        }

        /* 2048 Game */
        .game-2048-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .game-2048-header {
            display: flex;
            gap: 20px;
            align-items: center;
        }

        .score-box {
            background: rgba(255,255,255,0.1);
            padding: 10px 20px;
            border-radius: 10px;
            text-align: center;
        }

        .score-box label {
            font-size: 0.8rem;
            color: #a0a0c0;
            display: block;
        }

        .score-box .value {
            font-size: 1.5rem;
            font-weight: 700;
            color: #4facfe;
        }

        .board-2048 {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
            background: rgba(255,255,255,0.1);
            padding: 10px;
            border-radius: 15px;
            max-width: 400px;
            width: 100%;
            aspect-ratio: 1;
        }

        .tile-2048 {
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            font-weight: 700;
            transition: all 0.15s ease;
            background: rgba(255,255,255,0.05);
            color: #fff;
        }

        .tile-2048[data-value="2"] { background: rgba(238, 228, 218, 0.3); color: #776e65; }
        .tile-2048[data-value="4"] { background: rgba(237, 224, 200, 0.4); color: #776e65; }
        .tile-2048[data-value="8"] { background: rgba(242, 177, 121, 0.7); }
        .tile-2048[data-value="16"] { background: rgba(245, 149, 99, 0.8); }
        .tile-2048[data-value="32"] { background: rgba(246, 124, 95, 0.85); }
        .tile-2048[data-value="64"] { background: rgba(246, 94, 59, 0.9); }
        .tile-2048[data-value="128"] { background: rgba(237, 207, 114, 0.85); font-size: 1.3rem; }
        .tile-2048[data-value="256"] { background: rgba(237, 204, 97, 0.9); font-size: 1.3rem; }
        .tile-2048[data-value="512"] { background: rgba(237, 200, 80, 0.95); font-size: 1.3rem; }
        .tile-2048[data-value="1024"] { background: rgba(237, 197, 63, 1); font-size: 1.1rem; }
        .tile-2048[data-value="2048"] { background: rgba(237, 194, 46, 1); font-size: 1.1rem; color: #f9f6f2; }

        .tile-2048.new-tile {
            animation: appear 0.2s ease;
        }

        @keyframes appear {
            from { transform: scale(0); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }

        /* Modal */
        .modal-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.7);
            z-index: 1000;
            align-items: center;
            justify-content: center;
            backdrop-filter: blur(5px);
        }

        .modal-overlay.active {
            display: flex;
        }

        .modal {
            background: linear-gradient(135deg, #1a1a3e, #2d2d5e);
            border: 1px solid rgba(255,255,255,0.2);
            border-radius: 20px;
            padding: 40px;
            text-align: center;
            max-width: 400px;
            width: 90%;
            animation: modalIn 0.3s ease;
        }

        @keyframes modalIn {
            from { transform: scale(0.8); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }

        .modal h2 {
            font-size: 2rem;
            margin-bottom: 15px;
            background: linear-gradient(90deg, #f093fb, #f5576c);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .modal p {
            color: #a0a0c0;
            margin-bottom: 20px;
            font-size: 1.1rem;
        }

        .modal .ctrl-btn {
            margin-top: 10px;
        }

        /* Difficulty selector */
        .difficulty-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .diff-btn {
            padding: 8px 16px;
            border: 1px solid rgba(255,255,255,0.2);
            background: rgba(255,255,255,0.05);
            color: #fff;
            border-radius: 8px;
            cursor: pointer;
            font-family: 'Inter', sans-serif;
            transition: all 0.3s;
        }

        .diff-btn.active {
            background: linear-gradient(135deg, #667eea, #764ba2);
            border-color: transparent;
        }

        /* Responsive */
        @media (max-width: 600px) {
            .header h1 { font-size: 2rem; }
            .sudoku-cell { font-size: 0.9rem; }
            .memory-board { grid-template-columns: repeat(4, 1fr); gap: 8px; }
            .board-2048 { gap: 6px; padding: 8px; }
            .tile-2048 { font-size: 1.1rem; }
            .nav-btn { padding: 10px 16px; font-size: 0.85rem; }
        }

        /* Timer */
        .timer {
            font-size: 1.1rem;
            color: #a0a0c0;
        }

        .timer strong {
            color: #f5576c;
        }

        /* Instructions */
        .instructions {
            background: rgba(255,255,255,0.05);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 12px;
            padding: 15px 20px;
            margin-bottom: 20px;
            color: #a0a0c0;
            font-size: 0.9rem;
            line-height: 1.6;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🧠 MindVault</h1>
            <p>Challenge your mind with these classic puzzle games</p>
        </div>

        <div class="nav-bar">
            <button class="nav-btn active" onclick="showSection('home')">🏠 Home</button>
            <button class="nav-btn" onclick="showSection('sudoku')">🔢 Sudoku</button>
            <button class="nav-btn" onclick="showSection('memory')">🃏 Memory</button>
            <button class="nav-btn" onclick="showSection('keram')">🎨 Keram</button>
            <button class="nav-btn" onclick="showSection('game2048')">🎮 2048</button>
        </div>

        <!-- HOME SECTION -->
        <div id="home" class="game-section active">
            <div class="games-grid">
                <div class="game-card" onclick="showSection('sudoku')">
                    <span class="icon">🔢</span>
                    <h3>Sudoku</h3>
                    <p>Fill the 9×9 grid so that each row, column, and 3×3 box contains all digits from 1 to 9.</p>
                </div>
                <div class="game-card" onclick="showSection('memory')">
                    <span class="icon">🃏</span>
                    <h3>Memory Cards</h3>
                    <p>Flip cards and find matching pairs. Test your memory with this classic card matching game.</p>
                </div>
                <div class="game-card" onclick="showSection('keram')">
                    <span class="icon">🎨</span>
                    <h3>Keram</h3>
                    <p>Watch the pattern sequence and repeat it. How far can you go? A test of pattern memory.</p>
                </div>
                <div class="game-card" onclick="showSection('game2048')">
                    <span class="icon">🎮</span>
                    <h3>2048</h3>
                    <p>Slide tiles and merge numbers to reach 2048. Addictive and strategic puzzle game.</p>
                </div>
            </div>
        </div>

        <!-- SUDOKU SECTION -->
        <div id="sudoku" class="game-section">
            <div class="sudoku-container">
                <div class="instructions">
                    Select a cell and use the number pad to fill in values. Each row, column, and 3×3 box must contain 1-9.
                </div>
                <div class="difficulty-selector">
                    <button class="diff-btn active" onclick="newSudoku('easy')">Easy</button>
                    <button class="diff-btn" onclick="newSudoku('medium')">Medium</button>
                    <button class="diff-btn" onclick="newSudoku('hard')">Hard</button>
                </div>
                <div class="timer">Time: <strong id="sudoku-timer">00:00</strong></div>
                <div class="sudoku-board" id="sudoku-board"></div>
                <div class="number-pad">
                    <button class="num-btn" onclick="inputNumber(1)">1</button>
                    <button class="num-btn" onclick="inputNumber(2)">2</button>
                    <button class="num-btn" onclick="inputNumber(3)">3</button>
                    <button class="num-btn" onclick="inputNumber(4)">4</button>
                    <button class="num-btn" onclick="inputNumber(5)">5</button>
                    <button class="num-btn" onclick="inputNumber(6)">6</button>
                    <button class="num-btn" onclick="inputNumber(7)">7</button>
                    <button class="num-btn" onclick="inputNumber(8)">8</button>
                    <button class="num-btn" onclick="inputNumber(9)">9</button>
                    <button class="num-btn" onclick="inputNumber(0)">⌫</button>
                </div>
                <div class="game-controls">
                    <button class="ctrl-btn" onclick="checkSudoku()">✓ Check</button>
                    <button class="ctrl-btn" onclick="revealHint()">💡 Hint</button>
                    <button class="ctrl-btn primary" onclick="newSudoku(currentDifficulty)">🔄 New Game</button>
                </div>
            </div>
        </div>

        <!-- MEMORY SECTION -->
        <div id="memory" class="game-section">
            <div class="memory-container">
                <div class="instructions">
                    Flip two cards at a time. Find all matching pairs to win!
                </div>
                <div class="memory-stats">
                    <div><span>Moves: </span><strong id="memory-moves">0</strong></div>
                    <div><span>Pairs: </span><strong id="memory-pairs">0</strong>/8</div>
                    <div><span>Time: </span><strong id="memory-timer">00:00</strong></div>
                </div>
                <div class="memory-board" id="memory-board"></div>
                <div class="game-controls">
                    <button class="ctrl-btn primary" onclick="initMemory()">🔄 New Game</button>
                </div>
            </div>
        </div>

        <!-- KERAM SECTION -->
        <div id="keram" class="game-section">
            <div class="keram-container">
                <div class="instructions">
                    Watch the sequence of colors, then repeat it! Each round adds one more to the sequence.
                </div>
                <div class="keram-status" id="keram-status">Press Start to begin</div>
                <div class="keram-score">Level: <span id="keram-level">0</span></div>
                <div class="keram-board" id="keram-board">
                    <div class="keram-btn green" data-index="0" onclick="keramInput(0)"></div>
                    <div class="keram-btn red" data-index="1" onclick="keramInput(1)"></div>
                    <div class="keram-btn yellow" data-index="2" onclick="keramInput(2)"></div>
                    <div class="keram-btn blue" data-index="3" onclick="keramInput(3)"></div>
                </div>
                <div class="game-controls">
                    <button class="ctrl-btn primary" onclick="startKeram()">▶ Start</button>
                </div>
            </div>
        </div>

        <!-- 2048 SECTION -->
        <div id="game2048" class="game-section">
            <div class="game-2048-container">
                <div class="instructions">
                    Use arrow keys or swipe to move tiles. Merge same numbers to reach 2048!
                </div>
                <div class="game-2048-header">
                    <div class="score-box">
                        <label>SCORE</label>
                        <div class="value" id="score-2048">0</div>
                    </div>
                    <div class="score-box">
                        <label>BEST</label>
                        <div class="value" id="best-2048">0</div>
                    </div>
                </div>
                <div class="board-2048" id="board-2048"></div>
                <div class="game-controls">
                    <button class="ctrl-btn primary" onclick="init2048()">🔄 New Game</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal -->
    <div class="modal-overlay" id="modal">
        <div class="modal">
            <h2 id="modal-title">🎉 Congratulations!</h2>
            <p id="modal-message">You completed the puzzle!</p>
            <button class="ctrl-btn primary" onclick="closeModal()">Continue</button>
        </div>
    </div>

    <script>
        // Navigation
        function showSection(id) {
            document.querySelectorAll('.game-section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            const btns = document.querySelectorAll('.nav-btn');
            const names = ['home', 'sudoku', 'memory', 'keram', 'game2048'];
            const idx = names.indexOf(id);
            if (idx >= 0) btns[idx].classList.add('active');
        }

        function showModal(title, message) {
            document.getElementById('modal-title').textContent = title;
            document.getElementById('modal-message').textContent = message;
            document.getElementById('modal').classList.add('active');
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('active');
        }

        // ============ SUDOKU ============
        let sudokuSolution = [];
        let sudokuBoard = [];
        let sudokuGiven = [];
        let selectedCell = -1;
        let currentDifficulty = 'easy';
        let sudokuTimerInterval;
        let sudokuSeconds = 0;

        function generateSudoku() {
            let grid = Array(9).fill(null).map(() => Array(9).fill(0));
            
            function isValid(grid, row, col, num) {
                for (let i = 0; i < 9; i++) {
                    if (grid[row][i] === num || grid[i][col] === num) return false;
                }
                let boxRow = Math.floor(row / 3) * 3;
                let boxCol = Math.floor(col / 3) * 3;
                for (let i = 0; i < 3; i++) {
                    for (let j = 0; j < 3; j++) {
                        if (grid[boxRow + i][boxCol + j] === num) return false;
                    }
                }
                return true;
            }

            function solve(grid) {
                for (let row = 0; row < 9; row++) {
                    for (let col = 0; col < 9; col++) {
                        if (grid[row][col] === 0) {
                            let nums = [1,2,3,4,5,6,7,8,9];
                            nums.sort(() => Math.random() - 0.5);
                            for (let num of nums) {
                                if (isValid(grid, row, col, num)) {
                                    grid[row][col] = num;
                                    if (solve(grid)) return true;
                                    grid[row][col] = 0;
                                }
                            }
                            return false;
                        }
                    }
                }
                return true;
            }

            solve(grid);
            return grid;
        }

        function newSudoku(difficulty) {
            currentDifficulty = difficulty;
            document.querySelectorAll('.diff-btn').forEach(b => b.classList.remove('active'));
            if (event && event.target) event.target.classList.add('active');

            sudokuSolution = generateSudoku();
            sudokuBoard = sudokuSolution.map(row => [...row]);
            sudokuGiven = Array(9).fill(null).map(() => Array(9).fill(false));

            let cellsToRemove = difficulty === 'easy' ? 35 : difficulty === 'medium' ? 45 : 52;
            let positions = [];
            for (let i = 0; i < 81; i++) positions.push(i);
            positions.sort(() => Math.random() - 0.5);

            for (let i = 0; i < cellsToRemove; i++) {
                let pos = positions[i];
                let row = Math.floor(pos / 9);
                let col = pos % 9;
                sudokuBoard[row][col] = 0;
            }

            for (let i = 0; i < 81; i++) {
                let row = Math.floor(i / 9);
                let col = i % 9;
                if (sudokuBoard[row][col] !== 0) {
                    sudokuGiven[row][col] = true;
                }
            }

            selectedCell = -1;
            renderSudoku();
            startSudokuTimer();
        }

        function renderSudoku() {
            const board = document.getElementById('sudoku-board');
            board.innerHTML = '';
            for (let i = 0; i < 81; i++) {
                let row = Math.floor(i / 9);
                let col = i % 9;
                let cell = document.createElement('div');
                cell.className = 'sudoku-cell';
                if (sudokuGiven[row][col]) {
                    cell.classList.add('given');
                    cell.textContent = sudokuBoard[row][col];
                } else if (sudokuBoard[row][col] !== 0) {
                    cell.classList.add('user-input');
                    cell.textContent = sudokuBoard[row][col];
                }
                if (i === selectedCell) cell.classList.add('selected');
                
                if (selectedCell >= 0) {
                    let selRow = Math.floor(selectedCell / 9);
                    let selCol = selectedCell % 9;
                    if (row === selRow || col === selCol || 
                        (Math.floor(row/3) === Math.floor(selRow/3) && Math.floor(col/3) === Math.floor(selCol/3))) {
                        if (i !== selectedCell) cell.classList.add('highlight');
                    }
                }

                cell.addEventListener('click', () => {
                    if (!sudokuGiven[row][col]) {
                        selectedCell = i;
                        renderSudoku();
                    }
                });
                board.appendChild(cell);
            }
        }

        function inputNumber(num) {
            if (selectedCell < 0) return;
            let row = Math.floor(selectedCell / 9);
            let col = selectedCell % 9;
            if (sudokuGiven[row][col]) return;

            if (num === 0) {
                sudokuBoard[row][col] = 0;
            } else {
                sudokuBoard[row][col] = num;
            }
            renderSudoku();

            let complete = true;
            for (let i = 0; i < 81; i++) {
                let r = Math.floor(i / 9);
                let c = i % 9;
                if (sudokuBoard[r][c] === 0) { complete = false; break; }
            }
            if (complete) checkSudoku();
        }

        function checkSudoku() {
            let correct = true;
            for (let i = 0; i < 81; i++) {
                let row = Math.floor(i / 9);
                let col = i % 9;
                if (sudokuBoard[row][col] !== sudokuSolution[row][col]) {
                    correct = false;
                    if (!sudokuGiven[row][col] && sudokuBoard[row][col] !== 0) {
                        document.querySelectorAll('.sudoku-cell')[i].classList.add('error');
                    }
                }
            }
            if (correct) {
                clearInterval(sudokuTimerInterval);
                showModal('🎉 Sudoku Complete!', `You solved it in ${document.getElementById('sudoku-timer').textContent}!`);
            }
        }

        function revealHint() {
            if (selectedCell < 0) return;
            let row = Math.floor(selectedCell / 9);
            let col = selectedCell % 9;
            if (!sudokuGiven[row][col]) {
                sudokuBoard[row][col] = sudokuSolution[row][col];
                sudokuGiven[row][col] = true;
                renderSudoku();
            }
        }

        function startSudokuTimer() {
            clearInterval(sudokuTimerInterval);
            sudokuSeconds = 0;
            document.getElementById('sudoku-timer').textContent = '00:00';
            sudokuTimerInterval = setInterval(() => {
                sudokuSeconds++;
                let min = String(Math.floor(sudokuSeconds / 60)).padStart(2, '0');
                let sec = String(sudokuSeconds % 60).padStart(2, '0');
                document.getElementById('sudoku-timer').textContent = `${min}:${sec}`;
            }, 1000);
        }

        // Keyboard input for Sudoku
        document.addEventListener('keydown', (e) => {
            if (document.getElementById('sudoku').classList.contains('active')) {
                if (e.key >= '1' && e.key <= '9') {
                    inputNumber(parseInt(e.key));
                } else if (e.key === 'Backspace' || e.key === 'Delete' || e.key === '0') {
                    inputNumber(0);
                } else if (e.key === 'ArrowUp' && selectedCell >= 9) {
                    selectedCell -= 9;
                    renderSudoku();
                } else if (e.key === 'ArrowDown' && selectedCell < 72) {
                    selectedCell += 9;
                    renderSudoku();
                } else if (e.key === 'ArrowLeft' && selectedCell % 9 > 0) {
                    selectedCell--;
                    renderSudoku();
                } else if (e.key === 'ArrowRight' && selectedCell % 9 < 8) {
                    selectedCell++;
                    renderSudoku();
                }
            }
        });

        // ============ MEMORY GAME ============
        const memoryEmojis = ['🎸', '🎺', '', '🎭', '🎪', '', '🎲', '🎮'];
        let memoryCards = [];
        let flippedCards = [];
        let memoryMoves = 0;
        let memoryPairs = 0;
        let memoryLocked = false;
        let memoryTimerInterval;
        let memorySeconds = 0;

        function initMemory() {
            clearInterval(memoryTimerInterval);
            memorySeconds = 0;
            memoryMoves = 0;
            memoryPairs = 0;
            flippedCards = [];
            memoryLocked = false;
            document.getElementById('memory-moves').textContent = '0';
            document.getElementById('memory-pairs').textContent = '0';
            document.getElementById('memory-timer').textContent = '00:00';

            let pairs = [...memoryEmojis, ...memoryEmojis];
            pairs.sort(() => Math.random() - 0.5);
            memoryCards = pairs;

            renderMemory();
            
            memoryTimerInterval = setInterval(() => {
                memorySeconds++;
                let min = String(Math.floor(memorySeconds / 60)).padStart(2, '0');
                let sec = String(memorySeconds % 60).padStart(2, '0');
                document.getElementById('memory-timer').textContent = `${min}:${sec}`;
            }, 1000);
        }

        function renderMemory() {
            const board = document.getElementById('memory-board');
            board.innerHTML = '';
            memoryCards.forEach((emoji, index) => {
                let card = document.createElement('div');
                card.className = 'memory-card';
                if (flippedCards.includes(index)) card.classList.add('flipped');
                
                card.innerHTML = `
                    <div class="memory-card-inner">
                        <div class="memory-card-front"></div>
                        <div class="memory-card-back">${emoji}</div>
                    </div>
                `;
                card.addEventListener('click', () => flipMemoryCard(index));
                board.appendChild(card);
            });
        }

        function flipMemoryCard(index) {
            if (memoryLocked) return;
            if (flippedCards.includes(index)) return;
            if (flippedCards.length >= 2) return;

            flippedCards.push(index);
            renderMemory();

            if (flippedCards.length === 2) {
                memoryMoves++;
                document.getElementById('memory-moves').textContent = memoryMoves;
                memoryLocked = true;

                let [first, second] = flippedCards;
                if (memoryCards[first] === memoryCards[second]) {
                    memoryPairs++;
                    document.getElementById('memory-pairs').textContent = memoryPairs;
                    flippedCards = [];
                    memoryLocked = false;
                    
                    let cards = document.querySelectorAll('.memory-card');
                    cards[first].classList.add('matched');
                    cards[second].classList.add('matched');

                    if (memoryPairs === 8) {
                        clearInterval(memoryTimerInterval);
                        setTimeout(() => {
                            showModal('🎉 All Pairs Found!', `Completed in ${memoryMoves} moves and ${document.getElementById('memory-timer').textContent}!`);
                        }, 500);
                    }
                } else {
                    setTimeout(() => {
                        flippedCards = [];
                        memoryLocked = false;
                        renderMemory();
                    }, 1000);
                }
            }
        }

        // ============ KERAM (Pattern Memory Game) ============
        let keramSequence = [];
        let keramPlayerInput = [];
        let keramLevel = 0;
        let keramPlaying = false;
        let keramShowingSequence = false;

        function startKeram() {
            keramSequence = [];
            keramLevel = 0;
            keramPlaying = true;
            document.getElementById('keram-level').textContent = '0';
            nextKeramRound();
        }

        function nextKeramRound() {
            keramLevel++;
            document.getElementById('keram-level').textContent = keramLevel;
            keramPlayerInput = [];
            keramShowingSequence = true;
            document.getElementById('keram-status').textContent = 'Watch the sequence...';

            keramSequence.push(Math.floor(Math.random() * 4));
            showKeramSequence(0);
        }

        function showKeramSequence(index) {
            if (index >= keramSequence.length) {
                keramShowingSequence = false;
                document.getElementById('keram-status').textContent = 'Your turn! Repeat the pattern.';
                return;
            }

            setTimeout(() => {
                let btns = document.querySelectorAll('.keram-btn');
                let btn = btns[keramSequence[index]];
                btn.classList.add('active');
                
                setTimeout(() => {
                    btn.classList.remove('active');
                    setTimeout(() => showKeramSequence(index + 1), 200);
                }, 500);
            }, 300);
        }

        function keramInput(index) {
            if (!keramPlaying || keramShowingSequence) return;

            let btns = document.querySelectorAll('.keram-btn');
            btns[index].classList.add('active');
            setTimeout(() => btns[index].classList.remove('active'), 200);

            keramPlayerInput.push(index);
            let currentStep = keramPlayerInput.length - 1;

            if (keramPlayerInput[currentStep] !== keramSequence[currentStep]) {
                keramPlaying = false;
                document.getElementById('keram-status').textContent = `Game Over! You reached level ${keramLevel}`;
                setTimeout(() => {
                    showModal('🎨 Game Over!', `You reached level ${keramLevel} in Keram!`);
                }, 500);
                return;
            }

            if (keramPlayerInput.length === keramSequence.length) {
                document.getElementById('keram-status').textContent = '✓ Correct!';
                setTimeout(() => nextKeramRound(), 1000);
            }
        }

        // ============ 2048 GAME ============
        let grid2048 = [];
        let score2048 = 0;
        let best2048 = parseInt(localStorage.getItem('best2048') || '0');

        function init2048() {
            grid2048 = Array(4).fill(null).map(() => Array(4).fill(0));
            score2048 = 0;
            document.getElementById('score-2048').textContent = '0';
            document.getElementById('best-2048').textContent = best2048;
            addRandomTile();
            addRandomTile();
            render2048();
        }

        function addRandomTile() {
            let empty = [];
            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    if (grid2048[i][j] === 0) empty.push({r: i, c: j});
                }
            }
            if (empty.length === 0) return;
            let pos = empty[Math.floor(Math.random() * empty.length)];
            grid2048[pos.r][pos.c] = Math.random() < 0.9 ? 2 : 4;
        }

        function render2048() {
            const board = document.getElementById('board-2048');
            board.innerHTML = '';
            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    let tile = document.createElement('div');
                    tile.className = 'tile-2048';
                    if (grid2048[i][j] !== 0) {
                        tile.textContent = grid2048[i][j];
                        tile.setAttribute('data-value', grid2048[i][j]);
                    }
                    board.appendChild(tile);
                }
            }
        }

        function move2048(direction) {
            let moved = false;
            let newGrid = grid2048.map(row => [...row]);

            function slide(row) {
                let arr = row.filter(v => v !== 0);
                let result = [];
                for (let i = 0; i < arr.length; i++) {
                    if (i < arr.length - 1 && arr[i] === arr[i + 1]) {
                        result.push(arr[i] * 2);
                        score2048 += arr[i] * 2;
                        i++;
                    } else {
                        result.push(arr[i]);
                    }
                }
                while (result.length < 4) result.push(0);
                return result;
            }

            if (direction === 'left') {
                for (let i = 0; i < 4; i++) {
                    newGrid[i] = slide(newGrid[i]);
                }
            } else if (direction === 'right') {
                for (let i = 0; i < 4; i++) {
                    newGrid[i] = slide(newGrid[i].reverse()).reverse();
                }
            } else if (direction === 'up') {
                for (let j = 0; j < 4; j++) {
                    let col = [newGrid[0][j], newGrid[1][j], newGrid[2][j], newGrid[3][j]];
                    let slid = slide(col);
                    for (let i = 0; i < 4; i++) newGrid[i][j] = slid[i];
                }
            } else if (direction === 'down') {
                for (let j = 0; j < 4; j++) {
                    let col = [newGrid[3][j], newGrid[2][j], newGrid[1][j], newGrid[0][j]];
                    let slid = slide(col);
                    for (let i = 0; i < 4; i++) newGrid[3 - i][j] = slid[i];
                }
            }

            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    if (newGrid[i][j] !== grid2048[i][j]) moved = true;
                }
            }

            if (moved) {
                grid2048 = newGrid;
                addRandomTile();
                document.getElementById('score-2048').textContent = score2048;
                if (score2048 > best2048) {
                    best2048 = score2048;
                    localStorage.setItem('best2048', best2048);
                    document.getElementById('best-2048').textContent = best2048;
                }
                render2048();

                for (let i = 0; i < 4; i++) {
                    for (let j = 0; j < 4; j++) {
                        if (grid2048[i][j] === 2048) {
                            showModal('🎉 You Win!', `You reached 2048 with a score of ${score2048}!`);
                        }
                    }
                }

                if (isGameOver2048()) {
                    setTimeout(() => {
                        showModal('💀 Game Over!', `Final score: ${score2048}`);
                    }, 300);
                }
            }
        }

        function isGameOver2048() {
            for (let i = 0; i < 4; i++) {
                for (let j = 0; j < 4; j++) {
                    if (grid2048[i][j] === 0) return false;
                    if (j < 3 && grid2048[i][j] === grid2048[i][j + 1]) return false;
                    if (i < 3 && grid2048[i][j] === grid2048[i + 1][j]) return false;
                }
            }
            return true;
        }

        // Keyboard controls for 2048
        document.addEventListener('keydown', (e) => {
            if (document.getElementById('game2048').classList.contains('active')) {
                if (e.key === 'ArrowLeft') { e.preventDefault(); move2048('left'); }
                if (e.key === 'ArrowRight') { e.preventDefault(); move2048('right'); }
                if (e.key === 'ArrowUp') { e.preventDefault(); move2048('up'); }
                if (e.key === 'ArrowDown') { e.preventDefault(); move2048('down'); }
            }
        });

        // Touch/swipe support for 2048
        let touchStartX, touchStartY;
        document.getElementById('board-2048').addEventListener('touchstart', (e) => {
            touchStartX = e.touches[0].clientX;
            touchStartY = e.touches[0].clientY;
        });

        document.getElementById('board-2048').addEventListener('touchend', (e) => {
            if (!touchStartX || !touchStartY) return;
            let dx = e.changedTouches[0].clientX - touchStartX;
            let dy = e.changedTouches[0].clientY - touchStartY;
            
            if (Math.abs(dx) > Math.abs(dy)) {
                if (dx > 30) move2048('right');
                else if (dx < -30) move2048('left');
            } else {
                if (dy > 30) move2048('down');
                else if (dy < -30) move2048('up');
            }
            touchStartX = null;
            touchStartY = null;
        });

        // Initialize games
        newSudoku('easy');
        initMemory();
        init2048();
    </script>
</body>
</html>
