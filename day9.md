<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NutriScope — Precision Nutrition Tracker</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Space+Grotesk:wght@400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  :root {
    --bg-base: #070B14;
    --bg-card: #0F1629;
    --bg-card2: #141D35;
    --bg-input: #0A1020;
    --border: #1E2D4F;
    --border-light: #243356;
    --indigo: #6366F1;
    --indigo-light: #818CF8;
    --indigo-dim: rgba(99,102,241,0.15);
    --green: #10B981;
    --green-dim: rgba(16,185,129,0.12);
    --amber: #F59E0B;
    --amber-dim: rgba(245,158,11,0.12);
    --red: #EF4444;
    --red-dim: rgba(239,68,68,0.12);
    --cyan: #06B6D4;
    --purple: #A855F7;
    --text-primary: #F0F4FF;
    --text-secondary: #7B8EBF;
    --text-muted: #4A5880;
    --font-display: 'Space Grotesk', sans-serif;
    --font-body: 'Inter', sans-serif;
    --radius: 14px;
    --radius-sm: 8px;
    --shadow: 0 4px 24px rgba(0,0,0,0.5);
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg-base);
    color: var(--text-primary);
    font-family: var(--font-body);
    font-size: 14px;
    line-height: 1.6;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ─── LAYOUT ─── */
  .app-shell {
    display: flex;
    min-height: 100vh;
  }

  /* ─── SIDEBAR ─── */
  .sidebar {
    width: 240px;
    flex-shrink: 0;
    background: var(--bg-card);
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 0; left: 0;
    height: 100vh;
    z-index: 100;
    transition: transform 0.3s ease;
  }

  .sidebar-logo {
    padding: 24px 20px 20px;
    border-bottom: 1px solid var(--border);
  }

  .logo-mark {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  .logo-icon {
    width: 34px; height: 34px;
    background: var(--indigo);
    border-radius: 9px;
    display: flex; align-items: center; justify-content: center;
    font-size: 16px;
    flex-shrink: 0;
  }

  .logo-text {
    font-family: var(--font-display);
    font-size: 17px;
    font-weight: 700;
    color: var(--text-primary);
    letter-spacing: -0.3px;
  }

  .logo-text span {
    color: var(--indigo-light);
  }

  .sidebar-nav {
    flex: 1;
    padding: 16px 12px;
    display: flex;
    flex-direction: column;
    gap: 2px;
  }

  .nav-label {
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--text-muted);
    padding: 10px 8px 6px;
  }

  .nav-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 12px;
    border-radius: var(--radius-sm);
    cursor: pointer;
    transition: all 0.15s;
    color: var(--text-secondary);
    font-weight: 500;
    font-size: 13.5px;
    border: none;
    background: none;
    width: 100%;
    text-align: left;
  }

  .nav-item:hover {
    background: var(--indigo-dim);
    color: var(--text-primary);
  }

  .nav-item.active {
    background: var(--indigo-dim);
    color: var(--indigo-light);
    border: 1px solid rgba(99,102,241,0.25);
  }

  .nav-item .nav-icon {
    width: 18px;
    text-align: center;
    font-size: 15px;
  }

  .sidebar-footer {
    padding: 16px;
    border-top: 1px solid var(--border);
  }

  .reset-btn {
    width: 100%;
    padding: 9px;
    background: var(--red-dim);
    border: 1px solid rgba(239,68,68,0.2);
    color: var(--red);
    border-radius: var(--radius-sm);
    cursor: pointer;
    font-size: 12.5px;
    font-weight: 500;
    transition: all 0.15s;
  }

  .reset-btn:hover { background: rgba(239,68,68,0.2); }

  /* ─── MAIN ─── */
  .main-content {
    flex: 1;
    margin-left: 240px;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
  }

  /* ─── TOPBAR ─── */
  .topbar {
    background: var(--bg-card);
    border-bottom: 1px solid var(--border);
    padding: 0 28px;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: sticky;
    top: 0;
    z-index: 50;
  }

  .topbar-title {
    font-family: var(--font-display);
    font-size: 16px;
    font-weight: 600;
    color: var(--text-primary);
  }

  .topbar-date {
    font-size: 12px;
    color: var(--text-muted);
    margin-top: 1px;
  }

  .topbar-right {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .hamburger {
    display: none;
    background: none;
    border: none;
    color: var(--text-primary);
    font-size: 20px;
    cursor: pointer;
    padding: 4px;
  }

  /* ─── PAGES ─── */
  .page {
    display: none;
    padding: 28px;
    flex: 1;
    animation: fadeIn 0.2s ease;
  }

  .page.active { display: block; }

  @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: translateY(0); } }

  /* ─── SECTION HEADER ─── */
  .section-header {
    margin-bottom: 24px;
  }

  .section-title {
    font-family: var(--font-display);
    font-size: 22px;
    font-weight: 700;
    color: var(--text-primary);
    letter-spacing: -0.4px;
    margin-bottom: 4px;
  }

  .section-subtitle {
    color: var(--text-secondary);
    font-size: 13px;
  }

  /* ─── CARDS ─── */
  .card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 22px;
    box-shadow: var(--shadow);
  }

  .card-title {
    font-family: var(--font-display);
    font-size: 13px;
    font-weight: 600;
    color: var(--text-secondary);
    text-transform: uppercase;
    letter-spacing: 0.8px;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .card-title .dot {
    width: 7px; height: 7px;
    border-radius: 50%;
    background: var(--indigo);
    flex-shrink: 0;
  }

  .grid-2 {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
  }

  .grid-3 {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 16px;
  }

  .grid-4 {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
  }

  /* ─── FORM ELEMENTS ─── */
  .form-group {
    margin-bottom: 16px;
  }

  .form-label {
    display: block;
    font-size: 12px;
    font-weight: 500;
    color: var(--text-secondary);
    margin-bottom: 6px;
    letter-spacing: 0.3px;
  }

  .form-input, .form-select {
    width: 100%;
    background: var(--bg-input);
    border: 1px solid var(--border);
    border-radius: var(--radius-sm);
    padding: 10px 13px;
    color: var(--text-primary);
    font-family: var(--font-body);
    font-size: 14px;
    transition: border-color 0.15s, box-shadow 0.15s;
    outline: none;
    appearance: none;
  }

  .form-input:focus, .form-select:focus {
    border-color: var(--indigo);
    box-shadow: 0 0 0 3px var(--indigo-dim);
  }

  .form-select {
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='8' viewBox='0 0 12 8'%3E%3Cpath d='M1 1l5 5 5-5' stroke='%236366F1' stroke-width='1.5' fill='none' stroke-linecap='round'/%3E%3C/svg%3E");
    background-repeat: no-repeat;
    background-position: right 12px center;
    padding-right: 36px;
  }

  .form-select option { background: var(--bg-card2); }

  .btn {
    padding: 10px 20px;
    border-radius: var(--radius-sm);
    border: none;
    font-family: var(--font-body);
    font-size: 13.5px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.15s;
    display: inline-flex;
    align-items: center;
    gap: 7px;
  }

  .btn-primary {
    background: var(--indigo);
    color: white;
  }

  .btn-primary:hover { background: #7577F5; transform: translateY(-1px); box-shadow: 0 4px 16px rgba(99,102,241,0.4); }

  .btn-secondary {
    background: var(--bg-card2);
    border: 1px solid var(--border);
    color: var(--text-primary);
  }

  .btn-secondary:hover { border-color: var(--indigo); background: var(--indigo-dim); }

  .btn-danger {
    background: transparent;
    border: 1px solid rgba(239,68,68,0.3);
    color: var(--red);
    padding: 5px 10px;
    font-size: 12px;
  }

  .btn-danger:hover { background: var(--red-dim); }

  .btn-sm { padding: 7px 14px; font-size: 12.5px; }

  /* ─── PROFILE PAGE ─── */
  .profile-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }

  .profile-card { grid-column: span 2; }

  .save-row {
    display: flex;
    justify-content: flex-end;
    margin-top: 8px;
  }

  /* ─── BMI DISPLAY ─── */
  .bmi-display {
    background: var(--bg-card2);
    border-radius: var(--radius-sm);
    padding: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-top: 16px;
    border: 1px solid var(--border);
  }

  .bmi-num {
    font-family: var(--font-display);
    font-size: 32px;
    font-weight: 700;
    color: var(--text-primary);
    line-height: 1;
  }

  .bmi-label {
    font-size: 12px;
    color: var(--text-muted);
    margin-top: 2px;
  }

  .bmi-badge {
    padding: 5px 12px;
    border-radius: 20px;
    font-size: 12px;
    font-weight: 600;
  }

  /* ─── FOOD LOG PAGE ─── */
  .food-adder {
    display: grid;
    grid-template-columns: 1fr auto auto auto;
    gap: 10px;
    align-items: end;
    margin-bottom: 20px;
  }

  .food-table-wrap {
    overflow-x: auto;
    border-radius: var(--radius-sm);
    border: 1px solid var(--border);
  }

  .food-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
  }

  .food-table th {
    background: var(--bg-card2);
    color: var(--text-muted);
    font-weight: 600;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.7px;
    padding: 10px 14px;
    text-align: left;
    border-bottom: 1px solid var(--border);
    white-space: nowrap;
  }

  .food-table td {
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    color: var(--text-primary);
    vertical-align: middle;
  }

  .food-table tr:last-child td { border-bottom: none; }

  .food-table tr:hover td { background: rgba(99,102,241,0.04); }

  .food-table td.editable {
    cursor: text;
  }

  .food-table td.editable[contenteditable="true"] {
    outline: 1px solid var(--indigo);
    border-radius: 4px;
    background: var(--indigo-dim);
  }

  .qty-cell input {
    width: 70px;
    background: var(--bg-input);
    border: 1px solid var(--border);
    border-radius: 5px;
    padding: 5px 8px;
    color: var(--text-primary);
    font-size: 13px;
    text-align: center;
    outline: none;
  }

  .qty-cell input:focus { border-color: var(--indigo); }

  .unit-cell select {
    background: var(--bg-input);
    border: 1px solid var(--border);
    border-radius: 5px;
    padding: 5px 8px;
    color: var(--text-primary);
    font-size: 12px;
    outline: none;
    cursor: pointer;
  }

  .empty-log {
    text-align: center;
    padding: 48px 20px;
    color: var(--text-muted);
  }

  .empty-log .empty-icon { font-size: 36px; margin-bottom: 10px; }
  .empty-log .empty-text { font-size: 14px; }

  /* ─── DASHBOARD ─── */
  .energy-hero {
    background: linear-gradient(135deg, #0F1629 0%, #131E3A 100%);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 28px;
    display: flex;
    align-items: center;
    gap: 32px;
    margin-bottom: 20px;
    position: relative;
    overflow: hidden;
  }

  .energy-hero::before {
    content: '';
    position: absolute;
    top: -60px; right: -60px;
    width: 200px; height: 200px;
    background: radial-gradient(circle, rgba(99,102,241,0.15) 0%, transparent 70%);
    pointer-events: none;
  }

  .ring-wrap {
    position: relative;
    width: 140px; height: 140px;
    flex-shrink: 0;
  }

  #energyRingChart { position: absolute; top: 0; left: 0; }

  .ring-center {
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
  }

  .ring-pct {
    font-family: var(--font-display);
    font-size: 28px;
    font-weight: 700;
    color: var(--text-primary);
    line-height: 1;
  }

  .ring-pct-label {
    font-size: 10px;
    color: var(--text-muted);
    letter-spacing: 0.5px;
    margin-top: 2px;
  }

  .energy-details { flex: 1; }

  .energy-title {
    font-family: var(--font-display);
    font-size: 20px;
    font-weight: 700;
    color: var(--text-primary);
    margin-bottom: 6px;
  }

  .energy-subtitle {
    font-size: 12.5px;
    color: var(--text-secondary);
    margin-bottom: 18px;
  }

  .energy-stats {
    display: flex;
    gap: 24px;
  }

  .e-stat .e-val {
    font-family: var(--font-display);
    font-size: 20px;
    font-weight: 700;
    color: var(--text-primary);
    line-height: 1;
  }

  .e-stat .e-lbl {
    font-size: 11px;
    color: var(--text-muted);
    margin-top: 2px;
  }

  /* ─── MACRO CARDS ─── */
  .macro-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 18px;
  }

  .macro-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 12px;
  }

  .macro-name {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-secondary);
    text-transform: uppercase;
    letter-spacing: 0.6px;
    display: flex;
    align-items: center;
    gap: 7px;
  }

  .macro-dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .macro-val {
    font-family: var(--font-display);
    font-size: 22px;
    font-weight: 700;
    color: var(--text-primary);
    line-height: 1;
    margin-bottom: 3px;
  }

  .macro-sub {
    font-size: 11px;
    color: var(--text-muted);
    margin-bottom: 10px;
  }

  .prog-bar {
    height: 5px;
    background: var(--border);
    border-radius: 10px;
    overflow: hidden;
  }

  .prog-fill {
    height: 100%;
    border-radius: 10px;
    transition: width 0.5s ease;
  }

  /* ─── NUTRIENT TABLE ─── */
  .nutrient-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
  }

  .nutrient-table th {
    color: var(--text-muted);
    font-weight: 600;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.6px;
    padding: 8px 12px;
    text-align: left;
    border-bottom: 1px solid var(--border);
  }

  .nutrient-table td {
    padding: 10px 12px;
    border-bottom: 1px solid rgba(30,45,79,0.5);
    color: var(--text-primary);
  }

  .nutrient-table tr:last-child td { border-bottom: none; }

  .status-pill {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 11px;
    font-weight: 600;
  }

  .status-good { background: var(--green-dim); color: var(--green); }
  .status-low { background: var(--red-dim); color: var(--red); }
  .status-high { background: var(--amber-dim); color: var(--amber); }
  .status-na { background: rgba(74,88,128,0.2); color: var(--text-muted); }

  .mini-bar-wrap {
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .mini-bar {
    flex: 1;
    height: 4px;
    background: var(--border);
    border-radius: 10px;
    overflow: hidden;
  }

  .mini-bar-fill { height: 100%; border-radius: 10px; }

  /* ─── DEFICIENCY / EXCESS CARDS ─── */
  .alert-card {
    border-radius: var(--radius-sm);
    padding: 12px 16px;
    margin-bottom: 8px;
    display: flex;
    align-items: center;
    gap: 12px;
    border: 1px solid transparent;
  }

  .alert-card.deficit {
    background: var(--red-dim);
    border-color: rgba(239,68,68,0.2);
  }

  .alert-card.excess {
    background: var(--amber-dim);
    border-color: rgba(245,158,11,0.2);
  }

  .alert-icon { font-size: 18px; flex-shrink: 0; }

  .alert-info .alert-name {
    font-weight: 600;
    font-size: 13px;
    color: var(--text-primary);
    margin-bottom: 1px;
  }

  .alert-info .alert-detail {
    font-size: 11.5px;
    color: var(--text-secondary);
  }

  .alert-pct {
    margin-left: auto;
    font-family: var(--font-display);
    font-size: 16px;
    font-weight: 700;
  }

  .deficit .alert-pct { color: var(--red); }
  .excess .alert-pct { color: var(--amber); }

  /* ─── RECOMMENDATIONS ─── */
  .rec-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
    margin-bottom: 14px;
  }

  .rec-tag {
    display: inline-flex;
    align-items: center;
    gap: 5px;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 11px;
    font-weight: 600;
    margin-bottom: 10px;
  }

  .tag-add { background: var(--green-dim); color: var(--green); }
  .tag-swap { background: var(--indigo-dim); color: var(--indigo-light); }
  .tag-portion { background: var(--amber-dim); color: var(--amber); }

  .rec-title {
    font-size: 14.5px;
    font-weight: 600;
    color: var(--text-primary);
    margin-bottom: 6px;
  }

  .rec-desc {
    font-size: 13px;
    color: var(--text-secondary);
    line-height: 1.6;
  }

  /* ─── CHARTS ─── */
  .chart-wrap {
    position: relative;
    height: 220px;
  }

  /* ─── MOBILE BOTTOM NAV ─── */
  .mobile-nav {
    display: none;
    position: fixed;
    bottom: 0; left: 0; right: 0;
    background: var(--bg-card);
    border-top: 1px solid var(--border);
    padding: 8px 0 env(safe-area-inset-bottom, 8px);
    z-index: 100;
  }

  .mobile-nav-inner {
    display: flex;
    justify-content: space-around;
  }

  .mobile-nav-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 3px;
    padding: 6px 12px;
    cursor: pointer;
    color: var(--text-muted);
    font-size: 10px;
    font-weight: 500;
    border: none;
    background: none;
    transition: color 0.15s;
    border-radius: 8px;
  }

  .mobile-nav-item .m-icon { font-size: 18px; }

  .mobile-nav-item.active { color: var(--indigo-light); }

  .overlay {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.6);
    z-index: 90;
  }

  .overlay.active { display: block; }

  /* ─── RESPONSIVE ─── */
  @media (max-width: 900px) {
    .sidebar {
      transform: translateX(-100%);
    }

    .sidebar.open { transform: translateX(0); }

    .main-content { margin-left: 0; padding-bottom: 70px; }

    .hamburger { display: flex; }

    .mobile-nav { display: block; }

    .grid-4 { grid-template-columns: repeat(2, 1fr); }

    .grid-3 { grid-template-columns: 1fr 1fr; }

    .energy-hero { flex-direction: column; gap: 20px; }

    .food-adder {
      grid-template-columns: 1fr 1fr;
    }

    .food-adder .btn { grid-column: span 2; }

    .profile-grid { grid-template-columns: 1fr; }
    .profile-card { grid-column: span 1; }

    .page { padding: 16px; }
  }

  @media (max-width: 600px) {
    .grid-2 { grid-template-columns: 1fr; }
    .grid-3 { grid-template-columns: 1fr; }
    .grid-4 { grid-template-columns: 1fr 1fr; }
    .energy-stats { gap: 14px; }
    .food-adder { grid-template-columns: 1fr; }
    .food-adder .btn { grid-column: span 1; }
  }

  /* ─── SCROLLBAR ─── */
  ::-webkit-scrollbar { width: 6px; height: 6px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border-light); border-radius: 10px; }
  ::-webkit-scrollbar-thumb:hover { background: var(--indigo); }

  /* ─── TOAST ─── */
  .toast {
    position: fixed;
    bottom: 80px; right: 20px;
    background: var(--bg-card2);
    border: 1px solid var(--border);
    border-left: 3px solid var(--green);
    border-radius: var(--radius-sm);
    padding: 12px 16px;
    color: var(--text-primary);
    font-size: 13px;
    font-weight: 500;
    z-index: 9999;
    animation: slideIn 0.3s ease, fadeOut 0.4s ease 2.2s forwards;
    box-shadow: var(--shadow);
    max-width: 280px;
  }

  @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
  @keyframes fadeOut { to { opacity: 0; transform: translateY(-6px); } }

  .no-data-msg {
    text-align: center;
    padding: 40px 20px;
    color: var(--text-muted);
    font-size: 13.5px;
  }
</style>
</head>
<body>

<div class="overlay" id="overlay" onclick="closeSidebar()"></div>

<!-- SIDEBAR -->
<aside class="sidebar" id="sidebar">
  <div class="sidebar-logo">
    <div class="logo-mark">
      <div class="logo-icon">🌿</div>
      <div class="logo-text">Nutri<span>Scope</span></div>
    </div>
  </div>
  <nav class="sidebar-nav">
    <div class="nav-label">Menu</div>
    <button class="nav-item active" onclick="showPage('dashboard')">
      <span class="nav-icon">📊</span> Dashboard
    </button>
    <button class="nav-item" onclick="showPage('profile')">
      <span class="nav-icon">👤</span> Profile
    </button>
    <button class="nav-item" onclick="showPage('log')">
      <span class="nav-icon">🍽️</span> Food Log
    </button>
    <button class="nav-item" onclick="showPage('recommendations')">
      <span class="nav-icon">💡</span> Recommendations
    </button>
    <button class="nav-item" onclick="showPage('nutrients')">
      <span class="nav-icon">🔬</span> Nutrients
    </button>
  </nav>
  <div class="sidebar-footer">
    <button class="reset-btn" onclick="resetAll()">🗑 Reset All Data</button>
  </div>
</aside>

<!-- MAIN -->
<div class="main-content">
  <!-- TOPBAR -->
  <header class="topbar">
    <div>
      <button class="hamburger" onclick="toggleSidebar()">☰</button>
    </div>
    <div style="text-align:right">
      <div class="topbar-title" id="topbarTitle">Dashboard</div>
      <div class="topbar-date" id="topbarDate"></div>
    </div>
  </header>

  <!-- DASHBOARD PAGE -->
  <div class="page active" id="page-dashboard">
    <div class="section-header">
      <div class="section-title">Today's Overview</div>
      <div class="section-subtitle">Your nutrition summary based on logged meals</div>
    </div>

    <div id="nologmsg" class="no-data-msg" style="display:none">
      <div style="font-size:40px;margin-bottom:12px">🍃</div>
      <div>No foods logged yet. Go to <strong>Food Log</strong> to add your meals.</div>
    </div>

    <div id="dashContent">
      <!-- Energy Hero -->
      <div class="energy-hero">
        <div class="ring-wrap">
          <canvas id="energyRingChart" width="140" height="140"></canvas>
          <div class="ring-center">
            <div class="ring-pct" id="energyPct">0%</div>
            <div class="ring-pct-label">of goal</div>
          </div>
        </div>
        <div class="energy-details">
          <div class="energy-title">Energy Balance</div>
          <div class="energy-subtitle" id="energySubtitle">Set your profile to compute targets</div>
          <div class="energy-stats">
            <div class="e-stat">
              <div class="e-val" id="eConsumed">0</div>
              <div class="e-lbl">kcal consumed</div>
            </div>
            <div class="e-stat">
              <div class="e-val" id="eTarget">—</div>
              <div class="e-lbl">kcal target</div>
            </div>
            <div class="e-stat">
              <div class="e-val" id="eRemaining">—</div>
              <div class="e-lbl">kcal remaining</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Macros -->
      <div class="grid-4" style="margin-bottom:20px" id="macroCards"></div>

      <!-- Bottom Row -->
      <div class="grid-2" style="margin-bottom:20px">
        <div class="card">
          <div class="card-title"><span class="dot" style="background:var(--red)"></span>Top Deficiencies</div>
          <div id="deficiencyList"></div>
        </div>
        <div class="card">
          <div class="card-title"><span class="dot" style="background:var(--amber)"></span>Top Excesses</div>
          <div id="excessList"></div>
        </div>
      </div>

      <!-- Macro Bar Chart -->
      <div class="card">
        <div class="card-title"><span class="dot" style="background:var(--cyan)"></span>Macro Distribution</div>
        <div class="chart-wrap">
          <canvas id="macroBarChart"></canvas>
        </div>
      </div>
    </div>
  </div>

  <!-- PROFILE PAGE -->
  <div class="page" id="page-profile">
    <div class="section-header">
      <div class="section-title">Your Profile</div>
      <div class="section-subtitle">Used to calculate personalised energy and nutrient targets</div>
    </div>

    <div class="card">
      <div class="profile-grid">
        <div class="form-group">
          <label class="form-label">Age (years)</label>
          <input type="number" class="form-input" id="p_age" placeholder="e.g. 28" min="10" max="100">
        </div>
        <div class="form-group">
          <label class="form-label">Gender</label>
          <select class="form-select" id="p_gender">
            <option value="">Select gender</option>
            <option value="male">Male</option>
            <option value="female">Female</option>
            <option value="other">Other / Prefer not to say</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">Height (cm)</label>
          <input type="number" class="form-input" id="p_height" placeholder="e.g. 170" min="100" max="250">
        </div>
        <div class="form-group">
          <label class="form-label">Weight (kg)</label>
          <input type="number" class="form-input" id="p_weight" placeholder="e.g. 65" min="20" max="300">
        </div>
        <div class="form-group">
          <label class="form-label">Activity Level</label>
          <select class="form-select" id="p_activity">
            <option value="">Select activity level</option>
            <option value="sedentary">Sedentary (office job, little exercise)</option>
            <option value="light">Light (exercise 1–3 days/week)</option>
            <option value="moderate">Moderate (exercise 3–5 days/week)</option>
            <option value="active">Active (exercise 6–7 days/week)</option>
            <option value="very_active">Very Active (hard exercise, physical job)</option>
          </select>
        </div>
        <div class="form-group">
          <label class="form-label">Dietary Preference</label>
          <select class="form-select" id="p_diet">
            <option value="">Select preference</option>
            <option value="vegetarian">Vegetarian</option>
            <option value="eggetarian">Eggetarian</option>
            <option value="non_vegetarian">Non-Vegetarian</option>
          </select>
        </div>
        <div class="profile-card" id="bmiDisplay" style="display:none">
          <div class="bmi-display">
            <div>
              <div class="bmi-num" id="bmiVal">—</div>
              <div class="bmi-label">Body Mass Index (BMI)</div>
            </div>
            <div>
              <div class="bmi-badge" id="bmiBadge"></div>
            </div>
            <div style="text-align:right">
              <div style="font-size:12px;color:var(--text-secondary)">TDEE</div>
              <div style="font-family:var(--font-display);font-size:20px;font-weight:700;color:var(--text-primary)" id="tdeeVal">—</div>
              <div style="font-size:11px;color:var(--text-muted)">kcal / day</div>
            </div>
          </div>
        </div>
        <div class="profile-card">
          <div class="save-row">
            <button class="btn btn-primary" onclick="saveProfile()">💾 Save Profile</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- FOOD LOG PAGE -->
  <div class="page" id="page-log">
    <div class="section-header">
      <div class="section-title">Food Log</div>
      <div class="section-subtitle">Add everything you've eaten today</div>
    </div>

    <div class="card" style="margin-bottom:20px">
      <div class="card-title"><span class="dot"></span>Add Food</div>
      <div class="food-adder">
        <div class="form-group" style="margin-bottom:0">
          <label class="form-label">Food Item</label>
          <select class="form-select" id="foodSelect">
            <option value="">Select food…</option>
          </select>
        </div>
        <div class="form-group" style="margin-bottom:0">
          <label class="form-label">Quantity</label>
          <input type="number" class="form-input" id="foodQty" value="100" min="1" style="width:90px">
        </div>
        <div class="form-group" style="margin-bottom:0">
          <label class="form-label">Unit</label>
          <select class="form-select" id="foodUnit" style="width:90px">
            <option value="g">g</option>
            <option value="ml">ml</option>
            <option value="pcs">pcs</option>
          </select>
        </div>
        <button class="btn btn-primary" onclick="addFood()" style="align-self:flex-end">+ Add</button>
      </div>
    </div>

    <div class="card">
      <div class="card-title" style="margin-bottom:12px">
        <span class="dot" style="background:var(--green)"></span>Logged Foods
        <span style="margin-left:auto;font-weight:500;font-size:12px;color:var(--text-secondary)" id="logCount">0 items</span>
      </div>
      <div class="food-table-wrap">
        <table class="food-table">
          <thead>
            <tr>
              <th>Food</th>
              <th>Qty</th>
              <th>Unit</th>
              <th>Calories</th>
              <th>Protein</th>
              <th>Carbs</th>
              <th>Fat</th>
              <th>Fiber</th>
              <th></th>
            </tr>
          </thead>
          <tbody id="logTableBody">
            <tr><td colspan="9" class="empty-log"><div class="empty-icon">🍽️</div><div class="empty-text">No foods added yet</div></td></tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

  <!-- RECOMMENDATIONS PAGE -->
  <div class="page" id="page-recommendations">
    <div class="section-header">
      <div class="section-title">Recommendations</div>
      <div class="section-subtitle">Personalised suggestions based on your nutrient gaps</div>
    </div>
    <div id="recsContent"></div>
  </div>

  <!-- NUTRIENTS PAGE -->
  <div class="page" id="page-nutrients">
    <div class="section-header">
      <div class="section-title">Nutrient Breakdown</div>
      <div class="section-subtitle">Detailed view of all tracked nutrients vs. your targets</div>
    </div>
    <div class="card">
      <table class="nutrient-table">
        <thead>
          <tr>
            <th>Nutrient</th>
            <th>Consumed</th>
            <th>Target</th>
            <th>Progress</th>
            <th>Status</th>
          </tr>
        </thead>
        <tbody id="nutrientTableBody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- MOBILE NAV -->
<nav class="mobile-nav">
  <div class="mobile-nav-inner">
    <button class="mobile-nav-item active" onclick="showPage('dashboard')">
      <span class="m-icon">📊</span>Dashboard
    </button>
    <button class="mobile-nav-item" onclick="showPage('profile')">
      <span class="m-icon">👤</span>Profile
    </button>
    <button class="mobile-nav-item" onclick="showPage('log')">
      <span class="m-icon">🍽️</span>Log
    </button>
    <button class="mobile-nav-item" onclick="showPage('recommendations')">
      <span class="m-icon">💡</span>Recs
    </button>
    <button class="mobile-nav-item" onclick="showPage('nutrients')">
      <span class="m-icon">🔬</span>Nutrients
    </button>
  </div>
</nav>

<script>
// ───────────────────────────────────────────
// FOOD DATABASE (per 100g / 100ml / 1 piece)
// ───────────────────────────────────────────
const FOOD_DB = {
  "Rice":     { cal:130, pro:2.7, carb:28.2, fat:0.3, fiber:0.4, iron:0.2, ca:10, vitc:0, vitd:0, b12:0 },
  "Roti":     { cal:297, pro:7.9, carb:55.8, fat:3.7, fiber:2.7, iron:1.7, ca:38, vitc:0, vitd:0, b12:0 },
  "Dal":      { cal:116, pro:9.0, carb:20.0, fat:0.4, fiber:7.9, iron:3.3, ca:19, vitc:1.5, vitd:0, b12:0 },
  "Paneer":   { cal:265, pro:18.3, carb:1.2, fat:20.8, fiber:0, iron:0.2, ca:480, vitc:0, vitd:0.4, b12:1.3 },
  "Curd":     { cal:61, pro:3.5, carb:4.7, fat:3.3, fiber:0, iron:0.1, ca:121, vitc:0.5, vitd:0.1, b12:0.4 },
  "Chana":    { cal:164, pro:8.9, carb:27.4, fat:2.6, fiber:7.6, iron:2.9, ca:49, vitc:1.3, vitd:0, b12:0 },
  "Rajma":    { cal:127, pro:8.7, carb:22.8, fat:0.5, fiber:6.4, iron:2.2, ca:28, vitc:1.2, vitd:0, b12:0 },
  "Banana":   { cal:89, pro:1.1, carb:22.8, fat:0.3, fiber:2.6, iron:0.3, ca:5, vitc:8.7, vitd:0, b12:0 },
  "Apple":    { cal:52, pro:0.3, carb:13.8, fat:0.2, fiber:2.4, iron:0.1, ca:6, vitc:4.6, vitd:0, b12:0 },
  "Milk":     { cal:61, pro:3.2, carb:4.8, fat:3.3, fiber:0, iron:0.1, ca:113, vitc:0.5, vitd:1.2, b12:0.5 },
  "Oats":     { cal:379, pro:13.2, carb:67.7, fat:6.9, fiber:10.6, iron:4.7, ca:52, vitc:0, vitd:0, b12:0 },
  "Bread":    { cal:265, pro:9.0, carb:49.0, fat:3.2, fiber:2.7, iron:3.6, ca:260, vitc:0, vitd:0, b12:0 },
  "Egg":      { cal:155, pro:13.0, carb:1.1, fat:10.6, fiber:0, iron:1.8, ca:56, vitc:0, vitd:2.2, b12:1.1 },
  "Chicken":  { cal:239, pro:27.3, carb:0, fat:13.6, fiber:0, iron:1.3, ca:15, vitc:0, vitd:0.1, b12:0.3 },
  "Fish":     { cal:136, pro:20.1, carb:0, fat:5.7, fiber:0, iron:0.9, ca:28, vitc:0, vitd:11.2, b12:3.5 },
  "Potato":   { cal:77, pro:2.0, carb:17.0, fat:0.1, fiber:2.2, iron:0.8, ca:12, vitc:19.7, vitd:0, b12:0 },
  "Poha":     { cal:110, pro:2.4, carb:23.4, fat:0.6, fiber:0.2, iron:2.8, ca:14, vitc:0, vitd:0, b12:0 },
  "Idli":     { cal:58, pro:1.9, carb:11.4, fat:0.4, fiber:0.5, iron:0.4, ca:14, vitc:0, vitd:0, b12:0 },
  "Dosa":     { cal:168, pro:3.9, carb:24.1, fat:6.0, fiber:1.2, iron:1.0, ca:25, vitc:0, vitd:0, b12:0 },
  "Spinach":  { cal:23, pro:2.9, carb:3.6, fat:0.4, fiber:2.2, iron:2.7, ca:99, vitc:28.1, vitd:0, b12:0 }
};

const NUTRIENT_LABELS = {
  cal:"Calories (kcal)", pro:"Protein (g)", carb:"Carbohydrates (g)", fat:"Fat (g)",
  fiber:"Fiber (g)", iron:"Iron (mg)", ca:"Calcium (mg)", vitc:"Vitamin C (mg)",
  vitd:"Vitamin D (µg)", b12:"Vitamin B12 (µg)"
};

const NUTRIENT_UNITS = {
  cal:"kcal", pro:"g", carb:"g", fat:"g", fiber:"g",
  iron:"mg", ca:"mg", vitc:"mg", vitd:"µg", b12:"µg"
};

// ───────────────────────────────────────────
// STATE
// ───────────────────────────────────────────
let profile = JSON.parse(localStorage.getItem('ns_profile') || 'null');
let foodLog = JSON.parse(localStorage.getItem('ns_log') || '[]');
let energyRingInst = null, macroBarInst = null;
let currentPage = 'dashboard';

// ───────────────────────────────────────────
// INIT
// ───────────────────────────────────────────
document.addEventListener('DOMContentLoaded', () => {
  document.getElementById('topbarDate').textContent = new Date().toLocaleDateString('en-IN', {
    weekday:'long', day:'numeric', month:'long', year:'numeric'
  });

  // Populate food select
  const sel = document.getElementById('foodSelect');
  Object.keys(FOOD_DB).forEach(f => {
    const o = document.createElement('option');
    o.value = f; o.textContent = f;
    sel.appendChild(o);
  });

  // Load profile
  if (profile) loadProfileFields();

  renderLogTable();
  renderDashboard();
});

// ───────────────────────────────────────────
// NAVIGATION
// ───────────────────────────────────────────
function showPage(id) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.getElementById('page-' + id).classList.add('active');

  const titles = {
    dashboard: 'Dashboard', profile: 'Profile',
    log: 'Food Log', recommendations: 'Recommendations', nutrients: 'Nutrients'
  };

  document.querySelectorAll('.nav-item').forEach((btn, i) => {
    btn.classList.toggle('active', btn.textContent.trim().toLowerCase().startsWith(titles[id].split(' ')[0].toLowerCase()));
  });

  document.querySelectorAll('.mobile-nav-item').forEach(btn => {
    btn.classList.remove('active');
  });

  // Simpler active for mobile
  const mItems = document.querySelectorAll('.mobile-nav-item');
  const pageOrder = ['dashboard', 'profile', 'log', 'recommendations', 'nutrients'];
  mItems[pageOrder.indexOf(id)].classList.add('active');

  document.getElementById('topbarTitle').textContent = titles[id];
  currentPage = id;
  closeSidebar();

  if (id === 'dashboard') renderDashboard();
  if (id === 'recommendations') renderRecommendations();
  if (id === 'nutrients') renderNutrientTable();
}

function toggleSidebar() {
  document.getElementById('sidebar').classList.toggle('open');
  document.getElementById('overlay').classList.toggle('active');
}

function closeSidebar() {
  document.getElementById('sidebar').classList.remove('open');
  document.getElementById('overlay').classList.remove('active');
}

// ───────────────────────────────────────────
// PROFILE
// ───────────────────────────────────────────
function saveProfile() {
  const age = +document.getElementById('p_age').value;
  const gender = document.getElementById('p_gender').value;
  const height = +document.getElementById('p_height').value;
  const weight = +document.getElementById('p_weight').value;
  const activity = document.getElementById('p_activity').value;
  const diet = document.getElementById('p_diet').value;

  if (!age || !gender || !height || !weight || !activity || !diet) {
    showToast('Please fill in all profile fields', 'error'); return;
  }

  const bmr = gender === 'male'
    ? 10*weight + 6.25*height - 5*age + 5
    : 10*weight + 6.25*height - 5*age - 161;

  const actMultipliers = {
    sedentary: 1.2, light: 1.375, moderate: 1.55, active: 1.725, very_active: 1.9
  };

  const tdee = Math.round(bmr * actMultipliers[activity]);
  const bmi = +(weight / ((height/100)**2)).toFixed(1);

  profile = { age, gender, height, weight, activity, diet, tdee, bmi };
  localStorage.setItem('ns_profile', JSON.stringify(profile));

  // Show BMI
  document.getElementById('bmiDisplay').style.display = 'block';
  document.getElementById('bmiVal').textContent = bmi;
  document.getElementById('tdeeVal').textContent = tdee;

  let bmiCat = '', bmiColor = '';
  if (bmi < 18.5) { bmiCat = 'Underweight'; bmiColor = '#3B82F6'; }
  else if (bmi < 25) { bmiCat = 'Normal'; bmiColor = '#10B981'; }
  else if (bmi < 30) { bmiCat = 'Overweight'; bmiColor = '#F59E0B'; }
  else { bmiCat = 'Obese'; bmiColor = '#EF4444'; }

  const badge = document.getElementById('bmiBadge');
  badge.textContent = bmiCat;
  badge.style.cssText = `background:${bmiColor}20;color:${bmiColor};border:1px solid ${bmiColor}40;border-radius:20px;padding:5px 14px;font-size:13px;font-weight:600`;

  showToast('Profile saved! Targets updated.');
  renderDashboard();
}

function loadProfileFields() {
  if (!profile) return;
  document.getElementById('p_age').value = profile.age;
  document.getElementById('p_gender').value = profile.gender;
  document.getElementById('p_height').value = profile.height;
  document.getElementById('p_weight').value = profile.weight;
  document.getElementById('p_activity').value = profile.activity;
  document.getElementById('p_diet').value = profile.diet;

  document.getElementById('bmiDisplay').style.display = 'block';
  document.getElementById('bmiVal').textContent = profile.bmi;
  document.getElementById('tdeeVal').textContent = profile.tdee;

  let bmiCat = '', bmiColor = '';
  const bmi = profile.bmi;
  if (bmi < 18.5) { bmiCat = 'Underweight'; bmiColor = '#3B82F6'; }
  else if (bmi < 25) { bmiCat = 'Normal'; bmiColor = '#10B981'; }
  else if (bmi < 30) { bmiCat = 'Overweight'; bmiColor = '#F59E0B'; }
  else { bmiCat = 'Obese'; bmiColor = '#EF4444'; }

  const badge = document.getElementById('bmiBadge');
  badge.textContent = bmiCat;
  badge.style.cssText = `background:${bmiColor}20;color:${bmiColor};border:1px solid ${bmiColor}40;border-radius:20px;padding:5px 14px;font-size:13px;font-weight:600`;
}

// ───────────────────────────────────────────
// TARGETS
// ───────────────────────────────────────────
function getTargets() {
  if (!profile) {
    return { cal:2000, pro:50, carb:275, fat:65, fiber:28, iron:18, ca:1000, vitc:65, vitd:15, b12:2.4 };
  }

  const { tdee, weight, gender, age } = profile;
  const proTarget = weight * 1.2;
  const fatTarget = (tdee * 0.25) / 9;
  const carbTarget = (tdee - proTarget * 4 - fatTarget * 9) / 4;

  // Micronutrients: ICMR RDA
  const ironTarget = gender === 'female' && age >= 14 && age <= 50 ? 29 : 17;
  const vitdTarget = age > 70 ? 20 : 15;
  const b12Target = 2.2;
  const vitcTarget = gender === 'male' ? 80 : 65;

  return {
    cal: tdee, pro: Math.round(proTarget), carb: Math.round(carbTarget),
    fat: Math.round(fatTarget), fiber: 30, iron: ironTarget,
    ca: 1000, vitc: vitcTarget, vitd: vitdTarget, b12: b12Target
  };
}

// ───────────────────────────────────────────
// TOTALS FROM LOG
// ───────────────────────────────────────────
function getTotals() {
  const totals = { cal:0, pro:0, carb:0, fat:0, fiber:0, iron:0, ca:0, vitc:0, vitd:0, b12:0 };
  foodLog.forEach(item => {
    const base = FOOD_DB[item.food];
    const factor = item.qty / 100;
    Object.keys(totals).forEach(k => totals[k] += (base[k] || 0) * factor);
  });
  Object.keys(totals).forEach(k => totals[k] = +totals[k].toFixed(2));
  return totals;
}

// ───────────────────────────────────────────
// FOOD LOG
// ───────────────────────────────────────────
function addFood() {
  const food = document.getElementById('foodSelect').value;
  const qty = +document.getElementById('foodQty').value;
  const unit = document.getElementById('foodUnit').value;

  if (!food) { showToast('Select a food item', 'error'); return; }
  if (!qty || qty <= 0) { showToast('Enter a valid quantity', 'error'); return; }

  foodLog.push({ id: Date.now(), food, qty, unit });
  localStorage.setItem('ns_log', JSON.stringify(foodLog));
  renderLogTable();
  showToast(`${food} added to log`);

  // Reset
  document.getElementById('foodSelect').value = '';
  document.getElementById('foodQty').value = '100';
}

function removeFood(id) {
  foodLog = foodLog.filter(i => i.id !== id);
  localStorage.setItem('ns_log', JSON.stringify(foodLog));
  renderLogTable();
  if (currentPage === 'dashboard') renderDashboard();
}

function updateQty(id, val) {
  const item = foodLog.find(i => i.id === id);
  if (item) {
    item.qty = Math.max(1, +val || 1);
    localStorage.setItem('ns_log', JSON.stringify(foodLog));
    renderLogTableRow(id);
    if (currentPage === 'dashboard') renderDashboard();
  }
}

function renderLogTable() {
  const tbody = document.getElementById('logTableBody');
  document.getElementById('logCount').textContent = `${foodLog.length} item${foodLog.length !== 1 ? 's' : ''}`;

  if (!foodLog.length) {
    tbody.innerHTML = `<tr><td colspan="9" class="empty-log">
      <div class="empty-icon">🍽️</div>
      <div class="empty-text">No foods added yet. Select a food above and click + Add.</div>
    </td></tr>`;
    return;
  }

  tbody.innerHTML = foodLog.map(item => buildLogRow(item)).join('');
}

function renderLogTableRow(id) {
  const el = document.querySelector(`[data-row="${id}"]`);
  if (!el) return;
  const item = foodLog.find(i => i.id === id);
  if (!item) return;
  el.outerHTML = buildLogRow(item);
}

function buildLogRow(item) {
  const base = FOOD_DB[item.food];
  const f = item.qty / 100;
  const cal = (base.cal * f).toFixed(0);
  const pro = (base.pro * f).toFixed(1);
  const carb = (base.carb * f).toFixed(1);
  const fat = (base.fat * f).toFixed(1);
  const fiber = (base.fiber * f).toFixed(1);

  return `<tr data-row="${item.id}">
    <td><strong>${item.food}</strong></td>
    <td class="qty-cell"><input type="number" value="${item.qty}" min="1"
      onchange="updateQty(${item.id}, this.value)" oninput="updateQty(${item.id}, this.value)"></td>
    <td>${item.unit}</td>
    <td style="color:var(--amber)">${cal}</td>
    <td>${pro}g</td>
    <td>${carb}g</td>
    <td>${fat}g</td>
    <td>${fiber}g</td>
    <td><button class="btn btn-danger btn-sm" onclick="removeFood(${item.id})">✕</button></td>
  </tr>`;
}

// ───────────────────────────────────────────
// DASHBOARD
// ───────────────────────────────────────────
function renderDashboard() {
  const totals = getTotals();
  const targets = getTargets();
  const hasLog = foodLog.length > 0;

  document.getElementById('nologmsg').style.display = hasLog ? 'none' : 'block';
  document.getElementById('dashContent').style.display = hasLog ? 'block' : 'none';

  if (!hasLog) return;

  // Energy
  const pct = Math.min(Math.round((totals.cal / targets.cal) * 100), 200);
  document.getElementById('energyPct').textContent = `${Math.min(pct,100)}%`;
  document.getElementById('eConsumed').textContent = Math.round(totals.cal);
  document.getElementById('eTarget').textContent = profile ? targets.cal : '—';
  const rem = profile ? targets.cal - Math.round(totals.cal) : null;
  document.getElementById('eRemaining').textContent = rem !== null ? (rem > 0 ? rem : 0) : '—';
  document.getElementById('energySubtitle').textContent = profile
    ? `TDEE: ${targets.cal} kcal · ${profile.diet.replace('_', '-')} diet`
    : 'Complete your profile for personalised targets';

  // Ring Chart
  if (energyRingInst) energyRingInst.destroy();
  const ringCtx = document.getElementById('energyRingChart').getContext('2d');
  const ringPct = Math.min(pct, 100);
  const ringColor = ringPct >= 90 ? '#10B981' : ringPct >= 50 ? '#6366F1' : '#EF4444';
  energyRingInst = new Chart(ringCtx, {
    type: 'doughnut',
    data: {
      datasets: [{
        data: [ringPct, 100 - ringPct],
        backgroundColor: [ringColor, '#1E2D4F'],
        borderWidth: 0,
        circumference: 360,
        rotation: -90
      }]
    },
    options: {
      responsive: false,
      cutout: '78%',
      plugins: { legend: { display: false }, tooltip: { enabled: false } },
      animation: { duration: 800 }
    }
  });

  // Macro Cards
  const macros = [
    { key:'pro', label:'Protein', color:'#6366F1', unit:'g', icon:'🥩' },
    { key:'carb', label:'Carbs', color:'#06B6D4', unit:'g', icon:'🌾' },
    { key:'fat', label:'Fat', color:'#F59E0B', unit:'g', icon:'🥑' },
    { key:'fiber', label:'Fiber', color:'#10B981', unit:'g', icon:'🥦' },
  ];

  document.getElementById('macroCards').innerHTML = macros.map(m => {
    const consumed = totals[m.key];
    const target = targets[m.key];
    const mpct = Math.min(Math.round((consumed/target)*100), 100);
    return `<div class="macro-card">
      <div class="macro-header">
        <div class="macro-name">
          <span class="macro-dot" style="background:${m.color}"></span>${m.label}
        </div>
        <span style="font-size:16px">${m.icon}</span>
      </div>
      <div class="macro-val">${consumed}${m.unit}</div>
      <div class="macro-sub">Target: ${target}${m.unit} · ${mpct}%</div>
      <div class="prog-bar">
        <div class="prog-fill" style="width:${mpct}%;background:${m.color}"></div>
      </div>
    </div>`;
  }).join('');

  // Deficiencies & Excesses
  const nutriKeys = ['pro','carb','fat','fiber','iron','ca','vitc','vitd','b12'];
  const pctMap = {};
  nutriKeys.forEach(k => pctMap[k] = Math.round((totals[k] / targets[k]) * 100));

  const defKeys = nutriKeys.filter(k => pctMap[k] < 70).sort((a,b) => pctMap[a]-pctMap[b]).slice(0,4);
  const exKeys = nutriKeys.filter(k => pctMap[k] > 130).sort((a,b) => pctMap[b]-pctMap[a]).slice(0,4);

  const niceName = {
    pro:'Protein', carb:'Carbs', fat:'Fat', fiber:'Fiber',
    iron:'Iron', ca:'Calcium', vitc:'Vitamin C', vitd:'Vitamin D', b12:'Vitamin B12'
  };

  const defList = document.getElementById('deficiencyList');
  defList.innerHTML = defKeys.length ? defKeys.map(k => `
    <div class="alert-card deficit">
      <span class="alert-icon">📉</span>
      <div class="alert-info">
        <div class="alert-name">${niceName[k]}</div>
        <div class="alert-detail">${totals[k]}${NUTRIENT_UNITS[k]} of ${targets[k]}${NUTRIENT_UNITS[k]}</div>
      </div>
      <div class="alert-pct">${pctMap[k]}%</div>
    </div>`).join('')
    : `<div style="text-align:center;padding:20px;color:var(--green)">✅ No significant deficiencies</div>`;

  const exList = document.getElementById('excessList');
  exList.innerHTML = exKeys.length ? exKeys.map(k => `
    <div class="alert-card excess">
      <span class="alert-icon">📈</span>
      <div class="alert-info">
        <div class="alert-name">${niceName[k]}</div>
        <div class="alert-detail">${totals[k]}${NUTRIENT_UNITS[k]} vs ${targets[k]}${NUTRIENT_UNITS[k]}</div>
      </div>
      <div class="alert-pct">${pctMap[k]}%</div>
    </div>`).join('')
    : `<div style="text-align:center;padding:20px;color:var(--green)">✅ No significant excesses</div>`;

  // Macro Bar Chart
  if (macroBarInst) macroBarInst.destroy();
  const barCtx = document.getElementById('macroBarChart').getContext('2d');
  const barKeys = ['pro','carb','fat','fiber','iron','ca'];
  macroBarInst = new Chart(barCtx, {
    type: 'bar',
    data: {
      labels: barKeys.map(k => niceName[k]),
      datasets: [
        {
          label: 'Consumed',
          data: barKeys.map(k => totals[k]),
          backgroundColor: 'rgba(99,102,241,0.7)',
          borderRadius: 5,
          borderSkipped: false
        },
        {
          label: 'Target',
          data: barKeys.map(k => targets[k]),
          backgroundColor: 'rgba(255,255,255,0.08)',
          borderRadius: 5,
          borderSkipped: false
        }
      ]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: {
          labels: { color: '#7B8EBF', font: { size: 12 }, boxWidth: 12 }
        }
      },
      scales: {
        x: { ticks: { color: '#7B8EBF', font: { size: 11 } }, grid: { color: '#1E2D4F' } },
        y: { ticks: { color: '#7B8EBF', font: { size: 11 } }, grid: { color: '#1E2D4F' } }
      }
    }
  });
}

// ───────────────────────────────────────────
// NUTRIENT TABLE
// ───────────────────────────────────────────
function renderNutrientTable() {
  const totals = getTotals();
  const targets = getTargets();
  const keys = ['cal','pro','carb','fat','fiber','iron','ca','vitc','vitd','b12'];

  document.getElementById('nutrientTableBody').innerHTML = keys.map(k => {
    const consumed = totals[k];
    const target = targets[k];
    const pct = Math.min(Math.round((consumed/target)*100), 200);
    const barColor = pct > 120 ? '#F59E0B' : pct >= 70 ? '#10B981' : '#EF4444';
    let status, cls;
    if (pct >= 70 && pct <= 120) { status = 'Adequate'; cls = 'status-good'; }
    else if (pct < 70) { status = `Low (${pct}%)`; cls = 'status-low'; }
    else { status = `High (${pct}%)`; cls = 'status-high'; }

    return `<tr>
      <td style="font-weight:500">${NUTRIENT_LABELS[k]}</td>
      <td style="font-family:var(--font-display);font-weight:600">${consumed} ${NUTRIENT_UNITS[k]}</td>
      <td style="color:var(--text-secondary)">${target} ${NUTRIENT_UNITS[k]}</td>
      <td style="min-width:120px">
        <div class="mini-bar-wrap">
          <div class="mini-bar">
            <div class="mini-bar-fill" style="width:${Math.min(pct,100)}%;background:${barColor}"></div>
          </div>
          <span style="font-size:11px;color:var(--text-muted);min-width:32px">${Math.min(pct,999)}%</span>
        </div>
      </td>
      <td><span class="status-pill ${cls}">${status}</span></td>
    </tr>`;
  }).join('');
}

// ───────────────────────────────────────────
// RECOMMENDATIONS
// ───────────────────────────────────────────
function renderRecommendations() {
  const totals = getTotals();
  const targets = getTargets();
  const diet = profile ? profile.diet : 'vegetarian';
  const recs = [];

  const pct = k => Math.round((totals[k] / targets[k]) * 100);

  // ── Vitamin B12 ──
  if (pct('b12') < 60) {
    if (diet === 'vegetarian') {
      recs.push({ type:'add', tag:'Add Food', title:'Boost B12 with fortified foods',
        desc:'As a vegetarian, your B12 sources are limited. Try adding fortified milk (250ml/day) or paneer. Consider a supplement if gaps persist — B12 deficiency can cause fatigue and nerve damage.' });
    } else if (diet === 'eggetarian') {
      recs.push({ type:'add', tag:'Add Food', title:'Add eggs to raise B12 levels',
        desc:'Eggs are a solid B12 source for eggetarians. Aim for 2 eggs daily (boiled or poached) to help close the gap. Combined with fortified milk, this can meet most of your daily needs.' });
    } else {
      recs.push({ type:'add', tag:'Add Food', title:'Include fish or chicken for B12',
        desc:'Fish (especially tuna and salmon) is one of the richest sources of B12. Adding 100g of fish 3× a week significantly boosts your levels. Chicken is a milder source.' });
    }
  }

  // ── Vitamin D ──
  if (pct('vitd') < 60) {
    if (diet === 'non_vegetarian') {
      recs.push({ type:'add', tag:'Add Food', title:'Fish for Vitamin D',
        desc:'Fatty fish like mackerel or sardines provide excellent Vitamin D. Log 80–100g a few times per week. Egg yolks are another modest source if you prefer variety.' });
    } else {
      recs.push({ type:'add', tag:'Add Food', title:'Fortified milk + sunlight for Vitamin D',
        desc:'Dietary Vitamin D is limited in vegetarian diets. Fortified milk and curd can help. Most of your Vitamin D should come from 20–30 minutes of morning sunlight daily.' });
    }
  }

  // ── Calcium ──
  if (pct('ca') < 70) {
    recs.push({ type:'add', tag:'Add Food', title:'Increase calcium-rich foods',
      desc:`You're getting ${totals.ca}mg of calcium vs a target of ${targets.ca}mg. Add paneer (480mg/100g), curd (121mg/100g), or spinach (99mg/100g) to your meals to close this gap.` });
  }

  // ── Iron ──
  if (pct('iron') < 70) {
    recs.push({ type:'add', tag:'Add Food', title:'Iron intake is below target',
      desc:`Try adding spinach (2.7mg/100g), dal (3.3mg/100g), or oats (4.7mg/100g) to your day. Pair iron-rich foods with Vitamin C sources (potato, spinach) to improve absorption.` });
  }

  // ── Vitamin C ──
  if (pct('vitc') < 60) {
    recs.push({ type:'add', tag:'Add Food', title:'Boost Vitamin C with vegetables',
      desc:'Potato (19.7mg/100g) and spinach (28.1mg/100g) are excellent C sources. Add a side salad or sauté these with your main meal. Vitamin C also enhances iron absorption.' });
  }

  // ── Protein ──
  if (pct('pro') < 70) {
    if (diet === 'vegetarian') {
      recs.push({ type:'swap', tag:'Food Swap', title:'Swap rice-heavy meals with dal + paneer',
        desc:`Your protein intake is ${totals.pro}g vs a target of ${targets.pro}g. Replace a second serving of rice with a larger dal portion or 50g of paneer for a meaningful protein boost.` });
    } else if (diet === 'eggetarian') {
      recs.push({ type:'swap', tag:'Food Swap', title:'Add eggs to protein-light meals',
        desc:`Your protein target is ${targets.pro}g. Adding 2 boiled eggs to a low-protein meal (like poha or bread) adds ~26g protein with minimal calories.` });
    } else {
      recs.push({ type:'add', tag:'Add Food', title:'Add chicken or fish to hit protein goal',
        desc:`You're at ${totals.pro}g protein vs a target of ${targets.pro}g. 100g of grilled chicken breast adds ~27g protein. A fish portion at dinner could close the gap.` });
    }
  }

  // ── Fiber ──
  if (pct('fiber') < 70) {
    recs.push({ type:'add', tag:'Add Food', title:'More fiber needed for gut health',
      desc:'Oats (10.6g/100g), chana (7.6g/100g), and rajma (6.4g/100g) are fiber powerhouses. Swap refined bread or white rice with oats at breakfast or add a rajma curry at lunch.' });
  }

  // ── Excess carbs ──
  if (pct('carb') > 130) {
    recs.push({ type:'portion', tag:'Portion Adjust', title:'Reduce refined carbohydrate portions',
      desc:`Carbs are at ${pct('carb')}% of target. Consider reducing rice or bread portions and increasing dal, vegetables, or protein foods to keep calories in balance.` });
  }

  // ── Excess fat ──
  if (pct('fat') > 130) {
    recs.push({ type:'portion', tag:'Portion Adjust', title:'Fat intake is elevated',
      desc:`Fat is at ${pct('fat')}% of your target. Reduce cooking oil in dosa or poha, swap full-fat paneer for low-fat curd, or reduce fried items.` });
  }

  // ── Excess calories ──
  if (pct('cal') > 115) {
    recs.push({ type:'portion', tag:'Portion Adjust', title:'Caloric intake is above target',
      desc:`You've consumed ${Math.round(totals.cal)} kcal vs a target of ${targets.cal} kcal. Consider smaller portions of calorie-dense items (paneer, chicken, oats) or replace with lower-cal vegetables.` });
  }

  if (recs.length === 0 && foodLog.length > 0) {
    recs.push({ type:'add', tag:'Looking Good!', title:'Great nutritional balance today 🎉',
      desc:"You're hitting most of your targets. Keep maintaining variety in your food choices for long-term health. Consider logging dinner if you haven't yet." });
  }

  const tagClass = { add:'tag-add', swap:'tag-swap', portion:'tag-portion' };
  const tagIcon = { add:'➕', swap:'🔄', portion:'⚖️' };

  document.getElementById('recsContent').innerHTML = recs.length
    ? recs.map(r => `
      <div class="rec-card">
        <div class="rec-tag ${tagClass[r.type]}">${tagIcon[r.type]} ${r.tag}</div>
        <div class="rec-title">${r.title}</div>
        <div class="rec-desc">${r.desc}</div>
      </div>`).join('')
    : `<div class="no-data-msg">
        <div style="font-size:40px;margin-bottom:12px">💡</div>
        <div>Log some foods first to receive personalised recommendations.</div>
      </div>`;
}

// ───────────────────────────────────────────
// UTILS
// ───────────────────────────────────────────
function showToast(msg, type = 'success') {
  const existing = document.querySelector('.toast');
  if (existing) existing.remove();

  const t = document.createElement('div');
  t.className = 'toast';
  t.style.borderLeftColor = type === 'error' ? 'var(--red)' : 'var(--green)';
  t.textContent = msg;
  document.body.appendChild(t);
  setTimeout(() => t.remove(), 2700);
}

function resetAll() {
  if (!confirm('Reset all data? This cannot be undone.')) return;
  localStorage.removeItem('ns_profile');
  localStorage.removeItem('ns_log');
  profile = null;
  foodLog = [];
  location.reload();
}

<img width="1918" height="964" alt="Screenshot 2026-06-09 231051" src="https://github.com/user-attachments/assets/9822fb7b-74b0-4604-a101-a97cbdbdeb71" />
  <img width="1914" height="971" alt="Screenshot 2026-06-09 231239" src="https://github.com/user-attachments/assets/e45880c6-07d3-4347-8e62-7c5222aedf11" />
<img width="1919" height="968" alt="Screenshot 2026-06-09 231254" src="https://github.com/user-attachments/assets/e72bb1f3-34f4-4cd1-a6ad-39954751389c" />
  <img width="1912" height="965" alt="Screenshot 2026-06-09 231313" src="https://github.com/user-attachments/assets/d3c8e313-178e-4c07-b893-7b7d037bb9ad" />
  <img width="1911" height="966" alt="Screenshot 2026-06-09 231327" src="https://github.com/user-attachments/assets/f4015d1e-e49d-4061-be06-2d0c70b39f18" />



</script>
</body>
</html>
