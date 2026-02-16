<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Dots War — клон War of Dots</title>
    <style>
        * {
            user-select: none;
        }
        body {
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: #2a2a2a;
            font-family: Arial, sans-serif;
        }
        .game-container {
            text-align: center;
        }
        canvas {
            background: #1a1a1a;
            border: 3px solid #444;
            border-radius: 8px;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            cursor: crosshair;
        }
        .info-panel {
            margin-top: 15px;
            color: #fff;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: #333;
            padding: 10px 20px;
            border-radius: 8px;
            border: 1px solid #555;
        }
        .player-info {
            color: #4caf50;
            font-weight: bold;
        }
        .enemy-info {
            color: #f44336;
            font-weight: bold;
        }
        .selected-info {
            color: #ffd700;
            font-size: 14px;
        }
        button {
            background: #4CAF50;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 4px;
            cursor: pointer;
            font-weight: bold;
            transition: 0.2s;
        }
        button:hover {
            background: #45a049;
            transform: scale(1.05);
        }
        .instructions {
            color: #aaa;
            font-size: 12px;
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <canvas id="gameCanvas" width="1000" height="600"></canvas>
        
        <div class="info-panel">
            <div class="player-info" id="playerStats">👤 Игрок: 0 юнитов</div>
            <div class="selected-info" id="selectedStats">🔍 Выбрано: 0</div>
            <div class="enemy-info" id="enemyStats">👾 Враг: 0 юнитов</div>
        </div>
        
        <div style="margin-top: 10px; display: flex; gap: 10px; justify-content: center;">
            <button onclick="startNewGame()">🔄 Новая игра</button>
            <button onclick="spawnEnemyWave()">⚡ Волна врагов</button>
        </div>
        
        <div class="instructions">
            🖱️ ЛКМ на свой город — создать юнита | 🖱️ ЛКМ на врага — атаковать | 🖱️ ПКМ — отмена выбора
        </div>
    </div>

    <script>
        // Настройки игры
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // Состояние игры
        let gameRunning = true;
        let selectedUnits = [];
        let animationFrame;
        
        // Класс города
        class City {
            constructor(x, y, owner, type = 'normal') {
                this.x = x;
                this.y = y;
                this.owner = owner; // 'player' или 'enemy'
                this.type = type; // 'normal' или 'capital'
                this.units = owner === 'player' ? 5 : 8; // У врага изначально больше
                this.maxUnits = 5; // Максимум юнитов в городе
                this.productionRate = 0.02; // Скорость производства (на кадр)
                this.productionProgress = 0;
                this.radius = 20;
            }
            
            update() {
                if (this.units < this.maxUnits) {
                    this.productionProgress += this.productionRate;
                    if (this.productionProgress >= 1) {
                        this.units++;
                        this.productionProgress = 0;
                    }
                }
            }
            
            draw() {
                // Рисуем город
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                // Цвет зависит от владельца
                if (this.owner === 'player') {
                    ctx.fillStyle = '#4caf50';
                    ctx.strokeStyle = '#8bc34a';
                } else {
                    ctx.fillStyle = '#f44336';
                    ctx.strokeStyle = '#ff7043';
                }
                
                ctx.fill();
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 3;
                ctx.stroke();
                
                // Рисуем столицу иначе
                if (this.type === 'capital') {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.radius + 5, 0, Math.PI * 2);
                    ctx.strokeStyle = '#ffd700';
                    ctx.lineWidth = 2;
                    ctx.stroke();
                }
                
                // Количество юнитов
                ctx.fillStyle = 'white';
                ctx.font = 'bold 16px Arial';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(this.units, this.x, this.y);
                
                // Прогресс-бар производства
                if (this.units < this.maxUnits) {
                    ctx.fillStyle = '#555';
                    ctx.fillRect(this.x - 15, this.y - 35, 30, 5);
                    ctx.fillStyle = '#4caf50';
                    ctx.fillRect(this.x - 15, this.y - 35, 30 * (this.productionProgress), 5);
                }
            }
            
            // Создать юнита
            spawnUnit() {
                if (this.units > 0) {
                    this.units--;
                    return new Unit(this.x + (Math.random() - 0.5) * 30, 
                                   this.y + (Math.random() - 0.5) * 30, 
                                   this.owner);
                }
                return null;
            }
            
            // Проверка, находится ли точка внутри города
            containsPoint(px, py) {
                const dx = px - this.x;
                const dy = py - this.y;
                return Math.sqrt(dx*dx + dy*dy) < this.radius;
            }
        }
        
        // Класс юнита
        class Unit {
            constructor(x, y, owner) {
                this.x = x;
                this.y = y;
                this.owner = owner;
                this.targetX = x;
                this.targetY = y;
                this.speed = 2;
                this.radius = 6;
                this.isSelected = false;
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
            
            draw() {
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                
                // Цвет юнита
                if (this.owner === 'player') {
                    ctx.fillStyle = this.isSelected ? '#ffd700' : '#8bc34a';
                } else {
                    ctx.fillStyle = this.isSelected ? '#ffd700' : '#ff7043';
                }
                
                ctx.fill();
                ctx.strokeStyle = 'white';
                ctx.lineWidth = 1;
                ctx.stroke();
                
                // Маленький глазок для направления
                ctx.beginPath();
                ctx.arc(this.x + (this.targetX - this.x) * 0.1, 
                       this.y + (this.targetY - this.y) * 0.1, 
                       2, 0, Math.PI * 2);
                ctx.fillStyle = 'white';
                ctx.fill();
            }
            
            // Проверка, находится ли точка внутри юнита
            containsPoint(px, py) {
                const dx = px - this.x;
                const dy = py - this.y;
                return Math.sqrt(dx*dx + dy*dy) < this.radius + 5;
            }
        }
        
        // Игровые объекты
        let cities = [];
        let units = [];
        
        // Инициализация игры
        function initGame() {
            cities = [
                new City(200, 200, 'player', 'capital'),
                new City(800, 400, 'enemy', 'capital'),
                new City(400, 500, 'enemy'),
                new City(600, 100, 'enemy')
            ];
            units = [];
            selectedUnits = [];
            updateStats();
        }
        
        // Функция для новой игры
        function startNewGame() {
            initGame();
        }
        
        // Функция для спавна волны врагов
        function spawnEnemyWave() {
            for (let i = 0; i < 5; i++) {
                const enemyCity = cities.find(c => c.owner === 'enemy');
                if (enemyCity && enemyCity.units > 0) {
                    const unit = enemyCity.spawnUnit();
                    if (unit) {
                        unit.setTarget(300, 300); // Атакуют центр
                        units.push(unit);
                    }
                }
            }
        }
        
        // Обновление статистики
        function updateStats() {
            const playerUnits = units.filter(u => u.owner === 'player').length;
            const playerCities = cities.filter(c => c.owner === 'player').reduce((sum, c) => sum + c.units, 0);
            const enemyUnits = units.filter(u => u.owner === 'enemy').length;
            const enemyCities = cities.filter(c => c.owner === 'enemy').reduce((sum, c) => sum + c.units, 0);
            
            document.getElementById('playerStats').innerHTML = `👤 Игрок: ${playerUnits + playerCities} юнитов`;
            document.getElementById('enemyStats').innerHTML = `👾 Враг: ${enemyUnits + enemyCities} юнитов`;
            document.getElementById('selectedStats').innerHTML = `🔍 Выбрано: ${selectedUnits.length}`;
        }
        
        // Обработка кликов мыши
        canvas.addEventListener('mousedown', (e) => {
            e.preventDefault();
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width / rect.width;
            const scaleY = canvas.height / rect.height;
            
            const mouseX = (e.clientX - rect.left) * scaleX;
            const mouseY = (e.clientY - rect.top) * scaleY;
            
            if (e.button === 0) { // ЛКМ
                // Сначала проверяем города
                let clickedCity = null;
                for (let city of cities) {
                    if (city.containsPoint(mouseX, mouseY)) {
                        clickedCity = city;
                        break;
                    }
                }
                
                if (clickedCity) {
                    if (clickedCity.owner === 'player') {
                        // Создаем юнита из своего города
                        if (clickedCity.units > 0) {
                            const unit = clickedCity.spawnUnit();
                            if (unit) {
                                units.push(unit);
                                if (selectedUnits.length > 0) {
                                    unit.isSelected = true;
                                    selectedUnits.push(unit);
                                }
                            }
                        }
                    } else {
                        // Атакуем вражеский город выбранными юнитами
                        if (selectedUnits.length > 0) {
                            selectedUnits.forEach(unit => {
                                unit.setTarget(clickedCity.x, clickedCity.y);
                            });
                        }
                    }
                } else {
                    // Если кликнули не по городу, проверяем юнитов
                    let clickedUnit = null;
                    for (let unit of units) {
                        if (unit.containsPoint(mouseX, mouseY) && unit.owner === 'player') {
                            clickedUnit = unit;
                            break;
                        }
                    }
                    
                    if (clickedUnit) {
                        // Выделяем юнита
                        if (!e.shiftKey) {
                            selectedUnits.forEach(u => u.isSelected = false);
                            selectedUnits = [];
                        }
                        clickedUnit.isSelected = true;
                        selectedUnits.push(clickedUnit);
                    } else {
                        // Если кликнули по пустому месту с выбранными юнитами
                        if (selectedUnits.length > 0) {
                            selectedUnits.forEach(unit => {
                                unit.setTarget(mouseX, mouseY);
                            });
                        }
                    }
                }
            } else if (e.button === 2) { // ПКМ
                e.preventDefault();
                selectedUnits.forEach(u => u.isSelected = false);
                selectedUnits = [];
            }
            
            updateStats();
        });
        
        // Запрещаем контекстное меню на канвасе
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        
        // Игровой цикл
        function gameLoop() {
            if (!gameRunning) return;
            
            // Очищаем канвас
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Обновляем города
            cities.forEach(city => city.update());
            
            // Обновляем юниты
            units.forEach(unit => unit.update());
            
            // Проверка коллизий (боевка)
            for (let i = units.length - 1; i >= 0; i--) {
                for (let j = i - 1; j >= 0; j--) {
                    const unit1 = units[i];
                    const unit2 = units[j];
                    
                    if (unit1.owner !== unit2.owner) {
                        const dx = unit1.x - unit2.x;
                        const dy = unit1.y - unit2.y;
                        const distance = Math.sqrt(dx*dx + dy*dy);
                        
                        if (distance < unit1.radius + unit2.radius) {
                            // Бой: оба юнита умирают
                            if (unit1.isSelected) {
                                const index = selectedUnits.indexOf(unit1);
                                if (index > -1) selectedUnits.splice(index, 1);
                            }
                            if (unit2.isSelected) {
                                const index = selectedUnits.indexOf(unit2);
                                if (index > -1) selectedUnits.splice(index, 1);
                            }
                            
                            units.splice(i, 1);
                            units.splice(j, 1);
                            break;
                        }
                    }
                }
            }
            
            // Проверка захвата городов
            for (let i = cities.length - 1; i >= 0; i--) {
                const city = cities[i];
                
                for (let j = units.length - 1; j >= 0; j--) {
                    const unit = units[j];
                    
                    const dx = unit.x - city.x;
                    const dy = unit.y - city.y;
                    const distance = Math.sqrt(dx*dx + dy*dy);
                    
                    if (distance < city.radius + unit.radius) {
                        if (unit.owner !== city.owner) {
                            // Атака города
                            city.units--;
                            units.splice(j, 1);
                            
                            if (city.units <= 0) {
                                // Город захвачен
                                city.owner = unit.owner;
                                city.units = 3; // Начальные юниты в захваченном городе
                            }
                            break;
                        }
                    }
                }
            }
            
            // Рисуем всё
            cities.forEach(city => city.draw());
            units.forEach(unit => unit.draw());
            
            updateStats();
            
            // Продолжаем цикл
            animationFrame = requestAnimationFrame(gameLoop);
        }
        
        // Запуск игры
        initGame();
        gameLoop();
        
        // Очистка при закрытии
        window.addEventListener('beforeunload', () => {
            if (animationFrame) {
                cancelAnimationFrame(animationFrame);
            }
        });
    </script>
</body>
</html>
