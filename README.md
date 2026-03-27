<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - شبكة البلوتوث الآمنة</title>
    <style>
        :root {
            --primary: #1e3c72;
            --bt-blue: #0082fc;
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --sent-msg: #dcf8c6;
            --read-blue: #34b7f1;
            --danger: #e74c3c;
        }

        body, html { height: 100%; margin: 0; display: flex; justify-content: center; align-items: center; background: var(--bg-color); font-family: 'Segoe UI', sans-serif; }

        /* الرادار والأجهزة */
        .radar-section { padding: 15px; background: #f8f9fa; border-bottom: 1px solid #ddd; }
        .device-card { 
            display: flex; justify-content: space-between; align-items: center; 
            background: white; padding: 10px; margin-bottom: 5px; border-radius: 10px; border: 1px solid #eee;
        }
        .status-dot { width: 8px; height: 8px; background: #2ecc71; border-radius: 50%; display: inline-block; margin-left: 5px; }

        /* نافذة الشات */
        .chat-window {
            display: none; position: fixed; bottom: 20px; right: 20px;
            width: 360px; height: 550px; background: white; border-radius: 20px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2); flex-direction: column; z-index: 2000; overflow: hidden;
        }
        .chat-header { background: var(--bt-blue); color: white; padding: 15px; font-weight: bold; display: flex; justify-content: space-between; align-items: center; }
        .msg-list { flex: 1; padding: 10px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; background: #e5ddd5; }

        /* نظام الحظر */
        .admin-panel { font-size: 11px; background: #333; color: #fff; padding: 5px 10px; }
        .block-tag { color: var(--danger); cursor: pointer; font-weight: bold; }

        /* الرسائل وعلامات الصح */
        .msg { padding: 8px 12px; border-radius: 12px; font-size: 14px; max-width: 80%; position: relative; }
        .msg.sent { background: var(--sent-msg); align-self: flex-end; }
        .ticks { font-size: 10px; text-align: left; color: #999; }
        .ticks.read { color: var(--read-blue); }

        .chat-footer { display: flex; gap: 8px; padding: 10px; background: #f0f0f0; align-items: center; }
        .main-btn { background: var(--primary); color: white; border: none; padding: 10px; border-radius: 10px; cursor: pointer; font-weight: bold; }
        .btn-sm { padding: 5px 10px; font-size: 12px; border-radius: 5px; border: none; cursor: pointer; }
    </style>
</head>
<body onclick="startSystem()">

    <button class="main-btn" onclick="toggleWindow()" style="position:fixed; top:20px; right:20px;">فتح رادار تاريشت 📡</button>

    <div class="chat-window" id="mainWin">
        <div class="chat-header">
            <span>رادار تاريشت (Bluetooth v5.0)</span>
            <span onclick="toggleWindow()" style="cursor:pointer">×</span>
        </div>

        <div class="admin-panel" id="adminLog">
            جاري فحص الـ IPs النشطة... | <span id="ipCount">1</span> جهاز متصل
        </div>

        <div id="pairingArea" class="radar-section">
            <button class="main-btn" style="width:100%; margin-bottom:10px;" onclick="searchDevices()">بحث عن أجهزة قريبة 🔗</button>
            <div id="deviceList">
                </div>
        </div>

        <div id="chatInterface" style="display:none; flex-direction:column; flex:1;">
            <div id="msgBox" class="msg-list"></div>
            <div class="chat-footer">
                <span onclick="recordVoice()" id="micBtn" style="cursor:pointer">🎤</span>
                <input type="text" id="chatInp" placeholder="اكتب رسالة..." style="flex:1; padding:8px; border-radius:10px; border:1px solid #ddd;">
                <span onclick="sendText()" style="cursor:pointer; color:var(--bt-blue)">◀</span>
            </div>
        </div>
    </div>

    <script>
        let connectedDevice = null;
        let blockedIPs = JSON.parse(localStorage.getItem('blocked_list')) || [];
        let mediaRecorder;
        let audioChunks = [];

        function toggleWindow() {
            const win = document.getElementById('mainWin');
            win.style.display = (win.style.display === 'flex') ? 'none' : 'flex';
        }

        // 1. البحث عن الأجهزة (Bluetooth API Simulation + Logic)
        async function searchDevices() {
            const list = document.getElementById('deviceList');
            list.innerHTML = "<p style='font-size:12px; text-align:center;'>جاري المسح الترددي...</p>";
            
            try {
                // محاولة الاتصال الحقيقي بالبلوتوث
                if (navigator.bluetooth) {
                    await navigator.bluetooth.requestDevice({ acceptAllDevices: true });
                }
            } catch(e) {}

            setTimeout(() => {
                const mockDevices = [
                    { name: "جهاز ياسين", id: "192.168.1.45" },
                    { name: "هاتف مجهول", id: "10.0.0.22" }
                ];
                list.innerHTML = "";
                mockDevices.forEach(dev => {
                    if (blockedIPs.includes(dev.id)) return;
                    list.innerHTML += `
                        <div class="device-card">
                            <div><span class="status-dot"></span> <b>${dev.name}</b> <br><small>${dev.id}</small></div>
                            <div>
                                <button class="btn-sm" style="background:#2ecc71; color:white;" onclick="sendFriendRequest('${dev.name}', '${dev.id}')">طلب صداقة</button>
                                <button class="btn-sm" style="background:#e74c3c; color:white;" onclick="blockDevice('${dev.id}')">حظر</button>
                            </div>
                        </div>`;
                });
            }, 1500);
        }

        // 2. إرسال طلب صداقة
        function sendFriendRequest(name, ip) {
            alert(`تم إرسال طلب صداقة إلى ${name}... في انتظار القبول.`);
            setTimeout(() => {
                if(confirm(`وافق ${name} على طلبك. هل تريد بدء المحادثة الآمنة؟`)) {
                    connectedDevice = { name, ip };
                    document.getElementById('pairingArea').style.display = 'none';
                    document.getElementById('chatInterface').style.display = 'flex';
                    document.querySelector('.chat-header span').innerText = `متصل مع: ${name}`;
                    speak(`تم الاتصال بـ ${name}`);
                }
            }, 2000);
        }

        // 3. نظام الحظر (Block IP)
        function blockDevice(ip) {
            if(confirm(`هل تريد حظر الـ IP: ${ip} نهائياً؟`)) {
                blockedIPs.push(ip);
                localStorage.setItem('blocked_list', JSON.stringify(blockedIPs));
                alert("تم إدراج الجهاز في القائمة السوداء.");
                searchDevices();
            }
        }

        // 4. الشات والرسائل الصوتية (WhatsApp Style)
        function sendText() {
            const inp = document.getElementById('chatInp');
            if(inp.value) { appendMsg(inp.value, 'sent'); inp.value = ""; }
        }

        async function recordVoice() {
            const mic = document.getElementById('micBtn');
            if(!mediaRecorder || mediaRecorder.state === "inactive") {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                mediaRecorder = new MediaRecorder(stream);
                audioChunks = [];
                mediaRecorder.ondataavailable = e => audioChunks.push(e.data);
                mediaRecorder.onstop = () => {
                    const blob = new Blob(audioChunks, { type: 'audio/webm' });
                    const url = URL.createObjectURL(blob);
                    const player = `<div style="display:flex; align-items:center; gap:5px;">
                        <span onclick="new Audio('${url}').play()" style="cursor:pointer">▶️ سجل صوتي</span>
                        <div style="width:60px; height:3px; background:#34b7f1;"></div>
                    </div>`;
                    appendMsg(player, 'sent');
                };
                mediaRecorder.start();
                mic.style.color = "red";
            } else {
                mediaRecorder.stop();
                mic.style.color = "black";
            }
        }

        function appendMsg(content, type) {
            const box = document.getElementById('msgBox');
            const div = document.createElement('div');
            div.className = `msg ${type}`;
            div.innerHTML = `${content} <div class="ticks">✓✓</div>`;
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;

            // علامات الصح الزرقاء بعد القراءة
            setTimeout(() => {
                div.querySelector('.ticks').classList.add('read');
            }, 2000);
        }

        function speak(t) {
            const m = new SpeechSynthesisUtterance(t);
            m.lang = "ar-SA";
            window.speechSynthesis.speak(m);
        }

        function startSystem() {
            if(!window.sysStarted) {
                speak("نظام تاريشت للاتصال المباشر مفعل");
                window.sysStarted = true;
            }
        }
    </script>
</body>
</html>
