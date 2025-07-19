<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>TapSwap Game</title>
    <!-- Telegram WebApp JS -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        :root {
            --fallback-bg-color: #f7f8fa;
            --fallback-text-color: #1c1e21;
            --fallback-hint-color: #6c757d;
            --fallback-link-color: #007aff;
            --fallback-button-color: #007aff;
            --fallback-button-text-color: #ffffff;
            --fallback-secondary-bg-color: #ffffff;
            --primary-color: var(--tg-theme-button-color, var(--fallback-button-color));
            --secondary-color: var(--tg-theme-hint-color, var(--fallback-hint-color));
            --background-color: var(--tg-theme-bg-color, var(--fallback-bg-color));
            --text-color: var(--tg-theme-text-color, var(--fallback-text-color));
            --card-bg: var(--tg-theme-secondary-bg-color, var(--fallback-secondary-bg-color));
            --border-color: #eef0f2;
            --success-color: #34c759;
            --warning-color: #ff9500;
            --danger-color: #ff3b30;
            --energy-color: #ffcc00;
            --energy-bg: #e5e5ea;
            --bottom-nav-bg: var(--tg-theme-secondary-bg-color, #ffffff);
            --bottom-nav-border: rgba(60, 60, 67, 0.15);
            --bottom-nav-icon-color: var(--tg-theme-hint-color, #8e8e93);
            --bottom-nav-icon-active-color: var(--tg-theme-button-color, var(--fallback-link-color));
            --shadow-color-light: rgba(0, 0, 0, 0.06);
        }
        body.dark {
            --fallback-bg-color: #000000;
            --fallback-text-color: #ffffff;
            --fallback-hint-color: #8e8e93;
            --fallback-link-color: #0a84ff;
            --fallback-button-color: #0a84ff;
            --fallback-secondary-bg-color: #1c1c1e;
            --border-color: rgba(84, 84, 88, 0.4);
            --energy-bg: #3a3a3c;
            --bottom-nav-bg: #1c1c1e;
            --bottom-nav-border: rgba(84, 84, 88, 0.65);
            --shadow-color-light: rgba(0, 0, 0, 0.3);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        html, body {
            height: 100%;
            width: 100%;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            overscroll-behavior-y: none;
            background-color: var(--background-color);
            color: var(--text-color);
            font-size: 16px;
            line-height: 1.5;
        }
        #app { display: flex; flex-direction: column; height: 100vh; width: 100%; overflow: hidden; }
        #content { flex-grow: 1; overflow-y: auto; padding: 20px; padding-bottom: 85px; -webkit-overflow-scrolling: touch; }
        .page { display: none; flex-direction: column; align-items: center; width: 100%; animation: fadeIn 0.3s ease-out; }
        .page.active { display: flex; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        #bottomNav { position: fixed; bottom: 0; left: 0; right: 0; height: 70px; background-color: var(--bottom-nav-bg); border-top: 1px solid var(--bottom-nav-border); display: flex; justify-content: space-around; align-items: stretch; padding: 5px 0 10px 0; box-shadow: 0 -2px 10px var(--shadow-color-light); z-index: 1000; }
        .nav-button { background: none; border: none; color: var(--bottom-nav-icon-color); cursor: pointer; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 11px; font-weight: 500; flex-grow: 1; padding: 8px 0; transition: color 0.2s ease, transform 0.1s ease; position: relative; }
        .nav-button svg { width: 26px; height: 26px; margin-bottom: 4px; fill: currentColor; }
        .nav-button.active { color: var(--bottom-nav-icon-active-color); }
        .nav-button:active { transform: scale(0.95); }
        .card { background-color: var(--card-bg); border-radius: 18px; padding: 25px; margin-bottom: 20px; width: 100%; max-width: 550px; box-shadow: 0 5px 15px var(--shadow-color-light); border: 1px solid var(--border-color); overflow: hidden; }
        .card h2 { margin-bottom: 20px; font-size: 1.4em; font-weight: 600; color: var(--text-color); text-align: center; }
        #profilePage .info-item { display: flex; justify-content: space-between; align-items: center; padding: 12px 0; border-bottom: 1px solid var(--border-color); }
        #profilePage .info-item:last-child { border-bottom: none; }
        #profilePage .info-item span:first-child { color: var(--secondary-color); margin-right: 15px; }
        #profilePage .info-item span:last-child { font-weight: 500; text-align: right; word-break: break-all; color: var(--text-color); }
        #profilePage .profile-pic { width: 100px; height: 100px; border-radius: 50%; margin: 0 auto 25px auto; display: block; background-color: var(--border-color); border: 3px solid var(--card-bg); box-shadow: 0 2px 5px var(--shadow-color-light); object-fit: cover; background-size: cover; background-position: center; }
        #tapPage { justify-content: space-around; height: 100%; text-align: center; padding-top: 5vh; }
        .tap-area { display: flex; flex-direction: column; align-items: center; justify-content: center; flex-grow: 1; margin-bottom: 20px; }
        #catButton { background: none; border: none; cursor: pointer; padding: 0; user-select: none; -webkit-user-select: none; margin-bottom: 30px; }
        #catImage { width: 200px; height: 200px; border-radius: 50%; background: radial-gradient(circle, var(--warning-color) 60%, color-mix(in srgb, var(--warning-color) 80%, black)); display: flex; align-items: center; justify-content: center; font-size: 60px; box-shadow: 0 8px 25px color-mix(in srgb, var(--warning-color) 40%, transparent); border: 5px solid var(--background-color); background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><text y=".9em" font-size="90">🐱</text></svg>'); background-size: 75%; background-repeat: no-repeat; background-position: center; transition: transform 0.1s ease-out; }
        #catButton:active { transform: scale(0.92); }
        .score-display { font-size: 3.2em; font-weight: 700; margin-bottom: 15px; color: var(--text-color); display: flex; align-items: center; justify-content: center; gap: 10px; }
        .score-icon { display: inline-block; width: 36px; height: 36px; vertical-align: middle; background-color: var(--energy-color); border-radius: 50%; background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffffff"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm.99 14.71L10 15.03V9h2v5.03l2.01 1.68-.99 1z"/></svg>'); background-size: 65%; background-repeat: no-repeat; background-position: center; box-shadow: 0 1px 3px rgba(0,0,0,0.2); }
        .energy-section { width: 100%; max-width: 450px; margin: 0 auto; padding: 0 10px; }
        .energy-label { font-size: 1em; color: var(--secondary-color); margin-bottom: 8px; display: flex; justify-content: space-between; align-items: center; padding: 0 5px; }
        .energy-label span:last-child { font-weight: 600; color: var(--text-color); }
        .energy-bar-container { width: 100%; height: 16px; background-color: var(--energy-bg); border-radius: 8px; overflow: hidden; margin-bottom: 15px; box-shadow: inset 0 1px 3px rgba(0,0,0,0.1); }
        #energyBar { height: 100%; width: 100%; background: linear-gradient(90deg, color-mix(in srgb, var(--energy-color) 90%, #fff) 0%, var(--energy-color) 100%); border-radius: 8px; transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        #taskPage ul { list-style: none; width: 100%; max-width: 550px; padding: 0; }
        #taskPage li { background-color: var(--card-bg); border: 1px solid var(--border-color); border-radius: 12px; padding: 15px 20px; margin-bottom: 12px; display: flex; justify-content: space-between; align-items: center; }
        #taskPage .task-info { flex-grow: 1; margin-right: 15px; }
        #taskPage .task-title { font-weight: 500; margin-bottom: 4px; color: var(--text-color); }
        #taskPage .task-reward { font-size: 0.9em; color: var(--secondary-color); display: flex; align-items: center; gap: 4px; }
        #taskPage .task-reward svg { width: 16px; height: 16px; fill: var(--warning-color); flex-shrink: 0; }
        #taskPage .task-button { padding: 9px 18px; font-size: 0.95em; font-weight: 500; background-color: var(--primary-color); color: var(--fallback-button-text-color); border: none; border-radius: 10px; cursor: pointer; flex-shrink: 0; }
        #taskPage .task-button:disabled { background-color: color-mix(in srgb, var(--secondary-color) 50%, transparent); color: color-mix(in srgb, var(--text-color) 50%, transparent); cursor: not-allowed; opacity: 0.7; }
        #taskPage .task-button.completed { background-color: var(--success-color); color: white; }
        #taskPage .task-icon { width: 36px; height: 36px; margin-right: 15px; flex-shrink: 0; color: var(--tg-theme-link-color, var(--primary-color)); }
        #taskPage .task-icon svg { width: 100%; height: 100%; fill: currentColor; }
        .form-group { margin-bottom: 20px; }
        .form-group label { display: block; margin-bottom: 8px; font-size: 0.9em; font-weight: 500; color: var(--secondary-color); }
        .form-group input, .form-group select { width: 100%; padding: 14px 16px; border: 1px solid var(--border-color); border-radius: 10px; font-size: 1em; background-color: var(--background-color); color: var(--text-color); }
        .withdraw-button { width: 100%; padding: 16px; font-size: 1.1em; font-weight: 600; background-color: var(--primary-color); color: var(--fallback-button-text-color); border: none; border-radius: 12px; cursor: pointer; margin-top: 10px; }
        .withdraw-button:disabled { background-color: color-mix(in srgb, var(--secondary-color) 50%, transparent); cursor: not-allowed; opacity: 0.7; }
        .withdraw-message { margin-top: 20px; font-size: 0.95em; text-align: center; padding: 10px; border-radius: 8px; display: none; }
        .withdraw-message.success { color: var(--success-color); background-color: color-mix(in srgb, var(--success-color) 15%, transparent); }
        .withdraw-message.error { color: var(--danger-color); background-color: color-mix(in srgb, var(--danger-color) 15%, transparent); }
        .tap-feedback { position: absolute; font-size: 1.8em; font-weight: bold; color: var(--text-color); opacity: 1; animation: floatUp 0.8s ease-out forwards; pointer-events: none; user-select: none; white-space: nowrap; }
        @keyframes floatUp { to { transform: translateY(-70px) scale(0.7); opacity: 0; } }
        #loadingOverlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background-color: color-mix(in srgb, var(--background-color) 85%, transparent); display: flex; justify-content: center; align-items: center; z-index: 9999; font-size: 1.2em; backdrop-filter: blur(4px); -webkit-backdrop-filter: blur(4px); transition: opacity 0.3s ease-out; }
        .energy-section.shake { animation: shake 0.3s ease-in-out; }
        @keyframes shake { 0%, 100% { transform: translateX(0); } 25% { transform: translateX(-3px); } 75% { transform: translateX(3px); } }
        #notificationBanner { display: none; position: fixed; top: 0; left: 0; right: 0; padding: 12px 20px; background-color: var(--primary-color); color: var(--fallback-button-text-color); text-align: center; font-size: 0.9em; z-index: 2000; box-shadow: 0 2px 5px rgba(0,0,0,0.2); animation: slideDown 0.5s ease-out; }
        @keyframes slideDown { from { transform: translateY(-100%); } to { transform: translateY(0); } }
    </style>
</head>
<body>
    <div id="app">
         <div id="loadingOverlay"><p>Connecting...</p></div>
        <div id="notificationBanner"></div>

        <div id="content">
            <!-- Profile Page -->
            <div id="profilePage" class="page">
                <div class="card">
                    <h2>Profile</h2>
                    <img id="profilePic" class="profile-pic" alt="Profile Picture">
                    <div class="info-item"><span>Name:</span><span id="profileName">...</span></div>
                    <div class="info-item"><span>User ID:</span><span id="profileUserId">...</span></div>
                    <div class="info-item"><span>Points:</span><span id="profilePoints">0</span></div>
                    <div class="info-item"><span>Joined:</span><span id="profileJoinDate">...</span></div>
                    <div class="info-item"><span>Referral Link:</span><span id="profileReferral">...</span></div>
                </div>
            </div>

            <!-- Tap Page -->
            <div id="tapPage" class="page active">
                 <div class="score-display"><span class="score-icon"></span><span id="pointsDisplay">0</span></div>
                <div class="tap-area"><button id="catButton"><div id="catImage"></div></button></div>
                <div class="energy-section">
                     <div class="energy-label"><span>⚡ Energy</span><span id="energyLevel">... / ...</span></div>
                    <div class="energy-bar-container"><div id="energyBar"></div></div>
                </div>
            </div>

            <!-- Task Page -->
            <div id="taskPage" class="page">
                 <div class="card">
                     <h2>Tasks</h2>
                     <p style="text-align: center; color: var(--secondary-color); font-size: 0.95em; margin-top: -10px; margin-bottom: 25px;">Complete tasks to earn more points!</p>
                     <ul id="taskList"></ul>
                 </div>
            </div>

            <!-- Withdraw Page -->
            <div id="withdrawPage" class="page">
                 <div class="card">
                     <h2>Withdraw Points</h2>
                     <p style="text-align: center; color: var(--secondary-color); margin-bottom: 25px;">Available: <strong id="withdrawPointsAvailable" style="color: var(--text-color);">0</strong></p>
                     <div class="form-group"><label for="withdrawAmount">Amount:</label><input type="number" id="withdrawAmount" placeholder="Enter points" min="1"></div>
                     <div class="form-group"><label for="withdrawMethod">Method:</label><select id="withdrawMethod"><option value="paypal">PayPal</option><option value="wire">Wire Transfer</option><option value="crypto">Crypto (USDT)</option></select></div>
                     <div id="methodDetails">
                          <div class="form-group detail-field paypal-field"><label for="paypalEmail">PayPal Email:</label><input type="email" id="paypalEmail" placeholder="your.email@example.com"></div>
                          <div class="form-group detail-field wire-field" style="display: none;"><label for="bankAccount">Bank Account Number:</label><input type="text" id="bankAccount"><label for="bankRouting" style="margin-top: 10px;">Routing Number:</label><input type="text" id="bankRouting"></div>
                          <div class="form-group detail-field crypto-field" style="display: none;"><label for="cryptoAddress">USDT Wallet Address (TRC20):</label><input type="text" id="cryptoAddress" placeholder="T..."></div>
                     </div>
                     <button id="submitWithdrawal" class="withdraw-button">Request Withdrawal</button>
                     <p id="withdrawMessage" class="withdraw-message"></p>
                 </div>
            </div>
        </div>

        <!-- Bottom Navigation Bar -->
        <nav id="bottomNav">
            <button class="nav-button" data-page="profilePage"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/></svg>Profile</button>
            <button class="nav-button active" data-page="tapPage"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M11.92 2.75a.75.75 0 0 1 .58.2l7.5 7.5a.75.75 0 0 1 0 1.06l-4.25 4.25a.75.75 0 1 1-1.06-1.06l3.72-3.72-7.44-7.44A.75.75 0 0 1 11.92 2.75zM3.81 9.28a.75.75 0 0 1 0-1.06l4.25-4.25a.75.75 0 1 1 1.06 1.06L5.4 8.75l7.44 7.44a.75.75 0 0 1-1.06 1.06l-7.5-7.5a.75.75 0 0 1-.47-.77z"/></svg>Tap</button>
            <button class="nav-button" data-page="taskPage"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M16.5 3h-9C6.12 3 5 4.12 5 5.5v13C5 19.88 6.12 21 7.5 21h9c1.38 0 2.5-1.12 2.5-2.5v-13C19 4.12 17.88 3 16.5 3zM15 18H9v-1.5h6V18zm0-3.5H9V13h6v1.5zm0-3.5H9V8h6v3z"/></svg>Tasks</button>
            <button class="nav-button" data-page="withdrawPage"><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M20 4H4c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 14H4V8h16v10zm0-12H4V6h16v2zm-4 8H8v-2h8v2z"/></svg>Withdraw</button>
        </nav>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.15.0/firebase-app.js";
        import { getDatabase, ref, onValue, set, update, runTransaction, serverTimestamp, push, query, orderByChild, limitToLast } from "https://www.gstatic.com/firebasejs/9.15.0/firebase-database.js";

        const firebaseConfig = {
            apiKey: "AIzaSyAeoIjJuvLCGDJVvC0pSTHuitUKLq4PL5k",
            authDomain: "fir-17e00.firebaseapp.com",
            databaseURL: "https://fir-17e00-default-rtdb.firebaseio.com",
            projectId: "fir-17e00",
            storageBucket: "fir-17e00.firebasestorage.app",
            messagingSenderId: "1020536343739",
            appId: "1:1020536343739:web:829403344cf54bb2f6ed9e"
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const tg = window.Telegram.WebApp;

        let currentUserId = null;
        let localUserData = {};
        let dbTasks = {};

        const ENERGY_REFILL_RATE = 2;
        const ENERGY_PER_TAP = 1;
        const POINTS_PER_TAP = 1;

        const elements = {
            loadingOverlay: document.getElementById('loadingOverlay'),
            notificationBanner: document.getElementById('notificationBanner'),
            content: document.getElementById('content'),
            pages: document.querySelectorAll('.page'),
            navButtons: document.querySelectorAll('.nav-button'),
            pointsDisplay: document.getElementById('pointsDisplay'),
            profilePoints: document.getElementById('profilePoints'),
            withdrawPointsAvailable: document.getElementById('withdrawPointsAvailable'),
            catButton: document.getElementById('catButton'),
            energyLevelDisplay: document.getElementById('energyLevel'),
            energyBar: document.getElementById('energyBar'),
            taskList: document.getElementById('taskList'),
            profileName: document.getElementById('profileName'),
            profileUserId: document.getElementById('profileUserId'),
            profileJoinDate: document.getElementById('profileJoinDate'),
            profileReferral: document.getElementById('profileReferral'),
            profilePic: document.getElementById('profilePic'),
            withdrawAmountInput: document.getElementById('withdrawAmount'),
            withdrawMethodSelect: document.getElementById('withdrawMethod'),
            submitWithdrawalButton: document.getElementById('submitWithdrawal'),
            withdrawMessage: document.getElementById('withdrawMessage'),
            paypalEmailInput: document.getElementById('paypalEmail'),
            bankAccountInput: document.getElementById('bankAccount'),
            bankRoutingInput: document.getElementById('bankRouting'),
            cryptoAddressInput: document.getElementById('cryptoAddress'),
            detailFields: document.querySelectorAll('.detail-field')
        };
        
        function initApp() {
            tg.ready();
            applyTheme();
            tg.onEvent('themeChanged', applyTheme);
            tg.expand();
            try { tg.disableVerticalSwipes(); } catch (e) { console.warn("disableVerticalSwipes not supported."); }

            if (tg.initDataUnsafe && tg.initDataUnsafe.user) {
                currentUserId = tg.initDataUnsafe.user.id;
                setupFirebaseListeners();
            } else {
                elements.loadingOverlay.innerHTML = "<p>Cannot identify user. Please launch via Telegram.</p>";
                return;
            }
            
            setupEventListeners();
            showPage('tapPage');
            setInterval(refillEnergy, 1000);
        }

        function setupFirebaseListeners() {
            onValue(ref(db, 'tasks'), snapshot => {
                dbTasks = snapshot.val() || {};
                renderTasks();
            });
            
            const userRef = ref(db, 'users/' + currentUserId);
            onValue(userRef, snapshot => {
                if (snapshot.exists()) {
                    localUserData = snapshot.val();
                    if(localUserData.isBanned) {
                        document.body.innerHTML = `<div style="text-align: center; padding: 50px; font-size: 1.2em; color: var(--text-color);">You have been banned.</div>`;
                        return;
                    }
                    updateAllDisplays();
                } else {
                    const tgUser = tg.initDataUnsafe.user;
                    const newUserProfile = {
                        profile: { username: tgUser.username || `user${tgUser.id}`, firstName: tgUser.first_name || '', lastName: tgUser.last_name || '', photoUrl: tgUser.photo_url || '', joinDate: serverTimestamp() },
                        gameData: { points: 0, energy: 1000, maxEnergy: 1000, lastEnergyUpdate: serverTimestamp() },
                        tasks: {}, isBanned: false
                    };
                    set(userRef, newUserProfile);
                }
                if (elements.loadingOverlay.style.display !== 'none') {
                    elements.loadingOverlay.style.opacity = '0';
                    setTimeout(() => { elements.loadingOverlay.style.display = 'none'; }, 300);
                }
            });

            onValue(query(ref(db, 'notifications'), orderByChild('timestamp'), limitToLast(1)), snapshot => {
                if(snapshot.exists()) {
                    const [key] = Object.keys(snapshot.val());
                    showNotification(snapshot.val()[key].message);
                }
            });
        }
        
        function updateAllDisplays() {
            if(!localUserData.gameData) return;
            const points = localUserData.gameData.points || 0;
            const formattedPoints = Math.floor(points).toLocaleString('en-US');
            elements.pointsDisplay.textContent = formattedPoints;
            elements.profilePoints.textContent = formattedPoints;
            elements.withdrawPointsAvailable.textContent = formattedPoints;
            elements.withdrawAmountInput.max = Math.floor(points);

            const currentEnergy = Math.floor(localUserData.gameData.energy || 0);
            const maxEnergy = localUserData.gameData.maxEnergy || 1000;
            elements.energyLevelDisplay.textContent = `${currentEnergy.toLocaleString('en-US')} / ${maxEnergy.toLocaleString('en-US')}`;
            elements.energyBar.style.width = `${maxEnergy > 0 ? (currentEnergy / maxEnergy) * 100 : 0}%`;

            const profile = localUserData.profile;
            if (profile) {
                elements.profileName.textContent = `${profile.firstName || ''} ${profile.lastName || ''}`.trim() || profile.username;
                elements.profileUserId.textContent = currentUserId;
                elements.profilePic.src = profile.photoUrl || '';
                elements.profileJoinDate.textContent = profile.joinDate ? new Date(profile.joinDate).toLocaleDateString() : 'N/A';
                const link = `https://t.me/YourTapSwapBot?start=${currentUserId}`; //TODO: Your bot name
                elements.profileReferral.textContent = link;
                elements.profileReferral.onclick = () => tg.openTelegramLink(link);
            }
            renderTasks();
            updateWithdrawButtonState();
        }

        function applyTheme() {
             // Theme logic from previous steps...
        }
        
        function showNotification(message) {
            elements.notificationBanner.textContent = message;
            elements.notificationBanner.style.display = 'block';
            setTimeout(() => { elements.notificationBanner.style.display = 'none'; }, 5000);
        }

        function showPage(pageId) {
            elements.pages.forEach(page => page.classList.toggle('active', page.id === pageId));
            elements.navButtons.forEach(button => button.classList.toggle('active', button.dataset.page === pageId));
            elements.content.scrollTop = 0;
        }

        function handleTap(event) {
            if (localUserData.gameData.energy < ENERGY_PER_TAP) {
                document.querySelector('.energy-section').classList.add('shake');
                setTimeout(() => document.querySelector('.energy-section').classList.remove('shake'), 300);
                return;
            }

            runTransaction(ref(db, `users/${currentUserId}/gameData`), data => {
                if (data && data.energy >= ENERGY_PER_TAP) {
                    data.points = (data.points || 0) + POINTS_PER_TAP;
                    data.energy -= ENERGY_PER_TAP;
                    data.lastEnergyUpdate = serverTimestamp();
                }
                return data;
            });
            const x = event.clientX || (elements.catButton.offsetLeft + elements.catButton.offsetWidth / 2);
            const y = event.clientY || (elements.catButton.offsetTop + elements.catButton.offsetHeight / 2);
            const feedback = document.createElement('div');
            feedback.textContent = `+${POINTS_PER_TAP}`;
            feedback.className = 'tap-feedback';
            feedback.style.left = `${x}px`;
            feedback.style.top = `${y}px`;
            document.body.appendChild(feedback);
            feedback.addEventListener('animationend', () => feedback.remove());
        }

        function refillEnergy() {
            if (!localUserData.gameData || localUserData.gameData.energy >= localUserData.gameData.maxEnergy || !localUserData.gameData.lastEnergyUpdate) return;
            const timeElapsed = (Date.now() - localUserData.gameData.lastEnergyUpdate) / 1000;
            if (timeElapsed > 1) {
                const newEnergy = Math.min(localUserData.gameData.maxEnergy, localUserData.gameData.energy + timeElapsed * ENERGY_REFILL_RATE);
                update(ref(db, `users/${currentUserId}/gameData`), { energy: newEnergy, lastEnergyUpdate: serverTimestamp() });
            }
        }

        function renderTasks() {
            elements.taskList.innerHTML = '';
            Object.keys(dbTasks).forEach(taskId => {
                const taskInfo = dbTasks[taskId];
                const isCompleted = localUserData.tasks && localUserData.tasks[taskId];
                const li = document.createElement('li');
                li.innerHTML = `<span class="task-icon"><svg viewBox="0 0 24 24"><path d="M3.9 12c0-1.71 1.39-3.1 3.1-3.1h4V7H7c-2.76 0-5 2.24-5 5s2.24 5 5 5h4v-1.9H7c-1.71 0-3.1-1.39-3.1-3.1zM8 13h8v-2H8v2zm9-6h-4v1.9h4c1.71 0 3.1 1.39 3.1 3.1s-1.39 3.1-3.1 3.1h-4V17h4c2.76 0 5-2.24 5-5s-2.24-5-5-5z"/></svg></span><div class="task-info"><div class="task-title">${taskInfo.title}</div><div class="task-reward"><svg viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm.99 14.71L10 15.03V9h2v5.03l2.01 1.68-.99 1z"/></svg>+${taskInfo.reward.toLocaleString()} Points</div></div><button class="task-button" data-task-id="${taskId}" ${isCompleted ? 'disabled' : ''}>${isCompleted ? 'Claimed' : 'Go'}</button>`;
                if (isCompleted) li.querySelector('.task-button').classList.add('completed');
                else li.querySelector('.task-button').addEventListener('click', () => completeTask(taskId, taskInfo.reward));
                elements.taskList.appendChild(li);
            });
        }
        
        function completeTask(taskId, reward) {
            runTransaction(ref(db, `users/${currentUserId}`), data => {
                if (data && (!data.tasks || !data.tasks[taskId])) {
                    if (!data.tasks) data.tasks = {};
                    data.tasks[taskId] = true;
                    data.gameData.points += reward;
                    setTimeout(() => tg.showPopup({ title: 'Task Complete!', message: `You earned ${reward.toLocaleString()} points.`, buttons: [{ type: 'ok' }] }), 100);
                }
                return data;
            });
        }
        
        function updateWithdrawButtonState() {
            const amount = parseFloat(elements.withdrawAmountInput.value) || 0;
            elements.submitWithdrawalButton.disabled = !(amount > 0 && amount <= (localUserData.gameData?.points || 0));
        }

        function handleWithdraw() {
            const amount = parseFloat(elements.withdrawAmountInput.value);
            const method = elements.withdrawMethodSelect.value;
            if (isNaN(amount) || amount <= 0 || amount > localUserData.gameData.points) {
                showWithdrawMessage('Invalid amount or insufficient points.', 'error'); return;
            }
            
            let detailsPayload = {}, detailsValid = true;
            if (method === 'paypal') { /* ... validation ... */ detailsPayload.email = elements.paypalEmailInput.value; }
            if (method === 'wire') { /* ... validation ... */ detailsPayload.account = elements.bankAccountInput.value; }
            if (method === 'crypto') { /* ... validation ... */ detailsPayload.address = elements.cryptoAddressInput.value; }
            if (!detailsValid) { showWithdrawMessage('Please fill details correctly.', 'error'); return; }

            elements.submitWithdrawalButton.disabled = true;
            runTransaction(ref(db, `users/${currentUserId}/gameData`), data => {
                if (data && data.points >= amount) { data.points -= amount; return data; }
                return; 
            }).then(result => {
                if(result.committed) {
                    push(ref(db, 'withdrawals'), { userId: currentUserId, username: localUserData.profile.username, amount, method, details: detailsPayload, timestamp: serverTimestamp(), status: 'pending' });
                    showWithdrawMessage(`Request for ${amount.toLocaleString()} points submitted.`, 'success');
                    elements.withdrawAmountInput.value = '';
                } else { throw new Error("Insufficient points."); }
            }).catch(error => showWithdrawMessage(`Error: ${error.message}.`, 'error'))
              .finally(() => elements.submitWithdrawalButton.disabled = false);
        }

        function showWithdrawMessage(message, type = 'info') {
             elements.withdrawMessage.textContent = message;
             elements.withdrawMessage.className = `withdraw-message ${type}`;
             elements.withdrawMessage.style.display = 'block';
        }

        function setupEventListeners() {
            elements.navButtons.forEach(button => button.addEventListener('click', () => showPage(button.dataset.page)));
            elements.catButton.addEventListener('pointerup', handleTap);
            elements.withdrawMethodSelect.addEventListener('change', () => {
                elements.detailFields.forEach(field => field.style.display = 'none');
                document.querySelector(`.${elements.withdrawMethodSelect.value}-field`).style.display = 'block';
            });
            [elements.withdrawAmountInput, elements.paypalEmailInput, elements.bankAccountInput, elements.cryptoAddressInput].forEach(input => {
                input.addEventListener('input', updateWithdrawButtonState);
            });
            elements.submitWithdrawalButton.addEventListener('click', handleWithdraw);
        }

        document.addEventListener('DOMContentLoaded', initApp);
    </script>
</body>
</html>
