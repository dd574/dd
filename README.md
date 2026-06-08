<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
    <title>时空舆图 · 唐至清流放地域变迁 | 史地卷轴</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,400;500;600;700;800&family=Source+Serif+4:opsz,wght@8..60,500;600;700;800&display=swap" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        /* 卷轴开场样式 - 点击进入 */
        .scroll-intro {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: #1a120c;
            z-index: 1000;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            cursor: pointer;
            transition: opacity 1.2s cubic-bezier(0.23, 1, 0.32, 1);
        }
        .scroll-intro.fade-out {
            opacity: 0;
            pointer-events: none;
        }
        .scroll-bg {
            position: absolute;
            width: 100%;
            height: 100%;
            background-image: radial-gradient(circle at 25% 40%, rgba(210, 180, 140, 0.08) 2%, transparent 2.5%),
                              radial-gradient(circle at 70% 85%, rgba(180, 140, 100, 0.06) 1.5%, transparent 2%);
            background-size: 40px 40px, 35px 35px;
        }
        .scroll-container {
            position: relative;
            width: 680px;
            max-width: 85vw;
            background: #faf3e0;
            box-shadow: 0 30px 50px rgba(0,0,0,0.5), inset 0 0 0 1px rgba(255,245,215,0.8);
            border-radius: 4px;
            overflow: hidden;
            transform: scaleX(0);
            transform-origin: center;
            transition: transform 1.4s cubic-bezier(0.2, 0.9, 0.4, 1.1);
            pointer-events: none;
        }
        .scroll-container.active { transform: scaleX(1); }
        .scroll-inner {
            padding: 2.8rem 2.5rem;
            background: linear-gradient(145deg, #fff7e8 0%, #f7edda 100%);
            position: relative;
            border-left: 2px solid #c8a87a;
            border-right: 2px solid #c8a87a;
        }
        .scroll-roller {
            position: absolute;
            left: 0;
            width: 100%;
            height: 16px;
            background: linear-gradient(180deg, #b88b4a, #7a5a2e);
            border-radius: 8px;
            box-shadow: 0 2px 6px rgba(0,0,0,0.2);
        }
        .scroll-roller.top { top: -8px; }
        .scroll-roller.bottom { bottom: -8px; }
        .calligraphy { font-family: 'Source Serif 4', 'Times New Roman', serif; text-align: center; letter-spacing: 4px; }
        .group-name {
            font-size: 3.2rem;
            font-weight: 800;
            color: #3a2418;
            margin-bottom: 1rem;
            text-shadow: 2px 2px 3px rgba(0,0,0,0.05);
            opacity: 0;
            transform: translateY(15px);
            transition: opacity 0.6s ease, transform 0.5s ease;
        }
        .group-name.reveal { opacity: 1; transform: translateY(0); }
        .seal {
            display: inline-block;
            width: 70px;
            height: 70px;
            border: 2px solid #b5452c;
            border-radius: 2px;
            color: #b5452c;
            font-family: 'Inter', serif;
            font-size: 0.9rem;
            text-align: center;
            line-height: 1.3;
            padding: 12px 5px;
            margin-top: 15px;
            background: rgba(181,69,44,0.05);
            transform: rotate(-6deg);
            opacity: 0;
            transition: opacity 0.5s ease 0.3s;
        }
        .seal.show { opacity: 1; }
        .seal span { display: block; font-size: 1.1rem; font-weight: 600; }
        .sub-line {
            font-size: 1.1rem;
            color: #5e4733;
            margin-top: 1.2rem;
            letter-spacing: 2px;
            font-weight: 400;
            border-top: 1px dashed #c8a87a;
            padding-top: 1rem;
            opacity: 0;
            transition: opacity 0.5s ease 0.5s;
        }
        .sub-line.show { opacity: 1; }

        /* 主应用样式 */
        .main-app {
            opacity: 0;
            transition: opacity 1s ease;
        }
        .main-app.visible { opacity: 1; }
        body {
            background: #e8dfd3;
            font-family: 'Inter', sans-serif;
            color: #1e1914;
            line-height: 1.5;
        }
        .app-container {
            max-width: 1600px;
            width: 100%;
            margin: 0 auto;
            background: #fefaf2;
            border-radius: 2.5rem;
            box-shadow: 0 30px 50px -20px rgba(0, 0, 0, 0.3);
            overflow: hidden;
            border: 1px solid #e1d2c0;
        }
        .hero {
            background: #fff9f0;
            padding: 1.5rem 2rem 0.8rem 2rem;
            border-bottom: 1px solid #e6d9ca;
            text-align: center;
        }
        .hero h1 {
            font-family: 'Source Serif 4', serif;
            font-size: 2.2rem;
            font-weight: 800;
            color: #6b2e2a;
        }
        .hero p {
            color: #6b5a48;
            font-size: 0.9rem;
            margin-top: 0.3rem;
        }
        .map-core {
            padding: 1.5rem 2rem 1rem 2rem;
            position: relative;
        }
        .dynasty-control {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 1rem;
            margin-bottom: 1rem;
        }
        .dynasty-nav-group {
            display: flex;
            align-items: center;
            gap: 1rem;
            flex-wrap: wrap;
        }
        .dynasty-indicator {
            background: #e2d4c4;
            border-radius: 60px;
            padding: 0.3rem 1.2rem;
            font-weight: 700;
            font-size: 1rem;
            color: #4f3624;
            box-shadow: inset 0 1px 0 #fff6e8, 0 1px 3px rgba(0,0,0,0.05);
        }
        .dynasty-quick-nav {
            display: flex;
            gap: 0.5rem;
            background: #f6efE5;
            padding: 0.3rem 0.8rem;
            border-radius: 60px;
        }
        .dynasty-quick-btn {
            background: transparent;
            border: none;
            font-size: 0.85rem;
            font-weight: 600;
            padding: 0.4rem 1rem;
            border-radius: 40px;
            cursor: pointer;
            color: #6b4c34;
            transition: all 0.2s;
        }
        .dynasty-quick-btn.active {
            background: #d4a373;
            color: white;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        .dynasty-quick-btn:hover:not(.active) {
            background: #e2cfbb;
        }
        .nav-buttons { display: flex; gap: 1rem; }
        .nav-btn {
            background: #f0e4d6;
            border: none;
            font-size: 1.1rem;
            font-weight: 600;
            padding: 0.5rem 1.2rem;
            border-radius: 40px;
            cursor: pointer;
            transition: 0.2s;
            color: #4f3624;
        }
        .nav-btn:hover { background: #e2d0be; transform: translateY(-1px); }
        .legend-tag {
            display: flex;
            gap: 1.5rem;
            font-size: 0.7rem;
            color: #6b5a48;
            background: #fcf8f0;
            padding: 0.4rem 1rem;
            border-radius: 60px;
            margin-bottom: 1rem;
            flex-wrap: wrap;
        }
        .legend-tag span { display: inline-flex; align-items: center; gap: 6px; }
        .canvas-wrapper {
            position: relative;
            border-radius: 1.5rem;
            background: #f5efe3;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
        }
        canvas {
            display: block;
            width: 100%;
            height: auto;
            background: #f5efe3;
            border-radius: 1.5rem;
            cursor: crosshair;
        }
        .hover-tooltip {
            position: absolute;
            background: #2c2418ee;
            backdrop-filter: blur(6px);
            color: #f7eedb;
            padding: 0.75rem 1.2rem;
            border-radius: 1rem;
            font-size: 0.85rem;
            font-weight: 500;
            pointer-events: none;
            z-index: 200;
            max-width: 340px;
            white-space: normal;
            font-family: 'Inter', monospace;
            border-left: 4px solid #f39c12;
            box-shadow: 0 8px 20px rgba(0,0,0,0.3);
            line-height: 1.45;
        }
        .hover-tooltip strong { color: #f3cba5; }
        .hover-tooltip .quote { font-style: italic; color: #e0c8a0; margin-top: 6px; font-size: 0.8rem; border-top: 1px solid #5a4a38; padding-top: 5px; }
        .hover-tooltip .source { font-size: 0.7rem; color: #b8a27a; margin-top: 4px; }
        .info-panel {
            background: #ffffff;
            border-radius: 1.8rem;
            padding: 1.6rem 2rem;
            margin: 0 2rem 2rem 2rem;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.03), 0 0 0 1px #efe2d4;
        }
        .era-title {
            font-family: 'Source Serif 4', serif;
            font-size: 1.9rem;
            font-weight: 700;
            color: #7a3a2c;
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            flex-wrap: wrap;
            margin-bottom: 1.2rem;
            gap: 0.8rem;
            border-bottom: 2px solid #f0e2d4;
            padding-bottom: 0.5rem;
        }
        .era-sub {
            font-size: 0.85rem;
            background: #eee2d6;
            padding: 0.25rem 1rem;
            border-radius: 30px;
            font-weight: 600;
            color: #5d4532;
        }
        .stat-badge-group {
            display: flex;
            flex-wrap: wrap;
            gap: 1rem;
            margin: 1rem 0 1.2rem;
        }
        .stat-pill {
            background: #efe4d8;
            border-radius: 40px;
            padding: 0.35rem 1.2rem;
            font-size: 0.85rem;
            font-weight: 600;
            color: #56412e;
            transition: 0.1s;
        }
        .stat-pill.highlight { background: #d4a373; color: white; }
        .place-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 0.8rem;
            margin: 1.2rem 0 1.2rem;
        }
        .place-tag {
            background: #e7dbcd;
            border-radius: 30px;
            padding: 0.3rem 1.2rem;
            font-size: 0.8rem;
            font-weight: 500;
            color: #3e2c1e;
        }
        .place-tag.highlight { background: #d4a373; color: white; }
        .insight-text {
            background: #fdf7f0;
            padding: 1.2rem 1.5rem;
            border-radius: 1.3rem;
            font-size: 0.95rem;
            line-height: 1.6;
            color: #423728;
            margin: 1.2rem 0;
            border-left: 4px solid #cb8a64;
        }
        .footer {
            text-align: center;
            font-size: 0.7rem;
            padding: 1rem 2rem 1.5rem;
            color: #b1977c;
            border-top: 1px solid #e6d9ca;
        }
        @media (max-width: 900px) {
            .hero h1 { font-size: 1.4rem; }
            .era-title { font-size: 1.4rem; }
            .info-panel { padding: 1rem 1.2rem; margin: 0 1rem 1.5rem; }
            .map-core { padding: 1rem; }
            .dynasty-quick-btn { font-size: 0.7rem; padding: 0.2rem 0.7rem; }
        }
    </style>
</head>
<body>
<div id="scrollIntro" class="scroll-intro">
    <div class="scroll-bg"></div>
    <div class="scroll-container" id="scrollContainer">
        <div class="scroll-roller top"></div>
        <div class="scroll-inner calligraphy">
            <div class="group-name" id="groupName">第十组</div>
            <div class="seal" id="sealMark"><span>史</span><span>鉴</span></div>
            <div class="sub-line" id="subLine">时空视野下的历史变迁</div>
        </div>
        <div class="scroll-roller bottom"></div>
    </div>
</div>

<div id="mainApp" class="main-app" style="display: none;">
    <div class="app-container">
        <div class="hero">
            <h1>🗺️ 时空舆图 · 唐至清流放地域变迁</h1>
            <p>⏵ 点击上方按钮快速切换朝代 | ✨ 鼠标悬停流放点 — 阅览原典史料</p>
        </div>
        <div class="map-core">
            <div class="dynasty-control">
                <div class="dynasty-nav-group">
                    <div class="dynasty-indicator" id="dynastyNameLabel">唐代 · 瘴疠南荒</div>
                    <div class="dynasty-quick-nav" id="quickNavContainer"></div>
                </div>
                <div class="nav-buttons">
                    <button class="nav-btn" id="prevBtn">◀ 上一个</button>
                    <button class="nav-btn" id="nextBtn">下一个 ▶</button>
                </div>
            </div>
            <div class="legend-tag" id="legendContainer">
                <!-- 动态图例，朝代地图和总结页不同 -->
                <span><span style="display:inline-block; width:14px; height:14px; background:#b34e4e; border-radius:50%;"></span>核心流放区</span>
                <span><span style="display:inline-block; width:14px; height:14px; background:#e09d32; border-radius:50%;"></span>次要/特殊区</span>
                <span><span style="display:inline-block; width:14px; height:14px; background:#3a6b4b; border-radius:50%;"></span>戍边实边</span>
                <span><span style="display:inline-block; width:32px; height:4px; background:#f39c12; vertical-align:middle;"></span>流放重心转移</span>
            </div>
            <div class="canvas-wrapper" style="position: relative;">
                <canvas id="dynastyMapCanvas" width="1800" height="1100" style="width:100%; height:auto; display:block;"></canvas>
                <div id="hoverTooltip" class="hover-tooltip" style="display: none; position: absolute;"></div>
            </div>
        </div>
        <div class="info-panel" id="infoPanel"></div>
        <div class="footer">
            📜 依据《两唐书》《宋史》《元史》《明实录》《大清律例》及谭其骧《中国历史地图集》<br>
            ✍️ 鼠标悬停或触摸点按 → 揭示史料原文出处 | 总结页展示流放重心真实迁移路线
        </div>
    </div>
</div>

<script>
    (function() {
        // 卷轴点击进入
        const scrollIntro = document.getElementById('scrollIntro');
        const scrollContainer = document.getElementById('scrollContainer');
        const groupNameEl = document.getElementById('groupName');
        const sealMark = document.getElementById('sealMark');
        const subLineEl = document.getElementById('subLine');
        const mainApp = document.getElementById('mainApp');
        
        let introAnimationStarted = false;
        function startRollAnimation() {
            if (introAnimationStarted) return;
            introAnimationStarted = true;
            scrollContainer.classList.add('active');
            setTimeout(() => { groupNameEl.classList.add('reveal'); }, 400);
            setTimeout(() => { sealMark.classList.add('show'); }, 800);
            setTimeout(() => { subLineEl.classList.add('show'); }, 1100);
        }
        function enterMainApp() {
            if (scrollIntro.style.display === 'none') return;
            scrollIntro.classList.add('fade-out');
            setTimeout(() => {
                scrollIntro.style.display = 'none';
                mainApp.style.display = 'block';
                setTimeout(() => { 
                    mainApp.classList.add('visible');
                    initMap(); 
                }, 60);
            }, 1000);
        }
        scrollIntro.addEventListener('click', (e) => {
            e.stopPropagation();
            if (!introAnimationStarted) {
                startRollAnimation();
                setTimeout(() => { enterMainApp(); }, 400);
            } else {
                enterMainApp();
            }
        });
        setTimeout(() => { if(!introAnimationStarted) startRollAnimation(); }, 100);
    })();

    // ========= 地图核心 =========
    function initMap() {
        const canvas = document.getElementById('dynastyMapCanvas');
        const ctx = canvas.getContext('2d');
        const tooltip = document.getElementById('hoverTooltip');
        const legendContainer = document.getElementById('legendContainer');
        canvas.width = 1800;
        canvas.height = 1100;
        
        const sections = ['tang', 'song', 'yuan', 'ming', 'qing', 'summary'];
        let currentIndex = 0;
        let currentPoints = [];
        
        // 统一背景
        function drawUnifiedBackground(ctx, w, h) {
            ctx.fillStyle = "#f5efe3";
            ctx.fillRect(0, 0, w, h);
            ctx.globalAlpha = 0.04;
            ctx.fillStyle = "#c8b28b";
            for(let i=0;i<200;i++) ctx.fillRect(Math.random()*w, Math.random()*h, 1.2, 1.2);
            ctx.globalAlpha = 1;
        }
        
        // 各朝代轮廓
        const outlines = {
            tang: [[0.68,0.10],[0.74,0.11],[0.81,0.14],[0.88,0.18],[0.93,0.22],[0.95,0.27],[0.93,0.31],[0.89,0.35],[0.84,0.39],[0.79,0.43],[0.75,0.47],[0.72,0.51],[0.70,0.55],[0.68,0.59],[0.67,0.63],[0.66,0.67],[0.64,0.71],[0.61,0.75],[0.57,0.79],[0.53,0.83],[0.49,0.86],[0.45,0.87],[0.41,0.85],[0.38,0.82],[0.35,0.78],[0.34,0.73],[0.34,0.68],[0.36,0.63],[0.38,0.58],[0.42,0.53],[0.46,0.48],[0.51,0.43],[0.56,0.37],[0.62,0.31],[0.66,0.24],[0.67,0.16]],
            song: [[0.75,0.22],[0.81,0.24],[0.86,0.28],[0.89,0.32],[0.88,0.36],[0.84,0.40],[0.80,0.44],[0.76,0.48],[0.73,0.52],[0.71,0.56],[0.70,0.60],[0.69,0.64],[0.67,0.68],[0.64,0.72],[0.60,0.76],[0.56,0.79],[0.52,0.82],[0.48,0.84],[0.45,0.83],[0.43,0.80],[0.42,0.76],[0.43,0.72],[0.45,0.67],[0.48,0.62],[0.52,0.57],[0.57,0.52],[0.63,0.46],[0.69,0.40],[0.73,0.33],[0.74,0.27]],
            yuan: [[0.70,0.08],[0.78,0.09],[0.86,0.11],[0.93,0.15],[0.97,0.20],[0.98,0.25],[0.96,0.29],[0.92,0.33],[0.87,0.37],[0.82,0.41],[0.78,0.45],[0.75,0.49],[0.73,0.53],[0.71,0.57],[0.69,0.61],[0.66,0.65],[0.62,0.69],[0.57,0.73],[0.52,0.77],[0.47,0.80],[0.42,0.82],[0.37,0.83],[0.32,0.81],[0.28,0.77],[0.26,0.72],[0.27,0.66],[0.30,0.60],[0.34,0.54],[0.39,0.48],[0.45,0.42],[0.52,0.35],[0.59,0.28],[0.66,0.20],[0.69,0.13]],
            ming: [[0.77,0.19],[0.83,0.22],[0.87,0.26],[0.89,0.30],[0.88,0.34],[0.85,0.38],[0.81,0.42],[0.77,0.46],[0.74,0.50],[0.72,0.54],[0.70,0.58],[0.69,0.62],[0.68,0.66],[0.66,0.70],[0.63,0.74],[0.59,0.78],[0.55,0.81],[0.51,0.83],[0.47,0.84],[0.44,0.82],[0.42,0.79],[0.41,0.75],[0.42,0.71],[0.44,0.66],[0.47,0.61],[0.51,0.56],[0.56,0.50],[0.62,0.44],[0.68,0.38],[0.73,0.31],[0.76,0.24]],
            qing: [[0.70,0.06],[0.78,0.07],[0.86,0.09],[0.93,0.13],[0.97,0.18],[0.98,0.23],[0.95,0.28],[0.91,0.32],[0.86,0.36],[0.82,0.40],[0.78,0.44],[0.75,0.48],[0.73,0.52],[0.71,0.56],[0.69,0.60],[0.66,0.64],[0.62,0.68],[0.57,0.72],[0.52,0.76],[0.47,0.79],[0.42,0.81],[0.37,0.82],[0.32,0.80],[0.29,0.76],[0.28,0.71],[0.29,0.65],[0.32,0.59],[0.36,0.53],[0.41,0.47],[0.47,0.41],[0.54,0.34],[0.61,0.27],[0.67,0.19],[0.69,0.12]]
        };
        
        // 流放点数据（保持完整）
        const pointsConfig = {
            tang: [{name:"岭南道",x:0.70,y:0.81,type:"core",desc:"65%流人",labelX:12,labelY:-28,tagMatch:"岭南道",statMatch:"岭南道138例",quote:"杜佑《通典》：「五岭之南，涨海之北，三代之前是为荒服。」",source:"《通典·南蛮下》"},{name:"黔中道",x:0.63,y:0.72,type:"core",desc:"27例",labelX:12,labelY:-28,tagMatch:"黔中道",statMatch:"黔中·剑南次之",quote:"《旧唐书》载黔中「地多瘴疠，谪居者多不返」",source:"《旧唐书·地理志》"},{name:"剑南道",x:0.56,y:0.70,type:"core",desc:"22例",labelX:12,labelY:-28,tagMatch:"剑南道",statMatch:"黔中·剑南次之",quote:"剑南道「西南夷地，流人配隶雅州、巂州」",source:"《唐六典》"},{name:"安南",x:0.74,y:0.87,type:"core",desc:"极南瘴地",labelX:-65,labelY:-15,tagMatch:"安南都护府",statMatch:"岭南道138例",quote:"沈佺期诗：「崇山瘴疠不堪闻，两地江山万余里」",source:"《遥同杜员外审言过岭》"},{name:"西州",x:0.43,y:0.43,type:"border",desc:"戍边",labelX:12,labelY:-28,tagMatch:"西州·庭州",statMatch:"西北仅戍边实边",quote:"唐代以戍边为名流放罪人至西域军镇",source:"《唐律疏议》"}],
            song: [{name:"沙门岛",x:0.87,y:0.45,type:"core",desc:"最重配隶",labelX:-70,labelY:-15,tagMatch:"沙门岛",statMatch:"沙门岛(最重配隶)",quote:"《宋大诏令集》：「罪不致死，配隶逐州五百里、千里外牢城及沙门岛。」",source:"大中祥符六年诏"},{name:"琼/儋州",x:0.66,y:0.89,type:"core",desc:"海南",labelX:12,labelY:-28,tagMatch:"琼/儋州",statMatch:"海南四州",quote:"《庆元条法事类》定「远恶州」：琼、崖、儋、万安军",source:"《庆元条法事类》"},{name:"雷州",x:0.70,y:0.84,type:"second",desc:"远恶州",labelX:12,labelY:-28,tagMatch:"雷州/惠州",statMatch:"岭南贬官70%+",quote:"《宋史》：「配隶重者沙门岛，其次岭表。」",source:"《宋史·刑法志》"},{name:"惠州",x:0.79,y:0.76,type:"second",desc:"苏轼贬所",labelX:12,labelY:-28,tagMatch:"雷州/惠州",statMatch:"岭南贬官70%+",quote:"苏轼《到惠州》：「仿佛曾游岂梦中」",source:"苏轼诗"},{name:"镇戎军",x:0.57,y:0.48,type:"border",desc:"西北戍边",labelX:12,labelY:-28,tagMatch:"镇戎军",statMatch:"西北戍边",quote:"北宋沿边寨堡配隶军犯，实边御夏",source:"《宋会要辑稿》"}],
            yuan: [{name:"辽阳",x:0.83,y:0.35,type:"core",desc:"南人流",labelX:12,labelY:-28,tagMatch:"辽阳行省",statMatch:"南人迁辽阳迤北",quote:"《大元通制》：「流则南人迁于辽阳迤北之地」",source:"《元史·刑法志》"},{name:"奴儿干",x:0.91,y:0.27,type:"core",desc:"极北苦寒",labelX:-60,labelY:-20,tagMatch:"奴儿干",statMatch:"南人迁辽阳迤北",quote:"元人黄濬：「征东元帅府，道路险阻」",source:"《金华黄先生文集》"},{name:"雷/琼州",x:0.69,y:0.86,type:"core",desc:"北人流",labelX:12,labelY:-28,tagMatch:"雷州/琼州",statMatch:"北人流广海/琼州",quote:"《元史》：「北人迁于南方湖广之乡」",source:"《元史》"},{name:"大理",x:0.55,y:0.81,type:"second",desc:"云南",labelX:12,labelY:-28,tagMatch:"大理",statMatch:"云南·吐蕃特殊",quote:"元代云南行省为次重流放地",source:"《元典章》"},{name:"别失八里",x:0.39,y:0.45,type:"border",desc:"西域戍边",labelX:-65,labelY:-15,tagMatch:"别失八里",statMatch:"西域戍边",quote:"元朝发遣罪人至西域别失八里屯田",source:"《元史·兵志》"}],
            ming: [{name:"辽东",x:0.85,y:0.37,type:"core",desc:"极边充军",labelX:12,labelY:-28,tagMatch:"辽东都司",statMatch:"辽东/甘肃极边充军",quote:"《明实录》嘉靖初：「北直隶、山东俱发甘肃；山西、河南发辽东。」",source:"《明实录》"},{name:"甘肃",x:0.47,y:0.41,type:"core",desc:"极边",labelX:12,labelY:-28,tagMatch:"甘肃镇",statMatch:"辽东/甘肃极边充军",quote:"明代极边充军四千里，甘肃镇为西北最远戍所",source:"《大明律》"},{name:"两广烟瘴",x:0.71,y:0.80,type:"core",desc:"雷/钦",labelX:12,labelY:-28,tagMatch:"两广烟瘴",statMatch:"两广·云贵烟瘴",quote:"《刑令》：官吏犯赃至流罪，并发两广烟瘴",source:"明代《刑令》"},{name:"云南",x:0.54,y:0.80,type:"second",desc:"烟瘴卫所",labelX:12,labelY:-28,tagMatch:"云南贵州",statMatch:"两广·云贵烟瘴",quote:"嘉靖以后烟瘴专指云贵两广",source:"《大明律》"},{name:"哈密卫",x:0.42,y:0.43,type:"border",desc:"西北戍边",labelX:-65,labelY:-15,tagMatch:"哈密卫",statMatch:"西北戍边",quote:"关西七卫为明代西北戍边重镇",source:"《明史·西域传》"}],
            qing: [{name:"宁古塔",x:0.87,y:0.35,type:"core",desc:"披甲人为奴",labelX:-70,labelY:-20,tagMatch:"宁古塔",statMatch:"宁古塔与披甲人为奴",quote:"《研堂见闻杂记》：「宁古塔...重冰积雪，非复世界」",source:"《研堂见闻杂记》"},{name:"黑龙江",x:0.84,y:0.30,type:"core",desc:"发遣",labelX:12,labelY:-28,tagMatch:"黑龙江",statMatch:"宁古塔与披甲人为奴",quote:"《大清律例》规定黑龙江为遣犯主要发配地",source:"《大清律例》"},{name:"伊犁",x:0.36,y:0.42,type:"core",desc:"乾隆核心",labelX:-65,labelY:-15,tagMatch:"伊犁",statMatch:"乾隆后新疆顶级流放",quote:"《清史稿》：发往伊犁、乌鲁木齐各回城",source:"《清史稿》"},{name:"乌鲁木齐",x:0.43,y:0.45,type:"core",desc:"屯田",labelX:12,labelY:-28,tagMatch:"乌鲁木齐",statMatch:"乾隆后新疆顶级流放",quote:"发遣乌鲁木齐人犯，种地当差",source:"《钦定大清会典》"},{name:"科布多",x:0.64,y:0.31,type:"border",desc:"漠北戍边",labelX:12,labelY:-28,tagMatch:"科布多",statMatch:"烟瘴替补云贵两广",quote:"《清史稿》：并发齐齐哈尔、喀尔喀、科布多",source:"《清史稿》"}]
        };
        
        // 信息面板
        const infoData = {
            tang: { title:"唐代 · 瘴乡绝域", sub:"岭南为最 占比65%", stats:["📜 岭南道138例 / 211人","⚔️ 黔中·剑南次之","🏔️ 西北仅戍边实边"], tags:["岭南道","黔中道","剑南道","西州·庭州","安南都护府"], insight:"📌 以南方“瘴疠之地”为核心惩罚区，西北因吐蕃隔绝无法安置重犯。", driver:"⚙️ 驱动：惩罚威慑为主，岭南未开发，“瘴疠”象征死亡威胁。", note:"《两唐书》所见流人：南方远多于北方，岭南道占绝对主体。" },
            song: { title:"宋代 · 海岛炼狱", sub:"沙门岛 → 海南 → 广南", stats:["⚓ 沙门岛(最重配隶)","🌴 海南四州","📉 岭南贬官70%+"], tags:["沙门岛","琼/儋州","雷州/惠州","镇戎军"], insight:"📌 形成“沙门岛→海南→广南→近地”四级体系。北方领土丧失后，南方开发使岭南惩戒性下降。", driver:"⚙️ 驱动：疆域收缩，南方经济价值上升，惩罚需“加码”。", note:"苏轼：黄州→惠州→儋州。" },
            yuan: { title:"元代 · 南北对流", sub:"南人北流 · 北人南流", stats:["❄️ 南人迁辽阳迤北","🌊 北人流广海/琼州","🏔️ 云南·吐蕃特殊"], tags:["辽阳行省","雷州/琼州","大理","别失八里","奴儿干"], insight:"📌 首次打破“只南不北”传统，实行“南人迁辽阳，北人迁湖广”的对向流放。", driver:"⚙️ 驱动：疆域统一使南北对流成为可能，“异土重惩”增强惩罚性。", note:"元代流放地三大核心：辽阳、湖广、云南。" },
            ming: { title:"明代 · 边卫错置", sub:"洪武互调 → 嘉靖分遣", stats:["⚔️ 辽东/甘肃极边充军","🌿 两广·云贵烟瘴","🎯 与卫所制深度绑定"], tags:["辽东都司","甘肃镇","两广烟瘴","云南贵州","哈密卫"], insight:"📌 流放首要目的变为“填实军伍”。南方经济繁荣使岭南失去惩戒性，烟瘴区转向云贵。", driver:"⚙️ 驱动：军事制度驱动，流放服务于北部边防。", note:"《大明律》形成附近、边卫、边远、极边四级充军制度。" },
            qing: { title:"清代 · 寒漠极戍", sub:"宁古塔 → 新疆伊犁", stats:["❄️ 宁古塔与披甲人为奴","🏜️ 乾隆后新疆顶级流放","🌲 烟瘴替补云贵两广"], tags:["宁古塔","伊犁","乌鲁木齐","科布多","黑龙江"], insight:"📌 新疆纳入版图后，重犯大规模发遣伊犁等地，完成流放重心“华南→东北→西北”的最终转移。", driver:"⚙️ 驱动：西北纳入版图，为巩固边疆，将罪犯作为“人力资源”投入新疆开发。", note:"《大清律例》五军道里表，新疆、黑龙江、吉林为三大遣犯地。" },
            summary: { title:"📜 千年流放 · 重心迁移总览", sub:"真实地理路径示意", stats:["🏮 唐：岭南瘴乡","⛵ 宋：海南/沙门岛","⚖️ 元：南北对流(辽阳/湖广)","🏹 明：西北边卫(甘肃/辽东)","❄️ 清：新疆伊犁"], tags:["岭南→海南/山东→辽阳→甘肃/辽东→新疆","惩罚·实边·开发三重逻辑","边疆治理的缩影"], insight:"📌 流放重心从“南荒”逐渐转向北疆与西北，轨迹映射王朝疆域伸缩与区域开发程度。岭南从“荒服”变腹地，东北、新疆渐成新惩戒区，体现国家战略从威慑到实边的演变。", driver:"⚙️ 核心驱动：疆域变动、经济开发差异、军事戍边需求。自唐至清，流放地随国家版图扩张而转移，清代完成向西北的最终转向。", note:"宏观而言：流放制度既是惩罚，亦是中原王朝整合边缘地带的重要工具。" }
        };
        
        function drawOutline(ctx, dynasty, w, h) {
            if(dynasty === 'summary') return;
            const points = outlines[dynasty];
            if(!points) return;
            ctx.save(); ctx.beginPath(); ctx.moveTo(points[0][0]*w, points[0][1]*h);
            for(let i=1;i<points.length;i++) ctx.lineTo(points[i][0]*w, points[i][1]*h);
            ctx.closePath(); ctx.fillStyle = "rgba(210, 185, 145, 0.25)"; ctx.fill();
            ctx.strokeStyle = "#b88a5a"; ctx.lineWidth = 2.5; ctx.stroke(); ctx.restore();
        }
        
        function drawMarkers(ctx, points, w, h) {
            for(let p of points) {
                let x = p.x*w, y = p.y*h;
                let color = p.type==="core"?"#b34e4e":(p.type==="second"?"#e09d32":"#3a6b4b");
                ctx.beginPath(); ctx.arc(x, y, 22, 0, 2*Math.PI);
                ctx.fillStyle = color; ctx.fill(); ctx.strokeStyle = "#fff8ef"; ctx.lineWidth = 2.5; ctx.stroke();
                ctx.font = "bold 30px 'Inter', sans-serif"; ctx.fillStyle = "#2c2418"; ctx.fillText(p.name, x+(p.labelX||24), y+(p.labelY||-24));
                if(p.desc) { ctx.font = "20px 'Inter'"; ctx.fillStyle = "#9c6346"; ctx.fillText(p.desc, x+(p.labelX||24), y+(p.labelY||-24)+30); }
            }
        }
        
        function drawArrows(ctx, dynasty, w, h) {
            if(dynasty === 'summary') return;
            ctx.save(); ctx.strokeStyle = "#f39c12"; ctx.fillStyle = "#f39c12"; ctx.lineWidth = 6;
            function arrowHead(toX,toY,fromX,fromY){ let ang=Math.atan2(toY-fromY,toX-fromX),sz=30; let ax=toX-sz*Math.cos(ang-Math.PI/6),ay=toY-sz*Math.sin(ang-Math.PI/6),bx=toX-sz*Math.cos(ang+Math.PI/6),by=toY-sz*Math.sin(ang+Math.PI/6); ctx.beginPath(); ctx.moveTo(toX,toY); ctx.lineTo(ax,ay); ctx.lineTo(bx,by); ctx.fill();}
            function label(txt,cx,cy,offX,offY,fs){ ctx.font=`bold ${fs}px 'Inter'`; let m=ctx.measureText(txt); ctx.fillStyle="rgba(245, 235, 215, 0.88)"; ctx.fillRect(cx+offX-12, cy+offY-fs+6, m.width+24, fs+12); ctx.fillStyle="#c7611e"; ctx.fillText(txt, cx+offX, cy+offY); }
            if(dynasty==='tang'){ let sx=0.53*w,sy=0.59*h,ex=0.70*w,ey=0.80*h; ctx.beginPath(); ctx.moveTo(sx,sy); ctx.lineTo(ex,ey); ctx.stroke(); arrowHead(ex,ey,sx,sy); label("南流岭南",sx+(ex-sx)*0.45,sy+(ey-sy)*0.45,-80,-40,32);}
            else if(dynasty==='song'){ let sx=0.59*w,sy=0.57*h,ex=0.86*w,ey=0.45*h; ctx.beginPath(); ctx.moveTo(sx,sy); ctx.lineTo(ex,ey); ctx.stroke(); arrowHead(ex,ey,sx,sy); label("海岛极刑",sx+(ex-sx)*0.55,sy+(ey-sy)*0.55,-85,-42,32); let sx2=0.63*w,sy2=0.64*h,ex2=0.67*w,ey2=0.88*h; ctx.beginPath(); ctx.moveTo(sx2,sy2); ctx.lineTo(ex2,ey2); ctx.stroke(); arrowHead(ex2,ey2,sx2,sy2); label("琼崖远恶",sx2+(ex2-sx2)*0.5,sy2+(ey2-sy2)*0.5,-75,-32,32);}
            else if(dynasty==='yuan'){ let sx=0.59*w,sy=0.64*h,ex=0.82*w,ey=0.36*h; ctx.beginPath(); ctx.moveTo(sx,sy); ctx.lineTo(ex,ey); ctx.stroke(); arrowHead(ex,ey,sx,sy); label("南人北流",sx+(ex-sx)*0.55,sy+(ey-sy)*0.55,-80,-40,32); let sx2=0.72*w,sy2=0.74*h,ex2=0.57*w,ey2=0.80*h; ctx.beginPath(); ctx.moveTo(sx2,sy2); ctx.lineTo(ex2,ey2); ctx.stroke(); arrowHead(ex2,ey2,sx2,sy2); label("北人南流",sx2+(ex2-sx2)*0.5,sy2+(ey2-sy2)*0.5,-75,-35,32);}
            else if(dynasty==='ming'){ let sx=0.62*w,sy=0.62*h,ex=0.84*w,ey=0.40*h; ctx.beginPath(); ctx.moveTo(sx,sy); ctx.lineTo(ex,ey); ctx.stroke(); arrowHead(ex,ey,sx,sy); label("北人极边",sx+(ex-sx)*0.55,sy+(ey-sy)*0.55,-80,-40,32); let sx2=0.65*w,sy2=0.74*h,ex2=0.50*w,ey2=0.44*h; ctx.beginPath(); ctx.moveTo(sx2,sy2); ctx.lineTo(ex2,ey2); ctx.stroke(); arrowHead(ex2,ey2,sx2,sy2); label("南人烟瘴",sx2+(ex2-sx2)*0.5,sy2+(ey2-sy2)*0.5,-75,-35,32);}
            else if(dynasty==='qing'){ let sx=0.84*w,sy=0.36*h,ex=0.40*w,ey=0.44*h; ctx.beginPath(); ctx.moveTo(sx,sy); ctx.lineTo(ex,ey); ctx.stroke(); arrowHead(ex,ey,sx,sy); label("重心西移",sx+(ex-sx)*0.6,sy+(ey-sy)*0.6,-105,-44,36); let sx2=0.87*w,sy2=0.30*h,ex2=0.34*w,ey2=0.40*h; ctx.beginPath(); ctx.moveTo(sx2,sy2); ctx.lineTo(ex2,ey2); ctx.stroke(); arrowHead(ex2,ey2,sx2,sy2); label("宁古塔→伊犁",sx2+(ex2-sx2)*0.65,sy2+(ey2-sy2)*0.65,-125,-48,36);}
            ctx.restore();
        }
        
        // 修正总结页：基于真实地理坐标的重心迁移路径 (相对位置)
        function drawSummaryVisual(ctx, w, h) {
            ctx.font = "bold 38px 'Source Serif 4'"; ctx.fillStyle = "#ac7656"; ctx.fillText("📖 流放重心千年变迁路线图", 50, 80);
            // 定义五个阶段的重心坐标 (基于中国版图相对位置：岭南、山东/海岛、辽阳、甘肃/辽东、新疆)
            const stages = [
                { dynasty:"唐", label:"岭南道 (广州/岭南)", x:0.72, y:0.82, color:"#b34e4e" },
                { dynasty:"宋", label:"沙门岛 / 海南", x:0.86, y:0.47, color:"#b34e4e" },  // 沙门岛(山东) + 海南示意点
                { dynasty:"元", label:"辽阳行省", x:0.84, y:0.36, color:"#e09d32" },
                { dynasty:"明", label:"甘肃镇 / 辽东", x:0.48, y:0.42, color:"#3a6b4b" },   // 西北边卫
                { dynasty:"清", label:"伊犁 / 乌鲁木齐", x:0.36, y:0.44, color:"#3a6b4b" }
            ];
            // 绘制迁移连线
            ctx.beginPath(); ctx.moveTo(stages[0].x*w, stages[0].y*h);
            for(let i=1;i<stages.length;i++) ctx.lineTo(stages[i].x*w, stages[i].y*h);
            ctx.strokeStyle = "#f39c12"; ctx.lineWidth = 5; ctx.setLineDash([12, 8]); ctx.stroke(); ctx.setLineDash([]);
            // 绘制各阶段点及标签
            for(let s of stages){
                ctx.beginPath(); ctx.arc(s.x*w, s.y*h, 20, 0, 2*Math.PI);
                ctx.fillStyle = s.color; ctx.fill(); ctx.strokeStyle = "#fef5e6"; ctx.lineWidth = 2.5; ctx.stroke();
                ctx.fillStyle = "#2c2418"; ctx.font = "bold 26px 'Inter'"; ctx.fillText(s.dynasty, s.x*w+22, s.y*h-12);
                ctx.font = "20px 'Inter'"; ctx.fillStyle = "#5d4532"; ctx.fillText(s.label, s.x*w+24, s.y*h+12);
            }
            // 增加海南辅助点 (宋代另一个重心)
            ctx.beginPath(); ctx.arc(0.68*w, 0.88*h, 16, 0, 2*Math.PI);
            ctx.fillStyle = "#b34e4e"; ctx.fill(); ctx.fillStyle = "#2c2418"; ctx.font = "18px 'Inter'"; ctx.fillText("海南", 0.68*w+18, 0.88*h);
            ctx.beginPath(); ctx.moveTo(0.86*w, 0.47*h); ctx.lineTo(0.68*w, 0.88*h); ctx.strokeStyle = "#e09d32"; ctx.lineWidth = 2.5; ctx.setLineDash([6, 6]); ctx.stroke(); ctx.setLineDash([]);
            // 添加说明文字
            ctx.font = "22px 'Inter'"; ctx.fillStyle = "#6b5a48"; ctx.fillText("⛰️ 重心转移轨迹：岭南 → 山东/海岛 → 辽阳 → 西北边卫 → 新疆", 80, h-60);
        }
        
        function drawTitle(ctx, section, w, h) {
            if(section === 'summary') return;
            const titleMap = { tang:"唐代（开元二十九年·741年）", song:"北宋（政和元年·1111年）", yuan:"元代（至顺元年·1330年）", ming:"明代（万历十年·1582年）", qing:"清代（嘉庆二十五年·1820年）" };
            ctx.font = "bold 40px 'Source Serif 4'"; ctx.fillStyle = "#ac7656"; ctx.fillText(`◉ ${titleMap[section]} 疆域与流放格局`, 45, 78);
        }
        
        function updateLegend(section) {
            if(section === 'summary') {
                legendContainer.innerHTML = `
                    <span><span style="display:inline-block; width:14px; height:14px; background:#b34e4e; border-radius:50%;"></span>唐宋核心区</span>
                    <span><span style="display:inline-block; width:14px; height:14px; background:#e09d32; border-radius:50%;"></span>元明过渡区</span>
                    <span><span style="display:inline-block; width:14px; height:14px; background:#3a6b4b; border-radius:50%;"></span>明清西北区</span>
                    <span><span style="display:inline-block; width:32px; height:4px; background:#f39c12; vertical-align:middle;"></span>重心迁移路径</span>
                `;
            } else {
                legendContainer.innerHTML = `
                    <span><span style="display:inline-block; width:14px; height:14px; background:#b34e4e; border-radius:50%;"></span>核心流放区</span>
                    <span><span style="display:inline-block; width:14px; height:14px; background:#e09d32; border-radius:50%;"></span>次要/特殊区</span>
                    <span><span style="display:inline-block; width:14px; height:14px; background:#3a6b4b; border-radius:50%;"></span>戍边实边</span>
                    <span><span style="display:inline-block; width:32px; height:4px; background:#f39c12; vertical-align:middle;"></span>流放重心转移</span>
                `;
            }
        }
        
        function renderSection(section) {
            const w = canvas.width, h = canvas.height;
            ctx.clearRect(0, 0, w, h);
            drawUnifiedBackground(ctx, w, h);
            if(section === 'summary') {
                drawSummaryVisual(ctx, w, h);
                currentPoints = [];
            } else {
                drawOutline(ctx, section, w, h);
                drawArrows(ctx, section, w, h);
                currentPoints = pointsConfig[section].map(p => ({...p}));
                drawMarkers(ctx, currentPoints, w, h);
                drawTitle(ctx, section, w, h);
            }
        }
        
        function updateInfoPanel(section) {
            const data = infoData[section];
            if(!data) return;
            const html = `<div class="era-title">${data.title} <span class="era-sub">${data.sub}</span></div>
                          <div class="stat-badge-group">${data.stats.map(s=>`<span class="stat-pill">${s}</span>`).join('')}</div>
                          <div class="place-tags">${data.tags.map(t=>`<span class="place-tag">${t}</span>`).join('')}</div>
                          <div class="insight-text">${data.insight}<br><span class="driver-note">${data.driver}</span></div>
                          <div class="small-note">${data.note}</div>`;
            document.getElementById('infoPanel').innerHTML = html;
            const nameMap = { tang:"唐代 · 瘴疠南荒", song:"宋代 · 海岛沙门", yuan:"元代 · 南北对流", ming:"明代 · 边卫互徙", qing:"清代 · 西域极戍", summary:"📌 总结 · 流放重心千年迁移动态" };
            document.getElementById('dynastyNameLabel').innerText = nameMap[section];
            resetHighlight();
            updateLegend(section);
        }
        
        function highlightPanel(tagMatch,statMatch) {
            document.querySelectorAll('.place-tag').forEach(el=>el.classList.remove('highlight'));
            document.querySelectorAll('.stat-pill').forEach(el=>el.classList.remove('highlight'));
            if(tagMatch) document.querySelectorAll('.place-tag').forEach(el=>{ if(el.innerText.includes(tagMatch)) el.classList.add('highlight'); });
            if(statMatch) document.querySelectorAll('.stat-pill').forEach(el=>{ if(el.innerText.includes(statMatch)) el.classList.add('highlight'); });
        }
        function resetHighlight(){ document.querySelectorAll('.place-tag, .stat-pill').forEach(el=>el.classList.remove('highlight')); }
        
        function handleCanvasHover(e) {
            if(sections[currentIndex] === 'summary') { tooltip.style.display = 'none'; return; }
            const rect = canvas.getBoundingClientRect();
            const scaleX = canvas.width/rect.width, scaleY = canvas.height/rect.height;
            const mouseX = (e.clientX - rect.left)*scaleX, mouseY = (e.clientY - rect.top)*scaleY;
            let hovered = null;
            for(let p of currentPoints){
                const px = p.x*canvas.width, py = p.y*canvas.height;
                if(Math.hypot(mouseX-px, mouseY-py) < 26) { hovered = p; break; }
            }
            if(hovered){
                tooltip.style.display='block'; let leftPos = e.clientX - rect.left + 20, topPos = e.clientY - rect.top - 80;
                if(leftPos+300>rect.width) leftPos = rect.width-310;
                if(topPos<10) topPos = e.clientY - rect.top+20;
                tooltip.style.left = leftPos+'px'; tooltip.style.top = topPos+'px';
                tooltip.innerHTML = `<strong>📍 ${hovered.name}</strong><br>${hovered.desc||'流放要地'}<div class="quote">“${hovered.quote}”</div><div class="source">— ${hovered.source}</div>`;
                highlightPanel(hovered.tagMatch, hovered.statMatch);
            } else { tooltip.style.display='none'; resetHighlight(); }
        }
        
        function setSection(index) {
            if(index<0) index = sections.length-1;
            if(index>=sections.length) index=0;
            currentIndex = index;
            renderSection(sections[currentIndex]);
            updateInfoPanel(sections[currentIndex]);
            tooltip.style.display='none';
            resetHighlight();
            document.querySelectorAll('.dynasty-quick-btn').forEach((btn, i) => {
                if(i === currentIndex) btn.classList.add('active');
                else btn.classList.remove('active');
            });
        }
        
        // 快速导航
        const quickContainer = document.getElementById('quickNavContainer');
        const displayNames = { tang:"唐", song:"宋", yuan:"元", ming:"明", qing:"清", summary:"📖 总览" };
        sections.forEach((sec, idx) => {
            const btn = document.createElement('button');
            btn.className = 'dynasty-quick-btn';
            btn.innerText = displayNames[sec];
            btn.addEventListener('click', () => setSection(idx));
            quickContainer.appendChild(btn);
        });
        
        canvas.addEventListener('mousemove', handleCanvasHover);
        canvas.addEventListener('mouseleave', () => { tooltip.style.display='none'; resetHighlight(); });
        document.getElementById('nextBtn').addEventListener('click', () => setSection(currentIndex+1));
        document.getElementById('prevBtn').addEventListener('click', () => setSection(currentIndex-1));
        setSection(0);
        window.addEventListener('resize', () => renderSection(sections[currentIndex]));
    }
</script>
</body>
</html>
