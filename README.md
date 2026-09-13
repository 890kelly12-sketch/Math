<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>「心手口」18以內加數練習機（卡通手掌）</title>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        * { box-sizing: border-box; font-family: "PingFang HK","Chalkboard SE","Comic Sans MS","微軟正黑體",sans-serif; user-select: none; }
        body { background: linear-gradient(to bottom,#e0f2fe,#dcfce7); margin:0; padding:15px; display:flex; justify-content:center; align-items:center; min-height:100vh; }
        .container { background:#fff; border-radius:30px; padding:20px; box-shadow:0 10px 30px rgba(0,0,0,0.15); max-width:850px; width:100%; text-align:center; border:6px solid #86efac; position:relative; }

        .header-area { display:flex; align-items:center; justify-content:center; gap:15px; margin-bottom:15px; }
        h1 { font-size:32px; margin:0; background:linear-gradient(45deg,#0284c7,#f97316); -webkit-background-clip:text; -webkit-text-fill-color:transparent; }
        .btn-icon { background:#fef08a; border:3px solid #eab308; border-radius:50%; width:55px; height:55px; font-size:26px; cursor:pointer; box-shadow:0 4px 0 #ca8a04; display:flex; align-items:center; justify-content:center; transition:transform 0.1s; }
        .btn-icon:active { transform:translateY(4px); box-shadow:none; }

        .equation-box { display:flex; justify-content:center; align-items:center; gap:15px; background-color:#fef9c3; border:5px solid #fde047; border-radius:25px; padding:20px; margin-bottom:15px; position:relative; }
        .num-card { display:flex; flex-direction:column; align-items:center; }
        .tag-title { font-size:20px; font-weight:bold; color:#ea580c; }
        .num-val { font-size:55px; font-weight:900; color:#0369a1; line-height:1.1; }
        .operator { font-size:45px; font-weight:bold; color:#2563eb; }
        .ans-input { width:100px; height:70px; font-size:45px; text-align:center; border:4px solid #3b82f6; border-radius:15px; outline:none; font-weight:bold; color:#1d4ed8; background:#fff; }

        /* 手掌互動區 */
        .hand-area { display:none; justify-content:center; align-items:flex-end; gap:24px; min-height:200px; margin:20px 0; position:relative; }
        .palm { width:240px; height:170px; background:transparent; border-radius:28px; padding:6px; display:flex; flex-direction:column; align-items:center; justify-content:flex-end; position:relative; }
        .palm-label { position:absolute; top:6px; left:10px; font-weight:700; color:#b45309; background:rgba(255,255,255,0.6); padding:3px 8px; border-radius:12px; font-size:14px; }
        .palm-base { position:absolute; bottom:-10px; width:160px; height:40px; background:radial-gradient(ellipse at center, rgba(0,0,0,0.09), rgba(0,0,0,0.02)); border-radius:50%; z-index:0; }

        /* SVG finger styles */
        .svg-wrapper { width:100%; height:100%; display:block; }
        .svg-finger { cursor:pointer; transition:all 0.18s ease; transform-origin: center bottom; }
        .svg-finger .finger-shape { fill:#fed7aa; stroke:#f97316; stroke-width:3; }
        .svg-finger.folded .finger-shape { fill:#ffedd5; stroke-dasharray:3 3; }
        .bubble { fill:#0284c7; stroke:none; }
        .bubble-text { fill:white; font-weight:700; font-size:16px; dominant-baseline:middle; text-anchor:middle; pointer-events:none; }

        .btn-green { background-color:#22c55e; color:white; border:none; padding:12px 30px; font-size:22px; font-weight:bold; border-radius:50px; cursor:pointer; box-shadow:0 5px 0 #15803d; }
        .btn-green:active { transform:translateY(5px); box-shadow:none; }
        .btn-blue { background-color:#3b82f6; color:white; border:none; padding:12px 30px; font-size:22px; font-weight:bold; border-radius:50px; cursor:pointer; box-shadow:0 5px 0 #1d4ed8; display:none; }
        .btn-blue:active { transform:translateY(5px); box-shadow:none; }

        @media (max-width:640px) {
            .hand-area { gap:12px; flex-direction:column; align-items:center; }
            .palm { width:92%; }
        }
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

        <button class="btn-icon" style="position:absolute; right:-15px; bottom:-15px;" onclick="playBulbSpeech()" title="點擊獲得提示">💡</button>
    </div>

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
        osc.connect(gain); gain.connect(ctx.destination); osc.start(); osc.stop(ctx.currentTime + 0.3);
    }

    function speakCantonese(text, callback) {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.cancel();
            const u = new SpeechSynthesisUtterance(text);
            u.lang = 'zh-HK';
            u.rate = 0.85;
            u.pitch = 1.1;
            if (callback) u.onend = callback;
            window.speechSynthesis.speak(u);
        }
    }

    function initQuestion(mind, hand) {
        currentMind = mind; currentHand = hand; targetAnswer = mind + hand; currentClickIndex = 0;
        document.getElementById('num-mind').innerText = currentMind;
        document.getElementById('num-hand').innerText = currentHand;
        document.getElementById('user-ans').value = '';
        document.getElementById('hand-area').style.display = 'none';
        document.getElementById('next-btn').style.display = 'none';
        renderPalms(currentHand);
    }

    // 建立卡通手掌（SVG），每隻手掌最多顯示 5 隻手指
    function renderPalms(handCount) {
        const container = document.getElementById('hand-area');
        container.innerHTML = '';
        container.style.display = 'flex';

        const leftCount = handCount > 5 ? 5 : handCount;
        const rightCount = handCount > 5 ? handCount - 5 : 0;

        container.appendChild(createSVGPalm(leftCount, 0, '左手'));
        if (rightCount > 0) container.appendChild(createSVGPalm(rightCount, 5, '右手'));
    }

    // 使用 SVG 繪製一隻手掌，回傳 DOM 節點
    function createSVGPalm(count, startIndex, label) {
        const wrapper = document.createElement('div');
        wrapper.className = 'palm';

        const lbl = document.createElement('div');
        lbl.className = 'palm-label';
        lbl.innerText = label;
        wrapper.appendChild(lbl);

        const SVG_NS = "http://www.w3.org/2000/svg";
        const svg = document.createElementNS(SVG_NS, 'svg');
        svg.setAttribute('class', 'svg-wrapper');
        svg.setAttribute('viewBox', '0 0 240 170');
        svg.setAttribute('preserveAspectRatio', 'xMidYMid meet');

        // 背景掌心（卡通）
        const palmBg = document.createElementNS(SVG_NS, 'rect');
        palmBg.setAttribute('x', '18'); palmBg.setAttribute('y', '50'); palmBg.setAttribute('rx', '40'); palmBg.setAttribute('ry', '40');
        palmBg.setAttribute('width', '204'); palmBg.setAttribute('height', '98');
        palmBg.setAttribute('fill', 'url(#palmGrad)');
        palmBg.setAttribute('stroke', '#f97316');
        palmBg.setAttribute('stroke-width', '3');
        // 定義漸層
        const defs = document.createElementNS(SVG_NS, 'defs');
        const grad = document.createElementNS(SVG_NS, 'linearGradient');
        grad.setAttribute('id', 'palmGrad');
        grad.setAttribute('x1', '0'); grad.setAttribute('y1', '0'); grad.setAttribute('x2', '0'); grad.setAttribute('y2', '1');
        const stop1 = document.createElementNS(SVG_NS, 'stop'); stop1.setAttribute('offset','0'); stop1.setAttribute('stop-color','#fff1e6');
        const stop2 = document.createElementNS(SVG_NS, 'stop'); stop2.setAttribute('offset','1'); stop2.setAttribute('stop-color','#ffd8b5');
        grad.appendChild(stop1); grad.appendChild(stop2);
        defs.appendChild(grad);
        svg.appendChild(defs);
        svg.appendChild(palmBg);

        // fingers layout 計算
        const fingerW = 30;
        const spacing = 8;
        const totalWidth = count * fingerW + (count - 1) * spacing;
        const startX = (240 - totalWidth) / 2; // 放中間

        for (let i = 1; i <= count; i++) {
            const globalIndex = startIndex + i;
            const displayNum = currentMind + globalIndex;

            const g = document.createElementNS(SVG_NS, 'g');
            g.setAttribute('class', 'svg-finger');
            g.setAttribute('id', `finger-${globalIndex}`);
            const x = startX + (i - 1) * (fingerW + spacing);
            const y = 8;
            // 手指形狀（圓角矩形）
            const fingerRect = document.createElementNS(SVG_NS, 'rect');
            fingerRect.setAttribute('x', x.toString());
            fingerRect.setAttribute('y', y.toString());
            fingerRect.setAttribute('rx', '12');
            fingerRect.setAttribute('ry', '12');
            fingerRect.setAttribute('width', fingerW.toString());
            fingerRect.setAttribute('height', '98');
            fingerRect.setAttribute('class', 'finger-shape');
            fingerRect.setAttribute('id', `finger-shape-${globalIndex}`);

            // 連接小手掌部分（圓形過渡）
            const fingerCap = document.createElementNS(SVG_NS, 'ellipse');
            fingerCap.setAttribute('cx', (x + fingerW / 2).toString());
            fingerCap.setAttribute('cy', (y + 100).toString());
            fingerCap.setAttribute('rx', (fingerW / 2 + 6).toString());
            fingerCap.setAttribute('ry', '14');
            fingerCap.setAttribute('fill', 'rgba(0,0,0,0.02)');

            // 氣泡（圓 + 文字），預設隱藏（opacity 0）
            const bubble = document.createElementNS(SVG_NS, 'g');
            bubble.setAttribute('id', `bubble-${globalIndex}`);
            bubble.setAttribute('transform', `translate(${x + fingerW / 2}, ${y - 12})`);
            bubble.setAttribute('style', 'opacity:0; transition:opacity 0.18s ease;');

            const circ = document.createElementNS(SVG_NS, 'circle');
            circ.setAttribute('r', '18');
            circ.setAttribute('class', 'bubble');
            bubble.appendChild(circ);

            const txt = document.createElementNS(SVG_NS, 'text');
            txt.setAttribute('class', 'bubble-text');
            txt.setAttribute('y', '0');
            txt.setAttribute('x', '0');
            txt.textContent = displayNum;
            bubble.appendChild(txt);

            g.appendChild(fingerRect);
            g.appendChild(fingerCap);
            g.appendChild(bubble);

            // add click handler
            g.addEventListener('click', () => clickFinger(globalIndex, displayNum));

            svg.appendChild(g);
        }

        wrapper.appendChild(svg);

        const base = document.createElement('div');
        base.className = 'palm-base';
        wrapper.appendChild(base);

        return wrapper;
    }

    function clickFinger(index, num) {
        if (index !== currentClickIndex + 1) {
            speakCantonese("請順序點擊手指喔！");
            return;
        }
        currentClickIndex++;
        playDingSound();

        // 找到 SVG 節點，顯示泡泡並把指頭摺起（透過改變高度與位置）
        const fingerGroup = document.getElementById(`finger-${index}`);
        const fingerRect = document.getElementById(`finger-shape-${index}`);
        const bubbleGroup = document.getElementById(`bubble-${index}`);

        if (bubbleGroup) bubbleGroup.style.opacity = '1';
        if (fingerRect) {
            // 將手指縮短（用屬性改變），改為 folded 樣式
            fingerRect.setAttribute('height', '34');
            fingerRect.setAttribute('y', '72');
            // 加上 folded 樣式（改變填色、虛線）
            const g = fingerGroup;
            if (g) g.classList.add('folded');
        }
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
        if (isNaN(userVal)) { speakCantonese("請先填寫答案喔！"); return; }
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

    window.onload = function() { initQuestion(4, 5); };
</script>

</body>
</html>
