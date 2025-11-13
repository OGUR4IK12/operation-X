# operation-X
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>War Sodium</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        .container {
            max-width: 1000px;
            width: 100%;
            background: rgba(0, 0, 0, 0.7);
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            text-align: center;
        }

        h1 {
            font-size: 3rem;
            margin-bottom: 10px;
            text-shadow: 0 0 10px #00a8ff;
            color: #fdbb2d;
        }

        h2 {
            font-size: 1.5rem;
            margin-bottom: 20px;
            color: #00a8ff;
        }

        .subtitle {
            font-size: 1.2rem;
            margin-bottom: 30px;
            color: #fdbb2d;
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
        }

        .play-btn {
            background: linear-gradient(to right, #00b09b, #96c93d);
            color: white;
            padding: 15px 40px;
            font-size: 1.3rem;
        }

        .easy-btn {
            background: linear-gradient(to right, #00b09b, #96c93d);
            color: white;
        }

        .medium-btn {
            background: linear-gradient(to right, #ff9a00, #ff5e00);
            color: white;
        }

        .hard-btn {
            background: linear-gradient(to right, #ff416c, #ff4b2b);
            color: white;
        }

        .insane-btn {
            background: linear-gradient(to right, #8e2de2, #4a00e0);
            color: white;
        }

        button:hover {
            transform: translateY(-3px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
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
        }

        .blue-team {
            background: rgba(0, 120, 255, 0.3);
            border: 2px solid #0078ff;
        }

        .red-team {
            background: rgba(255, 0, 0, 0.3);
            border: 2px solid #ff0000;
        }

        .battlefield {
            width: 100%;
            height: 400px;
            background: linear-gradient(to bottom, #2c3e50, #4a6491);
            border-radius: 10px;
            position: relative;
            overflow: hidden;
            margin: 20px 0;
            border: 3px solid #34495e;
        }

        .front-line {
            position: absolute;
            top: 0;
            bottom: 0;
            width: 5px;
            background: linear-gradient(to bottom, #fdbb2d, #ff5e00);
            left: 50%;
            transform: translateX(-50%);
            transition: left 2s ease;
            box-shadow: 0 0 10px #fdbb2d;
        }

        .territory {
            position: absolute;
            top: 0;
            bottom: 0;
            transition: width 2s ease;
        }

        .blue-territory {
            left: 0;
            background: rgba(0, 120, 255, 0.3);
            width: 50%;
        }

        .red-territory {
            right: 0;
            background: rgba(255, 0, 0, 0.3);
            width: 50%;
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
        }

        .recruit-btn:disabled {
            background: #7f8c8d;
            cursor: not-allowed;
            transform: none;
        }

        .recruit-btn:disabled:hover {
            transform: none;
            box-shadow: none;
        }

        .countdown {
            font-size: 1.5rem;
            color: #fdbb2d;
            margin: 10px 0;
        }

        .deployment {
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
            margin-top: 20px;
        }

        .deploy-btn {
            background: linear-gradient(to right, #00b09b, #96c93d);
            color: white;
            flex: 1;
            min-width: 120px;
        }

        .battle-log {
            width: 100%;
            height: 100px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 8px;
            padding: 10px;
            overflow-y: auto;
            margin-top: 20px;
            text-align: left;
            font-size: 0.9rem;
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
        }

        @media (max-width: 768px) {
            h1 {
                font-size: 2rem;
            }
            
            .battlefield {
                height: 300px;
            }
            
            .game-info {
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
            <h1>War Sodium</h1>
            <h2>Мини-игра создана на 96% с помощью DeepSeek</h2>
            <p class="subtitle">Стратегическая битва за территорию</p>
            <button class="play-btn" id="playBtn">Играть</button>
        </div>

        <!-- Экран выбора сложности -->
        <div class="game-screen hidden" id="gameScreen">
            <h2>Выберите уровень сложности</h2>
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
            <h2>Битва за территорию</h2>
            
            <div class="game-info">
                <div class="blue-team">
                    Синие (Вы): <span id="blueSoldiers">0</span> солдат
                </div>
                <div class="red-team">
                    Красные (ИИ): <span id="redSoldiers">0</span> солдат
                </div>
            </div>

            <div class="countdown" id="countdown">До начала битвы: 30</div>

            <div class="battlefield">
                <div class="territory blue-territory" id="blueTerritory"></div>
                <div class="front-line" id="frontLine"></div>
                <div class="territory red-territory" id="redTerritory"></div>
            </div>

            <div class="controls">
                <button class="recruit-btn" id="recruitBtn">
                    Нанять солдат (+15K)
                    <div>Перезарядка: <span id="cooldown">5</span> сек</div>
                </button>

                <div class="deployment">
                    <button class="deploy-btn" data-sector="left">Левый фланг</button>
                    <button class="deploy-btn" data-sector="center">Центр</button>
                    <button class="deploy-btn" data-sector="right">Правый фланг</button>
                </div>
            </div>

            <div class="battle-log" id="battleLog">
                <div class="yellow-text">Битва начнется через 30 секунд...</div>
            </div>

            <button class="back-btn" id="backToDifficulty">Назад к выбору сложности</button>
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
        const frontLine = document.getElementById('frontLine');
        const battleLog = document.getElementById('battleLog');
        const deployButtons = document.querySelectorAll('.deploy-btn');

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
            territory: 50 // процент территории синих
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
            battleLog.innerHTML = '<div class="yellow-text">Битва начнется через 30 секунд...</div>';
            
            // Добавить сообщение о сложности
            addLogMessage(`Выбран уровень сложности: ${gameState.difficulty}. У противника ${gameState.redSoldiers.toLocaleString()} солдат.`, 'yellow-text');
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
                territory: 50
            };
        }

        // Обновление интерфейса
        function updateUI() {
            blueSoldiersElement.textContent = gameState.blueSoldiers.toLocaleString();
            redSoldiersElement.textContent = gameState.redSoldiers.toLocaleString();
            
            // Обновление территории
            blueTerritory.style.width = `${gameState.territory}%`;
            redTerritory.style.width = `${100 - gameState.territory}%`;
            frontLine.style.left = `${gameState.territory}%`;
            
            // Обновление кнопки найма
            if (gameState.recruitCooldown > 0) {
                recruitBtn.disabled = true;
                cooldownElement.textContent = gameState.recruitCooldown;
            } else {
                recruitBtn.disabled = false;
                cooldownElement.textContent = '0';
            }
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
            countdownElement.textContent = 'БИТВА НАЧАЛАСЬ!';
            addLogMessage('=== БИТВА НАЧАЛАСЬ! ===', 'yellow-text');
            
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
            
            addLogMessage(`ИИ распределил силы: Левый фланг ${left.toLocaleString()}, Центр ${center.toLocaleString()}, Правый фланг ${right.toLocaleString()}`, 'red-text');
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
            sectors.forEach(sector => {
                const blue = blueForces[sector] || 0;
                const red = redForces[sector] || 0;
                
                if (blue > red * 1.5) {
                    // Синие окружают красных на этом участке
                    blueEffectiveness += 2;
                    addLogMessage(`Синие окружают красных на ${getSectorName(sector)}!`, 'blue-text');
                } else if (red > blue * 1.5) {
                    // Красные окружают синих на этом участке
                    redEffectiveness += 2;
                    addLogMessage(`Красные окружают синих на ${getSectorName(sector)}!`, 'red-text');
                } else if (blue > red) {
                    blueEffectiveness += 1;
                } else if (red > blue) {
                    redEffectiveness += 1;
                }
            });
            
            // Изменение территории на основе эффективности
            if (blueEffectiveness > redEffectiveness) {
                gameState.territory += 1;
                addLogMessage('Сильные продвигаются вперед!', 'blue-text');
            } else if (redEffectiveness > blueEffectiveness) {
                gameState.territory -= 1;
                addLogMessage('Красные продвигаются вперед!', 'red-text');
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
            
            if (winner === 'blue') {
                addLogMessage('=== ПОБЕДА! Вы захватили 90% территории! ===', 'blue-text');
                alert('ПОБЕДА! Вы захватили 90% территории противника!');
            } else {
                addLogMessage('=== ПОРАЖЕНИЕ! Вы потеряли 90% территории! ===', 'red-text');
                alert('ПОРАЖЕНИЕ! Вы потеряли 90% своей территории!');
            }
        }

        // Наем солдат
        recruitBtn.addEventListener('click', () => {
            if (gameState.recruitCooldown > 0) return;
            
            gameState.blueSoldiers += 15000;
            gameState.recruitCooldown = 5;
            updateUI();
            addLogMessage('Нанято 15,000 солдат!', 'blue-text');
            
            // Запустить перезарядку
            const cooldownInterval = setInterval(() => {
                gameState.recruitCooldown--;
                updateUI();
                
                if (gameState.recruitCooldown <= 0) {
                    clearInterval(cooldownInterval);
                }
            }, 1000);
        });

        // Развертывание солдат
        deployButtons.forEach(button => {
            button.addEventListener('click', () => {
                const sector = button.dataset.sector;
                
                if (gameState.blueSoldiers < 5000) {
                    addLogMessage('Недостаточно солдат для развертывания! Нужно минимум 5,000.', 'yellow-text');
                    return;
                }
                
                // Развернуть 25% доступных солдат
                const deployCount = Math.max(5000, Math.floor(gameState.blueSoldiers * 0.25));
                gameState.blueDeployment[sector] += deployCount;
                gameState.blueSoldiers -= deployCount;
                
                addLogMessage(`Развернуто ${deployCount.toLocaleString()} солдат на ${getSectorName(sector)}.`, 'blue-text');
                updateUI();
            });
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
