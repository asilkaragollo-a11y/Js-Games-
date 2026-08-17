<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>CUBE RUSH</title>

<style>
*{
  box-sizing:border-box;
  -webkit-tap-highlight-color:transparent;
}

html,body{
  margin:0;
  width:100%;
  min-height:100%;
  background:#02070d;
  color:white;
  font-family:Arial,sans-serif;
}

body{
  overflow-x:hidden;
}

button,input{
  font-family:Arial,sans-serif;
}

button{
  -webkit-appearance:none;
  appearance:none;
  touch-action:manipulation;
  user-select:none;
  -webkit-user-select:none;
}

.screen{
  position:fixed;
  inset:0;
  z-index:1000;
  display:flex;
  align-items:center;
  justify-content:center;
  padding:20px;
  background:
    radial-gradient(circle at 50% 15%,#074b60 0%,#03151f 42%,#010408 100%);
}

.card{
  width:min(440px,100%);
  padding:28px 22px;
  border-radius:28px;
  background:rgba(3,12,20,.97);
  border:1px solid rgba(0,234,255,.55);
  box-shadow:
    0 0 20px rgba(0,234,255,.15),
    0 0 70px rgba(0,234,255,.08);
  text-align:center;
}

.logo{
  font-size:42px;
  font-weight:1000;
  letter-spacing:-2px;
  color:#08e9ff;
  text-shadow:
    0 0 8px #08e9ff,
    0 0 25px #08e9ff;
  margin-bottom:12px;
}

.subtitle{
  color:#a5c0ca;
  font-size:16px;
  line-height:1.5;
  margin-bottom:20px;
}

input{
  display:block;
  width:100%;
  height:58px;
  margin:10px 0;
  padding:0 17px;
  border-radius:16px;
  border:1px solid #00eaff55;
  outline:none;
  background:#f5ffb9;
  color:#050505;
  font-size:17px;
}

input:focus{
  border-color:#00eaff;
  box-shadow:0 0 15px #00eaff55;
}

.btn{
  display:block;
  width:100%;
  height:58px;
  margin:10px 0;
  border-radius:16px;
  font-size:17px;
  font-weight:900;
  cursor:pointer;
}

.btn:active{
  transform:scale(.98);
}

.primary{
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
  box-shadow:0 5px 20px rgba(0,180,255,.2);
}

.secondary{
  background:#071b25;
  color:white;
  border:1px solid #00eaff55;
}

.message{
  min-height:24px;
  margin-top:8px;
  font-size:14px;
  color:#8defff;
}

.hidden{
  display:none!important;
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
  font-size:11px;
}

.stat strong{
  display:block;
  margin-top:5px;
  font-size:24px;
}

#game{
  position:fixed;
  inset:0;
  display:none;
  width:100%;
  height:100%;
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
  background:rgba(3,17,25,.9);
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
  min-height:42px;
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
  align-items:center;
  justify-content:space-between;
  pointer-events:none;
}

.control-row{
  display:flex;
  gap:8px;
  pointer-events:auto;
}

.control{
  width:64px;
  height:60px;
  min-height:60px;
  border-radius:16px;
  background:rgba(5,24,34,.94);
  color:white;
  border:1px solid #00eaff55;
  font-size:25px;
  pointer-events:auto;
}

.jump{
  width:110px;
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
  font-weight:900;
  font-size:14px;
  pointer-events:auto;
}

.ranking{
  text-align:left;
  max-height:330px;
  overflow:auto;
  line-height:2;
  padding:5px;
}
</style>
</head>

<body>

<!-- ==================================================
     SUPABASE
     ==================================================

     DEIN SUPABASE-PROJEKT:
     https://lfsifdmaftztykpckdsh.supabase.co

     NUR HIER DEN ANON/PUBLIC/PUBLISHABLE KEY EINTRAGEN.

     NICHT den service_role KEY verwenden.
================================================== -->

<script>
const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/*
=========================================================
HIER EINMAL DEINEN ANON / PUBLIC / PUBLISHABLE KEY
EINTRAGEN:
=========================================================
*/

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";
</script>


<!-- ================= LOGIN ================= -->

<section id="loginScreen" class="screen">

  <div class="card">

    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
      Melde dich an und speichere deinen Highscore
      in der Rangliste.
    </div>

    <input
      id="loginEmail"
      type="email"
      autocomplete="email"
      placeholder="E-Mail"
    >

    <input
      id="loginPassword"
      type="password"
      autocomplete="current-password"
      placeholder="Passwort"
    >

    <button
      type="button"
      id="loginBtn"
      class="btn primary"
    >
      ANMELDEN
    </button>

    <button
      type="button"
      id="registerOpen"
      class="btn secondary"
    >
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
      Erstelle deinen Cube-Rush-Account.
    </div>

    <input
      id="registerUsername"
      type="text"
      maxlength="20"
      autocomplete="username"
      placeholder="Benutzername"
    >

    <input
      id="registerEmail"
      type="email"
      autocomplete="email"
      placeholder="E-Mail"
    >

    <input
      id="registerPassword"
      type="password"
      autocomplete="new-password"
      placeholder="Passwort"
    >

    <button
      type="button"
      id="registerBtn"
      class="btn primary"
    >
      ACCOUNT ERSTELLEN
    </button>

    <button
      type="button"
      id="backLogin"
      class="btn secondary"
    >
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
      Willkommen,
      <b id="menuUsername">Spieler</b>!
    </div>

    <div class="stats">

      <div class="stat">
        <small>MÜNZEN</small>
        <strong id="menuCoins">0</strong>
      </div>

      <div class="stat">
        <small>HIGHSCORE</small>
        <strong id="menuHighscore">0</strong>
      </div>

    </div>

    <button
      type="button"
      id="startBtn"
      class="btn primary"
    >
      ▶ SPIEL STARTEN
    </button>

    <button
      type="button"
      id="rankingBtn"
      class="btn secondary"
    >
      🏆 RANGLISTE
    </button>

    <button
      type="button"
      id="logoutBtn"
      class="btn secondary"
    >
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

    <button
      type="button"
      id="rankingBack"
      class="btn secondary"
    >
      ZURÜCK
    </button>

  </div>

</section>


<!-- ================= GAME OVER ================= -->

<section id="gameOverScreen" class="screen hidden">

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

    <button
      type="button"
      id="againBtn"
      class="btn primary"
    >
      🔄 NOCHMAL
    </button>

    <button
      type="button"
      id="gameMenuBtn"
      class="btn secondary"
    >
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

<button type="button" id="pause">⏸</button>

<div id="controls">

  <div class="control-row">

    <button type="button" id="leftBtn" class="control">
      ←
    </button>

    <button type="button" id="rightBtn" class="control">
      →
    </button>

  </div>

  <button type="button" id="jumpBtn" class="control jump">
    SPRINGEN
  </button>

</div>


<script>

/* =====================================================
   SPIEL / APP
===================================================== */

let authToken = "";
let currentPlayer = null;

const canvas =
  document.getElementById("game");

const ctx =
  canvas.getContext("2d");

let W = window.innerWidth;
let H = window.innerHeight;

canvas.width = W;
canvas.height = H;

window.addEventListener("resize",()=>{
  W = window.innerWidth;
  H = window.innerHeight;

  canvas.width = W;
  canvas.height = H;
});


/* =====================================================
   HILFSFUNKTIONEN
===================================================== */

function showScreen(id){

  document
    .querySelectorAll(".screen")
    .forEach(screen=>{
      screen.classList.add("hidden");
    });

  document
    .getElementById(id)
    .classList.remove("hidden");
}


function setMessage(id,text){

  document
    .getElementById(id)
    .textContent = text;
}


function clean(text){

  return String(text)
    .replaceAll("&","&amp;")
    .replaceAll("<","&lt;")
    .replaceAll(">","&gt;");
}


/* =====================================================
   SUPABASE REQUEST
===================================================== */

async function supabaseRequest(url,options={}){

  if(
    SUPABASE_KEY ===
    "DEIN_ANON_PUBLIC_KEY_HIER"
  ){

    throw new Error(
      "Der Supabase ANON/PUBLIC-Key fehlt."
    );
  }

  const headers = {

    "apikey":SUPABASE_KEY,

    "Content-Type":
      "application/json",

    ...(authToken
      ? {
          "Authorization":
            "Bearer "+authToken
        }
      : {}),

    ...(options.headers || {})
  };

  const response =
    await fetch(
      SUPABASE_URL + url,
      {
        ...options,
        headers
      }
    );

  const text =
    await response.text();

  let data = null;

  try{
    data =
      text
        ? JSON.parse(text)
        : null;
  }
  catch{
    data = null;
  }

  if(!response.ok){

    throw new Error(
      data?.msg ||
      data?.message ||
      data?.error_description ||
      "Supabase-Fehler"
    );
  }

  return data;
}


/* =====================================================
   LOGIN
===================================================== */

document
  .getElementById("loginBtn")
  .addEventListener("click",login);

async function login(){

  const email =
    document
      .getElementById("loginEmail")
      .value
      .trim();

  const password =
    document
      .getElementById("loginPassword")
      .value;

  if(!email || !password){

    setMessage(
      "loginMessage",
      "Bitte E-Mail und Passwort eingeben."
    );

    return;
  }

  const button =
    document.getElementById("loginBtn");

  button.disabled = true;
  button.textContent = "ANMELDUNG...";

  try{

    const data =
      await supabaseRequest(
        "/auth/v1/token?grant_type=password",
        {
          method:"POST",

          body:JSON.stringify({
            email:email,
            password:password
          })
        }
      );

    authToken =
      data.access_token;

    currentPlayer = {

      id:data.user.id,

      username:
        data.user.user_metadata?.username ||
        email.split("@")[0],

      coins:0,

      highscore:0
    };

    await loadPlayer();

    openMenu();

  }
  catch(error){

    setMessage(
      "loginMessage",
      "❌ "+error.message
    );

  }
  finally{

    button.disabled = false;
    button.textContent = "ANMELDEN";
  }
}


/* =====================================================
   REGISTRIERUNG
===================================================== */

document
  .getElementById("registerOpen")
  .addEventListener(
    "click",
    ()=>{
      setMessage("loginMessage","");
      showScreen("registerScreen");
    }
  );


document
  .getElementById("backLogin")
  .addEventListener(
    "click",
    ()=>{
      setMessage("registerMessage","");
      showScreen("loginScreen");
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
      .getElementById("registerUsername")
      .value
      .trim();

  const email =
    document
      .getElementById("registerEmail")
      .value
      .trim();

  const password =
    document
      .getElementById("registerPassword")
      .value;

  if(username.length < 3){

    setMessage(
      "registerMessage",
      "Benutzername: mindestens 3 Zeichen."
    );

    return;
  }

  if(!email.includes("@")){

    setMessage(
      "registerMessage",
      "Bitte eine gültige E-Mail eingeben."
    );

    return;
  }

  if(password.length < 6){

    setMessage(
      "registerMessage",
      "Passwort: mindestens 6 Zeichen."
    );

    return;
  }

  const button =
    document.getElementById("registerBtn");

  button.disabled = true;
  button.textContent = "ACCOUNT WIRD ERSTELLT...";

  try{

    const data =
      await supabaseRequest(
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

    if(data.access_token){

      authToken =
        data.access_token;

      currentPlayer = {

        id:data.user.id,

        username:username,

        coins:0,

        highscore:0
      };

      await createPlayer();

      openMenu();

    }
    else{

      setMessage(
        "registerMessage",
        "✅ Account erstellt. Prüfe deine E-Mail und bestätige den Account."
      );

    }

  }
  catch(error){

    setMessage(
      "registerMessage",
      "❌ "+error.message
    );

  }
  finally{

    button.disabled = false;
    button.textContent =
      "ACCOUNT ERSTELLEN";
  }
}


/* =====================================================
   PLAYER ERSTELLEN
===================================================== */

async function createPlayer(){

  try{

    await supabaseRequest(
      "/rest/v1/Players",
      {
        method:"POST",

        headers:{
          "Prefer":
            "resolution=ignore-duplicates"
        },

        body:JSON.stringify({

          id:currentPlayer.id,

          username:
            currentPlayer.username,

          coins:0,

          highscore:0
        })
      }
    );

  }
  catch(error){

    console.log(
      "Player-Tabelle:",
      error.message
    );
  }
}


/* =====================================================
   PLAYER LADEN
===================================================== */

async function loadPlayer(){

  try{

    const rows =
      await supabaseRequest(
        "/rest/v1/Players"+
        "?id=eq."+
        encodeURIComponent(
          currentPlayer.id
        )+
        "&select=*"
      );

    if(rows && rows.length){

      currentPlayer.username =
        rows[0].username ||
        currentPlayer.username;

      currentPlayer.coins =
        Number(rows[0].coins || 0);

      currentPlayer.highscore =
        Number(
          rows[0].highscore || 0
        );
    }

  }
  catch(error){

    console.log(
      "Player laden:",
      error.message
    );
  }
}


/* =====================================================
   PLAYER SPEICHERN
===================================================== */

async function savePlayer(){

  try{

    await supabaseRequest(
      "/rest/v1/Players?id=eq."+
      encodeURIComponent(
        currentPlayer.id
      ),
      {
        method:"PATCH",

        headers:{
          "Prefer":
            "return=minimal"
        },

        body:JSON.stringify({

          username:
            currentPlayer.username,

          coins:
            currentPlayer.coins,

          highscore:
            currentPlayer.highscore
        })
      }
    );

  }
  catch(error){

    console.log(
      "Speichern:",
      error.message
    );
  }
}


/* =====================================================
   MENÜ
===================================================== */

function openMenu(){

  document
    .getElementById("menuUsername")
    .textContent =
      currentPlayer.username;

  document
    .getElementById("menuCoins")
    .textContent =
      currentPlayer.coins
        .toLocaleString();

  document
    .getElementById("menuHighscore")
    .textContent =
      currentPlayer.highscore
        .toLocaleString();

  showScreen("menuScreen");
}


/* =====================================================
   LOGOUT
===================================================== */

document
  .getElementById("logoutBtn")
  .addEventListener(
    "click",
    ()=>{
      authToken = "";
      currentPlayer = null;

      document
        .getElementById("loginEmail")
        .value = "";

      document
        .getElementById("loginPassword")
        .value = "";

      showScreen("loginScreen");
    }
  );


/* =====================================================
   RANGLISTE
===================================================== */

document
  .getElementById("rankingBtn")
  .addEventListener(
    "click",
    loadRanking
  );


document
  .getElementById("rankingBack")
  .addEventListener(
    "click",
    openMenu
  );


async function loadRanking(){

  showScreen("rankingScreen");

  const list =
    document.getElementById(
      "rankingList"
    );

  list.textContent =
    "Lade Rangliste...";

  try{

    const rows =
      await supabaseRequest(
        "/rest/v1/Players"+
        "?select=username,highscore"+
        "&order=highscore.desc"+
        "&limit=50"
      );

    if(!rows.length){

      list.textContent =
        "Noch keine Spieler.";

      return;
    }

    list.innerHTML =
      rows.map(
        (row,index)=>`

          <div>
            <b>${index+1}.</b>
            ${clean(row.username)}
            —
            ${Number(
              row.highscore || 0
            ).toLocaleString()}
          </div>

        `
      ).join("");

  }
  catch(error){

    list.textContent =
      "❌ "+error.message;
  }
}


/* =====================================================
   GAME
===================================================== */

let running = false;
let paused = false;

let lane = 0;

let score = 0;
let roundCoins = 0;

let speed = 330;

let objects = [];

let carTimer = 1;
let coinTimer = 2;

let jumpY = 0;
let jumpVelocity = 0;

let lastTime =
  performance.now();


/* ================= ROAD ================= */

function roadWidth(){

  return Math.min(
    W * .86,
    650
  );
}

function roadLeft(){

  return (
    W - roadWidth()
  ) / 2;
}

function laneWidth(){

  return roadWidth()/3;
}

function laneX(n){

  return (
    roadLeft() +
    laneWidth()*(n+1) +
    laneWidth()/2
  );
}


/* ================= START ================= */

document
  .getElementById("startBtn")
  .addEventListener(
    "click",
    startGame
  );


function startGame(){

  document
    .querySelectorAll(".screen")
    .forEach(x=>{
      x.classList.add("hidden");
    });

  canvas.style.display =
    "block";

  document
    .getElementById("hud")
    .style.display =
      "flex";

  document
    .getElementById("controls")
    .style.display =
      "flex";

  document
    .getElementById("pause")
    .style.display =
      "block";

  running = true;
  paused = false;

  lane = 0;

  score = 0;
  roundCoins = 0;

  speed = 330;

  objects = [];

  carTimer = 1;
  coinTimer = 2;

  jumpY = 0;
  jumpVelocity = 0;

  lastTime =
    performance.now();
}


/* ================= BACKGROUND ================= */

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

  ctx.fillStyle =
    gradient;

  ctx.fillRect(
    0,0,W,H
  );


  /* CITY */

  for(
    let i=0;
    i<24;
    i++
  ){

    const width =
      25+(i%5)*12;

    const height =
      100+
      ((i*53)%240);

    const x =
      i*(W/23);

    ctx.fillStyle =
      "#071820";

    ctx.fillRect(
      x,
      H-height-120,
      width,
      height
    );

    ctx.fillStyle =
      "#00eaff55";

    for(
      let y=
        H-height-105;
      y<H-140;
      y+=25
    ){

      ctx.fillRect(
        x+7,
        y,
        5,
        8
      );
    }
  }


  /* ROAD */

  const rw =
    roadWidth();

  const rl =
    roadLeft();

  ctx.fillStyle =
    "#111820";

  ctx.fillRect(
    rl,
    0,
    rw,
    H
  );


  /* ROAD EDGES */

  ctx.fillStyle =
    "#00eaff";

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


  /* LANES */

  const lw =
    laneWidth();

  const offset =
    (score*9)%90;

  ctx.fillStyle =
    "#ffffff88";

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


/* ================= CARS ================= */

function spawnCar(){

  /*
    Nicht alle drei Spuren gleichzeitig blockieren.
    Dadurch bleibt immer eine Ausweichmöglichkeit.
  */

  const possible =
    [-1,0,1].filter(
      n=>
        !objects.some(
          object=>
            object.type==="car" &&
            object.lane===n &&
            object.y<250
        )
    );

  if(!possible.length)
    return;

  const selected =
    possible[
      Math.floor(
        Math.random()*
        possible.length
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


/* ================= COINS ================= */

function spawnCoins(){

  /*
    NEON CITY:
    Genau 3 bis 6 Münzen
    pro Gruppe.
  */

  const amount =
    3+
    Math.floor(
      Math.random()*4
    );

  const selectedLane =
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

      y:
        -40 -
        i*65
    });
  }
}


/* ================= OBJECTS ================= */

function drawObjects(){

  for(
    const object of objects
  ){

    const x =
      laneX(object.lane);


    if(object.type==="coin"){

      ctx.save();

      ctx.shadowColor =
        "#ffd600";

      ctx.shadowBlur =
        20;

      ctx.fillStyle =
        "#ffd600";

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

    }
    else{

      ctx.save();

      ctx.translate(
        x,
        object.y
      );

      ctx.shadowColor =
        object.color;

      ctx.shadowBlur =
        18;

      ctx.fillStyle =
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

      ctx.fillStyle =
        "#17303b";

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


/* ================= PLAYER ================= */

function drawPlayer(){

  const x =
    laneX(lane);

  const y =
    H*.76-jumpY;

  ctx.save();

  ctx.translate(
    x,
    y
  );

  ctx.shadowColor =
    "#00eaff";

  ctx.shadowBlur =
    25;

  ctx.fillStyle =
    "#00eaff";

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

  ctx.fillStyle =
    "#021017";

  ctx.fillRect(
    -13,
    -9,
    26,
    9
  );

  ctx.restore();
}


/* ================= MOVEMENT ================= */

function moveLeft(){

  if(!running || paused)
    return;

  lane =
    Math.max(
      -1,
      lane-1
    );
}

function moveRight(){

  if(!running || paused)
    return;

  lane =
    Math.min(
      1,
      lane+1
    );
}

function jump(){

  if(!running || paused)
    return;

  if(jumpY<=0){

    jumpVelocity =
      850;
  }
}


document
  .getElementById("leftBtn")
  .addEventListener(
    "click",
    moveLeft
  );

document
  .getElementById("rightBtn")
  .addEventListener(
    "click",
    moveRight
  );

document
  .getElementById("jumpBtn")
  .addEventListener(
    "click",
    jump
  );


/* ================= KEYBOARD ================= */

document.addEventListener(
  "keydown",
  event=>{

    if(event.key==="ArrowLeft")
      moveLeft();

    if(event.key==="ArrowRight")
      moveRight();

    if(
      event.key==="ArrowUp" ||
      event.key===" "
    )
      jump();
  }
);


/* ================= TOUCH ================= */

let startX=0;
let startY=0;

canvas.addEventListener(
  "touchstart",
  event=>{

    const touch =
      event.changedTouches[0];

    startX =
      touch.clientX;

    startY =
      touch.clientY;
  },
  {passive:true}
);


canvas.addEventListener(
  "touchend",
  event=>{

    const touch =
      event.changedTouches[0];

    const dx =
      touch.clientX-startX;

    const dy =
      touch.clientY-startY;

    if(
      Math.abs(dx)>
      Math.abs(dy)
    ){

      if(dx>35)
        moveRight();

      if(dx<-35)
        moveLeft();

    }
    else if(dy<-35){

      jump();
    }
  },
  {passive:true}
);


/* ================= UPDATE ================= */

function update(dt){

  if(!running || paused)
    return;

  const seconds =
    dt/1000;

  score +=
    seconds*10;

  speed =
    Math.min(
      700,
      speed+seconds*3
    );


  /* JUMP */

  jumpVelocity -=
    1900*seconds;

  jumpY +=
    jumpVelocity*seconds;

  if(jumpY<0){

    jumpY=0;
    jumpVelocity=0;
  }


  /* COINS */

  coinTimer -=
    seconds;

  if(coinTimer<=0){

    spawnCoins();

    coinTimer =
      4+
      Math.random()*1.5;
  }


  /* CARS */

  carTimer -=
    seconds;

  if(carTimer<=0){

    spawnCar();

    carTimer =
      1.25+
      Math.random()*.75;
  }


  /* OBJECTS */

  for(
    let i=objects.length-1;
    i>=0;
    i--
  ){

    const object =
      objects[i];

    object.y +=
      speed*seconds;


    if(
      object.y >
      H+120
    ){

      objects.splice(
        i,
        1
      );

      continue;
    }


    const playerY =
      H*.76-jumpY;


    /* COIN */

    if(
      object.type==="coin" &&
      object.lane===lane &&
      Math.abs(
        object.y-playerY
      )<55
    ){

      roundCoins++;

      objects.splice(
        i,
        1
      );

      continue;
    }


    /* CAR */

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
    .textContent =
      (
        currentPlayer.coins+
        roundCoins
      ).toLocaleString();

  document
    .getElementById("hudScore")
    .textContent =
      Math.floor(score)
        .toLocaleString();
}


/* ================= GAME LOOP ================= */

function gameLoop(time){

  const dt =
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


/* ================= PAUSE ================= */

document
  .getElementById("pause")
  .addEventListener(
    "click",
    ()=>{

      paused=!paused;

      document
        .getElementById("pause")
        .textContent =
          paused
            ? "▶"
            : "⏸";
    }
  );


/* ================= GAME OVER ================= */

async function endGame(){

  running=false;

  const final =
    Math.floor(score);

  currentPlayer.coins +=
    roundCoins;

  if(
    final>
    currentPlayer.highscore
  ){

    currentPlayer.highscore =
      final;
  }

  await savePlayer();

  document
    .getElementById("finalScore")
    .textContent =
      final.toLocaleString();

  document
    .getElementById("finalCoins")
    .textContent =
      roundCoins.toLocaleString();

  canvas.style.display =
    "none";

  document
    .getElementById("hud")
    .style.display =
      "none";

  document
    .getElementById("controls")
    .style.display =
      "none";

  document
    .getElementById("pause")
    .style.display =
      "none";

  showScreen(
    "gameOverScreen"
  );
}


/* ================= GAME OVER BUTTONS ================= */

document
  .getElementById("againBtn")
  .addEventListener(
    "click",
    startGame
  );


document
  .getElementById("gameMenuBtn")
  .addEventListener(
    "click",
    openMenu
  );


/* ================= START ================= */

showScreen(
  "loginScreen"
);

</script>

</body>
</html>
