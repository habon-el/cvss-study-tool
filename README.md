[index.html](https://github.com/user-attachments/files/27293862/index.html)



[README.md](https://github.com/user-attachments/files/27293902/README.md)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>CVSS Study Tool — CySA+</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500&family=IBM+Plex+Sans:wght@400;500;600&display=swap" rel="stylesheet" />
<style>
:root {
  --bg:       #0d0d0f;
  --surface:  #13131a;
  --surface2: #1a1a24;
  --border:   #2a2a3a;
  --text:     #e2e2f0;
  --muted:    #6a6a90;
  --accent:   #5b8dee;
  --font:     'IBM Plex Sans', sans-serif;
  --mono:     'IBM Plex Mono', monospace;
}
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body { background: var(--bg); color: var(--text); font-family: var(--font); min-height: 100vh; }

/* ── layout ── */
.shell { display: grid; grid-template-columns: 220px 1fr; min-height: 100vh; }
.sidebar { background: var(--surface); border-right: 1px solid var(--border); padding: 1.5rem 1rem; position: sticky; top: 0; height: 100vh; display: flex; flex-direction: column; gap: .25rem; }
.sidebar-logo { display: flex; align-items: center; gap: 10px; padding: .5rem .75rem 1.5rem; }
.logo-icon { width: 32px; height: 32px; background: var(--accent); border-radius: 8px; display: flex; align-items: center; justify-content: center; }
.logo-icon svg { width: 18px; height: 18px; fill: none; stroke: #fff; stroke-width: 2; }
.logo-text { font-size: 14px; font-weight: 600; line-height: 1.2; }
.logo-sub { font-size: 11px; color: var(--muted); }
.nav-item { display: flex; align-items: center; gap: 10px; padding: 9px 12px; border-radius: 8px; cursor: pointer; font-size: 13px; color: var(--muted); transition: all .15s; border: none; background: none; width: 100%; text-align: left; font-family: var(--font); }
.nav-item:hover { background: var(--surface2); color: var(--text); }
.nav-item.active { background: var(--surface2); color: var(--text); }
.nav-item svg { width: 16px; height: 16px; flex-shrink: 0; }
.nav-section { font-size: 10px; text-transform: uppercase; letter-spacing: .1em; color: var(--muted); padding: 1rem .75rem .4rem; font-weight: 600; }
.sidebar-bottom { margin-top: auto; padding-top: 1rem; border-top: 1px solid var(--border); }
.vuln-count { font-size: 12px; color: var(--muted); padding: .5rem .75rem; }
.vuln-count span { color: var(--accent); font-weight: 600; font-family: var(--mono); }

.content { padding: 2rem 2.5rem; max-width: 900px; }
.page { display: none; }
.page.active { display: block; }
.page-title { font-size: 22px; font-weight: 600; margin-bottom: .25rem; }
.page-sub { font-size: 14px; color: var(--muted); margin-bottom: 2rem; }

/* ── cards ── */
.card { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 1.25rem; margin-bottom: 1rem; }
.card-title { font-size: 13px; font-weight: 600; text-transform: uppercase; letter-spacing: .06em; color: var(--muted); margin-bottom: 1rem; }

/* ── form elements ── */
label { font-size: 12px; color: var(--muted); display: block; margin-bottom: 5px; font-weight: 500; text-transform: uppercase; letter-spacing: .05em; }
input[type=text], input[type=number], textarea, select {
  width: 100%; background: var(--surface2); border: 1px solid var(--border);
  border-radius: 8px; padding: 9px 12px; font-family: var(--font); font-size: 13px;
  color: var(--text); transition: border-color .15s;
}
input:focus, textarea:focus, select:focus { outline: none; border-color: var(--accent); }
textarea { resize: vertical; }
select { cursor: pointer; }
.form-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.form-grid-3 { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 12px; }
.form-full { grid-column: 1 / -1; }
.field { margin-bottom: 0; }

/* ── metric selector buttons ── */
.metric-group { display: flex; gap: 6px; flex-wrap: wrap; }
.metric-btn { background: var(--surface2); border: 1px solid var(--border); border-radius: 6px; color: var(--muted); font-size: 12px; font-family: var(--mono); padding: 5px 10px; cursor: pointer; transition: all .15s; }
.metric-btn:hover { border-color: var(--accent); color: var(--text); }
.metric-btn.selected { background: var(--accent); border-color: var(--accent); color: #fff; }

/* ── score display ── */
.score-hero { display: flex; align-items: center; gap: 1.5rem; padding: 1.5rem; background: var(--surface); border: 1px solid var(--border); border-radius: 12px; margin-bottom: 1rem; }
.score-big { font-size: 64px; font-weight: 600; font-family: var(--mono); line-height: 1; }
.score-info { flex: 1; }
.score-label { font-size: 12px; color: var(--muted); text-transform: uppercase; letter-spacing: .06em; margin-bottom: 6px; }
.sev-badge { display: inline-block; padding: 5px 16px; border-radius: 20px; font-size: 13px; font-weight: 600; text-transform: uppercase; letter-spacing: .06em; margin-bottom: 8px; }
.sev-none     { background: #1e1e2a; color: #8888aa; }
.sev-low      { background: #0d2e1c; color: #44cc88; }
.sev-medium   { background: #2e2405; color: #e8a820; }
.sev-high     { background: #2e1205; color: #ff7733; }
.sev-critical { background: #2e0505; color: #ff4444; }

.score-bar-wrap { background: var(--surface2); border-radius: 20px; height: 6px; width: 100%; overflow: hidden; }
.score-bar { height: 100%; border-radius: 20px; transition: width .5s ease, background .5s ease; }

.metrics-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 1rem; }
.metric-card { background: var(--surface2); border-radius: 10px; padding: 1rem; }
.metric-lbl { font-size: 11px; color: var(--muted); text-transform: uppercase; letter-spacing: .06em; margin-bottom: 4px; }
.metric-val { font-size: 22px; font-weight: 600; font-family: var(--mono); }

/* ── steps ── */
.steps-box { background: var(--surface2); border-radius: 10px; padding: 1rem 1.25rem; }
.step-row { display: flex; gap: 12px; padding: 5px 0; font-size: 13px; border-bottom: 1px solid var(--border); }
.step-row:last-child { border-bottom: none; }
.step-key { font-family: var(--mono); color: var(--accent); min-width: 110px; font-size: 12px; }
.step-val { color: var(--muted); flex: 1; font-family: var(--mono); font-size: 12px; }
.step-result { font-weight: 600; color: var(--text); font-family: var(--mono); font-size: 12px; }

/* ── vector display ── */
.vector-box { font-family: var(--mono); font-size: 13px; background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 10px 14px; color: var(--accent); word-break: break-all; margin-bottom: 1rem; cursor: pointer; transition: border-color .15s; position: relative; }
.vector-box:hover { border-color: var(--accent); }
.copy-hint { position: absolute; right: 10px; top: 50%; transform: translateY(-50%); font-size: 11px; color: var(--muted); }
.copy-hint.copied { color: #44cc88; }

/* ── buttons ── */
.btn { display: inline-flex; align-items: center; gap: 8px; padding: 10px 20px; border-radius: 8px; font-family: var(--font); font-size: 13px; font-weight: 500; cursor: pointer; transition: all .15s; border: none; }
.btn-primary { background: var(--accent); color: #fff; }
.btn-primary:hover { opacity: .88; }
.btn-ghost { background: none; border: 1px solid var(--border); color: var(--muted); }
.btn-ghost:hover { color: var(--text); border-color: var(--accent); }
.btn-danger { background: none; border: 1px solid #3a1515; color: #ff5555; }
.btn-danger:hover { background: #2e0808; }
.btn-row { display: flex; gap: 10px; margin-top: 1.25rem; flex-wrap: wrap; }

/* ── vuln library ── */
.vuln-item { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: .9rem 1rem; margin-bottom: 8px; display: flex; align-items: center; gap: 12px; cursor: pointer; transition: all .15s; border-left: 3px solid transparent; }
.vuln-item:hover { border-color: var(--border); border-left-color: var(--accent); background: var(--surface2); }
.vuln-score-badge { font-size: 18px; font-weight: 700; font-family: var(--mono); min-width: 42px; }
.vuln-info { flex: 1; min-width: 0; }
.vuln-name { font-size: 14px; font-weight: 500; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; margin-bottom: 3px; }
.vuln-meta { font-size: 12px; color: var(--muted); font-family: var(--mono); }
.vuln-actions { display: flex; gap: 6px; }
.icon-btn { background: none; border: 1px solid var(--border); color: var(--muted); border-radius: 6px; padding: 5px 8px; cursor: pointer; font-size: 12px; transition: all .15s; }
.icon-btn:hover { color: var(--text); border-color: var(--accent); }
.icon-btn.del:hover { color: #ff5555; border-color: #ff5555; }
.empty-lib { text-align: center; padding: 3rem 1rem; color: var(--muted); }
.empty-lib svg { margin: 0 auto 1rem; opacity: .3; display: block; }

/* ── cheatsheet ── */
.cheat-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.cheat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 1rem; }
.cheat-card h3 { font-size: 13px; font-weight: 600; margin-bottom: .75rem; color: var(--accent); }
.cheat-table { width: 100%; border-collapse: collapse; font-size: 12px; }
.cheat-table td { padding: 4px 6px; border-bottom: 1px solid var(--border); }
.cheat-table tr:last-child td { border-bottom: none; }
.cheat-table td:first-child { color: var(--muted); width: 40%; }
.cheat-table td:last-child { font-family: var(--mono); color: var(--text); }
.formula-box { background: var(--surface2); border-radius: 8px; padding: .75rem 1rem; font-family: var(--mono); font-size: 12px; color: var(--accent); margin-bottom: 8px; line-height: 1.8; }

/* ── toast ── */
.toast { position: fixed; bottom: 2rem; right: 2rem; background: #1a2e1a; border: 1px solid #44cc88; color: #44cc88; padding: 10px 18px; border-radius: 8px; font-size: 13px; opacity: 0; transition: opacity .3s; pointer-events: none; z-index: 999; }
.toast.show { opacity: 1; }

/* ── responsive ── */
@media (max-width: 700px) {
  .shell { grid-template-columns: 1fr; }
  .sidebar { display: none; }
  .content { padding: 1.25rem; }
  .form-grid, .form-grid-3, .metrics-row, .cheat-grid { grid-template-columns: 1fr; }
  .form-full { grid-column: 1; }
}
</style>
</head>
<body>

<div class="shell">
  <!-- ── Sidebar ── -->
  <aside class="sidebar">
    <div class="sidebar-logo">
      <div class="logo-icon">
        <svg viewBox="0 0 24 24"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>
      </div>
      <div>
        <div class="logo-text">CVSS Tool</div>
        <div class="logo-sub">CySA+ Study</div>
      </div>
    </div>

    <div class="nav-section">Tools</div>
    <button class="nav-item active" onclick="showPage('calculator', this)">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="4" y="2" width="16" height="20" rx="2"/><line x1="8" y1="6" x2="16" y2="6"/><line x1="8" y1="10" x2="16" y2="10"/><line x1="8" y1="14" x2="12" y2="14"/></svg>
      Calculator
    </button>
    <button class="nav-item" onclick="showPage('library', this)">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"/><path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"/></svg>
      My Vulnerabilities
    </button>
    <button class="nav-item" onclick="showPage('compare', this)">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/></svg>
      Compare Scans
    </button>

    <div class="nav-section">Learn</div>
    <button class="nav-item" onclick="showPage('cheatsheet', this)">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><line x1="12" y1="8" x2="12" y2="12"/><line x1="12" y1="16" x2="12.01" y2="16"/></svg>
      CVSS Cheat Sheet
    </button>

    <div class="sidebar-bottom">
      <div class="vuln-count">Saved: <span id="savedCount">0</span> vulns</div>
    </div>
  </aside>

  <!-- ── Content ── -->
  <main class="content">

    <!-- CALCULATOR PAGE -->
    <div class="page active" id="page-calculator">
      <div class="page-title">CVSS v3.1 Calculator</div>
      <div class="page-sub">Fill in the metrics — scores update live. Save to your library when done.</div>

      <div class="card">
        <div class="card-title">Vulnerability info</div>
        <div class="form-grid">
          <div class="field form-full">
            <label>Vulnerability name</label>
            <input type="text" id="vulnName" placeholder="e.g. SSL Certificate Cannot Be Trusted" />
          </div>
          <div class="field">
            <label>Host / IP</label>
            <input type="text" id="vulnHost" placeholder="192.168.1.1" />
          </div>
          <div class="field">
            <label>Port</label>
            <input type="text" id="vulnPort" placeholder="443" />
          </div>
          <div class="field form-full">
            <label>Notes</label>
            <textarea id="vulnNotes" rows="2" placeholder="Context, affected systems, remediation notes…"></textarea>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-title">Or paste a CVSS vector string</div>
        <div style="display:flex;gap:8px">
          <input type="text" id="vectorInput" placeholder="CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H" style="font-family:var(--mono);font-size:12px" />
          <button class="btn btn-ghost" onclick="parseVector()" style="white-space:nowrap">Parse</button>
        </div>
      </div>

      <div class="card">
        <div class="card-title">Exploitability metrics</div>
        <div class="form-grid" style="margin-bottom:1rem">
          <div class="field">
            <label>Attack Vector (AV)</label>
            <div class="metric-group" id="mg-AV">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('AV','N',this)">N — Network</button>
              <button class="metric-btn" data-val="A" onclick="setMetric('AV','A',this)">A — Adjacent</button>
              <button class="metric-btn" data-val="L" onclick="setMetric('AV','L',this)">L — Local</button>
              <button class="metric-btn" data-val="P" onclick="setMetric('AV','P',this)">P — Physical</button>
            </div>
          </div>
          <div class="field">
            <label>Attack Complexity (AC)</label>
            <div class="metric-group" id="mg-AC">
              <button class="metric-btn selected" data-val="L" onclick="setMetric('AC','L',this)">L — Low</button>
              <button class="metric-btn" data-val="H" onclick="setMetric('AC','H',this)">H — High</button>
            </div>
          </div>
          <div class="field">
            <label>Privileges Required (PR)</label>
            <div class="metric-group" id="mg-PR">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('PR','N',this)">N — None</button>
              <button class="metric-btn" data-val="L" onclick="setMetric('PR','L',this)">L — Low</button>
              <button class="metric-btn" data-val="H" onclick="setMetric('PR','H',this)">H — High</button>
            </div>
          </div>
          <div class="field">
            <label>User Interaction (UI)</label>
            <div class="metric-group" id="mg-UI">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('UI','N',this)">N — None</button>
              <button class="metric-btn" data-val="R" onclick="setMetric('UI','R',this)">R — Required</button>
            </div>
          </div>
        </div>
        <div class="field">
          <label>Scope (S)</label>
          <div class="metric-group" id="mg-S">
            <button class="metric-btn selected" data-val="U" onclick="setMetric('S','U',this)">U — Unchanged</button>
            <button class="metric-btn" data-val="C" onclick="setMetric('S','C',this)">C — Changed</button>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-title">Impact metrics (CIA triad)</div>
        <div class="form-grid-3">
          <div class="field">
            <label>Confidentiality (C)</label>
            <div class="metric-group" id="mg-C">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('C','N',this)">N</button>
              <button class="metric-btn" data-val="L" onclick="setMetric('C','L',this)">L</button>
              <button class="metric-btn" data-val="H" onclick="setMetric('C','H',this)">H</button>
            </div>
          </div>
          <div class="field">
            <label>Integrity (I)</label>
            <div class="metric-group" id="mg-I">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('I','N',this)">N</button>
              <button class="metric-btn" data-val="L" onclick="setMetric('I','L',this)">L</button>
              <button class="metric-btn" data-val="H" onclick="setMetric('I','H',this)">H</button>
            </div>
          </div>
          <div class="field">
            <label>Availability (A)</label>
            <div class="metric-group" id="mg-A">
              <button class="metric-btn selected" data-val="N" onclick="setMetric('A','N',this)">N</button>
              <button class="metric-btn" data-val="L" onclick="setMetric('A','L',this)">L</button>
              <button class="metric-btn" data-val="H" onclick="setMetric('A','H',this)">H</button>
            </div>
          </div>
        </div>
      </div>

      <!-- Live Result -->
      <div class="score-hero">
        <div class="score-big" id="bigScore">0.0</div>
        <div class="score-info">
          <div class="score-label">CVSS v3.1 Base Score</div>
          <span class="sev-badge sev-none" id="sevBadge">None</span>
          <div class="score-bar-wrap">
            <div class="score-bar" id="scoreBar" style="width:0%;background:#8888aa"></div>
          </div>
        </div>
      </div>

      <div class="metrics-row">
        <div class="metric-card"><div class="metric-lbl">ISS</div><div class="metric-val" id="issOut">0.0000</div></div>
        <div class="metric-card"><div class="metric-lbl">Impact score</div><div class="metric-val" id="impactOut">0.0000</div></div>
        <div class="metric-card"><div class="metric-lbl">Exploitability</div><div class="metric-val" id="exploitOut">0.0000</div></div>
      </div>

      <div class="card-title" style="margin-bottom:.5rem">Step-by-step calculation</div>
      <div class="steps-box" id="stepsBox"></div>

      <div style="margin-top:1rem">
        <div class="card-title" style="margin-bottom:.5rem">CVSS vector string <span style="font-weight:400;color:var(--muted)">(click to copy)</span></div>
        <div class="vector-box" id="vectorOut" onclick="copyVector()">
          CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:N/I:N/A:N
          <span class="copy-hint" id="copyHint">click to copy</span>
        </div>
      </div>

      <div class="btn-row">
        <button class="btn btn-primary" onclick="saveVuln()">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M19 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11l5 5v11a2 2 0 0 1-2 2z"/><polyline points="17 21 17 13 7 13 7 21"/><polyline points="7 3 7 8 15 8"/></svg>
          Save to Library
        </button>
        <button class="btn btn-ghost" onclick="resetCalc()">Reset</button>
      </div>
    </div>

    <!-- LIBRARY PAGE -->
    <div class="page" id="page-library">
      <div class="page-title">My Vulnerabilities</div>
      <div class="page-sub">All saved vulnerabilities — sorted by CVSS score. Click any to load it back into the calculator.</div>

      <div style="display:flex;gap:10px;margin-bottom:1.5rem;flex-wrap:wrap">
        <button class="btn btn-ghost" onclick="exportCSV()">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>
          Export CSV
        </button>
        <button class="btn btn-danger" onclick="clearAll()">Clear All</button>
      </div>

      <div id="libList"></div>
    </div>

    <!-- COMPARE PAGE -->
    <div class="page" id="page-compare">
      <div class="page-title">Compare Two Scans</div>
      <div class="page-sub">Select saved vulnerabilities for each scan — get a priority recommendation.</div>

      <div style="display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:1.5rem">
        <div class="card">
          <div class="card-title" style="color:#5b8dee">Scan A</div>
          <div class="field" style="margin-bottom:10px">
            <label>Scan name</label>
            <input type="text" id="scanNameA" placeholder="e.g. Web Server" value="Scan A" />
          </div>
          <div id="compareListA"></div>
          <button class="btn btn-ghost" style="width:100%;margin-top:8px;justify-content:center" onclick="addCompareRow('A')">+ Add vulnerability</button>
        </div>
        <div class="card">
          <div class="card-title" style="color:#e87733">Scan B</div>
          <div class="field" style="margin-bottom:10px">
            <label>Scan name</label>
            <input type="text" id="scanNameB" placeholder="e.g. Database" value="Scan B" />
          </div>
          <div id="compareListB"></div>
          <button class="btn btn-ghost" style="width:100%;margin-top:8px;justify-content:center" onclick="addCompareRow('B')">+ Add vulnerability</button>
        </div>
      </div>

      <button class="btn btn-primary" style="width:100%;justify-content:center;padding:12px" onclick="runCompare()">
        Analyze and recommend priority
      </button>

      <div id="compareResult" style="margin-top:1.5rem"></div>
    </div>

    <!-- CHEAT SHEET PAGE -->
    <div class="page" id="page-cheatsheet">
      <div class="page-title">CVSS v3.1 Cheat Sheet</div>
      <div class="page-sub">Quick reference for CySA+ exam — all metric values and formulas.</div>

      <div class="card" style="margin-bottom:1.5rem">
        <div class="card-title">Formulas</div>
        <div class="formula-box">ISS = 1 − (1−C) × (1−I) × (1−A)</div>
        <div class="formula-box">Impact (Scope Unchanged) = 6.42 × ISS
Impact (Scope Changed)   = 7.52×(ISS−0.029) − 3.25×(ISS−0.02)^15</div>
        <div class="formula-box">Exploitability = 8.22 × AV × AC × PR × UI</div>
        <div class="formula-box">Base Score (S:U) = roundUp( min(Impact + Exploit, 10) )
Base Score (S:C) = roundUp( min(1.08×(Impact + Exploit), 10) )</div>
        <div style="margin-top:.75rem;padding:.75rem 1rem;background:var(--surface2);border-radius:8px;font-size:13px;color:var(--muted)">
          Severity: None=0 &nbsp;|&nbsp; Low=0.1–3.9 &nbsp;|&nbsp; Medium=4.0–6.9 &nbsp;|&nbsp; High=7.0–8.9 &nbsp;|&nbsp; Critical=9.0–10.0
        </div>
      </div>

      <div class="cheat-grid">
        <div class="cheat-card">
          <h3>Attack Vector (AV)</h3>
          <table class="cheat-table">
            <tr><td>Network (N)</td><td>0.85 — remotely exploitable</td></tr>
            <tr><td>Adjacent (A)</td><td>0.62 — same network</td></tr>
            <tr><td>Local (L)</td><td>0.55 — local access needed</td></tr>
            <tr><td>Physical (P)</td><td>0.20 — physical access needed</td></tr>
          </table>
        </div>
        <div class="cheat-card">
          <h3>Attack Complexity (AC)</h3>
          <table class="cheat-table">
            <tr><td>Low (L)</td><td>0.77 — no special conditions</td></tr>
            <tr><td>High (H)</td><td>0.44 — specific conditions required</td></tr>
          </table>
          <h3 style="margin-top:1rem">User Interaction (UI)</h3>
          <table class="cheat-table">
            <tr><td>None (N)</td><td>0.85 — no user needed</td></tr>
            <tr><td>Required (R)</td><td>0.62 — user must act</td></tr>
          </table>
        </div>
        <div class="cheat-card">
          <h3>Privileges Required (PR)</h3>
          <table class="cheat-table">
            <tr><td>None (N)</td><td>0.85 / 0.85</td></tr>
            <tr><td>Low (L)</td><td>0.50 / 0.68 (S:C)</td></tr>
            <tr><td>High (H)</td><td>0.27 / 0.50 (S:C)</td></tr>
          </table>
          <div style="font-size:11px;color:var(--muted);margin-top:6px">Values change when Scope is Changed</div>
        </div>
        <div class="cheat-card">
          <h3>CIA Impact (C / I / A)</h3>
          <table class="cheat-table">
            <tr><td>None (N)</td><td>0.00</td></tr>
            <tr><td>Low (L)</td><td>0.22</td></tr>
            <tr><td>High (H)</td><td>0.56</td></tr>
          </table>
          <h3 style="margin-top:1rem">Scope (S)</h3>
          <table class="cheat-table">
            <tr><td>Unchanged (U)</td><td>impact stays in component</td></tr>
            <tr><td>Changed (C)</td><td>impact beyond component</td></tr>
          </table>
        </div>
      </div>

      <div class="card" style="margin-top:1.5rem">
        <div class="card-title">CySA+ exam tips</div>
        <div style="display:flex;flex-direction:column;gap:10px;font-size:14px;color:var(--muted)">
          <div><span style="color:var(--accent);font-weight:600">AV:N</span> is always the worst — network exploitable vulns score highest</div>
          <div><span style="color:var(--accent);font-weight:600">Scope:Changed</span> significantly increases the score — affects systems beyond the vulnerable component</div>
          <div><span style="color:var(--accent);font-weight:600">C:H/I:H/A:H</span> with AV:N = almost certainly Critical (9.0+)</div>
          <div><span style="color:var(--accent);font-weight:600">PR:N + UI:N</span> = no user interaction + no auth = most dangerous attack scenario</div>
          <div><span style="color:var(--accent);font-weight:600">ISS</span> measures combined CIA impact — if all three are None, Base Score is always 0</div>
          <div><span style="color:var(--accent);font-weight:600">roundUp()</span> always rounds UP to 1 decimal — never down (e.g. 6.41 → 6.5)</div>
        </div>
      </div>
    </div>

  </main>
</div>

<div class="toast" id="toast"></div>

<script>
const AV_MAP  = {N:0.85,A:0.62,L:0.55,P:0.20};
const AC_MAP  = {L:0.77,H:0.44};
const PR_U    = {N:0.85,L:0.50,H:0.27};
const PR_C    = {N:0.85,L:0.68,H:0.50};
const UI_MAP  = {N:0.85,R:0.62};
const CIA_MAP = {N:0,L:0.22,H:0.56};
const SEV_ORDER = {Critical:0,High:1,Medium:2,Low:3,None:4};

let metrics = {AV:'N',AC:'L',PR:'N',UI:'N',S:'U',C:'N',I:'N',A:'N'};
let compareRowsA = [], compareRowsB = [];

function roundUp(v){ return Math.ceil(v*10)/10; }

function calcCVSS(m){
  const av = AV_MAP[m.AV]??0.85, ac = AC_MAP[m.AC]??0.77;
  const pr = (m.S==='C'?PR_C:PR_U)[m.PR]??0.85, ui = UI_MAP[m.UI]??0.85;
  const c = CIA_MAP[m.C]??0, i = CIA_MAP[m.I]??0, a = CIA_MAP[m.A]??0;
  const ISS = 1-(1-c)*(1-i)*(1-a);
  let impact = m.S==='U' ? 6.42*ISS : 7.52*(ISS-0.029)-3.25*Math.pow(ISS-0.02,15);
  const exploit = 8.22*av*ac*pr*ui;
  let base;
  if(impact<=0) base=0;
  else if(m.S==='U') base=roundUp(Math.min(impact+exploit,10));
  else base=roundUp(Math.min(1.08*(impact+exploit),10));
  let sev = base===0?'None':base<4?'Low':base<7?'Medium':base<9?'High':'Critical';
  return {ISS:+ISS.toFixed(4),impact:+impact.toFixed(4),exploit:+exploit.toFixed(4),base:+base.toFixed(1),sev,av,ac,pr,ui,c,i,a};
}

const SEV_COLORS = {
  None:'#8888aa',Low:'#44cc88',Medium:'#e8a820',High:'#ff7733',Critical:'#ff4444'
};

function updateDisplay(){
  const s = calcCVSS(metrics);
  document.getElementById('bigScore').textContent = s.base.toFixed(1);
  document.getElementById('bigScore').style.color = SEV_COLORS[s.sev];
  const badge = document.getElementById('sevBadge');
  badge.textContent = s.sev;
  badge.className = 'sev-badge sev-'+s.sev.toLowerCase();
  document.getElementById('issOut').textContent = s.ISS;
  document.getElementById('impactOut').textContent = s.impact;
  document.getElementById('exploitOut').textContent = s.exploit;
  const bar = document.getElementById('scoreBar');
  bar.style.width = (s.base/10*100)+'%';
  bar.style.background = SEV_COLORS[s.sev];

  const pr = (metrics.S==='C'?PR_C:PR_U)[metrics.PR]??0.85;
  const issCalc = `1−(1−${CIA_MAP[metrics.C]})×(1−${CIA_MAP[metrics.I]})×(1−${CIA_MAP[metrics.A]})`;
  const impactCalc = metrics.S==='U'
    ? `6.42 × ${s.ISS}`
    : `7.52×(${s.ISS}−0.029) − 3.25×(${s.ISS}−0.02)^15`;
  const exploitCalc = `8.22 × ${s.av} × ${s.ac} × ${pr} × ${s.ui}`;
  const baseCalc = s.impact<=0 ? 'Impact ≤ 0 → 0'
    : metrics.S==='U' ? `roundUp(min(${s.impact}+${s.exploit}, 10))`
    : `roundUp(min(1.08×(${s.impact}+${s.exploit}), 10))`;

  document.getElementById('stepsBox').innerHTML = [
    ['ISS', issCalc, s.ISS],
    ['Impact (S:'+metrics.S+')', impactCalc, s.impact],
    ['Exploitability', exploitCalc, s.exploit],
    ['Base Score', baseCalc, s.base.toFixed(1)],
  ].map(([k,v,r])=>`<div class="step-row">
    <span class="step-key">${k}</span>
    <span class="step-val">= ${v}</span>
    <span class="step-result">= ${r}</span>
  </div>`).join('');

  const vec = `CVSS:3.1/AV:${metrics.AV}/AC:${metrics.AC}/PR:${metrics.PR}/UI:${metrics.UI}/S:${metrics.S}/C:${metrics.C}/I:${metrics.I}/A:${metrics.A}`;
  document.getElementById('vectorOut').childNodes[0].textContent = vec+' ';
}

function setMetric(key, val, btn){
  metrics[key] = val;
  document.querySelectorAll(`#mg-${key} .metric-btn`).forEach(b=>b.classList.remove('selected'));
  btn.classList.add('selected');
  updateDisplay();
}

function parseVector(){
  const raw = document.getElementById('vectorInput').value.trim();
  const parts = {};
  raw.split('/').forEach(p=>{const[k,v]=p.split(':');if(k&&v)parts[k]=v;});
  ['AV','AC','PR','UI','S','C','I','A'].forEach(k=>{
    if(parts[k]){
      metrics[k]=parts[k];
      document.querySelectorAll(`#mg-${k} .metric-btn`).forEach(b=>{
        b.classList.toggle('selected', b.dataset.val===parts[k]);
      });
    }
  });
  updateDisplay();
  showToast('Vector parsed!');
}

function copyVector(){
  const vec = `CVSS:3.1/AV:${metrics.AV}/AC:${metrics.AC}/PR:${metrics.PR}/UI:${metrics.UI}/S:${metrics.S}/C:${metrics.C}/I:${metrics.I}/A:${metrics.A}`;
  navigator.clipboard.writeText(vec).catch(()=>{});
  const hint = document.getElementById('copyHint');
  hint.textContent = 'copied!'; hint.classList.add('copied');
  setTimeout(()=>{hint.textContent='click to copy';hint.classList.remove('copied');},2000);
}

function resetCalc(){
  metrics = {AV:'N',AC:'L',PR:'N',UI:'N',S:'U',C:'N',I:'N',A:'N'};
  document.querySelectorAll('.metric-btn').forEach(b=>b.classList.remove('selected'));
  [{g:'AV',v:'N'},{g:'AC',v:'L'},{g:'PR',v:'N'},{g:'UI',v:'N'},{g:'S',v:'U'},{g:'C',v:'N'},{g:'I',v:'N'},{g:'A',v:'N'}].forEach(({g,v})=>{
    const btn = document.querySelector(`#mg-${g} [data-val="${v}"]`);
    if(btn) btn.classList.add('selected');
  });
  ['vulnName','vulnHost','vulnPort','vulnNotes','vectorInput'].forEach(id=>{
    const el = document.getElementById(id); if(el) el.value='';
  });
  updateDisplay();
}

// ── Storage ──
function getVulns(){ try{ return JSON.parse(localStorage.getItem('cvss_vulns')||'[]'); }catch(e){ return []; } }
function saveVulns(v){ localStorage.setItem('cvss_vulns',JSON.stringify(v)); updateCount(); }
function updateCount(){ document.getElementById('savedCount').textContent = getVulns().length; }

function saveVuln(){
  const name = document.getElementById('vulnName').value.trim() || 'Unnamed vulnerability';
  const s = calcCVSS(metrics);
  const vuln = {
    id: Date.now(),
    name, host: document.getElementById('vulnHost').value,
    port: document.getElementById('vulnPort').value,
    notes: document.getElementById('vulnNotes').value,
    metrics: {...metrics},
    score: s.base, sev: s.sev,
    vector: `CVSS:3.1/AV:${metrics.AV}/AC:${metrics.AC}/PR:${metrics.PR}/UI:${metrics.UI}/S:${metrics.S}/C:${metrics.C}/I:${metrics.I}/A:${metrics.A}`
  };
  const vulns = getVulns();
  vulns.push(vuln);
  vulns.sort((a,b)=>SEV_ORDER[a.sev]-SEV_ORDER[b.sev]||b.score-a.score);
  saveVulns(vulns);
  showToast('Saved to library!');
  renderLibrary();
}

function renderLibrary(){
  const vulns = getVulns();
  const el = document.getElementById('libList');
  if(!vulns.length){
    el.innerHTML = `<div class="empty-lib">
      <svg width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"/><path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"/></svg>
      <div style="font-size:16px;font-weight:500;margin-bottom:6px">No vulnerabilities saved yet</div>
      <div style="font-size:13px">Use the Calculator to score and save vulnerabilities</div>
    </div>`;
    return;
  }
  el.innerHTML = vulns.map(v=>`
    <div class="vuln-item" onclick="loadVuln(${v.id})">
      <div class="vuln-score-badge" style="color:${SEV_COLORS[v.sev]}">${v.score.toFixed(1)}</div>
      <div class="vuln-info">
        <div class="vuln-name">${v.name}</div>
        <div class="vuln-meta">${v.sev} &nbsp;·&nbsp; ${v.vector} ${v.host?'&nbsp;·&nbsp; '+v.host:''}</div>
      </div>
      <div class="vuln-actions" onclick="event.stopPropagation()">
        <button class="icon-btn del" onclick="deleteVuln(${v.id})">✕</button>
      </div>
    </div>`).join('');
}

function loadVuln(id){
  const v = getVulns().find(x=>x.id===id);
  if(!v) return;
  metrics = {...v.metrics};
  document.getElementById('vulnName').value = v.name;
  document.getElementById('vulnHost').value = v.host||'';
  document.getElementById('vulnPort').value = v.port||'';
  document.getElementById('vulnNotes').value = v.notes||'';
  document.querySelectorAll('.metric-btn').forEach(b=>b.classList.remove('selected'));
  Object.entries(metrics).forEach(([k,val])=>{
    const btn = document.querySelector(`#mg-${k} [data-val="${val}"]`);
    if(btn) btn.classList.add('selected');
  });
  updateDisplay();
  showPage('calculator', document.querySelector('.nav-item'));
  document.querySelectorAll('.nav-item').forEach((b,i)=>{ if(i===0) b.classList.add('active'); else b.classList.remove('active'); });
  showToast('Loaded into calculator!');
}

function deleteVuln(id){
  saveVulns(getVulns().filter(v=>v.id!==id));
  renderLibrary();
  showToast('Deleted');
}

function clearAll(){
  if(confirm('Delete all saved vulnerabilities?')){
    saveVulns([]);
    renderLibrary();
    showToast('Cleared');
  }
}

function exportCSV(){
  const vulns = getVulns();
  if(!vulns.length){ showToast('Nothing to export'); return; }
  const rows = [['Name','Host','Port','AV','AC','PR','UI','S','C','I','A','Score','Severity','Vector','Notes']];
  vulns.forEach(v=>rows.push([v.name,v.host,v.port,v.metrics.AV,v.metrics.AC,v.metrics.PR,v.metrics.UI,v.metrics.S,v.metrics.C,v.metrics.I,v.metrics.A,v.score,v.sev,v.vector,v.notes]));
  const csv = rows.map(r=>r.map(c=>`"${String(c??'').replace(/"/g,'""')}"`).join(',')).join('\n');
  const a = document.createElement('a');
  a.href = URL.createObjectURL(new Blob([csv],{type:'text/csv'}));
  a.download = 'cvss-vulnerabilities.csv';
  a.click();
  showToast('CSV exported!');
}

// ── Compare ──
function addCompareRow(scan){
  const vulns = getVulns();
  const id = 'cr-'+Date.now();
  const row = {id, name:'', score:5.0, sev:'Medium'};
  if(scan==='A') compareRowsA.push(row); else compareRowsB.push(row);
  renderCompareRows(scan);
}

function renderCompareRows(scan){
  const rows = scan==='A' ? compareRowsA : compareRowsB;
  const el = document.getElementById('compareList'+scan);
  const savedVulns = getVulns();
  el.innerHTML = rows.map((r,i)=>`
    <div style="display:flex;gap:6px;margin-bottom:6px;align-items:center">
      <input type="text" value="${r.name}" placeholder="Vulnerability name" style="flex:1;font-size:12px;padding:6px 8px"
        onchange="compareRowsSet('${scan}',${i},'name',this.value)" />
      <input type="number" value="${r.score}" min="0" max="10" step="0.1" style="width:52px;font-size:12px;padding:6px;text-align:center"
        onchange="compareRowsSet('${scan}',${i},'score',parseFloat(this.value)||0)" />
      <select style="width:90px;font-size:12px;padding:6px" onchange="compareRowsSet('${scan}',${i},'sev',this.value)">
        ${['Critical','High','Medium','Low','None'].map(s=>`<option${s===r.sev?' selected':''}>${s}</option>`).join('')}
      </select>
      <button class="icon-btn del" onclick="removeCompareRow('${scan}',${i})">✕</button>
    </div>`).join('');
  if(savedVulns.length && !rows.length){
    el.innerHTML = `<div style="font-size:12px;color:var(--muted);margin-bottom:8px">You have ${savedVulns.length} saved vulns — click + Add to enter them, or type new ones.</div>`;
  }
}

function compareRowsSet(scan,i,key,val){
  if(scan==='A') compareRowsA[i][key]=val; else compareRowsB[i][key]=val;
}
function removeCompareRow(scan,i){
  if(scan==='A') compareRowsA.splice(i,1); else compareRowsB.splice(i,1);
  renderCompareRows(scan);
}

function runCompare(){
  const nameA = document.getElementById('scanNameA').value||'Scan A';
  const nameB = document.getElementById('scanNameB').value||'Scan B';
  if(!compareRowsA.length && !compareRowsB.length){ showToast('Add vulnerabilities to both scans first'); return; }

  function scanStats(rows){
    if(!rows.length) return {avg:0,max:0,critical:0,high:0,medium:0,low:0,weighted:0};
    const counts={Critical:0,High:0,Medium:0,Low:0,None:0};
    let sum=0,max=0;
    rows.forEach(r=>{ const s=parseFloat(r.score)||0; sum+=s; if(s>max)max=s; counts[r.sev]=(counts[r.sev]||0)+1; });
    const w = counts.Critical*10+counts.High*7+counts.Medium*4+counts.Low*1;
    return {avg:+(sum/rows.length).toFixed(2),max,critical:counts.Critical,high:counts.High,medium:counts.Medium,low:counts.Low,
      weighted:+((sum/rows.length)*.4+max*.3+(w/rows.length)*.3).toFixed(2)};
  }

  const sA=scanStats(compareRowsA), sB=scanStats(compareRowsB);
  const winner = sA.weighted>=sB.weighted?'A':'B';
  const winName = winner==='A'?nameA:nameB;
  const loseName = winner==='A'?nameB:nameA;
  const winStats = winner==='A'?sA:sB;
  const loseStats= winner==='A'?sB:sA;
  const winRows  = winner==='A'?compareRowsA:compareRowsB;

  const reasons=[];
  if(winStats.critical>loseStats.critical) reasons.push(`${winStats.critical} critical vuln${winStats.critical>1?'s':''} vs ${loseStats.critical}`);
  if(winStats.max>loseStats.max) reasons.push(`highest score ${winStats.max} vs ${loseStats.max}`);
  if(winStats.avg>loseStats.avg) reasons.push(`higher avg score (${winStats.avg} vs ${loseStats.avg})`);
  if(!reasons.length) reasons.push('higher overall weighted risk');

  const topVulns = [...winRows].sort((a,b)=>SEV_ORDER[a.sev]-SEV_ORDER[b.sev]||b.score-a.score).slice(0,3);
  const actions=[];
  if(winStats.critical>0) actions.push(`Immediately patch ${winStats.critical} critical issue${winStats.critical>1?'s':''} in <strong>${winName}</strong>`);
  if(winStats.high>0) actions.push(`Resolve ${winStats.high} high-severity finding${winStats.high>1?'s':''} within 72 hours`);
  actions.push(`After ${winName} is resolved, move to <strong>${loseName}</strong>`);

  document.getElementById('compareResult').innerHTML = `
    <div style="background:var(--surface);border:1px solid var(--border);border-radius:12px;overflow:hidden">
      <div style="padding:1.25rem;background:var(--surface2);border-bottom:1px solid var(--border);display:flex;align-items:center;gap:14px">
        <div style="width:44px;height:44px;background:var(--accent);border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:20px;font-weight:700;color:#fff;flex-shrink:0">1</div>
        <div>
          <div style="font-size:11px;color:var(--muted);text-transform:uppercase;letter-spacing:.06em;margin-bottom:3px">Fix this first</div>
          <div style="font-size:18px;font-weight:600;color:var(--accent)">${winName}</div>
          <div style="font-size:13px;color:var(--muted);margin-top:2px">${reasons.join(' · ')}</div>
        </div>
      </div>
      <div style="display:grid;grid-template-columns:1fr 1fr;border-bottom:1px solid var(--border)">
        ${[{n:nameA,s:sA,col:'#5b8dee'},{n:nameB,s:sB,col:'#e87733'}].map(({n,s,col})=>`
          <div style="padding:1rem 1.25rem;border-right:1px solid var(--border)">
            <div style="font-size:12px;font-weight:600;color:${col};margin-bottom:.75rem">${n}</div>
            ${['Critical','High','Medium','Low'].map(sev=>`
              <div style="display:flex;align-items:center;gap:8px;margin-bottom:5px;font-size:12px">
                <span style="width:52px;color:var(--muted)">${sev}</span>
                <div style="flex:1;background:var(--surface2);border-radius:20px;height:5px;overflow:hidden">
                  <div style="height:100%;border-radius:20px;background:${SEV_COLORS[sev]};width:${Math.round((s[sev.toLowerCase()]||0)/Math.max(s.critical+s.high+s.medium+s.low,1)*100)}%"></div>
                </div>
                <span style="font-weight:600;min-width:16px;text-align:right">${s[sev.toLowerCase()]||0}</span>
              </div>`).join('')}
          </div>`).join('')}
      </div>
      <div style="padding:1rem 1.25rem">
        <div style="font-size:11px;text-transform:uppercase;letter-spacing:.06em;color:var(--muted);font-weight:600;margin-bottom:.75rem">Action plan</div>
        ${actions.map((a,i)=>`<div style="display:flex;gap:10px;font-size:13px;padding:4px 0;color:var(--muted)"><span style="color:var(--accent);font-family:var(--mono);flex-shrink:0">${i+1}.</span><span>${a}</span></div>`).join('')}
      </div>
    </div>`;
}

// ── Nav ──
function showPage(name, clickedBtn){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page-'+name).classList.add('active');
  document.querySelectorAll('.nav-item').forEach(b=>b.classList.remove('active'));
  if(clickedBtn) clickedBtn.classList.add('active');
  if(name==='library') renderLibrary();
  if(name==='compare'){ renderCompareRows('A'); renderCompareRows('B'); }
}

// ── Toast ──
function showToast(msg){
  const t=document.getElementById('toast');
  t.textContent=msg; t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'),2200);
}

updateDisplay();
updateCount();
</script>
</body>
</html>






