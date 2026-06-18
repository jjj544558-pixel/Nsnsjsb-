<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="HexCode">
<title>HexCode — iOS 26.2</title>
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  body {
    background: #08040a;
    color: #ffe0d0;
    font-family: 'Courier New', monospace;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    min-height: 100vh;
    overflow-x: hidden;
    user-select: none;
    padding: 20px 0;
  }
  #app {
    width: 100%;
    max-width: 440px;
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 14px;
  }
  .logo {
    font-size: 40px;
    font-weight: 900;
    background: linear-gradient(135deg, #ff3300, #ff6600, #ff9900);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    text-align: center;
    letter-spacing: 3px;
  }
  .subtitle { color: #ff6600; font-size: 11px; text-align: center; letter-spacing: 2px; }
  .badge-row { display: flex; gap: 7px; width: 100%; justify-content: center; flex-wrap: wrap; }
  .badge {
    background: rgba(255,60,0,0.06);
    border: 1px solid rgba(255,60,0,0.18);
    border-radius: 10px;
    padding: 8px 13px;
    text-align: center;
    font-size: 10px;
  }
  .badge span { color: #883300; display: block; font-size: 8px; text-transform: uppercase; letter-spacing: 2px; }
  .badge strong { color: #ff7733; font-size: 14px; }
  #main-btn {
    width: 150px; height: 150px; border-radius: 50%;
    border: 3px solid #ff3300;
    background: radial-gradient(circle at 40% 40%, #200800, #0a0200);
    color: #ff4400; font-size: 15px; font-weight: bold; cursor: pointer;
    font-family: 'Courier New', monospace;
    text-shadow: 0 0 25px #ff3300;
    box-shadow: 0 0 50px rgba(255,51,0,0.45), inset 0 0 50px rgba(255,51,0,0.05);
    transition: all 0.1s;
  }
  #main-btn:active { transform: scale(0.88); border-color: #ff9900; box-shadow: 0 0 80px rgba(255,153,0,0.7); }
  #main-btn.done { border-color: #00ff55; color: #00ff55; text-shadow: 0 0 25px #00ff55; box-shadow: 0 0 50px rgba(0,255,85,0.45); }
  #main-btn.running { animation: hexGlow 0.7s ease-in-out infinite; }
  @keyframes hexGlow {
    0%, 100% { box-shadow: 0 0 50px rgba(255,51,0,0.45); border-color: #ff3300; }
    50% { box-shadow: 0 0 90px rgba(255,153,0,0.8); border-color: #ff9900; }
  }
  #prog-wrap { width: 100%; height: 5px; background: rgba(255,255,255,0.04); border-radius: 3px; overflow: hidden; }
  #prog-fill { height: 100%; background: linear-gradient(90deg, #ff3300, #ff6600, #ff9900); width: 0%; transition: width 0.3s; }
  #stage { color: #ff6600; font-size: 11px; text-align: center; min-height: 16px; }
  #log-box { width: 100%; max-height: 200px; overflow-y: auto; background: rgba(0,0,0,0.55); border-radius: 10px; padding: 10px; font-size: 9px; display: none; border: 1px solid rgba(255,51,0,0.18); }
  #log-box.open { display: block; }
  .ll { margin: 2px 0; } .ll.e { color: #ff3333; } .ll.g { color: #00ff55; } .ll.w { color: #ff9900; } .ll.i { color: #ff7733; }
  #actions { display: none; width: 100%; gap: 8px; flex-direction: column; }
  #actions.open { display: flex; }
  .act { width: 100%; padding: 13px; border-radius: 10px; border: 1px solid rgba(255,255,255,0.07); background: rgba(255,255,255,0.02); color: #fff; font-family: 'Courier New', monospace; font-size: 13px; cursor: pointer; text-align: center; }
  .act.g { border-color: #00ff5544; color: #00ff55; } .act.o { border-color: #ff660044; color: #ff6600; } .act.y { border-color: #ff990044; color: #ff9900; }
  #pkg-list { display: none; width: 100%; }
  #pkg-list.open { display: block; }
  .pkg-cat { color: #ff6600; font-size: 11px; font-weight: bold; margin: 10px 0 4px; border-bottom: 1px solid rgba(255,51,0,0.25); padding-bottom: 3px; }
  .pkg-row { display: flex; justify-content: space-between; align-items: center; padding: 6px 10px; background: rgba(255,60,0,0.03); border-radius: 6px; margin: 2px 0; font-size: 11px; }
  .pkg-row span { color: #ccc; } .pkg-row small { color: #555; font-size: 9px; }
  .reboot-note { color: #ff6600; font-size: 10px; text-align: center; margin-top: -4px; display: none; }
  .reboot-note.show { display: block; }
  #particles { position: fixed; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 999; }
  .pt { position: absolute; width: 4px; height: 4px; border-radius: 50%; animation: ptUp 0.65s ease-out forwards; }
  @keyframes ptUp { 0% { opacity: 1; transform: translateY(0) scale(1); } 100% { opacity: 0; transform: translateY(-90px) scale(0); } }
</style>
</head>
<body>
<div id="app">
  <div class="logo">HEXCODE</div>
  <div class="subtitle">iOS 26.2 • Build 28C5047f • Semi-Untethered • 120 Apps</div>
  <div class="badge-row">
    <div class="badge"><span>Target</span><strong>iOS 26.2</strong></div>
    <div class="badge"><span>Build</span><strong>28C5047f</strong></div>
    <div class="badge"><span>Type</span><strong>Semi-Untethered</strong></div>
    <div class="badge"><span>Apps</span><strong>120</strong></div>
    <div class="badge"><span>Chip</span><strong>A19–A20</strong></div>
  </div>
  <button id="main-btn" onclick="fire()">💀<br>PWN</button>
  <div id="prog-wrap"><div id="prog-fill"></div></div>
  <div id="stage"></div>
  <div id="log-box"></div>
  <div class="reboot-note" id="reboot-note">⚠️ Semi-Untethered — Re-jailbreak via app after reboot</div>
  <div id="actions">
    <button class="act g" onclick="respring()">🔄 Respring</button>
    <button class="act o" onclick="togglePkgs()">📦 View 120 Packages</button>
    <button class="act y" onclick="copyRepo()">📋 Copy Repo URL</button>
  </div>
  <div id="pkg-list"></div>
</div>
<div id="particles"></div>
<script>
let running=false,done=false,logs=[];
function log(m,t='i'){logs.push({m,t});let e=document.getElementById('log-box');e.innerHTML=logs.map(l=>`<div class="ll ${l.t}">[${new Date().toLocaleTimeString()}] ${l.m}</div>`).join('');e.scrollTop=e.scrollHeight}
function prog(p,t){document.getElementById('prog-fill').style.width=p+'%';document.getElementById('stage').textContent=t}
function particles(x,y){let c=document.getElementById('particles');for(let i=0;i<18;i++){let d=document.createElement('div');d.className='pt';d.style.left=(x+(Math.random()-0.5)*80)+'px';d.style.top=(y+(Math.random()-0.5)*80)+'px';d.style.animationDelay=Math.random()*0.35+'s';d.style.background=['#ff3300','#ff6600','#ff9900','#ff4400','#ffcc00'][Math.floor(Math.random()*5)];c.appendChild(d);setTimeout(()=>d.remove(),650)}}
function fire(){if(running||done)return;running=true;let b=document.getElementById('main-btn');b.classList.add('running');document.getElementById('log-box').classList.add('open');document.getElementById('reboot-note').classList.add('show');
let s=[{p:8,t:'Stage 1/7: WebKit JIT RCE',m:'CVE-2028-0012 — DFG JIT Type Confusion',d:900},{p:16,t:'Stage 1/7: WebKit JIT RCE',m:'JIT spray deployed — ROP chain 312 bytes',d:500,type:'g'},{p:24,t:'Stage 2/7: PAC Bypass',m:'CVE-2028-0019 — Neural Engine PAC bypass',d:800},{p:32,t:'Stage 2/7: PAC Bypass',m:'Thread bound to NPU core 3 — PAC oracle located',d:400,type:'g'},{p:40,t:'Stage 3/7: SEP Compromise',m:'CVE-2028-0023 — SEP firmware downgrade',d:1000},{p:48,t:'Stage 3/7: SEP Compromise',m:'SEP keys extracted — all crypto functions unlocked',d:500,type:'w'},{p:56,t:'Stage 4/7: AMFI + Sandbox',m:'AMFI trust cache cleared — unsigned code OK',d:700},{p:64,t:'Stage 4/7: AMFI + Sandbox',m:'Sandbox escape via IOHIDUserClient',d:400,type:'g'},{p:72,t:'Stage 5/7: Root Filesystem',m:'Remounting / as read-write',d:600},{p:80,t:'Stage 5/7: Root Filesystem',m:'SIP neutered — full system access',d:400,type:'g'},{p:88,t:'Stage 6/7: Bootstrap',m:'Installing Sileo + daemon...',d:1200},{p:94,t:'Stage 6/7: Bootstrap',m:'SSH enabled — root:alpine',d:400,type:'w'},{p:98,t:'Stage 7/7: Packages',m:'Deploying 120 packages...',d:1500},{p:100,t:'COMPLETE',m:'💀 HexCode v2 — iOS 26.2 — 120 apps LIVE',d:300,type:'g'},{p:100,t:'COMPLETE',m:'Semi-Untethered: re-jailbreak via app after reboot',d:100,type:'w'}];
let i=0;function n(){if(i>=s.length){done=true;running=false;b.classList.remove('running');b.classList.add('done');b.innerHTML='✓<br>PWNED';document.getElementById('stage').textContent='Jailbroken — 120 Apps Installed';document.getElementById('actions').classList.add('open');buildPkgList();log('💀 HexCode complete. Repo: https://repo.hexcode.io','g');return}let st=s[i];prog(st.p,st.t);log(st.m,st.type||'i');i++;setTimeout(n,st.d)}n()}
const pkgs={"⚙️ System (20)":["Filza Pro","iCleaner Pro","AppSync Unified","NewTerm 4","OpenSSH","PowerModule 2","CCModules X","SwipeExpander X","CarBridge Elite","Snapper 3","CacheClearer Pro","ExactTime 3","SafariPlus 2","LetMeKnow X","KillX Ultimate","CrashReporter 5","System Info 2","TetherMe X","NetworkManager Pro","Fileza 2"],"🎮 Gaming (25)":["iGameGod","DolphiniOS","RetroArch","PPSSPP Gold","Delta Emulator","Flycast X","DuckStation","Provenance HD","Cheat Engine iOS","GameGem Pro","xModGames Elite","LocalIAPStore 3","SaveCloud Pro","GameSeagull X","EightBitDo Connect","ControllerX 2","Play! Emulator","MAME4iOS X","ScummVM HD","DosBox Turbo","UTM SE","ArcadeMania Pro","RetroX Ultimate","Delta X Pro","EmuHub Complete"],"🎨 UI & Theming (20)":["Snowboard 5","Axon Redux","Atria","Lynx 3","Dotto+","NiceBarX 26","Cylinder X","Eclipse X","Springtomize 26","Dress","Folds 2","Velvet 3","Kalm 2","Jellyfish X","Xen HTML 3","Frame 2","Viola Dark Pro","Bubblegum 2","Shuffle X","Quart Pro"],"🔒 Security (20)":["BioProtect XS","Crane X","Spoofy 2","Relay Pro","Shadow X","A-Bypass 2","KernBypass X","FlyJB X2","Liberty Lite 2","HideJB Pro","xCon 2","Flex 5","Choicy","TweakConfigurator 2","iSecureOS Pro","Firewall IP X","Guardian Pro","NoTrack 2","PrivateDns X","VPN Master Ultimate"],"🎬 Media (20)":["YouTube Plus","Spoti++","Watusi 5","Rocket X","TikTok God","TwitchPlus Pro","Cercube Max","AnimationsBeFastPlus 2","DLeasy Pro","Social Downloader X","MediaBox HD","MovieBox Pro","Kodi iOS 2","VLC Mobile Pro","Infuse Pro","nPlayer Plus","DS Video X","Plex++","Tidal++ Pro","Deezer++ Ultimate"],"🛠️ Developer (10)":["FLEX 5","Hopper Remote","ClassDump Dyld","osLog Pro","NetKill UI X","SysSecInfo 2","Decryptor X","Clutch 3","bfdecrypt 2","iTweak Dev Pro"],"💀 HexCode Exclusive (5)":["HexDaemon X","DarkHex Theme Pro","HexRepo Manager","HexTerm Pro","God Mode X"]};
function buildPkgList(){let e=document.getElementById('pkg-list'),t=0,h='';for(let[c,i]of Object.entries(pkgs)){h+=`<div class="pkg-cat">${c} (${i.length})</div>`;i.forEach(p=>{h+=`<div class="pkg-row"><span>${p}</span><small>✓ installed</small></div>`;t++})}h+=`<div style="text-align:center;color:#ff6600;margin-top:10px;font-size:10px;font-weight:bold;">Total: ${t} Packages Installed</div>`;e.innerHTML=h}
function respring(){log('Respringing...','i');document.getElementById('stage').textContent='Respringing...';setTimeout(()=>{document.getElementById('stage').textContent='Respring complete';log('Respring successful','g')},2000)}
function togglePkgs(){let e=document.getElementById('pkg-list');e.classList.toggle('open');if(e.classList.contains('open'))buildPkgList()}
function copyRepo(){navigator.clipboard.writeText('deb https://repo.hexcode.io/ ./ stable');log('Repo URL copied','g')}
document.getElementById('main-btn').addEventListener('click',function(e){if(!running&&!done)particles(e.clientX,e.clientY)});
</script>
</body>
</html>
