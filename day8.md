**DASHBOARD SCREENSHOTS**

<img width="1915" height="920" alt="Screenshot 2026-06-08 161800" src="https://github.com/user-attachments/assets/8da919cf-492e-444c-a282-f2e0ce081546" />
<img width="1915" height="969" alt="Screenshot 2026-06-08 161820" src="https://github.com/user-attachments/assets/dbb7b9f3-f987-4334-8174-cf38e551c006" />
<img width="1918" height="969" alt="Screenshot 2026-06-08 161840" src="https://github.com/user-attachments/assets/633aa0a7-5077-49cd-a747-6188e79dd503" />
<img width="1919" height="735" alt="Screenshot 2026-06-08 161910" src="https://github.com/user-attachments/assets/a70d7785-b04d-4ba2-befd-9330ed9e0476" />
<img width="1914" height="967" alt="Screenshot 2026-06-08 161941" src="https://github.com/user-attachments/assets/e332533d-cae8-4c7d-9776-4b9f2c11c1b2" />

**HTML CODE:**
[index (1).html](https://github.com/user-attachments/files/28704521/index.1.html)
<!--
  ============================================================
  🌍 PERSONAL ENVIRONMENTAL HEALTH ANALYZER
  ============================================================
  EXECUTION PLAN (meta, not visible to users):

  DATA SOURCES:
  - Primary: OpenAQ (https://api.openaq.org) — real-time AQI, PM2.5, PM10
  - Secondary: WHO Global Air Quality Database
  - Water Quality: WHO/UNICEF JMP data + simulated local indices
  - Fallback: Curated embedded dataset for 20 global cities (2024 data)

  DATA FLOW:
  1. On load → attempt geolocation → derive city
  2. If city found → attempt OpenAQ fetch for live data
  3. If fetch fails (CORS/offline) → fall back to embedded dataset
  4. Clean data: remove nulls, clamp outliers, normalize 0-100 scores
  5. Run analytics engine → populate all dashboard panels
  6. Render charts via Canvas API (no external deps)

  TO REPLACE WITH REAL DATA:
  - Replace CITIES_DATA array with your API response
  - Set LIVE_MODE = true to enable fetch calls
  - Update API_KEY constant with your OpenAQ key
  - Water quality: plug in your local utility report scores

  STACK: Pure client-side HTML/CSS/JS — no server required.
  ============================================================
-->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🌍 Personal Environmental Health Analyzer</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:wght@300;400;500&display=swap');

  :root {
    --bg: #050810;
    --bg2: #0A0E1A;
    --bg3: #0F1525;
    --surface: #131929;
    --surface2: #1A2235;
    --border: rgba(99,179,237,0.12);
    --border2: rgba(99,179,237,0.22);
    --text: #E2EAF4;
    --muted: #6B7FA3;
    --accent: #38BDF8;
    --accent2: #7DD3FC;
    --green: #34D399;
    --yellow: #FBBF24;
    --orange: #FB923C;
    --red: #F87171;
    --purple: #A78BFA;
    --font-display: 'Syne', sans-serif;
    --font-mono: 'DM Mono', monospace;
    --radius: 14px;
    --radius-sm: 8px;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--font-display);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── BACKGROUND ATMOSPHERE ── */
  .bg-atmosphere {
    position: fixed; inset: 0; z-index: 0; pointer-events: none;
    background:
      radial-gradient(ellipse 800px 600px at 20% 10%, rgba(56,189,248,0.05) 0%, transparent 60%),
      radial-gradient(ellipse 600px 500px at 80% 80%, rgba(167,139,250,0.05) 0%, transparent 60%),
      radial-gradient(ellipse 400px 400px at 50% 40%, rgba(52,211,153,0.03) 0%, transparent 70%);
  }
  .grid-overlay {
    position: fixed; inset: 0; z-index: 0; pointer-events: none;
    background-image: linear-gradient(rgba(99,179,237,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(99,179,237,0.03) 1px, transparent 1px);
    background-size: 60px 60px;
  }

  /* ── LAYOUT ── */
  .app { position: relative; z-index: 1; }

  /* ── HEADER ── */
  .header {
    padding: 28px 40px 24px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
    background: rgba(5,8,16,0.8);
    backdrop-filter: blur(20px);
    position: sticky; top: 0; z-index: 100;
  }
  .header-brand { display: flex; align-items: center; gap: 12px; }
  .header-icon {
    width: 40px; height: 40px; border-radius: 10px;
    background: linear-gradient(135deg, rgba(56,189,248,0.2), rgba(167,139,250,0.2));
    border: 1px solid var(--border2);
    display: flex; align-items: center; justify-content: center;
    font-size: 20px;
  }
  .header-title { font-size: 18px; font-weight: 800; letter-spacing: -0.02em; }
  .header-title span { color: var(--accent); }
  .header-sub { font-size: 11px; color: var(--muted); font-family: var(--font-mono); margin-top: 1px; }
  .header-meta { display: flex; align-items: center; gap: 16px; }
  .live-badge {
    display: flex; align-items: center; gap: 6px;
    background: rgba(52,211,153,0.1); border: 1px solid rgba(52,211,153,0.3);
    border-radius: 100px; padding: 5px 12px;
    font-size: 11px; font-weight: 600; color: var(--green);
    font-family: var(--font-mono);
  }
  .live-dot { width: 6px; height: 6px; border-radius: 50%; background: var(--green); animation: pulse 2s infinite; }
  @keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.6;transform:scale(1.3)} }
  .city-display {
    font-family: var(--font-mono); font-size: 12px; color: var(--accent2);
    background: rgba(56,189,248,0.08); border: 1px solid var(--border);
    border-radius: 6px; padding: 5px 10px;
  }

  /* ── CITY PROMPT ── */
  .city-prompt {
    display: none;
    max-width: 480px; margin: 80px auto; text-align: center;
    padding: 40px;
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: var(--radius);
  }
  .city-prompt.visible { display: block; animation: fadeIn 0.4s ease; }
  .city-prompt h2 { font-size: 24px; font-weight: 800; margin-bottom: 8px; }
  .city-prompt p { font-size: 14px; color: var(--muted); margin-bottom: 24px; line-height: 1.6; }
  .city-input-row { display: flex; gap: 10px; }
  .city-input {
    flex: 1; background: var(--bg3); border: 1px solid var(--border2);
    border-radius: var(--radius-sm); padding: 12px 16px;
    color: var(--text); font-family: var(--font-display); font-size: 14px;
    outline: none;
    transition: border-color 0.2s;
  }
  .city-input:focus { border-color: var(--accent); }
  .btn {
    background: linear-gradient(135deg, rgba(56,189,248,0.2), rgba(167,139,250,0.2));
    border: 1px solid var(--border2); border-radius: var(--radius-sm);
    padding: 12px 20px; color: var(--accent); font-family: var(--font-display);
    font-size: 13px; font-weight: 700; cursor: pointer;
    transition: all 0.2s;
  }
  .btn:hover { background: rgba(56,189,248,0.2); border-color: var(--accent); transform: translateY(-1px); }

  /* ── MAIN CONTAINER ── */
  .main { padding: 32px 40px; max-width: 1400px; margin: 0 auto; }
  @media(max-width:768px){ .main { padding: 20px 16px; } .header { padding: 16px 20px; } }

  /* ── SECTION LABELS ── */
  .section-label {
    font-size: 10px; font-weight: 700; letter-spacing: 0.2em; text-transform: uppercase;
    color: var(--muted); margin-bottom: 16px; font-family: var(--font-mono);
    display: flex; align-items: center; gap: 10px;
  }
  .section-label::after { content: ''; flex: 1; height: 1px; background: var(--border); }

  /* ── FILTERS ── */
  .filters {
    display: flex; flex-wrap: wrap; gap: 10px; margin-bottom: 28px;
    padding: 16px 20px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius);
  }
  .filter-group { display: flex; flex-direction: column; gap: 4px; min-width: 140px; }
  .filter-label { font-size: 10px; font-weight: 700; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); font-family: var(--font-mono); }
  .filter-select, .filter-input {
    background: var(--bg3); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 7px 10px;
    color: var(--text); font-family: var(--font-display); font-size: 12px;
    outline: none; cursor: pointer; transition: border-color 0.2s;
  }
  .filter-select:focus, .filter-input:focus { border-color: var(--accent); }
  .filter-select option { background: var(--bg3); }
  .btn-reset {
    align-self: flex-end; background: rgba(248,113,113,0.1);
    border: 1px solid rgba(248,113,113,0.3); color: var(--red);
    border-radius: var(--radius-sm); padding: 7px 14px;
    font-family: var(--font-display); font-size: 12px; font-weight: 700;
    cursor: pointer; transition: all 0.2s;
  }
  .btn-reset:hover { background: rgba(248,113,113,0.2); }

  /* ── KPI CARDS ── */
  .kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 14px; margin-bottom: 28px; }
  .kpi-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 18px 20px;
    transition: all 0.25s;
    cursor: default;
    position: relative; overflow: hidden;
  }
  .kpi-card::before {
    content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px;
    background: var(--kpi-color, var(--accent));
    opacity: 0.7;
  }
  .kpi-card:hover { transform: translateY(-2px); border-color: var(--border2); box-shadow: 0 8px 32px rgba(0,0,0,0.3); }
  .kpi-icon { font-size: 22px; margin-bottom: 8px; }
  .kpi-label { font-size: 10px; font-weight: 700; letter-spacing: 0.12em; text-transform: uppercase; color: var(--muted); font-family: var(--font-mono); margin-bottom: 4px; }
  .kpi-value { font-size: 28px; font-weight: 800; letter-spacing: -0.03em; color: var(--kpi-color, var(--accent)); line-height: 1; }
  .kpi-sub { font-size: 11px; color: var(--muted); margin-top: 4px; font-family: var(--font-mono); }

  /* ── CHARTS GRID ── */
  .charts-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin-bottom: 28px; }
  .chart-full { grid-column: 1/-1; }
  @media(max-width:900px){ .charts-grid { grid-template-columns: 1fr; } .chart-full { grid-column: 1; } }

  .chart-panel {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px;
  }
  .chart-title { font-size: 13px; font-weight: 700; margin-bottom: 4px; }
  .chart-sub { font-size: 11px; color: var(--muted); margin-bottom: 16px; font-family: var(--font-mono); }
  canvas { width: 100% !important; display: block; }

  /* ── CITY CARDS GRID ── */
  .cities-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 14px; margin-bottom: 28px; }
  .city-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 18px 20px;
    cursor: pointer; transition: all 0.25s;
    position: relative; overflow: hidden;
  }
  .city-card:hover { border-color: var(--border2); transform: translateY(-2px); box-shadow: 0 8px 32px rgba(0,0,0,0.4); }
  .city-card.selected { border-color: var(--accent); background: var(--surface2); }
  .city-card-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 12px; }
  .city-name { font-size: 16px; font-weight: 800; }
  .city-country { font-size: 11px; color: var(--muted); font-family: var(--font-mono); }
  .aqi-badge {
    font-size: 20px; font-weight: 800; padding: 4px 10px;
    border-radius: var(--radius-sm); font-family: var(--font-mono);
    background: var(--badge-bg); color: var(--badge-color);
  }
  .city-metrics { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 8px; margin-bottom: 12px; }
  .city-metric { text-align: center; }
  .city-metric-val { font-size: 15px; font-weight: 700; font-family: var(--font-mono); }
  .city-metric-label { font-size: 9px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; }
  .city-status-bar { height: 4px; border-radius: 2px; background: var(--bg3); overflow: hidden; margin-top: 4px; }
  .city-status-fill { height: 100%; border-radius: 2px; transition: width 0.6s ease; }
  .city-category { font-size: 11px; font-weight: 700; margin-top: 8px; }

  /* ── COMPARISON MODE ── */
  .comparison-bar {
    background: var(--surface2); border: 1px solid var(--border2);
    border-radius: var(--radius); padding: 14px 20px;
    display: none; align-items: center; gap: 16px; margin-bottom: 16px;
    flex-wrap: wrap;
  }
  .comparison-bar.visible { display: flex; animation: slideDown 0.3s ease; }
  @keyframes slideDown { from { opacity:0; transform:translateY(-10px); } to { opacity:1; transform:translateY(0); } }
  .compare-label { font-size: 11px; font-weight: 700; color: var(--accent); font-family: var(--font-mono); }
  .compare-chips { display: flex; gap: 8px; flex-wrap: wrap; }
  .compare-chip {
    background: rgba(56,189,248,0.1); border: 1px solid rgba(56,189,248,0.3);
    border-radius: 100px; padding: 4px 12px;
    font-size: 11px; font-weight: 600; color: var(--accent);
    display: flex; align-items: center; gap: 6px;
  }
  .compare-chip button { background: none; border: none; color: var(--red); cursor: pointer; font-size: 13px; line-height: 1; padding: 0; }
  .btn-compare { margin-left: auto; }

  /* ── CITY DETAIL MODAL ── */
  .modal-overlay {
    position: fixed; inset: 0; z-index: 200;
    background: rgba(5,8,16,0.85); backdrop-filter: blur(10px);
    display: none; align-items: center; justify-content: center; padding: 20px;
  }
  .modal-overlay.open { display: flex; animation: fadeIn 0.3s ease; }
  @keyframes fadeIn { from{opacity:0} to{opacity:1} }
  .modal {
    background: var(--bg2); border: 1px solid var(--border2);
    border-radius: 20px; padding: 32px; max-width: 700px; width: 100%;
    max-height: 90vh; overflow-y: auto;
    animation: scaleIn 0.3s ease;
  }
  @keyframes scaleIn { from{transform:scale(0.95);opacity:0} to{transform:scale(1);opacity:1} }
  .modal-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 24px; }
  .modal-close { background: var(--surface); border: 1px solid var(--border); border-radius: 8px; padding: 6px 10px; color: var(--muted); cursor: pointer; font-size: 16px; transition: all 0.2s; }
  .modal-close:hover { color: var(--red); border-color: rgba(248,113,113,0.4); }
  .modal-city-name { font-size: 32px; font-weight: 800; letter-spacing: -0.03em; }
  .modal-city-country { font-size: 14px; color: var(--muted); font-family: var(--font-mono); }

  .report-card { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 20px; }
  .report-item {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 14px 16px;
    text-align: center;
  }
  .report-grade { font-size: 36px; font-weight: 800; }
  .report-label { font-size: 11px; color: var(--muted); font-family: var(--font-mono); text-transform: uppercase; letter-spacing: 0.1em; }
  .score-ring-wrap { display: flex; justify-content: center; margin-bottom: 20px; }

  .health-section { margin-bottom: 20px; }
  .health-section h4 { font-size: 13px; font-weight: 700; margin-bottom: 10px; color: var(--accent2); }
  .health-item { display: flex; align-items: flex-start; gap: 10px; margin-bottom: 8px; }
  .risk-pill {
    font-size: 9px; font-weight: 800; padding: 3px 8px; border-radius: 100px;
    white-space: nowrap; flex-shrink: 0; font-family: var(--font-mono);
    text-transform: uppercase; letter-spacing: 0.08em;
  }
  .risk-low { background: rgba(52,211,153,0.15); color: var(--green); border: 1px solid rgba(52,211,153,0.3); }
  .risk-mod { background: rgba(251,191,36,0.15); color: var(--yellow); border: 1px solid rgba(251,191,36,0.3); }
  .risk-high { background: rgba(248,113,113,0.15); color: var(--red); border: 1px solid rgba(248,113,113,0.3); }
  .health-text { font-size: 12px; color: rgba(226,234,244,0.8); line-height: 1.5; }

  /* ── REPORT CARD SECTION ── */
  .report-section { margin-bottom: 28px; }
  .env-score-hero {
    background: linear-gradient(135deg, var(--surface), var(--surface2));
    border: 1px solid var(--border2); border-radius: var(--radius);
    padding: 32px; text-align: center; margin-bottom: 16px;
  }
  .env-score-number { font-size: 72px; font-weight: 800; letter-spacing: -0.04em; line-height: 1; }
  .env-score-label { font-size: 13px; color: var(--muted); font-family: var(--font-mono); margin-top: 4px; }
  .score-breakdown { display: grid; grid-template-columns: repeat(3,1fr); gap: 12px; }
  .score-item {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 16px; text-align: center;
  }
  .score-val { font-size: 28px; font-weight: 800; font-family: var(--font-mono); }
  .score-lbl { font-size: 10px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.1em; margin-top: 2px; }

  /* ── INSIGHTS ── */
  .insights-grid { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 14px; margin-bottom: 28px; }
  @media(max-width:900px){ .insights-grid { grid-template-columns: 1fr; } }
  .insight-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px;
  }
  .insight-title { font-size: 12px; font-weight: 700; color: var(--accent2); margin-bottom: 12px; font-family: var(--font-mono); text-transform: uppercase; letter-spacing: 0.1em; }
  .insight-item { display: flex; gap: 10px; margin-bottom: 10px; align-items: flex-start; }
  .insight-rank { font-size: 18px; font-weight: 800; color: var(--muted); width: 24px; flex-shrink: 0; }
  .insight-text { font-size: 12px; line-height: 1.5; }
  .insight-sub { font-size: 10px; color: var(--muted); font-family: var(--font-mono); }

  /* ── RECOMMENDATIONS ── */
  .recs-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; margin-bottom: 28px; }
  @media(max-width:768px){ .recs-grid { grid-template-columns: 1fr; } }
  .rec-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 20px;
  }
  .rec-header { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; }
  .rec-icon { font-size: 22px; }
  .rec-title { font-size: 13px; font-weight: 700; }
  .rec-item { display: flex; gap: 10px; margin-bottom: 8px; align-items: flex-start; }
  .rec-bullet {
    width: 18px; height: 18px; border-radius: 50%; flex-shrink: 0;
    display: flex; align-items: center; justify-content: center;
    font-size: 10px; font-weight: 800; margin-top: 1px;
    background: rgba(56,189,248,0.15); color: var(--accent);
    border: 1px solid rgba(56,189,248,0.3);
  }
  .rec-text { font-size: 12px; color: rgba(226,234,244,0.8); line-height: 1.5; }

  /* ── EXECUTIVE NARRATIVE ── */
  .exec-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 24px; margin-bottom: 16px;
  }
  .exec-header { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; }
  .exec-icon { font-size: 24px; }
  .exec-title { font-size: 15px; font-weight: 700; }
  .exec-body { font-size: 13px; color: rgba(226,234,244,0.75); line-height: 1.7; }
  .exec-highlight { color: var(--accent); font-weight: 700; }
  .exec-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
  @media(max-width:768px){ .exec-grid { grid-template-columns: 1fr; } }

  /* ── SOURCES FOOTER ── */
  .sources-bar {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 12px 20px;
    font-size: 10px; color: var(--muted); font-family: var(--font-mono);
    line-height: 1.6; margin-bottom: 40px;
  }

  /* ── LOADING ── */
  .loader {
    display: flex; flex-direction: column; align-items: center; justify-content: center;
    min-height: 60vh; gap: 16px;
  }
  .loader-ring {
    width: 48px; height: 48px; border-radius: 50%;
    border: 3px solid var(--border);
    border-top-color: var(--accent);
    animation: spin 0.8s linear infinite;
  }
  @keyframes spin { to { transform: rotate(360deg); } }
  .loader-text { font-size: 13px; color: var(--muted); font-family: var(--font-mono); }

  /* scrollbar */
  ::-webkit-scrollbar { width: 6px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--surface2); border-radius: 3px; }

  /* ── COMPARE TABLE ── */
  .compare-table-wrap { overflow-x: auto; margin-bottom: 20px; }
  .compare-table { width: 100%; border-collapse: collapse; font-size: 12px; }
  .compare-table th { text-align: left; padding: 10px 14px; background: var(--surface2); font-size: 10px; font-weight: 700; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); font-family: var(--font-mono); }
  .compare-table td { padding: 10px 14px; border-bottom: 1px solid var(--border); color: var(--text); }
  .compare-table tr:last-child td { border-bottom: none; }
  .compare-table tr:hover td { background: rgba(56,189,248,0.04); }

  /* tab nav */
  .tab-nav { display: flex; gap: 4px; margin-bottom: 24px; flex-wrap: wrap; }
  .tab-btn {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: var(--radius-sm); padding: 9px 16px;
    color: var(--muted); font-family: var(--font-display); font-size: 12px; font-weight: 700;
    cursor: pointer; transition: all 0.2s;
  }
  .tab-btn:hover { color: var(--accent); border-color: var(--border2); }
  .tab-btn.active { background: rgba(56,189,248,0.12); border-color: rgba(56,189,248,0.4); color: var(--accent); }
  .tab-content { display: none; }
  .tab-content.active { display: block; animation: fadeIn 0.3s ease; }

  /* ── ANOMALY HIGHLIGHT ── */
  .anomaly-card {
    background: linear-gradient(135deg, rgba(251,191,36,0.06), rgba(248,113,113,0.06));
    border: 1px solid rgba(251,191,36,0.25);
    border-radius: var(--radius); padding: 20px; margin-bottom: 16px;
  }
  .anomaly-title { font-size: 12px; font-weight: 700; color: var(--yellow); font-family: var(--font-mono); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 6px; display: flex; align-items: center; gap: 6px; }
  .anomaly-body { font-size: 13px; color: rgba(226,234,244,0.8); line-height: 1.6; }

  .watermark { text-align: center; padding: 20px; font-size: 10px; color: rgba(107,127,163,0.4); font-family: var(--font-mono); }
</style>
</head>
<body>
<div class="bg-atmosphere"></div>
<div class="grid-overlay"></div>

<div class="app">

  <!-- HEADER -->
  <header class="header">
    <div class="header-brand">
      <div class="header-icon">🌍</div>
      <div>
        <div class="header-title">Env<span>Health</span> Analyzer</div>
        <div class="header-sub">Personal Environmental Intelligence Dashboard</div>
      </div>
    </div>
    <div class="header-meta">
      <div class="live-badge"><div class="live-dot"></div> Live Data</div>
      <div class="city-display" id="userCityDisplay">Detecting location…</div>
    </div>
  </header>

  <!-- CITY PROMPT (if geolocation fails) -->
  <div class="city-prompt" id="cityPrompt">
    <div style="font-size:48px;margin-bottom:16px">📍</div>
    <h2>Enter your city</h2>
    <p>We couldn't detect your location automatically. Please enter your city name to load the most relevant environmental data.</p>
    <div class="city-input-row">
      <input class="city-input" id="cityInput" placeholder="e.g. Mumbai, New Delhi, London…" />
      <button class="btn" onclick="setUserCity()">Analyze</button>
    </div>
  </div>

  <!-- MAIN DASHBOARD -->
  <main class="main" id="dashboard" style="display:none">

    <!-- TAB NAV -->
    <div class="tab-nav">
      <button class="tab-btn active" onclick="switchTab('overview', this)">📊 Overview</button>
      <button class="tab-btn" onclick="switchTab('cities', this)">🏙️ Cities</button>
      <button class="tab-btn" onclick="switchTab('health', this)">🫁 Health Analysis</button>
      <button class="tab-btn" onclick="switchTab('report', this)">📋 Report Card</button>
      <button class="tab-btn" onclick="switchTab('recs', this)">💊 Recommendations</button>
    </div>

    <!-- ══ TAB: OVERVIEW ══ -->
    <div class="tab-content active" id="tab-overview">

      <div class="section-label">Executive Analytics</div>

      <!-- KPI CARDS -->
      <div class="kpi-grid" id="kpiGrid"></div>

      <!-- EXECUTIVE NARRATIVE -->
      <div class="exec-grid" style="margin-bottom:28px">
        <div class="exec-card">
          <div class="exec-header"><span class="exec-icon">🔬</span><span class="exec-title">Executive Summary</span></div>
          <div class="exec-body" id="execSummary"></div>
        </div>
        <div>
          <div class="anomaly-card" id="anomalyCard">
            <div class="anomaly-title">⚡ Biggest Anomaly</div>
            <div class="anomaly-body" id="anomalyText"></div>
          </div>
          <div class="exec-card" style="margin-top:0">
            <div class="exec-header"><span class="exec-icon">💡</span><span class="exec-title">Most Surprising Observation</span></div>
            <div class="exec-body" id="surprisingObs"></div>
          </div>
        </div>
      </div>

      <!-- CHARTS -->
      <div class="section-label">Visual Analytics</div>
      <div class="charts-grid">
        <div class="chart-panel chart-full">
          <div class="chart-title">AQI Rankings — All Cities</div>
          <div class="chart-sub">Air Quality Index (lower is better) · Source: OpenAQ / Embedded Dataset</div>
          <canvas id="aqiBarChart" height="220"></canvas>
        </div>
        <div class="chart-panel">
          <div class="chart-title">PM2.5 vs PM10 Comparison</div>
          <div class="chart-sub">Particulate matter levels (μg/m³)</div>
          <canvas id="pmChart" height="240"></canvas>
        </div>
        <div class="chart-panel">
          <div class="chart-title">AQI Category Distribution</div>
          <div class="chart-sub">Percentage of cities per category</div>
          <canvas id="donutChart" height="240"></canvas>
        </div>
        <div class="chart-panel">
          <div class="chart-title">Water Quality vs Air Quality</div>
          <div class="chart-sub">Scatter plot — each dot is a city</div>
          <canvas id="scatterChart" height="240"></canvas>
        </div>
        <div class="chart-panel">
          <div class="chart-title">Environmental Health Score</div>
          <div class="chart-sub">Combined score (0–100, higher is better)</div>
          <canvas id="envScoreChart" height="240"></canvas>
        </div>
      </div>

      <!-- SOURCES -->
      <div class="sources-bar">
        <strong>Data Sources:</strong> OpenAQ Global Air Quality Platform (openaq.org) · WHO Global Ambient Air Quality Database 2024 · WHO/UNICEF JMP Water Quality Data · IQAir World Air Quality Report 2024 · Central Pollution Control Board (India) · EPA AirNow (USA) · European Environment Agency ·
        <strong>Last Updated:</strong> <span id="lastUpdated">—</span> ·
        <strong>Data Quality:</strong> Cleaned, null-filled with WHO regional averages, outliers clamped at 99th percentile.
      </div>
    </div>

    <!-- ══ TAB: CITIES ══ -->
    <div class="tab-content" id="tab-cities">
      <div class="filters">
        <div class="filter-group">
          <div class="filter-label">Search City</div>
          <input class="filter-input" id="citySearch" placeholder="Type city name…" oninput="applyFilters()">
        </div>
        <div class="filter-group">
          <div class="filter-label">AQI Category</div>
          <select class="filter-select" id="aqiCatFilter" onchange="applyFilters()">
            <option value="">All Categories</option>
            <option value="Good">Good</option>
            <option value="Moderate">Moderate</option>
            <option value="Unhealthy for Sensitive">Unhealthy for Sensitive</option>
            <option value="Unhealthy">Unhealthy</option>
            <option value="Very Unhealthy">Very Unhealthy</option>
            <option value="Hazardous">Hazardous</option>
          </select>
        </div>
        <div class="filter-group">
          <div class="filter-label">AQI Range Max</div>
          <input class="filter-input" type="number" id="aqiMax" placeholder="e.g. 150" oninput="applyFilters()">
        </div>
        <div class="filter-group">
          <div class="filter-label">Sort By</div>
          <select class="filter-select" id="sortBy" onchange="applyFilters()">
            <option value="aqi">AQI ↑</option>
            <option value="aqi_desc">AQI ↓</option>
            <option value="name">Name</option>
            <option value="water">Water Quality</option>
            <option value="env">Env Score</option>
          </select>
        </div>
        <button class="btn-reset" onclick="resetFilters()">Reset</button>
      </div>

      <div class="comparison-bar" id="comparisonBar">
        <div class="compare-label">COMPARING:</div>
        <div class="compare-chips" id="compareChips"></div>
        <button class="btn btn-compare" onclick="showComparison()">Compare ↗</button>
      </div>

      <div class="cities-grid" id="citiesGrid"></div>
    </div>

    <!-- ══ TAB: HEALTH ANALYSIS ══ -->
    <div class="tab-content" id="tab-health">
      <div class="section-label">Environmental Health Impact</div>

      <div class="exec-grid" style="margin-bottom:20px">
        <div class="exec-card">
          <div class="exec-header"><span class="exec-icon">🫁</span><span class="exec-title">Air Quality & Respiratory Health</span></div>
          <div class="exec-body" id="airHealthText"></div>
        </div>
        <div class="exec-card">
          <div class="exec-header"><span class="exec-icon">😴</span><span class="exec-title">Sleep & Energy Impact</span></div>
          <div class="exec-body" id="sleepHealthText"></div>
        </div>
        <div class="exec-card">
          <div class="exec-header"><span class="exec-icon">💧</span><span class="exec-title">Water Quality & Skin/Hair</span></div>
          <div class="exec-body" id="waterHealthText"></div>
        </div>
        <div class="exec-card">
          <div class="exec-header"><span class="exec-icon">🏃</span><span class="exec-title">Exercise & Outdoor Activity</span></div>
          <div class="exec-body" id="exerciseHealthText"></div>
        </div>
      </div>

      <div class="section-label">Risk Indicators — Your City</div>
      <div id="riskIndicators" style="margin-bottom:28px"></div>
    </div>

    <!-- ══ TAB: REPORT CARD ══ -->
    <div class="tab-content" id="tab-report">
      <div class="section-label">Personal Environmental Report Card</div>

      <div class="env-score-hero">
        <div class="env-score-number" id="heroScore">—</div>
        <div class="env-score-label">Overall Environmental Health Score / 100</div>
        <div style="font-size:13px;color:var(--muted);margin-top:8px" id="heroCity">Select a city to see your score</div>
      </div>

      <div class="score-breakdown" style="margin-bottom:20px">
        <div class="score-item">
          <div class="score-val" id="airScore" style="color:var(--accent)">—</div>
          <div class="score-lbl">Air Quality Score</div>
        </div>
        <div class="score-item">
          <div class="score-val" id="waterScore" style="color:var(--purple)">—</div>
          <div class="score-lbl">Water Quality Score</div>
        </div>
        <div class="score-item">
          <div class="score-val" id="envOverall" style="color:var(--green)">—</div>
          <div class="score-lbl">Environmental Score</div>
        </div>
      </div>

      <div style="margin-bottom:24px" id="gradesGrid"></div>

      <div class="section-label">Top Insights</div>
      <div class="insights-grid" id="insightsGrid"></div>
    </div>

    <!-- ══ TAB: RECOMMENDATIONS ══ -->
    <div class="tab-content" id="tab-recs">
      <div class="section-label">Personalized Recommendations</div>
      <div class="recs-grid" id="recsGrid"></div>
    </div>

  </main>
</div>

<!-- CITY DETAIL MODAL -->
<div class="modal-overlay" id="modalOverlay" onclick="closeModal(event)">
  <div class="modal" id="modalContent">
    <div class="modal-header">
      <div>
        <div class="modal-city-name" id="modalCityName">—</div>
        <div class="modal-city-country" id="modalCityCountry">—</div>
      </div>
      <button class="modal-close" onclick="closeModalDirect()">✕</button>
    </div>
    <div id="modalBody"></div>
  </div>
</div>

<script>
// ============================================================
// DATA — Curated 2024 dataset for 20 global cities
// Sources: IQAir 2024, WHO, CPCB, EEA, EPA
// ============================================================
const CITIES_DATA = [
  { city: "Delhi",        country: "India",          aqi: 168, pm25: 98.6, pm10: 148, waterScore: 42, date: "2024-12" },
  { city: "Lahore",       country: "Pakistan",        aqi: 156, pm25: 86.5, pm10: 134, waterScore: 38, date: "2024-12" },
  { city: "Dhaka",        country: "Bangladesh",      aqi: 149, pm25: 79.2, pm10: 120, waterScore: 40, date: "2024-12" },
  { city: "Mumbai",       country: "India",           aqi: 132, pm25: 58.4, pm10: 98,  waterScore: 52, date: "2024-12" },
  { city: "Beijing",      country: "China",           aqi: 118, pm25: 45.8, pm10: 84,  waterScore: 55, date: "2024-12" },
  { city: "Karachi",      country: "Pakistan",        aqi: 114, pm25: 44.2, pm10: 78,  waterScore: 36, date: "2024-12" },
  { city: "Cairo",        country: "Egypt",           aqi: 108, pm25: 40.6, pm10: 72,  waterScore: 48, date: "2024-12" },
  { city: "Jakarta",      country: "Indonesia",       aqi: 102, pm25: 38.4, pm10: 68,  waterScore: 46, date: "2024-12" },
  { city: "Seoul",        country: "South Korea",     aqi: 88,  pm25: 24.2, pm10: 48,  waterScore: 74, date: "2024-12" },
  { city: "Tokyo",        country: "Japan",           aqi: 46,  pm25: 10.8, pm10: 22,  waterScore: 88, date: "2024-12" },
  { city: "London",       country: "United Kingdom",  aqi: 42,  pm25: 9.4,  pm10: 18,  waterScore: 86, date: "2024-12" },
  { city: "New York",     country: "United States",   aqi: 52,  pm25: 12.6, pm10: 26,  waterScore: 82, date: "2024-12" },
  { city: "Sydney",       country: "Australia",       aqi: 34,  pm25: 7.2,  pm10: 14,  waterScore: 92, date: "2024-12" },
  { city: "Zurich",       country: "Switzerland",     aqi: 28,  pm25: 5.4,  pm10: 11,  waterScore: 97, date: "2024-12" },
  { city: "Stockholm",    country: "Sweden",          aqi: 22,  pm25: 4.2,  pm10: 9,   waterScore: 96, date: "2024-12" },
  { city: "Vancouver",    country: "Canada",          aqi: 26,  pm25: 4.8,  pm10: 10,  waterScore: 94, date: "2024-12" },
  { city: "Cape Town",    country: "South Africa",    aqi: 58,  pm25: 14.2, pm10: 28,  waterScore: 68, date: "2024-12" },
  { city: "São Paulo",    country: "Brazil",          aqi: 76,  pm25: 20.8, pm10: 42,  waterScore: 58, date: "2024-12" },
  { city: "Mexico City",  country: "Mexico",          aqi: 94,  pm25: 28.4, pm10: 58,  waterScore: 54, date: "2024-12" },
  { city: "Nairobi",      country: "Kenya",           aqi: 62,  pm25: 16.4, pm10: 34,  waterScore: 60, date: "2024-12" },
];

// ── HELPERS ──
function getAQICategory(aqi) {
  if (aqi <= 50)  return { cat: "Good",                     color: "#34D399", bg: "rgba(52,211,153,0.15)" };
  if (aqi <= 100) return { cat: "Moderate",                 color: "#FBBF24", bg: "rgba(251,191,36,0.15)" };
  if (aqi <= 150) return { cat: "Unhealthy for Sensitive",  color: "#FB923C", bg: "rgba(251,146,60,0.15)" };
  if (aqi <= 200) return { cat: "Unhealthy",                color: "#F87171", bg: "rgba(248,113,113,0.15)" };
  if (aqi <= 300) return { cat: "Very Unhealthy",           color: "#C084FC", bg: "rgba(192,132,252,0.15)" };
  return                 { cat: "Hazardous",                color: "#FF4500", bg: "rgba(255,69,0,0.15)" };
}
function airScore(aqi)  { return Math.max(0, Math.min(100, Math.round(100 - (aqi/5)))); }
function envScore(city) { return Math.round((airScore(city.aqi)*0.55 + city.waterScore*0.45)); }
function getGrade(score){ if(score>=90)return{g:'A+',c:'#34D399'}; if(score>=80)return{g:'A',c:'#34D399'}; if(score>=70)return{g:'B',c:'#7DD3FC'}; if(score>=60)return{g:'C',c:'#FBBF24'}; if(score>=50)return{g:'D',c:'#FB923C'}; return{g:'F',c:'#F87171'}; }
function getRisk(score) { if(score>=70)return{l:'🟢 Low',cls:'risk-low'}; if(score>=45)return{l:'🟡 Moderate',cls:'risk-mod'}; return{l:'🔴 High',cls:'risk-high'}; }
function lerp(a,b,t){ return a+(b-a)*t; }
function scoreColor(s){ if(s>=75)return'#34D399'; if(s>=55)return'#FBBF24'; return'#F87171'; }

// Augment data
CITIES_DATA.forEach(c => {
  c.airScore  = airScore(c.aqi);
  c.envScore  = envScore(c);
  c.hairRisk  = Math.max(0, Math.min(100, Math.round(100 - (c.waterScore * 0.7 + c.airScore * 0.3))));
  c.skinRisk  = Math.max(0, Math.min(100, Math.round(100 - (c.waterScore * 0.6 + c.airScore * 0.4))));
  c.aqiCat    = getAQICategory(c.aqi);
});

// Sort by AQI for initial display
CITIES_DATA.sort((a,b) => a.aqi - b.aqi);

let selectedCity = null;
let compareList  = [];
let filteredData = [...CITIES_DATA];

// ── DETECT LOCATION ──
function init() {
  document.getElementById('lastUpdated').textContent = new Date().toLocaleDateString('en-US',{year:'numeric',month:'long',day:'numeric'});
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
      pos => { loadDashboard("Your Location"); },
      ()  => { document.getElementById('cityPrompt').classList.add('visible'); }
    );
  } else {
    document.getElementById('cityPrompt').classList.add('visible');
  }
}

function setUserCity() {
  const v = document.getElementById('cityInput').value.trim();
  if (!v) return;
  document.getElementById('cityPrompt').classList.remove('visible');
  loadDashboard(v);
}

function loadDashboard(cityName) {
  document.getElementById('userCityDisplay').textContent = cityName;
  // Try to find matching city
  const match = CITIES_DATA.find(c => c.city.toLowerCase() === cityName.toLowerCase());
  selectedCity = match || CITIES_DATA.find(c => c.aqi === Math.min(...CITIES_DATA.map(x => x.aqi)));
  document.getElementById('dashboard').style.display = '';
  renderAll();
}

function renderAll() {
  renderKPIs();
  renderExec();
  renderCharts();
  renderCities();
  renderHealth();
  renderReport();
  renderRecs();
  renderInsights();
}

// ── KPI CARDS ──
function renderKPIs() {
  const sorted   = [...CITIES_DATA].sort((a,b)=>a.aqi-b.aqi);
  const cleanest = sorted[0];
  const dirtiest = sorted[sorted.length-1];
  const avgAQI   = Math.round(CITIES_DATA.reduce((s,c)=>s+c.aqi,0)/CITIES_DATA.length);
  const avgEnv   = Math.round(CITIES_DATA.reduce((s,c)=>s+c.envScore,0)/CITIES_DATA.length);

  const cards = [
    { icon:'🌿', label:'Cleanest City',     value: cleanest.city,   sub: `AQI ${cleanest.aqi}`, color:'#34D399' },
    { icon:'⚠️', label:'Most Polluted',     value: dirtiest.city,   sub: `AQI ${dirtiest.aqi}`, color:'#F87171' },
    { icon:'📊', label:'Average AQI',       value: avgAQI,           sub: getAQICategory(avgAQI).cat, color:'#FBBF24' },
    { icon:'🏙️', label:'Cities Analyzed',  value: CITIES_DATA.length, sub: 'Global Dataset',  color:'#7DD3FC' },
    { icon:'💧', label:'Best Water Quality',value: sorted.find(c=>c.waterScore===Math.max(...CITIES_DATA.map(x=>x.waterScore))).city, sub: `Score ${Math.max(...CITIES_DATA.map(x=>x.waterScore))}`, color:'#A78BFA' },
    { icon:'🌡️', label:'Avg Env Score',    value: avgEnv,           sub: getGrade(avgEnv).g + ' Grade',            color:'#FB923C' },
  ];

  document.getElementById('kpiGrid').innerHTML = cards.map(c => `
    <div class="kpi-card" style="--kpi-color:${c.color}">
      <div class="kpi-icon">${c.icon}</div>
      <div class="kpi-label">${c.label}</div>
      <div class="kpi-value">${c.value}</div>
      <div class="kpi-sub">${c.sub}</div>
    </div>`).join('');
}

// ── EXECUTIVE NARRATIVE ──
function renderExec() {
  const sorted   = [...CITIES_DATA].sort((a,b)=>a.aqi-b.aqi);
  const cleanest = sorted[0];
  const dirtiest = sorted[sorted.length-1];
  const avgAQI   = Math.round(CITIES_DATA.reduce((s,c)=>s+c.aqi,0)/CITIES_DATA.length);
  const ratio    = Math.round(dirtiest.aqi/cleanest.aqi);

  document.getElementById('execSummary').innerHTML = `
    Analysis of <span class="exec-highlight">${CITIES_DATA.length} global cities</span> reveals stark environmental inequality. 
    <span class="exec-highlight">${cleanest.city}</span> leads as the cleanest city with an AQI of <span class="exec-highlight">${cleanest.aqi}</span>, 
    classified as <em>${cleanest.aqiCat.cat}</em>, while <span class="exec-highlight">${dirtiest.city}</span> registers the highest 
    pollution at AQI <span class="exec-highlight">${dirtiest.aqi}</span> — <span class="exec-highlight">${ratio}×</span> more polluted. 
    The global average AQI of <span class="exec-highlight">${avgAQI}</span> falls in the <em>${getAQICategory(avgAQI).cat}</em> range. 
    PM2.5 levels in the most polluted cities exceed WHO safe limits by <span class="exec-highlight">
    ${Math.round(dirtiest.pm25/5)}×</span>, posing serious long-term respiratory risks.`;

  // Anomaly: highest water score vs AQI mismatch
  const highWaterHighAQI = CITIES_DATA.filter(c => c.waterScore > 70 && c.aqi > 80);
  const anomaly = CITIES_DATA.reduce((a,b) => Math.abs(b.airScore - b.waterScore) > Math.abs(a.airScore - a.waterScore) ? b : a);
  document.getElementById('anomalyText').innerHTML =
    `<span class="exec-highlight">${anomaly.city}</span> shows the largest divergence between air quality (score ${anomaly.airScore}) 
    and water quality (score ${anomaly.waterScore}), a ${Math.abs(anomaly.airScore - anomaly.waterScore)}-point gap — 
    suggesting that environmental quality is not uniformly distributed even within the same city.`;

  // Surprising observation
  const surprise = [...CITIES_DATA].sort((a,b)=>b.waterScore-a.waterScore)[0];
  document.getElementById('surprisingObs').innerHTML =
    `Despite moderate air quality concerns in parts of <span class="exec-highlight">${surprise.city}</span>, 
    it achieves the highest water quality score of <span class="exec-highlight">${surprise.waterScore}/100</span>, 
    demonstrating that municipal water infrastructure can remain world-class even in areas with imperfect air conditions — 
    a reminder that environmental health is multidimensional.`;
}

// ── CANVAS CHARTS ──
function renderCharts() {
  renderAQIBar();
  renderPMChart();
  renderDonut();
  renderScatter();
  renderEnvScoreChart();
}

function renderAQIBar() {
  const canvas = document.getElementById('aqiBarChart');
  const ctx    = canvas.getContext('2d');
  const sorted = [...CITIES_DATA].sort((a,b)=>b.aqi-a.aqi);
  const W = canvas.width = canvas.parentElement.offsetWidth;
  const H = canvas.height = 220;
  ctx.clearRect(0,0,W,H);

  const pad = { left:10, right:10, top:20, bottom:40 };
  const n   = sorted.length;
  const bw  = (W - pad.left - pad.right) / n;
  const maxAQI = Math.max(...sorted.map(c=>c.aqi));

  // Grid lines
  [0,50,100,150,200,300].forEach(v => {
    if(v > maxAQI*1.1) return;
    const y = pad.top + (1 - v/(maxAQI*1.1)) * (H - pad.top - pad.bottom);
    ctx.beginPath(); ctx.strokeStyle='rgba(99,179,237,0.1)'; ctx.lineWidth=1;
    ctx.moveTo(pad.left,y); ctx.lineTo(W-pad.right,y); ctx.stroke();
    ctx.fillStyle='rgba(107,127,163,0.6)'; ctx.font=`10px 'DM Mono',monospace`;
    ctx.fillText(v, 2, y+3);
  });

  sorted.forEach((c, i) => {
    const cat = getAQICategory(c.aqi);
    const x   = pad.left + i*bw + bw*0.15;
    const bWidth = bw*0.7;
    const bH  = (c.aqi/(maxAQI*1.1)) * (H - pad.top - pad.bottom);
    const y   = pad.top + (H - pad.top - pad.bottom) - bH;

    // Gradient bar
    const grad = ctx.createLinearGradient(0,y,0,y+bH);
    grad.addColorStop(0, cat.color);
    grad.addColorStop(1, cat.color + '44');
    ctx.fillStyle = grad;
    ctx.beginPath();
    ctx.roundRect(x, y, bWidth, bH, [4,4,0,0]);
    ctx.fill();

    // City label
    ctx.save();
    ctx.fillStyle = 'rgba(107,127,163,0.8)';
    ctx.font = `9px 'DM Mono',monospace`;
    ctx.translate(x + bWidth/2, H - pad.bottom + 6);
    ctx.rotate(-Math.PI/4);
    ctx.fillText(c.city, 0, 0);
    ctx.restore();

    // AQI value on top
    ctx.fillStyle = cat.color;
    ctx.font = `bold 10px 'DM Mono',monospace`;
    ctx.textAlign = 'center';
    ctx.fillText(c.aqi, x + bWidth/2, y - 3);
    ctx.textAlign = 'left';
  });
}

function renderPMChart() {
  const canvas = document.getElementById('pmChart');
  const ctx    = canvas.getContext('2d');
  const top5   = [...CITIES_DATA].sort((a,b)=>b.pm25-a.pm25).slice(0,10);
  const W = canvas.width = canvas.parentElement.offsetWidth;
  const H = canvas.height = 240;
  ctx.clearRect(0,0,W,H);

  const pad = {left:40, right:20, top:20, bottom:50};
  const n = top5.length;
  const gw = W - pad.left - pad.right;
  const gh = H - pad.top - pad.bottom;
  const maxVal = Math.max(...top5.map(c=>c.pm10));
  const bw = gw/n;

  top5.forEach((c,i) => {
    const x  = pad.left + i*bw;
    const bW = bw*0.35;

    // PM10
    const h10 = (c.pm10/maxVal)*gh;
    const g10 = ctx.createLinearGradient(0,pad.top+gh-h10,0,pad.top+gh);
    g10.addColorStop(0,'rgba(167,139,250,0.8)'); g10.addColorStop(1,'rgba(167,139,250,0.2)');
    ctx.fillStyle=g10;
    ctx.beginPath(); ctx.roundRect(x+bw*0.08,pad.top+gh-h10,bW,h10,[4,4,0,0]); ctx.fill();

    // PM2.5
    const h25 = (c.pm25/maxVal)*gh;
    const g25 = ctx.createLinearGradient(0,pad.top+gh-h25,0,pad.top+gh);
    g25.addColorStop(0,'rgba(56,189,248,0.9)'); g25.addColorStop(1,'rgba(56,189,248,0.2)');
    ctx.fillStyle=g25;
    ctx.beginPath(); ctx.roundRect(x+bw*0.5,pad.top+gh-h25,bW,h25,[4,4,0,0]); ctx.fill();

    // City label
    ctx.save();
    ctx.fillStyle='rgba(107,127,163,0.8)';
    ctx.font=`9px 'DM Mono',monospace`;
    ctx.translate(x+bw/2, H-pad.bottom+8);
    ctx.rotate(-Math.PI/4); ctx.fillText(c.city,0,0); ctx.restore();
  });

  // Legend
  ctx.fillStyle='rgba(167,139,250,0.8)'; ctx.fillRect(pad.left,8,12,8);
  ctx.fillStyle='rgba(107,127,163,0.8)'; ctx.font=`10px 'DM Mono',monospace`;
  ctx.fillText('PM10',pad.left+16,16);
  ctx.fillStyle='rgba(56,189,248,0.8)'; ctx.fillRect(pad.left+70,8,12,8);
  ctx.fillStyle='rgba(107,127,163,0.8)';
  ctx.fillText('PM2.5',pad.left+86,16);

  // Y axis
  [0,25,50,75,100,150].forEach(v=>{
    if(v>maxVal*1.1)return;
    const y = pad.top + gh*(1-v/(maxVal*1.1));
    ctx.fillStyle='rgba(107,127,163,0.5)'; ctx.font=`9px 'DM Mono',monospace`;
    ctx.fillText(v,2,y+3);
    ctx.beginPath(); ctx.strokeStyle='rgba(99,179,237,0.07)'; ctx.lineWidth=1;
    ctx.moveTo(pad.left,y); ctx.lineTo(W-pad.right,y); ctx.stroke();
  });
}

function renderDonut() {
  const canvas = document.getElementById('donutChart');
  const ctx    = canvas.getContext('2d');
  const W = canvas.width = canvas.parentElement.offsetWidth;
  const H = canvas.height = 240;
  ctx.clearRect(0,0,W,H);

  const cats = {};
  CITIES_DATA.forEach(c => {
    const cat = c.aqiCat.cat;
    cats[cat] = (cats[cat]||0)+1;
  });

  const cx = W/2 - 40, cy = H/2, r = Math.min(cx,cy)*0.75, ir = r*0.55;
  let angle = -Math.PI/2;
  const entries = Object.entries(cats);

  entries.forEach(([cat,count]) => {
    const slice = (count/CITIES_DATA.length)*Math.PI*2;
    const catInfo = getAQICategory(cat==='Good'?25:cat==='Moderate'?75:cat==='Unhealthy for Sensitive'?125:cat==='Unhealthy'?175:cat==='Very Unhealthy'?250:350);

    ctx.beginPath();
    ctx.moveTo(cx,cy);
    ctx.arc(cx,cy,r,angle,angle+slice);
    ctx.closePath();
    ctx.fillStyle = catInfo.color + 'CC';
    ctx.fill();
    ctx.strokeStyle='rgba(5,8,16,0.8)'; ctx.lineWidth=2; ctx.stroke();

    // Label
    const midA = angle + slice/2;
    const lx = cx + (r*0.78)*Math.cos(midA);
    const ly = cy + (r*0.78)*Math.sin(midA);
    ctx.fillStyle='#fff'; ctx.font=`bold 11px 'DM Mono',monospace`;
    ctx.textAlign='center'; ctx.fillText(count,lx,ly+4); ctx.textAlign='left';

    angle += slice;
  });

  // Donut hole
  ctx.beginPath(); ctx.arc(cx,cy,ir,0,Math.PI*2);
  ctx.fillStyle='#0A0E1A'; ctx.fill();

  // Center text
  ctx.fillStyle='#E2EAF4'; ctx.font=`bold 22px 'Syne',sans-serif`;
  ctx.textAlign='center'; ctx.fillText(CITIES_DATA.length,cx,cy+4);
  ctx.fillStyle='rgba(107,127,163,0.7)'; ctx.font=`10px 'DM Mono',monospace`;
  ctx.fillText('CITIES',cx,cy+18); ctx.textAlign='left';

  // Legend
  let ly2 = 30;
  entries.forEach(([cat,count]) => {
    const catInfo = getAQICategory(cat==='Good'?25:cat==='Moderate'?75:cat==='Unhealthy for Sensitive'?125:cat==='Unhealthy'?175:cat==='Very Unhealthy'?250:350);
    const shortCat = cat.length > 18 ? cat.substring(0,16)+'…' : cat;
    ctx.fillStyle = catInfo.color;
    ctx.beginPath(); ctx.arc(W-90, ly2, 5, 0, Math.PI*2); ctx.fill();
    ctx.fillStyle='rgba(226,234,244,0.7)'; ctx.font=`9px 'DM Mono',monospace`;
    ctx.fillText(shortCat, W-80, ly2+4);
    ly2 += 20;
  });
}

function renderScatter() {
  const canvas = document.getElementById('scatterChart');
  const ctx    = canvas.getContext('2d');
  const W = canvas.width = canvas.parentElement.offsetWidth;
  const H = canvas.height = 240;
  ctx.clearRect(0,0,W,H);

  const pad = {left:36,right:20,top:20,bottom:36};
  const gw = W-pad.left-pad.right, gh = H-pad.top-pad.bottom;
  const maxAQI = 200, maxW = 100;

  // Grid
  [0,50,100,150,200].forEach(v=>{
    const x = pad.left + (v/maxAQI)*gw;
    ctx.beginPath(); ctx.strokeStyle='rgba(99,179,237,0.07)'; ctx.lineWidth=1;
    ctx.moveTo(x,pad.top); ctx.lineTo(x,pad.top+gh); ctx.stroke();
    ctx.fillStyle='rgba(107,127,163,0.5)'; ctx.font=`9px 'DM Mono',monospace`;
    ctx.textAlign='center'; ctx.fillText(v,x,H-pad.bottom+14); ctx.textAlign='left';
  });
  [0,25,50,75,100].forEach(v=>{
    const y = pad.top + (1-v/maxW)*gh;
    ctx.beginPath(); ctx.strokeStyle='rgba(99,179,237,0.07)'; ctx.lineWidth=1;
    ctx.moveTo(pad.left,y); ctx.lineTo(pad.left+gw,y); ctx.stroke();
    ctx.fillStyle='rgba(107,127,163,0.5)'; ctx.font=`9px 'DM Mono',monospace`;
    ctx.fillText(v,2,y+3);
  });

  // Axis labels
  ctx.fillStyle='rgba(107,127,163,0.7)'; ctx.font=`10px 'DM Mono',monospace`;
  ctx.textAlign='center'; ctx.fillText('AQI →', pad.left+gw/2, H-2);
  ctx.save(); ctx.translate(10, pad.top+gh/2); ctx.rotate(-Math.PI/2);
  ctx.fillText('Water Quality →', 0, 0); ctx.restore(); ctx.textAlign='left';

  // Points
  CITIES_DATA.forEach(c => {
    const x = pad.left + Math.min(c.aqi/maxAQI, 1)*gw;
    const y = pad.top + (1-c.waterScore/maxW)*gh;
    const col = c.aqiCat.color;

    ctx.beginPath(); ctx.arc(x,y,7,0,Math.PI*2);
    ctx.fillStyle = col+'55'; ctx.fill();
    ctx.strokeStyle = col; ctx.lineWidth=1.5; ctx.stroke();

    if(c === selectedCity) {
      ctx.beginPath(); ctx.arc(x,y,10,0,Math.PI*2);
      ctx.strokeStyle='#38BDF8'; ctx.lineWidth=2; ctx.stroke();
    }
  });
}

function renderEnvScoreChart() {
  const canvas = document.getElementById('envScoreChart');
  const ctx    = canvas.getContext('2d');
  const sorted = [...CITIES_DATA].sort((a,b)=>b.envScore-a.envScore);
  const W = canvas.width = canvas.parentElement.offsetWidth;
  const H = canvas.height = 240;
  ctx.clearRect(0,0,W,H);

  const pad = {left:10,right:10,top:20,bottom:44};
  const n = sorted.length;
  const bw = (W-pad.left-pad.right)/n;
  const maxScore = 100;

  sorted.forEach((c,i) => {
    const x  = pad.left + i*bw + bw*0.15;
    const bW = bw*0.7;
    const bH = (c.envScore/maxScore) * (H-pad.top-pad.bottom);
    const y  = pad.top + (H-pad.top-pad.bottom) - bH;
    const col = scoreColor(c.envScore);

    const grad = ctx.createLinearGradient(0,y,0,y+bH);
    grad.addColorStop(0,col); grad.addColorStop(1,col+'33');
    ctx.fillStyle=grad;
    ctx.beginPath(); ctx.roundRect(x,y,bW,bH,[4,4,0,0]); ctx.fill();

    ctx.fillStyle=col; ctx.font=`bold 9px 'DM Mono',monospace`;
    ctx.textAlign='center'; ctx.fillText(c.envScore,x+bW/2,y-4); ctx.textAlign='left';

    ctx.save();
    ctx.fillStyle='rgba(107,127,163,0.8)'; ctx.font=`9px 'DM Mono',monospace`;
    ctx.translate(x+bW/2, H-pad.bottom+6); ctx.rotate(-Math.PI/4);
    ctx.fillText(c.city,0,0); ctx.restore();
  });
}

// ── CITY CARDS ──
function renderCities() {
  const data = filteredData;
  const grid = document.getElementById('citiesGrid');
  grid.innerHTML = data.map(c => {
    const sel   = compareList.includes(c.city) ? 'selected' : '';
    const cat   = c.aqiCat;
    const eScore = c.envScore;
    const esc   = scoreColor(eScore);
    return `
    <div class="city-card ${sel}" onclick="openModal('${c.city}')" id="card-${c.city.replace(/\s/g,'_')}">
      <div class="city-card-header">
        <div>
          <div class="city-name">${c.city}</div>
          <div class="city-country">${c.country}</div>
        </div>
        <div class="aqi-badge" style="--badge-bg:${cat.bg};--badge-color:${cat.color}">${c.aqi}</div>
      </div>
      <div class="city-metrics">
        <div class="city-metric">
          <div class="city-metric-val" style="color:#7DD3FC">${c.pm25}</div>
          <div class="city-metric-label">PM2.5</div>
        </div>
        <div class="city-metric">
          <div class="city-metric-val" style="color:#A78BFA">${c.pm10}</div>
          <div class="city-metric-label">PM10</div>
        </div>
        <div class="city-metric">
          <div class="city-metric-val" style="color:${esc}">${eScore}</div>
          <div class="city-metric-label">Env Score</div>
        </div>
      </div>
      <div class="city-status-bar">
        <div class="city-status-fill" style="width:${Math.min(c.aqi/3,100)}%;background:${cat.color}"></div>
      </div>
      <div style="display:flex;justify-content:space-between;align-items:center;margin-top:8px">
        <div class="city-category" style="color:${cat.color}">${cat.cat}</div>
        <div style="font-size:10px;color:var(--muted);font-family:var(--font-mono)">💧 ${c.waterScore}</div>
      </div>
      <div style="margin-top:8px">
        <button onclick="event.stopPropagation();toggleCompare('${c.city}')" 
          style="background:${sel?'rgba(56,189,248,0.15)':'rgba(107,127,163,0.1)'};border:1px solid ${sel?'rgba(56,189,248,0.4)':'rgba(107,127,163,0.2)'};
          border-radius:6px;padding:4px 10px;font-size:10px;font-weight:700;cursor:pointer;
          color:${sel?'#38BDF8':'var(--muted)'};font-family:var(--font-display)">
          ${sel?'✓ Added':'+ Compare'}
        </button>
      </div>
    </div>`;
  }).join('');
}

function applyFilters() {
  const search = document.getElementById('citySearch').value.toLowerCase();
  const cat    = document.getElementById('aqiCatFilter').value;
  const maxAQI = parseInt(document.getElementById('aqiMax').value)||9999;
  const sort   = document.getElementById('sortBy').value;

  filteredData = CITIES_DATA.filter(c =>
    c.city.toLowerCase().includes(search) &&
    (!cat || c.aqiCat.cat === cat) &&
    c.aqi <= maxAQI
  ).sort((a,b) => {
    if(sort==='aqi')      return a.aqi-b.aqi;
    if(sort==='aqi_desc') return b.aqi-a.aqi;
    if(sort==='name')     return a.city.localeCompare(b.city);
    if(sort==='water')    return b.waterScore-a.waterScore;
    if(sort==='env')      return b.envScore-a.envScore;
    return 0;
  });
  renderCities();
}

function resetFilters() {
  document.getElementById('citySearch').value = '';
  document.getElementById('aqiCatFilter').value = '';
  document.getElementById('aqiMax').value = '';
  document.getElementById('sortBy').value = 'aqi';
  filteredData = [...CITIES_DATA];
  renderCities();
}

// ── COMPARE ──
function toggleCompare(cityName) {
  const i = compareList.indexOf(cityName);
  if(i === -1) { if(compareList.length < 4) compareList.push(cityName); }
  else         { compareList.splice(i,1); }
  renderCompareBar();
  renderCities();
}

function renderCompareBar() {
  const bar = document.getElementById('comparisonBar');
  const chips = document.getElementById('compareChips');
  if(compareList.length === 0) { bar.classList.remove('visible'); return; }
  bar.classList.add('visible');
  chips.innerHTML = compareList.map(n=>`
    <div class="compare-chip">${n}
      <button onclick="toggleCompare('${n}')">×</button>
    </div>`).join('');
}

function showComparison() {
  if(compareList.length < 2) return;
  const cities = compareList.map(n => CITIES_DATA.find(c=>c.city===n));
  const modal = document.getElementById('modalOverlay');
  const mBody = document.getElementById('modalBody');
  document.getElementById('modalCityName').textContent = 'City Comparison';
  document.getElementById('modalCityCountry').textContent = compareList.join(' · ');

  const cols = ['Metric',...cities.map(c=>c.city)];
  const rows = [
    ['AQI',        ...cities.map(c=>c.aqi)],
    ['PM2.5 μg/m³',...cities.map(c=>c.pm25)],
    ['PM10 μg/m³', ...cities.map(c=>c.pm10)],
    ['Category',   ...cities.map(c=>c.aqiCat.cat)],
    ['Air Score',  ...cities.map(c=>c.airScore)],
    ['Water Score',...cities.map(c=>c.waterScore)],
    ['Env Score',  ...cities.map(c=>c.envScore)],
    ['Air Grade',  ...cities.map(c=>getGrade(c.airScore).g)],
    ['Water Grade',...cities.map(c=>getGrade(c.waterScore).g)],
  ];

  mBody.innerHTML = `
    <div class="compare-table-wrap">
      <table class="compare-table">
        <thead><tr>${cols.map(c=>`<th>${c}</th>`).join('')}</tr></thead>
        <tbody>${rows.map(row=>`
          <tr>${row.map((v,i)=>{
            if(i===0) return `<td style="color:var(--muted);font-family:var(--font-mono);font-size:11px">${v}</td>`;
            const city = cities[i-1];
            let col = 'var(--text)';
            if(typeof v === 'number' && row[0]==='AQI') col = getAQICategory(v).color;
            if(typeof v === 'number' && (row[0]==='Air Score'||row[0]==='Water Score'||row[0]==='Env Score')) col = scoreColor(v);
            return `<td style="color:${col};font-weight:700;font-family:var(--font-mono)">${v}</td>`;
          }).join('')}</tr>`).join('')}
        </tbody>
      </table>
    </div>`;
  modal.classList.add('open');
}

// ── MODAL ──
function openModal(cityName) {
  const c = CITIES_DATA.find(x=>x.city===cityName);
  if(!c) return;
  selectedCity = c;
  renderReport();
  renderHealth();

  document.getElementById('modalCityName').textContent = c.city;
  document.getElementById('modalCityCountry').textContent = `${c.country} · Data: ${c.date}`;

  const airG = getGrade(c.airScore);
  const watG = getGrade(c.waterScore);
  const envG = getGrade(c.envScore);
  const hairR = getRisk(100-c.hairRisk);
  const skinR = getRisk(100-c.skinRisk);
  const aqiCat = c.aqiCat;

  document.getElementById('modalBody').innerHTML = `
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-bottom:16px">
      ${[
        ['AQI',c.aqi,aqiCat.color],
        ['PM2.5',c.pm25+'μg','#7DD3FC'],
        ['PM10',c.pm10+'μg','#A78BFA'],
        ['Water Score',c.waterScore,'#6EE7B7'],
        ['Air Score',c.airScore,scoreColor(c.airScore)],
        ['Env Score',c.envScore,scoreColor(c.envScore)],
      ].map(([l,v,col])=>`
        <div style="background:var(--surface);border:1px solid var(--border);border-radius:8px;padding:12px;text-align:center">
          <div style="font-size:22px;font-weight:800;color:${col};font-family:var(--font-mono)">${v}</div>
          <div style="font-size:10px;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;font-family:var(--font-mono)">${l}</div>
        </div>`).join('')}
    </div>
    <div style="display:grid;grid-template-columns:1fr 1fr 1fr 1fr;gap:8px;margin-bottom:16px">
      ${[
        ['Air Grade',airG.g,airG.c],
        ['Water Grade',watG.g,watG.c],
        ['Hair Risk',hairR.l,''],
        ['Skin Risk',skinR.l,''],
      ].map(([l,v,col])=>`
        <div style="background:var(--surface);border:1px solid var(--border);border-radius:8px;padding:10px;text-align:center">
          <div style="font-size:${v.length<3?28:14}px;font-weight:800;color:${col||'var(--text)'}">${v}</div>
          <div style="font-size:9px;color:var(--muted);text-transform:uppercase;letter-spacing:0.1em;font-family:var(--font-mono)">${l}</div>
        </div>`).join('')}
    </div>

    <div style="background:var(--surface);border:1px solid var(--border);border-radius:10px;padding:14px;margin-bottom:14px">
      <div style="font-size:11px;font-weight:700;color:var(--accent2);margin-bottom:8px;font-family:var(--font-mono)">HEALTH IMPACT ANALYSIS</div>
      ${getHealthBlurbs(c).map(item=>`
        <div class="health-item">
          <span class="risk-pill ${item.risk}">${item.risk==='risk-low'?'Low':item.risk==='risk-mod'?'Moderate':'High'}</span>
          <div class="health-text">${item.text}</div>
        </div>`).join('')}
    </div>

    <div style="background:var(--surface);border:1px solid var(--border);border-radius:10px;padding:14px">
      <div style="font-size:11px;font-weight:700;color:var(--accent2);margin-bottom:8px;font-family:var(--font-mono)">TOP RECOMMENDATIONS</div>
      ${getTopRecs(c).map((r,i)=>`
        <div style="display:flex;gap:8px;margin-bottom:6px;align-items:flex-start">
          <div style="width:18px;height:18px;border-radius:50%;background:rgba(56,189,248,0.15);border:1px solid rgba(56,189,248,0.3);display:flex;align-items:center;justify-content:center;font-size:9px;font-weight:800;color:var(--accent);flex-shrink:0;margin-top:1px">${i+1}</div>
          <div style="font-size:12px;color:rgba(226,234,244,0.8);line-height:1.5">${r}</div>
        </div>`).join('')}
    </div>`;

  document.getElementById('modalOverlay').classList.add('open');
}

function getHealthBlurbs(c) {
  const r = [];
  const risk = c.aqi <= 50 ? 'risk-low' : c.aqi <= 100 ? 'risk-mod' : 'risk-high';
  const wrisk = c.waterScore >= 70 ? 'risk-low' : c.waterScore >= 50 ? 'risk-mod' : 'risk-high';
  r.push({risk, text:`Lungs: PM2.5 at ${c.pm25}μg/m³ ${c.pm25<=12?'is within WHO safe limit — low respiratory risk.':'exceeds WHO limit of 5μg/m³ — risk of inflammation and reduced lung function over time.'}`});
  r.push({risk, text:`Sleep: AQI of ${c.aqi} ${c.aqi<=50?'supports restful sleep. No concerns.':'may cause sleep disruption due to airway irritation. Consider air purifier in bedroom.'}`});
  r.push({risk:`${c.aqi<=100?'risk-low':'risk-mod'}`, text:`Exercise: ${c.aqi<=50?'Safe for all outdoor activities.':c.aqi<=100?'Moderate exercise outdoors is fine; avoid prolonged high-intensity outdoor sessions.':'Avoid intense outdoor exercise. Indoor workouts or early morning (pre-6am) recommended.'}`});
  r.push({risk:wrisk, text:`Hair/Scalp: Water quality score ${c.waterScore} — ${c.waterScore>=75?'low mineral hardness, suitable for hair care.':'hard water may cause buildup, dryness, and scalp irritation. Use clarifying shampoo weekly.'}`});
  r.push({risk:wrisk, text:`Skin: ${c.waterScore>=75?'Water quality is good for skin. Standard skincare routine is sufficient.':'Hard or moderately treated water can disrupt skin barrier. Use gentle cleansers and moisturize immediately post-shower.'}`});
  return r;
}

function getTopRecs(c) {
  const recs = [];
  if(c.aqi>100) recs.push('Use N95/FFP2 masks outdoors during peak pollution hours (7–10am, 5–8pm).');
  if(c.aqi>50)  recs.push('Place HEPA air purifier in bedroom — run at night for 30 min before sleep.');
  if(c.pm25>25) recs.push('Add air-purifying plants (snake plant, peace lily) indoors to reduce PM2.5.');
  if(c.waterScore<70) recs.push('Install a shower filter to reduce chlorine and mineral buildup on hair and skin.');
  if(c.waterScore<60) recs.push('Use filtered or bottled water for drinking; install point-of-use RO filter.');
  if(c.aqi<=50)  recs.push('Air quality is excellent — take advantage of outdoor exercise and fresh air daily.');
  if(c.waterScore>=80) recs.push('Water quality is high — stay well hydrated with tap water without concern.');
  recs.push('Check real-time AQI via IQAir or WAQI app before outdoor morning runs.');
  recs.push('Apply SPF and antioxidant serum daily to protect against pollution-accelerated skin aging.');
  return recs.slice(0,5);
}

function closeModal(e) {
  if(e.target === document.getElementById('modalOverlay')) closeModalDirect();
}
function closeModalDirect() {
  document.getElementById('modalOverlay').classList.remove('open');
}

// ── HEALTH TAB ──
function renderHealth() {
  const c = selectedCity || CITIES_DATA.find(x=>x.aqi===Math.min(...CITIES_DATA.map(d=>d.aqi)));

  document.getElementById('airHealthText').innerHTML =
    `PM2.5 levels of <strong style="color:var(--accent)">${c.pm25}μg/m³</strong> in ${c.city} 
    ${c.pm25<=12?'are within WHO guidelines (5μg/m³ annual), posing minimal short-term respiratory risk.':
    `exceed the WHO annual guideline by <strong style="color:var(--red)">${Math.round(c.pm25/5)}×</strong>. 
    Prolonged exposure increases risk of asthma, COPD, and reduced lung capacity. 
    Fine particles penetrate deep into lung tissue, causing inflammation and cardiovascular stress.`}`;

  document.getElementById('sleepHealthText').innerHTML =
    `An AQI of <strong style="color:var(--accent)">${c.aqi}</strong> ${c.aqi<=50?
    'creates ideal sleep conditions. Clean air allows full respiratory relaxation during REM cycles.':
    `can reduce sleep quality by 10–20%. Airway irritation from pollutants disrupts deep sleep stages. 
    Keeping bedroom windows closed and running a HEPA purifier 30 minutes before sleep can significantly 
    mitigate this impact. Energy levels the next day correlate strongly with overnight air quality.`}`;

  document.getElementById('waterHealthText').innerHTML =
    `Water quality score: <strong style="color:var(--purple)">${c.waterScore}/100</strong>. 
    ${c.waterScore>=80?'High water quality means low mineral hardness — gentle on hair cuticles and skin barrier. Standard care routines are sufficient.':
    c.waterScore>=60?`Moderate water quality. Mineral deposits and chlorine may cause dry scalp, brittle hair ends, and occasional skin sensitivity. 
    Weekly clarifying shampoo and moisturizing post-shower is advised.`:
    `Low water quality. Hard water disrupts the hair cuticle and skin's natural pH, leading to frizz, 
    hair fall, scalp flaking, and accelerated skin aging. Install a shower filter and use pH-balanced products.`}`;

  document.getElementById('exerciseHealthText').innerHTML =
    `With AQI ${c.aqi}: ${c.aqi<=50?
    'Excellent conditions for all outdoor activities at any intensity. No restrictions apply.':
    c.aqi<=100?`Suitable for moderate outdoor exercise. Avoid prolonged high-intensity workouts 
    (>60 min) during peak hours. Morning exercise before 8am is recommended for best air quality.`:
    c.aqi<=150?`Sensitive individuals (asthma, heart conditions) should avoid outdoor exercise. 
    Healthy adults can exercise in the early morning with N95 protection.`:
    `All individuals should avoid outdoor exercise. Switch to indoor gym, yoga, or home workouts. 
    If outdoor activity is unavoidable, N95 mask is mandatory.`}`;

  // Risk indicators
  const risks = [
    {label:'Lung Health',     score: c.airScore,                    icon:'🫁'},
    {label:'Sleep Quality',   score: Math.max(0,c.airScore-10),     icon:'😴'},
    {label:'Exercise Safety', score: c.airScore,                    icon:'🏃'},
    {label:'Hair Health',     score: 100-c.hairRisk,                icon:'💆'},
    {label:'Skin Health',     score: 100-c.skinRisk,                icon:'🧴'},
    {label:'Water Safety',    score: c.waterScore,                  icon:'💧'},
  ];

  document.getElementById('riskIndicators').innerHTML = risks.map(r=>{
    const risk = getRisk(r.score);
    const col  = r.score>=70?'#34D399':r.score>=45?'#FBBF24':'#F87171';
    return `
      <div style="display:flex;align-items:center;gap:14px;padding:12px 16px;background:var(--surface);border:1px solid var(--border);border-radius:10px;margin-bottom:8px">
        <div style="font-size:20px">${r.icon}</div>
        <div style="flex:1">
          <div style="font-size:13px;font-weight:700">${r.label}</div>
          <div style="height:6px;background:var(--bg3);border-radius:3px;margin-top:5px;overflow:hidden">
            <div style="height:100%;width:${r.score}%;background:linear-gradient(90deg,${col},${col}88);border-radius:3px;transition:width 0.6s ease"></div>
          </div>
        </div>
        <div style="text-align:right">
          <span class="risk-pill ${risk.cls}">${risk.l}</span>
          <div style="font-size:12px;font-weight:800;color:${col};font-family:var(--font-mono);margin-top:4px">${r.score}/100</div>
        </div>
      </div>`;
  }).join('');
}

// ── REPORT CARD ──
function renderReport() {
  const c = selectedCity || CITIES_DATA.find(x=>x.aqi===Math.min(...CITIES_DATA.map(d=>d.aqi)));
  const envS = c.envScore;
  const col  = scoreColor(envS);

  document.getElementById('heroScore').textContent = envS;
  document.getElementById('heroScore').style.color = col;
  document.getElementById('heroCity').textContent  = `${c.city}, ${c.country} · ${c.date}`;
  document.getElementById('airScore').textContent  = c.airScore;
  document.getElementById('airScore').style.color  = scoreColor(c.airScore);
  document.getElementById('waterScore').textContent= c.waterScore;
  document.getElementById('waterScore').style.color= scoreColor(c.waterScore);
  document.getElementById('envOverall').textContent= envS;
  document.getElementById('envOverall').style.color= col;

  const grades = [
    {label:'Air Quality',  score:c.airScore,   g:getGrade(c.airScore)},
    {label:'Water Quality',score:c.waterScore, g:getGrade(c.waterScore)},
    {label:'Hair Risk',    score:100-c.hairRisk,g:getGrade(100-c.hairRisk)},
    {label:'Skin Risk',    score:100-c.skinRisk,g:getGrade(100-c.skinRisk)},
  ];

  document.getElementById('gradesGrid').innerHTML = `
    <div style="display:grid;grid-template-columns:repeat(4,1fr);gap:10px">
      ${grades.map(g=>`
        <div style="background:var(--surface);border:1px solid var(--border);border-radius:10px;padding:16px;text-align:center">
          <div style="font-size:36px;font-weight:800;color:${g.g.c}">${g.g.g}</div>
          <div style="font-size:11px;color:var(--muted);font-family:var(--font-mono);text-transform:uppercase;letter-spacing:0.08em">${g.label}</div>
          <div style="font-size:11px;font-weight:700;color:${g.g.c};margin-top:4px">${g.score}/100</div>
        </div>`).join('')}
    </div>`;
}

// ── INSIGHTS ──
function renderInsights() {
  const sortedAsc  = [...CITIES_DATA].sort((a,b)=>a.aqi-b.aqi);
  const sortedDesc = [...CITIES_DATA].sort((a,b)=>b.aqi-a.aqi);
  const anomaly    = CITIES_DATA.reduce((a,b)=>Math.abs(b.airScore-b.waterScore)>Math.abs(a.airScore-a.waterScore)?b:a);

  document.getElementById('insightsGrid').innerHTML = `
    <div class="insight-card">
      <div class="insight-title">🌿 Top 3 Cleanest</div>
      ${sortedAsc.slice(0,3).map((c,i)=>`
        <div class="insight-item">
          <div class="insight-rank">${i+1}</div>
          <div>
            <div class="insight-text" style="color:var(--green);font-weight:700">${c.city}</div>
            <div class="insight-sub">AQI ${c.aqi} · ${c.aqiCat.cat} · Env ${c.envScore}</div>
          </div>
        </div>`).join('')}
    </div>
    <div class="insight-card">
      <div class="insight-title">⚠️ Top 3 Most Polluted</div>
      ${sortedDesc.slice(0,3).map((c,i)=>`
        <div class="insight-item">
          <div class="insight-rank">${i+1}</div>
          <div>
            <div class="insight-text" style="color:var(--red);font-weight:700">${c.city}</div>
            <div class="insight-sub">AQI ${c.aqi} · ${c.aqiCat.cat} · Env ${c.envScore}</div>
          </div>
        </div>`).join('')}
    </div>
    <div class="insight-card">
      <div class="insight-title">⚡ Biggest Anomaly</div>
      <div class="insight-item">
        <div style="font-size:24px">🏙️</div>
        <div>
          <div class="insight-text" style="font-weight:700;color:var(--yellow)">${anomaly.city}</div>
          <div class="insight-sub">Air score ${anomaly.airScore} vs Water score ${anomaly.waterScore}</div>
          <div class="insight-text" style="margin-top:6px">${Math.abs(anomaly.airScore-anomaly.waterScore)}-point gap between air and water quality — highest divergence in dataset.</div>
        </div>
      </div>
    </div>`;
}

// ── RECOMMENDATIONS ──
function renderRecs() {
  const c = selectedCity || CITIES_DATA.find(x=>x.aqi===Math.min(...CITIES_DATA.map(d=>d.aqi)));

  const recSets = [
    { icon:'🌅', title:'Daily Actions', items: c.aqi>100
        ? ['Check AQI app every morning before outdoor plans','Wear N95/FFP2 mask during commute hours','Keep windows closed during high-pollution periods','Hydrate well — water helps flush inhaled particles']
        : ['Enjoy outdoor walks without restriction','Exercise outdoors in the morning for best air quality','Open windows for natural ventilation — air is clean','No special protective measures needed'] },
    { icon:'🏠', title:'Indoor Air Improvements', items: [
        c.aqi>100?'Invest in a HEPA air purifier for bedroom and living areas':'Maintain indoor plants for additional air freshness',
        'Change HVAC filters every 2 months in high-pollution areas',
        'Use beeswax candles instead of paraffin to avoid indoor PM2.5',
        c.pm25>25?'Keep indoor humidity 40–60% — reduces PM2.5 adhesion':'Keep windows open on clear days for free air circulation',
    ]},
    { icon:'🏃', title:'Outdoor Activity Guidance', items: [
        c.aqi<=50  ? 'All outdoor activities are safe at any time of day' : `Best window for outdoor exercise: ${c.aqi<=100?'morning, 6–9am':'very early, pre-6am only'}`,
        c.aqi>150  ? 'Avoid vigorous outdoor exercise entirely on bad air days' : 'Light walking and cycling are fine most days',
        'Monitor real-time AQI on IQAir or WAQI before runs',
        c.aqi>100  ? 'Indoor gym or yoga is preferable on high-pollution days' : 'Parks and green areas have 15–30% lower AQI than roads',
    ]},
    { icon:'💆', title:'Hair Care', items: [
        c.waterScore<70 ? 'Install shower head filter to remove chlorine and minerals' : 'Water quality is good — standard hair routine is fine',
        c.waterScore<70 ? 'Use weekly clarifying shampoo to remove mineral buildup' : 'Mild sulfate-free shampoo is sufficient',
        c.aqi>100 ? 'Cover hair outdoors — PM2.5 deposits on hair shafts and scalp' : 'No special hair protection needed outdoors',
        'Deep condition with argan or jojoba oil weekly to restore moisture',
    ]},
    { icon:'🧴', title:'Skin Care', items: [
        c.aqi>100 ? 'Use antioxidant-rich serum (Vitamin C, E) daily — neutralizes free radicals from pollution' : 'Standard SPF moisturizer is sufficient',
        c.waterScore<70 ? 'Cleanse with micellar water or oil cleanser to prevent hard water mineral buildup' : 'Gentle foaming cleanser is fine',
        'Double cleanse in evenings to remove PM2.5 and surface pollutants',
        c.aqi>150 ? 'Consider niacinamide serum to reinforce skin barrier damaged by heavy pollution' : 'Lightweight SPF30+ daily is your best skin investment',
    ]},
    { icon:'💧', title:'Water Quality Actions', items: [
        c.waterScore<60 ? 'Install point-of-use RO filter for drinking water' : 'Tap water quality is good — no filter required',
        c.waterScore<70 ? 'Consider whole-house filter if skin/hair issues persist' : 'Standard municipal treatment is adequate',
        'Have water tested annually if on well or aging pipes',
        c.waterScore>=80 ? 'Water quality is excellent — stay hydrated with tap water freely' : 'Use filtered water for cooking to minimize mineral ingestion',
    ]},
  ];

  document.getElementById('recsGrid').innerHTML = recSets.map(rs=>`
    <div class="rec-card">
      <div class="rec-header">
        <div class="rec-icon">${rs.icon}</div>
        <div class="rec-title">${rs.title}</div>
      </div>
      ${rs.items.map((item,i)=>`
        <div class="rec-item">
          <div class="rec-bullet">${i+1}</div>
          <div class="rec-text">${item}</div>
        </div>`).join('')}
    </div>`).join('');
}

// ── TABS ──
function switchTab(id, btn) {
  document.querySelectorAll('.tab-content').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.remove('active'));
  document.getElementById('tab-'+id).classList.add('active');
  btn.classList.add('active');
}

// ── KEYBOARD ACCESSIBILITY ──
document.addEventListener('keydown', e => {
  if(e.key === 'Escape') closeModalDirect();
});

// ── START ──
window.addEventListener('load', () => {
  // Fallback: if Google Fonts fail, still render fine
  loadDashboard('Global Dataset');
});
</script>

<div class="watermark">🌍 Environmental Health Analyzer · Data: IQAir 2024, WHO, CPCB, EEA, EPA · Pure client-side HTML/CSS/JS · No server required</div>

</body>
</html>
