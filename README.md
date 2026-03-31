<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام جرد الأصول المطور</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
    <style>
        :root { --primary: #003366; --accent: #27ae60; --bg: #f4f7f6; }
        body { font-family: 'Cairo', sans-serif; background: var(--bg); margin: 0; padding: 0; display: flex; flex-direction: column; align-items: center; }
        .header { background: var(--primary); color: white; width: 100%; padding: 15px; text-align: center; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .main-container { width: 95%; max-width: 450px; margin-top: 20px; }
        
        /* قسم البحث اليدوي */
        .search-box { background: white; padding: 15px; border-radius: 12px; margin-bottom: 15px; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .search-box input { width: 70%; padding: 10px; border: 1px solid #ddd; border-radius: 8px; font-family: 'Cairo'; }
        .search-box button { width: 25%; padding: 10px; background: var(--primary); color: white; border: none; border-radius: 8px; cursor: pointer; }

        /* إطار الكاميرا */
        #reader { width: 100%; border-radius: 15px; overflow: hidden; background: white; border: 2px solid #ddd; }
        
        .btn-start { width: 100%; padding: 12px; background: #555; color: white; border: none; border-radius: 10px; font-weight: bold; cursor: pointer; margin-bottom: 10px; }
        
        .result-card { display: none; background: white; border-radius: 12px; padding: 20px; margin-top: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); border-right: 6px solid var(--accent); }
        .data-item { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .label { font-weight: bold; color: #555; }
        .value { color: #000; font-weight: bold; }
        
        .loader { display: none; border: 4px solid #f3f3f3; border-top: 4px solid var(--primary); border-radius: 50%; width: 30px; height: 30px; animation: spin 1s linear infinite; margin: 20px auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

<div class="header">
    <h1>نظام تتبع الأصول الذكي</h1>
</div>

<div class="main-container">
    <div class="search-box">
        <input type="text" id="manual-code" placeholder="أدخل رقم الكود يدوياً...">
        <button onclick="manualSearch()">بحث</button>
    </div>

    <p style="text-align: center; color: #666;">-- أو استخدم الكاميرا --</p>
    
    <button id="start-btn" class="btn-start" onclick="initScanner()">تشغيل ماسح الباركود</button>
    
    <div id="reader"></div>
    <div id="loader" class="loader"></div>

    <div id="result-card" class="result-card">
        <h3 style="margin:0 0 15px 0; color:var(--primary); border-bottom: 2px solid #eee; padding-bottom: 5px;">بيانات الأصل:</h3>
        <div id="info-content"></div>
        <button onclick="location.reload()" class="btn-start" style="background:var(--accent); margin-top:15px; width:100%;">عملية جديدة</button>
    </div>
</div>

<script>
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwe555N9hWj3UjWGi9YWJO2eh-y9yGq9Tmjjs1mKWa6wEAwzggS5Zo1MIHhdq8Qm4tV/exec";
    let html5QrCode;

    // دالة البحث اليدوي
    function manualSearch() {
        const code = document.getElementById('manual-code').value;
        if(code) {
            if(html5QrCode) html5QrCode.stop(); // إيقاف الكاميرا لو شغالة
            fetchData(code);
        } else {
            alert("يرجى إدخال الكود أولاً");
        }
    }

    // تهيئة الكاميرا
    async function initScanner() {
        document.getElementById('start-btn').style.display = 'none';
        html5QrCode = new Html5Qrcode("reader");
        const config = { fps: 15, qrbox: { width: 250, height: 150 } };
        try {
            await html5QrCode.start({ facingMode: "environment" }, config, (text) => {
                html5QrCode.stop();
                fetchData(text);
            });
        } catch (err) {
            alert("تأكد من إعطاء إذن الكاميرا واستخدام رابط HTTPS");
            document.getElementById('start-btn').style.display = 'block';
        }
    }

    // جلب البيانات من شيت جوجل
    function fetchData(code) {
        document.getElementById('reader').style.display = 'none';
        document.getElementById('loader').style.display = 'block';
        document.getElementById('result-card').style.display = 'none';

        fetch(`${SCRIPT_URL}?code=${code}`)
            .then(res => res.json())
            .then(data => {
                document.getElementById('loader').style.display = 'none';
                if (data.status === "success") {
                    displayData(data);
                } else {
                    alert("الكود غير موجود في السجلات!");
                    location.reload();
                }
            })
            .catch(error => {
                alert("خطأ في الاتصال بقاعدة البيانات");
                location.reload();
            });
    }

    function displayData(data) {
        const card = document.getElementById('result-card');
        const content = document.getElementById('info-content');
        card.style.display = 'block';
        content.innerHTML = `
            <div class="data-item"><span class="label">📦 الاسم:</span> <span class="value">${data.name || '-'}</span></div>
            <div class="data-item"><span class="label">🏢 القسم:</span> <span class="value">${data.department || '-'}</span></div>
            <div class="data-item"><span class="label">📍 الفرع:</span> <span class="value">${data.branch || '-'}</span></div>
            <div class="data-item"><span class="label">👤 الموظف:</span> <span class="value">${data.employee || '-'}</span></div>
            <div class="data-item"><span class="label">⚙️ الحالة:</span> <span class="value">${data.condition || '-'}</span></div>
        `;
    }
</script>

</body>
</html>
