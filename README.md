<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>مسرع الإنترنت الذكي - العضوية المميزة</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary-color: #2980b9;
            --secondary-color: #2c3e50;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #333;
            --success-color: #27ae60;
            --danger-color: #e74c3c;
            --orange-accent: #f39c12;
        }

        /* --- تنسيق منشور النظام --- */
        .system-post {
            background: linear-gradient(135deg, #fffcf0 0%, #fff4e0 100%);
            border: 2px solid var(--orange-accent);
            border-radius: 15px;
            padding: 15px;
            margin-bottom: 25px;
            position: relative;
            box-shadow: 0 4px 15px rgba(243, 156, 18, 0.1);
            animation: slideDown 0.8s ease-out;
        }

        @keyframes slideDown {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .post-header {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 10px;
            border-bottom: 1px solid rgba(243, 156, 18, 0.2);
            padding-bottom: 8px;
        }

        .admin-badge {
            background: var(--orange-accent);
            color: white;
            font-size: 0.7rem;
            padding: 2px 8px;
            border-radius: 20px;
            font-weight: bold;
        }

        .post-content {
            font-size: 0.95rem;
            line-height: 1.6;
            color: var(--secondary-color);
            text-align: right;
        }

        .contact-btn {
            display: inline-block;
            margin-top: 10px;
            color: #fff;
            background: #25d366; /* لون الواتساب */
            padding: 5px 15px;
            border-radius: 8px;
            text-decoration: none;
            font-weight: bold;
            font-size: 0.85rem;
            transition: 0.3s;
        }

        .contact-btn:hover { background: #128c7e; }

        /* --- تنسيقات الإشعارات والواجهة --- */
        .header-actions { display: flex; justify-content: flex-end; margin-bottom: 10px; position: relative; }
        .notification-bell { position: relative; cursor: pointer; font-size: 1.5rem; color: var(--secondary-color); }
        .bell-ring { animation: ring 2s ease-in-out infinite; transform-origin: top center; }
        @keyframes ring { 0%, 100% { transform: rotate(0); } 10%, 30%, 50%, 70%, 90% { transform: rotate(10deg); } 20%, 40%, 60%, 80% { transform: rotate(-10deg); } }
        .badge { position: absolute; top: -5px; right: -5px; background: var(--danger-color); color: white; border-radius: 50%; padding: 2px 6px; font-size: 0.7rem; border: 2px solid white; }
        
        .profile-popup { display: none; position: absolute; top: 40px; left: 0; background: white; border-radius: 15px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); width: 200px; padding: 20px; z-index: 100; border: 1px solid #eee; }
        .robot-icon { font-size: 3rem; color: var(--success-color); margin-bottom: 10px; }
        .owner-name { color: var(--orange-accent); font-weight: bold; }
        .stars-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; }
        .star { position: absolute; background: #f1c40f; clip-path: polygon(50% 0%, 61% 35%, 98% 35%, 68% 57%, 79% 91%, 50% 70%, 21% 91%, 32% 57%, 2% 35%, 39% 35%); width: 8px; height: 8px; animation: twinkle 1.5s infinite; }
        @keyframes twinkle { 0%, 100% { opacity: 0; transform: scale(0.5); } 50% { opacity: 1; transform: scale(1.2); } }

        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: var(--bg-color); color: var(--text-color); display: flex; justify-content: center; align-items: center; min-height: 100vh; padding: 20px; }
        .container { background-color: var(--card-bg); padding: 30px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.15); width: 100%; max-width: 450px; text-align: center; }
        h1 { font-size: 1.4rem; margin-bottom: 20px; color: var(--secondary-color); }
        .gauge-container { position: relative; width: 180px; height: 90px; margin: 0 auto 25px auto; overflow: hidden; }
        .gauge-body { position: absolute; top: 0; left: 0; width: 100%; height: 200%; border-radius: 50%; background-color: #dfe6e9; border: 18px solid #eee; border-bottom-color: var(--primary-color); border-right-color: var(--primary-color); transform: rotate(45deg); transition: transform 0.8s; }
        #speed-text { font-size: 2.2rem; font-weight: bold; position: absolute; bottom: 65px; left: 50%; transform: translateX(-50%); }
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 15px; }
        .stat-card { background-color: #f9f9f9; padding: 12px; border-radius: 12px; border: 1px solid #eee; display: flex; align-items: center; gap: 8px; }
        .btn { width: 100%; padding: 15px; background: linear-gradient(135deg, var(--primary-color), #2980b9); color: white; border: none; border-radius: 12px; font-weight: bold; cursor: pointer; transition: 0.3s; }
        .scanning { animation: radar-pulse 1.2s infinite; border-radius: 50%; }
        @keyframes radar-pulse { 0% { box-shadow: 0 0 0 0 rgba(52, 152, 219, 0.4); } 70% { box-shadow: 0 0 0 20px rgba(52, 152, 219, 0); } }
    </style>
</head>
<body>

<div class="container">
    <div class="header-actions">
        <div class="notification-bell bell-ring" id="bell-container" onclick="toggleProfile()">
            <i class="fas fa-bell"></i>
            <span class="badge" id="notif-badge">1</span>
        </div>
        
        <div class="profile-popup" id="profile-popup">
            <div class="stars-container" id="stars-box"></div>
            <div class="profile-content">
                <i class="fab fa-android robot-icon"></i>
                <span class="owner-name" style="display:block">مروان ملوك</span>
                <small>صاحب الموقع الرسمي</small>
            </div>
        </div>
    </div>

    <div class="system-post">
        <div class="post-header">
            <i class="fas fa-user-shield" style="color: var(--orange-accent)"></i>
            <span style="font-weight: bold; font-size: 0.9rem;">إعلان من الإدارة</span>
            <span class="admin-badge">رسمي</span>
        </div>
        <div class="post-content">
            السلام عليكم ورحمة الله وبركاته، معكم صاحب الموقع. 
            إذا كنتم ترغبون في <strong>شراء عضوية مميزة</strong> في الموقع ثمنها <strong>5 آلاف درهم</strong>، يرجى مراسلتنا مباشرة.
            <br>
            <a href="tel:0679872832" class="contact-btn">
                <i class="fas fa-phone"></i> 0679872832
            </a>
        </div>
    </div>

    <h1><i class="fas fa-bolt" style="color:#f1c40f"></i> مسرع وسرعة الإنترنت</h1>
    
    <div class="gauge-container" id="gauge-zone">
        <div class="gauge-body" id="gauge-body"></div>
        <div class="gauge-cover" style="position: absolute; width: 80%; height: 160%; background: white; border-radius: 50%; top: 10%; left: 10%;">
            <span id="speed-text">0.0</span>
        </div>
    </div>

    <div class="stats-grid">
        <div class="stat-card" style="grid-column: span 2;">
            <i id="batt-icon" class="fas fa-battery-full"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">حالة الطاقة</div>
                <div id="battery-level" style="font-weight: bold;">--%</div>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-microchip"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">النظام</div>
                <div id="sys-status" style="font-weight: bold; font-size: 0.8rem;">مستقر</div>
            </div>
        </div>
        <div class="stat-card">
            <i class="fas fa-wifi"></i>
            <div style="text-align: right">
                <div style="font-size: 0.7rem; color: #7f8c8d;">الشبكة</div>
                <div id="network-type" style="font-weight: bold; font-size: 0.8rem;">--</div>
            </div>
        </div>
    </div>

    <button class="btn" id="start-btn" onclick="runSpeedBoost()">
        <i class="fas fa-rocket"></i> تحسين وزيادة السرعة الآن
    </button>
</div>

<script>
    // صوت الإشعارات
    const notificationSound = new Audio('https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3');

    window.addEventListener('DOMContentLoaded', () => {
        // إنشاء النجوم
        const starsBox = document.getElementById('stars-box');
        for(let i=0; i<15; i++) {
            const star = document.createElement('div');
            star.className = 'star';
            star.style.top = Math.random() * 100 + '%';
            star.style.left = Math.random() * 100 + '%';
            star.style.animationDelay = Math.random() * 2 + 's';
            starsBox.appendChild(star);
        }
        
        // تشغيل الصوت (يحتاج تفاعل مستخدم في بعض المتصفحات)
        setTimeout(() => {
            notificationSound.play().catch(() => {});
        }, 1200);
    });

    function toggleProfile() {
        const popup = document.getElementById('profile-popup');
        const badge = document.getElementById('notif-badge');
        const bell = document.getElementById('bell-container');
        popup.style.display = popup.style.display === 'block' ? 'none' : 'block';
        badge.style.display = 'none';
        bell.classList.remove('bell-ring');
    }

    // الأكواد الوظيفية
    function updateBattery(battery) {
        const level = Math.round(battery.level * 100);
        document.getElementById('battery-level').innerText = level + '%';
        const icon = document.getElementById('batt-icon');
        icon.style.color = level > 20 ? "#27ae60" : "#e74c3c";
    }

    if ('getBattery' in navigator) {
        navigator.getBattery().then(updateBattery);
    }

    function runSpeedBoost() {
        const btn = document.getElementById('start-btn');
        btn.disabled = true;
        btn.innerHTML = `<i class="fas fa-sync fa-spin"></i> جاري التحسين...`;
        document.getElementById('gauge-zone').classList.add('scanning');
        
        setTimeout(() => {
            const speed = (Math.random() * 50 + 20).toFixed(1);
            document.getElementById('speed-text').innerText = speed;
            const degree = 45 + (Math.min(speed, 100) / 100) * 180;
            document.getElementById('gauge-body').style.transform = `rotate(${degree}deg)`;
            
            btn.innerHTML = `<i class="fas fa-check-circle"></i> تم بنجاح`;
            document.getElementById('sys-status').innerText = "أداء مثالي";
            document.getElementById('gauge-zone').classList.remove('scanning');
        }, 2000);
    }
</script>

</body>
</html>
