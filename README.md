<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>رادار تاريشت - النسخة الاحترافية</title>
    
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database-compat.js"></script>
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

    <style>
        :root { --primary: #1e3c72; --accent: #2ecc71; --bg: #f8f9fa; }
        body { font-family: 'Segoe UI', sans-serif; background: var(--bg); display: flex; justify-content: center; padding: 20px; margin: 0; }
        .card { background: white; padding: 25px; border-radius: 25px; width: 100%; max-width: 400px; box-shadow: 0 15px 35px rgba(0,0,0,0.1); text-align: center; }
        
        .radar-circle { width: 80px; height: 80px; border: 3px solid var(--primary); border-radius: 50%; margin: 0 auto 20px; position: relative; display: flex; align-items: center; justify-content: center; animation: pulse 2s infinite; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(30, 60, 114, 0.4); } 100% { box-shadow: 0 0 0 20px rgba(30, 60, 114, 0); } }

        .user-list { background: #fff; border: 1px solid #eee; border-radius: 15px; margin: 20px 0; max-height: 200px; overflow-y: auto; text-align: right; }
        .user-item { padding: 12px 15px; border-bottom: 1px solid #f5f5f5; display: flex; justify-content: space-between; align-items: center; cursor: pointer; transition: 0.2s; }
        .user-item:hover { background: #eef5ff; }
        .user-item span { font-weight: bold; color: #333; }
        .connect-badge { background: var(--accent); color: white; padding: 4px 10px; border-radius: 8px; font-size: 11px; }

        input { width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #eee; border-radius: 12px; box-sizing: border-box; }
        button { background: var(--primary); color: white; border: none; padding: 12px; width: 100%; border-radius: 12px; cursor: pointer; font-weight: bold; }
        
        #chat-ui { display: none; border-top: 2px solid #eee; padding-top: 20px; }
        #messages { height: 180px; overflow-y: auto; background: #f9f9f9; padding: 10px; border-radius: 10px; margin-bottom: 10px; text-align: right; }
    </style>
</head>
<body>

<div class="card">
    <div class="radar-circle">📡</div>
    <h2 id="title">رادار تاريشت</h2>
    <p id="status-text" style="font-size: 13px; color: #666;">سجل اسمك لتظهر للأجهزة القريبة</p>

    <div id="login-section">
        <input type="text" id="username" placeholder="اكتب اسمك الحقيقي">
        <button onclick="startRadar()">تشغيل الرادار الآن</button>
    </div>

    <div id="radar-section" style="display:none;">
        <div id="userList" class="user-list">
            <p style="padding: 10px; font-size: 12px; color: #999;">جاري البحث عن متصلين...</p>
        </div>

        <div id="chat-ui">
            <p id="chatting-with" style="font-size: 12px; font-weight: bold;"></p>
            <div id="messages"></div>
            <div style="display: flex; gap: 5px;">
                <input type="text" id="msgInput" placeholder="اكتب رسالة..." style="margin:0;">
                <button onclick="sendMsg()" style="width: 80px;">إرسال</button>
            </div>
        </div>
    </div>
</div>

<script>
    // إعدادات Firebase (نسخة تجريبية عامة للتعلم)
    const firebaseConfig = {
        databaseURL: "https://tarisht-radar-default-rtdb.firebaseio.com/" 
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let peer, conn, myName;

    function startRadar() {
        myName = document.getElementById('username').value.trim();
        if(!myName) return alert("الرجاء إدخال الاسم");

        // 1. إعداد PeerJS للاتصال المباشر
        peer = new Peer(myName);

        peer.on('open', (id) => {
            document.getElementById('login-section').style.display = 'none';
            document.getElementById('radar-section').style.display = 'block';
            document.getElementById('status-text').innerText = "أنت الآن مرئي للجميع باسم: " + id;

            // 2. تسجيل الاسم في Firebase ليراك الآخرون
            const userRef = db.ref('online_users/' + id);
            userRef.set({ name: id, status: "online" });
            userRef.onDisconnect().remove(); // حذف الاسم تلقائياً عند إغلاق المتصفح

            listenForUsers();
        });

        peer.on('connection', (connection) => {
            conn = connection;
            setupChat();
        });
    }

    // 3. مراقبة قائمة المستخدمين في Firebase وتحديث القائمة تلقائياً
    function listenForUsers() {
        db.ref('online_users').on('value', (snapshot) => {
            const users = snapshot.val();
            const listDiv = document.getElementById('userList');
            listDiv.innerHTML = "";

            for (let id in users) {
                if (id === myName) continue; // لا تظهر اسمك لنفسك
                
                const item = document.createElement('div');
                item.className = 'user-item';
                item.innerHTML = `<span>👤 ${id}</span> <div class="connect-badge">طلب مراسلة</div>`;
                item.onclick = () => connectTo(id);
                listDiv.appendChild(item);
            }
            if (listDiv.innerHTML === "") listDiv.innerHTML = '<p style="padding:10px; font-size:12px;">لا يوجد أحد قريب حالياً...</p>';
        });
    }

    function connectTo(targetId) {
        conn = peer.connect(targetId);
        setupChat();
    }

    function setupChat() {
        document.getElementById('chat-ui').style.display = 'block';
        document.getElementById('chatting-with').innerText = "دردشة مع: " + conn.peer;
        
        conn.on('data', (data) => {
            appendMessage(conn.peer, data, 'blue');
        });
    }

    function sendMsg() {
        const input = document.getElementById('msgInput');
        const text = input.value;
        if(!text) return;
        
        conn.send(text);
        appendMessage("أنا", text, 'green');
        input.value = "";
    }

    function appendMessage(sender, text, color) {
        const msgDiv = document.getElementById('messages');
        msgDiv.innerHTML += `<div><b style="color:${color}">${sender}:</b> ${text}</div>`;
        msgDiv.scrollTop = msgDiv.scrollHeight;
    }
</script>

</body>
</html>
