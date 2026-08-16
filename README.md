<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>Cube Rush</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

<style>
*{box-sizing:border-box}

html,body{
 margin:0;
 width:100%;
 height:100%;
 overflow:hidden;
 background:#03050b;
 color:white;
 font-family:Arial,sans-serif;
}

canvas{
 position:fixed;
 inset:0;
 width:100%;
 height:100%;
 touch-action:none;
}

.screen{
 position:fixed;
 inset:0;
 z-index:20;
 display:flex;
 flex-direction:column;
 align-items:center;
 justify-content:center;
 padding:20px;
 background:
 radial-gradient(circle at 50% 20%,#073454,#02050c 65%);
 overflow:auto;
}

.hidden{display:none!important}

.logo{
 font-size:45px;
 font-weight:1000;
 letter-spacing:3px;
 color:#00eaff;
 text-shadow:0 0 12px #00eaff,0 0 35px #00eaff;
 text-align:center;
}

.subtitle{
 color:#8bbbc9;
 letter-spacing:4px;
 margin-bottom:20px;
}

input{
 width:320px;
 max-width:92vw;
 padding:15px;
 margin:6px;
 border-radius:12px;
 border:1px solid #00eaff;
 outline:none;
 background:#071421;
 color:white;
 font-size:16px;
}

input:focus{
 box-shadow:0 0 18px #00eaff66;
}

button{
 padding:13px 22px;
 margin:6px;
 border:0;
 border-radius:12px;
 background:#00eaff;
 color:#001018;
 font-weight:900;
 font-size:15px;
 cursor:pointer;
}

button:active{
 transform:scale(.95);
}

button.secondary{
 background:#102331;
 color:#8eefff;
 border:1px solid #00eaff55;
}

.msg{
 min-height:24px;
 max-width:350px;
 color:#a9d5df;
 text-align:center;
 margin:8px;
}

.coins{
 color:#ffd900;
 font-weight:900;
 font-size:20px;
 margin:10px;
}

.card{
 width:180px;
 min-height:220px;
 display:inline-flex;
 flex-direction:column;
 justify-content:space-between;
 align-items:center;
 margin:7px;
 padding:14px;
 background:linear-gradient(145deg,#0b1d2b,#06101a);
 border:1px solid #00eaff55;
 border-radius:18px;
 text-align:center;
 overflow:hidden;
}

.preview{
 width:130px;
 height:105px;
 border-radius:15px;
 position:relative;
 overflow:hidden;
 margin-bottom:8px;
 border:1px solid #ffffff22;
}

.avatar{
 width:55px;
 height:55px;
 position:absolute;
 left:50%;
 top:50%;
 transform:translate(-50%,-50%) rotate(45deg);
 border-radius:10px;
 box-shadow:0 0 25px currentColor;
}

.avatar:after{
 content:"";
 position:absolute;
 width:12px;
 height:12px;
 border-radius:50%;
 background:white;
 left:10px;
 top:10px;
 box-shadow:22px 22px 0 white;
}

.avatar.blue{background:#00eaff;color:#00eaff}
.avatar.red{background:#ff3158;color:#ff3158}
.avatar.purple{background:#9b4dff;color:#9b4dff}
.avatar.green{background:#25ff9a;color:#25ff9a}
.avatar.gold{background:#ffd400;color:#ffd400}
.avatar.white{background:#ffffff;color:#ffffff}
.avatar.black{background:#10131a;color:#8c95a0}
.avatar.orange{background:#ff7b22;color:#ff7b22}

.cityPreview{
 width:150px;
 height:105px;
 border-radius:15px;
 position:relative;
 overflow:hidden;
 border:1px solid #ffffff33;
}

.cityPreview span{
 position:absolute;
 bottom:0;
 width:22%;
 background:#ffffff33;
 border-top:2px solid #ffffff55;
}

.cityPreview span:nth-child(1){left:3%;height:45%}
.cityPreview span:nth-child(2){left:27%;height:70%}
.cityPreview span:nth-child(3){left:51%;height:55%}
.cityPreview span:nth-child(4){left:75%;height:82%}

.city-neon{
 background:linear-gradient(#16003b,#00b9d9);
}

.city-tokyo{
 background:linear-gradient(#280014,#ff236e);
}

.city-dubai{
 background:linear-gradient(#321400,#ff9d25);
}

.city-newyork{
 background:linear-gradient(#061b3c,#4b83ff);
}

.city-galaxy{
 background:radial-gradient(circle,#7435ff,#05001a 70%);
}

.rank{
 width:370px;
 max-width:94vw;
 display:flex;
 justify-content:space-between;
 align-items:center;
 padding:13px;
 margin:4px;
 background:#091522;
 border:1px solid #ffffff0b;
 border-radius:11px;
}

.rank:nth-child(1){
 border-color:#ffd700;
 box-shadow:0 0 15px #ffd70033;
}

.rank:nth-child(2){
 border-color:#c9d2d7;
}

.rank:nth-child(3){
 border-color:#cd7f32;
}

.rankName{
 font-weight:800;
}

.rankScore{
 color:#00eaff;
 font-weight:900;
}

#hud{
 position:fixed;
 z-index:10;
 top:10px;
 left:10px;
 right:10px;
 display:none;
 justify-content:space-between;
 gap:5px;
 pointer-events:none;
}

.hud{
 background:#06121ddd;
 border:1px solid #00eaff66;
 padding:8px 10px;
 border-radius:10px;
 font-weight:bold;
}

#pause{
 position:fixed;
 z-index:12;
 top:60px;
 right:10px;
 display:none;
}

.powerHUD{
 position:fixed;
 z-index:11;
 top:105px;
 left:50%;
 transform:translateX(-50%);
 display:none;
 gap:7px;
}

.power{
 background:#06121de8;
 border:1px solid #00eaff77;
 padding:7px 10px;
 border-radius:10px;
 font-weight:bold;
}

.power.magnet{
 color:#00eaff;
}

.power.double{
 color:#ffd900;
}

.scroll{
 max-width:95vw;
 text-align:center;
}

#characterList,
#cityList{
 max-width:950px;
 display:flex;
 justify-content:center;
 flex-wrap:wrap;
}

.small{
 color:#88a7b0;
 font-size:13px;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
 <div class="hud">COINS <span id="coins">0</span></div>
 <div class="hud">SCORE <span id="score">0</span></div>
 <div class="hud"><span id="cityName">Neon City</span></div>
</div>

<div id="powerHUD" class="powerHUD">
 <div id="magnetHUD" class="power magnet hidden">
  MAGNET <span id="magnetTime">30</span>s
 </div>
 <div id="doubleHUD" class="power double hidden">
  x2 COINS <span id="doubleTime">30</span>s
 </div>
</div>

<button id="pause">PAUSE</button>

<!-- LOGIN -->

<div id="login" class="screen">

 <div class="logo">CUBE RUSH</div>
 <div class="subtitle">ONLINE EDITION</div>

 <input
  id="username"
  placeholder="Benutzername"
  maxlength="18"
  autocomplete="username"
 >

 <input
  id="email"
  type="email"
  placeholder="E-Mail"
  autocomplete="email"
 >

 <input
  id="password"
  type="password"
  placeholder="Passwort"
  minlength="6"
  autocomplete="current-password"
 >

 <button onclick="registerUser()">REGISTRIEREN</button>
 <button onclick="loginUser()">ANMELDEN</button>

 <div id="loginMsg" class="msg"></div>

</div>

<!-- MENU -->

<div id="menu" class="screen hidden">

 <div class="logo">CUBE RUSH</div>

 <p>
  Spieler:
  <b id="player"></b>
 </p>

 <div class="coins">
  COINS <span id="menuCoins">0</span>
 </div>

 <button onclick="startGame()">SPIEL STARTEN</button>
 <button onclick="charactersMenu()">CHARAKTERE</button>
 <button onclick="citiesMenu()">STÄDTE</button>
 <button onclick="rankingMenu()">RANGLISTE</button>
 <button onclick="codeMenu()">ADMIN-CODE</button>
 <button class="secondary" onclick="logout()">ABMELDEN</button>

</div>

<!-- CHARACTERS -->

<div id="characters" class="screen hidden">

 <h1>CHARAKTERE</h1>

 <div class="coins">
  COINS <span id="charCoins">0</span>
 </div>

 <div id="characterList"></div>

 <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- CITIES -->

<div id="cities" class="screen hidden">

 <h1>STÄDTE</h1>

 <div id="cityList"></div>

 <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- RANKING -->

<div id="ranking" class="screen hidden">

 <h1>RANGLISTE</h1>

 <div id="rankingList">
  Lade Rangliste...
 </div>

 <button onclick="rankingMenu()">AKTUALISIEREN</button>
 <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- CODE -->

<div id="codes" class="screen hidden">

 <h1>ADMIN-CODE</h1>

 <input
  id="secretCode"
  inputmode="numeric"
  maxlength="3"
  placeholder="Code"
 >

 <button onclick="redeemCode()">EINLÖSEN</button>

 <div id="codeMsg" class="msg"></div>

 <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- PAUSE -->

<div id="pauseScreen" class="screen hidden">

 <h1>PAUSE</h1>

 <button onclick="resumeGame()">WEITERSPIELEN</button>
 <button onclick="quitGame()">MENÜ</button>

</div>

<!-- GAME OVER -->

<div id="gameOver" class="screen hidden">

 <h1>CRASH!</h1>

 <div id="gameOverText"></div>

 <button onclick="restartGame()">NOCHMAL</button>
 <button onclick="showMenu()">MENÜ</button>

</div>

<script>

/* =========================================================
   SUPABASE
========================================================= */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";

const supabaseClient =
window.supabase.createClient(
 SUPABASE_URL,
 SUPABASE_KEY
);

/* =========================================================
   PLAYER
========================================================= */

let playerData={
 id:"",
 username:"",
 email:"",
 coins:0,
 highscore:0,
 characters:[0],
 cities:[0],
 character:0,
 city:0
};

/* =========================================================
   CITIES
========================================================= */

const cityData=[

 {
  name:"Neon City",
  price:0,
  min:3,
  max:6,
  className:"city-neon"
 },

 {
  name:"Tokyo Night",
  price:5000,
  min:6,
  max:10,
  className:"city-tokyo"
 },

 {
  name:"Cyber Dubai",
  price:12000,
  min:9,
  max:14,
  className:"city-dubai"
 },

 {
  name:"Future New York",
  price:25000,
  min:13,
  max:19,
  className:"city-newyork"
 },

 {
  name:"Galaxy City",
  price:50000,
  min:18,
  max:26,
  className:"city-galaxy"
 }

];

/* =========================================================
   CHARACTERS
========================================================= */

const charData=[

 ["Blue",0,"blue"],
 ["Volt",300,"white"],
 ["Fire",700,"red"],
 ["Ice",1200,"white"],
 ["Purple",2000,"purple"],
 ["Poison",3000,"green"],
 ["Magma",4500,"orange"],
 ["Thunder",6500,"blue"],
 ["Void",9000,"black"],
 ["Omega",12000,"purple"],
 ["Cyber",16000,"blue"],
 ["Gold",22000,"gold"],
 ["Ruby",28000,"red"],
 ["Diamond",35000,"white"],
 ["Galaxy",45000,"purple"],
 ["Phantom",55000,"black"],
 ["Solar",70000,"gold"],
 ["Nebula",85000,"purple"],
 ["Aurora",100000,"green"],
 ["Cube God",150000,"gold"]

];

/* =========================================================
   SCREEN SYSTEM
========================================================= */

function hideAll(){

 document
 .querySelectorAll(".screen")
 .forEach(x=>{
  x.classList.add("hidden");
 });

}

function showMenu(){

 hideAll();

 document
 .getElementById("menu")
 .classList.remove("hidden");

 document
 .getElementById("player")
 .textContent=
 playerData.username;

 document
 .getElementById("menuCoins")
 .textContent=
 playerData.coins.toLocaleString();

}

/* =========================================================
   LOGIN
========================================================= */

function loginMsg(text){

 document
 .getElementById("loginMsg")
 .textContent=text;

}

async function registerUser(){

 const name=
 document.getElementById("username")
 .value.trim();

 const email=
 document.getElementById("email")
 .value.trim();

 const password=
 document.getElementById("password")
 .value;

 if(name.length<3){

  loginMsg(
   "Benutzername muss mindestens 3 Zeichen haben."
  );

  return;
 }

 if(!email){

  loginMsg("Bitte E-Mail eingeben.");

  return;
 }

 if(password.length<6){

  loginMsg(
   "Passwort muss mindestens 6 Zeichen haben."
  );

  return;
 }

 loginMsg("Registrierung läuft...");

 const {
  data,
  error
 }=
 await supabaseClient.auth.signUp({

  email:email,
  password:password,

  options:{
   data:{
    username:name
   }
  }

 });

 if(error){

  loginMsg(error.message);

  return;
 }

 if(!data.user){

  loginMsg(
   "Registrierung fehlgeschlagen."
  );

  return;
 }

 playerData.id=data.user.id;
 playerData.username=name;
 playerData.email=email;

 const {
  error:insertError
 }=
 await supabaseClient
 .from("Players")
 .insert({

  id:data.user.id,
  username:name,
  highscore:0

 });

 if(insertError &&
    !insertError.message
    .toLowerCase()
    .includes("duplicate")
 ){

  console.log(insertError);

 }

 if(data.session){

  showMenu();

 }else{

  loginMsg(
   "Registriert! Prüfe deine E-Mail und bestätige den Link."
  );

 }

}

async function loginUser(){

 const name=
 document.getElementById("username")
 .value.trim();

 const email=
 document.getElementById("email")
 .value.trim();

 const password=
 document.getElementById("password")
 .value;

 if(!name){

  loginMsg(
   "Bitte Benutzername eingeben."
  );

  return;
 }

 if(!email||!password){

  loginMsg(
   "Bitte E-Mail und Passwort eingeben."
  );

  return;
 }

 loginMsg("Anmeldung läuft...");

 const {
  data,
  error
 }=
 await supabaseClient.auth.signInWithPassword({

  email:email,
  password:password

 });

 if(error){

  loginMsg(error.message);

  return;
 }

 playerData.id=data.user.id;
 playerData.email=data.user.email;

 await loadOnlinePlayer();

 showMenu();

}

async function loadOnlinePlayer(){

 const {
  data,
  error
 }=
 await supabaseClient
 .from("Players")
 .select("*")
 .eq("id",playerData.id)
 .maybeSingle();

 if(error){

  console.log(error);
  return;

 }

 if(data){

  playerData.username=
   data.username ||
   playerData.username;

  playerData.highscore=
   Number(data.highscore)||0;

 }

}

async function logout(){

 await supabaseClient.auth.signOut();

 location.reload();

}

/* =========================================================
   ONLINE SCORE
========================================================= */

async function saveOnlineScore(){

 if(!playerData.id)
  return;

 const {
  error
 }=
 await supabaseClient
 .from("Players")
 .upsert({

  id:playerData.id,
  username:playerData.username,
  highscore:playerData.highscore

 },{
  onConflict:"id"
 });

 if(error){

  console.log(
   "Highscore konnte nicht gespeichert werden:",
   error
  );

 }

}

/* =========================================================
   CHARACTERS
========================================================= */

function charactersMenu(){

 hideAll();

 const list=
 document.getElementById(
  "characterList"
 );

 list.innerHTML="";

 document
 .getElementById("charCoins")
 .textContent=
 playerData.coins.toLocaleString();

 charData.forEach(
  (char,index)=>{

   const card=
   document.createElement("div");

   card.className="card";

   card.innerHTML=`

    <div class="preview">

      <div class="avatar ${char[2]}"></div>

    </div>

    <h3>${char[0]}</h3>

    <div class="small">
      ${
       playerData.characters.includes(index)
       ?"FREIGESCHALTET"
       :char[1].toLocaleString()+" COINS"
      }
    </div>

   `;

   const button=
   document.createElement("button");

   const unlocked=
   playerData.characters
   .includes(index);

   if(unlocked){

    button.textContent=
    playerData.character===index
    ?"AKTIV"
    :"AUSWÄHLEN";

    button.onclick=()=>{

     playerData.character=index;

     charactersMenu();

    };

   }else{

    button.textContent="KAUFEN";

    button.onclick=()=>{

     if(
      playerData.coins<
      char[1]
     ){

      alert(
       "Nicht genug Coins."
      );

      return;

     }

     playerData.coins-=char[1];

     playerData.characters.push(index);

     playerData.character=index;

     charactersMenu();

    };

   }

   card.appendChild(button);

   list.appendChild(card);

  }
 );

 document
 .getElementById("characters")
 .classList.remove("hidden");

}

/* =========================================================
   CITIES
========================================================= */

function citiesMenu(){

 hideAll();

 const list=
 document.getElementById(
  "cityList"
 );

 list.innerHTML="";

 cityData.forEach(
  (city,index)=>{

   const card=
   document.createElement("div");

   card.className="card";

   card.innerHTML=`

    <div class="cityPreview ${city.className}">

      <span></span>
      <span></span>
      <span></span>
      <span></span>

    </div>

    <h3>${city.name}</h3>

    <div class="small">
      ${city.min}–${city.max} Coins pro Gruppe
    </div>

    <div class="small">
      ${
       city.price===0
       ?"KOSTENLOS"
       :city.price.toLocaleString()+" COINS"
      }
    </div>

   `;

   const button=
   document.createElement("button");

   const unlocked=
   playerData.cities
   .includes(index);

   if(unlocked){

    button.textContent=
    playerData.city===index
    ?"AKTIV"
    :"AUSWÄHLEN";

    button.onclick=()=>{

     playerData.city=index;

     citiesMenu();

    };

   }else{

    button.textContent="FREISCHALTEN";

    button.onclick=()=>{

     if(
      playerData.coins<
      city.price
     ){

      alert(
       "Nicht genug Coins."
      );

      return;

     }

     playerData.coins-=city.price;

     playerData.cities.push(index);

     playerData.city=index;

     citiesMenu();

    };

   }

   card.appendChild(button);

   list.appendChild(card);

  }
 );

 document
 .getElementById("cities")
 .classList.remove("hidden");

}

/* =========================================================
   CODES
========================================================= */

function codeMenu(){

 hideAll();

 document
 .getElementById("secretCode")
 .value="";

 document
 .getElementById("codeMsg")
 .textContent="";

 document
 .getElementById("codes")
 .classList.remove("hidden");

}

async function redeemCode(){

 const code=
 document
 .getElementById("secretCode")
 .value.trim();

 const msg=
 document.getElementById(
  "codeMsg"
 );

 if(code==="110"){

  playerData.highscore=1000000;

  playerData.cities=
  cityData.map(
   (_,i)=>i
  );

  playerData.characters=
  charData.map(
   (_,i)=>i
  );

  playerData.coins=
  Math.max(
   playerData.coins,
   1000000
  );

  msg.textContent=
  "Code 110 aktiviert: 1.000.000 Highscore + alle Städte.";

 }

 else if(code==="112"){

  playerData.highscore=500000;

  playerData.cities=[
   0,
   1
  ];

  playerData.coins=
  Math.max(
   playerData.coins,
   500000
  );

  msg.textContent=
  "Code 112 aktiviert: 500.000 Highscore + 2 Städte.";

 }

 else{

  msg.textContent=
  "Falscher Code.";

  return;

 }

 await saveOnlineScore();

}

/* =========================================================
   ONLINE RANKING
========================================================= */

async function rankingMenu(){

 hideAll();

 const list=
 document.getElementById(
  "rankingList"
 );

 list.innerHTML=
 "Rangliste wird geladen...";

 const {
  data,
  error
 }=
 await supabaseClient
 .from("Players")
 .select("username,highscore")
 .order("highscore",{
  ascending:false
 })
 .limit(50);

 if(error){

  list.innerHTML=
  "Rangliste konnte nicht geladen werden.";

  console.log(error);

 }else{

  list.innerHTML="";

  if(!data.length){

   list.textContent=
   "Noch keine Spieler.";

  }

  data.forEach(
   (p,index)=>{

    const row=
    document.createElement("div");

    row.className="rank";

    row.innerHTML=`

     <span class="rankName">
      #${index+1} ${escapeHTML(p.username)}
     </span>

     <span class="rankScore">
      ${Number(p.highscore||0).toLocaleString()}
     </span>

    `;

    list.appendChild(row);

   }
  );

 }

 document
 .getElementById("ranking")
 .classList.remove("hidden");

}

function escapeHTML(text){

 return String(text)
 .replaceAll("&","&amp;")
 .replaceAll("<","&lt;")
 .replaceAll(">","&gt;")
 .replaceAll('"',"&quot;")
 .replaceAll("'","&#039;");

}

/* =========================================================
   GAME
========================================================= */

const canvas=
document.getElementById("game");

const ctx=
canvas.getContext("2d");

function resize(){

 canvas.width=innerWidth;
 canvas.height=innerHeight;

}

addEventListener(
 "resize",
 resize
);

resize();

let running=false;
let paused=false;

let score=0;
let runCoins=0;

let lane=0;
let playerX=0;

let objects=[];

let speed=250;

let last=
performance.now();

let magnet=0;
let doubleCoins=0;

let powerTimer=5;

function road(){

 const width=
 Math.min(
  innerWidth*.84,
 600
 );

 return{

  left:
  (innerWidth-width)/2,

  width:width

 };

}

function laneX(n){

 const r=road();

 return r.left+
 r.width*(n+0.5)/3;

}

/* =========================================================
   GAME START
========================================================= */

function startGame(){

 hideAll();

 running=true;
 paused=false;

 score=0;
 runCoins=0;

 lane=0;

 playerX=
 laneX(0);

 objects=[];

 speed=250;

 magnet=0;
 doubleCoins=0;

 powerTimer=5;

 document
 .getElementById("hud")
 .style.display="flex";

 document
 .getElementById("pause")
 .style.display="block";

 last=
 performance.now();

}

/* =========================================================
   WORLD
========================================================= */

function drawWorld(){

 const city=
 cityData[playerData.city];

 let top="#02040a";
 let bottom="#030912";

 if(city.name==="Tokyo Night"){

  top="#19000e";
  bottom="#050006";

 }

 if(city.name==="Cyber Dubai"){

  top="#2a1000";
  bottom="#080400";

 }

 if(city.name==="Future New York"){

  top="#06183b";
  bottom="#020611";

 }

 if(city.name==="Galaxy City"){

  top="#16002f";
  bottom="#02000a";

 }

 const gradient=
 ctx.createLinearGradient(
  0,0,
  0,innerHeight
 );

 gradient.addColorStop(0,top);
 gradient.addColorStop(1,bottom);

 ctx.fillStyle=gradient;

 ctx.fillRect(
  0,0,
  innerWidth,
  innerHeight
 );

 const r=road();

 ctx.fillStyle="#111820";

 ctx.fillRect(
  r.left,
  0,
  r.width,
  innerHeight
 );

 ctx.fillStyle=
 city.name==="Tokyo Night"
 ?" #ff236e"
 :"#00eaff";

 ctx.fillRect(
  r.left,
  0,
  3,
  innerHeight
 );

 ctx.fillRect(
  r.left+r.width-3,
  0,
  3,
  innerHeight
 );

 const lw=
 r.width/3;

 const offset=
 (score*7)%110;

 ctx.fillStyle="#ffffff66";

 for(
  let y=-110+offset;
  y<innerHeight;
  y+=110
 ){

  ctx.fillRect(
   r.left+lw-2,
   y,
   4,
   58
  );

  ctx.fillRect(
   r.left+lw*2-2,
   y,
   4,
   58
  );

 }

}

/* =========================================================
   PLAYER
========================================================= */

function drawPlayer(){

 const y=
 innerHeight*.78;

 ctx.save();

 ctx.translate(
  playerX,
  y
 );

 let color="#00eaff";

 const c=
 charData[playerData.character];

 if(c){

  const map={
   blue:"#00eaff",
   red:"#ff3158",
   purple:"#9b4dff",
   green:"#25ff9a",
   gold:"#ffd400",
   white:"#ffffff",
   black:"#151820",
   orange:"#ff7b22"
  };

  color=
  map[c[2]]||
  "#00eaff";

 }

 ctx.fillStyle=color;
 ctx.shadowColor=color;
 ctx.shadowBlur=25;

 ctx.beginPath();

 ctx.roundRect(
  -28,
  -28,
  56,
  56,
  9
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#061018";

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
   CARS
========================================================= */

function spawnCar(){

 /*
  Autos werden nur erzeugt,
  wenn die Spur weit genug vom
  letzten Auto entfernt ist.
 */

 const possible=[-1,0,1]
 .sort(()=>Math.random()-.5);

 for(const chosenLane of possible){

  const sameLane=
  objects.some(
   o=>
    o.type==="car" &&
    o.lane===chosenLane &&
    o.y<260
  );

  if(sameLane)
   continue;

  objects.push({

   type:"car",

   lane:chosenLane,

   y:-180,

   color:[
    "#ff1744",
    "#168cff",
    "#ffffff",
    "#ff9900",
    "#8b4dff",
    "#25d6c7"
   ][
    Math.floor(
     Math.random()*6
    )
   ]

  });

  return;

 }

}

function drawCar(car){

 const x=
 laneX(car.lane);

 ctx.save();

 ctx.translate(
  x,
  car.y
 );

 ctx.fillStyle=
 car.color;

 ctx.shadowColor=
 car.color;

 ctx.shadowBlur=18;

 ctx.beginPath();

 ctx.roundRect(
  -32,
  -58,
  64,
  116,
  12
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#102331";

 ctx.beginPath();

 ctx.roundRect(
  -22,
  -37,
  44,
  34,
  8
 );

 ctx.fill();

 ctx.fillStyle="#dffcff";

 ctx.fillRect(
  -23,
  31,
  14,
  7
 );

 ctx.fillRect(
  9,
  31,
  14,
  7
 );

 ctx.restore();

}

/* =========================================================
   COINS
========================================================= */

function spawnCoins(){

 const city=
 cityData[playerData.city];

 const amount=
 city.min+
 Math.floor(
  Math.random()*
  (
   city.max-
   city.min+
   1
  )
 );

 const randomLane=
 Math.floor(
  Math.random()*3
 )-1;

 /*
  Münzen werden in einer Reihe
  mit Abstand erzeugt.
 */

 for(
  let i=0;
  i<amount;
  i++
 ){

  objects.push({

   type:"coin",

   lane:randomLane,

   y:-40-i*72

  });

 }

}

function drawCoin(o,time){

 let x=
 laneX(o.lane);

 let y=o.y;

 if(magnet>0){

  x+=
  (playerX-x)*.18;

  y+=
  (innerHeight*.78-y)*.18;

 }

 const rotation=
 Math.cos(
  time*.008
 );

 ctx.save();

 ctx.translate(
  x,
  y
 );

 ctx.scale(
  Math.abs(rotation)*.7+.3,
  1
 );

 ctx.fillStyle="#ffd900";

 ctx.shadowColor="#ffd900";

 ctx.shadowBlur=18;

 ctx.beginPath();

 ctx.arc(
  0,
  0,
  14,
  0,
  Math.PI*2
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#9b7200";

 ctx.beginPath();

 ctx.arc(
  0,
  0,
  6,
  0,
  Math.PI*2
 );

 ctx.fill();

 ctx.restore();

}

/* =========================================================
   POWERUPS
========================================================= */

function spawnPower(){

 objects.push({

  type:
  Math.random()<.5
  ?"magnet"
  :"double",

  lane:
  Math.floor(
   Math.random()*3
  )-1,

  y:-80

 });

}

function drawPower(o){

 const x=
 laneX(o.lane);

 ctx.save();

 ctx.translate(
  x,
  o.y
 );

 ctx.fillStyle=
 o.type==="magnet"
 ?"rgba(0,234,255,.18)"
 :"rgba(255,217,0,.18)";

 ctx.strokeStyle=
 o.type==="magnet"
 ?" #00eaff"
 :"#ffd900";

 ctx.lineWidth=3;

 ctx.beginPath();

 ctx.arc(
  0,
  0,
  25,
  0,
  Math.PI*2
 );

 ctx.fill();
 ctx.stroke();

 ctx.fillStyle=
 o.type==="magnet"
 ?" #00eaff"
 :"#ffd900";

 ctx.font="bold 22px Arial";

 ctx.textAlign="center";
 ctx.textBaseline="middle";

 ctx.fillText(
  o.type==="magnet"
  ?"M"
  :"x2",
  0,
  0
 );

 ctx.restore();

}

/* =========================================================
   UPDATE
========================================================= */

function update(dt){

 if(!running||paused)
  return;

 const seconds=
 dt/1000;

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
  laneX(lane)-
  playerX
 )*
 Math.min(
  1,
  seconds*12
 );

 if(magnet>0)
  magnet=
  Math.max(
   0,
   magnet-seconds
  );

 if(doubleCoins>0)
  doubleCoins=
  Math.max(
   0,
   doubleCoins-seconds
  );

 updatePowerHUD();

 powerTimer-=seconds;

 if(powerTimer<=0){

  spawnPower();

  powerTimer=
  10+
  Math.random()*8;

 }

 for(
  let i=objects.length-1;
  i>=0;
  i--
 ){

  const o=
  objects[i];

  o.y+=
  speed*seconds;

  if(
   o.y>
   innerHeight+200
  ){

   objects.splice(i,1);

   continue;

  }

  /* POWERUP */

  if(
   (
    o.type==="magnet"||
    o.type==="double"
   )&&
   Math.abs(
    o.y-
    innerHeight*.78
   )<55&&
   Math.abs(
    laneX(o.lane)-
    playerX
   )<48
  ){

   if(o.type==="magnet")
    magnet=30;

   if(o.type==="double")
    doubleCoins=30;

   objects.splice(i,1);

   continue;

  }

  /* COIN */

  if(
   o.type==="coin"
  ){

   if(magnet>0)
    o.lane=lane;

   if(
    Math.abs(
     o.y-
     innerHeight*.78
    )<55&&
    Math.abs(
     laneX(o.lane)-
     playerX
    )<48
   ){

    runCoins+=
    doubleCoins>0
    ?2
    :1;

    objects.splice(i,1);

    continue;

   }

  }

  /* CAR */

  if(
   o.type==="car"&&
   Math.abs(
    o.y-
    innerHeight*.78
   )<70&&
   Math.abs(
    laneX(o.lane)-
    playerX
   )<48
  ){

   endGame();

   return;

  }

 }

 /*
  Regelmäßige Autos.
  Zusätzlich wird verhindert,
  dass mehrere Autos zu dicht
  nebeneinander entstehen.
 */

 if(
  Math.random()<
  dt/1000/1.25
 ){

  spawnCar();

 }

 /*
  Münzen kommen sehr häufig.
 */

 if(
  Math.random()<
  dt/1000/.65
 ){

  spawnCoins();

 }

 document
 .getElementById("score")
 .textContent=
 Math.floor(score)
 .toLocaleString();

 document
 .getElementById("coins")
 .textContent=
 (
  playerData.coins+
  runCoins
 ).toLocaleString();

 document
 .getElementById("cityName")
 .textContent=
 cityData[
  playerData.city
 ].name;

}

function updatePowerHUD(){

 const hud=
 document.getElementById(
  "powerHUD"
 );

 const magnetHUD=
 document.getElementById(
  "magnetHUD"
 );

 const doubleHUD=
 document.getElementById(
  "doubleHUD"
 );

 if(magnet>0){

  hud.style.display="flex";

  magnetHUD.classList.remove(
   "hidden"
  );

  document
  .getElementById("magnetTime")
  .textContent=
  Math.ceil(magnet);

 }else{

  magnetHUD.classList.add(
   "hidden"
  );

 }

 if(doubleCoins>0){

  hud.style.display="flex";

  doubleHUD.classList.remove(
   "hidden"
  );

  document
  .getElementById("doubleTime")
  .textContent=
  Math.ceil(doubleCoins);

 }else{

  doubleHUD.classList.add(
   "hidden"
  );

 }

 if(
  magnet<=0&&
  doubleCoins<=0
 ){

  hud.style.display="none";

 }

}

/* =========================================================
   LOOP
========================================================= */

function loop(time){

 const dt=
 Math.min(
  40,
  time-last
 );

 last=time;

 drawWorld();

 objects.forEach(o=>{

  if(o.type==="car")
   drawCar(o);

  else if(o.type==="coin")
   drawCoin(o,time);

  else
   drawPower(o);

 });

 drawPlayer();

 update(dt);

 requestAnimationFrame(loop);

}

requestAnimationFrame(loop);

/* =========================================================
   CONTROLS
========================================================= */

document.addEventListener(
 "keydown",
 e=>{

  if(!running||paused)
   return;

  if(e.key==="ArrowLeft"){

   lane=
   Math.max(
    -1,
    lane-1
   );

  }

  if(e.key==="ArrowRight"){

   lane=
   Math.min(
    1,
    lane+1
   );

  }

 }
);

let touchStartX=0;

canvas.addEventListener(
 "touchstart",
 e=>{

  if(!running||paused)
   return;

  touchStartX=
  e.changedTouches[0]
  .clientX;

 },
 {passive:true}
);

canvas.addEventListener(
 "touchend",
 e=>{

  if(!running||paused)
   return;

  const end=
  e.changedTouches[0]
  .clientX;

  const diff=
  end-touchStartX;

  if(
   Math.abs(diff)<30
  )
   return;

  if(diff>0){

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

document
.getElementById("pause")
.onclick=()=>{

 if(!running)
  return;

 paused=true;

 document
 .getElementById("pause")
 .style.display="none";

 document
 .getElementById("pauseScreen")
 .classList.remove(
  "hidden"
 );

};

function resumeGame(){

 paused=false;

 document
 .getElementById("pauseScreen")
 .classList.add(
  "hidden"
 );

 document
 .getElementById("pause")
 .style.display="block";

 last=
 performance.now();

}

function quitGame(){

 running=false;

 document
 .getElementById("pauseScreen")
 .classList.add(
  "hidden"
 );

 document
 .getElementById("hud")
 .style.display="none";

 document
 .getElementById("powerHUD")
 .style.display="none";

 showMenu();

}

/* =========================================================
   GAME OVER
========================================================= */

async function endGame(){

 if(!running)
  return;

 running=false;

 playerData.coins+=
 runCoins;

 const finalScore=
 Math.floor(score);

 if(
  finalScore>
  playerData.highscore
 ){

  playerData.highscore=
  finalScore;

 }

 await saveOnlineScore();

 document
 .getElementById("hud")
 .style.display="none";

 document
 .getElementById("pause")
 .style.display="none";

 document
 .getElementById("powerHUD")
 .style.display="none";

 document
 .getElementById("gameOverText")
 .innerHTML=`

  <p>
   SCORE:
   <b>${finalScore.toLocaleString()}</b>
  </p>

  <p>
   COINS:
   <b>${runCoins.toLocaleString()}</b>
  </p>

  <p>
   HIGHSCORE:
   <b>${playerData.highscore.toLocaleString()}</b>
  </p>

 `;

 document
 .getElementById("gameOver")
 .classList.remove(
  "hidden"
 );

}

function restartGame(){

 document
 .getElementById("gameOver")
 .classList.add(
  "hidden"
 );

 startGame();

}

/* =========================================================
   STARTUP
========================================================= */

async function init(){

 const {
  data
 }=
 await supabaseClient.auth.getSession();

 if(data.session){

  playerData.id=
  data.session.user.id;

  playerData.email=
  data.session.user.email||"";

  await loadOnlinePlayer();

  if(!playerData.username){

   playerData.username=
   data.session.user.user_metadata
   ?.username||
   "Spieler";

  }

  showMenu();

 }

}

init();

</script>

</body>
</html>
