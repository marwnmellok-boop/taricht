<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لوحة معلومات الشبكة</title>
    <style>
        body { font-family: sans-serif; background: #f4f4f9; display: flex; justify-content: center; padding: 20px; }
        .card { background: white; padding: 2rem; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); width: 100%; max-width: 400px; }
        h2 { color: #333; text-align: center; }
        .stat { margin-bottom: 15px; padding: 10px; border-bottom: 1px solid #eee; display: flex; justify-content: space-between; }
        .btn { width: 100%; padding: 10px; background: #007bff; color: white; border: none; border-radius: 5px; cursor: pointer; }
        .btn:hover { background: #0056b3; }
    </style>
</head>
<body>

<div class="card">
    <h2>زيادة سرعة الإنترنت</h2>
    
    <div class="stat">
        <span>🔋 مستوى البطارية:</span>
        <span id="battery-level">جاري الفحص...</span>
    </div>

    <div class="stat">
        <span>📶 نوع الشبكة:</span>
        <span id="network-type">جاري الفحص...</span>
    </div>

    <div class="stat">
        <span>🚀 سرعة التحميل:</span>
        <span id="speed-result">اضغط للفحص</span>
    </div>

    <button class="btn" onclick="checkSpeed()">ابدأ فحص السرعة</button>
    
    <p style="font-size: 0.8rem; color: #666; margin-top: 15px;">
        * ملاحظة: المتصفح لا يسمح بالوصول لتاريخ المواقع أو استهلاك التطبيقات الأخرى لخصوصيتك.
    </p>
</div>

<script>
    // 1. فحص البطارية
    if ('getBattery' in navigator) {
        navigator.getBattery().then(function(battery) {
            function updateBattery() {
                document.getElementById('battery-level').innerText = (battery.level * 100) + '%';
            }
            updateBattery();
            battery.addEventListener('levelchange', updateBattery);
        });
    }

    // 2. فحص نوع الشبكة
    const connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
    if (connection) {
        document.getElementById('network-type').innerText = connection.effectiveType.toUpperCase();
    }

    // 3. فحص السرعة (طريقة تقريبية عبر تحميل صورة)
    function checkSpeed() {
        const speedDisplay = document.getElementById('speed-result');
        speedDisplay.innerText = "جاري القياس...";
        
        const imageAddr = "https://upload.wikimedia.org/wikipedia/commons/3/3a/Cat03.jpg?n=" + Math.random();
        const startTime = new Date().getTime();
        const download = new Image();

        download.onload = function () {
            const endTime = new Date().getTime();
            const duration = (endTime - startTime) / 1000;
            const bitsLoaded = 4995374 * 8; // حجم الصورة الافتراضي بالبِت
            const speedBps = bitsLoaded / duration;
            const speedMbps = (speedBps / (1024 * 1024)).toFixed(2);
            speedDisplay.innerText = speedMbps + " Mbps";
        };
        download.src = imageAddr;
    }
</script>

</body>
</html>
