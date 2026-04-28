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
            transition: 0.2s;
            font-size: 14px;
            text-align: center;
        }
        .shop-btn:hover {
            background: #00ffaa;
            color: #0a1a2a;
        }
        .shop-btn.active {
            background: #00ffaa;
            color: #0a1a2a;
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
            z-index: 1000;
        }
        canvas#radarCanvas { width: 100%; height: 100%; border-radius: 50%; }
        /* Кастомні стилі для маркерів-зображень */
        .custom-marker-img {
            filter: drop-shadow(0 0 2px rgba(0,0,0,0.5));
            object-fit: contain;
        }
        .drone-marker img {
            transform: rotate(180deg); /* Щоб дрони дивились вниз, якщо треба */
        }
    </style>
</head>
<body>
<div id="map"></div>
<div class="radar-container">
    <canvas id="radarCanvas" width="200" height="200"></canvas>
</div>

<div class="game-ui">
    <div class="stats-panel">
        <div class="stat">
            <div class="stat-label">💰 БАЛАНС</div>
            <div class="stat-value" style="color:#ffaa44" id="balanceDisplay">10000</div>
        </div>
        <div class="stat">
            <div class="stat-label">🎯 ЗБИТО</div>
            <div class="stat-value" id="killsDisplay">0</div>
        </div>
    </div>
    <div class="shop">
        <div class="shop-btn" data-unit="pickup">🔫 Пікап<br><span style="font-size:10px">5500₴</span></div>
        <div class="shop-btn" data-unit="shilka">⚡ Шилка<br><span style="font-size:10px">12000₴</span></div>
        <div class="shop-btn" data-unit="reb">📡 РЕБ<br><span style="font-size:10px">8000₴</span></div>
    </div>
    <div class="warning" id="warningMsg">Оберіть юніт</div>
</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
    (function(){
        const UNITS = {
            pickup: { name: "Пікап 12.7мм", price: 5500, range: 80, cooldown: 1800, damage: 45, color: "#ffaa44", img: "pulemet.png" },
            shilka: { name: "Шилка 23мм", price: 12000, range: 120, cooldown: 900, damage: 85, color: "#ff6644", img: "shilka.png" },
            reb: { name: "РЕБ", price: 8000, range: 45, cooldown: 2500, damage: 0, isJammer: true, jamRadius: 45, color: "#aa88ff", img: "reb.png" }
        };

        let playerBalance = 10000;
        let totalKills = 0;
        let selectedUnit = null;
        let deployedUnits = [];
        let drones = [];
        let map, radarCtx, lastFrameTime;

        const ukraineBounds = L.latLngBounds([44.2, 22.1], [52.4, 40.2]);

        function updateUI() {
            document.getElementById('balanceDisplay').innerText = Math.floor(playerBalance);
            document.getElementById('killsDisplay').innerText = totalKills;
        }

        class Drone {
            constructor(startLat, startLng, targetCity) {
                this.isAlive = true;
                this.currentLat = startLat;
                this.currentLng = startLng;
                this.startLat = startLat;
                this.startLng = startLng;
                this.targetCity = targetCity;
                this.progress = 0;
                this.hp = 100;
                this.speed = 0.012;
                this.marker = null;
            }
            update(dt) {
                if(!this.isAlive) return;
                this.progress += this.speed * dt * 0.05;
                if(this.progress >= 1) {
                    this.isAlive = false;
                    playerBalance = Math.max(0, playerBalance - 1500);
                    if(this.marker) map.removeLayer(this.marker);
                    return;
                }
                this.currentLat = this.startLat + (this.targetCity.lat - this.startLat) * this.progress;
                this.currentLng = this.startLng + (this.targetCity.lng - this.startLng) * this.progress;
                if(this.marker) this.marker.setLatLng([this.currentLat, this.currentLng]);
            }
            render() {
                if(!this.isAlive) return;
                if(!this.marker) {
                    // Використовуємо твій geran2.png
                    const icon = L.icon({
                        iconUrl: 'geran2.png',
                        iconSize: [30, 30],
                        className: 'custom-marker-img'
                    });
                    this.marker = L.marker([this.currentLat, this.currentLng], { icon: icon }).addTo(map);
                }
            }
            destroy() {
                this.isAlive = false;
                if(this.marker) map.removeLayer(this.marker);
                totalKills++;
                playerBalance += 800;
            }
        }

        class DefenseUnit {
            constructor(type, lat, lng) {
                this.type = type;
                this.lat = lat;
                this.lng = lng;
                this.data = UNITS[type];
                this.lastShotTime = 0;
                this.marker = null;
            }
            render() {
                if(!this.marker) {
                    const icon = L.icon({
                        iconUrl: this.data.img,
                        iconSize: [40, 40],
                        iconAnchor: [20, 20],
                        className: 'custom-marker-img'
                    });
                    this.marker = L.marker([this.lat, this.lng], { icon: icon }).addTo(map);
                    L.circle([this.lat, this.lng], { 
                        radius: this.data.range * 1000, 
                        color: this.data.color, 
                        fillOpacity: 0.1,
                        weight: 1 
                    }).addTo(map);
                }
            }
            shoot(drones) {
                if(this.data.damage <= 0 || Date.now() - this.lastShotTime < this.data.cooldown) return;
                for(let drone of drones) {
                    let d = map.distance([this.lat, this.lng], [drone.currentLat, drone.currentLng]) / 1000;
                    if(d <= this.data.range) {
                        drone.hp -= this.data.damage;
                        this.lastShotTime = Date.now();
                        if(drone.hp <= 0) drone.destroy();
                        break;
                    }
                }
            }
        }

        function gameUpdate() {
            const now = Date.now();
            let dt = now - lastFrameTime || 16;
            lastFrameTime = now;

            drones.forEach(d => d.update(dt/1000));
            drones = drones.filter(d => d.isAlive);
            drones.forEach(d => d.render());
            
            deployedUnits.forEach(u => u.shoot(drones));
            updateUI();
        }

        function init() {
            map = L.map('map').setView([48.9, 31.5], 6);
            L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png').addTo(map);

            map.on('click', (e) => {
                if(!selectedUnit) return;
                if(playerBalance >= UNITS[selectedUnit].price) {
                    playerBalance -= UNITS[selectedUnit].price;
                    const u = new DefenseUnit(selectedUnit, e.latlng.lat, e.latlng.lng);
                    deployedUnits.push(u);
                    u.render();
                    selectedUnit = null;
                    document.querySelectorAll('.shop-btn').forEach(b => b.classList.remove('active'));
                }
            });

            document.querySelectorAll('.shop-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    selectedUnit = btn.dataset.unit;
                    document.querySelectorAll('.shop-btn').forEach(b => b.classList.remove('active'));
                    btn.classList.add('active');
                });
            });

            setInterval(gameUpdate, 50);
            setInterval(() => {
                if(drones.length < 10) {
                    drones.push(new Drone(51, 40, {lat: 50.4, lng: 30.5}));
                }
            }, 3000);
        }

        window.onload = init;
    })();
</script>
</body>
</html>
