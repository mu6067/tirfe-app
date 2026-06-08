# tirfe-app
<!DOCTYPE html>
<html lang="am">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ትርፌ አፕ (Tirfe App) - ማዕከላዊ የኪራይ ሥርዓት</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    
    <style>
        :root {
            --bg-color: #0b132b;
            --card-bg: #1c2541;
            --primary: #4a90e2;
            --success: #2ec4b6;
            --danger: #e63946;
            --warning: #ffb703;
            --purple: #6c5ce7;
            --text-main: #ffffff;
            --text-muted: #afb5c0;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; }
        body { background-color: var(--bg-color); color: var(--text-main); padding: 10px; font-size: 14px; }
        .container { max-width: 100%; margin: 0 auto; }
        .hidden { display: none !important; }
        
        /* የመግቢያ ገጽ */
        .login-box {
            max-width: 360px; margin: 40px auto;
            background-color: var(--card-bg); padding: 25px;
            border-radius: 12px; box-shadow: 0 10px 25px rgba(0,0,0,0.5);
            text-align: center; border: 1px solid rgba(255,255,255,0.05);
        }
        .login-box h2 { color: var(--success); font-size: 1.5rem; margin-bottom: 20px; }

        input, select {
            width: 100%; padding: 10px; margin: 8px 0;
            border-radius: 6px; border: 1px solid #3a506b;
            background-color: var(--bg-color); color: white; font-size: 0.95rem;
        }

        /* በተኖች */
        .btn-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 8px; margin: 15px 0; }
        button { padding: 10px 12px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; font-size: 0.85rem; text-align: center; }
        .btn-block { width: 100%; }
        .btn-primary { background-color: var(--primary); color: white; }
        .btn-success { background-color: var(--success); color: white; }
        .btn-danger { background-color: var(--danger); color: white; }
        .btn-warning { background-color: var(--warning); color: #000; }
        .btn-purple { background-color: var(--purple); color: white; }

        /* ካርዶች */
        .welcome-card { background: linear-gradient(135deg, #1c2541, #3a506b); padding: 15px; border-radius: 12px; text-align: center; margin-bottom: 15px; }
        .welcome-card h1 { color: var(--success); font-size: 1.4rem; }
        .welcome-card p { font-size: 0.85rem; color: var(--text-muted); }
        
        .meta-bar { background-color: rgba(255, 255, 255, 0.05); padding: 8px; border-radius: 6px; display: grid; grid-template-columns: repeat(2, 1fr); gap: 5px; font-size: 0.8rem; margin-bottom: 15px; text-align: center; }

        .dashboard-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px; margin-bottom: 15px; }
        .card { background-color: var(--card-bg); padding: 12px; border-radius: 8px; text-align: center; border-left: 4px solid var(--primary); }
        .card h3 { font-size: 0.75rem; color: var(--text-muted); margin-bottom: 5px; }
        .card p { font-size: 1.1rem; font-weight: bold; }

        .table-responsive { width: 100%; overflow-x: auto; -webkit-overflow-scrolling: touch; margin-top: 10px; border-radius: 6px; }
        .section-box { background-color: var(--card-bg); padding: 15px; border-radius: 10px; margin-bottom: 15px; }
        .section-box h2 { font-size: 1rem; margin-bottom: 10px; border-bottom: 1px solid #3a506b; padding-bottom: 5px; color: var(--primary); }

        table { width: 100%; border-collapse: collapse; min-width: 500px; }
        th, td { padding: 8px 10px; border-bottom: 1px solid #3a506b; text-align: left; font-size: 0.85rem; white-space: nowrap; }
        th { background-color: rgba(255,255,255,0.05); color: var(--text-muted); }
        
        .status-badge { padding: 2px 6px; border-radius: 4px; font-weight: bold; font-size: 0.75rem; }
        .status-active { background-color: rgba(46, 196, 182, 0.2); color: var(--success); }
        .status-blocked { background-color: rgba(230, 57, 70, 0.2); color: var(--danger); }
    </style>
</head>
<body>

<div class="container">

    <div id="loginPage" class="login-box">
        <h2>ትርፌ አፕ - መግቢያ</h2>
        <input type="text" id="loginUser" placeholder="የተጠቃሚ ስም (Username)">
        <input type="password" id="loginPass" placeholder="የይለፍ ቃል (Password)">
        <button class="btn-success btn-block" onclick="handleLogin()">ደህንነቱ የተጠበቀ መግቢያ</button>
        <p id="loginError" style="color: var(--danger); margin-top: 15px; font-size: 0.85rem; font-weight: bold;"></p>
    </div>

    <div id="adminPage" class="hidden">
        <div class="welcome-card">
            <h1>የባለቤቱ ማዕከላዊ ቁጥጥር ፓነል</h1>
            <p>እዚህ ገጽ ላይ አዳዲስ ተከራዮችን መመዝገብ እና ማገድ ይችላሉ።</p>
            <button class="btn-danger" style="margin-top: 10px; padding: 6px 12px;" onclick="logout()">ከሲስተሙ ውጣ (Logout)</button>
        </div>

        <div class="section-box">
            <h2>አዲስ ተከራይ መመዝገቢያ</h2>
            <input type="text" id="newShopName" placeholder="የሱቅ ስም (ምሳሌ፡ አልፋ)">
            <input type="text" id="newUsername" placeholder="Username (መግቢያ ስም)">
            <input type="text" id="newPassword" placeholder="Password (የይለፍ ቃል)">
            <button class="btn-primary btn-block" style="margin-top: 5px;" onclick="registerTenant()">ተከራይ በቋሚነት መዝግብ</button>
        </div>

        <div class="section-box">
            <h2>የተከራዮች እና የኪራይ ሁኔታ መከታተያ</h2>
            <div class="table-responsive">
                <table>
                    <thead>
                        <tr>
                            <th>የሱቅ ስም</th>
                            <th>Username</th>
                            <th>Password</th>
                            <th>የኪራይ ሁኔታ</th>
                            <th>ማዕከላዊ እርምጃ</th>
                        </tr>
                    </thead>
                    <tbody id="tenantTableBody"></tbody>
                </table>
            </div>
        </div>
    </div>

    <div id="appPage" class="hidden">
        <div class="welcome-card">
            <h1 id="shopTitle">"ትርፌ" መቆጣጠሪያ መተግበሪያ</h1>
            <p>ቅልጥፍና እና አስተማማኝነት ለሱቅዎ</p>
        </div>

        <div class="meta-bar">
            <div>📅 ቀን፡ <span id="metaDate">8/6/2026</span></div>
            <div>👤 ሰራተኛ፡ <span id="metaStaff">mulugeta</span></div>
            <div>🕒 ሰዓት፡ <span id="metaTime">12:48 ከሰዓት</span></div>
            <div>💰 መነሻ፡ <span id="metaChange">10000 ETB</span></div>
        </div>

        <div class="dashboard-grid">
            <div class="card"><h3>የሱቅ ካፒታል</h3><p id="dispCapital">0.00</p></div>
            <div class="card"><h3>የዛሬ ትርፍ</h3><p id="dispDailyProfit" style="color: var(--success);">0.00</p></div>
            <div class="card"><h3>የወር ትርፍ</h3><p id="dispMonthlyProfit" style="color: var(--success);">0.00</p></div>
            <div class="card"><h3>ከሳጥን የተነሳ</h3><p id="dispDrawer" style="color: var(--purple);">0.00</p></div>
        </div>

        <div class="btn-grid">
            <button class="btn-primary" onclick="alert('የእቃ መመዝገቢያ ከታች ይገኛል!')">📦 ዕቃ መዝግብ</button>
            <button class="btn-success" onclick="alert('እቃው አጠገብ ያለውን የ (+) ምልክት ይጫኑ!')">💰 ሽያጭ</button>
            <button class="btn-danger" onclick="promptExpense()">📉 የወጪ</button>
            <button class="btn-warning" onclick="promptDebt()">💳 የዕዳ</button>
            <button class="btn-purple" onclick="promptDrawer()">💸 ሳጥን ብር</button>
        </div>

        <div class="section-box">
            <h2>የዕቃዎች ዝርዝር እና ክምችት</h2>
            <input type="text" id="itemName" placeholder="የእቃ ስም">
            <div style="display: flex; gap: 5px;">
                <input type="number" id="itemCost" placeholder="መግዣ">
                <input type="number" id="itemPrice" placeholder="መሸጫ">
                <input type="number" id="itemQty" placeholder="ብዛት">
            </div>
            <button class="btn-success btn-block" onclick="addItem()">ዕቃውን በዝርዝር አስገባ</button>
            
            <div class="table-responsive">
                <table>
                    <thead>
                        <tr><th>የዕቃ ስም</th><th>መግዣ</th><th>መሸጫ</th><th>ብዛት</th><th>ሽያጭ</th><th>ቀሪ</th><th>ትርፍ</th></tr>
                    </thead>
                    <tbody id="inventoryTable"></tbody>
                </table>
            </div>
        </div>

        <div class="section-box">
            <h2>የዕዳ መዝገብ</h2>
            <div class="table-responsive">
                <table>
                    <thead><tr><th>ደንበኛ</th><th>ዝርዝር</th><th>መጠን</th></tr></thead>
                    <tbody id="debtTable"></tbody>
                </table>
            </div>
        </div>

        <div class="section-box">
            <h2>ከሳጥን የተነሳ የገንዘብ መቆጣጠሪያ</h2>
            <div class="table-responsive">
                <table>
                    <thead><tr><th>ምክንያት</th><th>የብር መጠን</th><th>ሁኔታ</th></tr></thead>
                    <tbody id="drawerTable"></tbody>
                </table>
            </div>
        </div>
        
        <button class="btn-danger btn-block" style="margin-top: 15px;" onclick="logout()">🚪 ከአፑ ውጣ</button>
    </div>

</div>

<script>
    const firebaseConfig = {
        databaseURL: "https://tirfe-app-v2-300c2-default-rtdb.firebaseio.com/" 
    };
    
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let localDB = { tenants: {} };
    let currentTenant = null;

    db.ref('tirfe_system').on('value', (snapshot) => {
        if(snapshot.exists()) {
            localDB = snapshot.val();
            if(!localDB.tenants) localDB.tenants = {};
            
            if(currentTenant) {
                let checkTenant = localDB.tenants[currentTenant.username];
                if(checkTenant.status === "blocked") {
                    alert("🔒 የኪራይ ጊዜዎ አብቅቷል!");
                    logout();
                    return;
                }
                currentTenant = checkTenant;
                loadTenantApp();
            }
            if(!document.getElementById('adminPage').classList.contains('hidden')) {
                renderAdminPanel();
            }
        }
    });

    function pushToFirebase() {
        db.ref('tirfe_system').set(localDB);
    }

    function handleLogin() {
        let user = document.getElementById('loginUser').value.trim();
        let pass = document.getElementById('loginPass').value.trim();
        let error = document.getElementById('loginError');

        if(user === "admin" && pass === "admin123") {
            document.getElementById('loginPage').classList.add('hidden');
            document.getElementById('adminPage').classList.remove('hidden');
            renderAdminPanel();
            return;
        }

        let tenant = localDB.tenants[user];
        if(tenant && tenant.password === pass) {
            if(tenant.status === "blocked") {
                error.innerText = "🔒 ኪራይዎ ተቋርጧል!";
                return;
            }
            currentTenant = tenant;
            document.getElementById('loginPage').classList.add('hidden');
            document.getElementById('appPage').classList.remove('hidden');
            document.getElementById('shopTitle').innerText = tenant.shopName + " - መቆጣጠሪያ";
            loadTenantApp();
        } else {
            error.innerText = "❌ ስም ወይም ፓስወርድ ስህተት ነው!";
        }
    }

    function registerTenant() {
        let shop = document.getElementById('newShopName').value.trim();
        let user = document.getElementById('newUsername').value.trim();
        let pass = document.getElementById('newPassword').value.trim();

        if(!shop || !user || !pass) { alert("ሁሉንም ይሙሉ!"); return; }

        localDB.tenants[user] = {
            shopName: shop, username: user, password: pass, status: "active",
            data: { capital: 0, dailyProfit: 0, monthlyProfit: 0, drawerAmt: 0, inventory: [], expenses: [], debts: [], drawerLog: [] }
        };
        pushToFirebase();
        alert("ተከራይ ተመዝግቧል!");
        
        document.getElementById('newShopName').value = '';
        document.getElementById('newUsername').value = '';
        document.getElementById('newPassword').value = '';
    }

    function toggleStatus(user) {
        localDB.tenants[user].status = localDB.tenants[user].status === "active" ? "blocked" : "active";
        pushToFirebase();
    }

    function renderAdminPanel() {
        let tbody = document.getElementById('tenantTableBody');
        tbody.innerHTML = '';
        Object.keys(localDB.tenants).forEach(key => {
            let t = localDB.tenants[key];
            let badge = t.status === 'active' ? '<span class="status-badge status-active">ስራ ላይ</span>' : '<span class="status-badge status-blocked">የታገደ</span>';
            let btnText = t.status === 'active' ? 'Block' : 'Unblock';
            let btnClass = t.status === 'active' ? 'btn-danger' : 'btn-success';

            tbody.innerHTML += `
                <tr>
                    <td><b>${t.shopName}</b></td>
                    <td>${t.username}</td>
                    <td>${t.password}</td>
                    <td>${badge}</td>
                    <td><button class="${btnClass}" style="padding:4px 8px; font-size:0.75rem;" onclick="toggleStatus('${t.username}')">${btnText}</button></td>
                </tr>
            `;
        });
    }

    function loadTenantApp() {
        let d = currentTenant.data;
        if(!d.inventory) d.inventory = [];
        if(!d.expenses) d.expenses = [];
        if(!d.debts) d.debts = [];
        if(!d.drawerLog) d.drawerLog = [];

        document.getElementById('dispCapital').innerText = d.capital.toFixed(2);
        document.getElementById('dispDailyProfit').innerText = d.dailyProfit.toFixed(2);
        document.getElementById('dispMonthlyProfit').innerText = d.monthlyProfit.toFixed(2);
        document.getElementById('dispDrawer').innerText = d.drawerAmt.toFixed(2);

        renderInventoryTable();
        renderDrawerTable();
        renderDebtTable();
    }

    function addItem() {
        let name = document.getElementById('itemName').value.trim();
        let cost = parseFloat(document.getElementById('itemCost').value) || 0;
        let price = parseFloat(document.getElementById('itemPrice').value) || 0;
        let qty = parseInt(document.getElementById('itemQty').value) || 0;

        if(!name) return;

        currentTenant.data.inventory.push({ name, cost, price, qty, sold: 0, profit: 0 });
        pushToFirebase();

        document.getElementById('itemName').value = '';
        document.getElementById('itemCost').value = '';
        document.getElementById('itemPrice').value = '';
        document.getElementById('itemQty').value = '';
    }

    function renderInventoryTable() {
        let tbody = document.getElementById('inventoryTable');
        tbody.innerHTML = '';
        currentTenant.data.inventory.forEach((i, idx) => {
            let remaining = i.qty - i.sold;
            tbody.innerHTML += `
                <tr>
                    <td><b>${i.name}</b></td>
                    <td>${i.cost}</td>
                    <td>${i.price}</td>
                    <td>${i.qty}</td>
                    <td><button class="btn-success" style="padding:2px 6px; font-size:0.75rem;" onclick="sellItem(${idx})">+</button> ${i.sold}</td>
                    <td>${remaining}</td>
                    <td style="color:var(--success); font-weight:bold;">${i.profit}</td>
                </tr>
            `;
        });
    }

    function sellItem(idx) {
        let item = currentTenant.data.inventory[idx];
        if(item.qty - item.sold <= 0) { alert("ዕቃው አልቋል!"); return; }
        
        item.sold += 1;
        let netProfit = item.price - item.cost;
        item.profit += netProfit;
        
        currentTenant.data.dailyProfit += netProfit;
        currentTenant.data.monthlyProfit += netProfit;
        currentTenant.data.capital += item.price;

        pushToFirebase();
    }

    // (ሌሎች የወጪ እና የዕዳ ፈንክሽኖች እንዳሉ ይቀጥላሉ...)
    function promptExpense() {
        let reason = prompt("የወጪ ምክንያት፦"); if(!reason) return;
        let amt = parseFloat(prompt("መጠን (ETB)፦")) || 0; if(amt <= 0) return;
        currentTenant.data.dailyProfit -= amt; currentTenant.data.monthlyProfit -= amt; currentTenant.data.capital -= amt;
        currentTenant.data.expenses.push({ reason, amt }); pushToFirebase();
    }

    function promptDebt() {
        let name = prompt("የተበዳሪ ስም፦"); if(!name) return;
        let detail = prompt("የዕቃ ዝርዝር፦");
        let amt = parseFloat(prompt("የዕዳ መጠን፦")) || 0; if(amt <= 0) return;
        currentTenant.data.debts.push({ name, detail, amt }); pushToFirebase();
    }

    function renderDebtTable() {
        let tbody = document.getElementById('debtTable'); tbody.innerHTML = '';
        currentTenant.data.debts.forEach(d => { tbody.innerHTML += `<tr><td>${d.name}</td><td>${d.detail}</td><td>${d.amt}</td></tr>`; });
    }

    function promptDrawer() {
        let reason = prompt("ምክንያት፦"); if(!reason) return;
        let amt = parseFloat(prompt("መጠን (ETB)፦")) || 0; if(amt <= 0) return;
        currentTenant.data.drawerAmt += amt;
        currentTenant.data.drawerLog.push({ reason, amt, status: 'ወጣ' }); pushToFirebase();
    }

    function renderDrawerTable() {
        let tbody = document.getElementById('drawerTable'); tbody.innerHTML = '';
        currentTenant.data.drawerLog.forEach(l => { tbody.innerHTML += `<tr><td>${l.reason}</td><td>${l.amt}</td><td><span class="status-badge status-blocked">${l.status}</span></td></tr>`; });
    }

    function logout() {
        currentTenant = null;
        document.getElementById('adminPage').classList.add('hidden');
        document.getElementById('appPage').classList.add('hidden');
        document.getElementById('loginPage').classList.remove('hidden');
    }
</script>
</body>
</html>
