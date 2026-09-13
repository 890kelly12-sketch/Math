<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>「心手口」18以內加數練習機（卡通手掌版）</title>
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        * { box-sizing: border-box; font-family: "PingFang HK","微軟正黑體",sans-serif; user-select: none; }
        body {
            background: linear-gradient(to bottom, #e0f2fe, #dcfce7);
            margin: 0; padding: 15px;
            display: flex; justify-content: center; align-items: center; min-height: 100vh;
        }
        .container {
            background: #fff; border-radius: 30px; padding: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.15); max-width: 900px; width: 100%;
            text-align: center; border: 6px solid #86efac; position: relative;
        }
        .header-area { display:flex; align-items:center; justify-content:center; gap:15px; margin-bottom:15px; }
        h1 { font-size:32px; margin:0; background:linear-gradient(45deg,#0284c7,#f97316); -webkit-background-clip:text; -webkit-text-fill-color:transparent; }
        .btn-icon { background:#fef08a; border:3px solid #eab308; border-radius:50%; width:55px; height:55px; font-size:26px; cursor:pointer; display:flex; align-items:center; justify-content:center; box-shadow:0 4px 0 #ca8a04; transition:transform .1s; }
        .equation-box {
            display:flex; justify-content:center; align-items:center; gap:15px;
            background:#fef9c3; border:5px solid #fde047; border-radius:25px; padding:20px; margin-bottom:15px; position:relative;
        }
        .num-card { display:flex; flex-direction:column; align-items:center; }
        .tag-title { font-size:20px; font-weight:700; color:#ea580c; }
        .num-val { font-size:55px; font-weight:900; color:#0369a1; line-height:1.1; }
        .operator { font-size:45px; font-weight:700; color:#2563eb; }
        .ans-input { width:100px; height:70px; font-size:45px; text-align:center; border:4px solid #3b82f6; border-radius:15px; outline:none; font-weight:bold; color:#1d4ed8; background:#fff; }

        /* 卡通手掌區樣式 */
        .hand-area { display:none; justify-content:center; gap:24px; margin:20px 0; align-items:flex-end; }
        .palm {
            width:180px; height:140px; background: radial-gradient(circle at 30% 30%, #fff7ed, #ffd9b6 40%, #fdbb74 100%);
            border-radius: 55% 55% 52% 52% / 60% 60% 40% 40%;
            border:4px solid #f97316; position: relative; display:flex; justify-content:center; align-items:flex-end; padding-bottom:12px;
            box-shadow: inset -6px -8px 15px rgba(0,0,0,0.06), 0 6px 0 rgba(0,0,0,0.04);
            transition: transform .12s ease;
        }
        .palm.small { width:150px; height:115px; }
        /* 裝手指的容器（相對定位） */
        .palm-inner { position: relative; width:100%; height:100%; display:block; }
        /* 每支手指（絕對定位於手掌上方）*/
        .finger {
            position: absolute; bottom: 52px; /* 讓手指延伸到掌心上方 */
            width: 26px; height: 88px; background: linear-gradient(#fed7aa,#f5b27a);
            border: 3px solid #f97316; border-radius: 14px 14px 6px 6px; transition: all .18s ease;
            display:flex; align-items:flex-start; justify-content:center; flex-direction:column;
            cursor:pointer;
            box-shadow: 0 4px 6px rgba(0,0,0,0.12);
        }
        /* 拇指放側邊 */
        .thumb {
            transform: rotate(-30deg);
            bottom: 38px; left: -8px;
            width:34px; height:64px; border-radius:14px 14px 8px 8px;
        }
        .finger.folded {
            height: 42px; background: linear-gradient(#fff2e1,#fff0df);
            border-style: dashed; transform: translateY(36px) rotate(-4deg);
            opacity: 0.98;
        }
        .finger .num-bubble {
            width:36px; height:36px; background:#0284c7; color:#fff; border-radius:50%;
            display:flex; align-items:center; justify-content:center; font-weight:700; font-size:18px; margin-top:6px; visibility:hidden;
            box-shadow: 0 3px 6px rgba(0,0,0,0.2);
        }
        /* 手指水平位置（五隻平均分布），用 data-pos 設定 left % */
        .finger[data-pos="1"]{ left: 22%; }
        .finger[data-pos="2"]{ left: 36%; }
        .finger[data-pos="3"]{ left: 50%; }
        .finger[data-pos="4"]{ left: 64%; }
        .finger[data-pos="5"]{ left: 78%; }
        /* 兩隻手排列 */
        .two-hands { display:flex; gap:18px; align-items:flex-end; }
        /* 點按反饋 */
        .palm.pulse { transform: scale(0.98); }

        .hand-group { display:flex; gap:12px; } /* 保持和舊版的容器兼容 */

        /* 行動按鈕 */
        .action-area { display:flex; justify-content:center; gap:15px; margin-top:20px; }
        .btn-green { background:#22c55e; color:#fff; border:none; padding:12px 30px; font-size:22px; font-weight:700; border-radius:50px; cursor:pointer; box-shadow:0 5px 0 #15803d; }
        .btn-blue { background:#3b82f6; color:#fff; border:none; padding:12px 30px; font-size:22px; font-weight:700; border-radius:50px; cursor:pointer; box-shadow:0 5px 0 #1d4ed8; display:none; }
        @media (max-width:520px) {
            .palm { width:140px; height:110px; }
            .finger { height:72px; width:22px; bottom:46px; }
            .num-val { font-size:40px; }
            .ans-input { width:80px; height:56px; font-size:36px; }
        }
    </style>
</head>
<body>
<div class="container">
    <div class="header-area">
        <button class="btn-icon" id="mouth-btn" onclick="playMouthSpeech()" title="讀出題目" aria-label="讀出題目">🗣️</button>
        <h1>「心手口」18以內加數練習機（卡通手掌）</h1>
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

        <button class="btn-icon" id="bulb-btn" style="position:absolute; right:-15px; bottom:-15px;" onclick="playBulbSpeech()" title="點擊獲得提示" aria-label="顯示提示">💡</button>
    </div>

    <!-- 卡通手掌區（動態生成）-->
    <div class="hand-area" id="hand-area" aria-hidden="true" aria-label="手掌與手指區"></div>

    <div class="action-area">
        <button class="btn-green" id="submit-btn" onclick="checkAnswer()" aria-label="提交答案">提交答案</button>
        <button class="btn-blue" id="next-btn" onclick="nextQuestion()" aria-label="下一題">➡️ 下一題</button>
    </div>

    <div id="sr-live" style="position:absolute; left:-9999px; width:1px; height:1px; overflow:hidden;" aria-live="polite"></div>
</div>

<script>
    // 全局變量
    let currentMind = 4;
    let currentHand = 5;
    let targetAnswer = 9;
    let currentClickIndex = 0;

    // AudioContext reuse
    let audioCtx = null;
    function getAudioContext() { if (!audioCtx) { try { audioCtx = new (window.AudioContext||window.webkitAudioContext)(); } catch(e){ audioCtx=null; } } return audioCtx; }
    function playDingSound() {
        const ctx = getAudioContext();
        if (!ctx) return;
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.type = 'sine'; osc.frequency.setValueAtTime(800, ctx.currentTime);
        gain.gain.setValueAtTime(0.3, ctx.currentTime); gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime+0.25);
        osc.connect(gain); gain.connect(ctx.destination);
        osc.start(); osc.stop(ctx.currentTime+0.25);
    }

    // TTS voice selection
    let selectedCantoneseVoice = null;
    function pickCantoneseVoice(){
        const vs = window.speechSynthesis.getVoices() || [];
        if (!vs.length) return null;
        const prefer = vs.find(v=> /yue|cantonese|hong ?kong|hk/i.test((v.name+' '+v.lang)));
        return prefer || vs.find(v=> /zh/i.test(v.lang)) || vs[0];
    }
    window.speechSynthesis.onvoiceschanged = ()=> selectedCantoneseVoice = pickCantoneseVoice();

    function speakCantonese(text, cb){
        if (!('speechSynthesis' in window)) {
            const sr = document.getElementById('sr-live'); sr.textContent = text; if (cb) cb(); return;
        }
        window.speechSynthesis.cancel();
        const u = new SpeechSynthesisUtterance(text);
        if (selectedCantoneseVoice) u.voice = selectedCantoneseVoice;
        u.lang = selectedCantoneseVoice ? selectedCantoneseVoice.lang||'zh-HK' : 'zh-HK';
        u.rate = 0.85; u.pitch = 1.05;
        if (cb) u.onend = cb;
        window.speechSynthesis.speak(u);
        document.getElementById('sr-live').textContent = text;
    }

    // 初始化
    function initQuestion(mind, hand){
        currentMind = mind; currentHand = hand; targetAnswer = mind+hand; currentClickIndex = 0;
        document.getElementById('num-mind').innerText = currentMind;
        document.getElementById('num-hand').innerText = currentHand;
        document.getElementById('user-ans').value = '';
        document.getElementById('hand-area').style.display = 'none';
        document.getElementById('hand-area').setAttribute('aria-hidden','true');
        document.getElementById('next-btn').style.display = 'none';
        renderPalms(currentHand);
    }

    // 生成卡通手掌與手指（每隻掌最多 5 指）
    function renderPalms(totalFingers){
        const container = document.getElementById('hand-area');
        container.innerHTML = '';
        let leftCount = Math.min(5, totalFingers);
        let rightCount = totalFingers > 5 ? totalFingers - 5 : 0;

        if (rightCount > 0) {
            const wrapper = document.createElement('div');
            wrapper.className = 'two-hands';
            wrapper.appendChild(createPalm(leftCount, 0, 'left'));
            wrapper.appendChild(createPalm(rightCount, leftCount, 'right'));
            container.appendChild(wrapper);
        } else {
            container.appendChild(createPalm(leftCount, 0, 'single'));
        }
    }

    // createPalm(count, startIndex, type)
    // startIndex: how many fingers already used (for numbering)
    function createPalm(count, startIndex, type){
        const palmWrap = document.createElement('div');
        palmWrap.className = 'palm';
        if (type === 'single') palmWrap.classList.add('small');

        const inner = document.createElement('div');
        inner.className = 'palm-inner';
        inner.setAttribute('aria-hidden','false');

        // create five slots but only show as many fingers as needed.
        // position indexes 1..5 map to left-to-right spots.
        let posOrder = [1,2,3,4,5];
        // If right hand visual (mirror), we'll still use same positions; visual mirroring is not needed for counting.
        for (let i=1;i<=count;i++){
            const globalIndex = startIndex + i; // 1-based overall finger index
            const finger = document.createElement('div');
            finger.className = 'finger';
            finger.dataset.index = globalIndex;
            finger.dataset.pos = i; // 1..5 -> CSS left%
            finger.setAttribute('role','button');
            finger.setAttribute('tabindex','0');

            // compute display number for bubble: currentMind + globalIndex
            const displayNum = currentMind + globalIndex;

            finger.innerHTML = `
                <div class="num-bubble" id="bubble-${globalIndex}">${displayNum}</div>
            `;
            // Accessibility label
            finger.setAttribute('aria-label', `手指 ${globalIndex}，數字 ${displayNum}`);
            // click/keyboard handlers
            finger.onclick = ()=> clickFinger(globalIndex, displayNum, palmWrap);
            finger.onkeydown = (e)=> { if (e.key==='Enter' || e.key===' ') { e.preventDefault(); clickFinger(globalIndex, displayNum, palmWrap); } };

            // set data-pos attribute for CSS left
            finger.setAttribute('data-pos', i.toString());

            inner.appendChild(finger);
        }

        // optional thumb (visual) - show for palms with at least 1 finger
        const thumb = document.createElement('div');
        thumb.className = 'finger thumb';
        thumb.style.left = '-6%';
        thumb.style.bottom = '36px';
        thumb.innerHTML = `<div style="width:1px;height:1px;opacity:0;"></div>`;
        inner.appendChild(thumb);

        palmWrap.appendChild(inner);
        return palmWrap;
    }

    // 點手指邏輯（序順檢查、收起動畫、氣泡顯示）
    function clickFinger(index, num, palmEl){
        if (index !== currentClickIndex + 1){
            speakCantonese("請順序點擊手指喔！");
            return;
        }
        currentClickIndex++;
        playDingSound();

        const bubble = document.getElementById(`bubble-${index}`);
        const finger = document.querySelector(`.finger[data-index='${index}']`);
        if (bubble) bubble.style.visibility = 'visible';
        if (finger) finger.classList.add('folded');

        // 手掌短暫縮放反饋
        if (palmEl){
            palmEl.classList.add('pulse');
            setTimeout(()=> palmEl.classList.remove('pulse'), 160);
        }
        speakCantonese(num.toString());
    }

    function playMouthSpeech(){ speakCantonese(`${currentMind} 加 ${currentHand} 等於幾多呀？`); }

    function playBulbSpeech(){
        // reset interaction so小朋友可以重新收手指
        currentClickIndex = 0;
        renderPalms(currentHand);
        const handArea = document.getElementById('hand-area');
        handArea.style.display = 'flex';
        handArea.setAttribute('aria-hidden','false');
        speakCantonese(`心裏面記住 ${currentMind}，順序收起手指 ${currentHand} 隻，一齊數下去，等於幾多呢？`);
    }

    function checkAnswer(){
        const raw = document.getElementById('user-ans').value;
        const userVal = parseInt(raw,10);
        if (isNaN(userVal)){
            speakCantonese("請先填寫答案喔！");
            return;
        }
        if (userVal === targetAnswer){
            confetti({ particleCount:100, spread:70, origin:{ y:0.6 } });
            document.getElementById('next-btn').style.display = 'inline-block';
            speakCantonese("你答對咗啦！");
            document.getElementById('sr-live').textContent = '答對了！按下一題';
        } else {
            speakCantonese("再數一次手指試試看！");
            document.getElementById('sr-live').textContent = '答案錯誤，請再試一次';
        }
    }

    // Enter 提交
    document.getElementById('user-ans').addEventListener('keydown', (e)=>{ if (e.key==='Enter'){ e.preventDefault(); checkAnswer(); } });

    function nextQuestion(){
        let nextMind = Math.floor(Math.random()*9)+1; // 1..9
        let maxHand = 18 - nextMind;
        let handLimit = Math.min(9, maxHand);
        handLimit = Math.max(1, handLimit);
        let nextHand = Math.floor(Math.random()*handLimit)+1;
        initQuestion(nextMind, nextHand);
    }

    window.onload = function(){
        selectedCantoneseVoice = pickCantoneseVoice();
        initQuestion(4,5);
        // resume audio on gesture if needed
        const resumeAudio = ()=> { const ctx = getAudioContext(); if (ctx && ctx.state==='suspended') ctx.resume(); window.removeEventListener('click', resumeAudio); window.removeEventListener('keydown', resumeAudio); };
        window.addEventListener('click', resumeAudio);
        window.addEventListener('keydown', resumeAudio);
    };
</script>
</body>
</html>
