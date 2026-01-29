<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Pixel Garden: Tokyo Time</title>
    <style>
        /* 这里是你原本的所有 CSS */
        :root { --sky-day: #87CEEB; --sky-dusk: #FF7F50; --sky-night: #1a1a2e; --grass: #4caf50; --soil: #5d4037; --pixel-size: 8px; /* 调整这个可以改变像素颗粒大小 */ --flower-color: #ffeb3b; /* 每日随机颜色 */ }
        body { margin: 0; overflow: hidden; background-color: var(--sky-day); font-family: 'Courier New', Courier, monospace; transition: background-color 3s ease; display: flex; justify-content: center; align-items: center; height: 100vh; user-select: none; }
        #game-container { position: relative; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: flex-end; }
        .celestial-body { position: absolute; top: 10%; right: 15%; width: 60px; height: 60px; background: #fff; border-radius: 50%; box-shadow: 0 0 20px rgba(255,255,255,0.8); transition: all 2s; }
        .sun { background: #ffd700; box-shadow: 0 0 40px #ffd700; }
        .moon { background: #f4f6f0; box-shadow: 0 0 20px #fff; }
        .aurora { position: absolute; top: 0; left: 0; width: 100%; height: 60%; background: linear-gradient(90deg, rgba(0,255,127,0) 0%, rgba(0,255,127,0.3) 50%, rgba(138,43,226,0) 100%); opacity: 0; transition: opacity 1s; pointer-events: none; z-index: 1; }
        .aurora.active { opacity: 1; animation: aurora-move 5s infinite linear; }
        @keyframes aurora-move { 0% { background-position: 0% 50%; } 100% { background-position: 100% 50%; } }
        .ground { width: 100%; height: 120px; background-color: var(--grass); border-top: 4px solid #388e3c; position: relative; z-index: 10; display: flex; justify-content: center; }
        .pot-area { position: absolute; bottom: 40px; width: 200px; height: 300px; display: flex; justify-content: center; align-items: flex-end; }
        .pixel-art { image-rendering: pixelated; }
        .seed { width: 20px; height: 12px; background: #8d6e63; border-radius: 50%; position: relative; }
        .zzz { position: absolute; top: -20px; right: -10px; font-size: 20px; color: #fff; opacity: 0; animation: float-zzz 2s infinite; }
        .sprout { width: 6px; height: 30px; background: #8bc34a; position: relative; }
        .sprout::before { content: ''; position: absolute; top: 0; left: -10px; width: 10px; height: 10px; background: #8bc34a; border-radius: 10px 0 10px 0; }
        .sprout::after { content: ''; position: absolute; top: 5px; right: -10px; width: 10px; height: 10px; background: #8bc34a; border-radius: 0 10px 0 10px; }
        .stem-tall { width: 8px; height: 100px; background: #66bb6a; position: relative; }
        .stem-tall::before, .stem-tall::after { content: ''; position: absolute; background: #66bb6a; width: 20px; height: 20px; }
        .stem-tall::before { top: 30%; left: -20px; border-radius: 20px 0 20px 0; }
        .stem-tall::after { top: 50%; right: -20px; border-radius: 0 20px 0 20px; }
        .bud { width: 24px; height: 30px; background: var(--flower-color); border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%; border: 2px solid #333; position: absolute; top: -30px; left: -8px; }
        .flower-head { position: absolute; top: -50px; left: -26px; width: 60px; height: 60px; display: flex; justify-content: center; align-items: center; }
        .petal { position: absolute; width: 20px; height: 20px; background: var(--flower-color); box-shadow: 2px 2px 0 rgba(0,0,0,0.1); }
        .petal:nth-child(1) { top: 0; left: 20px; }
        .petal:nth-child(2) { bottom: 0; left: 20px; }
        .petal:nth-child(3) { left: 0; top: 20px; }
        .petal:nth-child(4) { right: 0; top: 20px; }
        .center { position: absolute; width: 24px; height: 24px; background: #5d4037; z-index: 2; }
        .shake { animation: shake-anim 0.5s ease-in-out; }
        @keyframes shake-anim { 0% { transform: rotate(0deg); } 25% { transform: rotate(-5deg); } 50% { transform: rotate(5deg); } 75% { transform: rotate(-5deg); } 100% { transform: rotate(0deg); } }
        @keyframes float-zzz { 0% { opacity: 0; transform: translateY(0); } 50% { opacity: 1; } 100% { opacity: 0; transform: translateY(-20px); } }
        .bug { position: absolute; width: 12px; height: 8px; background: #333; border-radius: 4px; cursor: pointer; z-index: 100; display: none; box-shadow: 2px 2px 0 rgba(0,0,0,0.2); }
        .bug::before { content: ''; position: absolute; top: -4px; left: 0; width: 4px; height: 4px; border-left: 1px solid #333; }
        .heart-bubble { position: absolute; color: #ff4081; font-size: 24px; animation: float-up 1.5s forwards; pointer-events: none; z-index: 200; }
        @keyframes float-up { 0% { opacity: 1; transform: translateY(0) scale(0.5); } 100% { opacity: 0; transform: translateY(-50px) scale(1.5); } }
        .ui-bar { position: absolute; bottom: 20px; background: rgba(255, 255, 255, 0.9); padding: 10px 20px; border-radius: 12px; box-shadow: 0 4px 10px rgba(0,0,0,0.2); display: flex; gap: 15px; z-index: 20; border: 2px solid #333; }
        .btn { background: none; border: 2px solid #ddd; border-radius: 8px; padding: 8px 12px; cursor: pointer; font-size: 24px; transition: transform 0.1s, background 0.2s; }
        .btn:hover { background: #f0f0f0; transform: scale(1.1); }
        .btn:active { transform: scale(0.95); }
        .time-display { position: absolute; top: 20px; left: 20px; background: rgba(0,0,0,0.5); color: #fff; padding: 5px 10px; border-radius: 4px; font-size: 14px; }
        .stage-display { position: absolute; top: 20px; right: 20px; color: #fff; background: rgba(0,0,0,0.5); padding: 5px 10px; border-radius: 4px; font-size: 14px; }
    </style>
</head>
<body>

<div class="aurora" id="aurora"></div>

<div id="game-container">
    <div class="time-display" id="clock">Loading...</div>
    <div class="stage-display" id="status">Checking Tokyo...</div>

    <div class="celestial-body" id="sun-moon"></div>

    <div id="bug-container"></div>

    <div class="pot-area">
        <div id="plant" class="pixel-art"></div>
    </div>

    <div class="ground"></div>

    <div class="ui-bar">
        <button class="btn" onclick="waterPlant()" title="浇水">💧</button>
        <button class="btn" onclick="catchBugTrigger()" title="捉虫">🕸️</button>
        <button class="btn" onclick="fertilize()" title="施肥">🧪</button>
    </div>
</div>

<script>
    // === 核心配置 ===
    function getTokyoTime() {
        const now = new Date();
        const utc = now.getTime() + (now.getTimezoneOffset() * 60000);
        return new Date(utc + (3600000 * 9));
    }

    function getDailyColor() {
        const dateStr = getTokyoTime().toDateString();
        let hash = 0;
        for (let i = 0; i < dateStr.length; i++) { hash = dateStr.charCodeAt(i) + ((hash << 5) - hash); }
        const hue = Math.abs(hash) % 360;
        return `hsl(${hue}, 70%, 60%)`;
    }

    document.documentElement.style.setProperty('--flower-color', getDailyColor());

    const plantEl = document.getElementById('plant');
    const sunMoonEl = document.getElementById('sun-moon');
    const bodyEl = document.body;
    const statusEl = document.getElementById('status');
    const clockEl = document.getElementById('clock');
    const auroraEl = document.getElementById('aurora');

    function updateGame() {
        const tokyoTime = getTokyoTime();
        const hours = tokyoTime.getHours();
        const minutes = tokyoTime.getMinutes();
        
        clockEl.innerText = `Tokyo: ${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}`;

        let stage = 0;
        let bgType = 'night';

        if (hours >= 10 && hours < 12) { stage = 1; bgType = 'day'; }
        else if (hours >= 12 && hours < 14) { stage = 2; bgType = 'day'; }
        else if (hours >= 14 && hours < 16) { stage = 3; bgType = 'day'; }
        else if (hours >= 16 && hours < 18) { stage = 4; bgType = 'day'; }
        else if (hours >= 18 && hours < 19) { stage = 5; bgType = 'dusk'; }
        else { stage = 0; bgType = 'night'; } 

        renderEnvironment(bgType);
        renderPlant(stage);
    }

    function renderEnvironment(type) {
        if (type === 'day') {
            bodyEl.style.backgroundColor = 'var(--sky-day)';
            sunMoonEl.className = 'celestial-body sun';
        } else if (type === 'dusk') {
            bodyEl.style.backgroundColor = 'var(--sky-dusk)';
            sunMoonEl.className = 'celestial-body sun';
            sunMoonEl.style.top = '60%'; 
        } else {
            bodyEl.style.backgroundColor = 'var(--sky-night)';
            sunMoonEl.className = 'celestial-body moon';
            sunMoonEl.style.top = '10%';
        }
    }

    let currentRenderedStage = -1;

    function renderPlant(stage) {
        if (stage === currentRenderedStage) return; 
        currentRenderedStage = stage;

        plantEl.innerHTML = ''; 
        plantEl.className = 'pixel-art'; 

        let html = '';
        let statusText = '';

        if (stage === 0) {
            html = `<div class="seed"><div class="zzz">zZ</div></div>`;
            statusText = 'Night: Sleeping...';
        } else if (stage === 1) {
            html = `<div class="sprout"></div>`;
            statusText = '10:00 - Sprouting';
        } else if (stage === 2) {
            html = `<div class="stem-tall"></div>`;
            statusText = '12:00 - Growing';
        } else if (stage === 3) {
            html = `<div class="stem-tall"><div class="bud"></div></div>`;
            statusText = '14:00 - Budding';
        } else if (stage === 4) {
            html = `
                <div class="stem-tall">
                    <div class="flower-head">
                        <div class="petal"></div><div class="petal"></div>
                        <div class="petal"></div><div class="petal"></div>
                        <div class="center"></div>
                    </div>
                </div>`;
            statusText = '16:00 - Blooming!';
        } else if (stage === 5) {
            html = `<div class="stem-tall"><div class="seed" style="top:-10px; background:#3e2723;"></div></div>`;
            statusText = '18:00 - Fruiting';
        }

        plantEl.innerHTML = html;
        statusEl.innerText = statusText;
    }

    function waterPlant() { triggerJoy(); }
    function fertilize() {
        auroraEl.classList.add('active');
        setTimeout(() => { auroraEl.classList.remove('active'); }, 5000);
        triggerJoy();
    }
    function bugSystem() {
        setInterval(() => {
            const hour = getTokyoTime().getHours();
            if (hour < 10 || hour >= 19) return; 
            if (Math.random() > 0.7 && document.querySelector('.bug') === null) { spawnBug(); }
        }, 10000);
    }
    function spawnBug() {
        const bug = document.createElement('div');
        bug.className = 'bug';
        bug.style.display = 'block';
        const randomBottom = Math.floor(Math.random() * 200) + 50; 
        const randomLeft = Math.floor(Math.random() * 100) - 50; 
        bug.style.bottom = randomBottom + 'px';
        bug.style.left = randomLeft + 'px'; 
        bug.onclick = function(e) { e.stopPropagation(); this.remove(); triggerJoy(); };
        plantEl.appendChild(bug);
    }
    function catchBugTrigger() { spawnBug(); }

    function triggerJoy() {
        const content = plantEl.firstElementChild;
        if(content) { content.classList.remove('shake'); void content.offsetWidth; content.classList.add('shake'); }
        const heart = document.createElement('div');
        heart.className = 'heart-bubble';
        heart.innerText = '❤';
        const offset = Math.floor(Math.random() * 40) - 20;
        heart.style.left = `calc(50% + ${offset}px)`;
        heart.style.bottom = '100px';
        plantEl.appendChild(heart);
        setTimeout(() => heart.remove(), 1500);
    }

    setInterval(updateGame, 1000); 
    updateGame(); 
    bugSystem(); 
</script>
</body>
</html>
