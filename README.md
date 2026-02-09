
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>交易助手 Pro</title>
    <style>
        /* --- 基础设置 --- */
        :root {
            --primary: #007AFF; /* iOS 蓝 */
            --bg: #F2F2F7;      /* iOS 背景灰 */
            --card-bg: #FFFFFF;
            --text-main: #000000;
            --text-sub: #8E8E93;
            --green: #34C759;
            --red: #FF3B30;
            --border: #E5E5EA;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Helvetica Neue", Arial, sans-serif;
            background-color: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding: 0;
            -webkit-tap-highlight-color: transparent; /* 去除点击高亮 */
        }

        /* 顶部导航栏 */
        .navbar {
            background: var(--card-bg);
            padding: 15px;
            text-align: center;
            font-weight: 600;
            font-size: 18px;
            border-bottom: 1px solid #d1d1d6;
            position: sticky;
            top: 0;
            z-index: 100;
            padding-top: max(15px, env(safe-area-inset-top)); /* 适配刘海屏 */
        }

        /* 内容容器 */
        .container {
            padding: 16px;
            padding-bottom: 80px; /* 给底部留空 */
            max-width: 600px; /* 电脑上限制宽度，手机上占满 */
            margin: 0 auto;
        }

        /* --- 卡片通用样式 --- */
        .card {
            background: var(--card-bg);
            border-radius: 12px;
            padding: 16px;
            margin-bottom: 16px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.05);
        }

        h2 {
            font-size: 16px;
            margin: 0 0 12px 0;
            color: var(--text-sub);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        /* --- 输入框组 --- */
        .input-group {
            margin-bottom: 16px;
        }
        .input-group label {
            display: block;
            font-size: 14px;
            color: var(--text-main);
            margin-bottom: 8px;
            font-weight: 500;
        }
        .input-group input {
            width: 100%;
            height: 44px; /* 手指点击舒适高度 */
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 0 12px;
            font-size: 17px; /* 防止iOS缩放 */
            box-sizing: border-box;
            background: #fff;
            -webkit-appearance: none;
        }
        .input-group input:focus {
            border-color: var(--primary);
            outline: none;
        }

        /* 按钮 */
        .btn {
            width: 100%;
            height: 48px;
            background-color: var(--primary);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 17px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .btn:active {
            opacity: 0.8;
            transform: scale(0.98);
        }
        
        /* 计算结果展示 */
        .result-display {
            text-align: center;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid var(--border);
        }
        .result-val {
            font-size: 36px;
            font-weight: 700;
            color: var(--text-main);
            font-family: "SF Pro Display", sans-serif;
            margin: 5px 0;
        }
        .result-label {
            font-size: 13px;
            color: var(--text-sub);
        }

        /* --- 文件上传 --- */
        .file-upload-box {
            position: relative;
            background: #EBF5FF;
            border: 2px dashed #A3D0FF;
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            color: var(--primary);
            font-weight: 500;
            margin-bottom: 20px;
        }
        .file-upload-box input {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            opacity: 0;
            cursor: pointer;
        }

        /* --- 交易列表 (卡片流) --- */
        .tx-card {
            background: #fff;
            border-radius: 10px;
            padding: 12px 16px;
            margin-bottom: 12px;
            border: 1px solid var(--border);
            position: relative;
        }
        
        /* 第一行：日期和货币 */
        .tx-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 8px;
        }
        .tx-date {
            font-size: 13px;
            color: var(--text-sub);
        }
        .tx-currency {
            font-size: 17px;
            font-weight: 700;
            color: var(--text-main);
        }

        /* 第二行：信号 (可能会换行) */
        .tx-signal {
            font-size: 14px;
            color: #333;
            background: #f0f0f5;
            padding: 6px 10px;
            border-radius: 6px;
            margin-bottom: 10px;
            line-height: 1.4;
            /* 关键：防止长文本撑开 */
            word-wrap: break-word; 
            word-break: break-all;
        }

        /* 第三行：数据统计 */
        .tx-stats {
            display: flex;
            justify-content: space-between;
            border-top: 1px solid #f0f0f5;
            padding-top: 10px;
        }
        .stat-item {
            text-align: center;
            flex: 1;
        }
        .stat-label {
            font-size: 11px;
            color: var(--text-sub);
            margin-bottom: 2px;
        }
        .stat-val {
            font-size: 15px;
            font-weight: 600;
        }
        
        /* 颜色辅助类 */
        .text-green { color: var(--green); }
        .text-red { color: var(--red); }
        .text-gray { color: var(--text-sub); }

        /* 底部固定导航 (模拟App Tab) */
        .tab-bar {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            background: rgba(255,255,255,0.95);
            backdrop-filter: blur(10px);
            border-top: 1px solid #c6c6c8;
            display: flex;
            padding-bottom: env(safe-area-inset-bottom);
        }
        .tab-item {
            flex: 1;
            text-align: center;
            padding: 10px 0;
            color: var(--text-sub);
            font-size: 12px;
            cursor: pointer;
        }
        .tab-item.active {
            color: var(--primary);
        }
        .tab-icon {
            font-size: 20px;
            display: block;
            margin-bottom: 2px;
        }

        /* 页面切换逻辑 */
        .page { display: none; }
        .page.active { display: block; animation: fadeIn 0.2s; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

    </style>
</head>
<body>

<div class="navbar">交易助手</div>

<div class="container">
    
    <div id="page-calc" class="page active">
        <div class="card">
            <h2>仓位计算</h2>
            <div class="input-group">
                <label>固定亏损 (U)</label>
                <input type="number" id="fixedLoss" value="10" placeholder="0.00" oninput="calculate()">
            </div>
            <div class="input-group">
                <label>止损比例 (%)</label>
                <input type="number" id="lossRatio" value="4.2" placeholder="0.00" oninput="calculate()">
            </div>
            
            <div class="result-display">
                <div class="result-label">建议开仓价值 (U)</div>
                <div class="result-val" id="positionResult">0.00</div>
            </div>
        </div>
        
        <div class="card" style="background:#fff9f0; border:1px solid #ffeeba;">
            <h2 style="color:#b58900;">💡 提示</h2>
            <p style="font-size:13px; color:#666; margin:0;">
                输入止损后，系统会自动计算建议仓位，确保每次交易亏损固定。
            </p>
        </div>
    </div>

    <div id="page-log" class="page">
        <div class="file-upload-box">
            <span>📂 点击上传 Excel CSV</span>
            <input type="file" id="csvFileInput" accept=".csv">
        </div>

        <div id="log-list">
            <div style="text-align:center; color:#999; margin-top:50px;">
                暂无数据，请先上传文件
            </div>
        </div>
    </div>

</div>

<div class="tab-bar">
    <div class="tab-item active" onclick="switchPage('calc', this)">
        <span class="tab-icon">🧮</span>
        计算器
    </div>
    <div class="tab-item" onclick="switchPage('log', this)">
        <span class="tab-icon">📝</span>
        交易日志
    </div>
</div>

<script>
    // --- 页面切换逻辑 ---
    function switchPage(pageId, btn) {
        // 隐藏所有页面
        document.querySelectorAll('.page').forEach(el => el.classList.remove('active'));
        // 显示目标页面
        document.getElementById('page-' + pageId).classList.add('active');
        
        // 更新底部Tab状态
        document.querySelectorAll('.tab-item').forEach(el => el.classList.remove('active'));
        btn.classList.add('active');
    }

    // --- 计算器逻辑 (实时计算) ---
    function calculate() {
        const loss = parseFloat(document.getElementById('fixedLoss').value);
        const ratio = parseFloat(document.getElementById('lossRatio').value);
        
        const resultEl = document.getElementById('positionResult');
        
        if (loss > 0 && ratio > 0) {
            const pos = loss / (ratio / 100);
            resultEl.innerText = pos.toFixed(2);
            resultEl.style.color = '#000';
        } else {
            resultEl.innerText = "0.00";
            resultEl.style.color = '#ccc';
        }
    }
    // 初始化计算一次
    calculate();

    // --- CSV 处理逻辑 ---
    document.getElementById('csvFileInput').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        // 修改提示文字
        this.previousElementSibling.innerText = "✅ 已选择: " + file.name;

        const reader = new FileReader();
        reader.onload = function(e) {
            renderLog(e.target.result);
        };
        reader.readAsText(file);
    });

    function renderLog(csvText) {
        const listContainer = document.getElementById('log-list');
        listContainer.innerHTML = ''; // 清空

        const lines = csvText.split('\n');
        
        // 1. 寻找表头 (包含 "交易日期" 和 "货币对" 的那一行)
        let startIndex = -1;
        for(let i=0; i<lines.length; i++) {
            if (lines[i].includes('交易日期') && lines[i].includes('货币对')) {
                startIndex = i + 1; // 数据从下一行开始
                break;
            }
        }

        if (startIndex === -1) {
            alert('无法识别文件格式，请确保是正确的 CSV 文件');
            return;
        }

        let count = 0;

        // 2. 遍历数据行
        for (let i = startIndex; i < lines.length; i++) {
            const row = lines[i].trim();
            if (!row) continue; // 跳过空行

            // 简单逗号分割
            const cols = row.split(',');

            // 确保列数足够 (防止读取到末尾的空行)
            if (cols.length > 4) {
                // 解析数据
                const date = cols[0];
                const currency = cols[1];
                const signal = cols[2];
                const profitR = cols[3];
                const profitU = parseFloat(cols[4]);
                
                // 格式化收益颜色
                let profitClass = 'text-gray';
                let profitDisplay = cols[4];
                if (!isNaN(profitU)) {
                    if (profitU > 0) { profitClass = 'text-green'; profitDisplay = "+" + profitU; }
                    else if (profitU < 0) { profitClass = 'text-red'; }
                }

                // 生成卡片 HTML
                const cardHTML = `
                    <div class="tx-card">
                        <div class="tx-header">
                            <span class="tx-currency">${currency}</span>
                            <span class="tx-date">${date}</span>
                        </div>
                        <div class="tx-signal">
                            ${signal}
                        </div>
                        <div class="tx-stats">
                            <div class="stat-item">
                                <div class="stat-label">获利 R</div>
                                <div class="stat-val">${profitR}</div>
                            </div>
                            <div class="stat-item">
                                <div class="stat-label">收益 (U)</div>
                                <div class="stat-val ${profitClass}">${profitDisplay}</div>
                            </div>
                        </div>
                    </div>
                `;
                listContainer.insertAdjacentHTML('beforeend', cardHTML);
                count++;
            }
        }
        
        if (count === 0) {
            listContainer.innerHTML = '<div style="text-align:center; color:#999; margin-top:20px;">没有找到有效数据</div>';
        }
    }
</script>

</body>
</html>
