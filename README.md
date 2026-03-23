<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - تفاعل اللاعبين</title>
    <style>
        :root {
            --primary: #1e3c72;
            --accent: #ffd700;
            --white: #ffffff;
            --heart: #e74c3c;
        }

        body, html {
            height: 100%;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: var(--white);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        .card {
            background: #fff;
            padding: 25px;
            border-radius: 25px;
            box-shadow: 0 15px 50px rgba(0,0,0,0.1);
            width: 95%;
            max-width: 450px;
            text-align: center;
            border: 1px solid #f0f0f0;
        }

        h3 { color: var(--primary); margin: 0 0 15px 0; }

        .char-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            margin-bottom: 20px;
        }

        .char-item {
            cursor: pointer;
            padding: 8px;
            border-radius: 12px;
            border: 2px solid transparent;
            transition: 0.3s;
        }

        .char-item.active { border-color: var(--primary); background: #f0f4ff; }
        .char-item img { width: 45px; height: 45px; border-radius: 50%; }
        .char-item span { font-size: 11px; display: block; margin-top: 5px; }

        input, textarea {
            width: 100%;
            padding: 12px;
            margin-bottom: 12px;
            border: 1px solid #ddd;
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
            transition: 0.3s;
        }

        button:disabled { background: #ccc; }

        /* تنسيق الفيديو العريض في الأسفل */
        .video-section {
            margin-top: 25px;
            padding-top: 20px;
            border-top: 2px solid #f0f0f0;
        }

        .video-container {
            width: 100%;
            border-radius: 15px;
            overflow: hidden;
            aspect-ratio: 16/9; /* شكل عريض */
            background: #000;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }

        .video-container iframe { width: 100%; height: 100%; border: none; }

        /* نظام الإعجابات */
        .like-area {
            margin-top: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 10px;
            cursor: pointer;
            transition: 0.3s;
        }

        .like-icon { font-size: 24px; color: #ccc; transition: 0.3s; }
        .like-area.active .like-icon { color: var(--heart); transform: scale(1.2); }
        .like-text { font-size: 14px; font-weight: bold; color: #555; }

        #block-msg { color: red; font-size: 12px; display: none; margin-top: 10px; font-weight: bold; }
        .tap-hint { position: fixed; bottom: 10px; font-size: 11px; color: #ccc; }
    </style>
</head>
<body onclick="welcomeVoice()">

    <div class="card">
        <h3>تواصل مع إدارة تاريشت ⚽</h3>
        
        <div class="char-grid">
            <div class="char-item active" onclick="selChar(this, 'شعار الفريق')">
                <img src="https://cdn-icons-png.flaticon.com/512/53/53283.png"><span>الفريق</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'ميسي')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135715.png"><span>ميسي</span>
            </div>
            <div class="char-item" onclick="selChar(this, 'رونالدو')">
                <img src="https://cdn-icons-png.flaticon.com/512/3135/3135768.png"><span>رونالدو</span>
            </div>
        </div>

        <input type="text" id="name" placeholder="الاسم الكامل">
        <textarea id="msg" rows="2" placeholder="رسالتك..."></textarea>
        
        <button id="sendBtn" onclick="handleSend()">إرسال الرسالة والتفاعل</button>
        <div id="block-msg">⚠️ تم حظرك مؤقتاً!</div>

        <div class="video-section">
            <div class="video-container">
                <iframe src="https://www.youtube.com/embed/inhEapj87R8" 
                        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                        allowfullscreen>
                </iframe>
            </div>
            <div class="like-area" id="likeArea" onclick="toggleLike()">
                <span class="like-icon">❤</span>
                <span class="like-text" id="likeStatus">أعجبني الفيديو</span>
            </div>
        </div>
    </div>

    <div class="tap-hint">انقر لتفعيل الصوت 🔊</div>

    <script>
        let char = "شعار الفريق";
        let isLiked = false;
        let sendCount = 0;
        let hasWelcomed = false;

        function welcomeVoice() {
            if (!hasWelcomed) {
                const speech = new SpeechSynthesisUtterance("مرحباً بك في تاريشت");
                speech.lang = "ar-SA";
                window.speechSynthesis.speak(speech);
                hasWelcomed = true;
                document.querySelector('.tap-hint').style.display = 'none';
            }
        }

        function selChar(el, name) {
            document.querySelectorAll('.char-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            char = name;
        }

        function toggleLike() {
            isLiked = !isLiked;
            const area = document.getElementById('likeArea');
            const status = document.getElementById('likeStatus');
            if (isLiked) {
                area.classList.add('active');
                status.innerText = "تم الإعجاب بالفيديو";
            } else {
                area.classList.remove('active');
                status.innerText = "أعجبني الفيديو";
            }
        }

        function handleSend() {
            const name = document.getElementById('name').value;
            const msg = document.getElementById('msg').value;
            const btn = document.getElementById('sendBtn');
            const blockMsg = document.getElementById('block-msg');
            const reaction = isLiked ? "قام بالإعجاب بالفيديو ❤️" : "لم يقم بالإعجاب بالفيديو ❌";

            if(!name || !msg) return alert("يرجى ملء البيانات");

            sendCount++;
            if (sendCount >= 5) {
                btn.style.display = "none";
                blockMsg.style.display = "block";
                setTimeout(() => { sendCount = 0; btn.style.display = "block"; blockMsg.style.display = "none"; }, 3600000);
                return;
            }

            // إرسال البيانات بما فيها التفاعل (Like)
            const subject = encodeURIComponent(`رسالة من ${name}`);
            const body = encodeURIComponent(
                `اللاعب: ${name}\n` +
                `الشخصية: ${char}\n` +
                `التفاعل: ${reaction}\n\n` +
                `الرسالة: ${msg}`
            );

            window.location.href = `mailto:marwnmellok@gmail.com?subject=${subject}&body=${body}`;

            let timeLeft = 3;
            btn.disabled = true;
            const timer = setInterval(() => {
                btn.innerText = `انتظر ${timeLeft}...`;
                timeLeft--;
                if (timeLeft < 0) { clearInterval(timer); btn.innerText = "إرسال الرسالة والتفاعل"; btn.disabled = false; }
            }, 1000);
        }
    </script>
</body>
</html>
