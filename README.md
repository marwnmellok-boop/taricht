<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - النسخة الموسيقية</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --bg: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            overflow-x: hidden;
        }

        .card {
            background: #fff;
            padding: 25px;
            border-radius: 25px;
            box-shadow: 0 10px 50px rgba(0,0,0,0.08);
            width: 90%;
            max-width: 400px;
            text-align: center;
            border: 1px solid #f0f0f0;
            z-index: 10;
        }

        .main-avatar {
            width: 90px;
            height: 90px;
            border-radius: 50%;
            margin: 0 auto 15px;
            border: 3px solid var(--primary);
            padding: 3px;
            background: #fff;
        }

        .main-avatar img { width: 100%; height: 100%; border-radius: 50%; object-fit: cover; }

        .char-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 12px;
            margin: 20px 0;
        }

        .char-item {
            cursor: pointer;
            filter: grayscale(100%);
            transition: 0.3s;
        }

        .char-item.active { filter: grayscale(0%); transform: scale(1.1); }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; border: 2px solid #eee; }
        .char-item span { font-size: 11px; display: block; color: #666; margin-top: 4px; }

        input, textarea {
            width: 100%;
            padding: 12px;
            margin-bottom: 12px;
            border: 1px solid #eee;
            border-radius: 10px;
            box-sizing: border-box;
            background: #fafafa;
        }

        button {
            background: var(--primary);
            color: white;
            border: none;
            padding: 15px;
            border-radius: 12px;
            width: 100%;
            font-weight: bold;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(30, 60, 114, 0.2);
        }

        button:disabled { background: #ccc; box-shadow: none; }

        #block-msg { color: #d9534f; font-size: 13px; display: none; margin-top: 10px; font-weight: bold; }

        /* أيقونة التحكم بالصوت */
        .audio-control {
            position: absolute;
            top: 20px;
            right: 20px;
            cursor: pointer;
            font-size: 24px;
        }
    </style>
</head>
<body>

    <audio id="bgMusic" loop>
        <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mpeg">
    </audio>
    
    <audio id="clickSound"><source src="https://assets.mixkit.co/active_storage/sfx/2571/2571-preview.mp3"></audio>
    <audio id="whistleSound"><source src="https://assets.mixkit.co/active_storage/sfx/2051/2051-preview.mp3"></audio>

    <div class="audio-control" onclick="toggleMusic()">🎵</div>

    <div class="card">
        <div class="main-avatar" id="avatar-view">
            <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png">
        </div>

        <h3 style="color: var(--primary); margin: 0;">نادي تاريشت</h3>
        
        <div class="char-grid">
            <div class="char-item active" onclick="selectChar(this, 'الفريق', 'https://cdn-icons-png.flaticon.com/512/53/53283.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span>
            </div>
            <div class="char-item" onclick="selectChar(this, 'ميسي', 'https://cdn-icons-png.flaticon.com/512/3135/3135715.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span>
            </div>
            <div class="char-item" onclick="selectChar(this, 'رونالدو', 'https://cdn-icons-png.flaticon.com/512/3135/3135768.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span>
            </div>
            <div class="char-item" onclick="selectChar(this, 'نيمار', 'https://cdn-icons-png.flaticon.com/512/3135/3135789.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135789.png"><span>نيمار</span>
            </div>
            <div class="char-item" onclick="selectChar(this, 'مبابي', 'https://cdn-icons-png.flaticon.com/512/3135/3135823.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135823.png"><span>مبابي</span>
            </div>
            <div class="char-item" onclick="selectChar(this, 'هالاند', 'https://cdn-icons-png.flaticon.com/512/4140/4140047.png')">
                <img src="https://cdn-icons-png.flaticon.com/512/4140/4140047.png"><span>هالاند</span>
            </div>
        </div>

        <input type="text" id="username" placeholder="اسمك الكامل">
        <textarea id="message" rows="3" placeholder="اكتب رسالتك للإدارة..."></textarea>
        
        <button id="btnSend" onclick="sendAction()">إرسال الآن ⚽</button>
        <div id="block-msg">⚠️ تم حظرك مؤقتاً! كثرة الإرسال تضر الفريق.</div>
    </div>

    <script>
        const bgMusic = document.getElementById('bgMusic');
        const clickSound = document.getElementById('clickSound');
        const whistle = document.getElementById('whistleSound');
        let attempts = 0;
        let selectedName = "شعار الفريق";

        // تشغيل الموسيقى عند أول لمسة للشاشة (ضروري للمتصفحات)
        document.body.addEventListener('click', () => { bgMusic.play(); }, {once: true});

        function toggleMusic() {
            if (bgMusic.paused) bgMusic.play();
            else bgMusic.pause();
        }

        function selectChar(el, name, img) {
            clickSound.play(); // صوت نقرة
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            document.querySelector('#avatar-view img').src = img;
            selectedName = name;
        }

        function sendAction() {
            const user = document.getElementById('username').value;
            const msg = document.getElementById('message').value;
            const btn = document.getElementById('btnSend');

            if(!user || !msg) return alert("الرجاء إدخال البيانات");

            attempts++;
            whistle.play(); // صوت صافرة عند الإرسال

            if (attempts >= 5) {
                btn.style.display = "none";
                document.getElementById('block-msg').style.display = "block";
                setTimeout(() => {
                    attempts = 0;
                    btn.style.display = "block";
                    document.getElementById('block-msg').style.display = "none";
                }, 3600000); // حظر لمدة ساعة
                return;
            }

            // فتح البريد
            window.location.href = `mailto:marwnmellok@gmail.com?subject=رسالة من ${user}&body=اللاعب: ${user}%0Aالشخصية: ${selectedName}%0Aالرسالة: ${msg}`;

            // عد تنازلي 3 ثواني
            let count = 3;
            btn.disabled = true;
            const timer = setInterval(() => {
                btn.innerText = `انتظر ${count}...`;
                count--;
                if (count < 0) {
                    clearInterval(timer);
                    btn.innerText = "إرسال الآن ⚽";
                    btn.disabled = false;
                }
            }, 1000);
        }
    </script>
</body>
</html>
