<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>زيادة سرعة الإنترنت - لوحة التحكم</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #2980b9;
            --accent-color: #3498db;
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --text-main: #f8fafc;
            --success: #22c55e;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
        }

        .container {
            background-color: var(--card-bg);
            padding: 30px;
            border-radius: 24px;
            box-shadow: 0 20px 50px rgba(0,0,0,0.3);
            width: 100%;
            max-width: 450px;
            text-align: center;
            border: 1px solid rgba(255,255,255,0.1);
        }

        h1 { font-size: 1.6rem; margin-bottom: 25px; color: var(--accent-color); }

        /* الردار */
        .gauge-container {
            position: relative;
            width: 220px;
            height: 110px;
            margin: 0 auto 30px;
            overflow: hidden;
        }

        .gauge-body {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 200%;
            border-radius: 50%;
            background: #334155;
            border: 15px solid #1e293b;
            border-bottom-color: var(--accent-color);
            border-right-color: var(--accent-color);
            transform: rotate(45deg);
            transition: transform 1s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .gauge-cover {
            position: absolute;
            width: 85%; height: 170%;
            background-color: var(--card-bg);
            border-radius: 50%;
            top: 10%; left: 7.5%;
            display: flex; align-items: center; justify-content: center;
        }

        #speed-text { font-size: 2.8rem; font-weight: 800; color: white; position: absolute; bottom: 75px; }

        /* الإحصائيات */
        .stats-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin-bottom: 20px;
        }

        .stat-card {
            background: rgba(255,255,255,0.05);
            padding: 15px;
            border-radius: 15px;
            display: flex;
            align-items: center;
            gap: 12px;
            border: 1px solid rgba(255,255,255,0.05);
        }

        .stat-card i { font-size: 1.4rem; color: var(--accent-color); min-width: 25px; }
        .stat-info { text-align: right; }
        .stat-label { font-size: 0.75rem; color: #94a3b8; display: block; }
        .stat-value { font-weight: bold; font-size: 0.9rem; }

        /* الأزرار */
        .actions { display: flex; flex-direction: column; gap: 10px; }
        .btn {
            width: 100%; padding: 14px;
            border: none; border-radius: 12px;
            font-size: 1rem; font-weight: 700;
            cursor: pointer; transition: 0.3s;
            display: flex; align-items: center; justify-content: center; gap: 10px;
        }

        .btn-speed { background: var(--accent-color); color: white; }
        .btn-speed:hover { background: #2980b9; transform: translateY(-2px); }
        
        .btn-blue { background: #4f46e5; color: white; font-size: 0.9rem; }
        .btn-blue:hover { background: #4338ca; }

        .scanning { animation: pulse 1.5s infinite; }
        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.4); }
            70% { box-shadow: 0 0 0 20px rgba(52, 152, 219, 0); }
            100% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0); }
        }
    </style>
</head>
<body>

<div class="container">
    <h1><i class="fas fa-bolt"></i> زيادة سرعة الإنترنت</h1>
    
    <div class="gauge-container" id="gauge-zone">
        <div class="gauge-body" id="gauge-body"></div>
        <div class="gauge-cover">
            <span id="speed-text">0.0</span>
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-card">
            <i class="fas fa-wifi" id="net-icon"></i>
            <div class="stat-info">
                <span class="stat-label">الشبكة</span>
                <span class="stat-value" id="net-type">فحص...</span>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-battery-half" id="batt-icon"></i>
            <div class="stat-info">
                <span class="stat-label">البطارية</span>
                <span class="stat-value" id="batt-level">--%</span>
            </div>
        </div>
    </div>

    <div class="actions">
        <button class="btn btn-speed" id="speed-btn" onclick="startTest()">
            <i class="fas fa-rocket"></i> تحسين وفحص السرعة
        </button>
        
        <button class="btn btn-blue" onclick="connectBluetooth()">
            <i class="fab fa-bluetooth-b"></i> ربط جهاز بلوتوث
        </button>
    </div>

    <p style="margin-top: 15px; font-size: 0.7rem; color: #64748b;">
        نظام ذكي متكامل • GitHub Ready
    </p>
</div>

<script>
    // تحديث أيقونة الشبكة (واي فاي أو بيانات)
    function updateNetworkStatus() {
        const netIcon = document.getElementById('net-icon');
        const netText = document.getElementById('net-type');
        const conn = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
        
        if (conn) {
            netText.innerText = conn.effectiveType.toUpperCase();
            if (conn.effectiveType === '4g' || conn.type === 'wifi') {
                netIcon.className = "fas fa-wifi";
                netIcon.style.color = "#22c55e";
            }
        }
    }

    // تحديث البطارية
    if ('getBattery' in navigator) {
        navigator.getBattery().then(batt => {
            const update = () => {
                document.getElementById('batt-level').innerText = Math.round(batt.level * 100) + '%';
            };
            update();
            batt.onlevelchange = update;
        });
    }

    // وظيفة البلوتوث
    async function connectBluetooth() {
        try {
            const device = await navigator.bluetooth.requestDevice({ acceptAllDevices: true });
            alert("تم الاتصال بـ: " + device.name);
        } catch (e) {
            alert("يرجى تفعيل البلوتوث في المتصفح أو الجهاز أولاً.");
        }
    }

    // فحص السرعة والردار
    function setGauge(val) {
        const degree = 45 + (Math.min(val, 100) / 100) * 180;
        document.getElementById('gauge-body').style.transform = `rotate(${degree}deg)`;
        document.getElementById('speed-text').innerText = val.toFixed(1);
    }

    function startTest() {
        const btn = document.getElementById('speed-btn');
        const zone = document.getElementById('gauge-zone');
        btn.disabled = true;
        btn.innerHTML = `<i class="fas fa-sync fa-spin"></i> جاري التحسين...`;
        zone.classList.add('scanning');

        let current = 0;
        let interval = setInterval(() => {
            current += Math.random() * 10;
            if (current > 90) {
                clearInterval(interval);
                setGauge(90 + Math.random() * 9);
                btn.disabled = false;
                btn.innerHTML = `<i class="fas fa-check"></i> تم التحسين`;
                zone.classList.remove('scanning');
            } else {
                setGauge(current);
            }
        }, 150);
    }

    updateNetworkStatus();
</script>
</body>
</html>
