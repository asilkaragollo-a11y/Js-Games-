<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
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
  font-family:Arial,sans-serif;
  background:#02070d;
  color:white;
}

button,input{
  font-family:Arial,sans-serif;
}

button{
  touch-action:manipulation;
}

.screen{
  position:fixed;
  inset:0;
  z-index:100;
  display:flex;
  justify-content:center;
  align-items:center;
  padding:20px;
  background:
    radial-gradient(circle at 50% 10%,#07556b,#03151f 45%,#010408);
}

.hidden{
  display:none !important;
}

.card{
  width:100%;
  max-width:440px;
  padding:28px 22px;
  border-radius:28px;
  background:rgba(3,12,20,.98);
  border:1px solid #00eaff88;
  box-shadow:0 0 60px #00eaff22;
  text-align:center;
}

.logo{
  font-size:42px;
  font-weight:900;
  color:#08e9ff;
  text-shadow:0 0 10px #08e9ff,0 0 30px #08e9ff;
  margin-bottom:12px;
}

.subtitle{
  color:#a5c0ca;
  line-height:1.5;
  margin-bottom:18px;
}

/* EINGABEFELDER */

.input{
  display:block;
  width:100%;
  height:58px;
  margin:10px 0;
  padding:0 17px;

  border:2px solid #00eaff55;
  border-radius:16px;

  background:#f5ffb9;
  color:#000;

  font-size:17px;
  outline:none;

  pointer-events:auto;
  user-select:text;
  -webkit-user-select:text;
  -webkit-appearance:none;
  appearance:none;
}

.input:focus{
  border-color:#00eaff;
  box-shadow:0 0 18px #00eaff55;
}

.btn{
  display:block;
  width:100%;
  height:58px;
  margin:10px 0;
  border-radius:16px;
  border:0;
  font-size:17px;
  font-weight:900;
  cursor:pointer;
}

.primary{
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
}

.secondary{
  background:#071b25;
  color:white;
  border:1px solid #00eaff55;
}

.message{
  min-height:25px;
  margin-top:8px;
  color:#8defff;
  font-size:14px;
}

.stats{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:10px;
  margin:18px 0;
}

.stat{
  padding:15px;
  background:#071b25;
  border:1px solid #00eaff22;
  border-radius:15px;
}

.stat small{
  display:block;
  color:#7895a0;
}

.stat strong{
  display:block;
  margin-top:5px;
  font-size:24px;
}

.ranking{
  text-align:left;
  max-height:330px;
  overflow:auto;
  line-height:2;
}

/* SPIEL */

#game{
  position:fixed;
  inset:0;
  width:100%;
  height:100%;
  display:none;
  background:#02070d;
  z-index:10;
}

#hud{
  position:fixed;
  top:12px;
  left:10px;
  right:10px;
  z-index:20;
  display:none;
  justify-content:space-between;
  gap:6px;
  pointer-events:none;
}

.hud{
  padding:9px 12px;
  border-radius:12px;
  background:#031119e8;
  border:1px solid #00eaff55;
  font-weight:900;
  font-size:13px;
}

#pause{
  position:fixed;
  top:60px;
  right:10px;
  z-index:30;
  display:none;
  width:72px;
  height:42px;
  border-radius:12px;
  background:#071b25;
  color:white;
  border:1px solid #00eaff55;
}

#controls{
  position:fixed;
  bottom:16px;
  left:12px;
  right:12px;
  z-index:30;
  display:none;
  justify-content:space-between;
  align-items:center;
}

.controlRow{
  display:flex;
  gap:8px;
}

.control{
  width:64px;
  height:60px;
  border-radius:16px;
  background:#051822ee;
  color:white;
  border:1px solid #00eaff55;
  font-size:25px;
}

.jump{
  width:110px;
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
  font-weight:900;
  font-size:14px;
}
</style>
</head>

<body>

<!-- ==================================================
     SUPABASE
================================================== -->

<script>
const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/*
   HIER deinen bereits vorhandenen ANON/PUBLIC KEY einsetzen.

   Beispiel:

   const SUPABASE_KEY =
   "eyJ....";
*/

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";
</script>


<!-- ================= LOGIN ================= -->

<section id="loginScreen" class="screen">

<div class="card">

<div class="logo">CUBE RUSH</div>

<div class="subtitle">
Melde dich an und speichere deinen Highscore.
</div>

<input
  id="email"
  class="input"
  type="text"
  inputmode="email"
  autocomplete="email"
  placeholder="E-Mail"
>

<input
  id="password"
  class="input"
  type="password"
  autocomplete="current-password"
  placeholder="Passwort"
>

<button id="loginBtn" class="btn primary">
ANMELDEN
</button>

<button id="openRegister" class="btn secondary">
ACCOUNT ERSTELLEN
</button>

<div id="loginMessage" class="message"></div>

</div>
</section>


<!-- ================= REGISTRIERUNG ================= -->

<section id="registerScreen" class="screen hidden">

<div class="card">

<div class="logo">ACCOUNT</div>

<div class="subtitle">
Erstelle deinen Account.
</div>

<input
  id="username"
  class="input"
  type="text"
  maxlength="20"
  autocomplete="username"
  placeholder="Benutzername"
>

<input
  id="regEmail"
  class="input"
  type="text"
  inputmode="email"
  autocomplete="email"
  placeholder="E-Mail"
>

<input
  id="regPassword"
  class="input"
  type="password"
  autocomplete="new-password"
  placeholder="Passwort"
>

<button id="registerBtn" class="btn primary">
ACCOUNT ERSTELLEN
</button>

<button id="backLogin" class="btn secondary">
ZURÜCK
</button>

<div id="registerMessage" class="message"></div>

</div>
</section>


<!-- ================= MENÜ ================= -->

<section id="menuScreen" class="screen hidden">

<div class="card">

<div class="logo">CUBE RUSH</div>

<div class="subtitle">
Willkommen, <b id="playerName">Spieler</b>!
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

<button id="ranking" class="btn secondary">
🏆 RANGLISTE
</button>

<button id="logout" class="btn secondary">
ABMELDEN
</button>

</div>
</section>


<!-- ================= RANGLISTE ================= -->

<section id="rankingScreen" class="screen hidden">

<div class="card">

<div class="logo">🏆 RANGLISTE</div>

<div id="rankingList" class="ranking">
Lade Rangliste...
</div>

<button id="backMenu" class="btn secondary">
ZURÜCK
</button>

</div>
</section>


<!-- ================= GAME OVER ================= -->

<section id="gameOver" class="screen hidden">

<div class="card">

<div class="logo">CRASH!</div>

<div class="subtitle">
Deine Runde ist vorbei.
</div>

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


<!-- ================= SPIEL ================= -->

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

<button id="pause">
⏸
</button>

<div id="controls">

<div class="controlRow">

<button id="left" class="control">
←
</button>

<button id="right" class="control">
→
</button>

</div>

<button id="jump" class="control jump">
SPRINGEN
</button>

</div>


<script>

/* ==================================================
   AUTH
================================================== */

let token = "";
let player = null;


/* ==================================================
   SUPABASE REQUEST
================================================== */

async function api(path,options={}){

  const headers = {
    "apikey":SUPABASE_KEY,
    "Content-Type":"application/json"
  };

  if(token){
    headers["Authorization"] =
      "Bearer " + token;
  }

  const response =
    await fetch(
      SUPABASE_URL + path,
      {
        ...options,
        headers:{
          ...headers,
          ...(options.headers || {})
        }
      }
    );

  const text =
    await response.text();

  let data = null;

  try{
    data =
      text ? JSON.parse(text) : null;
  }catch{
    data = null;
  }

  if(!response.ok){

    throw new Error(
      data?.msg ||
      data?.message ||
      data?.error_description ||
      data?.error ||
      "Fehler"
    );
  }

  return data;
}


/* ==================================================
   SCREENS
================================================== */

function screen(id){

  document
    .querySelectorAll(".screen")
    .forEach(x =>
      x.classList.add("hidden")
    );

  document
    .getElementById(id)
    .classList.remove("hidden");
}


function message(id,text){

  document
    .getElementById(id)
    .textContent = text;
}


/* ==================================================
   LOGIN
================================================== */

document
.getElementById("loginBtn")
.addEventListener("click",login);


async function login(){

  const email =
    document
    .getElementById("email")
    .value
    .trim();

  const password =
    document
    .getElementById("password")
    .value;

  if(!email || !password){

    message(
      "loginMessage",
      "Bitte E-Mail und Passwort eingeben."
    );

    return;
  }

  try{

    const data =
      await api(
        "/auth/v1/token?grant_type=password",
        {
          method:"POST",
          body:JSON.stringify({
            email:email,
            password:password
          })
        }
      );

    token =
      data.access_token;

    player = {
      id:data.user.id,
      username:
        data.user.user_metadata?.username ||
        email.split("@")[0],
      coins:0,
      highscore:0
    };

    await loadPlayer();

    openMenu();

  }catch(error){

    message(
      "loginMessage",
      "❌ " + error.message
    );
  }
}


/* ==================================================
   REGISTRIERUNG
================================================== */

document
.getElementById("openRegister")
.addEventListener(
  "click",
  () => {
    message("loginMessage","");
    screen("registerScreen");
  }
);


document
.getElementById("backLogin")
.addEventListener(
  "click",
  () => {
    message("registerMessage","");
    screen("loginScreen");
  }
);


document
.getElementById("registerBtn")
.addEventListener(
  "click",
  register
);


async function register(){

  const username =
    document
    .getElementById("username")
    .value
    .trim();

  const email =
    document
    .getElementById("regEmail")
    .value
    .trim();

  const password =
    document
    .getElementById("regPassword")
    .value;

  if(username.length < 3){

    message(
      "registerMessage",
      "Benutzername: mindestens 3 Zeichen."
    );

    return;
  }

  if(!email.includes("@")){

    message(
      "registerMessage",
      "Bitte eine gültige E-Mail eingeben."
    );

    return;
  }

  if(password.length < 6){

    message(
      "registerMessage",
      "Passwort: mindestens 6 Zeichen."
    );

    return;
  }

  try{

    const data =
      await api(
        "/auth/v1/signup",
        {
          method:"POST",

          body:JSON.stringify({
            email:email,
            password:password,

            data:{
              username:username
            }
          })
        }
      );

    /*
      Wenn Confirm Email AUS ist,
      gibt Supabase direkt eine Session zurück.
    */

    if(data.access_token){

      token =
        data.access_token;

      player = {
        id:data.user.id,
        username:username,
        coins:0,
        highscore:0
      };

      await createPlayer();

      openMenu();

    }else{

      message(
        "registerMessage",
        "Der Account wurde erstellt. Wenn Supabase trotzdem eine E-Mail verlangt, ist 'Confirm email' in Supabase noch aktiviert."
      );
    }

  }catch(error){

    message(
      "registerMessage",
      "❌ " + error.message
    );
  }
}


/* ==================================================
   PLAYER
================================================== */

async function createPlayer(){

  try{

    await api(
      "/rest/v1/Players",
      {
        method:"POST",

        headers:{
          "Prefer":
          "resolution=ignore-duplicates"
        },

        body:JSON.stringify({
          id:player.id,
          username:player.username,
          coins:0,
          highscore:0
        })
      }
    );

  }catch(error){

    console.log(
      "Player konnte nicht erstellt werden:",
      error.message
    );
  }
}


async function loadPlayer(){

  try{

    const rows =
      await api(
        "/rest/v1/Players" +
        "?id=eq." +
        encodeURIComponent(player.id) +
        "&select=*"
      );

    if(rows.length){

      player.username =
        rows[0].username ||
        player.username;

      player.coins =
        Number(rows[0].coins || 0);

      player.highscore =
        Number(rows[0].highscore || 0);

    }else{

      await createPlayer();
    }

  }catch(error){

    console.log(
      "Spielerdaten:",
      error.message
    );
  }
}


async function savePlayer(){

  try{

    await api(
      "/rest/v1/Players?id=eq." +
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

  }catch(error){

    console.log(
      "Speichern:",
      error.message
    );
  }
}


/* ==================================================
   MENÜ
================================================== */

function openMenu(){

  document
    .getElementById("playerName")
    .textContent =
      player.username;

  document
    .getElementById("coins")
    .textContent =
      player.coins.toLocaleString();

  document
    .getElementById("highscore")
    .textContent =
      player.highscore.toLocaleString();

  screen("menuScreen");
}


/* ==================================================
   LOGOUT
================================================== */

document
.getElementById("logout")
.addEventListener(
  "click",
  () => {

    token="";
    player=null;

    document.getElementById("email").value="";
    document.getElementById("password").value="";

    screen("loginScreen");
  }
);


/* ==================================================
   RANGLISTE
================================================== */

document
.getElementById("ranking")
.addEventListener(
  "click",
  loadRanking
);


document
.getElementById("backMenu")
.addEventListener(
  "click",
  openMenu
);


async function loadRanking(){

  screen("rankingScreen");

  const list =
    document.getElementById(
      "rankingList"
    );

  list.textContent =
    "Lade Rangliste...";

  try{

    const rows =
      await api(
        "/rest/v1/Players" +
        "?select=username,highscore" +
        "&order=highscore.desc" +
        "&limit=50"
      );

    if(!rows.length){

      list.textContent =
        "Noch keine Spieler.";

      return;
    }

    list.innerHTML =
      rows.map(
        (r,i) => {

          const name =
            String(
              r.username || "Spieler"
            )
            .replaceAll("<","&lt;")
            .replaceAll(">","&gt;");

          return `
            <div>
              <b>${i+1}.</b>
              ${name}
              — ${Number(
                r.highscore || 0
              ).toLocaleString()}
            </div>
          `;
        }
      ).join("");

  }catch(error){

    list.textContent =
      "❌ " + error.message;
  }
}


/* ==================================================
   SPIEL
================================================== */

const canvas =
  document.getElementById("game");

const ctx =
  canvas.getContext("2d");

let W=innerWidth;
let H=innerHeight;

canvas.width=W;
canvas.height=H;

window.addEventListener(
  "resize",
  () => {

    W=innerWidth;
    H=innerHeight;

    canvas.width=W;
    canvas.height=H;
  }
);


let running=false;
let paused=false;

let lane=0;

let score=0;
let roundCoins=0;

let speed=330;

let objects=[];

let carTimer=1;
let coinTimer=2;

let jumpY=0;
let jumpVelocity=0;

let lastTime=
  performance.now();


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

  return (
    roadLeft()+
    laneWidth()*(n+1)+
    laneWidth()/2
  );
}


/* ==================================================
   START GAME
================================================== */

document
.getElementById("start")
.addEventListener(
  "click",
  startGame
);


function startGame(){

  document
    .querySelectorAll(".screen")
    .forEach(x =>
      x.classList.add("hidden")
    );

  canvas.style.display="block";

  document
    .getElementById("hud")
    .style.display="flex";

  document
    .getElementById("controls")
    .style.display="flex";

  document
    .getElementById("pause")
    .style.display="block";

  running=true;
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

  lastTime=
    performance.now();
}


/* ==================================================
   BACKGROUND
================================================== */

function drawBackground(){

  const gradient =
    ctx.createLinearGradient(
      0,0,0,H
    );

  gradient.addColorStop(
    0,
    "#063d50"
  );

  gradient.addColorStop(
    1,
    "#02050a"
  );

  ctx.fillStyle=gradient;

  ctx.fillRect(
    0,0,W,H
  );


  for(
    let i=0;
    i<24;
    i++
  ){

    const width=
      25+(i%5)*12;

    const height=
      100+((i*53)%240);

    const x=
      i*(W/23);

    ctx.fillStyle=
      "#071820";

    ctx.fillRect(
      x,
      H-height-120,
      width,
      height
    );
  }


  const rw=roadWidth();
  const rl=roadLeft();

  ctx.fillStyle="#111820";

  ctx.fillRect(
    rl,0,rw,H
  );

  ctx.fillStyle="#00eaff";

  ctx.fillRect(
    rl,0,3,H
  );

  ctx.fillRect(
    rl+rw-3,0,3,H
  );


  const lw=laneWidth();

  const offset=
    (score*9)%90;

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


/* ==================================================
   OBSTACLES
================================================== */

function spawnCar(){

  const possible =
    [-1,0,1].filter(
      n =>
        !objects.some(
          o =>
            o.type==="car" &&
            o.lane===n &&
            o.y<250
        )
    );

  if(!possible.length)
    return;

  const selected =
    possible[
      Math.floor(
        Math.random()*possible.length
      )
    ];

  objects.push({

    type:"car",
    lane:selected,
    y:-110,

    color:[
      "#ff1744",
      "#008cff",
      "#ff9800",
      "#ffffff"
    ][
      Math.floor(
        Math.random()*4
      )
    ]
  });
}


function spawnCoins(){

  const amount=
    3+Math.floor(
      Math.random()*4
    );

  const selectedLane=
    [-1,0,1][
      Math.floor(
        Math.random()*3
      )
    ];

  for(
    let i=0;
    i<amount;
    i++
  ){

    objects.push({
      type:"coin",
      lane:selectedLane,
      y:-40-i*65
    });
  }
}


/* ==================================================
   DRAW OBJECTS
================================================== */

function drawObjects(){

  for(
    const object of objects
  ){

    const x=
      laneX(object.lane);

    if(object.type==="coin"){

      ctx.save();

      ctx.shadowColor="#ffd600";
      ctx.shadowBlur=20;
      ctx.fillStyle="#ffd600";

      ctx.beginPath();

      ctx.arc(
        x,
        object.y,
        14,
        0,
        Math.PI*2
      );

      ctx.fill();

      ctx.restore();

    }else{

      ctx.save();

      ctx.translate(
        x,
        object.y
      );

      ctx.shadowColor=
        object.color;

      ctx.shadowBlur=18;

      ctx.fillStyle=
        object.color;

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


/* ==================================================
   PLAYER
================================================== */

function drawPlayer(){

  const x=laneX(lane);
  const y=H*.76-jumpY;

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


/* ==================================================
   STEUERUNG
================================================== */

function moveLeft(){

  if(!running || paused)
    return;

  lane=
    Math.max(
      -1,
      lane-1
    );
}


function moveRight(){

  if(!running || paused)
    return;

  lane=
    Math.min(
      1,
      lane+1
    );
}


function jump(){

  if(!running || paused)
    return;

  if(jumpY<=0)
    jumpVelocity=850;
}


document
.getElementById("left")
.addEventListener(
  "click",
  moveLeft
);


document
.getElementById("right")
.addEventListener(
  "click",
  moveRight
);


document
.getElementById("jump")
.addEventListener(
  "click",
  jump
);


document.addEventListener(
  "keydown",
  e => {

    if(e.key==="ArrowLeft")
      moveLeft();

    if(e.key==="ArrowRight")
      moveRight();

    if(
      e.key==="ArrowUp" ||
      e.key===" "
    )
      jump();
  }
);


/* ==================================================
   UPDATE
================================================== */

function update(dt){

  if(!running || paused)
    return;

  const seconds=dt/1000;

  score+=seconds*10;

  speed=
    Math.min(
      700,
      speed+seconds*3
    );


  jumpVelocity-=
    1900*seconds;

  jumpY+=
    jumpVelocity*seconds;

  if(jumpY<0){

    jumpY=0;
    jumpVelocity=0;
  }


  coinTimer-=seconds;

  if(coinTimer<=0){

    spawnCoins();

    coinTimer=
      4+Math.random()*1.5;
  }


  carTimer-=seconds;

  if(carTimer<=0){

    spawnCar();

    carTimer=
      1.25+Math.random()*.75;
  }


  for(
    let i=objects.length-1;
    i>=0;
    i--
  ){

    const object=
      objects[i];

    object.y+=
      speed*seconds;

    if(object.y>H+120){

      objects.splice(i,1);
      continue;
    }


    const playerY=
      H*.76-jumpY;


    if(
      object.type==="coin" &&
      object.lane===lane &&
      Math.abs(
        object.y-playerY
      )<55
    ){

      roundCoins++;

      objects.splice(i,1);

      continue;
    }


    if(
      object.type==="car" &&
      object.lane===lane &&
      Math.abs(
        object.y-playerY
      )<65
    ){

      if(jumpY<90){

        endGame();
        return;
      }
    }
  }


  document
    .getElementById("hudCoins")
    .textContent=
      (
        player.coins+
        roundCoins
      ).toLocaleString();

  document
    .getElementById("hudScore")
    .textContent=
      Math.floor(score)
      .toLocaleString();
}


/* ==================================================
   LOOP
================================================== */

function gameLoop(time){

  const dt=
    Math.min(
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

  requestAnimationFrame(
    gameLoop
  );
}

requestAnimationFrame(
  gameLoop
);


/* ==================================================
   PAUSE
================================================== */

document
.getElementById("pause")
.addEventListener(
  "click",
  () => {

    paused=!paused;

    document
      .getElementById("pause")
      .textContent=
        paused ? "▶" : "⏸";
  }
);


/* ==================================================
   GAME OVER
================================================== */

async function endGame(){

  running=false;

  const final=
    Math.floor(score);

  player.coins+=
    roundCoins;

  if(
    final>player.highscore
  ){

    player.highscore=
      final;
  }

  await savePlayer();

  document
    .getElementById("finalScore")
    .textContent=
      final.toLocaleString();

  document
    .getElementById("finalCoins")
    .textContent=
      roundCoins.toLocaleString();

  canvas.style.display="none";

  document
    .getElementById("hud")
    .style.display="none";

  document
    .getElementById("controls")
    .style.display="none";

  document
    .getElementById("pause")
    .style.display="none";

  screen("gameOver");
}


document
.getElementById("again")
.addEventListener(
  "click",
  startGame
);


document
.getElementById("gameMenu")
.addEventListener(
  "click",
  openMenu
);


/* ==================================================
   START
================================================== */

screen("loginScreen");

</script>

</body>
</html>
