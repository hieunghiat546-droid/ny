<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="utf-8">
<title>Iu mỗi chị</title>
<style>
  body {
    margin: 0;
    background: #000000;
    overflow: hidden;
    perspective: 1000px;
    font-family: sans-serif;
  }
  .scene {
    position: fixed;
    inset: 0;
    transform-style: preserve-3d;
  }
  .floating {
    position: absolute;
    top: -10vh;
    left: 0;
    font-weight: 700;
    color: #fff;
    text-shadow: 0 0 8px #0ff, 0 0 16px #0ff;
    white-space: nowrap;
    pointer-events: none;
    animation: fall linear forwards;
  }
  @keyframes fall {
    from { transform: translate3d(var(--x), -20vh, var(--z)) scale(var(--scale)); }
    to   { transform: translate3d(var(--x), 110vh, var(--z)) scale(var(--scale)); }
  }
  .heart {
    color: red;
    text-shadow: 0 0 8px #f33, 0 0 18px #f33;
  }

  /* Trái tim hồng bự ở giữa */
  .big-heart {
    position: fixed;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    font-size: 200px; /* LỚN HƠN */
    color: hotpink;
    text-shadow: 0 0 30px #f0c, 0 0 60px #f0c;
    animation: pulse 1.5s infinite;
    pointer-events: none;
    z-index: 5;
  }
  @keyframes pulse {
    0%, 100% { transform: translate(-50%, -50%) scale(1); }
    50% { transform: translate(-50%, -50%) scale(1.4); }
  }

  .burst {
    position: fixed;
    top: 50%; left: 50%;
    font-size: 28px;
    color: hotpink;
    opacity: 1;
    pointer-events: none;
    animation: burstAnim 1.2s forwards;
  }
  @keyframes burstAnim {
    to {
      transform: translate(var(--dx), var(--dy)) scale(0.5);
      opacity: 0;
    }
  }

  .play {
    position: fixed;
    top: 50%; left: 50%;
    transform: translate(-50%,-50%);
    background: #111;
    color: #fff;
    padding: 14px 20px;
    border-radius: 999px;
    font-weight: bold;
    cursor: pointer;
    z-index: 10;
    border: 1px solid #444;
  }
  #yt-holder {
    position: fixed;
    width: 1px; height: 1px;
    left: -9999px; top: -9999px;
    opacity: 0; pointer-events: none;
  }
</style>
</head>
<body>
  <div class="scene" id="scene"></div>
  <div class="big-heart">❤</div>
  <button class="play" id="playBtn">▶️ Bật nhạc & hiệu ứng</button>
  <div id="yt-holder"></div>

<script>
const scene = document.getElementById("scene");
const TOTAL = 200;
const TEXTS = ["em thích chị", "chị làm người yêu emm nha!","iuu chị"]

function rnd(min, max) { return Math.random() * (max - min) + min; }
function pick(arr) { return arr[Math.floor(Math.random() * arr.length)]; }

function spawn() {
  const el = document.createElement("div");
  const isHeart = Math.random() < 0.3;
  el.className = "floating" + (isHeart ? " heart" : "");
  el.textContent = isHeart ? "❤" : pick(TEXTS);

  const x = rnd(0, window.innerWidth) + "px";
  const z = rnd(-400, 200) + "px";
  const scale = rnd(0.6, 1.3);
  const dur = rnd(5, 12);

  el.style.setProperty("--x", x);
  el.style.setProperty("--z", z);
  el.style.setProperty("--scale", scale);
  el.style.animationDuration = dur + "s";
  el.style.fontSize = rnd(18, 34) + "px";

  scene.appendChild(el);
  setTimeout(() => el.remove(), dur * 1000);
}

function start() {
  for (let i = 0; i < TOTAL; i++) spawn();
  setInterval(spawn, 200);
  setInterval(heartBurst, 800); // tim giữa bắn ra
}

// === Xoay bằng chuột ===
let isDragging = false, lastX=0, lastY=0;
let rotX = 0, rotY = 0;
window.addEventListener("mousedown", e=>{
  isDragging = true; lastX = e.clientX; lastY = e.clientY;
});
window.addEventListener("mouseup", ()=> isDragging=false);
window.addEventListener("mousemove", e=>{
  if(!isDragging) return;
  const dx = e.clientX - lastX;
  const dy = e.clientY - lastY;
  rotY += dx * 0.2;
  rotX -= dy * 0.2;
  scene.style.transform = `rotateX(${rotX}deg) rotateY(${rotY}deg)`;
  lastX = e.clientX; lastY = e.clientY;
});

// === Trái tim giữa phóng ra ===
function heartBurst() {
  for (let i = 0; i < 6; i++) {
    const h = document.createElement("div");
    h.className = "burst";
    h.textContent = "❤";
    h.style.setProperty("--dx", `${rnd(-250,250)}px`);
    h.style.setProperty("--dy", `${rnd(-250,250)}px`);
    document.body.appendChild(h);
    setTimeout(()=>h.remove(), 1200);
  }
}

// ==== YouTube Audio ====
let ytReady=false, player;
const playBtn=document.getElementById("playBtn");
const tag=document.createElement("script");
tag.src="https://www.youtube.com/iframe_api";
document.body.appendChild(tag);

window.onYouTubeIframeAPIReady=function(){
  ytReady=true;
  player=new YT.Player("yt-holder",{
    videoId:"aeygAuvj5XQ",
    playerVars:{autoplay:0,controls:0,loop:1,playlist:"aeygAuvj5XQ"},
  });
};
playBtn.addEventListener("click",()=>{
  start();
  if(ytReady&&player){player.playVideo(); player.setVolume(80);}
  playBtn.remove();
});
</script>
</body>
</html>
