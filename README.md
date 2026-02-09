<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>交易助手</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --accent-color: #3498db;
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --success: #27ae60;
            --danger: #e74c3c;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            margin: 0;
            padding: 15px;
            background-color: var(--bg-color);
            color: #333;
            -webkit-tap-highlight-color: transparent;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            padding-bottom: 50px;
        }
        h1 {
            color: var(--primary-color);
            text-align: center;
            font-size: 1.5rem;
            margin-bottom: 20px;
        }
        h2 {
            font-size: 1.2rem;
            margin-top: 0;
            border-bottom: 1px solid #eee;
            padding-bottom: 10px;
            margin-bottom: 15px;
        }
        .card {
            background: var(--card-bg);
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.03);
            padding: 20px;
            margin-bottom: 20px;
        }
        
        /* 选项卡样式 - 移动端优化 */
        .tabs {
            display: flex;
            background: #e0e0e0;
            border-radius: 10px;
            padding: 4px;
            margin-bottom: 20px;
        }
        .tab-btn {
            flex: 1;
            background: transparent;
            border: none;
            padding: 12px 0;
            cursor: pointer;
            border-radius: 8px;
            font-size: 15px;
            font-weight: 500;
            color: #666;
            transition: all 0.3s ease;
        }
        .tab-btn.active {
            background: var(--card-bg);
            color: var(--accent-color);
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        .tab-content { display: none; }
        .tab-content.active { display: block; }

        /* 计算器表单 */
        .form-group {
            margin-bottom: 15px;
        }
        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            font-size: 0.9rem;
            color: #555;
        }
        .form-group input {
            width: 100%;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 8px;
            box-sizing: border-box;
            font-size: 16px; /* 防止iOS缩放 */
            outline: none;
        }
        .form-group input:focus {
            border-color: var(--accent-color);
        }
        .btn-primary {
            width: 100%;
            padding: 14px;
            background: var(--accent-color);
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
        }
        .btn-primary:active { opacity: 0.9; transform: scale(0.98); }

        .result-box {
            background: #e8f6f3;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            margin-top: 25px;
            border: 1px solid #d0ece7;
        }
        .result-value {
            font-size: 32px;
            color: var(--success);
            font-weight: 800;
            margin: 10px 0;
        }

        /* 响应式表格 (Card View) */
        table {
            width: 100%;
            border-collapse: collapse;
        }
        
        /* 默认桌面样式 */
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; }
        th { background-color: #f8f9fa; color: #666; font-size: 0.9rem; }
        
        .file-upload-wrapper {
            position: relative;
            overflow: hidden;
            display: inline-block;
            width: 100%;
        }
        .file-upload-btn {
            border: 2px dashed #bdc3c7;
            color: #7f8c8d;
            background-color: #fafafa;
            padding: 20px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: bold;
            text-align: center;
            cursor: pointer;
            display: block;
        }
        .file-upload-wrapper input[type=file] {
            font-size: 100px;
            position: absolute;
            left: 0;
            top: 0;
            opacity: 0;
            cursor: pointer;
            width: 100%;
            height: 100%;
        }

        .positive { color: var(--success); font-weight: bold; }
        .negative { color: var(--danger); font-weight: bold; }

        /* === 手机端适配核心代码 === */
        @media screen and (max-width: 600px) {
            /* 隐藏表头 */
            thead { display: none; }
            
            /* 表格行变成卡片 */
            tr {
                display: block;
                background: #fff;
                margin-bottom: 15px;
                border: 1px solid #eee;
                border-radius: 8px;
                box-shadow: 0 2px 5px rgba(0,0,0,0.05);
            }
            
            /* 单元格变成Flex布局 */
            td {
                display: flex;
                justify-content: space-between;
                align-items: center;
                padding: 12px 15px;
                border-bottom: 1px solid #f5f5f5;
                font-size: 14px;
            }
            
            td:last-child { border-bottom: none; }

            /* 使用 data-label 显示标题 */
            td::before {
                content: attr(data-label);
                font-weight: 600;
                color: #7f8c8d;
                margin-right: 15px;
            }
            
            /* 特殊处理：收益那一栏加大加粗 */
            td[data-label="收益 (U)"] {
                font-size: 16px;
            }
        }
    </style>
</head>
<body>

<div class="container">
    <h1>交易助手</h1>

    <div class="tabs">
        <button class="tab-btn active" onclick="showTab('calc')">仓位计算</button>
        <button class="tab-btn" onclick="showTab('log')">交易日志</button>
    </div>

    <div id="calc" class="tab-content active">
        <div class="card">
            <h2>快速计算器</h2>
            <div class="form-group">
                <label>固定亏损金额 (U)</label>
                <input type="number" id="fixedLoss" value="10" inputmode="decimal" placeholder="输入金额">
            </div>
            <div class="form-group">
                <label>止损比例 (%)</label>
                <input type="number" id="lossRatio" value="4.2" inputmode="decimal" placeholder="输入百分比">
            </div>
            <button class="btn-primary" onclick="calculatePosition()">计算仓位</button>
            
            <div class="result-box">
                <div style="font-size: 14px; color: #666;">建议开仓价值 (U)</div>
                <div class="result-value" id="positionResult">0.00</div>
                <div style="font-size: 12px; color: #999;">公式：亏损额 ÷ (止损比 ÷ 100)</div>
            </div>
        </div>
    </div>

    <div id="log" class="tab-content">
        <div class="card" style="padding: 15px;">
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
                <h2 style="margin:0; border:none;">交易记录</h2>
                <span style="font-size:12px; color:#999;" id="recordCount">0 条记录</span>
            </div>
            
            <div class="file-upload-wrapper">
                <div class="file-upload-btn" id="uploadText">点击上传 CSV 文件</div>
                <input type="file" id="csvFileInput" accept=".csv" />
            </div>

            <table id="logTable">
                <thead>
                    <tr>
                        <th>日期</th>
                        <th>货币</th>
                        <th>信号</th>
                        <th>获利R</th>
                        <th>收益 (U)</th>
                        <th>胜率</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td data-label="日期">2024-10-24</td>
                        <td data-label="货币">JASMY</td>
                        <td data-label="信号">区间底部</td>
                        <td data-label="获利R">0</td>
                        <td data-label="收益 (U)" class="negative">-0.27</td>
                        <td data-label="胜率">50%</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</div>

<script>
    // 切换标签页
    function showTab(tabId) {
        document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
        document.querySelectorAll('.tab-btn').forEach(el => el.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        event.target.classList.add('active');
    }

    // 计算仓位
    function calculatePosition() {
        const loss = parseFloat(document.getElementById('fixedLoss').value);
        const ratio = parseFloat(document.getElementById('lossRatio').value);
        
        if (loss && ratio) {
            const position = loss / (ratio / 100);
            document.getElementById('positionResult').innerText = position.toFixed(2);
        } else {
            document.getElementById('positionResult').innerText = "0.00";
        }
    }
    
    // 初始化计算
    calculatePosition();

    // CSV 处理逻辑
    document.getElementById('csvFileInput').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        document.getElementById('uploadText').innerText = "已选择: " + file.name;

        const reader = new FileReader();
        reader.onload = function(e) {
            parseCSV(e.target.result);
        };
        reader.readAsText(file);
    });

    function parseCSV(text) {
        const lines = text.split('\n');
        const tbody = document.querySelector('#logTable tbody');
        tbody.innerHTML = ''; 

        // 智能查找表头位置
        let dataStartIndex = 0;
        for(let i=0; i<lines.length; i++) {
            if(lines[i].includes('交易日期') && lines[i].includes('货币对')) {
                dataStartIndex = i + 1;
                break;
            }
        }

        let count = 0;
        for (let i = dataStartIndex; i < lines.length; i++) {
            const row = lines[i].trim();
            if (!row) continue;
            
            // 简单的逗号分割（复杂CSV可能需要正则处理引号）
            const cols = row.split(',');
            
            if (cols.length > 4) {
                const tr = document.createElement('tr');
                
                const profitU = parseFloat(cols[4]);
                const profitClass = profitU >= 0 ? 'positive' : 'negative';
                const profitText = isNaN(profitU) ? cols[4] : profitU;

                // 核心：data-label 属性用于手机端 CSS 显示标题
                tr.innerHTML = `
                    <td data-label="日期">${cols[0]}</td>
                    <td data-label="货币" style="font-weight:bold">${cols[1]}</td>
                    <td data-label="信号" style="font-size:12px; color:#666;">${cols[2]}</td>
                    <td data-label="获利R">${cols[3]}</td>
                    <td data-label="收益 (U)" class="${profitClass}">${profitText}</td>
                    <td data-label="胜率">${cols[9] || '-'}</td>
                `;
                tbody.appendChild(tr);
                count++;
            }
        }
        document.getElementById('recordCount').innerText = count + " 条记录";
    }
</script>

</body>
</html>
