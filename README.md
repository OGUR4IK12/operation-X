<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>ППО України — Захисти небо!</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            margin: 0;
            padding: 0;
            font-family: 'Courier New', 'Monaco', monospace;
            background: #0a0e1a;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }
        #map {
            height: 100vh;
            width: 100%;
            background: #0a1a2a;
        }
        
        /* Верхня панель */
        .top-bar {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            background: linear-gradient(135deg, rgba(10,20,30,0.95), rgba(5,10,15,0.95));
            backdrop-filter: blur(12px);
            z-index: 1000;
            padding: 10px 20px;
            border-bottom: 2px solid #00ffaa;
            box-shadow: 0 0 20px rgba(0,255,170,0.2);
        }
        
        .game-title {
            text-align: center;
            margin-bottom: 8px;
        }
        .game-title h1 {
            color: #00ffaa;
            font-size: 24px;
            letter-spacing: 4px;
            text-shadow: 0 0 10px #00ffaa;
            margin: 0;
        }
        .game-title p {
            color: #88aaff;
            font-size: 10px;
            letter-spacing: 2px;
            margin-top: 4px;
        }
        
        /* Статистика зверху */
        .stats-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
            margin-bottom: 5px;
        }
        .stats-group {
            display: flex;
            gap: 20px;
            background: rgba(0,0,0,0.5);
            padding: 5px 15px;
            border-radius: 30px;
        }
        .stat {
            display: flex;
            align-items: baseline;
            gap: 5px;
        }
        .stat-label {
            font-size: 11px;
            color: #88aaff;
        }
        .stat-value {
            font-size: 20px;
            font-weight: bold;
            color: #00ffaa;
            text-shadow: 0 0 5px #00ffaa;
        }
        .balance-value {
            color: #ffaa44;
            text-shadow: 0 0 5px #ffaa44;
        }
        
        /* Кнопки швидкості */
        .speed-buttons {
            display: flex;
            gap: 8px;
        }
        .speed-btn {
            background: rgba(0,0,0,0.6);
            border: 1px solid #00ffaa;
            color: #00ffaa;
            padding: 4px 12px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            font-size: 12px;
            transition: 0.2s;
        }
        .speed-btn.active {
            background: #00ffaa;
            color: #0a1a2a;
            box-shadow: 0 0 10px #00ffaa;
        }
        
        /* Вкладки магазину */
        .shop-tabs {
            display: flex;
            gap: 5px;
            background: rgba(0,0,0,0.5);
            border-radius: 30px;
            padding: 3px;
        }
        .tab-btn {
            background: transparent;
            border: none;
            color: #88aaff;
            padding: 5px 15px;
            border-radius: 25px;
            cursor: pointer;
            font-weight: bold;
            transition: 0.2s;
        }
        .tab-btn.active {
            background: #00ffaa;
            color: #0a1a2a;
        }
        
        /* Панель магазину (з'являється при активній вкладці) */
        .shop-panel {
            position: fixed;
            bottom: 20px;
            left: 20px;
            right: 20px;
            background: rgba(10, 20, 30, 0.95);
            backdrop-filter: blur(12px);
            border-radius: 16px;
            padding: 15px 20px;
            border: 1px solid rgba(0, 255, 170, 0.3);
            z-index: 1000;
            transform: translateY(100%);
            transition: transform 0.3s ease;
        }
        .shop-panel.open {
            transform: translateY(0);
        }
        .shop-items {
            display: flex;
            gap: 20px;
            justify-content: center;
            flex-wrap: wrap;
        }
        .shop-item {
            background: linear-gradient(135deg, #1a2a3a, #0a1a2a);
            border: 1px solid #00ffaa;
            border-radius: 12px;
            padding: 10px 20px;
            text-align: center;
            cursor: pointer;
            transition: 0.2s;
            min-width: 120px;
        }
        .shop-item:hover, .shop-item.active {
            background: #00ffaa;
            color: #0a1a2a;
            transform: scale(1.02);
        }
        .shop-item .item-icon {
            font-size: 32px;
        }
        .shop-item .item-name {
            font-weight: bold;
            margin: 5px 0;
        }
        .shop-item .item-price {
            font-size: 12px;
            color: #ffaa44;
        }
        
        /* Панель попереджень */
        .alert-panel {
            position: fixed;
            top: 100px;
            right: 20px;
            background: rgba(0,0,0,0.8);
            border-left: 4px solid #ff4444;
            border-radius: 8px;
            padding: 10px 15px;
            min-width: 200px;
            z-index: 1000;
            animation: pulse 0.5s infinite alternate;
        }
        .alert-title {
            color: #ff4444;
            font-weight: bold;
            margin-bottom: 5px;
        }
        .alert-message {
            color: #ff8888;
            font-size: 12px;
        }
        
        /* Радар */
        .radar-container {
            position: fixed;
            top: 120px;
            left: 20px;
            width: 150px;
            height: 150px;
            background: rgba(0, 20, 0, 0.7);
            border-radius: 50%;
            border: 2px solid #00ffaa;
            z-index: 1000;
        }
        canvas#radarCanvas {
            width: 100%;
            height: 100%;
            border-radius: 50%;
        }
        
        .warning-text {
            position: fixed;
            bottom: 100px;
            left: 20px;
            background: rgba(255,50,50,0.3);
            padding: 5px 12px;
            border-radius: 20px;
            color: #ff8888;
            font-size: 11px;
            z-index: 1000;
        }
        
        @keyframes pulse {
            0% { opacity: 0.6; border-left-color: #ff4444; }
            100% { opacity: 1; border-left-color: #ff0000; }
        }
        
        @keyframes blink {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }
        .threat-blink {
            animation: blink 0.5s infinite;
        }
        
        @media (max-width: 800px) {
            .stats-row { flex-direction: column; align-items: stretch; }
            .stats-group { justify-content: center; }
            .radar-container { width: 100px; height: 100px; top: 100px; left: 10px; }
            .alert-panel { top: 100px; right: 10px; min-width: 150px; }
            .shop-item { min-width: 100px; padding: 8px 12px; }
        }
    </style>
</head>
<body>
<div id="map"></div>

<div class="top-bar">
    <div class="game-title">
        <h1>⚡ ППО УКРАЇНИ ⚡</h1>
        <p>ЗАХИСТИ НЕБО ВІД ВОРОЖИХ ДРОНІВ</p>
    </div>
    <div class="stats-row">
        <div class="stats-group">
            <div class="stat"><span class="stat-label">💰</span><span class="stat-value balance-value" id="balanceDisplay">15000</span></div>
            <div class="stat"><span class="stat-label">🎯</span><span class="stat-value" id="killsDisplay">0</span></div>
            <div class="stat"><span class="stat-label">🚁</span><span class="stat-value" id="dronesCount">0</span></div>
        </div>
        <div class="speed-buttons">
            <div class="speed-btn" data-speed="1">1x</div>
            <div class="speed-btn" data-speed="10">10x</div>
            <div class="speed-btn" data-speed="30">30x</div>
            <div class="speed-btn active" data-speed="50">50x</div>
        </div>
        <div class="shop-tabs">
            <button class="tab-btn active" data-tab="shop">🏪 МАГАЗИН</button>
            <button class="tab-btn" data-tab="stats">📊 СТАТИСТИКА</button>
        </div>
    </div>
</div>

<div class="radar-container">
    <canvas id="radarCanvas" width="200" height="200"></canvas>
</div>

<div class="alert-panel" id="alertPanel" style="display: none;">
    <div class="alert-title">⚠️ НЕБЕЗПЕКА!</div>
    <div class="alert-message" id="alertMessage">Шахед наближається!</div>
</div>

<div class="warning-text" id="warningMsg">🛒 Відкрийте магазин та оберіть зброю →</div>

<div class="shop-panel" id="shopPanel">
    <div class="shop-items">
        <div class="shop-item" data-unit="pickup">
            <div class="item-icon">🔫</div>
            <div class="item-name">Пікап 12.7мм</div>
            <div class="item-price">5500 ₴</div>
        </div>
        <div class="shop-item" data-unit="shilka">
            <div class="item-icon">⚡</div>
            <div class="item-name">ЗСУ-23-4 "Шилка"</div>
            <div class="item-price">12000 ₴</div>
        </div>
        <div class="shop-item" data-unit="reb">
            <div class="item-icon">📡</div>
            <div class="item-name">РЕБ (Глушіння)</div>
            <div class="item-price">8000 ₴</div>
        </div>
    </div>
    <div style="text-align: center; margin-top: 10px; font-size: 11px; color: #88aaff;">
        💡 Клікніть на установку в магазині, потім на карті України
    </div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
(function(){
    // ========== КОНФІГУРАЦІЯ ==========
    const CITIES = [
        { name: "Київ", lat: 50.45, lng: 30.52 },
        { name: "Харків", lat: 49.99, lng: 36.23 },
        { name: "Дніпро", lat: 48.46, lng: 35.04 },
        { name: "Одеса", lat: 46.48, lng: 30.73 },
        { name: "Львів", lat: 49.84, lng: 24.03 },
        { name: "Запоріжжя", lat: 47.84, lng: 35.14 },
        { name: "Вінниця", lat: 49.23, lng: 28.48 },
        { name: "Черкаси", lat: 49.44, lng: 32.06 },
        { name: "Полтава", lat: 49.59, lng: 34.55 },
        { name: "Суми", lat: 50.91, lng: 34.80 }
    ];
    
    const LAUNCH_POINTS = [
        { name: "Схід", lat: 48.3, lng: 39.5 },
        { name: "Південь", lat: 46.2, lng: 33.2 },
        { name: "Північ", lat: 51.5, lng: 31.3 },
        { name: "Схід-2", lat: 47.5, lng: 38.0 },
        { name: "Крим", lat: 45.2, lng: 34.5 }
    ];
    
    const UNITS = {
        pickup: { name: "Пікап 12.7мм", price: 5500, range: 80, cooldown: 1800, damage: 45, color: "#ffaa44", icon: "🔫", img: "resources/pulemet.png" },
        shilka: { name: "Шилка 23мм", price: 12000, range: 120, cooldown: 900, damage: 85, color: "#ff6644", icon: "⚡", img: "resources/shilka.png" },
        reb: { name: "РЕБ", price: 8000, range: 45, cooldown: 2500, damage: 0, isJammer: true, jamRadius: 50, color: "#aa88ff", icon: "📡", img: "resources/reb.png" }
    };
    
    // Межі України (полігон для перевірки)
    const UKRAINE_POLYGON = [
        [52.3, 22.1], [52.2, 33.5], [50.2, 40.0], [46.0, 40.0], 
        [44.3, 33.9], [45.2, 29.0], [48.0, 22.1]
    ];
    
    // Швидкість гри
    let gameSpeed = 50;
    let playerBalance = 15000;
    let totalKills = 0;
    let selectedUnit = null;
    let deployedUnits = [];
    let drones = [];
    let map, radarCtx, lastFrameTime = 0;
    let lastSpawnTime = 0;
    let alertVisible = false;
    
    // Функція перевірки чи точка в межах України
    function isInsideUkraine(lat, lng) {
        // Використовуємо Leaflet для перевірки належності до полігону
        const polygon = L.polygon(UKRAINE_POLYGON);
        return polygon.getBounds().contains([lat, lng]) && 
               pointInPolygon(lat, lng, UKRAINE_POLYGON);
    }
    
    function pointInPolygon(lat, lng, polygon) {
        let inside = false;
        for (let i = 0, j = polygon.length-1; i < polygon.length; j = i++) {
            const xi = polygon[i][0], yi = polygon[i][1];
            const xj = polygon[j][0], yj = polygon[j][1];
            const intersect = ((yi > lng) != (yj > lng)) &&
                (lat < (xj - xi) * (lng - yi) / (yj - yi) + xi);
            if (intersect) inside = !inside;
        }
        return inside;
    }
    
    function updateUI() {
        document.getElementById('balanceDisplay').innerText = Math.floor(playerBalance);
        document.getElementById('killsDisplay').innerText = totalKills;
        document.getElementById('dronesCount').innerText = drones.length;
        
        // Оновлення панелі попереджень
        const threatDrones = drones.filter(d => d.isAlive && !d.isJammed && d.progress > 0.3);
        const alertPanel = document.getElementById('alertPanel');
        const alertMsg = document.getElementById('alertMessage');
        
        if(threatDrones.length > 0) {
            alertPanel.style.display = 'block';
            const nearest = threatDrones[0];
            const remaining = Math.round((1 - nearest.progress) * 100);
            alertMsg.innerHTML = `🚁 Шахед наближається! ${remaining}% до цілі (${nearest.target.name})`;
            alertPanel.classList.add('threat-blink');
        } else if(drones.length > 0) {
            alertPanel.style.display = 'block';
            alertMsg.innerHTML = `⚠️ У небі ${drones.length} ворожих дронів!`;
            alertPanel.classList.remove('threat-blink');
        } else {
            alertPanel.style.display = 'none';
        }
    }
    
    function addMoney(amount) {
        playerBalance += amount;
        updateUI();
        showFloatingText(`+${amount}₴`, '#00ffaa');
    }
    
    function showFloatingText(text, color, latLng = null) {
        if(!latLng && map) latLng = map.getCenter();
        if(!latLng) return;
        const point = map.latLngToContainerPoint(latLng);
        const div = document.createElement('div');
        div.textContent = text;
        div.style.cssText = `position:absolute; left:${point.x}px; top:${point.y}px; color:${color}; font-weight:bold; font-size:18px; text-shadow:0 0 5px black; pointer-events:none; z-index:2000; transition:all 1s ease-out; white-space:nowrap;`;
        document.body.appendChild(div);
        setTimeout(() => { div.style.transform = 'translateY(-40px)'; div.style.opacity = '0'; setTimeout(() => div.remove(), 1000); }, 10);
    }
    
    function getDistanceKm(lat1, lng1, lat2, lng2) {
        const R = 6371;
        const dLat = (lat2 - lat1) * Math.PI / 180;
        const dLon = (lng2 - lng1) * Math.PI / 180;
        const a = Math.sin(dLat/2)**2 + Math.cos(lat1*Math.PI/180) * Math.cos(lat2*Math.PI/180) * Math.sin(dLon/2)**2;
        return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
    }
    
    // ========== КЛАС ДРОНА ==========
    class Drone {
        constructor(start, target) {
            this.id = Math.random();
            this.startLat = start.lat;
            this.startLng = start.lng;
            this.target = target;
            this.currentLat = start.lat;
            this.currentLng = start.lng;
            this.progress = 0;
            this.hp = 100;
            this.isAlive = true;
            this.speed = 0.012;
            this.isJammed = false;
            this.jamEndTime = 0;
            this.marker = null;
        }
        
        update(deltaTime) {
            if(!this.isAlive) return;
            
            // Глушіння
            if(this.isJammed && Date.now() < this.jamEndTime) {
                return;
            } else {
                this.isJammed = false;
            }
            
            this.progress += this.speed * deltaTime * (gameSpeed / 30);
            if(this.progress >= 1) {
                this.isAlive = false;
                playerBalance = Math.max(0, playerBalance - 1200);
                showFloatingText(`-1200₴ (${this.target.name} атаковано)`, '#ff6666', [this.currentLat, this.currentLng]);
                if(this.marker) map.removeLayer(this.marker);
                updateUI();
                return;
            }
            this.currentLat = this.startLat + (this.target.lat - this.startLat) * this.progress;
            this.currentLng = this.startLng + (this.target.lng - this.startLng) * this.progress;
            if(this.marker) this.marker.setLatLng([this.currentLat, this.currentLng]);
        }
        
        render() {
            if(!this.isAlive) return;
            if(!this.marker) {
                // Використовуємо emoji, але можна замінити на geran2.png
                const droneIcon = L.divIcon({ html: '🚁', iconSize: [28,28], className: 'drone-icon' });
                this.marker = L.marker([this.currentLat, this.currentLng], { icon: droneIcon }).addTo(map);
            }
            if(this.marker._icon) {
                this.marker._icon.style.filter = this.isJammed ? 'hue-rotate(180deg)' : '';
                this.marker._icon.style.opacity = this.isJammed ? '0.7' : '1';
            }
        }
        
        destroy() {
            if(!this.isAlive) return;
            this.isAlive = false;
            if(this.marker) map.removeLayer(this.marker);
            totalKills++;
            addMoney(900);
            updateUI();
            showFloatingText('💥 ЗБИТО! +900₴', '#ffaa44', [this.currentLat, this.currentLng]);
        }
        
        applyJam(duration = 3000) {
            this.isJammed = true;
            this.jamEndTime = Date.now() + duration;
        }
    }
    
    // ========== КЛАС УСТАНОВКИ ==========
    class DefenseUnit {
        constructor(type, lat, lng) {
            this.type = type;
            this.lat = lat;
            this.lng = lng;
            this.data = UNITS[type];
            this.lastShotTime = 0;
            this.marker = null;
            this.rangeCircle = null;
            this.ammo = (type === 'pickup') ? 250 : (type === 'shilka') ? 600 : 1;
        }
        
        canShoot() {
            return Date.now() - this.lastShotTime >= this.data.cooldown && this.ammo > 0 && this.data.damage > 0;
        }
        
        shootAt(drone) {
            if(!this.canShoot()) return false;
            const dist = getDistanceKm(this.lat, this.lng, drone.currentLat, drone.currentLng);
            if(dist <= this.data.range) {
                this.lastShotTime = Date.now();
                this.ammo--;
                drone.hp -= this.data.damage;
                this.showTrail(drone.currentLat, drone.currentLng);
                if(drone.hp <= 0) drone.destroy();
                return true;
            }
            return false;
        }
        
        jamDrones(dronesList) {
            if(this.type !== 'reb') return;
            for(let drone of dronesList) {
                const dist = getDistanceKm(this.lat, this.lng, drone.currentLat, drone.currentLng);
                if(dist <= this.data.jamRadius && drone.isAlive && !drone.isJammed) {
                    drone.applyJam(3500);
                }
            }
        }
        
        showTrail(lat, lng) {
            const point = map.latLngToContainerPoint([lat, lng]);
            const div = document.createElement('div');
            div.innerHTML = '✨';
            div.style.cssText = `position:absolute; left:${point.x}px; top:${point.y}px; font-size:20px; pointer-events:none; z-index:2000; transition:all 0.2s ease-out;`;
            document.body.appendChild(div);
            setTimeout(() => { div.style.transform = 'scale(1.5)'; div.style.opacity = '0'; setTimeout(() => div.remove(), 300); }, 10);
        }
        
        render() {
            if(!this.marker) {
                // Використовуємо emoji, але можна замінити на png з resources/
                this.marker = L.marker([this.lat, this.lng], { 
                    icon: L.divIcon({ html: this.data.icon, iconSize: [32,32], className: 'unit-icon' })
                }).addTo(map);
                this.rangeCircle = L.circle([this.lat, this.lng], {
                    radius: this.data.range * 1000,
                    color: this.data.color,
                    fillColor: this.data.color,
                    fillOpacity: 0.1,
                    weight: 2
                }).addTo(map);
            }
            if(this.marker._icon) {
                this.marker._icon.style.filter = `drop-shadow(0 0 5px ${this.data.color})`;
            }
            if(this.marker.getTooltip()) {
                this.marker.setTooltipContent(`${this.data.name} | 🎯${this.ammo}`);
            } else {
                this.marker.bindTooltip(`${this.data.name} | 🎯${this.ammo}`, { sticky: true });
            }
        }
    }
    
    // ========== РАДАР ==========
    function drawRadar() {
        if(!radarCtx || !map) return;
        const canvas = document.getElementById('radarCanvas');
        const w = canvas.width, h = canvas.height;
        radarCtx.clearRect(0, 0, w, h);
        radarCtx.fillStyle = '#0a2a1a';
        radarCtx.fillRect(0, 0, w, h);
        radarCtx.strokeStyle = '#00ffaa';
        radarCtx.beginPath();
        radarCtx.arc(w/2, h/2, w/2 - 3, 0, 2*Math.PI);
        radarCtx.stroke();
        
        const mapSize = map.getSize();
        for(let drone of drones) {
            if(!drone.isAlive) continue;
            const point = map.latLngToContainerPoint([drone.currentLat, drone.currentLng]);
            const rx = (point.x / mapSize.x) * w;
            const ry = (point.y / mapSize.y) * h;
            if(rx >= 0 && rx <= w && ry >= 0 && ry <= h) {
                radarCtx.fillStyle = drone.isJammed ? '#ffaa44' : '#ff4444';
                radarCtx.beginPath();
                radarCtx.arc(rx, ry, 4, 0, 2*Math.PI);
                radarCtx.fill();
            }
        }
        for(let unit of deployedUnits) {
            const point = map.latLngToContainerPoint([unit.lat, unit.lng]);
            const rx = (point.x / mapSize.x) * w;
            const ry = (point.y / mapSize.y) * h;
            if(rx >= 0 && rx <= w && ry >= 0 && ry <= h) {
                radarCtx.fillStyle = unit.data.color;
                radarCtx.fillRect(rx-3, ry-3, 6, 6);
            }
        }
    }
    
    // ========== ІГРОВИЙ ЦИКЛ ==========
    function gameUpdate() {
        const now = Date.now();
        let dt = Math.min(100, now - lastFrameTime);
        if(dt < 0) dt = 16;
        lastFrameTime = now;
        
        for(let i=0; i<drones.length; i++) {
            drones[i].update(dt / 1000);
            if(!drones[i].isAlive) {
                drones.splice(i,1);
                i--;
            }
        }
        
        for(let unit of deployedUnits) {
            if(unit.type === 'reb') unit.jamDrones(drones);
        }
        
        for(let unit of deployedUnits) {
            if(unit.data.damage > 0) {
                for(let drone of drones) {
                    unit.shootAt(drone);
                    if(!drone.isAlive) break;
                }
            }
        }
        
        for(let unit of deployedUnits) unit.render();
        for(let drone of drones) drone.render();
        drawRadar();
        updateUI();
    }
    
    // ========== СПАВН ДРОНІВ ==========
    function spawnDrone() {
        const now = Date.now();
        const interval = Math.max(2000, 5000 - Math.floor(totalKills / 20) * 100);
        if(now - lastSpawnTime < interval) return;
        if(drones.length >= 25) return;
        
        const start = LAUNCH_POINTS[Math.floor(Math.random() * LAUNCH_POINTS.length)];
        const target = CITIES[Math.floor(Math.random() * CITIES.length)];
        const newDrone = new Drone(start, target);
        drones.push(newDrone);
        lastSpawnTime = now;
        updateUI();
    }
    
    // ========== ВСТАНОВЛЕННЯ ШВИДКОСТІ ==========
    function setSpeed(speed) {
        gameSpeed = speed;
        document.querySelectorAll('.speed-btn').forEach(btn => {
            if(parseInt(btn.dataset.speed) === speed) {
                btn.classList.add('active');
            } else {
                btn.classList.remove('active');
            }
        });
    }
    
    // ========== ІНІЦІАЛІЗАЦІЯ ==========
    function init() {
        // Карта (повний екран)
        map = L.map('map').setView([48.9, 31.5], 6);
        L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
            attribution: '© OpenStreetMap'
        }).addTo(map);
        
        // Полігон України
        const ukrainePoly = L.polygon(UKRAINE_POLYGON, { 
            color: '#3388ff', 
            weight: 2, 
            fillOpacity: 0.05,
            className: 'ukraine-border'
        }).addTo(map);
        
        radarCtx = document.getElementById('radarCanvas').getContext('2d');
        
        // Клік по карті для розміщення установки
        map.on('click', (e) => {
            if(!selectedUnit) {
                document.getElementById('warningMsg').innerHTML = "⚠️ Оберіть установку в магазині!";
                document.getElementById('warningMsg').style.color = "#ff8888";
                return;
            }
            if(!isInsideUkraine(e.latlng.lat, e.latlng.lng)) {
                document.getElementById('warningMsg').innerHTML = "❌ Можна ставити лише на території України!";
                document.getElementById('warningMsg').style.color = "#ff8888";
                setTimeout(() => {
                    document.getElementById('warningMsg').innerHTML = "🛒 Відкрийте магазин та оберіть зброю →";
                    document.getElementById('warningMsg').style.color = "#88aaff";
                }, 2000);
                return;
            }
            const price = UNITS[selectedUnit].price;
            if(playerBalance < price) {
                document.getElementById('warningMsg').innerHTML = "💰 Недостатньо коштів!";
                setTimeout(() => {
                    document.getElementById('warningMsg').innerHTML = "🛒 Відкрийте магазин та оберіть зброю →";
                }, 2000);
                return;
            }
            playerBalance -= price;
            const newUnit = new DefenseUnit(selectedUnit, e.latlng.lat, e.latlng.lng);
            deployedUnits.push(newUnit);
            newUnit.render();
            updateUI();
            document.getElementById('warningMsg').innerHTML = `✅ ${UNITS[selectedUnit].name} розміщено!`;
            setTimeout(() => {
                document.getElementById('warningMsg').innerHTML = "🛒 Відкрийте магазин та оберіть зброю →";
            }, 2000);
            selectedUnit = null;
            document.querySelectorAll('.shop-item').forEach(item => item.classList.remove('active'));
        });
        
        // Вибір установки з магазину
        document.querySelectorAll('.shop-item').forEach(item => {
            item.addEventListener('click', () => {
                const unitType = item.dataset.unit;
                if(selectedUnit === unitType) {
                    selectedUnit = null;
                    item.classList.remove('active');
                    document.getElementById('warningMsg').innerHTML = "⚡ Режим розміщення вимкнено";
                } else {
                    document.querySelectorAll('.shop-item').forEach(i => i.classList.remove('active'));
                    selectedUnit = unitType;
                    item.classList.add('active');
                    document.getElementById('warningMsg').innerHTML = `📍 Розмістіть ${UNITS[selectedUnit].name} на карті України`;
                    document.getElementById('warningMsg').style.color = "#00ffaa";
                }
            });
        });
        
        // Кнопки швидкості
        document.querySelectorAll('.speed-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                const speed = parseInt(btn.dataset.speed);
                setSpeed(speed);
            });
        });
        
        // Вкладки магазину/статистики
        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                const tab = btn.dataset.tab;
                document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                
                if(tab === 'shop') {
                    document.getElementById('shopPanel').classList.add('open');
                } else {
                    document.getElementById('shopPanel').classList.remove('open');
                }
            });
        });
        
        // Відкриваємо магазин за замовчуванням
        document.getElementById('shopPanel').classList.add('open');
        
        // Запуск ігрових інтервалів
        lastFrameTime = Date.now();
        setInterval(gameUpdate, 50);
        setInterval(spawnDrone, 500);
        updateUI();
    }
    
    window.onload = init;
})();
</script>
</body>
</html>
