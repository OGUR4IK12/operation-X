<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Война Тысячи Тысяч</title>
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
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.3);
            border-radius: 20px;
            overflow: hidden;
            border: 3px solid #3a5a3a;
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
            border: 3px solid #5a7a5a;
            border-radius: 20px;
        }
        
        #mainMenu h1 {
            color: #7aff7a;
            font-size: 64px;
            text-shadow: 0 0 20px #00ff00;
            margin-bottom: 20px;
            letter-spacing: 4px;
            font-family: 'Courier New', monospace;
            border-right: 4px solid #7aff7a;
            padding-right: 10px;
            animation: blink 1s infinite;
        }
        
        @keyframes blink {
            0%, 100% { border-color: #7aff7a; }
            50% { border-color: transparent; }
        }
        
        .menuButtons {
            display: flex;
            gap: 20px;
            margin-top: 40px;
        }
        
        .menuBtn {
            background: transparent;
            color: #7aff7a;
            border: 2px solid #7aff7a;
            padding: 15px 40px;
            font-size: 24px;
            font-family: 'Courier New', monospace;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 10px rgba(122, 255, 122, 0.3);
        }
        
        .menuBtn:hover {
            background: #7aff7a;
            color: #0a1a0a;
            box-shadow: 0 0 30px #7aff7a;
            transform: scale(1.1);
        }
        
        /* Интерфейс во время игры */
        #gameUI {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            color: #7aff7a;
            font-size: 20px;
            text-shadow: 0 0 10px #00ff00;
            background: rgba(20, 30, 20, 0.7);
            padding: 15px 25px;
            border-radius: 50px;
            border: 2px solid #5a7a5a;
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
            color: #7aff7a;
            border: 2px solid #7aff7a;
            padding: 5px 20px;
            font-family: 'Courier New', monospace;
            font-size: 18px;
            cursor: pointer;
            transition: 0.3s;
            border-radius: 30px;
        }
        
        #pauseBtn:hover {
            background: #7aff7a;
            color: #1a2a1a;
        }
        
        /* Панель управления */
        #controlPanel {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(20, 30, 20, 0.9);
            border: 2px solid #5a7a5a;
            border-radius: 50px;
            padding: 10px 20px;
            display: flex;
            gap: 15px;
            backdrop-filter: blur(5px);
            z-index: 5;
        }
        
        .controlBtn {
            background: transparent;
            color: #7aff7a;
            border: 1px solid #7aff7a;
            padding: 8px 20px;
            font-family: 'Courier New', monospace;
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s;
            border-radius: 30px;
        }
        
        .controlBtn:hover {
            background: #7aff7a;
            color: #1a2a1a;
        }
        
        .controlBtn.active {
            background: #7aff7a;
            color: #1a2a1a;
        }
        
        #gameOverScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(10, 20, 10, 0.95);
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
        
        #gameOverScreen button {
            background: transparent;
            color: #ff4f4f;
            border: 2px solid #ff4f4f;
            padding: 10px 30px;
            font-size: 24px;
            font-family: 'Courier New', monospace;
            cursor: pointer;
            transition: 0.3s;
            margin-top: 20px;
        }
        
        #gameOverScreen button:hover {
            background: #ff4f4f;
            color: #0a1a0a;
        }
    </style>
</head>
<body>
    <div id="gameWrapper">
        <canvas id="gameCanvas" width="1200" height="700"></canvas>
        
        <!-- Главное меню -->
        <div id="mainMenu">
            <h1>ВОЙНА ТЫСЯЧИ ТЫСЯЧ</h1>
            <div style="color: #7aff7a; font-size: 20px; margin-bottom: 30px; text-align: center;">
                <p>⚔️ ЗАЩИТИ СВОИ ЗЕМЛИ ⚔️</p>
                <p style="font-size: 16px; margin-top: 10px;">Кликай по своим базам чтобы создать юнитов</p>
                <p style="font-size: 14px; color: #5a9a5a;">Враг атакует волнами!</p>
            </div>
            <div class="menuButtons">
                <button class="menuBtn" onclick="startGame()">НАЧАТЬ ВОЙНУ</button>
                <button class="menuBtn" onclick="location.reload()">ВЫХОД</button>
            </div>
        </div>
        
        <!-- Интерфейс игры -->
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
                    <span>⚔️</span>
                    <span id="waveCounter">1</span>
                </div>
            </div>
            <button id="pauseBtn" onclick="togglePause()">ПАУЗА</button>
        </div>
        
        <!-- Панель управления -->
        <div id="controlPanel" style="display: none;">
            <button class="controlBtn" onclick="spawnUnitAtBase()">➕ СОЗДАТЬ ЮНИТА</button>
            <button class="controlBtn" onclick="upgradeBase()">⬆️ УЛУЧШИТЬ БАЗУ</button>
            <button class="controlBtn active" onclick="toggleAutoSpawn()">🔄 АВТОСПАВН</button>
        </div>
        
        <!-- Экран поражения -->
        <div id="gameOverScreen">
            <h2>ВЫ ПРОИГРАЛИ</h2>
            <p style="font-size: 24px; margin: 20px 0;">Волна <span id="finalWave">1</span></p>
            <button onclick="restartGame()">НАЧАТЬ СНАЧАЛА</button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // Состояние игры
        let gameRunning = false;
        let paused = false;
        let gameObjects = [];
        let playerBases = [];
        let enemyBases = [];
        let units = [];
        let particles = [];
        
        let playerScore = 0;
        let enemyScore = 0;
        let waveNumber = 1;
        let autoSpawnEnabled = true;
        
        // Класс базы
        class Base {
            constructor(x, y, type) { // type: 'player' или 'enemy'
                this.x = x;
                this.y = y;
                this.type = type;
                this.health = 100;
                this.maxHealth = 100;
                this.units = type === 'player' ? 8 : 5;
                this.maxUnits = 15;
                this.spawnRate = 0.02;
                this.spawnProgress = 0;
                this.level = 1;
                
                // Визуальные эффекты
                this.pulsePhase = Math.random() * Math.PI * 2;
            }
            
            update() {
                // Спавн юнитов
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
                // Рисуем базу
                ctx.save();
                
                // Пульсирующий ореол
                ctx.shadowColor = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.shadowBlur = 20 + Math.sin(this.pulsePhase) * 10;
                
                // Основа базы
                ctx.beginPath();
                ctx.arc(this.x, this.y, 30, 0, Math.PI * 2);
                
                // Градиент
                const gradient = ctx.createRadialGradient(this.x - 10, this.y - 10, 5, this.x, this.y, 40);
                if (this.type === 'player') {
                    gradient.addColorStop(0, '#7aff7a');
                    gradient.addColorStop(1, '#2a5a2a');
                } else {
                    gradient.addColorStop(0, '#ff4f4f');
                    gradient.addColorStop(1, '#5a2a2a');
                }
                
                ctx.fillStyle = gradient;
                ctx.fill();
                
                // Обводка
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 3;
                ctx.stroke();
                
                // Уровень базы
                ctx.shadowBlur = 0;
                ctx.fillStyle = 'white';
                ctx.font = 'bold 20px "Courier New"';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.units, this.x, this.y);
                
                // Полоска здоровья
                ctx.fillStyle = '#333';
                ctx.fillRect(this.x - 25, this.y - 45, 50, 8);
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.fillRect(this.x - 25, this.y - 45, 50 * healthPercent, 8);
                
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
                this.targetX = x;
                this.targetY = y;
                this.speed = type === 'player' ? 2.5 : 2;
                this.health = 20;
                this.damage = 10;
                this.radius = 8;
                this.isSelected = false;
                
                // Для движения линией (красные идут строем)
                this.formationX = 0;
                this.formationY = 0;
            }
            
            setTarget(tx, ty) {
                this.targetX = tx;
                this.targetY = ty;
            }
            
            update() {
                // Движение к цели
                const dx = this.targetX - this.x;
                const dy = this.targetY - this.y;
                const distance = Math.sqrt(dx*dx + dy*dy);
                
                if (distance > 2) {
                    const moveX = (dx / distance) * this.speed;
                    const moveY = (dy / distance) * this.speed;
                    this.x += moveX;
                    this.y += moveY;
                }
            }
            
            draw(isInFormation = false) {
                ctx.save();
                
                // Тень/ореол
                ctx.shadowColor = this.type === 'player' ? '#7aff7a' : '#ff4f4f';
                ctx.shadowBlur = 15;
                
                // Рисуем юнита
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                // Градиент
                const gradient = ctx.createRadialGradient(this.x - 3, this.y - 3, 2, this.x, this.y, this.radius + 5);
                if (this.type === 'player') {
                    gradient.addColorStop(0, '#b2ffb2');
                    gradient.addColorStop(1, '#3a7a3a');
                } else {
                    gradient.addColorStop(0, '#ff8080');
                    gradient.addColorStop(1, '#7a3a3a');
                }
                
                ctx.fillStyle = gradient;
                ctx.fill();
                
                // Обводка (золотая если выбран)
                ctx.strokeStyle = this.isSelected ? '#ffd700' : 'white';
                ctx.lineWidth = this.isSelected ? 3 : 2;
                ctx.stroke();
                
                // Линия движения для врагов (красная линия впереди)
                if (this.type === 'enemy' && !isInFormation) {
                    ctx.beginPath();
                    ctx.moveTo(this.x, this.y);
                    ctx.lineTo(this.targetX, this.targetY);
                    ctx.strokeStyle = '#ff4f4f';
                    ctx.lineWidth = 2;
                    ctx.setLineDash([5, 5]);
                    ctx.stroke();
                    ctx.setLineDash([]);
                }
                
                // Глазок направления
                ctx.shadowBlur = 0;
                ctx.beginPath();
                ctx.arc(
                    this.x + (this.targetX - this.x) * 0.2,
                    this.y + (this.targetY - this.y) * 0.2,
                    2, 0, Math.PI * 2
                );
                ctx.fillStyle = 'white';
                ctx.fill();
                
                ctx.restore();
            }
            
            takeDamage(amount) {
                this.health -= amount;
                return this.health <= 0;
            }
        }
        
        // Класс частиц (эффекты)
        class Particle {
            constructor(x, y, color) {
                this.x = x;
                this.y = y;
                this.vx = (Math.random() - 0.5) * 4;
                this.vy = (Math.random() - 0.5) * 4;
                this.color = color;
                this.life = 1;
                this.size = Math.random() * 3 + 2;
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
        
        // Инициализация игры
        function initGame() {
            playerBases = [
                new Base(200, 350, 'player'),
                new Base(400, 200, 'player')
            ];
            
            enemyBases = [
                new Base(1000, 350, 'enemy'),
                new Base(800, 500, 'enemy')
            ];
            
            units = [];
            particles = [];
            playerScore = 0;
            enemyScore = 0;
            waveNumber = 1;
        }
        
        // Запуск игры
        window.startGame = function() {
            document.getElementById('mainMenu').style.display = 'none';
            document.getElementById('gameUI').style.display = 'flex';
            document.getElementById('controlPanel').style.display = 'flex';
            initGame();
            gameRunning = true;
            gameLoop();
        };
        
        // Перезапуск
        window.restartGame = function() {
            document.getElementById('gameOverScreen').style.display = 'none';
            startGame();
        };
        
        // Пауза
        window.togglePause = function() {
            paused = !paused;
            document.getElementById('pauseBtn').textContent = paused ? 'ПРОДОЛЖИТЬ' : 'ПАУЗА';
        };
        
        // Создать юнита на базе
        window.spawnUnitAtBase = function() {
            if (!gameRunning || paused) return;
            
            for (let base of playerBases) {
                if (base.units > 0) {
                    const unit = base.spawnUnit();
                    if (unit) {
                        // Отправляем к вражеской базе
                        if (enemyBases.length > 0) {
                            const targetBase = enemyBases[0];
                            unit.setTarget(targetBase.x, targetBase.y);
                        }
                        units.push(unit);
                    }
                    break;
                }
            }
        };
        
        // Улучшить базу
        window.upgradeBase = function() {
            if (!gameRunning || paused) return;
            
            for (let base of playerBases) {
                if (base.units >= 5) {
                    base.units -= 5;
                    base.level++;
                    base.maxHealth += 50;
                    base.health = base.maxHealth;
                    base.maxUnits += 5;
                    break;
                }
            }
        };
        
        // Автоспавн
        window.toggleAutoSpawn = function() {
            autoSpawnEnabled = !autoSpawnEnabled;
            const btn = document.querySelector('.controlBtn.active');
            if (btn) {
                btn.style.background = autoSpawnEnabled ? '#7aff7a' : 'transparent';
                btn.style.color = autoSpawnEnabled ? '#1a2a1a' : '#7aff7a';
            }
        };
        
        // Обновление статистики
        function updateUI() {
            document.getElementById('playerScore').textContent = playerBases.reduce((sum, b) => sum + b.units, 0) + units.filter(u => u.type === 'player').length;
            document.getElementById('enemyScore').textContent = enemyBases.reduce((sum, b) => sum + b.units, 0) + units.filter(u => u.type === 'enemy').length;
            document.getElementById('waveCounter').textContent = waveNumber;
        }
        
        // Спавн волны врагов (линией)
        function spawnEnemyWave() {
            if (!gameRunning || paused) return;
            
            // Увеличиваем сложность с каждой волной
            waveNumber++;
            const enemiesCount = 5 + waveNumber * 2;
            
            if (enemyBases.length > 0) {
                const base = enemyBases[0];
                
                // Создаем врагов линией
                for (let i = 0; i < enemiesCount; i++) {
                    if (base.units > 0) {
                        const unit = base.spawnUnit();
                        if (unit) {
                            // Располагаем их линией
                            unit.x = base.x - 50 - i * 15;
                            unit.y = base.y - 30 + (i % 3) * 30;
                            
                            // Атакуют ближайшую базу игрока
                            if (playerBases.length > 0) {
                                const targetBase = playerBases[Math.floor(Math.random() * playerBases.length)];
                                unit.setTarget(targetBase.x, targetBase.y);
                            }
                            units.push(unit);
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
                
                // Автоспавн врагов
                if (autoSpawnEnabled && Math.random() < 0.01) {
                    spawnEnemyWave();
                }
                
                // Движение юнитов
                units.forEach(unit => unit.update());
                
                // Проверка коллизий (бой)
                for (let i = units.length - 1; i >= 0; i--) {
                    const unit = units[i];
                    
                    // Атака баз
                    if (unit.type === 'enemy') {
                        for (let base of playerBases) {
                            const dx = unit.x - base.x;
                            const dy = unit.y - base.y;
                            if (Math.sqrt(dx*dx + dy*dy) < 40) {
                                // Враг атакует базу
                                const died = base.takeDamage(unit.damage);
                                units.splice(i, 1);
                                
                                if (died) {
                                    // База уничтожена
                                    playerBases = playerBases.filter(b => b !== base);
                                    for (let p = 0; p < 10; p++) {
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
                            if (Math.sqrt(dx*dx + dy*dy) < 40) {
                                // Игрок атакует вражескую базу
                                const died = base.takeDamage(unit.damage);
                                units.splice(i, 1);
                                
                                if (died) {
                                    // База уничтожена
                                    enemyBases = enemyBases.filter(b => b !== base);
                                    for (let p = 0; p < 10; p++) {
                                        particles.push(new Particle(base.x, base.y, '#7aff7a'));
                                    }
                                }
                                break;
                            }
                        }
                    }
                    
                    // Бой между юнитами
                    for (let j = i - 1; j >= 0; j--) {
                        const unit2 = units[j];
                        if (unit.type !== unit2.type) {
                            const dx = unit.x - unit2.x;
                            const dy = unit.y - unit2.y;
                            if (Math.sqrt(dx*dx + dy*dy) < unit.radius + unit2.radius) {
                                // Бой
                                unit.health -= unit2.damage;
                                unit2.health -= unit.damage;
                                
                                if (unit.health <= 0) {
                                    units.splice(i, 1);
                                    particles.push(new Particle(unit.x, unit.y, unit.type === 'player' ? '#7aff7a' : '#ff4f4f'));
                                }
                                if (unit2.health <= 0) {
                                    units.splice(j, 1);
                                    particles.push(new Particle(unit2.x, unit2.y, unit2.type === 'player' ? '#7aff7a' : '#ff4f4f'));
                                }
                                break;
                            }
                        }
                    }
                }
                
                // Обновление частиц
                particles = particles.filter(p => !p.update());
                
                // Проверка поражения
                if (playerBases.length === 0) {
                    gameRunning = false;
                    document.getElementById('finalWave').textContent = waveNumber;
                    document.getElementById('gameOverScreen').style.display = 'block';
                }
                
                // Проверка победы
                if (enemyBases.length === 0) {
                    // Новая волна
                    spawnEnemyWave();
                }
                
                updateUI();
            }
            
            // Отрисовка
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Рисуем "поляну" (траву)
            ctx.fillStyle = '#1a2a1a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Сетка (для военного стиля)
            ctx.strokeStyle = '#3a4a3a';
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
                ctx.strokeStyle = '#2a3a2a';
                ctx.stroke();
            }
            
            // Рисуем базы
            [...playerBases, ...enemyBases].forEach(base => base.draw());
            
            // Рисуем юнитов
            units.forEach(unit => unit.draw());
            
            // Рисуем частицы
            particles.forEach(p => p.draw());
            
            requestAnimationFrame(gameLoop);
        }
        
        // Обработка кликов мыши
        canvas.addEventListener('mousedown', (e) => {
            if (!gameRunning || paused) return;
            
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            
            const mouseX = (e.clientX - rect.left) * scaleX;
            const mouseY = (e.clientY - rect.top) * scaleY;
            
            if (e.button === 0) { // ЛКМ
                // Проверяем клик по базе игрока
                for (let base of playerBases) {
                    const dx = mouseX - base.x;
                    const dy = mouseY - base.y;
                    if (Math.sqrt(dx*dx + dy*dy) < 30) {
                        if (base.units > 0) {
                            const unit = base.spawnUnit();
                            if (unit) {
                                // Отправляем к врагу
                                if (enemyBases.length > 0) {
                                    unit.setTarget(enemyBases[0].x, enemyBases[0].y);
                                }
                                units.push(unit);
                            }
                        }
                        return;
                    }
                }
                
                // Проверка клика по юнитам
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
                    // Выделяем юнита
                    if (!e.shiftKey) {
                        units.forEach(u => u.isSelected = false);
                    }
                    clickedUnit.isSelected = !clickedUnit.isSelected;
                } else {
                    // Отправляем выделенных юнитов
                    const selectedUnits = units.filter(u => u.isSelected);
                    if (selectedUnits.length > 0) {
                        selectedUnits.forEach(unit => {
                            unit.setTarget(mouseX, mouseY);
                        });
                    }
                }
            }
        });
        
        // Запрет контекстного меню
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        
        // Запуск
        initGame();
    </script>
</body>
</html>
