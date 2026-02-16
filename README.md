<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Война Тысячи Тысяч — Изогнутая Линия</title>
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
            <h1>ИЗОГНУТАЯ ЛИНИЯ</h1>
            <div style="color: #ff0000; font-size: 20px; margin-bottom: 30px; text-align: center;">
                <p>⚔️ ЛИНИЯ ИЗГИБАЕТСЯ КАК НИТКА ⚔️</p>
                <p style="font-size: 16px; margin-top: 10px;">Она повторяет позиции врагов</p>
                <p style="font-size: 14px; color: #ff6666;">Чем больше врагов - тем сильнее изгиб</p>
            </div>
            <div class="menuButtons">
                <button class="menuBtn" onclick="startGame()">ВСТУПИТЬ В БОЙ</button>
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
                    <span>〰️</span>
                    <span id="blackLinePos">100%</span>
                </div>
            </div>
            <button id="pauseBtn" onclick="togglePause()">ПАУЗА</button>
        </div>
        
        <!-- Экран поражения -->
        <div id="gameOverScreen">
            <h2>ЛИНИЯ ДОШЛА</h2>
            <p style="font-size: 24px; margin: 20px 0;">Вы не остановили наступление</p>
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
        
        // Для изогнутой линии - массив точек
        let linePoints = [];
        const SEGMENTS = 30; // Количество сегментов линии
        
        // Класс базы
        class Base {
            constructor(x, y, type) {
                this.x = x;
                this.y = y;
                this.type = type;
                this.health = 250;
                this.maxHealth = 250;
                this.units = type === 'player' ? 12 : 20;
                this.maxUnits = 25;
                this.spawnRate = 0.04;
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
                ctx.shadowColor = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.shadowBlur = 30 + Math.sin(this.pulsePhase) * 10;
                
                // База
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                const gradient = ctx.createRadialGradient(this.x - 10, this.y - 10, 5, this.x, this.y, 45);
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
                ctx.font = 'bold 24px "Courier New"';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.units, this.x, this.y);
                
                // Полоска здоровья
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 30, this.y - 50, 60, 8);
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = this.type === 'player' ? '#00ff00' : '#ff0000';
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
                this.targetX = type === 'player' ? canvas.width - 200 : 100;
                this.targetY = y;
                this.speed = type === 'player' ? 2.2 : 2.5;
                this.health = 60;
                this.maxHealth = 60;
                this.damage = 8;
                this.attackCooldown = 0;
                this.attackRange = 30;
                this.radius = 12;
                this.isSelected = false;
                
                // Для построения в линию
                this.inCombat = false;
                this.formationOffset = (Math.random() - 0.5) * 30;
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
                
                // Цвет
                if (this.type === 'player') {
                    ctx.fillStyle = '#00ff00';
                } else {
                    // Враги красные
                    const darkness = 0.5 + (1 - healthPercent) * 0.5;
                    ctx.fillStyle = `rgb(255, ${Math.floor(100 * darkness)}, ${Math.floor(100 * darkness)})`;
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
                ctx.fillRect(this.x - 12, this.y - 20, 24, 4);
                ctx.fillStyle = this.type === 'player' ? '#00ff00' : '#ff0000';
                ctx.fillRect(this.x - 12, this.y - 20, 24 * healthPercent, 4);
                
                ctx.restore();
            }
            
            attack(target) {
                if (this.attackCooldown <= 0) {
                    target.health -= this.damage;
                    this.attackCooldown = 15;
                    
                    // Эффект попадания
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
                this.vx = (Math.random() - 0.5) * 10;
                this.vy = (Math.random() - 0.5) * 10;
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
                new Base(150, 500, 'player'),
                new Base(150, 700, 'player')
            ];
            
            enemyBases = [
                new Base(1250, 250, 'enemy'),
                new Base(1250, 450, 'enemy'),
                new Base(1250, 650, 'enemy')
            ];
            
            units = [];
            particles = [];
            
            // Инициализация точек линии
            linePoints = [];
            for (let i = 0; i <= SEGMENTS; i++) {
                linePoints.push({
                    x: canvas.width - 150,
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
            const playerBaseUnits = playerBases.reduce((sum, b) => sum + b.units, 0);
            const enemyUnits = units.filter(u => u.type === 'enemy').length;
            const enemyBaseUnits = enemyBases.reduce((sum, b) => sum + b.units, 0);
            
            document.getElementById('playerScore').textContent = playerUnits + playerBaseUnits;
            document.getElementById('enemyScore').textContent = enemyUnits + enemyBaseUnits;
            
            // Средняя позиция линии
            let avgX = 0;
            linePoints.forEach(p => avgX += p.x);
            avgX /= linePoints.length;
            const linePercent = Math.floor((avgX / canvas.width) * 100);
            document.getElementById('blackLinePos').textContent = linePercent + '%';
        }
        
        // Спавн врагов
        function spawnEnemies() {
            if (!gameRunning || paused) return;
            
            for (let base of enemyBases) {
                if (Math.random() < 0.03 && base.units > 0) {
                    const unit = base.spawnUnit();
                    if (unit) {
                        // Цель - левая сторона (игроки)
                        unit.targetX = 200 + Math.random() * 200;
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
            
            // Удаление мертвых
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
        
        // Обновление изогнутой линии
        function updateLinePoints() {
            const enemyUnits = units.filter(u => u.type === 'enemy');
            
            // Если есть враги, строим линию по ним
            if (enemyUnits.length > 0) {
                // Группируем врагов по высоте (y)
                const yGroups = {};
                enemyUnits.forEach(unit => {
                    const yGroup = Math.floor(unit.y / 50) * 50; // Группируем каждые 50 пикселей
                    if (!yGroups[yGroup]) {
                        yGroups[yGroup] = [];
                    }
                    yGroups[yGroup].push(unit);
                });
                
                // Обновляем каждую точку линии
                for (let i = 0; i <= SEGMENTS; i++) {
                    const targetY = (i / SEGMENTS) * canvas.height;
                    
                    // Ищем врагов рядом с этой высотой
                    let closestY = null;
                    let minDist = Infinity;
                    let closestX = canvas.width - 150; // Значение по умолчанию
                    
                    Object.keys(yGroups).forEach(yStr => {
                        const y = parseInt(yStr);
                        const dist = Math.abs(y - targetY);
                        
                        if (dist < minDist && dist < 100) {
                            minDist = dist;
                            closestY = y;
                            
                            // Берем среднюю X позицию врагов в этой группе
                            const unitsInGroup = yGroups[y];
                            let sumX = 0;
                            unitsInGroup.forEach(u => sumX += u.x);
                            closestX = sumX / unitsInGroup.length;
                        }
                    });
                    
                    // Целевая позиция для точки (на 50 пикселей впереди врагов)
                    let targetX = closestX - 50;
                    
                    // Ограничиваем, чтобы линия не уходила слишком далеко
                    targetX = Math.max(100, Math.min(canvas.width - 50, targetX));
                    
                    // Плавно двигаем точку к цели
                    linePoints[i].x += (targetX - linePoints[i].x) * 0.05;
                    
                    // Вертикальная позиция тоже плавно двигается
                    linePoints[i].y += (targetY - linePoints[i].y) * 0.1;
                }
            } else {
                // Если врагов нет, линия отодвигается назад
                for (let i = 0; i <= SEGMENTS; i++) {
                    const targetY = (i / SEGMENTS) * canvas.height;
                    const targetX = canvas.width - 150;
                    
                    linePoints[i].x += (targetX - linePoints[i].x) * 0.05;
                    linePoints[i].y += (targetY - linePoints[i].y) * 0.1;
                }
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
                        if (Math.sqrt(dx*dx + dy*dy) < 60) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                playerBases = playerBases.filter(b => b !== base);
                                for (let p = 0; p < 30; p++) {
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
                        if (Math.sqrt(dx*dx + dy*dy) < 60) {
                            base.health -= unit.damage;
                            units.splice(i, 1);
                            
                            if (base.health <= 0) {
                                enemyBases = enemyBases.filter(b => b !== base);
                                for (let p = 0; p < 30; p++) {
                                    particles.push(new Particle(base.x, base.y, '#00ff00'));
                                }
                            }
                            break;
                        }
                    }
                }
            }
        }
        
        // Отрисовка изогнутой линии
        function drawCurvedLine() {
            ctx.save();
            
            // Рисуем изогнутую линию как нитку
            ctx.beginPath();
            ctx.moveTo(linePoints[0].x, linePoints[0].y);
            
            // Используем кривые Безье для плавности
            for (let i = 1; i < linePoints.length; i++) {
                const p1 = linePoints[i - 1];
                const p2 = linePoints[i];
                
                // Контрольные точки для плавного изгиба
                const cp1x = p1.x + (p2.x - p1.x) * 0.3;
                const cp1y = p1.y;
                const cp2x = p1.x + (p2.x - p1.x) * 0.7;
                const cp2y = p2.y;
                
                ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, p2.x, p2.y);
            }
            
            // Толстая черная линия
            ctx.strokeStyle = '#000000';
            ctx.lineWidth = 8;
            ctx.shadowColor = '#000000';
            ctx.shadowBlur = 20;
            ctx.stroke();
            
            // Белая окантовка
            ctx.beginPath();
            ctx.moveTo(linePoints[0].x, linePoints[0].y);
            for (let i = 1; i < linePoints.length; i++) {
                const p1 = linePoints[i - 1];
                const p2 = linePoints[i];
                const cp1x = p1.x + (p2.x - p1.x) * 0.3;
                const cp1y = p1.y;
                const cp2x = p1.x + (p2.x - p1.x) * 0.7;
                const cp2y = p2.y;
                ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, p2.x, p2.y);
            }
            ctx.strokeStyle = '#ffffff';
            ctx.lineWidth = 2;
            ctx.shadowBlur = 0;
            ctx.stroke();
            
            // Красное свечение
            ctx.beginPath();
            ctx.moveTo(linePoints[0].x, linePoints[0].y);
            for (let i = 1; i < linePoints.length; i++) {
                const p1 = linePoints[i - 1];
                const p2 = linePoints[i];
                const cp1x = p1.x + (p2.x - p1.x) * 0.3;
                const cp1y = p1.y;
                const cp2x = p1.x + (p2.x - p1.x) * 0.7;
                const cp2y = p2.y;
                ctx.bezierCurveTo(cp1x, cp1y, cp2x, cp2y, p2.x, p2.y);
            }
            ctx.strokeStyle = '#ff0000';
            ctx.lineWidth = 1;
            ctx.shadowColor = '#ff0000';
            ctx.shadowBlur = 15;
            ctx.stroke();
            
            ctx.restore();
            
            // Рисуем маленькие узелки на линии (как на нитке)
            ctx.save();
            ctx.shadowBlur = 10;
            ctx.shadowColor = '#ff0000';
            for (let i = 0; i < linePoints.length; i += 3) {
                ctx.beginPath();
                ctx.arc(linePoints[i].x, linePoints[i].y, 4, 0, Math.PI * 2);
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
                
                // Спавн врагов
                spawnEnemies();
                
                // Бой
                updateCombat();
                
                // Атака баз
                attackBases();
                
                // Обновление изогнутой линии
                updateLinePoints();
                
                // Частицы
                particles = particles.filter(p => !p.update());
                
                // Проверка поражения (любая точка линии слишком близко)
                let gameOver = false;
                for (let point of linePoints) {
                    if (point.x < 120) {
                        gameOver = true;
                        break;
                    }
                }
                
                if (gameOver) {
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
            
            // Рисуем изогнутую линию (самое главное!)
            drawCurvedLine();
            
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
                            // Создаем 2 юнита за клик
                            for (let i = 0; i < 2; i++) {
                                const unit = base.spawnUnit();
                                if (unit) {
                                    unit.targetX = canvas.width - 300;
                                    unit.targetY = 200 + Math.random() * 500;
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
