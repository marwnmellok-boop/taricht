<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نادي تاريشت - مراسلة آمنة</title>
    <style>
        :root {
            --primary: #1e3c72;
            --secondary: #2a5298;
            --white: #ffffff;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
        }

        .card {
            background: var(--white);
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            width: 90%;
            max-width: 400px;
            text-align: center;
        }

        h2 { color: var(--primary); margin-bottom: 20px; }

        input, textarea {
            width: 100%;
            padding: 12px;
            margin-bottom: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            box-sizing: border-box;
            font-family: inherit;
        }

        button {
            background: var(--primary);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 8px;
            width: 100%;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
        }

        button:hover { background: var(--secondary); }

        #status {
            margin-top: 15px;
            font-size: 14px;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div class="card">
        <h2>تواصل مع الإدارة ⚽</h2>
        <form id="secure-form">
            <input type="email" name="email" id="email" placeholder="بريدك الإلكتروني" required>
            <textarea name="message" id="message" rows="4" placeholder="اكتب رسالتك هنا..." required></textarea>
            
            <input type="hidden" name="user_ip" id="user_ip">
            
            <button type="submit" id="submit-btn">إرسال الرسالة</button>
        </form>
        <div id="status"></div>
    </div>

    <script>
        // 1. جلب عنوان الـ IP الخاص بالمستخدم عند فتح الصفحة
        fetch('https://api.ipify.org?format=json')
            .then(response => response.json())
            .then(data => {
                document.getElementById('user_ip').value = data.ip;
                console.log("IP Detected:", data.ip);
            })
            .catch(err => {
                document.getElementById('user_ip').value = "تعذر جلب الـ IP";
            });

        // 2. معالجة إرسال النموذج
        const form = document.getElementById("secure-form");
        const status = document.getElementById("status");
        const btn = document.getElementById("submit-btn");

        form.addEventListener("submit", async function(event) {
            event.preventDefault();
            btn.innerText = "جاري التأمين والإرسال...";
            btn.disabled = true;

            const formData = new FormData(form);

            fetch("https://formspree.io/f/marwnmellok@gmail.com", {
                method: "POST",
                body: formData,
                headers: { 'Accept': 'application/json' }
            }).then(response => {
                if (response.ok) {
                    status.innerHTML = "✅ تم الإرسال (تم تسجيل الـ IP)";
                    status.style.color = "green";
                    form.reset();
                } else {
                    status.innerHTML = "❌ حدث خطأ في الإرسال";
                    status.style.color = "red";
                }
            }).catch(() => {
                status.innerHTML = "❌ خطأ في الاتصال";
                status.style.color = "red";
            }).finally(() => {
                btn.innerText = "إرسال الرسالة";
                btn.disabled = false;
            });
        });
    </script>

</body>
</html>
