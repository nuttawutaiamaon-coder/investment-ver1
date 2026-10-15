<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Foreign Stock Portfolio Tracker</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Prompt', sans-serif; }
    </style>
</head>
<body class="bg-gray-50 text-gray-800 min-h-screen p-4 md:p-8">

    <div class="max-w-5xl mx-auto space-y-6">
        <!-- Header -->
        <header class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 flex flex-col md:flex-row justify-between items-start md:items-center gap-4">
            <div>
                <h1 class="text-2xl font-bold text-gray-900">📈 บันทึกการลงทุนหุ้นต่างประเทศ</h1>
                <p class="text-sm text-gray-500">จัดการข้อมูลซื้อหุ้นและเงินปันผล พร้อมสรุปผลรวมอัตโนมัติ</p>
            </div>
            <div class="flex gap-2 w-full md:w-auto">
                <button onclick="openBuyModal()" class="flex-1 md:flex-none bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-xl text-sm font-medium transition shadow-sm">
                    + เพิ่มรายการซื้อหุ้น
                </button>
                <button onclick="openDivModal()" class="flex-1 md:flex-none bg-emerald-600 hover:bg-emerald-700 text-white px-4 py-2 rounded-xl text-sm font-medium transition shadow-sm">
                    + บันทึกปันผล
                </button>
            </div>
        </header>

        <!-- Summary Cards -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-100">
                <p class="text-sm font-medium text-gray-500">มูลค่าลงทุนรวม (THB)</p>
                <h3 id="totalInvestTHB" class="text-2xl font-bold text-gray-900 mt-1">0.00</h3>
            </div>
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-100">
                <p class="text-sm font-medium text-gray-500">จำนวนหุ้นทั้งหมด</p>
                <h3 id="totalShares" class="text-2xl font-bold text-blue-600 mt-1">0.00</h3>
            </div>
            <div class="bg-white p-5 rounded-2xl shadow-sm border border-gray-100">
                <p class="text-sm font-medium text-gray-500">เงินปันผลรวมรับ (THB)</p>
                <h3 id="totalDividend" class="text-2xl font-bold text-emerald-600 mt-1">0.00</h3>
            </div>
        </div>

        <!-- Tables Section -->
        <div class="grid grid-cols-1 gap-6">
            <!-- Buy Transactions Table -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
                <div class="p-5 border-b border-gray-100 font-semibold text-gray-800">🛒 ประวัติการซื้อหุ้น</div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-50 text-xs font-semibold text-gray-500 uppercase">
                                <th class="p-4">วันที่</th>
                                <th class="p-4">ราคา/หน่วย (USD)</th>
                                <th class="p-4">อัตราแลกเปลี่ยน (THB)</th>
                                <th class="p-4">ปริมาณหุ้น</th>
                                <th class="p-4">รวมเป็นเงิน (THB)</th>
                                <th class="p-4 text-center">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="buyTableBody" class="divide-y divide-gray-100 text-sm">
                            <!-- ข้อมูลจะถูกเติมด้วย JavaScript -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- Dividend Table -->
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
                <div class="p-5 border-b border-gray-100 font-semibold text-gray-800">💰 ประวัติเงินปันผล</div>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-gray-50 text-xs font-semibold text-gray-500 uppercase">
                                <th class="p-4">วันที่</th>
                                <th class="p-4">อัตราปันผลต่อหุ้น (USD)</th>
                                <th class="p-4">จำนวนหุ้น ณ ตอนนั้น</th>
                                <th class="p-4">เงินปันผลรับรวม (THB)</th>
                                <th class="p-4 text-center">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="divTableBody" class="divide-y divide-gray-100 text-sm">
                            <!-- ข้อมูลจะถูกเติมด้วย JavaScript -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal: Add Buy -->
    <div id="buyModal" class="fixed inset-0 bg-black/50 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 space-y-4">
            <h3 class="text-lg font-bold text-gray-900">เพิ่มรายการซื้อหุ้น</h3>
            <div class="space-y-3">
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">วันที่</label>
                    <input type="date" id="buyDate" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">ราคาต่อหน่วย (USD)</label>
                    <input type="number" step="any" id="buyPrice" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="0.00">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">อัตราแลกเปลี่ยน (THB / 1 USD)</label>
                    <input type="number" step="any" id="buyFx" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="35.00">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">ปริมาณหุ้น</label>
                    <input type="number" step="any" id="buyShares" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="0">
                </div>
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeBuyModal()" class="px-4 py-2 text-sm text-gray-500 hover:bg-gray-100 rounded-xl transition">ยกเลิก</button>
                <button onclick="saveBuy()" class="px-4 py-2 text-sm bg-blue-600 text-white rounded-xl hover:bg-blue-700 transition">บันทึก</button>
            </div>
        </div>
    </div>

    <!-- Modal: Add Dividend -->
    <div id="divModal" class="fixed inset-0 bg-black/50 hidden flex items-center justify-center p-4 z-50">
        <div class="bg-white rounded-2xl max-w-md w-full p-6 space-y-4">
            <h3 class="text-lg font-bold text-gray-900">บันทึกรับเงินปันผล</h3>
            <div class="space-y-3">
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">วันที่ได้รับ</label>
                    <input type="date" id="divDate" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">อัตราปันผลต่อหุ้น (USD)</label>
                    <input type="number" step="any" id="divRate" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="0.00">
                </div>
                <div>
                    <label class="block text-xs font-medium text-gray-500 mb-1">อัตราแลกเปลี่ยน ณ วันรับปันผล (THB / 1 USD)</label>
                    <input type="number" step="any" id="divFx" class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500" placeholder="35.00">
                </div>
            </div>
            <div class="flex justify-end gap-2 pt-2">
                <button onclick="closeDivModal()" class="px-4 py-2 text-sm text-gray-500 hover:bg-gray-100 rounded-xl transition">ยกเลิก</button>
                <button onclick="saveDiv()" class="px-4 py-2 text-sm bg-emerald-600 text-white rounded-xl hover:bg-emerald-700 transition">บันทึก</button>
            </div>
        </div>
    </div>

    <!-- JavaScript Logic -->
    <script>
        // โหลดข้อมูลจาก LocalStorage หรือใช้ค่าว่าง
        let buyTransactions = JSON.parse(localStorage.getItem('buyTransactions')) || [];
        let divTransactions = JSON.parse(localStorage.getItem('divTransactions')) || [];

        function saveData() {
            localStorage.setItem('buyTransactions', JSON.stringify(buyTransactions));
            localStorage.setItem('divTransactions', JSON.stringify(divTransactions));
            render();
        }

        // Modal Controls
        function openBuyModal() { document.getElementById('buyModal').classList.remove('hidden'); }
        function closeBuyModal() { document.getElementById('buyModal').classList.add('hidden'); }
        function openDivModal() { document.getElementById('divModal').classList.remove('hidden'); }
        function closeDivModal() { document.getElementById('divModal').classList.add('hidden'); }

        // Save Buy
        function saveBuy() {
            const date = document.getElementById('buyDate').value;
            const price = parseFloat(document.getElementById('buyPrice').value);
            const fx = parseFloat(document.getElementById('buyFx').value);
            const shares = parseFloat(document.getElementById('buyShares').value);

            if (!date || isNaN(price) || isNaN(fx) || isNaN(shares)) {
                alert('กรุณากรอกข้อมูลให้ครบถ้วน');
                return;
            }

            buyTransactions.push({ id: Date.now(), date, price, fx, shares });
            closeBuyModal();
            saveData();
        }

        // Save Dividend
        function saveDiv() {
            const date = document.getElementById('divDate').value;
            const rate = parseFloat(document.getElementById('divRate').value);
            const fx = parseFloat(document.getElementById('divFx').value);

            if (!date || isNaN(rate) || isNaN(fx)) {
                alert('กรุณากรอกข้อมูลให้ครบถ้วน');
                return;
            }

            // คำนวณจำนวนหุ้นทั้งหมด ณ วันที่รับปันผล (หรือใช้จำนวนหุ้นปัจจุบัน)
            const currentTotalShares = buyTransactions.reduce((sum, item) => sum + item.shares, 0);

            divTransactions.push({ id: Date.now(), date, rate, fx, sharesAtTheTime: currentTotalShares });
            closeDivModal();
            saveData();
        }

        // Delete items
        function deleteBuy(id) {
            buyTransactions = buyTransactions.filter(item => item.id !== id);
            saveData();
        }

        function deleteDiv(id) {
            divTransactions = divTransactions.filter(item => item.id !== id);
            saveData();
        }

        // Render Data & Summaries
        function render() {
            const buyTbody = document.getElementById('buyTableBody');
            const divTbody = document.getElementById('divTableBody');
            
            buyTbody.innerHTML = '';
            divTbody.innerHTML = '';

            let totalInvestTHB = 0;
            let totalShares = 0;
            let totalDividendTHB = 0;

            // Render Buy Table
            buyTransactions.forEach(item => {
                const totalTHB = item.price * item.fx * item.shares;
                totalInvestTHB += totalTHB;
                totalShares += item.shares;

                buyTbody.innerHTML += `
                    <tr class="hover:bg-gray-50/50">
                        <td class="p-4">${item.date}</td>
                        <td class="p-4">$${item.price.toFixed(2)}</td>
                        <td class="p-4">฿${item.fx.toFixed(2)}</td>
                        <td class="p-4">${item.shares.toLocaleString()}</td>
                        <td class="p-4 font-medium">฿${totalTHB.toLocaleString('en-US', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                        <td class="p-4 text-center">
                            <button onclick="deleteBuy(${item.id})" class="text-red-500 hover:text-red-700 text-xs bg-red-50 px-2 py-1 rounded-lg">ลบ</button>
                        </td>
                    </tr>
                `;
            });

            if (buyTransactions.length === 0) {
                buyTbody.innerHTML = `<tr><td colspan="6" class="p-6 text-center text-gray-400">ยังไม่มีรายการซื้อหุ้น</td></tr>`;
            }

            // Render Dividend Table
            divTransactions.forEach(item => {
                const totalDivTHB = item.rate * item.sharesAtTheTime * item.fx;
                totalDividendTHB += totalDivTHB;

                divTbody.innerHTML += `
                    <tr class="hover:bg-gray-50/50">
                        <td class="p-4">${item.date}</td>
                        <td class="p-4">$${item.rate.toFixed(4)}</td>
                        <td class="p-4">${item.sharesAtTheTime.toLocaleString()}</td>
                        <td class="p-4 font-medium text-emerald-600">฿${totalDivTHB.toLocaleString('en-US', {minimumFractionDigits: 2, maximumFractionDigits: 2})}</td>
                        <td class="p-4 text-center">
                            <button onclick="deleteDiv(${item.id})" class="text-red-500 hover:text-red-700 text-xs bg-red-50 px-2 py-1 rounded-lg">ลบ</button>
                        </td>
                    </tr>
                `;
            });

            if (divTransactions.length === 0) {
                divTbody.innerHTML = `<tr><td colspan="5" class="p-6 text-center text-gray-400">ยังไม่มีรายการปันผล</td></tr>`;
            }

            // Update Summary Cards
            document.getElementById('totalInvestTHB').innerText = `฿${totalInvestTHB.toLocaleString('en-US', {minimumFractionDigits: 2, maximumFractionDigits: 2})}`;
            document.getElementById('totalShares').innerText = totalShares.toLocaleString();
            document.getElementById('totalDividend').innerText = `฿${totalDividendTHB.toLocaleString('en-US', {minimumFractionDigits: 2, maximumFractionDigits: 2})}`;
        }

        // Initial Load
        render();
    </script>
</body>
</html>
