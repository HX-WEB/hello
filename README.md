<!doctype html>
<html lang="zh-CN">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>紧急警报！</title>
<style>
  :root{--bg:#0b1220;--accent:#ff4757;--accent2:#ffd24a}
  html,body{height:100%;margin:0;font-family: "Helvetica Neue", Arial, sans-serif;background:linear-gradient(120deg,#081226 0%, #0d1b2a 50%, #07111b 100%);color:#fff}
  .wrap{min-height:100%;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:20px;box-sizing:border-box}
  .alert {
    background:rgba(255,255,255,0.04);
    border:2px solid rgba(255,71,87,0.14);
    padding:28px;
    width:100%;
    max-width:720px;
    border-radius:12px;
    text-align:center;
    box-shadow: 0 10px 30px rgba(0,0,0,0.5);
    position:relative;
    overflow:hidden;
  }
  h1{margin:0 0 8px;font-size:28px;letter-spacing:1px}
  p.lead{margin:6px 0 18px;color:#d9e6ffcc}
  .meter{
    height:18px;
    background:rgba(255,255,255,0.06);
    border-radius:9px;
    overflow:hidden;
    margin:14px 0 22px;
  }
  .meter > i{
    display:block;height:100%;width:0%;
    background:linear-gradient(90deg,var(--accent),var(--accent2));
    transition:width 800ms ease;
  }
  .btns{display:flex;gap:12px;justify-content:center;flex-wrap:wrap}
  .btn{
    padding:10px 16px;border-radius:10px;border:0;cursor:pointer;font-weight:700;
    background:linear-gradient(90deg,#2f3640,#3b3f46);color:#fff;box-shadow:0 6px 18px rgba(0,0,0,0.35);
  }
  .btn.ack{background:linear-gradient(90deg,#1dd1a1,#10ac84)}
  .small{font-size:13px;color:#cbd7ff88;margin-top:10px}
  /* silly animation */
  .floating-emoji{position:absolute;right:-60px;top:-80px;font-size:140px;transform:rotate(-25deg);opacity:0.12;pointer-events:none}
  .confetti{pointer-events:none;position:absolute;inset:0}
  .hidden{display:none}
  footer{margin-top:20px;color:#b7c6ff66;font-size:13px}
</style>
</head>
<body>
<div class="wrap">
  <div class="alert" role="alert" aria-live="polite">
    <div class="floating-emoji">💥</div>
    <h1>紧急警报 —— 检测到异常活动！</h1>
    <p class="lead">系统正在尝试修复问题，请勿关闭此页面。进度：</p>

    <div class="meter" aria-hidden="true"><i id="bar"></i></div>
    <div class="btns">
      <button class="btn" id="panic">查看详情</button>
      <button class="btn ack" id="calm">我没事，点我解除</button>
    </div>

    <div id="extra" class="hidden" style="margin-top:18px">
      <p style="color:#ffd6a5;font-weight:700">详情：检测到 1 个可疑的彩蛋进程（emoji.exe）。正在隔离...</p>
      <p class="small">（这是一个整蛊页面 — 没有危险 — 点击绿色按钮即可结束。）</p>
    </div>

    <canvas class="confetti" id="confetti"></canvas>
  </div>

  <footer>提示：请善意整蛊，不要用来欺骗或恐吓他人。</footer>
</div>

<script>
/* 轻量的伪“进度+彩带”整蛊逻辑 — 无任何恶意行为 */
const bar = document.getElementById('bar');
const panic = document.getElementById('panic');
const calm = document.getElementById('calm');
const extra = document.getElementById('extra');

let pct = 0;
function step() {
  pct += Math.floor(Math.random()*12)+8;
  if (pct > 100) pct = 100;
  bar.style.width = pct + '%';
  if (pct < 100) setTimeout(step, 650 + Math.random()*700);
  else {
    // 当完成后显示爆点
    explode();
  }
}
setTimeout(step, 600);

/* 按钮行为 */
panic.addEventListener('click', ()=> {
  extra.classList.remove('hidden');
  // 快速推进
  pct = Math.max(pct, 60);
  bar.style.width = (pct+10) + '%';
});
calm.addEventListener('click', ()=> {
  // 结束并显示“整蛊成功”
  showGoodNews();
});

/* 完成触发的动画（彩带） */
function explode(){
  const canvas = document.getElementById('confetti');
  const ctx = canvas.getContext('2d');
  function resize(){ canvas.width = innerWidth; canvas.height = innerHeight; }
  resize(); window.addEventListener('resize', resize);
  const pieces = [];
  for(let i=0;i<120;i++){
    pieces.push({
      x: Math.random()*canvas.width,
      y: Math.random()*canvas.height - canvas.height/2,
      r: Math.random()*6+4,
      d: Math.random()*20+20,
      vx: (Math.random()-0.5)*6,
      vy: Math.random()*4+2,
      color: `hsl(${Math.random()*360} 80% 65%)`,
      rot: Math.random()*360,
      vr: (Math.random()-0.5)*10
    });
  }
  let t=0;
  function loop(){
    t++;
    ctx.clearRect(0,0,canvas.width,canvas.height);
    for(const p of pieces){
      p.x += p.vx;
      p.y += p.vy + Math.sin((t+p.d)/20)*0.6;
      p.rot += p.vr;
      ctx.save();
      ctx.translate(p.x,p.y);
      ctx.rotate(p.rot*Math.PI/180);
      ctx.fillStyle = p.color;
      ctx.fillRect(-p.r/2,-p.r/2,p.r,p.r*1.6);
      ctx.restore();
    }
    if (t < 280) requestAnimationFrame(loop);
    else canvas.style.display = 'none';
  }
  loop();
}

/* 结束页面显示 */
function showGoodNews(){
  const main = document.querySelector('.alert');
  main.innerHTML = `
    <h1 style="color:#7effc8">整蛊成功 🎉</h1>
    <p style="color:#e6fff5">你发现了一个玩笑页面 — 一切安全无害。</p>
    <p style="margin-top:14px">要回放整蛊效果，按下面按钮：</p>
    <div style="margin-top:12px">
      <button class="btn" id="replay">重放</button>
      <button class="btn ack" id="share">复制分享链接</button>
    </div>
    <p style="margin-top:10px;color:#dbe9ffcc;font-size:13px">温馨提示：请勿用于欺骗、诈骗或造成心理伤害。</p>
  `;
  document.getElementById('replay').addEventListener('click', ()=> location.reload());
  document.getElementById('share').addEventListener('click', ()=> {
    const txt = '这是一个整蛊页面（无害）—— 快去看看： ' + location.href;
    navigator.clipboard?.writeText(txt).then(()=> alert('已复制分享文本！'));
  });
}
</script>
