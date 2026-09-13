<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>「心手口」18以內加數練習機（卡通手掌版）</title>
    <!-- Canvas Confetti 答對特效庫 -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            font-family: "PingFang HK", "Chalkboard SE", "Comic Sans MS", "微軟正黑體", sans-serif;
            user-select: none;
        }
        body {
            background: linear-gradient(to bottom, #e0f2fe, #dcfce7);
            margin: 0;
            padding: 15px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .container {
            background: #ffffff;
            border-radius: 30px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.15);
            max-width: 900px;
            width: 100%;
            text-align: center;
            border: 6px solid #86efac;
            position: relative;
        }
        
        /* 頂部標題 */
        .header-area {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
            margin-bottom: 15px;
        }
        h1 {
            font-size: 28px;
            margin: 0;
            background: linear-gradient(45deg, #0284c7, #f97316);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .btn-icon {
            background: #fef08a;
            border: 3px solid #eab308;
            border-radius: 50%;
            width: 55px;
            height: 55px;
            font-size: 26px;
            cursor: pointer;
            box-shadow: 0 4px 0 #ca8a04;
            transition: transform 0.1s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .btn-icon:active { transform: translateY(4px); box-shadow: none; }

        /* 算式區域 */
        .equation-box {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            background-color: #fef9c3;
            border: 5px solid #fde047;
            border-radius: 25px;
            padding: 15px;
            margin-bottom: 15px;
            position: relative;
        }
        .num-card { display: flex; flex-direction: column; align-items: center; }
        .tag-title { font-size: 18px; font-weight: bold; color: #ea580c; }
        .num-val { font-size: 50px; font-weight: 900; color: #0369a1; line-height: 1.1; }
        .operator { font-size: 40px; font-weight: bold; color: #2563eb; }
        .ans-input {
            width: 90px;
            height: 65px;
            font-size: 40px;
            text-align: center;
            border: 4px solid #3b82f6;
            border-radius: 15px;
            outline: none;
            font-weight: bold;
            color: #1d4ed8;
            background: #ffffff;
        }

        /* 🖐️ 一排卡通手指（不分左右手） */
        .hand-area {
            display: none;
            justify-content: center;
            align-items: flex-end;
            min-height: 220px;
            margin: 16px 0 8px;
            padding: 10px 6px 0;
        }

        .finger-row {
            display: flex;
            align-items: flex-end;
            justify-content: center;
            gap: 8px;
            flex-wrap: wrap;
            background: linear-gradient(to bottom, #ffedd5, #fed7aa);
            border: 4px solid #f97316;
            border-radius: 28px 28px 36px 36px;
            padding: 18px 16px 22px;
            box-shadow: inset 0 -8px 0 #fdba74, 0 4px 10px rgba(0,0,0,0.08);
            max-width: 100%;
        }

        .c-finger {
            position: relative;
            width: 36px;
            height: 110px;
            background: linear-gradient(to bottom, #ffedd5, #fed7aa);
            border: 4px solid #f97316;
            border-radius: 18px 18px 12px 12px;
            cursor: pointer;
            transition: height 0.35s ease, transform 0.35s ease, background 0.35s ease;
            transform-origin: bottom center;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding-top: 6px;
            box-shadow: 0 3px 0 #fdba74;
            flex-shrink: 0;
        }
        .c-finger:nth-child(even) { height: 118px; }
        .c-finger:nth-child(3n) { height: 104px; }

        .c-finger::before {
            content: '';
            position: absolute;
            top: 35%;
            left: 15%;
            right: 15%;
            height: 3px;
            background: rgba(249, 115, 22, 0.25);
            border-radius: 2px;
        }
        .c-finger::after {
            content: '';
            position: absolute;
            top: 60%;
            left: 20%;
            right: 20%;
            height: 2px;
            background: rgba(249, 115, 22, 0.2);
            border-radius: 2px;
        }

        /* 點擊後慢慢收起 */
        .c-finger.folded {
            height: 34px;
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
            cursor: default;
        }

        /* 數字氣泡 */
        .finger-bubble {
            width: 34px;
            height: 34px;
            background: #0284c7;
            color: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 17px;
            font-weight: bold;
            visibility: hidden;
            opacity: 0;
            box-shadow: 0 3px 8px rgba(0,0,0,0.25);
            position: absolute;
            top: -42px;
            transition: opacity 0.25s ease, transform 0.25s ease;
            z-index: 10;
        }
        .finger-bubble.show {
            visibility: visible;
            opacity: 1;
            transform: scale(1.1);
        }

        /* 提示文字 */
        .hint-text {
            font-size: 16px;
            color: #0369a1;
            margin-top: 5px;
            font-weight: 600;
            min-height: 24px;
        }

        /* 控制按鈕 */
        .action-area {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 15px;
        }
        .btn-green {
            background-color: #22c55e;
            color: white;
            border: none;
            padding: 10px 28px;
            font-size: 20px;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px 0 #15803d;
        }
        .btn-green:active { transform: translateY(4px); box-shadow: none; }
        .btn-blue {
            background-color: #3b82f6;
            color: white;
            border: none;
            padding: 10px 28px;
            font-size: 20px;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px 0 #1d4ed8;
            display: none;
        }
        .btn-blue:active { transform: translateY(4px); box-shadow: none; }
    </style>
</head>
<body>

<div class="container">
    <div class="header-area">
        <button class="btn-icon" onclick="playMouthSpeech()" title="讀出題目">🗣️</button>
        <h1>「心手口」18以內加數練習機</h1>
    </div>

    <div class="equation-box">
        <div class="num-card">
            <div class="tag-title">❤️ 心</div>
            <div class="num-val" id="num-mind">4</div>
        </div>
        <div class="operator">＋</div>
        <div class="num-card">
            <div class="tag-title">✋ 手</div>
            <div class="num-val" id="num-hand">5</div>
        </div>
        <div class="operator">＝</div>
        <div class="num-card">
            <div class="tag-title">答案</div>
            <input type="number" class="ans-input" id="user-ans" placeholder="?" />
        </div>
        
        <button class="btn-icon" style="position: absolute; right: -15px; bottom: -15px;" onclick="playBulbSpeech()" title="點擊獲得提示">💡</button>
    </div>

    <!-- 🖐️ 完整卡通手掌區域 -->
    <div class="hand-area" id="hand-area"></div>
    <div class="hint-text" id="hint-text"></div>

    <div class="action-area">
        <button class="btn-green" onclick="checkAnswer()">提交答案</button>
        <button class="btn-blue" id="next-btn" onclick="nextQuestion()">➡️ 下一題</button>
    </div>
</div>

<script>
    let currentMind = 4;
    let currentHand = 5;
    let targetAnswer = 9;
    let currentClickIndex = 0;   // 已經成功點了幾根
    let totalToFold = 0;         // 這題要折幾根手指

    function playDingSound() {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(880, ctx.currentTime);
        gain.gain.setValueAtTime(0.25, ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.25);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start();
        osc.stop(ctx.currentTime + 0.25);
    }

    function speakCantonese(text) {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.cancel();
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'zh-HK';
            utterance.rate = 0.85;
            utterance.pitch = 1.1;
            window.speechSynthesis.speak(utterance);
        }
    }

    function initQuestion(mind, hand) {
        currentMind = mind;
        currentHand = hand;
        targetAnswer = mind + hand;
        currentClickIndex = 0;
        totalToFold = hand;

        document.getElementById('num-mind').innerText = currentMind;
        document.getElementById('num-hand').innerText = currentHand;
        document.getElementById('user-ans').value = '';
        document.getElementById('hand-area').style.display = 'none';
        document.getElementById('next-btn').style.display = 'none';
        document.getElementById('hint-text').innerText = '';

        renderCartoonHands(currentHand);
    }

    // 根據「手」的數字，顯示一排手指（+9 就 9 根，不分左右手）
    function renderCartoonHands(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';

        const row = document.createElement('div');
        row.className = 'finger-row';

        for (let i = 1; i <= handCount; i++) {
            const displayNum = currentMind + i;
            const finger = document.createElement('div');
            finger.className = 'c-finger';
            finger.id = `finger-${i}`;
            finger.onclick = () => clickFinger(i, displayNum);
            finger.innerHTML = `<div class="finger-bubble" id="bubble-${i}">${displayNum}</div>`;
            row.appendChild(finger);
        }

        container.appendChild(row);
    }

    function clickFinger(index, num) {
        // 由左到右順序點擊即可（不必由拇指開始）
        if (index !== currentClickIndex + 1) {
            speakCantonese("請由左到右順序點擊手指喔！");
            document.getElementById('hint-text').innerText = '⚠️ 請由左到右順序點擊手指';
            return;
        }

        currentClickIndex++;
        playDingSound();

        const bubble = document.getElementById(`bubble-${index}`);
        const finger = document.getElementById(`finger-${index}`);
        if (bubble) bubble.classList.add('show');
        if (finger) finger.classList.add('folded');

        speakCantonese(num.toString());

        if (currentClickIndex < totalToFold) {
            document.getElementById('hint-text').innerText = `已數 ${currentClickIndex} 根，請繼續點下一根手指`;
        } else {
            document.getElementById('hint-text').innerText = '請看看最後一根手指上的數字，然後把答案填到上面空格裡';
        }
    }

    function playMouthSpeech() {
        speakCantonese(`${currentMind} 加 ${currentHand} 等於幾多呀？`);
    }

    function playBulbSpeech() {
        document.getElementById('hand-area').style.display = 'flex';
        document.getElementById('hint-text').innerText = 
            '👉 心裏面記住數字，然後順序點擊手指往上數！';
        speakCantonese(`心裏面記住 ${currentMind}，順數加上手指 ${currentHand}，等於幾多呢？`);
    }

    function checkAnswer() {
        const userVal = parseInt(document.getElementById('user-ans').value);
        if (isNaN(userVal)) {
            speakCantonese("請先填寫答案喔！");
            return;
        }

        if (userVal === targetAnswer) {
            confetti({ particleCount: 120, spread: 70, origin: { y: 0.6 } });
            document.getElementById('next-btn').style.display = 'inline-block';
            speakCantonese("你答對咗啦！好叻呀！");
            document.getElementById('hint-text').innerText = '✅ 答對啦！好叻！';
        } else {
            speakCantonese("再數一次手指試試看！");
            document.getElementById('hint-text').innerText = '再數一次手指試試看～';
        }
    }

    function nextQuestion() {
        let nextMind = Math.floor(Math.random() * 9) + 1;
        let maxHand = 18 - nextMind;
        let handLimit = Math.min(9, maxHand);
        let nextHand = Math.floor(Math.random() * handLimit) + 1;

        initQuestion(nextMind, nextHand);
    }

    window.onload = function() {
        initQuestion(4, 5);
    };
</script>

</body>
</html>
