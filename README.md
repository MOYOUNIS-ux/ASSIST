<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام جرد الأصول الذكي</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        :root { --primary: #003366; --accent: #27ae60; --bg: #f4f7f6; }
        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--bg); margin: 0; padding: 0; display: flex; flex-direction: column; align-items: center; }
        .header { background: var(--primary); color: white; width: 100%; padding: 20px; text-align: center; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        
        .main-container { width: 95%; max-width: 450px; margin-top: 20px; }
        
        /* إطار الكاميرا */
        #reader { width: 100%; border-radius: 15px; overflow: hidden; background: white; border: 2px solid #ddd; }
        
        .btn-start { width: 100%; padding: 15px; background: var(--primary); color: white; border: none; border-radius: 10px; font-size: 1.1rem; font-weight: bold; cursor: pointer; margin-bottom: 10px; }
        
        .result-card { display: none; background: white; border-radius: 12px; padding: 20px; margin-top: 20px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); border-right: 6px solid var(--accent); }
        .data-item { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .label { font-weight: bold; color: #555; }
        .value { color: #000; font-weight: 500; }
        
        .loader { display: none; border: 4px solid #f3f3f3; border-top: 4px solid var(--primary); border-radius: 50%; width: 35px; height: 35px; animation: spin 1s linear infinite; margin: 20px auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

<div class="header">
    <h1>نظام تتبع الأصول</h1>
    <p>فرع الدقي - الإدارة المالية</p>
</div>

<div class="main-container">
    <button id="start-btn" class="btn-start" onclick="initScanner()">إبدأ مسح الكود (الكاميرا)</button>
    
    <div id="reader"></div>
    <div id="loader" class="loader"></div>

    <div id="result-card" class="result-card">
        <h3 style="margin-top:0; color:var(--primary);">تفاصيل الأصل:</h3>
        <div id="info-content"></div>
        <button onclick="location.reload()" class="btn-start" style="background:var(--accent); margin-top:15px;">مسح أصل آخر</button>
    </div>
</div>

<script>
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwe555N9hWj3UjWGi9YWJO2eh-y9yGq9Tmjjs1mKWa6wEAwzggS5Zo1MIHhdq8Qm4tV/exec";
    let html5QrCode;

    async function initScanner() {
        document.getElementById('start-btn').style.display = 'none';
        html5QrCode = new Html5Qrcode("reader");
        
        const config = { fps: 10, qrbox: { width: 250, height: 150 } };
        
        try {
            // محاولة تشغيل الكاميرا الخلفية مباشرة
            await html5QrCode.start({ facingMode: "environment" }, config, onScanSuccess);
        } catch (err) {
            alert("لم نتمكن من فتح الكاميرا. تأكد من إعطاء الإذن أو استخدام رابط HTTPS");
            document.getElementById('start-btn').style.display = 'block';
        }
    }

    async function onScanSuccess(decodedText) {
        // إيقاف الكاميرا فوراً بعد قراءة الكود
        await html5QrCode.stop();
        document.getElementById('reader').style.display = 'none';
        document.getElementById('loader').style.display = 'block';

        // إرسال الطلب لـ Google Sheets
        fetch(`${SCRIPT_URL}?code=${decodedText}`)
            .then(res => res.json())
            .then(data => {
                document.getElementById('loader').style.display = 'none';
                if (data.status === "success") {
                    displayData(data);
                } else {
                    alert("الكود غير موجود في السجلات أونلاين!");
                    location.reload();
                }
            })
            .catch(error => {
                alert("حدث خطأ أثناء جلب البيانات.");
                location.reload();
            });
    }

    function displayData(data) {
        const card = document.getElementById('result-card');
        const content = document.getElementById('info-content');
        card.style.display = 'block';
        
        content.innerHTML = `
            <div class="data-item"><span class="label">📦 اسم الأصل:</span> <span class="value">${data.name || 'غير متوفر'}</span></div>
            <div class="data-item"><span class="label">🏢 القسم:</span> <span class="value">${data.department || 'غير متوفر'}</span></div>
            <div class="data-item"><span class="label">📍 الفرع:</span> <span class="value">${data.branch || 'غير متوفر'}</span></div>
            <div class="data-item"><span class="label">👤 الموظف:</span> <span class="value">${data.employee || 'غير متوفر'}</span></div>
            <div class="data-item"><span class="label">⚙️ الحالة الفنية:</span> <span class="value">${data.condition || 'غير متوفر'}</span></div>
            <div class="data-item"><span class="label">📅 التاريخ:</span> <span class="value">${data.date || 'غير متوفر'}</span></div>
        `;
    }
</script>

</body>
</html>
