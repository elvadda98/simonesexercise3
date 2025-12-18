<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced English Practice: Session 3</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --accent-color: #1abc9c;
            --light-bg: #f9fbfd;
            --dark-text: #2c3e50;
            --success-color: #27ae60;
            --warning-color: #f1c40f;
            --error-color: #e74c3c;
        }

        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--light-bg); color: var(--dark-text); margin: 0; padding-bottom: 50px; }
        header { background: var(--primary-color); color: white; padding: 25px; text-align: center; position: relative; }
        .score-display { position: absolute; top: 25px; right: 25px; background: rgba(255,255,255,0.1); padding: 5px 15px; border-radius: 20px; font-weight: bold; }
        .container { max-width: 950px; margin: 30px auto; padding: 0 20px; }
        
        .nav-buttons { display: flex; gap: 10px; margin-bottom: 25px; flex-wrap: wrap; }
        .nav-button { flex: 1; padding: 12px; border: none; border-radius: 8px; background: #dfe6e9; cursor: pointer; font-weight: bold; transition: 0.3s; }
        .nav-button.active { background: var(--secondary-color); color: white; }

        .exercise-section { background: white; padding: 30px; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .word-bank { display: flex; flex-wrap: wrap; gap: 12px; background: #f1f4f8; padding: 20px; border-radius: 10px; margin-bottom: 20px; border: 2px dashed var(--secondary-color); }
        .word-bank-item { background: white; padding: 8px 15px; border-radius: 6px; font-weight: bold; box-shadow: 0 2px 5px rgba(0,0,0,0.1); color: var(--primary-color); }

        .vocab-item { display: flex; align-items: center; gap: 20px; padding: 15px 0; border-bottom: 1px solid #eee; }
        .word-cell { flex: 0 0 180px; font-weight: bold; color: var(--secondary-color); font-size: 1.1em; }

        .gap-sentence { margin: 20px 0; font-size: 1.1em; line-height: 1.8; }
        .gap-input { border: none; border-bottom: 2px solid var(--secondary-color); width: 150px; text-align: center; font-size: inherit; outline: none; background: transparent; }
        
        .matching-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .match-box { padding: 15px; border: 2px solid #ecf0f1; border-radius: 8px; cursor: pointer; transition: 0.2s; background: white; }
        .match-box.selected { border-color: var(--secondary-color); background: #ebf5fb; }
        .match-box.correct { background: var(--success-color); color: white; border-color: var(--success-color); cursor: default; }

        .status-msg { display: block; margin-top: 5px; font-size: 0.85em; }
        .status-green { color: var(--success-color); font-weight: bold; }
        .status-yellow { color: var(--warning-color); font-weight: bold; }
        .status-red { color: var(--error-color); font-weight: bold; }

        .speaker-btn, .mic-btn { background: #f1f4f8; border: none; border-radius: 50%; width: 40px; height: 40px; cursor: pointer; display: inline-flex; align-items: center; justify-content: center; font-size: 1.2em; }
        .action-btn { background: var(--accent-color); color: white; border: none; padding: 12px 30px; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 20px; }
    </style>
</head>
<body>

<header>
    <h1>Vocabulary: Travel & Emotions</h1>
    <div class="score-display">Total Score: <span id="score-val">0</span></div>
</header>

<div class="container">
    <div class="nav-buttons">
        <button class="nav-button active" onclick="switchTab(0)">1. Study List</button>
        <button class="nav-button" onclick="switchTab(1)">2. Writing</button>
        <button class="nav-button" onclick="switchTab(2)">3. Matching</button>
        <button class="nav-button" onclick="switchTab(3)">4. Pronunciation</button>
    </div>

    <div id="tab-0" class="exercise-section">
        <h2>📚 Vocabulary List</h2>
        <div id="vocab-list"></div>
    </div>

    <div id="tab-1" class="exercise-section" style="display:none">
        <h2>✍️ Fill in the Gaps (Writing)</h2>
        <div id="writing-bank" class="word-bank"></div>
        <div id="writing-content"></div>
        <button class="action-btn" onclick="checkWriting()">Check Answers</button>
    </div>

    <div id="tab-2" class="exercise-section" style="display:none">
        <h2>🔗 Match Word & Meaning</h2>
        <div id="match-content" class="matching-grid"></div>
    </div>

    <div id="tab-3" class="exercise-section" style="display:none">
        <h2>🗣️ Pronunciation (Guess & Speak)</h2>
        <div id="pron-bank" class="word-bank"></div>
        <div id="pron-content"></div>
    </div>
</div>

<script>
    const data = [
        { word: "glasses", def: "Containers used for drinking wine or water.", sent: "We poured the red wine into the clean ___.", pron: "Please bring two more ___." },
        { word: "fall asleep", def: "To start sleeping.", sent: "I was so tired I could ___ standing up!", pron: "Don't ___ during the movie." },
        { word: "engine", def: "The machine that power a vehicle.", sent: "The car won't start; I think there's a problem with the ___.", pron: "Listen to the sound of the ___." },
        { word: "far away", def: "At a great distance.", sent: "The mountains look very beautiful from ___.", pron: "They live very ___ from here." },
        { word: "so", def: "Used to show a consequence (così).", sent: "I missed the bus, ___ I had to walk home.", pron: "It was raining, ___ I took an umbrella." },
        { word: "anyway", def: "Used to change the subject or resume a point (comunque).", sent: "I'm not sure if he's coming, but let's start ___.", pron: "___, as I was saying..." },
        { word: "tire", def: "The rubber part of a wheel (pneumatico).", sent: "I have a flat ___, so I need to change it.", pron: "The back ___ is low on air." },
        { word: "scratch", def: "A thin mark or cut on a surface (graffio).", sent: "I found a tiny ___ on the side of my car.", pron: "There is a ___ on my phone screen." },
        { word: "upset", def: "Feeling unhappy, disappointed, or worried.", sent: "She was very ___ when she lost her keys.", pron: "Don't be ___ about the results." },
        { word: "wear out", def: "To become thin or unusable through use.", sent: "Cheap shoes tend to ___ very quickly.", pron: "My tires are starting to ___." }
    ];

    let totalScore = 0;

    function shuffle(array) {
        let currentIndex = array.length, randomIndex;
        while (currentIndex != 0) {
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex--;
            [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
        }
        return array;
    }

    function getEditDistance(a, b) {
        if (a.length === 0) return b.length; 
        if (b.length === 0) return a.length;
        var matrix = [];
        for (var i = 0; i <= b.length; i++) { matrix[i] = [i]; }
        for (var j = 0; j <= a.length; j++) { matrix[0][j] = j; }
        for (i = 1; i <= b.length; i++) {
            for (j = 1; j <= a.length; j++) {
                if (b.charAt(i - 1) == a.charAt(j - 1)) { matrix[i][j] = matrix[i-1][j-1]; } 
                else { matrix[i][j] = Math.min(matrix[i-1][j-1] + 1, Math.min(matrix[i][j-1] + 1, matrix[i-1][j] + 1)); }
            }
        }
        return matrix[b.length][a.length];
    }

    function speak(text) {
        const synth = window.speechSynthesis;
        const utterance = new SpeechSynthesisUtterance(text);
        const voices = synth.getVoices();
        const nativeVoice = voices.find(v => (v.lang === 'en-US' || v.lang === 'en-GB') && v.name.includes('Google')) || voices.find(v => v.lang.includes('en')) || voices[0];
        utterance.voice = nativeVoice;
        utterance.rate = 0.95;
        synth.speak(utterance);
    }

    function switchTab(idx) {
        document.querySelectorAll('.exercise-section').forEach((s, i) => s.style.display = i === idx ? 'block' : 'none');
        document.querySelectorAll('.nav-button').forEach((b, i) => b.classList.toggle('active', i === idx));
    }

    function init() {
        const list = document.getElementById('vocab-list');
        list.innerHTML = "";
        data.forEach(item => {
            list.innerHTML += `<div class="vocab-item"><div class="word-cell">${item.word}</div><button class="speaker-btn" onclick="speak('${item.word}')">🔊</button><div>${item.def}</div></div>`;
        });

        const wordList = data.map(d => d.word);
        document.getElementById('writing-bank').innerHTML = shuffle([...wordList]).map(w => `<div class="word-bank-item">${w}</div>`).join('');
        document.getElementById('pron-bank').innerHTML = shuffle([...wordList]).map(w => `<div class="word-bank-item">${w}</div>`).join('');

        const wCont = document.getElementById('writing-content');
        wCont.innerHTML = "";
        data.forEach((item, i) => {
            wCont.innerHTML += `<div class="gap-sentence">${i+1}. ${item.sent.replace('___', `<input type="text" class="gap-input" id="w-in-${i}">`)} <div id="w-fb-${i}"></div></div>`;
        });

        const mCont = document.getElementById('match-content');
        mCont.innerHTML = "";
        const mWords = shuffle(data.map((d, i) => ({ t: d.word, id: i, type: 'w' })));
        const mDefs = shuffle(data.map((d, i) => ({ t: d.def, id: i, type: 'd' })));
        [...mWords, ...mDefs].forEach(obj => {
            mCont.innerHTML += `<div class="match-box" data-id="${obj.id}" data-type="${obj.type}" onclick="handleMatch(this)">${obj.t}</div>`;
        });

        const pCont = document.getElementById('pron-content');
        pCont.innerHTML = "";
        data.forEach((item, i) => {
            pCont.innerHTML += `<div class="gap-sentence">${item.pron.replace(item.word, '_____')} <button class="mic-btn" onclick="testSpeech('${item.word}', ${i})">🎤</button> <div id="p-fb-${i}" style="color:#888; font-size: 0.9em;">Guess the word!</div></div>`;
        });
    }

    function checkWriting() {
        data.forEach((item, i) => {
            const input = document.getElementById(`w-in-${i}`);
            const fb = document.getElementById(`w-fb-${i}`);
            const val = input.value.trim().toLowerCase();
            const target = item.word.toLowerCase();
            const dist = getEditDistance(val, target);

            if (val === target) {
                fb.innerHTML = `<span class="status-green">✅ Perfect!</span>`;
                totalScore += 1;
            } else if (dist <= 2 || (target.includes(' ') && val === target.replace(' ', ''))) {
                fb.innerHTML = `<span class="status-yellow">⚠️ Close! Note: "${item.word}"</span>`;
                totalScore += 0.5;
            } else {
                fb.innerHTML = `<span class="status-red">❌ Correct: "${item.word}"</span>`;
            }
        });
        updateDisplayScore();
    }

    let firstMatch = null;
    function handleMatch(el) {
        if (el.classList.contains('correct')) return;
        if (!firstMatch) {
            firstMatch = el; el.classList.add('selected');
        } else {
            if (firstMatch.dataset.id === el.dataset.id && firstMatch.dataset.type !== el.dataset.type) {
                firstMatch.classList.replace('selected', 'correct'); el.classList.add('correct');
                totalScore += 1;
            } else {
                firstMatch.classList.remove('selected');
            }
            firstMatch = null;
            updateDisplayScore();
        }
    }

    function testSpeech(word, idx) {
        const rec = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
        rec.lang = 'en-US'; rec.start();
        document.getElementById(`p-fb-${idx}`).innerText = "Listening...";
        rec.onresult = (e) => {
            const heard = e.results[0][0].transcript.toLowerCase();
            const fb = document.getElementById(`p-fb-${idx}`);
            const target = word.toLowerCase();
            const dist = getEditDistance(heard, target);

            if (heard.includes(target)) {
                fb.innerHTML = `<span class="status-green">✅ Perfect!</span>`;
                totalScore += 1;
            } else if (dist <= 3) {
                fb.innerHTML = `<span class="status-yellow">⚠️ Almost! You said "${heard}". Correct: "${word}"</span>`;
                totalScore += 0.5;
            } else {
                fb.innerHTML = `<span class="status-red">❌ Heard: "${heard}". Try: "${word}"</span>`;
            }
            updateDisplayScore();
        };
    }

    function updateDisplayScore() {
        document.getElementById('score-val').innerText = Math.floor(totalScore);
    }

    window.onload = () => {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.onvoiceschanged = init;
            if (window.speechSynthesis.getVoices().length > 0) init();
        } else {
            init();
        }
    };
</script>
</body>
</html>
