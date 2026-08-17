<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>CUBE RUSH</title>

<style>
*{
  box-sizing:border-box;
  -webkit-tap-highlight-color:transparent;
}

html,body{
  margin:0;
  width:100%;
  height:100%;
  overflow:hidden;
  background:#02060b;
  color:white;
  font-family:Arial,sans-serif;
}

button,input{
  font-family:Arial,sans-serif;
}

button{
  border:0;
  cursor:pointer;
  -webkit-appearance:none;
}

.screen{
  position:fixed;
  inset:0;
  z-index:20;
  display:flex;
  align-items:center;
  justify-content:center;
  padding:16px;
  overflow:auto;
  background:
    radial-gradient(circle at 50% 0%,#07566b,#03151e 45%,#010306);
}

.card{
  width:min(470px,100%);
  max-height:94vh;
  overflow:auto;
  padding:24px 18px;
  border-radius:28px;
  background:rgba(3,11,18,.97);
  border:1px solid #00eaff66;
  box-shadow:0 0 40px #00eaff18;
  text-align:center;
}

.logo{
  font-size:39px;
  font-weight:1000;
  letter-spacing:-2px;
  color:#08e9ff;
  text-shadow:0 0 12px #08e9ff,0 0 30px #08e9ff;
}

.subtitle{
  color:#a7c4ce;
  margin:8px 0 18px;
  line-height:1.4;
}

input{
  width:100%;
  height:55px;
  margin:7px 0;
  padding:0 15px;
  border-radius:14px;
  border:1px solid #00eaff55;
  outline:none;
  background:#f4ffbd;
  color:#111;
  font-size:16px;
}

.btn{
  width:100%;
  min-height:54px;
  margin:6px 0;
  border-radius:15px;
  font-weight:900;
  font-size:15px;
}

.primary{
  color:#001018;
  background:linear-gradient(135deg,#00edff,#078eff);
}

.secondary{
  color:white;
  background:#071b25;
  border:1px solid #00eaff44;
}

.message{
  min-height:22px;
  margin-top:8px;
  color:#8df5ff;
  font-size:13px;
}

.hidden{
  display:none!important;
}

.stats{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:8px;
  margin:14px 0;
}

.stat{
  padding:12px;
  border-radius:14px;
  background:#071b25;
  border:1px solid #00eaff22;
}

.stat small{
  display:block;
  color:#75939d;
}

.stat strong{
  display:block;
  margin-top:5px;
  font-size:21px;
}

.grid{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:9px;
}

.item{
  padding:10px;
  border-radius:17px;
  background:#071820;
  border:1px solid #00eaff22;
}

.item.selected{
  border-color:#00eaff;
  box-shadow:0 0 20px #00eaff22;
}

.preview{
  height:85px;
  display:flex;
  justify-content:center;
  align-items:center;
  position:relative;
}

.cube{
  width:46px;
  height:46px;
  border-radius:12px;
  box-shadow:0 0 20px currentColor;
}

.name{
  font-weight:900;
  margin:4px;
}

.price{
  color:#ffd600;
  font-size:12px;
}

.mult{
  color:#8ba8b2;
  font-size:11px;
  margin:4px;
}

.city{
  height:85px;
  border-radius:12px;
  position:relative;
  overflow:hidden;
  margin-bottom:7px;
}

.building{
  position:absolute;
  bottom:0;
  background:#06141c;
  border-top:1px solid #00eaff77;
}

#game{
  position:fixed;
  inset:0;
  width:100%;
  height:100%;
  display:none;
  z-index:1;
  touch-action:none;
}

#hud{
  position:fixed;
  top:10px;
  left:8px;
  right:8px;
  z-index:5;
  display:none;
  justify-content:space-between;
  gap:5px;
  pointer-events:none;
}

.hud{
  padding:7px 9px;
  border-radius:11px;
  background:#031119dd;
  border:1px solid #00eaff55;
  font-size:11px;
  font-weight:900;
}

#pause{
  position:fixed;
  top:53px;
  right:9px;
  z-index:6;
  width:58px;
  height:38px;
  border-radius:11px;
  background:#071b25dd;
  border:1px solid #00eaff55;
  color:white;
  display:none;
}

.power{
  position:fixed;
  top:95px;
  left:50%;
  transform:translateX(-50%);
  z-index:6;
  padding:7px 12px;
  border-radius:14px;
  background:#031119dd;
  border:1px solid #00eaff66;
  font-weight:900;
  font-size:13px;
  display:none;
}

.rank{
  text-align:left;
  padding:7px;
  border-bottom:1px solid #ffffff12;
}

.tabs{
  display:flex;
  gap:7px;
  margin-bottom:10px;
}

.tabs button{
  flex:1;
  padding:11px 5px;
  border-radius:12px;
  background:#071b25;
  color:white;
  border:1px solid #00eaff33;
  font-weight:900;
}

.tabs .active{
  background:#00eaff;
  color:#001018;
}
</style>
</head>

<body>

<section id="login" class="screen">
  <div class="card">
    <div class="logo">CUBE RUSH</div>
    <div class="subtitle">Melde dich bei CUBE RUSH an.</div>

    <input id="email" type="email" placeholder="E-Mail">
    <input id="password" type="password" placeholder="Passwort">

    <button id="loginBtn" class="btn primary">
      ANMELDEN
    </button>

    <button id="registerOpen" class="btn secondary">
      ACCOUNT ERSTELLEN
    </button>

    <div id="loginMsg" class="message"></div>
  </div>
</section>

<section id="register" class="screen hidden">
  <div class="card">
    <div class="logo">ACCOUNT</div>

    <input id="username" maxlength="20" placeholder="Benutzername">
    <input id="regEmail" type="email" placeholder="E-Mail">
    <input id="regPassword" type="password" placeholder="Passwort">

    <button id="registerBtn" class="btn primary">
      ACCOUNT ERSTELLEN
    </button>

    <button id="backLogin" class="btn secondary">
      ZURÜCK
    </button>

    <div id="regMsg" class="message"></div>
  </div>
</section>

<section id="menu" class="screen hidden">
  <div class="card">
    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
      Willkommen, <b id="playerName"></b>
    </div>

    <div class="stats">
      <div class="stat">
        <small>🪙 MÜNZEN</small>
        <strong id="coins">0</strong>
      </div>

      <div class="stat">
        <small>🏆 HIGHSCORE</small>
        <strong id="highscore">0</strong>
      </div>
    </div>

    <button id="start" class="btn primary">
      ▶ SPIEL STARTEN
    </button>

    <button id="shop" class="btn secondary">
      🛍️ SHOP
    </button>

    <button id="rankBtn" class="btn secondary">
      🏆 RANGLISTE
    </button>

    <button id="codeBtn" class="btn secondary">
      🔐 CODE EINGEBEN
    </button>

    <button id="logout" class="btn secondary">
      ABMELDEN
    </button>
  </div>
</section>

<section id="shopScreen" class="screen hidden">
  <div class="card">

    <div class="logo">SHOP</div>

    <div class="tabs">
      <button id="charTab" class="active">CHARAKTERE</button>
      <button id="cityTab">STÄDTE</button>
    </div>

    <div id="shopGrid" class="grid"></div>

    <button id="shopBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>

<section id="rankScreen" class="screen hidden">
  <div class="card">
    <div class="logo">🏆</div>
    <div id="rankList">Lade...</div>

    <button id="rankBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>

<section id="codeScreen" class="screen hidden">
  <div class="card">
    <div class="logo">🔐 CODE</div>

    <input id="codeInput"
           inputmode="numeric"
           maxlength="3"
           placeholder="Code">

    <button id="useCode" class="btn primary">
      EINLÖSEN
    </button>

    <div id="codeMsg" class="message"></div>

    <button id="codeBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>

<section id="gameOver" class="screen hidden">
  <div class="card">
    <div class="logo">GAME OVER</div>

    <div class="stats">
      <div class="stat">
        <small>SCORE</small>
        <strong id="finalScore">0</strong>
      </div>

      <div class="stat">
        <small>MÜNZEN</small>
        <strong id="finalCoins">0</strong>
      </div>
    </div>

    <button id="again" class="btn primary">
      🔄 NOCHMAL
    </button>

    <button id="gameMenu" class="btn secondary">
      MENÜ
    </button>
  </div>
</section>

<canvas id="game"></canvas>

<div id="hud">
  <div class="hud">🪙 <span id="hudCoins">0</span></div>
  <div class="hud">🏆 <span id="hudScore">0</span></div>
  <div class="hud" id="hudWorld">NEON CITY</div>
</div>

<div id="power" class="power"></div>

<button id="pause">⏸</button>


<script>
/* =========================
   SUPABASE
========================= */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";

let token="";
let player=null;


/* =========================
   CHARAKTERE
========================= */

const characters=[
 {name:"Cube",price:0,mult:1,color:"#00eaff"},
 {name:"Neon",price:500,mult:1.1,color:"#00ff66"},
 {name:"Fire",price:1000,mult:1.2,color:"#ff1744"},
 {name:"Ocean",price:2000,mult:1.3,color:"#008cff"},
 {name:"Purple",price:3500,mult:1.4,color:"#b000ff"},
 {name:"Gold",price:5000,mult:1.5,color:"#ffd600"},
 {name:"Lava",price:7500,mult:1.7,color:"#ff5a00"},
 {name:"Ice",price:10000,mult:1.9,color:"#8df6ff"},
 {name:"Rainbow",price:15000,mult:2,color:"#ff4dff"},
 {name:"Toxic",price:20000,mult:2.2,color:"#aaff00"},
 {name:"Diamond",price:30000,mult:2.5,color:"#b9f2ff"},
 {name:"Galaxy",price:45000,mult:2.8,color:"#7c4dff"},
 {name:"Plasma",price:60000,mult:3,color:"#ff00aa"},
 {name:"Cyber",price:80000,mult:3.3,color:"#00ffcc"},
 {name:"Royal",price:100000,mult:3.6,color:"#ffcc00"},
 {name:"Shadow",price:125000,mult:4,color:"#777777"},
 {name:"Cosmic",price:160000,mult:4.5,color:"#8b5cff"},
 {name:"Legend",price:220000,mult:5,color:"#ff9100"},
 {name:"Infinity",price:350000,mult:6,color:"#ffffff"},
 {name:"CUBE GOD",price:500000,mult:7,color:"#00ffff"}
];


/* =========================
   10 WELTEN
========================= */

const worlds=[
 {
  name:"Neon City",
  price:0,
  mult:1,
  min:3,
  max:6,
  sky:"#063d50",
  ground:"#101820"
 },
 {
  name:"Tokyo",
  price:2500,
  mult:1.5,
  min:6,
  max:10,
  sky:"#39105b",
  ground:"#160b1d"
 },
 {
  name:"New York",
  price:7500,
  mult:2,
  min:10,
  max:14,
  sky:"#10294f",
  ground:"#111820"
 },
 {
  name:"Miami",
  price:15000,
  mult:2.5,
  min:14,
  max:18,
  sky:"#17606b",
  ground:"#101d20"
 },
 {
  name:"Dubai",
  price:30000,
  mult:3,
  min:18,
  max:23,
  sky:"#633b19",
  ground:"#20140a"
 },
 {
  name:"Cyber City",
  price:60000,
  mult:4,
  min:23,
  max:28,
  sky:"#25004f",
  ground:"#0c0715"
 },
 {
  name:"Galaxy City",
  price:120000,
  mult:5,
  min:28,
  max:34,
  sky:"#05002d",
  ground:"#05030f"
 },
 {
  name:"Mars City",
  price:250000,
  mult:6,
  min:34,
  max:40,
  sky:"#6b160e",
  ground:"#28100d"
 },
 {
  name:"Crystal City",
  price:500000,
  mult:8,
  min:40,
  max:48,
  sky:"#103e55",
  ground:"#07151e"
 },
 {
  name:"Infinity City",
  price:1000000,
  mult:10,
  min:48,
  max:60,
  sky:"#00001d",
  ground:"#03030c"
 }
];


/* =========================
   DATEN
========================= */

function localKey(){
 return "cubeRush_"+player.id;
}

function data(){
 try{
  return JSON.parse(localStorage.getItem(localKey())||"{}");
 }catch{
  return {};
 }
}

function saveData(d){
 localStorage.setItem(localKey(),JSON.stringify(d));
}

function unlockedChars(){
 return data().chars||[0];
}

function unlockedWorlds(){
 return data().worlds||[0];
}

function selectedChar(){
 return Number(data().char||0);
}

function selectedWorld(){
 return Number(data().world||0);
}

function $(id){
 return document.getElementById(id);
}

function screen(id){
 document.querySelectorAll(".screen").forEach(x=>{
  x.classList.add("hidden");
 });
 $(id).classList.remove("hidden");
}


/* =========================
   SUPABASE
========================= */

async function request(path,opt={}){
 const headers={
  apikey:SUPABASE_KEY,
  "Content-Type":"application/json",
  ...(token?{Authorization:"Bearer "+token}:{}),
  ...(opt.headers||{})
 };

 const r=await fetch(
  SUPABASE_URL+path,
  {...opt,headers}
 );

 const text=await r.text();

 let json=null;

 try{
  json=text?JSON.parse(text):null;
 }catch{}

 if(!r.ok){
  throw new Error(
   json?.msg||
   json?.message||
   json?.error_description||
   text||
   "Supabase Fehler"
  );
 }

 return json;
}


/* =========================
   LOGIN
========================= */

$("registerOpen").onclick=()=>{
 screen("register");
};

$("backLogin").onclick=()=>{
 screen("login");
};

$("loginBtn").onclick=async()=>{

 const email=$("email").value.trim();
 const password=$("password").value;

 if(!email||!password){
  $("loginMsg").textContent=
   "Bitte E-Mail und Passwort eingeben.";
  return;
 }

 try{

  const r=await request(
   "/auth/v1/token?grant_type=password",
   {
    method:"POST",
    body:JSON.stringify({email,password})
   }
  );

  token=r.access_token;

  player={
   id:r.user.id,
   username:r.user.user_metadata?.username||
   email.split("@")[0],
   coins:0,
   highscore:0
  };

  await loadPlayer();
  menu();

 }catch(e){

  $("loginMsg").textContent=
   "❌ "+e.message;
 }
};


/* =========================
   REGISTRIERUNG
========================= */

$("registerBtn").onclick=async()=>{

 const name=$("username").value.trim();
 const email=$("regEmail").value.trim();
 const password=$("regPassword").value;

 if(name.length<3){
  $("regMsg").textContent=
   "Benutzername mindestens 3 Zeichen.";
  return;
 }

 if(password.length<6){
  $("regMsg").textContent=
   "Passwort mindestens 6 Zeichen.";
  return;
 }

 try{

  const r=await request(
   "/auth/v1/signup",
   {
    method:"POST",
    body:JSON.stringify({
     email,
     password,
     data:{username:name}
    })
   }
  );

  if(!r.access_token){
   $("regMsg").textContent=
    "Account erstellt. Wenn Supabase eine Bestätigung verlangt, ist Confirm email noch aktiviert.";
   return;
  }

  token=r.access_token;

  player={
   id:r.user.id,
   username:name,
   coins:0,
   highscore:0
  };

  await createPlayer();
  menu();

 }catch(e){

  $("regMsg").textContent=
   "❌ "+e.message;
 }
};


/* =========================
   PLAYER
========================= */

async function createPlayer(){

 try{
  await request(
   "/rest/v1/Players",
   {
    method:"POST",
    headers:{
     Prefer:"resolution=ignore-duplicates"
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
  console.log(e.message);
 }
}

async function loadPlayer(){

 try{

  const rows=await request(
   "/rest/v1/Players?id=eq."+
   encodeURIComponent(player.id)+
   "&select=*"
  );

  if(rows?.length){

   player.username=
    rows[0].username||player.username;

   player.coins=
    Number(rows[0].coins||0);

   player.highscore=
    Number(rows[0].highscore||0);
  }

 }catch(e){
  console.log(e.message);
 }
}

async function savePlayer(){

 try{

  await request(
   "/rest/v1/Players?id=eq."+
   encodeURIComponent(player.id),
   {
    method:"PATCH",
    headers:{
     Prefer:"return=minimal"
    },
    body:JSON.stringify({
     username:player.username,
     coins:Math.floor(player.coins),
     highscore:Math.floor(player.highscore)
    })
   }
  );

 }catch(e){
  console.log(e.message);
 }
}


/* =========================
   MENU
========================= */

function menu(){

 $("playerName").textContent=player.username;
 $("coins").textContent=
  Math.floor(player.coins).toLocaleString();

 $("highscore").textContent=
  Math.floor(player.highscore).toLocaleString();

 screen("menu");
}

$("logout").onclick=()=>{
 token="";
 player=null;
 screen("login");
};


/* =========================
   SHOP
========================= */

$("shop").onclick=()=>{
 renderChars();
 screen("shopScreen");
};

$("charTab").onclick=()=>{
 $("charTab").classList.add("active");
 $("cityTab").classList.remove("active");
 renderChars();
};

$("cityTab").onclick=()=>{
 $("cityTab").classList.add("active");
 $("charTab").classList.remove("active");
 renderWorlds();
};

$("shopBack").onclick=menu;


function renderChars(){

 const grid=$("shopGrid");
 const owned=unlockedChars();
 const selected=selectedChar();

 grid.innerHTML="";

 characters.forEach((c,i)=>{

  const div=document.createElement("div");

  div.className=
   "item"+(selected===i?" selected":"");

  div.innerHTML=`
   <div class="preview" style="color:${c.color}">
    <div class="cube"
         style="background:${c.color}"></div>
   </div>

   <div class="name">${c.name}</div>

   <div class="price">
    ${c.price===0?"KOSTENLOS":
      "🪙 "+c.price.toLocaleString()}
   </div>

   <div class="mult">
    Münzen ×${c.mult}
   </div>

   <button class="btn ${
    owned.includes(i)?"secondary":"primary"
   }">
    ${
     selected===i
     ?"AUSGEWÄHLT"
     :owned.includes(i)
     ?"AUSWÄHLEN"
     :"KAUFEN"
    }
   </button>
  `;

  div.querySelector("button").onclick=async()=>{

   const d=data();

   if(!d.chars)d.chars=[0];

   if(d.chars.includes(i)){

    d.char=i;
    saveData(d);

    renderChars();
    return;
   }

   if(player.coins<c.price){
    alert("Nicht genug Münzen!");
    return;
   }

   player.coins-=c.price;

   d.chars.push(i);
   d.char=i;

   saveData(d);

   await savePlayer();

   renderChars();
 };

 grid.appendChild(div);
 });
}


function renderWorlds(){

 const grid=$("shopGrid");
 const owned=unlockedWorlds();
 const selected=selectedWorld();

 grid.innerHTML="";

 worlds.forEach((w,i)=>{

  const div=document.createElement("div");

  div.className=
   "item"+(selected===i?" selected":"");

  div.innerHTML=`
   <div class="city"
        style="background:linear-gradient(${w.sky},${w.ground})">
    ${[0,1,2,3,4].map(n=>`
      <div class="building"
           style="
           left:${n*21+4}%;
           width:${15+n*2}px;
           height:${35+n*10}px">
      </div>
    `).join("")}
   </div>

   <div class="name">${w.name}</div>

   <div class="price">
    ${w.price===0?"KOSTENLOS":
      "🪙 "+w.price.toLocaleString()}
   </div>

   <div class="mult">
    Münzen ×${w.mult}
   </div>

   <button class="btn ${
    owned.includes(i)?"secondary":"primary"
   }">
    ${
     selected===i
     ?"AUSGEWÄHLT"
     :owned.includes(i)
     ?"AUSWÄHLEN"
     :"KAUFEN"
    }
   </button>
  `;

  div.querySelector("button").onclick=async()=>{

   const d=data();

   if(!d.worlds)d.worlds=[0];

   if(d.worlds.includes(i)){

    d.world=i;
    saveData(d);

    renderWorlds();
    return;
   }

   if(player.coins<w.price){
    alert("Nicht genug Münzen!");
    return;
   }

   player.coins-=w.price;

   d.worlds.push(i);
   d.world=i;

   saveData(d);

   await savePlayer();

   renderWorlds();
 };

 grid.appendChild(div);
 });
}


/* =========================
   CODES
========================= */

$("codeBtn").onclick=()=>{
 $("codeInput").value="";
 $("codeMsg").textContent="";
 screen("codeScreen");
};

$("codeBack").onclick=menu;

$("useCode").onclick=async()=>{

 const code=$("codeInput").value.trim();
 const d=data();

 if(code==="110"){

  player.coins=1000000;
  player.highscore=1000000;

  d.chars=characters.map((_,i)=>i);
  d.worlds=worlds.map((_,i)=>i);

  d.char=0;
  d.world=0;

  saveData(d);
  await savePlayer();

  $("codeMsg").textContent=
   "✅ 110 aktiviert! Alles freigeschaltet.";

 }else if(code==="112"){

  player.coins=100000;
  d.chars=[0,1,2,3,4];
  d.worlds=[0,1,2];

  d.char=0;
  d.world=0;

  saveData(d);
  await savePlayer();

  $("codeMsg").textContent=
   "✅ 112 aktiviert!";

 }else{

  $("codeMsg").textContent=
   "❌ Ungültiger Code.";
 }
};


/* =========================
   RANGLISTE
========================= */

$("rankBtn").onclick=async()=>{

 screen("rankScreen");

 try{

  const rows=await request(
   "/rest/v1/Players"+
   "?select=username,highscore"+
   "&order=highscore.desc"+
   "&limit=50"
  );

  $("rankList").innerHTML=
   rows.map((r,i)=>`
    <div class="rank">
     <b>${i+1}.</b>
     ${String(r.username)}
     — ${Number(r.highscore||0).toLocaleString()}
    </div>
   `).join("");

 }catch(e){

  $("rankList").textContent=
   "❌ "+e.message;
 }
};

$("rankBack").onclick=menu;


/* =========================
   SPIEL
========================= */

const canvas=$("game");
const ctx=canvas.getContext("2d");

let W=innerWidth;
let H=innerHeight;

function resize(){
 W=innerWidth;
 H=innerHeight;
 canvas.width=W;
 canvas.height=H;
}

addEventListener("resize",resize);
resize();


let running=false;
let paused=false;

let lane=0;
let score=0;
let earned=0;

let speed=300;
let objects=[];

let coinTimer=0;
let obstacleTimer=0;

let doubleTime=0;
let magnetTime=0;

let last=performance.now();


function roadWidth(){
 return Math.min(W*.86,650);
}

function roadLeft(){
 return (W-roadWidth())/2;
}

function laneWidth(){
 return roadWidth()/3;
}

function laneX(n){
 return roadLeft()+
  laneWidth()*(n+1)+
  laneWidth()/2;
}


/* =========================
   START
========================= */

$("start").onclick=startGame;

function startGame(){

 document.querySelectorAll(".screen")
  .forEach(x=>x.classList.add("hidden"));

 canvas.style.display="block";
 $("hud").style.display="flex";
 $("pause").style.display="block";

 running=true;
 paused=false;

 lane=0;
 score=0;
 earned=0;

 speed=300;

 objects=[];

 coinTimer=.4;
 obstacleTimer=1;

 doubleTime=0;
 magnetTime=0;

 last=performance.now();

 $("hudWorld").textContent=
  worlds[selectedWorld()].name;

 updatePowerUI();
}


/* =========================
   WELT-FORTSCHRITT
========================= */

function worldDifficulty(){

 const w=selectedWorld();

 return w;
}


/* =========================
   MÜNZEN ANZAHL
========================= */

function coinsPerGroup(){

 const w=worlds[selectedWorld()];

 return Math.floor(
  w.min+
  Math.random()*(w.max-w.min+1)
 );
}


/* =========================
   OBJEKT SPAWN
========================= */

function freeLane(y){

 const choices=[-1,0,1].filter(laneNumber=>{

  return !objects.some(o=>
   o.lane===laneNumber &&
   Math.abs(o.y-y)<150
  );
 });

 if(!choices.length)return null;

 return choices[
  Math.floor(Math.random()*choices.length)
 ];
}


function spawnCoinGroup(){

 const amount=coinsPerGroup();

 let laneNumber=
  Math.floor(Math.random()*3)-1;

 let startY=-50;

 for(let i=0;i<amount;i++){

  const y=startY-i*48;

  /*
   Keine Münze darf direkt vor einem Auto
   auf derselben Spur erscheinen.
  */

  const blocked=
   objects.some(o=>
    o.type==="car" &&
    o.lane===laneNumber &&
    Math.abs(o.y-y)<180
   );

  if(blocked){

   laneNumber=
    Math.floor(Math.random()*3)-1;
  }

  objects.push({
   type:"coin",
   lane:laneNumber,
   y:y,
   collected:false
  });
 }
}


function spawnCar(){

 const laneNumber=freeLane(-120);

 if(laneNumber===null)return;

 objects.push({
  type:"car",
  lane:laneNumber,
  y:-130,
  color:[
   "#ff1744",
   "#008cff",
   "#ff9800",
   "#ffffff",
   "#9c27b0"
  ][Math.floor(Math.random()*5)]
 });
}


function spawnPower(type){

 const laneNumber=freeLane(-100);

 if(laneNumber===null)return;

 objects.push({
  type:type,
  lane:laneNumber,
  y:-100
 });
}


/* =========================
   HINTERGRUND
========================= */

function drawBackground(){

 const w=worlds[selectedWorld()];

 const g=ctx.createLinearGradient(0,0,0,H);

 g.addColorStop(0,w.sky);
 g.addColorStop(1,w.ground);

 ctx.fillStyle=g;
 ctx.fillRect(0,0,W,H);

 /*
   Sterne
 */

 if(selectedWorld()>=5){

  ctx.fillStyle="#ffffff88";

  for(let i=0;i<80;i++){

   const x=(i*97)%W;
   const y=(i*53)%H;

   ctx.fillRect(x,y,2,2);
  }
 }


 /*
   Gebäude
 */

 for(let i=0;i<24;i++){

  const width=
   20+(i%5)*10;

  const height=
   70+((i*43)%220);

  const x=i*(W/24);

  ctx.fillStyle=
   selectedWorld()>=8
   ?" #163b4a"
   :"#071820";

  ctx.fillRect(
   x,
   H-height-95,
   width,
   height
  );

  ctx.fillStyle="#00eaff77";

  for(
   let y=H-height-75;
   y<H-110;
   y+=22
  ){

   ctx.fillRect(
    x+5,
    y,
    4,
    6
   );
  }
 }


 /*
   Straße
 */

 const rw=roadWidth();
 const rl=roadLeft();

 ctx.fillStyle="#111820";
 ctx.fillRect(rl,0,rw,H);

 ctx.fillStyle="#00eaff";
 ctx.fillRect(rl,0,3,H);
 ctx.fillRect(rl+rw-3,0,3,H);

 const lw=laneWidth();

 const offset=
  (score*10)%90;

 ctx.fillStyle="#ffffff77";

 for(let y=-90+offset;y<H;y+=90){

  ctx.fillRect(rl+lw-2,y,4,45);
  ctx.fillRect(rl+lw*2-2,y,4,45);
 }
}


/* =========================
   OBJEKTE ZEICHNEN
========================= */

function drawObjects(){

 for(const o of objects){

  const x=laneX(o.lane);

  if(o.type==="coin"){

   ctx.save();

   ctx.shadowColor="#ffd600";
   ctx.shadowBlur=20;

   ctx.fillStyle="#ffd600";

   ctx.beginPath();
   ctx.arc(x,o.y,13,0,Math.PI*2);
   ctx.fill();

   ctx.fillStyle="#fff7a0";

   ctx.beginPath();
   ctx.arc(x-4,o.y-4,4,0,Math.PI*2);
   ctx.fill();

   ctx.restore();

  }else if(o.type==="double"){

   ctx.save();

   ctx.shadowColor="#00eaff";
   ctx.shadowBlur=25;

   ctx.fillStyle="#00eaff";

   ctx.beginPath();
   ctx.arc(x,o.y,22,0,Math.PI*2);
   ctx.fill();

   ctx.fillStyle="#001018";
   ctx.font="bold 17px Arial";
   ctx.textAlign="center";
   ctx.fillText("×2",x,o.y+6);

   ctx.restore();

  }else if(o.type==="magnet"){

   ctx.save();

   ctx.shadowColor="#ff1744";
   ctx.shadowBlur=25;

   ctx.strokeStyle="#ff1744";
   ctx.lineWidth=7;

   ctx.beginPath();
   ctx.arc(x,o.y,17,0,Math.PI);
   ctx.stroke();

   ctx.fillStyle="#ff1744";
   ctx.font="bold 18px Arial";
   ctx.textAlign="center";
   ctx.fillText("🧲",x,o.y+7);

   ctx.restore();

  }else{

   ctx.save();

   ctx.translate(x,o.y);

   ctx.shadowColor=o.color;
   ctx.shadowBlur=20;

   ctx.fillStyle=o.color;

   ctx.beginPath();
   ctx.roundRect(-30,-50,60,100,12);
   ctx.fill();

   ctx.shadowBlur=0;

   ctx.fillStyle="#17303b";
   ctx.fillRect(-18,-27,36,22);

   ctx.restore();
  }
 }
}


/* =========================
   CHARAKTER ZEICHNEN
========================= */

function drawPlayer(){

 const c=characters[selectedChar()];

 const x=laneX(lane);
 const y=H*.76;

 ctx.save();

 ctx.translate(x,y);

 ctx.shadowColor=c.color;
 ctx.shadowBlur=30;

 ctx.fillStyle=c.color;

 ctx.beginPath();
 ctx.roundRect(-28,-28,56,56,12);
 ctx.fill();

 ctx.shadowBlur=0;

 /*
   Je besser der Charakter,
   desto mehr Glitzer.
 */

 const glitter=
  Math.min(20,Math.floor(c.mult*3));

 for(let i=0;i<glitter;i++){

  const a=
   performance.now()/500+i;

  const gx=
   Math.cos(a)*(30+(i%4)*4);

  const gy=
   Math.sin(a)*(30+(i%4)*4);

  ctx.fillStyle="#ffffffcc";

  ctx.fillRect(gx,gy,3,3);
 }

 ctx.fillStyle="#021017";
 ctx.fillRect(-13,-8,26,8);

 ctx.restore();
}


/* =========================
   WISCHEN
========================= */

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

  if(!running||paused)return;

  const t=e.changedTouches[0];

  const dx=t.clientX-touchX;
  const dy=t.clientY-touchY;

  /*
    Nur horizontales Wischen.
    Kein Springen.
  */

  if(Math.abs(dx)>45 &&
     Math.abs(dx)>Math.abs(dy)){

   if(dx<0){
    lane=Math.max(-1,lane-1);
   }else{
    lane=Math.min(1,lane+1);
   }
  }
 },
 {passive:true}
);


/* =========================
   POWER-UP UI
========================= */

function updatePowerUI(){

 const p=$("power");

 const parts=[];

 if(doubleTime>0)
  parts.push("✖️2 "+Math.ceil(doubleTime)+"s");

 if(magnetTime>0)
  parts.push("🧲 "+Math.ceil(magnetTime)+"s");

 if(parts.length){

  p.textContent=parts.join("   ");
  p.style.display="block";

 }else{

  p.style.display="none";
 }
}


/* =========================
   MÜNZEN EINSAMMELN
========================= */

function collectCoin(){

 const c=characters[selectedChar()];
 const w=worlds[selectedWorld()];

 let amount=
  1*c.mult*w.mult;

 if(doubleTime>0)
  amount*=2;

 earned+=amount;
}


/* =========================
   UPDATE
========================= */

function update(dt){

 if(!running||paused)return;

 const s=dt/1000;

 score+=s*10;

 speed=
  Math.min(
   700,
   speed+s*3+
   selectedWorld()*0.7
  );


 coinTimer-=s;

 if(coinTimer<=0){

  spawnCoinGroup();

  coinTimer=
   3.8+
   Math.random()*1.5;
 }


 /*
   Hindernisse werden mit jeder Welt
   häufiger.
 */

 obstacleTimer-=s;

 if(obstacleTimer<=0){

  spawnCar();

  const difficulty=
   selectedWorld();

  obstacleTimer=
   Math.max(
    .65,
    1.35-difficulty*.055
   );
 }


 /*
   Power-ups
 */

 if(Math.random()<0.0015){
  spawnPower("double");
 }

 if(Math.random()<0.0012){
  spawnPower("magnet");
 }


 if(doubleTime>0)
  doubleTime=Math.max(0,doubleTime-s);

 if(magnetTime>0)
  magnetTime=Math.max(0,magnetTime-s);

 updatePowerUI();


 const playerY=H*.76;

 for(let i=objects.length-1;i>=0;i--){

  const o=objects[i];

  o.y+=speed*s;


  if(o.y>H+150){

   objects.splice(i,1);
   continue;
  }


  /*
    Magnet:
    Münzen auf allen Spuren
    werden angezogen.
  */

  if(
   o.type==="coin" &&
   magnetTime>0 &&
   Math.abs(o.y-playerY)<130
  ){

   o.lane=lane;
  }


  if(
   o.type==="coin" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<55
  ){

   collectCoin();

   objects.splice(i,1);

   continue;
  }


  if(
   o.type==="double" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<65
  ){

   doubleTime=30;

   objects.splice(i,1);

   continue;
  }


  if(
   o.type==="magnet" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<65
  ){

   magnetTime=30;

   objects.splice(i,1);

   continue;
  }


  /*
    Auto-Kollision.
  */

  if(
   o.type==="car" &&
   o.lane===lane &&
   Math.abs(o.y-playerY)<65
  ){

   endGame();
   return;
  }
 }


 $("hudCoins").textContent=
  Math.floor(
   player.coins+earned
  ).toLocaleString();

 $("hudScore").textContent=
  Math.floor(score).toLocaleString();
}


/* =========================
   GAME LOOP
========================= */

function loop(t){

 const dt=Math.min(40,t-last);

 last=t;

 if(canvas.style.display!=="none"){

  drawBackground();
  drawObjects();
  drawPlayer();
  update(dt);
 }

 requestAnimationFrame(loop);
}

requestAnimationFrame(loop);


/* =========================
   PAUSE
========================= */

$("pause").onclick=()=>{

 paused=!paused;

 $("pause").textContent=
  paused?"▶":"⏸";
};


/* =========================
   GAME OVER
========================= */

async function endGame(){

 if(!running)return;

 running=false;

 const final=Math.floor(score);

 player.coins+=Math.floor(earned);

 if(final>player.highscore)
  player.highscore=final;

 await savePlayer();

 $("finalScore").textContent=
  final.toLocaleString();

 $("finalCoins").textContent=
  Math.floor(earned).toLocaleString();

 canvas.style.display="none";
 $("hud").style.display="none";
 $("pause").style.display="none";
 $("power").style.display="none";

 menu();
}


/* =========================
   GAME OVER / MENÜ
========================= */

$("again").onclick=startGame;
$("gameMenu").onclick=menu;


/* =========================
   START
========================= */

screen("login");
</script>

</body>
</html>
