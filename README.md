<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">

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
  touch-action:manipulation;
  -webkit-appearance:none;
  appearance:none;
}

.screen{
  position:fixed;
  inset:0;
  z-index:100;
  display:flex;
  align-items:center;
  justify-content:center;
  padding:18px;
  overflow:auto;
  background:
    radial-gradient(circle at 50% 0%,#07546a,#03151e 42%,#010306 100%);
}

.card{
  width:min(470px,100%);
  max-height:94vh;
  overflow:auto;
  padding:25px 20px;
  border-radius:28px;
  background:rgba(3,11,18,.97);
  border:1px solid #00eaff77;
  box-shadow:
    0 0 25px #00eaff22,
    0 0 90px #00eaff11;
  text-align:center;
}

.logo{
  font-size:40px;
  font-weight:1000;
  letter-spacing:-2px;
  color:#08e9ff;
  text-shadow:0 0 10px #08e9ff,0 0 30px #08e9ff;
  margin-bottom:8px;
}

.subtitle{
  color:#a8c3cc;
  line-height:1.5;
  margin-bottom:18px;
}

input{
  width:100%;
  height:56px;
  margin:8px 0;
  padding:0 16px;
  border-radius:15px;
  border:1px solid #00eaff55;
  outline:none;
  background:#f5ffbd;
  color:#050505;
  font-size:17px;
}

input:focus{
  border-color:#00eaff;
  box-shadow:0 0 15px #00eaff66;
}

.btn{
  width:100%;
  min-height:55px;
  margin:7px 0;
  border-radius:15px;
  font-size:16px;
  font-weight:900;
  cursor:pointer;
}

.btn:active{
  transform:scale(.98);
}

.primary{
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
  box-shadow:0 5px 20px #00baff33;
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

.hidden{
  display:none!important;
}

.stats{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:9px;
  margin:15px 0;
}

.stat{
  padding:13px;
  border-radius:14px;
  background:#071b25;
  border:1px solid #00eaff22;
}

.stat small{
  display:block;
  color:#7895a0;
  font-size:10px;
}

.stat strong{
  display:block;
  margin-top:5px;
  font-size:23px;
}

.grid{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:10px;
  margin:12px 0;
}

.shopItem{
  position:relative;
  padding:12px;
  border-radius:18px;
  background:#071821;
  border:1px solid #00eaff22;
  overflow:hidden;
}

.shopItem.selected{
  border-color:#00eaff;
  box-shadow:0 0 18px #00eaff33;
}

.preview{
  height:85px;
  display:flex;
  align-items:center;
  justify-content:center;
  position:relative;
}

.cube{
  width:48px;
  height:48px;
  border-radius:12px;
  box-shadow:0 0 18px currentColor,0 0 40px currentColor;
}

.glow{
  position:absolute;
  width:75px;
  height:75px;
  border-radius:50%;
  background:currentColor;
  opacity:.12;
  filter:blur(15px);
}

.shopName{
  font-weight:900;
  margin:3px;
}

.shopPrice{
  color:#ffd600;
  font-size:13px;
  margin:4px;
}

.mini{
  font-size:11px;
  color:#9ab0b9;
}

.cityPreview{
  height:90px;
  border-radius:13px;
  margin-bottom:8px;
  position:relative;
  overflow:hidden;
  background:
    linear-gradient(#061827,#02070d);
}

.building{
  position:absolute;
  bottom:0;
  width:25px;
  background:#071b27;
  border-top:1px solid #00eaff77;
}

.cityStars{
  position:absolute;
  inset:0;
  background-image:
    radial-gradient(circle,#ffffffaa 1px,transparent 2px);
  background-size:25px 25px;
}

#game{
  position:fixed;
  inset:0;
  display:none;
  width:100%;
  height:100%;
  background:#02070d;
  z-index:1;
}

#hud{
  position:fixed;
  top:10px;
  left:8px;
  right:8px;
  z-index:5;
  display:none;
  justify-content:space-between;
  gap:6px;
  pointer-events:none;
}

.hud{
  padding:8px 10px;
  border-radius:12px;
  background:#031119dd;
  border:1px solid #00eaff55;
  font-size:12px;
  font-weight:900;
}

#controls{
  position:fixed;
  left:12px;
  right:12px;
  bottom:18px;
  z-index:6;
  display:none;
  justify-content:space-between;
  pointer-events:none;
}

.control{
  width:78px;
  height:62px;
  border-radius:17px;
  background:#071b25ee;
  border:1px solid #00eaff66;
  color:white;
  font-size:28px;
  pointer-events:auto;
}

#pause{
  position:fixed;
  top:55px;
  right:10px;
  z-index:7;
  display:none;
  width:68px;
  height:40px;
  border-radius:12px;
  background:#071b25ee;
  color:white;
  border:1px solid #00eaff55;
}

.ranking{
  text-align:left;
  max-height:55vh;
  overflow:auto;
  line-height:2;
}

.rank{
  padding:4px 7px;
  border-bottom:1px solid #ffffff0d;
}

.tabs{
  display:flex;
  gap:7px;
  margin-bottom:10px;
}

.tabs button{
  flex:1;
  padding:11px 4px;
  border-radius:12px;
  background:#071b25;
  color:white;
  border:1px solid #00eaff33;
  font-weight:900;
}

.tabs button.active{
  background:#05dff5;
  color:#001018;
}

.codeBox{
  margin-top:8px;
  padding:12px;
  border-radius:15px;
  background:#071b25;
}

.note{
  color:#77939d;
  font-size:11px;
  margin-top:8px;
}
</style>
</head>

<body>

<!-- LOGIN -->
<section id="loginScreen" class="screen">
  <div class="card">
    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
      Melde dich an und speichere deinen Fortschritt.
    </div>

    <input id="loginEmail"
           type="email"
           autocomplete="email"
           placeholder="E-Mail">

    <input id="loginPassword"
           type="password"
           autocomplete="current-password"
           placeholder="Passwort">

    <button id="loginBtn" class="btn primary">
      ANMELDEN
    </button>

    <button id="registerOpen" class="btn secondary">
      ACCOUNT ERSTELLEN
    </button>

    <div id="loginMessage" class="message"></div>
  </div>
</section>


<!-- REGISTER -->
<section id="registerScreen" class="screen hidden">
  <div class="card">
    <div class="logo">ACCOUNT</div>

    <div class="subtitle">
      Erstelle deinen CUBE-RUSH-Account.
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

    <button id="registerBtn" class="btn primary">
      ACCOUNT ERSTELLEN
    </button>

    <button id="backLogin" class="btn secondary">
      ZURÜCK
    </button>

    <div id="registerMessage" class="message"></div>
  </div>
</section>


<!-- MENU -->
<section id="menuScreen" class="screen hidden">
  <div class="card">
    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
      Willkommen, <b id="menuUsername">Spieler</b>!
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

    <button id="startBtn" class="btn primary">
      ▶ SPIEL STARTEN
    </button>

    <button id="shopBtn" class="btn secondary">
      🛍️ SHOP
    </button>

    <button id="citiesBtn" class="btn secondary">
      🌆 STÄDTE
    </button>

    <button id="rankingBtn" class="btn secondary">
      🏆 RANGLISTE
    </button>

    <button id="codeBtn" class="btn secondary">
      🔐 CODE EINGEBEN
    </button>

    <button id="logoutBtn" class="btn secondary">
      ABMELDEN
    </button>
  </div>
</section>


<!-- SHOP -->
<section id="shopScreen" class="screen hidden">
  <div class="card">

    <div class="logo">🛍️ SHOP</div>

    <div class="subtitle">
      Kaufe deinen Charakter.
      Je teurer, desto mehr Münzen bekommst du.
    </div>

    <div class="tabs">
      <button id="charactersTab" class="active">
        CHARAKTERE
      </button>
      <button id="citiesTab">
        STÄDTE
      </button>
    </div>

    <div id="shopGrid" class="grid"></div>

    <button id="shopBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>


<!-- RANKING -->
<section id="rankingScreen" class="screen hidden">
  <div class="card">
    <div class="logo">🏆 RANGLISTE</div>

    <div id="rankingList" class="ranking">
      Lade Rangliste...
    </div>

    <button id="rankingBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>


<!-- CODE -->
<section id="codeScreen" class="screen hidden">
  <div class="card">
    <div class="logo">🔐 CODE</div>

    <div class="subtitle">
      Gib einen Freischaltcode ein.
    </div>

    <div class="codeBox">
      <input id="adminCode"
             type="text"
             inputmode="numeric"
             maxlength="3"
             placeholder="Code">

      <button id="redeemCode" class="btn primary">
        CODE EINLÖSEN
      </button>

      <div id="codeMessage" class="message"></div>
    </div>

    <button id="codeBack" class="btn secondary">
      ZURÜCK
    </button>
  </div>
</section>


<!-- GAME OVER -->
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

    <button id="againBtn" class="btn primary">
      🔄 NOCHMAL
    </button>

    <button id="gameMenuBtn" class="btn secondary">
      MENÜ
    </button>
  </div>
</section>


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
    <span id="hudCity">NEON CITY</span>
  </div>
</div>

<button id="pause">⏸</button>

<div id="controls">
  <button id="leftBtn" class="control">←</button>
  <button id="rightBtn" class="control">→</button>
</div>


<script>
/* =====================================================
   SUPABASE
===================================================== */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";

let authToken = "";
let currentPlayer = null;


/* =====================================================
   CHARAKTERE
===================================================== */

const characters = [
  {name:"Cube",price:0,mult:1,color:"#00eaff"},
  {name:"Neon",price:500,mult:1.1,color:"#00ff66"},
  {name:"Fire",price:1000,mult:1.2,color:"#ff1744"},
  {name:"Ocean",price:1500,mult:1.3,color:"#008cff"},
  {name:"Purple",price:2500,mult:1.4,color:"#b000ff"},
  {name:"Gold",price:4000,mult:1.5,color:"#ffd600"},
  {name:"Lava",price:6000,mult:1.6,color:"#ff5a00"},
  {name:"Ice",price:8000,mult:1.7,color:"#8df6ff"},
  {name:"Rainbow",price:10000,mult:1.8,color:"#ff4dff"},
  {name:"Toxic",price:12500,mult:2,color:"#aaff00"},
  {name:"Diamond",price:15000,mult:2.2,color:"#b9f2ff"},
  {name:"Galaxy",price:20000,mult:2.4,color:"#7c4dff"},
  {name:"Plasma",price:25000,mult:2.6,color:"#ff00aa"},
  {name:"Cyber",price:30000,mult:2.8,color:"#00ffcc"},
  {name:"Royal",price:40000,mult:3,color:"#ffcc00"},
  {name:"Shadow",price:50000,mult:3.2,color:"#8a8a8a"},
  {name:"Cosmic",price:65000,mult:3.5,color:"#8b5cff"},
  {name:"Legend",price:80000,mult:4,color:"#ff9100"},
  {name:"Infinity",price:100000,mult:4.5,color:"#ffffff"},
  {name:"CUBE GOD",price:150000,mult:5,color:"#00ffff"}
];


/* =====================================================
   STÄDTE
===================================================== */

const cities = [
  {name:"Neon City",price:0,sky:"#063d50"},
  {name:"Tokyo",price:1000,sky:"#30105c"},
  {name:"New York",price:2500,sky:"#10294f"},
  {name:"Miami",price:5000,sky:"#174f57"},
  {name:"Dubai",price:10000,sky:"#55321b"},
  {name:"Cyber City",price:25000,sky:"#25004f"}
];


/* =====================================================
   LOCAL SAVE
===================================================== */

function localData(){

  if(!currentPlayer) return {};

  const key =
    "cubeRush_"+currentPlayer.id;

  try{
    return JSON.parse(
      localStorage.getItem(key) || "{}"
    );
  }catch{
    return {};
  }
}

function saveLocal(data){

  if(!currentPlayer) return;

  const key =
    "cubeRush_"+currentPlayer.id;

  localStorage.setItem(
    key,
    JSON.stringify(data)
  );
}

function getUnlockedCharacters(){

  const d=localData();

  if(Array.isArray(d.characters))
    return d.characters;

  return [0];
}

function getUnlockedCities(){

  const d=localData();

  if(Array.isArray(d.cities))
    return d.cities;

  return [0];
}

function selectedCharacter(){

  const d=localData();

  return Number.isInteger(d.selectedCharacter)
    ? d.selectedCharacter
    : 0;
}

function selectedCity(){

  const d=localData();

  return Number.isInteger(d.selectedCity)
    ? d.selectedCity
    : 0;
}


/* =====================================================
   HILFSFUNKTIONEN
===================================================== */

function $(id){
  return document.getElementById(id);
}

function showScreen(id){

  document
    .querySelectorAll(".screen")
    .forEach(s=>{
      s.classList.add("hidden");
    });

  $(id).classList.remove("hidden");
}

function message(id,text){
  $(id).textContent=text;
}


/* =====================================================
   SUPABASE REQUEST
===================================================== */

async function supabaseRequest(path,options={}){

  const headers={
    apikey:SUPABASE_KEY,
    "Content-Type":"application/json",
    ...(authToken
      ? {Authorization:"Bearer "+authToken}
      : {}),
    ...(options.headers||{})
  };

  const response=
    await fetch(
      SUPABASE_URL+path,
      {
        ...options,
        headers
      }
    );

  const text=
    await response.text();

  let data=null;

  try{
    data=text?JSON.parse(text):null;
  }catch{
    data=null;
  }

  if(!response.ok){

    throw new Error(
      data?.msg ||
      data?.message ||
      data?.error_description ||
      text ||
      "Supabase-Fehler"
    );
  }

  return data;
}


/* =====================================================
   LOGIN
===================================================== */

$("loginBtn").onclick=async()=>{

  const email=
    $("loginEmail").value.trim();

  const password=
    $("loginPassword").value;

  if(!email||!password){
    message(
      "loginMessage",
      "Bitte E-Mail und Passwort eingeben."
    );
    return;
  }

  $("loginBtn").disabled=true;
  $("loginBtn").textContent="ANMELDUNG...";

  try{

    const data=
      await supabaseRequest(
        "/auth/v1/token?grant_type=password",
        {
          method:"POST",
          body:JSON.stringify({
            email,
            password
          })
        }
      );

    authToken=data.access_token;

    currentPlayer={
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
      "❌ "+error.message
    );

  }finally{

    $("loginBtn").disabled=false;
    $("loginBtn").textContent="ANMELDEN";
  }
};


/* =====================================================
   REGISTER
===================================================== */

$("registerOpen").onclick=()=>{
  message("loginMessage","");
  showScreen("registerScreen");
};

$("backLogin").onclick=()=>{
  message("registerMessage","");
  showScreen("loginScreen");
};

$("registerBtn").onclick=async()=>{

  const username=
    $("registerUsername").value.trim();

  const email=
    $("registerEmail").value.trim();

  const password=
    $("registerPassword").value;

  if(username.length<3){
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

  if(password.length<6){
    message(
      "registerMessage",
      "Passwort: mindestens 6 Zeichen."
    );
    return;
  }

  $("registerBtn").disabled=true;
  $("registerBtn").textContent="ERSTELLE ACCOUNT...";

  try{

    const data=
      await supabaseRequest(
        "/auth/v1/signup",
        {
          method:"POST",
          body:JSON.stringify({
            email,
            password,
            data:{username}
          })
        }
      );

    /*
      Falls Confirm Email in Supabase wirklich
      ausgeschaltet ist, kommt direkt ein Token.
    */

    if(!data.access_token){

      message(
        "registerMessage",
        "Der Account wurde erstellt. Falls Supabase noch eine Bestätigung verlangt, ist „Confirm email“ noch aktiviert."
      );

      return;
    }

    authToken=data.access_token;

    currentPlayer={
      id:data.user.id,
      username,
      coins:0,
      highscore:0
    };

    await createPlayer();

    openMenu();

  }catch(error){

    message(
      "registerMessage",
      "❌ "+error.message
    );

  }finally{

    $("registerBtn").disabled=false;
    $("registerBtn").textContent="ACCOUNT ERSTELLEN";
  }
};


/* =====================================================
   PLAYER
===================================================== */

async function createPlayer(){

  try{

    await supabaseRequest(
      "/rest/v1/Players",
      {
        method:"POST",
        headers:{
          Prefer:"resolution=ignore-duplicates"
        },
        body:JSON.stringify({
          id:currentPlayer.id,
          username:currentPlayer.username,
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

    const rows=
      await supabaseRequest(
        "/rest/v1/Players"+
        "?id=eq."+
        encodeURIComponent(currentPlayer.id)+
        "&select=*"
      );

    if(rows?.length){

      currentPlayer.username=
        rows[0].username ||
        currentPlayer.username;

      currentPlayer.coins=
        Number(rows[0].coins||0);

      currentPlayer.highscore=
        Number(rows[0].highscore||0);
    }

  }catch(error){

    console.log(
      "Player laden:",
      error.message
    );
  }
}

async function savePlayer(){

  try{

    await supabaseRequest(
      "/rest/v1/Players?id=eq."+
      encodeURIComponent(currentPlayer.id),
      {
        method:"PATCH",
        headers:{
          Prefer:"return=minimal"
        },
        body:JSON.stringify({
          username:currentPlayer.username,
          coins:currentPlayer.coins,
          highscore:currentPlayer.highscore
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


/* =====================================================
   MENU
===================================================== */

function openMenu(){

  $("menuUsername").textContent=
    currentPlayer.username;

  $("menuCoins").textContent=
    currentPlayer.coins.toLocaleString();

  $("menuHighscore").textContent=
    currentPlayer.highscore.toLocaleString();

  showScreen("menuScreen");
}

$("logoutBtn").onclick=()=>{

  authToken="";
  currentPlayer=null;

  $("loginEmail").value="";
  $("loginPassword").value="";

  showScreen("loginScreen");
};


/* =====================================================
   SHOP
===================================================== */

$("shopBtn").onclick=()=>{
  renderCharacters();
  showScreen("shopScreen");
};

$("citiesBtn").onclick=()=>{
  renderCities();
  showScreen("shopScreen");
};

$("charactersTab").onclick=()=>{
  $("charactersTab").classList.add("active");
  $("citiesTab").classList.remove("active");
  renderCharacters();
};

$("citiesTab").onclick=()=>{
  $("citiesTab").classList.add("active");
  $("charactersTab").classList.remove("active");
  renderCities();
};

$("shopBack").onclick=openMenu;


function renderCharacters(){

  const grid=$("shopGrid");

  const unlocked=
    getUnlockedCharacters();

  const selected=
    selectedCharacter();

  grid.innerHTML="";

  characters.forEach((c,index)=>{

    const owned=
      unlocked.includes(index);

    const item=
      document.createElement("div");

    item.className=
      "shopItem"+
      (selected===index?" selected":"");

    item.innerHTML=`
      <div class="preview"
           style="color:${c.color}">
        <div class="glow"></div>
        <div class="cube"
             style="background:${c.color}">
        </div>
      </div>

      <div class="shopName">
        ${c.name}
      </div>

      <div class="shopPrice">
        ${
          c.price===0
          ? "KOSTENLOS"
          : "🪙 "+c.price.toLocaleString()
        }
      </div>

      <div class="mini">
        Münzen ×${c.mult}
      </div>

      <button class="btn ${
        owned ? "secondary":"primary"
      }">
        ${
          selected===index
          ? "AUSGEWÄHLT"
          : owned
            ? "AUSWÄHLEN"
            : "KAUFEN"
        }
      </button>
    `;

    const btn=item.querySelector("button");

    btn.onclick=()=>{

      const d=localData();

      if(!Array.isArray(d.characters))
        d.characters=[0];

      if(owned){

        d.selectedCharacter=index;
        saveLocal(d);

        renderCharacters();
        return;
      }

      if(currentPlayer.coins<c.price){

        alert(
          "Du hast nicht genug Münzen!"
        );

        return;
      }

      currentPlayer.coins-=c.price;

      d.characters.push(index);
      d.selectedCharacter=index;

      saveLocal(d);

      savePlayer();

      renderCharacters();
      openMenu();
    };

    grid.appendChild(item);
  });
}


function renderCities(){

  const grid=$("shopGrid");

  const unlocked=
    getUnlockedCities();

  const selected=
    selectedCity();

  grid.innerHTML="";

  cities.forEach((c,index)=>{

    const owned=
      unlocked.includes(index);

    const item=
      document.createElement("div");

    item.className=
      "shopItem"+
      (selected===index?" selected":"");

    item.innerHTML=`
      <div class="cityPreview"
           style="background:
           linear-gradient(${c.sky},#02070d)">
        <div class="cityStars"></div>
        ${[0,1,2,3,4].map(
          n=>`
          <div class="building"
               style="
               left:${n*22+5}%;
               height:${35+n*10}px;
               ">
          </div>`
        ).join("")}
      </div>

      <div class="shopName">
        ${c.name}
      </div>

      <div class="shopPrice">
        ${
          c.price===0
          ? "KOSTENLOS"
          : "🪙 "+c.price.toLocaleString()
        }
      </div>

      <button class="btn ${
        owned ? "secondary":"primary"
      }">
        ${
          selected===index
          ? "AUSGEWÄHLT"
          : owned
            ? "AUSWÄHLEN"
            : "KAUFEN"
        }
      </button>
    `;

    const btn=item.querySelector("button");

    btn.onclick=()=>{

      const d=localData();

      if(!Array.isArray(d.cities))
        d.cities=[0];

      if(owned){

        d.selectedCity=index;
        saveLocal(d);

        renderCities();
        return;
      }

      if(currentPlayer.coins<c.price){

        alert(
          "Du hast nicht genug Münzen!"
        );

        return;
      }

      currentPlayer.coins-=c.price;

      d.cities.push(index);
      d.selectedCity=index;

      saveLocal(d);

      savePlayer();

      renderCities();
      openMenu();
    };

    grid.appendChild(item);
  });
}


/* =====================================================
   ADMIN-CODES
===================================================== */

$("codeBtn").onclick=()=>{
  $("adminCode").value="";
  $("codeMessage").textContent="";
  showScreen("codeScreen");
};

$("codeBack").onclick=openMenu;

$("redeemCode").onclick=async()=>{

  const code=
    $("adminCode").value.trim();

  const d=localData();

  if(code==="110"){

    currentPlayer.coins=1000000;
    currentPlayer.highscore=1000000;

    d.characters=
      characters.map((_,i)=>i);

    d.cities=
      cities.map((_,i)=>i);

    d.selectedCharacter=0;
    d.selectedCity=0;

    saveLocal(d);

    await savePlayer();

    message(
      "codeMessage",
      "✅ Code 110 aktiviert! Alles freigeschaltet."
    );

  }else if(code==="112"){

    currentPlayer.coins=100000;

    d.characters=
      [0,1,2,3,4];

    d.cities=
      [0,1,2];

    d.selectedCharacter=0;
    d.selectedCity=0;

    saveLocal(d);

    await savePlayer();

    message(
      "codeMessage",
      "✅ Code 112 aktiviert!"
    );

  }else{

    message(
      "codeMessage",
      "❌ Ungültiger Code."
    );

    return;
  }

  setTimeout(
    openMenu,
    1200
  );
};


/* =====================================================
   RANGLISTE
===================================================== */

$("rankingBtn").onclick=async()=>{

  showScreen("rankingScreen");

  $("rankingList").textContent=
    "Lade Rangliste...";

  try{

    const rows=
      await supabaseRequest(
        "/rest/v1/Players"+
        "?select=username,highscore"+
        "&order=highscore.desc"+
        "&limit=50"
      );

    if(!rows?.length){

      $("rankingList").textContent=
        "Noch keine Spieler.";

      return;
    }

    $("rankingList").innerHTML=
      rows.map(
        (r,i)=>`
          <div class="rank">
            <b>${i+1}.</b>
            ${escapeHTML(r.username)}
            — ${Number(r.highscore||0)
              .toLocaleString()}
          </div>
        `
      ).join("");

  }catch(error){

    $("rankingList").textContent=
      "❌ "+error.message;
  }
};

$("rankingBack").onclick=openMenu;

function escapeHTML(text){

  return String(text)
    .replaceAll("&","&amp;")
    .replaceAll("<","&lt;")
    .replaceAll(">","&gt;")
    .replaceAll('"',"&quot;");
}


/* =====================================================
   GAME
===================================================== */

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
let roundCoins=0;

let speed=330;

let objects=[];

let carTimer=1;

let coinTimer=2;

let lastTime=performance.now();


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


/* =====================================================
   START
===================================================== */

$("startBtn").onclick=startGame;

function startGame(){

  document
    .querySelectorAll(".screen")
    .forEach(s=>{
      s.classList.add("hidden");
    });

  canvas.style.display="block";
  $("hud").style.display="flex";
  $("controls").style.display="flex";
  $("pause").style.display="block";

  running=true;
  paused=false;

  lane=0;

  score=0;
  roundCoins=0;

  speed=330;

  objects=[];

  carTimer=1;
  coinTimer=2;

  lastTime=performance.now();

  $("hudCity").textContent=
    cities[selectedCity()].name;
}


/* =====================================================
   BACKGROUND
===================================================== */

function drawBackground(){

  const city=
    cities[selectedCity()];

  const gradient=
    ctx.createLinearGradient(0,0,0,H);

  gradient.addColorStop(
    0,
    city.sky
  );

  gradient.addColorStop(
    1,
    "#02050a"
  );

  ctx.fillStyle=gradient;
  ctx.fillRect(0,0,W,H);


  /* STADT */

  for(let i=0;i<26;i++){

    const width=
      20+(i%5)*11;

    const height=
      80+((i*47)%230);

    const x=
      i*(W/25);

    ctx.fillStyle="#071820";

    ctx.fillRect(
      x,
      H-height-100,
      width,
      height
    );

    ctx.fillStyle="#00eaff66";

    for(
      let y=H-height-80;
      y<H-115;
      y+=23
    ){

      ctx.fillRect(
        x+5,
        y,
        4,
        7
      );
    }
  }


  /* ROAD */

  const rw=roadWidth();
  const rl=roadLeft();

  ctx.fillStyle="#111820";

  ctx.fillRect(
    rl,
    0,
    rw,
    H
  );

  ctx.fillStyle="#00eaff";

  ctx.fillRect(rl,0,3,H);
  ctx.fillRect(rl+rw-3,0,3,H);


  /* LANES */

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


/* =====================================================
   OBJECTS
===================================================== */

function spawnCar(){

  const possible=
    [-1,0,1].filter(n=>
      !objects.some(o=>
        o.type==="car" &&
        o.lane===n &&
        o.y<220
      )
    );

  if(!possible.length)return;

  const selected=
    possible[
      Math.floor(
        Math.random()*possible.length
      )
    ];

  objects.push({
    type:"car",
    lane:selected,
    y:-120,
    color:[
      "#ff1744",
      "#008cff",
      "#ff9800",
      "#ffffff",
      "#a000ff"
    ][
      Math.floor(Math.random()*5)
    ]
  });
}


function spawnCoins(){

  const amount=
    3+Math.floor(Math.random()*4);

  const selectedLane=
    [-1,0,1][
      Math.floor(Math.random()*3)
    ];

  for(let i=0;i<amount;i++){

    objects.push({
      type:"coin",
      lane:selectedLane,
      y:-40-i*65
    });
  }
}


function drawObjects(){

  for(const object of objects){

    const x=laneX(object.lane);

    if(object.type==="coin"){

      ctx.save();

      ctx.shadowColor="#ffd600";
      ctx.shadowBlur=22;

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

      ctx.fillStyle="#fff7a0";

      ctx.beginPath();

      ctx.arc(
        x-4,
        object.y-4,
        4,
        0,
        Math.PI*2
      );

      ctx.fill();

      ctx.restore();

    }else{

      ctx.save();

      ctx.translate(x,object.y);

      ctx.shadowColor=object.color;
      ctx.shadowBlur=20;

      ctx.fillStyle=object.color;

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
   PLAYER
===================================================== */

function drawPlayer(){

  const character=
    characters[selectedCharacter()];

  const x=laneX(lane);
  const y=H*.76;

  ctx.save();

  ctx.translate(x,y);

  ctx.shadowColor=
    character.color;

  ctx.shadowBlur=
    28;

  ctx.fillStyle=
    character.color;

  ctx.beginPath();

  ctx.roundRect(
    -27,
    -27,
    54,
    54,
    12
  );

  ctx.fill();

  /*
    Mehr Glitzer für teurere Charaktere
  */

  const glitter=
    Math.min(
      20,
      Math.floor(character.mult*3)
    );

  ctx.shadowBlur=0;

  for(let i=0;i<glitter;i++){

    const a=
      (performance.now()/400+i);

    const gx=
      Math.cos(a)*(25+i%5*3);

    const gy=
      Math.sin(a)*(25+i%4*3);

    ctx.fillStyle="#ffffffcc";

    ctx.fillRect(
      gx,
      gy,
      3,
      3
    );
  }

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
   MOVEMENT
===================================================== */

function moveLeft(){

  if(!running||paused)return;

  lane=Math.max(-1,lane-1);
}

function moveRight(){

  if(!running||paused)return;

  lane=Math.min(1,lane+1);
}

$("leftBtn").onclick=moveLeft;
$("rightBtn").onclick=moveRight;


document.addEventListener(
  "keydown",
  e=>{

    if(e.key==="ArrowLeft")
      moveLeft();

    if(e.key==="ArrowRight")
      moveRight();
  }
);


/* =====================================================
   SWIPE
===================================================== */

let startX=0;

canvas.addEventListener(
  "touchstart",
  e=>{
    startX=
      e.changedTouches[0].clientX;
  },
  {passive:true}
);

canvas.addEventListener(
  "touchend",
  e=>{

    const endX=
      e.changedTouches[0].clientX;

    const dx=endX-startX;

    if(dx>35)
      moveRight();

    if(dx<-35)
      moveLeft();
  },
  {passive:true}
);


/* =====================================================
   UPDATE
===================================================== */

function update(dt){

  if(!running||paused)return;

  const seconds=dt/1000;

  score+=seconds*10;

  speed=
    Math.min(
      700,
      speed+seconds*3
    );


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
      1.15+Math.random()*.7;
  }


  for(
    let i=objects.length-1;
    i>=0;
    i--
  ){

    const object=objects[i];

    object.y+=speed*seconds;

    if(object.y>H+120){

      objects.splice(i,1);
      continue;
    }

    const playerY=H*.76;


    /* COIN */

    if(
      object.type==="coin" &&
      object.lane===lane &&
      Math.abs(object.y-playerY)<55
    ){

      const character=
        characters[selectedCharacter()];

      const multiplier=
        character.mult;

      roundCoins+=multiplier;

      objects.splice(i,1);

      continue;
    }


    /* CAR */

    if(
      object.type==="car" &&
      object.lane===lane &&
      Math.abs(object.y-playerY)<65
    ){

      endGame();

      return;
    }
  }


  $("hudCoins").textContent=
    Math.floor(
      currentPlayer.coins+
      roundCoins
    ).toLocaleString();

  $("hudScore").textContent=
    Math.floor(score).toLocaleString();
}


/* =====================================================
   GAME LOOP
===================================================== */

function gameLoop(time){

  const dt=
    Math.min(
      40,
      time-lastTime
    );

  lastTime=time;

  if(canvas.style.display!=="none"){

    drawBackground();
    drawObjects();
    drawPlayer();
    update(dt);
  }

  requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);


/* =====================================================
   PAUSE
===================================================== */

$("pause").onclick=()=>{

  paused=!paused;

  $("pause").textContent=
    paused ? "▶" : "⏸";
};


/* =====================================================
   GAME OVER
===================================================== */

async function endGame(){

  if(!running)return;

  running=false;

  const final=
    Math.floor(score);

  currentPlayer.coins+=
    Math.floor(roundCoins);

  if(final>currentPlayer.highscore){

    currentPlayer.highscore=final;
  }

  await savePlayer();

  $("finalScore").textContent=
    final.toLocaleString();

  $("finalCoins").textContent=
    Math.floor(roundCoins)
      .toLocaleString();

  canvas.style.display="none";
  $("hud").style.display="none";
  $("controls").style.display="none";
  $("pause").style.display="none";

  showScreen("gameOverScreen");
}


/* =====================================================
   GAME OVER BUTTONS
===================================================== */

$("againBtn").onclick=startGame;

$("gameMenuBtn").onclick=openMenu;


/* =====================================================
   START
===================================================== */

showScreen("loginScreen");

</script>

</body>
</html>
