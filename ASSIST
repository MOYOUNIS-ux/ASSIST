<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام جرد الأصول الذكي</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { 
            --primary: #003366; 
            --primary-light: #004c99;
            --accent: #27ae60; 
            --bg: #f0f2f5; 
            --white: #ffffff;
        }

        body { 
            font-family: 'Cairo', sans-serif; 
            background: var(--bg); 
            margin: 0; 
            padding: 0; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            min-height: 100vh;
        }

        .header { 
            background: linear-gradient(135deg, var(--primary), var(--primary-light));
            color: white; 
            width: 100%; 
            padding: 30px 0; 
            text-align: center; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.15);
            border-bottom-left-radius: 30px;
            border-bottom-right-radius: 30px;
        }

        .header h1 { margin: 0; font-size: 1.5rem; font-weight: 700; letter-spacing: 0.5px; }

        .main-container { 
            width: 92%; 
            max-width: 450px; 
            margin-top: -20px; 
            z-index: 10;
        }

        .search-box { 
            background: var(--white); 
            padding: 20px; 
            border-radius: 20px; 
            margin-bottom: 20px; 
            box-shadow: 0 10px 25px rgba(0,0,0,0.05); 
            display: flex;
            gap: 10px;
        }

        .search-box input { 
            flex: 1;
            padding: 12px 15px; 
            border: 2px solid #edf2f7; 
            border-radius: 12px; 
            font-family: 'Cairo'; 
            outline: none;
            transition: 0.3s;
            font-size: 0.9rem;
        }

        .search-box input:focus { border-color: var(--primary); }

        .search-box button { 
            padding: 0 20px; 
            background: var(--primary); 
            color: white; 
            border: none; 
            border-radius: 12px; 
            cursor: pointer; 
            font-weight: 600;
            transition: 0.3s;
        }

        .search-box button:hover { background: var(--primary-light); transform: translateY(-2px); }

        .divider { text-align: center; color: #718096; margin: 15px 0; font-size: 0.85rem; font-weight: 600; }

        #reader { 
            width: 100%; 
            border-radius: 20px; 
            overflow: hidden; 
            background: var(--white); 
            border: 4px solid var(--white); 
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }
        
        .btn-start { 
            width: 100%; 
            padding: 15px; 
            background: #4a5568; 
            color: white; 
            border: none; 
            border-radius: 15px; 
            font-weight: 700; 
            cursor: pointer; 
            margin-bottom: 15px; 
            transition: 0.3s;
        }

        .btn-start:hover { background: #2d3748; }

        .result-card { 
            display: none; 
            background: var(--white); 
            border-radius: 20px; 
            padding: 25px; 
            margin-top: 20px; 
            box-shadow: 0 15px 35px rgba(0,0,0,0.1); 
            border-top: 6px solid var(--accent); 
            animation: slideIn 0.5s ease-out;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateY(30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .data-item { 
            display: flex; 
            justify-content: space-between; 
            padding: 12px 0; 
            border-bottom: 1px solid #f1f5f9; 
        }

        .data-item:last-of-type { border-bottom: none; }
        .label { color: #718096; font-weight: 600; font-size: 0.9rem; }
        .value { color: var(--primary); font-weight: 700; }

        .loader { 
            display: none; 
            border: 4px solid #e2e8f0; 
            border-top: 4px solid var(--primary); 
            border-radius: 50%; 
            width: 40px; 
            height: 40px; 
            animation: spin 1s linear infinite; 
            margin: 25px auto; 
        }

        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        .footer {
            margin-top: auto;
            padding: 40px 0;
            text-align: center;
            width: 100%;
        }

        .footer span {
            display: block;
            font-size: 0.75rem;
            color: #a0aec0;
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-bottom: 5px;
        }

        .footer h2 {
            margin: 0;
            font-size: 1.1rem;
            color: var(--primary);
            font-weight: 700;
            opacity: 0.8;
        }
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

    <div class="divider">أو استخدام المسح الضوئي</div>
    
    <button id="start-btn" class="btn-start" onclick="initScanner()">تشغيل الكاميرا</button>
    
    <div id="reader"></div>
    <div id="loader" class="loader"></div>

    <div id="result-card" class="result-card">
        <h3 style="margin:0 0 15px 0; color:var(--primary); font-size: 1.1rem; border-right: 4px solid var(--accent); padding-right: 10px;">بيانات الأصل المكتشف</h3>
        <div id="info-content"></div>
        <button onclick="location.reload()" class="btn-start" style="background:var(--accent); margin-top:20px; width:100%;">فحص جديد</button>
    </div>
</div>

<div class="footer">
    <span>Developed By</span>
    <h2>MOHAMED YOUNIS</h2>
</div>

<script>
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwe555N9hWj3UjWGi9YWJO2eh-y9yGq9Tmjjs1mKWa6wEAwzggS5Zo1MIHhdq8Qm4tV/exec";
    let html5QrCode;

    function manualSearch() {
        const code = document.getElementById('manual-code').value;
        if(code) {
            if(html5QrCode) html5QrCode.stop().catch(()=>{}); 
            fetchData(code);
        } else {
            alert("يرجى إدخال الكود أولاً");
        }
    }

    async function initScanner() {
        document.getElementById('start-btn').style.display = 'none';
        html5QrCode = new Html5Qrcode("reader");
        const config = { fps: 15, qrbox: { width: 250, height: 150 } };
        try {
            await html5QrCode.start({ facingMode: "environment" }, config, (text) => {
                html5QrCode.stop().then(() => fetchData(text));
            });
        } catch (err) {
            alert("تأكد من إعطاء إذن الكاميرا واستخدام رابط HTTPS");
            document.getElementById('start-btn').style.display = 'block';
        }
    }

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
            <div class="data-item"><span class="label">📍 الموقع:</span> <span class="value">${data.branch || '-'}</span></div>
            <div class="data-item"><span class="label">👤 العهدة:</span> <span class="value">${data.employee || '-'}</span></div>
            <div class="data-item"><span class="label">⚙️ الحالة:</span> <span class="value">${data.condition || '-'}</span></div>
        `;
    }
</script>

</body>
</html>
