# Earning225<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CashReward Admin Panel</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f3f4f6; }
        /* --- Mobile First Approach --- */
        
        /* Sidebar is hidden on mobile by default */
        .sidebar {
            position: fixed;
            top: 0;
            left: 0;
            height: 100%;
            width: 250px; /* 64 in tailwind */
            z-index: 40;
            background-color: #1f2937;
            color: #d1d5db;
            transform: translateX(-100%);
            transition: transform 0.3s ease-in-out;
        }
        .sidebar.show {
            transform: translateX(0);
        }
        .sidebar-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background-color: rgba(0,0,0,0.5);
            z-index: 30;
        }
        .sidebar.show + .sidebar-overlay {
            display: block;
        }
        .sidebar a { border-left: 3px solid transparent; transition: all 0.2s; }
        .sidebar a:hover { background-color: #374151; }
        .sidebar a.active { background-color: #4f46e5; color: white; border-left-color: #818cf8; }

        /* Main content takes full width on mobile */
        .main-content { width: 100%; }

        /* --- Desktop Styles --- */
        @media (min-width: 768px) {
            .sidebar {
                position: static; /* Unfix it for desktop */
                transform: translateX(0);
                flex-shrink: 0;
            }
            .sidebar-overlay, .mobile-header {
                display: none; /* Hide overlay and mobile header on desktop */
            }
            .main-content-container { display: flex; }
        }

        .stat-card { background-color: white; border-radius: 0.5rem; box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.06); }
        .page { display: none; }
        .page.active { display: block; animation: fadeIn 0.5s; }
        .btn { padding: 0.5rem 1rem; border-radius: 0.375rem; font-weight: 600; transition: all 0.2s; }
        .btn:active { transform: scale(0.95); }
        .btn-primary { background-color: #4f46e5; color: white; }
        .btn-primary:hover { background-color: #4338ca; }
        .btn-success { background-color: #10b981; color: white; }
        .btn-danger { background-color: #ef4444; color: white; }
        .btn-warning { background-color: #f59e0b; color: white; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 0.75rem; text-align: left; border-bottom: 1px solid #e5e7eb; }
        th { background-color: #f9fafb; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

    <div id="app-container" class="hidden">
        <!-- Mobile Header with Hamburger Menu -->
        <header class="mobile-header md:hidden sticky top-0 z-20 flex items-center justify-between p-4 bg-white shadow-md">
            <h1 id="page-title" class="text-xl font-bold text-gray-800">Dashboard</h1>
            <button id="menu-toggle-btn">
                <i data-lucide="menu" class="w-6 h-6"></i>
            </button>
        </header>

        <div class="main-content-container">
            <!-- Sidebar -->
            <aside class="sidebar">
                <div class="p-4 text-2xl font-bold text-white">CashReward</div>
                <nav class="mt-8">
                    <a href="#" class="nav-link flex items-center py-3 px-4" data-page="dashboard-page" data-title="Dashboard"><i data-lucide="home" class="w-5 h-5 mr-3"></i>Dashboard</a>
                    <a href="#" class="nav-link flex items-center py-3 px-4" data-page="users-page" data-title="Users"><i data-lucide="users" class="w-5 h-5 mr-3"></i>Users</a>
                    <a href="#" class="nav-link flex items-center py-3 px-4" data-page="withdrawals-page" data-title="Withdrawals"><i data-lucide="dollar-sign" class="w-5 h-5 mr-3"></i>Withdrawals</a>
                    <a href="#" class="nav-link flex items-center py-3 px-4" data-page="notifications-page" data-title="Notifications"><i data-lucide="send" class="w-5 h-5 mr-3"></i>Notifications</a>
                    <a href="#" class="nav-link flex items-center py-3 px-4" data-page="settings-page" data-title="Settings"><i data-lucide="settings" class="w-5 h-5 mr-3"></i>Settings</a>
                    <a href="#" id="admin-logout-btn" class="flex items-center py-3 px-4 mt-8"><i data-lucide="log-out" class="w-5 h-5 mr-3"></i>Logout</a>
                </nav>
            </aside>
            <div class="sidebar-overlay"></div>

            <!-- Main Content -->
            <main class="main-content flex-1 p-4 sm:p-6 lg:p-8 overflow-y-auto">
                 <h1 id="desktop-page-title" class="text-3xl font-bold mb-6 hidden md:block">Dashboard</h1>
                <!-- Dashboard Page -->
                <div id="dashboard-page" class="page">
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                        <div class="stat-card p-6"><h2 class="text-gray-500 text-lg">Total Users</h2><p id="total-users" class="text-4xl font-bold mt-2">0</p></div>
                        <div class="stat-card p-6"><h2 class="text-gray-500 text-lg">Pending Withdrawals</h2><p id="pending-withdrawals" class="text-4xl font-bold mt-2">0</p></div>
                    </div>
                </div>

                <!-- Users Page -->
                <div id="users-page" class="page">
                    <div class="bg-white shadow rounded-lg overflow-x-auto">
                        <table class="min-w-full">
                            <thead><tr><th>Name</th><th>Email</th><th>Balance</th><th>Status</th><th>Actions</th></tr></thead>
                            <tbody id="users-table-body"></tbody>
                        </table>
                    </div>
                </div>

                <!-- Withdrawals Page -->
                <div id="withdrawals-page" class="page">
                     <div class="bg-white shadow rounded-lg overflow-x-auto">
                        <table>
                            <thead><tr><th>User Name</th><th>Amount</th><th>Method</th><th>Details</th><th>Requested At</th><th>Actions</th></tr></thead>
                            <tbody id="withdrawals-table-body"></tbody>
                        </table>
                    </div>
                </div>

                <!-- Notifications Page -->
                <div id="notifications-page" class="page">
                    <div class="stat-card p-6 w-full max-w-lg mx-auto md:mx-0">
                        <div class="mb-4"><label for="notification-title" class="block font-semibold mb-2">Title</label><input type="text" id="notification-title" class="w-full px-4 py-2 border rounded-lg"></div>
                        <div class="mb-6"><label for="notification-message" class="block font-semibold mb-2">Message</label><textarea id="notification-message" rows="4" class="w-full px-4 py-2 border rounded-lg"></textarea></div>
                        <button id="send-notification-btn" class="btn btn-primary w-full">Send to All Users</button>
                    </div>
                </div>

                <!-- Settings Page -->
                <div id="settings-page" class="page">
                    <div class="stat-card p-6 w-full max-w-lg mx-auto md:mx-0">
                        <div class="mb-6"><label for="min-withdrawal" class="block font-semibold mb-2">Minimum Withdrawal (Coins)</label><input type="number" id="min-withdrawal" class="w-full px-4 py-2 border rounded-lg"></div>
                        <div class="mb-6"><label for="daily-ad-limit" class="block font-semibold mb-2">Daily Ad Watch Limit</label><input type="number" id="daily-ad-limit" class="w-full px-4 py-2 border rounded-lg"></div>
                        <div class="mb-6">
                            <label class="block font-semibold mb-2">Coin Value</label>
                            <div class="flex items-center space-x-2">
                                <input type="number" id="coin-value-coins" class="w-full px-4 py-2 border rounded-lg" placeholder="Coins">
                                <span class="font-semibold text-gray-600">= ৳</span>
                                <input type="number" id="coin-value-taka" class="w-full px-4 py-2 border rounded-lg" placeholder="Taka">
                            </div>
                        </div>
                        <div class="mb-6"><label class="block font-semibold mb-2">Payment Methods (comma-separated)</label><input type="text" id="payment-methods" class="w-full px-4 py-2 border rounded-lg" placeholder="e.g. bKash,Nagad,Rocket"></div>
                        <button id="save-settings-btn" class="btn btn-primary w-full">Save Settings</button>
                    </div>
                </div>
            </main>
        </div>
    </div>
    
    <!-- Login View -->
    <div id="admin-login-view" class="min-h-screen flex items-center justify-center bg-gray-100">
        <div class="w-full max-w-md p-8 space-y-6 bg-white rounded-lg shadow-md">
            <h1 class="text-3xl font-bold text-center text-gray-900">Admin Panel Login</h1>
            <input id="admin-email" type="email" placeholder="Admin Email" class="w-full px-4 py-2 border rounded-lg">
            <input id="admin-password" type="password" placeholder="Password" class="w-full px-4 py-2 border rounded-lg">
            <button id="admin-login-btn" class="w-full btn btn-primary">Login</button>
        </div>
    </div>

    <!-- Alert / Confirm Modals can be added here if needed -->
    
    <script type="module">
        const firebaseConfig = {
          apiKey: "AIzaSyDGW23aqYMKAjqBmrYNy6fN8kB8y2aiWR8",
          authDomain: "rbots-4f874.firebaseapp.com",
          databaseURL: "https://rbots-4f874-default-rtdb.firebaseio.com",
          projectId: "rbots-4f874",
          storageBucket: "rbots-4f874.appspot.com",
          messagingSenderId: "217252882686",
          appId: "1:217252882686:web:e5217da460e04fcb7e9daf"
        };
        
        const ADMIN_UIDS = ["5XlYAIUUH3WAWuVQKNppu4TZleo2"]; 

        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-app.js";
        import { getAuth, onAuthStateChanged, signInWithEmailAndPassword, signOut } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-auth.js";
        import { getFirestore, collection, doc, getDoc, setDoc, updateDoc, query, where, onSnapshot, addDoc, serverTimestamp } from "https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore.js";

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        const loginView = document.getElementById('admin-login-view');
        const appContainer = document.getElementById('app-container');
        const sidebar = document.querySelector('.sidebar');
        const sidebarOverlay = document.querySelector('.sidebar-overlay');
        const menuToggleBtn = document.getElementById('menu-toggle-btn');

        function showAlert(message) { alert(message); }
        function showConfirm(message) { return confirm(message); }

        window.onload = () => {
            lucide.createIcons();
            setupEventListeners();
            onAuthStateChanged(auth, (user) => {
                if (user && ADMIN_UIDS.includes(user.uid)) {
                    loginView.style.display = 'none';
                    appContainer.classList.remove('hidden');
                    navigateTo('dashboard-page', 'Dashboard');
                    loadDashboardData();
                    loadUsers();
                    loadWithdrawals();
                    loadSettings();
                } else {
                    loginView.style.display = 'flex';
                    appContainer.classList.add('hidden');
                }
            });
        };
        
        function closeSidebar() {
            sidebar.classList.remove('show');
        }

        function navigateTo(pageId, pageTitle) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
            
            document.querySelectorAll('.nav-link').forEach(link => {
                link.classList.remove('active');
                if (link.dataset.page === pageId) link.classList.add('active');
            });

            document.getElementById('page-title').textContent = pageTitle;
            document.getElementById('desktop-page-title').textContent = pageTitle;
            closeSidebar();
        }

        function setupEventListeners() {
            document.getElementById('admin-login-btn').addEventListener('click', handleAdminLogin);
            document.getElementById('admin-logout-btn').addEventListener('click', () => signOut(auth));
            document.getElementById('save-settings-btn').addEventListener('click', saveSettings);
            document.getElementById('send-notification-btn').addEventListener('click', sendNotification);
            document.querySelectorAll('.nav-link').forEach(link => {
                link.addEventListener('click', (e) => { 
                    e.preventDefault(); 
                    navigateTo(link.dataset.page, link.dataset.title); 
                });
            });
            menuToggleBtn.addEventListener('click', () => sidebar.classList.toggle('show'));
            sidebarOverlay.addEventListener('click', closeSidebar);
        }

        async function handleAdminLogin() {
            const email = document.getElementById('admin-email').value;
            const password = document.getElementById('admin-password').value;
            try {
                const userCredential = await signInWithEmailAndPassword(auth, email, password);
                if (!ADMIN_UIDS.includes(userCredential.user.uid)) {
                    await signOut(auth);
                    showAlert("You are not authorized to access this panel.");
                }
            } catch (error) { showAlert(error.message); }
        }

        function loadDashboardData() {
            onSnapshot(collection(db, "users"), snap => { document.getElementById('total-users').textContent = snap.size; });
            onSnapshot(query(collection(db, "withdrawals"), where("status", "==", "pending")), snap => { document.getElementById('pending-withdrawals').textContent = snap.size; });
        }

        function loadUsers() {
            const usersTableBody = document.getElementById('users-table-body');
            onSnapshot(collection(db, "users"), (snapshot) => {
                usersTableBody.innerHTML = '';
                snapshot.forEach(doc => {
                    const user = doc.data();
                    const isBlocked = user.isBlocked || false;
                    const row = `<tr>
                        <td class="whitespace-nowrap">${user.name || 'N/A'}</td><td>${user.email}</td><td>${user.balance || 0}</td>
                        <td><span class="px-2 inline-flex text-xs leading-5 font-semibold rounded-full ${isBlocked ? 'bg-red-100 text-red-800' : 'bg-green-100 text-green-800'}">${isBlocked ? 'Blocked' : 'Active'}</span></td>
                        <td><button class="btn text-xs ${isBlocked ? 'btn-success' : 'btn-danger'}" onclick="window.toggleBlockUser('${doc.id}', ${isBlocked})">${isBlocked ? 'Unblock' : 'Block'}</button></td>
                    </tr>`;
                    usersTableBody.innerHTML += row;
                });
            });
        }

        function loadWithdrawals() {
            const withdrawalsTableBody = document.getElementById('withdrawals-table-body');
            const q = query(collection(db, "withdrawals"), where("status", "==", "pending"));
            onSnapshot(q, (snapshot) => {
                withdrawalsTableBody.innerHTML = '';
                 if (snapshot.empty) {
                    withdrawalsTableBody.innerHTML = '<tr><td colspan="6" class="text-center text-gray-500 py-4">No pending requests.</td></tr>';
                    return;
                }
                snapshot.forEach(docSnap => {
                    const request = docSnap.data();
                    const requestedDate = request.requestedAt?.toDate().toLocaleString() || 'N/A';
                    const row = `<tr>
                        <td>${request.userName || 'N/A'}</td><td>${request.amount}</td><td>${request.method}</td>
                        <td>${request.paymentDetail || 'N/A'}</td><td>${requestedDate}</td>
                        <td class="flex flex-col sm:flex-row gap-2">
                            <button class="btn btn-success text-xs" onclick="window.handleWithdrawal('${docSnap.id}', 'approved')">Approve</button>
                            <button class="btn btn-danger text-xs" onclick="window.handleWithdrawal('${docSnap.id}', 'rejected')">Reject</button>
                        </td>
                    </tr>`;
                    withdrawalsTableBody.innerHTML += row;
                });
            });
        }
        
        async function loadSettings() {
            const configRef = doc(db, "config", "main");
            try {
                const docSnap = await getDoc(configRef);
                if (docSnap.exists()) {
                    const config = docSnap.data();
                    document.getElementById('min-withdrawal').value = config.minWithdrawal || '';
                    document.getElementById('daily-ad-limit').value = config.dailyAdLimit || '';
                    document.getElementById('coin-value-coins').value = config.coinValueCoins || '';
                    document.getElementById('coin-value-taka').value = config.coinValueTaka || '';
                    document.getElementById('payment-methods').value = (config.paymentMethods || []).join(',');
                }
            } catch (e) { console.error("Error loading settings: ", e); }
        }

        window.toggleBlockUser = async (userId, isBlocked) => {
            const action = isBlocked ? 'unblock' : 'block';
            if (showConfirm(`Are you sure you want to ${action} this user?`)) {
                try {
                    await updateDoc(doc(db, "users", userId), { isBlocked: !isBlocked });
                    showAlert(`User has been successfully ${action}ed.`);
                } catch (error) { showAlert(`Error: ${error.message}`); }
            }
        };

        window.handleWithdrawal = async (requestId, newStatus) => {
            if (showConfirm(`Are you sure you want to ${newStatus} this request?`)) {
                const requestRef = doc(db, "withdrawals", requestId);
                try {
                    // If rejecting, refund the coins to the user
                    if (newStatus === 'rejected') {
                        const requestSnap = await getDoc(requestRef);
                        if (requestSnap.exists()) {
                            const requestData = requestSnap.data();
                            const userRef = doc(db, "users", requestData.userId);
                            const userSnap = await getDoc(userRef);
                            if(userSnap.exists()){
                               const currentBalance = userSnap.data().balance || 0;
                               await updateDoc(userRef, { balance: currentBalance + requestData.amount });
                            }
                        }
                    }
                    await updateDoc(requestRef, { status: newStatus });
                    showAlert(`Request has been ${newStatus}.`);
                } catch (error) { showAlert(`Error: ${error.message}`); }
            }
        };

        async function sendNotification() {
            const title = document.getElementById('notification-title').value;
            const message = document.getElementById('notification-message').value;
            if (!title || !message) return showAlert("Title and message are both required.");
            try {
                await addDoc(collection(db, "notifications"), { title, message, createdAt: serverTimestamp() });
                showAlert("Notification has been sent to all users!");
                document.getElementById('notification-title').value = '';
                document.getElementById('notification-message').value = '';
            } catch (error) { showAlert(`Error: ${error.message}`); }
        }

        async function saveSettings() {
            const settingsData = {
                minWithdrawal: parseInt(document.getElementById('min-withdrawal').value) || 0,
                dailyAdLimit: parseInt(document.getElementById('daily-ad-limit').value) || 0,
                coinValueCoins: parseInt(document.getElementById('coin-value-coins').value) || 0,
                coinValueTaka: parseFloat(document.getElementById('coin-value-taka').value) || 0,
                paymentMethods: document.getElementById('payment-methods').value.split(',').map(s => s.trim()).filter(Boolean)
            };
            try {
                await setDoc(doc(db, "config", "main"), settingsData, { merge: true });
                showAlert("Settings have been saved successfully!");
            } catch (error) { showAlert(`Error saving settings: ${error.message}`); }
        }
    </script>
</body>
</html>
