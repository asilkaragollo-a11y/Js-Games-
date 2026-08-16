<!DOCTYPE html>
<html lang="de">
<head>

<meta charset="UTF-8">

<meta name="viewport"
content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">

<title>Cube Rush</title>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

<style>

*{
 box-sizing:border-box;
}

body{
 margin:0;
 overflow:hidden;
 background:#050814;
 color:white;
 font-family:Arial,sans-serif;
}

canvas{
 position:fixed;
 inset:0;
 width:100%;
 height:100%;
}

.screen{
 position:fixed;
 inset:0;
 z-index:10;
 display:flex;
 flex-direction:column;
 align-items:center;
 justify-content:center;
 padding:20px;
 background:linear-gradient(#07182d,#02040a);
 overflow:auto;
}

.hidden{
 display:none!important;
}

h1{
 color:#00eaff;
 text-shadow:0 0 20px #00eaff;
}

input{
 width:300px;
 max-width:90%;
 padding:14px;
 margin:6px;
 border-radius:12px;
 border:1px solid #00eaff;
 background:#081522;
 color:white;
 font-size:16px;
 outline:none;
}

input:focus{
 border-color:#00eaff;
 box-shadow:0 0 15px #00eaff66;
}

button{
 padding:13px 22px;
 margin:6px;
 border:0;
 border-radius:12px;
 background:#00eaff;
 color:#001018;
 font-weight:bold;
 font-size:15px;
 cursor:pointer;
}

button:active{
 transform:scale(.95);
}

.logo{
 font-size:45px;
 font-weight:900;
 color:#00eaff;
 text-shadow:0 0 30px #00eaff;
 margin-bottom:5px;
 text-align:center;
}

.coins{
 color:#ffd900;
 font-weight:bold;
 font-size:20px;
 margin:10px;
}

.card{
 display:inline-block;
 width:160px;
 min-height:150px;
 margin:5px;
 padding:12px;
 background:#091522;
 border:1px solid #00eaff55;
 border-radius:15px;
 text-align:center;
}

.rank{
 width:350px;
 max-width:90vw;
 display:flex;
 justify-content:space-between;
 padding:12px;
 margin:4px;
 background:#091522;
 border-radius:10px;
}

#hud{
 position:fixed;
 z-index:5;
 top:10px;
 left:10px;
 right:10px;
 display:none;
 justify-content:space-between;
 pointer-events:none;
}

.hud{
 background:#06121dcc;
 border:1px solid #00eaff66;
 padding:8px 12px;
 border-radius:10px;
 font-weight:bold;
}

#pause{
 position:fixed;
 z-index:6;
 top:60px;
 right:10px;
 display:none;
}

.msg{
 min-height:20px;
 color:#a9c5d0;
 text-align:center;
 max-width:350px;
}

/* Eigene Symbole statt Apple-Emojis */

.symbol{
 display:inline-flex;
 align-items:center;
 justify-content:center;
 width:22px;
 height:22px;
 border-radius:50%;
 border:2px solid currentColor;
 font-size:11px;
 font-weight:900;
 margin-right:4px;
}

.coinSymbol{
 color:#ffd900;
}

.scoreSymbol{
 color:#00eaff;
}

.citySymbol{
 color:#ff55dd;
}

</style>

</head>

<body>

<canvas id="game"></canvas>


<div id="hud">

 <div class="hud">
  <span class="symbol coinSymbol">$</span>
  <span id="coins">0</span>
 </div>

 <div class="hud">
  <span class="symbol scoreSymbol">S</span>
  <span id="score">0</span>
 </div>

 <div class="hud">
  <span class="symbol citySymbol">C</span>
  <span id="cityName">Neon City</span>
 </div>

</div>


<button id="pause">II</button>


<!-- LOGIN -->

<div id="login" class="screen">

 <div class="logo">
  CUBE RUSH
 </div>

 <p>ONLINE EDITION</p>

 <input
  id="username"
  type="text"
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
  autocomplete="current-password"
 >

 <button id="registerButton">
  REGISTRIEREN
 </button>

 <button id="loginButton">
  ANMELDEN
 </button>

 <div id="loginMsg" class="msg"></div>

</div>


<!-- MENU -->

<div id="menu" class="screen hidden">

 <div class="logo">
  CUBE RUSH
 </div>

 <p>
  Spieler:
  <b id="player"></b>
 </p>

 <div class="coins">
  <span class="symbol coinSymbol">$</span>
  <span id="menuCoins">0</span>
 </div>

 <button onclick="startGame()">
  START
 </button>

 <button onclick="charactersMenu()">
  CHARAKTERE
 </button>

 <button onclick="citiesMenu()">
  STÄDTE
 </button>

 <button onclick="rankingMenu()">
  RANGLISTE
 </button>

 <button onclick="codeMenu()">
  CODE
 </button>

 <button onclick="logout()">
  ABMELDEN
 </button>

</div>


<!-- CHARACTERS -->

<div id="characters" class="screen hidden">

 <h1>CHARAKTERE</h1>

 <div class="coins">
  <span class="symbol coinSymbol">$</span>
  <span id="charCoins">0</span>
 </div>

 <div id="characterList"></div>

 <button onclick="showMenu()">
  ZURÜCK
 </button>

</div>


<!-- CITIES -->

<div id="cities" class="screen hidden">

 <h1>STÄDTE</h1>

 <div id="cityList"></div>

 <button onclick="showMenu()">
  ZURÜCK
 </button>

</div>


<!-- RANKING -->

<div id="ranking" class="screen hidden">

 <h1>RANGLISTE</h1>

 <div id="rankingList">
  Rangliste wird geladen...
 </div>

 <button onclick="showMenu()">
  ZURÜCK
 </button>

</div>


<!-- CODE -->

<div id="codes" class="screen hidden">

 <h1>CODE</h1>

 <input
  id="secretCode"
  inputmode="numeric"
  maxlength="3"
  placeholder="Code"
 >

 <button onclick="redeemCode()">
  EINLÖSEN
 </button>

 <div id="codeMsg" class="msg"></div>

 <button onclick="showMenu()">
  ZURÜCK
 </button>

</div>


<!-- PAUSE -->

<div id="pauseScreen" class="screen hidden">

 <h1>PAUSE</h1>

 <button onclick="resumeGame()">
  WEITERSPIELEN
 </button>

 <button onclick="quitGame()">
  MENÜ
 </button>

</div>


<!-- GAME OVER -->

<div id="gameOver" class="screen hidden">

 <h1>CRASH!</h1>

 <div id="gameOverText"></div>

 <button onclick="restartGame()">
  NOCHMAL
 </button>

 <button onclick="showMenu()">
  MENÜ
 </button>

</div>


<script>

/* =========================================
   SUPABASE
========================================= */

const SUPABASE_URL =
 "https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
 "sb_publishable_LaQ9ibF7A_s6pZUucnnuaw_yHVf0lvc";

const supabase =
 window.supabase.createClient(
  SUPABASE_URL,
  SUPABASE_KEY
);


/* =========================================
   SPIELER
========================================= */

let playerData = {

 username:"",
 coins:0,
 highscore:0,

 characters:[0],

 cities:[0],

 character:0,

 city:0

};


/* =========================================
   STÄDTE
========================================= */

const cityData=[

 {
  name:"Neon City",
  price:0,
  min:3,
  max:6
 },

 {
  name:"Tokyo Night",
  price:5000,
  min:7,
  max:10
 },

 {
  name:"Cyber Dubai",
  price:12000,
  min:10,
  max:14
 },

 {
  name:"Future New York",
  price:25000,
  min:14,
  max:18
 },

 {
  name:"Galaxy City",
  price:50000,
  min:18,
  max:25
 }

];


/* =========================================
   CHARAKTERE
========================================= */

const charData=[

 ["Blue",0],
 ["Volt",300],
 ["Fire",700],
 ["Ice",1200],
 ["Purple",2000],
 ["Poison",3000],
 ["Magma",4500],
 ["Thunder",6500],
 ["Void",9000],
 ["Omega",12000],
 ["Cyber",16000],
 ["Gold",22000],
 ["Ruby",28000],
 ["Diamond",35000],
 ["Galaxy",45000],
 ["Phantom",55000],
 ["Solar",70000],
 ["Nebula",85000],
 ["Aurora",100000],
 ["Cube God",150000]

];


/* =========================================
   LOKALER SPEICHER
========================================= */

function save(){

 localStorage.setItem(
  "cubeRushPlayer",
  JSON.stringify(playerData)
 );

}


/* =========================================
   LOGIN
========================================= */

document
 .getElementById("registerButton")
 .addEventListener(
  "click",
  register
 );


document
 .getElementById("loginButton")
 .addEventListener(
  "click",
  loginUser
 );


async function register(){

 const name =
  document
  .getElementById("username")
  .value
  .trim();

 const email =
  document
  .getElementById("email")
  .value
  .trim();

 const password =
  document
  .getElementById("password")
  .value;


 if(name.length < 3){

  loginMsg(
   "Benutzername muss mindestens 3 Zeichen haben."
  );

  return;

 }


 if(!email.includes("@")){

  loginMsg(
   "Bitte eine gültige E-Mail eingeben."
  );

  return;

 }


 if(password.length < 6){

  loginMsg(
   "Passwort muss mindestens 6 Zeichen haben."
  );

  return;

 }


 loginMsg(
  "Account wird erstellt..."
 );


 const {
  data,
  error
 } =
 await supabase.auth.signUp({

  email:email,

  password:password,

  options:{
   data:{
    username:name
   }
  }

 });


 if(error){

  loginMsg(
   error.message
  );

  return;

 }


 if(!data.user){

  loginMsg(
   "Account konnte nicht erstellt werden."
  );

  return;

 }


 const {
  error:profileError
 } =
 await supabase
 .from("profiles")
 .insert({

  id:data.user.id,

  username:name,

  coins:0,

  highscore:0

 });


 if(profileError){

  console.error(
   profileError
  );

  loginMsg(
   "Account erstellt, aber Profil konnte nicht gespeichert werden."
  );

  return;

 }


 playerData.username =
  name;

 playerData.coins=0;

 playerData.highscore=0;

 save();


 loginMsg(
  "Registrierung erfolgreich!"
 );


 showMenu();

}


async function loginUser(){

 const email =
  document
  .getElementById("email")
  .value
  .trim();

 const password =
  document
  .getElementById("password")
  .value;


 if(!email){

  loginMsg(
   "Bitte deine E-Mail eingeben."
  );

  return;

 }


 if(!password){

  loginMsg(
   "Bitte dein Passwort eingeben."
  );

  return;

 }


 loginMsg(
  "Anmeldung läuft..."
 );


 const {
  data,
  error
 } =
 await supabase.auth.signInWithPassword({

  email:email,

  password:password

 });


 if(error){

  loginMsg(
   "Anmeldung fehlgeschlagen: "+
   error.message
  );

  return;

 }


 const user =
  data.user;


 const {
  data:profile,
  error:profileError
 } =
 await supabase
 .from("profiles")
 .select(
  "username,coins,highscore"
 )
 .eq(
  "id",
  user.id
 )
 .single();


 if(profileError){

  loginMsg(
   "Profil konnte nicht geladen werden."
  );

  console.error(
   profileError
  );

  return;

 }


 playerData.username =
  profile.username;

 playerData.coins =
  profile.coins || 0;

 playerData.highscore =
  profile.highscore || 0;


 save();

 showMenu();

}


/* =========================================
   LOGIN STATUS PRÜFEN
========================================= */

async function checkSession(){

 const {
  data
 } =
 await supabase.auth.getSession();


 if(
  data &&
  data.session
 ){

  const user =
   data.session.user;


  const {
   data:profile
  } =
  await supabase
  .from("profiles")
  .select(
   "username,coins,highscore"
  )
  .eq(
   "id",
   user.id
  )
  .single();


  if(profile){

   playerData.username =
    profile.username;

   playerData.coins =
    profile.coins || 0;

   playerData.highscore =
    profile.highscore || 0;


   save();

   showMenu();

  }

 }

}


function loginMsg(text){

 document
 .getElementById(
  "loginMsg"
 )
 .textContent=text;

}


async function logout(){

 await supabase.auth.signOut();

 localStorage.removeItem(
  "cubeRushPlayer"
 );

 location.reload();

}


/* =========================================
   MENÜ
========================================= */

function hideAll(){

 document
 .querySelectorAll(".screen")
 .forEach(
  x =>
   x.classList.add(
    "hidden"
   )
 );

}


function showMenu(){

 hideAll();

 document
 .getElementById("menu")
 .classList
 .remove("hidden");

 document
 .getElementById("player")
 .textContent =
  playerData.username;

 document
 .getElementById("menuCoins")
 .textContent =
  playerData.coins
  .toLocaleString();

}


/* =========================================
   CHARAKTERE
========================================= */

function charactersMenu(){

 hideAll();

 const list =
  document
  .getElementById(
   "characterList"
  );

 list.innerHTML="";

 document
 .getElementById("charCoins")
 .textContent =
  playerData.coins
  .toLocaleString();


 charData.forEach(
  (char,index)=>{

   const card =
    document
    .createElement(
     "div"
    );

   card.className="card";


   const unlocked =
    playerData.characters
    .includes(index);


   card.innerHTML=`

    <h3>
     ${char[0]}
    </h3>

    <p>

     ${
      unlocked
      ?
      "FREIGESCHALTET"
      :
      "$ "+
      char[1]
      .toLocaleString()
     }

    </p>

   `;


   const button =
    document
    .createElement(
     "button"
    );


   if(unlocked){

    button.textContent =
     playerData.character===index
     ?
     "AKTIV"
     :
     "AUSWÄHLEN";


    button.onclick=()=>{

     playerData.character =
      index;

     save();

     charactersMenu();

    };

   }else{

    button.textContent =
     "KAUFEN";


    button.onclick=()=>{

     if(
      playerData.coins <
      char[1]
     ){

      alert(
       "Du hast nicht genug Coins."
      );

      return;

     }


     playerData.coins -=
      char[1];

     playerData.characters
      .push(index);

     playerData.character =
      index;


     save();

     charactersMenu();

    };

   }


   card.appendChild(
    button
   );

   list.appendChild(
    card
   );

  }
 );


 document
 .getElementById(
  "characters"
 )
 .classList
 .remove("hidden");

}


/* =========================================
   STÄDTE
========================================= */

function citiesMenu(){

 hideAll();

 const list =
  document
  .getElementById(
   "cityList"
  );

 list.innerHTML="";


 cityData.forEach(
  (city,index)=>{

   const card =
    document
    .createElement(
     "div"
    );

   card.className="card";


   const unlocked =
    playerData.cities
    .includes(index);


   card.innerHTML=`

    <h3>
     ${city.name}
    </h3>

    <p>
     ${city.min}-${city.max}
     Coins
    </p>

    <p>
     ${
      city.price===0
      ?
      "KOSTENLOS"
      :
      city.price
      .toLocaleString()+
      " Coins"
     }
    </p>

   `;


   const button =
    document
    .createElement(
     "button"
    );


   if(unlocked){

    button.textContent =
     playerData.city===index
     ?
     "AKTIV"
     :
     "AUSWÄHLEN";


    button.onclick=()=>{

     playerData.city =
      index;

     save();

     citiesMenu();

    };

   }else{

    button.textContent =
     "FREISCHALTEN";


    button.onclick=()=>{

     if(
      playerData.coins <
      city.price
     ){

      alert(
       "Du hast nicht genug Coins."
      );

      return;

     }


     playerData.coins -=
      city.price;

     playerData.cities
      .push(index);

     playerData.city =
      index;


     save();

     citiesMenu();

    };

   }


   card.appendChild(
    button
   );

   list.appendChild(
    card
   );

  }
 );


 document
 .getElementById(
  "cities"
 )
 .classList
 .remove("hidden");

}


/* =========================================
   CODE
========================================= */

function codeMenu(){

 hideAll();

 document
 .getElementById(
  "secretCode"
 )
 .value="";

 document
 .getElementById(
  "codeMsg"
 )
 .textContent="";


 document
 .getElementById(
  "codes"
 )
 .classList
 .remove(
  "hidden"
 );

}


function redeemCode(){

 const code =
  document
  .getElementById(
   "secretCode"
  )
  .value;


 const msg =
  document
  .getElementById(
   "codeMsg"
  );


 if(code==="110"){

  playerData.coins +=
   100000;

  playerData.characters =
   charData.map(
    (_,i)=>i
   );

  msg.textContent =
   "Code 110 aktiviert!";

 }


 else if(code==="112"){

  playerData.coins +=
   50000;

  playerData.characters =
   [0,1,2,3,4];

  msg.textContent =
   "Code 112 aktiviert!";

 }


 else{

  msg.textContent =
   "Falscher Code.";

 }


 save();

}


/* =========================================
   ONLINE RANGLISTE
========================================= */

async function rankingMenu(){

 hideAll();

 const list =
  document
  .getElementById(
   "rankingList"
  );


 list.innerHTML =
  "Rangliste wird geladen...";


 const {
  data,
  error
 } =
 await supabase
 .from("profiles")
 .select(
  "username,highscore"
 )
 .order(
  "highscore",
  {
   ascending:false
  }
 )
 .limit(50);


 if(error){

  console.error(error);

  list.innerHTML =
   "Rangliste konnte nicht geladen werden.";

  return;

 }


 list.innerHTML="";


 if(
  !data ||
  data.length===0
 ){

  list.innerHTML =
   "Noch keine Spieler.";

 }else{

  data.forEach(
   (p,index)=>{

    const row =
     document
     .createElement(
      "div"
     );

    row.className="rank";


    row.innerHTML=`

     <span>
      ${index+1}.
      ${escapeHTML(
       p.username
      )}
     </span>

     <b>
      ${Number(
       p.highscore || 0
      ).toLocaleString()}
     </b>

    `;


    list.appendChild(
     row
    );

   }
  );

 }


 document
 .getElementById(
  "ranking"
 )
 .classList
 .remove(
  "hidden"
 );

}


function escapeHTML(text){

 const div =
  document
  .createElement(
   "div"
  );

 div.textContent =
  text || "";

 return div.innerHTML;

}


/* =========================================
   SPIEL
========================================= */

const canvas =
 document.getElementById(
  "game"
 );

const ctx =
 canvas.getContext(
  "2d"
 );


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


let running=false;
let paused=false;
let score=0;
let runCoins=0;
let lane=0;
let playerX=0;
let objects=[];
let speed=250;
let last=performance.now();
let magnet=0;
let doubleCoins=0;
let powerTimer=10;


function road(){

 const width =
  Math.min(
   innerWidth*.84,
   600
  );


 return{

  left:
   (innerWidth-width)/2,

  width

 };

}


function laneX(n){

 const r =
  road();


 return r.left+
  r.width*
  (n+1.5)/3;

}


/* =========================================
   START
========================================= */

function startGame(){

 hideAll();

 running=true;

 paused=false;

 score=0;

 runCoins=0;

 lane=0;

 playerX =
  laneX(0);

 objects=[];

 speed=250;

 magnet=0;

 doubleCoins=0;

 powerTimer=10;


 document
 .getElementById(
  "hud"
 )
 .style.display="flex";


 document
 .getElementById(
  "pause"
 )
 .style.display="block";


 last =
  performance.now();

}


/* =========================================
   WELT
========================================= */

function drawWorld(){

 const r =
  road();


 ctx.fillStyle =
  "#02040a";

 ctx.fillRect(
  0,
  0,
  innerWidth,
  innerHeight
 );


 ctx.fillStyle =
  "#111820";

 ctx.fillRect(
  r.left,
  0,
  r.width,
  innerHeight
 );


 ctx.fillStyle =
  "#00eaff";

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


 const lw =
  r.width/3;


 const offset =
  (score*4)%100;


 ctx.fillStyle =
  "#ffffff55";


 for(
  let y=-100+offset;
  y<innerHeight;
  y+=100
 ){

  ctx.fillRect(
   r.left+lw-2,
   y,
   4,
   55
  );

  ctx.fillRect(
   r.left+lw*2-2,
   y,
   4,
   55
  );

 }

}


/* =========================================
   SPIELER
========================================= */

function drawPlayer(){

 const y =
  innerHeight*.72;


 ctx.save();

 ctx.translate(
  playerX,
  y
 );


 ctx.fillStyle =
  "#00eaff";

 ctx.shadowColor =
  "#00eaff";

 ctx.shadowBlur=25;


 ctx.fillRect(
  -28,
  -28,
  56,
  56
 );


 ctx.shadowBlur=0;

 ctx.fillStyle="#fff";


 ctx.fillRect(
  -18,
  -15,
  12,
  7
 );


 ctx.fillRect(
  7,
  -15,
  12,
  7
 );


 ctx.restore();

}


/* =========================================
   AUTOS
========================================= */

function spawnCar(){

 objects.push({

  type:"car",

  lane:
   Math.floor(
    Math.random()*3
   )-1,

  y:-150,

  color:[
   "#ff1744",
   "#168cff",
   "#ffffff",
   "#ff9900",
   "#8b4dff"
  ][
   Math.floor(
    Math.random()*5
   )
  ]

 });

}


function drawCar(car){

 const x =
  laneX(
   car.lane
  );


 ctx.save();

 ctx.translate(
  x,
  car.y
 );


 ctx.fillStyle =
  car.color;

 ctx.shadowColor =
  car.color;

 ctx.shadowBlur=18;


 ctx.beginPath();

 ctx.roundRect(
  -32,
  -50,
  64,
  100,
  10
 );

 ctx.fill();


 ctx.shadowBlur=0;


 ctx.fillStyle =
  "#102331";


 ctx.beginPath();

 ctx.roundRect(
  -21,
  -31,
  42,
  28,
  7
 );

 ctx.fill();


 ctx.fillStyle="#fff";


 ctx.fillRect(
  -22,
  28,
  14,
  7
 );

 ctx.fillRect(
  8,
  28,
  14,
  7
 );


 ctx.restore();

}


/* =========================================
   COINS
========================================= */

function spawnCoins(){

 const city =
  cityData[
   playerData.city
  ];


 const amount =
  city.min+
  Math.floor(
   Math.random()*
   (
    city.max-
    city.min+
    1
   )
  );


 const randomLane =
  Math.floor(
   Math.random()*3
  )-1;


 for(
  let i=0;
  i<amount;
  i++
 ){

  objects.push({

   type:"coin",

   lane:randomLane,

   y:-50-i*75

  });

 }

}


function drawCoin(
 o,
 time
){

 let x =
  laneX(
   o.lane
  );

 let y =
  o.y;


 if(magnet>0){

  x +=
   (
    playerX-x
   )*.18;

  y +=
   (
    innerHeight*.72-y
   )*.18;

 }


 const scale =
  .5+
  Math.abs(
   Math.cos(
    time*.008
   )
  )*.5;


 ctx.save();

 ctx.translate(
  x,
  y
 );

 ctx.scale(
  scale,
  1
 );


 ctx.fillStyle =
  "#ffd900";

 ctx.shadowColor =
  "#ffd900";

 ctx.shadowBlur=20;


 ctx.beginPath();

 ctx.arc(
  0,
  0,
  15,
  0,
  Math.PI*2
 );

 ctx.fill();


 ctx.restore();

}


/* =========================================
   POWERUPS
========================================= */

function spawnPower(){

 objects.push({

  type:
   Math.random()<.5
   ?
   "magnet"
   :
   "double",

  lane:
   Math.floor(
    Math.random()*3
   )-1,

  y:-80

 });

}


function drawPower(o){

 const x =
  laneX(
   o.lane
  );


 ctx.save();

 ctx.translate(
  x,
  o.y
 );


 ctx.font =
  "bold 30px Arial";

 ctx.textAlign =
  "center";

 ctx.textBaseline =
  "middle";


 ctx.fillStyle =
  o.type==="magnet"
  ?
  "#00eaff"
  :
  "#ffd900";


 ctx.shadowBlur=20;

 ctx.shadowColor =
  ctx.fillStyle;


 ctx.fillText(
  o.type==="magnet"
  ?
  "M"
  :
  "x2",
  0,
  0
 );


 ctx.restore();

}


/* =========================================
   UPDATE
========================================= */

function update(dt){

 if(
  !running ||
  paused
 )
  return;


 const seconds =
  dt/1000;


 score +=
  seconds*10;


 speed =
  Math.min(
   700,
   speed+
   seconds*4
  );


 playerX +=
  (
   laneX(lane)-
   playerX
  )*
  Math.min(
   1,
   seconds*12
  );


 if(magnet>0)
  magnet -=
   seconds;


 if(doubleCoins>0)
  doubleCoins -=
   seconds;


 powerTimer -=
  seconds;


 if(powerTimer<=0){

  spawnPower();

  powerTimer =
   15+
   Math.random()*15;

 }


 for(
  let i=
   objects.length-1;
  i>=0;
  i--
 ){

  const o =
   objects[i];


  o.y +=
   speed*
   seconds;


  if(
   o.y>
   innerHeight+150
  ){

   objects.splice(
    i,
    1
   );

   continue;

  }


  /* POWERUP */

  if(

   (
    o.type==="magnet" ||
    o.type==="double"
   )

   &&

   Math.abs(
    o.y-
    innerHeight*.72
   )<55

   &&

   Math.abs(
    laneX(o.lane)-
    playerX
   )<45

  ){

   if(
    o.type==="magnet"
   )
    magnet=30;


   if(
    o.type==="double"
   )
    doubleCoins=30;


   objects.splice(
    i,
    1
   );

   continue;

  }


  /* MAGNET */

  if(
   o.type==="coin" &&
   magnet>0
  ){

   o.lane=lane;

  }


  /* COIN */

  if(

   o.type==="coin"

   &&

   Math.abs(
    o.y-
    innerHeight*.72
   )<50

   &&

   Math.abs(
    laneX(o.lane)-
    playerX
   )<45

  ){

   runCoins +=
    doubleCoins>0
    ?
    2
    :
    1;


   objects.splice(
    i,
    1
   );

   continue;

  }


  /* AUTO */

  if(

   o.type==="car"

   &&

   Math.abs(
    o.y-
    innerHeight*.72
   )<65

   &&

   Math.abs(
    laneX(o.lane)-
    playerX
   )<45

  ){

   endGame();

   return;

  }

 }


 if(
  Math.random()<
  dt/1000/1.2
 ){

  spawnCar();

 }


 if(
  Math.random()<
  dt/1000/1.8
 ){

  spawnCoins();

 }


 document
 .getElementById(
  "score"
 )
 .textContent =
  Math.floor(
   score
  )
  .toLocaleString();


 document
 .getElementById(
  "coins"
 )
 .textContent =
  (
   playerData.coins+
   runCoins
  )
  .toLocaleString();


 document
 .getElementById(
  "cityName"
 )
 .textContent =
  cityData[
   playerData.city
  ].name;

}


/* =========================================
   GAME LOOP
========================================= */

function loop(time){

 const dt =
  Math.min(
   40,
   time-last
  );


 last=time;


 drawWorld();


 objects.forEach(
  o=>{

   if(
    o.type==="car"
   )
    drawCar(o);

   else if(
    o.type==="coin"
   )
    drawCoin(
     o,
     time
    );

   else
    drawPower(o);

  }
 );


 drawPlayer();


 update(dt);


 requestAnimationFrame(
  loop
 );

}


requestAnimationFrame(
 loop
 );


/* =========================================
   STEUERUNG
========================================= */

document.addEventListener(
 "keydown",
 e=>{

  if(
   !running ||
   paused
  )
   return;


  if(
   e.key==="ArrowLeft"
  ){

   lane =
    Math.max(
     -1,
     lane-1
    );

  }


  if(
   e.key==="ArrowRight"
  ){

   lane =
    Math.min(
     1,
     lane+1
    );

  }

 }
);


let touchStart=0;


canvas.addEventListener(
 "touchstart",
 e=>{

  if(
   !running ||
   paused
  )
   return;


  touchStart =
   e.changedTouches[0]
   .clientX;

 },
 {
  passive:true
 }
);


canvas.addEventListener(
 "touchend",
 e=>{

  if(
   !running ||
   paused
  )
   return;


  const end =
   e.changedTouches[0]
   .clientX;


  const difference =
   end-touchStart;


  if(
   Math.abs(
    difference
   )<30
  )
   return;


  if(
   difference>0
  ){

   lane =
    Math.min(
     1,
     lane+1
    );

  }else{

   lane =
    Math.max(
     -1,
     lane-1
    );

  }

 },
 {
  passive:true
 }
);


/* =========================================
   PAUSE
========================================= */

document
 .getElementById(
  "pause"
 )
 .onclick=()=>{

  if(!running)
   return;


  paused=true;


  document
  .getElementById(
   "pause"
  )
  .style.display="none";


  document
  .getElementById(
   "pauseScreen"
  )
  .classList
  .remove(
   "hidden"
  );

 };


function resumeGame(){

 paused=false;


 document
 .getElementById(
  "pauseScreen"
 )
 .classList
 .add(
  "hidden"
 );


 document
 .getElementById(
  "pause"
 )
 .style.display="block";


 last =
  performance.now();

}


function quitGame(){

 running=false;


 document
 .getElementById(
  "pauseScreen"
 )
 .classList
 .add(
  "hidden"
 );


 document
 .getElementById(
  "hud"
 )
 .style.display="none";


 showMenu();

}


/* =========================================
   GAME OVER + ONLINE SPEICHERN
========================================= */

async function endGame(){

 if(!running)
  return;


 running=false;


 playerData.coins +=
  runCoins;


 const finalScore =
  Math.floor(
   score
  );


 if(
  finalScore >
  playerData.highscore
 ){

  playerData.highscore =
   finalScore;

 }


 save();


 const {
  data
 } =
 await supabase.auth.getUser();


 if(
  data &&
  data.user
 ){

  await supabase
  .from("profiles")
  .update({

   coins:
    playerData.coins,

   highscore:
    playerData.highscore

  })
  .eq(
   "id",
   data.user.id
  );

 }


 document
 .getElementById(
  "hud"
 )
 .style.display="none";


 document
 .getElementById(
  "pause"
 )
 .style.display="none";


 document
 .getElementById(
  "gameOverText"
 )
 .innerHTML=`

  <p>
   Score:
   <b>
    ${finalScore.toLocaleString()}
   </b>
  </p>

  <p>
   Coins:
   <b>
    ${runCoins}
   </b>
  </p>

  <p>
   Highscore:
   <b>
    ${playerData.highscore.toLocaleString()}
   </b>
  </p>

 `;


 document
 .getElementById(
  "gameOver"
 )
 .classList
 .remove(
  "hidden"
 );

}


function restartGame(){

 document
 .getElementById(
  "gameOver"
 )
 .classList
 .add(
  "hidden"
 );

 startGame();

}


/* =========================================
   START
========================================= */

checkSession();

</script>

</body>
</html>
