<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>雙語加班費計算機 | เครื่องคำนวณค่าล่วงเวลา</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&family=Sarabun:wght@400;500;700&display=swap');
        
        body {
            font-family: 'Noto Sans TC', 'Sarabun', sans-serif;
            background-color: #f8fafc;
            -webkit-tap-highlight-color: transparent; /* 移除手機上點擊時的藍色高亮 */
        }
        
        /* 隱藏數字輸入框的上下箭頭 */
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { 
            -webkit-appearance: none; 
            margin: 0; 
        }
        
        /* 優雅的手機點擊縮放微動畫 */
        .btn-active-scale:active {
            transform: scale(0.96);
        }
    </style>
</head>
<body class="min-h-screen pb-40">

    <!-- Top Bar / App Header -->
    <div class="sticky top-0 z-30 bg-gradient-to-r from-blue-600 to-indigo-600 text-white shadow-md">
        <div class="max-w-md mx-auto px-4 py-4 flex items-center justify-between">
            <div>
                <h1 class="text-xl font-bold tracking-tight">加班費計算機</h1>
                <p class="text-xs text-blue-100 mt-0.5">เครื่องคำนวณค่าล่วงเวลา</p>
            </div>
            <button type="button" onclick="clearAll()" class="btn-active-scale transition bg-blue-700/50 hover:bg-blue-700 text-xs px-3 py-1.5 rounded-lg border border-blue-400/30 flex items-center gap-1">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                </svg>
                <span>清空重填 / ล้างข้อมูล</span>
            </button>
        </div>
    </div>

    <!-- Main Container -->
    <div class="max-w-md mx-auto px-4 py-5 space-y-5">

        <!-- Salary Configuration Card (Mobile Dashboard style) -->
        <div class="bg-white rounded-2xl p-4 shadow-sm border border-slate-100 space-y-4">
            <div>
                <div class="flex justify-between items-baseline mb-2">
                    <label class="block text-slate-800 font-bold text-sm">
                        基本月薪 <span class="text-xs font-normal text-slate-400 ml-1">เงินเดือนพื้นฐาน</span>
                    </label>
                    <span class="text-xs text-slate-400">公式：月薪 ÷ 240</span>
                </div>
                <div class="relative">
                    <span class="absolute left-4 top-3 text-slate-400 text-base font-medium">$</span>
                    <input type="number" id="baseSalary" oninput="updateBaseSalary()" placeholder="請輸入月薪 (เช่น 30000)" class="w-full pl-8 pr-4 py-3 bg-slate-50 border border-slate-200 rounded-xl text-base focus:bg-white focus:ring-2 focus:ring-blue-500 focus:border-blue-500 transition-all outline-none font-medium">
                </div>
                <!-- Quick Salary Suggestion Buttons for Fast Mobile Tapping -->
                <div class="flex gap-2 mt-3 overflow-x-auto pb-1">
                    <button type="button" onclick="quickSetSalary(29500)" class="btn-active-scale transition-all flex-shrink-0 bg-slate-100 hover:bg-blue-50 hover:text-blue-600 active:bg-blue-100 text-slate-600 text-xs px-3 py-1.5 rounded-lg border border-transparent font-medium">
                        $29,500 (基本薪)
                    </button>
                    <button type="button" onclick="quickSetSalary(30000)" class="btn-active-scale transition-all flex-shrink-0 bg-slate-100 hover:bg-blue-50 hover:text-blue-600 active:bg-blue-100 text-slate-600 text-xs px-3 py-1.5 rounded-lg border border-transparent font-medium">
                        $30,000
                    </button>
                    <button type="button" onclick="quickSetSalary(35000)" class="btn-active-scale transition-all flex-shrink-0 bg-slate-100 hover:bg-blue-50 hover:text-blue-600 active:bg-blue-100 text-slate-600 text-xs px-3 py-1.5 rounded-lg border border-transparent font-medium">
                        $35,000
                    </button>
                    <button type="button" onclick="quickSetSalary(40000)" class="btn-active-scale transition-all flex-shrink-0 bg-slate-100 hover:bg-blue-50 hover:text-blue-600 active:bg-blue-100 text-slate-600 text-xs px-3 py-1.5 rounded-lg border border-transparent font-medium">
                        $40,000
                    </button>
                </div>
            </div>

            <!-- Calculated Hourly Wage Box -->
            <div class="bg-blue-50/50 rounded-xl p-3 border border-blue-100/50 flex items-center justify-between">
                <div>
                    <div class="text-[11px] text-slate-400 font-bold uppercase tracking-wider">換算時薪 (รายชั่วโมง)</div>
                    <div class="text-[10px] text-slate-400">月薪除以 240 小時所得</div>
                </div>
                <div class="text-right">
                    <span class="text-2xl font-bold text-blue-700 font-mono">$<span id="hourlyWageDisplay">0</span></span>
                </div>
            </div>
        </div>

        <!-- OT Items Header & FAB-style Add Button -->
        <div class="flex items-center justify-between px-1">
            <div>
                <h3 class="font-bold text-slate-800 text-base">加班細項列表</h3>
                <p class="text-[11px] text-slate-400">รายการคิดค่าโอทีรายวัน</p>
            </div>
            <button type="button" onclick="addNewItem()" class="btn-active-scale transition-all bg-green-600 hover:bg-green-700 active:bg-green-800 text-white font-bold py-2.5 px-4 rounded-xl shadow-md shadow-green-100 text-sm flex items-center gap-1.5 border border-green-500">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 4v16m8-8H4" /></svg>
                <span>新增加班 / เพิ่มรายการ</span>
            </button>
        </div>

        <!-- Dynamic Cards Container -->
        <div id="cardsContainer" class="space-y-4">
            <!-- Dynamic item cards will be appended here -->
        </div>

        <!-- Empty State Placeholder (Correctly kept outside the cards container to prevent deletion and avoid JS Errors!) -->
        <div id="emptyPlaceholder" class="bg-white rounded-2xl py-12 px-6 border border-dashed border-slate-200 text-center space-y-3">
            <div class="w-14 h-14 bg-slate-50 rounded-full flex items-center justify-center mx-auto text-slate-300">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M12 9v3m0 0v3m0-3h3m-3 0H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
            </div>
            <div>
                <p class="text-slate-700 font-bold text-sm">尚未加入任何加班時段</p>
                <p class="text-xs text-slate-400 mt-1">แตะปุ่ม "新增加班" ด้านบนเพื่อเริ่มรายการแรก</p>
            </div>
        </div>

        <!-- Collapsible Rules Guide (For Saving Space on Mobile screens) -->
        <div class="bg-white rounded-2xl border border-slate-100 overflow-hidden shadow-sm">
            <button type="button" onclick="toggleRules()" class="w-full flex justify-between items-center p-4 bg-slate-50 hover:bg-slate-100/50 transition-colors">
                <div class="flex items-center gap-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                    <span class="font-bold text-slate-700 text-sm">計算規則詳細說明 / กฎเกณฑ์</span>
                </div>
                <svg id="rulesArrow" xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-slate-400 transform transition-transform duration-200" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" /></svg>
            </button>
            <div id="rulesContent" class="hidden p-4 border-t border-slate-50 text-xs text-slate-500 space-y-4 leading-relaxed">
                <div>
                    <h4 class="font-bold text-slate-800 flex items-center gap-1.5 mb-1">
                        <span class="w-1.5 h-3 bg-blue-500 rounded-full"></span>
                        平日加班規則 (วันธรรมดา)
                    </h4>
                    <p>第 1 ~ 2 小時：時薪 × 1.33</p>
                    <p>第 3 小時起：時薪 × 1.66</p>
                    <p class="text-indigo-600 font-medium mt-0.5">• 滿 2 小時或以上(含)：加發晚餐獎金 100 元 (อาหารเย็น)</p>
                    <p class="text-indigo-600 font-medium">• 滿 5 小時或以上(含)：再加發宵夜獎金 100 元 (共計 200 元)</p>
                </div>
                <div class="border-t border-slate-100 pt-3">
                    <h4 class="font-bold text-slate-800 flex items-center gap-1.5 mb-1">
                        <span class="w-1.5 h-3 bg-orange-500 rounded-full"></span>
                        假日加班規則 (วันหยุด)
                    </h4>
                    <p>第 1 ~ 8 小時：時薪 × 1.66</p>
                    <p>第 9 小時起：時薪 × 2.66</p>
                    <p class="text-indigo-600 font-medium mt-0.5">• 滿 5 小時或以上(含)：加發中餐獎金 100 元 (อาหารกลางวัน)</p>
                    <p class="text-indigo-600 font-medium">• 滿 10 小時或以上(含)：再加發晚餐獎金 100 元 (共計 200 元)</p>
                    <p class="text-indigo-600 font-medium">• 滿 13 小時或以上(含)：再加發宵夜獎金 100 元 (共計 300 元)</p>
                </div>
            </div>
        </div>

    </div>

    <!-- Sticky App-Style Checkout Bar (Sticky Bottom Sheet) -->
    <div class="fixed bottom-0 left-0 right-0 bg-white/95 backdrop-blur-md border-t border-slate-200/80 shadow-[0_-5px_15px_rgba(0,0,0,0.06)] z-40 pb-safe">
        <div class="max-w-md mx-auto px-4 py-3 space-y-3">
            
            <!-- Secondary breakdown toggler panel -->
            <div id="breakdownPanel" class="hidden grid grid-cols-2 gap-4 pb-2 text-xs border-b border-slate-100">
                <div>
                    <p class="text-slate-400">總加班費 (รวมค่าล่วงเวลา):</p>
                    <p class="text-base font-bold text-blue-600 font-mono mt-0.5">$<span id="globalOtPay">0</span></p>
                </div>
                <div class="border-l border-slate-100 pl-4">
                    <p class="text-slate-400">總獎金 (รวมโบนัสอาหาร):</p>
                    <p class="text-base font-bold text-orange-500 font-mono mt-0.5">$<span id="globalBonus">0</span></p>
                </div>
            </div>

            <!-- Grand total line -->
            <div class="flex items-center justify-between">
                <button type="button" onclick="toggleBreakdown()" class="btn-active-scale transition flex items-center gap-1 bg-slate-100 hover:bg-slate-200 text-slate-600 px-3 py-1.5 rounded-lg text-xs font-bold">
                    <span>明細 / รายละเอียด</span>
                    <svg id="breakdownArrow" xmlns="http://www.w3.org/2000/svg" class="h-3.5 w-3.5 transform transition-transform" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M5 15l7-7 7 7" /></svg>
                </button>

                <div class="text-right">
                    <div class="text-[10px] text-slate-400 font-bold uppercase tracking-wider">大計總金額 (ยอดสุทธิ)</div>
                    <div class="text-2xl font-black text-slate-900 font-mono flex items-center justify-end gap-1">
                        <span class="text-blue-600 text-lg">$</span>
                        <span id="globalTotal" class="text-blue-600">0</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 用於追蹤項目列的 ID
        let itemIdCounter = 0;
        let items = [];

        // 頁面初始化
        window.onload = function() {
            // 從空白狀態開始，不主動塞入預設列，呈現清爽介面。
            renderEmptyPlaceholder();
        };

        // 常用月薪快速設定按鈕
        function quickSetSalary(amount) {
            const input = document.getElementById('baseSalary');
            input.value = amount;
            updateBaseSalary();
        }

        // 月薪變動時更新與重算
        function updateBaseSalary() {
            calculateAll();
        }

        // 新增一個加班項目卡片
        function addNewItem() {
            // 隱藏空白預留提示
            document.getElementById('emptyPlaceholder').style.display = 'none';

            const id = itemIdCounter++;
            const item = {
                id: id,
                dayType: 'weekday', // weekday or holiday
                days: 1,
                hours: 1
            };
            items.push(item);

            // 建立卡片元件
            const container = document.getElementById('cardsContainer');
            const card = document.createElement('div');
            card.id = `card-${id}`;
            card.className = "bg-white rounded-2xl p-4 shadow-sm border border-slate-100 space-y-4 relative transition-all duration-200 overflow-hidden";
            
            // 卡片 HTML 模板
            card.innerHTML = `
                <!-- Card Header (Segmented control replacing clunky selects) -->
                <div class="flex items-center justify-between gap-3">
                    <div class="grid grid-cols-2 p-1 bg-slate-100 rounded-xl flex-1 max-w-[210px]">
                        <button type="button" id="toggle-weekday-${id}" onclick="setDayType(${id}, 'weekday')" class="py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale bg-white text-blue-700 shadow-sm">
                            平日 วันธรรมดา
                        </button>
                        <button type="button" id="toggle-holiday-${id}" onclick="setDayType(${id}, 'holiday')" class="py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale text-slate-500">
                            假日 วันหยุด
                        </button>
                    </div>
                    
                    <!-- Action controls -->
                    <div class="flex items-center gap-1">
                        <button type="button" onclick="cloneItem(${id})" title="複製" class="btn-active-scale w-9 h-9 flex items-center justify-center bg-slate-50 text-slate-500 hover:bg-blue-50 hover:text-blue-600 rounded-xl transition-all">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7v8a2 2 0 002 2h6M8 7V5a2 2 0 012-2h4.586a1 1 0 01.707.293l4.414 4.414a1 1 0 01.293.707V15a2 2 0 01-2 2h-2M8 7H6a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2v-2" />
                            </svg>
                        </button>
                        <button type="button" onclick="deleteItem(${id})" title="刪除" class="btn-active-scale w-9 h-9 flex items-center justify-center bg-red-50 text-red-500 hover:bg-red-100 rounded-xl transition-all">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                            </svg>
                        </button>
                    </div>
                </div>

                <!-- Input Steppers Section (Tap Targets) -->
                <div class="grid grid-cols-2 gap-4">
                    <!-- Days Stepper -->
                    <div class="space-y-1.5">
                        <label class="block text-xs text-slate-500 font-medium">
                            天數 <span class="text-[10px] text-slate-400">จำนวนวัน</span>
                        </label>
                        <div class="flex items-center justify-between bg-slate-50 border border-slate-200 rounded-xl p-1 h-12">
                            <button type="button" onclick="adjustValue(${id}, 'days', -1)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">-</button>
                            <input type="number" id="input-days-${id}" value="1" min="1" oninput="manualInputChange(${id}, 'days')" class="w-10 text-center bg-transparent border-none text-base font-bold text-slate-800 focus:outline-none">
                            <button type="button" onclick="adjustValue(${id}, 'days', 1)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">+</button>
                        </div>
                    </div>

                    <!-- Hours Stepper -->
                    <div class="space-y-1.5">
                        <label class="block text-xs text-slate-500 font-medium">
                            每日小時 <span class="text-[10px] text-slate-400">ชั่วโมง/วัน</span>
                        </label>
                        <div class="flex items-center justify-between bg-slate-50 border border-slate-200 rounded-xl p-1 h-12">
                            <button type="button" onclick="adjustValue(${id}, 'hours', -0.5)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">-</button>
                            <input type="number" id="input-hours-${id}" value="1" min="0.5" step="0.5" oninput="manualInputChange(${id}, 'hours')" class="w-10 text-center bg-transparent border-none text-base font-bold text-slate-800 focus:outline-none">
                            <button type="button" onclick="adjustValue(${id}, 'hours', 0.5)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">+</button>
                        </div>
                    </div>
                </div>

                <!-- Live Subtotal Inside Card -->
                <div class="bg-slate-50 rounded-xl p-3 grid grid-cols-3 gap-1 text-center">
                    <div>
                        <div class="text-[9px] text-slate-400 font-medium">加班費 / ค่าล่วงเวลา</div>
                        <div class="text-xs font-bold font-mono text-blue-600 mt-1">$<span id="otDisplay-${id}">0</span></div>
                    </div>
                    <div class="border-l border-slate-200/60">
                        <div class="text-[9px] text-slate-400 font-medium">獎金 / โบนัส</div>
                        <div class="text-xs font-bold font-mono text-orange-500 mt-0.5">$<span id="bonusDisplay-${id}">0</span></div>
                        <div class="text-[8px] text-slate-400 leading-none truncate" id="bonusLabel-${id}">-</div>
                    </div>
                    <div class="border-l border-slate-200/60">
                        <div class="text-[9px] text-slate-700 font-bold">小計 / รวม</div>
                        <div class="text-sm font-black font-mono text-slate-800 mt-1">$<span id="rowTotalDisplay-${id}">0</span></div>
                    </div>
                </div>
            `;

            container.appendChild(card);
            calculateAll();
        }

        // 增減按鈕邏輯
        function adjustValue(id, field, delta) {
            const index = items.findIndex(item => item.id === id);
            if (index === -1) return;

            let val = parseFloat(items[index][field]) || 0;
            val += delta;

            // 限制防呆：天數不能小於 1，時數不能小於 0.5
            if (field === 'days' && val < 1) val = 1;
            if (field === 'hours' && val < 0.5) val = 0.5;

            items[index][field] = val;
            
            // 更新輸入框數值
            document.getElementById(`input-${field}-${id}`).value = val;
            calculateAll();
        }

        // 當使用者自己用鍵盤輸入數值時
        function manualInputChange(id, field) {
            const index = items.findIndex(item => item.id === id);
            if (index === -1) return;

            const inputEl = document.getElementById(`input-${field}-${id}`);
            let val = parseFloat(inputEl.value) || 0;

            // 限制防呆
            if (field === 'days' && val < 1) val = 1;
            if (field === 'hours' && val < 0.5) val = 0.5;

            items[index][field] = val;
            calculateAll();
        }

        // 平日假日切換器
        function setDayType(id, type) {
            const index = items.findIndex(item => item.id === id);
            if (index === -1) return;

            items[index].dayType = type;

            const weekdayBtn = document.getElementById(`toggle-weekday-${id}`);
            const holidayBtn = document.getElementById(`toggle-holiday-${id}`);

            if (type === 'weekday') {
                weekdayBtn.className = "py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale bg-white text-blue-700 shadow-sm";
                holidayBtn.className = "py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale text-slate-500";
            } else {
                weekdayBtn.className = "py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale text-slate-500";
                holidayBtn.className = "py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale bg-white text-orange-700 shadow-sm";
            }

            calculateAll();
        }

        // 刪除項目卡片
        function deleteItem(id) {
            const card = document.getElementById(`card-${id}`);
            if (card) {
                // 做一個小小的淡出特效後移除
                card.style.opacity = '0';
                card.style.transform = 'translateY(10px)';
                setTimeout(() => {
                    card.remove();
                    items = items.filter(item => item.id !== id);
                    renderEmptyPlaceholder();
                    calculateAll();
                }, 200);
            }
        }

        // 複製項目卡片
        function cloneItem(id) {
            const index = items.findIndex(item => item.id === id);
            if (index === -1) return;

            const baseItem = items[index];
            const newId = itemIdCounter++;
            
            const newItem = {
                id: newId,
                dayType: baseItem.dayType,
                days: baseItem.days,
                hours: baseItem.hours
            };
            items.push(newItem);

            // 渲染複製出來的卡片
            const container = document.getElementById('cardsContainer');
            const card = document.createElement('div');
            card.id = `card-${newId}`;
            card.className = "bg-white rounded-2xl p-4 shadow-sm border border-slate-100 space-y-4 relative transition-all duration-200 overflow-hidden";
            
            card.innerHTML = `
                <!-- Card Header -->
                <div class="flex items-center justify-between gap-3">
                    <div class="grid grid-cols-2 p-1 bg-slate-100 rounded-xl flex-1 max-w-[210px]">
                        <button type="button" id="toggle-weekday-${newId}" onclick="setDayType(${newId}, 'weekday')" class="py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale ${newItem.dayType === 'weekday' ? 'bg-white text-blue-700 shadow-sm' : 'text-slate-500'}">
                            平日 วันธรรมดา
                        </button>
                        <button type="button" id="toggle-holiday-${newId}" onclick="setDayType(${newId}, 'holiday')" class="py-1.5 text-xs font-bold rounded-lg transition-all btn-active-scale ${newItem.dayType === 'holiday' ? 'bg-white text-orange-700 shadow-sm' : 'text-slate-500'}">
                            假日 วันหยุด
                        </button>
                    </div>
                    
                    <!-- Action controls -->
                    <div class="flex items-center gap-1">
                        <button type="button" onclick="cloneItem(${newId})" title="複製" class="btn-active-scale w-9 h-9 flex items-center justify-center bg-slate-50 text-slate-500 hover:bg-blue-50 hover:text-blue-600 rounded-xl transition-all">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7v8a2 2 0 002 2h6M8 7V5a2 2 0 012-2h4.586a1 1 0 01.707.293l4.414 4.414a1 1 0 01.293.707V15a2 2 0 01-2 2h-2M8 7H6a2 2 0 00-2 2v10a2 2 0 002 2h8a2 2 0 002-2v-2" />
                            </svg>
                        </button>
                        <button type="button" onclick="deleteItem(${newId})" title="刪除" class="btn-active-scale w-9 h-9 flex items-center justify-center bg-red-50 text-red-500 hover:bg-red-100 rounded-xl transition-all">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                            </svg>
                        </button>
                    </div>
                </div>

                <!-- Input Steppers Section -->
                <div class="grid grid-cols-2 gap-4">
                    <!-- Days -->
                    <div class="space-y-1.5">
                        <label class="block text-xs text-slate-500 font-medium">
                            天數 <span class="text-[10px] text-slate-400">จำนวนวัน</span>
                        </label>
                        <div class="flex items-center justify-between bg-slate-50 border border-slate-200 rounded-xl p-1 h-12">
                            <button type="button" onclick="adjustValue(${newId}, 'days', -1)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">-</button>
                            <input type="number" id="input-days-${newId}" value="${newItem.days}" min="1" oninput="manualInputChange(${newId}, 'days')" class="w-10 text-center bg-transparent border-none text-base font-bold text-slate-800 focus:outline-none">
                            <button type="button" onclick="adjustValue(${newId}, 'days', 1)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">+</button>
                        </div>
                    </div>

                    <!-- Hours -->
                    <div class="space-y-1.5">
                        <label class="block text-xs text-slate-500 font-medium">
                            每日小時 <span class="text-[10px] text-slate-400">ชั่วโมง/วัน</span>
                        </label>
                        <div class="flex items-center justify-between bg-slate-50 border border-slate-200 rounded-xl p-1 h-12">
                            <button type="button" onclick="adjustValue(${newId}, 'hours', -0.5)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">-</button>
                            <input type="number" id="input-hours-${newId}" value="${newItem.hours}" min="0.5" step="0.5" oninput="manualInputChange(${newId}, 'hours')" class="w-10 text-center bg-transparent border-none text-base font-bold text-slate-800 focus:outline-none">
                            <button type="button" onclick="adjustValue(${newId}, 'hours', 0.5)" class="btn-active-scale w-10 h-10 rounded-lg bg-white shadow-sm border border-slate-200 text-slate-600 font-bold text-lg flex items-center justify-center">+</button>
                        </div>
                    </div>
                </div>

                <!-- Live Subtotal Inside Card -->
                <div class="bg-slate-50 rounded-xl p-3 grid grid-cols-3 gap-1 text-center">
                    <div>
                        <div class="text-[9px] text-slate-400 font-medium">加班費 / ค่าล่วงเวลา</div>
                        <div class="text-xs font-bold font-mono text-blue-600 mt-1">$<span id="otDisplay-${newId}">0</span></div>
                    </div>
                    <div class="border-l border-slate-200/60">
                        <div class="text-[9px] text-slate-400 font-medium">獎金 / โบนัส</div>
                        <div class="text-xs font-bold font-mono text-orange-500 mt-0.5">$<span id="bonusDisplay-${newId}">0</span></div>
                        <div class="text-[8px] text-slate-400 leading-none truncate" id="bonusLabel-${newId}">-</div>
                    </div>
                    <div class="border-l border-slate-200/60">
                        <div class="text-[9px] text-slate-700 font-bold">小計 / รวม</div>
                        <div class="text-sm font-black font-mono text-slate-800 mt-1">$<span id="rowTotalDisplay-${newId}">0</span></div>
                    </div>
                </div>
            `;

            container.appendChild(card);
            calculateAll();
        }

        // 當項目空了時，自動出現空白導引背景
        function renderEmptyPlaceholder() {
            const placeholder = document.getElementById('emptyPlaceholder');
            if (placeholder) {
                if (items.length === 0) {
                    placeholder.style.display = 'block';
                } else {
                    placeholder.style.display = 'none';
                }
            }
        }

        // 清空重填
        function clearAll() {
            items = [];
            document.getElementById('cardsContainer').innerHTML = '';
            document.getElementById('baseSalary').value = '';
            renderEmptyPlaceholder();
            calculateAll();
        }

        // 核心加班費與獎金算式邏輯 (採用修正後的最新規則)
        function calculateAll() {
            const baseSalary = parseFloat(document.getElementById('baseSalary').value) || 0;
            const hourlyWage = baseSalary / 240;
            document.getElementById('hourlyWageDisplay').innerText = baseSalary > 0 ? Math.round(hourlyWage).toLocaleString() : '0';

            let globalOtPay = 0;
            let globalBonus = 0;

            items.forEach(item => {
                const id = item.id;
                const days = parseFloat(item.days) || 0;
                const hours = parseFloat(item.hours) || 0;
                const type = item.dayType;

                let dailyOtPay = 0;
                let dailyBonus = 0;
                let bonusLabels = [];

                if (type === 'weekday') {
                    // 平日加班費計算
                    if (hours > 0) {
                        if (hours <= 2) {
                            dailyOtPay = hours * hourlyWage * 1.33;
                        } else {
                            dailyOtPay = (2 * hourlyWage * 1.33) + ((hours - 2) * hourlyWage * 1.66);
                        }

                        // 平日獎金判斷 (滿 2 小時加發晚餐 100，滿 5 小時再加發宵夜 100)
                        if (hours >= 2) { 
                            dailyBonus += 100;
                            bonusLabels.push("晚餐");
                        }
                        if (hours >= 5) { 
                            dailyBonus += 100;
                            bonusLabels.push("宵夜");
                        }
                    }
                } else {
                    // 假日加班費計算 (1~8小時為1.66，9小時起為2.66)
                    if (hours > 0) {
                        if (hours <= 8) {
                            dailyOtPay = hours * hourlyWage * 1.66;
                        } else {
                            dailyOtPay = (8 * hourlyWage * 1.66) + ((hours - 8) * hourlyWage * 2.66);
                        }

                        // 假日獎金判斷 (滿 5 中餐 100，滿 10 再加發晚餐 100，滿 13 再加發宵夜 100)
                        if (hours >= 5) { 
                            dailyBonus += 100;
                            bonusLabels.push("中餐");
                        }
                        if (hours >= 10) { 
                            dailyBonus += 100;
                            bonusLabels.push("晚餐");
                        }
                        if (hours >= 13) { 
                            dailyBonus += 100;
                            bonusLabels.push("宵夜");
                        }
                    }
                }

                const totalOtPay = Math.round(dailyOtPay * days);
                const totalBonus = Math.round(dailyBonus * days);
                const rowTotal = totalOtPay + totalBonus;

                globalOtPay += totalOtPay;
                globalBonus += totalBonus;

                // 填入卡片內各細項 (防呆：檢查 DOM 節點是否存在)
                const otDisp = document.getElementById(`otDisplay-${id}`);
                const bonusDisp = document.getElementById(`bonusDisplay-${id}`);
                const bonusLbl = document.getElementById(`bonusLabel-${id}`);
                const rowTotalDisp = document.getElementById(`rowTotalDisplay-${id}`);

                if (otDisp) otDisp.innerText = totalOtPay.toLocaleString();
                if (bonusDisp) bonusDisp.innerText = totalBonus.toLocaleString();
                if (bonusLbl) bonusLbl.innerText = bonusLabels.length > 0 ? bonusLabels.join('+') : '-';
                if (rowTotalDisp) rowTotalDisp.innerText = rowTotal.toLocaleString();
            });

            // 算完後，累計塞入底部懸浮面板
            const globalTotal = globalOtPay + globalBonus;
            document.getElementById('globalOtPay').innerText = globalOtPay.toLocaleString();
            document.getElementById('globalBonus').innerText = globalBonus.toLocaleString();
            document.getElementById('globalTotal').innerText = globalTotal.toLocaleString();
        }

        // 展開 / 收合底部細節面板
        function toggleBreakdown() {
            const panel = document.getElementById('breakdownPanel');
            const arrow = document.getElementById('breakdownArrow');
            if (panel.classList.contains('hidden')) {
                panel.classList.remove('hidden');
                arrow.classList.add('rotate-180');
            } else {
                panel.classList.add('hidden');
                arrow.classList.remove('rotate-180');
            }
        }

        // 展開 / 收合規則說明
        function toggleRules() {
            const content = document.getElementById('rulesContent');
            const arrow = document.getElementById('rulesArrow');
            if (content.classList.contains('hidden')) {
                content.classList.remove('hidden');
                arrow.classList.add('rotate-180');
            } else {
                content.classList.add('hidden');
                arrow.classList.remove('rotate-180');
            }
        }
    </script>
</body>
</html>
