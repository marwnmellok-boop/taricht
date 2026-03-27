<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار تاريشت - تتبع اللاعبين 🛰️</title>
    
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>

    <style>
        :root { --primary: #1e3c72; --bg: #f4f7f6; }
        body, html { margin: 0; padding: 0; height: 100%; font-family: 'Segoe UI', sans-serif; background: var(--bg); }
        
        #map { height: 60vh; width: 100%; border-bottom: 5px solid var(--primary); }
        
        .control-panel { padding: 20px; background: white; border-radius: 25px 25px 0 0; margin-top: -20px; position: relative; z-index: 1000; box-shadow: 0 -10px 20px rgba(0,0,0,0.1); height: 40vh; overflow-y: auto; }
        
        input { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #eee; border-radius: 12px; box-sizing: border-box; }
        button { background: var(--primary); color: white; border: none; padding: 15px; width: 100%; border-radius: 12px; cursor: pointer; font-weight: bold; font-size: 16px; }
        
        .player-card { display: flex; justify-content: space-between; padding: 10px; border-bottom: 1px solid #eee; font-size: 14px; }
        .distance { color: #ff4757; font-weight: bold; }
    </style>
</head>
<body>

<div id="map"></div>

<div class="control-panel">
    <div id="login-screen">
        <h3>رادار نادي تاريشت 📡</h3>
        <p style="font-size: 13px; color: #666;">سيظهر موقعك للاعبين الآخرين في النادي</p>
        <input type="text" id="playerName" placeholder="ادخل اسمك كلاعب">
        <button onclick="joinRadar()">دخول الرادار</button>
    </div>

    <div id="radar-screen" style="display:none;">
        <h4>اللاعبون القريبون منك:</h4>
        <div id="playersList"></div>
    </div>
</div>

<script>
    // إعداد Firebase (قاعدة بيانات لحظية)
    const firebaseConfig = { databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let map, myMarker, playersMarkers = {};
    let myId = "";

    // تهيئة الخريطة (تبدأ بموقع افتراضي حتى نحصل على GPS)
    map = L.map('map').setView([33.5731, -7.5898], 13); // إحداثيات الدار البيضاء كمثال
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

    function joinRadar() {
        myId = document.getElementById('playerName').value.trim();
        if(!myId) return alert("يرجى كتابة الاسم");

        if (navigator.geolocation) {
            // تتبع الموقع بشكل حي ومستمر
            navigator.geolocation.watchPosition(updateMyLocation, handleError, {
                enableHighAccuracy: true
            });
            
            document.getElementById('login-screen').style.display = 'none';
            document.getElementById('radar-screen').style.display = 'block';
            
            listenForPlayers();
        } else {
            alert("متصفحك لا يدعم نظام تحديد المواقع GPS");
        }
    }

    function updateMyLocation(position) {
        const lat = position.coords.latitude;
        const lng = position.coords.longitude;

        // تحديث الخريطة الخاصة بي
        if (!myMarker) {
            myMarker = L.marker([lat, lng]).addTo(map).bindPopup("أنا (" + myId + ")").openPopup();
            map.setView([lat, lng], 15);
        } else {
            myMarker.setLatLng([lat, lng]);
        }

        // إرسال الموقع لـ Firebase ليراه الآخرون
        db.ref('players/' + myId).set({
            name: myId,
            lat: lat,
            lng: lng,
            lastSeen: Date.now()
        });
        
        // حذف اللاعب عند الخروج
        db.ref('players/' + myId).onDisconnect().remove();
    }

    function listenForPlayers() {
        db.ref('players').on('value', snapshot => {
            const players = snapshot.val();
            const listDiv = document.getElementById('playersList');
            listDiv.innerHTML = "";

            for (let id in players) {
                if (id === myId) continue;

                const p = players[id];
                
                // تحديث العلامات على الخريطة
                if (playersMarkers[id]) {
                    playersMarkers[id].setLatLng([p.lat, p.lng]);
                } else {
                    playersMarkers[id] = L.marker([p.lat, p.lng], {
                        icon: L.divIcon({className: 'other-player', html: '📍', iconSize: [20, 20]})
                    }).addTo(map).bindPopup(p.name);
                }

                // حساب المسافة وعرضها في القائمة
                const dist = calculateDistance(myMarker.getLatLng().lat, myMarker.getLatLng().lng, p.lat, p.lng);
                listDiv.innerHTML += `
                    <div class="player-card">
                        <span>👤 ${p.name}</span>
                        <span class="distance">يبعد ${dist.toFixed(2)} كم</span>
                    </div>
                `;
            }
        });
    }

    function calculateDistance(lat1, lon1, lat2, lon2) {
        const R = 6371; 
        const dLat = (lat2-lat1) * Math.PI / 180;
        const dLon = (lon2-lon1) * Math.PI / 180;
        const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
                  Math.cos(lat1*Math.PI/180) * Math.cos(lat2*Math.PI/180) * Math.sin(dLon/2) * Math.sin(dLon/2);
        const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
        return R * c;
    }

    function handleError(err) {
        alert("خطأ: يرجى تفعيل الموقع (GPS) وتجربة الدخول عبر رابط https");
    }
</script>

</body>
</html>
