<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>「心手口」趣味加法練習機</title>
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
        
        /* 標題與語音引導 */
        .header-area {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
            margin-bottom: 15px;
        }
        h1 {
            font-size: 32px;
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
        .btn-icon:active {
            transform: translateY(4px);
            box-shadow: none;
        }

        /* 題目區域 */
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
        .num-card {
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .tag-title {
            font-size: 20px;
            font-weight: bold;
            color: #ea580c;
        }
        .num-val {
            font-size: 55px;
            font-weight: 900;
            color: #0369a1;
            line-height: 1.1;
        }
        .operator {
            font-size: 45px;
            font-weight: bold;
            color: #2563eb;
        }
        .ans-input {
            width: 90px;
            height: 70px;
            font-size: 45px;
            text-align: center;
            border: 4px solid #3b82f6;
            border-radius: 15px;
            outline: none;
            font-weight: bold;
            color: #1d4ed8;
            background: #ffffff;
        }

        /* 手指互動區 */
        .hand-area {
            display: flex;
            justify-content: center;
            align-items: flex-end;
            gap: 20px;
            min-height: 200px;
            margin: 20px 0;
            position: relative;
        }
        .hand-group {
            display: flex;
            gap: 10px;
            background: #ffedd5;
            padding: 15px 15px 0 15px;
            border-radius: 30px 30px 10px 10px;
            border: 3px solid #fdba74;
            border-bottom: none;
        }
        .finger-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            cursor: pointer;
        }
        .num-bubble {
            width: 38px;
            height: 38px;
            background-color: #0284c7;
            color: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 20px;
            font-weight: bold;
            margin-bottom: 8px;
            visibility: hidden;
            box-shadow: 0 3px 6px rgba(0,0,0,0.2);
        }
        .finger-shape {
            width: 32px;
            height: 100px;
            background-color: #fed7aa;
            border: 3px solid #f97316;
            border-radius: 15px 15px 5px 5px;
            transition: all 0.2s ease;
        }
        .finger-shape.folded {
            height: 35px;
            background-color: #ffedd5;
            border-style: dashed;
        }

        /* 星星總結提示 */
        .star-box {
            display: none;
            background: #fef08a;
            border: 3px solid #eab308;
            padding: 8px 20px;
            border-radius: 20px;
            font-size: 22px;
            font-weight: bold;
            color: #854d0e;
            margin: 10px auto;
            width: fit-content;
            animation: bounce 0.5s infinite alternate;
        }
        @keyframes bounce {
            0% { transform: translateY(0); }
            100% { transform: translateY(-5px); }
        }

        /* 按鈕區 */
        .action-area {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            margin-top: 15px;
        }
        .btn-green {
            background-color: #22c55e;
            color: white;
            border: none;
            padding: 10px 25px;
            font-size: 22px;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 5px 0 #15803d;
        }
        .btn-green:active {
            transform: translateY(5px);
            box-shadow: none;
        }
        .btn-blue {
            background-color: #3b82f6;
            color: white;
            border: none;
            padding: 10px 25px;
            font-size: 22px;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 5px 0 #1d4ed8;
            display: none;
        }
        .btn-blue:active {
            transform: translateY(5px);
            box-shadow: none;
        }
    </style>
</head>
<body>

<div class="container">
    <!-- 標題與廣東話嘴巴引導按鈕 -->
    <div class="header-area">
        <button class="btn-icon" onclick="playMouthSpeech()" title="讀出題目">🗣️</button>
        <h1>「心手口」趣味加法練習機</h1>
    </div>

    <!-- 題目區域 -->
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
        
        <!-- 小提示燈泡按鈕 -->
        <button class="btn-icon" style="position: absolute; right: -15px; bottom: -15px;" onclick="playBulbSpeech()" title="小提示">💡</button>
    </div>

    <!-- 手指互動區 -->
    <div class="hand-area" id="hand-area">
        <!-- 由 JavaScript 動態生成手指 -->
    </div>

    <!-- 星星總結提示 -->
    <div class="star-box" id="star-box">
        ⭐ 最後數到的數是 <span id="star-num">9</span>！
    </div>

    <!-- 操作與下一題按鈕 -->
    <div class="action-area">
        <button class="btn-green" onclick="checkAnswer()">✔️ 核對答案</button>
        <button class="btn-blue" id="next-btn" onclick="nextQuestion()">➡️ 下一題</button>
    </div>
</div>

<script>
    // 全局變量
    let currentMind = 4;
    let currentHand = 5;
    let targetAnswer = 9;
    let currentClickIndex = 0;

    // 音效合成 (叮一聲)
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

    // 廣東話語音朗讀 (zh-HK)
    function speakCantonese(text, callback) {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.cancel();
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'zh-HK'; // 設定廣東話
            utterance.rate = 0.85;    // 適合小學生的放慢語速
            utterance.pitch = 1.1;
            if (callback) {
                utterance.onend = callback;
            }
            window.speechSynthesis.speak(utterance);
        }
    }

    // 初始化題目
    function initQuestion(mind, hand) {
        currentMind = mind;
        currentHand = hand;
        targetAnswer = mind + hand;
        currentClickIndex = 0;

        document.getElementById('num-mind').innerText = currentMind;
        document.getElementById('num-hand').innerText = currentHand;
        document.getElementById('user-ans').value = '';
        document.getElementById('star-box').style.display = 'none';
        document.getElementById('next-btn').style.display = 'none';

        renderFingers(currentHand);

        // 自動播放老師引導語音
        let countSeq = [];
        for (let i = 1; i <= currentHand; i++) {
            countSeq.push(currentMind + i);
        }
        let guideSpeech = `依家用滑鼠，由大拇指開始，一隻一隻手指收埋佢，跟住老師一齊數：${countSeq.join('、')}。`;
        speakCantonese(guideSpeech);
    }

    // 動態渲染手指 (支援單手與雙手)
    function renderFingers(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';

        let totalFingers = handCount;
        let leftHandCount = totalFingers > 5 ? 5 : totalFingers;
        let rightHandCount = totalFingers > 5 ? totalFingers - 5 : 0;

        // 生成左手/單手
        container.appendChild(createHandGroup(leftHandCount, 0));

        // 如果超過 5 隻，生成右手
        if (rightHandCount > 0) {
            container.appendChild(createHandGroup(rightHandCount, 5));
        }
    }

    function createHandGroup(count, startIndex) {
        const group = document.createElement('div');
        group.className = 'hand-group';

        for (let i = 1; i <= count; i++) {
            let globalIndex = startIndex + i;
            let displayNum = currentMind + globalIndex;

            const fingerItem = document.createElement('div');
            fingerItem.className = 'finger-item';
            fingerItem.onclick = () => clickFinger(globalIndex, displayNum);

            fingerItem.innerHTML = `
                <div class="num-bubble" id="bubble-${globalIndex}">${displayNum}</div>
                <div class="finger-shape" id="finger-${globalIndex}"></div>
            `;
            group.appendChild(fingerItem);
        }
        return group;
    }

    // 手指點擊互動
    function clickFinger(index, num) {
        if (index !== currentClickIndex + 1) {
            speakCantonese("請照順序收埋手指喔！");
            return;
        }

        currentClickIndex++;
        playDingSound();

        // 顯示數字氣泡與收起手指
        document.getElementById(`bubble-${index}`).style.visibility = 'visible';
        document.getElementById(`finger-${index}`).classList.add('folded');

        // 讀出對應順數數字
        speakCantonese(num.toString());

        // 當所有手指收起時
        if (currentClickIndex === currentHand) {
            document.getElementById('star-num').innerText = targetAnswer;
            document.getElementById('star-box').style.display = 'block';

            setTimeout(() => {
                let summarySpeech = `哇！我哋數到 ${targetAnswer} 啦！所以心裏面嘅 ${currentMind} 加埋手指嘅 ${currentHand}，答案就係 ${targetAnswer} 喇！`;
                speakCantonese(summarySpeech);
            }, 800);
        }
    }

    // 按鈕功能：左上角嘴巴
    function playMouthSpeech() {
        speakCantonese(`${currentMind} 加 ${currentHand} 等於幾多呀？`);
    }

    // 按鈕功能：右下角小燈泡提示
    function playBulbSpeech() {
        speakCantonese(`心裏面記住 ${currentMind}，加上手指 ${currentHand}，等於幾多呢？`);
    }

    // 核對答案
    function checkAnswer() {
        const userVal = parseInt(document.getElementById('user-ans').value);
        if (isNaN(userVal)) {
            speakCantonese("請先在空格填寫答案喔！");
            return;
        }

        if (userVal === targetAnswer) {
            // 彩帶特效
            confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
            document.getElementById('next-btn').style.display = 'inline-block';
            speakCantonese("答對咗啦！你真係棒！");
        } else {
            speakCantonese("再數一次手指試試看！");
        }
    }

    // 隨機生成下一題 (18以內加數)
    function nextQuestion() {
        let nextMind = Math.floor(Math.random() * 9) + 1; // 1~9
        let maxHand = 18 - nextMind;
        let handLimit = Math.min(9, maxHand);
        let nextHand = Math.floor(Math.random() * handLimit) + 1; // 1~9

        initQuestion(nextMind, nextHand);
    }

    // 頁面載入後自動開啟第一題 (預設 4 + 5)
    window.onload = function() {
        initQuestion(4, 5);
    };
</script>

</body>
</html>
