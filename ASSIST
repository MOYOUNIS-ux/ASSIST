<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام جرد الأصول الذكي</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        :root { --primary: #003366; --success: #28a745; --bg: #f8f9fa; }
        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--bg); margin: 0; display: flex; flex-direction: column; align-items: center; }
        .header { background: var(--primary); color: white; width: 100%; padding: 15px; text-align: center; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        .container { width: 95%; max-width: 450px; margin-top: 20px; }
        #reader { width: 100%; border-radius: 12px; overflow: hidden; border: 2px solid #ddd; background: #fff; }
        .btn-action { width: 100%; padding: 15px; background: var(--primary); color: white; border: none; border-radius: 8px; font-size: 1.1rem; cursor: pointer; margin-bottom: 10px; }
        .result-card { display: none; background: white; border-radius: 10px; padding: 20px; margin-top: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); border-right: 6px solid var(--success); }
        .data-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #eee; }
        .label { font-weight: bold; color: #666; }
        .value { color: #000; }
        .loader { display: none; border: 4px solid #f3f3f3; border-top: 4px solid var(--primary); border-radius: 50%; width: 30px; height: 30px; animation: spin 1s linear infinite; margin: 20px auto; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

<div class="header">
    <h2>نظام تتبع الأصول - الإدارة المالية</h2>
</div>

<div class="container">
    <button id="start-btn" class="btn-action" onclick="startScanner()">فتح الكاميرا والبدء</button>
    <div id="reader"></div>
    <div id="loader" class="loader"></div>

    <div id="result-card" class="result-card">
        <h3 style="margin-top:0; color:var(--primary);">بيانات الأصل:</h3>
        <div id="info-content"></div>
        <button onclick="location.reload()" class="btn-action" style="background:var(--success); margin-top:15px;">مسح أصل جديد</button>
    </div>
</div>

<script>
    // رابط الـ Script الخاص بك
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwe555N9hWj3UjWGi9YWJO2eh-y9yGq9Tmjjs1mKWa6wEAwzggS5Zo1MIHhdq8Qm4tV/exec";
    let html5QrCode;

    async function startScanner() {
        document.getElementById('start-btn').style.display = 'none';
        html5QrCode = new Html5Qrcode("reader");
        const config = { 
            fps: 15, 
            qrbox: { width: 250, height: 150 },
            aspectRatio: 1.0
        };
        
        try {
            await html5QrCode.start({ facingMode: "environment" }, config, onScanSuccess);
        } catch (err) {
            alert("خطأ: يرجى التأكد من استخدام رابط HTTPS وإعطاء إذن الكاميرا.");
            document.getElementById('start-btn').style.display = 'block';
        }
    }

    async function onScanSuccess(decodedText) {
        await html5QrCode.stop();
        document.getElementById('reader').style.display = 'none';
        document.getElementById('loader').style.display = 'block';

        // إرسال الكود للبحث في الشيت أونلاين
        fetch(`${SCRIPT_URL}?code=${decodedText}`)
            .then(res => res.json())
            .then(data => {
                document.getElementById('loader').style.display = 'none';
                if (data.status === "success") {
                    showData(data);
                } else {
                    alert("الكود غير مسجل!");
                    location.reload();
                }
            })
            .catch(err => {
                alert("حدث خطأ في الاتصال.");
                location.reload();
            });
    }

    function showData(data) {
        document.getElementById('result-card').style.display = 'block';
        document.getElementById('info-content').innerHTML = `
            <div class="data-row"><span class="label">اسم الأصل:</span> <span class="value">${data.name || '-'}</span></div>
            <div class="data-row"><span class="label">القسم:</span> <span class="value">${data.department || '-'}</span></div>
            <div class="data-row"><span class="label">الفرع:</span> <span class="value">${data.branch || '-'}</span></div>
            <div class="data-row"><span class="label">الموظف:</span> <span class="value">${data.employee || '-'}</span></div>
            <div class="data-row"><span class="label">الحالة:</span> <span class="value">${data.condition || '-'}</span></div>
        `;
    }
</script>
</body>
</html>
