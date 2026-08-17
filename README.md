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
  background:#02070d;
  color:white;
  font-family:Arial,sans-serif;
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
  align-items:center;
  justify-content:center;
  padding:20px;
  background:
    radial-gradient(circle at 50% 10%,#07556b,#03151f 45%,#010408);
}

.hidden{
  display:none!important;
}

.card{
  width:min(440px,100%);
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

input{
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
}

input:focus{
  border-color:#00eaff;
  box-shadow:0 0 18px #00eaff55;
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

.primary{
  background:linear-gradient(135deg,#05eaff,#078eff);
  color:#001018;
  border:0;
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

.control-row{
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

<!-- =================================================
     SUPABASE
     ================================================= -->

<script>

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio";

</script>


<!-- ================= LOGIN ================= -->

<section id="loginScreen" class="screen">

<div class="card">

<div class="logo">
CUBE RUSH
</div>

<div class="subtitle">
Melde dich an und speichere deinen Highscore.
</div>

<input
id="loginEmail"
type="email"
autocomplete="email"
placeholder="E-Mail">

<input
id="loginPassword"
type="password"
autocomplete="current-password"
placeholder="Passwort">

<button
id="loginBtn"
class="btn primary"
type="button">
ANMELDEN
</button>

<button
id="openRegister"
class="btn secondary"
type="button">
ACCOUNT ERSTELLEN
</button>

<div id="loginMessage" class="message"></div>

</div>
</section>


<!-- ================= REGISTRIERUNG ================= -->

<section id="registerScreen" class="screen hidden">

<div class="card">

<div class="logo">
ACCOUNT
</div>

<div class="subtitle">
Erstelle deinen Cube-Rush-Account.
</div>

<input
id="registerUsername"
type="text"
maxlength="20"
placeholder="Benutzername">

<input
id="registerEmail"
type="email"
autocomplete="email"
placeholder="E-Mail">

<input
id="registerPassword"
type="password"
autocomplete="new-password"
placeholder="Passwort">

<button
id="registerBtn"
class="btn primary"
type="button">
ACCOUNT ERSTELLEN
</button>

<button
id="backLogin"
class="btn secondary"
type="button">
ZURÜCK
</button>

<div id="registerMessage" class="message"></div>

</div>
</section>


<!-- ================= MENÜ ================= -->

<section id="menuScreen" class="screen hidden">

<div class="card">

<div class="logo">
CUBE RUSH
</div>

<div class="subtitle">
Willkommen,
<b id="menuUsername">Spieler</b>!
</div>

<div class="stats">

<div class="stat">
<small>🪙 MÜNZEN</small>
<strong id="menuCoins">0</strong>
</div>

<div class="stat">
<small>🏆 HIGHSCORE</small>
<strong id="menuHighscore">0</strong>
</div>

</div>

<button
id="startBtn"
class="btn primary"
type="button">
▶ SPIEL STARTEN
</button>

<button
id="rankingBtn"
class="btn secondary"
type="button">
🏆 RANGLISTE
</button>

<button
id="logoutBtn"
class="btn secondary"
type="button">
ABMELDEN
</button>

</div>
</section>


<!-- ================= RANGLISTE ================= -->

<section id="rankingScreen" class="screen hidden">

<div class="card">

<div class="logo">
🏆 RANGLISTE
</div>

<div id="rankingList" class="ranking">
Lade Rangliste...
</div>

<button
id="rankingBack"
class="btn secondary"
type="button">
ZURÜCK
</button>

</div>
</section>


<!-- ================= GAME OVER ================= -->

<section id="gameOverScreen" class="screen hidden">

<div class="card">

<div class="logo">
CRASH!
</div>

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
id="againBtn"
class="btn primary"
type="button">
🔄 NOCHMAL
</button>

<button
id="gameMenuBtn"
class="btn secondary"
type="button">
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

<button id="pause" type="button">
⏸
</button>

<div id="controls">

<div class="control-row">

<button id="leftBtn" class="control" type="button">
←
</button>

<button id="rightBtn" class="control" type="button">
→
</button>

</div>

<button id="jumpBtn" class="control jump" type="button">
SPRINGEN
</button>

</div>


<script>

/* =================================================
   APP
   ================================================= */

const $ = id =>
document.getElementById(id);

let accessToken = "";
let currentPlayer = null;


/* =================================================
   SCREENS
   ================================================= */

function showScreen(id){

document
.querySelectorAll(".screen")
.forEach(screen =>
screen.classList.add("hidden")
);

$(id).classList.remove("hidden");

}


/* =================================================
   MESSAGE
   ================================================= */

function message(id,text){

$(id).textContent = text;

}


/* =================================================
   SUPABASE
   ================================================= */

async function supabaseRequest(
path,
options={}
){

if(
SUPABASE_KEY ===
"DEIN_ANON_PUBLIC_KEY_HIER"
){

throw new Error(
"Supabase-Key fehlt."
);

}

const headers = {

"apikey":
SUPABASE_KEY,

"Content-Type":
"application/json",

...(accessToken
?
{
"Authorization":
"Bearer " + accessToken
}
:
{}),

...(options.headers || {})

};

const response =
await fetch(
SUPABASE_URL + path,
{
...options,
headers:headers
}
);

const text =
await response.text();

let data = null;

try{

data =
text
?
JSON.parse(text)
:
null;

}catch{

data = null;

}

if(!response.ok){

throw new Error(

data?.msg ||
data?.message ||
data?.error_description ||
data?.hint ||
data?.error ||
"Supabase-Fehler"

);

}

return data;

}


/* =================================================
   LOGIN
   ================================================= */

$("loginBtn").onclick =
async function(){

const email =
$("loginEmail")
.value
.trim();

const password =
$("loginPassword")
.value;

if(!email || !password){

message(
"loginMessage",
"Bitte E-Mail und Passwort eingeben."
);

return;

}

message(
"loginMessage",
"Anmeldung..."
);

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

accessToken =
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

message(
"loginMessage",
"❌ " + error.message
);

}

};


/* =================================================
   ACCOUNT ÖFFNEN
   ================================================= */

$("openRegister").onclick =
function(){

message(
"loginMessage",
""
);

showScreen(
"registerScreen"
);

};


/* =================================================
   ZURÜCK
   ================================================= */

$("backLogin").onclick =
function(){

message(
"registerMessage",
""
);

showScreen(
"loginScreen"
);

};


/* =================================================
   REGISTRIERUNG
   ================================================= */

$("registerBtn").onclick =
async function(){

const username =
$("registerUsername")
.value
.trim();

const email =
$("registerEmail")
.value
.trim();

const password =
$("registerPassword")
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

message(
"registerMessage",
"Account wird erstellt..."
);

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

/*
Wenn access_token vorhanden ist,
ist der Account sofort angemeldet.
*/

if(!data.access_token){

throw new Error(
"Supabase verlangt eine E-Mail-Bestätigung. Deaktiviere in Supabase bei Authentication → Settings die Option 'Confirm email'."
);

}

accessToken =
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
catch(error){

message(
"registerMessage",
"❌ " + error.message
);

}

};


/* =================================================
   PLAYER ERSTELLEN
   ================================================= */

async function createPlayer(){

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


/* =================================================
   PLAYER LADEN
   ================================================= */

async function loadPlayer(){

const rows =
await supabaseRequest(

"/rest/v1/Players" +
"?id=eq." +
encodeURIComponent(
currentPlayer.id
) +
"&select=*"

);

if(rows.length > 0){

currentPlayer.username =
rows[0].username ||
currentPlayer.username;

currentPlayer.coins =
Number(
rows[0].coins || 0
);

currentPlayer.highscore =
Number(
rows[0].highscore || 0
);

}
else{

await createPlayer();

}

}


/* =================================================
   PLAYER SPEICHERN
   ================================================= */

async function savePlayer(){

await supabaseRequest(

"/rest/v1/Players?id=eq." +
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


/* =================================================
   MENÜ
   ================================================= */

function openMenu(){

$("menuUsername")
.textContent =
currentPlayer.username;

$("menuCoins")
.textContent =
currentPlayer.coins
.toLocaleString();

$("menuHighscore")
.textContent =
currentPlayer.highscore
.toLocaleString();

showScreen(
"menuScreen"
);

}


/* =================================================
   LOGOUT
   ================================================= */

$("logoutBtn").onclick =
function(){

accessToken = "";

currentPlayer = null;

$("loginEmail").value = "";

$("loginPassword").value = "";

showScreen(
"loginScreen"
);

};


/* =================================================
   RANGLISTE
   ================================================= */

$("rankingBtn").onclick =
async function(){

showScreen(
"rankingScreen"
);

$("rankingList")
.textContent =
"Lade Rangliste...";

try{

const rows =
await supabaseRequest(

"/rest/v1/Players" +
"?select=username,highscore" +
"&order=highscore.desc" +
"&limit=50"

);

if(!rows.length){

$("rankingList")
.textContent =
"Noch keine Spieler.";

return;

}

$("rankingList")
.innerHTML =
rows
.map(
(row,index)=>{

const username =
String(
row.username || "Spieler"
)
.replaceAll("<","&lt;")
.replaceAll(">","&gt;");

return `
<div>
<b>${index+1}.</b>
${username}
—
${Number(
row.highscore || 0
).toLocaleString()}
</div>
`;

}
)
.join("");

}
catch(error){

$("rankingList")
.textContent =
"❌ " + error.message;

}

};


$("rankingBack").onclick =
openMenu;


/* =================================================
   SPIEL
   ================================================= */

const canvas =
$("game");

const ctx =
canvas.getContext("2d");

let W =
window.innerWidth;

let H =
window.innerHeight;

function resize(){

W =
window.innerWidth;

H =
window.innerHeight;

canvas.width =
W;

canvas.height =
H;

}

resize();

window.addEventListener(
"resize",
resize
);


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


/* =================================================
   STRASSE
   ================================================= */

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

return roadWidth() / 3;

}

function laneX(n){

return (
roadLeft() +
laneWidth() * (n+1) +
laneWidth()/2
);

}


/* =================================================
   START
   ================================================= */

$("startBtn").onclick =
startGame;

$("againBtn").onclick =
startGame;


function startGame(){

document
.querySelectorAll(".screen")
.forEach(x =>
x.classList.add("hidden")
);

canvas.style.display =
"block";

$("hud").style.display =
"flex";

$("controls").style.display =
"flex";

$("pause").style.display =
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


/* =================================================
   BACKGROUND
   ================================================= */

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
0,
0,
W,
H
);


/* CITY */

for(
let i=0;
i<24;
i++
){

const width =
25 + (i%5)*12;

const height =
100 + ((i*53)%240);

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


/* EDGES */

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


/* =================================================
   CARS
   ================================================= */

function spawnCar(){

const possible =
[-1,0,1]
.filter(
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


/* =================================================
   MÜNZEN
   ================================================= */

function spawnCoins(){

const amount =
3 +
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

y:-40-i*65

});

}

}


/* =================================================
   OBJEKTE ZEICHNEN
   ================================================= */

function drawObjects(){

for(
const object of objects
){

const x =
laneX(object.lane);

if(
object.type === "coin"
){

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

ctx.shadowBlur = 0;

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


/* =================================================
   SPIELER
   ================================================= */

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


/* =================================================
   BEWEGUNG
   ================================================= */

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

if(
!running ||
paused
)
return;

if(
jumpY <= 0
){

jumpVelocity =
850;

}

}


$("leftBtn").onclick =
moveLeft;

$("rightBtn").onclick =
moveRight;

$("jumpBtn").onclick =
jump;


/* =================================================
   TASTATUR
   ================================================= */

document.addEventListener(
"keydown",
function(event){

if(
event.key === "ArrowLeft"
)
moveLeft();

if(
event.key === "ArrowRight"
)
moveRight();

if(
event.key === "ArrowUp" ||
event.key === " "
)
jump();

}
);


/* =================================================
   UPDATE
   ================================================= */

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

if(
coinTimer<=0
){

spawnCoins();

coinTimer =
4+
Math.random()*1.5;

}


/* CARS */

carTimer -=
seconds;

if(
carTimer<=0
){

spawnCar();

carTimer =
1.25+
Math.random()*.75;

}


/* OBJEKTE */

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


/* MÜNZE */

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


/* AUTO */

if(
object.type==="car" &&
object.lane===lane &&
Math.abs(
object.y-playerY
)<65
){

if(
jumpY<90
){

endGame();

return;

}

}

}


$("hudCoins")
.textContent =
(
currentPlayer.coins+
roundCoins
)
.toLocaleString();

$("hudScore")
.textContent =
Math.floor(score)
.toLocaleString();

}


/* =================================================
   GAME LOOP
   ================================================= */

function gameLoop(time){

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


/* =================================================
   PAUSE
   ================================================= */

$("pause").onclick =
function(){

paused =
!paused;

$("pause").textContent =
paused
?
"▶"
:
"⏸";

};


/* =================================================
   GAME OVER
   ================================================= */

async function endGame(){

running=false;

const final =
Math.floor(score);

currentPlayer.coins +=
roundCoins;

if(
final >
currentPlayer.highscore
){

currentPlayer.highscore =
final;

}

try{

await savePlayer();

}
catch(error){

console.log(
"Speichern fehlgeschlagen:",
error
);

}

$("finalScore")
.textContent =
final.toLocaleString();

$("finalCoins")
.textContent =
roundCoins.toLocaleString();

canvas.style.display =
"none";

$("hud").style.display =
"none";

$("controls").style.display =
"none";

$("pause").style.display =
"none";

showScreen(
"gameOverScreen"
);

}


/* =================================================
   MENÜ
   ================================================= */

$("gameMenuBtn").onclick =
openMenu;


/* =================================================
   START
   ================================================= */

showScreen(
"loginScreen"
);

</script>

</body>
</html>
