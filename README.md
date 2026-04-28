<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ППО України — Захисти небо!</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * { user-select: none; -webkit-tap-highlight-color: transparent; }
        body { margin: 0; padding: 0; font-family: 'Courier New', monospace; background: #0a0e1a; overflow: hidden; }
        #map { height: 100vh; width: 100%; background: #0a1a2a; }
        .game-ui {
            position: fixed; bottom: 20px; left: 20px; right: 20px;
            background: rgba(10, 20, 30, 0.9); backdrop-filter: blur(8px);
            border-radius: 16px; padding: 12px 20px;
            display: flex; flex-wrap: wrap; justify-content: space-between;
            align-items: center; gap: 15px;
            border: 1px solid rgba(0, 255, 170, 0.3);
            z-index: 1000;
        }
        .stats-panel {
            display: flex; gap: 25px;
            background: rgba(0, 0, 0, 0.6); padding: 8px 18px;
            border-radius: 40px; border-left: 3px solid #00ffaa;
        }
        .stat { display: flex; flex-direction: column; align-items: center; }
        .stat-label { font-size: 10px; color: #88aaff; letter-spacing: 1px; }
        .stat-value { font-size: 22px; font-weight: bold; color: #00ffaa; text-shadow: 0 0 5px #00ffaa; }
        .shop {
            display: flex; gap: 12px;
            background: rgba(0, 0, 0, 0.5); padding: 5px 15px;
            border-radius: 50px;
        }
        .shop-btn {
            background: linear-gradient(135deg, #1a2a3a, #0a1a2a);
            border: 1px solid #00ffaa; color: #00ffaa;
            padding: 8px 16px; border-radius: 40px;
            cursor: pointer; font-weight: bold;
            transition: 0.2s; text-align: center;
        }
        .shop-btn:hover, .shop-btn.active {
            background: #00ffaa; color: #0a1a2a;
            box-shadow: 0 0 15px #00ffaa;
        }
        .warning {
            background: rgba(255, 50, 50, 0.2);
            border-left: 4px solid #ff4444;
            padding: 5px 12px; border-radius: 8px;
            color: #ff8888; font-size: 12px;
        }
        .radar-container {
            position: fixed; top: 20px; right: 20px;
            width: 180px; height: 180px;
            background: rgba(0, 20, 0, 0.7);
            border-radius: 50%; border: 2px solid #00ffaa;
            z-index: 1000;
        }
        canvas#radarCanvas { width: 100%; height: 100%; border-radius: 50%; }
        .drone-icon { filter: drop-shadow(0 0 4px rgba(255,0,0,0.5)); }
        .unit-icon { filter: drop-shadow(0 0 4px rgba(0,255,170,0.5)); }
    </style>
</head>
<body>
<div id="map"></div>
<div class="radar-container">
    <canvas id="radarCanvas" width="200" height="200"></canvas>
</div>
<div class="game-ui">
    <div class="stats-panel">
        <div class="stat"><div class="stat-label">💰 БАЛАНС</div><div class="stat-value" id="balanceDisplay" style="color:#ffaa44">15000</div></div>
        <div class="stat"><div class="stat-label">🎯 ЗБИТО</div><div class="stat-value" id="killsDisplay">0</div></div>
        <div class="stat"><div class="stat-label">🚁 ДРОНІВ</div><div class="stat-value" id="dronesCount">0</div></div>
    </div>
    <div class="shop">
        <div class="shop-btn" data-unit="pickup">🔫 Пікап<br><span style="font-size:10px">5500₴</span></div>
        <div class="shop-btn" data-unit="shilka">⚡ Шилка<br><span style="font-size:10px">12000₴</span></div>
        <div class="shop-btn" data-unit="reb">📡 РЕБ<br><span style="font-size:10px">8000₴</span></div>
    </div>
    <div class="warning" id="warningMsg">Оберіть установку та клікніть на карті</div>
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
        { name: "Вінниця", lat: 49.23, lng: 28.48 }
    ];
    
    const LAUNCH_POINTS = [
        { name: "Схід", lat: 48.3, lng: 39.5 },
        { name: "Південь", lat: 46.2, lng: 33.2 },
        { name: "Північ", lat: 51.5, lng: 31.3 },
        { name: "Крим", lat: 45.2, lng: 34.5 }
    ];
    
    const UNITS = {
        pickup: { name: "Пікап 12.7мм", price: 5500, range: 80, cooldown: 1800, damage: 45, color: "#ffaa44", icon: "🔫" },
        shilka: { name: "Шилка 23мм", price: 12000, range: 120, cooldown: 900, damage: 85, color: "#ff6644", icon: "⚡" },
        reb: { name: "РЕБ", price: 8000, range: 45, cooldown: 2500, damage: 0, isJammer: true, jamRadius: 50, color: "#aa88ff", icon: "📡" }
    };
    
    // Межі України (спрощені)
    const UKRAINE_BOUNDS = L.latLngBounds([44.2, 22.1], [52.4, 40.2]);
    
    // Змінні гри
    let playerBalance = 15000;
    let totalKills = 0;
    let selectedUnit = null;
    let deployedUnits = [];
    let drones = [];
    let map, radarCtx, lastFrameTime = 0;
    
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
            
            // Перевірка глушіння
            if(this.isJammed && Date.now() < this.jamEndTime) {
                // Дрон стоїть на місці при глушінні
                return;
            } else {
                this.isJammed = false;
            }
            
            this.progress += this.speed * deltaTime * 0.08;
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
                const droneIcon = L.divIcon({ html: '🚁', iconSize: [28,28], className: 'drone-icon' });
                this.marker = L.marker([this.currentLat, this.currentLng], { icon: droneIcon }).addTo(map);
            }
            if(this.marker._icon) {
                this.marker._icon.style.filter = this.isJammed ? 'hue-rotate(180deg)' : '';
            }
        }
        
        destroy() {
            if(!this.isAlive) return;
            this.isAlive = false;
            if(this.marker) map.removeLayer(this.marker);
            totalKills++;
            addMoney(900);
            updateUI();
            showFloatingText('💥 ЗБИТО!', '#ffaa44', [this.currentLat, this.currentLng]);
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
        
        // Оновлення дронів
        for(let i=0; i<drones.length; i++) {
            drones[i].update(dt / 1000);
            if(!drones[i].isAlive) {
                drones.splice(i,1);
                i--;
            }
        }
        
        // Глушіння від РЕБ
        for(let unit of deployedUnits) {
            if(unit.type === 'reb') unit.jamDrones(drones);
        }
        
        // Стрільба
        for(let unit of deployedUnits) {
            if(unit.data.damage > 0) {
                for(let drone of drones) {
                    unit.shootAt(drone);
                    if(!drone.isAlive) break;
                }
            }
        }
        
        // Відмальовка
        for(let unit of deployedUnits) unit.render();
        for(let drone of drones) drone.render();
        drawRadar();
        updateUI();
    }
    
    // ========== СПАВН ДРОНІВ ==========
    let lastSpawnTime = 0;
    function spawnDrone() {
        const now = Date.now();
        if(now - lastSpawnTime < 4000) return;
        if(drones.length >= 20) return;
        
        const start = LAUNCH_POINTS[Math.floor(Math.random() * LAUNCH_POINTS.length)];
        const target = CITIES[Math.floor(Math.random() * CITIES.length)];
        const newDrone = new Drone(start, target);
        drones.push(newDrone);
        lastSpawnTime = now;
        updateUI();
    }
    
    // ========== ІНІЦІАЛІЗАЦІЯ ==========
    function init() {
        map = L.map('map').setView([48.9, 31.5], 6);
        L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', {
            attribution: '© OpenStreetMap'
        }).addTo(map);
        
        // Полігон України
        const ukrainePoly = L.polygon([
            [52.3, 22.1], [52.2, 33.5], [50.2, 40.0], [46.0, 40.0], [44.3, 33.9], [45.2, 29.0], [48.0, 22.1]
        ], { color: '#3388ff', weight: 2, fillOpacity: 0.05 }).addTo(map);
        
        radarCtx = document.getElementById('radarCanvas').getContext('2d');
        
        // Клік по карті для розміщення
        map.on('click', (e) => {
            if(!selectedUnit) {
                document.getElementById('warningMsg').innerHTML = "⚠️ Оберіть установку в меню!";
                return;
            }
            if(!UKRAINE_BOUNDS.contains(e.latlng)) {
                document.getElementById('warningMsg').innerHTML = "❌ Розміщуйте установки лише на території України!";
                return;
            }
            const price = UNITS[selectedUnit].price;
            if(playerBalance < price) {
                document.getElementById('warningMsg').innerHTML = "💰 Недостатньо коштів!";
                return;
            }
            playerBalance -= price;
            const newUnit = new DefenseUnit(selectedUnit, e.latlng.lat, e.latlng.lng);
            deployedUnits.push(newUnit);
            newUnit.render();
            updateUI();
            document.getElementById('warningMsg').innerHTML = `✅ ${UNITS[selectedUnit].name} розміщено!`;
            selectedUnit = null;
            document.querySelectorAll('.shop-btn').forEach(btn => btn.classList.remove('active'));
        });
        
        // Вибір установки
        document.querySelectorAll('.shop-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                const unitType = btn.dataset.unit;
                if(selectedUnit === unitType) {
                    selectedUnit = null;
                    btn.classList.remove('active');
                    document.getElementById('warningMsg').innerHTML = "⚡ Режим розміщення вимкнено";
                } else {
                    document.querySelectorAll('.shop-btn').forEach(b => b.classList.remove('active'));
                    selectedUnit = unitType;
                    btn.classList.add('active');
                    document.getElementById('warningMsg').innerHTML = `📍 Розмістіть ${UNITS[selectedUnit].name} на карті`;
                }
            });
        });
        
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
