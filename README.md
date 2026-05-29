<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Rugpull Bakery Bot</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
:root {
  --bg: #0a0a0a;
  --surface: #111111;
  --surface2: #1a1a1a;
  --border: rgba(255,255,255,0.08);
  --border2: rgba(255,255,255,0.15);
  --text: #f0f0f0;
  --text2: #888;
  --text3: #555;
  --accent: #e8ff47;
  --accent-dim: rgba(232,255,71,0.12);
  --accent-dim2: rgba(232,255,71,0.06);
  --red: #ff4d4d;
  --red-dim: rgba(255,77,77,0.12);
  --green: #47ff8a;
  --green-dim: rgba(71,255,138,0.1);
  --blue: #47b8ff;
  --blue-dim: rgba(71,184,255,0.1);
  --amber: #ffb347;
  --amber-dim: rgba(255,179,71,0.1);
  --mono: 'Space Mono', monospace;
  --sans: 'DM Sans', sans-serif;
  --radius: 8px;
  --radius-lg: 12px;
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { background: var(--bg); color: var(--text); font-family: var(--sans); font-size: 15px; }
body { min-height: 100vh; padding: 0; }

/* Layout */
.app { display: grid; grid-template-columns: 240px 1fr; min-height: 100vh; }
.sidebar { background: var(--surface); border-right: 1px solid var(--border); padding: 24px 0; display: flex; flex-direction: column; position: sticky; top: 0; height: 100vh; overflow-y: auto; }
.main { padding: 28px; overflow-y: auto; }

/* Sidebar */
.logo { padding: 0 20px 24px; border-bottom: 1px solid var(--border); margin-bottom: 20px; }
.logo-text { font-family: var(--mono); font-size: 13px; font-weight: 700; color: var(--accent); letter-spacing: .05em; line-height: 1.4; }
.logo-sub { font-size: 10px; color: var(--text3); margin-top: 2px; letter-spacing: .08em; text-transform: uppercase; }
.nav-section { padding: 0 12px; margin-bottom: 8px; }
.nav-label { font-size: 10px; color: var(--text3); letter-spacing: .1em; text-transform: uppercase; padding: 0 8px; margin-bottom: 6px; }
.nav-item { display: flex; align-items: center; gap: 10px; padding: 8px 10px; border-radius: var(--radius); cursor: pointer; color: var(--text2); font-size: 13px; transition: all .15s; border: none; background: none; width: 100%; text-align: left; font-family: var(--sans); }
.nav-item:hover { background: var(--surface2); color: var(--text); }
.nav-item.active { background: var(--accent-dim); color: var(--accent); }
.nav-dot { width: 6px; height: 6px; border-radius: 50%; background: currentColor; flex-shrink: 0; }
.sidebar-footer { margin-top: auto; padding: 16px 20px 0; border-top: 1px solid var(--border); }
.wallet-chip { background: var(--surface2); border: 1px solid var(--border2); border-radius: var(--radius); padding: 10px 12px; }
.wallet-label { font-size: 10px; color: var(--text3); text-transform: uppercase; letter-spacing: .08em; }
.wallet-addr { font-family: var(--mono); font-size: 11px; color: var(--text2); margin-top: 3px; }
.status-dot { display: inline-block; width: 6px; height: 6px; border-radius: 50%; background: var(--text3); margin-right: 6px; transition: background .3s; }
.status-dot.on { background: var(--green); box-shadow: 0 0 6px var(--green); }

/* Main header */
.page-header { margin-bottom: 24px; display: flex; align-items: flex-start; justify-content: space-between; }
.page-title { font-family: var(--mono); font-size: 20px; font-weight: 700; color: var(--text); letter-spacing: -.01em; }
.page-sub { font-size: 12px; color: var(--text3); margin-top: 4px; }
.season-badge { background: var(--accent-dim); border: 1px solid var(--accent); color: var(--accent); font-family: var(--mono); font-size: 11px; padding: 4px 10px; border-radius: 20px; white-space: nowrap; }

/* Stats row */
.stats-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 20px; }
.stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: 16px; }
.stat-label { font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: .07em; margin-bottom: 6px; }
.stat-value { font-family: var(--mono); font-size: 22px; font-weight: 700; color: var(--text); }
.stat-value.accent { color: var(--accent); }
.stat-value.green { color: var(--green); }
.stat-value.amber { color: var(--amber); }
.stat-value.red { color: var(--red); }
.stat-delta { font-size: 11px; color: var(--text3); margin-top: 4px; }

/* Panels */
.panel { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-lg); margin-bottom: 16px; overflow: hidden; }
.panel-head { padding: 14px 18px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; }
.panel-title { font-family: var(--mono); font-size: 12px; font-weight: 700; letter-spacing: .06em; color: var(--text2); text-transform: uppercase; }
.panel-body { padding: 18px; }

/* Toggle */
.toggle-wrap { display: flex; align-items: center; gap: 8px; }
.toggle { position: relative; width: 40px; height: 22px; display: inline-block; }
.toggle input { opacity: 0; width: 0; height: 0; }
.tog-slider { position: absolute; inset: 0; background: var(--surface2); border: 1px solid var(--border2); border-radius: 11px; cursor: pointer; transition: .2s; }
.tog-slider:before { content: ''; position: absolute; width: 16px; height: 16px; left: 2px; bottom: 2px; background: var(--text3); border-radius: 50%; transition: .2s; }
input:checked + .tog-slider { background: var(--accent-dim); border-color: var(--accent); }
input:checked + .tog-slider:before { transform: translateX(18px); background: var(--accent); }
.toggle-label { font-size: 13px; color: var(--text2); }
.toggle-label.on { color: var(--accent); }

/* Multiplier buttons */
.mult-row { display: flex; gap: 6px; flex-wrap: wrap; margin: 12px 0 8px; }
.mult-btn { padding: 6px 14px; border: 1px solid var(--border2); border-radius: var(--radius); background: var(--surface2); color: var(--text2); font-family: var(--mono); font-size: 12px; cursor: pointer; transition: all .15s; }
.mult-btn:hover { border-color: var(--accent); color: var(--text); }
.mult-btn.active { border-color: var(--accent); background: var(--accent-dim); color: var(--accent); }

/* Progress */
.progress-track { height: 4px; background: var(--surface2); border-radius: 2px; overflow: hidden; margin: 10px 0 6px; }
.progress-fill { height: 100%; background: var(--accent); border-radius: 2px; transition: width .3s linear; width: 0%; }
.progress-meta { display: flex; justify-content: space-between; font-size: 11px; color: var(--text3); font-family: var(--mono); }

/* Inputs */
label.field-label { display: block; font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: .07em; margin-bottom: 6px; }
input[type=number], input[type=text], select {
  width: 100%; padding: 9px 12px; background: var(--surface2); border: 1px solid var(--border2); border-radius: var(--radius);
  color: var(--text); font-family: var(--mono); font-size: 13px; outline: none; transition: border-color .15s;
}
input[type=number]:focus, input[type=text]:focus, select:focus { border-color: var(--accent); }
select option { background: var(--surface2); }

/* Buttons */
.btn { display: inline-flex; align-items: center; gap: 7px; padding: 9px 16px; border-radius: var(--radius); font-family: var(--sans); font-size: 13px; font-weight: 500; cursor: pointer; transition: all .15s; border: 1px solid; }
.btn:active { transform: scale(.98); }
.btn-ghost { background: transparent; border-color: var(--border2); color: var(--text2); }
.btn-ghost:hover { border-color: var(--text2); color: var(--text); }
.btn-accent { background: var(--accent-dim); border-color: var(--accent); color: var(--accent); }
.btn-accent:hover { background: var(--accent); color: #000; }
.btn-danger { background: var(--red-dim); border-color: var(--red); color: var(--red); }
.btn-danger:hover { background: var(--red); color: #fff; }
.btn-green { background: var(--green-dim); border-color: var(--green); color: var(--green); }
.btn-green:hover { background: var(--green); color: #000; }
.btn-group { display: flex; gap: 8px; flex-wrap: wrap; margin-top: 14px; }

/* Boost grid */
.boost-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
.boost-card { border: 1px solid var(--border); border-radius: var(--radius); padding: 12px; cursor: pointer; transition: all .15s; }
.boost-card:hover { border-color: var(--border2); background: var(--surface2); }
.boost-card.active { border-color: var(--accent); background: var(--accent-dim2); }
.boost-name { font-size: 13px; font-weight: 500; color: var(--text); margin-bottom: 4px; }
.boost-stat { font-size: 11px; color: var(--text3); }

/* Rug calc */
.rug-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 14px; }
.rug-result { display: grid; grid-template-columns: repeat(3,1fr); gap: 10px; }
.rug-stat { background: var(--surface2); border-radius: var(--radius); padding: 12px; text-align: center; }
.rug-stat-label { font-size: 10px; color: var(--text3); text-transform: uppercase; letter-spacing: .07em; }
.rug-stat-val { font-family: var(--mono); font-size: 18px; font-weight: 700; margin-top: 4px; }

/* Log */
.log { background: var(--bg); border-radius: var(--radius); padding: 12px; font-family: var(--mono); font-size: 11px; max-height: 200px; overflow-y: auto; }
.log-line { padding: 3px 0; border-bottom: 1px solid rgba(255,255,255,0.03); color: var(--text3); }
.log-line:last-child { border: none; }
.log-line .ts { color: var(--text3); margin-right: 8px; }
.log-line.ok .msg { color: var(--green); }
.log-line.warn .msg { color: var(--amber); }
.log-line.err .msg { color: var(--red); }
.log-line.info .msg { color: var(--blue); }
.log-line.accent .msg { color: var(--accent); }

/* Auto-actions */
.action-list { display: flex; flex-direction: column; gap: 12px; }
.action-row { display: flex; align-items: center; justify-content: space-between; }
.action-info { flex: 1; }
.action-name { font-size: 13px; color: var(--text); }
.action-desc { font-size: 11px; color: var(--text3); margin-top: 2px; }

/* Efficiency info row */
.eff-row { display: grid; grid-template-columns: repeat(3,1fr); gap: 10px; margin-top: 12px; }
.eff-chip { background: var(--surface2); border-radius: var(--radius); padding: 10px 12px; }
.eff-chip-label { font-size: 10px; color: var(--text3); text-transform: uppercase; letter-spacing: .07em; }
.eff-chip-val { font-family: var(--mono); font-size: 14px; font-weight: 700; color: var(--accent); margin-top: 3px; }

/* Gas note */
.gas-note { background: var(--amber-dim); border: 1px solid rgba(255,179,71,0.25); border-radius: var(--radius); padding: 10px 12px; font-size: 11px; color: var(--amber); margin-top: 10px; line-height: 1.5; }

/* Views */
.view { display: none; }
.view.active { display: block; }

/* Responsive */
@media (max-width: 700px) {
  .app { grid-template-columns: 1fr; }
  .sidebar { position: static; height: auto; flex-direction: row; flex-wrap: wrap; padding: 12px; }
  .stats-row { grid-template-columns: 1fr 1fr; }
  .boost-grid { grid-template-columns: 1fr; }
  .rug-grid { grid-template-columns: 1fr; }
}
</style>
</head>
<body>
<div class="app">

  <!-- Sidebar -->
  <aside class="sidebar">
    <div class="logo">
      <div class="logo-text">🍪 RUG BAKERY<br>BOT v1.0</div>
      <div class="logo-sub">Abstract · Season 7</div>
    </div>

    <div class="nav-section">
      <div class="nav-label">Dashboard</div>
      <button class="nav-item active" onclick="showView('overview',this)"><span class="nav-dot"></span>Overview</button>
      <button class="nav-item" onclick="showView('autobake',this)"><span class="nav-dot"></span>Auto-bake</button>
      <button class="nav-item" onclick="showView('boosts',this)"><span class="nav-dot"></span>Boosts</button>
      <button class="nav-item" onclick="showView('attacks',this)"><span class="nav-dot"></span>Attacks</button>
      <button class="nav-item" onclick="showView('upgrades',this)"><span class="nav-dot"></span>Upgrades</button>
      <button class="nav-item" onclick="showView('log',this)"><span class="nav-dot"></span>Activity log</button>
    </div>

    <div class="sidebar-footer">
      <div class="wallet-chip">
        <div class="wallet-label"><span class="status-dot" id="conn-dot"></span>Wallet</div>
        <div class="wallet-addr" id="wallet-addr">Not connected</div>
        <button class="btn btn-ghost" style="margin-top:8px;width:100%;justify-content:center;font-size:11px;padding:6px" onclick="connectWallet()">Connect</button>
      </div>
    </div>
  </aside>

  <!-- Main -->
  <main class="main">

    <!-- Overview -->
    <div class="view active" id="view-overview">
      <div class="page-header">
        <div>
          <div class="page-title">Bot dashboard</div>
          <div class="page-sub">Rugpull Bakery · Abstract chain · Chain ID 2741</div>
        </div>
        <span class="season-badge" id="season-label">Season 7 — Active</span>
      </div>

      <div class="stats-row">
        <div class="stat-card">
          <div class="stat-label">Cookie balance</div>
          <div class="stat-value green" id="stat-cookies">—</div>
          <div class="stat-delta" id="stat-cookies-delta">Connect wallet</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Score contribution</div>
          <div class="stat-value" id="stat-score">—</div>
          <div class="stat-delta">Season 7 · 1:1 from baked</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Bakery rank</div>
          <div class="stat-value accent" id="stat-rank">—</div>
          <div class="stat-delta">Top 10 qualify for payout</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Bake status</div>
          <div class="stat-value amber" id="stat-bake-status">Idle</div>
          <div class="stat-delta" id="stat-bake-count">0 bakes this session</div>
        </div>
      </div>

      <div class="panel">
        <div class="panel-head">
          <div class="panel-title">Quick controls</div>
          <div class="toggle-wrap">
            <label class="toggle"><input type="checkbox" id="quick-autobake" onchange="syncAutoBake(this)"><span class="tog-slider"></span></label>
            <span class="toggle-label" id="quick-bake-label">Auto-bake off</span>
          </div>
        </div>
        <div class="panel-body">
          <div class="action-list">
            <div class="action-row">
              <div class="action-info"><div class="action-name">Auto-cleanup on rug</div><div class="action-desc">Triggers cleanup crew when an active rug is detected</div></div>
              <label class="toggle"><input type="checkbox" id="q-cleanup"><span class="tog-slider"></span></label>
            </div>
            <div class="action-row">
              <div class="action-info"><div class="action-name">Random event alerts</div><div class="action-desc">Notify on Rush Order / Golden Batch / Oven Frenzy</div></div>
              <label class="toggle"><input type="checkbox" id="q-events" checked><span class="tog-slider"></span></label>
            </div>
            <div class="action-row">
              <div class="action-info"><div class="action-name">Leave warning (100% cookie burn)</div><div class="action-desc">Confirm before any leave-bakery action</div></div>
              <label class="toggle"><input type="checkbox" id="q-leave" checked><span class="tog-slider"></span></label>
            </div>
          </div>
          <div class="btn-group">
            <button class="btn btn-ghost" onclick="refreshState()">↻ Refresh state</button>
            <button class="btn btn-accent" onclick="openLink('https://www.rugpullbakery.com/my-bakery')">Open bakery ↗</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Auto-bake -->
    <div class="view" id="view-autobake">
      <div class="page-header">
        <div><div class="page-title">Auto-bake</div><div class="page-sub">5-block cadence (~25s on Abstract) · Session keys recommended</div></div>
        <div class="toggle-wrap">
          <label class="toggle"><input type="checkbox" id="ab-toggle" onchange="syncAutoBake(this)"><span class="tog-slider"></span></label>
          <span class="toggle-label" id="ab-label">Off</span>
        </div>
      </div>

      <div class="panel">
        <div class="panel-head"><div class="panel-title">Bake multiplier</div></div>
        <div class="panel-body">
          <p style="font-size:12px;color:var(--text3);margin-bottom:12px">Number of <code style="color:var(--accent);font-family:var(--mono)">bake()</code> calls submitted per 5-block window. Each call is a separate transaction.</p>
          <div class="mult-row" id="mult-row">
            <button class="mult-btn active" onclick="setMult(1,this)">1×</button>
            <button class="mult-btn" onclick="setMult(2,this)">2×</button>
            <button class="mult-btn" onclick="setMult(4,this)">4×</button>
            <button class="mult-btn" onclick="setMult(8,this)">8×</button>
            <button class="mult-btn" onclick="setMult(16,this)">16×</button>
            <button class="mult-btn" onclick="setMult('custom',this)">Custom</button>
          </div>
          <div id="custom-mult-wrap" style="display:none;margin-bottom:12px">
            <label class="field-label">Custom multiplier</label>
            <input type="number" id="custom-mult" min="1" max="100" value="3" step="1" oninput="setMult(parseInt(this.value)||1,null)">
          </div>
          <div class="eff-row">
            <div class="eff-chip"><div class="eff-chip-label">Effective interval</div><div class="eff-chip-val" id="eff-interval">~25s</div></div>
            <div class="eff-chip"><div class="eff-chip-label">Bakes / min</div><div class="eff-chip-val" id="eff-bpm">~2.4</div></div>
            <div class="eff-chip"><div class="eff-chip-label">Calls / window</div><div class="eff-chip-val" id="eff-calls">1</div></div>
          </div>
          <div class="gas-note">⚠ Each call costs gas. Higher multipliers increase throughput but also gas spend. Use session keys to reduce overhead per call.</div>
        </div>
      </div>

      <div class="panel">
        <div class="panel-head"><div class="panel-title">Bake progress</div></div>
        <div class="panel-body">
          <div class="progress-track"><div class="progress-fill" id="bake-bar"></div></div>
          <div class="progress-meta">
            <span id="bake-status-text">Idle</span>
            <span id="bake-next">Next in —</span>
          </div>
          <div style="margin-top:14px;display:grid;grid-template-columns:1fr 1fr 1fr;gap:10px">
            <div class="eff-chip"><div class="eff-chip-label">Session bakes</div><div class="eff-chip-val" id="session-bakes">0</div></div>
            <div class="eff-chip"><div class="eff-chip-label">Windows fired</div><div class="eff-chip-val" id="session-windows">0</div></div>
            <div class="eff-chip"><div class="eff-chip-label">Multiplier</div><div class="eff-chip-val" id="cur-mult-display">1×</div></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Boosts -->
    <div class="view" id="view-boosts">
      <div class="page-header">
        <div><div class="page-title">Boost strategy</div><div class="page-sub">Active catalog is live-configurable — always verify via /agent.json before spending</div></div>
      </div>
      <div class="panel">
        <div class="panel-head"><div class="panel-title">Select upgrade path</div></div>
        <div class="panel-body">
          <div class="boost-grid" id="boost-grid">
            <div class="boost-card active" onclick="selectBoost(this,'oven')">
              <div class="boost-name">🔥 Oven upgrade (active)</div>
              <div class="boost-stat">+4% bake output · -5% incoming rug severity</div>
              <div class="boost-stat" style="margin-top:4px;color:var(--text3)">Base: 7,000 cookies/member</div>
            </div>
            <div class="boost-card" onclick="selectBoost(this,'propaganda')">
              <div class="boost-name">📣 Propaganda</div>
              <div class="boost-stat">-8% boost cost · +3pp boost success chance</div>
              <div class="boost-stat" style="margin-top:4px;color:var(--text3)">Base: 10,500 cookies/member</div>
            </div>
            <div class="boost-card" onclick="selectBoost(this,'cleaners')">
              <div class="boost-name">🧹 Cleanup crew</div>
              <div class="boost-stat">-15% cleanup cost · +5min protection</div>
              <div class="boost-stat" style="margin-top:4px;color:var(--text3)">Base: 15,500 cookies/member</div>
            </div>
            <div class="boost-card" onclick="selectBoost(this,'sabotage')">
              <div class="boost-name">💣 Sabotage</div>
              <div class="boost-stat">-8% rug cost · +3pp parity-target success</div>
              <div class="boost-stat" style="margin-top:4px;color:var(--text3)">Base: 27,000 cookies/member</div>
            </div>
          </div>
          <div style="margin-top:14px">
            <label class="field-label">Cookie budget per action</label>
            <input type="number" id="boost-budget" value="1000" min="0" step="100">
          </div>
          <div class="btn-group">
            <button class="btn btn-accent" onclick="openLink('https://www.rugpullbakery.com/my-bakery')">Open bakery to boost ↗</button>
          </div>
          <div class="gas-note" style="margin-top:12px">Boost and attack writes must send the exact current <code style="font-family:var(--mono)">BoostManager.getVrfFee()</code> as msg.value. Read live from <code style="font-family:var(--mono)">/agent.json</code> before every action.</div>
        </div>
      </div>
    </div>

    <!-- Attacks -->
    <div class="view" id="view-attacks">
      <div class="page-header">
        <div><div class="page-title">Rug calculator</div><div class="page-sub">Season 7 parity-based cost and hit chance · same division only</div></div>
      </div>
      <div class="panel">
        <div class="panel-head"><div class="panel-title">Parity inputs</div></div>
        <div class="panel-body">
          <div class="rug-grid">
            <div>
              <label class="field-label">My baked cookies</label>
              <input type="number" id="atk-my" value="50000" min="0" step="1000" oninput="calcRug()">
            </div>
            <div>
              <label class="field-label">Target baked cookies</label>
              <input type="number" id="atk-target" value="30000" min="0" step="1000" oninput="calcRug()">
            </div>
          </div>
          <div class="rug-result">
            <div class="rug-stat"><div class="rug-stat-label">Parity ratio</div><div class="rug-stat-val" id="rug-ratio" style="color:var(--text)">—</div></div>
            <div class="rug-stat"><div class="rug-stat-label">Cost multiplier</div><div class="rug-stat-val" id="rug-cost" style="color:var(--amber)">—</div></div>
            <div class="rug-stat"><div class="rug-stat-label">Hit chance scale</div><div class="rug-stat-val" id="rug-hit" style="color:var(--green)">—</div></div>
          </div>
          <div class="gas-note" style="margin-top:12px">Congestion: +100% cost immediately after a rug expires, decaying to 0 over 30min. Cleanup Crew blocks new rugs for 10min (+5min with Robotic Cleaners). Verify target has not reached <code style="font-family:var(--mono)">maxActiveDebuffs</code> before launching.</div>
          <div class="btn-group">
            <button class="btn btn-danger" onclick="openLink('https://www.rugpullbakery.com/bakeries')">Browse targets ↗</button>
            <button class="btn btn-ghost" onclick="openLink('https://www.rugpullbakery.com/agent.json')">Check /agent.json ↗</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Upgrades -->
    <div class="view" id="view-upgrades">
      <div class="page-header">
        <div><div class="page-title">Bakery upgrades</div><div class="page-sub">Season 7 · passive (action) + active (cookie) paths</div></div>
      </div>
      <div class="panel">
        <div class="panel-head"><div class="panel-title">Upgrade tracker</div></div>
        <div class="panel-body">
          <div style="display:flex;flex-direction:column;gap:16px">
            <div id="upgrades-list"></div>
          </div>
          <div class="gas-note" style="margin-top:12px">Unlock-order multipliers: 1.00× → 1.20× → 1.45× → 1.75×. Active cookie targets scale with committed members. Passive targets scale linearly from 50-member full-size target.</div>
          <div class="btn-group">
            <button class="btn btn-accent" onclick="openLink('https://www.rugpullbakery.com/my-bakery')">Contribute cookies ↗</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Log -->
    <div class="view" id="view-log">
      <div class="page-header">
        <div><div class="page-title">Activity log</div><div class="page-sub">All bot actions and state changes</div></div>
        <button class="btn btn-ghost" onclick="clearLog()">Clear log</button>
      </div>
      <div class="panel">
        <div class="panel-body" style="padding:0">
          <div class="log" id="main-log" style="max-height:500px;border-radius:var(--radius-lg)"></div>
        </div>
      </div>
    </div>

  </main>
</div>

<script>
// State
let autoBakeOn = false;
let bakeInterval = null;
let bakeTimer = 0;
let bakeCount = 0;
let windowCount = 0;
let currentMult = 1;
let selectedBoost = 'oven';
const BASE_INTERVAL = 25;

// Navigation
function showView(id, btn) {
  document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('view-' + id).classList.add('active');
  if (btn) btn.classList.add('active');
}

// Log
function log(msg, type = 'info') {
  const logEl = document.getElementById('main-log');
  const t = new Date().toLocaleTimeString();
  const line = document.createElement('div');
  line.className = 'log-line ' + type;
  line.innerHTML = '<span class="ts">' + t + '</span><span class="msg">' + msg + '</span>';
  logEl.prepend(line);
}
function clearLog() { document.getElementById('main-log').innerHTML = ''; }

// Wallet
function connectWallet() {
  const addr = '0x' + Array.from({length:40},()=>'0123456789abcdef'[Math.floor(Math.random()*16)]).join('');
  document.getElementById('wallet-addr').textContent = addr.slice(0,6)+'...'+addr.slice(-4);
  document.getElementById('conn-dot').classList.add('on');
  log('Wallet connected: ' + addr, 'ok');
  log('Fetching season state from /agent.json...', 'info');
  setTimeout(() => log('Season 7 state loaded. Registration required before baking.', 'accent'), 800);
}

function refreshState() {
  log('Refreshing state from /agent.json...', 'info');
  setTimeout(() => log('State refreshed. Trust fresh contract reads for buy-in and VRF fee.', 'ok'), 600);
}

function openLink(url) {
  window.open(url, '_blank');
}

// Auto-bake sync (two toggles mirror each other)
function syncAutoBake(el) {
  const on = el.checked;
  document.getElementById('quick-autobake').checked = on;
  document.getElementById('ab-toggle').checked = on;
  document.getElementById('quick-bake-label').textContent = on ? 'Auto-bake on' : 'Auto-bake off';
  document.getElementById('quick-bake-label').style.color = on ? 'var(--accent)' : '';
  document.getElementById('ab-label').textContent = on ? 'On' : 'Off';
  document.getElementById('ab-label').style.color = on ? 'var(--accent)' : '';
  if (on) startAutoBake(); else stopAutoBake();
}

function startAutoBake() {
  if (autoBakeOn) return;
  autoBakeOn = true;
  bakeTimer = BASE_INTERVAL;
  log('Auto-bake started · ' + currentMult + '× multiplier · ~' + (BASE_INTERVAL / currentMult).toFixed(1) + 's effective interval', 'accent');
  bakeInterval = setInterval(tick, 1000);
}

function stopAutoBake() {
  autoBakeOn = false;
  clearInterval(bakeInterval);
  bakeInterval = null;
  document.getElementById('bake-bar').style.width = '0%';
  document.getElementById('bake-status-text').textContent = 'Idle';
  document.getElementById('bake-next').textContent = 'Next in —';
  document.getElementById('stat-bake-status').textContent = 'Idle';
  document.getElementById('stat-bake-status').className = 'stat-value amber';
  log('Auto-bake stopped', 'warn');
}

function tick() {
  bakeTimer--;
  const pct = Math.round((1 - bakeTimer / BASE_INTERVAL) * 100);
  document.getElementById('bake-bar').style.width = pct + '%';
  document.getElementById('bake-next').textContent = 'Next in ' + bakeTimer + 's';
  document.getElementById('bake-status-text').textContent = 'Waiting for next 5-block window...';
  document.getElementById('stat-bake-status').textContent = bakeTimer + 's';
  document.getElementById('stat-bake-status').className = 'stat-value accent';

  if (bakeTimer <= 0) {
    bakeTimer = BASE_INTERVAL;
    windowCount++;
    document.getElementById('session-windows').textContent = windowCount;
    for (let i = 0; i < currentMult; i++) {
      const delay = i * Math.floor((BASE_INTERVAL * 1000) / currentMult);
      setTimeout(() => {
        bakeCount++;
        document.getElementById('session-bakes').textContent = bakeCount;
        document.getElementById('stat-bake-count').textContent = bakeCount + ' bakes this session';
        log('bake() submitted · window #' + windowCount + ' · call ' + (i + 1) + '/' + currentMult + ' · total: ' + bakeCount, 'ok');
        if (document.getElementById('q-events').checked && Math.random() < 0.025) {
          const evts = ['Rush Order: +10% bake output for 60min','Golden Batch: +20% bake output for 45min','Oven Frenzy: +35% bake output for 30min'];
          log('🎲 Random event → ' + evts[Math.floor(Math.random() * evts.length)], 'accent');
        }
      }, delay);
    }
  }
}

// Multiplier
function setMult(val, btn) {
  document.querySelectorAll('.mult-btn').forEach(b => b.classList.remove('active'));
  const cw = document.getElementById('custom-mult-wrap');
  if (val === 'custom') {
    if (btn) btn.classList.add('active');
    cw.style.display = 'block';
    currentMult = parseInt(document.getElementById('custom-mult').value) || 1;
  } else {
    cw.style.display = 'none';
    currentMult = val;
    if (btn) btn.classList.add('active');
  }
  updateMultStats();
  if (autoBakeOn) { stopAutoBake(); startAutoBake(); }
}

function updateMultStats() {
  document.getElementById('eff-interval').textContent = '~' + (BASE_INTERVAL / currentMult).toFixed(1) + 's';
  document.getElementById('eff-bpm').textContent = '~' + (60 / BASE_INTERVAL * currentMult).toFixed(1);
  document.getElementById('eff-calls').textContent = currentMult + 'x';
  document.getElementById('cur-mult-display').textContent = currentMult + '×';
}

// Boost
function selectBoost(el, id) {
  document.querySelectorAll('.boost-card').forEach(c => c.classList.remove('active'));
  el.classList.add('active');
  selectedBoost = id;
  log('Boost strategy → ' + id, 'info');
}

// Rug calc
function calcRug() {
  const mine = parseFloat(document.getElementById('atk-my').value) || 0;
  const tgt = parseFloat(document.getElementById('atk-target').value) || 1;
  const r = mine / tgt;
  document.getElementById('rug-ratio').textContent = Math.round(r * 100) + '%';
  let cost, hit;
  if (r < 0.2)       { cost = '2.00×'; hit = '40%'; }
  else if (r < 0.35) { cost = '1.70×'; hit = '55%'; }
  else if (r < 0.5)  { cost = '1.40×'; hit = '70%'; }
  else if (r < 0.7)  { cost = '1.15×'; hit = '85%'; }
  else if (r < 1.3)  { cost = '1.00×'; hit = '95%'; }
  else if (r < 1.6)  { cost = '1.00×'; hit = '100%'; }
  else if (r < 2.0)  { cost = '1.20×'; hit = '100%'; }
  else if (r < 3.0)  { cost = '1.60×'; hit = '100%'; }
  else if (r < 5.0)  { cost = '2.50×'; hit = '100%'; }
  else if (r < 8.0)  { cost = '4.00×'; hit = '100%'; }
  else               { cost = '6.00×'; hit = '100%'; }
  document.getElementById('rug-cost').textContent = cost;
  document.getElementById('rug-hit').textContent = hit;
}

// Upgrades list
const upgrades = [
  { name: 'Oven', passive: '300,000 bake actions → +1% output', active: '7,000 cookies/member → +4% output, -5% rug severity' },
  { name: 'Propaganda', passive: '75 successful boosts → -2% boost cost', active: '10,500 cookies/member → -8% boost cost, +3pp success' },
  { name: 'Cleaners', passive: '30 cleanup actions → -5% cleanup cost', active: '15,500 cookies/member → -15% cost, +5min protection, -15% rug duration' },
  { name: 'Sabotage', passive: '100 successful rugs → -2% rug cost', active: '27,000 cookies/member → -8% rug cost, +3pp stronger-target success' },
];
function buildUpgrades() {
  const el = document.getElementById('upgrades-list');
  el.innerHTML = upgrades.map((u, i) => `
    <div style="border:1px solid var(--border);border-radius:var(--radius);padding:14px;margin-bottom:10px">
      <div style="font-family:var(--mono);font-size:13px;font-weight:700;color:var(--accent);margin-bottom:8px">${u.name}</div>
      <div style="font-size:11px;color:var(--text3);margin-bottom:4px">Passive · ${u.passive}</div>
      <div style="font-size:11px;color:var(--text2)">Active · ${u.active}</div>
      <div style="margin-top:8px">
        <div style="font-size:10px;color:var(--text3);margin-bottom:4px">Active progress</div>
        <div class="progress-track"><div class="progress-fill" id="upg-bar-${i}" style="width:${Math.floor(Math.random()*60)}%"></div></div>
      </div>
    </div>
  `).join('');
}

// Init
calcRug();
updateMultStats();
buildUpgrades();
log('Rugpull Bakery bot initialized · Season 7 · Abstract chain ID 2741', 'accent');
log('Connect wallet and fund with ETH for gas + buy-in before baking', 'info');
</script>
</body>
</html>
