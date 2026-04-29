<!DOCTYPE html>

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FXGlobe · Investment Plan Generator</title>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:wght@400;500;600;700&family=Barlow:wght@300;400;500;600;700&family=Barlow+Condensed:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --gold: #C9A84C; --gold-light: #E8C97A; --dark: #0A0A0A; --dark2: #141414;
    --dark3: #1E1E1E; --border: #333333; --text: #E8E8E8; --muted: #888888;
    --red: #D0281E;
  }
  * { margin:0; padding:0; box-sizing:border-box; }
  body { background:var(--dark); color:var(--text); font-family:'Barlow',sans-serif; min-height:100vh; }
  .app { display:flex; min-height:100vh; }

/* SIDEBAR */
.sidebar { width:380px; min-width:380px; background:var(–dark2); border-right:1px solid var(–border); display:flex; flex-direction:column; position:sticky; top:0; height:100vh; overflow-y:auto; }
.sidebar-header { padding:28px 28px 20px; border-bottom:1px solid var(–border); }
.sidebar-logo { font-family:‘Barlow Condensed’,sans-serif; font-size:28px; font-weight:700; letter-spacing:2px; color:#FFFFFF; }
.sidebar-logo .fx { color:#FFFFFF; }
.sidebar-logo .globe { color:var(–red); }
.sidebar-logo .dot-com { display:block; font-size:11px; font-weight:400; letter-spacing:4px; color:var(–gold); margin-top:1px; }
.tagline { font-family:‘Barlow Condensed’,sans-serif; font-size:11px; letter-spacing:3px; color:var(–muted); text-transform:uppercase; margin-top:6px; }
.form-section { padding:20px 28px; border-bottom:1px solid var(–border); }
.section-label { font-family:‘Barlow Condensed’,sans-serif; font-size:10px; letter-spacing:3px; color:var(–gold); text-transform:uppercase; margin-bottom:14px; }
.field { margin-bottom:14px; }
.field label { display:block; font-size:11px; font-weight:500; color:var(–muted); letter-spacing:1px; text-transform:uppercase; margin-bottom:5px; }
.field input, .field select { width:100%; background:var(–dark3); border:1px solid var(–border); color:#FFF; font-family:‘Barlow’,sans-serif; font-size:15px; padding:10px 14px; outline:none; transition:border-color 0.2s; -webkit-appearance:none; }
.field input:focus, .field select:focus { border-color:var(–gold); }
.field select option { background:var(–dark3); }
.field input::placeholder { color:var(–border); }
.field-row { display:grid; grid-template-columns:1fr 1fr; gap:12px; }
.generate-btn { margin:18px 28px 10px; width:calc(100% - 56px); background:var(–gold); color:var(–dark); border:none; font-family:‘Barlow Condensed’,sans-serif; font-size:14px; font-weight:700; letter-spacing:3px; text-transform:uppercase; padding:15px; cursor:pointer; transition:background 0.2s; }
.generate-btn:hover { background:var(–gold-light); }
.print-btn { margin:0 28px 24px; width:calc(100% - 56px); background:transparent; color:var(–gold); border:1px solid var(–gold); font-family:‘Barlow Condensed’,sans-serif; font-size:13px; font-weight:600; letter-spacing:3px; text-transform:uppercase; padding:13px; cursor:pointer; transition:all 0.2s; display:none; }
.print-btn:hover { background:var(–gold); color:var(–dark); }

/* PREVIEW */
.preview-area { flex:1; background:#E8E8E8; padding:40px; display:flex; justify-content:center; align-items:flex-start; overflow-y:auto; }
.placeholder-msg { display:flex; flex-direction:column; align-items:center; justify-content:center; height:100%; min-height:500px; color:#999; text-align:center; }
.placeholder-msg .icon { font-size:48px; margin-bottom:16px; opacity:0.3; }
.placeholder-msg p { font-family:‘Barlow Condensed’,sans-serif; letter-spacing:2px; text-transform:uppercase; font-size:13px; }
#pdf-document { display:none; }

/* PDF PAGE */
.pdf-page { width:794px; min-height:1123px; background:#FFFFFF; position:relative; overflow:hidden; box-shadow:0 8px 60px rgba(0,0,0,0.25); margin-bottom:20px; font-family:‘Barlow’,sans-serif; color:#1A1A1A; display:flex; flex-direction:column; }

/* WATERMARK */
.watermark { position:absolute; top:0; left:0; right:0; bottom:0; pointer-events:none; z-index:1; overflow:hidden; }
.watermark-grid { position:absolute; top:-200px; left:-200px; width:1400px; height:1600px; display:grid; grid-template-columns:repeat(3,1fr); gap:0; transform:rotate(-35deg); transform-origin:center; }
.wm-cell { padding:60px 20px; text-align:center; }
.wm-text { font-family:‘Cormorant Garamond’,serif; font-size:34px; font-weight:700; color:rgba(201,168,76,0.10); letter-spacing:6px; text-transform:uppercase; white-space:nowrap; display:block; line-height:1; }
.wm-sub { font-family:‘Barlow Condensed’,sans-serif; font-size:11px; color:rgba(201,168,76,0.07); letter-spacing:3px; text-transform:uppercase; display:block; margin-top:4px; }

/* PAGE CONTENT */
.page-content { position:relative; z-index:2; display:flex; flex-direction:column; flex:1; }

/* PAGE HEADER */
.page-header { background:#FFFFFF; padding:18px 40px; display:flex; justify-content:space-between; align-items:center; border-bottom:3px solid #C9A84C; }
.doc-logo-text { font-family:‘Barlow Condensed’,sans-serif; font-size:26px; font-weight:700; letter-spacing:2px; line-height:1; }
.doc-logo-text .fx { color:#0F0F0F; }
.doc-logo-text .globe { color:#D0281E; }
.doc-logo-url { font-family:‘Barlow Condensed’,sans-serif; font-size:10px; letter-spacing:3px; color:#C9A84C; text-transform:uppercase; margin-top:2px; }
.page-header-right { text-align:right; }
.page-title-main { font-family:‘Barlow Condensed’,sans-serif; font-size:17px; font-weight:700; letter-spacing:4px; text-transform:uppercase; color:#0F0F0F; }
.page-confidential { font-family:‘Barlow Condensed’,sans-serif; font-size:10px; letter-spacing:3px; color:#C9A84C; text-transform:uppercase; margin-top:2px; }
.accent-bar { height:3px; background:linear-gradient(90deg,#C9A84C,#E8C97A,#C9A84C); }

/* CLIENT HERO */
.client-hero { background:#F8F6F2; padding:22px 40px; border-bottom:1px solid #E0D8CC; display:flex; justify-content:space-between; align-items:center; }
.client-info-left .label { font-size:10px; font-weight:600; letter-spacing:2px; text-transform:uppercase; color:#999; margin-bottom:3px; }
.client-name-display { font-family:‘Cormorant Garamond’,serif; font-size:28px; font-weight:600; color:#0F0F0F; }
.client-meta { font-size:12px; color:#888; margin-top:3px; }
.capital-display { text-align:right; }
.capital-display .label { font-size:10px; font-weight:600; letter-spacing:2px; text-transform:uppercase; color:#999; margin-bottom:3px; }
.capital-amount { font-family:‘Cormorant Garamond’,serif; font-size:40px; font-weight:700; color:#0F0F0F; line-height:1; }
.risk-badge { display:inline-block; background:#0F0F0F; color:#C9A84C; font-family:‘Barlow Condensed’,sans-serif; font-size:11px; font-weight:700; letter-spacing:3px; text-transform:uppercase; padding:5px 12px; margin-top:6px; }
.prepared-by { font-family:‘Barlow Condensed’,sans-serif; font-size:10px; letter-spacing:2px; color:#D0281E; text-transform:uppercase; font-weight:700; margin-top:4px; text-align:right; }

/* MAIN GRID */
.main-grid { padding:24px 40px; display:grid; grid-template-columns:1fr 1fr; gap:20px; }
.panel { border:1px solid #E8E0D0; }
.panel-header { background:#0F0F0F; padding:9px 14px; font-family:‘Barlow Condensed’,sans-serif; font-size:11px; font-weight:700; letter-spacing:3px; text-transform:uppercase; color:#C9A84C; }
.panel-body { padding:14px; }
.data-row { display:flex; justify-content:space-between; align-items:center; padding:6px 0; border-bottom:1px solid #F0EBE0; font-size:12.5px; }
.data-row:last-child { border-bottom:none; }
.data-row .key { color:#666; }
.data-row .val { font-weight:600; color:#1A1A1A; font-family:‘Barlow Condensed’,sans-serif; font-size:14px; }
.data-row .val.highlight { color:#C9A84C; font-size:16px; }
.data-row .val.green { color:#27AE60; }

/* MILESTONES */
.milestones { padding:0 40px 20px; }
.section-title { font-family:‘Barlow Condensed’,sans-serif; font-size:11px; font-weight:700; letter-spacing:3px; text-transform:uppercase; color:#0F0F0F; margin-bottom:10px; padding-bottom:7px; border-bottom:2px solid #C9A84C; display:flex; justify-content:space-between; align-items:center; }
.section-title span.sub { color:#C9A84C; font-size:10px; font-weight:400; }
.milestone-grid { display:grid; grid-template-columns:repeat(5,1fr); gap:8px; }
.milestone-card { background:#F8F6F2; border:1px solid #E8E0D0; border-top:3px solid #C9A84C; padding:10px; text-align:center; }
.milestone-year { font-family:‘Barlow Condensed’,sans-serif; font-size:10px; font-weight:700; letter-spacing:2px; text-transform:uppercase; color:#999; margin-bottom:5px; }
.milestone-val { font-family:‘Cormorant Garamond’,serif; font-size:17px; font-weight:700; color:#0F0F0F; line-height:1; }

/* PROFILE COMPARISON */
.profile-section { padding:0 40px 20px; }
.profile-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:10px; }
.profile-card { border:1px solid #E8E0D0; overflow:hidden; }
.profile-card-header { padding:9px 12px; display:flex; align-items:center; gap:7px; }
.profile-card-header.conservative { background:#2C3E50; }
.profile-card-header.moderate { background:#7D6608; }
.profile-card-header.aggressive { background:#0F0F0F; }
.profile-dot { width:7px; height:7px; border-radius:50%; flex-shrink:0; }
.profile-dot.c { background:#85C1E9; }
.profile-dot.m { background:#F7DC6F; }
.profile-dot.a { background:#C9A84C; }
.profile-name { font-family:‘Barlow Condensed’,sans-serif; font-size:11px; font-weight:700; letter-spacing:2px; text-transform:uppercase; color:#FFF; }
.profile-rate { font-size:10px; color:rgba(255,255,255,0.6); margin-left:auto; font-family:‘Barlow Condensed’,sans-serif; }
.profile-card-body { padding:10px 12px; background:#FAFAF8; }
.profile-row { display:flex; justify-content:space-between; font-size:11.5px; padding:3px 0; border-bottom:1px solid #F0EBE0; }
.profile-row:last-child { border-bottom:none; }
.profile-row .k { color:#888; }
.profile-row .v { font-weight:600; font-family:‘Barlow Condensed’,sans-serif; font-size:13px; }

/* LEVERAGE */
.leverage-section { padding:0 40px 20px; }
.leverage-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:8px; }
.leverage-card { background:#0F0F0F; padding:14px; text-align:center; border-bottom:3px solid #C9A84C; }
.leverage-mult { font-family:‘Barlow Condensed’,sans-serif; font-size:12px; letter-spacing:2px; color:#C9A84C; text-transform:uppercase; margin-bottom:5px; }
.leverage-val { font-family:‘Cormorant Garamond’,serif; font-size:22px; font-weight:700; color:#FFFFFF; }

/* NOTES */
.notes-section { padding:0 40px 20px; }
.notes-box { border:1px solid #E8E0D0; min-height:60px; padding:12px 14px; background:#FAFAF8; font-size:12px; color:#555; line-height:1.6; white-space:pre-wrap; }

/* FOOTER */
.page-footer { background:#0F0F0F; padding:12px 40px; display:flex; justify-content:space-between; align-items:center; margin-top:auto; }
.footer-left { font-size:9px; color:#555; letter-spacing:0.5px; line-height:1.5; max-width:500px; }
.footer-brand { font-family:‘Barlow Condensed’,sans-serif; font-size:15px; font-weight:700; letter-spacing:3px; }
.footer-brand .fx { color:#FFFFFF; }
.footer-brand .globe { color:#D0281E; }
.footer-brand .url { display:block; font-size:10px; font-weight:400; letter-spacing:2px; color:#C9A84C; margin-top:1px; text-align:right; }

/* PRINT */
@media print {
body { background:white !important; }
.sidebar, .placeholder-msg { display:none !important; }
.preview-area { padding:0 !important; background:white !important; }
#pdf-document { display:block !important; }
.pdf-page { box-shadow:none !important; margin:0 !important; page-break-after:always; width:100% !important; }
.generate-btn, .print-btn { display:none !important; }
.wm-text { color:rgba(201,168,76,0.10) !important; -webkit-print-color-adjust:exact; print-color-adjust:exact; }
.wm-sub { color:rgba(201,168,76,0.06) !important; -webkit-print-color-adjust:exact; print-color-adjust:exact; }
.page-header, .page-footer, .panel-header, .risk-badge, .client-hero,
.milestone-card, .leverage-card, .profile-card-header, .accent-bar, .watermark { -webkit-print-color-adjust:exact; print-color-adjust:exact; }
}

/* ── MOBILE RESPONSIVE ─────────────────────────────────────────────── */
@media (max-width: 768px) {
.app { flex-direction: column; }

```
.sidebar {
  width: 100%;
  min-width: unset;
  height: auto;
  position: relative;
  top: 0;
}

.preview-area {
  padding: 16px;
  background: #E8E8E8;
}

.pdf-page {
  width: 100%;
  min-height: auto;
  box-shadow: 0 4px 20px rgba(0,0,0,0.2);
}

.page-header {
  padding: 12px 16px;
  flex-direction: column;
  align-items: flex-start;
  gap: 8px;
}

.doc-logo-text { font-size: 20px; }
.page-title-main { font-size: 12px; letter-spacing: 2px; }
.page-confidential { font-size: 8px; }

.client-hero {
  padding: 14px 16px;
  flex-direction: column;
  gap: 12px;
}

.capital-display { text-align: left; }
.capital-amount { font-size: 30px; }
.client-name-display { font-size: 22px; }

.main-grid {
  padding: 14px 16px;
  grid-template-columns: 1fr;
  gap: 14px;
}

.milestones, .profile-section, .leverage-section, .notes-section {
  padding: 0 16px 16px;
}

.milestone-grid {
  grid-template-columns: repeat(2, 1fr);
  gap: 6px;
}

.milestone-grid .milestone-card:last-child {
  grid-column: span 2;
}

.profile-grid { grid-template-columns: 1fr; }
.leverage-grid { grid-template-columns: 1fr; gap: 6px; }

.page-footer {
  padding: 10px 16px;
  flex-direction: column;
  gap: 6px;
  align-items: flex-start;
}

.footer-left { font-size: 8px; }

.generate-btn, .print-btn {
  margin: 12px 16px 8px;
  width: calc(100% - 32px);
}

.field-row { grid-template-columns: 1fr 1fr; }

/* Mobile PDF button - replaces print */
.mobile-save-btn {
  display: block !important;
}
```

}

@media (min-width: 769px) {
.mobile-save-btn { display: none !important; }
}

.mobile-save-btn {
display: none;
margin: 0 16px 16px;
width: calc(100% - 32px);
background: transparent;
color: var(–gold);
border: 1px solid var(–gold);
font-family: ‘Barlow Condensed’, sans-serif;
font-size: 13px;
font-weight: 600;
letter-spacing: 3px;
text-transform: uppercase;
padding: 13px;
cursor: pointer;
transition: all 0.2s;
}

.mobile-save-btn:hover { background: var(–gold); color: var(–dark); }

</style>
</head>
<body>
<div class="app">

  <!-- SIDEBAR -->

  <div class="sidebar">
    <div class="sidebar-header">
      <img src="data:image/jpeg;base64,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" style="height:36px;display:block;margin-bottom:4px;">
      <div class="tagline">Investment Plan Generator</div>
    </div>

```
<div class="form-section">
  <div class="section-label">A · Client Details</div>
  <div class="field"><label>Client Full Name</label><input type="text" id="clientName" placeholder="e.g. John Smith"></div>
  <div class="field"><label>Account Manager</label><input type="text" id="accountManager" placeholder="Adviser name"></div>
</div>

<div class="form-section">
  <div class="section-label">B · Capital & Strategy</div>
  <div class="field"><label>Starting Capital (USD)</label><input type="number" id="capital" placeholder="e.g. 50000" min="1000"></div>
  <div class="field-row">
    <div class="field"><label>Horizon (months)</label><input type="number" id="horizon" placeholder="24" min="1" max="120" value="24"></div>
    <div class="field"><label>Withdrawal %</label><input type="number" id="withdrawal" placeholder="20" min="0" max="100" value="20"></div>
  </div>
</div>

<div class="form-section">
  <div class="section-label">C · Risk Profile</div>
  <div class="field"><label>Risk Appetite</label>
    <select id="riskProfile">
      <option value="conservative">Conservative · Low Risk · ≈6.5%/month</option>
      <option value="moderate">Moderate · Medium Risk · ≈13.4%/month</option>
      <option value="aggressive" selected>Aggressive · Higher Risk · ≈23.1%/month</option>
    </select>
  </div>
</div>

<div class="form-section">
  <div class="section-label">D · Adviser Notes</div>
  <div class="field"><label>Live Call Notes (optional)</label>
    <textarea id="notes" style="width:100%;background:#1E1E1E;border:1px solid #333;color:#E8E8E8;font-family:'Barlow',sans-serif;font-size:13px;padding:10px 14px;outline:none;resize:vertical;min-height:70px;line-height:1.5;" placeholder="Type notes during client call..."></textarea>
  </div>
</div>

<button class="generate-btn" onclick="generatePlan()">Generate Investment Plan</button>
<button class="print-btn" id="printBtn" onclick="window.print()">⬇ Save / Print PDF</button>
<button class="mobile-save-btn" id="mobileSaveBtn" onclick="mobileSave()">⬇ Save as PDF</button>
```

  </div>

  <!-- PREVIEW -->

  <div class="preview-area" id="previewArea">
    <div class="placeholder-msg" id="placeholderMsg">
      <div class="icon">📋</div>
      <p>Enter client details and generate plan</p>
      <p id="mobileHint" style="font-size:10px;color:#aaa;margin-top:8px;display:none;">On mobile: fill in the form above, hit Generate, then use Save as PDF</p>
    </div>
    <script>if(window.innerWidth <= 768) document.getElementById('mobileHint').style.display='block';</script>
    <div id="pdf-document"></div>
  </div>
</div>

<script>
const PROFILES = {
  conservative: { label:'Conservative', rate:0.003, days:21, maxRisk:0.01, rateLabel:'≈6.5%/month', dotClass:'c', headerClass:'conservative' },
  moderate:     { label:'Moderate',     rate:0.006, days:21, maxRisk:0.02, rateLabel:'≈13.4%/month', dotClass:'m', headerClass:'moderate' },
  aggressive:   { label:'Aggressive',   rate:0.01,  days:21, maxRisk:0.05, rateLabel:'≈23.1%/month', dotClass:'a', headerClass:'aggressive' }
};

function mReturn(r,d){ return Math.pow(1+r,d)-1; }

function calc(cap, profile, months, wPct) {
  const r = mReturn(profile.rate, profile.days);
  const w = wPct/100;
  return {
    r,
    m1Profit: cap*r,
    m1Withdrawal: cap*r*w,
    m1Net: cap*r*(1-w),
    horizonBal: cap*Math.pow(1+r,months),
    totalProfit: cap*Math.pow(1+r,months)-cap,
    roi: (cap*Math.pow(1+r,months)-cap)/cap,
    annReturn: Math.pow(1+r,12)-1,
    yr1: cap*Math.pow(1+r,12),
    yr2: cap*Math.pow(1+r,24),
    yr3: cap*Math.pow(1+r,36),
    yr4: cap*Math.pow(1+r,48),
    yr5: cap*Math.pow(1+r,60),
    yr1W: cap*w*(Math.pow(1+r,12)-1),
    yr2W: cap*Math.pow(1+r,12)*w*(Math.pow(1+r,12)-1)
  };
}

function fmt(n){
  if(Math.abs(n)>=1e9) return '$'+(n/1e9).toFixed(2)+'B';
  if(Math.abs(n)>=1e6) return '$'+(n/1e6).toFixed(2)+'M';
  if(Math.abs(n)>=1e3) return '$'+Math.round(n).toLocaleString();
  return '$'+n.toFixed(2);
}
function fmtPct(n){ return (n*100).toFixed(1)+'%'; }

function buildWatermark(){
  let cells='';
  for(let i=0;i<18;i++) cells+=`<div class="wm-cell"><span class="wm-text">FXGLOBE</span><span class="wm-sub">fxglobe.com</span></div>`;
  return `<div class="watermark"><div class="watermark-grid">${cells}</div></div>`;
}

function generatePlan(){
  const clientName = document.getElementById('clientName').value.trim()||'Client';
  const accountManager = document.getElementById('accountManager').value.trim()||'—';
  const capital = parseFloat(document.getElementById('capital').value)||50000;
  const horizon = parseInt(document.getElementById('horizon').value)||24;
  const wPct = parseFloat(document.getElementById('withdrawal').value)||20;
  const riskKey = document.getElementById('riskProfile').value;
  const notes = document.getElementById('notes').value.trim()||'Type notes during client call...';

  const profile = PROFILES[riskKey];
  const p = calc(capital, profile, horizon, wPct);
  const pC = calc(capital, PROFILES.conservative, horizon, wPct);
  const pM = calc(capital, PROFILES.moderate, horizon, wPct);
  const pA = calc(capital, PROFILES.aggressive, horizon, wPct);

  const today = new Date().toLocaleDateString('en-GB',{day:'2-digit',month:'long',year:'numeric'});
  const dblMonths = Math.ceil(Math.log(2)/Math.log(1+p.r));
  const dblStr = dblMonths>=12 ? Math.floor(dblMonths/12)+'y '+(dblMonths%12)+'m' : dblMonths+' months';

  const html = `
  <div class="pdf-page">
    ${buildWatermark()}
    <div class="page-content">

      <div class="page-header">
        <div style="width:160px;"></div>
        <div class="page-header-right">
          <div class="page-title-main">Investment Plan · Forex Trading Projection</div>
          <div class="page-confidential">Strictly Confidential · fxglobe.com · Not For Distribution</div>
        </div>
      </div>
      <div class="accent-bar"></div>

      <div class="client-hero">
        <div class="client-info-left">
          <div class="label">Prepared for</div>
          <div class="client-name-display">${clientName}</div>
          <div class="client-meta">Date of Plan: ${today} &nbsp;·&nbsp; Account Manager: ${accountManager}</div>
        </div>
        <div class="capital-display">
          <div class="label">Starting Capital</div>
          <div class="capital-amount">${fmt(capital)}</div>
          <div class="risk-badge">${profile.label} Profile</div>
          <div class="prepared-by">FXGlobe.com</div>
        </div>
      </div>

      <div class="main-grid">
        <div class="panel">
          <div class="panel-header">E · Your Personalised Plan Projections &nbsp;<span style="color:#C9A84C;font-size:10px;letter-spacing:2px;font-weight:400;">· fxglobe.com</span></div>
          <div class="panel-body">
            
            <div class="data-row"><span class="key">Monthly Return (est.)</span><span class="val highlight">${fmtPct(p.r)}</span></div>
            <div class="data-row"><span class="key">Month 1 Gross Profit</span><span class="val">${fmt(p.m1Profit)}</span></div>
            <div class="data-row"><span class="key">Month 1 Withdrawal (${wPct}%)</span><span class="val">${fmt(p.m1Withdrawal)}</span></div>
            <div class="data-row"><span class="key">Month 1 Net Retained</span><span class="val green">${fmt(p.m1Net)}</span></div>
            <div class="data-row"><span class="key">Year 1 Projected Profit</span><span class="val">${fmt(p.yr1-capital)}</span></div>
            <div class="data-row"><span class="key">Year 1 Closing Balance</span><span class="val highlight">${fmt(p.yr1)}</span></div>
            <div class="data-row"><span class="key">Annualised Return (est.)</span><span class="val">${fmtPct(p.annReturn)}</span></div>
            <div class="data-row"><span class="key">⏱ Time to Double Capital</span><span class="val">${dblStr}</span></div>
          </div>
        </div>
        <div class="panel">
          <div class="panel-header">Portfolio Overview · At Horizon (${horizon} months) &nbsp;<span style="color:#C9A84C;font-size:10px;letter-spacing:2px;font-weight:400;">· fxglobe.com</span></div>
          <div class="panel-body">
            
            <div class="data-row"><span class="key">Horizon Balance</span><span class="val highlight">${fmt(p.horizonBal)}</span></div>
            <div class="data-row"><span class="key">Total Profit</span><span class="val green">${fmt(p.totalProfit)}</span></div>
            <div class="data-row"><span class="key">Net ROI</span><span class="val">${fmtPct(p.roi)}</span></div>
            <div class="data-row"><span class="key">Year 1 Total Withdrawals</span><span class="val">${fmt(p.yr1W)}</span></div>
            <div class="data-row"><span class="key">Year 2 Total Withdrawals</span><span class="val">${fmt(p.yr2W)}</span></div>
            <div class="data-row"><span class="key">Risk Profile</span><span class="val">${profile.label}</span></div>
            <div class="data-row"><span class="key">Daily Return Target</span><span class="val">${fmtPct(profile.rate)}</span></div>
            <div class="data-row"><span class="key">Max Risk Per Trade</span><span class="val">${fmtPct(profile.maxRisk)}</span></div>
          </div>
        </div>
      </div>

      <div class="milestones">
        <div class="section-title">Balance Milestones — Compounding Journey <span class="sub" style="color:#C9A84C;">· fxglobe.com</span></div>
        <div class="milestone-grid">
          <div class="milestone-card"><div class="milestone-year">End of Year 1</div><div class="milestone-val">${fmt(p.yr1)}</div></div>
          <div class="milestone-card"><div class="milestone-year">End of Year 2</div><div class="milestone-val">${fmt(p.yr2)}</div></div>
          <div class="milestone-card"><div class="milestone-year">End of Year 3</div><div class="milestone-val">${fmt(p.yr3)}</div></div>
          <div class="milestone-card"><div class="milestone-year">End of Year 4</div><div class="milestone-val">${fmt(p.yr4)}</div></div>
          <div class="milestone-card"><div class="milestone-year">End of Year 5</div><div class="milestone-val">${fmt(p.yr5)}</div></div>
        </div>
      </div>

      <div class="profile-section">
        <div class="section-title">Risk Profile Comparison — Same Capital ${fmt(capital)} <span class="sub" style="color:#C9A84C;">· fxglobe.com</span></div>
        <div class="profile-grid">
          ${['conservative','moderate','aggressive'].map(key=>{
            const pr=PROFILES[key]; const pc=calc(capital,pr,horizon,wPct);
            return `<div class="profile-card">
              <div class="profile-card-header ${pr.headerClass}">
                <div class="profile-dot ${pr.dotClass}"></div>
                <span class="profile-name">${pr.label} · <span style="font-size:9px;letter-spacing:1px;color:#C9A84C;opacity:1;">FXGLOBE</span></span>
                <span class="profile-rate">${pr.rateLabel}</span>
              </div>
              <div class="profile-card-body">
                <div class="profile-row"><span class="k">Month 1 Profit</span><span class="v">${fmt(pc.m1Profit)}</span></div>
                <div class="profile-row"><span class="k">Year 1 Balance</span><span class="v">${fmt(pc.yr1)}</span></div>
                <div class="profile-row"><span class="k">At Horizon</span><span class="v">${fmt(pc.horizonBal)}</span></div>
                <div class="profile-row"><span class="k">Monthly Net Payout</span><span class="v">${fmt(pc.m1Net)}</span></div>
              </div>
            </div>`;
          }).join('')}
        </div>
      </div>

      <div class="leverage-section">
        <div class="section-title">The Bigger The Deposit — The Bigger The Returns <span class="sub" style="color:#C9A84C;">· fxglobe.com &nbsp;|&nbsp; At Horizon · ${horizon} months</span></div>
        <div class="leverage-grid">
          <div class="leverage-card"><div class="leverage-mult">Current Deposit</div><div style="font-family:'Barlow Condensed',sans-serif;font-size:9px;letter-spacing:2px;color:#C9A84C;margin-bottom:4px;">FXGLOBE.COM</div><div class="leverage-val">${fmt(p.horizonBal)}</div></div>
          <div class="leverage-card"><div class="leverage-mult">2× Deposit</div><div style="font-size:9px;letter-spacing:2px;color:#C9A84C;font-family:'Barlow Condensed',sans-serif;margin-bottom:4px;">FXGLOBE.COM</div><div class="leverage-val">${fmt(p.horizonBal*2)}</div></div>
          <div class="leverage-card"><div class="leverage-mult">5× Deposit</div><div style="font-size:9px;letter-spacing:2px;color:#C9A84C;font-family:'Barlow Condensed',sans-serif;margin-bottom:4px;">FXGLOBE.COM</div><div class="leverage-val">${fmt(p.horizonBal*5)}</div></div>
        </div>
      </div>

      <div class="notes-section">
        <div class="section-title">F · Adviser Notes — Live Call <span class="sub" style="color:#C9A84C;">· fxglobe.com</span></div>
        <div class="notes-box">${notes}</div>
      </div>

      <div class="page-footer">
        <div class="footer-left">FOR PROFESSIONAL USE ONLY · ILLUSTRATIVE PROJECTIONS · PAST PERFORMANCE DOES NOT GUARANTEE FUTURE RESULTS · This document has been prepared by FXGlobe for the exclusive use of the named recipient. Unauthorised reproduction or use is strictly prohibited. © FXGlobe ${new Date().getFullYear()} · All Rights Reserved · fxglobe.com</div>
        <div class="footer-brand"><span class="fx" style="color:#C9A84C;">FX</span><span class="globe" style="color:#C9A84C;">GLOBE</span><span class="url">fxglobe.com</span></div>
      </div>

    </div>
  </div>`;

  document.getElementById('placeholderMsg').style.display='none';
  document.getElementById('pdf-document').innerHTML=html;
  document.getElementById('pdf-document').style.display='block';
  document.getElementById('printBtn').style.display='block';
  document.getElementById('mobileSaveBtn').style.display='block';
}

function mobileSave() {
  // On mobile, trigger print which gives Save as PDF option
  // Also scroll to top of document first
  document.getElementById('pdf-document').scrollIntoView({behavior: 'smooth'});
  setTimeout(function() {
    window.print();
  }, 500);
}

</script>

</body>
</html>
