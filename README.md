<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>交易日志与仓位管理</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --accent-color: #3498db;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: var(--bg-color);
            color: #333;
        }
        .container {
            max-width: 1000px;
            margin: 0 auto;
        }
        h1, h2 {
            color: var(--primary-color);
            text-align: center;
        }
        .card {
            background: var(--card-bg);
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            padding: 25px;
            margin-bottom: 25px;
        }
        
        /* 选项卡样式 */
        .tabs {
            display: flex;
            justify-content: center;
            margin-bottom: 20px;
        }
        .tab-btn {
            background: #e0e0e0;
            border: none;
            padding: 10px 20px;
            margin: 0 5px;
            cursor: pointer;
            border-radius: 5px;
            font-size: 16px;
        }
        .tab-btn.active {
            background: var(--accent-color);
            color: white;
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }

        /* 计算器样式 */
        .calc-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            align-items: end;
        }
        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
        }
        .form-group input {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .result-box {
            background: #e8f6f3;
            padding: 15px;
            border-radius: 4px;
            text-align: center;
            margin-top: 20px;
        }
        .result-value {
            font-size: 24px;
            color: #27ae60;
            font-weight: bold;
        }

        /* 表格样式 */
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            font-size: 14px;
        }
        th, td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }
        th {
            background-color: var(--primary-color);
            color: white;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        .file-upload {
            text-align: center;
            padding: 20px;
            border: 2px dashed #ccc;
            border-radius: 8px;
            margin-bottom: 15px;
        }
        .positive { color: green; font-weight: bold; }
        .negative { color: red; font-weight: bold; }
    </style>
</head>
<body>

<div class="container">
    <h1>交易管理看板</h1>

    <div class="tabs">
        <button class="tab-btn active" onclick="showTab('calc')">仓位计算器</button>
        <button class="tab-btn" onclick="showTab('log')">交易日志</button>
    </div>

    <div id="calc" class="tab-content active">
        <div class="card">
            <h2>仓位计算</h2>
            <div class="calc-grid">
                <div class="form-group">
                    <label>固定亏损金额 (U)</label>
                    <input type="number" id="fixedLoss" value="10" placeholder="例如: 10">
                </div>
                <div class="form-group">
                    <label>止损比例 (%)</label>
                    <input type="number" id="lossRatio" value="4.2" placeholder="例如: 4.2">
                </div>
                <div class="form-group">
                    <button onclick="calculatePosition()" style="width:100%; padding:10px; background:var(--accent-color); color:white; border:none; border-radius:4px; cursor:pointer;">计算</button>
                </div>
            </div>
            <div class="result-box">
                <div>建议开仓价值 (U)</div>
                <div class="result-value" id="positionResult">0.00</div>
                <div style="font-size: 12px; color: #666; margin-top: 5px;">公式：固定亏损 / (止损比例 / 100)</div>
            </div>
        </div>
    </div>

    <div id="log" class="tab-content">
        <div class="card">
            <h2>交易记录</h2>
            <div class="file-upload">
                <p>请上传 "Transaction log.xlsx - 日志.csv" 文件以加载数据</p>
                <input type="file" id="csvFileInput" accept=".csv" />
            </div>
            <div style="overflow-x: auto;">
                <table id="logTable">
                    <thead>
                        <tr>
                            <th>交易日期</th>
                            <th>货币对</th>
                            <th>操作信号</th>
                            <th>获利 R</th>
                            <th>收益 (U)</th>
                            <th>胜率 %</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr>
                            <td>2024-10-24</td>
                            <td>JASMY</td>
                            <td>区间底部、头肩底</td>
                            <td>0</td>
                            <td class="negative">-0.27</td>
                            <td>50</td>
                        </tr>
                        <tr>
                            <td>2024-10-29</td>
                            <td>BOME</td>
                            <td>突破区间底部上方PRZ</td>
                            <td>2</td>
                            <td class="positive">600</td>
                            <td>-</td>
                        </tr>
                    </tbody>
                </table>
            </div>
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
            // 核心公式：仓位 = 固定亏损 / (百分比 / 100)
            const position = loss / (ratio / 100);
            document.getElementById('positionResult').innerText = position.toFixed(2);
        } else {
            document.getElementById('positionResult').innerText = "0.00";
        }
    }

    // 页面加载时自动计算一次
    calculatePosition();

    // CSV 解析逻辑
    document.getElementById('csvFileInput').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;

        const reader = new FileReader();
        reader.onload = function(e) {
            const text = e.target.result;
            parseCSV(text);
        };
        reader.readAsText(file);
    });

    function parseCSV(text) {
        // 简单的CSV解析器
        const lines = text.split('\n');
        const tbody = document.querySelector('#logTable tbody');
        tbody.innerHTML = ''; // 清空现有数据

        // 根据你的文件结构，第一行可能是 "2024.01..." 这样的标题，第二行才是列名
        // 所以我们从数据行开始读取（假设数据从第3行开始，即索引2）
        // 你的文件：行0=总标题, 行1=列名, 行2=数据
        
        // 查找数据起始位置：找到包含"交易日期"的行作为表头参考，下一行开始是数据
        let dataStartIndex = 0;
        for(let i=0; i<lines.length; i++) {
            if(lines[i].includes('交易日期') && lines[i].includes('货币对')) {
                dataStartIndex = i + 1;
                break;
            }
        }

        for (let i = dataStartIndex; i < lines.length; i++) {
            const row = lines[i].trim();
            if (!row) continue;
            
            // 处理CSV逗号分隔（注意：如果单元格内有逗号会变复杂，这里假设简单逗号分隔）
            const cols = row.split(',');
            
            // 映射列：日期(0), 货币对(1), 信号(2), 获利R(3), 收益U(4), ..., 胜率(9)
            // 请根据实际CSV列顺序调整索引
            if (cols.length > 4) {
                const tr = document.createElement('tr');
                
                // 收益U formatting
                const profitU = parseFloat(cols[4]);
                const profitClass = profitU >= 0 ? 'positive' : 'negative';
                
                tr.innerHTML = `
                    <td>${cols[0]}</td>
                    <td>${cols[1]}</td>
                    <td>${cols[2]}</td>
                    <td>${cols[3]}</td>
                    <td class="${profitClass}">${cols[4]}</td>
                    <td>${cols[9] || '-'}</td>
                `;
                tbody.appendChild(tr);
            }
        }
    }
</script>

</body>
</html>
