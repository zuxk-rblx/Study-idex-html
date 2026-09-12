<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Study Buddy Quiz</title>
  
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>

  <style>
    @import url('https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&display=swap');

    :root {
      --primary: #ff4772; 
      --primary-hover: #e83e60; 
      --primary-light: #fff0f3;
      --bg-gradient: #f4f7f6;
      --card-bg: #ffffff; 
      --text: #2d3436; 
      --text-muted: #636e72;
      --border: #eaedf0; 
      --success: #2ecc71; 
      --danger: #ff7675; 
      --radius: 24px;
      font-family: 'Nunito', system-ui, sans-serif;
    }
    
    * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
    body { background: var(--bg-gradient); min-height: 100vh; display: flex; justify-content: center; align-items: center; color: var(--text); padding: 1rem; }
    
    /* ADAPTIVE CONTAINER */
    .app-container { background: var(--card-bg); border-radius: var(--radius); box-shadow: 0 20px 50px rgba(0,0,0,0.1); width: 100%; max-width: 1000px; position: relative; overflow: hidden; transition: all 0.3s ease; display: flex; flex-direction: column; }
    .padded-view { padding: 2.5rem; max-width: 600px; margin: 0 auto; width: 100%; }

    /* ====================================================
       SPLIT LOGIN LAYOUT
       ==================================================== */
    .split-layout { display: flex; flex-direction: row; width: 100%; min-height: 600px; background: white; }
    .split-left { flex: 1; background: linear-gradient(145deg, #ff4772 0%, #ff6b8b 100%); color: white; padding: 3rem 2.5rem; display: flex; flex-direction: column; justify-content: space-between; position: relative; }
    .split-right { flex: 1.2; background: white; padding: 3rem 3rem; display: flex; flex-direction: column; justify-content: center; }
    
    .login-heading h1 { font-size: 2.5rem; font-weight: 800; margin-bottom: 0.5rem; line-height: 1.2; }
    .login-heading p { font-size: 1rem; opacity: 0.9; line-height: 1.5; }
    .split-right h2 { font-size: 1.8rem; font-weight: 800; margin-bottom: 0.3rem; color: var(--text); }
    .split-right .subtitle { font-size: 0.9rem; color: var(--text-muted); margin-bottom: 1.5rem; line-height: 1.4; }

    /* CSS Panda Logo (Hidden Admin Trigger on Left) */
    .css-panda { position: relative; width: 70px; height: 60px; cursor: pointer; transition: transform 0.2s ease; margin-bottom: 1.5rem; }
    .css-panda:active { transform: scale(0.94); }
    .panda-head { position: absolute; bottom: 0; left: 5px; width: 60px; height: 48px; background: #fff; border-radius: 50px 50px 40px 40px; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15); border: 2.5px solid #ffb3c6; z-index: 2; }
    .panda-ear { position: absolute; width: 20px; height: 20px; background: #2d3436; border-radius: 50%; top: 4px; z-index: 1; }
    .panda-ear.left { left: 0px; }
    .panda-ear.right { right: 0px; }
    .panda-eye { position: absolute; width: 14px; height: 18px; background: #2d3436; border-radius: 50% 50% 45% 45%; top: 16px; }
    .panda-eye.left { left: 10px; transform: rotate(15deg); }
    .panda-eye.right { right: 10px; transform: rotate(-15deg); }
    .panda-eye .pupil { position: absolute; width: 4px; height: 4px; background: #fff; border-radius: 50%; top: 4px; left: 4px; }
    .panda-nose { position: absolute; width: 8px; height: 5px; background: #2d3436; border-radius: 50%; top: 34px; left: 50%; transform: translateX(-50%); }

    /* Form Styles */
    .form-group { margin-bottom: 1.2rem; text-align: left; }
    .form-label { display: block; font-size: 0.85rem; font-weight: 700; color: #555; margin-bottom: 0.4rem; }
    .form-control { width: 100%; padding: 0.9rem 1rem; border: 2px solid var(--border); border-radius: 12px; font-size: 1rem; color: var(--text); background: #fafafa; transition: all 0.2s; font-family: 'Nunito', sans-serif; }
    .form-control:focus { outline: none; border-color: var(--primary); background: #fff; }
    textarea.form-control { resize: vertical; min-height: 80px; }
    
    .line-input { border: none; border-bottom: 2px solid #e0e0e0; border-radius: 0; background: transparent; padding: 0.6rem 0.2rem; font-weight: 600; }
    .line-input:focus { border-bottom-color: var(--text); background: transparent; box-shadow: none; }

    .password-wrapper { position: relative; width: 100%; display: flex; align-items: center; }
    .password-wrapper .form-control { padding-right: 3rem; }
    
    /* Proper SVG Eye Toggle */
    .toggle-password { position: absolute; right: 10px; background: none; border: none; cursor: pointer; color: #888; padding: 4px; display: flex; align-items: center; justify-content: center; border-radius: 50%; outline: none; }
    .toggle-password:hover { color: var(--text); }
    .toggle-password svg { width: 22px; height: 22px; }

    /* Admin Inline Box (Left Side) */
    .admin-login-box { display: none; background: rgba(255,255,255,0.15); border: 1.5px solid rgba(255,255,255,0.3); border-radius: 16px; padding: 1.2rem; margin-top: 1.5rem; backdrop-filter: blur(4px); animation: fadeIn 0.25s ease-out; }
    .admin-login-box .form-control { background: white; border: none; border-radius: 10px; padding: 0.8rem; }
    
    /* Buttons */
    .btn { display: inline-flex; align-items: center; justify-content: center; padding: 0.9rem 1.5rem; border-radius: 12px; font-size: 1.05rem; font-weight: 800; border: none; cursor: pointer; transition: all 0.2s ease; width: 100%; font-family: 'Nunito', sans-serif; }
    .btn-dark { background: #1a1a1a; color: white; margin-top: 1rem; }
    .btn-dark:hover { background: #000; transform: translateY(-2px); box-shadow: 0 6px 15px rgba(0,0,0,0.15); }
    .btn-primary { background: var(--primary); color: white; box-shadow: 0 4px 14px rgba(255, 71, 114, 0.35); }
    .btn-primary:hover { background: var(--primary-hover); transform: translateY(-2px); }
    .btn-secondary { background: #eee; color: #555; }
    .btn-danger { background: var(--danger); color: white; }
    .btn-success { background: var(--success); color: white; }
    .btn-sm { padding: 0.5rem 1rem; font-size: 0.85rem; border-radius: 8px; width: auto; margin-top: 0; }
    
    .demo-box { background: #f8f9fa; border: 1.5px dashed #ccc; padding: 0.8rem; border-radius: 12px; margin-top: 1.5rem; text-align: center; font-size: 0.85rem; color: #555; }
    .demo-box span { color: var(--primary); font-weight: 800; font-size: 0.95rem; }

    .inline-error { color: var(--danger); font-size: 0.85rem; font-weight: 800; margin-top: 0.5rem; display: none; text-align: center; }

    .view-section { display: none; animation: fadeIn 0.3s ease; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

    /* ====================================================
       DARK JUNGLE SCENE (GROUNDED)
       ==================================================== */
    .jungle-scene-container { width: 100%; height: 180px; background: linear-gradient(to bottom, #112b1c, #1a3c26); border-radius: 18px; margin-bottom: 2rem; overflow: hidden; position: relative; box-shadow: inset 0 6px 20px rgba(0,0,0,0.25); }
    .jungle-svg { width: 100%; height: 100%; display: block; }

    .animal-head { transform-box: fill-box; transform-origin: 50% 100%; transition: transform 0.4s ease-in-out; will-change: transform; }
    .animal-eyes { transform-box: fill-box; transform-origin: 50% 50%; transition: transform 0.3s ease-in-out; will-change: transform; }

    .fly-animation { animation: fly 14s linear infinite; }
    .tiger-patrol { animation: patrol 18s linear infinite; }
    .elephant-stroll { animation: stroll 25s linear infinite; }
    .frog-hop { animation: hop 6s linear infinite; }
    .monkey-swing { animation: swing 4s ease-in-out infinite; transform-origin: 50px 0px; }
    .bamboo-chew { animation: chew 0.8s infinite alternate; transform-origin: bottom left; }
    .panda-idle { animation: pandaBounce 2.6s ease-in-out infinite; transform-origin: 50% 100%; transform-box: fill-box; }

    @keyframes fly { 0% { transform: translateX(-50px) scaleX(1); } 49% { transform: translateX(450px) scaleX(1); } 50% { transform: translateX(450px) scaleX(-1); } 99% { transform: translateX(-50px) scaleX(-1); } 100% { transform: translateX(-50px) scaleX(1); } }
    @keyframes patrol { 0% { transform: translateX(50px) scaleX(1); } 45% { transform: translateX(120px) scaleX(1); } 50% { transform: translateX(120px) scaleX(-1); } 95% { transform: translateX(50px) scaleX(-1); } 100% { transform: translateX(50px) scaleX(1); } }
    @keyframes stroll { 0% { transform: translateX(-80px); } 100% { transform: translateX(450px); } }
    @keyframes hop { 0%, 20%, 100% { transform: translate(0, 0); } 5% { transform: translate(15px, -20px); } 10% { transform: translate(30px, 0); } 15% { transform: translate(45px, -20px); } 18% { transform: translate(60px, 0); } }
    @keyframes swing { 0%, 100% { transform: rotate(-15deg); } 50% { transform: rotate(15deg); } }
    @keyframes chew { 0% { transform: rotate(0deg); } 100% { transform: rotate(12deg); } }
    @keyframes pandaBounce { 0%, 100% { transform: translateY(0) scaleY(1); } 50% { transform: translateY(-5px) scaleY(1.02); } }

    /* ---- Peeking reaction: each animal eases smoothly to a graceful resting pose, ----
       ---- staggered slightly so the whole scene settles like a little ripple ---- */
    .peeking .fly-animation   { animation: none !important; transform: translateX(210px) scaleX(1) !important; transition: transform 0.6s cubic-bezier(0.34,1.56,0.64,1) 0s; }
    .peeking .tiger-patrol    { animation: none !important; transform: translateX(70px) scaleX(1) !important; transition: transform 0.6s cubic-bezier(0.34,1.56,0.64,1) 0.08s; }
    .peeking .elephant-stroll { animation: none !important; transform: translateX(170px) !important; transition: transform 0.6s cubic-bezier(0.34,1.56,0.64,1) 0.16s; }
    .peeking .frog-hop        { animation: none !important; transform: translate(0,0) !important; transition: transform 0.4s ease-out 0.24s; }
    .peeking .monkey-swing    { animation: none !important; transform: rotate(0deg) !important; transition: transform 0.5s cubic-bezier(0.34,1.56,0.64,1) 0.32s; }
    .peeking .bamboo-chew     { animation: none !important; transform: rotate(0deg) !important; transition: transform 0.3s ease-out 0.1s; }
    .peeking .panda-idle      { animation: none !important; transform: translateY(0) scaleY(1) !important; transition: transform 0.5s cubic-bezier(0.34,1.56,0.64,1) 0.4s; }

    .peeking .animal-head                  { transition-timing-function: cubic-bezier(0.34,1.56,0.64,1); transition-duration: 0.55s; }
    .peeking .fly-animation .animal-head   { transform: translateY(4px) rotate(11deg); transition-delay: 0s; }
    .peeking .tiger-patrol .animal-head    { transform: translateY(5px) rotate(-9deg); transition-delay: 0.08s; }
    .peeking .elephant-stroll .animal-head { transform: translateY(4px) rotate(9deg); transition-delay: 0.16s; }
    .peeking .frog-hop .animal-head        { transform: translateY(4px) rotate(-9deg); transition-delay: 0.24s; }
    .peeking .monkey-swing .animal-head    { transform: translateY(7px) rotate(16deg); transition-delay: 0.32s; }
    .peeking .panda-idle .animal-head      { transform: translateY(5px) rotate(7deg); transition-delay: 0.4s; }

    .peeking .animal-eyes { transform: scaleY(0.15); }

    /* ====================================================
       QUIZ RUNTIME
       ==================================================== */
    .quiz-top-bar { display: flex; justify-content: space-between; align-items: center; margin-bottom: 1.5rem; }
    .q-counter { font-size: 1.1rem; font-weight: 800; color: var(--primary); text-transform: uppercase; }
    .timer-badge { font-weight: 800; font-size: 1rem; background: #f8f9fa; padding: 0.5rem 1rem; border-radius: 12px; border: 2px solid var(--border); display: flex; align-items: center; gap: 0.5rem; }
    .progress-track { width: 100%; height: 8px; background: var(--border); border-radius: 10px; overflow: hidden; margin-bottom: 2rem; }
    .progress-fill { height: 100%; width: 0%; background: var(--primary); transition: width 0.4s ease; }
    .question-title { font-size: 1.4rem; font-weight: 800; line-height: 1.4; margin-bottom: 2rem; }
    
    .options-grid { display: flex; flex-direction: column; gap: 1rem; margin-bottom: 2rem; }
    .opt-btn { background: #fff; border: 2px solid var(--border); border-radius: 16px; padding: 1.2rem 1.5rem; font-size: 1.1rem; font-weight: 700; text-align: left; cursor: pointer; display: flex; align-items: center; gap: 1rem; transition: all 0.2s; color: var(--text); }
    .opt-btn:hover:not(:disabled) { border-color: var(--primary); background: var(--primary-light); transform: translateY(-2px); }
    .opt-key { display: inline-flex; width: 32px; height: 32px; border-radius: 50%; background: var(--border); color: #555; font-size: 0.95rem; font-weight: 800; justify-content: center; align-items: center; flex-shrink: 0; }
    .opt-btn.correct { background: #eafaf1; border-color: var(--success); color: var(--success); }
    .opt-btn.correct .opt-key { background: var(--success); color: white; }
    .opt-btn.wrong { background: #ffebee; border-color: var(--danger); color: var(--danger); }
    .opt-btn.wrong .opt-key { background: var(--danger); color: white; }

    .quiz-feedback { text-align: left; margin-bottom: 1.5rem; display: none; }
    .feedback-alert { font-weight: 800; font-size: 1.2rem; margin-bottom: 0.5rem; }
    .score-view { display: none; text-align: center; padding: 2rem 0; }
    .score-circle { width: 150px; height: 150px; border-radius: 50%; background: var(--primary-light); border: 6px solid var(--primary); display: flex; flex-direction: column; justify-content: center; align-items: center; margin: 2rem auto; }
    .score-circle .pct { font-size: 3.5rem; font-weight: 800; color: var(--primary); }

    /* ====================================================
       ADMIN DASHBOARD (PURE SVGS, NO CSS GLITCHES)
       ==================================================== */
    .admin-layout { display: flex; min-height: 650px; width: 100%; background: #ffffff; }
    .admin-sidebar { width: 240px; background: #fafafa; padding: 2rem 1.5rem; border-right: 1px solid var(--border); display: flex; flex-direction: column; gap: 0.5rem; }
    .admin-content { flex: 1; padding: 2.5rem; overflow-y: auto; }
    
    .admin-brand { display: flex; align-items: center; gap: 10px; font-weight: 800; font-size: 1.3rem; color: var(--primary); margin-bottom: 2.5rem; }
    .admin-brand svg { width: 24px; height: 24px; }
    
    .admin-tab-btn { display: flex; align-items: center; gap: 12px; width: 100%; padding: 1rem 1.2rem; border-radius: 12px; background: transparent; color: var(--text-muted); font-weight: 700; font-size: 1rem; text-align: left; border: none; cursor: pointer; transition: all 0.2s; margin-bottom: 0.3rem; }
    .admin-tab-btn svg { width: 20px; height: 20px; }
    .admin-tab-btn:hover { background: var(--border); color: var(--text); }
    .admin-tab-btn.active { background: var(--primary-light); color: var(--primary); }
    .admin-tab-pane { display: none; animation: fadeIn 0.3s ease; }
    .admin-tab-pane.active { display: block; }
    
    .content-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 2rem; }
    .content-header h3 { font-size: 1.8rem; font-weight: 800; color: var(--text); }

    /* STATS GRID FOR 4 ITEMS */
    .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 1.2rem; margin-bottom: 1.5rem; }
    .dashboard-card { background: #fafafa; border: 1px solid var(--border); padding: 1.5rem; border-radius: 16px; }
    .dashboard-card .label { font-size: 0.85rem; font-weight: 700; color: var(--text-muted); text-transform: uppercase; margin-bottom: 0.5rem; }
    .dashboard-card .val { font-size: 2rem; font-weight: 800; color: var(--text); }

    .card-panel { background: #fafafa; border: 1px solid var(--border); padding: 1.5rem; border-radius: 16px; margin-bottom: 1.5rem; }
    .student-list-item, .history-card { display: flex; justify-content: space-between; align-items: center; background: #fff; border: 1px solid var(--border); padding: 1rem 1.2rem; border-radius: 12px; margin-bottom: 0.8rem; }
    .q-admin-card { background: #fff; border: 1px solid var(--border); border-radius: 12px; padding: 1.2rem; margin-bottom: 1rem; display: flex; justify-content: space-between; align-items: center; }
    .status-badge { font-size: 0.75rem; padding: 2px 8px; border-radius: 10px; font-weight: 700; color: white; margin-left: 10px; vertical-align: middle; }

    .custom-modal-overlay { display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0, 0, 0, 0.45); z-index: 999; justify-content: center; align-items: center; padding: 1rem; backdrop-filter: blur(4px); }
    .custom-modal-box { background: white; border-radius: 20px; padding: 2rem; max-width: 350px; width: 100%; text-align: center; box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2); }
    .modal-actions { display: flex; gap: 1rem; justify-content: center; margin-top: 1.5rem; }

    @media (max-width: 768px) {
      .jungle-scene-container { height: 190px; }
      .jungle-svg { min-width: 100%; }

      .split-layout { flex-direction: column; min-height: auto; }
      .split-left { padding: 3rem 2rem; border-radius: 0; }
      .split-right { padding: 3rem 2rem; }
      .admin-layout { flex-direction: column; }
      .admin-sidebar { width: 100%; padding: 1.5rem; border-right: none; border-bottom: 1px solid var(--border); flex-direction: row; flex-wrap: wrap; justify-content: center; gap: 0.5rem; }
      .admin-brand { width: 100%; justify-content: center; margin-bottom: 1rem; }
      .admin-tab-btn { width: auto; padding: 0.6rem 1rem; flex: 1 1 auto; justify-content: center; }
      .admin-content { padding: 1.5rem; }
      .stats-grid { grid-template-columns: 1fr; }
      .dashboard-card.full { grid-column: span 1; }
      .q-admin-card { flex-direction: column; align-items: flex-start; gap: 1rem; }
    }
  </style>
</head>
<body>

  <!-- Custom Dialog -->
  <div class="custom-modal-overlay" id="confirmModal">
    <div class="custom-modal-box">
      <h3 id="modalTitle" style="font-size:1.3rem; margin-bottom:0.5rem; font-weight:800;">Confirm Action</h3>
      <p id="modalDesc" style="color: var(--text-muted); font-size: 0.95rem;">Are you sure?</p>
      <div class="modal-actions">
        <button class="btn btn-secondary btn-sm" onclick="closeConfirmModal(false)">Cancel</button>
        <button class="btn btn-danger btn-sm" onclick="closeConfirmModal(true)">Confirm</button>
      </div>
    </div>
  </div>

  <div class="app-container" id="mainAppCard">
    
    <!-- VIEW 1: SPLIT LOGIN SCREEN -->
    <div class="view-section split-layout" id="viewLogin">
      
      <!-- LEFT SIDE (Pink gradient + Admin Access) -->
      <div class="split-left">
        <div>
          <!-- Admin Access Panda -->
          <div class="css-panda" onclick="toggleInlineAdminPrompt()" title="Admin Access">
            <div class="panda-ear left"></div>
            <div class="panda-ear right"></div>
            <div class="panda-head">
              <div class="panda-eye left"><div class="pupil"></div></div>
              <div class="panda-eye right"><div class="pupil"></div></div>
              <div class="panda-nose"></div>
            </div>
          </div>
          
          <div class="login-heading" style="text-align: left; align-items:flex-start;">
            <h1>Study<br>Buddy!</h1>
            <p>Welcome to the cutest quiz platform. Test your knowledge and track your progress!</p>
          </div>

          <!-- HIDDEN ADMIN LOGIN BOX -->
          <div class="admin-login-box" id="adminSecretBox">
            <label style="display:block; font-size:0.9rem; font-weight:800; color:#fff; margin-bottom:0.6rem;">Admin Access</label>
            <div style="display:flex; gap:0.5rem; align-items:center;">
              <div class="password-wrapper" style="flex-grow: 1;">
                <input type="password" class="form-control" id="secretPassInput" placeholder="Passcode..." style="padding-right: 2.5rem;" />
                <button type="button" class="toggle-password" onclick="togglePasswordVisibility('secretPassInput', this)" style="color: #666;">
                  <svg viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" fill="none"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>
                </button>
              </div>
              <button class="btn btn-dark" style="width:auto; margin-top:0; border-radius:10px; padding: 0.9rem 1.2rem;" onclick="verifyAdminPasscode()">Go</button>
            </div>
            <div class="inline-error" id="adminPassError" style="color:#ffebee;">Wrong passcode.</div>
          </div>
        </div>
        <div style="font-size: 0.8rem; opacity: 0.8; margin-top: 2rem;">© 2026 Study Quiz. All rights reserved.</div>
      </div>

      <!-- RIGHT SIDE (Student Login & Jungle) -->
      <div class="split-right">
        <h2>Welcome Back!</h2>
        <p class="subtitle">Enter your Student ID and Password.</p>
        
        <!-- JUNGLE SCENE -->
        <div class="jungle-scene-container" id="jungleScene">
          <svg viewBox="0 0 700 240" class="jungle-svg" preserveAspectRatio="xMidYMid slice" aria-label="Animated jungle with animals">
            <defs>
              <linearGradient id="jungleSky" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#0b2417"/>
                <stop offset="58%" stop-color="#123a23"/>
                <stop offset="100%" stop-color="#1b4b2b"/>
              </linearGradient>
              <linearGradient id="groundGrad" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#2d6b38"/>
                <stop offset="100%" stop-color="#102817"/>
              </linearGradient>
              <linearGradient id="elephantGrad2" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#87919f"/>
                <stop offset="100%" stop-color="#59616c"/>
              </linearGradient>
              <linearGradient id="tigerGrad2" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#ffc95a"/>
                <stop offset="100%" stop-color="#e49f18"/>
              </linearGradient>
              <linearGradient id="monkeyGrad2" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#bd8558"/>
                <stop offset="100%" stop-color="#825334"/>
              </linearGradient>
              <linearGradient id="frogGrad2" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#78e64e"/>
                <stop offset="100%" stop-color="#39a92b"/>
              </linearGradient>
              <linearGradient id="birdGrad2" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#ff7650"/>
                <stop offset="100%" stop-color="#d83d20"/>
              </linearGradient>
              <radialGradient id="pandaGrad2" cx="50%" cy="35%" r="70%">
                <stop offset="0%" stop-color="#ffffff"/>
                <stop offset="100%" stop-color="#e9edef"/>
              </radialGradient>
            </defs>

            <!-- Deep layered jungle -->
            <rect width="700" height="240" fill="url(#jungleSky)"/>
            <circle cx="560" cy="42" r="25" fill="#b7d98d" opacity=".10"/>
            <path d="M0 112 Q90 72 180 112 T360 106 T540 112 T760 92 V240 H0Z" fill="#173d24"/>
            <path d="M0 145 Q120 108 245 145 T500 132 T760 142 V240 H0Z" fill="#23552c"/>
            <path d="M0 184 Q150 145 300 181 T620 170 T760 178 V240 H0Z" fill="url(#groundGrad)"/>

            <!-- Large trees, vines and canopy -->
            <g opacity=".95">
              <path d="M22 0 H68 L58 190 H28Z" fill="#241912"/>
              <path d="M610 0 H666 L650 190 H620Z" fill="#241912"/>
              <path d="M125 0 H151 L145 160 H126Z" fill="#2a1d14"/>
              <path d="M470 0 H500 L505 170 H477Z" fill="#2a1d14"/>
              <path d="M0 35 Q100 82 205 32 T390 35 T590 30 T760 45" fill="none" stroke="#2c6334" stroke-width="18" stroke-linecap="round"/>
              <path d="M0 70 Q95 35 180 72 T360 68 T550 74 T760 60" fill="none" stroke="#1f512b" stroke-width="14" stroke-linecap="round"/>
              <path d="M88 0 Q110 55 96 110 M178 0 Q155 48 174 92 M420 0 Q445 50 428 112 M575 0 Q550 45 568 100"
                    fill="none" stroke="#3b7a3e" stroke-width="4" stroke-linecap="round"/>
              <path d="M105 28 Q135 44 152 30 M520 32 Q550 50 575 35 M650 60 Q675 72 700 58"
                    fill="none" stroke="#4d8b48" stroke-width="3" stroke-linecap="round"/>
            </g>

            <!-- Ground plants -->
            <g fill="none" stroke-linecap="round">
              <path d="M70 210 q-12-30-25-38 M72 210 q8-30 25-44 M70 210 q0-36 2-55" stroke="#4e9347" stroke-width="5"/>
              <path d="M645 214 q-8-35-24-48 M646 214 q10-32 29-47 M646 214 q2-34 5-55" stroke="#4e9347" stroke-width="5"/>
              <path d="M320 225 q-7-25-20-35 M320 225 q8-24 22-36" stroke="#63a84f" stroke-width="4"/>
            </g>

            <!-- 1. ELEPHANT — full body -->
            <g transform="translate(12 122)">
              <g class="elephant-stroll">
                <ellipse cx="72" cy="88" rx="52" ry="8" fill="#000" opacity=".25"/>
                <path d="M28 46 C22 19 47 7 79 12 C111 16 124 37 116 62 L103 75 L40 75 C30 68 27 58 28 46Z" fill="url(#elephantGrad2)"/>
                <path d="M42 66 L43 91 Q43 98 50 98 H60 Q64 96 61 90 L60 68Z" fill="#59616c"/>
                <path d="M86 68 L87 91 Q87 98 94 98 H104 Q108 96 105 90 L103 66Z" fill="#59616c"/>
                <path d="M112 34 Q141 47 132 70 Q127 82 118 84" fill="none" stroke="#59616c" stroke-width="11" stroke-linecap="round"/>
                <path d="M36 29 Q15 10 25 0 Q43 10 51 28" fill="#707a87"/>
                <path d="M42 27 Q24 12 29 5" fill="none" stroke="#4c545e" stroke-width="2"/>
                <g class="animal-head">
                  <path d="M31 42 Q52 18 82 25 Q104 30 108 50 Q101 70 77 72 Q49 72 31 55Z" fill="url(#elephantGrad2)"/>
                  <circle cx="55" cy="42" r="3.5" fill="#fff"/>
                  <circle cx="55" cy="42" r="1.8" fill="#111" class="animal-eyes"/>
                  <path d="M102 51 Q114 57 108 66" fill="none" stroke="#59616c" stroke-width="8" stroke-linecap="round"/>
                </g>
                <path d="M24 48 Q8 56 12 72" fill="none" stroke="#707a87" stroke-width="6" stroke-linecap="round"/>
              </g>
            </g>

            <!-- 2. TIGER — full body -->
            <g transform="translate(180 150)">
              <g class="tiger-patrol">
                <ellipse cx="48" cy="65" rx="45" ry="7" fill="#000" opacity=".24"/>
                <path d="M15 27 Q18 7 45 9 Q76 10 82 32 L74 53 H24 Q13 47 15 27Z" fill="url(#tigerGrad2)"/>
                <path d="M28 46 L27 68 Q27 73 33 73 H40 L42 48Z M58 47 L58 69 Q59 74 65 74 H72 L71 46Z" fill="#d99816"/>
                <path d="M18 23 Q0 12 4 0 Q19 6 29 17" fill="none" stroke="#e49f18" stroke-width="6" stroke-linecap="round"/>
                <path d="M25 17 L33 27 M45 13 L48 26 M64 16 L59 28" stroke="#2b2925" stroke-width="4" stroke-linecap="round"/>
                <g class="animal-head">
                  <circle cx="80" cy="27" r="19" fill="url(#tigerGrad2)"/>
                  <path d="M67 14 L62 3 L75 10 M88 10 L98 3 L96 18" fill="#e49f18"/>
                  <path d="M71 18 L77 23 M90 18 L84 23 M80 10 V17" stroke="#2d3436" stroke-width="2" stroke-linecap="round"/>
                  <ellipse cx="80" cy="33" rx="9" ry="6" fill="#fff"/>
                  <circle cx="74" cy="25" r="4" fill="#fff"/><circle cx="86" cy="25" r="4" fill="#fff"/>
                  <circle cx="74" cy="25" r="1.8" fill="#111" class="animal-eyes"/><circle cx="86" cy="25" r="1.8" fill="#111" class="animal-eyes"/>
                  <path d="M77 37 Q80 40 83 37" fill="none" stroke="#5d351e" stroke-width="1.5"/>
                </g>
              </g>
            </g>

            <!-- 3. PANDA — large, centered, full body -->
            <g transform="translate(335 132)" class="panda-idle">
              <ellipse cx="50" cy="92" rx="43" ry="8" fill="#000" opacity=".24"/>
              <path d="M15 48 Q12 18 50 14 Q88 18 85 48 L78 73 Q68 83 32 83 Q22 79 15 48Z" fill="url(#pandaGrad2)"/>
              <path d="M28 68 L25 91 Q25 97 32 98 H42 Q46 96 42 90 L43 70Z M58 70 L58 91 Q58 97 65 98 H75 Q79 96 75 90 L73 68Z" fill="#25292d"/>
              <path d="M20 47 Q3 56 7 72" fill="none" stroke="#25292d" stroke-width="14" stroke-linecap="round"/>
              <path d="M79 47 Q96 56 92 72" fill="none" stroke="#25292d" stroke-width="14" stroke-linecap="round"/>
              <circle cx="7" cy="72" r="8" fill="#25292d"/>
              <circle cx="92" cy="72" r="8" fill="#25292d"/>
              <g class="animal-head">
                <circle cx="25" cy="17" r="12" fill="#25292d"/>
                <circle cx="75" cy="17" r="12" fill="#25292d"/>
                <circle cx="50" cy="32" r="31" fill="#fff"/>
                <ellipse cx="39" cy="29" rx="9" ry="12" fill="#25292d" transform="rotate(-20 39 29)"/>
                <ellipse cx="61" cy="29" rx="9" ry="12" fill="#25292d" transform="rotate(20 61 29)"/>
                <circle cx="39" cy="28" r="4" fill="#fff"/><circle cx="61" cy="28" r="4" fill="#fff"/>
                <circle cx="39" cy="28" r="1.8" fill="#111" class="animal-eyes"/><circle cx="61" cy="28" r="1.8" fill="#111" class="animal-eyes"/>
                <ellipse cx="50" cy="45" rx="6" ry="4" fill="#25292d"/>
                <path d="M50 48 Q44 54 39 50 M50 48 Q56 54 61 50" fill="none" stroke="#25292d" stroke-width="2" stroke-linecap="round"/>
              </g>
              <path d="M76 65 Q88 51 92 38" fill="none" stroke="#4c9a3d" stroke-width="5" stroke-linecap="round"/>
              <path d="M86 47 Q94 41 98 47 M81 55 Q89 49 93 55" fill="none" stroke="#63ad4c" stroke-width="4" stroke-linecap="round"/>
            </g>

            <!-- 4. MONKEY — full hanging body -->
            <g transform="translate(510 18)">
              <g class="monkey-swing">
                <path d="M24 0 V78" fill="none" stroke="#805738" stroke-width="7" stroke-linecap="round"/>
                <path d="M25 76 Q2 91 12 111" fill="none" stroke="#9a6845" stroke-width="7" stroke-linecap="round"/>
                <path d="M20 75 Q48 90 40 111" fill="none" stroke="#9a6845" stroke-width="7" stroke-linecap="round"/>
                <ellipse cx="25" cy="94" rx="18" ry="25" fill="url(#monkeyGrad2)"/>
                <path d="M14 111 L6 132 M36 111 L45 132" stroke="#825334" stroke-width="7" stroke-linecap="round"/>
                <path d="M13 131 L3 132 M43 132 L52 132" stroke="#825334" stroke-width="5" stroke-linecap="round"/>
                <path d="M10 86 Q-8 72 2 57" fill="none" stroke="#9a6845" stroke-width="7" stroke-linecap="round"/>
                <path d="M40 86 Q57 72 49 57" fill="none" stroke="#9a6845" stroke-width="7" stroke-linecap="round"/>
                <g class="animal-head" transform="translate(25 55)">
                  <circle cx="-18" cy="0" r="10" fill="#9a6845"/><circle cx="18" cy="0" r="10" fill="#9a6845"/>
                  <circle cx="0" cy="0" r="23" fill="url(#monkeyGrad2)"/>
                  <ellipse cx="0" cy="8" rx="15" ry="10" fill="#e4c7a6"/>
                  <circle cx="-7" cy="-4" r="4" fill="#fff"/><circle cx="7" cy="-4" r="4" fill="#fff"/>
                  <circle cx="-7" cy="-4" r="1.7" fill="#111" class="animal-eyes"/><circle cx="7" cy="-4" r="1.7" fill="#111" class="animal-eyes"/>
                  <path d="M-5 11 Q0 15 5 11" fill="none" stroke="#654128" stroke-width="2" stroke-linecap="round"/>
                </g>
              </g>
            </g>

            <!-- 5. FROG — full body -->
            <g transform="translate(105 184)">
              <g class="frog-hop">
                <ellipse cx="38" cy="28" rx="33" ry="6" fill="#000" opacity=".22"/>
                <ellipse cx="38" cy="18" rx="27" ry="18" fill="url(#frogGrad2)"/>
                <path d="M20 24 L7 38 M56 24 L69 38" stroke="#43ad2e" stroke-width="8" stroke-linecap="round"/>
                <path d="M10 38 L2 44 M66 38 L74 44" stroke="#43ad2e" stroke-width="6" stroke-linecap="round"/>
                <g class="animal-head">
                  <circle cx="21" cy="3" r="11" fill="#55c83b"/><circle cx="55" cy="3" r="11" fill="#55c83b"/>
                  <circle cx="21" cy="3" r="6" fill="#fff"/><circle cx="55" cy="3" r="6" fill="#fff"/>
                  <circle cx="21" cy="3" r="2" fill="#111" class="animal-eyes"/><circle cx="55" cy="3" r="2" fill="#111" class="animal-eyes"/>
                  <path d="M22 17 Q38 28 54 17" fill="none" stroke="#267d21" stroke-width="3" stroke-linecap="round"/>
                </g>
              </g>
            </g>

            <!-- 6. PARROT — full body -->
            <g transform="translate(285 20)">
              <g class="fly-animation">
                <path d="M20 0 Q34 12 26 26 Q13 18 20 0Z" fill="#2b7fdb" opacity=".8"/>
                <ellipse cx="25" cy="38" rx="17" ry="25" fill="url(#birdGrad2)"/>
                <path d="M19 31 Q4 42 15 59 Q27 48 28 34Z" fill="#f4c62d"/>
                <path d="M25 60 L18 72 M31 60 L37 72" stroke="#e4a92b" stroke-width="4" stroke-linecap="round"/>
                <g class="animal-head">
                  <circle cx="25" cy="20" r="18" fill="url(#birdGrad2)"/>
                  <path d="M10 18 Q-3 23 10 29 Q17 27 19 22Z" fill="#f2c338"/>
                  <path d="M38 18 Q51 23 38 29 Q33 27 32 22Z" fill="#f2c338"/>
                  <circle cx="20" cy="17" r="4" fill="#fff"/>
                  <circle cx="20" cy="17" r="1.7" fill="#111" class="animal-eyes"/>
                </g>
              </g>
            </g>

            <!-- Foreground leaves -->
            <g fill="#326d36">
              <path d="M0 238 Q18 190 42 178 Q40 220 18 240Z"/>
              <path d="M700 240 Q682 192 658 178 Q660 220 682 240Z"/>
              <path d="M250 240 Q270 203 294 192 Q286 225 270 240Z"/>
              <path d="M455 240 Q438 203 420 195 Q427 226 442 240Z"/>
            </g>
          </svg>
        </div>

        <div class="form-group" style="margin-bottom: 1.5rem;">
          <input type="text" class="form-control line-input" id="studentIdInput" placeholder="Student ID" />
        </div>
        
        <div class="form-group" style="margin-bottom: 2rem;">
          <div class="password-wrapper">
            <input type="password" class="form-control line-input" id="studentPassInput" placeholder="Password" />
            <button type="button" class="toggle-password" onclick="togglePasswordVisibility('studentPassInput', this)">
              <!-- SVG Eye -->
              <svg viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" fill="none"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>
            </button>
          </div>
          <div class="inline-error" id="loginErrorMsg">Invalid Student ID or Password.</div>
        </div>

        <button class="btn btn-dark" style="border-radius:12px; padding:1.1rem;" onclick="handleStudentLogin()">Login Now</button>
      </div>
    </div>

    <!-- VIEW 2: QUIZ RUNTIME -->
    <div class="view-section padded-view" id="viewQuiz">
      <div id="quizActiveBody">
        <div class="quiz-top-bar">
          <div class="q-counter" id="qCounterText">Question 1</div>
        </div>
        <div class="progress-track"><div class="progress-fill" id="progressBar"></div></div>
        <div class="question-title" id="questionTitleText">Loading...</div>
        <div class="options-grid" id="optionsContainer"></div>
        <div class="quiz-feedback" id="feedbackArea"><div class="feedback-alert" id="feedbackText"></div></div>
        <button class="btn btn-primary" id="btnNext" style="display:none;" onclick="handleNextQuestion()">Next Question</button>
      </div>
      <div class="score-view" id="scoreScreen">
        <h2 style="font-size:2.2rem; font-weight:800; margin-bottom:1rem;">You Finished!</h2>
        <div class="score-circle"><span class="pct" id="scorePctText">0%</span></div>
        <button class="btn btn-dark" style="margin-top:2rem;" onclick="returnToLogin()">Log Out</button>
      </div>
    </div>

    <!-- VIEW 3: ADMIN DASHBOARD -->
    <div class="view-section admin-layout" id="viewAdmin">
      
      <div class="admin-sidebar">
        <div class="admin-brand">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"></path></svg>
          Study Admin
        </div>
        
        <button class="admin-tab-btn active" id="tabBtnDash" onclick="switchAdminTab('dash')">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="3" width="7" height="7"></rect><rect x="14" y="3" width="7" height="7"></rect><rect x="14" y="14" width="7" height="7"></rect><rect x="3" y="14" width="7" height="7"></rect></svg>
          Dashboard
        </button>
        <button class="admin-tab-btn" id="tabBtnStudents" onclick="switchAdminTab('students')">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17 21v-2a4 4 0 00-4-4H5a4 4 0 00-4 4v2"></path><circle cx="9" cy="7" r="4"></circle><path d="M23 21v-2a4 4 0 00-3-3.87M16 3.13a4 4 0 010 7.75"></path></svg>
          Students
        </button>
        <button class="admin-tab-btn" id="tabBtnQuestions" onclick="switchAdminTab('questions')">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line></svg>
          Questions
        </button>
        <button class="admin-tab-btn" id="tabBtnHistory" onclick="switchAdminTab('history')">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"></polyline></svg>
          Results
        </button>
        <button class="admin-tab-btn" id="tabBtnSettings" onclick="switchAdminTab('settings')">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="3"></circle><path d="M19.4 15a1.65 1.65 0 00.33 1.82l.06.06a2 2 0 010 2.83 2 2 0 01-2.83 0l-.06-.06a1.65 1.65 0 00-1.82-.33 1.65 1.65 0 00-1 1.51V21a2 2 0 01-2 2 2 2 0 01-2-2v-.09A1.65 1.65 0 009 19.4a1.65 1.65 0 00-1.82.33l-.06.06a2 2 0 01-2.83 0 2 2 0 010-2.83l.06-.06a1.65 1.65 0 00.33-1.82 1.65 1.65 0 00-1.51-1H3a2 2 0 01-2-2 2 2 0 012-2h.09A1.65 1.65 0 004.6 9a1.65 1.65 0 00-.33-1.82l-.06-.06a2 2 0 010-2.83 2 2 0 012.83 0l.06.06a1.65 1.65 0 001.82.33H9a1.65 1.65 0 001-1.51V3a2 2 0 012-2 2 2 0 012 2v.09a1.65 1.65 0 001 1.51 1.65 1.65 0 001.82-.33l.06-.06a2 2 0 012.83 0 2 2 0 010 2.83l-.06.06a1.65 1.65 0 00-.33 1.82V9a1.65 1.65 0 001.51 1H21a2 2 0 012 2 2 2 0 01-2 2h-.09a1.65 1.65 0 00-1.51 1z"></path></svg>
          Settings
        </button>
        <div style="flex-grow:1;"></div>
        <button class="btn btn-secondary" onclick="returnToLogin()">Log Out</button>
      </div>

      <div class="admin-content">
        <!-- DASHBOARD (Upgraded) -->
        <div class="admin-tab-pane active" id="paneDash">
          <div class="content-header"><h3>Dashboard</h3></div>
          <div class="stats-grid">
            <div class="dashboard-card"><div class="label">Attempts</div><div class="val" id="dashTotalAttempts">0</div></div>
            <div class="dashboard-card"><div class="label">Avg Score</div><div class="val" id="dashAvgScore">0%</div></div>
            <div class="dashboard-card"><div class="label">Top Score</div><div class="val" id="dashTopScore">0%</div></div>
            <div class="dashboard-card"><div class="label">Total Questions</div><div class="val" id="dashTotalQ">0</div></div>
          </div>
        </div>

        <!-- STUDENTS -->
        <div class="admin-tab-pane" id="paneStudents">
          <div class="content-header"><h3>Manage Students</h3></div>
          <div class="card-panel">
            <h4 style="margin-bottom:1rem;">Create New Account</h4>
            <div class="form-group"><label class="form-label">Student ID</label><input type="text" class="form-control" id="newStId" placeholder="e.g. student123" /></div>
            <div class="form-group"><label class="form-label">Full Name</label><input type="text" class="form-control" id="newStName" placeholder="John Doe" /></div>
            <div class="form-group"><label class="form-label">Password</label><input type="password" class="form-control" id="newStPass" placeholder="Password..." /></div>
            <button class="btn btn-primary" style="width:200px;" onclick="createNewStudent()">Add Student</button>
            <div class="inline-error" id="stFormError">Please fill all fields.</div>
          </div>
          <h4 style="margin-bottom:1rem;">Registered Students (<span id="stCountBadge">0</span>)</h4>
          <div id="adminStudentsList"></div>
        </div>

        <!-- QUESTIONS (Upgraded) -->
        <div class="admin-tab-pane" id="paneQuestions">
          <div class="content-header"><h3>Question Bank</h3></div>
          <div class="card-panel" style="background:#fff0f3; border-color:#ff4772;">
            <h4 style="margin-bottom:0.5rem; color:#ff4772;">Auto-Extract from PDF</h4>
            <div style="display:flex; gap:1rem; align-items:center;">
              <input type="file" id="pdfFileInput" accept="application/pdf" class="form-control" style="background:white; flex-grow:1;" />
              <button class="btn btn-primary" style="width:auto;" onclick="processPDF()">Extract</button>
            </div>
            <div class="inline-error" id="pdfErrorMsg"></div>
            <div id="pdfSuccessMsg" style="color:var(--success); font-size:0.85rem; margin-top:0.5rem; display:none;"></div>
          </div>

          <div class="card-panel">
            <h4 id="qFormHeading" style="margin-bottom:1rem;">Add Question Manually</h4>
            <input type="hidden" id="editQIndex" value="-1" />
            <div class="form-group"><label class="form-label">Question Text</label><textarea class="form-control" id="inputQText"></textarea></div>
            <div class="stats-grid" style="grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); margin-bottom: 0;">
              <div class="form-group"><label class="form-label">Option A</label><input type="text" class="form-control" id="inputOptA" /></div>
              <div class="form-group"><label class="form-label">Option B</label><input type="text" class="form-control" id="inputOptB" /></div>
              <div class="form-group"><label class="form-label">Option C</label><input type="text" class="form-control" id="inputOptC" /></div>
              <div class="form-group"><label class="form-label">Option D</label><input type="text" class="form-control" id="inputOptD" /></div>
            </div>
            <div class="form-group" style="max-width: 250px;">
              <label class="form-label">Correct Answer</label>
              <select class="form-control" id="selectCorrectOpt">
                <option value="0">Option A</option><option value="1">Option B</option><option value="2">Option C</option><option value="3">Option D</option>
              </select>
            </div>
            <div style="display:flex; gap:1rem; margin-top: 1rem;">
              <button class="btn btn-primary" style="width:150px;" onclick="saveQuestionRecord()">Save</button>
              <button class="btn btn-secondary" id="btnCancelEditQ" style="display:none; width:150px;" onclick="resetQuestionForm()">Cancel</button>
            </div>
            <div class="inline-error" id="qFormError">Fill all fields.</div>
          </div>
          <h4 style="margin-bottom:1rem;">Available Questions (<span id="qCountBadge">0</span>)</h4>
          <div id="adminQuestionsList"></div>
        </div>

        <!-- HISTORY (Upgraded) -->
        <div class="admin-tab-pane" id="paneHistory">
          <div class="content-header">
            <h3>Student Results</h3>
            <div style="display:flex; gap:0.5rem;">
              <button class="btn btn-primary btn-sm" onclick="exportHistoryCSV()">Export CSV</button>
              <button class="btn btn-danger btn-sm" onclick="promptClearHistory()">Clear History</button>
            </div>
          </div>
          <div id="historyListContainer"></div>
        </div>

        <!-- SETTINGS -->
        <div class="admin-tab-pane" id="paneSettings">
          <div class="content-header"><h3>Security Settings</h3></div>
          <div class="card-panel" style="max-width: 500px;">
            <div class="form-group">
              <label class="form-label">Change Admin Passcode</label>
              <div class="password-wrapper" style="margin-bottom: 1rem;">
                <input type="password" class="form-control" id="inputNewAdminPass" placeholder="Enter new passcode..." />
                <button type="button" class="toggle-password" onclick="togglePasswordVisibility('inputNewAdminPass', this)">
                  <svg viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" fill="none"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>
                </button>
              </div>
              <button class="btn btn-dark" onclick="updateAdminPasscode()">Save New Passcode</button>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>

  <script>
    const EYE_SVG = `<svg viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" fill="none"><path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path><circle cx="12" cy="12" r="3"></circle></svg>`;
    const EYE_OFF_SVG = `<svg viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" fill="none"><path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"></path><line x1="1" y1="1" x2="23" y2="23"></line></svg>`;

    function togglePasswordVisibility(inputId, btnElement) {
      const input = document.getElementById(inputId);
      const jungleScene = document.getElementById('jungleScene');

      if (input.type === "password") {
        input.type = "text"; 
        btnElement.innerHTML = EYE_OFF_SVG;
        if(inputId === 'studentPassInput' && jungleScene) jungleScene.classList.add('peeking');
      } else {
        input.type = "password"; 
        btnElement.innerHTML = EYE_SVG;
        const adPass = document.getElementById('secretPassInput');
        const stPass = document.getElementById('studentPassInput');
        if (adPass && stPass && adPass.type === "password" && stPass.type === "password") {
          if(jungleScene) jungleScene.classList.remove('peeking');
        }
      }
    }

    function toggleInlineAdminPrompt() {
      const box = document.getElementById('adminSecretBox');
      box.style.display = (box.style.display === 'block') ? 'none' : 'block';
      document.getElementById('adminPassError').style.display = 'none';
      if (box.style.display === 'none') document.getElementById('secretPassInput').value = '';
    }

    let confirmCallback = null;
    function showCustomConfirm(title, desc, cb) {
      document.getElementById('modalTitle').innerText = title;
      document.getElementById('modalDesc').innerText = desc;
      confirmCallback = cb;
      document.getElementById('confirmModal').style.display = 'flex';
    }
    function closeConfirmModal(confirm) {
      document.getElementById('confirmModal').style.display = 'none';
      if(confirm && confirmCallback) confirmCallback();
      confirmCallback = null;
    }

    function safeSetItem(key, val) { try { localStorage.setItem(key, val); } catch(e) {} }
    function safeGetItem(key) { try { return localStorage.getItem(key); } catch(e) { return null; } }

    async function hashSHA256(text) {
      try {
        const msgUint8 = new TextEncoder().encode(text);
        const hashBuffer = await crypto.subtle.digest('SHA-256', msgUint8);
        return Array.from(new Uint8Array(hashBuffer)).map(b => b.toString(16).padStart(2, '0')).join('');
      } catch(e) { return text; }
    }

    const DEFAULT_ADMIN = "2028hop@";
    const DEFAULT_Q = [
      { q: "What is the capital of Japan?", options: ["Seoul", "Beijing", "Tokyo", "Bangkok"], correct: 2, enabled: true },
      { q: "What is 5 multiplied by 5?", options: ["20", "25", "30", "35"], correct: 1, enabled: true }
    ];

    async function initSecurityAndData() {
      if (!safeGetItem('quiz_admin_pass')) {
        safeSetItem('quiz_admin_pass', await hashSHA256(DEFAULT_ADMIN));
      }
      const storedQ = safeGetItem('quiz_questions');
      if (!storedQ || storedQ === '[]') safeSetItem('quiz_questions', JSON.stringify(DEFAULT_Q));

      const storedS = safeGetItem('quiz_students_list');
      if (!storedS) {
        safeSetItem('quiz_students_list', JSON.stringify([]));
      }
    }

    function getQuestions() { return JSON.parse(safeGetItem('quiz_questions') || '[]'); }
    function saveQuestions(qs) { safeSetItem('quiz_questions', JSON.stringify(qs)); }
    function getHistory() { return JSON.parse(safeGetItem('quiz_history') || '[]'); }
    function saveHistory(h) { safeSetItem('quiz_history', JSON.stringify(h)); }
    function getStudentsList() { return JSON.parse(safeGetItem('quiz_students_list') || '[]'); }
    function saveStudentsList(st) { safeSetItem('quiz_students_list', JSON.stringify(st)); }

    let timerInterval = null; 
    
    // ====================================================
    // FIX FOR ADMIN LAYOUT STACKING BUG
    // ====================================================
    function showView(view) {
      if (timerInterval) clearInterval(timerInterval); 
      ['viewLogin', 'viewQuiz', 'viewAdmin'].forEach(v => document.getElementById(v).style.display = 'none');
      
      const appCard = document.getElementById('mainAppCard');
      
      if(view === 'admin') {
        // MUST BE FLEX SO THE SIDEBAR AND CONTENT SIT SIDE-BY-SIDE
        document.getElementById('viewAdmin').style.display = 'flex';
        appCard.style.maxWidth = '1000px';
        appCard.style.padding = '0';
        loadAdminDashboard(); renderAdminQuestions(); renderHistoryList(); renderStudentsList();
      } else if(view === 'login') {
        document.getElementById('viewLogin').style.display = 'flex';
        appCard.style.maxWidth = '1000px';
        appCard.style.padding = '0';
      } else {
        document.getElementById('viewQuiz').style.display = 'block';
        appCard.style.maxWidth = '600px';
        appCard.style.padding = '0';
      }
    }
    
    function returnToLogin() { 
      document.getElementById('studentIdInput').value = '';
      document.getElementById('studentPassInput').value = '';
      document.getElementById('secretPassInput').value = '';
      document.getElementById('studentPassInput').type = 'password';
      document.getElementById('secretPassInput').type = 'password';
      document.getElementById('jungleScene').classList.remove('peeking');
      document.querySelectorAll('.toggle-password').forEach(el => el.innerHTML = EYE_SVG);
      document.getElementById('adminPassError').style.display = 'none';
      document.getElementById('loginErrorMsg').style.display = 'none';
      document.getElementById('adminSecretBox').style.display = 'none';
      showView('login'); 
    }

    async function verifyAdminPasscode() {
      const entered = document.getElementById('secretPassInput').value.trim();
      const stored = safeGetItem('quiz_admin_pass');
      if ((await hashSHA256(entered)) === stored) {
        showView('admin'); 
        document.getElementById('adminPassError').style.display = 'none';
        document.getElementById('secretPassInput').value = '';
      } else { 
        document.getElementById('adminPassError').style.display = 'block'; 
      }
    }

    async function handleStudentLogin() {
      const id = document.getElementById('studentIdInput').value.trim();
      const pass = document.getElementById('studentPassInput').value.trim();
      const err = document.getElementById('loginErrorMsg');
      err.style.display = 'none';

      if (!id || !pass) { err.style.display = 'block'; return; }
      const hash = await hashSHA256(pass);
      const students = getStudentsList();
      const foundStudent = students.find(s => s.id === id && s.passHash === hash);
      
      if (foundStudent) {
        sessionStorage.setItem('active_student', foundStudent.name); 
        startQuizSession();
      } else { err.style.display = 'block'; }
    }

    let sessionQuestions = [], currentQIdx = 0, studentAnswers = [], quizStartTime;
    
    function startQuizSession() {
      sessionQuestions = getQuestions().filter(q => q.enabled !== false);
      if(!sessionQuestions.length) return alert("No active questions available in the Bank. Ask the Admin to enable some!");
      currentQIdx = 0; studentAnswers = []; quizStartTime = new Date();
      document.getElementById('quizActiveBody').style.display = 'block';
      document.getElementById('scoreScreen').style.display = 'none';
      showView('quiz'); renderCurrentQuestion();
    }

    function renderCurrentQuestion() {
      const q = sessionQuestions[currentQIdx];
      document.getElementById('btnNext').style.display = 'none';
      document.getElementById('feedbackArea').style.display = 'none';
      document.getElementById('qCounterText').innerText = `Question ${currentQIdx + 1} of ${sessionQuestions.length}`;
      document.getElementById('questionTitleText').innerText = q.q;
      document.getElementById('progressBar').style.width = `${(currentQIdx / sessionQuestions.length) * 100}%`;
      
      const container = document.getElementById('optionsContainer'); container.innerHTML = '';
      q.options.forEach((opt, idx) => {
        const btn = document.createElement('button'); btn.className = 'opt-btn';
        btn.innerHTML = `<span class="opt-key">${['A','B','C','D'][idx]}</span> <span>${opt}</span>`;
        btn.onclick = () => selectQuizAnswer(idx); container.appendChild(btn);
      });
    }

    function selectQuizAnswer(idx) {
      const q = sessionQuestions[currentQIdx]; const isCorrect = (idx === q.correct);
      studentAnswers.push({ text: q.q, selected: idx, correct: q.correct, isCorrect });
      
      const btns = document.querySelectorAll('.opt-btn');
      btns.forEach(b => b.disabled = true);
      btns[idx].classList.add(isCorrect ? 'correct' : 'wrong');
      if(!isCorrect) btns[q.correct].classList.add('correct');
      
      document.getElementById('feedbackText').innerText = isCorrect ? 'Correct!' : 'Incorrect';
      document.getElementById('feedbackArea').style.display = 'block';
      document.getElementById('btnNext').style.display = 'block';
    }

    function handleNextQuestion() {
      if (++currentQIdx < sessionQuestions.length) renderCurrentQuestion();
      else finishQuizSession();
    }

    function finishQuizSession() {
      document.getElementById('quizActiveBody').style.display = 'none';
      document.getElementById('scoreScreen').style.display = 'block';
      const correct = studentAnswers.filter(a => a.isCorrect).length;
      const pct = Math.round((correct / sessionQuestions.length) * 100);
      document.getElementById('scorePctText').innerText = `${pct}%`;
      
      if(pct >= 60) confetti({ particleCount: 50, spread: 60 });
      
      const h = getHistory();
      h.unshift({ studentName: sessionStorage.getItem('active_student'), score: pct, date: new Date().toLocaleDateString() });
      saveHistory(h);
    }

    function switchAdminTab(tab) {
      ['dash', 'students', 'questions', 'history', 'settings'].forEach(t => {
        const Cap = t.charAt(0).toUpperCase() + t.slice(1);
        document.getElementById(`tabBtn${Cap}`).classList.remove('active');
        document.getElementById(`pane${Cap}`).classList.remove('active');
      });
      const TabCap = tab.charAt(0).toUpperCase() + tab.slice(1);
      document.getElementById(`tabBtn${TabCap}`).classList.add('active');
      document.getElementById(`pane${TabCap}`).classList.add('active');
    }

    function loadAdminDashboard() {
      const h = getHistory();
      document.getElementById('dashTotalAttempts').innerText = h.length;
      document.getElementById('dashTotalQ').innerText = getQuestions().length;
      if(h.length > 0) {
        document.getElementById('dashAvgScore').innerText = Math.round(h.reduce((sum,x)=>sum+x.score,0)/h.length) + "%";
        document.getElementById('dashTopScore').innerText = Math.max(...h.map(x=>x.score)) + "%";
      } else {
        document.getElementById('dashAvgScore').innerText = "0%";
        document.getElementById('dashTopScore').innerText = "0%";
      }
    }

    function renderStudentsList() {
      const st = getStudentsList();
      document.getElementById('stCountBadge').innerText = st.length;
      const list = document.getElementById('adminStudentsList'); list.innerHTML = '';
      st.forEach((s, idx) => {
        list.innerHTML += `<div class="student-list-item">
          <div><strong style="font-size:1.1rem; color:var(--text);">${s.name}</strong> <br><small style="color:var(--text-muted); font-weight:700;">ID: ${s.id}</small></div>
          <button class="btn btn-danger btn-sm" onclick="deleteStudent(${idx})">Delete Account</button>
        </div>`;
      });
    }

    async function createNewStudent() {
      const id = document.getElementById('newStId').value.trim();
      const name = document.getElementById('newStName').value.trim();
      const pass = document.getElementById('newStPass').value.trim();
      const err = document.getElementById('stFormError');
      if(!id || !name || !pass) { err.style.display = 'block'; return; }
      err.style.display = 'none';

      const stList = getStudentsList();
      if(stList.find(s => s.id === id)) { err.innerText = "Student ID already exists!"; err.style.display = 'block'; return; }

      const hash = await hashSHA256(pass);
      stList.push({ id: id, name: name, passHash: hash });
      saveStudentsList(stList);
      document.getElementById('newStId').value = ''; document.getElementById('newStName').value = ''; document.getElementById('newStPass').value = '';
      renderStudentsList();
    }

    function deleteStudent(idx) { showCustomConfirm("Delete Student?", "Remove this account permanently?", () => { const st = getStudentsList(); st.splice(idx, 1); saveStudentsList(st); renderStudentsList(); }); }

    function renderAdminQuestions() {
      const qs = getQuestions();
      document.getElementById('qCountBadge').innerText = qs.length;
      const list = document.getElementById('adminQuestionsList'); list.innerHTML = '';
      qs.forEach((q, idx) => {
        const isActive = q.enabled !== false;
        const statusColor = isActive ? 'var(--success)' : '#b2bec3';
        const statusText = isActive ? 'Active' : 'Hidden';
        const toggleBtnText = isActive ? 'Hide' : 'Show';

        list.innerHTML += `<div class="q-admin-card" style="${!isActive ? 'opacity: 0.6;' : ''}">
          <div style="flex-grow:1;">
            <strong style="font-size:1.1rem; color:var(--text);">#${idx+1}. ${q.q}</strong>
            <span class="status-badge" style="background:${statusColor};">${statusText}</span><br>
            <small style="color:var(--success); font-weight:700;">Correct Ans: ${q.options[q.correct]}</small>
          </div>
          <div class="q-admin-actions">
            <button class="btn btn-secondary btn-sm" onclick="toggleQuestionStatus(${idx})">${toggleBtnText}</button>
            <button class="btn btn-secondary btn-sm" onclick="startEditQuestion(${idx})">Edit</button>
            <button class="btn btn-danger btn-sm" onclick="deleteQuestion(${idx})">Delete</button>
          </div>
        </div>`;
      });
    }

    function toggleQuestionStatus(idx) {
      const qs = getQuestions();
      qs[idx].enabled = qs[idx].enabled === false ? true : false;
      saveQuestions(qs);
      renderAdminQuestions();
    }

    function startEditQuestion(idx) {
      const q = getQuestions()[idx];
      document.getElementById('inputQText').value = q.q;
      document.getElementById('inputOptA').value = q.options[0];
      document.getElementById('inputOptB').value = q.options[1]; 
      document.getElementById('inputOptC').value = q.options[2];
      document.getElementById('inputOptD').value = q.options[3];
      document.getElementById('selectCorrectOpt').value = q.correct;
      document.getElementById('editQIndex').value = idx;
      document.getElementById('btnCancelEditQ').style.display = 'inline-block';
      document.getElementById('qFormHeading').scrollIntoView({behavior: 'smooth'});
    }

    function saveQuestionRecord() {
      const q = document.getElementById('inputQText').value, a = document.getElementById('inputOptA').value;
      const b = document.getElementById('inputOptB').value, c = document.getElementById('inputOptC').value;
      const d = document.getElementById('inputOptD').value, corr = parseInt(document.getElementById('selectCorrectOpt').value);
      if(!q || !a || !b || !c || !d) return document.getElementById('qFormError').style.display='block';
      const qs = getQuestions(); const idx = parseInt(document.getElementById('editQIndex').value);
      const newQ = {q, options:[a,b,c,d], correct:corr, enabled:true};
      if(idx >= 0) {
        newQ.enabled = qs[idx].enabled; 
        qs[idx] = newQ; 
      } else { qs.push(newQ); }
      saveQuestions(qs); resetQuestionForm(); renderAdminQuestions();
    }
    
    function resetQuestionForm() {
      ['inputQText','inputOptA','inputOptB','inputOptC','inputOptD'].forEach(id => document.getElementById(id).value = '');
      document.getElementById('editQIndex').value = '-1';
      document.getElementById('btnCancelEditQ').style.display = 'none'; document.getElementById('qFormError').style.display='none';
    }
    function deleteQuestion(idx) { showCustomConfirm("Delete?", "Delete this question?", () => { const qs = getQuestions(); qs.splice(idx, 1); saveQuestions(qs); renderAdminQuestions(); }); }

    pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.worker.min.js';
    async function processPDF() {
      const fileInput = document.getElementById('pdfFileInput');
      const errDiv = document.getElementById('pdfErrorMsg');
      const sucDiv = document.getElementById('pdfSuccessMsg');
      errDiv.style.display = 'none'; sucDiv.style.display = 'none';

      if (!fileInput.files.length) { errDiv.innerText = "Please select a PDF first."; errDiv.style.display = 'block'; return; }
      
      const fileReader = new FileReader();
      fileReader.onload = async function() {
        try {
          const pdf = await pdfjsLib.getDocument(new Uint8Array(this.result)).promise;
          let text = "";
          for (let i = 1; i <= pdf.numPages; i++) {
            const page = await pdf.getPage(i);
            const content = await page.getTextContent();
            text += content.items.map(item => item.str).join(" ") + "\n";
          }
          parseExtractedText(text);
          sucDiv.innerText = "Success! Questions added.";
          sucDiv.style.display = 'block'; fileInput.value = "";
        } catch (e) { errDiv.innerText = "Error: Invalid or corrupted PDF."; errDiv.style.display = 'block'; }
      };
      fileReader.readAsArrayBuffer(fileInput.files[0]);
    }

    function parseExtractedText(text) {
      const blocks = text.split(/Q:/i).filter(b => b.trim().length > 0);
      const newQs = [];
      blocks.forEach(block => {
        try {
          const qText = block.split(/A:/i)[0];
          const a = block.match(/A:\s*(.*?)(?=B:|C:|D:|Correct:|$)/is);
          const b = block.match(/B:\s*(.*?)(?=C:|D:|Correct:|$)/is);
          const c = block.match(/C:\s*(.*?)(?=D:|Correct:|$)/is);
          const d = block.match(/D:\s*(.*?)(?=Correct:|$)/is);
          const correct = block.match(/Correct:\s*([A-D])/i);
          if (qText && a && b && c && d && correct) {
            const cLetter = correct[1].toUpperCase();
            let cIndex = cLetter === 'B' ? 1 : cLetter === 'C' ? 2 : cLetter === 'D' ? 3 : 0;
            newQs.push({ q: qText.trim(), options: [a[1].trim(), b[1].trim(), c[1].trim(), d[1].trim()], correct: cIndex, enabled: true });
          }
        } catch(e){}
      });
      if(newQs.length > 0) saveQuestions(getQuestions().concat(newQs));
      else { document.getElementById('pdfErrorMsg').innerText = "Format Error (Must be Q: A: B: C: D: Correct:)"; document.getElementById('pdfErrorMsg').style.display='block'; }
      renderAdminQuestions();
    }

    function exportHistoryCSV() {
      const h = getHistory();
      if(!h.length) return alert("No results to export!");
      let csv = "Date,Student Name,Score (%)\n";
      h.forEach(row => { csv += `"${row.date}","${row.studentName}","${row.score}"\n`; });
      const blob = new Blob([csv], { type: 'text/csv' });
      const url = window.URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.setAttribute('hidden', '');
      a.setAttribute('href', url);
      a.setAttribute('download', 'quiz_results.csv');
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
    }

    function renderHistoryList() {
      const h = getHistory();
      document.getElementById('historyListContainer').innerHTML = h.map(x => `<div class="history-card"><strong>${x.studentName}</strong> <span style="font-size:1.4rem; font-weight:800; color:var(--primary);">${x.score}%</span></div>`).join('');
    }
    function promptClearHistory() { showCustomConfirm("Clear History", "Erase all records?", () => { saveHistory([]); renderHistoryList(); loadAdminDashboard(); }); }
    
    async function updateAdminPasscode() { 
      const newPass = document.getElementById('inputNewAdminPass').value.trim();
      if(!newPass) return;
      safeSetItem('quiz_admin_pass', await hashSHA256(newPass)); 
      document.getElementById('inputNewAdminPass').value = '';
      alert("Admin Passcode Saved!"); 
    }

    window.addEventListener('DOMContentLoaded', async () => { 
      await initSecurityAndData(); 
      showView('login'); 
    });
  </script>
</body>
</html>
