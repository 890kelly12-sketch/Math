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
            max-width: 850px;
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

        /* 🖐️ 完整卡通手掌互動區 */
        .hand-area {
            display: none; /* 按燈泡時展開 */
            justify-content: center;
            align-items: center;
            gap: 30px;
            min-height: 240px;
            margin: 15px 0;
            flex-wrap: wrap;
        }
        .cartoon-hand-container {
            position: relative;
            width: 220px;
            height: 210px;
        }
        /* 卡通掌心 */
        .palm-main {
            position: absolute;
            bottom: 0;
            left: 35px;
            width: 150px;
            height: 120px;
            background: #ffedd5;
            border: 4px solid #f97316;
            border-radius: 40px 40px 50px 50px;
            box-shadow: inset 0 -6px 0 #fdba74;
            z-index: 2;
        }
        /* 拇指肉墊裝飾 */
        .palm-thumb-pad {
            position: absolute;
            bottom: 10px;
            left: 20px;
            width: 50px;
            height: 60px;
            background: #ffedd5;
            border: 4px solid #f97316;
            border-radius: 50%;
            z-index: 1;
        }
        /* 卡通手指 */
        .c-finger {
            position: absolute;
            background: #ffedd5;
            border: 4px solid #f97316;
            border-radius: 20px 20px 10px 10px;
            cursor: pointer;
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
            transform-origin: bottom center;
            display: flex;
            justify-content: center;
            align-items: top;
            padding-top: 5px;
            z-index: 3;
        }
        /* 拇指 */
        .f-thumb  { width: 34px; height: 65px; left: 5px; bottom: 40px; transform: rotate(-40deg); transform-origin: bottom right; }
        /* 食指 */
        .f-index  { width: 32px; height: 85px; left: 50px; bottom: 100px; }
        /* 中指 */
        .f-middle { width: 32px; height: 95px; left: 94px; bottom: 100px; }
        /* 無名指 */
        .f-ring   { width: 32px; height: 85px; left: 138px; bottom: 100px; }
        /* 尾指 */
        .f-pinky  { width: 28px; height: 70px; left: 180px; bottom: 75px; transform: rotate(15deg); transform-origin: bottom left; }

        /* 縮手指 (折起) 狀態 */
        .f-thumb.folded  { height: 30px; transform: rotate(-40deg) translateY(25px); background: #fed7aa; border-style: dashed; }
        .f-index.folded  { height: 30px; transform: translateY(55px); background: #fed7aa; border-style: dashed; }
        .f-middle.folded { height: 30px; transform: translateY(65px); background: #fed7aa; border-style: dashed; }
        .f-ring.folded   { height: 30px; transform: translateY(55px); background: #fed7aa; border-style: dashed; }
        .f-pinky.folded  { height: 25px; transform: rotate(15deg) translateY(45px); background: #fed7aa; border-style: dashed; }

        /* 數字氣泡 */
        .finger-bubble {
            width: 32px;
            height: 32px;
            background: #0284c7;
            color: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 18px;
            font-weight: bold;
            visibility: hidden;
            box-shadow: 0 3px 6px rgba(0,0,0,0.2);
            position: absolute;
            top: -38px;
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

    <div class="action-area">
        <button class="btn-green" onclick="checkAnswer()">提交答案</button>
        <button class="btn-blue" id="next-btn" onclick="nextQuestion()">➡️ 下一題</button>
    </div>
</div>

<script>
    let currentMind = 4;
    let currentHand = 5;
    let targetAnswer = 9;
    let currentClickIndex = 0;

    function playDingSound() {
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(800, ctx.currentTime);
        gain.gain.setValueAtTime(0.3, ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.3);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start();
        osc.stop(ctx.currentTime + 0.3);
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

        document.getElementById('num-mind').innerText = currentMind;
        document.getElementById('num-hand').innerText = currentHand;
        document.getElementById('user-ans').value = '';
        document.getElementById('hand-area').style.display = 'none';
        document.getElementById('next-btn').style.display = 'none';

        renderCartoonHands(currentHand);
    }

    // 動態生成卡通手掌（支援單手 1~5 隻與雙手 6~9 隻）
    function renderCartoonHands(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';

        let leftCount = handCount > 5 ? 5 : handCount;
        let rightCount = handCount > 5 ? handCount - 5 : 0;

        container.appendChild(createSingleCartoonHand(leftCount, 0));

        if (rightCount > 0) {
            container.appendChild(createSingleCartoonHand(rightCount, 5));
        }
    }

    function createSingleCartoonHand(fingerCount, startIndex) {
        const handWrap = document.createElement('div');
        handWrap.className = 'cartoon-hand-container';

        // 生成掌心
        handWrap.innerHTML = `
            <div class="palm-thumb-pad"></div>
            <div class="palm-main"></div>
        `;

        const fingerClasses = ['f-thumb', 'f-index', 'f-middle', 'f-ring', 'f-pinky'];

        for (let i = 0; i < fingerCount; i++) {
            let globalIndex = startIndex + i + 1;
            let displayNum = currentMind + globalIndex;

            const finger = document.createElement('div');
            finger.className = `c-finger ${fingerClasses[i]}`;
            finger.id = `finger-${globalIndex}`;
            finger.onclick = () => clickFinger(globalIndex, displayNum);

            finger.innerHTML = `<div class="finger-bubble" id="bubble-${globalIndex}">${displayNum}</div>`;
            handWrap.appendChild(finger);
        }
        return handWrap;
    }

    function clickFinger(index, num) {
        if (index !== currentClickIndex + 1) {
            speakCantonese("請順序點擊手指喔！");
            return;
        }

        currentClickIndex++;
        playDingSound();

        // 顯示數字氣泡並觸發縮手指（折起）動畫
        document.getElementById(`bubble-${index}`).style.visibility = 'visible';
        document.getElementById(`finger-${index}`).classList.add('folded');

        speakCantonese(num.toString());
    }

    function playMouthSpeech() {
        speakCantonese(`${currentMind} 加 ${currentHand} 等於幾多呀？`);
    }

    function playBulbSpeech() {
        document.getElementById('hand-area').style.display = 'flex';
        speakCantonese(`心裏面記住 ${currentMind}，順數加上手指 ${currentHand}，等於幾多呢？`);
    }

    function checkAnswer() {
        const userVal = parseInt(document.getElementById('user-ans').value);
        if (isNaN(userVal)) {
            speakCantonese("請先填寫答案喔！");
            return;
        }

        if (userVal === targetAnswer) {
            confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
            document.getElementById('next-btn').style.display = 'inline-block';
            speakCantonese("你答對咗啦！");
        } else {
            speakCantonese("再數一次手指試試看！");
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
