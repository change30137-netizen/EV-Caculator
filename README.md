<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>電壓降檢討自動化系統 - Terry CTO</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- PDF 產生必備函式庫 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        .input-card { @apply bg-white p-5 rounded-xl border border-gray-200 shadow-sm transition-all hover:shadow-md; }
        .label-text { @apply block text-xs font-bold text-gray-500 uppercase tracking-wider mb-2; }
        .input-field { @apply w-full bg-gray-50 border border-gray-300 rounded-lg px-3 py-2 text-lg font-semibold text-slate-800 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:bg-white; }
        .formula-display { @apply mt-4 p-3 bg-slate-100 rounded-lg border-l-4 border-slate-400 font-mono text-sm text-slate-700 break-words; }
        .status-badge { @apply px-3 py-1 rounded-full text-xs font-bold transition-all duration-300; }
        .pending { @apply bg-gray-100 text-gray-400; }
        .calculated { @apply bg-blue-100 text-blue-700; }
    </style>
</head>
<body class="bg-slate-50 min-h-screen pb-20">

    <!-- 導航欄 -->
    <nav class="bg-slate-900 text-white p-4 sticky top-0 z-50 shadow-lg">
        <div class="max-w-5xl mx-auto flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold">電壓降自動化檢討</h1>
                <p class="text-[10px] text-slate-400">Terry CTO 內部技術規範版本</p>
            </div>
            <div class="flex gap-2">
                <button onclick="downloadPDF()" class="bg-white/10 hover:bg-white/20 px-4 py-2 rounded text-sm font-medium transition">下載 PDF 報告</button>
            </div>
        </div>
    </nav>

    <main class="max-w-5xl mx-auto p-4 md:p-8 space-y-8" id="report-container">
        
        <!-- 區間一 -->
        <div class="input-card border-l-8 border-l-blue-500">
            <div class="flex justify-between items-start mb-4">
                <h2 class="text-lg font-bold text-slate-800">1. [受電開關 → 錶前開關] <span class="text-sm font-normal text-slate-500">(單相三線)</span></h2>
                <span id="badge1" class="status-badge pending">待計算</span>
            </div>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div>
                    <label class="label-text">電流 (A)</label>
                    <input type="number" id="s1_i" value="400" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">距離 (m)</label>
                    <input type="number" id="s1_l" value="15" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">R (Ω/km)</label>
                    <input type="number" id="s1_r" value="0.0739" step="0.0001" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">電壓 (V)</label>
                    <input type="number" id="s1_v" value="110" class="input-field" oninput="updatePreview()">
                </div>
            </div>
            <div id="s1_formula" class="formula-display">填入數值以預覽算式...</div>
        </div>

        <!-- 區間二 -->
        <div class="input-card border-l-8 border-l-emerald-500">
            <div class="flex justify-between items-start mb-4">
                <h2 class="text-lg font-bold text-slate-800">2. [錶前開關 → 錶後開關] <span class="text-sm font-normal text-slate-500">(單相三線)</span></h2>
                <span id="badge2" class="status-badge pending">待計算</span>
            </div>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div>
                    <label class="label-text">電流 (A)</label>
                    <input type="number" id="s2_i" value="75" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">距離 (m)</label>
                    <input type="number" id="s2_l" value="5" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">R (Ω/km)</label>
                    <input type="number" id="s2_r" value="0.378" step="0.0001" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">電壓 (V)</label>
                    <input type="number" id="s2_v" value="110" class="input-field" oninput="updatePreview()">
                </div>
            </div>
            <div id="s2_formula" class="formula-display">填入數值以預覽算式...</div>
        </div>

        <!-- 區間三 -->
        <div class="input-card border-l-8 border-l-orange-500">
            <div class="flex justify-between items-start mb-4">
                <h2 class="text-lg font-bold text-slate-800">3. [錶後開關 → CH1] <span class="text-sm font-normal text-slate-500">(單相二線)</span></h2>
                <span id="badge3" class="status-badge pending">待計算</span>
            </div>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div>
                    <label class="label-text">電流 (A)</label>
                    <input type="number" id="s3_i" value="32" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">距離 (m)</label>
                    <input type="number" id="s3_l" value="45" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">R (Ω/km)</label>
                    <input type="number" id="s3_r" value="1.4533" step="0.0001" class="input-field" oninput="updatePreview()">
                </div>
                <div>
                    <label class="label-text">電壓 (V)</label>
                    <input type="number" id="s3_v" value="220" class="input-field" oninput="updatePreview()">
                </div>
            </div>
            <div id="s3_formula" class="formula-display">填入數值以預覽算式...</div>
        </div>

        <!-- 計算按鈕 -->
        <div class="py-4">
            <button onclick="calculateAll()" id="btn-calc" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-5 rounded-2xl text-xl shadow-xl transition transform active:scale-95 flex items-center justify-center gap-3">
                <span>⚡</span> 開始執行檢討計算
            </button>
        </div>

        <!-- 總結結果 (預設隱藏或灰階，計算後才亮起) -->
        <div id="result-section" class="bg-slate-200 rounded-2xl p-8 text-slate-500 shadow-inner transition-all duration-500">
            <div class="grid md:grid-cols-2 gap-8 mb-8">
                <div>
                    <h3 class="text-xs font-bold uppercase mb-2">受電開關 -> 錶後壓降 (1+2)</h3>
                    <div class="text-4xl font-mono" id="sum_12">--- %</div>
                    <p class="text-xs mt-2" id="check_12">目標：< 2%</p>
                </div>
                <div>
                    <h3 class="text-xs font-bold uppercase mb-2">總壓降檢討 (1+2+3)</h3>
                    <div class="text-4xl font-mono" id="sum_all">--- %</div>
                    <p class="text-xs mt-2" id="check_all">目標：< 5%</p>
                </div>
            </div>
            <div id="verdict_box" class="p-4 rounded-xl text-center text-xl font-bold bg-white/50 border-2 border-dashed border-slate-300">
                請點擊上方按鈕執行檢討
            </div>
        </div>
    </main>

    <script>
        // 取得數值工具
        const getVal = (id) => parseFloat(document.getElementById(id).value) || 0;

        // 僅更新算式預覽 (不計算結果)
        function updatePreview() {
            // 重置狀態
            document.querySelectorAll('.status-badge').forEach(el => {
                el.innerText = '已變動';
                el.className = 'status-badge bg-yellow-100 text-yellow-700';
            });
            document.getElementById('result-section').className = "bg-slate-200 rounded-2xl p-8 text-slate-500 shadow-inner";
            document.getElementById('verdict_box').innerText = "數值已變動，請重新計算";

            // 預覽 1
            const i1 = getVal('s1_i'), l1 = getVal('s1_l'), r1 = getVal('s1_r'), v1 = getVal('s1_v');
            document.getElementById('s1_formula').innerHTML = `預覽算式: ${i1} x ${l1} x (${r1} x 1 + 0) / 1000 / ${v1} = ? %`;
            
            // 預覽 2
            const i2 = getVal('s2_i'), l2 = getVal('s2_l'), r2 = getVal('s2_r'), v2 = getVal('s2_v');
            document.getElementById('s2_formula').innerHTML = `預覽算式: ${i2} x ${l2} x (${r2} x 1 + 0) / 1000 / ${v2} = ? %`;

            // 預覽 3
            const i3 = getVal('s3_i'), l3 = getVal('s3_l'), r3 = getVal('s3_r'), v3 = getVal('s3_v');
            document.getElementById('s3_formula').innerHTML = `預覽算式: 2 x ${i3} x ${l3} x (${r3} x 1 + 0) / 1000 / ${v3} = ? %`;
        }

        // 正式執行計算
        function calculateAll() {
            // 1. 區間一 (1PH3W)
            const i1 = getVal('s1_i'), l1 = getVal('s1_l'), r1 = getVal('s1_r'), v1 = getVal('s1_v');
            const vd1 = (i1 * l1 * r1) / 1000 / v1 * 100;
            document.getElementById('s1_formula').innerHTML = `VD% = ${i1} x ${l1} x (${r1} x 1 + 0) / 1000 / ${v1} = <strong>${vd1.toFixed(3)}%</strong>`;
            document.getElementById('badge1').innerText = '計算完成';
            document.getElementById('badge1').className = 'status-badge bg-blue-100 text-blue-700';
            
            // 2. 區間二 (1PH3W)
            const i2 = getVal('s2_i'), l2 = getVal('s2_l'), r2 = getVal('s2_r'), v2 = getVal('s2_v');
            const vd2 = (i2 * l2 * r2) / 1000 / v2 * 100;
            document.getElementById('s2_formula').innerHTML = `VD% = ${i2} x ${l2} x (${r2} x 1 + 0) / 1000 / ${v2} = <strong>${vd2.toFixed(3)}%</strong>`;
            document.getElementById('badge2').innerText = '計算完成';
            document.getElementById('badge2').className = 'status-badge bg-emerald-100 text-emerald-700';

            // 3. 區間三 (1PH2W -> 系數 2)
            const i3 = getVal('s3_i'), l3 = getVal('s3_l'), r3 = getVal('s3_r'), v3 = getVal('s3_v');
            const vd3 = (2 * i3 * l3 * r3) / 1000 / v3 * 100;
            document.getElementById('s3_formula').innerHTML = `VD% = 2 x ${i3} x ${l3} x (${r3} x 1 + 0) / 1000 / ${v3} = <strong>${vd3.toFixed(3)}%</strong> < 3%`;
            document.getElementById('badge3').innerText = '計算完成';
            document.getElementById('badge3').className = 'status-badge bg-orange-100 text-orange-700';

            // 總計
            const s12 = vd1 + vd2;
            const sall = vd1 + vd2 + vd3;

            // 更新 UI 顯示
            const resultSection = document.getElementById('result-section');
            resultSection.className = "bg-slate-800 rounded-2xl p-8 text-white shadow-2xl transition-all duration-500";
            
            document.getElementById('sum_12').innerText = `${s12.toFixed(3)}%`;
            document.getElementById('sum_all').innerText = `${sall.toFixed(3)}%`;

            // 判定
            const vBox = document.getElementById('verdict_box');
            const is12Pass = s12 < 2;
            const isAllPass = sall < 5;

            document.getElementById('check_12').innerHTML = `目標：< 2% ${is12Pass ? '✅' : '❌'}`;
            document.getElementById('check_all').innerHTML = `目標：< 5% ${isAllPass ? '✅' : '❌'}`;

            if (is12Pass && isAllPass) {
                vBox.innerText = "⭐ 檢討合格：各項指標均符合規範";
                vBox.className = "p-4 rounded-xl text-center text-xl font-bold bg-emerald-500 text-white shadow-lg animate-bounce";
                setTimeout(() => vBox.classList.remove('animate-bounce'), 1000);
            } else {
                vBox.innerText = "⚠️ 警告：電壓降超出法規限制";
                vBox.className = "p-4 rounded-xl text-center text-xl font-bold bg-red-600 text-white shadow-lg";
            }
        }

        async function downloadPDF() {
            const container = document.getElementById('report-container');
            // 先執行一次計算確保報告內容完整
            calculateAll();
            
            const canvas = await html2canvas(container, { scale: 2 });
            const imgData = canvas.toDataURL('image/png');
            const { jsPDF } = window.jspdf;
            const pdf = new jsPDF('p', 'mm', 'a4');
            const pdfWidth = pdf.internal.pageSize.getWidth();
            const pdfHeight = (canvas.height * pdfWidth) / canvas.width;
            pdf.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight);
            pdf.save(`電壓降檢討報告_${new Date().toLocaleDateString()}.pdf`);
        }

        // 啟動初始化預覽
        window.onload = updatePreview;
    </script>
</body>
</html>
