<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Авіастратегія: Україна та Росія</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0a1929, #1a3658);
            color: white;
            overflow: hidden;
            height: 100vh;
        }

        header {
            background: rgba(0, 0, 0, 0.8);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
            z-index: 1000;
            position: relative;
            border-bottom: 2px solid #ff8a00;
        }

        h1 {
            font-size: 24px;
            background: linear-gradient(90deg, #ff8a00, #e52e71);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .game-info {
            display: flex;
            align-items: center;
            gap: 20px;
        }

        #moneyDisplay {
            font-size: 18px;
            font-weight: bold;
            color: #4CAF50;
            background: rgba(0, 0, 0, 0.5);
            padding: 8px 15px;
            border-radius: 20px;
            border: 1px solid #4CAF50;
        }

        button {
            background: linear-gradient(90deg, #ff8a00, #e52e71);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 30px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s ease;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        }

        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
        }

        button:disabled {
            background: #666;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: rgba(0, 0, 0, 0.9);
            z-index: 999;
            transition: opacity 0.5s ease;
        }

        .hidden {
            display: none;
        }

        #startScreen {
            text-align: center;
            padding: 20px;
            background: radial-gradient(circle at center, #1a2a6c, #0a1929);
        }

        #startScreen h2 {
            font-size: 42px;
            margin-bottom: 20px;
            background: linear-gradient(90deg, #ff8a00, #e52e71);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }

        #startScreen p {
            font-size: 20px;
            margin-bottom: 30px;
            max-width: 600px;
            line-height: 1.6;
            color: #ccc;
        }

        #startGameBtn {
            font-size: 22px;
            padding: 15px 40px;
            background: linear-gradient(90deg, #ff8a00, #e52e71);
        }

        #gameScreen {
            padding-top: 70px;
            flex-direction: row;
            align-items: stretch;
            background: transparent;
        }

        #map {
            flex: 1;
            height: calc(100vh - 70px);
            background: #0a0a0a;
        }

        .game-map {
            width: 100%;
            height: 100%;
            background: 
                linear-gradient(45deg, #1a1a1a 25%, transparent 25%),
                linear-gradient(-45deg, #1a1a1a 25%, transparent 25%),
                linear-gradient(45deg, transparent 75%, #1a1a1a 75%),
                linear-gradient(-45deg, transparent 75%, #1a1a1a 75%);
            background-size: 20px 20px;
            background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
            position: relative;
        }

        #controlPanel {
            width: 350px;
            background: rgba(0, 0, 0, 0.85);
            padding: 20px;
            overflow-y: auto;
            border-left: 2px solid #333;
        }

        #controlPanel h3 {
            margin-bottom: 20px;
            text-align: center;
            color: #ff8a00;
            font-size: 22px;
            border-bottom: 1px solid #333;
            padding-bottom: 10px;
        }

        .info-section {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            border: 1px solid #333;
        }

        .info-section h4 {
            margin-bottom: 10px;
            color: #4CAF50;
            font-size: 18px;
        }

        .plane-item {
            background: rgba(255, 255, 255, 0.05);
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border: 1px solid #333;
        }

        .plane-info {
            display: flex;
            flex-direction: column;
        }

        .plane-name {
            font-weight: bold;
            color: #ff8a00;
        }

        .plane-stats {
            font-size: 12px;
            color: #aaa;
        }

        .control-btn {
            width: 100%;
            margin-top: 10px;
            padding: 8px;
            font-size: 14px;
        }

        .country-ukraine {
            fill: rgba(0, 87, 183, 0.3);
            stroke: #0057b7;
            stroke-width: 2;
        }

        .country-russia {
            fill: rgba(255, 255, 255, 0.3);
            stroke: #fff;
            stroke-width: 2;
        }

        .city-marker {
            background: white;
            border-radius: 50%;
            width: 8px;
            height: 8px;
            box-shadow: 0 0 10px rgba(255, 255, 255, 0.7);
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .city-marker:hover {
            transform: scale(1.5);
            box-shadow: 0 0 15px rgba(255, 255, 255, 1);
        }

        .airport-marker {
            background: #4CAF50;
            border-radius: 50%;
            width: 12px;
            height: 12px;
            box-shadow: 0 0 15px rgba(76, 175, 80, 0.7);
            cursor: pointer;
        }

        .plane-marker {
            width: 24px;
            height: 24px;
            background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ff8a00"><path d="M20.56 3.91c.59.59.59 1.54 0 2.12l-4.95 4.95 2.12 5.66-2.83 2.83-1.41-5.65-4.95 4.95-.7-.7 4.95-4.95L9.3 8.1l2.83-2.83 5.66 2.12 4.95-4.95c.58-.58 1.53-.58 2.12 0z"/></svg>') no-repeat center;
            background-size: contain;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .plane-marker:hover {
            transform: scale(1.2);
        }

        .plane-path {
            stroke: #ff8a00;
            stroke-width: 2;
            stroke-dasharray: 5, 5;
            fill: none;
        }

        .city-tooltip {
            position: absolute;
            background: rgba(0, 0, 0, 0.8);
            color: white;
            padding: 8px 12px;
            border-radius: 5px;
            font-size: 14px;
            pointer-events: none;
            z-index: 1000;
            border: 1px solid #333;
        }

        .notification {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background: rgba(76, 175, 80, 0.9);
            color: white;
            padding: 15px 25px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
            z-index: 1001;
            transform: translateX(150%);
            transition: transform 0.5s ease;
        }

        .notification.show {
            transform: translateX(0);
        }

        .map-overlay {
            position: absolute;
            top: 10px;
            left: 10px;
            background: rgba(0, 0, 0, 0.7);
            padding: 10px;
            border-radius: 5px;
            z-index: 1000;
        }

        @media (max-width: 768px) {
            #gameScreen {
                flex-direction: column;
            }
            
            #controlPanel {
                width: 100%;
                height: 40vh;
            }
            
            #map {
                height: 60vh;
            }
            
            header {
                flex-direction: column;
                gap: 10px;
            }
        }
    </style>
</head>
<body>
    <!-- Меню сайту -->
    <header>
        <h1>Авіастратегія: Україна та Росія</h1>
        <div class="game-info">
            <span id="moneyDisplay">Гроші: $250,000</span>
            <button id="playButton">Грати</button>
        </div>
    </header>

    <!-- Головний екран -->
    <div id="startScreen" class="screen">
        <h2>Ласкаво просимо до Авіастратегії!</h2>
        <p>Побудуйте авіаційну імперію, створюючи аеропорти та купуючи літаки. Починайте з України та розширюйте свою мережу!</p>
        <button id="startGameBtn">Почати гру</button>
    </div>

    <!-- Ігровий екран -->
    <div id="gameScreen" class="screen hidden">
        <!-- Карта -->
        <div id="map" class="game-map">
            <div class="map-overlay">
                <strong>Карта України та Росії</strong><br>
                <span style="color: #0057b7">■ Україна</span><br>
                <span style="color: white">■ Росія</span>
            </div>
            <!-- SVG карта буде додана через JavaScript -->
        </div>

        <!-- Панель управління -->
        <div id="controlPanel">
            <h3>Панель управління</h3>
            <div id="cityInfo" class="info-section hidden">
                <h4 id="cityName">Назва міста</h4>
                <p>Населення: <span id="cityPopulation">0</span></p>
                <p>Країна: <span id="cityCountry">-</span></p>
                <button id="buildAirportBtn">Побудувати аеропорт ($1,000,000)</button>
            </div>
            
            <div id="airportInfo" class="info-section hidden">
                <h4>Аеропорт</h4>
                <p>Статус: <span id="airportStatus">Активний</span></p>
                <div id="airportPlanes">
                    <h5>Літаки:</h5>
                    <div id="planesList"></div>
                </div>
                <div id="buyPlaneSection">
                    <h5>Купити літак:</h5>
                    <button class="buy-plane control-btn" data-plane="f16">F-16 ($1,700,000)</button>
                    <button class="buy-plane control-btn" data-plane="mig29">МіГ-29 ($1,500,000)</button>
                    <button class="buy-plane control-btn" data-plane="su27">Су-27 ($1,800,000)</button>
                </div>
            </div>
            
            <div id="planeInfo" class="info-section hidden">
                <h4 id="planeName">Літак</h4>
                <p>Тип: <span id="planeType">-</span></p>
                <p>Швидкість: <span id="planeSpeed">-</span></p>
                <p>Базується в: <span id="planeBase">-</span></p>
                <button id="directPlaneBtn" class="control-btn">Направити літак</button>
                <button id="stopDirectBtn" class="control-btn hidden">Зупинити направлення</button>
            </div>
            
            <div class="info-section">
                <h4>Доходи</h4>
                <p>+$70,000 кожні 3 секунди</p>
                <p>Наступний дохід через: <span id="incomeTimer">3с</span></p>
            </div>
        </div>
    </div>

    <!-- Сповіщення -->
    <div id="notification" class="notification"></div>

    <script>
        // Дані гри
        const gameData = {
            money: 250000,
            income: 70000,
            incomeInterval: 3000, // 3 секунди
            cities: [
                { id: 1, name: "Київ", country: "ua", population: 2884000, x: 45, y: 35 },
                { id: 2, name: "Харків", country: "ua", population: 1441000, x: 55, y: 30 },
                { id: 3, name: "Одеса", country: "ua", population: 1017000, x: 35, y: 50 },
                { id: 4, name: "Львів", country: "ua", population: 724000, x: 30, y: 25 },
                { id: 5, name: "Дніпро", country: "ua", population: 966000, x: 52, y: 40 },
                { id: 6, name: "Москва", country: "ru", population: 12655000, x: 60, y: 20 },
                { id: 7, name: "Санкт-Петербург", country: "ru", population: 5398000, x: 55, y: 10 },
                { id: 8, name: "Новосибірськ", country: "ru", population: 1620000, x: 85, y: 25 },
                { id: 9, name: "Єкатеринбург", country: "ru", population: 1495000, x: 70, y: 20 },
                { id: 10, name: "Казань", country: "ru", population: 1257000, x: 65, y: 25 }
            ],
            planes: {
                f16: { name: "F-16 Fighting Falcon", price: 1700000, speed: "Макс. 2,170 км/год" },
                mig29: { name: "МіГ-29 Fulcrum", price: 1500000, speed: "Макс. 2,400 км/год" },
                su27: { name: "Су-27 Flanker", price: 1800000, speed: "Макс. 2,500 км/год" }
            },
            airports: [],
            playerPlanes: [],
            selectedCity: null,
            selectedPlane: null,
            isDirecting: false
        };

        // Елементи DOM
        const startScreen = document.getElementById('startScreen');
        const gameScreen = document.getElementById('gameScreen');
        const startGameBtn = document.getElementById('startGameBtn');
        const playButton = document.getElementById('playButton');
        const moneyDisplay = document.getElementById('moneyDisplay');
        const mapContainer = document.getElementById('map');
        const cityInfo = document.getElementById('cityInfo');
        const cityName = document.getElementById('cityName');
        const cityPopulation = document.getElementById('cityPopulation');
        const cityCountry = document.getElementById('cityCountry');
        const buildAirportBtn = document.getElementById('buildAirportBtn');
        const airportInfo = document.getElementById('airportInfo');
        const planesList = document.getElementById('planesList');
        const buyPlaneSection = document.getElementById('buyPlaneSection');
        const planeInfo = document.getElementById('planeInfo');
        const planeName = document.getElementById('planeName');
        const planeType = document.getElementById('planeType');
        const planeSpeed = document.getElementById('planeSpeed');
        const planeBase = document.getElementById('planeBase');
        const directPlaneBtn = document.getElementById('directPlaneBtn');
        const stopDirectBtn = document.getElementById('stopDirectBtn');
        const incomeTimer = document.getElementById('incomeTimer');
        const notification = document.getElementById('notification');

        // Елементи карти
        let svgMap;
        let cityMarkers = [];
        let airportMarkers = [];
        let planeMarkers = [];
        let planePaths = [];
        let tooltip = null;

        // Ініціалізація гри
        function initGame() {
            // Ініціалізація карти
            initMap();
            
            // Додавання міст на карту
            addCitiesToMap();
            
            // Оновлення відображення грошей
            updateMoneyDisplay();
            
            // Запуск доходу
            startIncome();
            
            // Сховати стартовий екран, показати ігровий
            startScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            
            showNotification("Гра розпочалася! Ви починаєте в Україні.");
        }

        // Ініціалізація карти
        function initMap() {
            // Створення SVG карти
            svgMap = document.createElementNS("http://www.w3.org/2000/svg", "svg");
            svgMap.setAttribute("width", "100%");
            svgMap.setAttribute("height", "100%");
            svgMap.style.cursor = "grab";
            
            // Додавання контурів України
            const ukraine = document.createElementNS("http://www.w3.org/2000/svg", "path");
            ukraine.setAttribute("class", "country-ukraine");
            ukraine.setAttribute("d", "M30,25 L40,30 L50,35 L55,40 L60,45 L55,50 L50,55 L45,60 L40,55 L35,50 L30,45 L25,40 L20,35 Z");
            svgMap.appendChild(ukraine);
            
            // Додавання контурів Росії
            const russia = document.createElementNS("http://www.w3.org/2000/svg", "path");
            russia.setAttribute("class", "country-russia");
            russia.setAttribute("d", "M55,10 L65,15 L75,20 L85,25 L90,30 L85,35 L80,40 L75,35 L70,30 L65,25 L60,20 L55,15 Z");
            svgMap.appendChild(russia);
            
            // Додавання SVG до контейнера
            mapContainer.appendChild(svgMap);
            
            // Додавання обробників подій для переміщення карти
            let isDragging = false;
            let startX, startY;
            let translateX = 0, translateY = 0;
            let scale = 1;
            
            svgMap.addEventListener('mousedown', (e) => {
                if (e.button === 2) { // Права кнопка миші
                    isDragging = true;
                    startX = e.clientX - translateX;
                    startY = e.clientY - translateY;
                    svgMap.style.cursor = 'grabbing';
                }
            });
            
            svgMap.addEventListener('mousemove', (e) => {
                if (isDragging) {
                    translateX = e.clientX - startX;
                    translateY = e.clientY - startY;
                    svgMap.style.transform = `translate(${translateX}px, ${translateY}px) scale(${scale})`;
                }
            });
            
            svgMap.addEventListener('mouseup', () => {
                isDragging = false;
                svgMap.style.cursor = 'grab';
            });
            
            svgMap.addEventListener('wheel', (e) => {
                e.preventDefault();
                const delta = -e.deltaY / 1000;
                scale = Math.min(Math.max(0.5, scale + delta), 3);
                svgMap.style.transform = `translate(${translateX}px, ${translateY}px) scale(${scale})`;
            });
            
            // Заборонити контекстне меню
            svgMap.addEventListener('contextmenu', (e) => e.preventDefault());
            
            // Для мобільних пристроїв
            svgMap.addEventListener('touchstart', (e) => {
                isDragging = true;
                startX = e.touches[0].clientX - translateX;
                startY = e.touches[0].clientY - translateY;
            });
            
            svgMap.addEventListener('touchmove', (e) => {
                if (isDragging) {
                    translateX = e.touches[0].clientX - startX;
                    translateY = e.touches[0].clientY - startY;
                    svgMap.style.transform = `translate(${translateX}px, ${translateY}px) scale(${scale})`;
                }
            });
            
            svgMap.addEventListener('touchend', () => {
                isDragging = false;
            });
        }

        // Додавання міст на карту
        function addCitiesToMap() {
            gameData.cities.forEach(city => {
                // Створення маркера міста
                const marker = document.createElementNS("http://www.w3.org/2000/svg", "circle");
                marker.setAttribute("class", "city-marker");
                marker.setAttribute("cx", city.x);
                marker.setAttribute("cy", city.y);
                marker.setAttribute("r", 4);
                marker.setAttribute("data-city-id", city.id);
                
                // Додавання обробників подій
                marker.addEventListener('mouseover', (e) => {
                    showCityTooltip(e, city);
                });
                
                marker.addEventListener('mouseout', hideCityTooltip);
                
                marker.addEventListener('click', () => {
                    selectCity(city);
                });
                
                svgMap.appendChild(marker);
                cityMarkers.push(marker);
            });
        }

        // Показати підказку міста
        function showCityTooltip(e, city) {
            if (tooltip) {
                tooltip.remove();
            }
            
            tooltip = document.createElement('div');
            tooltip.className = 'city-tooltip';
            tooltip.textContent = `${city.name} (${city.country === 'ua' ? 'Україна' : 'Росія'})`;
            tooltip.style.left = (e.clientX + 10) + 'px';
            tooltip.style.top = (e.clientY - 30) + 'px';
            
            document.body.appendChild(tooltip);
        }

        // Сховати підказку міста
        function hideCityTooltip() {
            if (tooltip) {
                tooltip.remove();
                tooltip = null;
            }
        }

        // Вибір міста
        function selectCity(city) {
            gameData.selectedCity = city;
            
            // Оновлення інформації про місто
            cityName.textContent = city.name;
            cityPopulation.textContent = city.population.toLocaleString();
            cityCountry.textContent = city.country === 'ua' ? 'Україна' : 'Росія';
            
            // Перевірка, чи є в місті аеропорт
            const hasAirport = gameData.airports.some(airport => airport.cityId === city.id);
            
            if (hasAirport) {
                // Показати інформацію про аеропорт
                cityInfo.classList.add('hidden');
                showAirportInfo(city.id);
            } else {
                // Показати кнопку для будівництва аеропорту
                airportInfo.classList.add('hidden');
                cityInfo.classList.remove('hidden');
                
                // Активувати/деактивувати кнопку будівництва в залежності від грошей
                buildAirportBtn.disabled = gameData.money < 1000000;
            }
            
            // Сховати інформацію про літак
            planeInfo.classList.add('hidden');
        }

        // Показати інформацію про аеропорт
        function showAirportInfo(cityId) {
            const airport = gameData.airports.find(a => a.cityId === cityId);
            if (!airport) return;
            
            // Оновити список літаків
            updatePlanesList(cityId);
            
            // Активувати/деактивувати кнопки купівлі літаків
            document.querySelectorAll('.buy-plane').forEach(btn => {
                const planeType = btn.dataset.plane;
                btn.disabled = gameData.money < gameData.planes[planeType].price;
            });
            
            // Показати панель аеропорту
            airportInfo.classList.remove('hidden');
        }

        // Оновити список літаків
        function updatePlanesList(cityId) {
            planesList.innerHTML = '';
            
            const planesInCity = gameData.playerPlanes.filter(plane => plane.baseCityId === cityId);
            
            if (planesInCity.length === 0) {
                planesList.innerHTML = '<p>Немає літаків</p>';
                return;
            }
            
            planesInCity.forEach(plane => {
                const planeItem = document.createElement('div');
                planeItem.className = 'plane-item';
                
                planeItem.innerHTML = `
                    <div class="plane-info">
                        <span class="plane-name">${plane.name}</span>
                        <span class="plane-stats">${plane.speed}</span>
                    </div>
                    <button class="select-plane" data-plane-id="${plane.id}">Вибрати</button>
                `;
                
                planesList.appendChild(planeItem);
            });
            
            // Додати обробники подій для кнопок вибору літака
            document.querySelectorAll('.select-plane').forEach(btn => {
                btn.addEventListener('click', () => {
                    const planeId = parseInt(btn.dataset.planeId);
                    selectPlane(planeId);
                });
            });
        }

        // Вибір літака
        function selectPlane(planeId) {
            const plane = gameData.playerPlanes.find(p => p.id === planeId);
            if (!plane) return;
            
            gameData.selectedPlane = plane;
            
            // Оновити інформацію про літак
            planeName.textContent = plane.name;
            planeType.textContent = plane.type;
            planeSpeed.textContent = plane.speed;
            
            const baseCity = gameData.cities.find(c => c.id === plane.baseCityId);
            planeBase.textContent = baseCity ? baseCity.name : 'Невідомо';
            
            // Показати панель літака
            planeInfo.classList.remove('hidden');
            
            // Сховати панель аеропорту
            airportInfo.classList.add('hidden');
        }

        // Побудова аеропорту
        function buildAirport() {
            if (!gameData.selectedCity) return;
            
            if (gameData.money < 1000000) {
                showNotification("Недостатньо коштів для будівництва аеропорту!");
                return;
            }
            
            // Перевірка, чи вже є аеропорт у цьому місті
            if (gameData.airports.some(airport => airport.cityId === gameData.selectedCity.id)) {
                showNotification("Аеропорт вже побудований у цьому місті!");
                return;
            }
            
            // Відняти гроші
            gameData.money -= 1000000;
            updateMoneyDisplay();
            
            // Додати аеропорт
            const airport = {
                id: gameData.airports.length + 1,
                cityId: gameData.selectedCity.id
            };
            
            gameData.airports.push(airport);
            
            // Додати маркер аеропорту на карту
            addAirportMarker(gameData.selectedCity);
            
            // Оновити інтерфейс
            cityInfo.classList.add('hidden');
            showAirportInfo(gameData.selectedCity.id);
            
            showNotification(`Аеропорт побудовано в місті ${gameData.selectedCity.name}!`);
        }

        // Додати маркер аеропорту на карту
        function addAirportMarker(city) {
            const marker = document.createElementNS("http://www.w3.org/2000/svg", "circle");
            marker.setAttribute("class", "airport-marker");
            marker.setAttribute("cx", city.x);
            marker.setAttribute("cy", city.y);
            marker.setAttribute("r", 6);
            marker.setAttribute("data-city-id", city.id);
            
            marker.addEventListener('click', () => {
                selectCity(city);
            });
            
            svgMap.appendChild(marker);
            airportMarkers.push(marker);
        }

        // Купівля літака
        function buyPlane(planeType) {
            if (!gameData.selectedCity) return;
            
            const planeData = gameData.planes[planeType];
            if (!planeData) return;
            
            if (gameData.money < planeData.price) {
                showNotification(`Недостатньо коштів для покупки ${planeData.name}!`);
                return;
            }
            
            // Перевірка, чи є аеропорт у місті
            if (!gameData.airports.some(airport => airport.cityId === gameData.selectedCity.id)) {
                showNotification("Спочатку побудуйте аеропорт у цьому місті!");
                return;
            }
            
            // Відняти гроші
            gameData.money -= planeData.price;
            updateMoneyDisplay();
            
            // Додати літак
            const plane = {
                id: gameData.playerPlanes.length + 1,
                name: planeData.name,
                type: planeType,
                speed: planeData.speed,
                baseCityId: gameData.selectedCity.id,
                x: gameData.selectedCity.x,
                y: gameData.selectedCity.y
            };
            
            gameData.playerPlanes.push(plane);
            
            // Додати маркер літака на карту
            addPlaneMarker(plane);
            
            // Оновити список літаків
            updatePlanesList(gameData.selectedCity.id);
            
            showNotification(`Літак ${planeData.name} куплено!`);
        }

        // Додати маркер літака на карту
        function addPlaneMarker(plane) {
            const marker = document.createElementNS("http://www.w3.org/2000/svg", "image");
            marker.setAttribute("class", "plane-marker");
            marker.setAttribute("x", plane.x - 12);
            marker.setAttribute("y", plane.y - 12);
            marker.setAttribute("width", 24);
            marker.setAttribute("height", 24);
            marker.setAttribute("href", 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ff8a00"><path d="M20.56 3.91c.59.59.59 1.54 0 2.12l-4.95 4.95 2.12 5.66-2.83 2.83-1.41-5.65-4.95 4.95-.7-.7 4.95-4.95L9.3 8.1l2.83-2.83 5.66 2.12 4.95-4.95c.58-.58 1.53-.58 2.12 0z"/></svg>');
            marker.setAttribute("data-plane-id", plane.id);
            
            marker.addEventListener('click', () => {
                selectPlane(plane.id);
            });
            
            svgMap.appendChild(marker);
            planeMarkers.push(marker);
        }

        // Направлення літака
        function directPlane() {
            if (!gameData.selectedPlane) return;
            
            gameData.isDirecting = true;
            directPlaneBtn.classList.add('hidden');
            stopDirectBtn.classList.remove('hidden');
            
            // Додати обробник кліку по карті
            svgMap.addEventListener('click', onMapClickForDirection);
            svgMap.style.cursor = 'crosshair';
            
            showNotification("Натисніть на карту, щоб задати маршрут літаку");
        }

        // Зупинити направлення літака
        function stopDirecting() {
            gameData.isDirecting = false;
            directPlaneBtn.classList.remove('hidden');
            stopDirectBtn.classList.add('hidden');
            
            // Видалити обробник кліку по карті
            svgMap.removeEventListener('click', onMapClickForDirection);
            svgMap.style.cursor = 'grab';
            
            // Видалити лінію маршруту, якщо вона є
            if (planePaths.length > 0) {
                planePaths.forEach(path => path.remove());
                planePaths = [];
            }
        }

        // Обробник кліку по карті для направлення літака
        function onMapClickForDirection(e) {
            if (!gameData.selectedPlane || !gameData.isDirecting) return;
            
            const rect = svgMap.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;
            
            // Оновити позицію літака
            const plane = gameData.playerPlanes.find(p => p.id === gameData.selectedPlane.id);
            if (plane) {
                plane.x = x;
                plane.y = y;
                
                // Оновити маркер літака на карті
                const planeMarker = planeMarkers.find(m => m.getAttribute('data-plane-id') == plane.id);
                if (planeMarker) {
                    planeMarker.setAttribute('x', x - 12);
                    planeMarker.setAttribute('y', y - 12);
                }
                
                // Додати лінію маршруту
                const baseCity = gameData.cities.find(c => c.id === plane.baseCityId);
                if (baseCity) {
                    const path = document.createElementNS("http://www.w3.org/2000/svg", "line");
                    path.setAttribute("class", "plane-path");
                    path.setAttribute("x1", baseCity.x);
                    path.setAttribute("y1", baseCity.y);
                    path.setAttribute("x2", x);
                    path.setAttribute("y2", y);
                    
                    svgMap.appendChild(path);
                    planePaths.push(path);
                }
                
                showNotification(`Літак направлено до нової позиції!`);
            }
            
            // Зупинити направлення після вибору точки
            stopDirecting();
        }

        // Оновлення відображення грошей
        function updateMoneyDisplay() {
            moneyDisplay.textContent = `Гроші: $${gameData.money.toLocaleString()}`;
        }

        // Запуск доходу
        function startIncome() {
            let timeLeft = gameData.incomeInterval / 1000;
            
            const incomeInterval = setInterval(() => {
                timeLeft--;
                incomeTimer.textContent = `${timeLeft}с`;
                
                if (timeLeft <= 0) {
                    gameData.money += gameData.income;
                    updateMoneyDisplay();
                    timeLeft = gameData.incomeInterval / 1000;
                    showNotification(`Отримано дохід: $${gameData.income.toLocaleString()}`);
                }
            }, 1000);
        }

        // Показати сповіщення
        function showNotification(message) {
            notification.textContent = message;
            notification.classList.add('show');
            
            setTimeout(() => {
                notification.classList.remove('show');
            }, 3000);
        }

        // Обробники подій
        startGameBtn.addEventListener('click', initGame);
        playButton.addEventListener('click', initGame);
        
        buildAirportBtn.addEventListener('click', buildAirport);
        
        document.querySelectorAll('.buy-plane').forEach(btn => {
            btn.addEventListener('click', () => {
                buyPlane(btn.dataset.plane);
            });
        });
        
        directPlaneBtn.addEventListener('click', directPlane);
        stopDirectBtn.addEventListener('click', stopDirecting);

        // Початкова ініціалізація
        updateMoneyDisplay();
    </script>
</body>
</html>
