# TotheMoon
Travel to the Moon maths game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    
    <!-- GitHub/SEO Meta Tags -->
    <title>Galaxy Math Quest - Kids Multiplication Game</title>
    <meta name="description" content="A fun, space-themed math game for kids to practice multiplication and division.">
    <meta property="og:title" content="Galaxy Math Quest">
    <meta property="og:description" content="Help the rocket reach the Moon by solving math puzzles!">
    <meta property="og:type" content="website">
    
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka+One&family=Quicksand:wght@400;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --app-height: 100%;
        }
        
        body {
            font-family: 'Quicksand', sans-serif;
            background: radial-gradient(circle at top right, #1e1b4b, #020617);
            color: white;
            overflow: hidden;
            touch-action: manipulation;
            /* Using dvh for better mobile browser support */
            height: 100vh;
            height: 100dvh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            margin: 0;
        }

        .heading-font {
            font-family: 'Fredoka One', cursive;
        }

        /* Star Background */
        .star {
            position: absolute;
            background: white;
            border-radius: 50%;
            opacity: 0.5;
            animation: twinkle var(--duration) infinite ease-in-out;
            z-index: 0;
        }
        @keyframes twinkle {
            0%, 100% { opacity: 0.3; transform: scale(1); }
            50% { opacity: 1; transform: scale(1.2); }
        }
        
        .main-wrapper {
            width: 100%;
            max-width: 480px;
            padding: 1rem;
            margin-bottom: 100px; /* Space for the Journey Bar */
            z-index: 10;
        }

        /* Rocket & Boost Animations */
        .rocket-container {
            transition: left 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
            display: flex;
            align-items: center;
        }
        
        .rocket-icon {
            font-size: 2.5rem;
            transform: rotate(45deg);
        }
        
        .boost-fire {
            width: 35px;
            height: 18px;
            background: linear-gradient(to left, transparent, #fb923c, #ef4444, #fff);
            border-radius: 50% 0 0 50%;
            opacity: 0;
            margin-right: -4px;
            filter: blur(1.5px);
        }

        .boosting .boost-fire {
            opacity: 1;
            animation: flicker 0.1s infinite;
        }

        @keyframes flicker {
            0%, 100% { transform: scaleX(1); opacity: 0.8; }
            50% { transform: scaleX(2); opacity: 1; }
        }

        .correct-flash {
            animation: flashGreen 0.5s ease-out;
        }
        @keyframes flashGreen {
            0% { background-color: transparent; }
            50% { background-color: rgba(34, 197, 94, 0.2); }
            100% { background-color: transparent; }
        }

        .wrong-shake {
            animation: shake 0.4s ease-in-out;
        }
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-12px); }
            75% { transform: translateX(12px); }
        }

        @keyframes flyAway {
            0% { transform: scale(1); }
            100% { transform: scale(5) translateX(300px); opacity: 0; }
        }
        .rocket-landed {
            animation: flyAway 1.5s forwards ease-in;
        }

        .float {
            animation: float 4s infinite ease-in-out;
        }
        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        /* Button Active State for Touch */
        .math-btn:active {
            transform: translateY(4px);
            box-shadow: none !important;
        }
    </style>
</head>
<body>
    <!-- Background Layer -->
    <div id="stars-container" aria-hidden="true"></div>

    <div class="main-wrapper">
        <!-- Main Game Card -->
        <div id="game-container" class="relative w-full bg-white/5 backdrop-blur-2xl border-4 border-white/20 rounded-[2.5rem] p-6 sm:p-8 shadow-2xl transition-all duration-300 overflow-hidden">
            
            <!-- Start Screen -->
            <div id="start-screen" class="text-center py-6">
                <div class="text-8xl mb-6 float">🚀</div>
                <h1 class="heading-font text-5xl mb-4 text-yellow-400 drop-shadow-md">Galaxy Quest</h1>
                <p class="text-lg mb-10 text-indigo-100">Fly Earth to Moon Mission!</p>
                <button onclick="startGame()" class="bg-gradient-to-b from-yellow-300 to-orange-500 text-indigo-950 font-bold py-5 px-14 rounded-3xl text-3xl shadow-[0_8px_0_rgb(194,120,3)] transition-all transform hover:scale-105 active:translate-y-2 active:shadow-none">
                    LIFT OFF
                </button>
            </div>

            <!-- Game Play Screen -->
            <div id="play-screen" class="hidden">
                <div class="flex justify-between items-center mb-6">
                    <div class="bg-black/40 rounded-2xl px-5 py-2 flex items-center gap-2 border border-white/10">
                        <span class="text-yellow-400 text-xl">⭐</span>
                        <span id="score-display" class="font-bold text-3xl text-yellow-50">0</span>
                    </div>
                    <div class="flex gap-1" id="lives-container"></div>
                </div>

                <div class="text-center mb-8 py-12 bg-white/5 rounded-[2rem] border-2 border-white/10 shadow-inner">
                    <div id="question-text" class="heading-font text-6xl sm:text-7xl text-white tracking-widest">
                        --
                    </div>
                </div>

                <div id="options-grid" class="grid grid-cols-2 gap-5">
                    <!-- Buttons generated by JS -->
                </div>
                
                <div id="feedback-msg" class="h-10 mt-8 text-center font-bold text-3xl opacity-0 transition-all duration-300 transform scale-90">
                    --
                </div>
            </div>

            <!-- End Screen -->
            <div id="game-over-screen" class="hidden text-center py-8">
                <h2 id="end-title" class="heading-font text-5xl mb-6 text-yellow-400">Mission Success!</h2>
                <div id="end-emoji" class="text-8xl mb-8">🌑✨</div>
                <p class="text-2xl mb-2 text-indigo-200">Total Solved</p>
                <div id="final-score" class="text-7xl font-bold text-white mb-10">0</div>
                <button onclick="startGame()" class="bg-white text-indigo-900 font-bold py-4 px-12 rounded-2xl text-2xl shadow-xl hover:bg-indigo-50 transition-all transform hover:scale-105">
                    REPLAY
                </button>
            </div>
        </div>
    </div>

    <!-- Journey Tracker -->
    <div id="progress-container" class="fixed bottom-8 left-0 w-full px-6 hidden">
        <div class="relative w-full max-w-xl mx-auto h-20 bg-black/50 backdrop-blur-lg rounded-full border-2 border-white/10 flex items-center px-6 overflow-hidden">
            <div class="z-20 text-4xl mr-auto filter drop-shadow-lg">🌍</div>
            <div class="absolute left-16 right-16 h-1 bg-white/10 rounded-full"></div>
            <div id="rocket-wrapper" class="rocket-container absolute z-30" style="left: 10%">
                <div class="boost-fire"></div>
                <div class="rocket-icon">🚀</div>
            </div>
            <div class="z-20 text-4xl ml-auto filter drop-shadow-md">🌑</div>
        </div>
        <p class="text-center mt-3 text-indigo-300 font-bold tracking-widest uppercase text-[10px]">Earth → Moon Progress</p>
    </div>

    <script>
        let score = 0;
        let lives = 5;
        let currentAnswer = 0;
        const WIN_SCORE = 15;
        
        // Audio System
        let audioCtx;
        function resumeAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();
        }

        function playSound(type) {
            resumeAudio();
            if (!audioCtx) return;

            if (type === 'boost') {
                const duration = 0.8;
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                const filter = audioCtx.createBiquadFilter();
                
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(40, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(400, audioCtx.currentTime + 0.1);
                osc.frequency.exponentialRampToValueAtTime(100, audioCtx.currentTime + duration);

                filter.type = 'lowpass';
                filter.frequency.setValueAtTime(800, audioCtx.currentTime);

                gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + duration);

                osc.connect(filter);
                filter.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + duration);
            } else if (type === 'win') {
                const melody = [523, 659, 784, 1046];
                melody.forEach((f, i) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    const time = audioCtx.currentTime + (i * 0.12);
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(f, time);
                    gain.gain.setValueAtTime(0.2, time);
                    gain.gain.exponentialRampToValueAtTime(0.001, time + 0.4);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(time);
                    osc.stop(time + 0.5);
                });
            }
        }

        // Star Background
        const starsContainer = document.getElementById('stars-container');
        for (let i = 0; i < 70; i++) {
            const star = document.createElement('div');
            star.className = 'star';
            const size = Math.random() * 2 + 1;
            star.style.width = size + 'px';
            star.style.height = size + 'px';
            star.style.left = Math.random() * 100 + 'vw';
            star.style.top = Math.random() * 100 + 'vh';
            star.style.setProperty('--duration', (Math.random() * 3 + 2) + 's');
            starsContainer.appendChild(star);
        }

        function startGame() {
            resumeAudio();
            score = 0;
            lives = 5;
            updateHUD();
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('game-over-screen').classList.add('hidden');
            document.getElementById('play-screen').classList.remove('hidden');
            document.getElementById('progress-container').classList.remove('hidden');
            const rocket = document.getElementById('rocket-wrapper');
            rocket.classList.remove('rocket-landed');
            rocket.style.display = 'flex';
            nextQuestion();
        }

        function nextQuestion() {
            const isMult = Math.random() > 0.4;
            let n1, n2, qStr;

            if (isMult) {
                const limit = score < 5 ? 5 : 10;
                n1 = Math.floor(Math.random() * limit) + 1;
                n2 = Math.floor(Math.random() * (score < 4 ? 5 : 10)) + 1;
                currentAnswer = n1 * n2;
                qStr = `${n1} × ${n2}`;
            } else {
                const div = Math.floor(Math.random() * 5) + 1; 
                const quo = Math.floor(Math.random() * (score < 5 ? 5 : 10)) + 1;
                currentAnswer = quo;
                qStr = `${quo * div} ÷ ${div}`;
            }

            document.getElementById('question-text').innerText = qStr;
            generateOptions(currentAnswer);
        }

        function generateOptions(correct) {
            const options = [correct];
            while (options.length < 4) {
                let w = correct + (Math.floor(Math.random() * 10) - 5);
                if (w > 0 && w !== correct && !options.includes(w)) options.push(w);
                else if (options.length < 4) {
                    w = correct + (Math.floor(Math.random() * 15) + 1);
                    if (!options.includes(w)) options.push(w);
                }
            }
            options.sort(() => Math.random() - 0.5);

            const grid = document.getElementById('options-grid');
            grid.innerHTML = '';
            options.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'math-btn bg-indigo-600/80 text-white font-bold py-6 rounded-3xl text-4xl shadow-[0_6px_0_rgb(30,27,75)] border border-white/10 outline-none select-none';
                btn.innerText = opt;
                btn.onclick = () => check(opt, btn);
                grid.appendChild(btn);
            });
        }

        function check(val, btn) {
            resumeAudio();
            const buttons = document.querySelectorAll('#options-grid button');
            buttons.forEach(b => b.disabled = true);

            if (val === currentAnswer) {
                score++;
                updateHUD();
                playSound('boost');
                
                const rocket = document.getElementById('rocket-wrapper');
                rocket.classList.add('boosting');
                
                const msg = document.getElementById('feedback-msg');
                msg.innerText = "ZOOM! 🚀";
                msg.className = 'h-10 mt-8 text-center font-bold text-3xl text-green-400 opacity-100 scale-110';
                
                btn.classList.replace('bg-indigo-600/80', 'bg-green-500');
                btn.style.boxShadow = '0 6px 0 rgb(21, 128, 61)';
                document.getElementById('game-container').classList.add('correct-flash');
                
                setTimeout(() => {
                    rocket.classList.remove('boosting');
                    document.getElementById('game-container').classList.remove('correct-flash');
                    msg.style.opacity = '0';
                    if (score >= WIN_SCORE) win();
                    else nextQuestion();
                }, 1000);
            } else {
                lives--;
                updateHUD();
                document.getElementById('game-container').classList.add('wrong-shake');
                btn.classList.add('opacity-40', 'grayscale');
                const msg = document.getElementById('feedback-msg');
                msg.innerText = "Try Again! ✨";
                msg.className = 'h-10 mt-8 text-center font-bold text-3xl text-orange-400 opacity-100';
                
                setTimeout(() => {
                    document.getElementById('game-container').classList.remove('wrong-shake');
                    msg.style.opacity = '0';
                    if (lives <= 0) end(false);
                    else buttons.forEach(b => { if(!b.classList.contains('opacity-40')) b.disabled = false; });
                }, 800);
            }
        }

        function updateHUD() {
            document.getElementById('score-display').innerText = score;
            const container = document.getElementById('lives-container');
            container.innerHTML = '';
            for (let i = 0; i < 5; i++) {
                const h = document.createElement('span');
                h.innerText = i < lives ? '❤️' : '🖤';
                h.className = 'text-2xl';
                container.appendChild(h);
            }
            const prog = (score / WIN_SCORE) * 75 + 10;
            document.getElementById('rocket-wrapper').style.left = prog + '%';
        }

        function win() {
            playSound('win');
            document.getElementById('rocket-wrapper').classList.add('rocket-landed');
            setTimeout(() => end(true), 1200);
        }

        function end(isWin) {
            document.getElementById('play-screen').classList.add('hidden');
            document.getElementById('game-over-screen').classList.remove('hidden');
            document.getElementById('progress-container').classList.add('hidden');
            document.getElementById('final-score').innerText = score;
            const t = document.getElementById('end-title');
            const e = document.getElementById('end-emoji');
            if (isWin) {
                t.innerText = "Mission Success!";
                t.className = "heading-font text-5xl mb-6 text-green-400";
                e.innerText = "🌑✨🚀";
            } else {
                t.innerText = "Mission Ended";
                t.className = "heading-font text-5xl mb-6 text-orange-400";
                e.innerText = "🌍🛰️";
            }
        }
    </script>
</body>
</html>
