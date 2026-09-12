<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>心手口趣味加法練習機</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: "MicroSoft YaHei", "BiauKai", sans-serif;
            user-select: none;
        }
        body {
            background-color: #eef9f2;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }
        .container {
            background: white;
            border-radius: 25px;
            padding: 25px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            max-width: 800px;
            width: 100%;
            text-align: center;
            border: 4px solid #8bc34a;
        }
        h1 {
            color: #2e7d32;
            margin-top: 0;
            font-size: 32px;
        }
        .speaker-btn {
            background-color: #ff9800;
            color: white;
            border: none;
            padding: 10px 20px;
            font-size: 18px;
            border-radius: 50px;
            cursor: pointer;
            box-shadow: 0 4px #e65100;
            margin-bottom: 20px;
            transition: 0.1s;
        }
        .speaker-btn:active {
            transform: translateY(4px);
            box-shadow: none;
        }
        
        /* 題目顯示區 */
        .equation-box {
            display: flex;
            justify-content: center;
            align-items: flex-end;
            gap: 20px;
            background-color: #fffde7;
            border: 3px dashed #fbc02d;
            border-radius: 20px;
            padding: 20px;
            margin-bottom: 25px;
        }
        .num-group {
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .label-tag {
            font-size: 18px;
            font-weight: bold;
            color: #d84315;
            margin-bottom: 5px;
        }
        .symbol {
            font-size: 48px;
            font-weight: bold;
            color: #333;
            line-height: 1;
            margin-bottom: 10px;
        }
        .num-display {
            font-size: 64px;
            font-weight: bold;
            color: #1565c0;
            line-height: 1;
        }

        /* 互動手掌區 */
        .hand-container {
            display: flex;
            justify-content: center;
            align-items: flex-end;
            gap: 15px;
            height: 220px;
            margin: 30px 0;
            position: relative;
        }
        .finger-wrapper {
            display: flex;
            flex-direction: column;
            align-items: center;
            cursor: pointer;
        }
        .count-bubble {
            width: 40px;
            height: 40px;
            background-color: #e91e63;
            color: white;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 22px;
            font-weight: bold;
            margin-bottom: 10px;
            visibility: hidden;
            box-shadow: 0 3px 6px rgba(0,0,0,0.2);
            animation: pop 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
        }
        @keyframes pop {
            0% { transform: scale(0); }
            100% { transform: scale(1); }
        }
        .finger {
            width: 40px;
            height: 120px;
            background-color: #ffcc80;
            border: 3px solid #ef6c00;
            border-radius: 20px 20px 5px 5px;
            transition: all 0.2s ease;
        }
        .finger.folded {
            height: 40px;
            background-color: #ffe0b2;
            border-style: dashed;
        }

        /* 手掌底座 */
        .palm {
            position: absolute;
            bottom: -20px;
            width: 260px;
            height: 60px;
            background-color: #ffcc80;
            border: 3px solid #ef6c00;
            border-top: none;
            border-radius: 0 0 30px 30px;
            z-index: -1;
        }

        /* 操作區 */
        .action-area {
            margin-top: 20px;
        }
        .instruction {
            font-size: 20px;
            color: #333;
            margin-bottom: 15px;
            font-weight: bold;
        }
        .answer-btn {
            background-color: #4caf50;
            color: white;
            border: none;
            padding: 12px 30px;
            font-size: 22px;
            border-radius: 15px;
            cursor: pointer;
            box-shadow: 0 5px #2e7d32;
            display: none;
        }
        .answer-btn:active {
            transform: translateY(4px);
            box-shadow: none;
        }
        .result-box {
            font-size: 28px;
            color: #d32f2f;
            font-weight: bold;
            margin-top: 15px;
            min-height: 40px;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>心手口加法學習機</h1>
    <button class="speaker-btn" onclick="speakText(instructionText)">🔊 聽題目與說明</button>

    <!-- 題目區域 -->
    <div class="equation-box">
        <!-- 心（加數/心中的數） -->
        <div class="num-group">
            <div class="label-tag">❤️ 心 (記在心中)</div>
            <div class="num-display" id="num-mind">4</div>
        </div>

        <div class="symbol">+</div>

        <!-- 手（被加數/伸出的手指數） -->
        <div class="num-group">
            <div class="label-tag">✋ 手 (伸出手指)</div>
            <div class="num-display" id="num-hand">5</div>
        </div>

        <div class="symbol">=</div>

        <div class="num-group">
            <div class="label-tag">❓ 答案</div>
            <div class="num-display" id="answer-display">?</div>
        </div>
    </div>

    <!-- 說明與提示 -->
    <div class="instruction" id="instruction-text">
        請用滑鼠順序點擊手指，把手指收起來，並在口中數數！
    </div>

    <!-- 互動手指區域 -->
    <div class="hand-container" id="hand-container">
        <!-- 手指會由 JavaScript 動態生成 -->
        <div class="palm"></div>
    </div>

    <!-- 操作按鈕與結果 -->
    <div class="action-area">
        <button class="answer-btn" id="calc-btn" onclick="checkAnswer()">公布答案！</button>
        <div class="result-box" id="result-box"></div>
    </div>
</div>

<script>
    // 預設題目：4 + 5
    const mindNum = 4; // 心裡的數
    const handNum = 5; // 手指的數
    const targetAnswer = mindNum + handNum;
    
    let currentClickCount = 0;
    let instructionText = `題目是 ${mindNum} 加 ${handNum}。心裡記住 ${mindNum}，請用滑鼠點擊手指，順序把手指收起來，接著數：5、6、7、8、9！`;

    // 初始化頁面
    window.onload = function() {
        document.getElementById('num-mind').innerText = mindNum;
        document.getElementById('num-hand').innerText = handNum;
        createFingers(handNum);
        speakText(instructionText);
    };

    // 語音朗讀功能 (HTML5 SpeechSynthesis)
    function speakText(text) {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.cancel(); // 停止先前的發音
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = 'zh-TW'; // 設定繁體中文
            utterance.rate = 0.85;     // 放慢語速適合小學生
            utterance.pitch = 1.1;     // 稍微提高音調使其聽起來溫柔
            window.speechSynthesis.speak(utterance);
        } else {
            alert("您的瀏覽器不支援語音朗讀功能。");
        }
    }

    // 動態建立手指數量
    function createFingers(count) {
        const container = document.getElementById('hand-container');
        // 清空手指（保留掌心）
        const palm = container.querySelector('.palm');
        container.innerHTML = '';
        container.appendChild(palm);

        for (let i = 1; i <= count; i++) {
            const wrapper = document.createElement('div');
            wrapper.className = 'finger-wrapper';
            wrapper.dataset.index = i;
            
            // 計算點擊後要顯示的順數數字 (例如：4+1=5, 4+2=6...)
            const countNum = mindNum + i;

            wrapper.innerHTML = `
                <div class="count-bubble" id="bubble-${i}">${countNum}</div>
                <div class="finger" id="finger-${i}"></div>
            `;

            wrapper.onclick = function() {
                clickFinger(i, countNum);
            };

            container.appendChild(wrapper);
        }
    }

    // 點擊手指邏輯
    function clickFinger(index, countNum) {
        // 限制必須按順序點擊
        if (index !== currentClickCount + 1) {
            speakText("請照順序點擊下一隻手指喔！");
            return;
        }

        currentClickCount++;

        // 顯示對應數字氣泡
        const bubble = document.getElementById(`bubble-${index}`);
        bubble.style.visibility = 'visible';

        // 改變手指樣式為收起
        const finger = document.getElementById(`finger-${index}`);
        finger.classList.add('folded');

        // 語音朗讀該數值
        speakText(countNum.toString());

        // 當所有手指都收起後
        if (currentClickCount === handNum) {
            document.getElementById('instruction-text').innerText = `非常好！最後數到的數字是 ${targetAnswer}！`;
            document.getElementById('calc-btn').style.display = 'inline-block';
            setTimeout(() => {
                speakText(`太棒了！手指都收起來了，最後數到了 ${targetAnswer}。請點擊公布答案！`);
            }, 1000);
        }
    }

    // 結算答案
    function checkAnswer() {
        document.getElementById('answer-display').innerText = targetAnswer;
        document.getElementById('result-box').innerText = `🎉 答對了！ ${mindNum} + ${handNum} = ${targetAnswer}`;
        speakText(`答對了！${mindNum} 加 ${handNum} 等於 ${targetAnswer}！你太聰明了！`);
    }
</script>

</body>
</html>
