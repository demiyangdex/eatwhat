<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>今晚吃什麼？ | 深夜食堂</title>
    <style>
        /* 1. 視覺與風格設定 */
        :root {
            --bg-dark: #121216;
            --card-bg: #1e1e24;
            --text-main: #f5f5f5;
            --text-sub: #a0a0b0;
            --accent: #ff9f43;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
        }

        body {
            background: radial-gradient(circle at center, #2c2c35 0%, var(--bg-dark) 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            color: var(--text-main);
        }

        .container {
            width: 90%;
            max-width: 450px;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        h1 {
            font-size: 28px;
            font-weight: 700;
            letter-spacing: 2px;
            margin-bottom: 8px;
        }

        .subtitle {
            font-size: 14px;
            color: var(--text-sub);
            letter-spacing: 1px;
            margin-bottom: 30px;
        }

        /* 分類按鈕區 */
        .buttons {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            justify-content: center;
            margin-bottom: 35px;
        }

        button {
            border: 1px solid #444;
            padding: 10px 18px;
            font-size: 15px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            background-color: #2b2b36;
            color: var(--text-main);
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }

        button:hover {
            transform: translateY(-2px);
            background-color: #3b3b48;
            border-color: var(--accent);
            box-shadow: 0 6px 12px rgba(255, 159, 67, 0.2);
        }

        button:active {
            transform: translateY(0);
        }

        /* 3D 翻牌卡片容器 */
        .card-container {
            width: 280px;
            height: 360px;
            perspective: 1000px; 
            margin-bottom: 30px;
        }

        .card {
            width: 100%;
            height: 100%;
            position: relative;
            transition: transform 0.6s cubic-bezier(0.4, 0.2, 0.2, 1);
            transform-style: preserve-3d;
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 20px;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }

        /* 卡片背面 */
        .card-back {
            background: var(--card-bg);
            border: 2px solid #444;
            background-image: repeating-linear-gradient(45deg, transparent, transparent 10px, rgba(255,255,255,0.02) 10px, rgba(255,255,255,0.02) 20px);
        }

        .card-back h2 {
            font-size: 80px;
            color: #444;
            text-shadow: 0 4px 8px rgba(0,0,0,0.6);
        }

        /* 卡片正面 */
        .card-front {
            background: linear-gradient(145deg, var(--card-bg), #121216);
            border: 2px solid var(--accent);
            box-shadow: 0 0 15px rgba(255, 159, 67, 0.3); /* 霓虹光暈 */
            transform: rotateY(180deg);
        }

        .food-emoji {
            font-size: 80px;
            margin-bottom: 20px;
            filter: drop-shadow(0 6px 12px rgba(0,0,0,0.5));
        }

        .food-name {
            font-size: 28px;
            font-weight: bold;
            color: var(--accent);
            margin-bottom: 15px;
            letter-spacing: 2px;
        }

        .food-comment {
            font-size: 15px;
            color: var(--text-sub);
            line-height: 1.6;
        }

        /* 操作按鈕區 */
        .action-area {
            display: flex;
            gap: 15px;
            opacity: 0;
            visibility: hidden;
            transition: all 0.4s ease;
        }

        .action-area.show {
            opacity: 1;
            visibility: visible;
        }

        .action-btn {
            padding: 12px 24px;
            font-size: 16px;
            border-radius: 30px;
            font-weight: bold;
        }

        .btn-again {
            background-color: transparent;
            border: 1px solid var(--text-sub);
        }

        .btn-confirm {
            background-color: var(--accent);
            color: var(--bg-dark);
            border: none;
            box-shadow: 0 4px 15px rgba(255, 159, 67, 0.4);
        }

        .btn-confirm:hover {
            background-color: #ffb067;
            color: var(--bg-dark);
        }

    </style>
</head>
<body>

    <div class="container">
        <h1>今晚吃什麼？</h1>
        <div class="subtitle">深夜食堂，為你決定</div>
        
        <div class="buttons">
            <button onclick="drawFood('fastfood')">🍔 速食</button>
            <button onclick="drawFood('noodle')">🍜 麵食</button>
            <button onclick="drawFood('japanese')">🍣 日式</button>
            <button onclick="drawFood('taiwanese')">🍲 台式</button>
            <button onclick="drawFood('random')">🎲 隨便啦</button>
        </div>

        <!-- 3D 翻牌區塊 -->
        <div class="card-container">
            <div class="card" id="food-card">
                <div class="card-face card-back">
                    <h2>?</h2>
                </div>
                <div class="card-face card-front">
                    <div class="food-emoji" id="display-emoji">🍽️</div>
                    <div class="food-name" id="display-name"></div>
                    <div class="food-comment" id="display-comment"></div>
                </div>
            </div>
        </div>

        <!-- 操作按鈕 -->
        <div class="action-area" id="action-area">
            <button class="action-btn btn-again" onclick="resetCard()">🔄 再抽一次</button>
            <button class="action-btn btn-confirm" onclick="confirmChoice()">✅ 就決定是你了</button>
        </div>
    </div>

    <script>
        // 2. 資料結構設定
        const foodData = {
            fastfood: [
                { emoji: "🍔", name: "麥當勞", comment: "大薯加大，快樂加倍！今晚就放縱一下。" },
                { emoji: "🍕", name: "披薩", comment: "牽絲的起司是減肥的敵人，但今天不管了。" },
                { emoji: "🍗", name: "炸雞", comment: "吃一口酥脆的炸雞，把所有煩惱都咬碎。" }
            ],
            noodle: [
                { emoji: "🍜", name: "拉麵", comment: "今晚就用濃郁的湯頭，好好療癒疲憊的自己吧。" },
                { emoji: "🍝", name: "義大利麵", comment: "點杯紅酒，假裝自己正在米蘭度假。" },
                { emoji: "🥣", name: "陽春麵", comment: "簡單純粹，就像深夜裡最真實的陪伴。" }
            ],
            japanese: [
                { emoji: "🍣", name: "壽司", comment: "一口一個，錢包在哭泣但味蕾很開心。" },
                { emoji: "🍱", name: "丼飯", comment: "滿滿的料蓋在飯上，大口扒飯最爽快。" },
                { emoji: "🍛", name: "咖哩飯", comment: "濃郁的咖哩，絕對要加一顆半熟蛋才對味。" }
            ],
            taiwanese: [
                { emoji: "🍲", name: "火鍋", comment: "不管夏天冬天，台灣人的胃就是要有熱湯！" },
                { emoji: "🍚", name: "滷肉飯", comment: "肥瘦相間的滷肉，國民美食當之無愧。" },
                { emoji: "🥟", name: "水餃", comment: "沾點醬油配碗酸辣湯，今晚就這樣定了吧。" }
            ]
        };

        const card = document.getElementById('food-card');
        const actionArea = document.getElementById('action-area');
        const displayEmoji = document.getElementById('display-emoji');
        const displayName = document.getElementById('display-name');
        const displayComment = document.getElementById('display-comment');

        // 3. 互動邏輯 (狀態機設計)
        function drawFood(category) {
            // 若已翻開，先翻回背面等待動畫結束
            if (card.classList.contains('flipped')) {
                resetCard();
                setTimeout(() => executeDraw(category), 600);
            } else {
                executeDraw(category);
            }
        }

        function executeDraw(category) {
            let pool = [];
            if (category === 'random') {
                for (let key in foodData) {
                    pool = pool.concat(foodData[key]);
                }
            } else {
                pool = foodData[category];
            }

            const randomIndex = Math.floor(Math.random() * pool.length);
            const food = pool[randomIndex];

            displayEmoji.innerText = food.emoji;
            displayName.innerText = food.name;
            displayComment.innerText = food.comment;

            card.classList.add('flipped');
            actionArea.classList.add('show');
        }

        function resetCard() {
            card.classList.remove('flipped');
            actionArea.classList.remove('show');
        }

        function confirmChoice() {
            alert("太棒了！今晚就吃這個！🤤");
        }
    </script>
</body>
</html>
