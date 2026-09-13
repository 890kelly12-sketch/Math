<!DOCTYPE html>
<html lang="zh-HK">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>「心手口」18以內加數練習機（卡通手掌）</title>
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  <style>
    * { box-sizing: border-box; font-family:"PingFang HK","Chalkboard SE","Comic Sans MS","微軟正黑體",sans-serif; user-select:none; }
    body { margin:0; padding:15px; min-height:100vh; display:flex; align-items:center; justify-content:center;
           background: linear-gradient(to bottom,#e0f2fe,#dcfce7); }
    .container { width:100%; max-width:850px; background:#fff; border-radius:28px; padding:20px;
                 box-shadow:0 10px 30px rgba(0,0,0,0.15); border:6px solid #86efac; text-align:center; }

    .header-area { display:flex; align-items:center; justify-content:center; gap:15px; margin-bottom:12px; }
    h1 { margin:0; font-size:28px; background:linear-gradient(45deg,#0284c7,#f97316);
         -webkit-background-clip:text; -webkit-text-fill-color:transparent; }

    .btn-icon { width:55px; height:55px; border-radius:50%; background:#fef08a; border:3px solid #eab308;
                box-shadow:0 4px 0 #ca8a04; display:flex; align-items:center; justify-content:center; cursor:pointer; }

    .equation-box { display:flex; align-items:center; justify-content:center; gap:14px; padding:18px;
                     border-radius:20px; background:#fef9c3; border:5px solid #fde047; position:relative; margin-bottom:14px; }
    .num-card { display:flex; flex-direction:column; align-items:center; }
    .tag-title { font-size:18px; font-weight:700; color:#ea580c; }
    .num-val { font-size:48px; font-weight:900; color:#0369a1; }
    .operator { font-size:40px; font-weight:800; color:#2563eb; }
    .ans-input { width:100px; height:64px; font-size:40px; text-align:center; border-radius:12px;
                 border:4px solid #3b82f6; outline:none; font-weight:700; color:#1d4ed8; }

    /* 手掌區（SVG） */
    .hand-area { display:none; gap:22px; margin:18px 0; justify-content:center; align-items:flex-end; min-height:190px; }

    .palm { width:240px; height:170px; position:relative; }
    .palm-label { position:absolute; left:10px; top:6px; background:rgba(255,255,255,0.6); padding:4px 8px; border-radius:12px;
                  font-weight:700; color:#b45309; font-size:13px; }

    /* SVG finger styling and animations */
    .svg-wrapper { width:100%; height:100%; display:block; }
    .svg-finger { cursor:pointer; transition:transform 260ms cubic-bezier(.2,.9,.3,1); transform-box:fill-box; /* for transform-origin to work */ }
    /* transform-origin set per finger via inline style so folding pivots from finger base */
    .svg-finger.folded { transform: rotate(70deg) translateY(10px); }

    /* bubble transition */
    .bubble { transition:opacity 220ms ease, transform 220ms ease; opacity:0; transform:translateY(-6px) scale(0.9); }
    .bubble.visible { opacity:1; transform:translateY(0) scale(1); }

    /* 主要顏色（手掌樣式像附圖：膚色 + 粗外框） */
    .finger-shape { fill:#f6b88b; stroke:#2b1b16; stroke-width:6; stroke-linejoin:round; }
    .palm-body { fill:#f6b88b; stroke:#2b1b16; stroke-width:6; stroke-linejoin:round; }

    .palm-base { position:absolute; bottom:-8px; left:50%; transform:translateX(-50%); width:160px; height:36px;
                 border-radius:50%; background:radial-gradient(ellipse at center, rgba(0,0,0,0.08), rgba(0,0,0,0.02)); z-index:0; }

    .action-area { display:flex; gap:12px; justify-content:center; align-items:center; margin-top:6px; }
    .btn-green { background:#22c55e; color:#fff; border-radius:48px; padding:12px 28px; font-weight:800; font-size:18px; border:none; box-shadow:0 5px 0 #15803d; cursor:pointer; }
    .btn-blue { background:#3b82f6; color:#fff; border-radius:48px; padding:12px 28px; font-weight:800; font-size:18px; border:none; box-shadow:0 5px 0 #1d4ed8; cursor:pointer; display:none; }

    @media (max-width:640px) {
      .hand-area { flex-direction:column; gap:12px; }
      .palm { width:92%; height:180px; }
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
        <input id="user-ans" class="ans-input" type="number" placeholder="?" />
      </div>

      <button class="btn-icon" style="position:absolute; right:-15px; bottom:-15px;" onclick="playBulbSpeech()" title="點擊獲得提示">💡</button>
    </div>

    <div id="hand-area" class="hand-area"></div>

    <div class="action-area">
      <button class="btn-green" onclick="checkAnswer()">提交答案</button>
      <button class="btn-blue" id="next-btn" onclick="nextQuestion()">➡️ 下一題</button>
    </div>
  </div>

<script>
  // 全域變數
  let currentMind = 4;
  let currentHand = 5;
  let targetAnswer = 9;
  let currentClickIndex = 0;

  function playDingSound() {
    try {
      const ctx = new (window.AudioContext || window.webkitAudioContext)();
      const o = ctx.createOscillator();
      const g = ctx.createGain();
      o.type = 'sine';
      o.frequency.setValueAtTime(800, ctx.currentTime);
      g.gain.setValueAtTime(0.28, ctx.currentTime);
      g.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.28);
      o.connect(g); g.connect(ctx.destination); o.start(); o.stop(ctx.currentTime + 0.28);
    } catch (e) { /* audio may be blocked */ }
  }

  function speakCantonese(text, cb){
    if ('speechSynthesis' in window) {
      window.speechSynthesis.cancel();
      const u = new SpeechSynthesisUtterance(text);
      u.lang = 'zh-HK';
      u.rate = 0.85;
      u.pitch = 1.05;
      if (cb) u.onend = cb;
      window.speechSynthesis.speak(u);
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
    renderPalms(currentHand);
  }

  // 產生手掌（每隻手掌最多 5 隻手指），每根手指用 SVG group 表示，點擊會折起
  function renderPalms(handCount) {
    const container = document.getElementById('hand-area');
    container.innerHTML = '';
    container.style.display = 'flex';

    const leftCount = handCount > 5 ? 5 : handCount;
    const rightCount = handCount > 5 ? handCount - 5 : 0;

    container.appendChild(createPalmSVG(leftCount, 0, '左手'));
    if (rightCount > 0) container.appendChild(createPalmSVG(rightCount, 5, '右手'));
  }

  // 建立一個含 SVG 卡通手掌的 DOM
  function createPalmSVG(count, startIndex, label) {
    const wrapper = document.createElement('div');
    wrapper.className = 'palm';

    const lbl = document.createElement('div');
    lbl.className = 'palm-label';
    lbl.textContent = label;
    wrapper.appendChild(lbl);

    const SVG_NS = "http://www.w3.org/2000/svg";
    const svg = document.createElementNS(SVG_NS, 'svg');
    svg.setAttribute('class', 'svg-wrapper');
    svg.setAttribute('viewBox', '0 0 240 170');
    svg.setAttribute('preserveAspectRatio', 'xMidYMid meet');

    // 漸層 defs（用於掌心）
    const defs = document.createElementNS(SVG_NS, 'defs');
    const grad = document.createElementNS(SVG_NS, 'linearGradient');
    grad.setAttribute('id','palmGrad2'); grad.setAttribute('x1','0'); grad.setAttribute('y1','0'); grad.setAttribute('x2','0'); grad.setAttribute('y2','1');
    const s1 = document.createElementNS(SVG_NS,'stop'); s1.setAttribute('offset','0'); s1.setAttribute('stop-color','#fce7d6');
    const s2 = document.createElementNS(SVG_NS,'stop'); s2.setAttribute('offset','1'); s2.setAttribute('stop-color','#f6b88b');
    grad.appendChild(s1); grad.appendChild(s2); defs.appendChild(grad);
    svg.appendChild(defs);

    // 掌心主體（簡化的卡通掌）
    const palmPath = document.createElementNS(SVG_NS, 'path');
    // 一個圓潤掌心路徑（簡化）
    palmPath.setAttribute('d', 'M60 110 C40 100 36 76 60 62 C70 56 86 54 98 68 C110 54 130 50 148 62 C162 72 168 92 160 108 C148 132 110 142 80 140 C72 139 66 133 60 110 Z');
    palmPath.setAttribute('class','palm-body');
    palmPath.setAttribute('fill','url(#palmGrad2)');
    palmPath.setAttribute('stroke','#2b1b16');
    palmPath.setAttribute('stroke-width','6');
    palmPath.setAttribute('stroke-linejoin','round');
    svg.appendChild(palmPath);

    // finger 尺寸與間距
    const fingerW = 26;
    const spacing = 8;
    const totalWidth = count * fingerW + (count - 1) * spacing;
    const startX = (240 - totalWidth) / 2 + 8; // padding 微調
    const fingerTop = 6;

    for (let i = 1; i <= count; i++) {
      const globalIndex = startIndex + i;
      const displayNum = currentMind + globalIndex;

      const g = document.createElementNS(SVG_NS, 'g');
      g.setAttribute('class','svg-finger');
      g.setAttribute('id', `finger-${globalIndex}`);

      // 設置 transform-origin 讓每指從底部摺起（CSS transform-box + transform-origin 需要 fill-box）
      // 這裡使用 inline style 設定 transform-origin 為該指形中心底部（像 "x y"）
      const fingerCenterX = startX + (i-1)*(fingerW+spacing) + fingerW/2;
      // transform-origin 的 Y 使用像素，即底部位置（fingerTop + fingerHeight）
      const fingerHeight = 110;
      const originY = fingerTop + fingerHeight;
      g.style.transformOrigin = `${fingerCenterX}px ${originY}px`;

      // 手指主體（圓角矩形）
      const r = document.createElementNS(SVG_NS, 'rect');
      r.setAttribute('x', (startX + (i-1)*(fingerW+spacing)).toString());
      r.setAttribute('y', fingerTop.toString());
      r.setAttribute('rx', '14'); r.setAttribute('ry','14');
      r.setAttribute('width', fingerW.toString()); r.setAttribute('height', fingerHeight.toString());
      r.setAttribute('class','finger-shape');
      r.setAttribute('id', `finger-shape-${globalIndex}`);
      g.appendChild(r);

      // 指根處的圓形過渡（讓手指看起來接在掌心）
      const cap = document.createElementNS(SVG_NS, 'ellipse');
      cap.setAttribute('cx', (startX + (i-1)*(fingerW+spacing) + fingerW/2).toString());
      cap.setAttribute('cy', (fingerTop + fingerHeight + 6).toString());
      cap.setAttribute('rx', (fingerW/2 + 6).toString());
      cap.setAttribute('ry', '10');
      cap.setAttribute('fill','rgba(0,0,0,0.02)');
      g.appendChild(cap);

      // 氣泡（圓 + 文字），預設隱藏（opacity 0）
      const bubble = document.createElementNS(SVG_NS, 'g');
      bubble.setAttribute('id', `bubble-${globalIndex}`);
      bubble.setAttribute('transform', `translate(${(startX + (i-1)*(fingerW+spacing) + fingerW/2)} ${fingerTop - 18})`);
      bubble.setAttribute('class', 'bubble');

      const circ = document.createElementNS(SVG_NS, 'circle');
      circ.setAttribute('r','16');
      circ.setAttribute('fill','#0284c7');
      bubble.appendChild(circ);

      const txt = document.createElementNS(SVG_NS, 'text');
      txt.setAttribute('x','0'); txt.setAttribute('y','0'); txt.setAttribute('class','bubble-text');
      txt.setAttribute('fill','#fff'); txt.setAttribute('font-weight','700'); txt.setAttribute('font-size','14');
      txt.setAttribute('text-anchor','middle'); txt.setAttribute('dominant-baseline','middle');
      txt.textContent = displayNum;
      bubble.appendChild(txt);

      g.appendChild(bubble);

      // 點擊回調
      g.addEventListener('click', () => clickFinger(globalIndex, displayNum));

      svg.appendChild(g);
    }

    wrapper.appendChild(svg);

    // 底部陰影
    const base = document.createElement('div');
    base.className = 'palm-base';
    wrapper.appendChild(base);

    return wrapper;
  }

  // 點擊手指邏輯（必須順序點）
  function clickFinger(index, num) {
    if (index !== currentClickIndex + 1) {
      speakCantonese("請順序點擊手指喔！");
      return;
    }
    currentClickIndex++;
    playDingSound();

    // 顯示泡泡（改 class）
    const bubble = document.getElementById(`bubble-${index}`);
    if (bubble) {
      bubble.classList.add('visible');
      // also directly set opacity for compatibility
      bubble.style.opacity = '1';
      bubble.style.transform = 'translateY(0) scale(1)';
    }

    // 把指頭摺起（加入 folded class 觸發 transform）
    const g = document.getElementById(`finger-${index}`);
    if (g) {
      g.classList.add('folded');
      // 同時把指的 rect 改較短（若需要保險的視覺效果）
      const rect = document.getElementById(`finger-shape-${index}`);
      if (rect) {
        rect.setAttribute('height','36');
        rect.setAttribute('y','80');
      }
    }

    // 讀出數字（廣東話）
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
    const v = parseInt(document.getElementById('user-ans').value);
    if (isNaN(v)) { speakCantonese("請先填寫答案喔！"); return; }
    if (v === targetAnswer) {
      confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
      document.getElementById('next-btn').style.display = 'inline-block';
      speakCantonese("你答對咗啦！");
    } else {
      speakCantonese("再數一次手指試試看！");
    }
  }

  function nextQuestion() {
    const nextMind = Math.floor(Math.random()*9)+1;
    const maxHand = 18 - nextMind;
    const handLimit = Math.min(9, maxHand);
    const nextHand = Math.floor(Math.random()*handLimit)+1;
    initQuestion(nextMind, nextHand);
  }

  window.onload = function(){ initQuestion(4,5); };

</script>
</body>
</html>
