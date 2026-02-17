<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Война Тысячи Тысяч — Линия Фронта</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }
        
        body {
            background: linear-gradient(145deg, #0a0a0a 0%, #1a1a1a 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Courier New', monospace;
        }
        
        #gameWrapper {
            position: relative;
            box-shadow: 0 0 30px rgba(255, 0, 0, 0.5);
            border-radius: 20px;
            overflow: hidden;
            border: 3px solid #8b0000;
        }
        
        canvas {
            display: block;
            background: #1e3a1e;
            cursor: crosshair;
        }
        
        /* Главное меню */
        #mainMenu {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(10, 10, 10, 0.95);
            backdrop-filter: blur(5px);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10;
            border: 3px solid #8b0000;
            border-radius: 20px;
        }
        
        #mainMenu h1 {
            color: #ff0000;
            font-size: 64px;
            text-shadow: 0 0 30px #8b0000;
            margin-bottom: 20px;
            letter-spacing: 4px;
            font-family: 'Courier New', monospace;
            border-right: 4px solid #ff0000;
            padding-right: 10px;
            animation: blink 1s infinite;
        }
        
        @keyframes blink {
            0%, 100% { border-color: #ff0000; }
            50% { border-color: transparent; }
        }
        
        .menuButtons {
            display: flex;
            gap: 20px;
            margin-top: 40px;
        }
        
        .menuBtn {
            background: transparent;
            color: #ff0000;
            border: 2px solid #ff0000;
            padding: 15px 40px;
            font-size: 24px;
            font-family: 'Courier New', monospace;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 10px rgba(255, 0, 0, 0.5);
        }
        
        .menuBtn:hover {
            background: #ff0000;
            color: #000000;
            box-shadow: 0 0 50px #ff0000;
            transform: scale(1.1);
        }
        
        /* Интерфейс */
        #gameUI {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            color: #ff0000;
            font-size: 20px;
            text-shadow: 0 0 10px #8b0000;
            background: rgba(0, 0, 0, 0.7);
            padding: 15px 25px;
            border-radius: 50px;
            border: 2px solid #8b0000;
            backdrop-filter: blur(5px);
            z-index: 5;
            font-family: 'Courier New', monospace;
        }
        
        .statBlock {
            display: flex;
            gap: 30px;
        }
        
        .statItem {
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .dot {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            display: inline-block;
        }
        
        .dot.green { background: #00ff00; box-shadow: 0 0 15px #00ff00; }
        .dot.red { background: #ff0000; box-shadow: 0 0 15px #ff0000; }
        .dot.yellow { background: #ffff00; box-shadow: 0 0 15px #ffff00; }
        
        #pauseBtn {
            background: transparent;
            color: #ff0000;
            border: 2px solid #ff0000;
            padding: 5px 20px;
            font-family: 'Courier New', monospace;
            font-size: 18px;
            cursor: pointer;
            transition: 0.3s;
            border-radius: 30px;
        }
        
        #pauseBtn:hover {
            background: #ff0000;
            color: #000000;
        }
        
        /* Экран поражения */
        #gameOverScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            border: 3px solid #ff0000;
            border-radius: 20px;
            padding: 40px;
            text-align: center;
            color: #ff0000;
            z-index: 20;
            display: none;
            backdrop-filter: blur(5px);
        }
        
        #gameOverScreen h2 {
            font-size: 48px;
            margin-bottom: 20px;
            text-shadow: 0 0 20px #ff0000;
        }
    </style>
</head>
<body>
    <div id="gameWrapper">
        <canvas id="gameCanvas" width="1400" height="800"></canvas>
        
        <!-- Главное меню -->
        <div id="mainMenu">
            <h1>ЛИНИЯ ФРОНТА</h1>
            <div style="color: #ff0000; font-size: 20px; margin-bottom: 30px; text-align: center;">
                <p>⚔️ ЖЕЛТЫЕ ТОЧКИ = БАЗЫ ⚔️</p>
                <p style="font-size: 16px; margin-top: 10px;">Из них по КД выходят юниты</p>
                <p style="font-size: 14px; color: #ff6666;">Зеленые слева, Красные справа</p>
            </div>
            <div class="menuButtons">
                <button class="menuBtn" onclick="startGame()">НАЧАТЬ БИТВУ</button>
                <button class="menuBtn" onclick="location.reload()">ВЫХОД</button>
            </div>
        </div>
        
        <!-- Интерфейс -->
        <div id="gameUI" style="display: none;">
            <div class="statBlock">
                <div class="statItem">
                    <span class="dot green"></span>
                    <span id="playerScore">0</span>
                </div>
                <div class="statItem">
                    <span class="dot red"></span>
                    <span id="enemyScore">0</span>
                </div>
                <div class="statItem">
                    <span>⚫</span>
                    <span id="blackLinePos">50%</span>
                </div>
            </div>
            <button id="pauseBtn" onclick="togglePause()">ПАУЗА</button>
        </div>
        
        <!-- Экран поражения -->
        <div id="gameOverScreen">
            <h2>ЛИНИЯ ДОШЛА ДО БАЗ</h2>
            <p style="font-size: 24px; margin: 20px 0;">Враг прорвал оборону</p>
            <button class="menuBtn" onclick="restartGame()">НАЧАТЬ СНАЧАЛА</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // Состояние игры
        let gameRunning = false;
        let paused = false;
        let playerBases = [];
        let enemyBases = [];
        let units = [];
        let particles = [];
        let lastSpawnTime = 0;
        const SPAWN_COOLDOWN = 2000; // 2 секунды между спавнами
        
        // Статистика
        let playerScore = 0;
        let enemyScore = 0;
        
        // Линия
        let linePoints = [];
        const SEGMENTS = 40;
        
        // Класс базы - ЖЕЛТЫЕ ТОЧКИ
        class Base {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type; // 'player' или 'enemy'
                this.health = 500;
                this.maxHealth = 500;
                this.radius = 25; // Маленькие точки
                this.pulsePhase = Math.random() * Math.PI * 2;
                this.spawnTimer = 0;
            }
            
            update() {
                this.pulsePhase += 0.05;
            }
            
            draw() {
                ctx.save();
                
                // Пульсация
                ctx.shadowColor = '#ffff00';
                ctx.shadowBlur = 20 + Math.sin(this.pulsePhase) * 10;
                
                // Желтая база
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                const gradient = ctx.createRadialGradient(this.x - 5, this.y - 5, 5, this.x, this.y, 30);
                gradient.addColorStop(0, '#ffff00');
                gradient.addColorStop(1, '#cc9900');
                
                ctx.fillStyle = gradient;
                ctx.fill();
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 2;
                ctx.stroke();
                
                ctx.restore();
            }
            
            takeDamage(amount) {
                this.health -= amount;
                return this.health <= 0;
            }
        }
        
        // Класс юнита - ОЧЕНЬ МЕДЛЕННЫЕ
        class Unit {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type;
                this.speed = 0.2; // ОЧЕНЬ МЕДЛЕННО
                this.health = 100;
                this.maxHealth = 100;
                this.damage = 15;
                this.attackCooldown = 0;
                this.attackRange = 40;
                this.radius = 10;
                this.isSelected = false;
                this.inCombat = false;
            }
            
            update() {
                if (this.attackCooldown > 0) {
                    this.attackCooldown--;
                }
                
                // Находим ближайшую точку линии
                let targetX = linePoints[0].x;
                for (let point of linePoints) {
                    if (Math.abs(point.y - this.y) < 20) {
                        targetX = point.x;
                        break;
                    }
                }
                
                // Движение к линии
                if (this.type === 'player') {
                    // Игроки идут к линии справа
                    if (this.x > targetX + 5) {
                        this.x -= this.speed;
                    }
                } else {
                    // Враги идут к линии слева
                    if (this.x < targetX - 5) {
                        this.x += this.speed;
                    }
                }
                
                // Ограничение по краям
                this.x = Math.max(20, Math.min(canvas.width - 20, this.x));
                this.y = Math.max(20, Math.min(canvas.height - 20, this.y));
            }
            
            draw() {
                ctx.save();
                
                // Здоровье
                const healthPercent = this.health / this.maxHealth;
                
                // Цвет
                if (this.type === 'player') {
                    ctx.fillStyle = '#00ff00';
                } else {
                    ctx.fillStyle = '#ff3333';
                }
                
                // Тень
                ctx.shadowColor = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.shadowBlur = 10;
                
                // Юнит
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fill();
                
                // Обводка
                ctx.strokeStyle = this.isSelected ? '#ffd700' : 'white';
                ctx.lineWidth = this.isSelected ? 2 : 1;
                ctx.stroke();
                
                // Полоска здоровья
                ctx.shadowBlur = 0;
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 8, this.y - 15, 16, 3);
                ctx.fillStyle = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.fillRect(this.x - 8, this.y - 15, 16 * healthPercent, 3);
                
                ctx.restore();
            }
            
            attack(target) {
                if (this.attackCooldown <= 0) {
                    target.health -= this.damage;
                    this.attackCooldown = 30;
                    
                    particles.push(new Particle(
                        target.x, target.y,
                        this.type === 'player' ? '#00ff00' : '#ff0000'
                    ));
                    
                    return true;
                }
                return false;
            }
        }
        
        // Класс частиц
        class Particle {
            constructor(x, y, color) {
                this.x = x;
                this.y = y;
                this.vx = (Math.random() - 0.5) * 6;
                this.vy = (Math.random() - 0.5) * 6;
                this.color = color;
                this.life = 1;
                this.size = Math.random() * 4 + 2;
            }
            
            update() {
                this.x += this.vx;
                this.y += this.vy;
                this.life -= 0.03;
                return this.life <= 0;
            }
            
            draw() {
                ctx.save();
                ctx.globalAlpha = this.life;
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fill();
                ctx.restore();
            }
        }
        
        // Инициализация
        function initGame() {
            // ЖЕЛТЫЕ БАЗЫ - по 3 с каждой стороны
            playerBases = [
                new Base(100, 200, 'player'),
                new Base(100, 400, 'player'),
                new Base(100, 600, 'player')
            ];
            
            enemyBases = [
                new Base(1300, 300, 'enemy'),
                new Base(1300, 500, 'enemy'),
                new Base(1300, 700, 'enemy')
            ];
            
            units = [];
            particles = [];
            lastSpawnTime = Date.now();
            
            // Инициализация линии (прямая посередине)
            linePoints = [];
            for (let i = 0; i <= SEGMENTS; i++) {
                linePoints.push({
                    x: canvas.width / 2,
                    y: (i / SEGMENTS) * canvas.height
                });
            }
        }
        
        // Запуск
        window.startGame = function() {
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameUI').style.display = 'flex';
            initGame();
            gameRunning = true;
            gameLoop();
        };
        
        window.restartGame = function() {
            document.getElementById('gameOverScreen').style.display = 'none';
            startGame();
        };
        
        window.togglePause = function() {
            paused = !paused;
            document.getElementById('pauseBtn').textContent = paused ? 'ПРОДОЛЖИТЬ' : 'ПАУЗА';
        };
        
        // Обновление UI
        function updateUI() {
            const playerUnits = units.filter(u => u.type === 'player').length;
            const enemyUnits = units.filter(u => u.type === 'enemy').length;
            
            document.getElementById('playerScore').textContent = playerUnits;
            document.getElementById('enemyScore').textContent = enemyUnits;
            
            let avgX = 0;
            linePoints.forEach(p => avgX += p.x);
            avgX /= linePoints.length;
            const linePercent = Math.floor((avgX / canvas.width) * 100);
            document.getElementById('blackLinePos').textContent = linePercent + '%';
        }
        
        // Спавн юнитов из желтых баз по КД
        function spawnUnitsFromBases() {
            if (!gameRunning || paused) return;
            
            const currentTime = Date.now();
            if (currentTime - lastSpawnTime < SPAWN_COOLDOWN) return;
            
            lastSpawnTime = currentTime;
            
            // Спавним юнитов из каждой базы
            for (let base of playerBases) {
                // Спавним юнита справа от линии
                let lineX = linePoints[Math.floor(Math.random() * linePoints.length)].x;
                let spawnX = Math.max(lineX + 30, base.x + 20);
                
                let unit = new Unit(spawnX, base.y + (Math.random() - 0.5) * 30, 'player');
                units.push(unit);
            }
            
            for (let base of enemyBases) {
                // Спавним юнита слева от линии
                let lineX = linePoints[Math.floor(Math.random() * linePoints.length)].x;
                let spawnX = Math.min(lineX - 30, base.x - 20);
                
                let unit = new Unit(spawnX, base.y + (Math.random() - 0.5) * 30, 'enemy');
                units.push(unit);
            }
        }
        
        // Обновление боя
        function updateCombat() {
            units.forEach(u => u.inCombat = false);
            
            for (let i = 0; i < units.length; i++) {
                for (let j = i + 1; j < units.length; j++) {
                    const u1 = units[i];
                    const u2 = units[j];
                    
                    if (u1.type !== u2.type) {
                        const dx = u1.x - u2.x;
                        const dy = u1.y - u2.y;
                        const dist = Math.sqrt(dx*dx + dy*dy);
                        
                        if (dist < u1.attackRange + u2.attackRange) {
                            u1.inCombat = true;
                            u2.inCombat = true;
                            
                            if (u1.attackCooldown <= 0) {
                                u1.attack(u2);
                            }
                            if (u2.attackCooldown <= 0) {
                                u2.attack(u1);
                            }
                        }
                    }
                }
            }
            
            units = units.filter(u => {
                if (u.health <= 0) {
                    for (let p = 0; p < 8; p++) {
                        particles.push(new Particle(
                            u.x, u.y,
                            u.type === 'player' ? '#00ff00' : '#ff0000'
                        ));
                    }
                    return false;
                }
                return true;
            });
        }
        
        // Механика линии - двигается от перевеса
        function updateLine() {
            let playerCount = units.filter(u => u.type === 'player').length;
            let enemyCount = units.filter(u => u.type === 'enemy').length;
            
            // Разница в количестве определяет движение линии
            let diff = playerCount - enemyCount;
            
            // Каждая точка линии двигается в зависимости от перевеса
            for (let i = 0; i <= SEGMENTS; i++) {
                linePoints[i].x += diff * 0.2;
            }
        }
        
        // Атака баз
        function attackBases() {
            for (let i = units.length - 1; i >= 0; i--) {
                const unit = units[i];
                
                if (unit.type === 'enemy') {
                    for (let base of playerBases) {
                        const dx = unit.x - base.x;
                        const dy = unit.y - base.y;
                        if (Math.sqrt(dx*dx + dy*dy) < 40) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                playerBases = playerBases.filter(b => b !== base);
                                for (let p = 0; p < 20; p++) {
                                    particles.push(new Particle(base.x, base.y, '#ff0000'));
                                }
                            }
                            break;
                        }
                    }
                } else {
                    for (let base of enemyBases) {
                        const dx = unit.x - base.x;
                        const dy = unit.y - base.y;
                        if (Math.sqrt(dx*dx + dy*dy) < 40) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                enemyBases = enemyBases.filter(b => b !== base);
                                for (let p = 0; p < 20; p++) {
                                    particles.push(new Particle(base.x, base.y, '#00ff00'));
                                }
                            }
                            break;
                        }
                    }
                }
            }
        }
        
        // Отрисовка линии
        function drawLine() {
            if (linePoints.length < 2) return;
            
            ctx.save();
            
            ctx.beginPath();
            ctx.moveTo(linePoints[0].x, linePoints[0].y);
            
            for (let i = 1; i < linePoints.length; i++) {
                ctx.lineTo(linePoints[i].x, linePoints[i].y);
            }
            
            // Черная линия
            ctx.strokeStyle = '#000000';
            ctx.lineWidth = 6;
            ctx.shadowColor = '#ff0000';
            ctx.shadowBlur = 15;
            ctx.stroke();
            
            ctx.restore();
        }
        
        // Игровой цикл
        function gameLoop() {
            if (!gameRunning) return;
            
            if (!paused) {
                // Обновление
                [...playerBases, ...enemyBases].forEach(base => base.update());
                units.forEach(unit => unit.update());
                
                spawnUnitsFromBases(); // Спавн по КД
                updateCombat();
                attackBases();
                updateLine();
                
                particles = particles.filter(p => !p.update());
                
                // Проверка поражения
                let avgX = 0;
                linePoints.forEach(p => avgX += p.x);
                avgX /= linePoints.length;
                
                if (avgX < 100) {
                    gameRunning = false;
                    document.getElementById('gameOverScreen').style.display = 'block';
                }
                
                updateUI();
            }
            
            // Отрисовка
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Фон
            ctx.fillStyle = '#1e3a1e';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Сетка
            ctx.strokeStyle = '#2a4a2a';
            ctx.lineWidth = 1;
            for (let i = 0; i < canvas.width; i += 50) {
                ctx.beginPath();
                ctx.moveTo(i, 0);
                ctx.lineTo(i, canvas.height);
                ctx.stroke();
            }
            for (let i = 0; i < canvas.height; i += 50) {
                ctx.beginPath();
                ctx.moveTo(0, i);
                ctx.lineTo(canvas.width, i);
                ctx.stroke();
            }
            
            drawLine();
            
            // Базы (ЖЕЛТЫЕ)
            [...playerBases, ...enemyBases].forEach(base => base.draw());
            
            // Юниты
            units.forEach(unit => unit.draw());
            
            // Частицы
            particles.forEach(p => p.draw());
            
            requestAnimationFrame(gameLoop);
        }
        
        initGame();
    </script>
</body>
</html>
