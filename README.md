<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>雙語加班費計算機 | เครื่องคำนวณค่าล่วงเวลา</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&family=Sarabun:wght@400;500;700&display=swap');
        body {
            font-family: 'Noto Sans TC', 'Sarabun', sans-serif;
            background-color: #f3f4f6;
        }
        /* 隱藏數字輸入框的上下箭頭 */
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { 
            -webkit-appearance: none; 
            margin: 0; 
        }
    </style>
</head>
<body class="min-h-screen p-4 md:p-8 flex items-center justify-center">

    <div class="max-w-5xl w-full bg-white rounded-2xl shadow-xl overflow-hidden">
        
        <!-- Header -->
        <div class="bg-blue-600 p-6 text-white text-center">
            <h1 class="text-2xl md:text-3xl font-bold mb-2">多項目加班費計算機</h1>
            <h2 class="text-base md:text-lg opacity-90">เครื่องคำนวณค่าล่วงเวลาหลายรายการ</h2>
        </div>

        <div class="p-4 md:p-8 space-y-6">
            
            <!-- Base Salary Input & Global Info -->
            <div class="bg-gray-50 rounded-xl p-5 border border-gray-200 grid grid-cols-1 md:grid-cols-3 gap-4 items-center">
                <div>
                    <label class="block text-gray-700 font-bold mb-1">
                        基本月薪 <span class="text-sm font-normal text-gray-500 ml-1">เงินเดือนพื้นฐาน</span>
                    </label>
                    <div class="relative">
                        <span class="absolute left-3 top-2.5 text-gray-500">$</span>
                        <input type="number" id="baseSalary" value="" placeholder="例如: 30000" class="w-full pl-8 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition">
                    </div>
                </div>
                
                <div class="text-center md:text-left md:pl-4">
                    <p class="text-sm text-gray-500">換算時薪 (ค่าจ้างรายชั่วโมง):</p>
                    <p class="text-2xl font-bold text-gray-800 font-mono">$ <span id="hourlyWageDisplay">0</span></p>
                    <p class="text-xs text-gray-400 mt-1">計算公式：月薪 ÷ 240 (เงินเดือน ÷ 240)</p>
                </div>

                <div class="text-right">
                    <button type="button" onclick="addRow()" class="w-full md:w-auto bg-green-600 hover:bg-green-700 text-white font-bold py-2.5 px-6 rounded-lg shadow-md transition flex items-center justify-center gap-2 ml-auto">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" /></svg>
                        <span>新增加班項目 / เพิ่มรายการ</span>
                    </button>
                </div>
            </div>

            <!-- Overtime Rows Container (Desktop View & Mobile Cards) -->
            <div class="overflow-x-auto">
                <!-- Table for larger screens -->
                <table class="w-full min-w-[800px] text-left border-collapse hidden md:table">
                    <thead>
                        <tr class="bg-gray-100 text-gray-700 uppercase text-sm leading-normal border-b border-gray-200">
                            <th class="py-3 px-4 font-bold text-center w-32">加班類型<br><span class="text-xs font-normal text-gray-400">ประเภทวัน</span></th>
                            <th class="py-3 px-4 font-bold text-center w-32">天數<br><span class="text-xs font-normal text-gray-400">จำนวนวัน</span></th>
                            <th class="py-3 px-4 font-bold text-center w-36">每日小時<br><span class="text-xs font-normal text-gray-400">ชั่วโมง/วัน</span></th>
                            <th class="py-3 px-4 font-bold text-right w-40">加班費金額<br><span class="text-xs font-normal text-gray-400">ค่าล่วงเวลา</span></th>
                            <th class="py-3 px-4 font-bold text-right w-44">獎金 (中餐/宵夜)<br><span class="text-xs font-normal text-gray-400">โบนัสอาหาร</span></th>
                            <th class="py-3 px-4 font-bold text-right w-40">項目小計<br><span class="text-xs font-normal text-gray-400">รวมรายวัน</span></th>
                            <th class="py-3 px-4 font-bold text-center w-28">操作<br><span class="text-xs font-normal text-gray-400">จัดการ</span></th>
                        </tr>
                    </thead>
                    <tbody id="rowContainer" class="text-gray-600 text-sm font-light">
                        <!-- Dynamic rows will be inserted here -->
                    </tbody>
                </table>

                <!-- Mobile cards container -->
                <div id="mobileCardContainer" class="md:hidden space-y-4">
                    <!-- Dynamic cards for mobile will be inserted here -->
                </div>
            </div>

            <!-- Global Summary Card -->
            <div class="bg-blue-50 rounded-xl p-6 border border-blue-200">
                <h3 class="text-lg font-bold text-blue-900 mb-4 border-b border-blue-200 pb-2 flex justify-between items-center">
                    <span>總計結果加總 (總結算)</span>
                    <span class="text-sm font-normal text-blue-700">ยอดรวมทั้งหมด</span>
                </h3>
                
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 text-center">
                    
                    <!-- 總加班費 -->
                    <div class="bg-white p-4 rounded-lg shadow-sm border border-blue-100">
                        <div class="text-sm font-bold text-gray-500">總加班費加總</div>
                        <div class="text-xs text-gray-400">รวมค่าล่วงเวลาทั้งหมด</div>
                        <div class="text-2xl font-bold text-blue-600 font-mono mt-1">$ <span id="globalOtPay">0</span></div>
                    </div>

                    <!-- 總獎金 -->
                    <div class="bg-white p-4 rounded-lg shadow-sm border border-blue-100">
                        <div class="text-sm font-bold text-gray-500">總獎金加總 (中餐+宵夜)</div>
                        <div class="text-xs text-gray-400">รวมโบนัสทั้งหมด</div>
                        <div class="text-2xl font-bold text-orange-500 font-mono mt-1">$ <span id="globalBonus">0</span></div>
                    </div>

                    <!-- 大總計 (加班費+獎金) -->
                    <div class="bg-blue-600 p-4 rounded-lg shadow-md text-white">
                        <div class="text-sm font-bold opacity-90">大計總金額 (加班費 + 獎金)</div>
                        <div class="text-xs opacity-75">ยอดรวมสุทธิทั้งหมด</div>
                        <div class="text-3xl font-bold font-mono mt-1">$ <span id="globalTotal">0</span></div>
                    </div>

                </div>
            </div>

            <!-- Rules & Explanation -->
            <div class="bg-gray-50 rounded-xl p-4 border border-gray-200 text-xs text-gray-500 leading-relaxed space-y-2">
                <p class="font-bold text-gray-700">計算規則標準說明 / รายละเอียดกฎเกณฑ์ :</p>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
                    <p><strong>平日加班規則 (วันธรรมดา):</strong><br>
                        - 第 1~2 小時：時薪 × 1.33<br>
                        - 第 3 小時起：時薪 × 1.66<br>
                        - 加班滿 5 小時或以上：加發 100 宵夜獎金 (ค่าอาหารดึก)
                    </p>
                    <p><strong>假日加班規則 (วันหยุด):</strong><br>
                        - 第 1~2 小時：時薪 × 1.33<br>
                        - 第 3 小時起：時薪 × 1.67<br>
                        - 加班滿 5 小時或以上：加發 100 中餐獎金 (ค่าอาหารกลางวัน)
                    </p>
                </div>
            </div>

        </div>
    </div>

    <script>
        // 用於追蹤項目列的 ID
        let rowIdCounter = 0;
        let rows = [];

        window.onload = function() {
            // 綁定薪資輸入事件
            document.getElementById('baseSalary').addEventListener('input', calculateAll);
            
            // 已移除預設載入資料，保持完全空白。
            // 使用者點擊「新增加班項目」按鈕即可新增項目。
        };

        // 新增一行加班計算
        function addRow(dayType = 'weekday', days = '', hours = '') {
            const id = rowIdCounter++;
            rows.push(id);

            // 1. 建立桌上型電腦版表格列 (Desktop Row)
            const tbody = document.getElementById('rowContainer');
            const tr = document.createElement('tr');
            tr.id = `row-${id}`;
            tr.className = "border-b border-gray-100 hover:bg-gray-50 transition";
            tr.innerHTML = `
                <td class="py-3 px-4 text-center">
                    <select id="dayType-${id}" onchange="calculateAll()" class="w-full px-2 py-1.5 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm">
                        <option value="weekday" ${dayType === 'weekday' ? 'selected' : ''}>平日 วันธรรมดา</option>
                        <option value="holiday" ${dayType === 'holiday' ? 'selected' : ''}>假日 วันหยุด</option>
                    </select>
                </td>
                <td class="py-3 px-4 text-center">
                    <div class="relative">
                        <input type="number" id="days-${id}" value="${days}" placeholder="天數" min="1" oninput="calculateAll()" class="w-full text-center px-2 py-1.5 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm">
                    </div>
                </td>
                <td class="py-3 px-4 text-center">
                    <div class="relative">
                        <input type="number" id="hours-${id}" value="${hours}" placeholder="小時" min="0.5" step="0.5" oninput="calculateAll()" class="w-full text-center px-2 py-1.5 border border-gray-300 rounded focus:ring-1 focus:ring-blue-500 text-sm">
                    </div>
                </td>
                <td class="py-3 px-4 text-right font-mono font-medium text-blue-600 text-base">
                    $ <span id="otDisplay-${id}">0</span>
                </td>
                <td class="py-3 px-4 text-right">
                    <div class="font-mono font-medium text-orange-500 text-base">$ <span id="bonusDisplay-${id}">0</span></div>
                    <div class="text-[10px] text-gray-400" id="bonusLabel-${id}">-</div>
                </td>
                <td class="py-3 px-4 text-right font-mono font-bold text-gray-800 text-base">
                    $ <span id="rowTotalDisplay-${id}">0</span>
                </td>
                <td class="py-3 px-4 text-center">
                    <div class="flex items-center justify-center gap-1.5">
                        <button type="button" onclick="cloneRow(${id})" title="複製" class="text-blue-500 hover:text-blue-700 p-1 hover:bg-blue-50 rounded transition">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7v8a2 2 0 002 2h6M8 7V5a2 2 0 012-2h4.586a1 1 0 01.707.293l4.414 4.414a1 1 0 01.293.707V15a2 2 0 01-2 2h-2M8 7H6a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2v-2" /></svg>
                        </button>
                        <button type="button" onclick="deleteRow(${id})" title="刪除" class="text-red-500 hover:text-red-700 p-1 hover:bg-red-50 rounded transition">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
                        </button>
                    </div>
                </td>
            `;
            tbody.appendChild(tr);

            // 2. 建立手機板卡片 (Mobile Card)
            const mobileContainer = document.getElementById('mobileCardContainer');
            const card = document.createElement('div');
            card.id = `card-${id}`;
            card.className = "bg-white p-4 rounded-xl shadow-sm border border-gray-200 space-y-3 relative";
            card.innerHTML = `
                <div class="flex justify-between items-center border-b pb-2">
                    <span class="font-bold text-gray-700">加班項目 / รายการ</span>
                    <div class="flex items-center gap-2">
                        <button type="button" onclick="cloneRow(${id})" class="text-blue-500 text-xs px-2 py-1 bg-blue-50 rounded hover:bg-blue-100 flex items-center gap-1">
                            複製 คัดลอก
                        </button>
                        <button type="button" onclick="deleteRow(${id})" class="text-red-500 text-xs px-2 py-1 bg-red-50 rounded hover:bg-red-100 flex items-center gap-1">
                            刪除 ลบ
                        </button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs text-gray-500 mb-1">加班類型 / ประเภทวัน</label>
                        <select id="dayType-mob-${id}" onchange="syncAndCalc(${id}, 'dayType')" class="w-full px-2 py-1 border border-gray-300 rounded text-sm">
                            <option value="weekday" ${dayType === 'weekday' ? 'selected' : ''}>平日 วันธรรมดา</option>
                            <option value="holiday" ${dayType === 'holiday' ? 'selected' : ''}>假日 วันหยุด</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs text-gray-500 mb-1">天數 / จำนวนวัน</label>
                        <input type="number" id="days-mob-${id}" value="${days}" placeholder="天數" min="1" oninput="syncAndCalc(${id}, 'days')" class="w-full px-2 py-1 border border-gray-300 rounded text-sm text-center">
                    </div>
                    <div class="col-span-2">
                        <label class="block text-xs text-gray-500 mb-1">每日加班時數 / ชั่วโมงต่อวัน</label>
                        <input type="number" id="hours-mob-${id}" value="${hours}" placeholder="小時" min="0.5" step="0.5" oninput="syncAndCalc(${id}, 'hours')" class="w-full px-2 py-1 border border-gray-300 rounded text-sm text-center">
                    </div>
                </div>
                <div class="bg-gray-50 p-3 rounded-lg grid grid-cols-3 gap-1 text-center text-xs">
                    <div>
                        <div class="text-gray-400">加班金額 / ค่าโอที</div>
                        <div class="font-bold font-mono text-blue-600 mt-1">$ <span id="otDisplay-mob-${id}">0</span></div>
                    </div>
                    <div>
                        <div class="text-gray-400">獎金 / โบนัส</div>
                        <div class="font-bold font-mono text-orange-500 mt-1">$ <span id="bonusDisplay-mob-${id}">0</span></div>
                    </div>
                    <div>
                        <div class="text-gray-700 font-bold">小計 / รวม</div>
                        <div class="font-bold font-mono text-gray-900 mt-1">$ <span id="rowTotalDisplay-mob-${id}">0</span></div>
                    </div>
                </div>
            `;
            mobileContainer.appendChild(card);

            calculateAll();
        }

        // 刪除項目列
        function deleteRow(id) {
            // 刪除桌機版列
            const row = document.getElementById(`row-${id}`);
            if (row) row.remove();

            // 刪除手機版卡片
            const card = document.getElementById(`card-${id}`);
            if (card) card.remove();

            // 從陣列中移除
            rows = rows.filter(item => item !== id);
            calculateAll();
        }

        // 複製項目列
        function cloneRow(id) {
            const dayType = document.getElementById(`dayType-${id}`).value;
            const days = parseFloat(document.getElementById(`days-${id}`).value) || '';
            const hours = parseFloat(document.getElementById(`hours-${id}`).value) || '';
            addRow(dayType, days, hours);
        }

        // 當手機板的輸入框更動時，同步到桌機板並觸發重算
        function syncAndCalc(id, field) {
            const mobVal = document.getElementById(`${field}-mob-${id}`).value;
            document.getElementById(`${field}-${id}`).value = mobVal;
            calculateAll();
        }

        // 主計算函數
        function calculateAll() {
            const baseSalary = parseFloat(document.getElementById('baseSalary').value) || 0;
            const hourlyWage = baseSalary / 240;
            document.getElementById('hourlyWageDisplay').innerText = baseSalary > 0 ? Math.round(hourlyWage).toLocaleString() : '0';

            let totalGlobalOtPay = 0;
            let totalGlobalBonus = 0;

            // 巡迴計算每一個加班項目
            rows.forEach(id => {
                const type = document.getElementById(`dayType-${id}`).value;
                const days = parseFloat(document.getElementById(`days-${id}`).value) || 0;
                const hours = parseFloat(document.getElementById(`hours-${id}`).value) || 0;

                let dailyOtPay = 0;
                let dailyBonus = 0;
                let bonusLabel = '-';

                if (type === 'weekday') {
                    // 平日加班
                    if (hours > 0) {
                        if (hours <= 2) {
                            dailyOtPay = hours * hourlyWage * 1.33;
                        } else {
                            dailyOtPay = (2 * hourlyWage * 1.33) + ((hours - 2) * hourlyWage * 1.66);
                        }
                        if (hours >= 5) {
                            dailyBonus = 100;
                            bonusLabel = '宵夜獎金 (ค่าอาหารดึก)';
                        }
                    }
                } else {
                    // 假日加班
                    if (hours > 0) {
                        if (hours <= 2) {
                            dailyOtPay = hours * hourlyWage * 1.33;
                        } else {
                            dailyOtPay = (2 * hourlyWage * 1.33) + ((hours - 2) * hourlyWage * 1.67);
                        }
                        if (hours >= 5) {
                            dailyBonus = 100;
                            bonusLabel = '中餐獎金 (ค่าอาหารกลางวัน)';
                        }
                    }
                }

                const totalOtPay = Math.round(dailyOtPay * days);
                const totalBonus = Math.round(dailyBonus * days);
                const rowTotal = totalOtPay + totalBonus;

                totalGlobalOtPay += totalOtPay;
                totalGlobalBonus += totalBonus;

                // 渲染桌機版數值與標籤
                document.getElementById(`otDisplay-${id}`).innerText = totalOtPay.toLocaleString();
                document.getElementById(`bonusDisplay-${id}`).innerText = totalBonus.toLocaleString();
                document.getElementById(`bonusLabel-${id}`).innerText = bonusLabel;
                document.getElementById(`rowTotalDisplay-${id}`).innerText = rowTotal.toLocaleString();

                // 同步更新手機版數值與欄位值
                document.getElementById(`dayType-mob-${id}`).value = type;
                document.getElementById(`days-mob-${id}`).value = days === 0 ? '' : days;
                document.getElementById(`hours-mob-${id}`).value = hours === 0 ? '' : hours;

                document.getElementById(`otDisplay-mob-${id}`).innerText = totalOtPay.toLocaleString();
                document.getElementById(`bonusDisplay-mob-${id}`).innerText = totalBonus.toLocaleString();
                document.getElementById(`rowTotalDisplay-mob-${id}`).innerText = rowTotal.toLocaleString();
            });

            // 大總計輸出
            const globalTotal = totalGlobalOtPay + totalGlobalBonus;
            document.getElementById('globalOtPay').innerText = totalGlobalOtPay.toLocaleString();
            document.getElementById('globalBonus').innerText = totalGlobalBonus.toLocaleString();
            document.getElementById('globalTotal').innerText = globalTotal.toLocaleString();
        }
    </script>
</body>
</html>
