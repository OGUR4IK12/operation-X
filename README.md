<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Operation X - War Sodium</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0c0c2d, #1a1a4a, #2d0c2d);
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            width: 100%;
            background: rgba(10, 10, 40, 0.85);
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 0 30px rgba(100, 0, 255, 0.5);
            text-align: center;
            border: 1px solid #4a00e0;
        }

        header {
            margin-bottom: 20px;
            padding-bottom: 15px;
            border-bottom: 2px solid #8e2de2;
        }

        h1 {
            font-size: 3.5rem;
            margin-bottom: 10px;
            text-shadow: 0 0 15px #00f7ff, 0 0 25px #00f7ff;
            color: #ffffff;
            letter-spacing: 2px;
        }

        h2 {
            font-size: 1.5rem;
            margin-bottom: 10px;
            color: #8e2de2;
        }

        .subtitle {
            font-size: 1.2rem;
            margin-bottom: 30px;
            color: #00f7ff;
        }

        .deepseek-info {
            background: rgba(0, 247, 255, 0.1);
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 20px;
            border: 1px solid #00f7ff;
        }

        .menu, .game-screen, .battle-screen {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .difficulty-buttons {
            display: flex;
            gap: 15px;
            margin: 20px 0;
            flex-wrap: wrap;
            justify-content: center;
        }

        button {
            padding: 12px 25px;
            font-size: 1.1rem;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: bold;
            text-transform: uppercase;
            background: rgba(255, 255, 255, 0.1);
            color: white;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .play-btn {
            background: linear-gradient(to right, #8e2de2, #4a00e0);
            color: white;
            padding: 15px 50px;
            font-size: 1.5rem;
            border: none;
            box-shadow: 0 0 15px rgba(142, 45, 226, 0.7);
        }

        .easy-btn {
            background: linear-gradient(to right, #00b09b, #96c93d);
            border: none;
        }

        .medium-btn {
            background: linear-gradient(to right, #ff9a00, #ff5e00);
            border: none;
        }

        .hard-btn {
            background: linear-gradient(to right, #ff416c, #ff4b2b);
            border: none;
        }

        .insane-btn {
            background: linear-gradient(to right, #8e2de2, #4a00e0);
            border: none;
        }

        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
        }

        .game-info {
            display: flex;
            justify-content: space-between;
            width: 100%;
            margin-bottom: 20px;
            font-size: 1.2rem;
        }

        .blue-team, .red-team {
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: bold;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .blue-team {
            background: rgba(0, 120, 255, 0.2);
            border: 2px solid #0078ff;
            box-shadow: 0 0 10px rgba(0, 120, 255, 0.5);
        }

        .red-team {
            background: rgba(255, 0, 0, 0.2);
            border: 2px solid #ff0000;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
        }

        .soldier-count {
            font-size: 1.8rem;
            margin-top: 5px;
            text-shadow: 0 0 5px currentColor;
        }

        .battlefield-container {
            width: 100%;
            height: 500px;
            position: relative;
            margin: 20px 0;
            border: 3px solid #34495e;
            border-radius: 10px;
            overflow: hidden;
            background: linear-gradient(to bottom, #1a2a6c, #2d0c2d);
        }

        .battlefield {
            width: 100%;
            height: 100%;
            position: relative;
        }

        .territory {
            position: absolute;
            top: 0;
            bottom: 0;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: width 2s ease;
        }

        .blue-territory {
            left: 0;
            background: rgba(0, 120, 255, 0.15);
            width: 50%;
        }

        .red-territory {
            right: 0;
            background: rgba(255, 0, 0, 0.15);
            width: 50%;
        }

        .front-line {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 8px;
            background: linear-gradient(to bottom, #fdbb2d, #ff5e00);
            left: 50%;
            transform: translateX(-50%);
            transition: all 3s ease;
            box-shadow: 0 0 15px #fdbb2d;
            z-index: 10;
        }

        .territory-label {
            font-size: 1.5rem;
            font-weight: bold;
            text-shadow: 0 0 10px currentColor;
            padding: 10px 20px;
            border-radius: 5px;
            background: rgba(0, 0, 0, 0.5);
        }

        .blue-label {
            color: #00a8ff;
        }

        .red-label {
            color: #ff3e3e;
        }

        .controls {
            display: flex;
            flex-direction: column;
            gap: 15px;
            width: 100%;
        }

        .recruit-btn {
            background: linear-gradient(to right, #0072ff, #00c6ff);
            color: white;
            font-size: 1.2rem;
            padding: 15px;
            border-radius: 8px;
            position: relative;
            border: none;
            box-shadow: 0 0 10px rgba(0, 114, 255, 0.7);
        }

        .recruit-btn:disabled {
            background: #7f8c8d;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .recruit-btn:disabled:hover {
            transform: none;
            box-shadow: none;
        }

        .countdown {
            font-size: 1.5rem;
            color: #fdbb2d;
            margin: 10px 0;
            text-shadow: 0 0 10px #fdbb2d;
        }

        .deployment-info {
            display: flex;
            justify-content: space-between;
            width: 100%;
            margin-top: 20px;
        }

        .deployment-sector {
            flex: 1;
            padding: 10px;
            margin: 0 5px;
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.1);
        }

        .sector-left {
            border: 1px solid #00b09b;
        }

        .sector-center {
            border: 1px solid #ff9a00;
        }

        .sector-right {
            border: 1px solid #ff416c;
        }

        .battle-log {
            width: 100%;
            height: 120px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 8px;
            padding: 10px;
            overflow-y: auto;
            margin-top: 20px;
            text-align: left;
            font-size: 0.9rem;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .blue-text {
            color: #00a8ff;
        }

        .red-text {
            color: #ff3e3e;
        }

        .yellow-text {
            color: #fdbb2d;
        }

        .hidden {
            display: none;
        }

        .back-btn {
            background: linear-gradient(to right, #8e2de2, #4a00e0);
            color: white;
            margin-top: 20px;
            border: none;
        }

        .instructions {
            margin-top: 20px;
            padding: 15px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 8px;
            text-align: left;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .instructions h3 {
            color: #fdbb2d;
            margin-bottom: 10px;
        }

        .instructions ul {
            padding-left: 20px;
        }

        .instructions li {
            margin-bottom: 8px;
        }

        /* Стили для стрелок */
        .arrow {
            position: absolute;
            width: 0;
            height: 0;
            border-style: solid;
            cursor: pointer;
            z-index: 20;
            transition: all 0.3s ease;
        }

        .arrow:hover {
            transform: scale(1.2);
        }

        .arrow-left {
            left: 20%;
            top: 50%;
            transform: translateY(-50%);
            border-width: 15px 30px 15px 0;
            border-color: transparent #00a8ff transparent transparent;
        }

        .arrow-center {
            left: 50%;
            top: 50%;
            transform: translate(-50%, -50%);
            border-width: 30px 0 30px 50px;
            border-color: transparent transparent transparent #00a8ff;
        }

        .arrow-right {
            right: 20%;
            top: 50%;
            transform: translateY(-50%);
            border-width: 15px 0 15px 30px;
            border-color: transparent transparent transparent #00a8ff;
        }

        /* Модальное окно для ввода солдат */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 100;
            justify-content: center;
            align-items: center;
        }

        .modal-content {
            background: linear-gradient(135deg, #1a1a4a, #2d0c2d);
            padding: 30px;
            border-radius: 15px;
            width: 400px;
            text-align: center;
            border: 2px solid #8e2de2;
            box-shadow: 0 0 30px rgba(142, 45, 226, 0.7);
        }

        .modal h3 {
            margin-bottom: 20px;
            color: #fdbb2d;
        }

        .modal input {
            width: 100%;
            padding: 12px;
            margin: 15px 0;
            border-radius: 8px;
            border: 1px solid #8e2de2;
            background: rgba(0, 0, 0, 0.5);
            color: white;
            font-size: 1.2rem;
            text-align: center;
        }

        .modal-buttons {
            display: flex;
            gap: 15px;
            justify-content: center;
        }

        .modal-btn {
            padding: 10px 20px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
        }

        .confirm-btn {
            background: linear-gradient(to right, #00b09b, #96c93d);
            color: white;
        }

        .cancel-btn {
            background: linear-gradient(to right, #ff416c, #ff4b2b);
            color: white;
        }

        @media (max-width: 768px) {
            h1 {
                font-size: 2.5rem;
            }
            
            .battlefield-container {
                height: 400px;
            }
            
            .game-info {
                flex-direction: column;
                gap: 10px;
            }
            
            .deployment-info {
                flex-direction: column;
                gap: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Меню -->
        <div class="menu" id="menu">
            <header>
                <h1>OPERATION X</h1>
                <h2>War Sodium - Тактическая битва</h2>
                <p class="subtitle">Стратегическое сражение за территорию</p>
            </header>
            
            <div class="deepseek-info">
                Мини-игра создана на 96% с помощью DeepSeek
            </div>
            
            <button class="play-btn" id="playBtn">Начать операцию</button>
            
            <div class="instructions">
                <h3>Инструкция:</h3>
                <ul>
                    <li>Выберите уровень сложности</li>
                    <li>Накопите солдат перед битвой (кнопка найма)</li>
                    <li>Нажмите на стрелки для отправки войск на фронт</li>
                    <li>Введите количество солдат для отправки</li>
                    <li>Используйте тактические преимущества для окружения противника</li>
                    <li>Захватите 90% территории для победы!</li>
                </ul>
            </div>
        </div>

        <!-- Экран выбора сложности -->
        <div class="game-screen hidden" id="gameScreen">
            <h2>Выберите уровень сложности операции</h2>
            <div class="difficulty-buttons">
                <button class="easy-btn" data-difficulty="easy">Легкий (100K)</button>
                <button class="medium-btn" data-difficulty="medium">Средний (300K)</button>
                <button class="hard-btn" data-difficulty="hard">Сложный (600K)</button>
                <button class="insane-btn" data-difficulty="insane">Хард (1M)</button>
            </div>
            <button class="back-btn" id="backToMenu">Назад в меню</button>
        </div>

        <!-- Экран битвы -->
        <div class="battle-screen hidden" id="battleScreen">
            <h2>OPERATION X - Активная фаза</h2>
            
            <div class="game-info">
                <div class="blue-team">
                    <div>СИНИЕ (ВАШИ СИЛЫ)</div>
                    <div class="soldier-count" id="blueSoldiers">0</div>
                    <div>солдат</div>
                </div>
                <div class="red-team">
                    <div>КРАСНЫЕ (СИЛЫ ИИ)</div>
                    <div class="soldier-count" id="redSoldiers">0</div>
                    <div>солдат</div>
                </div>
            </div>

            <div class="countdown" id="countdown">До начала битвы: 30</div>

            <div class="battlefield-container">
                <div class="battlefield" id="battlefield">
                    <div class="territory blue-territory" id="blueTerritory">
                        <div class="territory-label blue-label" id="blueLabel">0</div>
                    </div>
                    <div class="front-line" id="frontLine"></div>
                    <div class="territory red-territory" id="redTerritory">
                        <div class="territory-label red-label" id="redLabel">100,000</div>
                    </div>
                    
                    <!-- Стрелки для управления войсками -->
                    <div class="arrow arrow-left" id="arrowLeft"></div>
                    <div class="arrow arrow-center" id="arrowCenter"></div>
                    <div class="arrow arrow-right" id="arrowRight"></div>
                </div>
            </div>

            <div class="controls">
                <button class="recruit-btn" id="recruitBtn">
                    Мобилизовать солдат (+15K)
                    <div>Перезарядка: <span id="cooldown">5</span> сек</div>
                </button>

                <div class="deployment-info">
                    <div class="deployment-sector sector-left">
                        <div>Левый фланг</div>
                        <div id="deployLeft">0</div>
                    </div>
                    <div class="deployment-sector sector-center">
                        <div>Центр</div>
                        <div id="deployCenter">0</div>
                    </div>
                    <div class="deployment-sector sector-right">
                        <div>Правый фланг</div>
                        <div id="deployRight">0</div>
                    </div>
                </div>
            </div>

            <div class="battle-log" id="battleLog">
                <div class="yellow-text">Операция начнется через 30 секунд. Мобилизуйте войска!</div>
            </div>

            <button class="back-btn" id="backToDifficulty">Прервать операцию</button>
        </div>
    </div>

    <!-- Модальное окно для ввода количества солдат -->
    <div class="modal" id="soldierModal">
        <div class="modal-content">
            <h3 id="modalTitle">Отправка подкреплений</h3>
            <p>Введите количество солдат для отправки:</p>
            <input type="number" id="soldierInput" min="1000" value="5000">
            <div class="modal-buttons">
                <button class="modal-btn confirm-btn" id="confirmDeploy">Отправить</button>
                <button class="modal-btn cancel-btn" id="cancelDeploy">Отмена</button>
            </div>
        </div>
    </div>

    <script>
        // Элементы DOM
        const menu = document.getElementById('menu');
        const gameScreen = document.getElementById('gameScreen');
        const battleScreen = document.getElementById('battleScreen');
        const playBtn = document.getElementById('playBtn');
        const backToMenu = document.getElementById('backToMenu');
        const backToDifficulty = document.getElementById('backToDifficulty');
        const difficultyButtons = document.querySelectorAll('.difficulty-buttons button');
        const blueSoldiersElement = document.getElementById('blueSoldiers');
        const redSoldiersElement = document.getElementById('redSoldiers');
        const countdownElement = document.getElementById('countdown');
        const recruitBtn = document.getElementById('recruitBtn');
        const cooldownElement = document.getElementById('cooldown');
        const blueTerritory = document.getElementById('blueTerritory');
        const redTerritory = document.getElementById('redTerritory');
        const blueLabel = document.getElementById('blueLabel');
        const redLabel = document.getElementById('redLabel');
        const frontLine = document.getElementById('frontLine');
        const battleLog = document.getElementById('battleLog');
        const deployLeft = document.getElementById('deployLeft');
        const deployCenter = document.getElementById('deployCenter');
        const deployRight = document.getElementById('deployRight');
        const battlefield = document.getElementById('battlefield');
        const arrowLeft = document.getElementById('arrowLeft');
        const arrowCenter = document.getElementById('arrowCenter');
        const arrowRight = document.getElementById('arrowRight');
        const soldierModal = document.getElementById('soldierModal');
        const soldierInput = document.getElementById('soldierInput');
        const confirmDeploy = document.getElementById('confirmDeploy');
        const cancelDeploy = document.getElementById('cancelDeploy');
        const modalTitle = document.getElementById('modalTitle');

        // Игровые переменные
        let gameState = {
            difficulty: 'easy',
            blueSoldiers: 0,
            redSoldiers: 0,
            blueDeployment: { left: 0, center: 0, right: 0 },
            redDeployment: { left: 0, center: 0, right: 0 },
            countdown: 30,
            battleStarted: false,
            recruitCooldown: 0,
            territory: 50, // процент территории синих
            selectedSector: null
        };

        // Настройки сложности
        const difficultySettings = {
            easy: { redSoldiers: 100000 },
            medium: { redSoldiers: 300000 },
            hard: { redSoldiers: 600000 },
            insane: { redSoldiers: 1000000 }
        };

        // Навигация
        playBtn.addEventListener('click', () => {
            menu.classList.add('hidden');
            gameScreen.classList.remove('hidden');
        });

        backToMenu.addEventListener('click', () => {
            gameScreen.classList.add('hidden');
            menu.classList.remove('hidden');
        });

        backToDifficulty.addEventListener('click', () => {
            battleScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            resetGame();
        });

        // Выбор сложности
        difficultyButtons.forEach(button => {
            button.addEventListener('click', () => {
                gameState.difficulty = button.dataset.difficulty;
                startGame();
            });
        });

        // Начать игру
        function startGame() {
            gameScreen.classList.add('hidden');
            battleScreen.classList.remove('hidden');
            
            // Инициализация игры
            const settings = difficultySettings[gameState.difficulty];
            gameState.redSoldiers = settings.redSoldiers;
            gameState.blueSoldiers = 0;
            gameState.countdown = 30;
            gameState.battleStarted = false;
            gameState.recruitCooldown = 0;
            gameState.territory = 50;
            
            updateUI();
            startCountdown();
            
            // Очистить лог битвы
            battleLog.innerHTML = '<div class="yellow-text">Операция начнется через 30 секунд. Мобилизуйте войска!</div>';
            
            // Добавить сообщение о сложности
            addLogMessage(`Активирована операция уровня "${getDifficultyName(gameState.difficulty)}". Противник имеет ${gameState.redSoldiers.toLocaleString()} солдат.`, 'yellow-text');
        }

        // Получить название сложности
        function getDifficultyName(difficulty) {
            const names = {
                'easy': 'ЛЕГКИЙ',
                'medium': 'СРЕДНИЙ',
                'hard': 'СЛОЖНЫЙ',
                'insane': 'ХАРД'
            };
            return names[difficulty];
        }

        // Сброс игры
        function resetGame() {
            gameState = {
                difficulty: 'easy',
                blueSoldiers: 0,
                redSoldiers: 0,
                blueDeployment: { left: 0, center: 0, right: 0 },
                redDeployment: { left: 0, center: 0, right: 0 },
                countdown: 30,
                battleStarted: false,
                recruitCooldown: 0,
                territory: 50,
                selectedSector: null
            };
        }

        // Обновление интерфейса
        function updateUI() {
            blueSoldiersElement.textContent = gameState.blueSoldiers.toLocaleString();
            redSoldiersElement.textContent = gameState.redSoldiers.toLocaleString();
            
            blueLabel.textContent = gameState.blueSoldiers.toLocaleString();
            redLabel.textContent = gameState.redSoldiers.toLocaleString();
            
            // Обновление территории
            blueTerritory.style.width = `${gameState.territory}%`;
            redTerritory.style.width = `${100 - gameState.territory}%`;
            
            // Обновление линии фронта с изгибом
            updateFrontLine();
            
            // Обновление кнопки найма
            if (gameState.recruitCooldown > 0) {
                recruitBtn.disabled = true;
                cooldownElement.textContent = gameState.recruitCooldown;
            } else {
                recruitBtn.disabled = false;
                cooldownElement.textContent = '0';
            }
            
            // Обновление развертывания
            deployLeft.textContent = gameState.blueDeployment.left.toLocaleString();
            deployCenter.textContent = gameState.blueDeployment.center.toLocaleString();
            deployRight.textContent = gameState.blueDeployment.right.toLocaleString();
        }

        // Обновление линии фронта с изгибом
        function updateFrontLine() {
            // Создаем изгиб линии фронта на основе распределения сил
            const leftBalance = gameState.blueDeployment.left - gameState.redDeployment.left;
            const centerBalance = gameState.blueDeployment.center - gameState.redDeployment.center;
            const rightBalance = gameState.blueDeployment.right - gameState.redDeployment.right;
            
            // Рассчитываем общий изгиб
            let curve = 0;
            if (Math.abs(leftBalance) > 1000) curve -= leftBalance / 50000;
            if (Math.abs(rightBalance) > 1000) curve += rightBalance / 50000;
            
            // Ограничиваем изгиб
            curve = Math.max(-30, Math.min(30, curve));
            
            // Применяем изгиб к линии фронта
            frontLine.style.left = `${gameState.territory}%`;
            frontLine.style.transform = `translateX(-50%) skewX(${curve}deg)`;
        }

        // Обратный отсчет до битвы
        function startCountdown() {
            const countdownInterval = setInterval(() => {
                gameState.countdown--;
                countdownElement.textContent = `До начала битвы: ${gameState.countdown}`;
                
                if (gameState.countdown <= 0) {
                    clearInterval(countdownInterval);
                    startBattle();
                }
            }, 1000);
        }

        // Начать битву
        function startBattle() {
            gameState.battleStarted = true;
            countdownElement.textContent = 'ОПЕРАЦИЯ НАЧАЛАСЬ!';
            addLogMessage('=== ОПЕРАЦИЯ X АКТИВИРОВАНА! ===', 'yellow-text');
            
            // Показать стрелки управления
            arrowLeft.style.display = 'block';
            arrowCenter.style.display = 'block';
            arrowRight.style.display = 'block';
            
            // Запустить логику ИИ
            setInterval(aiLogic, 2000);
            
            // Запустить симуляцию битвы
            setInterval(simulateBattle, 1000);
        }

        // Логика ИИ
        function aiLogic() {
            if (!gameState.battleStarted) return;
            
            // ИИ распределяет солдат случайным образом
            const totalRed = gameState.redSoldiers;
            const left = Math.floor(totalRed * (0.2 + Math.random() * 0.3));
            const center = Math.floor(totalRed * (0.3 + Math.random() * 0.3));
            const right = totalRed - left - center;
            
            gameState.redDeployment = { left, center, right };
            
            addLogMessage(`Противник перегруппировал силы: Левый фланг ${left.toLocaleString()}, Центр ${center.toLocaleString()}, Правый фланг ${right.toLocaleString()}`, 'red-text');
            
            updateUI();
        }

        // Симуляция битвы
        function simulateBattle() {
            if (!gameState.battleStarted) return;
            
            // Расчет сил на каждом участке
            const blueForces = gameState.blueDeployment;
            const redForces = gameState.redDeployment;
            
            // Общая эффективность
            let blueEffectiveness = 0;
            let redEffectiveness = 0;
            
            // Расчет эффективности на каждом участке
            const sectors = ['left', 'center', 'right'];
            let sectorMessages = [];
            
            sectors.forEach(sector => {
                const blue = blueForces[sector] || 0;
                const red = redForces[sector] || 0;
                
                if (blue > red * 1.5) {
                    // Синие окружают красных на этом участке
                    blueEffectiveness += 2;
                    sectorMessages.push(`Синие окружают противника на ${getSectorName(sector)}!`);
                } else if (red > blue * 1.5) {
                    // Красные окружают синих на этом участке
                    redEffectiveness += 2;
                    sectorMessages.push(`Красные окружают ваши силы на ${getSectorName(sector)}!`);
                } else if (blue > red) {
                    blueEffectiveness += 1;
                } else if (red > blue) {
                    redEffectiveness += 1;
                }
                
                // Потери в бою
                const blueLosses = Math.min(blue, Math.floor(red * 0.1));
                const redLosses = Math.min(red, Math.floor(blue * 0.1));
                
                gameState.blueDeployment[sector] -= blueLosses;
                gameState.redDeployment[sector] -= redLosses;
                gameState.blueSoldiers = Math.max(0, gameState.blueSoldiers - blueLosses);
                gameState.redSoldiers = Math.max(0, gameState.redSoldiers - redLosses);
            });
            
            // Изменение территории на основе эффективности и разницы в силах
            const totalBlue = gameState.blueSoldiers + gameState.blueDeployment.left + gameState.blueDeployment.center + gameState.blueDeployment.right;
            const totalRed = gameState.redSoldiers + gameState.redDeployment.left + gameState.redDeployment.center + gameState.redDeployment.right;
            
            // Рассчитываем скорость продвижения фронта в зависимости от разницы в силах
            const forceDifference = totalBlue - totalRed;
            const advanceSpeed = Math.max(0.1, Math.min(2, 1 + forceDifference / 100000));
            
            if (blueEffectiveness > redEffectiveness) {
                gameState.territory += advanceSpeed;
                addLogMessage(`Ваши войска продвигаются вперед! (скорость: ${advanceSpeed.toFixed(1)}x)`, 'blue-text');
            } else if (redEffectiveness > blueEffectiveness) {
                gameState.territory -= advanceSpeed;
                addLogMessage(`Противник продвигается вперед! (скорость: ${advanceSpeed.toFixed(1)}x)`, 'red-text');
            }
            
            // Добавить сообщения о секторах
            if (sectorMessages.length > 0) {
                sectorMessages.forEach(msg => addLogMessage(msg, 'yellow-text'));
            }
            
            // Ограничение территории от 5% до 95%
            gameState.territory = Math.max(5, Math.min(95, gameState.territory));
            
            // Обновление интерфейса
            updateUI();
            
            // Проверка условий победы/поражения
            if (gameState.territory >= 90) {
                endGame('blue');
            } else if (gameState.territory <= 10) {
                endGame('red');
            }
        }

        // Получить название участка
        function getSectorName(sector) {
            const names = {
                'left': 'левом фланге',
                'center': 'центре',
                'right': 'правом фланге'
            };
            return names[sector];
        }

        // Завершение игры
        function endGame(winner) {
            gameState.battleStarted = false;
            
            // Скрыть стрелки
            arrowLeft.style.display = 'none';
            arrowCenter.style.display = 'none';
            arrowRight.style.display = 'none';
            
            if (winner === 'blue') {
                addLogMessage('=== ПОБЕДА! ОПЕРАЦИЯ УСПЕШНО ЗАВЕРШЕНА! ===', 'blue-text');
                addLogMessage('Вы захватили 90% территории противника!', 'blue-text');
                setTimeout(() => alert('ПОБЕДА! ОПЕРАЦИЯ УСПЕШНО ЗАВЕРШЕНА! Вы захватили 90% территории противника!'), 100);
            } else {
                addLogMessage('=== ПОРАЖЕНИЕ! ОПЕРАЦИЯ ПРОВАЛЕНА! ===', 'red-text');
                addLogMessage('Вы потеряли 90% своей территории!', 'red-text');
                setTimeout(() => alert('ПОРАЖЕНИЕ! ОПЕРАЦИЯ ПРОВАЛЕНА! Вы потеряли 90% своей территории!'), 100);
            }
        }

        // Наем солдат
        recruitBtn.addEventListener('click', () => {
            if (gameState.recruitCooldown > 0) return;
            
            gameState.blueSoldiers += 15000;
            gameState.recruitCooldown = 5;
            updateUI();
            addLogMessage('Мобилизовано 15,000 солдат!', 'blue-text');
            
            // Запустить перезарядку
            const cooldownInterval = setInterval(() => {
                gameState.recruitCooldown--;
                updateUI();
                
                if (gameState.recruitCooldown <= 0) {
                    clearInterval(cooldownInterval);
                }
            }, 1000);
        });

        // Обработчики для стрелок
        arrowLeft.addEventListener('click', () => {
            if (!gameState.battleStarted) return;
            gameState.selectedSector = 'left';
            showSoldierModal('левый фланг');
        });

        arrowCenter.addEventListener('click', () => {
            if (!gameState.battleStarted) return;
            gameState.selectedSector = 'center';
            showSoldierModal('центр');
        });

        arrowRight.addEventListener('click', () => {
            if (!gameState.battleStarted) return;
            gameState.selectedSector = 'right';
            showSoldierModal('правый фланг');
        });

        // Показать модальное окно для ввода солдат
        function showSoldierModal(sectorName) {
            modalTitle.textContent = `Отправка подкреплений на ${sectorName}`;
            soldierInput.value = Math.min(10000, gameState.blueSoldiers);
            soldierInput.max = gameState.blueSoldiers;
            soldierModal.style.display = 'flex';
        }

        // Подтверждение отправки солдат
        confirmDeploy.addEventListener('click', () => {
            const soldierCount = parseInt(soldierInput.value);
            
            if (isNaN(soldierCount) || soldierCount < 1000) {
                alert('Минимальное количество солдат для отправки - 1000!');
                return;
            }
            
            if (soldierCount > gameState.blueSoldiers) {
                alert('Недостаточно солдат для отправки!');
                return;
            }
            
            // Отправляем солдат на выбранный участок
            gameState.blueDeployment[gameState.selectedSector] += soldierCount;
            gameState.blueSoldiers -= soldierCount;
            
            addLogMessage(`Отправлено ${soldierCount.toLocaleString()} солдат на ${getSectorName(gameState.selectedSector)}.`, 'blue-text');
            
            updateUI();
            soldierModal.style.display = 'none';
        });

        // Отмена отправки солдат
        cancelDeploy.addEventListener('click', () => {
            soldierModal.style.display = 'none';
        });

        // Добавить сообщение в лог
        function addLogMessage(message, cssClass = '') {
            const logEntry = document.createElement('div');
            logEntry.innerHTML = message;
            if (cssClass) logEntry.className = cssClass;
            battleLog.appendChild(logEntry);
            battleLog.scrollTop = battleLog.scrollHeight;
        }
    </script>
</body>
</html>
