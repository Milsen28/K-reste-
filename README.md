<!doctype html>
<html lang="da">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover" />
  <title>Til dig ❤️</title>
  <meta name="theme-color" content="#b0002a" />
  <style>
    :root{
      --bg1:#140014;
      --bg2:#3b0012;
      --accent:#ff2a6d;
      --accent2:#ff4d4d;
      --card:#ffffff10;
      --stroke:#ffffff20;
      --text:#fff;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji";
      color:var(--text);
      overflow:hidden;
      background:
        radial-gradient(1200px 800px at 20% 10%, #ff2a6d25 0%, transparent 55%),
        radial-gradient(900px 700px at 80% 30%, #ff4d4d22 0%, transparent 60%),
        linear-gradient(160deg, var(--bg1), var(--bg2));
    }

    /* Floating hearts canvas */
    #hearts{
      position:fixed;
      inset:0;
      width:100%;
      height:100%;
      z-index:0;
      pointer-events:none;
    }

    /* Main “card” area */
    .wrap{
      position:relative;
      z-index:1;
      height:100%;
      display:flex;
      align-items:center;
      justify-content:center;
      padding:24px;
    }
    .card{
      width:min(520px, 92vw);
      border-radius:24px;
      background: linear-gradient(180deg, #ffffff12, #ffffff08);
      border:1px solid var(--stroke);
      box-shadow: 0 25px 70px #00000055;
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      padding:20px 18px 18px;
    }
    .title{
      display:flex;
      align-items:center;
      gap:10px;
      font-weight:800;
      letter-spacing:0.3px;
      font-size:22px;
      margin:0 0 8px 0;
    }
    .subtitle{
      margin:0 0 14px 0;
      opacity:.9;
      line-height:1.35;
      font-size:14.5px;
    }

    .actions{
      display:flex;
      gap:10px;
      flex-wrap:wrap;
      margin-top:14px;
    }
    button{
      appearance:none;
      border:none;
      border-radius:14px;
      padding:12px 14px;
      font-weight:700;
      font-size:15px;
      cursor:pointer;
      color:#fff;
      background: linear-gradient(135deg, var(--accent), var(--accent2));
      box-shadow: 0 12px 30px #ff2a6d33;
      transition: transform .08s ease, filter .2s ease;
      touch-action: manipulation;
    }
    button:active{ transform: translateY(1px) scale(0.99); }
    .ghost{
      background: #ffffff10;
      border:1px solid #ffffff22;
      box-shadow:none;
      color:#fff;
    }

    .hint{
      margin-top:10px;
      font-size:12.5px;
      opacity:.85;
    }

    /* Modal */
    .modal{
      position:fixed;
      inset:0;
      display:none;
      align-items:center;
      justify-content:center;
      padding:22px;
      z-index:5;
      background: #00000066;
      backdrop-filter: blur(6px);
      -webkit-backdrop-filter: blur(6px);
    }
    .modal.show{ display:flex; }
    .sheet{
      width:min(680px, 94vw);
      max-height:78vh;
      overflow:auto;
      border-radius:22px;
      border:1px solid #ffffff24;
      background: linear-gradient(180deg, #2a0012cc, #120008cc);
      box-shadow: 0 30px 90px #00000088;
      padding:18px 18px 14px;
    }
    .sheet h2{
      margin:0 0 10px 0;
      font-size:18px;
      display:flex;
      align-items:center;
      gap:10px;
    }
    .sheet p{
      margin:0 0 10px 0;
      line-height:1.5;
      font-size:15px;
      white-space:pre-line;
    }
    .sheet .closeRow{
      display:flex;
      justify-content:flex-end;
      margin-top:10px;
    }
    .tiny{
      font-size:12px;
      opacity:.8;
      margin-top:6px;
    }

    /* Small “interactive map” gimmick: a tappable heart-map */
    .map{
      margin-top:14px;
      border-radius:18px;
      border:1px solid #ffffff22;
      background: radial-gradient(600px 220px at 50% 20%, #ff2a6d22, transparent 60%),
                  linear-gradient(180deg, #ffffff10, #ffffff06);
      padding:12px;
    }
    .mapTitle{
      font-weight:800;
      font-size:13px;
      opacity:.95;
      display:flex;
      align-items:center;
      justify-content:space-between;
      margin-bottom:10px;
    }
    .grid{
      display:grid;
      grid-template-columns: repeat(6, 1fr);
      gap:8px;
    }
    .pin{
      aspect-ratio:1/1;
      border-radius:14px;
      border:1px solid #ffffff22;
      background:#ffffff0c;
      display:flex;
      align-items:center;
      justify-content:center;
      font-size:18px;
      cursor:pointer;
      user-select:none;
      transition: transform .1s ease, background .2s ease;
    }
    .pin:active{ transform: scale(0.98); }
    .pin:hover{ background:#ffffff12; }
  </style>
</head>

<body>
  <canvas id="hearts"></canvas>

  <div class="wrap">
    <div class="card">
      <h1 class="title">Til dig, min skat ❤️</h1>
      <p class="subtitle">
        Tryk på knappen for at åbne dit kærlighedsbrev – og tryk på hjerterne nedenfor for små overraskelser.
      </p>

      <div class="actions">
        <button id="openLetter">Åbn brevet 💌</button>
        <button class="ghost" id="moreHearts">Flere hjerter ✨</button>
      </div>

      <div class="map" aria-label="Kærlighedskort">
        <div class="mapTitle">
          <span>Kærlighedskort (tryk på hjerter)</span>
          <span style="opacity:.8;font-size:12px">❤️🧭</span>
        </div>
        <div class="grid" id="grid"></div>
        <div class="tiny">Tip: Hvis du trykker mange gange, bliver der ekstra hjerter i luften.</div>
      </div>

      <div class="hint">Åbner bedst på telefon i Safari/Chrome.</div>
    </div>
  </div>

  <!-- Modal -->
  <div class="modal" id="modal" role="dialog" aria-modal="true" aria-labelledby="modalTitle">
    <div class="sheet">
      <h2 id="modalTitle">Kære skat 💖</h2>
      <p id="letterText"></p>
      <div class="closeRow">
        <button class="ghost" id="closeModal">Luk</button>
      </div>
    </div>
  </div>

  <script>
    // ====== Your letter text (exactly as provided) ======
    const LETTER =
`Kære skat,
Tak for dig. Tak for alt du er og alt du giver mig. Du er uden tvivl den helt rigtige for mig, og jeg glæder mig til et liv med dig.

Du er den sødeste, dejligste og mest omsorgsfulde og betænksomme person jeg har mødt. Du tænker altid på alle andre før dig selv, og vil gerne have alle er glade.
MEN SKAT - nu er jeg her, og så sørger jeg for du altid er glad! Det vil jeg gøre mit bedste for resten af livet med dig.

Jeg elsker dig, så uendelig højt!
Kys din.`;

    // ====== Modal behavior ======
    const modal = document.getElementById("modal");
    const letterText = document.getElementById("letterText");
    document.getElementById("openLetter").addEventListener("click", () => {
      letterText.textContent = LETTER;
      modal.classList.add("show");
      burst(18);
    });
    document.getElementById("closeModal").addEventListener("click", () => modal.classList.remove("show"));
    modal.addEventListener("click", (e) => { if(e.target === modal) modal.classList.remove("show"); });

    // ====== Cute “love map” pins ======
    const grid = document.getElementById("grid");
    const surprises = [
      "Du er mit hjem ❤️",
      "Du gør mig tryg 🫶",
      "Jeg vælger dig — altid 💍",
      "Mit hjerte banker for dig 💓",
      "Min yndlingsperson 🥰",
      "Tak for din ro 🌙",
      "Tak for dit grin ✨",
      "Du er min bedste dag ☀️",
      "Kys her 😘",
      "Kram her 🤍",
      "Mit livs kærlighed 💘",
      "Dig + mig = os 💞"
    ];

    function toast(msg){
      // Lightweight toast using modal title area: small temporary overlay
      const t = document.createElement("div");
      t.textContent = msg;
      t.style.position = "fixed";
      t.style.left = "50%";
      t.style.bottom = "18px";
      t.style.transform = "translateX(-50%)";
      t.style.padding = "10px 12px";
      t.style.borderRadius = "14px";
      t.style.background = "#00000088";
      t.style.border = "1px solid #ffffff22";
      t.style.backdropFilter = "blur(6px)";
      t.style.color = "white";
      t.style.fontWeight = "700";
      t.style.zIndex = 10;
      t.style.maxWidth = "92vw";
      t.style.textAlign = "center";
      document.body.appendChild(t);
      setTimeout(()=>{ t.style.opacity = "0"; t.style.transition="opacity .25s ease"; }, 1400);
      setTimeout(()=>t.remove(), 1750);
    }

    for(let i=0;i<18;i++){
      const d = document.createElement("div");
      d.className = "pin";
      d.textContent = "❤️";
      d.addEventListener("click", () => {
        const msg = surprises[Math.floor(Math.random()*surprises.length)];
        toast(msg);
        burst(8);
      });
      grid.appendChild(d);
    }

    document.getElementById("moreHearts").addEventListener("click", ()=> burst(28));

    // ====== Floating hearts animation (canvas) ======
    const canvas = document.getElementById("hearts");
    const ctx = canvas.getContext("2d");
    let W, H, DPR;

    function resize(){
      DPR = Math.max(1, Math.min(2, window.devicePixelRatio || 1));
      W = canvas.width = Math.floor(window.innerWidth * DPR);
      H = canvas.height = Math.floor(window.innerHeight * DPR);
      canvas.style.width = window.innerWidth + "px";
      canvas.style.height = window.innerHeight + "px";
      ctx.setTransform(DPR,0,0,DPR,0,0);
    }
    window.addEventListener("resize", resize);
    resize();

    const palette = ["#ff2a6d","#ff4d4d","#ff3b7a","#d8003a","#ff1744","#ff5a7d","#c2185b"];
    const hearts = [];

    function heartPath(x,y,s,rot){
      ctx.save();
      ctx.translate(x,y);
      ctx.rotate(rot);
      ctx.scale(s,s);
      ctx.beginPath();
      // simple heart shape
      ctx.moveTo(0, 0.35);
      ctx.bezierCurveTo(-0.9, -0.2, -0.6, -1.1, 0, -0.55);
      ctx.bezierCurveTo(0.6, -1.1, 0.9, -0.2, 0, 0.35);
      ctx.closePath();
      ctx.restore();
    }

    function spawn(n=1, x=null, y=null){
      for(let i=0;i<n;i++){
        hearts.push({
          x: (x ?? (Math.random()*window.innerWidth)),
          y: (y ?? (window.innerHeight + 20 + Math.random()*60)),
          vy: 0.6 + Math.random()*1.4,
          vx: (Math.random()-0.5)*0.6,
          s: 8 + Math.random()*18,
          rot: (Math.random()-0.5)*0.7,
          vr: (Math.random()-0.5)*0.02,
          a: 0.65 + Math.random()*0.35,
          c: palette[Math.floor(Math.random()*palette.length)]
        });
      }
    }

    function burst(n){
      spawn(n, window.innerWidth*0.5 + (Math.random()-0.5)*80, window.innerHeight*0.7 + (Math.random()-0.5)*60);
    }

    // Initial gentle hearts
    spawn(26);

    // Tap anywhere -> extra hearts
    window.addEventListener("pointerdown", (e)=>{
      spawn(10, e.clientX, e.clientY);
    }, {passive:true});

    function tick(){
      ctx.clearRect(0,0,window.innerWidth, window.innerHeight);

      // subtle glow vignette
      ctx.save();
      ctx.globalAlpha = 0.25;
      ctx.beginPath();
      ctx.arc(window.innerWidth*0.3, window.innerHeight*0.2, Math.min(window.innerWidth, window.innerHeight)*0.6, 0, Math.PI*2);
      ctx.fillStyle = "#ff2a6d";
      ctx.fill();
      ctx.restore();

      for(let i=hearts.length-1;i>=0;i--){
        const h = hearts[i];
        h.x += h.vx;
        h.y -= h.vy;
        h.rot += h.vr;
        h.a -= 0.0018;

        ctx.save();
        ctx.globalAlpha = Math.max(0, h.a);
        ctx.fillStyle = h.c;
        heartPath(h.x, h.y, h.s/20, h.rot);
        ctx.fill();
        ctx.restore();

        if(h.y < -60 || h.a <= 0){
          hearts.splice(i,1);
        }
      }

      // keep some hearts always floating
      if(hearts.length < 35) spawn(2);

      requestAnimationFrame(tick);
    }
    tick();
  </script>
</body>
</html>
