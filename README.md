<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>War Sodium</title>
<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        background: #111;
        color: #fff;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    h1 { margin-top: 20px; }
    #menu, #game { display: none; flex-direction: column; align-items: center; }
    button { padding: 10px 20px; margin: 5px; cursor: pointer; font-size: 16px; }
    #battlefield {
        position: relative;
        width: 800px;
        height: 400px;
        background: #222;
        margin-top: 20px;
        border: 2px solid #555;
        overflow: hidden;
    }
    .frontline {
        position: absolute;
        top: 0; bottom: 0;
        width: 4px;
        background: yellow;
        left: 400px;
    }
    .soldier-counter {
        position: absolute;
        top: 10px;
        font-weight: bold;
        font-size: 18px;
        background: rgba(0,0,0,0.5);
        padding: 2px 5px;
        border-radius: 5px;
    }
    #controls { margin-top: 10px; }
</style>
</head>
<body>

<div id="menu">
    <h1>War Sodium</h1>
    <p>Выберите сложность:</p>
    <button onclick="startGame('easy')">Лёгкий</button>
    <button onclick="startGame('medium')">Средний</button>
    <button onclick="startGame('hard')">Сложный</button>
    <button onclick="startGame('hardest')">Хард</button>
</div>

<div id="game">
    <h1>War Sodium</h1>
    <div id="battlefield">
        <div class="frontline" id="frontline"></div>
        <div class="soldier-counter" id="redCount" style="right:10px;">0</div>
        <div class="soldier-counter" id="blueCount" style="left:10px;">0</div>
    </div>
    <div id="controls">
        <button id="addSoldiersBtn">+15k солдат</button>
    </div>
</div>

<script>
let redSoldiers = 0;
let blueSoldiers = 0;
let frontline = document.getElementById('frontline');
let redCounter = document.getElementById('redCount');
let blueCounter = document.getElementById('blueCount');
let addBtn = document.getElementById('addSoldiersBtn');
let addCooldown = false;
let battlefieldWidth = 800;

function startGame(difficulty) {
    document.getElementById('menu').style.display = 'none';
    document.getElementById('game').style.display = 'flex';
    
    switch(difficulty){
        case 'easy': redSoldiers = 100000; break;
        case 'medium': redSoldiers = 300000; break;
        case 'hard': redSoldiers = 600000; break;
        case 'hardest': redSoldiers = 1000000; break;
    }
    blueSoldiers = 0;
    updateCounters();
    startBattle();
}

function updateCounters() {
    redCounter.textContent = redSoldiers.toLocaleString();
    blueCounter.textContent = blueSoldiers.toLocaleString();
}

addBtn.onclick = function() {
    if(addCooldown) return;
    blueSoldiers += 15000;
    updateCounters();
    addCooldown = true;
    setTimeout(()=>{ addCooldown=false; }, 5000);
};

function startBattle() {
    function step() {
        if(redSoldiers <=0 || blueSoldiers <=0) return;
        
        // простой ИИ: красные наступают на синих
        let redAttack = Math.min(redSoldiers, 5000 + Math.random()*5000);
        let blueAttack = Math.min(blueSoldiers, 5000 + Math.random()*5000);
        
        redSoldiers -= blueAttack;
        blueSoldiers -= redAttack;
        
        // линия фронта: соотношение сил
        let total = redSoldiers + blueSoldiers;
        let ratio = blueSoldiers/total;
        frontline.style.left = (ratio * battlefieldWidth) + 'px';
        
        updateCounters();
        
        requestAnimationFrame(step);
    }
    requestAnimationFrame(step);
}

// показать меню при загрузке
document.getElementById('menu').style.display = 'flex';
</script>

</body>
</html>
