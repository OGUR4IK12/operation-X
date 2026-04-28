<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>SAM PROTOCOL — ППО України</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }
        
        body {
            background: #0a0a0a;
            font-family: 'Courier New', 'Fira Code', 'Monaco', monospace;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }
        
        /* Термінальний стиль */
        .terminal {
            background: #0a0a0a;
            color: #00ff88;
            height: 100vh;
            width: 100vw;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden;
        }
        
        /* Верхня панель як у SAM Protocol */
        .top-bar {
            background: #050505;
            border-bottom: 1px solid #00ff44;
            padding: 8px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
            z-index: 100;
            font-size: 12px;
        }
        
        .logo-area {
            display: flex;
            align-items: baseline;
            gap: 15px;
        }
        
        .logo {
            color: #00ff88;
            font-weight: bold;
            font-size: 14px;
            letter-spacing: 2px;
        }
        
        .menu-links {
            display: flex;
            gap: 20px;
        }
        
        .menu-link {
            color: #00aa55;
            cursor: pointer;
            transition: 0.2s;
            font-size: 11px;
        }
        
        .menu-link:hover, .menu-link.active {
            color: #00ff88;
            text-shadow: 0 0 5px #00ff88;
        }
        
        .balance-area {
            color: #ffaa44;
            font-weight: bold;
        }
        
        .time-area {
            color: #5588aa;
            font-family: monospace;
        }
        
        /* Друга панель (області) */
        .region-bar {
            background: #0a100a;
            border-bottom: 1px solid #00aa44;
            padding: 6px 16px;
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
            font-size: 10px;
            z-index: 99;
        }
        
        .region {
            color: #44aa66;
            cursor: pointer;
            transition: 0.2s;
        }
        
        .region:hover, .region.active {
            color: #88ffaa;
            text-shadow: 0 0 3px #88ffaa;
        }
        
        /* Мапа */
        #map {
            flex: 1;
            width: 100%;
            background: #0a1a0a;
            z-index: 1;
        }
        
        /* Термінальне вікно магазину */
        .terminal-window {
            position: fixed;
            bottom: 20px;
            left: 20px;
            right: 20px;
            background: #0a0a0a;
            border: 1px solid #00ff44;
            border-radius: 0;
            padding: 0;
            z-index: 1000;
            display: none;
            flex-direction: column;
            font-family: monospace;
            box-shadow: 0 0 20px rgba(0,255,68,0.1);
        }
        
        .terminal-window.open {
            display: flex;
        }
        
        .terminal-header {
            background: #00aa44;
            color: #0a0a0a;
            padding: 6px 12px;
            font-weight: bold;
            display: flex;
            justify-content: space-between;
        }
        
        .terminal-body {
            padding: 15px;
            max-height: 60vh;
            overflow-y: auto;
        }
        
        .shop-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 12px;
        }
        
        .shop-card {
            border: 1px solid #00aa44;
            padding: 12px;
            cursor: pointer;
            transition: 0.2s;
            background: #0a150a;
        }
        
        .shop-card:hover, .shop-card.active {
            background: #00aa44;
            color: #0a0a0a;
            border-color: #00ff88;
        }
        
        .shop-card .price {
            color: #ffaa44;
            font-size: 11px;
            margin-top: 8px;
        }
        
        /* Радар */
        .radar-panel {
            position: fixed;
            bottom: 20px;
            right: 20px;
            width: 160px;
            background: #050505;
            border: 1px solid #00aa44;
            padding: 10px;
            z-index: 100;
        }
        
        .radar-title {
            font-size: 9px;
            color: #00aa44;
            text-align: center;
            margin-bottom: 5px;
        }
        
        canvas#radarCanvas {
            width: 100%;
            height: 100%;
            background: #0a1a0a;
        }
        
        /* Панель моніторингу */
        .monitor-panel {
            position: fixed;
            top: 100px;
            right: 20px;
            width: 220px;
            background: #0a0a0a;
            border: 1px solid #00aa44;
            padding: 10px;
            z-index: 100;
            font-size: 10px;
        }
        
        .monitor-title {
            color: #00ff88;
            border-bottom: 1px solid #00aa44;
            padding-bottom: 3px;
            margin-bottom: 8px;
        }
        
        .monitor-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 4px;
            color: #66aa88;
        }
        
        .threat-active {
            color: #ff4444;
            animation: blink 0.5s infinite;
        }
        
        /* Статус бар (нижній) */
        .status-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: #050505;
            border-top: 1px solid #00aa44;
            padding: 4px 16px;
            display: flex;
            justify-content: space-between;
            font-size: 10px;
            color: #558866;
            z-index: 100;
        }
        
        @keyframes blink {
            0% { opacity: 1; text-shadow: 0 0 0 red; }
            100% { opacity: 0.7; text-shadow: 0 0 5px red; }
        }
        
        /* Стилі Leaflet */
        .leaflet-control-attribution {
            background: rgba(0,0,0,0.7) !important;
            color: #448866 !important;
            font-size: 8px !important;
        }
        
        .leaflet-tile-pane {
            filter: grayscale(0.3) contrast(1.2) brightness(0.6);
        }
        
        /* Скролл */
        ::-webkit-scrollbar {
            width: 6px;
            background: #0a0a0a;
        }
        ::-webkit-scrollbar-thumb {
            background: #00aa44;
            border-radius: 0;
        }
        
        @media (max-width: 800px) {
            .top-bar { flex-direction: column; align-items: stretch; }
            .monitor-panel { display: none; }
            .radar-panel { width: 100px; bottom: 40px; }
        }
    </style>
</head>
<body>
<div class="terminal">
    <!-- Верхня панель як у SAM Protocol -->
    <div class="top-bar">
        <div class="logo-area">
            <div class="logo">▶ SAM PROTOCOL (DEMO)</div>
            <div class="menu-links">
                <span class="menu-link" data-tab="shop">МАГАЗИН</span>
                <span class="menu-link" data-tab="stats">ДАНІ</span>
                <span class="menu-link" data-tab="radar">РАДАР</span>
            </div>
        </div>
        <div class="balance-area">💰 БАЛАНС: <span id="balanceDisplay">15000</span> ₴</div>
        <div class="time-area" id="timeDisplay">ДЕНЬ 1 | 09:41</div>
    </div>
    
    <!-- Панель областей -->
    <div class="region-bar" id="regionBar">
        <span class="region active" data-region="poltava">POLTAVA OBLAST</span>
        <span class="region" data-region="kharkiv">KHARKIV OBLAST</span>
        <span class="region" data-region="dnipro">DNIPROPETROVSK OBLAST</span>
        <span class="region" data-region="donetsk">DONETSK OBLAST</span>
    </div>
    
    <!-- Мапа -->
    <div id="map"></div>
    
    <!-- Радар -->
    <div class="radar-panel">
        <div class="radar-title">⚡ RADAR ARRAY ⚡</div>
        <canvas id="radarCanvas" width="140" height="140"></canvas>
    </div>
    
    <!-- Моніторинг -->
    <div class="monitor-panel">
        <div class="monitor-title">📡 THREAT MONITOR</div>
        <div class="monitor-row">АКТИВНІ ЦІЛІ: <span id="threatCount">0</span></div>
        <div class="monitor-row">ЗБИТО: <span id="killsDisplay">0</span></div>
        <div class="monitor-row" id="threatWarning">СТАТУС: 🔴 НОРМАЛЬНО</div>
    </div>
    
    <!-- Термінальне вікно магазину -->
    <div class="terminal-window" id="shopWindow">
        <div class="terminal-header">
            <span>⚡ SAM PROTOCOL — ТЕРМІНАЛ ЗАКУПІВЛІ ⚡</span>
            <span style="cursor:pointer" id="closeShop">[X]</span>
        </div>
        <div class="terminal-body">
            <div class="shop-grid" id="shopGrid">
                <div class="shop-card" data-unit="pickup">
                    <div>🔫 ПІКАП 12.7мм</div>
                    <div class="price">5 500 ₴</div>
                </div>
                <div class="shop-card" data-unit="shilka">
                    <div>⚡ ЗСУ-23-4 "ШИЛКА"</div>
                    <div class="price">12 000 ₴</div>
                </div>
                <div class="shop-card" data-unit="reb">
                    <div>📡 РЕБ (ГЛУШІННЯ)</div>
                    <div class="price">8 000 ₴</div>
                </div>
            </div>
            <div style="margin-top: 12px; font-size: 10px; color: #448866; text-align: center;">
                > ОБЕРІТЬ ЗБРОЮ ТА КЛІКНІТЬ НА КАРТІ
            </div>
        </div>
    </div>
    
    <!-- Статус-бар -->
    <div class="status-bar">
        <span>Шаблон - (928373)</span>
        <span>SAM Protocol (DEMO) by C</span>
        <span id="clockStatus">19:39 | 28.04.2026</span>
    </div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
(function(){
    // ========== КОНФІГ ==========
    const CITIES = [
        { name: "ПОЛТАВА", lat: 49.59, lng: 34.55, region: "poltava" },
        { name: "КРЕМЕНЧУК", lat: 49.07, lng: 33.42, region: "poltava" },
        { name: "ХАРКІВ", lat: 49.99, lng: 36.23, region: "kharkiv" },
        { name: "КРАМАТОРСЬК", lat: 48.72, lng: 37.58, region: "donetsk" },
        { name: "ДНІПРО", lat: 48.46, lng: 35.04, region: "dnipro" },
        { name: "СВЄРОДОНЕЦЬК", lat: 48.63, lng: 38.54, region: "donetsk" },
        { name: "ЧЕРКАСИ", lat: 49.44, lng: 32.06, region: "poltava" }
    ];
    
    const LAUNCH_POINTS = [
        { name: "СХІД", lat: 48.3, lng: 39.5 },
        { name: "ПІВДЕНЬ", lat: 46.2, lng: 33.2 },
        { name: "ПІВНІЧ", lat: 51.5, lng: 31.3 },
        { name: "БІЛГОРОД", lat: 50.6, lng: 36.6 }
    ];
    
    const UNITS = {
        pickup: { name: "ПІКАП 12.7мм", price: 5500, range: 80, cooldown: 1600, damage: 45, color: "#ffaa44", icon: "🔫" },
        shilka: { name: "ШИЛКА 23мм", price: 12000, range: 120, cooldown: 800, damage: 85, color: "#ff6644", icon: "⚡" },
        reb: { name: "РЕБ", price: 8000, range: 45, cooldown: 2000, damage: 0, isJammer: true, jamRadius: 50, color: "#aa88ff", icon: "📡" }
    };
    
    // Полігон України
    const UKRAINE_POLYGON = [
        [52.3, 22.1], [52.2, 33.5], [50.2, 40.0], [46.0, 40.0], 
        [44.3, 33.9], [45.2, 29.0], [48.0, 22.1]
    ];
    
    // Змінні гри
    let playerBalance = 15000;
    let totalKills = 0;
    let selectedUnit = null;
    let deployedUnits = [];
    let drones = [];
    let map, radarCtx, lastFrameTime = 0;
    let lastSpawnTime = 0;
    let gameSpeed = 30;
    let currentRegion = "poltava";
    let gameTime = { day: 1, hour: 9, minute: 41 };
    
    // Функції
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
    
    function isInsideUkraine(lat, lng) {
        return pointInPolygon(lat, lng, UKRAINE_POLYGON);
    }
    
    function updateUI() {
        document.getElementById('balanceDisplay').innerText = Math.floor(playerBalance).toLocaleString();
        document.getElementById('killsDisplay').innerText = totalKills;
        document.getElementById('threatCount').innerText = drones.filter(d => d.isAlive && !d.isJammed).length;
        
        const threatWarning = document.getElementById('threatWarning');
        if(drones.length > 5) {
            threatWarning.innerHTML = "СТАТУС: 🔴 КРИТИЧНА НЕБЕЗПЕКА!";
            threatWarning.className = "monitor-row threat-active";
        } else if(drones.length > 0) {
            threatWarning.innerHTML = "СТАТУС: 🟡 УВАГА! ШАХЕДИ В НЕБІ";
            threatWarning.className = "monitor-row";
        } else {
            threatWarning.innerHTML = "СТАТУС: 🟢 НОРМАЛЬНО";
            threatWarning.className = "monitor-row";
        }
        
        // Час
        const timeStr = `ДЕНЬ ${gameTime.day} | ${String(gameTime.hour).padStart(2,'0')}:${String(gameTime.minute).padStart(2,'0')}`;
        document.getElementById('timeDisplay').innerHTML = timeStr;
        
        // Статус бар час
        const now = new Date();
        document.getElementById('clockStatus').innerHTML = `${String(gameTime.hour).padStart(2,'0')}:${String(gameTime.minute).padStart(2,'0')} | ${String(now.getDate()).padStart(2,'0')}.${String(now.getMonth()+1).padStart(2,'0')}.2026`;
    }
    
    function addMoney(amount) {
        playerBalance += amount;
        updateUI();
        showFloatingText(`+${amount}₴`, '#00ff88');
    }
    
    function showFloatingText(text, color, latLng = null) {
        if(!latLng && map) latLng = map.getCenter();
        if(!latLng) return;
        const point = map.latLngToContainerPoint(latLng);
        const div = document.createElement('div');
        div.textContent = text;
        div.style.cssText = `position:absolute; left:${point.x}px; top:${point.y}px; color:${color}; font-weight:bold; font-size:14px; text-shadow:0 0 5px black; pointer-events:none; z-index:2000; transition:all 1s ease-out; white-space:nowrap; font-family:monospace;`;
        document.body.appendChild(div);
        setTimeout(() => { div.style.transform = 'translateY(-30px)'; div.style.opacity = '0'; setTimeout(() => div.remove(), 1000); }, 10);
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
            this.speed = 0.014;
            this.isJammed = false;
            this.jamEndTime = 0;
            this.marker = null;
        }
        
        update(deltaTime) {
            if(!this.isAlive) return;
            if(this.isJammed && Date.now() < this.jamEndTime) return;
            else this.isJammed = false;
            
            this.progress += this.speed * deltaTime * (gameSpeed / 30);
            if(this.progress >= 1) {
                this.isAlive = false;
                playerBalance = Math.max(0, playerBalance - 1500);
                showFloatingText(`-1500₴ (${this.target.name} АТАКОВАНО)`, '#ff4444', [this.currentLat, this.currentLng]);
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
                const droneIcon = L.divIcon({ html: '✈', iconSize: [24,24], className: 'drone-marker' });
                this.marker = L.marker([this.currentLat, this.currentLng], { icon: droneIcon }).addTo(map);
            }
            if(this.marker._icon) {
                this.marker._icon.style.filter = this.isJammed ? 'hue-rotate(180deg)' : '';
                this.marker._icon.style.opacity = this.isJammed ? '0.5' : '1';
            }
        }
        
        destroy() {
            if(!this.isAlive) return;
            this.isAlive = false;
            if(this.marker) map.removeLayer(this.marker);
            totalKills++;
            addMoney(1200);
            updateUI();
            showFloatingText('💥 ЗБИТО! +1200₴', '#ffaa44', [this.currentLat, this.currentLng]);
        }
        
        applyJam(duration = 3500) {
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
            this.ammo = (type === 'pickup') ? 300 : (type === 'shilka') ? 700 : 1;
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
                    drone.applyJam(4000);
                }
            }
        }
        
        showTrail(lat, lng) {
            const point = map.latLngToContainerPoint([lat, lng]);
            const div = document.createElement('div');
            div.innerHTML = '💥';
            div.style.cssText = `position:absolute; left:${point.x}px; top:${point.y}px; font-size:16px; pointer-events:none; z-index:2000; transition:all 0.2s ease-out;`;
            document.body.appendChild(div);
            setTimeout(() => { div.style.transform = 'scale(1.5)'; div.style.opacity = '0'; setTimeout(() => div.remove(), 300); }, 10);
        }
        
        render() {
            if(!this.marker) {
                this.marker = L.marker([this.lat, this.lng], { 
                    icon: L.divIcon({ html: this.data.icon, iconSize: [28,28], className: 'unit-marker' })
                }).addTo(map);
                this.rangeCircle = L.circle([this.lat, this.lng], {
                    radius: this.data.range * 1000,
                    color: this.data.color,
                    fillColor: this.data.color,
                    fillOpacity: 0.1,
                    weight: 1
                }).addTo(map);
            }
            if(this.marker._icon) {
                this.marker._icon.style.filter = `drop-shadow(0 0 3px ${this.data.color})`;
            }
            this.marker.bindTooltip(`${this.data.name} | 🎯${this.ammo}`, { sticky: true, className: 'terminal-tooltip' });
        }
    }
    
    // ========== РАДАР ==========
    function drawRadar() {
        if(!radarCtx || !map) return;
        const canvas = document.getElementById('radarCanvas');
        canvas.width = 140;
        canvas.height = 140;
        const w = 140, h = 140;
        radarCtx.clearRect(0, 0, w, h);
        radarCtx.fillStyle = '#0a1a0a';
        radarCtx.fillRect(0, 0, w, h);
        radarCtx.strokeStyle = '#00ff88';
        radarCtx.lineWidth = 1;
        radarCtx.beginPath();
        radarCtx.arc(w/2, h/2, w/2 - 2, 0, 2*Math.PI);
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
                radarCtx.arc(rx, ry, 3, 0, 2*Math.PI);
                radarCtx.fill();
            }
        }
        for(let unit of deployedUnits) {
            const point = map.latLngToContainerPoint([unit.lat, unit.lng]);
            const rx = (point.x / mapSize.x) * w;
            const ry = (point.y / mapSize.y) * h;
            if(rx >= 0 && rx <= w && ry >= 0 && ry <= h) {
                radarCtx.fillStyle = unit.data.color;
                radarCtx.fillRect(rx-2, ry-2, 4, 4);
            }
        }
    }
    
    // ========== ІГРОВИЙ ЦИКЛ ==========
    function gameUpdate() {
        const now = Date.now();
        let dt = Math.min(100, now - lastFrameTime);
        if(dt < 0) dt = 16;
        lastFrameTime = now;
        
        // Оновлення часу
        if(lastFrameTime % 3000 < 50) {
            gameTime.minute += 1;
            if(gameTime.minute >= 60) {
                gameTime.minute = 0;
                gameTime.hour += 1;
                if(gameTime.hour >= 24) {
                    gameTime.hour = 0;
                    gameTime.day += 1;
                }
            }
            updateUI();
        }
        
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
        const interval = Math.max(2500, 5000 - Math.floor(totalKills / 25) * 150);
        if(now - lastSpawnTime < interval) return;
        if(drones.length >= 22) return;
        
        const start = LAUNCH_POINTS[Math.floor(Math.random() * LAUNCH_POINTS.length)];
        const citiesInRegion = CITIES.filter(c => c.region === currentRegion);
        const target = citiesInRegion.length ? citiesInRegion[Math.floor(Math.random() * citiesInRegion.length)] : CITIES[Math.floor(Math.random() * CITIES.length)];
        
        const newDrone = new Drone(start, target);
        drones.push(newDrone);
        lastSpawnTime = now;
        updateUI();
    }
    
    // ========== ІНІЦІАЛІЗАЦІЯ ==========
    function init() {
        map = L.map('map').setView([49.0, 33.5], 6.5);
        L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
            attribution: '© OpenStreetMap'
        }).addTo(map);
        
        const ukrainePoly = L.polygon(UKRAINE_POLYGON, { 
            color: '#00ff88', 
            weight: 1, 
            fillOpacity: 0.02,
            className: 'ukraine-border'
        }).addTo(map);
        
        radarCtx = document.getElementById('radarCanvas').getContext('2d');
        
        // Клік по карті
        map.on('click', (e) => {
            if(!selectedUnit) {
                document.getElementById('closeShop').click();
                return;
            }
            if(!isInsideUkraine(e.latlng.lat, e.latlng.lng)) {
                alert("❌ РОЗМІЩЕННЯ ДОЗВОЛЕНЕ ТІЛЬКИ НА ТЕРИТОРІЇ УКРАЇНИ");
                return;
            }
            const price = UNITS[selectedUnit].price;
            if(playerBalance < price) {
                alert("💰 НЕДОСТАТНЬО КОШТІВ!");
                return;
            }
            playerBalance -= price;
            const newUnit = new DefenseUnit(selectedUnit, e.latlng.lat, e.latlng.lng);
            deployedUnits.push(newUnit);
            newUnit.render();
            updateUI();
            selectedUnit = null;
            document.querySelectorAll('.shop-card').forEach(c => c.classList.remove('active'));
        });
        
        // Магазин
        document.querySelectorAll('.shop-card').forEach(card => {
            card.addEventListener('click', (e) => {
                e.stopPropagation();
                const unitType = card.dataset.unit;
                if(selectedUnit === unitType) {
                    selectedUnit = null;
                    card.classList.remove('active');
                } else {
                    document.querySelectorAll('.shop-card').forEach(c => c.classList.remove('active'));
                    selectedUnit = unitType;
                    card.classList.add('active');
                }
            });
        });
        
        // Меню
        document.getElementById('closeShop').addEventListener('click', () => {
            document.getElementById('shopWindow').classList.remove('open');
        });
        
        document.querySelectorAll('.menu-link').forEach(link => {
            link.addEventListener('click', () => {
                const tab = link.dataset.tab;
                if(tab === 'shop') {
                    document.getElementById('shopWindow').classList.toggle('open');
                } else if(tab === 'stats') {
                    alert("📊 СТАТИСТИКА\nЗбито: " + totalKills + "\nУстановок: " + deployedUnits.length + "\nАктивних дронів: " + drones.length);
                } else if(tab === 'radar') {
                    alert("📡 РАДАР\nАктивних цілей: " + drones.length);
                }
            });
        });
        
        // Регіони
        document.querySelectorAll('.region').forEach(region => {
            region.addEventListener('click', () => {
                document.querySelectorAll('.region').forEach(r => r.classList.remove('active'));
                region.classList.add('active');
                currentRegion = region.dataset.region;
                
                let center = [49.0, 33.5];
                if(currentRegion === 'kharkiv') center = [49.99, 36.23];
                if(currentRegion === 'dnipro') center = [48.46, 35.04];
                if(currentRegion === 'donetsk') center = [48.0, 37.8];
                if(currentRegion === 'poltava') center = [49.59, 34.55];
                map.setView(center, 8);
            });
        });
        
        lastFrameTime = Date.now();
        setInterval(gameUpdate, 50);
        setInterval(spawnDrone, 800);
        updateUI();
        
        // Демо-установки для прикладу
        setTimeout(() => {
            if(deployedUnits.length === 0) {
                const demo = new DefenseUnit('pickup', 49.59, 34.55);
                deployedUnits.push(demo);
                demo.render();
            }
        }, 1000);
    }
    
    window.onload = init;
})();
</script>
</body>
</html>
