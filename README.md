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
            background: linear-gradient(145deg, #0a0f0a 0%, #1a2f1a 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Courier New', monospace;
        }
        
        #gameWrapper {
            position: relative;
            box-shadow: 0 0 30px rgba(255, 100, 100, 0.3);
            border-radius: 20px;
            overflow: hidden;
            border: 3px solid #5a3a3a;
        }
        
        canvas {
            display: block;
            background: #2a3a2a;
            cursor: crosshair;
        }
        
        /* Главное меню */
        #mainMenu {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(10, 20, 10, 0.95);
            backdrop-filter: blur(5px);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10;
            border: 3px solid #7a5a5a;
            border-radius: 20px;
        }
        
        #mainMenu h1 {
            color: #ff7a7a;
            font-size: 64px;
            text-shadow: 0 0 20px #ff0000;
            margin-bottom: 20px;
            letter-spacing: 4px;
            font-family: 'Courier New', monospace;
            border-right: 4px solid #ff7a7a;
            padding-right: 10px;
            animation: blink 1s infinite;
        }
        
        @keyframes blink {
            0%, 100% { border-color: #ff7a7a; }
            50% { border-color: transparent; }
        }
        
        .menuButtons {
            display: flex;
            gap: 20px;
            margin-top: 40px;
        }
        
        .menuBtn {
            background: transparent;
            color: #ff7a7a;
            border: 2px solid #ff7a7a;
            padding: 15px 40px;
            font-size: 24px;
            font-family: 'Courier New', monospace;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 10px rgba(255, 122, 122, 0.3);
        }
        
        .menuBtn:hover {
            background: #ff7a7a;
            color: #0a1a0a;
            box-shadow: 0 0 30px #ff7a7a;
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
            color: #ff7a7a;
            font-size: 20px;
            text-shadow: 0 0 10px #ff0000;
            background: rgba(30, 20, 20, 0.7);
            padding: 15px 25px;
            border-radius: 50px;
            border: 2px solid #7a5a5a;
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
        
        .dot.green { background: #7aff7a; box-shadow: 0 0 15px #00ff00; }
        .dot.red { background: #ff4f4f; box-shadow: 0 0 15px #ff0000; }
        
        #pauseBtn {
            background: transparent;
            color: #ff7a7a;
            border: 2px solid #ff7a7a;
            padding: 5px 20px;
            font-family: 'Courier New', monospace;
            font-size: 18px;
            cursor: pointer;
            transition: 0.3s;
            border-radius: 30px;
        }
        
        #pauseBtn:hover {
            background: #ff7a7a;
            color: #1a1a1a;
        }
        
        /* Панель управления */
        #controlPanel {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(30, 20, 20, 0.9);
            border: 2px solid #7a5a5a;
            border-radius: 50px;
            padding: 10px 20px;
            display: flex;
            gap: 15px;
            backdrop-filter: blur(5px);
            z-index: 5;
        }
        
        .controlBtn {
            background: transparent;
            color: #ff7a7a;
            border: 1px solid #ff7a7a;
            padding: 8px 20px;
            font-family: 'Courier New', monospace;
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s;
            border-radius: 30px;
        }
        
        .controlBtn:hover {
            background: #ff7a7a;
            color: #1a1a1a;
        }
        
        /* Экран поражения */
        #gameOverScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(30, 10, 10, 0.95);
            border: 3px solid #ff4f4f;
            border-radius: 20px;
            padding: 40px;
            text-align: center;
            color: #ff4f4f;
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
            <div style="color: #ff7a7a; font-size: 20px; margin-bottom: 30px; text-align: center;">
                <p>⚔️ УДЕРЖИ ЛИНИЮ ОБОРОНЫ ⚔️</p>
                <p style="font-size: 16px; margin-top: 10px;">Кликай по базам чтобы создать юнитов</p>
                <p style="font-size: 14px; color: #ff9a9a;">Красная линия — фронт</p>
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
                    <span>📊</span>
                    <span id="frontLinePos">50%</span>
                </div>
            </div>
            <button id="pauseBtn" onclick="togglePause()">ПАУЗА</button>
        </div>
        
        <!-- Экран поражения -->
        <div id="gameOverScreen">
            <h2>ЛИНИЯ ПРОРВАНА</h2>
            <p style="font-size: 24px; margin: 20px 0;">Вы погибли в бою</p>
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
        
        // Статистика
        let playerScore = 0;
        let enemyScore = 0;
        let frontLineX = canvas.width / 2; // Линия фронта
        
        // Класс базы
        class Base {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type;
                this.health = 200;
                this.maxHealth = 200;
                this.units = type === 'player' ? 10 : 15;
                this.maxUnits = 20;
                this.spawnRate = 0.03;
                this.spawnProgress = 0;
                this.radius = 35;
                this.pulsePhase = Math.random() * Math.PI * 2;
            }
            
            update() {
                if (this.units < this.maxUnits) {
                    this.spawnProgress += this.spawnRate;
                    if (this.spawnProgress >= 1) {
                        this.units++;
                        this.spawnProgress = 0;
                    }
                }
                this.pulsePhase += 0.05;
            }
            
            spawnUnit() {
                if (this.units > 0) {
                    this.units--;
                    return new Unit(
                        this.x + (Math.random() - 0.5) * 50,
                        this.y + (Math.random() - 0.5) * 50,
                        this.type
                    );
                }
                return null;
            }
            
            draw() {
                ctx.save();
                
                // Пульсация
                ctx.shadowColor = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.shadowBlur = 30 + Math.sin(this.pulsePhase) * 10;
                
                // База
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                const gradient = ctx.createRadialGradient(this.x - 10, this.y - 10, 5, this.x, this.y, 45);
                if (this.type === 'player') {
                    gradient.addColorStop(0, '#7aff7a');
                    gradient.addColorStop(1, '#2a5a2a');
                } else {
                    gradient.addColorStop(0, '#ff4f4f');
                    gradient.addColorStop(1, '#5a2a2a');
                }
                
                ctx.fillStyle = gradient;
                ctx.fill();
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 3;
                ctx.stroke();
                
                // Количество юнитов
                ctx.shadowBlur = 0;
                ctx.fillStyle = 'white';
                ctx.font = 'bold 24px "Courier New"';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.units, this.x, this.y);
                
                // Полоска здоровья
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 30, this.y - 50, 60, 8);
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.fillRect(this.x - 30, this.y - 50, 60 * healthPercent, 8);
                
                ctx.restore();
            }
            
            takeDamage(amount) {
                this.health -= amount;
                return this.health <= 0;
            }
        }
        
        // Класс юнита
        class Unit {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type;
                this.targetX = type === 'player' ? canvas.width - 100 : 100;
                this.targetY = y;
                this.speed = type === 'player' ? 2 : 2.2;
                this.health = 50;
                this.maxHealth = 50;
                this.damage = 7;
                this.attackCooldown = 0;
                this.attackRange = 25;
                this.radius = 10;
                this.isSelected = false;
                
                // Для построения в линию
                this.inCombat = false;
            }
            
            setTarget(tx, ty) {
                this.targetX = tx;
                this.targetY = ty;
            }
            
            update() {
                if (this.attackCooldown > 0) {
                    this.attackCooldown--;
                }
                
                // Движение к цели
                const dx = this.targetX - this.x;
                const dy = this.targetY - this.y;
                const distance = Math.sqrt(dx*dx + dy*dy);
                
                if (distance > 5 && !this.inCombat) {
                    const moveX = (dx / distance) * this.speed;
                    const moveY = (dy / distance) * this.speed;
                    this.x += moveX;
                    this.y += moveY;
                }
                
                // Ограничение по краям
                this.x = Math.max(20, Math.min(canvas.width - 20, this.x));
                this.y = Math.max(20, Math.min(canvas.height - 20, this.y));
            }
            
            draw() {
                ctx.save();
                
                // Здоровье
                const healthPercent = this.health / this.maxHealth;
                
                // Цвет в зависимости от здоровья
                let color;
                if (this.type === 'player') {
                    color = `rgb(${Math.floor(100 + 155 * (1 - healthPercent))}, 255, ${Math.floor(100 + 155 * (1 - healthPercent))})`;
                } else {
                    color = `rgb(255, ${Math.floor(100 + 155 * (1 - healthPercent))}, ${Math.floor(100 + 155 * (1 - healthPercent))})`;
                }
                
                // Тень
                ctx.shadowColor = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.shadowBlur = 15;
                
                // Юнит
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fillStyle = color;
                ctx.fill();
                
                // Обводка
                ctx.strokeStyle = this.isSelected ? '#ffd700' : 'white';
                ctx.lineWidth = this.isSelected ? 3 : 2;
                ctx.stroke();
                
                // Полоска здоровья
                ctx.shadowBlur = 0;
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 12, this.y - 18, 24, 4);
                ctx.fillStyle = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.fillRect(this.x - 12, this.y - 18, 24 * healthPercent, 4);
                
                // Красная линия впереди для врагов
                if (this.type === 'enemy' && !this.inCombat) {
                    ctx.beginPath();
                    ctx.moveTo(this.x, this.y);
                    ctx.lineTo(this.targetX, this.y);
                    ctx.strokeStyle = '#ff4f4f';
                    ctx.lineWidth = 2;
                    ctx.setLineDash([5, 5]);
                    ctx.stroke();
                    ctx.setLineDash([]);
                }
                
                ctx.restore();
            }
            
            attack(target) {
                if (this.attackCooldown <= 0) {
                    target.health -= this.damage;
                    this.attackCooldown = 20;
                    
                    // Эффект попадания
                    particles.push(new Particle(
                        target.x, target.y,
                        this.type === 'player' ? '#7aff7a' : '#ff4f4f'
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
                this.vx = (Math.random() - 0.5) * 8;
                this.vy = (Math.random() - 0.5) * 8;
                this.color = color;
                this.life = 1;
                this.size = Math.random() * 4 + 2;
            }
            
            update() {
                this.x += this.vx;
                this.y += this.vy;
                this.life -= 0.02;
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
            playerBases = [
                new Base(200, 400, 'player'),
                new Base(200, 600, 'player')
            ];
            
            enemyBases = [
                new Base(1200, 300, 'enemy'),
                new Base(1200, 500, 'enemy'),
                new Base(1200, 700, 'enemy')
            ];
            
            units = [];
            particles = [];
            frontLineX = canvas.width / 2;
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
            const playerBaseUnits = playerBases.reduce((sum, b) => sum + b.units, 0);
            const enemyUnits = units.filter(u => u.type === 'enemy').length;
            const enemyBaseUnits = enemyBases.reduce((sum, b) => sum + b.units, 0);
            
            document.getElementById('playerScore').textContent = playerUnits + playerBaseUnits;
            document.getElementById('enemyScore').textContent = enemyUnits + enemyBaseUnits;
            
            // Позиция линии фронта в процентах
            const frontPercent = Math.floor((frontLineX / canvas.width) * 100);
            document.getElementById('frontLinePos').textContent = frontPercent + '%';
        }
        
        // Спавн врагов
        function spawnEnemies() {
            if (!gameRunning || paused) return;
            
            for (let base of enemyBases) {
                if (Math.random() < 0.02 && base.units > 0) {
                    const unit = base.spawnUnit();
                    if (unit) {
                        // Цель - левая сторона
                        unit.targetX = 100;
                        unit.targetY = 200 + Math.random() * 400;
                        units.push(unit);
                    }
                }
            }
        }
        
        // Обновление боя
        function updateCombat() {
            // Сбрасываем флаг боя
            units.forEach(u => u.inCombat = false);
            
            // Бой между юнитами
            for (let i = 0; i < units.length; i++) {
                for (let j = i + 1; j < units.length; j++) {
                    const u1 = units[i];
                    const u2 = units[j];
                    
                    if (u1.type !== u2.type) {
                        const dx = u1.x - u2.x;
                        const dy = u1.y - u2.y;
                        const dist = Math.sqrt(dx*dx + dy*dy);
                        
                        if (dist < u1.attackRange + u2.attackRange) {
                            // Отмечаем что юниты в бою
                            u1.inCombat = true;
                            u2.inCombat = true;
                            
                            // Атака
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
            
            // Удаление мертвых юнитов
            units = units.filter(u => {
                if (u.health <= 0) {
                    // Взрыв
                    for (let p = 0; p < 5; p++) {
                        particles.push(new Particle(
                            u.x, u.y,
                            u.type === 'player' ? '#7aff7a' : '#ff4f4f'
                        ));
                    }
                    return false;
                }
                return true;
            });
        }
        
        // Обновление линии фронта
        function updateFrontLine() {
            if (units.length === 0) return;
            
            // Находим среднюю позицию врагов и игроков
            let playerX = 0;
            let playerCount = 0;
            let enemyX = 0;
            let enemyCount = 0;
            
            units.forEach(u => {
                if (u.type === 'player') {
                    playerX += u.x;
                    playerCount++;
                } else {
                    enemyX += u.x;
                    enemyCount++;
                }
            });
            
            if (playerCount > 0 && enemyCount > 0) {
                playerX /= playerCount;
                enemyX /= enemyCount;
                
                // Линия фронта посередине между армиями
                frontLineX = (playerX + enemyX) / 2;
            }
        }
        
        // Атака баз
        function attackBases() {
            // Враги атакуют базы игрока
            for (let i = units.length - 1; i >= 0; i--) {
                const unit = units[i];
                
                if (unit.type === 'enemy') {
                    for (let base of playerBases) {
                        const dx = unit.x - base.x;
                        const dy = unit.y - base.y;
                        if (Math.sqrt(dx*dx + dy*dy) < 50) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                playerBases = playerBases.filter(b => b !== base);
                                for (let p = 0; p < 20; p++) {
                                    particles.push(new Particle(base.x, base.y, '#ff4f4f'));
                                }
                            }
                            break;
                        }
                    }
                } else {
                    for (let base of enemyBases) {
                        const dx = unit.x - base.x;
                        const dy = unit.y - base.y;
                        if (Math.sqrt(dx*dx + dy*dy) < 50) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                enemyBases = enemyBases.filter(b => b !== base);
                                for (let p = 0; p < 20; p++) {
                                    particles.push(new Particle(base.x, base.y, '#7aff7a'));
                                }
                            }
                            break;
                        }
                    }
                }
            }
        }
        
        // Игровой цикл
        function gameLoop() {
            if (!gameRunning) return;
            
            if (!paused) {
                // Обновление
                [...playerBases, ...enemyBases].forEach(base => base.update());
                units.forEach(unit => unit.update());
                
                // Спавн врагов
                spawnEnemies();
                
                // Бой
                updateCombat();
                
                // Атака баз
                attackBases();
                
                // Линия фронта
                updateFrontLine();
                
                // Частицы
                particles = particles.filter(p => !p.update());
                
                // Проверка поражения
                if (playerBases.length === 0) {
                    gameRunning = false;
                    document.getElementById('gameOverScreen').style.display = 'block';
                }
                
                updateUI();
            }
            
            // Отрисовка
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Фон (поляна)
            ctx.fillStyle = '#1a2a1a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Сетка
            ctx.strokeStyle = '#2a3a2a';
            ctx.lineWidth = 1;
            for (let i = 0; i < canvas.width; i += 50) {
                ctx.beginPath();
                ctx.moveTo(i, 0);
                ctx.lineTo(i, canvas.height);
                ctx.strokeStyle = '#2a3a2a';
                ctx.stroke();
            }
            for (let i = 0; i < canvas.height; i += 50) {
                ctx.beginPath();
                ctx.moveTo(0, i);
                ctx.lineTo(canvas.width, i);
                ctx.stroke();
            }
            
            // ЛИНИЯ ФРОНТА (основная фишка!)
            ctx.save();
            ctx.beginPath();
            ctx.moveTo(frontLineX, 0);
            ctx.lineTo(frontLineX, canvas.height);
            ctx.strokeStyle = '#ff4f4f';
            ctx.lineWidth = 4;
            ctx.setLineDash([20, 20]);
            ctx.shadowColor = '#ff0000';
            ctx.shadowBlur = 20;
            ctx.stroke();
            
            // Подпись линии
            ctx.shadowBlur = 0;
            ctx.font = 'bold 20px "Courier New"';
            ctx.fillStyle = '#ff4f4f';
            ctx.fillText('ЛИНИЯ ФРОНТА', frontLineX - 100, 50);
            ctx.restore();
            
            // Базы
            [...playerBases, ...enemyBases].forEach(base => base.draw());
            
            // Юниты
            units.forEach(unit => unit.draw());
            
            // Частицы
            particles.forEach(p => p.draw());
            
            requestAnimationFrame(gameLoop);
        }
        
        // Обработка кликов
        canvas.addEventListener('mousedown', (e) => {
            if (!gameRunning || paused) return;
            
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            
            const mouseX = (e.clientX - rect.left) * scaleX;
            const mouseY = (e.clientY - rect.top) * scaleY;
            
            if (e.button === 0) { // ЛКМ
                // Проверка баз
                for (let base of playerBases) {
                    const dx = mouseX - base.x;
                    const dy = mouseY - base.y;
                    if (Math.sqrt(dx*dx + dy*dy) < base.radius) {
                        if (base.units > 0) {
                            for (let i = 0; i < 3; i++) {
                                const unit = base.spawnUnit();
                                if (unit) {
                                    unit.targetX = canvas.width - 200;
                                    unit.targetY = 200 + Math.random() * 400;
                                    units.push(unit);
                                }
                            }
                        }
                        return;
                    }
                }
                
                // Выделение юнитов
                let clickedUnit = null;
                for (let unit of units) {
                    if (unit.type === 'player') {
                        const dx = mouseX - unit.x;
                        const dy = mouseY - unit.y;
                        if (Math.sqrt(dx*dx + dy*dy) < unit.radius + 5) {
                            clickedUnit = unit;
                            break;
                        }
                    }
                }
                
                if (clickedUnit) {
                    if (!e.shiftKey) {
                        units.forEach(u => u.isSelected = false);
                    }
                    clickedUnit.isSelected = !clickedUnit.isSelected;
                } else {
                    // Отправка выделенных
                    const selected = units.filter(u => u.isSelected);
                    selected.forEach(unit => {
                        unit.setTarget(mouseX, mouseY);
                    });
                }
            }
        });
        
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        
        initGame();
    </script>
</body>
</html>
