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

        /* 🖐️ 完整卡通手掌互動區 */
        .hand-area {
            display: none;
            justify-content: center;
            align-items: flex-end;
            gap: 40px;
            min-height: 260px;
            margin: 20px 0 10px;
            flex-wrap: wrap;
            padding: 10px;
        }

        .cartoon-hand-container {
            position: relative;
            width: 200px;
            height: 230px;
            transition: transform 0.5s ease;
        }

        /* 右手鏡像 */
        .cartoon-hand-container.right-hand {
            transform: scaleX(-1);
        }
        /* 右手的數字氣泡要再反轉回來，否則數字會左右顛倒 */
        .cartoon-hand-container.right-hand .finger-bubble {
            transform: scaleX(-1);
        }

        /* ===== 完整掌心 ===== */
        .palm-main {
            position: absolute;
            bottom: 0;
            left: 25px;
            width: 150px;
            height: 115px;
            background: linear-gradient(to bottom, #ffedd5, #fed7aa);
            border: 4px solid #f97316;
            border-radius: 45px 45px 55px 55px;
            box-shadow: inset 0 -8px 0 #fdba74, 0 4px 8px rgba(0,0,0,0.1);
            z-index: 2;
            transition: all 0.45s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* 拇指根部肉墊 */
        .palm-thumb-pad {
            position: absolute;
            bottom: 15px;
            left: 8px;
            width: 55px;
            height: 65px;
            background: linear-gradient(135deg, #ffedd5, #fed7aa);
            border: 4px solid #f97316;
            border-radius: 50%;
            z-index: 1;
            transition: all 0.45s ease;
        }

        /* 合實拳頭時的掌心變化 */
        .cartoon-hand-container.fist .palm-main {
            height: 95px;
            border-radius: 50px;
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
        }
        .cartoon-hand-container.fist .palm-thumb-pad {
            transform: scale(0.85) translate(5px, 5px);
            opacity: 0.9;
        }

        /* ===== 卡通手指（永遠5根） ===== */
        .c-finger {
            position: absolute;
            background: linear-gradient(to bottom, #ffedd5, #fed7aa);
            border: 4px solid #f97316;
            border-radius: 18px 18px 12px 12px;
            cursor: pointer;
            transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
            transform-origin: bottom center;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            padding-top: 6px;
            z-index: 3;
            box-shadow: 0 3px 0 #fdba74;
        }

        /* 手指關節線（增加立體感） */
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

        /* 拇指 */
        .f-thumb {
            width: 36px;
            height: 70px;
            left: 2px;
            bottom: 35px;
            transform: rotate(-42deg);
            transform-origin: bottom right;
            border-radius: 20px 18px 14px 16px;
            z-index: 4;
        }
        /* 食指 */
        .f-index {
            width: 34px;
            height: 95px;
            left: 42px;
            bottom: 105px;
        }
        /* 中指 */
        .f-middle {
            width: 35px;
            height: 105px;
            left: 85px;
            bottom: 108px;
        }
        /* 無名指 */
        .f-ring {
            width: 33px;
            height: 92px;
            left: 128px;
            bottom: 100px;
        }
        /* 尾指 */
        .f-pinky {
            width: 29px;
            height: 75px;
            left: 168px;
            bottom: 80px;
            transform: rotate(12deg);
            transform-origin: bottom left;
        }

        /* ===== 折起手指（慢慢收起） ===== */
        .f-thumb.folded {
            height: 38px;
            transform: rotate(-55deg) translateY(18px) translateX(5px);
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
        }
        .f-index.folded {
            height: 32px;
            transform: translateY(58px) rotate(3deg);
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
        }
        .f-middle.folded {
            height: 32px;
            transform: translateY(68px) rotate(-2deg);
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
        }
        .f-ring.folded {
            height: 30px;
            transform: translateY(58px) rotate(2deg);
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
        }
        .f-pinky.folded {
            height: 28px;
            transform: rotate(18deg) translateY(42px) translateX(-3px);
            background: linear-gradient(to bottom, #fed7aa, #fdba74);
            box-shadow: none;
        }

        /* 合實拳頭時，所有手指再更收緊一點 */
        .cartoon-hand-container.fist .f-thumb.folded {
            height: 32px;
            transform: rotate(-70deg) translateY(22px) translateX(12px);
        }
        .cartoon-hand-container.fist .f-index.folded,
        .cartoon-hand-container.fist .f-middle.folded,
        .cartoon-hand-container.fist .f-ring.folded {
            height: 26px;
            transform: translateY(72px);
        }
        .cartoon-hand-container.fist .f-pinky.folded {
            height: 24px;
            transform: rotate(25deg) translateY(50px) translateX(-5px);
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

    // 根據要數的數字，顯示對應數量的手指（連同完整掌心）
    function renderCartoonHands(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';

        // 左手：最多顯示 5 根
        const leftCount = Math.min(handCount, 5);
        const leftHand = createCartoonHand(0, leftCount, false);
        container.appendChild(leftHand);

        // 如果超過 5 根，再顯示右手（鏡像）
        if (handCount > 5) {
            const rightCount = handCount - 5;
            const rightHand = createCartoonHand(5, rightCount, true);
            container.appendChild(rightHand);
        }
    }

    /**
     * 建立一隻手掌（只顯示需要的手指數量 + 完整掌心）
     * startIndex  : 這隻手在整體計數的起始偏移（0 或 5）
     * fingerCount : 這隻手要顯示幾根手指
     * isRight     : 是否為右手（需要鏡像）
     */
    function createCartoonHand(startIndex, fingerCount, isRight) {
        const handWrap = document.createElement('div');
        handWrap.className = 'cartoon-hand-container' + (isRight ? ' right-hand' : '');
        handWrap.id = isRight ? 'hand-right' : 'hand-left';

        // 完整掌心（永遠有）
        handWrap.innerHTML = `
            <div class="palm-thumb-pad"></div>
            <div class="palm-main"></div>
        `;

        const fingerClasses = ['f-thumb', 'f-index', 'f-middle', 'f-ring', 'f-pinky'];

        // 只生成需要的手指數量
        for (let i = 0; i < fingerCount; i++) {
            const globalIndex = startIndex + i + 1;          // 1~5 或 6~10
            const displayNum = currentMind + globalIndex;   // 心 + 這根手指的序號

            const finger = document.createElement('div');
            finger.className = `c-finger ${fingerClasses[i]}`;
            finger.id = `finger-${globalIndex}`;
            finger.dataset.index = globalIndex;
            finger.dataset.num = displayNum;
            finger.style.cursor = 'pointer';
            finger.onclick = () => clickFinger(globalIndex, displayNum);

            // 數字氣泡
            finger.innerHTML = `<div class="finger-bubble" id="bubble-${globalIndex}">${displayNum}</div>`;

            handWrap.appendChild(finger);
        }
        return handWrap;
    }

    function clickFinger(index, num) {
        // 必須順序點擊
        if (index !== currentClickIndex + 1) {
            speakCantonese("請順序點擊手指喔！");
            document.getElementById('hint-text').innerText = '⚠️ 請由左到右、由拇指開始順序點擊！';
            return;
        }

        currentClickIndex++;
        playDingSound();

        // 顯示數字氣泡 + 折起手指
        const bubble = document.getElementById(`bubble-${index}`);
        const finger = document.getElementById(`finger-${index}`);
        if (bubble) {
            bubble.classList.add('show');
        }
        if (finger) {
            finger.classList.add('folded');
        }

        // 讀出當前數字
        speakCantonese(num.toString());

        // 更新提示（不透露最終答案）
        document.getElementById('hint-text').innerText = 
            `已數 ${currentClickIndex} 根 → 現在是 ${num}`;

        // 如果這隻手的手指全部折完，就合實成拳頭
        checkAndMakeFist();
    }

    // 檢查是否要合實拳頭
    function checkAndMakeFist() {
        // 左手：折了這隻手全部需要的手指就合拳
        const leftNeeded = Math.min(5, totalToFold);
        if (currentClickIndex >= leftNeeded) {
            const leftHand = document.getElementById('hand-left');
            if (leftHand) leftHand.classList.add('fist');
        }
        // 右手：折了超過5根且全部完成才合拳
        if (totalToFold > 5 && currentClickIndex >= totalToFold) {
            const rightHand = document.getElementById('hand-right');
            if (rightHand) rightHand.classList.add('fist');
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
