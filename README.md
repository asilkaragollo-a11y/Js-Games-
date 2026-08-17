<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
body{margin:0;overflow:hidden;background:#02040a;color:#fff;font-family:Arial,sans-serif}
canvas{position:fixed;inset:0;width:100%;height:100%}

.screen{
 position:fixed;inset:0;z-index:20;
 display:flex;flex-direction:column;align-items:center;
 justify-content:center;padding:20px;
 background:linear-gradient(160deg,#061a30,#02040a);
 overflow:auto
}
.hidden{display:none!important}

.logo{
 font-size:46px;font-weight:900;color:#00eaff;
 text-shadow:0 0 30px #00eaff;
 margin-bottom:15px;text-align:center
}

.subtitle{color:#9bd9e5;margin-bottom:15px}

input{
 width:330px;max-width:95%;
 padding:16px;margin:6px;
 border:1px solid #00eaff;
 border-radius:13px;
 background:#071522;color:white;
 font-size:16px;
 outline:none
}

input:focus{
 box-shadow:0 0 15px #00eaff66
}

button{
 padding:14px 25px;
 margin:6px;
 border:0;
 border-radius:13px;
 background:#00eaff;
 color:#001018;
 font-size:15px;
 font-weight:bold
}

button:active{transform:scale(.96)}

.message{
 width:330px;
 max-width:95%;
 min-height:28px;
 text-align:center;
 color:#9eefff
}

.hud{
 position:fixed;
 z-index:10;
 top:10px;
 left:8px;
 right:8px;
 display:none;
 justify-content:space-between;
 gap:5px
}

.hudBox{
 background:#061522e8;
 border:1px solid #00eaff55;
 border-radius:10px;
 padding:9px 11px;
 font-weight:bold
}

#pauseBtn{
 display:none;
 position:fixed;
 z-index:12;
 top:60px;
 right:10px
}

.card{
 width:340px;
 max-width:95%;
 background:#071522;
 border:1px solid #00eaff44;
 border-radius:16px;
 padding:15px;
 margin:6px;
 text-align:center
}

.rank{
 width:350px;
 max-width:95%;
 display:flex;
 justify-content:space-between;
 padding:13px;
 margin:4px;
 border-radius:10px;
 background:#071522;
 border:1px solid #ffffff12
}

.city{
 height:65px;
 border-radius:12px;
 margin-bottom:10px
}

.neon{
 background:linear-gradient(90deg,#001522,#00eaff,#001522);
 box-shadow:0 0 20px #00eaff
}

.tokyo{
 background:linear-gradient(90deg,#190018,#ff0066,#190018)
}

.dubai{
 background:linear-gradient(90deg,#211000,#ff9900,#211000)
}

.newyork{
 background:linear-gradient(90deg,#00142b,#0077ff,#00142b)
}

.galaxy{
 background:linear-gradient(90deg,#16002c,#8b00ff,#001b33)
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<!-- ================= HUD ================= -->

<div id="hud" class="hud">
 <div class="hudBox">🪙 <span id="hudCoins">0</span></div>
 <div class="hudBox">🏆 <span id="hudScore">0</span></div>
 <div class="hudBox">🌆 <span id="hudCity">Neon City</span></div>
</div>

<button id="pauseBtn" onclick="pauseGame()">⏸ PAUSE</button>


<!-- ================= LOGIN ================= -->

<section id="loginScreen" class="screen">

 <div class="logo">CUBE RUSH</div>

 <div class="subtitle">
  Melde dich an, um deinen Highscore zu speichern.
 </div>

 <input
  id="loginEmail"
  type="email"
  placeholder="📧 E-Mail"
  autocomplete="email"
 >

 <input
  id="loginPassword"
  type="password"
  placeholder="🔒 Passwort"
  autocomplete="current-password"
 >

 <button onclick="login()">ANMELDEN</button>

 <button onclick="openRegister()">
  ACCOUNT ERSTELLEN
 </button>

 <div id="loginMessage" class="message"></div>

</section>


<!-- ================= REGISTRIERUNG ================= -->

<section id="registerScreen" class="screen hidden">

 <div class="logo">CUBE RUSH</div>

 <h2>Account erstellen</h2>

 <!-- DAS IST DER BENUTZERNAME -->

 <input
  id="registerUsername"
  type="text"
  placeholder="👤 DEIN BENUTZERNAME"
  maxlength="20"
  autocomplete="username"
 >

 <input
  id="registerEmail"
  type="email"
  placeholder="📧 E-Mail"
  autocomplete="email"
 >

 <input
  id="registerPassword"
  type="password"
  placeholder="🔒 Passwort"
  autocomplete="new-password"
 >

 <button onclick="register()">
  REGISTRIEREN
 </button>

 <button onclick="openLogin()">
  ZURÜCK
 </button>

 <div id="registerMessage" class="message"></div>

</section>


<!-- ================= HAUPTMENÜ ================= -->

<section id="menuScreen" class="screen hidden">

 <div class="logo">CUBE RUSH</div>

 <h2>
  Willkommen <span id="menuUsername"></span> 👋
 </h2>

 <p>🪙 Coins:
  <b id="menuCoins">0</b>
 </p>

 <p>🏆 Highscore:
  <b id="menuHighscore">0</b>
 </p>

 <button onclick="startGame()">
  ▶ SPIEL STARTEN
 </button>

 <button onclick="openCities()">
  🌆 STÄDTE
 </button>

 <button onclick="openRanking()">
  🏆 RANGLISTE
 </button>

 <button onclick="logout()">
  ABMELDEN
 </button>

</section>


<!-- ================= STÄDTE ================= -->

<section id="citiesScreen" class="screen hidden">

 <h1>🌆 STÄDTE</h1>

 <div id="cityList"></div>

 <button onclick="showMenu()">ZURÜCK</button>

</section>


<!-- ================= RANGLISTE ================= -->

<section id="rankingScreen" class="screen hidden">

 <h1>🏆 RANGLISTE</h1>

 <div id="rankingList">
  Lade Rangliste...
 </div>

 <button onclick="showMenu()">ZURÜCK</button>

</section>


<!-- ================= PAUSE ================= -->

<section id="pauseScreen" class="screen hidden">

 <h1>⏸ PAUSE</h1>

 <button onclick="resumeGame()">
  WEITERSPIELEN
 </button>

 <button onclick="quitGame()">
  MENÜ
 </button>

</section>


<!-- ================= GAME OVER ================= -->

<section id="gameOverScreen" class="screen hidden">

 <h1>💥 CRASH!</h1>

 <p>
  Score:
  <b id="finalScore">0</b>
 </p>

 <p>
  Münzen:
  <b id="finalCoins">0</b>
 </p>

 <p>
  Dein Highscore:
  <b id="finalHighscore">0</b>
 </p>

 <button onclick="restartGame()">
  🔄 NOCHMAL
 </button>

 <button onclick="showMenu()">
  MENÜ
 </button>

</section>


<script>

/* =========================================================
   SUPABASE
   ========================================================= */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/*
===========================================================
HIER DEINEN ANON/PUBLIC KEY EINSETZEN.

NICHT den service_role Key verwenden.
===========================================================
*/

const SUPABASE_ANON_KEY =
"Diesen eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio Das?


let authToken = null;


/* =========================================================
   SPIELER
   ========================================================= */

let player = {
 id:null,
 username:"",
 email:"",
 coins:0,
 highscore:0,
 city:0,
 unlockedCities:[0]
};


/* =========================================================
   SUPABASE REQUEST
   ========================================================= */

async function supabase(path,options={}){

 const headers={
  "apikey":SUPABASE_ANON_KEY,
  "Content-Type":"application/json",
  ...options.headers
 };

 if(authToken){
  headers.Authorization=
   "Bearer "+authToken;
 }

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
 }catch{}

 if(!response.ok){

  throw new Error(
   data?.msg ||
   data?.message ||
   data?.error_description ||
   text ||
   "Supabase Fehler"
  );

 }

 return data;
}


/* =========================================================
   REGISTRIEREN
   ========================================================= */

async function register(){

 const username=
  document
   .getElementById("registerUsername")
   .value
   .trim();

 const email=
  document
   .getElementById("registerEmail")
   .value
   .trim();

 const password=
  document
   .getElementById("registerPassword")
   .value;

 const message=
  document
   .getElementById("registerMessage");


 if(username.length<3){

  message.textContent=
   "❌ Benutzername braucht mindestens 3 Zeichen.";

  return;

 }


 if(username.length>20){

  message.textContent=
   "❌ Benutzername darf maximal 20 Zeichen haben.";

  return;

 }


 if(!email.includes("@")){

  message.textContent=
   "❌ Bitte eine gültige E-Mail eingeben.";

  return;

 }


 if(password.length<6){

  message.textContent=
   "❌ Passwort braucht mindestens 6 Zeichen.";

  return;

 }


 message.textContent=
  "⏳ Account wird erstellt...";


 try{

  const data=
   await supabase(
    "/auth/v1/signup",
    {
     method:"POST",

     body:JSON.stringify({

      email:email,

      password:password,

      /*
       DER BENUTZERNAME WIRD HIER
       MIT DEM ACCOUNT VERKNÜPFT.
      */

      data:{
       username:username
      }

     })
    }
   );


  if(!data.user){

   throw new Error(
    "Account konnte nicht erstellt werden."
   );

  }


  player.id=
   data.user.id;

  player.username=
   username;

  player.email=
   email;


  if(data.access_token){

   authToken=
    data.access_token;

   await createPlayer();

   saveLocal();

   updateMenu();

   showMenu();

  }else{

   message.textContent=
    "✅ Account erstellt! Bitte bestätige deine E-Mail und melde dich danach an.";

  }


 }catch(error){

  console.error(error);

  message.textContent=
   "❌ "+error.message;

 }

}


/* =========================================================
   PLAYER IN DATENBANK
   ========================================================= */

async function createPlayer(){

 try{

  await supabase(
   "/rest/v1/Players",
   {
    method:"POST",

    headers:{
     Prefer:"return=minimal"
    },

    body:JSON.stringify({

     id:player.id,

     username:player.username,

     highscore:0

    })
   }
  );

 }catch(error){

  console.log(
   "Player eventuell bereits vorhanden:",
   error
  );

 }

}


/* =========================================================
   LOGIN
   ========================================================= */

async function login(){

 const email=
  document
   .getElementById("loginEmail")
   .value
   .trim();

 const password=
  document
   .getElementById("loginPassword")
   .value;

 const message=
  document
   .getElementById("loginMessage");


 message.textContent=
  "⏳ Anmeldung...";


 try{

  const data=
   await supabase(
    "/auth/v1/token?grant_type=password",
    {
     method:"POST",

     body:JSON.stringify({

      email:email,

      password:password

     })
    }
   );


  authToken=
   data.access_token;

  player.id=
   data.user.id;

  player.email=
   data.user.email;


  await loadPlayer();

  loadLocal();

  updateMenu();

  showMenu();


 }catch(error){

  message.textContent=
   "❌ "+error.message;

 }

}


/* =========================================================
   PLAYER LADEN
   ========================================================= */

async function loadPlayer(){

 try{

  const data=
   await supabase(
    "/rest/v1/Players?id=eq."+
    encodeURIComponent(player.id)+
    "&select=*"
   );


  if(data.length>0){

   player.username=
    data[0].username ||
    player.email.split("@")[0];

   player.highscore=
    Number(
     data[0].highscore || 0
    );

  }else{

   player.username=
    player.email.split("@")[0];

   await createPlayer();

  }

 }catch(error){

  console.log(error);

 }

}


/* =========================================================
   HIGHSCORE SPEICHERN
   ========================================================= */

async function saveHighscore(){

 try{

  await supabase(
   "/rest/v1/Players?id=eq."+
   encodeURIComponent(player.id),

   {
    method:"PATCH",

    headers:{
     Prefer:"return=minimal"
    },

    body:JSON.stringify({

     username:player.username,

     highscore:player.highscore

    })
   }
  );

 }catch(error){

  console.log(
   "Highscore Fehler:",
   error
  );

 }

}


/* =========================================================
   RANGLISTE
   ========================================================= */

async function openRanking(){

 show("rankingScreen");

 const list=
  document.getElementById(
   "rankingList"
  );

 list.innerHTML=
  "⏳ Lade Rangliste...";


 try{

  const data=
   await supabase(
    "/rest/v1/Players"+
    "?select=username,highscore"+
    "&order=highscore.desc"+
    "&limit=100"
   );


  list.innerHTML="";


  if(data.length===0){

   list.innerHTML=
    "Noch keine Spieler.";

   return;

  }


  data.forEach(
   (playerData,index)=>{

    const row=
     document.createElement("div");

    row.className="rank";

    row.innerHTML=
     `
      <span>
       ${index+1}.
       ${escapeHTML(playerData.username)}
      </span>

      <b>
       ${Number(
        playerData.highscore||0
       ).toLocaleString()}
      </b>
     `;

    list.appendChild(row);

   }
  );


 }catch(error){

  list.innerHTML=
   "❌ Rangliste konnte nicht geladen werden.";

 }

}


/* =========================================================
   SICHERER BENUTZERNAME
   ========================================================= */

function escapeHTML(text){

 return String(text)

  .replaceAll("&","&amp;")
  .replaceAll("<","&lt;")
  .replaceAll(">","&gt;")
  .replaceAll('"',"&quot;")
  .replaceAll("'","&#039;");

}


/* =========================================================
   STÄDTE
   ========================================================= */

const citiesData=[

 {
  name:"Neon City",
  price:0,
  minCoins:3,
  maxCoins:6,
  css:"neon"
 },

 {
  name:"Tokyo Night",
  price:5000,
  minCoins:7,
  maxCoins:12,
  css:"tokyo"
 },

 {
  name:"Cyber Dubai",
  price:12000,
  minCoins:12,
  maxCoins:18,
  css:"dubai"
 },

 {
  name:"Future New York",
  price:25000,
  minCoins:18,
  maxCoins:25,
  css:"newyork"
 },

 {
  name:"Galaxy City",
  price:50000,
  minCoins:25,
  maxCoins:35,
  css:"galaxy"
 }

];


function openCities(){

 show("citiesScreen");

 const list=
  document.getElementById(
   "cityList"
  );

 list.innerHTML="";


 citiesData.forEach(
  (city,index)=>{

   const card=
    document.createElement("div");

   card.className="card";


   const unlocked=
    player.unlockedCities
     .includes(index);


   card.innerHTML=
    `
     <div class="city ${city.css}"></div>

     <h2>${city.name}</h2>

     <p>
      🪙 ${city.minCoins}-
      ${city.maxCoins}
      Münzen pro Gruppe
     </p>

     <p>
      ${
       city.price===0
       ?"KOSTENLOS"
       :city.price.toLocaleString()+" Coins"
      }
     </p>
    `;


   const button=
    document.createElement("button");


   if(unlocked){

    button.textContent=
     player.city===index
     ?"✓ AKTIV"
     :"AUSWÄHLEN";


    button.onclick=()=>{

     player.city=index;

     saveLocal();

     openCities();

    };


   }else{

    button.textContent=
     "FREISCHALTEN";


    button.onclick=()=>{

     if(player.coins<city.price){

      alert(
       "Du hast nicht genug Coins."
      );

      return;

     }


     player.coins-=
      city.price;

     player.unlockedCities.push(
      index
     );

     player.city=
      index;

     saveLocal();

     openCities();

    };

   }


   card.appendChild(button);

   list.appendChild(card);

  }
 );

}


/* =========================================================
   MENÜ
   ========================================================= */

function updateMenu(){

 document
  .getElementById("menuUsername")
  .textContent=
   player.username;

 document
  .getElementById("menuCoins")
  .textContent=
   player.coins.toLocaleString();

 document
  .getElementById("menuHighscore")
  .textContent=
   player.highscore.toLocaleString();

}


function showMenu(){

 show("menuScreen");

 updateMenu();

}


/* =========================================================
   SCREENS
   ========================================================= */

function show(id){

 document
  .querySelectorAll(".screen")
  .forEach(
   screen=>
    screen.classList.add("hidden")
  );


 document
  .getElementById(id)
  .classList.remove("hidden");

}


function openRegister(){

 show("registerScreen");

}


function openLogin(){

 show("loginScreen");

}


/* =========================================================
   LOCAL SPEICHER
   ========================================================= */

function saveLocal(){

 localStorage.setItem(
  "cubeRushCoins",
  player.coins
 );

 localStorage.setItem(
  "cubeRushCity",
  player.city
 );

 localStorage.setItem(
  "cubeRushUnlocked",
  JSON.stringify(
   player.unlockedCities
  )
 );

}


function loadLocal(){

 player.coins=
  Number(
   localStorage.getItem(
    "cubeRushCoins"
   )||0
  );


 player.city=
  Number(
   localStorage.getItem(
    "cubeRushCity"
   )||0
  );


 try{

  player.unlockedCities=
   JSON.parse(
    localStorage.getItem(
     "cubeRushUnlocked"
    )||"[0]"
   );

 }catch{

  player.unlockedCities=[0];

 }

}


/* =========================================================
   SPIEL
   ========================================================= */

const canvas=
 document.getElementById("game");

const ctx=
 canvas.getContext("2d");


let gameRunning=false;
let gamePaused=false;

let lane=0;

let score=0;
let roundCoins=0;

let speed=280;

let objects=[];

let playerX=0;

let lastTime=0;

let coinTimer=0;
let carTimer=0;


/*
 AUTOS HABEN EINEN FESTEN MINDESTABSTAND.
 */

const MIN_CAR_DISTANCE=260;


function resizeCanvas(){

 canvas.width=
  window.innerWidth;

 canvas.height=
  window.innerHeight;

}


resizeCanvas();

window.addEventListener(
 "resize",
 resizeCanvas
);


/* =========================================================
   STRASSEN
   ========================================================= */

function getRoad(){

 const width=
  Math.min(
   window.innerWidth*0.84,
   620
  );


 return{

  left:
   (window.innerWidth-width)/2,

  width:width

 };

}


function getLaneX(laneNumber){

 const road=
  getRoad();

 const laneWidth=
  road.width/3;


 return(
  road.left+
  laneWidth*(laneNumber+1)+
  laneWidth/2
 );

}


/* =========================================================
   SPIEL STARTEN
   ========================================================= */

function startGame(){

 showGameHUD();

 gameRunning=true;
 gamePaused=false;

 lane=0;

 score=0;

 roundCoins=0;

 speed=280;

 objects=[];

 coinTimer=1;

 carTimer=.8;

 playerX=
  getLaneX(lane);

 lastTime=
  performance.now();

}


function showGameHUD(){

 document
  .getElementById("hud")
  .style.display="flex";

 document
  .getElementById("pauseBtn")
  .style.display="block";

}


function hideGameHUD(){

 document
  .getElementById("hud")
  .style.display="none";

 document
  .getElementById("pauseBtn")
  .style.display="none";

}


/* =========================================================
   HINTERGRUND
   ========================================================= */

function drawBackground(){

 let top="#02040a";

 if(player.city===1)
  top="#190018";

 if(player.city===2)
  top="#211000";

 if(player.city===3)
  top="#00172b";

 if(player.city===4)
  top="#17002b";


 const gradient=
  ctx.createLinearGradient(
   0,0,0,canvas.height
  );


 gradient.addColorStop(
  0,
  top
 );

 gradient.addColorStop(
  1,
  "#02040a"
 );


 ctx.fillStyle=gradient;

 ctx.fillRect(
  0,
  0,
  canvas.width,
  canvas.height
 );


 const road=
  getRoad();


 ctx.fillStyle=
  "#111a22";


 ctx.fillRect(
  road.left,
  0,
  road.width,
  canvas.height
 );


 ctx.fillStyle=
  "#00eaff";


 ctx.fillRect(
  road.left,
  0,
  3,
  canvas.height
 );


 ctx.fillRect(
  road.left+road.width-3,
  0,
  3,
  canvas.height
 );


 const laneWidth=
  road.width/3;


 const offset=
  (score*5)%90;


 ctx.fillStyle=
  "#ffffff55";


 for(
  let y=-100+offset;
  y<canvas.height;
  y+=90
 ){

  ctx.fillRect(
   road.left+
   laneWidth-2,
   y,
   4,
   50
  );


  ctx.fillRect(
   road.left+
   laneWidth*2-2,
   y,
   4,
   50
  );

 }

}


/* =========================================================
   SPIELER
   ========================================================= */

function drawPlayer(){

 const y=
  canvas.height*.76;


 ctx.save();

 ctx.translate(
  playerX,
  y
 );


 ctx.shadowColor=
  "#00eaff";

 ctx.shadowBlur=25;


 ctx.fillStyle=
  "#00eaff";


 ctx.fillRect(
  -27,
  -27,
  54,
  54
 );


 ctx.shadowBlur=0;


 ctx.fillStyle="white";


 ctx.fillRect(
  -17,
  -14,
  11,
  7
 );


 ctx.fillRect(
  6,
  -14,
  11,
  7
 );


 ctx.restore();

}


/* =========================================================
   AUTOS
   ========================================================= */

function canSpawnCar(laneNumber){

 return !objects.some(
  object=>
   object.type==="car" &&
   object.lane===laneNumber &&
   Math.abs(
    object.y+120
   )<
    MIN_CAR_DISTANCE
 );

}


function spawnCar(){

 const possible=
  [-1,0,1]
   .filter(canSpawnCar);


 if(possible.length===0)
  return;


 const chosenLane=
  possible[
   Math.floor(
    Math.random()*
    possible.length
   )
  ];


 const colors=[
  "#ff1744",
  "#168cff",
  "#ffffff",
  "#ff9900",
  "#8b4dff"
 ];


 objects.push({

  type:"car",

  lane:chosenLane,

  y:-150,

  color:
   colors[
    Math.floor(
     Math.random()*
     colors.length
    )
   ]

 });

}


function drawCar(car){

 const x=
  getLaneX(car.lane);


 ctx.save();

 ctx.translate(
  x,
  car.y
 );


 ctx.shadowColor=
  car.color;

 ctx.shadowBlur=15;


 ctx.fillStyle=
  car.color;


 ctx.beginPath();

 ctx.roundRect(
  -31,
  -50,
  62,
  100,
  10
 );

 ctx.fill();


 ctx.shadowBlur=0;


 ctx.fillStyle=
  "#102331";


 ctx.beginPath();

 ctx.roundRect(
  -21,
  -30,
  42,
  28,
  7
 );

 ctx.fill();


 ctx.fillStyle=
  "white";


 ctx.fillRect(
  -22,
  28,
  13,
  7
 );


 ctx.fillRect(
  9,
  28,
  13,
  7
 );


 ctx.restore();

}


/* =========================================================
   MÜNZEN
   ========================================================= */

function spawnCoins(){

 const city=
  citiesData[player.city];


 const amount=
  city.minCoins+
  Math.floor(
   Math.random()*
   (
    city.maxCoins-
    city.minCoins+
    1
   )
  );


 const chosenLane=
  [-1,0,1][
   Math.floor(
    Math.random()*3
   )
  ];


 /*
  Die Münzen kommen regelmäßig,
  aber mit Abstand.
 */

 for(
  let i=0;
  i<amount;
  i++
 ){

  objects.push({

   type:"coin",

   lane:chosenLane,

   y:
    -60-
    i*65

  });

 }

}


function drawCoin(coin){

 const x=
  getLaneX(
   coin.lane
  );


 ctx.save();

 ctx.translate(
  x,
  coin.y
 );


 ctx.fillStyle=
  "#ffd900";

 ctx.shadowColor=
  "#ffd900";

 ctx.shadowBlur=20;


 ctx.beginPath();

 ctx.arc(
  0,
  0,
  14,
  0,
  Math.PI*2
 );

 ctx.fill();


 ctx.restore();

}


/* =========================================================
   UPDATE
   ========================================================= */

function update(delta){

 if(
  !gameRunning||
  gamePaused
 )
  return;


 const seconds=
  delta/1000;


 score+=
  seconds*10;


 speed=
  Math.min(
   700,
   speed+
   seconds*4
  );


 playerX+=
  (
   getLaneX(lane)-
   playerX
  )*
  Math.min(
   1,
   seconds*12
  );


 coinTimer-=seconds;

 carTimer-=seconds;


 if(coinTimer<=0){

  spawnCoins();

  coinTimer=
   3+
   Math.random()*2;

 }


 if(carTimer<=0){

  spawnCar();

  carTimer=
   1.3+
   Math.random()*.8;

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


  if(
   object.y>
   canvas.height+150
  ){

   objects.splice(
    i,
    1
   );

   continue;

  }


  const playerY=
   canvas.height*.76;


  if(
   object.type==="coin"&&
   Math.abs(
    object.y-playerY
   )<55&&
   Math.abs(
    getLaneX(
     object.lane
    )-
    playerX
   )<50
  ){

   roundCoins++;

   objects.splice(
    i,
    1
   );

   continue;

  }


  if(
   object.type==="car"&&
   Math.abs(
    object.y-playerY
   )<65&&
   Math.abs(
    getLaneX(
     object.lane
    )-
    playerX
   )<52
  ){

   endGame();

   return;

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
   Math.floor(
    score
   ).toLocaleString();


 document
  .getElementById("hudCity")
  .textContent=
   citiesData[
    player.city
   ].name;

}


/* =========================================================
   GAME LOOP
   ========================================================= */

function gameLoop(time){

 const delta=
  Math.min(
   40,
   time-lastTime
  );


 lastTime=time;


 drawBackground();


 objects.forEach(
  object=>{

   if(
    object.type==="car"
   )
    drawCar(object);

   else
    drawCoin(object);

  }
 );


 drawPlayer();


 update(delta);


 requestAnimationFrame(
  gameLoop
 );

}


requestAnimationFrame(
 gameLoop
 );


/* =========================================================
   TASTATUR
   ========================================================= */

document.addEventListener(
 "keydown",
 event=>{

  if(
   !gameRunning||
   gamePaused
  )
   return;


  if(
   event.key==="ArrowLeft"||
   event.key.toLowerCase()==="a"
  ){

   lane=
    Math.max(
     -1,
     lane-1
    );

  }


  if(
   event.key==="ArrowRight"||
   event.key.toLowerCase()==="d"
  ){

   lane=
    Math.min(
     1,
     lane+1
    );

  }

 }
);


/* =========================================================
   HANDY SWIPE
   ========================================================= */

let touchStartX=0;


canvas.addEventListener(
 "touchstart",
 event=>{

  if(
   !gameRunning||
   gamePaused
  )
   return;


  touchStartX=
   event.changedTouches[0]
    .clientX;

 },
 {passive:true}
);


canvas.addEventListener(
 "touchend",
 event=>{

  if(
   !gameRunning||
   gamePaused
  )
   return;


  const endX=
   event.changedTouches[0]
    .clientX;


  const difference=
   endX-touchStartX;


  if(
   Math.abs(difference)<30
  )
   return;


  if(difference>0){

   lane=
    Math.min(
     1,
     lane+1
    );

  }else{

   lane=
    Math.max(
     -1,
     lane-1
    );

  }

 },
 {passive:true}
);


/* =========================================================
   PAUSE
   ========================================================= */

function pauseGame(){

 if(!gameRunning)
  return;

 gamePaused=true;

 document
  .getElementById("pauseBtn")
  .style.display="none";

 show("pauseScreen");

}


function resumeGame(){

 gamePaused=false;

 showGameHUD();

 lastTime=
  performance.now();

}


function quitGame(){

 gameRunning=false;

 hideGameHUD();

 showMenu();

}


/* =========================================================
   GAME OVER
   ========================================================= */

async function endGame(){

 if(!gameRunning)
  return;


 gameRunning=false;


 player.coins+=
  roundCoins;


 const finalScore=
  Math.floor(score);


 if(
  finalScore>
  player.highscore
 ){

  player.highscore=
   finalScore;

  await saveHighscore();

 }


 saveLocal();

 hideGameHUD();


 document
  .getElementById("finalScore")
  .textContent=
   finalScore.toLocaleString();


 document
  .getElementById("finalCoins")
  .textContent=
   roundCoins.toLocaleString();


 document
  .getElementById("finalHighscore")
  .textContent=
   player.highscore.toLocaleString();


 show("gameOverScreen");

}


function restartGame(){

 startGame();

}


/* =========================================================
   ABMELDEN
   ========================================================= */

async function logout(){

 try{

  await supabase(
   "/auth/v1/logout",
   {method:"POST"}
  );

 }catch{}


 authToken=null;

 location.reload();

}


/* =========================================================
   START
   ========================================================= */

loadLocal();

show("loginScreen");

</script>

</body>
</html>
