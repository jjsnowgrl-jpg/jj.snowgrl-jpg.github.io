<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>RELAY — Restaurant Dashboard</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;700;800&display=swap" rel="stylesheet">
<style>
  *{box-sizing:border-box;margin:0;padding:0;}
  body{background:#1a1f2e;color:#cdd5e8;font-family:'IBM Plex Mono',monospace;height:100vh;overflow:hidden;}
  button{font-family:inherit;cursor:pointer;}
  button:hover{filter:brightness(1.12);}
  input{font-family:inherit;}
  input:focus{outline:none;}

  @keyframes pulse{0%,100%{opacity:1}50%{opacity:0.45}}
  @keyframes handsGlow{0%,100%{box-shadow:0 0 0 4px #fde68a55,0 8px 32px #f59e0b66}50%{box-shadow:0 0 0 8px #fde68a88,0 8px 48px #f59e0b99}}
  @keyframes handsWave{0%{transform:rotate(-12deg)}100%{transform:rotate(12deg)}}
  .pulse-anim{animation:pulse 1.4s infinite;}
  .chip-pulse{animation:pulse 1.2s infinite;}

  ::-webkit-scrollbar{width:4px;height:4px;}
  ::-webkit-scrollbar-track{background:#1a1f2e;}
  ::-webkit-scrollbar-thumb{background:#2e3850;border-radius:4px;}

  /* ── SCREENS ── */
  #screen-setup,#screen-mode,#screen-app{display:none;height:100vh;}
  #screen-setup.active,#screen-mode.active{display:flex;align-items:center;justify-content:center;}
  #screen-app.active{display:flex;flex-direction:column;}

  .splash-card{background:#20273a;border:1px solid #2e3850;border-radius:18px;padding:40px 48px;text-align:center;box-shadow:0 8px 40px #00000055;max-width:500px;width:100%;}
  .splash-logo{font-size:30px;font-weight:800;letter-spacing:0.2em;color:#e2e8f8;margin-bottom:6px;display:flex;align-items:center;justify-content:center;gap:10px;}
  .splash-sub{color:#5a6a8a;font-size:12px;letter-spacing:0.08em;margin-bottom:24px;}

  .setup-label{font-size:11px;color:#4a5878;font-weight:700;letter-spacing:0.06em;margin-bottom:8px;text-align:left;}
  .setup-input{background:#252d3d;border:1px solid #2e3850;border-radius:6px;color:#cdd5e8;font-size:12px;padding:9px 11px;width:100%;}
  .setup-btn{border-radius:7px;border:1px solid;padding:9px 16px;font-weight:800;font-size:12px;letter-spacing:0.04em;transition:all 0.15s;}
  .setup-hint{font-size:10px;color:#3d4f6e;margin-top:5px;text-align:left;}
  .setup-err{color:#f87171;font-size:11px;margin-top:12px;background:#2d0a0a;border:1px solid #dc262655;border-radius:6px;padding:8px 10px;display:none;text-align:left;}

  .mode-btns{display:flex;gap:16px;margin-top:8px;}
  .mode-btn{flex:1;border-radius:13px;padding:28px 24px;cursor:pointer;display:flex;flex-direction:column;align-items:center;gap:7px;transition:transform 0.15s,box-shadow 0.15s;background:#20273a;}
  .mode-btn:hover{transform:translateY(-2px);box-shadow:0 4px 20px #00000055;}
  .mode-btn .emoji{font-size:32px;}
  .mode-btn strong{font-size:14px;color:#e2e8f8;letter-spacing:0.04em;}
  .mode-btn span.sub{font-size:11px;color:#5a6a8a;}
  .mode-btn.foh{border:1px solid #1e3a6e;}
  .mode-btn.boh{border:1px solid #7c2d0a;}

  /* ── HEADER ── */
  #app-header{display:flex;align-items:center;gap:10px;padding:10px 20px;background:#20273a;border-bottom:1px solid #2e3850;position:sticky;top:0;z-index:200;box-shadow:0 1px 6px #00000033;flex-shrink:0;}
  .h-title{font-size:17px;font-weight:800;letter-spacing:0.2em;color:#e2e8f8;}
  .h-badge{font-size:11px;font-weight:700;padding:3px 10px;border-radius:6px;border:1px solid;letter-spacing:0.06em;}
  .h-alerts{display:flex;gap:8px;align-items:center;flex:1;margin-left:6px;}
  .h-chip{font-size:11px;font-weight:700;padding:3px 10px;border-radius:6px;border:1px solid;letter-spacing:0.05em;display:flex;align-items:center;gap:5px;}
  .sync-dot{font-size:10px;display:flex;align-items:center;gap:4px;font-weight:700;}
  .sync-dot .dot{width:7px;height:7px;border-radius:50%;display:inline-block;}
  .hold-btn{border-radius:7px;border:1px solid;padding:6px 14px;font-weight:800;font-size:11px;letter-spacing:0.06em;transition:all 0.15s;}
  .switch-btn{background:transparent;border:1px solid #2e3850;border-radius:6px;color:#5a6a8a;font-size:11px;padding:6px 12px;letter-spacing:0.04em;}

  /* ── BANNERS ── */
  .hold-banner{background:#1e0d35;border-bottom:1px solid #5b21b6;padding:8px 22px;font-size:12px;color:#c4b5fd;letter-spacing:0.04em;text-align:center;font-weight:700;flex-shrink:0;}
  .hands-banner{background:#1c1600;border-bottom:2px solid #d97706;padding:10px 22px;font-size:13px;color:#fde047;letter-spacing:0.04em;text-align:center;font-weight:800;display:flex;align-items:center;justify-content:center;gap:8px;flex-shrink:0;}

  /* ── BODY ── */
  #app-body{display:flex;flex:1;overflow:hidden;}

  /* ── GRID PANEL ── */
  #grid-panel{flex:1;padding:18px 20px;overflow-y:auto;border-right:1px solid #2e3850;background:#1e2436;}
  .panel-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:14px;gap:8px;}
  .panel-title{font-weight:800;letter-spacing:0.08em;font-size:12px;}
  .panel-hint{color:#3d4f6e;font-size:11px;}
  .course-table{border-collapse:separate;border-spacing:3px 3px;min-width:540px;}
  .th-corner{padding:6px 12px;text-align:left;}
  .th-course{padding:5px 4px;font-size:11px;color:#4a5878;font-weight:700;letter-spacing:0.06em;text-align:center;white-space:nowrap;vertical-align:middle;}
  .td-label{padding:5px 10px;vertical-align:middle;min-width:112px;}
  .tbl-name{font-weight:700;color:#cdd5e8;font-size:13px;}
  .tbl-pax{color:#e2e8f8;font-size:11px;}
  .cell-btn{width:96px;height:40px;border-radius:7px;border:1px solid;font-size:10px;font-weight:800;letter-spacing:0.03em;transition:all 0.1s;user-select:none;line-height:1.2;display:flex;flex-direction:column;align-items:center;justify-content:center;}
  .cell-awaiting{font-size:8px;opacity:0.5;margin-top:1px;}
  .legend{display:flex;flex-wrap:wrap;gap:12px;margin-top:16px;padding-top:12px;border-top:1px solid #2e3850;align-items:center;}
  .legend-item{display:flex;align-items:center;gap:5px;font-size:11px;font-weight:600;}
  .legend-dot{width:8px;height:8px;border-radius:50%;display:inline-block;}
  .legend-override{color:#2e3850;font-size:11px;margin-left:auto;}
  .x-btn{background:transparent;border:none;color:#ef4444;font-size:14px;padding:0 2px;line-height:1;opacity:0.5;}
  .add-btn{border-radius:5px;border:1px solid;padding:5px 10px;font-size:11px;font-weight:700;letter-spacing:0.04em;}
  .mini-input{background:#252d3d;border:1px solid #2e3850;border-radius:5px;color:#cdd5e8;font-size:11px;padding:5px 8px;width:100px;}

  /* ── SIDE PANEL ── */
  #side-panel{width:240px;min-width:220px;display:flex;flex-direction:column;padding:18px 14px;background:#1a1f2e;border-left:1px solid #2e3850;overflow:hidden;}
  .hands-btn{border-radius:14px;border:2px solid;padding:18px 12px;font-weight:800;text-align:center;transition:all 0.2s;width:100%;}
  .hands-emoji{font-size:36px;display:block;margin-bottom:6px;}
  .hands-label{font-size:16px;font-weight:900;letter-spacing:0.12em;display:block;}
  .hands-sub{font-size:10px;opacity:0.7;margin-top:4px;display:block;letter-spacing:0.05em;}
  .dismiss-btn{border-radius:8px;border:1px solid #2e3850;background:#20273a;color:#8899bb;font-size:11px;font-weight:700;padding:7px 12px;width:100%;letter-spacing:0.04em;}
  .divider{border-top:1px solid #2e3850;margin:0 0 14px;}
  .section-label{font-size:11px;font-weight:700;color:#4a5878;letter-spacing:0.08em;margin-bottom:10px;}
  .notes-list{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:6px;margin-bottom:10px;max-height:calc(100vh - 460px);}
  .note-empty{color:#3d4f6e;font-size:11px;text-align:center;padding:16px 0;font-style:italic;}
  .note-item{border-radius:6px;padding:7px 8px;}
  .note-header{display:flex;align-items:center;gap:5px;margin-bottom:3px;}
  .note-tag{font-size:9px;font-weight:800;padding:1px 5px;border-radius:3px;border:1px solid;letter-spacing:0.06em;}
  .note-from{font-size:10px;font-weight:700;letter-spacing:0.06em;}
  .note-time{font-size:9px;color:#3d4f6e;margin-left:auto;}
  .note-del{background:none;border:none;color:#3d4f6e;font-size:12px;padding:0 0 0 2px;line-height:1;}
  .note-text{font-size:11px;color:#cdd5e8;line-height:1.4;}
  .tag-btns{display:flex;gap:4px;margin-bottom:6px;}
  .tag-btn{flex:1;border:1px solid;border-radius:5px;padding:4px 3px;font-size:10px;font-weight:700;letter-spacing:0.04em;transition:all 0.12s;}
  .note-compose{display:flex;gap:5px;}
  .note-input{flex:1;background:#252d3d;border:1px solid #2e3850;border-radius:6px;color:#cdd5e8;font-size:11px;padding:7px 8px;}
  .note-send{border-radius:6px;border:none;padding:7px 10px;font-weight:800;font-size:11px;}

  /* ── CONTEXT MENU ── */
  #ctx-menu{position:fixed;z-index:999;background:#20273a;border:1px solid #2e3850;border-radius:10px;padding:4px;box-shadow:0 8px 32px #00000055;display:none;flex-direction:column;gap:2px;min-width:130px;}
  #ctx-menu.open{display:flex;}
  .ctx-label{color:#4a5878;font-size:10px;padding:4px 10px 2px;letter-spacing:0.07em;font-weight:700;}
  .ctx-item{padding:7px 12px;border-radius:6px;border:1px solid transparent;font-size:11px;font-weight:700;text-align:left;width:100%;}

  /* ── ADD ROW ── */
  .add-row{display:flex;gap:8px;align-items:center;padding-top:12px;}
</style>
</head>
<body>

<!-- ══ SETUP SCREEN ══ -->
<div id="screen-setup" class="active">
  <div class="splash-card">
    <div class="splash-logo"><span style="color:#60a5fa">⬡</span> RELAY</div>
    <p class="splash-sub">Restaurant Communication System</p>

    <div style="margin-bottom:20px;text-align:left;">
      <div class="setup-label">JSONBIN MASTER KEY</div>
      <input class="setup-input" id="api-input" type="password"
        placeholder="$2a$10$… (from jsonbin.io → Account → API Keys)"
        oninput="document.getElementById('key-status').textContent=''" />
      <div style="display:flex;align-items:center;gap:8px;margin-top:6px;">
        <span class="setup-hint">Free at <span style="color:#60a5fa">jsonbin.io</span> — sign up, copy your Master Key</span>
        <button class="setup-btn" style="background:#1a2a1a;border-color:#15803d;color:#4ade80;padding:4px 10px;font-size:10px;margin-left:auto;white-space:nowrap;" onclick="testKey()">Test key</button>
        <span id="key-status" style="font-size:11px;"></span>
      </div>
    </div>

    <button class="setup-btn" style="background:#172554;border-color:#1e40af;color:#60a5fa;width:100%;margin-bottom:16px;font-size:13px;" onclick="createBin()">
      ✦ Create new shift
    </button>

    <div style="border-top:1px solid #2e3850;padding-top:16px;text-align:left;">
      <div class="setup-label">JOIN EXISTING SHIFT</div>
      <div style="display:flex;gap:8px;">
        <input class="setup-input" id="bin-input" placeholder="Paste Bin ID from other device"
          style="flex:1;" onkeydown="if(event.key==='Enter')joinBin()" />
        <button class="setup-btn" style="background:#1e0d35;border-color:#7c3aed;color:#d8b4fe;white-space:nowrap;" onclick="joinBin()">Join →</button>
      </div>
    </div>

    <div id="setup-err" class="setup-err"></div>
    <div style="font-size:10px;color:#2a3550;margin-top:16px;line-height:1.6;">
      JSONBin is free · ~10k reads/month · open in browser — not as a Claude artifact
    </div>
  </div>
</div>

<!-- ══ MODE SCREEN ══ -->
<div id="screen-mode">
  <div class="splash-card">
    <div class="splash-logo"><span style="color:#60a5fa">⬡</span> RELAY</div>

    <div id="bin-id-box" style="background:#0c1a35;border:1px solid #2563eb;border-radius:10px;padding:14px 16px;margin-bottom:20px;text-align:left;display:none;">
      <div style="font-size:10px;color:#4a5878;font-weight:700;letter-spacing:0.06em;margin-bottom:6px;">YOUR BIN ID — share with the other device</div>
      <div id="bin-id-display" style="font-size:14px;font-weight:800;color:#60a5fa;letter-spacing:0.04em;word-break:break-all;"></div>
      <button onclick="copyBinId()" style="margin-top:8px;background:#172554;border:1px solid #1e40af;border-radius:5px;color:#93c5fd;font-size:10px;padding:4px 10px;">Copy</button>
    </div>

    <div class="mode-btns">
      <button class="mode-btn foh" onclick="setMode('FOH')">
        <span class="emoji">🍽️</span>
        <strong>Front of House</strong>
        <span class="sub">Servers & Floor Staff</span>
      </button>
      <button class="mode-btn boh" onclick="setMode('BOH')">
        <span class="emoji">🔥</span>
        <strong>Back of House</strong>
        <span class="sub">Kitchen & Expeditors</span>
      </button>
    </div>
    <p style="color:#2a3550;font-size:10px;margin-top:16px;letter-spacing:0.05em;">Syncs every 2 seconds · last-write-wins</p>
  </div>
</div>

<!-- ══ APP SCREEN ══ -->
<div id="screen-app">
  <!-- Header -->
  <header id="app-header">
    <span id="h-accent-icon" style="font-size:20px;color:#60a5fa">⬡</span>
    <span class="h-title">RELAY</span>
    <span id="h-badge" class="h-badge"></span>
    <div class="h-alerts" id="h-alerts"></div>
    <span class="sync-dot" id="sync-dot"><span class="dot" id="sync-dot-inner"></span><span id="sync-label">live</span></span>
    <button class="hold-btn" id="hold-btn" onclick="toggleHold()">⏸ HOLD</button>
    <button class="switch-btn" onclick="switchMode()">Switch Mode</button>
  </header>
  <div class="hold-banner" id="hold-banner" style="display:none">
    ⏸ HOLD MODE — tap to hold · double-tap a held cell to release ·
    <span style="text-decoration:underline;cursor:pointer;" onclick="toggleHold()">Done</span>
  </div>
  <div class="hands-banner" id="hands-banner" style="display:none">
    <span style="font-size:22px;animation:handsWave 0.6s infinite alternate">👐</span>
    &nbsp;HANDS — come to the kitchen now&nbsp;
    <span style="font-size:22px;animation:handsWave 0.6s infinite alternate">👐</span>
  </div>

  <div id="app-body">
    <!-- Grid Panel -->
    <div id="grid-panel">
      <div class="panel-head">
        <span class="panel-title" id="panel-title">COURSE TRACKER</span>
        <span class="panel-hint" id="panel-hint"></span>
      </div>
      <div style="overflow-x:auto;">
        <table class="course-table" id="course-table"></table>
      </div>
      <div class="legend" id="legend"></div>
    </div>

    <!-- Side Panel -->
    <div id="side-panel">
      <div style="display:flex;flex-direction:column;gap:8px;margin-bottom:18px;">
        <button class="hands-btn" id="hands-btn" onclick="toggleHands()">
          <span class="hands-emoji" id="hands-emoji">👐</span>
          <span class="hands-label">HANDS</span>
          <span class="hands-sub" id="hands-sub"></span>
        </button>
        <button class="dismiss-btn" id="dismiss-btn" style="display:none;" onclick="dismissHands()">✓ On my way</button>
      </div>

      <div class="divider"></div>

      <div style="display:flex;flex-direction:column;flex:1;min-height:0;">
        <div class="section-label">SHIFT NOTES</div>
        <div class="notes-list" id="notes-list"></div>
        <div class="tag-btns" id="tag-btns"></div>
        <div class="note-compose">
          <input class="note-input" id="note-input" placeholder="Add a note…"
            onkeydown="if(event.key==='Enter')sendNote()" />
          <button class="note-send" id="note-send" onclick="sendNote()">+</button>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Context Menu -->
<div id="ctx-menu">
  <div class="ctx-label">OVERRIDE</div>
</div>

<script>
// ══ CONSTANTS ══════════════════════════════════════════════════════════════════
const S = { NONE:"NONE",REQUEST:"REQUEST",FIRING:"FIRING",POURING:"POURING",PLATING:"PLATING",SERVED:"SERVED",HOLD:"HOLD" };
const SD = {
  NONE:    {foh:"—",          boh:"—",        color:"#5a6580",bg:"#252d3d",border:"#2e3850",pulse:false},
  REQUEST: {foh:"Request",    boh:"Request!",  color:"#4ade80",bg:"#0d2b1a",border:"#16a34a",pulse:true},
  FIRING:  {foh:"Firing",     boh:"Fire ✓",   color:"#86efac",bg:"#052e16",border:"#15803d",pulse:false},
  POURING: {foh:"Pouring",    boh:"Pour",      color:"#fde047",bg:"#1c1600",border:"#ca8a04",pulse:false},
  PLATING: {foh:"Plate/Run",  boh:"Plating",   color:"#fca5a5",bg:"#2d0a0a",border:"#dc2626",pulse:true},
  SERVED:  {foh:"Served",     boh:"Served",    color:"#93c5fd",bg:"#0c1a35",border:"#2563eb",pulse:false},
  HOLD:    {foh:"HOLD",       boh:"HOLD",      color:"#d8b4fe",bg:"#1e0d35",border:"#7c3aed",pulse:false},
};
const FOH_CYCLE = {NONE:"REQUEST",REQUEST:"REQUEST",FIRING:"POURING",POURING:"SERVED",PLATING:"SERVED",SERVED:"NONE"};
const BOH_CYCLE = {NONE:"FIRING",REQUEST:"FIRING",FIRING:"POURING",POURING:"PLATING",PLATING:"SERVED",SERVED:"NONE"};

const INIT_COURSES = ["Amuse","Appetizer","Soup / Salad","Entrée","Dessert"];
const INIT_TABLES  = [{id:1,label:"Table 1",pax:2},{id:2,label:"Table 2",pax:4},{id:3,label:"Table 3",pax:3},{id:4,label:"Table 4",pax:6},{id:5,label:"Bar 1",pax:1}];

function blankGrid(tables,courses){
  const g={};
  tables.forEach(t=>{g[t.id]={};courses.forEach(c=>{g[t.id][c]=S.NONE;});});
  return g;
}
const DEFAULT_STATE={tables:INIT_TABLES,courses:INIT_COURSES,grid:blankGrid(INIT_TABLES,INIT_COURSES),hands:false,notes:[],ts:0};
const JSONBIN_API="https://api.jsonbin.io/v3";
const POLL_MS=2000;

// ══ STATE ══════════════════════════════════════════════════════════════════════
let state    = JSON.parse(JSON.stringify(DEFAULT_STATE));
let mode     = null;     // "FOH" | "BOH"
let holdMode = false;
let binId    = "";
let apiKey   = "";
let pollInterval = null;
let nextId   = Date.now();
let ctxCell  = null;     // {tableId, course}
let noteTag  = "info";
let lastTap  = {key:"",time:0}; // double-tap detection

// ══ JSONBIN ════════════════════════════════════════════════════════════════════
async function binRead(){
  try{
    const r=await fetch(`${JSONBIN_API}/b/${binId}/latest`,{headers:{"X-Master-Key":apiKey}});
    if(!r.ok){console.error("binRead",r.status);return null;}
    const j=await r.json();return j.record??null;
  }catch(e){console.error("binRead err",e);return null;}
}
async function binWrite(data){
  try{
    const r=await fetch(`${JSONBIN_API}/b/${binId}`,{
      method:"PUT",
      headers:{"Content-Type":"application/json","X-Master-Key":apiKey},
      body:JSON.stringify(data)
    });
    setSyncStatus(r.ok?"ok":"error");
    return r.ok;
  }catch(e){setSyncStatus("error");return false;}
}
async function binCreate(key,data){
  try{
    const r=await fetch(`${JSONBIN_API}/b`,{
      method:"POST",
      headers:{"Content-Type":"application/json","X-Master-Key":key,"X-Bin-Name":"relay-shift"},
      body:JSON.stringify(data)
    });
    if(!r.ok){const t=await r.text();return{error:r.status+" "+t};}
    const j=await r.json();
    return{id:j.metadata?.id??null};
  }catch(e){return{error:String(e)};}
}

// ══ SETUP ACTIONS ══════════════════════════════════════════════════════════════
async function testKey(){
  const key=document.getElementById("api-input").value.trim();
  const ks=document.getElementById("key-status");
  if(!key){showSetupErr("Paste your Master Key first.");return;}
  ks.textContent="testing…";ks.style.color="#fde047";
  try{
    const r=await fetch(`${JSONBIN_API}/b`,{
      method:"POST",
      headers:{"Content-Type":"application/json","X-Master-Key":key,"X-Bin-Name":"relay-keytest"},
      body:JSON.stringify({test:true})
    });
    if(r.ok){
      const j=await r.json();
      const tid=j.metadata?.id;
      if(tid)fetch(`${JSONBIN_API}/b/${tid}`,{method:"DELETE",headers:{"X-Master-Key":key}}).catch(()=>{});
      ks.textContent="✓ valid";ks.style.color="#4ade80";
    }else if(r.status===401){
      ks.textContent="✗ invalid key";ks.style.color="#f87171";
      showSetupErr("401 Unauthorized — wrong key. Use the Master Key from jsonbin.io → Account → API Keys.");
    }else{
      const t=await r.text();
      ks.textContent="✗ error "+r.status;ks.style.color="#f87171";
      showSetupErr("Error "+r.status+": "+t.slice(0,120));
    }
  }catch(e){ks.textContent="✗ network error";ks.style.color="#f87171";showSetupErr("Network error: "+e);}
}

async function createBin(){
  const key=document.getElementById("api-input").value.trim();
  if(!key){showSetupErr("Paste your Master Key first.");return;}
  setBusy(true);
  const result=await binCreate(key,DEFAULT_STATE);
  if(!result?.id){showSetupErr("Could not create bin: "+(result?.error??"unknown"));setBusy(false);return;}
  apiKey=key;binId=result.id;
  setBusy(false);
  showModeScreen();
}

async function joinBin(){
  const key=document.getElementById("api-input").value.trim();
  const bid=document.getElementById("bin-input").value.trim();
  if(!key){showSetupErr("Paste your Master Key first.");return;}
  if(!bid){showSetupErr("Paste the Bin ID to join.");return;}
  setBusy(true);
  apiKey=key;binId=bid;
  const remote=await binRead();
  if(!remote){showSetupErr("Could not read that Bin ID — check it's correct.");apiKey="";binId="";setBusy(false);return;}
  applyRemote(remote);
  setBusy(false);
  showModeScreen();
}

function showSetupErr(msg){
  const el=document.getElementById("setup-err");
  el.textContent=msg;el.style.display="block";
}
function setBusy(b){
  document.querySelectorAll(".setup-btn,.setup-input").forEach(el=>el.disabled=b);
}
function showModeScreen(){
  document.getElementById("screen-setup").classList.remove("active");
  document.getElementById("screen-mode").classList.add("active");
  const box=document.getElementById("bin-id-box");
  if(binId){box.style.display="block";document.getElementById("bin-id-display").textContent=binId;}
}
function copyBinId(){navigator.clipboard?.writeText(binId);}

// ══ MODE ═══════════════════════════════════════════════════════════════════════
function setMode(m){
  mode=m;
  document.getElementById("screen-mode").classList.remove("active");
  document.getElementById("screen-app").classList.add("active");
  renderAll();
  startPoll();
}
function switchMode(){
  stopPoll();
  mode=null;
  document.getElementById("screen-app").classList.remove("active");
  document.getElementById("screen-mode").classList.add("active");
}

// ══ SYNC ═══════════════════════════════════════════════════════════════════════
function startPoll(){
  pollInterval=setInterval(async()=>{
    const remote=await binRead();
    if(!remote){setSyncStatus("error");return;}
    if((remote.ts??0)>(state.ts??0)){applyRemote(remote);}
    setSyncStatus("ok");
  },POLL_MS);
}
function stopPoll(){clearInterval(pollInterval);}

async function apply(patch){
  Object.assign(state,patch,{ts:Date.now()});
  renderAll();
  await binWrite(state);
}
function applyRemote(remote){
  Object.assign(state,{...DEFAULT_STATE,...remote});
  if(mode)renderAll();
}
function setSyncStatus(s){
  const dot=document.getElementById("sync-dot-inner");
  const lbl=document.getElementById("sync-label");
  const wrap=document.getElementById("sync-dot");
  if(s==="ok"){dot.style.background="#4ade80";dot.style.boxShadow="0 0 5px #4ade8088";lbl.style.color="#4ade8088";lbl.textContent="live";}
  else{dot.style.background="#f87171";dot.style.boxShadow="none";lbl.style.color="#f87171";lbl.textContent="sync err";}
}

// ══ RENDER ═════════════════════════════════════════════════════════════════════
const isFOH=()=>mode==="FOH";
const accent=()=>isFOH()?"#60a5fa":"#fb923c";
const accentBg=()=>isFOH()?"#172554":"#431407";
const accentBorder=()=>isFOH()?"#1e40af":"#9a3412";

function renderAll(){
  if(!mode)return;
  renderHeader();
  renderGrid();
  renderSidePanel();
  renderNotes();
}

function renderHeader(){
  const foh=isFOH();
  const ac=accent();
  document.getElementById("h-accent-icon").style.color=ac;
  const badge=document.getElementById("h-badge");
  badge.textContent=foh?"🍽️ FOH":"🔥 BOH";
  badge.style.background=accentBg();badge.style.color=ac;badge.style.borderColor=accentBorder();

  // chips
  const alerts=document.getElementById("h-alerts");
  alerts.innerHTML="";
  const counts={};
  Object.values(S).forEach(s=>counts[s]=0);
  state.tables.forEach(t=>state.courses.forEach(c=>{counts[state.grid[t.id]?.[c]??S.NONE]++;}));

  if(counts.REQUEST>0){
    const c=mkEl("span","h-chip chip-pulse");
    c.style.cssText="color:#4ade80;border-color:#16a34a;background:#0d2b1a;";
    c.textContent=`● ${counts.REQUEST} Request${counts.REQUEST>1?"s":""}`;
    alerts.appendChild(c);
  }
  if(counts.PLATING>0){
    const c=mkEl("span","h-chip chip-pulse");
    c.style.cssText="color:#fca5a5;border-color:#dc2626;background:#2d0a0a;";
    c.textContent=`● ${counts.PLATING} ${foh?"Plate/Run":"Plating"}`;
    alerts.appendChild(c);
  }
  if(counts.HOLD>0){
    const c=mkEl("span","h-chip");
    c.style.cssText="color:#d8b4fe;border-color:#7c3aed;background:#1e0d35;";
    c.textContent=`⏸ ${counts.HOLD} Hold`;
    alerts.appendChild(c);
  }

  // hold btn
  const hb=document.getElementById("hold-btn");
  if(holdMode){hb.style.background="#2d1260";hb.style.color="#c4b5fd";hb.style.borderColor="#7c3aed";hb.style.boxShadow="0 0 0 3px #4c288966";hb.textContent="⏸ Tap a cell…";}
  else{hb.style.background="#1e0d35";hb.style.color="#a78bfa";hb.style.borderColor="#4c2889";hb.style.boxShadow="none";hb.textContent="⏸ HOLD";}

  document.getElementById("hold-banner").style.display=holdMode?"block":"none";

  // HANDS banner (FOH only)
  document.getElementById("hands-banner").style.display=(state.hands&&isFOH())?"flex":"none";

  // panel hint
  document.getElementById("panel-title").style.color=ac;
  document.getElementById("panel-hint").textContent=foh
    ?"FOH: — → Request · (await BOH) · Firing → Pouring → Served"
    :"BOH: — → Fire → Pour → Plating → Served · tap Request! to confirm";
}

function renderGrid(){
  const foh=isFOH();
  const ac=accent();
  const table=document.getElementById("course-table");
  table.innerHTML="";

  // THEAD
  const thead=document.createElement("thead");
  const hrow=document.createElement("tr");
  const corner=document.createElement("th");
  corner.className="th-corner";
  corner.innerHTML=`<span style="color:#4a5878;font-size:11px;font-weight:700;letter-spacing:0.06em">TABLE / PAX</span>`;
  hrow.appendChild(corner);

  state.courses.forEach(c=>{
    const th=document.createElement("th");
    th.className="th-course";
    th.innerHTML=`${esc(c)} <button class="x-btn" onclick="removeCourse('${esc(c)}')">×</button>`;
    hrow.appendChild(th);
  });

  // add-course th
  const addTh=document.createElement("th");
  addTh.className="th-course";
  addTh.innerHTML=`<div style="display:flex;gap:4px;align-items:center;">
    <input class="mini-input" id="new-course-input" placeholder="+ Course" onkeydown="if(event.key==='Enter')addCourse()" />
    <button class="add-btn" style="background:${accentBg()};color:${ac};border-color:${accentBorder()}" onclick="addCourse()">+</button>
  </div>`;
  hrow.appendChild(addTh);
  thead.appendChild(hrow);
  table.appendChild(thead);

  // TBODY
  const tbody=document.createElement("tbody");

  state.tables.forEach(tbl=>{
    const row=document.createElement("tr");
    row.style.borderBottom="1px solid #252d3d";

    const td=document.createElement("td");
    td.className="td-label";
    td.innerHTML=`<div style="display:flex;align-items:center;justify-content:space-between;gap:6px;">
      <div><div class="tbl-name">${esc(tbl.label)}</div><div class="tbl-pax">${tbl.pax} pax</div></div>
      <button class="x-btn" onclick="removeTable(${tbl.id})">×</button>
    </div>`;
    row.appendChild(td);

    state.courses.forEach(course=>{
      const status=state.grid[tbl.id]?.[course]??S.NONE;
      const d=SD[status];
      const lbl=foh?d.foh:d.boh;
      const locked=foh&&status===S.REQUEST&&!holdMode;
      const isHeld=status===S.HOLD;
      const isHoldTarget=holdMode&&!isHeld;

      const cell=document.createElement("td");
      cell.style.cssText="padding:3px;text-align:center;";

      const btn=document.createElement("button");
      btn.className="cell-btn"+(d.pulse&&!holdMode?" pulse-anim":"");
      btn.style.background=isHoldTarget?"#180a24":d.bg;
      btn.style.color=isHoldTarget?"#6c3483":d.color;
      btn.style.borderColor=holdMode?"#5b2c6f":d.border;
      btn.style.borderStyle=isHoldTarget?"dashed":"solid";
      btn.style.cursor=locked?"not-allowed":holdMode?"crosshair":"pointer";
      btn.style.opacity=locked?"0.65":"1";
      if(isHeld)btn.style.boxShadow="0 0 0 2px #7c3aed88";
      else if(d.pulse&&!holdMode)btn.style.boxShadow=`0 0 0 2px ${d.border}66,0 2px 8px ${d.border}44`;

      btn.innerHTML=`${esc(lbl)}${locked?'<span class="cell-awaiting">awaiting BOH</span>':""}`;

      const cellKey=`${tbl.id}:${course}`;
      btn.addEventListener("click",e=>{e.stopPropagation();tapCell(tbl.id,course,cellKey);});
      btn.addEventListener("contextmenu",e=>{e.preventDefault();e.stopPropagation();if(!holdMode)openCtx(e,tbl.id,course);});

      cell.appendChild(btn);
      row.appendChild(cell);
    });

    row.appendChild(document.createElement("td")); // spacer
    tbody.appendChild(row);
  });

  // Add table row
  const addRow=document.createElement("tr");
  const addCell=document.createElement("td");
  addCell.colSpan=state.courses.length+2;
  addCell.className="add-row";
  addCell.innerHTML=`
    <input class="mini-input" id="new-tbl-label" placeholder="Table name" onkeydown="if(event.key==='Enter')addTable()" />
    <input class="mini-input" id="new-tbl-pax" type="number" min="1" max="20" placeholder="Pax" value="2" style="width:54px" />
    <button class="add-btn" style="background:${accentBg()};color:${ac};border-color:${accentBorder()}" onclick="addTable()">+ Add Table</button>
  `;
  addRow.appendChild(addCell);
  tbody.appendChild(addRow);
  table.appendChild(tbody);

  // Legend
  const leg=document.getElementById("legend");
  leg.innerHTML="";
  Object.entries(SD).filter(([k])=>k!==S.NONE).forEach(([k,d])=>{
    const item=document.createElement("span");
    item.className="legend-item";
    item.style.color=d.color;
    item.innerHTML=`<span class="legend-dot" style="background:${d.border}"></span>${esc(foh?d.foh:d.boh)}`;
    leg.appendChild(item);
  });
  const over=document.createElement("span");
  over.className="legend-override";
  over.textContent="Right-click = override";
  leg.appendChild(over);
}

function renderSidePanel(){
  const foh=isFOH();
  const ac=accent();
  const h=state.hands;

  // HANDS button
  const btn=document.getElementById("hands-btn");
  const emoji=document.getElementById("hands-emoji");
  const sub=document.getElementById("hands-sub");
  const dismiss=document.getElementById("dismiss-btn");

  if(h){
    btn.style.background="linear-gradient(135deg,#d97706,#f59e0b)";
    btn.style.color="#1c0a00";btn.style.borderColor="#fbbf24";
    btn.style.boxShadow="0 0 0 4px #fde68a55,0 8px 32px #f59e0b66";
    btn.style.animation="handsGlow 1.2s infinite";
    emoji.style.animation="handsWave 0.5s infinite alternate";
    sub.textContent=foh?"BOH needs you — go!":"Signalling FOH…";
    dismiss.style.display="block";
    dismiss.textContent=foh?"✓ On my way":"✕ Cancel signal";
  }else{
    btn.style.background="#1c1600";btn.style.color="#92400e";btn.style.borderColor="#854d0e";
    btn.style.boxShadow="0 1px 4px #00000044";btn.style.animation="none";
    emoji.style.animation="none";
    sub.textContent=foh?"Tap when BOH calls":"Call FOH to kitchen";
    dismiss.style.display="none";
  }

  // note send btn color
  document.getElementById("note-send").style.background=ac;
  document.getElementById("note-send").style.color="#0d1117";
}

function renderNotes(){
  const list=document.getElementById("notes-list");
  list.innerHTML="";

  const TC={
    allergy:{bg:"#2d0a3a",border:"#a855f7",color:"#e879f9",label:"⚠ ALLERGY"},
    eighty6:{bg:"#2d0a0a",border:"#dc2626",color:"#fca5a5",label:"86"},
    info:   {bg:"#0c1a35",border:"#2563eb",color:"#93c5fd",label:"INFO"},
  };

  if(!state.notes||state.notes.length===0){
    list.innerHTML='<div class="note-empty">No notes yet</div>';
  }else{
    (state.notes??[]).forEach(n=>{
      const tc=TC[n.tag]??TC.info;
      const div=document.createElement("div");
      div.className="note-item";
      div.style.background="#252d3d";
      div.style.borderLeft=`3px solid ${tc.border}`;
      div.style.border=`1px solid ${tc.border}33`;
      div.innerHTML=`<div class="note-header">
        <span class="note-tag" style="background:${tc.bg};color:${tc.color};border-color:${tc.border}55">${tc.label}</span>
        <span class="note-from" style="color:${n.from==="BOH"?"#fb923c":"#60a5fa"}">${n.from}</span>
        <span class="note-time">${esc(n.time)}</span>
        <button class="note-del" onclick="deleteNote(${n.id})">×</button>
      </div>
      <div class="note-text">${esc(n.text)}</div>`;
      list.appendChild(div);
    });
  }

  // tag buttons
  const tagBtns=document.getElementById("tag-btns");
  tagBtns.innerHTML="";
  [{v:"info",l:"Info",bg:"#0c1a35",border:"#2563eb",col:"#93c5fd"},
   {v:"allergy",l:"⚠ Allergy",bg:"#2d0a3a",border:"#a855f7",col:"#e879f9"},
   {v:"eighty6",l:"86",bg:"#2d0a0a",border:"#dc2626",col:"#fca5a5"}].forEach(o=>{
    const b=document.createElement("button");
    b.className="tag-btn";
    b.textContent=o.l;
    b.style.background=noteTag===o.v?o.bg:"#1e2436";
    b.style.color=noteTag===o.v?o.col:"#3d4f6e";
    b.style.borderColor=noteTag===o.v?o.border:"#2e3850";
    b.onclick=()=>{noteTag=o.v;renderNotes();};
    tagBtns.appendChild(b);
  });
}

// ══ INTERACTIONS ═══════════════════════════════════════════════════════════════
function tapCell(tableId,course,cellKey){
  const cur=state.grid[tableId]?.[course]??S.NONE;

  // Double-tap detection for HOLD release
  const now=Date.now();
  if(cur===S.HOLD&&lastTap.key===cellKey&&now-lastTap.time<500){
    lastTap={key:"",time:0};
    const ng={...state.grid,[tableId]:{...state.grid[tableId],[course]:S.NONE}};
    apply({grid:ng});return;
  }
  lastTap={key:cellKey,time:now};

  if(holdMode){
    if(cur===S.HOLD)return;
    const ng={...state.grid,[tableId]:{...state.grid[tableId],[course]:S.HOLD}};
    apply({grid:ng});return;
  }
  if(cur===S.HOLD)return;
  if(cur===S.REQUEST&&isFOH())return;
  const cycle=isFOH()?FOH_CYCLE:BOH_CYCLE;
  const next=cycle[cur]??S.NONE;
  const ng={...state.grid,[tableId]:{...state.grid[tableId],[course]:next}};
  apply({grid:ng});
}

function toggleHold(){
  holdMode=!holdMode;
  renderHeader();
  renderGrid();
}

function toggleHands(){apply({hands:!state.hands});}
function dismissHands(){apply({hands:false});}

function addTable(){
  const label=(document.getElementById("new-tbl-label")?.value??"").trim();
  const pax=parseInt(document.getElementById("new-tbl-pax")?.value??2)||2;
  if(!label)return;
  const id=nextId++;
  const nt=[...state.tables,{id,label,pax}];
  const ng={...state.grid,[id]:Object.fromEntries(state.courses.map(c=>[c,S.NONE]))};
  apply({tables:nt,grid:ng});
}
function removeTable(id){
  const nt=state.tables.filter(x=>x.id!==id);
  const ng={...state.grid};delete ng[id];
  apply({tables:nt,grid:ng});
}
function addCourse(){
  const c=(document.getElementById("new-course-input")?.value??"").trim();
  if(!c||state.courses.includes(c))return;
  const nc=[...state.courses,c];
  const ng={...state.grid};
  Object.keys(ng).forEach(tid=>{ng[tid]={...ng[tid],[c]:S.NONE};});
  apply({courses:nc,grid:ng});
}
function removeCourse(c){
  const nc=state.courses.filter(x=>x!==c);
  const ng={...state.grid};
  Object.keys(ng).forEach(tid=>{const r={...ng[tid]};delete r[c];ng[tid]=r;});
  apply({courses:nc,grid:ng});
}
function sendNote(){
  const input=document.getElementById("note-input");
  const text=(input?.value??"").trim();
  if(!text)return;
  const time=new Date().toLocaleTimeString([],{hour:"2-digit",minute:"2-digit"});
  const nn=[...(state.notes??[]),{id:Date.now(),from:mode,text,tag:noteTag,time}];
  apply({notes:nn});
  if(input)input.value="";
}
function deleteNote(id){
  apply({notes:state.notes.filter(n=>n.id!==id)});
}

// ══ CONTEXT MENU ═══════════════════════════════════════════════════════════════
function openCtx(e,tableId,course){
  ctxCell={tableId,course};
  const menu=document.getElementById("ctx-menu");
  menu.innerHTML=`<div class="ctx-label">OVERRIDE</div>`;
  Object.entries(SD).forEach(([s,d])=>{
    const btn=document.createElement("button");
    btn.className="ctx-item";
    btn.style.cssText=`color:${d.color};background:${d.bg};border-color:${d.border};`;
    btn.textContent=isFOH()?d.foh:d.boh;
    btn.onclick=()=>{forceCell(tableId,course,s);};
    menu.appendChild(btn);
  });
  menu.style.top=e.clientY+"px";
  menu.style.left=e.clientX+"px";
  menu.classList.add("open");
}
function forceCell(tableId,course,status){
  const ng={...state.grid,[tableId]:{...state.grid[tableId],[course]:status}};
  apply({grid:ng});
  document.getElementById("ctx-menu").classList.remove("open");
}
document.addEventListener("click",()=>{document.getElementById("ctx-menu").classList.remove("open");});

// ══ UTILS ═════════════════════════════════════════════════════════════════════
function mkEl(tag,cls){const e=document.createElement(tag);if(cls)e.className=cls;return e;}
function esc(s){const d=document.createElement("div");d.textContent=String(s??"")||"";return d.innerHTML;}
</script>
</body>
</html>
