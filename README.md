<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار تاريشت - خريطة الأفاتار 🗺️</title>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        :root { --primary: #1e3c72; --accent: #00d2ff; }
        body, html { margin: 0; padding: 0; height: 100%; font-family: 'Segoe UI', sans-serif; overflow: hidden; }
        
        #map { height: 100vh; width: 100%; position: absolute; top: 0; left: 0; z-index: 1; }
        
        /* واجهة التحكم العائمة */
        .overlay-ui {
            position: absolute; bottom: 20px; left: 50%; transform: translateX(-50%);
            z-index: 2000; width: 90%; max-width: 380px;
            background: rgba(255, 255, 255, 0.95); padding: 20px;
            border-radius: 25px; box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            text-align: center; backdrop-filter: blur(10px);
        }

        /* تصميم الأفاتار على الخريطة */
        .avatar-marker {
            background: white; border: 3px solid var(--primary);
            border-radius: 50%; overflow: hidden; width: 45px !important; height: 45px !important;
            box-shadow: 0 0 15px rgba(0,0,0,0.3);
        }
        .avatar-marker img { width: 100%; height: 100%; object-fit: cover; }

        input { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #ddd; border-radius: 15px; box-sizing: border-box; }
        button { background: var(--primary); color: white; border: none; padding: 15px; width: 100%; border-radius: 15px; cursor: pointer; font-weight: bold; font-size: 16px; }
        
        #status { font-size: 12px; color: #555; margin-bottom: 5px; }
    </style>
</head>
<body>

<div id="map"></div>

<div class="overlay-ui">
    <div id="login-screen">
        <h3>رادار تاريشت 📡</h3>
        <p id="status">الرجاء تفعيل الـ GPS والسماح بالوصول للموقع</p>
        <input type="text" id="username" placeholder="اكتب اسمك للمشاركة">
        <button onclick="joinRadar()">تفعيل الأفاتار على الخريطة</button>
    </div>

    <div id="radar-info" style="display:none;">
        <p>أنت متصل الآن كـ: <b id="display-name" style="color:var(--primary)"></b></p>
        <div id="nearby-count" style="font-size: 11px; color:green;">جاري البحث عن لاعبين قريبين...</div>
    </div>
</div>

<script>
    // إعدادات Firebase
    const firebaseConfig = { databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let map, myMarker, playersMarkers = {};
    let myName = "";
    let myAvatar = "";

    // إنشاء الخريطة
    map = L.map('map', { zoomControl: false }).setView([33.5731, -7.5898], 13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

    function joinRadar() {
        myName = document.getElementById('username').value.trim();
        if(!myName) return alert("يرجى كتابة اسمك");

        // توليد أفاتار عشوائي بناءً على الاسم من خدمة Adlee (مجانية)
        myAvatar = `https://api.dicebear.com/7.x/avataaars/svg?seed=${myName}`;

        if (navigator.geolocation) {
            navigator.geolocation.watchPosition(position => {
                const lat = position.coords.latitude;
                const lng = position.coords.longitude;

                updateMapAndFirebase(lat, lng);
            }, () => alert("فشل تحديد الموقع. تأكد من تفعيل GPS و HTTPS"), { enableHighAccuracy: true });

            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('radar-info').style.display = 'block';
            document.getElementById('display-name').innerText = myName;
            
            listenForOthers();
        }
    }

    function updateMapAndFirebase(lat, lng) {
        // 1. تحديث موقعي على خريطتي
        const icon = L.divIcon({
            className: 'avatar-marker',
            html: `<img src="${myAvatar}" alt="avatar">`,
            iconSize: [45, 45]
        });

        if (!myMarker) {
            myMarker = L.marker([lat, lng], { icon: icon }).addTo(map).bindPopup("أنت: " + myName).openPopup();
            map.setView([lat, lng], 16);
        } else {
            myMarker.setLatLng([lat, lng]);
        }

        // 2. إرسال البيانات لـ Firebase (الاسم، الموقع، الأفاتار)
        db.ref('players/' + myName).set({
            name: myName,
            lat: lat,
            lng: lng,
            avatar: myAvatar,
            time: Date.now()
        });
        db.ref('players/' + myName).onDisconnect().remove();
    }

    function listenForOthers() {
        db.ref('players').on('value', snapshot => {
            const players = snapshot.val();
            let count = 0;

            for (let id in players) {
                if (id === myName) continue;
                count++;
                const p = players[id];

                const otherIcon = L.divIcon({
                    className: 'avatar-marker',
                    html: `<img src="${p.avatar}" alt="avatar">`,
                    iconSize: [45, 45]
                });

                if (playersMarkers[id]) {
                    playersMarkers[id].setLatLng([p.lat, p.lng]);
                } else {
                    playersMarkers[id] = L.marker([p.lat, p.lng], { icon: otherIcon })
                        .addTo(map).bindPopup(p.name);
                }
            }
            document.getElementById('nearby-count').innerText = `يوجد ${count} لاعبين آخرين على الخريطة`;
        });
    }
</script>

</body>
</html>
