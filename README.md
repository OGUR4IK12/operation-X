<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ППО України — Захисти небо!</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }
        body {
            margin: 0;
            padding: 0;
            font-family: 'Courier New', 'Monaco', monospace;
            background: #0a0e1a;
            overflow: hidden;
        }
        #map {
            height: 100vh;
            width: 100%;
            background: #0a1a2a;
        }
        /* UI Панель */
        .game-ui {
            position: fixed;
            bottom: 20px;
            left: 20px;
            right: 20px;
            background: rgba(10, 20, 30, 0.9);
            backdrop-filter: blur(8px);
            border-radius: 16px;
            padding: 12px 20px;
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            gap: 15px;
            border: 1px solid rgba(0, 255, 170, 0.3);
            box-shadow: 0 0 20px rgba(0, 255, 170, 0.1);
            z-index: 1000;
            font-family: monospace;
            pointer-events: auto;
        }
        .stats-panel {
            display: flex;
            gap: 25px;
            background: rgba(0, 0, 0, 0.6);
            padding: 8px 18px;
            border-radius: 40px;
            border-left: 3px solid #00ffaa;
        }
        .stat {
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .stat-label {
            font-size: 10px;
            color: #88aaff;
            letter-spacing: 1px;
        }
        .stat-value {
            font-size: 22px;
            font-weight: bold;
            color: #00ffaa;
            text-shadow: 0 0 5px #00ffaa;
        }
        .balance-value {
            color: #ffaa44;
            text-shadow: 0 0 5px #ffaa44;
        }
        .shop {
            display: flex;
            gap: 12px;
            background: rgba(0, 0, 0, 0.5);
            padding: 5px 15px;
            border-radius: 50px;
        }
        .shop-btn {
            background: linear-gradient(135deg, #1a2a3a, #0a1a2a);
            border: 1px solid #00ffaa;
            color: #00ffaa;
            padding: 8px 16px;
            border-radius: 40px;
            cursor: pointer;
            font-weight: bold;
            font-family: monospace;
            transition: 0.2s;
            font-size: 14px;
        }
        .shop-btn:hover {
            background: #00ffaa;
            color: #0a1a2a;
            box-shadow: 0 0 15px #00ffaa;
            transform: scale(1.02);
        }
        .shop-btn.active {
            background: #00ffaa;
            color: #0a1a2a;
            box-shadow: 0 0 15px #00ffaa;
        }
        .warning {
            background: rgba(255, 50, 50, 0.2);
            border-left: 4px solid #ff4444;
            padding: 5px 12px;
            border-radius: 8px;
            color: #ff8888;
            font-size: 12px;
        }
        .radar-container {
            position: fixed;
            top: 20px;
            right: 20px;
            width: 180px;
            height: 180px;
            background: rgba(0, 20, 0, 0.7);
            border-radius: 50%;
            border: 2px solid #00ffaa;
            box-shadow: 0 0 20px rgba(0,255,170,0.3);
            z-index: 1000;
            backdrop-filter: blur(4px);
        }
        canvas#radarCanvas {
            width: 100%;
            height: 100%;
            border-radius: 50%;
        }
        .instruction {
            position: fixed;
            top: 20px;
            left: 20px;
            background: rgba(0,0,0,0.6);
            padding: 8px 15px;
            border-radius: 20px;
            color: #88aaff;
            font-size: 11px;
            pointer-events: none;
            z-index: 1000;
        }
        @keyframes pulse {
            0% { opacity: 0.6; text-shadow: 0 0 0px red; }
            100% { opacity: 1; text-shadow: 0 0 8px red; }
        }
        .alert {
            animation: pulse 0.5s infinite alternate;
            color: #ff4444;
        }
        @media (max-width: 800px) {
            .game-ui { flex-direction: column; align-items: stretch; bottom: 10px; left: 10px; right: 10px; }
            .shop { justify-content: center; }
            .radar-container { width: 120px; height: 120px; top: 10px; right: 10px; }
        }
    </style>
</head>
<body>
<div id="map"></div>
<div class="radar-container">
    <canvas id="radarCanvas" width="200" height="200"></canvas>
</div>
<div class="instruction">
    🖱️ Клік на мапі → розмістити обрану установку | 🔫 Авто-стрільба по дронах
</div>

<div class="game-ui">
    <div class="stats-panel">
        <div class="stat">
            <div class="stat-label">💰 БАЛАНС</div>
            <div class="stat-value balance-value" id="balanceDisplay">10000</div>
        </div>
        <div class="stat">
            <div class="stat-label">🎯 ЗБИТО</div>
            <div class="stat-value" id="killsDisplay">0</div>
        </div>
        <div class="stat">
            <div class="stat-label">🚁 ДРОНІВ</div>
            <div class="stat-value" id="dronesCount">0</div>
        </div>
    </div>
    <div class="shop">
        <div class="shop-btn" data-unit="pickup">🔫 Пікап (12.7мм)<br><span style="font-size:10px">5500₴</span></div>
        <div class="shop-btn" data-unit="shilka">⚡ ЗСУ-23-4 "Шилка"<br><span style="font-size:10px">12000₴</span></div>
        <div class="shop-btn" data-unit="reb">📡 РЕБ (глушіння)<br><span style="font-size:10px">8000₴</span></div>
    </div>
    <div class="warning" id="warningMsg">
        ⚡ Оберіть установку та клікніть на карті України
    </div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    (function(){
        // ---------- КОНФІГ ГРИ ----------
        const BALANCE_START = 10000;
        const REWARD_PER_KILL = 800;
        const DRONE_SPAWN_INTERVAL_MS = 4000; // кожні 4 секунди новий дрон
        const MAX_DRONES = 18;
        
        // Характеристики установок
        const UNITS = {
            pickup: { name: "Пікап 12.7мм", price: 5500, range: 80, cooldown: 1800, damage: 45, color: "#ffaa44", icon: "🔫" },
            shilka: { name: "Шилка 23мм", price: 12000, range: 120, cooldown: 900, damage: 85, color: "#ff6644", icon: "⚡" },
            reb: { name: "РЕБ", price: 8000, range: 45, cooldown: 2500, damage: 0, isJammer: true, jamRadius: 45, color: "#aa88ff", icon: "📡" }
        };
        
        // Стан гри
        let playerBalance = BALANCE_START;
        let totalKills = 0;
        let selectedUnit = null; // pickup, shilka, reb
        let deployedUnits = [];    // масив об'єктів установок
        let drones = [];
        let gameInterval = null;
        let lastFrameTime = 0;
        let map = null;
        let radarCtx = null;
        let radarAnimation = null;
        
        // Межі України (спрощений полігон для перевірки розміщення)
        const ukraineBounds = L.latLngBounds(
            [44.2, 22.1], // пд-зх
            [52.4, 40.2]  // пн-сх
        );
        
        // ------ ДОПОМІЖНІ ФУНКЦІЇ ------
        function updateUI() {
            document.getElementById('balanceDisplay').innerText = Math.floor(playerBalance);
            document.getElementById('killsDisplay').innerText = totalKills;
            document.getElementById('dronesCount').innerText = drones.length;
        }
        
        function addMoney(amount) {
            playerBalance += amount;
            updateUI();
            showFloatingText(`+${amount}₴`, '#00ffaa');
        }
        
        function showFloatingText(text, color, latLng = null) {
            // якщо немає координат – показуємо в центрі карти
            if(!latLng && map) latLng = map.getCenter();
            if(!latLng) return;
            const container = document.createElement('div');
            container.textContent = text;
            container.style.position = 'absolute';
            container.style.color = color;
            container.style.fontWeight = 'bold';
            container.style.fontSize = '18px';
            container.style.textShadow = '0 0 5px black';
            container.style.pointerEvents = 'none';
            container.style.zIndex = '2000';
            container.style.whiteSpace = 'nowrap';
            const point = map.latLngToContainerPoint(latLng);
            container.style.left = point.x + 'px';
            container.style.top = point.y + 'px';
            container.style.transition = 'all 1s ease-out';
            document.body.appendChild(container);
            setTimeout(() => {
                container.style.transform = 'translateY(-40px)';
                container.style.opacity = '0';
                setTimeout(() => container.remove(), 1000);
            }, 10);
        }
        
        // перевірка чи точка на території України (спрощено)
        function isInsideUkraine(lat, lng) {
            return ukraineBounds.contains([lat, lng]);
        }
        
        // відстань в км (гаверсинус)
        function getDistanceKm(lat1, lng1, lat2, lng2) {
            const R = 6371;
            const dLat = (lat2 - lat1) * Math.PI / 180;
            const dLon = (lng2 - lng1) * Math.PI / 180;
            const a = Math.sin(dLat/2)**2 + Math.cos(lat1*Math.PI/180)*Math.cos(lat2*Math.PI/180)* Math.sin(dLon/2)**2;
            return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
        }
        
        // ------ КЛАС ДРОН ------
        class Drone {
            constructor(startLat, startLng, targetCity) {
                this.id = Math.random();
                this.startLat = startLat;
                this.startLng = startLng;
                this.currentLat = startLat;
                this.currentLng = startLng;
                this.targetCity = targetCity; // {lat, lng, name}
                this.progress = 0; // 0..1
                this.hp = 100;
                this.isAlive = true;
                this.speed = 0.012; // відносна швидкість (чим більше, тим швидше)
                this.marker = null;
                this.isBeingJammed = false;
                this.jamEndTime = 0;
            }
            
            update(deltaTime) {
                if(!this.isAlive) return;
                // Ефект глушіння: дрон стоїть на місці або рухається повільніше
                let currentSpeed = this.speed;
                if(this.isBeingJammed && Date.now() < this.jamEndTime) {
                    currentSpeed = 0.005; // сповільнення
                } else {
                    this.isBeingJammed = false;
                }
                
                this.progress += currentSpeed * deltaTime * 0.05;
                if(this.progress >= 1) {
                    this.progress = 1;
                    this.isAlive = false;
                    // дрон досяг цілі – зменшуємо баланс (штраф)
                    playerBalance = Math.max(0, playerBalance - 1500);
                    updateUI();
                    showFloatingText("-1500₴ (місто атаковано)", "#ff6666", [this.currentLat, this.currentLng]);
                    if(this.marker) map.removeLayer(this.marker);
                    return;
                }
                this.currentLat = this.startLat + (this.targetCity.lat - this.startLat) * this.progress;
                this.currentLng = this.startLng + (this.targetCity.lng - this.startLng) * this.progress;
                if(this.marker) {
                    this.marker.setLatLng([this.currentLat, this.currentLng]);
                }
            }
            
            render() {
                if(!this.isAlive) {
                    if(this.marker) map.removeLayer(this.marker);
                    return;
                }
                if(!this.marker) {
                    const droneIcon = L.divIcon({ html: '🚁', iconSize: [24,24], className: 'drone-marker' });
                    this.marker = L.marker([this.currentLat, this.currentLng], { icon: droneIcon, zIndexOffset: 500 }).addTo(map);
                }
                // додатковий стиль якщо дрон приглушений
                if(this.isBeingJammed && this.marker._icon) {
                    this.marker._icon.style.filter = "hue-rotate(180deg)";
                } else if(this.marker._icon) {
                    this.marker._icon.style.filter = "";
                }
            }
            
            destroy() {
                if(!this.isAlive) return;
                this.isAlive = false;
                if(this.marker) map.removeLayer(this.marker);
                totalKills++;
                addMoney(REWARD_PER_KILL);
                updateUI();
                showFloatingText(`+${REWARD_PER_KILL}₴ ЗБИТТЯ!`, "#ffaa44", [this.currentLat, this.currentLng]);
            }
        }
        
        // ------ УСТАНОВКИ ППО ------
        class DefenseUnit {
            constructor(type, lat, lng) {
                this.id = Math.random();
                this.type = type;
                this.lat = lat;
                this.lng = lng;
                this.lastShotTime = 0;
                this.data = UNITS[type];
                this.marker = null;
                this.rangeCircle = null;
                this.ammo = (type === 'pickup') ? 200 : (type === 'shilka') ? 500 : 1;
                this.maxAmmo = this.ammo;
            }
            
            canShoot() {
                return (Date.now() - this.lastShotTime) >= this.data.cooldown && this.ammo > 0 && this.data.damage > 0;
            }
            
            shootAt(drone) {
                if(!this.canShoot()) return false;
                const dist = getDistanceKm(this.lat, this.lng, drone.currentLat, drone.currentLng);
                if(dist <= this.data.range) {
                    this.lastShotTime = Date.now();
                    this.ammo--;
                    drone.hp -= this.data.damage;
                    if(drone.hp <= 0) {
                        drone.destroy();
                    }
                    // візуальний ефект пострілу
                    this.showShotEffect(drone.currentLat, drone.currentLng);
                    return true;
                }
                return false;
            }
            
            jamDrones(dronesList) {
                if(this.type !== 'reb') return;
                for(let drone of dronesList) {
                    const dist = getDistanceKm(this.lat, this.lng, drone.currentLat, drone.currentLng);
                    if(dist <= this.data.jamRadius && drone.isAlive) {
                        drone.isBeingJammed = true;
                        drone.jamEndTime = Date.now() + 3000;
                    }
                }
            }
            
            showShotEffect(lat, lng) {
                const point = map.latLngToContainerPoint([lat, lng]);
                const div = document.createElement('div');
                div.innerHTML = '💥';
                div.style.position = 'absolute';
                div.style.left = point.x + 'px';
                div.style.top = point.y + 'px';
                div.style.fontSize = '24px';
                div.style.pointerEvents = 'none';
                div.style.zIndex = 2000;
                div.style.transition = 'transform 0.2s ease-out';
                document.body.appendChild(div);
                setTimeout(() => {
                    div.style.transform = 'scale(2)';
                    div.style.opacity = '0';
                    setTimeout(() => div.remove(), 300);
                }, 10);
            }
            
            render() {
                if(!this.marker) {
                    const iconHtml = `<div style="font-size:28px; filter:drop-shadow(0 0 5px ${this.data.color});">${this.data.icon}</div>`;
                    const unitIcon = L.divIcon({ html: iconHtml, iconSize: [32,32], className: 'unit-marker' });
                    this.marker = L.marker([this.lat, this.lng], { icon: unitIcon }).addTo(map);
                    const circleStyle = { color: this.data.color, weight: 2, fillColor: this.data.color, fillOpacity: 0.15 };
                    this.rangeCircle = L.circle([this.lat, this.lng], { radius: this.data.range * 1000, ...circleStyle }).addTo(map);
                    if(this.type === 'reb') {
                        this.rangeCircle.setStyle({ color: "#aa88ff", fillColor: "#aa88ff" });
                    }
                    this.marker.bindTooltip(`${this.data.name} | 🎯${this.ammo}/${this.maxAmmo}`, { sticky: true });
                } else {
                    this.marker.setLatLng([this.lat, this.lng]);
                    this.rangeCircle.setLatLng([this.lat, this.lng]);
                    this.marker.setTooltipContent(`${this.data.name} | 🎯${this.ammo}/${this.maxAmmo}`);
                }
            }
        }
        
        // ------ ІГРОВИЙ ЦИКЛ ------
        function gameUpdate() {
            const now = Date.now();
            let delta = Math.min(100, now - lastFrameTime);
            if(delta < 0) delta = 16;
            lastFrameTime = now;
            
            // 1. Оновлюємо дронів
            for(let i=0; i<drones.length; i++) {
                drones[i].update(delta/1000);
                if(!drones[i].isAlive) {
                    if(drones[i].marker) map.removeLayer(drones[i].marker);
                    drones.splice(i,1);
                    i--;
                }
            }
            
            // 2. Глушіння від РЕБ
            for(let unit of deployedUnits) {
                if(unit.type === 'reb') {
                    unit.jamDrones(drones);
                }
            }
            
            // 3. Стрільба по дронах
            for(let unit of deployedUnits) {
                if(unit.data.damage > 0) {
                    for(let drone of drones) {
                        unit.shootAt(drone);
                        if(!drone.isAlive) break;
                    }
                }
            }
            
            // 4. Відмальовка всіх об'єктів
            for(let d of drones) d.render();
            for(let u of deployedUnits) u.render();
            updateUI();
            drawRadar();
        }
        
        // ------ РАДАР ------ 
        function drawRadar() {
            if(!radarCtx) return;
            const canvas = document.getElementById('radarCanvas');
            const w = canvas.width, h = canvas.height;
            radarCtx.clearRect(0,0,w,h);
            radarCtx.fillStyle = '#0a2a1a';
            radarCtx.fillRect(0,0,w,h);
            radarCtx.strokeStyle = '#00ffaa';
            radarCtx.lineWidth = 1;
            radarCtx.beginPath();
            radarCtx.arc(w/2, h/2, w/2-2, 0, 2*Math.PI);
            radarCtx.stroke();
            // центр карти
            const center = map.getCenter();
            const zoom = map.getZoom();
            const rad = (w/2) - 4;
            
            for(let drone of drones) {
                const point = map.latLngToContainerPoint([drone.currentLat, drone.currentLng]);
                const mapSize = map.getSize();
                const rx = (point.x / mapSize.x) * w;
                const ry = (point.y / mapSize.y) * h;
                if(rx >=0 && rx <= w && ry>=0 && ry<=h) {
                    radarCtx.fillStyle = drone.isBeingJammed ? "#ffaa44" : "#ff4444";
                    radarCtx.beginPath();
                    radarCtx.arc(rx, ry, 3, 0, 2*Math.PI);
                    radarCtx.fill();
                }
            }
            for(let unit of deployedUnits) {
                const point = map.latLngToContainerPoint([unit.lat, unit.lng]);
                const mapSize = map.getSize();
                const rx = (point.x / mapSize.x) * w;
                const ry = (point.y / mapSize.y) * h;
                if(rx >=0 && rx <= w && ry>=0 && ry<=h) {
                    radarCtx.fillStyle = unit.data.color;
                    radarCtx.beginPath();
                    radarCtx.rect(rx-2, ry-2, 5,5);
                    radarCtx.fill();
                }
            }
        }
        
        // ------ СПАВН ДРОНІВ ------
        function spawnDrone() {
            if(drones.length >= MAX_DRONES) return;
            // точки запуску (поза межами України або на кордоні)
            const launchPoints = [
                { lat: 48.3, lng: 39.5, name: "Схід" },   // Луганщина
                { lat: 46.2, lng: 33.2, name: "Південь" },
                { lat: 51.5, lng: 31.3, name: "Північ" },
                { lat: 47.8, lng: 35.2, name: "Запоріжжя" }
            ];
            const cities = [
                { lat: 50.45, lng: 30.52, name: "Київ" },
                { lat: 49.99, lng: 36.23, name: "Харків" },
                { lat: 48.46, lng: 35.04, name: "Дніпро" },
                { lat: 46.48, lng: 30.73, name: "Одеса" },
                { lat: 49.84, lng: 24.03, name: "Львів" }
            ];
            const start = launchPoints[Math.floor(Math.random()*launchPoints.length)];
            const target = cities[Math.floor(Math.random()*cities.length)];
            const newDrone = new Drone(start.lat, start.lng, target);
            drones.push(newDrone);
            updateUI();
        }
        
        // ------ ОБРОБНИК РОЗМІЩЕННЯ ------
        function onMapClick(e) {
            if(!selectedUnit) {
                document.getElementById('warningMsg').innerHTML = "⚠️ Оберіть установку в меню!";
                return;
            }
            const { lat, lng } = e.latlng;
            if(!isInsideUkraine(lat, lng)) {
                document.getElementById('warningMsg').innerHTML = "❌ Розміщуйте установки лише на території України!";
                return;
            }
            const unitPrice = UNITS[selectedUnit].price;
            if(playerBalance < unitPrice) {
                document.getElementById('warningMsg').innerHTML = "💰 Недостатньо коштів!";
                return;
            }
            // перевірка на перетин з іншими установками (опціонально)
            playerBalance -= unitPrice;
            const newUnit = new DefenseUnit(selectedUnit, lat, lng);
            deployedUnits.push(newUnit);
            newUnit.render();
            updateUI();
            document.getElementById('warningMsg').innerHTML = `✅ ${UNITS[selectedUnit].name} розміщено!`;
            selectedUnit = null;
            // зняти активний стиль з кнопок
            document.querySelectorAll('.shop-btn').forEach(btn => btn.classList.remove('active'));
        }
        
        // ------ ІНІЦІАЛІЗАЦІЯ ------
        function initGame() {
            // Карта
            map = L.map('map').setView([48.9, 31.5], 6);
            L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
                attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OSM</a> contributors'
            }).addTo(map);
            // полігон України (символічний)
            const ukrPoly = L.polygon([
                [52.3, 22.1], [52.2, 33.5], [50.2, 40.0], [46.0, 40.0], [44.3, 33.9], [45.2, 29.0], [48.0, 22.1]
            ], { color: '#3388ff', weight: 2, fillOpacity: 0.1 }).addTo(map);
            
            radarCtx = document.getElementById('radarCanvas').getContext('2d');
            // налаштування подій
            map.on('click', onMapClick);
            
            // вибір установки через UI
            document.querySelectorAll('.shop-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const unitType = btn.getAttribute('data-unit');
                    if(selectedUnit === unitType) {
                        selectedUnit = null;
                        btn.classList.remove('active');
                        document.getElementById('warningMsg').innerHTML = "⚡ Режим розміщення вимкнено";
                    } else {
                        document.querySelectorAll('.shop-btn').forEach(b => b.classList.remove('active'));
                        selectedUnit = unitType;
                        btn.classList.add('active');
                        document.getElementById('warningMsg').innerHTML = `📍 Розмістіть ${UNITS[unitType].name} на карті`;
                    }
                });
            });
            
            // демо-старт кількох установок для прикладу
            // (коментар, якщо треба стартові)
            // const demoUnit = new DefenseUnit('pickup', 49.0, 31.4);
            // deployedUnits.push(demoUnit); demoUnit.render();
            
            // Запуск ігрового циклу
            lastFrameTime = Date.now();
            setInterval(() => gameUpdate(), 50);
            setInterval(() => spawnDrone(), DRONE_SPAWN_INTERVAL_MS);
            updateUI();
        }
        
        window.onload = initGame;
    })();
</script>
</body>
</html>
