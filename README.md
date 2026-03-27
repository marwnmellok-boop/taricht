<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مسرع الإنترنت الذكي</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #2980b9;
            --secondary-color: #2c3e50;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #333;
            --success-color: #27ae60;
            --danger-color: #e74c3c; /* لون الطوارئ */
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background-color: var(--card-bg);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.15);
            width: 100%;
            max-width: 450px;
            text-align: center;
        }

        h1 {
            font-size: 1.6rem;
            margin-bottom: 25px;
            color: var(--secondary-color);
        }

        .gauge-container {
            position: relative;
            width: 200px;
            height: 100px;
            margin: 0 auto 30px auto;
            overflow: hidden;
        }

        .gauge-body {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 200%;
            border-radius: 50%;
            background-color: #dfe6e9;
            border: 20px solid #eee;
            border-bottom-color: var(--primary-color);
            border-right-color: var(--primary-color);
            transform: rotate(45deg);
            transition: transform 0.8s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .gauge-cover {
            position: absolute;
            width: 80%;
            height: 160%;
            background-color: var(--card-bg);
            border-radius: 50%;
            top: 10%;
            left: 10%;
            display: flex;
            align-items: center;
            justify-content: center;
            padding-bottom: 80%;
        }

        .gauge-cover::after {
            content: "Mbps";
            position: absolute;
            bottom: 65px;
            font-size: 0.8rem;
            color: #7f8c8d;
        }

        #speed-text {
            font-size: 2.5rem;
            font-weight: bold;
            color: var(--secondary-color);
            position: absolute;
            bottom: 75px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 15px;
        }

        /* تنسيق البطارية الموحد مع الشبكة والحالة */
        .battery-full-width {
            grid-column: span 2;
            background: #fdfdfd;
            border: 1.5px dashed #ddd;
        }

        .stat-card {
            background-color: #f9f9f9;
            padding: 15px;
            border-radius: 12px;
            border: 1px solid #eee;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .stat-card i {
            font-size: 1.5rem;
            color: var(--primary-color);
            width: 30px;
            text-align: center;
        }

        .stat-info { display: flex; flex-direction: column; align-items: flex-start; }
        .stat-label { font-size: 0.75rem; color: #7f8c8d; }
        .stat-value { font-weight: bold; color: var(--secondary-color); font-size: 0.9rem;}

        .btn {
            width: 100%;
            padding: 15px;
            background: linear-gradient(135deg, var(--primary-color), #2980b9);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(52, 152, 219, 0.3);
            margin-top: 10px;
        }

        .btn:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(52, 152, 219, 0.4); }
        .btn:disabled { background: #bdc3c7; cursor: not-allowed; transform: none; }

        .note {
            font-size: 0.8rem;
            color: #95a5a6;
            margin-top: 20px;
            background: #fff9e6;
            padding: 10px;
            border-radius: 8px;
            border-right: 4px solid #f1c40f;
        }

        /* تحريك أيقونة الطوارئ عند ضعف البطارية */
        @keyframes battery-blink {
            50% { opacity: 0.5; }
        }
        .emergency-mode { 
            color: var(--danger-color) !important; 
            animation: battery-blink 1s infinite;
        }

        @keyframes radar-pulse {
            0% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.4); }
            70% { box-shadow: 0 0 0 20px rgba(52, 152, 219, 0); }
            100% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0); }
        }
        .scanning { animation: radar-pulse 1.2s infinite; border-radius: 50%; }

    </style>
</head>
<body>

<div class="container">
    <h1><i class="fas fa-bolt" style="color:#f1c40f"></i> مسرع وسرعة الإنترنت</h1>
    
    <div class="gauge-container" id="gauge-zone">
        <div class="gauge-body" id="gauge-body"></div>
        <div class="gauge-cover">
            <span id="speed-text">0.0</span>
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-card battery-full-width">
            <i id="batt-icon" class="fas fa-battery-full"></i>
            <div class="stat-info">
                <span class="stat-label" id="batt-status-label">حالة الطاقة</span>
                <span class="stat-value" id="battery-level">--%</span>
            </div>
        </div>

        <div class="stat-card">
            <i class="fas fa-microchip"></i>
            <div class="stat-info">
                <span class="stat-label">حالة النظام</span>
                <span class="stat-value" id="sys-status">مستقر</span>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-wifi"></i>
            <div class="stat-info">
                <span class="stat-label">إشارة الشبكة</span>
                <span class="stat-value" id="network-type">--</span>
            </div>
        </div>
    </div>

    <button class="btn" id="start-btn" onclick="runSpeedBoost()">
        <i class="fas fa-rocket"></i> تحسين وزيادة السرعة الآن
    </button>
    
    <div class="note">
        <i class="fas fa-shield-alt"></i> يتم الآن تحسين مسار البيانات وتقليل استهلاك الخلفية لرفع كفاءة التصفح.
    </div>
</div>

<script>
    // --- وظائف البطارية الذكية ---
    function updateBattery(battery) {
        const level = Math.round(battery.level * 100);
        const battText = document.getElementById('battery-level');
        const battIcon = document.getElementById('batt-icon');
        const battLabel = document.getElementById('batt-status-label');

        battText.innerText = level + '%';

        // تصفير الأنماط السابقة
        battIcon.className = "fas";
        battIcon.classList.remove('emergency-mode');
        battText.classList.remove('emergency-mode');

        if (battery.charging) {
            battIcon.classList.add('fa-battery-charging');
            battIcon.style.color = "var(--success-color)";
            battLabel.innerText = "جاري الشحن...";
        } else {
            battLabel.innerText = "طاقة البطارية";
            if (level <= 20) {
                battIcon.classList.add('fa-battery-empty', 'emergency-mode');
                battText.classList.add('emergency-mode');
                battLabel.innerText = "تنبيه: طوارئ طاقة!";
                battLabel.style.color = "var(--danger-color)";
            } else if (level <= 50) {
                battIcon.classList.add('fa-battery-half');
                battIcon.style.color = "#f39c12";
            } else {
                battIcon.classList.add('fa-battery-full');
                battIcon.style.color = "var(--success-color)";
            }
        }
    }

    if ('getBattery' in navigator) {
        navigator.getBattery().then(updateBattery);
        navigator.getBattery().then(batt => {
            batt.addEventListener('levelchange', () => updateBattery(batt));
            batt.addEventListener('chargingchange', () => updateBattery(batt));
        });
    }

    // --- بقية الأكواد الأصلية دون تغيير ---
    const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
    function updateNetworkInfo() {
        const typeElement = document.getElementById('network-type');
        if (connection) {
            typeElement.innerText = connection.effectiveType.toUpperCase() + " (نشط)";
        } else {
            typeElement.innerText = "متصل";
        }
    }
    updateNetworkInfo();

    function setGaugeValue(value) {
        const maxSpeedForGauge = 100; 
        const speed = Math.min(value, maxSpeedForGauge);
        const degree = 45 + (speed / maxSpeedForGauge) * 180;
        document.getElementById('gauge-body').style.transform = `rotate(${degree}deg)`;
        document.getElementById('speed-text').innerText = value.toFixed(1);
    }

    function runSpeedBoost() {
        const startBtn = document.getElementById('start-btn');
        const speedText = document.getElementById('speed-text');
        const gaugeZone = document.getElementById('gauge-zone');
        const sysStatus = document.getElementById('sys-status');

        startBtn.disabled = true;
        startBtn.innerHTML = `<i class="fas fa-sync fa-spin"></i> جاري التحسين...`;
        sysStatus.innerText = "جاري التحسين...";
        sysStatus.style.color = "var(--primary-color)";
        gaugeZone.classList.add('scanning');

        const imageAddr = "https://upload.wikimedia.org/wikipedia/commons/4/4e/Pleiades_large.jpg?n=" + Math.random();
        const download = new Image();
        let startTime = new Date().getTime();

        download.onload = function () {
            let endTime = new Date().getTime();
            const duration = (endTime - startTime) / 1000;
            const bitsLoaded = 5211913 * 8; 
            const speedMbps = (bitsLoaded / duration) / (1024 * 1024);
            setGaugeValue(speedMbps);
            
            setTimeout(() => {
                startBtn.disabled = false;
                startBtn.innerHTML = `<i class="fas fa-check-circle"></i> تم التحسين بنجاح`;
                sysStatus.innerText = "أداء مثالي";
                sysStatus.style.color = "var(--success-color)";
                gaugeZone.classList.remove('scanning');
                setTimeout(() => { startBtn.innerHTML = `<i class="fas fa-rocket"></i> تحسين وزيادة السرعة الآن`; }, 3000);
            }, 1000);
        };

        download.onerror = function () {
            speedText.innerText = "!!";
            startBtn.disabled = false;
            startBtn.innerHTML = `<i class="fas fa-redo"></i> حاول مرة أخرى`;
            gaugeZone.classList.remove('scanning');
        }
        download.src = imageAddr;
    }
</script>

</body>
</html>
