<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush – Neon City</title>

<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#02050a;color:white;font-family:Arial,sans-serif}
button,input{font:inherit}
button{cursor:pointer;border:0}
.hidden{display:none!important}

.screen{
 position:fixed;inset:0;z-index:100;
 display:flex;align-items:center;justify-content:center;
 padding:20px;
 background:radial-gradient(circle at top,#063d52,#02050a 70%);
}

.card{
 width:min(430px,100%);
 padding:25px 20px;
 border-radius:25px;
 background:rgba(5,16,25,.97);
 border:1px solid #00eaff55;
 box-shadow:0 0 45px #00eaff22;
 text-align:center;
}

.logo{
 font-size:39px;font-weight:1000;color:#00eaff;
 text-shadow:0 0 10px #00eaff,0 0 25px #00eaff;
 margin-bottom:7px;
}

.sub{color:#8ba8b2;font-size:14px;margin-bottom:18px;line-height:1.5}

input{
 width:100%;height:53px;margin:7px 0;padding:0 15px;
 border-radius:14px;border:1px solid #00eaff40;
 background:#071923;color:white;outline:0;
}

input:focus{border-color:#00eaff;box-shadow:0 0 15px #00eaff22}

button{
 width:100%;min-height:52px;margin-top:9px;border-radius:14px;
}

.primary{
 background:linear-gradient(135deg,#00eaff,#008cff);
 color:#001017;font-weight:900;
}

.secondary{
 background:#091b26;color:white;border:1px solid #00eaff35;
}

.msg{min-height:22px;margin-top:10px;color:#8beeff;font-size:14px}

.stats{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin:18px 0}
.stat{background:#071923;padding:14px;border-radius:14px}
.label{font-size:11px;color:#75929d}
.value{font-size:23px;font-weight:900;margin-top:4px}

#game{
 position:fixed;inset:0;width:100%;height:100%;
 display:none;background:#02050a;
}

#hud{
 display:none;position:fixed;z-index:20;top:10px;left:10px;right:10px;
 justify-content:space-between;gap:7px;pointer-events:none;
}

.hud{
 padding:9px 11px;border-radius:12px;background:#061923dd;
 border:1px solid #00eaff40;font-weight:900;font-size:13px;
}

#pause{
 display:none;position:fixed;z-index:30;top:60px;right:10px;
 width:70px;height:40px;min-height:40px;background:#071923;
 color:white;border:1px solid #00eaff55;
}

#controls{
 display:none;position:fixed;z-index:30;left:12px;right:12px;bottom:15px;
 justify-content:space-between;align-items:center;pointer-events:none;
}

.controlRow{display:flex;gap:8px;pointer-events:auto}

.control{
 width:62px;height:58px;min-height:58px;
 background:#071923ee;color:white;border:1px solid #00eaff55;
 font-size:24px;
}

.jump{
 width:105px;
 background:linear-gradient(135deg,#00eaff,#008cff);
 color:#001017;font-weight:900;pointer-events:auto;
}

.rank{
 text-align:left;max-height:330px;overflow:auto;line-height:1.9;
}
</style>
</head>

<body>

<!-- ==================================================
     SUPABASE EINSTELLUNGEN
     ==================================================

     DEINE SUPABASE URL:
     https://lfsifdmaftztykpckdsh.supabase.co

     NUR DIE NÄCHSTE ZEILE MUSST DU ÄNDERN:

     SUPABASE_KEY = "DEIN_ANON_PUBLIC_KEY_HIER"

     NICHT den service_role Key verwenden!
================================================== -->

<script>
const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/* =====================================================
   >>> HIER DEINEN ANON/PUBLIC/PUBLISHABLE KEY EINFÜGEN <<<
===================================================== */

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfi";

/* ===================================================== */
</script>


<!-- LOGIN -->

<div id="loginScreen" class="screen">
 <div class="card">

  <div class="logo">CUBE RUSH</div>

  <div class="sub">
   🌆 NEON CITY<br>
   Laufe, springe, sammle Münzen und werde Nr. 1.
  </div>

  <input id="loginEmail" type="email" placeholder="E-Mail">
  <input id="loginPassword" type="password" placeholder="Passwort">

  <button id="loginBtn" class="primary">
   ANMELDEN
  </button>

  <button id="registerOpen" class="secondary">
   ACCOUNT ERSTELLEN
  </button>

  <div id="loginMsg" class="msg"></div>

 </div>
</div>


<!-- REGISTRIERUNG -->

<div id="registerScreen" class="screen hidden">
 <div class="card">

  <div class="logo">ACCOUNT</div>

  <div class="sub">
   Erstelle deinen eigenen Spieler.
  </div>

  <input id="username" type="text" maxlength="20" placeholder="Benutzername">
  <input id="registerEmail" type="email" placeholder="E-Mail">
  <input id="registerPassword" type="password" placeholder="Passwort">

  <button id="registerBtn" class="primary">
   ACCOUNT ERSTELLEN
  </button>

  <button id="backLogin" class="secondary">
   ZURÜCK
  </button>

  <div id="registerMsg" class="msg"></div>

 </div>
</div>


<!-- HAUPTMENÜ -->

<div id="menuScreen" class="screen hidden">
 <div class="card">

  <div class="logo">CUBE RUSH</div>

  <div class="sub">
   Willkommen,
   <b id="menuName">Spieler</b>
  </div>

  <div class="stats">

   <div class="stat">
    <div class="label">MÜNZEN</div>
    <div id="menuCoins" class="value">0</div>
   </div>

   <div class="stat">
    <div class="label">HIGHSCORE</div>
    <div id="menuScore" class="value">0</div>
   </div>

  </div>

  <button id="startBtn" class="primary">
   ▶ SPIEL STARTEN
  </button>

  <button id="rankingBtn" class="secondary">
   🏆 RANGLISTE
  </button>

  <button id="logoutBtn" class="secondary">
   ABMELDEN
  </button>

 </div>
</div>


<!-- RANGLISTE -->

<div id="rankingScreen" class="screen hidden">
 <div class="card">

  <div class="logo">🏆 TOP SPIELER</div>

  <div id="ranking" class="rank">
   Lade Rangliste...
  </div>

  <button id="rankingBack" class="secondary">
   ZURÜCK
  </button>

 </div>
</div>


<!-- GAME OVER -->

<div id="gameOverScreen" class="screen hidden">
 <div class="card">

  <div class="logo">CRASH!</div>

  <div class="sub">
   Deine Runde ist vorbei.
  </div>

  <div class="stats">

   <div class="stat">
    <div class="label">SCORE</div>
    <div id="finalScore" class="value">0</div>
   </div>

   <div class="stat">
    <div class="label">MÜNZEN</div>
    <div id="finalCoins" class="value">0</div>
   </div>

  </div>

  <button id="againBtn" class="primary">
   🔄 NOCHMAL
  </button>

  <button id="gameMenuBtn" class="secondary">
   MENÜ
  </button>

 </div>
</div>


<!-- SPIEL -->

<canvas id="game"></canvas>

<div id="hud">

 <div class="hud">
  🪙 <span id="hudCoins">0</span>
 </div>

 <div class="hud">
  🏆 <span id="hudScore">0</span>
 </div>

 <div class="hud">
  🌆 NEON CITY
 </div>

</div>

<button id="pause">⏸</button>

<div id="controls">

 <div class="controlRow">

  <button id="left" class="control">←</button>
  <button id="right" class="control">→</button>

 </div>

 <button id="jump" class="control jump">
  SPRINGEN
 </button>

</div>


<script>

/* =====================================================
   GRUNDVARIABLEN
===================================================== */

let token = "";
let player = null;

const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let W = innerWidth;
let H = innerHeight;

canvas.width = W;
canvas.height = H;

addEventListener("resize",()=>{
 W=innerWidth;
 H=innerHeight;
 canvas.width=W;
 canvas.height=H;
});


/* =====================================================
   SUPABASE API
===================================================== */

async function api(path,options={}){

 if(
  !SUPABASE_KEY ||
  SUPABASE_KEY === "DEIN_ANON_PUBLIC_KEY_HIER"
 ){
  throw new Error(
   "Supabase-Key fehlt. Trage oben deinen ANON/PUBLIC-Key ein."
  );
 }

 const headers={
  "apikey":SUPABASE_KEY,
  "Content-Type":"application/json",
  ...(token?{"Authorization":"Bearer "+token}:{}),
  ...(options.headers||{})
 };

 const response=await fetch(
  SUPABASE_URL+path,
  {
   ...options,
   headers
  }
 );

 const text=await response.text();

 let data=null;

 try{
  data=text?JSON.parse(text):null;
 }catch{}

 if(!response.ok){
  throw new Error(
   data?.message ||
   data?.msg ||
   data?.error_description ||
   text ||
   "Supabase Fehler"
  );
 }

 return data;
}


/* =====================================================
   SCREENS
===================================================== */

function screen(id){

 document.querySelectorAll(".screen")
 .forEach(x=>x.classList.add("hidden"));

 document.getElementById(id)
 .classList.remove("hidden");
}


function msg(id,text){

 document.getElementById(id).textContent=text;
}


/* =====================================================
   ACCOUNT ERSTELLEN
===================================================== */

document.getElementById("registerBtn").onclick=
async()=>{

 const name=
  document.getElementById("username")
  .value.trim();

 const email=
  document.getElementById("registerEmail")
  .value.trim();

 const password=
  document.getElementById("registerPassword")
  .value;


 if(name.length<3){
  msg("registerMsg","Benutzername muss mindestens 3 Zeichen haben.");
  return;
 }

 if(!email.includes("@")){
  msg("registerMsg","Bitte eine gültige E-Mail eingeben.");
  return;
 }

 if(password.length<6){
  msg("registerMsg","Passwort muss mindestens 6 Zeichen haben.");
  return;
 }

 msg("registerMsg","Account wird erstellt...");

 try{

  const data=await api(
   "/auth/v1/signup",
   {
    method:"POST",
    body:JSON.stringify({
     email,
     password,
     data:{
      username:name
     }
    })
   }
  );

  if(!data.user){
   throw new Error("Account konnte nicht erstellt werden.");
  }

  if(data.access_token){

   token=data.access_token;

   player={
    id:data.user.id,
    username:name,
    coins:0,
    highscore:0
   };

   await createProfile();

   openMenu();

  }else{

   msg(
    "registerMsg",
    "✅ Account erstellt. Bestätige deine E-Mail und melde dich danach an."
   );

  }

 }catch(e){

  msg("registerMsg","❌ "+e.message);

 }
};


/* =====================================================
   PROFIL ERSTELLEN
===================================================== */

async function createProfile(){

 try{

  await api(
   "/rest/v1/Players",
   {
    method:"POST",
    headers:{
     "Prefer":"resolution=ignore-duplicates"
    },
    body:JSON.stringify({
     id:player.id,
     username:player.username,
     coins:0,
     highscore:0
    })
   }
  );

 }catch(e){

  console.log("Profil:",e.message);

 }
}


/* =====================================================
   LOGIN
===================================================== */

document.getElementById("loginBtn").onclick=
async()=>{

 const email=
  document.getElementById("loginEmail")
  .value.trim();

 const password=
  document.getElementById("loginPassword")
  .value;

 if(!email || !password){

  msg(
   "loginMsg",
   "Bitte E-Mail und Passwort eingeben."
  );

  return;
 }

 msg("loginMsg","Anmeldung...");

 try{

  const data=await api(
   "/auth/v1/token?grant_type=password",
   {
    method:"POST",
    body:JSON.stringify({
     email,
     password
    })
   }
  );

  token=data.access_token;

  player={
   id:data.user.id,
   username:
    data.user.user_metadata?.username ||
    email.split("@")[0],
   coins:0,
   highscore:0
  };

  await loadProfile();

  openMenu();

 }catch(e){

  msg(
   "loginMsg",
   "❌ "+e.message
  );

 }
};


/* =====================================================
   PROFIL LADEN
===================================================== */

async function loadProfile(){

 try{

  const rows=await api(
   "/rest/v1/Players"+
   "?id=eq."+
   encodeURIComponent(player.id)+
   "&select=*"
  );

  if(rows && rows.length){

   player.username=
    rows[0].username ||
    player.username;

   player.coins=
    Number(rows[0].coins||0);

   player.highscore=
    Number(rows[0].highscore||0);
  }

 }catch(e){

  console.log("Profil laden:",e.message);

 }
}


/* =====================================================
   PROFIL SPEICHERN
===================================================== */

async function saveProfile(){

 try{

  await api(
   "/rest/v1/Players?id=eq."+
   encodeURIComponent(player.id),
   {
    method:"PATCH",
    headers:{
     "Prefer":"return=minimal"
    },
    body:JSON.stringify({
     username:player.username,
     coins:player.coins,
     highscore:player.highscore
    })
   }
  );

 }catch(e){

  console.log("Speichern:",e.message);

 }
}


/* =====================================================
   MENU
===================================================== */

function openMenu(){

 document.getElementById("menuName")
 .textContent=player.username;

 document.getElementById("menuCoins")
 .textContent=player.coins.toLocaleString();

 document.getElementById("menuScore")
 .textContent=player.highscore.toLocaleString();

 screen("menuScreen");
}


/* =====================================================
   NAVIGATION
===================================================== */

document.getElementById("registerOpen").onclick=()=>{
 msg("registerMsg","");
 screen("registerScreen");
};

document.getElementById("backLogin").onclick=()=>{
 screen("loginScreen");
};

document.getElementById("logoutBtn").onclick=()=>{
 token="";
 player=null;
 screen("loginScreen");
};


/* =====================================================
   RANGLISTE
===================================================== */

document.getElementById("rankingBtn").onclick=
async()=>{

 screen("rankingScreen");

 const box=document.getElementById("ranking");

 box.innerHTML="Lade Rangliste...";

 try{

  const rows=await api(
   "/rest/v1/Players"+
   "?select=username,highscore"+
   "&order=highscore.desc"+
   "&limit=50"
  );

  if(!rows.length){

   box.innerHTML="Noch keine Spieler.";

   return;
  }

  box.innerHTML=rows.map(
   (x,i)=>`
    <div>
     <b>${i+1}.</b>
     ${safe(x.username)}
     — ${Number(x.highscore||0).toLocaleString()}
    </div>
   `
  ).join("");

 }catch(e){

  box.innerHTML="❌ "+safe(e.message);

 }
};

document.getElementById("rankingBack").onclick=
()=>{
 openMenu();
};


function safe(text){

 return String(text)
 .replaceAll("&","&amp;")
 .replaceAll("<","&lt;")
 .replaceAll(">","&gt;")
 .replaceAll('"',"&quot;");
}


/* =====================================================
   SPIELVARIABLEN
===================================================== */

let playing=false;
let paused=false;

let lane=0;

/*
 -1 = LINKS
  0 = MITTE
  1 = RECHTS

Alle drei Spuren funktionieren.
*/

let score=0;
let roundCoins=0;

let speed=330;

let objects=[];

let carTimer=1;
let coinTimer=2;

let jumpY=0;
let jumpVelocity=0;

let lastTime=performance.now();


/* =====================================================
   SPUREN
===================================================== */

function road(){

 return Math.min(W*.86,650);
}


function roadLeft(){

 return (W-road())/2;
}


function laneWidth(){

 return road()/3;
}


function laneX(n){

 return (
  roadLeft()+
  laneWidth()*(n+1)+
  laneWidth()/2
 );
}


/* =====================================================
   SPIEL STARTEN
===================================================== */

document.getElementById("startBtn").onclick=
()=>{
 startGame();
};


function startGame(){

 document.querySelectorAll(".screen")
 .forEach(x=>x.classList.add("hidden"));

 canvas.style.display="block";

 document.getElementById("hud")
 .style.display="flex";

 document.getElementById("controls")
 .style.display="flex";

 document.getElementById("pause")
 .style.display="block";

 playing=true;
 paused=false;

 lane=0;

 score=0;
 roundCoins=0;

 speed=330;

 objects=[];

 carTimer=1;
 coinTimer=2;

 jumpY=0;
 jumpVelocity=0;

 lastTime=performance.now();

 document.getElementById("pause")
 .textContent="⏸";
}


/* =====================================================
   NEON CITY HINTERGRUND
===================================================== */

function drawBackground(){

 const g=ctx.createLinearGradient(
  0,0,0,H
 );

 g.addColorStop(0,"#073a4d");
 g.addColorStop(1,"#02050a");

 ctx.fillStyle=g;
 ctx.fillRect(0,0,W,H);


 /* Gebäude */

 for(let i=0;i<22;i++){

  const bw=28+(i%4)*12;
  const bh=100+(i*37)%250;

  const bx=i*(W/21);

  ctx.fillStyle="#07151e";

  ctx.fillRect(
   bx,
   H-bh-140,
   bw,
   bh
  );

  /* Fenster */

  ctx.fillStyle="#00eaff55";

  for(
   let y=H-bh-125;
   y<H-150;
   y+=25
  ){

   ctx.fillRect(
    bx+7,
    y,
    5,
    8
   );

  }
 }


 /* Straße */

 const rw=road();
 const rl=roadLeft();

 ctx.fillStyle="#111820";

 ctx.fillRect(
  rl,
  0,
  rw,
  H
 );


 /* Neon-Ränder */

 ctx.fillStyle="#00eaff";

 ctx.fillRect(
  rl,
  0,
  3,
  H
 );

 ctx.fillRect(
  rl+rw-3,
  0,
  3,
  H
 );


 /* Spurmarkierungen */

 const lw=laneWidth();

 const offset=(score*9)%90;

 ctx.fillStyle="#ffffff88";

 for(
  let y=-90+offset;
  y<H;
  y+=90
 ){

  ctx.fillRect(
   rl+lw-2,
   y,
   4,
   45
  );

  ctx.fillRect(
   rl+lw*2-2,
   y,
   4,
   45
  );
 }
}


/* =====================================================
   AUTOS
===================================================== */

function spawnCar(){

 /*
   Niemals die komplette Straße blockieren.
   So bleibt mindestens eine Spur frei.
 */

 const free=[-1,0,1].filter(
  n=>!objects.some(
   o=>o.type==="car" &&
      o.lane===n &&
      o.y<230
  )
 );

 if(!free.length)return;

 const n=
  free[
   Math.floor(
    Math.random()*free.length
   )
  ];

 objects.push({
  type:"car",
  lane:n,
  y:-100,
  color:[
   "#ff1744",
   "#008cff",
   "#ff9800",
   "#ffffff"
  ][Math.floor(Math.random()*4)]
 });
}


/* =====================================================
   MÜNZEN
===================================================== */

function spawnCoins(){

 /*
   NEON CITY:
   Immer nur 3–6 Münzen
   in einer Gruppe.
 */

 const amount=
  3+
  Math.floor(
   Math.random()*4
  );

 const n=
  [-1,0,1][
   Math.floor(
    Math.random()*3
   )
  ];

 for(let i=0;i<amount;i++){

  objects.push({
   type:"coin",
   lane:n,
   y:-50-i*65
  });
 }
}


/* =====================================================
   OBJEKTE ZEICHNEN
===================================================== */

function drawObjects(){

 for(const o of objects){

  const x=laneX(o.lane);

  if(o.type==="coin"){

   ctx.save();

   ctx.shadowColor="#ffd600";
   ctx.shadowBlur=20;

   ctx.fillStyle="#ffd600";

   ctx.beginPath();

   ctx.arc(
    x,
    o.y,
    14,
    0,
    Math.PI*2
   );

   ctx.fill();

   ctx.restore();

  }else{

   ctx.save();

   ctx.translate(x,o.y);

   ctx.shadowColor=o.color;
   ctx.shadowBlur=20;

   ctx.fillStyle=o.color;

   ctx.beginPath();

   ctx.roundRect(
    -30,
    -50,
    60,
    100,
    12
   );

   ctx.fill();

   ctx.shadowBlur=0;

   ctx.fillStyle="#17303b";

   ctx.fillRect(
    -19,
    -29,
    38,
    24
   );

   ctx.restore();
  }
 }
}


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer(){

 const x=laneX(lane);

 const y=
  H*.76-jumpY;

 ctx.save();

 ctx.translate(x,y);

 ctx.shadowColor="#00eaff";
 ctx.shadowBlur=25;

 ctx.fillStyle="#00eaff";

 ctx.beginPath();

 ctx.roundRect(
  -26,
  -26,
  52,
  52,
  10
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#021017";

 ctx.fillRect(
  -13,
  -9,
  26,
  9
 );

 ctx.restore();
}


/* =====================================================
   LINKS / RECHTS
===================================================== */

function left(){

 if(!playing||paused)return;

 lane=Math.max(-1,lane-1);
}


function right(){

 if(!playing||paused)return;

 lane=Math.min(1,lane+1);
}


document.getElementById("left").onclick=left;
document.getElementById("right").onclick=right;


/* =====================================================
   SPRINGEN
===================================================== */

function jump(){

 if(!playing||paused)return;

 if(jumpY<=0){

  jumpVelocity=850;
 }
}


document.getElementById("jump").onclick=jump;


/* =====================================================
   UPDATE
===================================================== */

function update(dt){

 if(!playing||paused)return;

 const s=dt/1000;

 score+=s*10;

 speed=Math.min(
  700,
  speed+s*3
 );


 /* Sprung */

 jumpVelocity-=1900*s;

 jumpY+=jumpVelocity*s;

 if(jumpY<0){

  jumpY=0;
  jumpVelocity=0;
 }


 /* Münzen */

 coinTimer-=s;

 if(coinTimer<=0){

  spawnCoins();

  coinTimer=
   4+
   Math.random()*1.5;
 }


 /* Autos */

 carTimer-=s;

 if(carTimer<=0){

  spawnCar();

  carTimer=
   1.25+
   Math.random()*.75;
 }


 /* Objekte */

 for(
  let i=objects.length-1;
  i>=0;
  i--
 ){

  const o=objects[i];

  o.y+=speed*s;


  if(o.y>H+120){

   objects.splice(i,1);
   continue;
  }


  const playerY=
   H*.76-jumpY;


  /* Münze einsammeln */

  if(
   o.type==="coin" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<55
  ){

   roundCoins++;

   objects.splice(i,1);

   continue;
  }


  /* Auto */

  if(
   o.type==="car" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<65
  ){

   /*
     Wenn der Spieler hoch genug springt,
     kommt er über das Auto.
   */

   if(jumpY<90){

    endGame();

    return;
   }
  }
 }


 document.getElementById("hudCoins")
 .textContent=
  (
   player.coins+
   roundCoins
  ).toLocaleString();

 document.getElementById("hudScore")
 .textContent=
  Math.floor(score).toLocaleString();
}


/* =====================================================
   GAME LOOP
===================================================== */

function loop(time){

 const dt=Math.min(
  40,
  time-lastTime
 );

 lastTime=time;

 if(
  canvas.style.display!=="none"
 ){

  drawBackground();
  drawObjects();
  drawPlayer();

  update(dt);
 }

 requestAnimationFrame(loop);
}

requestAnimationFrame(loop);


/* =====================================================
   TASTATUR
===================================================== */

document.addEventListener(
 "keydown",
 e=>{

  if(e.key==="ArrowLeft"){
   e.preventDefault();
   left();
  }

  if(e.key==="ArrowRight"){
   e.preventDefault();
   right();
  }

  if(
   e.key==="ArrowUp"||
   e.key===" "
  ){
   e.preventDefault();
   jump();
  }
 }
);


/* =====================================================
   TOUCH / SWIPE
===================================================== */

let touchX=0;
let touchY=0;

canvas.addEventListener(
 "touchstart",
 e=>{

  const t=e.changedTouches[0];

  touchX=t.clientX;
  touchY=t.clientY;

 },
 {passive:true}
);

canvas.addEventListener(
 "touchend",
 e=>{

  const t=e.changedTouches[0];

  const dx=t.clientX-touchX;
  const dy=t.clientY-touchY;

  if(Math.abs(dx)>Math.abs(dy)){

   if(dx>35)right();
   if(dx<-35)left();

  }else if(dy<-35){

   jump();
  }

 },
 {passive:true}
);


/* =====================================================
   PAUSE
===================================================== */

document.getElementById("pause").onclick=()=>{

 paused=!paused;

 document.getElementById("pause")
 .textContent=
  paused?"▶":"⏸";
};


/* =====================================================
   GAME OVER
===================================================== */

async function endGame(){

 playing=false;

 const finalScore=
  Math.floor(score);

 player.coins+=roundCoins;

 if(finalScore>player.highscore){

  player.highscore=finalScore;
 }

 /*
   Spielstand speichern
 */

 await saveProfile();


 document.getElementById("finalScore")
 .textContent=
  finalScore.toLocaleString();

 document.getElementById("finalCoins")
 .textContent=
  roundCoins.toLocaleString();


 canvas.style.display="none";

 document.getElementById("hud")
 .style.display="none";

 document.getElementById("controls")
 .style.display="none";

 document.getElementById("pause")
 .style.display="none";

 screen("gameOverScreen");
}


/* =====================================================
   NOCHMAL
===================================================== */

document.getElementById("againBtn").onclick=()=>{
 startGame();
};


/* =====================================================
   MENÜ
===================================================== */

document.getElementById("gameMenuBtn").onclick=()=>{
 openMenu();
};


/* =====================================================
   START
===================================================== */

screen("loginScreen");

</script>

</body>
</html>
