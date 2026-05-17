<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Spese">
<title>💶 Spese Mensili</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@300;400;500&family=DM+Sans:wght@400;600;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0d0d14;
    --surface: rgba(255,255,255,0.04);
    --border: rgba(255,255,255,0.08);
    --orange: #FF6B35;
    --teal: #4ECDC4;
    --yellow: #FFEAA7;
    --text: #e8e8e8;
    --muted: #888;
    --dim: #555;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Mono', monospace;
    min-height: 100vh;
    min-height: -webkit-fill-available;
    overflow-x: hidden;
  }
  html { height: -webkit-fill-available; }
  .safe-top { padding-top: max(18px, env(safe-area-inset-top)); }

  .header {
    background: rgba(13,13,20,0.95);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
    padding: 0 16px 14px;
    position: sticky; top: 0; z-index: 100;
    display: flex; align-items: flex-end; justify-content: space-between;
  }
  .header-title { font-size: 9px; letter-spacing: 4px; color: var(--muted); margin-bottom: 2px; }
  .header-name { font-family: 'DM Sans', sans-serif; font-size: 20px; font-weight: 700; color: #fff; }
  .save-dot { font-size: 9px; margin-top: 2px; letter-spacing: 1px; height: 14px; }
  .tab-row { display: flex; gap: 6px; }
  .tab-btn {
    padding: 6px 13px; border-radius: 6px; border: 1px solid;
    cursor: pointer; font-size: 10px; letter-spacing: 2px;
    text-transform: uppercase; font-family: 'DM Mono', monospace; transition: all 0.2s;
  }
  .tab-btn.active { background: var(--orange); border-color: var(--orange); color: #fff; }
  .tab-btn.inactive { background: transparent; border-color: rgba(255,255,255,0.15); color: var(--muted); }

  .content { padding: 16px; }

  .month-row { display: flex; gap: 5px; flex-wrap: wrap; margin-bottom: 14px; }
  .month-btn {
    padding: 5px 9px; border-radius: 4px; border: 1px solid;
    cursor: pointer; font-size: 10px; font-family: 'DM Mono', monospace;
    transition: all 0.15s; min-width: 34px; text-align: center; line-height: 1.3;
  }
  .month-btn.active { border-color: var(--teal); background: rgba(78,205,196,0.12); color: var(--teal); }
  .month-btn.inactive { border-color: rgba(255,255,255,0.1); background: transparent; color: var(--dim); }
  .month-sub { display: block; font-size: 7px; }

  .total-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 13px 16px; margin-bottom: 14px;
    display: flex; justify-content: space-between; align-items: center;
  }
  .total-label { font-size: 10px; color: var(--muted); letter-spacing: 2px; }
  .total-value { font-family: 'DM Sans', sans-serif; font-size: 24px; font-weight: 700; color: var(--orange); }

  /* Category cards - full width for multi-entry */
  .cat-grid { display: flex; flex-direction: column; gap: 9px; }
  .cat-card {
    background: var(--surface); border-radius: 11px; padding: 13px;
    border: 1px solid rgba(255,255,255,0.07); transition: border-color 0.2s;
  }
  .cat-head {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 10px;
  }
  .cat-head-left { display: flex; align-items: center; gap: 6px; }
  .cat-icon { font-size: 17px; }
  .cat-name { font-size: 9px; color: #bbb; letter-spacing: 0.5px; }
  .cat-total { font-family: 'DM Sans', sans-serif; font-size: 15px; font-weight: 700; }

  /* Entries list */
  .entries-list { display: flex; flex-direction: column; gap: 6px; margin-bottom: 8px; }
  .entry-row { display: flex; align-items: center; gap: 6px; }
  .entry-euro { color: var(--dim); font-size: 12px; flex-shrink: 0; }
  .entry-input {
    background: rgba(255,255,255,0.04); border: 1px solid rgba(255,255,255,0.1);
    border-radius: 6px; color: var(--text); font-size: 15px;
    font-family: 'DM Sans', sans-serif; font-weight: 600;
    width: 100%; outline: none; padding: 6px 8px;
    transition: border-color 0.2s, color 0.2s;
    -webkit-appearance: none;
  }
  .entry-input::placeholder { color: var(--dim); font-weight: 400; font-size: 13px; }
  .entry-input:focus { border-color: rgba(255,255,255,0.3); }
  .entry-del {
    background: transparent; border: none; color: var(--dim);
    font-size: 16px; cursor: pointer; padding: 4px; flex-shrink: 0;
    line-height: 1;
  }
  .add-btn {
    background: transparent; border: 1px dashed rgba(255,255,255,0.15);
    border-radius: 6px; color: var(--muted); font-size: 11px;
    font-family: 'DM Mono', monospace; cursor: pointer;
    padding: 5px 10px; width: 100%; text-align: center;
    transition: all 0.15s; letter-spacing: 1px;
  }
  .add-btn:active { background: rgba(255,255,255,0.05); }

  /* Summary */
  .kpi-row { display: grid; grid-template-columns: repeat(3,1fr); gap: 9px; margin-bottom: 16px; }
  .kpi-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 11px; padding: 10px 8px; text-align: center;
  }
  .kpi-label { font-size: 8px; color: var(--muted); letter-spacing: 1px; margin-bottom: 4px; }
  .kpi-value { font-family: 'DM Sans', sans-serif; font-size: 13px; font-weight: 700; }

  .chart-card, .avg-card {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 11px; padding: 14px; margin-bottom: 14px;
  }
  .card-label { font-size: 9px; letter-spacing: 3px; color: var(--muted); margin-bottom: 10px; }
  .bar-row { display: flex; gap: 3px; align-items: flex-end; height: 68px; }
  .bar-col { flex: 1; display: flex; flex-direction: column; align-items: center; gap: 3px; }
  .bar { width: 100%; border-radius: 2px 2px 0 0; min-height: 2px; transition: height 0.3s; }
  .bar-lbl { font-size: 7px; color: var(--dim); }

  .avg-list { display: flex; flex-direction: column; gap: 11px; }
  .avg-top { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 4px; }
  .avg-name { font-size: 12px; color: #ccc; }
  .avg-sub { font-size: 9px; color: var(--dim); margin-left: 5px; }
  .avg-right { text-align: right; flex-shrink: 0; margin-left: 8px; }
  .avg-val { font-family: 'DM Sans', sans-serif; font-size: 13px; font-weight: 700; }
  .avg-unit { font-size: 8px; color: var(--muted); }
  .avg-tot { font-size: 9px; color: var(--dim); }
  .progress-bg { height: 3px; background: rgba(255,255,255,0.05); border-radius: 2px; }
  .progress-fill { height: 100%; border-radius: 2px; transition: width 0.4s; }
  .empty { color: var(--dim); text-align: center; padding: 20px; font-size: 12px; }

  input[type=number]::-webkit-inner-spin-button,
  input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; }
  input[type=number] { -moz-appearance: textfield; }
</style>
</head>
<body>

<div class="header safe-top">
  <div>
    <div class="header-title">TRACCIATORE</div>
    <div class="header-name">💶 Spese Mensili</div>
    <div class="save-dot" id="saveDot"></div>
  </div>
  <div class="tab-row">
    <button class="tab-btn active" id="btnInput" onclick="switchView('input')">Inserisci</button>
    <button class="tab-btn inactive" id="btnSummary" onclick="switchView('summary')">Riepilogo</button>
  </div>
</div>

<div id="viewInput" class="content"></div>
<div id="viewSummary" class="content" style="display:none"></div>

<script>
const CATS = [
  { id:"auto",       label:"Auto",         icon:"🚗", color:"#FF6B35" },
  { id:"benzina",    label:"Benzina",      icon:"⛽", color:"#F7C59F" },
  { id:"spesa",      label:"Spesa",        icon:"🛒", color:"#4ECDC4" },
  { id:"affitto",    label:"Affitto/Mutuo",icon:"🏠", color:"#45B7D1" },
  { id:"bollette",   label:"Bollette",     icon:"💡", color:"#96CEB4" },
  { id:"ristorante", label:"Ristorante",   icon:"🍽️", color:"#FFEAA7" },
  { id:"salute",     label:"Salute",       icon:"💊", color:"#DDA0DD" },
  { id:"abbonamenti",label:"Abbonamenti",  icon:"📱", color:"#98D8C8" },
  { id:"palestra",   label:"Palestra",     icon:"🏋️", color:"#FF8FAB" },
  { id:"sim",        label:"SIM",          icon:"📶", color:"#A8DADC" },
  { id:"apple",      label:"Apple",        icon:"🍎", color:"#C8B6FF" },
  { id:"altro",      label:"Altro",        icon:"📦", color:"#B8B8B8" },
];
const MONTHS = ["Gen","Feb","Mar","Apr","Mag","Giu","Lug","Ago","Set","Ott","Nov","Dic"];

let activeMonth = new Date().getMonth();
let currentView = "input";
let saveTimer = null;

// Data structure: data[month][catId] = ["12.50", "34.00", ...]
function loadData() {
  try {
    const raw = localStorage.getItem("spese-v2");
    if (raw) return JSON.parse(raw);
  } catch(e) {}
  return makeEmpty();
}

function makeEmpty() {
  const d = {};
  for (let m = 0; m < 12; m++) {
    d[m] = {};
    CATS.forEach(c => { d[m][c.id] = [""]; });
  }
  return d;
}

let data = loadData();

// Make sure new categories exist in old data
CATS.forEach(c => {
  for (let m = 0; m < 12; m++) {
    if (!data[m]) data[m] = {};
    if (!data[m][c.id]) data[m][c.id] = [""];
  }
});

function saveData() {
  const dot = document.getElementById("saveDot");
  dot.style.color = "#888";
  dot.textContent = "salvataggio...";
  if (saveTimer) clearTimeout(saveTimer);
  saveTimer = setTimeout(() => {
    try {
      localStorage.setItem("spese-v2", JSON.stringify(data));
      dot.style.color = "#4ECDC4";
      dot.textContent = "✓ salvato";
      setTimeout(() => { dot.textContent = ""; }, 2000);
    } catch(e) {
      dot.style.color = "#FF6B35";
      dot.textContent = "⚠ errore";
    }
  }, 600);
}

function catTotal(m, catId) {
  return (data[m][catId] || [""]).reduce((s, v) => s + (parseFloat(v) || 0), 0);
}

function monthTotal(m) {
  return CATS.reduce((s, c) => s + catTotal(m, c.id), 0);
}

function fmt(n) {
  return n.toLocaleString("it-IT", { minimumFractionDigits: 2, maximumFractionDigits: 2 });
}

// ---- RENDER INPUT ----
function renderInput() {
  const el = document.getElementById("viewInput");
  const totals = Array.from({length:12}, (_, i) => monthTotal(i));

  const monthBtns = MONTHS.map((m, i) => {
    const active = i === activeMonth;
    const t = totals[i];
    return `<button class="month-btn ${active?'active':'inactive'}" onclick="setMonth(${i})">
      ${m}${t > 0 ? `<span class="month-sub">€${Math.round(t)}</span>` : ''}
    </button>`;
  }).join('');

  const catCards = CATS.map(cat => {
    const entries = data[activeMonth][cat.id] || [""];
    const total = catTotal(activeMonth, cat.id);
    const hasVal = total > 0;
    const borderColor = hasVal ? cat.color + "55" : "rgba(255,255,255,0.07)";

    const entryRows = entries.map((val, idx) => `
      <div class="entry-row" id="erow-${cat.id}-${idx}">
        <span class="entry-euro">€</span>
        <input class="entry-input" type="number" inputmode="decimal" placeholder="0,00"
          value="${val}"
          oninput="handleEntry('${cat.id}', ${idx}, this.value)"
          style="${parseFloat(val) > 0 ? `color:${cat.color};border-color:${cat.color}44` : ''}"
        />
        ${entries.length > 1 ? `<button class="entry-del" onclick="removeEntry('${cat.id}', ${idx})">✕</button>` : ''}
      </div>
    `).join('');

    return `<div class="cat-card" id="card-${cat.id}" style="border-color:${borderColor}">
      <div class="cat-head">
        <div class="cat-head-left">
          <span class="cat-icon">${cat.icon}</span>
          <span class="cat-name">${cat.label.toUpperCase()}</span>
        </div>
        <span class="cat-total" id="ctot-${cat.id}" style="color:${hasVal ? cat.color : 'var(--dim)'}">
          ${hasVal ? '€ ' + fmt(total) : '—'}
        </span>
      </div>
      <div class="entries-list" id="elist-${cat.id}">${entryRows}</div>
      <button class="add-btn" onclick="addEntry('${cat.id}')">+ aggiungi importo</button>
    </div>`;
  }).join('');

  el.innerHTML = `
    <div class="month-row">${monthBtns}</div>
    <div class="total-card">
      <span class="total-label">TOTALE ${MONTHS[activeMonth].toUpperCase()}</span>
      <span class="total-value" id="monthTotal">€ ${fmt(totals[activeMonth])}</span>
    </div>
    <div class="cat-grid">${catCards}</div>
    <div style="height:max(20px, env(safe-area-inset-bottom))"></div>
  `;
}

function handleEntry(catId, idx, val) {
  data[activeMonth][catId][idx] = val;
  updateCatDisplay(catId);
  document.getElementById("monthTotal").textContent = "€ " + fmt(monthTotal(activeMonth));
  saveData();
}

function addEntry(catId) {
  data[activeMonth][catId].push("");
  // Re-render just the entries list
  const cat = CATS.find(c => c.id === catId);
  const entries = data[activeMonth][catId];
  const eList = document.getElementById("elist-" + catId);
  eList.innerHTML = entries.map((val, idx) => `
    <div class="entry-row" id="erow-${catId}-${idx}">
      <span class="entry-euro">€</span>
      <input class="entry-input" type="number" inputmode="decimal" placeholder="0,00"
        value="${val}"
        oninput="handleEntry('${catId}', ${idx}, this.value)"
        style="${parseFloat(val) > 0 ? `color:${cat.color};border-color:${cat.color}44` : ''}"
      />
      ${entries.length > 1 ? `<button class="entry-del" onclick="removeEntry('${catId}', ${idx})">✕</button>` : ''}
    </div>
  `).join('');
  // Focus last input
  setTimeout(() => {
    const inputs = eList.querySelectorAll('.entry-input');
    if (inputs.length) inputs[inputs.length-1].focus();
  }, 50);
}

function removeEntry(catId, idx) {
  data[activeMonth][catId].splice(idx, 1);
  if (data[activeMonth][catId].length === 0) data[activeMonth][catId] = [""];
  const cat = CATS.find(c => c.id === catId);
  const entries = data[activeMonth][catId];
  const eList = document.getElementById("elist-" + catId);
  eList.innerHTML = entries.map((val, i) => `
    <div class="entry-row">
      <span class="entry-euro">€</span>
      <input class="entry-input" type="number" inputmode="decimal" placeholder="0,00"
        value="${val}"
        oninput="handleEntry('${catId}', ${i}, this.value)"
        style="${parseFloat(val) > 0 ? `color:${cat.color};border-color:${cat.color}44` : ''}"
      />
      ${entries.length > 1 ? `<button class="entry-del" onclick="removeEntry('${catId}', ${i})">✕</button>` : ''}
    </div>
  `).join('');
  updateCatDisplay(catId);
  document.getElementById("monthTotal").textContent = "€ " + fmt(monthTotal(activeMonth));
  saveData();
}

function updateCatDisplay(catId) {
  const cat = CATS.find(c => c.id === catId);
  const total = catTotal(activeMonth, catId);
  const hasVal = total > 0;
  const card = document.getElementById("card-" + catId);
  const totEl = document.getElementById("ctot-" + catId);
  if (card) card.style.borderColor = hasVal ? cat.color + "55" : "rgba(255,255,255,0.07)";
  if (totEl) {
    totEl.style.color = hasVal ? cat.color : "var(--dim)";
    totEl.textContent = hasVal ? "€ " + fmt(total) : "—";
  }
  // Update input colors
  const entries = data[activeMonth][catId];
  const eList = document.getElementById("elist-" + catId);
  if (eList) {
    eList.querySelectorAll('.entry-input').forEach((inp, i) => {
      const v = parseFloat(entries[i]) || 0;
      inp.style.color = v > 0 ? cat.color : "";
      inp.style.borderColor = v > 0 ? cat.color + "44" : "";
    });
  }
}

function setMonth(i) {
  activeMonth = i;
  renderInput();
}

// ---- RENDER SUMMARY ----
function renderSummary() {
  const el = document.getElementById("viewSummary");
  const totals = Array.from({length:12}, (_, i) => monthTotal(i));
  const nonZeroTotals = totals.filter(v => v > 0);
  const grandAvg = nonZeroTotals.length ? nonZeroTotals.reduce((a,b)=>a+b,0)/nonZeroTotals.length : 0;
  const totalAll = totals.reduce((a,b)=>a+b,0);
  const maxMonth = Math.max(...totals, 1);

  const catAvgs = CATS.map(cat => {
    const vals = Array.from({length:12}, (_, m) => catTotal(m, cat.id));
    const nz = vals.filter(v => v > 0);
    const avg = nz.length ? nz.reduce((a,b)=>a+b,0)/nz.length : 0;
    const total = vals.reduce((a,b)=>a+b,0);
    return { ...cat, avg, total, months: nz.length };
  }).filter(c => c.avg > 0).sort((a,b) => b.avg - a.avg);

  const maxAvg = Math.max(...catAvgs.map(c=>c.avg), 1);

  const bars = MONTHS.map((m, i) => {
    const h = totals[i] > 0 ? (totals[i]/maxMonth)*56 + 4 : 2;
    const bg = totals[i] > 0 ? "linear-gradient(to top,#FF6B35,#F7C59F)" : "rgba(255,255,255,0.05)";
    return `<div class="bar-col">
      <div class="bar" style="height:${h}px;background:${bg}"></div>
      <span class="bar-lbl">${m}</span>
    </div>`;
  }).join('');

  const avgItems = catAvgs.length ? catAvgs.map(cat => `
    <div class="avg-item">
      <div class="avg-top">
        <span class="avg-name">${cat.icon} ${cat.label}<span class="avg-sub">(${cat.months} ${cat.months===1?'mese':'mesi'})</span></span>
        <div class="avg-right">
          <div class="avg-val" style="color:${cat.color}">€ ${fmt(cat.avg)}<span class="avg-unit">/mese</span></div>
          <div class="avg-tot">tot. € ${fmt(cat.total)}</div>
        </div>
      </div>
      <div class="progress-bg">
        <div class="progress-fill" style="width:${(cat.avg/maxAvg)*100}%;background:${cat.color}"></div>
      </div>
    </div>
  `).join('') : `<div class="empty">Inserisci le spese nel tab "Inserisci" per vedere il riepilogo</div>`;

  el.innerHTML = `
    <div class="kpi-row">
      <div class="kpi-card">
        <div class="kpi-label">MEDIA/MESE</div>
        <div class="kpi-value" style="color:#FF6B35">€ ${fmt(grandAvg)}</div>
      </div>
      <div class="kpi-card">
        <div class="kpi-label">TOTALE ANNO</div>
        <div class="kpi-value" style="color:#4ECDC4">€ ${fmt(totalAll)}</div>
      </div>
      <div class="kpi-card">
        <div class="kpi-label">MESE TOP</div>
        <div class="kpi-value" style="color:#FFEAA7">€ ${fmt(Math.max(...totals))}</div>
      </div>
    </div>
    <div class="chart-card">
      <div class="card-label">ANDAMENTO MENSILE</div>
      <div class="bar-row">${bars}</div>
    </div>
    <div class="avg-card">
      <div class="card-label">MEDIA PER CATEGORIA</div>
      <div class="avg-list">${avgItems}</div>
    </div>
    <div style="height:max(20px, env(safe-area-inset-bottom))"></div>
  `;
}

function switchView(v) {
  currentView = v;
  document.getElementById("viewInput").style.display = v === "input" ? "" : "none";
  document.getElementById("viewSummary").style.display = v === "summary" ? "" : "none";
  document.getElementById("btnInput").className = "tab-btn " + (v==="input"?"active":"inactive");
  document.getElementById("btnSummary").className = "tab-btn " + (v==="summary"?"active":"inactive");
  if (v === "summary") renderSummary();
}

renderInput();
</script>
</body>
</html>
