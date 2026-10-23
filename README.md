<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>كلاش سكواد - نسخة المتصفح</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --accent: #f97316;
            --text: #f8fafc;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        h1 { color: var(--accent); margin-bottom: 5px; }
        .stats-bar {
            display: flex;
            gap: 20px;
            background: var(--card-bg);
            padding: 10px 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            font-size: 1.1rem;
            border: 1px solid #334155;
        }
        .container {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
            width: 100%;
            max-width: 600px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.5);
            text-align: center;
        }
        button {
            background-color: var(--accent);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 6px;
            font-size: 1rem;
            cursor: pointer;
            margin: 5px;
            font-weight: bold;
            transition: 0.2s;
        }
        button:hover { opacity: 0.9; transform: scale(1.02); }
        button:disabled { background-color: #475569; cursor: not-allowed; }
        .shop-items {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin: 15px 0;
        }
        .item-card {
            background: #0f172a;
            padding: 12px;
            border-radius: 8px;
            border: 1px solid #334155;
        }
        .log-box {
            background: #090d16;
            padding: 10px;
            border-radius: 6px;
            height: 120px;
            overflow-y: auto;
            text-align: right;
            font-family: monospace;
            margin-top: 15px;
            border: 1px solid #1e293b;
            font-size: 0.9rem;
        }
    </style>
</head>
<body>

    <h1>Clash Squad Web</h1>
    <div class="stats-bar">
        <div>الجولة: <span id="roundNum">1</span></div>
        <div>النقود: <span id="playerCoins">500</span> 🪙</div>
        <div>نتيجة الفريقين: <span id="teamScore">0 - 0</span></div>
    </div>

    <div class="container" id="gameContainer">
        <!-- سيتم تغيير المحتوى تلقائياً حسب مرحلة اللعبة -->
        <div id="phaseContent">
            <h2>مرحلة التجهيز (متجر كلاش سكواد)</h2>
            <p>قم بشراء معداتك قبل بدء الاشتباك!</p>
            <div class="shop-items" id="shopItems"></div>
            <button onclick="startCombat()">بدء الجولة ⚔️</button>
        </div>
    </div>

    <div class="container" style="margin-top: 15px;">
        <h3>سجل المعركة:</h3>
        <div class="log-box" id="logBox">تم بدء اللعبة. استعد للجولة الأولى!</div>
    </div>

    <script>
        let gameState = {
            round: 1,
            coins: 500,
            myTeamScore: 0,
            enemyTeamScore: 0,
            hasWeapon: false,
            hasArmor: false
        };

        const weapons = [
            { id: 'gun1', name: 'مسدس USP', price: 0, type: 'weapon' },
            { id: 'gun2', name: 'سلاح MP40', price: 1500, type: 'weapon' },
            { id: 'armor', name: 'درع مستوى 2', price: 400, type: 'armor' },
            { id: 'medkit', name: 'عدة طبية', price: 200, type: 'item' }
        ];

        function logMessage(msg) {
            const box = document.getElementById('logBox');
            box.innerHTML += `<br>> ${msg}`;
            box.scrollTop = box.scrollHeight;
        }

        function renderShop() {
            document.getElementById('playerCoins').innerText = gameState.coins;
            document.getElementById('roundNum').innerText = gameState.round;
            document.getElementById('teamScore').innerText = `${gameState.myTeamScore} - ${gameState.enemyTeamScore}`;

            let html = `<h2>مرحلة التجهيز (المتجر)</h2>
                        <p>رصيدك الحالي: ${gameState.coins} 🪙</p>
                        <div class="shop-items">`;

            weapons.forEach(w => {
                html += `<div class="item-card">
                            <strong>${w.name}</strong><br>السعر: ${w.price} 🪙<br>
                            <button onclick="buyItem('${w.id}', ${w.price})">شراء</button>
                         </div>`;
            });

            html += `</div><button onclick="startCombat()">بدء الاشتباك 🚀</button>`;
            document.getElementById('gameContainer').innerHTML = html;
        }

        function buyItem(id, price) {
            if (gameState.coins >= price) {
                gameState.coins -= price;
                if(id === 'armor') gameState.hasArmor = true;
                if(id === 'gun2') gameState.hasWeapon = true;
                logMessage(`تم شراء: ${id}`);
                renderShop();
            } else {
                logMessage("النقود غير كافية!");
            }
        }

        function startCombat() {
            document.getElementById('gameContainer').innerHTML = `
                <h2>معركة كلاش سكواد قيد الإثارة! 🎯</h2>
                <p>فريقك يواجه العدو في المنطقة...</p>
                <button onclick="resolveCombat()">إنهاء الجولة</button>
            `;
            logMessage(`بدأت الجولة ${gameState.round}! الاشتباك جارٍ...`);
        }

        function resolveCombat() {
            // محاكاة نتيجة الجولة عشوائياً أو بناءً على التجهيز
            let playerWinChance = 0.5;
            if (gameState.hasWeapon) playerWinChance += 0.2;
            if (gameState.hasArmor) playerWinChance += 0.15;

            let isWin = Math.random() < playerWinChance;

            if (isWin) {
                gameState.myTeamScore++;
                gameState.coins += 3000; // مكافأة الفوز
                logMessage(`🎉 فاز فريقك بالجولة ${gameState.round}! حصلت على 3000 عملة.`);
            } else {
                gameState.enemyTeamScore++;
                gameState.coins += 1200; // مكافأة الخسارة
                logMessage(`❌ خسرت الجولة ${gameState.round} لصالح العدو.`);
            }

            gameState.round++;

            // التحقق من نهاية اللعبة (الفوز بـ 4 جولات)
            if (gameState.myTeamScore >= 4 || gameState.enemyTeamScore >= 4) {
                let winner = gameState.myTeamScore >= 4 ? "فريقك البطل!" : "فريق الأعداء!";
                document.getElementById('gameContainer').innerHTML = `
                    <h2>انتهت المباراة! 🏆</h2>
                    <h3>النتيجة النهائية: ${gameState.myTeamScore} مقابل ${gameState.enemyTeamScore}</h3>
                    <p>الفائز: ${winner}</p>
                    <button onclick="resetGame()">إعادة اللعب</button>
                `;
                logMessage(`انتهت المباراة بفوز ${winner}`);
            } else {
                setTimeout(renderShop, 2000);
            }
        }

        function resetGame() {
            gameState = { round: 1, coins: 500, myTeamScore: 0, enemyTeamScore: 0, hasWeapon: false, hasArmor: false };
            renderShop();
        }

        renderShop();
    </script>
</body>
</html>
