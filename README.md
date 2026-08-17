<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

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
  background:#02050a;
  color:white;
}

button,input{
  font:inherit;
}

button{
  cursor:pointer;
  touch-action:manipulation;
  -webkit-user-select:none;
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
    radial-gradient(circle at top,#06394b,#02050a 65%);
}

.hidden{
  display:none!important;
}

.box{
  width:min(420px,100%);
  padding:25px 20px;
  border-radius:25px;
  background:rgba(5,17,26,.97);
  border:1px solid #00d9ff55;
  box-shadow:0 0 40px #00d9ff18;
  text-align:center;
}

.logo{
  font-size:40px;
  font-weight:1000;
  color:#00eaff;
  text-shadow:0 0 20px #00eaff;
  margin-bottom:8px;
}

.text{
  color:#91b7c3;
  font-size:14px;
  line-height:1.5;
  margin-bottom:18px;
}

input{
  display:block;
  width:100%;
  height:54px;
  margin:9px 0;
  padding:0 15px;
  border-radius:14px;
  border:1px solid #00d9ff45;
  outline:none;
  background:#07141d;
  color:white;
}

input:focus{
  border-color:#00eaff;
}

button{
  display:block;
  width:100%;
  min-height:52px;
  margin-top:9px;
  border-radius:14px;
  border:0;
}

.mainBtn{
  background:linear-gradient(135deg,#00eaff,#008cff);
  color:#001018;
  font-weight:900;
}

.secondBtn{
  background:#0a1b26;
  color:white;
  border:1px solid #00d9ff35;
}

.message{
  min-height:22px;
  margin-top:10px;
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
  background:#07141d;
  padding:15px 8px;
  border-radius:15px;
}

.small{
  color:#759aa6;
  font-size:12px;
}

.big{
  margin-top:5px;
  font-size:22px;
  font-weight:900;
}

#game{
  display:none;
  position:fixed;
  inset:0;
  width:100%;
  height:100%;
  background:#02050a;
}

#hud{
  display:none;
  position:fixed;
  top:10px;
  left:10px;
  right:10px;
  z-index:20;
  justify-content:space-between;
  pointer-events:none;
}

.hud{
  background:#06151ecc;
  border:1px solid #00d9ff45;
  border-radius:12px;
  padding:9px 12px;
  font-weight:900;
}

.controls{
  display:none;
  position:fixed;
  bottom:18px;
  left:15px;
  right:15px;
  z-index:30;
  justify-content:space-between;
  pointer-events:none;
}

.controlGroup{
  display:flex;
  gap:8px;
  pointer-events:auto;
}

.control{
  width:65px;
  height:60px;
  border-radius:18px;
  background:#071923e8;
  border:1px solid #00d9ff55;
  color:white;
  font-size:24px;
}

.jump{
  width:110px;
  background:linear-gradient(135deg,#00eaff,#008cff);
  color:#001018;
  font-weight:900;
}

#pause{
  display:none;
  position:fixed;
  top:60px;
  right:12px;
  z-index:40;
  width:80px;
  height:42px;
  background:#071923;
  color:white;
  border:1px solid #00d9ff55;
}
</style>
</head>

<body>

<!-- LOGIN -->
<div id="loginScreen" class="screen">
  <div class="box">

    <div class="logo">CUBE RUSH</div>

    <div class="text">
      Melde dich an und speichere deinen Highscore
      in der Rangliste.
    </div>

    <input id="loginEmail" type="email" placeholder="E-Mail">
    <input id="loginPassword" type="password" placeholder="Passwort">

    <button id="loginButton" class="mainBtn">
      ANMELDEN
    </button>

    <button id="registerOpen" class="secondBtn">
      ACCOUNT ERSTELLEN
    </button>

    <div id="loginMessage" class="message"></div>

  </div>
</div>


<!-- REGISTER -->
<div id="registerScreen" class="screen hidden">
  <div class="box">

    <div class="logo">CUBE RUSH</div>

    <div class="text">
      Erstelle deinen Account mit eigenem
      Benutzernamen.
    </div>

    <input id="registerUsername"
           type="text"
           maxlength="20"
           placeholder="Benutzername">

    <input id="registerEmail"
           type="email"
           placeholder="E-Mail">

    <input id="registerPassword"
           type="password"
           placeholder="Passwort">

    <button id="registerButton" class="mainBtn">
      ACCOUNT ERSTELLEN
    </button>

    <button id="registerBack" class="secondBtn">
      ZURÜCK
    </button>

    <div id="registerMessage" class="message"></div>

  </div>
</div>


<!-- MENU -->
<div id="menuScreen" class="screen hidden">
  <div class="box">

    <div class="logo">CUBE RUSH</div>

    <div class="text">
      Willkommen <b id="menuUsername">Spieler</b>
    </div>

    <div class="stats">

      <div class="stat">
        <div class="small">COINS</div>
        <div id="menuCoins" class="big">0</div>
      </div>

      <div class="stat">
        <div class="small">HIGHSCORE</div>
        <div id="menuScore" class="big">0</div>
      </div>

    </div>

    <button id="startButton" class="mainBtn">
      ▶ SPIEL STARTEN
    </button>

    <button id="rankingButton" class="secondBtn">
      🏆 RANGLISTE
    </button>

    <button id="logoutButton" class="secondBtn">
      ABMELDEN
    </button>

  </div>
</div>


<!-- RANKING -->
<div id="rankingScreen" class="screen hidden">
  <div class="box">

    <div class="logo">RANGLISTE</div>

    <div id="rankingContent" class="text">
      Lade Rangliste...
    </div>

    <button id="rankingBack" class="secondBtn">
      ZURÜCK
    </button>

  </div>
</div>


<!-- GAME OVER -->
<div id="gameOverScreen" class="screen hidden">
  <div class="box">

    <div class="logo">CRASH!</div>

    <div class="stats">

      <div class="stat">
        <div class="small">SCORE</div>
        <div id="finalScore" class="big">0</div>
      </div>

      <div class="stat">
        <div class="small">COINS</div>
        <div id="finalCoins" class="big">0</div>
      </div>

    </div>

    <button id="againButton" class="mainBtn">
      🔄 NOCHMAL
    </button>

    <button id="gameMenuButton" class="secondBtn">
      MENÜ
    </button>

  </div>
</div>


<!-- GAME -->
<canvas id="game"></canvas>

<div id="hud">

  <div class="hud">
    🪙 <span id="hudCoins">0</span>
  </div>

  <div class="hud">
    🏆 <span id="hudScore">0</span>
  </div>

  <div class="hud">
    🌆 Neon City
  </div>

</div>

<button id="pause">⏸</button>

<div id="controls" class="controls">

  <div class="controlGroup">
    <button id="left" class="control">←</button>
    <button id="right" class="control">→</button>
  </div>

  <button id="jump" class="control jump">
    SPRINGEN
  </button>

</div>


<script>

/* =====================================================
   1. SUPABASE
===================================================== */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/*
   NUR HIER DEINEN NEUEN
   PUBLISHABLE / ANON KEY EINSETZEN.
*/

const SUPABASE_ANON_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfi


/* =====================================================
   2. SPIELER
===================================================== */

let token = "";

let player = {
  id:"",
  username:"",
  coins:0,
  highscore:0
};


/* =====================================================
   3. HILFSFUNKTIONEN
===================================================== */

function show(id){

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


async function supabase(path,options={}){

  if(
    !SUPABASE_ANON_KEY ||
    SUPABASE_ANON_KEY ===
    "DEIN_ANON_KEY_HIER"
  ){

    throw new Error(
      "Supabase-Key fehlt."
    );
  }


  const headers = {

    apikey:
      SUPABASE_ANON_KEY,

    "Content-Type":
      "application/json",

    ...(token
      ? {
          Authorization:
          "Bearer " + token
        }
      : {}),

    ...(options.headers || {})

  };


  const response =
    await fetch(
      SUPABASE_URL + path,
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
      data?.message ||
      data?.msg ||
      data?.error_description ||
      text ||
      "Fehler"
    );
  }


  return data;
}


/* =====================================================
   4. REGISTRIEREN
===================================================== */

document
  .getElementById("registerButton")
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

    message(
      "registerMessage",
      "Benutzername mindestens 3 Zeichen."
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
      "Passwort mindestens 6 Zeichen."
    );

    return;
  }


  message(
    "registerMessage",
    "Account wird erstellt..."
  );


  try{

    const data =
      await supabase(
        "/auth/v1/signup",
        {
          method:"POST",

          body:JSON.stringify({

            email,
            password,

            data:{
              username
            }

          })
        }
      );


    if(!data.user){

      throw new Error(
        "Account konnte nicht erstellt werden."
      );
    }


    player.id =
      data.user.id;

    player.username =
      username;


    /*
      Falls E-Mail-Bestätigung
      aktiviert ist:
    */

    if(!data.access_token){

      message(
        "registerMessage",
        "Account erstellt. Bestätige deine E-Mail und melde dich danach an."
      );

      return;
    }


    token =
      data.access_token;


    await createPlayer();


    loadLocal();

    updateMenu();

    show("menuScreen");

  }
  catch(error){

    message(
      "registerMessage",
      "❌ " + error.message
    );
  }
}


/* =====================================================
   5. PLAYER IN PLAYERS
===================================================== */

async function createPlayer(){

  try{

    await supabase(
      "/rest/v1/Players",
      {
        method:"POST",

        headers:{
          Prefer:
          "resolution=ignore-duplicates"
        },

        body:JSON.stringify({

          id:player.id,

          username:
            player.username,

          highscore:0

        })
      }
    );

  }
  catch(error){

    console.log(
      "Players:",
      error.message
    );
  }
}


/* =====================================================
   6. LOGIN
===================================================== */

document
  .getElementById("loginButton")
  .addEventListener(
    "click",
    login
  );


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

    message(
      "loginMessage",
      "E-Mail und Passwort eingeben."
    );

    return;
  }


  message(
    "loginMessage",
    "Anmeldung..."
  );


  try{

    const data =
      await supabase(
        "/auth/v1/token?grant_type=password",
        {
          method:"POST",

          body:JSON.stringify({

            email,
            password

          })
        }
      );


    token =
      data.access_token;


    player.id =
      data.user.id;


    player.username =
      data.user.user_metadata?.username ||
      email.split("@")[0];


    await loadPlayer();

    loadLocal();

    updateMenu();

    show("menuScreen");

  }
  catch(error){

    message(
      "loginMessage",
      "❌ " + error.message
    );
  }
}


/* =====================================================
   7. PLAYER LADEN
===================================================== */

async function loadPlayer(){

  try{

    const rows =
      await supabase(
        "/rest/v1/Players" +
        "?id=eq." +
        encodeURIComponent(
          player.id
        ) +
        "&select=*"
      );


    if(rows.length){

      player.username =
        rows[0].username ||
        player.username;

      player.highscore =
        Number(
          rows[0].highscore || 0
        );
    }

  }
  catch(error){

    console.log(
      error.message
    );
  }
}


/* =====================================================
   8. HIGHSCORE SPEICHERN
===================================================== */

async function saveHighscore(){

  try{

    await supabase(
      "/rest/v1/Players?id=eq." +
      encodeURIComponent(
        player.id
      ),
      {
        method:"PATCH",

        headers:{
          Prefer:
          "return=minimal"
        },

        body:JSON.stringify({

          username:
            player.username,

          highscore:
            player.highscore

        })
      }
    );

  }
  catch(error){

    console.log(
      "Highscore konnte nicht gespeichert werden:",
      error.message
    );
  }
}


/* =====================================================
   9. RANGLISTE
===================================================== */

document
  .getElementById("rankingButton")
  .addEventListener(
    "click",
    ranking
  );


async function ranking(){

  show("rankingScreen");


  const box =
    document
      .getElementById(
        "rankingContent"
      );


  box.textContent =
    "Lade Rangliste...";


  try{

    const rows =
      await supabase(
        "/rest/v1/Players" +
        "?select=username,highscore" +
        "&order=highscore.desc" +
        "&limit=50"
      );


    if(!rows.length){

      box.textContent =
        "Noch keine Spieler.";

      return;
    }


    box.innerHTML =
      rows.map(
        (x,i)=>
        `${i+1}. ${escapeHTML(
          x.username
        )} — ${Number(
          x.highscore || 0
        ).toLocaleString()}`
      )
      .join("<br><br>");

  }
  catch(error){

    box.textContent =
      "❌ " + error.message;
  }
}


function escapeHTML(value){

  return String(value)
    .replaceAll("&","&amp;")
    .replaceAll("<","&lt;")
    .replaceAll(">","&gt;");
}


/* =====================================================
   10. NAVIGATION
===================================================== */

document
  .getElementById("registerOpen")
  .addEventListener(
    "click",
    ()=>{
      show("registerScreen");
    }
  );


document
  .getElementById("registerBack")
  .addEventListener(
    "click",
    ()=>{
      show("loginScreen");
    }
  );


document
  .getElementById("rankingBack")
  .addEventListener(
    "click",
    ()=>{
      show("menuScreen");
    }
  );


document
  .getElementById("logoutButton")
  .addEventListener(
    "click",
    ()=>{
      token="";
      show("loginScreen");
    }
  );


/* =====================================================
   11. LOCAL DATEN
===================================================== */

function loadLocal(){

  player.coins =
    Number(
      localStorage.getItem(
        "cubeCoins"
      ) || 0
    );
}


function saveLocal(){

  localStorage.setItem(
    "cubeCoins",
    player.coins
  );
}


function updateMenu(){

  document
    .getElementById(
      "menuUsername"
    )
    .textContent =
      player.username ||
      "Spieler";


  document
    .getElementById(
      "menuCoins"
    )
    .textContent =
      player.coins.toLocaleString();


  document
    .getElementById(
      "menuScore"
    )
    .textContent =
      player.highscore.toLocaleString();
}


/* =====================================================
   12. GAME
===================================================== */

const canvas =
  document.getElementById(
    "game"
  );

const ctx =
  canvas.getContext("2d");


let playing=false;
let paused=false;

let lane=0;

let score=0;
let roundCoins=0;

let playerY=0;
let jumpSpeed=0;

let speed=300;

let objects=[];

let coinTimer=1;
let carTimer=1;

let lastTime=0;


function resize(){

  canvas.width =
    innerWidth;

  canvas.height =
    innerHeight;
}


addEventListener(
  "resize",
  resize
);

resize();


function laneX(l){

  const width =
    Math.min(
      canvas.width*.86,
      650
    );


  const left =
    (canvas.width-width)/2;


  const laneWidth =
    width/3;


  return (
    left +
    laneWidth*(l+1) +
    laneWidth/2
  );
}


/* =====================================================
   13. START
===================================================== */

document
  .getElementById("startButton")
  .addEventListener(
    "click",
    startGame
  );


function startGame(){

  document
    .querySelectorAll(".screen")
    .forEach(
      x =>
      x.classList.add("hidden")
    );


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


  playing=true;
  paused=false;

  lane=0;

  score=0;
  roundCoins=0;

  playerY=0;
  jumpSpeed=0;

  speed=300;

  objects=[];

  coinTimer=1.5;
  carTimer=1;

  lastTime=
    performance.now();

}


/* =====================================================
   14. BACKGROUND
===================================================== */

function background(){

  const gradient =
    ctx.createLinearGradient(
      0,0,
      0,canvas.height
    );


  gradient.addColorStop(
    0,
    "#052333"
  );


  gradient.addColorStop(
    1,
    "#02050a"
  );


  ctx.fillStyle =
    gradient;


  ctx.fillRect(
    0,
    0,
    canvas.width,
    canvas.height
  );


  /*
    Stadt
  */

  for(
    let i=0;
    i<16;
    i++
  ){

    const w =
      35+(i%3)*18;

    const h =
      100+(i*47)%200;

    ctx.fillStyle =
      "#07151f";


    ctx.fillRect(
      i*canvas.width/16,
      canvas.height-h-140,
      w,
      h
    );
  }


  /*
    Straße
  */

  const width =
    Math.min(
      canvas.width*.86,
      650
    );


  const left =
    (canvas.width-width)/2;


  ctx.fillStyle =
    "#11181e";


  ctx.fillRect(
    left,
    0,
    width,
    canvas.height
  );


  ctx.fillStyle =
    "#00eaff";


  ctx.fillRect(
    left,
    0,
    3,
    canvas.height
  );


  ctx.fillRect(
    left+width-3,
    0,
    3,
    canvas.height
  );


  const lw =
    width/3;


  const offset =
    (score*8)%90;


  ctx.fillStyle =
    "#ffffff99";


  for(
    let y=-90+offset;
    y<canvas.height;
    y+=90
  ){

    ctx.fillRect(
      left+lw-2,
      y,
      4,
      45
    );


    ctx.fillRect(
      left+lw*2-2,
      y,
      4,
      45
    );
  }
}


/* =====================================================
   15. AUTOS
===================================================== */

function car(){

  /*
    Die drei Spuren bleiben frei bewegbar.
    Es werden niemals alle Spuren
    gleichzeitig blockiert.
  */

  const possible =
    [-1,0,1].filter(
      l =>
      !objects.some(
        o =>
        o.type==="car" &&
        o.lane===l &&
        o.y<250
      )
    );


  if(!possible.length)
    return;


  const l =
    possible[
      Math.floor(
        Math.random()*
        possible.length
      )
    ];


  objects.push({

    type:"car",

    lane:l,

    y:-100,

    color:
      [
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


/* =====================================================
   16. MÜNZEN
===================================================== */

function coins(){

  /*
    NEON CITY:
    Immer zwischen 3 und 6 Münzen.
  */

  const amount =
    3+
    Math.floor(
      Math.random()*4
    );


  const l =
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

      lane:l,

      y:-50-i*60

    });
  }
}


/* =====================================================
   17. ZEICHNEN
===================================================== */

function drawObjects(){

  objects.forEach(
    o=>{

      const x =
        laneX(o.lane);


      if(o.type==="coin"){

        ctx.save();

        ctx.fillStyle =
          "#ffd600";

        ctx.shadowColor =
          "#ffd600";

        ctx.shadowBlur =
          20;


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

      }
      else{

        ctx.save();

        ctx.translate(
          x,
          o.y
        );


        ctx.shadowColor =
          o.color;

        ctx.shadowBlur =
          18;


        ctx.fillStyle =
          o.color;


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
          "#17303d";


        ctx.fillRect(
          -19,
          -28,
          38,
          24
        );


        ctx.restore();
      }

    }
  );
}


function drawPlayer(){

  const x =
    laneX(lane);


  const y =
    canvas.height*.76-
    playerY;


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


  ctx.restore();
}


/* =====================================================
   18. SPRINGEN
===================================================== */

function jump(){

  if(
    !playing ||
    paused
  )
    return;


  if(playerY<=1){

    jumpSpeed=820;
  }
}


document
  .getElementById("jump")
  .addEventListener(
    "click",
    jump
  );


/* =====================================================
   19. LINKS / RECHTS
===================================================== */

function left(){

  if(!playing || paused)
    return;

  lane=
    Math.max(
      -1,
      lane-1
    );
}


function right(){

  if(!playing || paused)
    return;

  lane=
    Math.min(
      1,
      lane+1
    );
}


document
  .getElementById("left")
  .addEventListener(
    "click",
    left
  );


document
  .getElementById("right")
  .addEventListener(
    "click",
    right
  );


/* =====================================================
   20. UPDATE
===================================================== */

function update(dt){

  if(!playing || paused)
    return;


  const s =
    dt/1000;


  score +=
    s*10;


  speed =
    Math.min(
      700,
      speed+s*3
    );


  /*
    Sprung
  */

  jumpSpeed -=
    1900*s;


  playerY +=
    jumpSpeed*s;


  if(playerY<0){

    playerY=0;
    jumpSpeed=0;
  }


  /*
    Münzen
  */

  coinTimer -= s;


  if(coinTimer<=0){

    coins();

    coinTimer =
      3.5+
      Math.random()*1.5;
  }


  /*
    Autos
  */

  carTimer -= s;


  if(carTimer<=0){

    car();

    carTimer =
      1.3+
      Math.random()*.8;
  }


  /*
    Objekte
  */

  for(
    let i=objects.length-1;
    i>=0;
    i--
  ){

    const o=
      objects[i];


    o.y +=
      speed*s;


    if(
      o.y>
      canvas.height+100
    ){

      objects.splice(i,1);

      continue;
    }


    /*
      Münze
    */

    if(
      o.type==="coin" &&
      o.lane===lane &&
      Math.abs(
        o.y-
        (
          canvas.height*.76-
          playerY
        )
      )<50
    ){

      roundCoins++;

      objects.splice(i,1);

      continue;
    }


    /*
      Auto
    */

    if(
      o.type==="car" &&
      o.lane===lane &&
      Math.abs(
        o.y-
        (
          canvas.height*.76-
          playerY
        )
      )<65
    ){

      /*
        Wenn hoch genug gesprungen:
        Auto überspringen.
      */

      if(playerY<85){

        endGame();

        return;
      }
    }
  }


  document
    .getElementById("hudCoins")
    .textContent =
      (
        player.coins+
        roundCoins
      ).toLocaleString();


  document
    .getElementById("hudScore")
    .textContent =
      Math.floor(
        score
      ).toLocaleString();
}


/* =====================================================
   21. LOOP
===================================================== */

function loop(time){

  const dt =
    Math.min(
      40,
      time-lastTime
    );


  lastTime=time;


  if(
    canvas.style.display !==
    "none"
  ){

    background();

    drawObjects();

    drawPlayer();

    update(dt);
  }


  requestAnimationFrame(
    loop
  );
}


requestAnimationFrame(
  loop
);


/* =====================================================
   22. TASTATUR
===================================================== */

document.addEventListener(
  "keydown",
  e=>{

    if(e.key==="ArrowLeft")
      left();

    if(e.key==="ArrowRight")
      right();

    if(
      e.key==="ArrowUp" ||
      e.key===" "
    ){

      e.preventDefault();

      jump();
    }
  }
);


/* =====================================================
   23. TOUCH SWIPE
===================================================== */

let startX=0;
let startY=0;


canvas.addEventListener(
  "touchstart",
  e=>{

    const t=
      e.changedTouches[0];

    startX=t.clientX;
    startY=t.clientY;

  },
  {passive:true}
);


canvas.addEventListener(
  "touchend",
  e=>{

    const t=
      e.changedTouches[0];


    const dx=
      t.clientX-startX;

    const dy=
      t.clientY-startY;


    if(
      Math.abs(dx)>
      Math.abs(dy)
    ){

      if(dx>30)
        right();

      if(dx<-30)
        left();

    }
    else if(
      dy<-30
    ){

      jump();
    }

  },
  {passive:true}
);


/* =====================================================
   24. PAUSE
===================================================== */

document
  .getElementById("pause")
  .addEventListener(
    "click",
    ()=>{
      paused=true;
    }
  );


/* =====================================================
   25. GAME OVER
===================================================== */

async function endGame(){

  playing=false;


  player.coins +=
    roundCoins;


  const final =
    Math.floor(score);


  if(
    final>
    player.highscore
  ){

    player.highscore=
      final;

    await saveHighscore();
  }


  saveLocal();


  document
    .getElementById(
      "finalScore"
    )
    .textContent =
      final.toLocaleString();


  document
    .getElementById(
      "finalCoins"
    )
    .textContent =
      roundCoins.toLocaleString();


  canvas.style.display=
    "none";


  document
    .getElementById(
      "hud"
    )
    .style.display=
    "none";


  document
    .getElementById(
      "controls"
    )
    .style.display=
    "none";


  document
    .getElementById(
      "pause"
    )
    .style.display=
    "none";


  show(
    "gameOverScreen"
  );
}


/* =====================================================
   26. NOCHMAL / MENÜ
===================================================== */

document
  .getElementById("againButton")
  .addEventListener(
    "click",
    startGame
  );


document
  .getElementById("gameMenuButton")
  .addEventListener(
    "click",
    ()=>{
      updateMenu();
      show("menuScreen");
    }
  );


/* =====================================================
   27. START
===================================================== */

loadLocal();

show("loginScreen");

</script>

</body>
</html>
