<img width="1362" height="967" alt="Screenshot 2026-06-21 225519" src="https://github.com/user-attachments/assets/a649c63f-3367-47ba-a6b0-0ef42e02ebd4" />
<img width="1377" height="760" alt="Screenshot 2026-06-21 225553" src="https://github.com/user-attachments/assets/b868920e-258b-4691-ae77-0a2ebea36977" />
<img width="1350" height="570" alt="Screenshot 2026-06-21 225652" src="https://github.com/user-attachments/assets/a28f0835-46ce-4322-95ac-a1e444017bd2" />
<img width="1367" height="962" alt="Screenshot 2026-06-21 225709" src="https://github.com/user-attachments/assets/d6803fcb-4328-4231-802c-2c2ac538f732" />
<img width="1351" height="936" alt="Screenshot 2026-06-21 225723" src="https://github.com/user-attachments/assets/6a1969f1-a2b1-4d01-95ad-2a8cc13c3fc5" />
<img width="1353" height="897" alt="Screenshot 2026-06-21 225742" src="https://github.com/user-attachments/assets/4d8cbfb4-d10d-413b-8f19-3230dfc37cd6" />
<img width="1371" height="852" alt="Screenshot 2026-06-21 225800" src="https://github.com/user-attachments/assets/53296c62-2235-4975-a464-38831d0707d8" />


[digital_footprint_dashboard.html](https://github.com/user-attachments/files/29181493/digital_footprint_dashboard.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Digital Footprint & Privacy Dashboard</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@3.10.0/dist/tabler-icons.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
  :root {
    --bg: #0a0b0e;
    --bg-surface: #111318;
    --bg-card: #161920;
    --bg-card-hover: #1c2028;
    --border: rgba(255,255,255,0.06);
    --border-md: rgba(255,255,255,0.10);
    --text-primary: #f0f2f5;
    --text-secondary: #8b92a0;
    --text-muted: #4a5060;
    --accent: #e85d4a;
    --accent-dim: rgba(232,93,74,0.12);
    --accent-border: rgba(232,93,74,0.3);
    --amber: #f5a623;
    --amber-dim: rgba(245,166,35,0.12);
    --green: #3ecf8e;
    --green-dim: rgba(62,207,142,0.1);
    --blue: #4f8ef7;
    --blue-dim: rgba(79,142,247,0.12);
    --purple: #8b5cf6;
    --purple-dim: rgba(139,92,246,0.12);
    --radius-sm: 6px;
    --radius-md: 10px;
    --radius-lg: 14px;
    --radius-xl: 18px;
    --mono: 'JetBrains Mono', monospace;
    --sans: 'Inter', system-ui, sans-serif;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    font-family: var(--sans);
    background: var(--bg);
    color: var(--text-primary);
    min-height: 100vh;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  /* ── Layout ── */
  .shell {
    max-width: 1100px;
    margin: 0 auto;
    padding: 0 24px 80px;
  }

  /* ── Header ── */
  .header {
    padding: 36px 0 32px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 32px;
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 16px;
  }
  .header-left {}
  .header-eyebrow {
    font-family: var(--mono);
    font-size: 11px;
    font-weight: 500;
    letter-spacing: .12em;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 10px;
  }
  .header-title {
    font-size: 26px;
    font-weight: 600;
    color: var(--text-primary);
    letter-spacing: -.02em;
    line-height: 1.2;
    margin-bottom: 6px;
  }
  .header-sub {
    font-size: 13px;
    color: var(--text-secondary);
  }
  .header-right {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 8px;
  }
  .report-date {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-muted);
  }
  .tag {
    display: inline-flex;
    align-items: center;
    gap: 4px;
    font-size: 10px;
    font-weight: 600;
    letter-spacing: .06em;
    text-transform: uppercase;
    padding: 3px 7px;
    border-radius: 4px;
  }
  .tag-fact { background: var(--blue-dim); color: var(--blue); border: 1px solid rgba(79,142,247,.2); }
  .tag-est  { background: var(--amber-dim); color: var(--amber); border: 1px solid rgba(245,166,35,.2); }

  /* ── Section labels ── */
  .section-eyebrow {
    font-family: var(--mono);
    font-size: 10px;
    font-weight: 500;
    letter-spacing: .1em;
    text-transform: uppercase;
    color: var(--text-muted);
    margin-bottom: 14px;
    padding-top: 32px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .section-eyebrow::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  /* ── Cards ── */
  .card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 20px;
    transition: border-color .2s;
  }
  .card:hover { border-color: var(--border-md); }
  .card-title {
    font-size: 12px;
    font-weight: 500;
    color: var(--text-secondary);
    margin-bottom: 14px;
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .card-title i { font-size: 15px; }

  /* ── Stat grid ── */
  .stat-grid {
    display: grid;
    grid-template-columns: repeat(4, minmax(0,1fr));
    gap: 12px;
    margin-bottom: 14px;
  }
  .stat-card {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-md);
    padding: 16px;
    position: relative;
    overflow: hidden;
  }
  .stat-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }
  .stat-card.c-red::before   { background: var(--accent); }
  .stat-card.c-amber::before { background: var(--amber); }
  .stat-card.c-blue::before  { background: var(--blue); }
  .stat-card.c-green::before { background: var(--green); }
  .stat-label {
    font-size: 11px;
    color: var(--text-muted);
    margin-bottom: 6px;
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .06em;
  }
  .stat-value {
    font-size: 26px;
    font-weight: 600;
    color: var(--text-primary);
    letter-spacing: -.02em;
    line-height: 1;
    margin-bottom: 4px;
  }
  .stat-sub {
    font-size: 11px;
    color: var(--text-secondary);
  }

  /* ── Score dual cards ── */
  .score-grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0,1fr));
    gap: 12px;
    margin-bottom: 14px;
  }
  .score-card {
    background: var(--bg-card);
    border: 1px solid var(--border);
    border-radius: var(--radius-lg);
    padding: 24px;
    display: flex;
    align-items: center;
    gap: 20px;
  }
  .score-ring { flex-shrink: 0; }
  .score-info {}
  .score-label {
    font-size: 11px;
    color: var(--text-muted);
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .08em;
    margin-bottom: 6px;
  }
  .score-status {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-size: 13px;
    font-weight: 500;
    padding: 4px 10px;
    border-radius: var(--radius-sm);
    margin-bottom: 8px;
  }
  .score-status.orange { background: rgba(232,93,74,.15); color: #f08070; border: 1px solid rgba(232,93,74,.2); }
  .score-status.amber  { background: var(--amber-dim);  color: var(--amber);  border: 1px solid rgba(245,166,35,.2); }
  .score-desc {
    font-size: 12px;
    color: var(--text-secondary);
    line-height: 1.55;
  }

  /* ── Grids ── */
  .grid-2 { display: grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap: 12px; margin-bottom: 14px; }
  .grid-3 { display: grid; grid-template-columns: repeat(3, minmax(0,1fr)); gap: 12px; margin-bottom: 14px; }

  /* ── Heatmap ── */
  .heat-grid {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 8px;
    margin-bottom: 12px;
  }
  .heat-cell {
    border-radius: var(--radius-md);
    padding: 14px 8px;
    text-align: center;
    border: 1px solid transparent;
  }
  .heat-cell.level-crit   { background: rgba(232,93,74,.16); border-color: rgba(232,93,74,.25); }
  .heat-cell.level-high   { background: rgba(245,166,35,.14); border-color: rgba(245,166,35,.22); }
  .heat-cell.level-med    { background: rgba(79,142,247,.12); border-color: rgba(79,142,247,.2); }
  .heat-cell.level-low    { background: rgba(62,207,142,.1);  border-color: rgba(62,207,142,.18); }
  .heat-cat {
    font-size: 10px;
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .06em;
    margin-bottom: 6px;
  }
  .heat-cell.level-crit .heat-cat  { color: #f08070; }
  .heat-cell.level-high .heat-cat  { color: var(--amber); }
  .heat-cell.level-med  .heat-cat  { color: var(--blue); }
  .heat-cell.level-low  .heat-cat  { color: var(--green); }
  .heat-score {
    font-size: 20px;
    font-weight: 600;
    letter-spacing: -.02em;
  }
  .heat-cell.level-crit .heat-score { color: var(--accent); }
  .heat-cell.level-high .heat-score { color: var(--amber); }
  .heat-cell.level-med  .heat-score { color: var(--blue); }
  .heat-cell.level-low  .heat-score { color: var(--green); }
  .heat-legend {
    display: flex;
    gap: 16px;
    flex-wrap: wrap;
  }
  .heat-legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
    font-size: 11px;
    color: var(--text-muted);
  }
  .heat-legend-dot {
    width: 8px; height: 8px;
    border-radius: 2px;
  }

  /* ── Company ranking ── */
  .company-row {
    display: flex;
    align-items: center;
    gap: 14px;
    padding: 12px 0;
    border-bottom: 1px solid var(--border);
  }
  .company-row:last-child { border-bottom: none; }
  .company-rank {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-muted);
    width: 18px;
    flex-shrink: 0;
  }
  .company-logo {
    width: 34px; height: 34px;
    border-radius: var(--radius-sm);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    font-weight: 700;
    flex-shrink: 0;
  }
  .company-info { flex: 1; min-width: 0; }
  .company-name {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-primary);
    margin-bottom: 2px;
  }
  .company-apps {
    font-size: 11px;
    color: var(--text-muted);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .company-exposure {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 5px;
    flex-shrink: 0;
    min-width: 90px;
  }
  .exposure-label {
    font-size: 11px;
    font-weight: 500;
  }
  .bar-track {
    width: 90px; height: 4px;
    background: var(--bg-surface);
    border-radius: 99px;
    overflow: hidden;
  }
  .bar-fill {
    height: 100%;
    border-radius: 99px;
  }

  /* ── Matrix ── */
  .matrix-wrap { overflow-x: auto; }
  .matrix {
    width: 100%;
    border-collapse: collapse;
    font-size: 11px;
    min-width: 520px;
  }
  .matrix th {
    padding: 8px 6px;
    text-align: center;
    color: var(--text-muted);
    font-family: var(--mono);
    font-size: 10px;
    letter-spacing: .06em;
    text-transform: uppercase;
    font-weight: 500;
    border-bottom: 1px solid var(--border);
  }
  .matrix th:first-child { text-align: left; }
  .matrix td {
    padding: 8px 6px;
    text-align: center;
    border-bottom: 1px solid var(--border);
    vertical-align: middle;
  }
  .matrix td:first-child {
    text-align: left;
    font-size: 12px;
    color: var(--text-secondary);
    font-weight: 500;
    white-space: nowrap;
    padding-right: 16px;
  }
  .matrix tr:last-child td { border-bottom: none; }
  .mcell {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 3px 8px;
    border-radius: 4px;
    font-size: 10px;
    font-weight: 600;
    font-family: var(--mono);
  }
  .mcell.h { background: rgba(232,93,74,.18); color: #f08070; }
  .mcell.m { background: rgba(245,166,35,.15); color: var(--amber); }
  .mcell.l { background: rgba(62,207,142,.12); color: var(--green); }
  .mcell.n { background: var(--bg-surface);    color: var(--text-muted); }

  /* ── Risk radar ── */
  .radar-wrap { position: relative; width: 100%; height: 280px; }

  /* ── Twin traits ── */
  .trait-row {
    display: flex;
    gap: 12px;
    padding: 13px 0;
    border-bottom: 1px solid var(--border);
    align-items: flex-start;
  }
  .trait-row:last-child { border-bottom: none; }
  .trait-icon {
    width: 32px; height: 32px;
    border-radius: var(--radius-sm);
    background: var(--bg-surface);
    border: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 16px;
    color: var(--text-secondary);
    flex-shrink: 0;
    margin-top: 1px;
  }
  .trait-label {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-primary);
    margin-bottom: 3px;
  }
  .trait-desc {
    font-size: 12px;
    color: var(--text-secondary);
    line-height: 1.55;
  }

  /* ── WOW Insights ── */
  .wow-grid { display: grid; grid-template-columns: repeat(2, minmax(0,1fr)); gap: 10px; }
  .wow-card {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-md);
    padding: 16px;
    position: relative;
  }
  .wow-accent {
    position: absolute;
    top: 0; left: 0;
    width: 3px; height: 100%;
    border-radius: var(--radius-md) 0 0 var(--radius-md);
  }
  .wow-title {
    font-size: 12px;
    font-weight: 600;
    color: var(--text-primary);
    margin-bottom: 5px;
    padding-left: 4px;
  }
  .wow-text {
    font-size: 11px;
    color: var(--text-secondary);
    line-height: 1.6;
    padding-left: 4px;
  }

  /* ── Assets ── */
  .asset-row {
    display: flex;
    align-items: flex-start;
    gap: 14px;
    padding: 12px 0;
    border-bottom: 1px solid var(--border);
  }
  .asset-row:last-child { border-bottom: none; }
  .asset-num {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-muted);
    width: 20px;
    flex-shrink: 0;
    padding-top: 2px;
  }
  .asset-info { flex: 1; }
  .asset-name {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-primary);
    margin-bottom: 3px;
  }
  .asset-desc {
    font-size: 11px;
    color: var(--text-secondary);
    line-height: 1.5;
  }
  .asset-badge {
    flex-shrink: 0;
    display: inline-flex;
    align-items: center;
    font-size: 10px;
    font-weight: 600;
    font-family: var(--mono);
    padding: 4px 8px;
    border-radius: var(--radius-sm);
    border: 1px solid transparent;
    margin-top: 2px;
  }
  .ab-critical { background: rgba(232,93,74,.18); color: #f08070; border-color: rgba(232,93,74,.25); }
  .ab-high     { background: rgba(245,166,35,.15); color: var(--amber); border-color: rgba(245,166,35,.22); }
  .ab-medium   { background: rgba(79,142,247,.12); color: var(--blue); border-color: rgba(79,142,247,.2); }

  /* ── Simulator ── */
  .sim-tabs {
    display: flex;
    gap: 6px;
    flex-wrap: wrap;
    margin-bottom: 16px;
  }
  .sim-tab {
    font-size: 12px;
    font-weight: 500;
    padding: 7px 14px;
    border-radius: var(--radius-md);
    border: 1px solid var(--border-md);
    background: transparent;
    color: var(--text-secondary);
    cursor: pointer;
    font-family: var(--sans);
    transition: all .15s;
  }
  .sim-tab:hover { background: var(--bg-surface); color: var(--text-primary); }
  .sim-tab.active {
    background: var(--accent-dim);
    border-color: var(--accent-border);
    color: #f08070;
  }
  .sim-panel {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-md);
    padding: 16px;
  }
  .sim-result-title {
    font-size: 13px;
    font-weight: 600;
    color: var(--text-primary);
    margin-bottom: 8px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .sim-delta {
    font-family: var(--mono);
    font-size: 12px;
    color: var(--green);
    background: var(--green-dim);
    padding: 2px 8px;
    border-radius: 4px;
  }
  .sim-text {
    font-size: 12px;
    color: var(--text-secondary);
    line-height: 1.65;
  }

  /* ── Plan ── */
  .plan-step {
    display: flex;
    gap: 14px;
    padding: 14px 0;
    border-bottom: 1px solid var(--border);
    align-items: flex-start;
  }
  .plan-step:last-child { border-bottom: none; }
  .plan-num {
    width: 26px; height: 26px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 11px;
    font-weight: 700;
    font-family: var(--mono);
    flex-shrink: 0;
    margin-top: 1px;
  }
  .pn-high   { background: rgba(232,93,74,.18); color: #f08070; }
  .pn-medium { background: rgba(245,166,35,.15); color: var(--amber); }
  .pn-low    { background: rgba(62,207,142,.12); color: var(--green); }
  .plan-priority {
    font-size: 10px;
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .08em;
    margin-bottom: 4px;
  }
  .plan-title {
    font-size: 13px;
    font-weight: 500;
    color: var(--text-primary);
    margin-bottom: 4px;
  }
  .plan-desc {
    font-size: 12px;
    color: var(--text-secondary);
    line-height: 1.6;
  }

  /* ── Verdict ── */
  .verdict {
    background: linear-gradient(135deg, rgba(232,93,74,.06) 0%, rgba(79,142,247,.04) 100%);
    border: 1px solid rgba(232,93,74,.18);
    border-radius: var(--radius-xl);
    padding: 28px;
    margin-top: 32px;
  }
  .verdict-header {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 16px;
  }
  .verdict-icon {
    width: 40px; height: 40px;
    border-radius: var(--radius-md);
    background: var(--accent-dim);
    border: 1px solid var(--accent-border);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    color: var(--accent);
    flex-shrink: 0;
  }
  .verdict-title {
    font-size: 18px;
    font-weight: 600;
    color: var(--text-primary);
    letter-spacing: -.01em;
  }
  .verdict-sub {
    font-size: 12px;
    color: var(--text-secondary);
    margin-top: 2px;
  }
  .verdict-body {
    font-size: 13px;
    color: var(--text-secondary);
    line-height: 1.75;
  }
  .verdict-body p { margin-bottom: 10px; }
  .verdict-body p:last-child { margin-bottom: 0; }
  .verdict-scores {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    margin: 20px 0;
  }
  .vscore-card {
    background: rgba(255,255,255,.03);
    border: 1px solid var(--border);
    border-radius: var(--radius-md);
    padding: 14px;
    text-align: center;
  }
  .vscore-num {
    font-size: 28px;
    font-weight: 700;
    font-family: var(--mono);
    letter-spacing: -.03em;
    margin-bottom: 4px;
  }
  .vscore-label {
    font-size: 11px;
    color: var(--text-muted);
    font-family: var(--mono);
    text-transform: uppercase;
    letter-spacing: .06em;
  }
  .disclaimer {
    background: var(--bg-surface);
    border: 1px solid var(--border);
    border-radius: var(--radius-md);
    padding: 14px;
    font-size: 11px;
    color: var(--text-muted);
    line-height: 1.65;
    margin-top: 24px;
    font-family: var(--mono);
  }
  .disclaimer strong { color: var(--text-secondary); font-weight: 500; }

  /* ── Footer ── */
  .footer {
    border-top: 1px solid var(--border);
    padding: 24px 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 12px;
    margin-top: 48px;
  }
  .footer-left {
    font-size: 11px;
    color: var(--text-muted);
    font-family: var(--mono);
  }
  .footer-tags { display: flex; gap: 6px; }

  /* ── Responsive ── */
  @media (max-width: 700px) {
    .stat-grid { grid-template-columns: repeat(2, 1fr); }
    .score-grid { grid-template-columns: 1fr; }
    .heat-grid  { grid-template-columns: repeat(3, 1fr); }
    .grid-2, .grid-3, .wow-grid { grid-template-columns: 1fr; }
    .verdict-scores { grid-template-columns: 1fr; }
    .header { flex-direction: column; }
  }
</style>
</head>
<body>

<div class="shell">

  <!-- ── Header ── -->
  <header class="header">
    <div class="header-left">
      <div class="header-eyebrow">Digital Footprint Analysis</div>
      <h1 class="header-title">Your privacy & exposure report</h1>
      <p class="header-sub">Based on 15 self-reported services across 7+ parent companies</p>
    </div>
    <div class="header-right">
      <div class="report-date" id="reportDate"></div>
      <div style="display:flex;gap:6px;">
        <span class="tag tag-fact"><i class="ti ti-check" style="font-size:10px"></i>FACT = reported</span>
        <span class="tag tag-est"><i class="ti ti-sparkles" style="font-size:10px"></i>ESTIMATE = inferred</span>
      </div>
    </div>
  </header>

  <!-- ── Overview stats ── -->
  <div class="stat-grid">
    <div class="stat-card c-blue">
      <div class="stat-label">Services used</div>
      <div class="stat-value">15</div>
      <div class="stat-sub"><span class="tag tag-fact">FACT</span></div>
    </div>
    <div class="stat-card c-amber">
      <div class="stat-label">Parent companies</div>
      <div class="stat-value">7<span style="font-size:14px;color:var(--text-muted)">+</span></div>
      <div class="stat-sub"><span class="tag tag-fact">FACT</span></div>
    </div>
    <div class="stat-card c-red">
      <div class="stat-label">Ecosystem concentration</div>
      <div class="stat-value">68<span style="font-size:14px">%</span></div>
      <div class="stat-sub"><span class="tag tag-est">ESTIMATE</span></div>
    </div>
    <div class="stat-card c-green">
      <div class="stat-label">Tracking surface</div>
      <div class="stat-value" style="font-size:20px;padding-top:4px">High</div>
      <div class="stat-sub"><span class="tag tag-est">ESTIMATE</span></div>
    </div>
  </div>

  <!-- ── Scores ── -->
  <div class="section-eyebrow">Scores</div>
  <div class="score-grid">
    <div class="score-card">
      <div class="score-ring">
        <svg width="90" height="90" viewBox="0 0 90 90" aria-label="Digital Footprint Score: 74 out of 100 — Significant">
          <circle cx="45" cy="45" r="34" fill="none" stroke="rgba(255,255,255,.06)" stroke-width="7"/>
          <circle cx="45" cy="45" r="34" fill="none" stroke="#e85d4a" stroke-width="7"
            stroke-dasharray="213.6" stroke-dashoffset="55.5"
            stroke-linecap="round" transform="rotate(-90 45 45)"/>
          <text x="45" y="49" text-anchor="middle" font-size="20" font-weight="700"
            font-family="'JetBrains Mono',monospace" fill="#f0f2f5">74</text>
        </svg>
      </div>
      <div class="score-info">
        <div class="score-label">Digital footprint score</div>
        <div class="score-status orange">🟠 Significant</div>
        <p class="score-desc">Active presence across social, gaming, commerce, comms &amp; finance platforms. <span class="tag tag-est">ESTIMATE</span></p>
      </div>
    </div>
    <div class="score-card">
      <div class="score-ring">
        <svg width="90" height="90" viewBox="0 0 90 90" aria-label="Privacy Score: 34 out of 100 — Fair">
          <circle cx="45" cy="45" r="34" fill="none" stroke="rgba(255,255,255,.06)" stroke-width="7"/>
          <circle cx="45" cy="45" r="34" fill="none" stroke="#f5a623" stroke-width="7"
            stroke-dasharray="213.6" stroke-dashoffset="140.9"
            stroke-linecap="round" transform="rotate(-90 45 45)"/>
          <text x="45" y="49" text-anchor="middle" font-size="20" font-weight="700"
            font-family="'JetBrains Mono',monospace" fill="#f0f2f5">34</text>
        </svg>
      </div>
      <div class="score-info">
        <div class="score-label">Privacy score</div>
        <div class="score-status amber">🟠 Fair</div>
        <p class="score-desc">Multiple high-collection platforms with limited default privacy protections enabled. <span class="tag tag-est">ESTIMATE</span></p>
      </div>
    </div>
  </div>

  <!-- ── Heatmap ── -->
  <div class="section-eyebrow">Exposure heatmap</div>
  <div class="card" style="margin-bottom:14px">
    <div class="heat-grid">
      <div class="heat-cell level-crit">
        <div class="heat-cat">Search</div>
        <div class="heat-score">95</div>
      </div>
      <div class="heat-cell level-crit">
        <div class="heat-cat">Social</div>
        <div class="heat-score">92</div>
      </div>
      <div class="heat-cell level-crit">
        <div class="heat-cat">Ads cross-site</div>
        <div class="heat-score">88</div>
      </div>
      <div class="heat-cell level-crit">
        <div class="heat-cat">Commerce</div>
        <div class="heat-score">85</div>
      </div>
      <div class="heat-cell level-high">
        <div class="heat-cat">Video</div>
        <div class="heat-score">80</div>
      </div>
      <div class="heat-cell level-high">
        <div class="heat-cat">Comms</div>
        <div class="heat-score">78</div>
      </div>
      <div class="heat-cell level-high">
        <div class="heat-cat">Gaming</div>
        <div class="heat-score">72</div>
      </div>
      <div class="heat-cell level-high">
        <div class="heat-cat">Finance</div>
        <div class="heat-score">68</div>
      </div>
      <div class="heat-cell level-med">
        <div class="heat-cat">Photos</div>
        <div class="heat-score">60</div>
      </div>
      <div class="heat-cell level-low">
        <div class="heat-cat">Audio</div>
        <div class="heat-score">55</div>
      </div>
    </div>
    <div class="heat-legend">
      <div class="heat-legend-item"><div class="heat-legend-dot" style="background:rgba(232,93,74,.6)"></div>Critical (80–100)</div>
      <div class="heat-legend-item"><div class="heat-legend-dot" style="background:rgba(245,166,35,.6)"></div>High (60–79)</div>
      <div class="heat-legend-item"><div class="heat-legend-dot" style="background:rgba(79,142,247,.6)"></div>Moderate (40–59)</div>
      <div class="heat-legend-item"><div class="heat-legend-dot" style="background:rgba(62,207,142,.6)"></div>Lower (0–39)</div>
      <div class="heat-legend-item" style="margin-left:auto"><span class="tag tag-est">ESTIMATE</span></div>
    </div>
  </div>

  <!-- ── Company ranking + Chart ── -->
  <div class="section-eyebrow">Company exposure ranking</div>
  <div class="grid-2">
    <div class="card">
      <div class="card-title"><i class="ti ti-building-skyscraper" aria-hidden="true"></i> Parent companies by exposure</div>
      <div class="company-row">
        <div class="company-rank">01</div>
        <div class="company-logo" style="background:rgba(79,142,247,.15);color:#4f8ef7">G</div>
        <div class="company-info">
          <div class="company-name">Google (Alphabet)</div>
          <div class="company-apps">Search · Pay · Photos · YouTube</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#e85d4a">Extreme</div>
          <div class="bar-track"><div class="bar-fill" style="width:98%;background:#e85d4a"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">02</div>
        <div class="company-logo" style="background:rgba(139,92,246,.15);color:#8b5cf6">M</div>
        <div class="company-info">
          <div class="company-name">Meta</div>
          <div class="company-apps">Instagram · WhatsApp</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#e85d4a">Very high</div>
          <div class="bar-track"><div class="bar-fill" style="width:90%;background:#e85d4a"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">03</div>
        <div class="company-logo" style="background:rgba(232,93,74,.12);color:#e85d4a">B</div>
        <div class="company-info">
          <div class="company-name">ByteDance</div>
          <div class="company-apps">TikTok</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#e85d4a">Very high</div>
          <div class="bar-track"><div class="bar-fill" style="width:85%;background:#e85d4a"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">04</div>
        <div class="company-logo" style="background:rgba(245,166,35,.12);color:#f5a623">A</div>
        <div class="company-info">
          <div class="company-name">Amazon</div>
          <div class="company-apps">Amazon (shopping)</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#f5a623">High</div>
          <div class="bar-track"><div class="bar-fill" style="width:78%;background:#f5a623"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">05</div>
        <div class="company-logo" style="background:rgba(62,207,142,.1);color:#3ecf8e">Sn</div>
        <div class="company-info">
          <div class="company-name">Snap Inc.</div>
          <div class="company-apps">Snapchat</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#f5a623">Moderate</div>
          <div class="bar-track"><div class="bar-fill" style="width:65%;background:#f5a623"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">06</div>
        <div class="company-logo" style="background:rgba(79,142,247,.1);color:#4f8ef7">Sp</div>
        <div class="company-info">
          <div class="company-name">Spotify</div>
          <div class="company-apps">Spotify</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#3ecf8e">Moderate</div>
          <div class="bar-track"><div class="bar-fill" style="width:55%;background:#3ecf8e"></div></div>
        </div>
      </div>
      <div class="company-row">
        <div class="company-rank">07</div>
        <div class="company-logo" style="background:rgba(255,255,255,.04);color:var(--text-muted)">+4</div>
        <div class="company-info">
          <div class="company-name">Apple · Krafton · Roblox · Meesho</div>
          <div class="company-apps">iMessage · PUBG Mobile · Roblox · Meesho</div>
        </div>
        <div class="company-exposure">
          <div class="exposure-label" style="color:#3ecf8e">Lower</div>
          <div class="bar-track"><div class="bar-fill" style="width:35%;background:#3ecf8e"></div></div>
        </div>
      </div>
      <p style="font-size:10px;color:var(--text-muted);margin-top:10px;font-family:var(--mono)"><span class="tag tag-est">ESTIMATE</span> — based on published privacy policies and industry reporting.</p>
    </div>

    <div class="card">
      <div class="card-title"><i class="ti ti-chart-pie" aria-hidden="true"></i> Data collection by company share</div>
      <div style="position:relative;width:100%;height:230px">
        <canvas id="companyPie" role="img" aria-label="Pie chart: Google 35%, Meta 22%, ByteDance 16%, Amazon 12%, Snap 8%, Others 7%">Google 35%, Meta 22%, ByteDance 16%, Amazon 12%, Snap 8%, Others 7%</canvas>
      </div>
      <div style="display:flex;flex-wrap:wrap;gap:8px;margin-top:12px">
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#4f8ef7;display:inline-block"></span>Google 35%</span>
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#8b5cf6;display:inline-block"></span>Meta 22%</span>
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#e85d4a;display:inline-block"></span>ByteDance 16%</span>
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#f5a623;display:inline-block"></span>Amazon 12%</span>
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#3ecf8e;display:inline-block"></span>Snap 8%</span>
        <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#5f6470;display:inline-block"></span>Others 7%</span>
      </div>
    </div>
  </div>

  <!-- ── Data Collection Matrix ── -->
  <div class="section-eyebrow">Data collection matrix</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-table" aria-hidden="true"></i> Estimated data types collected per company</div>
    <div class="matrix-wrap">
      <table class="matrix">
        <thead>
          <tr>
            <th>Company</th>
            <th>Location</th>
            <th>Biometric</th>
            <th>Contacts</th>
            <th>Purchases</th>
            <th>Content</th>
            <th>Behaviour</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Google</td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
          </tr>
          <tr>
            <td>Meta</td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
          </tr>
          <tr>
            <td>ByteDance</td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell h">HIGH</span></td>
          </tr>
          <tr>
            <td>Amazon</td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
          </tr>
          <tr>
            <td>Snap</td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell m">MED</span></td>
          </tr>
          <tr>
            <td>Spotify</td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell n">—</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell h">HIGH</span></td>
            <td><span class="mcell m">MED</span></td>
          </tr>
          <tr>
            <td>Others</td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell l">LOW</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell m">MED</span></td>
            <td><span class="mcell m">MED</span></td>
          </tr>
        </tbody>
      </table>
    </div>
    <p style="font-size:10px;color:var(--text-muted);margin-top:10px;font-family:var(--mono)"><span class="tag tag-est">ESTIMATE</span> — inferred from published privacy policies and independent research. Not verified from personal account data.</p>
  </div>

  <!-- ── Risk Radar ── -->
  <div class="section-eyebrow">Risk radar</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-radar" aria-hidden="true"></i> Six-axis risk profile</div>
    <div class="radar-wrap">
      <canvas id="radarChart" role="img" aria-label="Risk radar: Cross-app tracking 90, Identity profiling 78, Behavioural targeting 88, Location inference 65, Financial exposure 70, Content analysis 82">Cross-app tracking 90, Identity profiling 78, Behavioural targeting 88, Location inference 65, Financial data exposure 70, Content analysis 82.</canvas>
    </div>
    <div style="display:flex;gap:16px;flex-wrap:wrap;margin-top:8px">
      <span style="display:flex;align-items:center;gap:5px;font-size:11px;color:var(--text-secondary)"><span style="width:9px;height:9px;border-radius:2px;background:#e85d4a;opacity:.6;display:inline-block"></span>Risk level (0–100)</span>
      <span class="tag tag-est" style="margin-left:auto">ESTIMATE</span>
    </div>
  </div>

  <!-- ── Digital Twin ── -->
  <div class="section-eyebrow">Digital twin profile</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-user-scan" aria-hidden="true"></i> What ad platforms likely infer about you</div>
    <p style="font-size:12px;color:var(--text-muted);margin-bottom:14px;font-family:var(--mono)">All traits below are <span class="tag tag-est">ESTIMATE</span> — inferred from platform demographics & usage patterns. None are confirmed facts.</p>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-device-mobile" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Mobile-first user</div>
        <div class="trait-desc">Inferred from the mobile-dominated app stack (PUBG Mobile, Meesho, Snapchat, WhatsApp). Likely spends the majority of screen time on a smartphone rather than desktop.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-users" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Youth / Gen Z demographic</div>
        <div class="trait-desc">TikTok, Snapchat, Roblox, PUBG Mobile, and Discord collectively skew strongly toward users aged 13–24. Advertisers would most likely categorise this profile in that demographic bracket.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-device-gamepad-2" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Active gamer</div>
        <div class="trait-desc">PUBG Mobile and Roblox together suggest regular gaming habits across different genres. Gaming platforms typically capture session length, in-app spend signals, and social graph data.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-shopping-bag" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Online shopper (value-conscious)</div>
        <div class="trait-desc">Amazon + Meesho signals a dual approach to e-commerce — international platform and value-market purchases. Meesho in particular suggests price-sensitivity or small-business shopping patterns.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-music" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Music & video entertainment consumer</div>
        <div class="trait-desc">Spotify + YouTube together construct a detailed audio and video taste profile. This data is used for targeted content recommendations and third-party ad targeting.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-social" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">High social media engagement</div>
        <div class="trait-desc">Three major short-form and social platforms (Instagram, Snapchat, TikTok) suggest frequent content creation or consumption. All three run competing ad networks that can cross-target the same user.</div>
      </div>
    </div>
    <div class="trait-row">
      <div class="trait-icon"><i class="ti ti-credit-card" aria-hidden="true"></i></div>
      <div>
        <div class="trait-label">Digital payments user</div>
        <div class="trait-desc">Google Pay usage implies a transaction history tied to your Google account — linking purchases to your search and location data. Financial behaviour is among the most commercially valuable data types.</div>
      </div>
    </div>
  </div>

  <!-- ── WOW Insights ── -->
  <div class="section-eyebrow">WOW insights</div>
  <div class="wow-grid" style="margin-bottom:14px">
    <div class="wow-card">
      <div class="wow-accent" style="background:#e85d4a"></div>
      <div class="wow-title">Google sees nearly everything</div>
      <div class="wow-text">Search queries, YouTube watch history, payment transactions, and photo metadata are all within a single company. Google likely holds the most complete individual profile in this entire footprint. <span class="tag tag-est">ESTIMATE</span></div>
    </div>
    <div class="wow-card">
      <div class="wow-accent" style="background:#8b5cf6"></div>
      <div class="wow-title">3 platforms share your face and voice</div>
      <div class="wow-text">TikTok, Snapchat, and Instagram all involve camera and microphone access and may collect biometric-adjacent data via face filters and voice features — each with a different corporate owner. <span class="tag tag-est">ESTIMATE</span></div>
    </div>
    <div class="wow-card">
      <div class="wow-accent" style="background:#f5a623"></div>
      <div class="wow-title">Your contacts are with multiple companies</div>
      <div class="wow-text">WhatsApp, Snapchat, Discord, and iMessage likely have access to your device contact list. This indirectly creates profiles of people who have not themselves consented to data sharing. <span class="tag tag-est">ESTIMATE</span></div>
    </div>
    <div class="wow-card">
      <div class="wow-accent" style="background:#4f8ef7"></div>
      <div class="wow-title">Ad platforms can cross-match your identity</div>
      <div class="wow-text">Meta, Google, and ByteDance all run competing ad networks. Even different usernames don't fully protect you — device fingerprints, email hashes, and phone numbers can re-link profiles. <span class="tag tag-est">ESTIMATE</span></div>
    </div>
    <div class="wow-card">
      <div class="wow-accent" style="background:#3ecf8e"></div>
      <div class="wow-title">You operate across 4 data law jurisdictions</div>
      <div class="wow-text">ByteDance is subject to Chinese data law. Meta &amp; Google are US-based. Meesho falls under India's IT Act. Your data is governed by a patchwork of international regulations. <span class="tag tag-fact">FACT</span> (jurisdictions) · <span class="tag tag-est">ESTIMATE</span> (implications)</div>
    </div>
    <div class="wow-card">
      <div class="wow-accent" style="background:#e85d4a"></div>
      <div class="wow-title">Discord is the wild card</div>
      <div class="wow-text">Discord knows who you game with, what you talk about in servers, and your voice call patterns. It's often overlooked in privacy audits but collects significant behavioural and social graph data. <span class="tag tag-est">ESTIMATE</span></div>
    </div>
  </div>

  <!-- ── Most Valuable Data Assets ── -->
  <div class="section-eyebrow">Most valuable data assets</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-crown" aria-hidden="true"></i> Ranked by commercial value to advertisers</div>
    <div class="asset-row">
      <div class="asset-num">01</div>
      <div class="asset-info">
        <div class="asset-name">Search & real-time intent history</div>
        <div class="asset-desc">Google Search — reveals live desires, fears, health concerns, and purchase intent. The single highest-value advertising signal that exists. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-critical">CRITICAL</div>
    </div>
    <div class="asset-row">
      <div class="asset-num">02</div>
      <div class="asset-info">
        <div class="asset-name">Payment & transaction data</div>
        <div class="asset-desc">Google Pay — purchase amounts, merchant categories, frequency, and timing. Directly correlated to spending behaviour and income signals. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-critical">CRITICAL</div>
    </div>
    <div class="asset-row">
      <div class="asset-num">03</div>
      <div class="asset-info">
        <div class="asset-name">Social graph & communication patterns</div>
        <div class="asset-desc">WhatsApp + iMessage + Discord — who you communicate with, how frequently, and relationship closeness. Used to infer life events and social influence. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-high">VERY HIGH</div>
    </div>
    <div class="asset-row">
      <div class="asset-num">04</div>
      <div class="asset-info">
        <div class="asset-name">Photo & location metadata</div>
        <div class="asset-desc">Google Photos — images often carry embedded GPS coordinates, face recognition data, and timestamps that reconstruct your movement history. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-high">VERY HIGH</div>
    </div>
    <div class="asset-row">
      <div class="asset-num">05</div>
      <div class="asset-info">
        <div class="asset-name">Entertainment taste profile</div>
        <div class="asset-desc">YouTube + Spotify + TikTok together — taste, mood, cultural identity, and recreational habits. Valuable for psychographic targeting across all ad networks. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-high">HIGH</div>
    </div>
    <div class="asset-row">
      <div class="asset-num">06</div>
      <div class="asset-info">
        <div class="asset-name">Shopping & price sensitivity data</div>
        <div class="asset-desc">Amazon + Meesho — browsing habits, cart abandonment, price comparison patterns, and purchase history. Informs commercial targeting. <span class="tag tag-est">ESTIMATE</span></div>
      </div>
      <div class="asset-badge ab-medium">HIGH</div>
    </div>
  </div>

  <!-- ── Privacy Simulator ── -->
  <div class="section-eyebrow">Privacy improvement simulator</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-adjustments-horizontal" aria-hidden="true"></i> Select an action to see estimated privacy impact <span class="tag tag-est" style="margin-left:auto">ESTIMATE</span></div>
    <div class="sim-tabs">
      <button class="sim-tab active" onclick="showSim(this,'tiktok')">Delete TikTok</button>
      <button class="sim-tab" onclick="showSim(this,'google')">Turn off Google activity</button>
      <button class="sim-tab" onclick="showSim(this,'vpn')">Use a VPN</button>
      <button class="sim-tab" onclick="showSim(this,'contacts')">Revoke contacts access</button>
      <button class="sim-tab" onclick="showSim(this,'email')">Switch to private email</button>
    </div>
    <div class="sim-panel" id="simPanel">
      <div class="sim-result-title">
        Delete TikTok
        <span class="sim-delta" id="simDelta">+8 to +12 privacy pts</span>
      </div>
      <div class="sim-text" id="simText">
        Removing TikTok eliminates ByteDance from your footprint entirely. This cuts biometric data exposure (face filter processing, voice data), removes one of the highest-risk cross-border data flows — to a Chinese-jurisdiction entity — and stops behavioural profiling on one of the most data-hungry platforms. TikTok's algorithm tracks watch time at a granular per-second level. Digital footprint score would also decrease by an estimated 6 to 8 points.
      </div>
    </div>
  </div>

  <!-- ── Privacy Plan ── -->
  <div class="section-eyebrow">Privacy improvement plan</div>
  <div class="card" style="margin-bottom:14px">
    <div class="card-title"><i class="ti ti-list-check" aria-hidden="true"></i> Prioritised actions by impact</div>
    <div class="plan-step">
      <div class="plan-num pn-high">1</div>
      <div>
        <div class="plan-priority" style="color:#e85d4a">Critical priority</div>
        <div class="plan-title">Audit app permissions right now</div>
        <div class="plan-desc">Go to phone Settings → Privacy → Permission Manager. Revoke microphone, camera, contacts, and location from any app that doesn't explicitly need them. Gaming apps and social media rarely need all four.</div>
      </div>
    </div>
    <div class="plan-step">
      <div class="plan-num pn-high">2</div>
      <div>
        <div class="plan-priority" style="color:#e85d4a">Critical priority</div>
        <div class="plan-title">Disable Google activity tracking</div>
        <div class="plan-desc">Visit myactivity.google.com → turn off Web & App Activity, Location History, and YouTube History. Set auto-delete to every 3 months. This addresses the single largest exposure in your footprint.</div>
      </div>
    </div>
    <div class="plan-step">
      <div class="plan-num pn-high">3</div>
      <div>
        <div class="plan-priority" style="color:#e85d4a">Critical priority</div>
        <div class="plan-title">Turn off Meta cross-app tracking</div>
        <div class="plan-desc">Facebook/Instagram Settings → Ads → Ad Preferences → turn off "Ads based on data from partners" and disconnect your activity from external apps and websites.</div>
      </div>
    </div>
    <div class="plan-step">
      <div class="plan-num pn-medium">4</div>
      <div>
        <div class="plan-priority" style="color:#f5a623">Medium priority</div>
        <div class="plan-title">Enable disappearing messages on WhatsApp</div>
        <div class="plan-desc">Set message expiry to 7 or 30 days across all chats. Reduces the permanent message archive maintained by the platform and decreases the breadth of communication data stored.</div>
      </div>
    </div>
    <div class="plan-step">
      <div class="plan-num pn-medium">5</div>
      <div>
        <div class="plan-priority" style="color:#f5a623">Medium priority</div>
        <div class="plan-title">Use separate email addresses for shopping vs social</div>
        <div class="plan-desc">Isolating identities prevents data brokers from cross-linking your Amazon and Meesho purchase history with your social media and search profiles — one of the primary identity-matching techniques.</div>
      </div>
    </div>
    <div class="plan-step">
      <div class="plan-num pn-low">6</div>
      <div>
        <div class="plan-priority" style="color:#3ecf8e">Long-term improvement</div>
        <div class="plan-title">Replace Google Search with a privacy-respecting alternative</div>
        <div class="plan-desc">DuckDuckGo or Brave Search as your default eliminates one of the highest-value data collection points in this entire footprint — real-time intent data. This alone is estimated to add 10 to 15 privacy points.</div>
      </div>
    </div>
  </div>

  <!-- ── Final Verdict ── -->
  <div class="verdict">
    <div class="verdict-header">
      <div class="verdict-icon"><i class="ti ti-shield-exclamation" aria-hidden="true"></i></div>
      <div>
        <div class="verdict-title">Significant, high-concentration digital footprint</div>
        <div class="verdict-sub">Final assessment based on 15 self-reported services</div>
      </div>
    </div>

    <div class="verdict-scores">
      <div class="vscore-card">
        <div class="vscore-num" style="color:#e85d4a">74</div>
        <div class="vscore-label">Footprint score</div>
      </div>
      <div class="vscore-card">
        <div class="vscore-num" style="color:#f5a623">34</div>
        <div class="vscore-label">Privacy score</div>
      </div>
      <div class="vscore-card">
        <div class="vscore-num" style="color:#4f8ef7">7+</div>
        <div class="vscore-label">Parent companies</div>
      </div>
    </div>

    <div class="verdict-body">
      <p>Across 15 services and 7+ parent companies, your digital life is extensively documented — particularly by Google, which simultaneously touches search intent, payments, photos, and video consumption. Meta and ByteDance add substantial social and biometric data layers on top of this. The combination of these platforms creates a rich, cross-referenced advertising and inference profile that is commercially very valuable to third parties.</p>
      <p>The highest-risk areas are cross-app ad tracking and the concentration of sensitive data types — payments, search queries, location, and contacts — within Google's single ecosystem. A privacy score of 34 reflects that most default settings across these platforms are calibrated toward data collection, not user protection.</p>
      <p><strong style="font-weight:600;color:var(--text-primary)">Your practical priority:</strong> Start with Google activity controls and a full app permission audit. These two actions together require no app deletions and offer the highest privacy gain per minute of effort of anything in this report.</p>
    </div>

    <div class="disclaimer">
      <strong>Disclaimer:</strong> All inferences in this report are estimates based on publicly known platform data practices, published privacy policies, and independent research. This dashboard does not access, connect to, or query any private databases, personal accounts, or third-party data brokers. Facts are limited to the list of services you provided. Everything else is clearly marked as an estimate and should be treated as informational guidance, not a verified audit of your personal data exposure.
    </div>
  </div>

  <!-- ── Footer ── -->
  <footer class="footer">
    <div class="footer-left">Digital Footprint Dashboard · Generated <span id="footerDate"></span></div>
    <div class="footer-tags">
      <span class="tag tag-fact">15 SERVICES REPORTED</span>
      <span class="tag tag-est">INFERENCES ESTIMATED</span>
    </div>
  </footer>

</div>

<script>
  // Date
  const now = new Date();
  const fmt = { year:'numeric', month:'long', day:'numeric' };
  const ds = now.toLocaleDateString('en-IN', fmt);
  document.getElementById('reportDate').textContent = 'Generated ' + ds;
  document.getElementById('footerDate').textContent = ds;

  // Pie chart
  new Chart(document.getElementById('companyPie'), {
    type: 'doughnut',
    data: {
      labels: ['Google','Meta','ByteDance','Amazon','Snap','Others'],
      datasets: [{
        data: [35, 22, 16, 12, 8, 7],
        backgroundColor: ['#4f8ef7','#8b5cf6','#e85d4a','#f5a623','#3ecf8e','#5f6470'],
        borderWidth: 0,
        hoverOffset: 6
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      cutout: '65%',
      plugins: {
        legend: { display: false },
        tooltip: {
          callbacks: {
            label: ctx => ' ' + ctx.label + ': ' + ctx.parsed + '%'
          }
        }
      }
    }
  });

  // Radar chart
  new Chart(document.getElementById('radarChart'), {
    type: 'radar',
    data: {
      labels: ['Cross-app tracking','Identity profiling','Behavioural targeting','Location inference','Financial exposure','Content analysis'],
      datasets: [{
        label: 'Risk level',
        data: [90, 78, 88, 65, 70, 82],
        backgroundColor: 'rgba(232,93,74,0.12)',
        borderColor: '#e85d4a',
        borderWidth: 2,
        pointBackgroundColor: '#e85d4a',
        pointRadius: 5,
        pointHoverRadius: 7
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: { legend: { display: false } },
      scales: {
        r: {
          min: 0, max: 100,
          ticks: {
            stepSize: 25,
            color: 'rgba(255,255,255,0.2)',
            font: { size: 9, family: "'JetBrains Mono',monospace" },
            backdropColor: 'transparent'
          },
          grid: { color: 'rgba(255,255,255,0.06)' },
          angleLines: { color: 'rgba(255,255,255,0.06)' },
          pointLabels: {
            color: 'rgba(255,255,255,0.45)',
            font: { size: 11, family: "'Inter',sans-serif" }
          }
        }
      }
    }
  });

  // Simulator
  const simData = {
    tiktok: {
      title: 'Delete TikTok',
      delta: '+8 to +12 privacy pts',
      text: 'Removing TikTok eliminates ByteDance from your footprint entirely. This cuts biometric data exposure (face filter processing, voice data), removes one of the highest-risk cross-border data flows — to a Chinese-jurisdiction entity — and stops behavioural profiling on one of the most data-hungry platforms. TikTok\'s algorithm tracks watch time at a granular per-second level. Digital footprint score would also decrease by an estimated 6 to 8 points.'
    },
    google: {
      title: 'Turn off Google activity',
      delta: '+15 to +20 privacy pts',
      text: 'Disabling Web & App Activity, Location History, and YouTube History on myactivity.google.com significantly reduces the most complete profile in your footprint. Google Pay and Google Search will still operate and collect some data during active use, but historical aggregation stops. This is the single highest-impact privacy action available to you. Estimated digital footprint score reduction: 10 to 14 points.'
    },
    vpn: {
      title: 'Use a VPN',
      delta: '+5 to +8 privacy pts',
      text: 'A VPN masks your IP address from apps and websites, reducing location inference by advertisers and hiding your internet traffic from your ISP. However, it does NOT prevent data collection by the apps themselves — TikTok, Meta, and Google will continue to collect in-app behaviour. A VPN is a network-layer protection only, not a substitute for adjusting in-app privacy settings. Estimated improvement is network-level exposure only.'
    },
    contacts: {
      title: 'Revoke contacts access',
      delta: '+6 to +10 privacy pts',
      text: 'Revoking contacts access from WhatsApp, Snapchat, Discord, and Instagram prevents these apps from harvesting your phonebook. Crucially, this also protects people in your contacts who have not personally consented to any data sharing with these platforms. On iOS, go to Settings → Privacy → Contacts. On Android, Settings → Apps → [App Name] → Permissions → Contacts.'
    },
    email: {
      title: 'Switch to private email',
      delta: '+10 to +14 privacy pts',
      text: 'Using ProtonMail or Tutanota as your primary email, with separate addresses for shopping accounts and social accounts, prevents data brokers from cross-linking your Amazon and Meesho purchase history with your social and Google profile. Email address is the most common identity-matching key used by ad networks to merge datasets across different platforms. This takes effect gradually over weeks as accounts update.'
    }
  };

  function showSim(btn, key) {
    document.querySelectorAll('.sim-tab').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    const d = simData[key];
    document.querySelector('.sim-result-title').childNodes[0].textContent = d.title + ' ';
    document.getElementById('simDelta').textContent = d.delta;
    document.getElementById('simText').textContent = d.text;
  }
</script>
</body>
</html>
