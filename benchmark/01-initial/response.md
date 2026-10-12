```html
<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no, viewport-fit=cover">
<meta name="theme-color" content="#04060f">
<title>Zeka Uçuş</title>
<style>
:root{
  --bg:#04060f; --cyan:#39e6ff; --cyan2:#8ff6ff; --pink:#ff4d9d;
  --gold:#ffd24a; --green:#54ff9f; --red:#ff5470; --text:#eaf6ff; --dim:#93a6c4;
  --panel:rgba(9,15,32,.82); --line:rgba(120,200,255,.22);
  --font-display:"Bahnschrift","DIN Alternate","Franklin Gothic Medium","Trebuchet MS",system-ui,sans-serif;
  --font-body:"Segoe UI",system-ui,-apple-system,"Helvetica Neue",Arial,sans-serif;
  --font-mono:ui-monospace,"SF Mono","Cascadia Code",Consolas,monospace;
}
*{box-sizing:border-box}
html,body{height:100%}
body{
  margin:0; overflow:hidden; background:var(--bg); color:var(--text);
  font-family:var(--font-body); -webkit-font-smoothing:antialiased;
  -webkit-tap-highlight-color:transparent; user-select:none;
  -webkit-user-select:none; touch-action:none;
}
#bg{position:fixed; inset:0; z-index:0; overflow:hidden}
#bg::before{
  content:""; position:absolute; inset:-25%;
  background:
    radial-gradient(60% 50% at 22% 18%, rgba(57,230,255,.16), transparent 60%),
    radial-gradient(55% 55% at 82% 78%, rgba(255,77,157,.15), transparent 60%),
    radial-gradient(45% 45% at 60% 40%, rgba(120,90,255,.12), transparent 60%);
  animation:drift 26s ease-in-out infinite alternate;
}
#bg::after{
  content:""; position:absolute; inset:0;
  background-image:
    linear-gradient(rgba(120,200,255,.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(120,200,255,.05) 1px, transparent 1px);
  background-size:44px 44px;
  -webkit-mask-image:radial-gradient(circle at 50% 42%, #000 30%, transparent 82%);
  mask-image:radial-gradient(circle at 50% 42%, #000 30%, transparent 82%);
  animation:grid 22s linear infinite;
}
@keyframes drift{from{transform:translate3d(-2%,-1%,0) scale(1)}to{transform:translate3d(3%,2%,0) scale(1.08)}}
@keyframes grid{from{background-position:0 0}to{background-position:0 44px}}

#stage{
  position:relative; z-index:1; height:100%;
  display:flex; align-items:center; justify-content:center;
  padding:clamp(6px,1.4vw,18px);
}
#frame{
  position:relative; width:100%; max-width:480px; aspect-ratio:480/720;
  max-height:100%;
  border:1px solid var(--line); border-radius:14px; overflow:hidden;
  background:linear-gradient(180deg,#070b1c,#04060f);
  box-shadow:0 0 0 1px rgba(57,230,255,.06), 0 24px 70px rgba(0,0,0,.6),
             0 0 46px rgba(57,230,255,.10), inset 0 0 60px rgba(0,0,0,.5);
}
#frame::before{
  content:""; position:absolute; inset:0; z-index:0; pointer-events:none;
  background:
    radial-gradient(120% 60% at 50% -10%, rgba(57,230,255,.10), transparent 60%),
    radial-gradient(120% 60% at 50% 110%, rgba(255,77,157,.10), transparent 60%);
}
#game{position:absolute; inset:0; width:100%; height:100%; display:block; z-index:1; touch-action:none}

/* HUD */
#hud{
  position:absolute; top:0; left:0; right:0; z-index:3;
  display:flex; align-items:center; justify-content:space-between; gap:8px;
  padding:10px 12px; pointer-events:none;
}
.hud-left{display:flex; align-items:center; gap:10px}
#lives{display:flex; gap:5px}
.life{width:16px; height:16px; display:block; filter:drop-shadow(0 0 5px rgba(57,230,255,.7))}
.life.off{opacity:.22; filter:none}
.hud-mid{display:flex; flex-direction:column; align-items:center; line-height:1}
.hud-label{font-family:var(--font-mono); font-size:9px; letter-spacing:.28em; color:var(--dim)}
#score{font-family:var(--font-display); font-weight:700; font-size:clamp(20px,6.4vw,30px);
  color:var(--cyan2); text-shadow:0 0 14px rgba(57,230,255,.55); margin-top:2px}
.hud-right{display:flex; flex-direction:column; align-items:flex-end; line-height:1}
#best{font-family:var(--font-mono); font-size:12px; color:var(--gold);
  text-shadow:0 0 10px rgba(255,210,74,.4); margin-top:2px}
#pauseBtn{
  pointer-events:auto; width:38px; height:38px; border-radius:10px; cursor:pointer;
  border:1px solid var(--line); background:rgba(10,18,36,.7); color:var(--cyan2);
  display:grid; place-items:center; transition:transform .15s, box-shadow .2s, background .2s;
}
#pauseBtn:hover{transform:translateY(-1px); box-shadow:0 0 16px rgba(57,230,255,.4); background:rgba(16,28,52,.85)}
#pauseBtn:active{transform:translateY(0)}
#pauseBtn svg{width:16px; height:16px}

/* Overlays */
.overlay{
  position:absolute; inset:0; z-index:4; display:flex; align-items:center; justify-content:center;
  padding:20px; background:radial-gradient(120% 120% at 50% 30%, rgba(6,10,24,.55), rgba(3,5,14,.86));
  backdrop-filter:blur(3px); -webkit-backdrop-filter:blur(3px);
}
.overlay.hidden{display:none}
.panel{
  width:100%; max-width:340px; text-align:center;
  background:var(--panel); border:1px solid var(--line); border-radius:16px;
  padding:22px 20px 24px; box-shadow:0 0 40px rgba(57,230,255,.12), inset 0 0 40px rgba(0,0,0,.4);
  animation:pop .45s cubic-bezier(.2,.9,.25,1.2) both;
}
@keyframes pop{from{opacity:0; transform:translateY(14px) scale(.96)}to{opacity:1; transform:none}}
.eyebrow{font-family:var(--font-mono); font-size:10px; letter-spacing:.34em; color:var(--cyan); margin-bottom:10px}
.title{
  font-family:var(--font-display); font-weight:800; line-height:.92; margin:0;
  font-size:clamp(38px,12vw,58px); letter-spacing:.02em;
  color:#fff; text-shadow:0 0 22px rgba(57,230,255,.6), 0 0 4px rgba(255,255,255,.4);
}
.title span{color:var(--pink); text-shadow:0 0 22px rgba(255,77,157,.6)}
.tag{color:var(--dim); font-size:13px; margin:12px 0 18px; line-height:1.5}
.howto{
  text-align:left; margin:0 auto 18px; max-width:280px;
  display:grid; gap:9px; font-size:12.5px; color:#cfe0f5;
}
.howto div{display:flex; align-items:center; gap:10px}
.howto b{color:var(--cyan2); font-weight:600}
.keys{display:flex; gap:4px; flex-wrap:wrap}
kbd{
  font-family:var(--font-mono); font-size:11px; color:var(--text);
  background:rgba(120,200,255,.1); border:1px solid var(--line); border-bottom-width:2px;
  border-radius:6px; padding:2px 7px; min-width:22px; text-align:center;
}
.btn{
  font-family:var(--font-display); font-weight:700; letter-spacing:.06em;
  font-size:16px; color:#04121a; cursor:pointer;
  background:linear-gradient(180deg,var(--cyan2),var(--cyan));
  border:none; border-radius:12px; padding:13px 26px; width:100%;
  box-shadow:0 6px 0 rgba(20,120,150,.5), 0 0 26px rgba(57,230,255,.4);
  transition:transform .12s, box-shadow .2s, filter .2s;
}
.btn:hover{filter:brightness(1.08); box-shadow:0 6px 0 rgba(20,120,150,.5), 0 0 34px rgba(57,230,255,.6)}
.btn:active{transform:translateY(4px); box-shadow:0 2px 0 rgba(20,120,150,.5), 0 0 18px rgba(57,230,255,.4)}
.btn.ghost{
  background:transparent; color:var(--cyan2); border:1px solid var(--line);
  box-shadow:none; margin-top:10px;
}
.btn.ghost:hover{background:rgba(57,230,255,.08); box-shadow:0 0 18px rgba(57,230,255,.25)}
.btn:focus-visible, #pauseBtn:focus-visible{outline:2px solid var(--cyan2); outline-offset:3px}
.result{display:flex; gap:14px; justify-content:center; margin:6px 0 18px}
.stat{flex:1; background:rgba(120,200,255,.05); border:1px solid var(--line); border-radius:12px; padding:12px 8px}
.stat .k{font-family:var(--font-mono); font-size:9px; letter-spacing:.24em; color:var(--dim)}
.stat .v{font-family:var(--font-display); font-weight:800; font-size:clamp(24px,8vw,34px); margin-top:4px}
.stat.score .v{color:var(--cyan2); text-shadow:0 0 16px rgba(57,230,255,.5)}
.stat.best .v{color:var(--gold); text-shadow:0 0 16px rgba(255,210,74,.4)}
#newBest{color:var(--green); font-family:var(--font-mono); font-size:11px; letter-spacing:.14em;
  margin-bottom:14px; min-height:14px; text-shadow:0 0 12px rgba(84,255,159,.5)}
#newBest.hidden{display:none}

@media (max-height:520px){
  .panel{padding:16px 16px 18px}
  .tag{margin:8px 0 12px}
  .howto{gap:6px; margin-bottom:12px}
}
@media (prefers-reduced-motion:reduce){
  #bg::before,#bg::after{animation:none}
  .panel{animation:none}
  .btn,#pauseBtn{transition:none}
}
</style>
</head>
<body>
<div id="bg" aria-hidden="true"></div>

<div id="stage">
  <div id="frame">
    <canvas id="game"></canvas>

    <div id="hud">
      <div class="hud-left">
        <div id="lives" aria-label="Can"></div>
      </div>
      <div class="hud-mid">
        <span class="hud-label">SKOR</span>
        <span id="score">0</span>
      </div>
      <div class="hud-right">
        <span class="hud-label">REKOR</span>
        <span id="best">0</span>
        <button id="pauseBtn" aria-label="Duraklat" title="Duraklat (P)">
          <svg viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
            <rect x="6" y="5" width="4" height="14" rx="1"></rect>
            <rect x="14" y="5" width="4" height="14" rx="1"></rect>
          </svg>
        </button>
      </div>
    </div>

    <!-- Başlangıç -->
    <div class="overlay" id="startScreen">
      <div class="panel">
        <div class="eyebrow">NEON UZAY SAVAŞI</div>
        <h1 class="title">ZEKA<br><span>UÇUŞ</span></h1>
        <p class="tag">Düşmanları düşür, rekoru kır. Gökyüzü seni bekliyor.</p>
        <div class="howto">
          <div><span class="keys"><kbd>W</kbd><kbd>A</kbd><kbd>S</kbd><kbd>D</kbd><kbd>↑</kbd><kbd>↓</kbd><kbd>←</kbd><kbd>→</kbd></span><b>Hareket</b></div>
          <div><span class="keys"><kbd>Boşluk</kbd></span><b>Ateş et</b></div>
          <div><span class="keys"><kbd>P</kbd></span><b>Duraklat</b></div>
          <div><span class="keys"><kbd>📱</kbd></span><b>Sürükle + otomatik ateş</b></div>
        </div>
        <button class="btn" id="startBtn">BAŞLA</button>
      </div>
    </div>

    <!-- Duraklatma -->
    <div class="overlay hidden" id="pauseScreen">
      <div class="panel">
        <div class="eyebrow">MOLA</div>
        <h1 class="title" style="font-size:clamp(30px,10vw,44px)">DURAKLATILDI</h1>
        <p class="tag">Nefes al. Gökyüzü bekliyor.</p>
        <button class="btn" id="resumeBtn">DEVAM ET</button>
        <button class="btn ghost" id="restartBtn">YENİDEN BAŞLAT</button>
      </div>
    </div>

    <!-- Oyun Sonu -->
    <div class="overlay hidden"