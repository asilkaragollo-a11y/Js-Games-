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
  background:#02050a;
  color:white;
  font-family:Arial,sans-serif;
}

button,input{
  font-family:inherit;
}

button{
  border:0;
  cursor:pointer;
}

.screen{
  position:fixed;
  inset:0;
  z-index:20;
  display:flex;
  align-items:center;
  justify-content:center;
  padding:16px;
  background:
    radial-gradient(circle at 50% 0%,#073f52,#03131c 48%,#010307);
}

.card{
  width:min(470px,100%);
  max-height:94vh;
  overflow:auto;
  padding:24px 18px;
  border-radius:28px;
  background:rgba(3,10,17,.97);
  border:1px solid #00eaff55;
  box-shadow:0 0 45px #00eaff18;
  text-align:center;
}

.hidden{
  display:none!important;
}

.logo{
  font-size:38px;
  font-weight:1000;
  letter-spacing:-2px;
  color:#00eaff;
  text-shadow:0 0 12px #00eaff,0 0 30px #00eaff;
}

.subtitle{
  color:#a6c1ca;
  margin:8px 0 18px;
}

input{
  width:100%;
  height:54px;
  margin:6px 0;
  padding:0 15px;
  border-radius:14px;
  border:1px solid #00eaff55;
  outline:none;
  background:#f2ffbd;
  color:#111;
  font-size:16px;
}

.btn{
  width:100%;
  min-height:53px;
  margin:6px 0;
  border-radius:15px;
  font-weight:900;
  font-size:15px;
}

.primary{
  background:linear-gradient(135deg,#00eaff,#168dff);
  color:#001018;
}

.secondary{
  background:#071b25;
  color:white;
  border:1px solid #00eaff44;
}

.message{
  min-height:22px;
  margin-top:8px;
  color:#8df6ff;
  font-size:13px;
}

.stats{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:8px;
  margin:15px 0;
}

.stat{
  background:#071b25;
  border:1px solid #00eaff22;
  border-radius:14px;
  padding:12px;
}

.stat small{
  display:block;
  color:#76939d;
}

.stat strong{
  display:block;
  margin-top:4px;
  font-size:20px;
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

.grid{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:9px;
}

.item{
  padding:9px;
  border-radius:17px;
  background:#071820;
  border:1px solid #00eaff22;
}

.item.selected{
  border-color:#00eaff;
  box-shadow:0 0 22px #00eaff22;
}

.preview{
  height:100px;
  display:flex;
  justify-content:center;
  align-items:center;
}

.character{
  width:55px;
  height:55px;
  border-radius:16px;
  position:relative;
  box-shadow:0 0 25px currentColor;
}

.character:before,
.character:after{
  content:"";
  position:absolute;
  background:#061018;
}

.character:before{
  width:9px;
  height:9px;
  border-radius:50%;
  left:13px;
  top:17px;
  box-shadow:20px 0 #061018;
}

.character:after{
  width:24px;
  height:5px;
  border-radius:10px;
  left:15px;
  bottom:12px;
}

.name{
  font-weight:900;
}

.price{
  color:#ffd600;
  font-size:12px;
  margin:3px;
}

.mult{
  color:#91aab3;
  font-size:11px;
}

.city{
  height:90px;
  border-radius:12px;
  overflow:hidden;
  position:relative;
  margin-bottom:7px;
}

.building{
  position:absolute;
  bottom:0;
  background:#06131b;
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
  top:9px;
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
  top:54px;
  right:9px;
  z-index:7;
  width:58px;
  height:38px;
  border-radius:11px;
  background:#071b25dd;
  color:white;
  border:1px solid #00eaff55;
  display:none;
}

#power{
  position:fixed;
  top:98px;
  left:50%;
  transform:translateX(-50%);
  z-index:6;
  padding:7px 13px;
  border-radius:14px;
  background:#031119dd;
  border:1px solid #00eaff66;
  font-size:13px;
  font-weight:900;
  display:none;
}

/* PAUSE */

#pauseMenu{
  position:fixed;
  inset:0;
  z-index:30;
  display:none;
  align-items:center;
  justify-content:center;
  background:rgba(0,0,0,.72);
  backdrop-filter:blur(7px);
}

.pauseCard{
  width:min(350px,90%);
  padding:28px 20px;
  border-radius:26px;
  background:#041019f5;
  border:1px solid #00eaff66;
  box-shadow:0 0 45px #00eaff22;
  text-align:center;
}

.pauseTitle{
  font-size:32px;
  font-weight:1000;
  color:#00eaff;
  margin-bottom:18px;
}

.pauseBtn{
  width:100%;
  min-height:55px;
  margin:6px 0;
  border-radius:15px;
  font-weight:900;
  font-size:16px;
}

.resume{
  background:#00eaff;
  color:#001018;
}

.giveup{
  background:#271018;
  color:#ff6175;
  border:1px solid #ff174455;
}

.rank{
  text-align:left;
  padding:8px;
  border-bottom:1px solid #ffffff12;
}
</style>
</head>

<body>

<!-- LOGIN -->

<section id="login" class="screen">
<div class="card">

<div class="logo">CUBE RUSH</div>

<div class="subtitle">
Dein Endless Runner
</div>

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


<!-- REGISTRIERUNG -->

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


<!-- HAUPTMENÜ -->

<section id="menu" class="screen hidden">
<div class="card">

<div class="logo">CUBE RUSH</div>

<div class="subtitle">
Willkommen, <b id="playerName"></b>
</div>

<div class="stats">

<div class="stat">
<small>MÜNZEN</small>
<strong id="coins">0</strong>
</div>

<div class="stat">
<small>HIGHSCORE</small>
<strong id="highscore">0</strong>
</div>

</div>

<button id="start" class="btn primary">
SPIEL STARTEN
</button>

<button id="shop" class="btn secondary">
SHOP
</button>

<button id="rankBtn" class="btn secondary">
RANGLISTE
</button>

<button id="codeBtn" class="btn secondary">
CODE
</button>

<button id="logout" class="btn secondary">
ABMELDEN
</button>

</div>
</section>


<!-- SHOP -->

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


<!-- RANGLISTE -->

<section id="rankScreen" class="screen hidden">
<div class="card">

<div class="logo">RANGLISTE</div>

<div id="rankList">
Lade...
</div>

<button id="rankBack" class="btn secondary">
ZURÜCK
</button>

</div>
</section>


<!-- CODES -->

<section id="codeScreen" class="screen hidden">
<div class="card">

<div class="logo">ADMIN CODE</div>

<input
 id="codeInput"
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


<!-- GAME OVER -->

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
NOCHMAL
</button>

<button id="gameMenu" class="btn secondary">
MENÜ
</button>

</div>
</section>


<!-- SPIEL -->

<canvas id="game"></canvas>

<div id="hud">

<div class="hud">
MÜNZEN <span id="hudCoins">0</span>
</div>

<div class="hud">
SCORE <span id="hudScore">0</span>
</div>

<div class="hud" id="hudWorld">
NEON CITY
</div>

</div>

<div id="power"></div>

<button id="pause">
PAUSE
</button>


<!-- PAUSE-MENÜ -->

<div id="pauseMenu">

<div class="pauseCard">

<div class="pauseTitle">
PAUSE
</div>

<button id="resumeBtn" class="pauseBtn resume">
WEITERSPIELEN
</button>

<button id="giveUpBtn" class="pauseBtn giveup">
AUFGEBEN
</button>

</div>

</div>


<script>

/* =========================================
SUPABASE
========================================= */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";

let accessToken="";
let refreshToken="";
let player=null;


/* =========================================
CHARAKTERE
========================================= */

const characters=[

{name:"Pulse",price:0,mult:1,color:"#00eaff"},
{name:"Volt",price:500,mult:1.1,color:"#00ff88"},
{name:"Flame",price:1000,mult:1.2,color:"#ff304f"},
{name:"Aqua",price:2000,mult:1.3,color:"#008cff"},
{name:"Violet",price:3500,mult:1.4,color:"#b000ff"},
{name:"Gold",price:5000,mult:1.5,color:"#ffd000"},
{name:"Inferno",price:7500,mult:1.7,color:"#ff6500"},
{name:"Frost",price:10000,mult:1.9,color:"#9df7ff"},
{name:"Prism",price:15000,mult:2,color:"#ff4dff"},
{name:"Toxic",price:20000,mult:2.2,color:"#aaff00"},
{name:"Diamond",price:30000,mult:2.5,color:"#c8f5ff"},
{name:"Galaxy",price:45000,mult:2.8,color:"#765cff"},
{name:"Plasma",price:60000,mult:3,color:"#ff00a8"},
{name:"Cyber",price:80000,mult:3.3,color:"#00ffd0"},
{name:"Royal",price:100000,mult:3.6,color:"#ffcc00"},
{name:"Shadow",price:125000,mult:4,color:"#777b8a"},
{name:"Cosmic",price:160000,mult:4.5,color:"#915cff"},
{name:"Legend",price:220000,mult:5,color:"#ff8c00"},
{name:"Infinity",price:350000,mult:6,color:"#ffffff"},
{name:"Cube God",price:500000,mult:7,color:"#00ffff"}

];


/* =========================================
10 STÄDTE
========================================= */

const worlds=[

{
name:"Neon City",
price:0,
mult:1,
min:3,
max:6,
sky:"#06485a",
ground:"#0c1720"
},

{
name:"Tokyo",
price:2500,
mult:1.5,
min:6,
max:10,
sky:"#481060",
ground:"#17091c"
},

{
name:"New York",
price:7500,
mult:2,
min:10,
max:14,
sky:"#15365e",
ground:"#101820"
},

{
name:"Miami",
price:15000,
mult:2.5,
min:14,
max:18,
sky:"#167081",
ground:"#101d20"
},

{
name:"Dubai",
price:30000,
mult:3,
min:18,
max:23,
sky:"#70451d",
ground:"#20130a"
},

{
name:"Cyber City",
price:60000,
mult:4,
min:23,
max:28,
sky:"#31005d",
ground:"#0b0614"
},

{
name:"Galaxy City",
price:120000,
mult:5,
min:28,
max:34,
sky:"#05002f",
ground:"#05030e"
},

{
name:"Mars City",
price:250000,
mult:6,
min:34,
max:40,
sky:"#74170e",
ground:"#28100d"
},

{
name:"Crystal City",
price:500000,
mult:8,
min:40,
max:48,
sky:"#104b65",
ground:"#06151e"
},

{
name:"Infinity City",
price:1000000,
mult:10,
min:48,
max:60,
sky:"#000022",
ground:"#02020b"
}

];


/* =========================================
HELPER
========================================= */

const $=id=>document.getElementById(id);

function show(id){

document.querySelectorAll(".screen")
.forEach(s=>s.classList.add("hidden"));

$(id).classList.remove("hidden");
}

function localKey(){

return "cubeRush_"+player.id;
}

function getData(){

try{
return JSON.parse(
localStorage.getItem(localKey())||"{}"
);
}catch{
return {};
}
}

function saveData(d){

localStorage.setItem(
localKey(),
JSON.stringify(d)
);
}


/* =========================================
SUPABASE REQUEST
========================================= */

async function api(path,options={}){

const headers={
apikey:SUPABASE_KEY,
"Content-Type":"application/json",
...(accessToken?
{Authorization:"Bearer "+accessToken}:{}),
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
data?.msg||
data?.message||
data?.error_description||
text||
"Fehler"
);
}

return data;
}


/* =========================================
SESSION SPEICHERN
========================================= */

function saveSession(session){

if(!session)return;

accessToken=session.access_token||"";
refreshToken=session.refresh_token||"";

localStorage.setItem(
"cubeRush_session",
JSON.stringify({
access_token:accessToken,
refresh_token:refreshToken
})
);
}


function loadSavedSession(){

try{

const s=JSON.parse(
localStorage.getItem("cubeRush_session")
);

if(!s)return null;

return s;

}catch{

return null;

}
}


/* =========================================
ACCOUNT
========================================= */

$("registerOpen").onclick=()=>{
show("register");
};

$("backLogin").onclick=()=>{
show("login");
};


$("registerBtn").onclick=async()=>{

const username=$("username").value.trim();
const email=$("regEmail").value.trim();
const password=$("regPassword").value;

if(username.length<3){

$("regMsg").textContent=
"Benutzername muss mindestens 3 Zeichen haben.";

return;
}

if(password.length<6){

$("regMsg").textContent=
"Passwort muss mindestens 6 Zeichen haben.";

return;
}

try{

const result=await api(
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

if(!result.access_token){

$("regMsg").textContent=
"Account erstellt. Falls eine Bestätigung verlangt wird, ist 'Confirm email' in Supabase noch aktiviert.";

return;
}

saveSession(result);

accessToken=result.access_token;

player={
id:result.user.id,
username,
coins:0,
highscore:0
};

await createPlayer();

openMenu();

}catch(error){

$("regMsg").textContent=
"FEHLER: "+error.message;

}

};


/* =========================================
LOGIN
========================================= */

$("loginBtn").onclick=async()=>{

const email=$("email").value.trim();
const password=$("password").value;

if(!email||!password){

$("loginMsg").textContent=
"Bitte E-Mail und Passwort eingeben.";

return;
}

try{

const result=await api(
"/auth/v1/token?grant_type=password",
{
method:"POST",
body:JSON.stringify({
email,
password
})
}
);

saveSession(result);

accessToken=result.access_token;

player={
id:result.user.id,
username:
result.user.user_metadata?.username||
email.split("@")[0],
coins:0,
highscore:0
};

await loadPlayer();

openMenu();

}catch(error){

$("loginMsg").textContent=
"FEHLER: "+error.message;

}

};


/* =========================================
ACCOUNT AUTOMATISCH LADEN
========================================= */

async function restoreSession(){

const session=loadSavedSession();

if(!session)return;

try{

accessToken=session.access_token;
refreshToken=session.refresh_token;

const user=await api(
"/auth/v1/user"
);

if(!user?.id)return;

player={
id:user.id,
username:
user.user_metadata?.username||
"user",
coins:0,
highscore:0
};

await loadPlayer();

openMenu();

}catch(error){

localStorage.removeItem("cubeRush_session");

accessToken="";
refreshToken="";

}

}


/* =========================================
PLAYER
========================================= */

async function createPlayer(){

try{

await api(
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

}catch(error){

console.log(error);

}

}


async function loadPlayer(){

try{

const rows=await api(
"/rest/v1/Players?id=eq."+
encodeURIComponent(player.id)+
"&select=*"
);

if(rows&&rows.length){

player.username=
rows[0].username||
player.username;

player.coins=
Number(rows[0].coins||0);

player.highscore=
Number(rows[0].highscore||0);

}

}catch(error){

console.log(error);

}

}


async function savePlayer(){

try{

await api(
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

}catch(error){

console.log(error);

}

}


/* =========================================
MENU
========================================= */

function openMenu(){

$("playerName").textContent=
player.username;

$("coins").textContent=
Math.floor(player.coins).toLocaleString();

$("highscore").textContent=
Math.floor(player.highscore).toLocaleString();

show("menu");

}


$("logout").onclick=()=>{

localStorage.removeItem("cubeRush_session");

accessToken="";
refreshToken="";
player=null;

$("email").value="";
$("password").value="";

show("login");

};


/* =========================================
SHOP
========================================= */

$("shop").onclick=()=>{
renderCharacters();
show("shopScreen");
};

$("charTab").onclick=()=>{
$("charTab").classList.add("active");
$("cityTab").classList.remove("active");
renderCharacters();
};

$("cityTab").onclick=()=>{
$("cityTab").classList.add("active");
$("charTab").classList.remove("active");
renderWorlds();
};

$("shopBack").onclick=openMenu;


function renderCharacters(){

const grid=$("shopGrid");
const d=getData();

const owned=d.chars||[0];
const selected=Number(d.char||0);

grid.innerHTML="";

characters.forEach((character,index)=>{

const item=document.createElement("div");

item.className=
"item"+
(selected===index?" selected":"");

item.innerHTML=`

<div class="preview">

<div
 class="character"
 style="
 background:${character.color};
 color:${character.color};
 ">
</div>

</div>

<div class="name">
${character.name}
</div>

<div class="price">
${character.price===0?
"KOSTENLOS":
"🪙 "+character.price.toLocaleString()}
</div>

<div class="mult">
Münzen ×${character.mult}
</div>

<button class="btn ${
owned.includes(index)?
"secondary":
"primary"
}">
${
selected===index?
"AUSGEWÄHLT":
owned.includes(index)?
"AUSWÄHLEN":
"KAUFEN"
}
</button>

`;

item.querySelector("button").onclick=
async()=>{

const data=getData();

data.chars=data.chars||[0];

if(data.chars.includes(index)){

data.char=index;

saveData(data);

renderCharacters();

return;
}

if(player.coins<character.price){

alert("Du hast nicht genug Münzen.");

return;
}

player.coins-=character.price;

data.chars.push(index);
data.char=index;

saveData(data);

await savePlayer();

renderCharacters();

};

grid.appendChild(item);

});

}


/* =========================================
STÄDTE SHOP
========================================= */

function renderWorlds(){

const grid=$("shopGrid");
const d=getData();

const owned=d.worlds||[0];
const selected=Number(d.world||0);

grid.innerHTML="";

worlds.forEach((world,index)=>{

const item=document.createElement("div");

item.className=
"item"+
(selected===index?" selected":"");

let buildings="";

for(let i=0;i<5;i++){

buildings+=`

<div
class="building"
style="
left:${i*21+3}%;
width:${18+i*2}px;
height:${35+i*10}px;
">
</div>

`;

}

item.innerHTML=`

<div
 class="city"
 style="
 background:
 linear-gradient(
 ${world.sky},
 ${world.ground}
 );
 ">
 ${buildings}
</div>

<div class="name">
${world.name}
</div>

<div class="price">
${world.price===0?
"KOSTENLOS":
"🪙 "+world.price.toLocaleString()}
</div>

<div class="mult">
Münzen ×${world.mult}
</div>

<button class="btn ${
owned.includes(index)?
"secondary":
"primary"
}">
${
selected===index?
"AUSGEWÄHLT":
owned.includes(index)?
"AUSWÄHLEN":
"KAUFEN"
}
</button>

`;

item.querySelector("button").onclick=
async()=>{

const data=getData();

data.worlds=data.worlds||[0];

if(data.worlds.includes(index)){

data.world=index;

saveData(data);

renderWorlds();

return;
}

if(player.coins<world.price){

alert("Nicht genug Münzen.");

return;
}

player.coins-=world.price;

data.worlds.push(index);
data.world=index;

saveData(data);

await savePlayer();

renderWorlds();

};

grid.appendChild(item);

});

}


/* =========================================
ADMIN CODES
========================================= */

$("codeBtn").onclick=()=>{
$("codeInput").value="";
$("codeMsg").textContent="";
show("codeScreen");
};

$("codeBack").onclick=openMenu;


$("useCode").onclick=async()=>{

const code=$("codeInput").value.trim();
const data=getData();

if(code==="110"){

player.coins=1000000;
player.highscore=1000000;

data.chars=
characters.map((_,i)=>i);

data.worlds=
worlds.map((_,i)=>i);

data.char=0;
data.world=0;

saveData(data);

await savePlayer();

$("codeMsg").textContent=
"Code 110 aktiviert.";

return;
}


if(code==="112"){

player.coins=100000;

data.chars=[0,1,2,3,4];
data.worlds=[0,1,2];

data.char=0;
data.world=0;

saveData(data);

await savePlayer();

$("codeMsg").textContent=
"Code 112 aktiviert.";

return;
}


$("codeMsg").textContent=
"Ungültiger Code.";

};


/* =========================================
RANGLISTE
========================================= */

$("rankBtn").onclick=async()=>{

show("rankScreen");

try{

const rows=await api(
"/rest/v1/Players"+
"?select=username,highscore"+
"&order=highscore.desc"+
"&limit=50"
);

$("rankList").innerHTML=
rows.map((row,index)=>`

<div class="rank">
<b>${index+1}.</b>
${String(row.username)}
—
${Number(row.highscore||0).toLocaleString()}
</div>

`).join("");

}catch(error){

$("rankList").textContent=
"Fehler: "+error.message;

}

};

$("rankBack").onclick=openMenu;


/* =========================================
GAME ENGINE
========================================= */

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

let lastTime=performance.now();


function roadWidth(){

return Math.min(
W*.86,
650
);

}


function roadLeft(){

return (W-roadWidth())/2;

}


function laneWidth(){

return roadWidth()/3;

}


function laneX(index){

return roadLeft()+
laneWidth()*(index+1)+
laneWidth()/2;

}


/* =========================================
GAME START
========================================= */

$("start").onclick=startGame;

function startGame(){

document.querySelectorAll(".screen")
.forEach(s=>s.classList.add("hidden"));

canvas.style.display="block";

$("hud").style.display="flex";
$("pause").style.display="block";

running=true;
paused=false;

$("pauseMenu").style.display="none";

lane=0;

score=0;
earned=0;

speed=300;

objects=[];

coinTimer=.5;
obstacleTimer=1;

doubleTime=0;
magnetTime=0;

lastTime=performance.now();

$("hudWorld").textContent=
worlds[Number(getData().world||0)].name;

updatePower();

}


/* =========================================
MÜNZEN
========================================= */

function coinAmount(){

const world=
worlds[Number(getData().world||0)];

return Math.floor(
world.min+
Math.random()*
(world.max-world.min+1)
);

}


/* =========================================
FREIE SPUR
========================================= */

function freeLane(y){

const possible=[-1,0,1]
.filter(laneNumber=>{

return !objects.some(object=>

object.lane===laneNumber&&
Math.abs(object.y-y)<180

);

});

if(!possible.length)return null;

return possible[
Math.floor(Math.random()*possible.length)
];

}


/* =========================================
MÜNZEN SPAWNEN
========================================= */

function spawnCoins(){

const amount=coinAmount();

let selectedLane=
Math.floor(Math.random()*3)-1;

for(let i=0;i<amount;i++){

const y=-50-i*48;

const blocked=
objects.some(object=>

object.type==="car"&&
object.lane===selectedLane&&
Math.abs(object.y-y)<180

);

if(blocked){

const free=freeLane(y);

if(free!==null){
selectedLane=free;
}

}

objects.push({
type:"coin",
lane:selectedLane,
y
});

}

}


/* =========================================
AUTOS
========================================= */

function spawnCar(){

const laneNumber=
freeLane(-130);

if(laneNumber===null)return;

const colors=[
"#ff1744",
"#008cff",
"#ff9800",
"#ffffff",
"#8e44ad"
];

objects.push({

type:"car",
lane:laneNumber,
y:-130,

color:
colors[
Math.floor(Math.random()*colors.length)
]

});

}


/* =========================================
POWERUPS
========================================= */

function spawnPower(type){

const laneNumber=
freeLane(-100);

if(laneNumber===null)return;

objects.push({

type,
lane:laneNumber,
y:-100

});

}


/* =========================================
HINTERGRUND
========================================= */

function drawBackground(){

const world=
worlds[Number(getData().world||0)];

const gradient=
ctx.createLinearGradient(0,0,0,H);

gradient.addColorStop(0,world.sky);
gradient.addColorStop(1,world.ground);

ctx.fillStyle=gradient;
ctx.fillRect(0,0,W,H);


/* Sterne */

if(Number(getData().world||0)>=5){

ctx.fillStyle="#ffffff99";

for(let i=0;i<70;i++){

const x=(i*97)%W;
const y=(i*53)%H;

ctx.fillRect(x,y,2,2);

}

}


/* Gebäude */

for(let i=0;i<24;i++){

const width=20+(i%5)*10;

const height=
70+((i*43)%220);

const x=i*(W/24);

ctx.fillStyle=
"#071820";

ctx.fillRect(
x,
H-height-95,
width,
height
);

ctx.fillStyle="#00eaff66";

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


/* Straße */

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


const lw=laneWidth();

const offset=
(score*10)%90;

ctx.fillStyle="#ffffff66";

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


/* =========================================
OBJEKTE
========================================= */

function drawObjects(){

for(const object of objects){

const x=laneX(object.lane);

if(object.type==="coin"){

ctx.save();

ctx.shadowColor="#ffd000";
ctx.shadowBlur=20;

ctx.fillStyle="#ffd000";

ctx.beginPath();

ctx.arc(
x,
object.y,
13,
0,
Math.PI*2
);

ctx.fill();

ctx.fillStyle="#fff5a0";

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

}


else if(object.type==="double"){

ctx.save();

ctx.shadowColor="#00eaff";
ctx.shadowBlur=25;

ctx.fillStyle="#00eaff";

ctx.beginPath();

ctx.arc(
x,
object.y,
22,
0,
Math.PI*2
);

ctx.fill();

ctx.fillStyle="#001018";

ctx.font="bold 17px Arial";
ctx.textAlign="center";

ctx.fillText(
"×2",
x,
object.y+6
);

ctx.restore();

}


else if(object.type==="magnet"){

ctx.save();

ctx.strokeStyle="#ff1744";
ctx.shadowColor="#ff1744";
ctx.shadowBlur=20;
ctx.lineWidth=7;

ctx.beginPath();

ctx.arc(
x,
object.y,
17,
0,
Math.PI
);

ctx.stroke();

ctx.fillStyle="#ff1744";

ctx.font="bold 20px Arial";
ctx.textAlign="center";

ctx.fillText(
"M",
x,
object.y+7
);

ctx.restore();

}


else{

ctx.save();

ctx.translate(
x,
object.y
);

ctx.shadowColor=
object.color;

ctx.shadowBlur=20;

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
-18,
-27,
36,
22
);

ctx.restore();

}

}

}


/* =========================================
CHARAKTER
========================================= */

function drawPlayer(){

const d=getData();

const character=
characters[
Number(d.char||0)
];

const x=laneX(lane);
const y=H*.76;

ctx.save();

ctx.translate(x,y);

ctx.shadowColor=
character.color;

ctx.shadowBlur=30;

ctx.fillStyle=
character.color;

ctx.beginPath();

ctx.roundRect(
-28,
-28,
56,
56,
15
);

ctx.fill();


/* Gesicht */

ctx.shadowBlur=0;

ctx.fillStyle="#061018";

ctx.beginPath();

ctx.arc(
-11,
-6,
5,
0,
Math.PI*2
);

ctx.fill();

ctx.beginPath();

ctx.arc(
11,
-6,
5,
0,
Math.PI*2
);

ctx.fill();


/* Mund */

ctx.fillRect(
-12,
10,
24,
5
);


/* Glitzer */

const glitter=
Math.min(
24,
Math.floor(character.mult*4)
);

for(let i=0;i<glitter;i++){

const angle=
performance.now()/450+i;

const distance=
32+(i%5)*4;

const gx=
Math.cos(angle)*distance;

const gy=
Math.sin(angle)*distance;

ctx.fillStyle="#ffffffcc";

ctx.fillRect(
gx,
gy,
3,
3
);

}

ctx.restore();

}


/* =========================================
TOUCH / WISCHEN
========================================= */

let touchStartX=0;
let touchStartY=0;

canvas.addEventListener(
"touchstart",
event=>{

const touch=
event.changedTouches[0];

touchStartX=
touch.clientX;

touchStartY=
touch.clientY;

},
{passive:true}
);


canvas.addEventListener(
"touchend",
event=>{

if(!running||paused)return;

const touch=
event.changedTouches[0];

const dx=
touch.clientX-touchStartX;

const dy=
touch.clientY-touchStartY;


/* Nur Wischen */

if(
Math.abs(dx)>45&&
Math.abs(dx)>Math.abs(dy)
){

if(dx<0){

lane=
Math.max(-1,lane-1);

}else{

lane=
Math.min(1,lane+1);

}

}

},
{passive:true}
);


/* =========================================
POWER UI
========================================= */

function updatePower(){

const power=$("power");

const parts=[];

if(doubleTime>0){

parts.push(
"×2 "+Math.ceil(doubleTime)+"s"
);

}

if(magnetTime>0){

parts.push(
"MAGNET "+Math.ceil(magnetTime)+"s"
);

}

if(parts.length){

power.textContent=
parts.join("   ");

power.style.display="block";

}else{

power.style.display="none";

}

}


/* =========================================
MÜNZE EINSAMMELN
========================================= */

function collectCoin(){

const d=getData();

const character=
characters[
Number(d.char||0)
];

const world=
worlds[
Number(d.world||0)
];


/*
Normale Münze = +1.
Danach werden Charakter und Welt
angewendet.
*/

let amount=
1*
character.mult*
world.mult;

if(doubleTime>0){

amount*=2;

}

earned+=amount;

}


/* =========================================
GAME UPDATE
========================================= */

function update(dt){

if(!running||paused)return;

const seconds=dt/1000;

score+=seconds*10;

speed=
Math.min(
700,
speed+seconds*3
);


/* Münzen */

coinTimer-=seconds;

if(coinTimer<=0){

spawnCoins();

coinTimer=
3.8+
Math.random()*1.5;

}


/* Autos */

obstacleTimer-=seconds;

if(obstacleTimer<=0){

spawnCar();

const world=
Number(getData().world||0);

obstacleTimer=
Math.max(
.65,
1.35-world*.055
);

}


/* Powerups */

if(Math.random()<.0015){

spawnPower("double");

}

if(Math.random()<.0012){

spawnPower("magnet");

}


/* Timer */

if(doubleTime>0){

doubleTime=
Math.max(
0,
doubleTime-seconds
);

}

if(magnetTime>0){

magnetTime=
Math.max(
0,
magnetTime-seconds
);

}

updatePower();


/* Objekte bewegen */

const playerY=H*.76;

for(
let i=objects.length-1;
i>=0;
i--
){

const object=objects[i];

object.y+=speed*seconds;


if(object.y>H+150){

objects.splice(i,1);

continue;

}


/* Magnet */

if(
object.type==="coin"&&
magnetTime>0&&
Math.abs(object.y-playerY)<150
){

object.lane=lane;

}


/* Münze */

if(
object.type==="coin"&&
object.lane===lane&&
Math.abs(object.y-playerY)<55
){

collectCoin();

objects.splice(i,1);

continue;

}


/* x2 */

if(
object.type==="double"&&
object.lane===lane&&
Math.abs(object.y-playerY)<65
){

doubleTime=30;

objects.splice(i,1);

continue;

}


/* Magnet */

if(
object.type==="magnet"&&
object.lane===lane&&
Math.abs(object.y-playerY)<65
){

magnetTime=30;

objects.splice(i,1);

continue;

}


/* Auto */

if(
object.type==="car"&&
object.lane===lane&&
Math.abs(object.y-playerY)<65
){

endGame();

return;

}

}


/* HUD */

$("hudCoins").textContent=
Math.floor(
player.coins+earned
).toLocaleString();

$("hudScore").textContent=
Math.floor(score).toLocaleString();

}


/* =========================================
GAME LOOP
========================================= */

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

requestAnimationFrame(gameLoop);

}

requestAnimationFrame(gameLoop);


/* =========================================
PAUSE
========================================= */

$("pause").onclick=()=>{

if(!running)return;

paused=true;

$("pauseMenu").style.display="flex";

};


$("resumeBtn").onclick=()=>{

paused=false;

$("pauseMenu").style.display="none";

lastTime=performance.now();

};


$("giveUpBtn").onclick=()=>{

paused=false;

$("pauseMenu").style.display="none";

running=false;

canvas.style.display="none";
$("hud").style.display="none";
$("pause").style.display="none";
$("power").style.display="none";

openMenu();

};


/* =========================================
GAME OVER
========================================= */

async function endGame(){

if(!running)return;

running=false;

const finalScore=
Math.floor(score);

const finalCoins=
Math.floor(earned);

player.coins+=finalCoins;

if(finalScore>player.highscore){

player.highscore=
finalScore;

}

await savePlayer();

$("finalScore").textContent=
finalScore.toLocaleString();

$("finalCoins").textContent=
finalCoins.toLocaleString();

canvas.style.display="none";

$("hud").style.display="none";

$("pause").style.display="none";

$("power").style.display="none";

$("pauseMenu").style.display="none";

show("gameOver");

}


/* =========================================
GAME OVER BUTTONS
========================================= */

$("again").onclick=startGame;

$("gameMenu").onclick=openMenu;


/* =========================================
START
========================================= */

show("login");

restoreSession();

</script>

</body>
</html>
