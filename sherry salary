<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sherry Aerial 薪資查詢系統</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body { background-color: #f8f9fa; padding: 20px; font-family: "Microsoft JhengHei", sans-serif; }
        .container { max-width: 600px; background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .header-logo { text-align: center; margin-bottom: 25px; font-weight: bold; color: #6a1b9a; }
        .salary-card { border-left: 5px solid #6a1b9a; background: #f3e5f5; margin-bottom: 20px; }
        .hidden { display: none; }
        .loading-spinner { display: none; text-align: center; margin: 20px 0; }
    </style>
</head>
<body>

<div class="container">
    <div class="header-logo"><h2>Sherry Aerial 薪資查詢</h2></div>

    <div id="login-section">
        <div class="mb-3">
            <label class="form-label">老師姓名</label>
            <input type="text" id="teacherName" class="form-control" placeholder="請輸入您的姓名">
        </div>
        <div class="mb-3">
            <label class="form-label">查詢密碼</label>
            <input type="password" id="password" class="form-control" placeholder="預設為 0000 或自訂密碼">
        </div>
        <button class="btn btn-primary w-100" onclick="login()">登入查詢</button>
    </div>

    <div id="loader" class="loading-spinner">
        <div class="spinner-border text-primary" role="status"></div>
        <p class="mt-2">資料讀取中，請稍候...</p>
    </div>

    <div id="result-section" class="hidden">
        <div class="card salary-card mb-3">
            <div class="card-body">
                <h5 class="card-title" id="display-name">老師，您好</h5>
                <hr>
                <p class="mb-1">基礎鐘點費：<strong id="base-fee">0</strong></p>
                <p class="mb-1">獎金金額：<strong id="bonus-fee">0</strong></p>
                <h4 class="text-danger mt-2">應領總薪資：<span id="total-fee">0</span></h4>
            </div>
        </div>

        <h6>本月課程明細：</h6>
        <div class="table-responsive">
            <table class="table table-sm table-striped">
                <thead><tr><th>日期</th><th>課程</th><th>鐘點</th></tr></thead>
                <tbody id="class-detail"></tbody>
            </table>
        </div>

        <div id="confirm-box" class="mt-4">
            <button id="confirm-btn" class="btn btn-success w-100" onclick="confirmSalary()">確認薪資無誤</button>
            <p id="confirmed-msg" class="text-center text-success mt-2 hidden">✅ 您已完成本月核對</p>
        </div>
        
        <button class="btn btn-outline-secondary w-100 mt-3" onclick="location.reload()">登出</button>
    </div>
</div>

<script>
    // ⚠️ 請替換成你部署後的「網頁應用程式 URL」
    const GAS_URL = "https://script.google.com/macros/s/AKfycbyMMrzKOIhEOeDpHP74910qzfd-HAPnUgq0FTAImHUHCODT2DcnGZmyypS4fdOx9pYAaA/exec";

    let currentTeacher = "";

    async function login() {
        const name = document.getElementById('teacherName').value;
        const pwd = document.getElementById('password').value;

        if (!name || !pwd) { alert("請填寫姓名與密碼"); return; }

        showLoader(true);
        
        try {
            const response = await fetch(`${GAS_URL}?action=login&name=${encodeURIComponent(name)}&password=${encodeURIComponent(pwd)}`);
            const result = await response.json();

            if (result.success) {
                renderData(result.data);
                currentTeacher = name;
            } else {
                alert("登入失敗：" + result.message);
            }
        } catch (e) {
            alert("系統連線異常，請稍後再試");
        } finally {
            showLoader(false);
        }
    }

    function renderData(data) {
        document.getElementById('login-section').classList.add('hidden');
        document.getElementById('result-section').classList.remove('hidden');
        
        document.getElementById('display-name').innerText = `${data.name} 老師，您好`;
        document.getElementById('base-fee').innerText = `$${data.base.toLocaleString()}`;
        document.getElementById('bonus-fee').innerText = `$${data.bonus.toLocaleString()}`;
        document.getElementById('total-fee').innerText = `$${data.total.toLocaleString()}`;

        let html = "";
        data.classes.forEach(c => {
            html += `<tr><td>${c.date}</td><td>${c.title}</td><td>$${c.fee}</td></tr>`;
        });
        document.getElementById('class-detail').innerHTML = html;

        if (data.isConfirmed) {
            showConfirmedStatus();
        }
    }

    async function confirmSalary() {
        if (!confirm("確認薪資金額正確嗎？")) return;

        showLoader(true);
        try {
            const response = await fetch(`${GAS_URL}?action=confirm&name=${encodeURIComponent(currentTeacher)}`);
            const result = await response.json();
            if (result.success) {
                showConfirmedStatus();
                alert("已成功確認！");
            }
        } catch (e) {
            alert("確認失敗，請檢查網路");
        } finally {
            showLoader(false);
        }
    }

    function showConfirmedStatus() {
        document.getElementById('confirm-btn').classList.add('hidden');
        document.getElementById('confirmed-msg').classList.remove('hidden');
    }

    function showLoader(show) {
        document.getElementById('loader').style.display = show ? "block" : "none";
        if(show) document.getElementById('login-section').classList.add('hidden');
    }
</script>

</body>
</html>
