<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>「心手口」18以內加數練習機</title>
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
        .equation-box {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            background-color: #fef9c3;
            border: 5px solid #fde047;
            border-radius: 25px;
            padding: 20px;
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
            width: 100px;
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
        .hand-area {
            display: none;
            justify-content: center;
            align-items: flex-end;
            gap: 20px;
            min-height: 180px;
            margin: 20px 0;
            position: relative;
        }
        .hand-group {
            display: flex;
            gap: 12px;
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
            width: 40px;
            height: 40px;
            background-color: #0284c7;
            color: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 22px;
            font-weight: bold;
            margin-bottom: 8px;
            visibility: hidden;
            box-shadow: 0 3px 6px rgba(0,0,0,0.2);
        }
        .finger-shape {
            width: 34px;
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
        .action-area {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 15px;
            margin-top: 20px;
        }
        .btn-green {
            background-color: #22c55e;
            color: white;
            border: none;
            padding: 12px 30px;
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
            padding: 12px 30px;
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
        /* small responsive adjustments */
        @media (max-width: 520px) {
            .num-val { font-size: 40px; }
            .operator { font-size: 34px; }
            .ans-input { width: 80px; height: 56px; font-size: 36px; }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header-area">
        <button class="btn-icon" id="mouth-btn" onclick="playMouthSpeech()" title="讀出題目" aria-label="讀出題目">🗣️</button>
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
            <input type="number" class="ans-input" id="user-ans" placeholder="?" min="0" max="18" step="1" aria-label="答案輸入" />
        </div>

        <button class="btn-icon" id="bulb-btn" style="position: absolute; right: -15px; bottom: -15px;" onclick="playBulbSpeech()" title="點擊獲得提示" aria-label="顯示提示">💡</button>
    </div>

    <div class="hand-area" id="hand-area" aria-hidden="true" aria-label="手指區">
        <!-- 由 JavaScript 動態生成手指 -->
    </div>

    <div class="action-area">
        <button class="btn-green" id="submit-btn" onclick="checkAnswer()" aria-label="提交答案">提交答案</button>
        <button class="btn-blue" id="next-btn" onclick="nextQuestion()" aria-label="下一題">➡️ 下一題</button>
    </div>

    <!-- Live region for screen readers -->
    <div id="sr-live" style="position: absolute; left: -9999px; width:1px; height:1px; overflow:hidden;" aria-live="polite"></div>
</div>

<script>
    // 全局變量
    let currentMind = 4;
    let currentHand = 5;
    let targetAnswer = 9;
    let currentClickIndex = 0;

    // Reusable AudioContext to avoid creating many contexts
    let audioCtx = null;

    function getAudioContext() {
        if (!audioCtx) {
            try {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            } catch (e) {
                audioCtx = null;
            }
        }
        return audioCtx;
    }

    // 叮一聲
    function playDingSound() {
        const ctx = getAudioContext();
        if (!ctx) return; // some browsers block audio without gesture
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = 'sine';
        osc.frequency.setValueAtTime(800, ctx.currentTime);
        gain.gain.setValueAtTime(0.3, ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.25);
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start();
        osc.stop(ctx.currentTime + 0.25);
    }

    // 尋找合適的粵語語音（有些瀏覽器沒有）
    let selectedCantoneseVoice = null;
    function pickCantoneseVoice() {
        const voices = window.speechSynthesis.getVoices() || [];
        if (!voices.length) return null;
        // prefer voices that contain 'yue' / 'Cantonese' / 'Hong Kong' / 'HongKong' / 'HK'
        const prefer = voices.find(v => /yue|cantonese|hong ?kong|hk/i.test(v.name + ' ' + v.lang));
        return prefer || voices.find(v => /zh/i.test(v.lang)) || voices[0];
    }
    // ensure voices loaded
    window.speechSynthesis.onvoiceschanged = () => {
        selectedCantoneseVoice = pickCantoneseVoice();
    };

    function speakCantonese(text, callback) {
        if (!('speechSynthesis' in window)) {
            // fallback: update sr-live for screen readers
            const sr = document.getElementById('sr-live');
            sr.textContent = text;
            if (callback) callback();
            return;
        }
        window.speechSynthesis.cancel();
        const utterance = new SpeechSynthesisUtterance(text);
        // prefer selected voice if available
        if (selectedCantoneseVoice) {
            utterance.voice = selectedCantoneseVoice;
        }
        // set a reasonable lang; many browsers accept 'zh-HK' or 'yue-HK'
        utterance.lang = selectedCantoneseVoice ? selectedCantoneseVoice.lang || 'zh-HK' : 'zh-HK';
        utterance.rate = 0.85;
        utterance.pitch = 1.05;
        if (callback) utterance.onend = callback;
        window.speechSynthesis.speak(utterance);
        // also update sr-live (useful when voices not present)
        document.getElementById('sr-live').textContent = text;
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
        document.getElementById('hand-area').style.display = 'none';
        document.getElementById('hand-area').setAttribute('aria-hidden', 'true');
        document.getElementById('next-btn').style.display = 'none';

        renderFingers(currentHand);
    }

    // 動態渲染手指 (支援單手 1-5 與雙手 6-9)
    function renderFingers(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';

        let totalFingers = handCount;
        let leftHandCount = totalFingers > 5 ? 5 : totalFingers;
        let rightHandCount = totalFingers > 5 ? totalFingers - 5 : 0;

        // 生成第一隻手掌
        container.appendChild(createHandGroup(leftHandCount, 0));

        // 如果超過 5 隻手指，生成第二隻手掌
        if (rightHandCount > 0) {
            container.appendChild(createHandGroup(rightHandCount, 5));
        }
    }

    function createHandGroup(count, startIndex) {
        const group = document.createElement('div');
        group.className = 'hand-group';

        for (let i = 1; i <= count; i++) {
            let globalIndex = startIndex + i; // 1..9
            let displayNum = currentMind + globalIndex; // show currentMind + 1..currentMind+handCount

            const fingerItem = document.createElement('div');
            fingerItem.className = 'finger-item';
            fingerItem.tabIndex = 0;
            fingerItem.setAttribute('role', 'button');
            fingerItem.setAttribute('aria-label', `手指 ${globalIndex} 數字 ${displayNum}`);
            fingerItem.onclick = () => clickFinger(globalIndex, displayNum);
            fingerItem.onkeydown = (e) => {
                if (e.key === 'Enter' || e.key === ' ') {
                    e.preventDefault();
                    clickFinger(globalIndex, displayNum);
                }
            };

            fingerItem.innerHTML = `
                <div class="num-bubble" id="bubble-${globalIndex}">${displayNum}</div>
                <div class="finger-shape" id="finger-${globalIndex}"></div>
            `;
            group.appendChild(fingerItem);
        }
        return group;
    }

    // 手指點擊互動與順數
    function clickFinger(index, num) {
        if (index !== currentClickIndex + 1) {
            speakCantonese("請順序點擊手指喔！");
            return;
        }

        currentClickIndex++;
        playDingSound();

        const bubble = document.getElementById(`bubble-${index}`);
        const finger = document.getElementById(`finger-${index}`);
        if (bubble) bubble.style.visibility = 'visible';
        if (finger) finger.classList.add('folded');

        speakCantonese(num.toString());
    }

    // 左上角嘴巴按鈕
    function playMouthSpeech() {
        speakCantonese(`${currentMind} 加 ${currentHand} 等於幾多呀？`);
    }

    // 顯示手掌並播放提示 (點燈泡)
    function playBulbSpeech() {
        // reset finger interaction for this question so student can try again
        currentClickIndex = 0;
        renderFingers(currentHand);

        const handArea = document.getElementById('hand-area');
        handArea.style.display = 'flex';
        handArea.setAttribute('aria-hidden', 'false');

        speakCantonese(`心裏面記住 ${currentMind}，順數加上手指 ${currentHand}，等於幾多呢？`);
    }

    // 提交答案與核對
    function checkAnswer() {
        const input = document.getElementById('user-ans');
        const raw = input.value;
        const userVal = parseInt(raw, 10);
        if (isNaN(userVal)) {
            speakCantonese("請先填寫答案喔！");
            return;
        }

        if (userVal === targetAnswer) {
            confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
            document.getElementById('next-btn').style.display = 'inline-block';
            speakCantonese("你答對咗啦！");
            // announce for screen readers
            document.getElementById('sr-live').textContent = '答對了！按下一題';
        } else {
            speakCantonese("再數一次手指試試看！");
            document.getElementById('sr-live').textContent = '答案錯誤，請再試一次';
        }
    }

    // Enter 鍵提交
    document.getElementById('user-ans').addEventListener('keydown', (e) => {
        if (e.key === 'Enter') {
            e.preventDefault();
            checkAnswer();
        }
    });

    // 隨機生成下一題 (18 以內加數)
    function nextQuestion() {
        // 1..9 for mind
        let nextMind = Math.floor(Math.random() * 9) + 1;
        let maxHand = 18 - nextMind;
        let handLimit = Math.min(9, maxHand);
        // ensure handLimit at least 1
        handLimit = Math.max(1, handLimit);
        let nextHand = Math.floor(Math.random() * handLimit) + 1; // 1..handLimit

        initQuestion(nextMind, nextHand);
    }

    // 頁面載入後預設題目
    window.onload = function() {
        // try to prefetch voices
        selectedCantoneseVoice = pickCantoneseVoice();
        initQuestion(4, 5);
        // resume audio context upon first user gesture, if needed
        const resumeAudio = () => {
            const ctx = getAudioContext();
            if (ctx && ctx.state === 'suspended') ctx.resume();
            // remove handlers once resumed
            window.removeEventListener('click', resumeAudio);
            window.removeEventListener('keydown', resumeAudio);
        };
        window.addEventListener('click', resumeAudio);
        window.addEventListener('keydown', resumeAudio);
    };
</script>
</body>
</html>
