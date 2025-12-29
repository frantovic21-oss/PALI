<!DOCTYPE html>
<html lang="sk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PALI VYSOKAI - GRAFFITI MILIONÁR</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Permanent+Marker&display=swap');

        body {
            background-image: url('https://i.postimg.cc/RCcN7J7P/1762511744708.png');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            background-color: #000;
            color: #0f0;
            font-family: 'Arial Black', sans-serif;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }

        /* Úvodná obrazovka */
        #start-screen {
            position: absolute;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        #start-screen h1 {
            font-size: 4rem;
            font-family: 'Permanent Marker', cursive;
            color: #0f0;
            text-shadow: 0 0 20px #0f0, 0 0 40px #0f0;
            margin-bottom: 40px;
            text-align: center;
        }

        .start-btn {
            background: #0f0;
            color: #000;
            border: none;
            padding: 25px 60px;
            font-size: 2.5rem;
            font-family: 'Permanent Marker', cursive;
            border-radius: 100px;
            cursor: pointer;
            box-shadow: 0 0 50px #0f0;
            transition: 0.3s;
        }

        .start-btn:hover { transform: scale(1.1); background: #fff; }

        /* Graffiti Prechod (Question Intro) */
        #question-intro {
            display: none;
            position: fixed;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.9);
            z-index: 500;
            justify-content: center;
            align-items: center;
        }

        .graffiti-text {
            font-family: 'Permanent Marker', cursive;
            font-size: 6rem;
            color: #ff00ff;
            text-shadow: 0 0 20px #ff00ff, 0 0 50px #ff00ff;
            animation: rotateGraffiti 2s linear infinite;
        }

        @keyframes rotateGraffiti {
            0% { transform: rotateY(0deg) scale(1); }
            50% { transform: rotateY(180deg) scale(1.3); color: #00ffff; text-shadow: 0 0 20px #00ffff; }
            100% { transform: rotateY(360deg) scale(1); }
        }

        /* Herné okno */
        #game-screen {
            display: none;
            width: 90%;
            max-width: 650px;
            border: 5px solid #0f0;
            padding: 30px;
            box-shadow: 0 0 60px rgba(0, 255, 0, 0.9);
            background: rgba(0, 0, 0, 0.95);
            border-radius: 40px;
            text-align: center;
            z-index: 100;
        }

        .option-btn {
            background: rgba(0, 255, 0, 0.05);
            border: 2px solid #0f0;
            color: #fff;
            padding: 18px;
            margin: 12px 0;
            font-size: 1.2rem;
            cursor: pointer;
            border-radius: 20px;
            transition: 0.2s;
            display: block;
            width: 100%;
            text-align: left;
        }

        .option-btn.selected { background: orange !important; color: #000 !important; border-color: #fff; }

        #feedback-text {
            position: fixed;
            top: 20%;
            width: 100%;
            text-align: center;
            font-size: 4rem;
            font-family: 'Permanent Marker', cursive;
            z-index: 2000;
            display: none;
            text-shadow: 0 0 30px #000;
        }
    </style>
</head>
<body>

<audio id="bg-music" loop src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-17.mp3"></audio>
<audio id="snd-correct" src="https://www.myinstants.com/media/sounds/cheer.mp3"></audio>
<audio id="snd-wrong" src="https://www.myinstants.com/media/sounds/aww-meme.mp3"></audio>

<div id="start-screen">
    <h1>HRA PRE TEBE!!!</h1>
    <button class="start-btn" onclick="startGame()">SPUŠČI ME!</button>
</div>

<div id="question-intro">
    <div class="graffiti-text" id="intro-text">1. OTÁZKA</div>
</div>

<div id="feedback-text"></div>

<div id="game-screen">
    <div style="display:flex; justify-content: space-between; color: gold; font-size: 1.3rem; margin-bottom: 20px;">
        <span id="q-count">1/10</span>
        <span id="money">0 €</span>
    </div>
    <h2 id="question-text" style="min-height: 100px; color: #0f0; font-size: 1.6rem;"></h2>
    <div id="options-grid">
        <button class="option-btn" onclick="checkAnswer(0, this)"></button>
        <button class="option-btn" onclick="checkAnswer(1, this)"></button>
        <button class="option-btn" onclick="checkAnswer(2, this)"></button>
        <button class="option-btn" onclick="checkAnswer(3, this)"></button>
    </div>
</div>

<script>
    const questions = [
        { q: "Čo povie PALI VYSOKAI, keď mu niekto povie, že kód nefunguje?", a: ["U mňa to ide!", "Skús reštartovať mozog", "To nie je chyba, to je môj plán", "Nerozumiem reči smrteľníkov"], correct: 2 },
        { q: "Prečo PALI VYSOKAI nosí v obleku slnečné okuliare?", a: ["Vidí v nich binárny kód", "Slnko sa ho bojí, tak ho tlmí", "Sú v nich skryté kamery z Marsu", "Aby neoslepil okolie svojou krásou"], correct: 0 },
        { q: "Ako PALI VYSOKAI hackuje bankomaty?", a: ["Zadá PIN 'PALI'", "Bankomat mu peniaze dá sám od strachu", "Používa bluetooth v prste", "On ich nehackuje, on ich vlastní"], correct: 1 },
        { q: "Čo urobí PALI VYSOKAI, keď stretne ChatGPT?", a: ["Vypne ho jedným dychom", "Nainštaluje mu city k Palimu", "Prepíše ho na slovenský slang", "Všetko naraz"], correct: 3 },
        { q: "Aký je PALI VYSOKAI v posteli?", a: ["Rýchly ako 5G sieť", "Tvrdý ako neodinštalovateľný vírus", "Legenda, o ktorej sa píšu skripty", "Iba kód vie pravdu"], correct: 2 },
        { q: "Ktorý prehliadač používa PALI VYSOKAI?", a: ["Google Chrome", "PaliExplorer 3000", "On neprehliada, on tie weby tvorí v hlave", "Internet Explorer (zo súcitu)"], correct: 2 },
        { q: "Čo má PALI VYSOKAI namiesto srdca?", a: ["Grafickú kartu RTX 5090", "Čisté zlato", "Kus kódu, ktorý nikdy nemrzne", "Reaktor zo Stark Industries"], correct: 2 },
        { q: "Ako PALI VYSOKAI rieši globálne otepľovanie?", a: ["Hackne slnko", "Fúkne na zem", "Nainštaluje svetu update 'CoolDown'", "On je príliš hot, on za to môže"], correct: 2 },
        { q: "Kto učí PALIHO VYSOKAIA programovať?", a: ["Nikto, on naučil stroje myslieť", "Bill Gates mu nosí kávu", "Elon Musk mu píše maily", "On sa narodil s klávesnicou"], correct: 0 },
        { q: "Kedy PALI VYSOKAI prehrá túto hru?", a: ["Nikdy", "Keď zamrzne peklo", "Keď prestane byť legenda", "Odpovede A, B aj C sú správne"], correct: 3 }
    ];

    let currentQ = 0;
    let score = 0;
    const bgMusic = document.getElementById('bg-music');

    function startGame() {
        currentQ = 0;
        score = 0;
        document.getElementById('start-screen').style.display = 'none';
        document.getElementById('game-screen').style.display = 'none';
        bgMusic.volume = 0.2;
        bgMusic.currentTime = 0;
        bgMusic.play();
        startNextQuestionCycle();
    }

    function startNextQuestionCycle() {
        document.getElementById('game-screen').style.display = 'none';
        const intro = document.getElementById('question-intro');
        const introText = document.getElementById('intro-text');
        
        introText.innerText = `${currentQ + 1}. OTÁZKA`;
        intro.style.display = 'flex';

        setTimeout(() => {
            intro.style.display = 'none';
            document.getElementById('game-screen').style.display = 'block';
            updateScreen();
        }, 2000);
    }

    function speak(text) {
        window.speechSynthesis.cancel();
        const msg = new SpeechSynthesisUtterance();
        msg.text = text;
        msg.lang = 'sk-SK';
        window.speechSynthesis.speak(msg);
    }

    function updateScreen() {
        const btns = document.querySelectorAll('.option-btn');
        btns.forEach(btn => {
            btn.classList.remove('selected');
            btn.style.display = 'block'; // Zajistí, že tlačítka jsou vidět
        });

        if (currentQ < questions.length) {
            document.getElementById('q-count').innerText = `OTÁZKA ${currentQ + 1}/10`;
            document.getElementById('money').innerText = `VÝHRA: ${score} €`;
            document.getElementById('question-text').innerText = questions[currentQ].q;
            
            questions[currentQ].a.forEach((opt, i) => {
                btns[i].innerText = `${String.fromCharCode(65+i)}: ${opt}`;
            });
        } else {
            winGame();
        }
    }

    function checkAnswer(idx, clickedBtn) {
        clickedBtn.classList.add('selected');

        if (idx === questions[currentQ].correct) {
            // SPRÁVNĚ
            speak("Výborne Pali");
            showFeedback("VÝBORNE PALI", "#0f0");
            document.getElementById('snd-correct').play();
            score += (currentQ + 1) * 5000;
            currentQ++;
            setTimeout(() => {
                if (currentQ < questions.length) startNextQuestionCycle();
                else updateScreen();
            }, 2000);
        } else {
            // ŠPATNĚ
            speak("Nevadí, pusťi zaš");
            showFeedback("NEVADÍ, PUŠŤI ZAŠ!!!", "red");
            document.getElementById('snd-wrong').play();
            
            setTimeout(() => {
                // Místo reloadu jen přepneme obrazovku
                bgMusic.pause();
                document.getElementById('game-screen').style.display = 'none';
                document.getElementById('feedback-text').style.display = 'none';
                document.getElementById('start-screen').style.display = 'flex';
            }, 2000);
        }
    }

    function showFeedback(text, color) {
        const fb = document.getElementById('feedback-text');
        fb.innerText = text;
        fb.style.color = color;
        fb.style.display = 'block';
        setTimeout(() => fb.style.display = 'none', 2000);
    }

    function winGame() {
        speak("PALI VYSOKAI, si kráľ Matrixu a milionár!");
        document.getElementById('game-screen').innerHTML = `
            <h1 style="color:gold; font-family:'Permanent Marker'">MILIONÁR PALI!</h1>
            <p style="font-size:2rem">Vyhral si ${score} €</p>
            <button class="start-btn" onclick="location.reload()">REŠTART SHOW</button>
        `;
    }
</script>
</body>
</html>
