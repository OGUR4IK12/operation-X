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
                <p>⚔️ ЮНИТЫ НЕ МОГУТ ПЕРЕСЕЧЬ ЛИНИЮ ⚔️</p>
                <p style="font-size: 16px; margin-top: 10px;">Кликни по ЗЕЛЕНОЙ базе чтобы создать юнита</p>
                <p style="font-size: 14px; color: #ff6666;">Линия двигается от давления масс</p>
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
        
        // Статистика
        let playerScore = 0;
        let enemyScore = 0;
        
        // Черная линия - ПРЯМАЯ ВЕРТИКАЛЬНАЯ
        let lineX = canvas.width / 2; // Начинается по середине
        let targetLineX = canvas.width / 2;
        
        // Класс базы
        class Base {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type;
                this.health = 500;
                this.maxHealth = 500;
                this.units = type === 'player' ? 40 : 45;
                this.maxUnits = 80;
                this.spawnRate = 0.08;
                this.spawnProgress = 0;
                this.radius = 50;
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
                        this.x + (Math.random() - 0.5) * 40,
                        this.y + (Math.random() - 0.5) * 40,
                        this.type
                    );
                }
                return null;
            }
            
            draw() {
                ctx.save();
                
                // Пульсация
                ctx.shadowColor = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.shadowBlur = 30 + Math.sin(this.pulsePhase) * 10;
                
                // База
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                const gradient = ctx.createRadialGradient(this.x - 10, this.y - 10, 5, this.x, this.y, 55);
                if (this.type === 'player') {
                    gradient.addColorStop(0, '#00ff00');
                    gradient.addColorStop(1, '#006400');
                } else {
                    gradient.addColorStop(0, '#ff0000');
                    gradient.addColorStop(1, '#8b0000');
                }
                
                ctx.fillStyle = gradient;
                ctx.fill();
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 3;
                ctx.stroke();
                
                // Количество юнитов
                ctx.shadowBlur = 0;
                ctx.fillStyle = 'white';
                ctx.font = 'bold 28px "Courier New"';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.units, this.x, this.y);
                
                // Полоска здоровья
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 35, this.y - 70, 70, 8);
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.fillRect(this.x - 35, this.y - 70, 70 * healthPercent, 8);
                
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
                this.targetX = type === 'player' ? canvas.width - 200 : 100;
                this.targetY = y;
                this.speed = type === 'player' ? 1.0 : 1.2;
                this.health = 200;
                this.maxHealth = 200;
                this.damage = 25;
                this.attackCooldown = 0;
                this.attackRange = 60;
                this.radius = 18;
                this.isSelected = false;
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
                
                // ГЛАВНОЕ: юниты НЕ МОГУТ ПЕРЕСЕЧЬ ЛИНИЮ
                // Если юнит игрока справа от линии - он не может идти левее линии
                if (this.type === 'player' && this.targetX < lineX) {
                    this.targetX = lineX + 10; // Останавливается перед линией
                }
                
                // Если враг слева от линии - он не может идти правее линии
                if (this.type === 'enemy' && this.targetX > lineX) {
                    this.targetX = lineX - 10; // Останавливается перед линией
                }
                
                // Движение к цели
                const dx = this.targetX - this.x;
                const dy = this.targetY - this.y;
                const distance = Math.sqrt(dx*dx + dy*dy);
                
                if (distance > 5 && !this.inCombat) {
                    const moveX = (dx / distance) * this.speed;
                    const moveY = (dy / distance) * this.speed;
                    
                    // Проверка: не пересечет ли движение линию?
                    const newX = this.x + moveX;
                    
                    // Игрок не может пересечь линию справа налево
                    if (this.type === 'player' && newX < lineX) {
                        this.x = lineX; // Останавливается точно на линии
                    } 
                    // Враг не может пересечь линию слева направо
                    else if (this.type === 'enemy' && newX > lineX) {
                        this.x = lineX; // Останавливается точно на линии
                    }
                    else {
                        this.x = newX;
                        this.y += moveY;
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
                ctx.shadowBlur = 15;
                
                // Юнит
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fill();
                
                // Обводка
                ctx.strokeStyle = this.isSelected ? '#ffd700' : 'white';
                ctx.lineWidth = this.isSelected ? 3 : 2;
                ctx.stroke();
                
                // Полоска здоровья
                ctx.shadowBlur = 0;
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 15, this.y - 30, 30, 5);
                ctx.fillStyle = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.fillRect(this.x - 15, this.y - 30, 30 * healthPercent, 5);
                
                ctx.restore();
            }
            
            attack(target) {
                if (this.attackCooldown <= 0) {
                    target.health -= this.damage;
                    this.attackCooldown = 40;
                    
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
                this.vx = (Math.random() - 0.5) * 8;
                this.vy = (Math.random() - 0.5) * 8;
                this.color = color;
                this.life = 1;
                this.size = Math.random() * 5 + 3;
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
            playerBases = [
                new Base(150, 300, 'player'),
                new Base(150, 500, 'player')
            ];
            
            enemyBases = [
                new Base(1250, 350, 'enemy'),
                new Base(1250, 550, 'enemy')
            ];
            
            units = [];
            particles = [];
            
            // Линия строго по середине
            lineX = canvas.width / 2;
            targetLineX = canvas.width / 2;
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
            
            const linePercent = Math.floor((lineX / canvas.width) * 100);
            document.getElementById('blackLinePos').textContent = linePercent + '%';
        }
        
        // Спавн врагов
        function spawnEnemies() {
            if (!gameRunning || paused) return;
            
            for (let base of enemyBases) {
                if (Math.random() < 0.04 && base.units > 0) {
                    const unit = base.spawnUnit();
                    if (unit) {
                        unit.targetX = lineX - 30; // Идут к линии
                        unit.targetY = 200 + Math.random() * 400;
                        units.push(unit);
                    }
                }
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
                    for (let p = 0; p < 10; p++) {
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
        
        // ИСПРАВЛЕННАЯ МЕХАНИКА ЛИНИИ
        function updateLine() {
            // Считаем количество юнитов с каждой стороны
            let playerCount = 0;
            let enemyCount = 0;
            
            for (let unit of units) {
                if (unit.type === 'player') {
                    playerCount++;
                } else {
                    enemyCount++;
                }
            }
            
            // Разница в количестве
            let diff = enemyCount - playerCount;
            
            // Линия двигается в зависимости от перевеса
            // Если врагов больше - линия идет ВЛЕВО (к игроку)
            // Если игроков больше - линия идет ВПРАВО (к врагу)
            targetLineX += diff * 0.3;
            
            // Ограничиваем движение линии
            targetLineX = Math.max(250, Math.min(1150, targetLineX));
            
            // Плавно двигаем линию
            lineX += (targetLineX - lineX) * 0.05;
        }
        
        // Атака баз
        function attackBases() {
            for (let i = units.length - 1; i >= 0; i--) {
                const unit = units[i];
                
                if (unit.type === 'enemy') {
                    for (let base of playerBases) {
                        const dx = unit.x - base.x;
                        const dy = unit.y - base.y;
                        if (Math.sqrt(dx*dx + dy*dy) < 90) {
                            base.health -= unit.damage * 2;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                playerBases = playerBases.filter(b => b !== base);
                                for (let p = 0; p < 40; p++) {
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
                        if (Math.sqrt(dx*dx + dy*dy) < 90) {
                            base.health -= unit.damage * 2;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                enemyBases = enemyBases.filter(b => b !== base);
                                for (let p = 0; p < 40; p++) {
                                    particles.push(new Particle(base.x, base.y, '#00ff00'));
                                }
                            }
                            break;
                        }
                    }
                }
            }
        }
        
        // Отрисовка линии (ПРЯМАЯ)
        function drawLine() {
            ctx.save();
            
            // Прямая вертикальная линия
            ctx.beginPath();
            ctx.moveTo(lineX, 20);
            ctx.lineTo(lineX, canvas.height - 20);
            
            // Толстая черная линия
            ctx.strokeStyle = '#000000';
            ctx.lineWidth = 8;
            ctx.shadowColor = '#000000';
            ctx.shadowBlur = 20;
            ctx.stroke();
            
            // Белая окантовка
            ctx.beginPath();
            ctx.moveTo(lineX, 20);
            ctx.lineTo(lineX, canvas.height - 20);
            ctx.strokeStyle = '#ffffff';
            ctx.lineWidth = 2;
            ctx.shadowBlur = 0;
            ctx.stroke();
            
            // Красное свечение
            ctx.beginPath();
            ctx.moveTo(lineX, 20);
            ctx.lineTo(lineX, canvas.height - 20);
            ctx.strokeStyle = '#ff0000';
            ctx.lineWidth = 1;
            ctx.shadowColor = '#ff0000';
            ctx.shadowBlur = 15;
            ctx.stroke();
            
            ctx.restore();
            
            // Маленькие точки на линии
            ctx.save();
            ctx.shadowBlur = 10;
            ctx.shadowColor = '#ff0000';
            for (let y = 50; y < canvas.height; y += 100) {
                ctx.beginPath();
                ctx.arc(lineX, y, 5, 0, Math.PI * 2);
                ctx.fillStyle = '#000000';
                ctx.fill();
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 1;
                ctx.stroke();
            }
            ctx.restore();
        }
        
        // Игровой цикл
        function gameLoop() {
            if (!gameRunning) return;
            
            if (!paused) {
                // Обновление
                [...playerBases, ...enemyBases].forEach(base => base.update());
                units.forEach(unit => unit.update());
                
                spawnEnemies();
                updateCombat();
                attackBases();
                updateLine(); // Обновляем линию
                
                particles = particles.filter(p => !p.update());
                
                // Проверка поражения
                if (lineX < 200) {
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
            
            // Рисуем ПРЯМУЮ линию
            drawLine();
            
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
                // Проверяем базы игрока
                for (let base of playerBases) {
                    const dx = mouseX - base.x;
                    const dy = mouseY - base.y;
                    const dist = Math.sqrt(dx*dx + dy*dy);
                    
                    if (dist < base.radius) {
                        if (base.units > 0) {
                            // Создаем 3 юнита за клик
                            for (let i = 0; i < 3; i++) {
                                if (base.units > 0) {
                                    const unit = base.spawnUnit();
                                    if (unit) {
                                        unit.targetX = lineX + 30; // Идут к линии
                                        unit.targetY = 200 + Math.random() * 400;
                                        units.push(unit);
                                    }
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
                    
                    // Если выбран юнит, можно отправить его к цели
                    const selected = units.filter(u => u.isSelected);
                    selected.forEach(unit => {
                        // Но цель не может быть за линией!
                        let targetX = mouseX;
                        if (unit.type === 'player' && targetX < lineX) {
                            targetX = lineX + 10;
                        }
                        unit.setTarget(targetX, mouseY);
                    });
                } else {
                    const selected = units.filter(u => u.isSelected);
                    selected.forEach(unit => {
                        // Цель не может быть за линией
                        let targetX = mouseX;
                        if (unit.type === 'player' && targetX < lineX) {
                            targetX = lineX + 10;
                        }
                        unit.setTarget(targetX, mouseY);
                    });
                }
            }
        });
        
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        
        initGame();
    </script>
</body>
</html>
