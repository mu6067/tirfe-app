# tirfe-app
<!DOCTYPE html>
<html lang="am">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ትርፌ የሱቅ መቆጣጠሪያ አፕሊኬሽን - Ultimate v5</title>
    <style>
        :root {
            --bg-color: #0b0f19;
            --card-bg: #151f32;
            --text-color: #f1f5f9;
            --accent-color: #38bdf8;
            --success-color: #4ade80;
            --danger-color: #f87171;
            --warning-color: #fbbf24;
            --purple-color: #c084fc;
        }
        body {
            font-family: system-ui, -apple-system, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
        }
        .container { max-width: 1200px; margin: 0 auto; }
        
        .welcome-header {
            text-align: center; margin-bottom: 30px; padding: 20px;
            background: linear-gradient(135deg, #1e293b, #0f172a);
            border-radius: 12px; border: 1px solid #334155;
        }
        .welcome-header h1 { color: var(--success-color); margin: 0; font-size: 28px; }
        .welcome-header p { color: var(--accent-color); margin: 5px 0 0 0; font-size: 14px; }

        .session-badge {
            display: block; text-align: center; color: var(--warning-color); 
            font-size: 13px; margin-bottom: 20px; background: rgba(251, 191, 36, 0.1);
            padding: 10px; border-radius: 8px; border: 1px dashed var(--warning-color);
        }
        
        .dashboard {
            display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 15px; margin-bottom: 25px;
        }
        .card {
            background-color: var(--card-bg); padding: 15px; border-radius: 10px; text-align: center; border: 1px solid #24334d;
        }
        .card h3 { margin: 0 0 10px 0; font-size: 13px; color: #94a3b8; }
        .card p { margin: 0; font-size: 18px; font-weight: bold; color: var(--success-color); }

        .actions { display: flex; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        button { padding: 10px 16px; border: none; border-radius: 6px; font-weight: bold; cursor: pointer; transition: all 0.2s; }
        .btn-add { background-color: var(--accent-color); color: #000; }
        .btn-sell { background-color: var(--success-color); color: #000; }
        .btn-expense { background-color: #f43f5e; color: #fff; }
        .btn-credit { background-color: #f59e0b; color: #000; }
        .btn-draw { background-color: var(--purple-color); color: #000; }
        .btn-config { background-color: #64748b; color: #fff; }
        .btn-next-day { background-color: #a855f7; color: #fff; }
        .btn-clear { background-color: var(--danger-color); color: #000; margin-left: auto; }
        button:hover { opacity: 0.9; transform: translateY(-1px); }

        table { width: 100%; border-collapse: collapse; background-color: var(--card-bg); border-radius: 8px; overflow: hidden; margin-bottom: 30px; border: 1px solid #24334d; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #24334d; }
        th { background-color: #0f172a; color: var(--accent-color); font-size: 14px; }
        tr:hover { background-color: rgba(255,255,255,0.01); }

        .low-stock { background-color: rgba(248, 113, 113, 0.12); }
        .badge-danger { background: var(--danger-color); color: #000; padding: 3px 6px; border-radius: 4px; font-size: 11px; font-weight: bold; cursor: pointer; }

        .report-section { background-color: #090d16; border: 2px dashed var(--success-color); padding: 20px; border-radius: 10px; margin-top: 20px; display: none; }
        .grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 20px; }
        @media(max-width: 900px) { .grid-3 { grid-template-columns: 1fr; } }
    </style>
</head>
<body>

<div class="container">
    <div class="welcome-header">
        <h1>እንኳን ደህና መጡ ወደ " ትርፌ " መቆጣጠሪያ መተግበሪያ</h1>
        <p>ቀልጣፋ፣ ዘመናዊ እና አስተማማኝ የሱቅዎ የሂሳብ ረዳት</p>
    </div>

    <div id="sessionDisplay" class="session-badge">የዕለቱ መረጃ በመጠባበቅ ላይ...</div>

    <div class="dashboard">
        <div class="card">
            <h3>የአሁን የሱቅ ካፒታል</h3>
            <p id="totalCapital" style="color: var(--accent-color);">0.00 ETB</p>
        </div>
        <div class="card">
            <h3>የዛሬ የተጣራ ትርፍ</h3>
            <p id="todayNetProfit">0.00 ETB</p>
        </div>
        <div class="card">
            <h3>የዚህ ወር አጠቃላይ ትርፍ</h3>
            <p id="monthlyNetProfit" style="color: var(--warning-color);">0.00 ETB</p>
        </div>
        <div class="card">
            <h3>ከሳጥን የተነሳ (ያልተመለሰ)</h3>
            <p id="totalDraws" style="color: var(--purple-color);">0.00 ETB</p>
        </div>
        <div class="card">
            <h3>ማታ በካሽ ሳጥን መገኘት ያለበት</h3>
            <p id="totalInCash" style="color: #38bdf8;">0.00 ETB</p>
        </div>
    </div>

    <div class="actions">
        <button class="btn-add" onclick="addItem()">📦 አዲስ ዕቃ / ጭማሪ መዝግብ</button>
        <button class="btn-sell" onclick="sellItem()">$ የሽያጭ መዝገብ</button>
        <button class="btn-expense" onclick="addExpense()">📉 ወጪ መዝግብ</button>
        <button class="btn-credit" onclick="addCredit()">💳 ዕዳ መዝግብ</button>
        <button class="btn-draw" onclick="addDraw()">💸 ከሳጥን ብር አንሳ</button>
        <button class="btn-config" onclick="configureBank()">🏦 የባንክ አፕ ማያያዣ</button>
        <button class="btn-next-day" onclick="startNewDaySession()">🔄 አዲስ ቀን ጀምር</button>
        <button class="btn-clear" onclick="clearAllData()">🗑️ ሲስተሙን አጽዳ</button>
    </div>

    <h2>የዕቃዎች ዝርዝር እና የክምችት ሁኔታ</h2>
    <table>
        <thead>
            <tr>
                <th>የዕቃ ስም</th>
                <th>መግዣ ዋጋ (ካፒታል)</th>
                <th>መሸጫ ዋጋ</th>
                <th>የነበረው ብዛት</th>
                <th>ዛሬ የተሸጠ</th>
                <th>ቀሪ ዕቃ</th>
                <th>የዛሬ ትርፍ</th>
            </tr>
        </thead>
        <tbody id="inventoryBody"></tbody>
    </table>

    <div class="grid-3">
        <div>
            <h2>የዕዳ መዝገብ (የተሰበሰበ ሂሳብ ብቻ ይደመራል)</h2>
            <table>
                <thead>
                    <tr>
                        <th>ደንበኛ እና ስልክ</th>
                        <th>የዕዳ ዝርዝር</th>
                        <th>ገንዘብ መጠን</th>
                        <th>ድርጊት</th>
                    </tr>
                </thead>
                <tbody id="creditBody"></tbody>
            </table>
        </div>

        <div>
            <h2>ከሳጥን የተነሳ ገንዘብ መቆጣጠሪያ</h2>
            <table>
                <thead>
                    <tr>
                        <th>ምክንያት</th>
                        <th>የብር መጠን</th>
                        <th>ሁኔታ</th>
                    </tr>
                </thead>
                <tbody id="drawBody"></tbody>
            </table>
        </div>

        <div>
            <h2>የቀደሙ ቀናት ታሪክ መዝገብ</h2>
            <table>
                <thead>
                    <tr>
                        <th>ቀን እና ሰራተኛ</th>
                        <th>የስራ ሰዓት</th>
                        <th>ሽያጭ</th>
                        <th>የቀን ትርፍ</th>
                    </tr>
                </thead>
                <tbody id="historyBody"></tbody>
            </table>
        </div>
    </div>

    <div id="reportBox" class="report-section">
        <h2 style="color: var(--success-color); margin: 0 0 10px 0;">🌙 የእለቱ ማታ 4:00 አውቶማቲክ ሪፖርት</h2>
        <textarea id="reportText" style="width: 100%; height: 240px; background: #000; color: #4ade80; border: 1px solid #334155; padding: 10px; font-family: monospace; border-radius: 5px; resize: none;" readonly></textarea>
        <button style="background: var(--success-color); color: #000; margin-top: 10px;" onclick="copyReport()">📋 ሪፖርቱን ኮፒ አድርግ</button>
    </div>
</div>

<script>
    let storeData = JSON.parse(localStorage.getItem('trfeInventory')) || [];
    let sessionData = JSON.parse(localStorage.getItem('trfeSession')) || null;
    let historyData = JSON.parse(localStorage.getItem('trfeHistory')) || [];
    let expenseData = JSON.parse(localStorage.getItem('trfeExpenses')) || [];
    let creditData = JSON.parse(localStorage.getItem('trfeCredits')) || [];
    let drawData = JSON.parse(localStorage.getItem('trfeDraws')) || [];
    let collectedCreditToday = parseFloat(localStorage.getItem('trfeCollectedCreditToday')) || 0;
    let preferredBankApp = localStorage.getItem('trfeBankApp') || "telebirr://";

    function checkMorningSession() {
        if (!sessionData) {
            let today = new Date();
            let dateStr = today.toLocaleDateString('am-ET');
            let timeStr = today.toLocaleTimeString('am-ET', { hour: '2-digit', minute: '2-digit' });

            let employee = prompt("የዛሬውን ሰራተኛ ስም ያስገቡ፦"); if(!employee) employee = "ያልተመዘገበ ሰራተኛ";
            let floatMoney = parseFloat(prompt("ለመልስ የሚሆን በሳጥን የተተወ መነሻ ገንዘብ (Float)፦")) || 0;
            
            sessionData = { date: dateStr, loginTime: timeStr, employee: employee, initialFloat: floatMoney };
            localStorage.setItem('trfeSession', JSON.stringify(sessionData));
            expenseData = []; localStorage.setItem('trfeExpenses', JSON.stringify(expenseData));
            collectedCreditToday = 0; localStorage.setItem('trfeCollectedCreditToday', 0);
        }
        displaySession();
    }

    function displaySession() {
        if(sessionData) {
            document.getElementById('sessionDisplay').innerText = `📅 ቀን፦ ${sessionData.date} | 👤 ሰራተኛ፦ ${sessionData.employee} | 🕒 የገባበት ሰዓት፦ ${sessionData.loginTime} | 💰 መነሻ ብር፦ ${sessionData.initialFloat} ETB`;
        }
    }

    function addItem() {
        let name = prompt("የዕቃውን ስም ያስገቡ፦"); if (!name) return;
        let cost = parseFloat(prompt("የአሁኑን አዲስ መግዣ ዋጋ (ካፒታል) ያስገቡ፦")) || 0;
        let sell = parseFloat(prompt("የአሁኑን አዲስ መሸጫ ዋጋ ያስገቡ፦")) || 0;
        let qty = parseInt(prompt("የመጣው አዲስ ብዛት ያስገቡ፦")) || 0;

        let existing = storeData.find(i => i.name.toLowerCase() === name.toLowerCase());
        if (existing) {
            existing.qty = (existing.qty - existing.sold) + qty;
            existing.cost = cost; 
            existing.sell = sell;
            existing.sold = 0;
            alert(`የ ${name} ዋጋና ብዛት በተሳካ ሁኔታ ተሻሽሏል!`);
        } else {
            storeData.push({ name, cost, sell, qty, sold: 0 });
        }
        saveAndRefresh();
    }

    function sellItem() {
        let name = prompt("የሚሸጠውን ዕቃ ስም ያስገቡ፦"); if (!name) return;
        let item = storeData.find(i => i.name.toLowerCase() === name.toLowerCase());
        if (!item) { alert("ይህ ዕቃ በክምችት ውስጥ የለም!"); return; }

        let remaining = item.qty - item.sold;
        let count = parseInt(prompt(`ስንት ፍሬ ተሸጠ? (ቀሪ ክምችት፡ ${remaining})፦`)) || 0;
        if (count > remaining) { alert("በሱቁ ውስጥ ካለው ቀሪ ዕቃ በላይ መሸጥ አይቻልም!"); return; }

        item.sold += count;
        saveAndRefresh();
    }

    function restockAndPay(itemName) {
        let payChoice = prompt(`የ ${itemName} ማዘዣ ዘዴ ይምረጡ:\n1 = በጥሬ ብር (Cash)\n2 = በባንክ አፕሊኬሽን (Bank App)`);
        if(payChoice === "2") {
            alert("ክፍያውን ለመፈጸም አውቶማቲክ ወደ ተመረጠው የባንክ መተግበሪያ ያሻግርዎታል።");
            window.location.href = preferredBankApp;
        }
        addItem();
    }

    function addExpense() {
        let reason = prompt("የወጪውን ዝርዝር ምክንያት ያስገቡ፦"); if(!reason) return;
        let amount = parseFloat(prompt("የወጪው የገንዘብ መጠን በብር፦")) || 0; if(amount <= 0) return;

        expenseData.push({ reason, amount });
        localStorage.setItem('trfeExpenses', JSON.stringify(expenseData));
        renderApp();
    }

    // 💳 ዕዳ መዝገብ ማስተካከያ (ዕቃ ይቀንሳል ነገር ግን ሽያጭ ላይ አይደመርም)
    function addCredit() {
        let customer = prompt("የባለዕዳውን ደንበኛ ሙሉ ስም ያስገቡ፦"); if(!customer) return;
        let phone = prompt("የደንበኛውን ስልክ ቁጥር ያስገቡ፦");
        let itemName = prompt("የወሰደውን ዕቃ ስም ያስገቡ፦");
        
        let item = storeData.find(i => i.name.toLowerCase() === itemName.toLowerCase());
        let calculatedPrice = 0;
        let costValue = 0;
        
        if (item) {
            let count = parseInt(prompt(`የወሰደው ዕቃ ብዛት? (የአንዱ ዋጋ: ${item.sell} ETB)፦`)) || 1;
            let remaining = item.qty - item.sold;
            if (count > remaining) { alert("በክምችት ውስጥ በቂ ዕቃ የለም!"); return; }
            
            calculatedPrice = item.sell * count;
            costValue = item.cost * count;
            item.qty -= count; // ዕቃው በቀጥታ ከሱቅ ክምችት ላይ ይቀንሳል
        } else {
            calculatedPrice = parseFloat(prompt("ዕቃው ስላልተገኘ ጠቅላላ ዋጋውን እራስዎ ያስገቡ፦")) || 0;
            costValue = calculatedPrice * 0.8; // ግምት
        }

        // ዕዳው ሲመዘገብ መግዣ ዋጋውን (Cost) እና መሸጫውን ለይቶ ይይዛል
        creditData.push({ customer: `${customer} (${phone})`, description: itemName, amount: calculatedPrice, cost: costValue });
        localStorage.setItem('trfeCredits', JSON.stringify(creditData));
        saveAndRefresh();
    }

    // ዕዳ ሲሰበሰብ (ብሩ ሲመጣ) ወደ ዛሬ ሽያጭ እና ትርፍ ይደመራል
    function payCredit(index) {
        if(confirm("ይህ ዕዳ ሙሉ በሙሉ ተከፍሎ ተወራርዷል? (አሁን ወደ የዛሬ ገቢ ይደመራል)")) {
            let paidAmount = creditData[index].amount;
            let paidCost = creditData[index].cost;
            
            // የዛሬው የተሰበሰበ ዕዳ መዝገብ ላይ ይደመራል
            collectedCreditToday += paidAmount;
            localStorage.setItem('trfeCollectedCreditToday', collectedCreditToday);

            // ለትርፍ ስሌት እንዲጠቅም በጊዜያዊነት ወደ ሽያጭ ታሪክ ይለገጻል
            storeData.push({ name: `የተሰበሰበ እዳ (${creditData[index].description})`, cost: paidCost, sell: paidAmount, qty: 1, sold: 1, isCreditPay: true });

            creditData.splice(index, 1);
            localStorage.setItem('trfeCredits', JSON.stringify(creditData));
            saveAndRefresh();
        }
    }

    function addDraw() {
        let reason = prompt("ከሳጥን ላይ ብር የወሰደው ሰው ስም ወይም ምክንያት ያስገቡ፦"); if(!reason) return;
        let amount = parseFloat(prompt("የተነሳው የገንዘብ መጠን፦")) || 0; if(amount <= 0) return;

        drawData.push({ reason, amount, status: "ያልተመለሰ" });
        localStorage.setItem('trfeDraws', JSON.stringify(drawData));
        renderApp();
    }

    function returnDraw(index) {
        if(confirm("ይህ የተነሳው ገንዘብ ሙሉ በሙሉ ወደ ሳጥን ተመልሶ ገብቷል?")) {
            drawData[index].status = "ገብቷል (ተወራርዷል)";
            localStorage.setItem('trfeDraws', JSON.stringify(drawData));
            renderApp();
        }
    }

    function configureBank() {
        let choice = prompt("ለክፍያ የሚጠቀሙበትን ዋና የባንክ አፕ ይምረጡ:\n1 = Telebirr\n2 = CBE Birr\n3 = Awash Sol");
        if(choice === "1") preferredBankApp = "telebirr://";
        else if(choice === "2") preferredBankApp = "cbebirr://";
        else if(choice === "3") preferredBankApp = "awashsol://";
        localStorage.setItem('trfeBankApp', preferredBankApp);
        alert("የባንክ መተግበሪያው በተሳካ ሁኔታ ተገናኝቷል!");
    }

    function startNewDaySession() {
        if (storeData.length === 0) return;
        if (confirm("የዛሬውን ሂሳብ ዘግተው ወደ ቀጣይ ቀን ታሪክ ማስተላለፍ ይፈልጋሉ?")) {
            let today = new Date();
            let logoutTime = today.toLocaleTimeString('am-ET', { hour: '2-digit', minute: '2-digit' });

            let tSales = collectedCreditToday, todayProfit = 0, tExp = 0;
            storeData.forEach(item => {
                tSales += (item.sell * item.sold);
                todayProfit += ((item.sell - item.cost) * item.sold);
            });
            expenseData.forEach(e => tExp += e.amount);

            historyData.push({
                date: sessionData.date,
                employee: sessionData.employee,
                hours: `ከ ${sessionData.loginTime} እስከ ${logoutTime}`,
                sales: tSales,
                profit: todayProfit - tExp
            });
            localStorage.setItem('trfeHistory', JSON.stringify(historyData));

            // የጽዳት ስራ (ጊዜያዊ የእዳ መሰብሰቢያዎችን ማጥፋት)
            storeData = storeData.filter(item => !item.isCreditPay);
            storeData.forEach(item => { item.qty = item.qty - item.sold; item.sold = 0; });
            localStorage.setItem('trfeInventory', JSON.stringify(storeData));

            sessionData = null; localStorage.removeItem('trfeSession');
            expenseData = []; localStorage.removeItem('trfeExpenses');
            localStorage.removeItem('trfeCollectedCreditToday');
            location.reload();
        }
    }

    function checkAutoReportTime() {
        let now = new Date();
        if (now.getHours() === 22 && now.getMinutes() === 0) { generateReport(); }
    }

    function generateReport() {
        let tSales = collectedCreditToday, todayProfit = 0, details = "", tExp = 0, expDetails = "", tDraw = 0;
        storeData.forEach(item => {
            if(item.sold > 0) {
                tSales += (item.sell * item.sold);
                todayProfit += ((item.sell - item.cost) * item.sold);
                if(!item.isCreditPay) details += `- ${item.name}: ${item.sold} ፍሬ ተሸጠ\n`;
            }
        });
        expenseData.forEach(e => { tExp += e.amount; expDetails += `- ${e.reason}: ${e.amount} ETB\n`; });
        drawData.forEach(d => { if(d.status === "ያልተመለሰ") tDraw += d.amount; });
        
        let finalInCash = sessionData.initialFloat + tSales - tExp - tDraw;

        let reportStr = `=== ትርፌ እለታዊ የሂሳብ ሪፖርት ===\nቀን: ${sessionData.date}\n------------------------------\nመነሻ ካሽ: ${sessionData.initialFloat.toFixed(2)} ETB\nጠቅላላ የዛሬ ገቢ (ሽያጭ + የተሰበሰበ እዳ): ${tSales.toFixed(2)} ETB\n  * ከዕዳ የተሰበሰበ ገቢ: ${collectedCreditToday.toFixed(2)} ETB\n\nየዛሬ ወጪዎች:\n${expDetails || "- ወጪ የለም\n"}ጠቅላላ ወጪ: ${tExp.toFixed(2)} ETB\nከሳጥን የተነሳ ብር: ${tDraw.toFixed(2)} ETB\n------------------------------\n💰 ማታ በሳጥን መገኘት ያለበት ካሽ:\n👉 [ ${finalInCash.toFixed(2)} ETB ] 👈\n==============================`;
        document.getElementById('reportText').value = reportStr; document.getElementById('reportBox').style.display = "block";
    }

    function copyReport() {
        let copyText = document.getElementById("reportText"); copyText.select(); navigator.clipboard.writeText(copyText.value);
        alert("ሪፖርቱ በተሳካ ሁኔታ ተገልብጧል!");
    }

    function clearAllData() {
        if (confirm("⚠️ ሁሉንም መረጃዎች ማጥፋት ይፈልጋሉ?")) { localStorage.clear(); location.reload(); }
    }

    function saveAndRefresh() {
        localStorage.setItem('trfeInventory', JSON.stringify(storeData));
        renderApp();
    }

    function renderApp() {
        let tbody = document.getElementById('inventoryBody'); tbody.innerHTML = '';
        let tSales = collectedCreditToday, todayProfit = 0, tExp = 0, tDraw = 0, currentTotalCapital = 0;

        storeData.forEach(item => {
            let remaining = item.qty - item.sold;
            if(remaining < 0) remaining = 0;
            let profit = (item.sell - item.cost) * item.sold;
            
            tSales += (item.sell * item.sold); 
            todayProfit += profit;
            
            if(!item.isCreditPay) {
                currentTotalCapital += (item.cost * remaining);
            }

            let isLowStock = remaining <= 5 ? 'class="low-stock"' : '';
            let alertBadge = remaining <= 5 ? `<span class="badge-danger" onclick="restockAndPay('${item.name}')">⚠️ አልቋል! ይግዙ</span>` : '';

            if(!item.isCreditPay) {
                tbody.innerHTML += `<tr ${isLowStock}><td><strong>${item.name}</strong> ${alertBadge}</td><td>${item.cost.toFixed(2)} ETB</td><td>${item.sell.toFixed(2)} ETB</td><td>${item.qty}</td><td style="color:#4ade80;">${item.sold}</td><td style="color:#f87171; font-weight:bold;">${remaining}</td><td style="font-weight:bold;color:#4ade80;">${profit.toFixed(2)} ETB</td></tr>`;
            }
        });

        expenseData.forEach(e => tExp += e.amount);
        drawData.forEach(d => { if(d.status === "ያልተመለሰ") tDraw += d.amount; });
        let initFloat = sessionData ? sessionData.initialFloat : 0;

        let totalMonthlyProfit = todayProfit - tExp;
        historyData.forEach(h => totalMonthlyProfit += h.profit);

        document.getElementById('totalCapital').innerText = currentTotalCapital.toFixed(2) + " ETB";
        document.getElementById('todayNetProfit').innerText = (todayProfit - tExp).toFixed(2) + " ETB";
        document.getElementById('monthlyNetProfit').innerText = totalMonthlyProfit.toFixed(2) + " ETB";
        document.getElementById('totalDraws').innerText = tDraw.toFixed(2) + " ETB";
        document.getElementById('totalInCash').innerText = (initFloat + tSales - tExp - tDraw).toFixed(2) + " ETB";

        let cbody = document.getElementById('creditBody'); cbody.innerHTML = '';
        creditData.forEach((c, index) => {
            cbody.innerHTML += `<tr><td>${c.customer}</td><td>${c.description}</td><td style="color:var(--warning-color);font-weight:bold;">${c.amount.toFixed(2)} ETB</td><td><button style="background:var(--success-color);color:#000;padding:4px 8px;font-size:12px;" onclick="payCredit(${index})">ተከፈለ (አውራርድ)</button></td></tr>`;
        });

        let dbody = document.getElementById('drawBody'); dbody.innerHTML = '';
        drawData.forEach((d, index) => {
            let statusBtn = d.status === "ያልተመለሰ" ? `<button style="background:var(--accent-color);color:#000;padding:4px 8px;font-size:12px;" onclick="returnDraw(${index})">መልስ</button>` : `<span style="color:var(--success-color); font-size:12px;">✅ ገብቷል</span>`;
            dbody.innerHTML += `<tr><td>${d.reason}</td><td style="color:var(--purple-color);font-weight:bold;">${d.amount.toFixed(2)} ETB</td><td>${statusBtn}</td></tr>`;
        });

        let hbody = document.getElementById('historyBody'); hbody.innerHTML = '';
        historyData.forEach(h => {
            hbody.innerHTML += `<tr><td><strong>${h.date}</strong><br><small style="color:#94a3b8;">${h.employee}</small></td><td style="font-size:12px; color:var(--accent-color);">${h.hours}</td><td>${h.sales.toFixed(2)}</td><td style="color:#4ade80;font-weight:bold;">${h.profit.toFixed(2)}</td></tr>`;
        });
    }

    checkMorningSession(); renderApp();
    setInterval(checkAutoReportTime, 60000);
</script>

</body>
</html>
