<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#02040a;color:white;font-family:Arial,sans-serif}
canvas{position:fixed;inset:0;width:100%;height:100%}
.screen{position:fixed;inset:0;z-index:20;display:flex;align-items:center;justify-content:center;flex-direction:column;padding:20px;background:radial-gradient(circle at 50% 15%,#103c5a,#02040a 72%)}
.hidden{display:none!important}
.logo{font-size:44px;font-weight:1000;letter-spacing:4px;text-shadow:0 0 10px #00eaff,0 0 30px #00eaff}
.subtitle{color:#9bb7c7;margin:6px 0 20px}
input{width:min(360px,90vw);padding:14px;margin:5px;border-radius:12px;border:1px solid #00eaff;background:#06111b;color:white;outline:none}
button{border:0;border-radius:12px;padding:13px 20px;margin:5px;font-weight:900;background:#00eaff;color:#001018;box-shadow:0 0 18px #00eaff55}
button:active{transform:scale(.96)}
.menuButton{min-width:190px}
#hud{position:fixed;z-index:10;top:10px;left:10px;right:10px;display:none;justify-content:space-between;pointer-events:none}
.hudBox{padding:8px 11px;border-radius:10px;background:#04111ddd;border:1px solid #00eaff55;font-size:13px;font-weight:900}
#pauseButton{position:fixed;z-index:12;top:52px;right:10px;display:none}
.panel{width:min(900px,96vw);max-height:90vh;overflow:auto;text-align:center}
.card{display:inline-block;vertical-align:top;width:155px;min-height:145px;margin:5px;padding:12px;border-radius:15px;background:#07131e;border:1px solid #00eaff44}
.rank{display:flex;justify-content:space-between;padding:11px;margin:4px 0;border-radius:10px;background:#07131e}
.coins{color:#ffe000;font-size:20px;font-weight:900;margin:10px}
.message{color:#a9bdc8;font-size:13px;margin:10px}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
  <div class="hudBox">🪙 <span id="coinsHud">0</span></div>
  <div class="hudBox">🏆 <span id="scoreHud">0</span></div>
  <div class="hudBox">🌆 <span id="cityHud">Neon City</span></div>
</div>

<button id="pauseButton">⏸</button>

<div id="loginScreen" class="screen">

  <div class="logo">CUBE RUSH</div>
  <div class="subtitle">ONLINE EDITION</div>

  <input id="email" type="email" placeholder="E-Mail">
  <input id="password" type="password" placeholder="Passwort">
  <input id="username" class="hidden" maxlength="18" placeholder="Spielername">

  <div>
    <button id="loginMode">ANMELDEN</button>
    <button id="registerMode">REGISTRIEREN</button>
  </div>

  <button id="loginButton">ANMELDEN</button>

  <div id="loginMessage" class="message"></div>
</div>

<div id="menuScreen" class="screen hidden">

  <div class="logo">CUBE RUSH</div>

  <div>Spieler: <b id="playerName"></b></div>

  <div class="coins">
    🪙 <span id="menuCoins">0</span>
  </div>

  <button class="menuButton" id="startButton">▶ SPIEL STARTEN</button>
  <button class="menuButton" id="characterButton">🧊 CHARAKTERE</button>
  <button class="menuButton" id="cityButton">🌆 STÄDTE</button>
  <button class="menuButton" id="rankingButton">🏆 RANGLISTE</button>
  <button class="menuButton" id="codeButton">🔐 CODE</button>
  <button class="menuButton" id="logoutButton">ABMELDEN</button>

</div>

<div id="characterScreen" class="screen hidden">
  <div class="panel">
    <h1>🧊 CHARAKTERE</h1>
    <div class="coins">🪙 <span id="characterCoins">0</span></div>
    <div id="characterList"></div>
    <button onclick="showMenu()">ZURÜCK</button>
  </div>
</div>

<div id="cityScreen" class="screen hidden">
  <div class="panel">
    <h1>🌆 STÄDTE</h1>
    <div id="cityList"></div>
    <button onclick="showMenu()">ZURÜCK</button>
  </div>
</div>

<div id="rankingScreen" class="screen hidden">
  <div class="panel">
    <h1>🏆 WELTWEITE RANGLISTE</h1>
    <div id="rankingList">Lade Rangliste...</div>
    <button onclick="showMenu()">ZURÜCK</button>
  </div>
</div>

<div id="codeScreen" class="screen hidden">

  <h1>🔐 CODE</h1>

  <input id="secretCode"
         inputmode="numeric"
         maxlength="3"
         placeholder="Code eingeben">

  <button id="redeemButton">EINLÖSEN</button>

  <div id="codeMessage" class="message"></div>

  <button onclick="showMenu()">ZURÜCK</button>

</div>

<div id="pauseScreen" class="screen hidden">

  <h1>⏸ PAUSE</h1>

  <button id="resumeButton">▶ WEITERSPIELEN</button>
  <button id="quitButton">✕ AUFGEBEN</button>

</div>

<div id="gameOverScreen" class="screen hidden">

  <h1>💥 CRASH!</h1>

  <div id="gameOverText"></div>

  <button id="retryButton">🔄 NOCHMAL</button>
  <button onclick="showMenu()">MENÜ</button>

</div>

<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>

<script>

/* =====================================================
   DEINE SUPABASE-DATEN
===================================================== */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"sb_publishable_LaQ9ibF7A_s6pZUucnnuaw_yHVf0lvc";

const supabaseClient =
supabase.createClient(
  SUPABASE_URL,
  SUPABASE_KEY,
  {
    auth:{
      persistSession:true,
      autoRefreshToken:true
    }
  }
);


/* =====================================================
   STÄDTE
===================================================== */

const cities=[

  {
    id:0,
    name:"Neon City",
    price:0,
    minCoins:3,
    maxCoins:6,
    color:"#00eaff"
  },

  {
    id:1,
    name:"Tokyo Night",
    price:5000,
    minCoins:7,
    maxCoins:10,
    color:"#ff2ab5"
  },

  {
    id:2,
    name:"Cyber Dubai",
    price:15000,
    minCoins:10,
    maxCoins:14,
    color:"#ff9900"
  },

  {
    id:3,
    name:"Future New York",
    price:35000,
    minCoins:14,
    maxCoins:18,
    color:"#765cff"
  },

  {
    id:4,
    name:"Galaxy City",
    price:75000,
    minCoins:18,
    maxCoins:25,
    color:"#d000ff"
  }

];


/* =====================================================
   CHARAKTERE
===================================================== */

const characters=[

 ["BLUE",0,"#008cff"],
 ["VOLT",300,"#ffe000"],
 ["FIRE",700,"#ff3300"],
 ["ICE",1200,"#00eaff"],
 ["PURPLE",2000,"#a033ff"],
 ["POISON",3000,"#55ff00"],
 ["MAGMA",4500,"#ff5500"],
 ["THUNDER",6500,"#ffff00"],
 ["VOID",9000,"#7655ff"],
 ["OMEGA",12000,"#ffffff"],
 ["CYBER",16000,"#00ff99"],
 ["GOLD",22000,"#ffc400"],
 ["RUBY",28000,"#ff164f"],
 ["DIAMOND",35000,"#9fffff"],
 ["GALAXY",45000,"#ff32c8"],
 ["PHANTOM",55000,"#777799"],
 ["SOLAR",70000,"#ff8700"],
 ["NEBULA",85000,"#ff32c8"],
 ["AURORA",100000,"#00ffee"],
 ["CUBE GOD",150000,"#ffffff"]

];


let currentUser=null;

let profile={
 username:"",
 coins:0,
 highscore:0,
 unlocked_chars:[0],
 unlocked_cities:[0],
 selected_char:0,
 selected_city:0
};

let selectedCharacter=0;
let selectedCity=0;


/* =====================================================
   MENÜ
===================================================== */

function hideScreens(){

 document
 .querySelectorAll(".screen")
 .forEach(s=>s.classList.add("hidden"));

}

function showMenu(){

 hideScreens();

 menuScreen.classList.remove("hidden");

 updateMenu();

}

function updateMenu(){

 playerName.textContent=profile.username;

 menuCoins.textContent=
 Number(profile.coins).toLocaleString();

 characterCoins.textContent=
 Number(profile.coins).toLocaleString();

 cityHud.textContent=
 cities[selectedCity].name;

}


/* =====================================================
   LOGIN
===================================================== */

let registerModeActive=false;

loginMode.onclick=()=>{

 registerModeActive=false;

 username.classList.add("hidden");

 loginButton.textContent="ANMELDEN";

};

registerMode.onclick=()=>{

 registerModeActive=true;

 username.classList.remove("hidden");

 loginButton.textContent="KONTO ERSTELLEN";

};

loginButton.onclick=async()=>{

 const mail=email.value.trim();
 const pass=password.value;
 const name=username.value.trim();

 if(!mail||!pass){

   loginMessage.textContent=
   "Bitte E-Mail und Passwort eingeben.";

   return;

 }

 loginMessage.textContent="Bitte warten...";

 if(registerModeActive){

   if(!name){

     loginMessage.textContent=
     "Bitte Spielernamen eingeben.";

     return;

   }

   const result=
   await supabaseClient.auth.signUp({

     email:mail,
     password:pass,

     options:{
       data:{username:name}
     }

   });

   if(result.error){

     loginMessage.textContent=
     result.error.message;

     return;

   }

   if(result.data.session){

     await loadProfile();
     showMenu();

   }else{

     loginMessage.textContent=
     "Konto erstellt. Bestätige gegebenenfalls deine E-Mail.";

   }

 }else{

   const result=
   await supabaseClient.auth.signInWithPassword({

     email:mail,
     password:pass

   });

   if(result.error){

     loginMessage.textContent=
     result.error.message;

     return;

   }

   await loadProfile();
   showMenu();

 }

};


/* =====================================================
   PROFIL
===================================================== */

async function loadProfile(){

 const result=
 await supabaseClient.auth.getUser();

 currentUser=result.data.user;

 if(!currentUser)return;

 const query=
 await supabaseClient
 .from("profiles")
 .select("*")
 .eq("id",currentUser.id)
 .maybeSingle();

 if(query.data){

   profile=query.data;

 }else{

   const newProfile={

     id:currentUser.id,

     username:
     currentUser.user_metadata?.username||
     currentUser.email.split("@")[0],

     coins:0,
     highscore:0,
     unlocked_chars:[0],
     unlocked_cities:[0],
     selected_char:0,
     selected_city:0

   };

   const insert=
   await supabaseClient
   .from("profiles")
   .insert(newProfile)
   .select()
   .single();

   if(insert.data)
   profile=insert.data;

 }

 selectedCharacter=
 Number(profile.selected_char||0);

 selectedCity=
 Number(profile.selected_city||0);

 updateMenu();

}


async function saveProfile(){

 if(!currentUser)return;

 await supabaseClient
 .from("profiles")
 .update({

   coins:profile.coins,
   highscore:profile.highscore,
   unlocked_chars:profile.unlocked_chars,
   unlocked_cities:profile.unlocked_cities,
   selected_char:selectedCharacter,
   selected_city:selectedCity

 })
 .eq("id",currentUser.id);

}


/* =====================================================
   CHARAKTERE
===================================================== */

characterButton.onclick=()=>{

 hideScreens();

 renderCharacters();

 characterScreen.classList.remove("hidden");

};

function renderCharacters(){

 characterList.innerHTML="";

 characters.forEach((character,index)=>{

   const card=document.createElement("div");

   card.className="card";

   const name=character[0];
   const price=character[1];
   const color=character[2];

   card.innerHTML=`

     <div style="
       font-size:40px;
       color:${color};
       text-shadow:0 0 10px ${color},0 0 25px ${color};
     ">■</div>

     <b style="color:${color}">
       ${name}
     </b>

     <br><br>

     ${
       profile.unlocked_chars.includes(index)
       ?
       "✓ FREIGESCHALTET"
       :
       "🪙 "+price.toLocaleString()
     }

   `;

   const button=document.createElement("button");

   if(profile.unlocked_chars.includes(index)){

     button.textContent=
     selectedCharacter===index
     ?"✓ AKTIV"
     :"AUSWÄHLEN";

     button.onclick=async()=>{

       selectedCharacter=index;

       await saveProfile();

       renderCharacters();

     };

   }else{

     button.textContent="KAUFEN";

     button.onclick=async()=>{

       if(profile.coins<price){

         alert("Nicht genug Coins.");
         return;

       }

       profile.coins-=price;

       profile.unlocked_chars.push(index);

       selectedCharacter=index;

       await saveProfile();

       updateMenu();

       renderCharacters();

     };

   }

   card.appendChild(button);

   characterList.appendChild(card);

 });

}


/* =====================================================
   STÄDTE
===================================================== */

cityButton.onclick=()=>{

 hideScreens();

 renderCities();

 cityScreen.classList.remove("hidden");

};

function renderCities(){

 cityList.innerHTML="";

 cities.forEach(city=>{

   const card=document.createElement("div");

   card.className="card";

   card.innerHTML=`

     <h3 style="
       color:${city.color};
       text-shadow:0 0 15px ${city.color};
     ">
       ${city.name}
     </h3>

     <p>
       🪙 ${city.minCoins}-${city.maxCoins}
     </p>

     <p>
       ${
         city.price===0
         ?"KOSTENLOS"
         :city.price.toLocaleString()+" Coins"
       }
     </p>

   `;

   const button=document.createElement("button");

   if(profile.unlocked_cities.includes(city.id)){

     button.textContent=
     selectedCity===city.id
     ?"✓ AKTIV"
     :"AUSWÄHLEN";

     button.onclick=async()=>{

       selectedCity=city.id;

       await saveProfile();

       updateMenu();

       renderCities();

     };

   }else{

     button.textContent="FREISCHALTEN";

     button.onclick=async()=>{

       if(profile.coins<city.price){

         alert("Nicht genug Coins.");
         return;

       }

       profile.coins-=city.price;

       profile.unlocked_cities.push(city.id);

       selectedCity=city.id;

       await saveProfile();

       updateMenu();

       renderCities();

     };

   }

   card.appendChild(button);

   cityList.appendChild(card);

 });

}


/* =====================================================
   RANGLISTE
===================================================== */

rankingButton.onclick=async()=>{

 hideScreens();

 rankingScreen.classList.remove("hidden");

 rankingList.textContent="Lade Rangliste...";

 const result=
 await supabaseClient
 .from("profiles")
 .select("username,highscore")
 .order("highscore",{ascending:false})
 .limit(100);

 if(result.error){

   rankingList.textContent=
   "Fehler beim Laden.";

   return;

 }

 rankingList.innerHTML="";

 result.data.forEach((player,index)=>{

   const row=document.createElement("div");

   row.className="rank";

   row.innerHTML=`

     <span>
       ${index+1}.
       ${escapeHTML(player.username)}
     </span>

     <b>
       ${Number(player.highscore).toLocaleString()}
     </b>

   `;

   rankingList.appendChild(row);

 });

};

function escapeHTML(text){

 return String(text)
 .replaceAll("&","&amp;")
 .replaceAll("<","&lt;")
 .replaceAll(">","&gt;")
 .replaceAll('"',"&quot;")
 .replaceAll("'","&#039;");

}


/* =====================================================
   CODES
===================================================== */

codeButton.onclick=()=>{

 hideScreens();

 secretCode.value="";

 codeMessage.textContent="";

 codeScreen.classList.remove("hidden");

};

redeemButton.onclick=async()=>{

 const code=secretCode.value.trim();

 if(code==="110"){

   profile.coins+=100000;

   profile.unlocked_chars=
   characters.map((_,i)=>i);

   profile.unlocked_cities=
   cities.map(city=>city.id);

   await saveProfile();

   codeMessage.textContent=
   "✅ Code 110 aktiviert! Alle Charaktere und 100.000 Coins.";

 }

 else if(code==="112"){

   profile.coins+=50000;

   profile.unlocked_chars=
   [0,1,2,3,4];

   await saveProfile();

   codeMessage.textContent=
   "✅ Code 112 aktiviert! 50.000 Coins und 5 Charaktere.";

 }

 else{

   codeMessage.textContent=
   "❌ Falscher Code.";

 }

 secretCode.value="";

 updateMenu();

};


/* =====================================================
   ABMELDEN
===================================================== */

logoutButton.onclick=async()=>{

 await supabaseClient.auth.signOut();

 currentUser=null;

 hideScreens();

 loginScreen.classList.remove("hidden");

};


/* =====================================================
   SPIEL
===================================================== */

const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");

function resize(){

 const dpr=Math.min(devicePixelRatio||1,2);

 canvas.width=innerWidth*dpr;
 canvas.height=innerHeight*dpr;

 canvas.style.width=innerWidth+"px";
 canvas.style.height=innerHeight+"px";

 ctx.setTransform(dpr,0,0,dpr,0,0);

}

addEventListener("resize",resize);
resize();


let gameRunning=false;
let paused=false;
let score=0;
let runCoins=0;
let speed=250;
let playerLane=0;
let playerX=0;
let objects=[];
let lastTime=performance.now();
let magnetTime=0;
let doubleTime=0;


/* =====================================================
   STRASSE
===================================================== */

function getRoad(){

 const width=Math.min(innerWidth*.84,600);

 return{
   left:(innerWidth-width)/2,
   width
 };

}

function laneX(lane){

 const road=getRoad();

 return road.left+
 road.width*(lane+1.5)/3;

}


/* =====================================================
   WELT
===================================================== */

function drawWorld(){

 const W=innerWidth;
 const H=innerHeight;

 ctx.fillStyle="#02040a";
 ctx.fillRect(0,0,W,H);

 const city=cities[selectedCity];

 const gradient=
 ctx.createLinearGradient(0,0,0,H);

 gradient.addColorStop(0,city.color+"22");
 gradient.addColorStop(1,"#02040a");

 ctx.fillStyle=gradient;
 ctx.fillRect(0,0,W,H);

 const road=getRoad();

 for(let i=0;i<18;i++){

   const bw=35+(i*17)%60;
   const bh=120+(i*53)%260;
   const x=i*80-20;

   ctx.fillStyle="#07111b";

   ctx.fillRect(
     x,
     H*.38-bh,
     bw,
     bh
   );

   ctx.fillStyle=city.color+"55";

   for(
     let y=H*.38-bh+15;
     y<H*.38;
     y+=25
   ){

     ctx.fillRect(
       x+8,
       y,
       6,
       8
     );

   }

 }

 ctx.fillStyle="#111820";

 ctx.fillRect(
   road.left,
   0,
   road.width,
   H
 );

 ctx.fillStyle=city.color;

 ctx.fillRect(
   road.left,
   0,
   3,
   H
 );

 ctx.fillRect(
   road.left+road.width-3,
   0,
   3,
   H
 );

 const laneWidth=road.width/3;

 ctx.fillStyle="#ffffff55";

 const offset=(score*3)%100;

 for(
   let y=-100+offset;
   y<H;
   y+=100
 ){

   ctx.fillRect(
     road.left+laneWidth-2,
     y,
     4,
     55
   );

   ctx.fillRect(
     road.left+laneWidth*2-2,
     y,
     4,
     55
   );

 }

}


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer(){

 const x=playerX;
 const y=innerHeight*.72;
 const size=58;

 const color=
 characters[selectedCharacter][2];

 ctx.save();

 ctx.translate(x,y);

 ctx.shadowColor=color;
 ctx.shadowBlur=25;

 ctx.fillStyle=color;

 ctx.fillRect(
   -size/2,
   -size/2,
   size,
   size
 );

 ctx.shadowBlur=0;

 ctx.fillStyle="#ffffffaa";

 ctx.fillRect(-18,-18,13,7);
 ctx.fillRect(8,-18,10,7);

 ctx.restore();

}


/* =====================================================
   AUTOS
===================================================== */

function spawnCar(){

 objects.push({

   type:"car",

   lane:
   Math.floor(Math.random()*3)-1,

   y:-150,

   width:62,

   height:96,

   color:[
     "#ff2048",
     "#168cff",
     "#ffffff",
     "#ffae00",
     "#8b4dff"
   ][
     Math.floor(Math.random()*5)
   ]

 });

}

function drawCar(car){

 const x=laneX(car.lane);

 ctx.save();

 ctx.translate(x,car.y);

 ctx.shadowColor=car.color;
 ctx.shadowBlur=14;

 ctx.fillStyle=car.color;

 ctx.beginPath();

 ctx.roundRect(
   -31,
   -48,
   62,
   96,
   10
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#102331";

 ctx.beginPath();

 ctx.roundRect(
   -20,
   -30,
   40,
   27,
   7
 );

 ctx.fill();

 ctx.fillStyle="#ffffff";

 ctx.fillRect(-22,25,13,7);
 ctx.fillRect(9,25,13,7);

 ctx.restore();

}


/* =====================================================
   MÜNZEN
===================================================== */

function spawnCoinLine(){

 const city=cities[selectedCity];

 const amount=
 city.minCoins+
 Math.floor(
   Math.random()*
   (city.maxCoins-city.minCoins+1)
 );

 const lane=
 Math.floor(Math.random()*3)-1;

 for(let i=0;i<amount;i++){

   objects.push({

     type:"coin",
     lane:lane,
     y:-50-i*58

   });

 }

}

function drawCoin(coin,time){

 const x=laneX(coin.lane);

 const scale=
 .45+
 Math.abs(Math.cos(time*.008))*.55;

 ctx.save();

 ctx.translate(x,coin.y);
 ctx.scale(scale,1);

 ctx.fillStyle="#ffe000";
 ctx.shadowColor="#ffe000";
 ctx.shadowBlur=20;

 ctx.beginPath();

 ctx.arc(
   0,0,15,0,Math.PI*2
 );

 ctx.fill();

 ctx.shadowBlur=0;

 ctx.fillStyle="#725800";
 ctx.font="bold 13px Arial";
 ctx.textAlign="center";
 ctx.textBaseline="middle";

 ctx.fillText("C",0,1);

 ctx.restore();

}


/* =====================================================
   SPIEL STARTEN
===================================================== */

startButton.onclick=startGame;

function startGame(){

 hideScreens();

 gameRunning=true;
 paused=false;

 score=0;
 runCoins=0;
 speed=250;

 playerLane=0;

 playerX=laneX(0);

 objects=[];

 magnetTime=0;
 doubleTime=0;

 hud.style.display="flex";
 pauseButton.style.display="block";

 lastTime=performance.now();

}


/* =====================================================
   SPIEL UPDATE
===================================================== */

function update(delta){

 if(!gameRunning||paused)return;

 const seconds=delta/1000;
 const H=innerHeight;

 speed=Math.min(700,speed+seconds*4);

 score+=seconds*10;

 playerX+=
 (
   laneX(playerLane)-playerX
 )*
 Math.min(1,seconds*14);

 if(magnetTime>0)magnetTime-=seconds;
 if(doubleTime>0)doubleTime-=seconds;

 for(let i=objects.length-1;i>=0;i--){

   const object=objects[i];

   object.y+=speed*seconds;

   if(object.y>H+150){

     objects.splice(i,1);
     continue;

   }

   if(
     object.type==="coin"&&
     magnetTime>0
   ){

     object.y+=
     (H*.72-object.y)*
     Math.min(1,seconds*5);

     object.lane=playerLane;

   }

   if(
     object.type==="coin"&&
     Math.abs(object.y-H*.72)<45&&
     Math.abs(laneX(object.lane)-playerX)<42
   ){

     runCoins+=
     doubleTime>0?2:1;

     objects.splice(i,1);

     continue;

   }

   if(
     object.type==="car"&&
     Math.abs(object.y-H*.72)<60&&
     Math.abs(laneX(object.lane)-playerX)<42
   ){

     endGame();

     return;

   }

 }

 if(Math.random()<delta/1000/1.25)
   spawnCar();

 if(Math.random()<delta/1000/2)
   spawnCoinLine();

 scoreHud.textContent=Math.floor(score);

 coinsHud.textContent=
 (
   profile.coins+runCoins
 ).toLocaleString();

}


/* =====================================================
   GAME LOOP
===================================================== */

function gameLoop(time){

 const delta=Math.min(40,time-lastTime);

 lastTime=time;

 drawWorld();

 for(const object of objects){

   if(object.type==="car")
     drawCar(object);
   else
     drawCoin(object,time);

 }

 drawPlayer();

 update(delta);

 requestAnimationFrame(gameLoop);

}

requestAnimationFrame(gameLoop);


/* =====================================================
   STEUERUNG
===================================================== */

let touchStartX=0;

canvas.addEventListener(
 "touchstart",
 event=>{

   if(!gameRunning||paused)return;

   touchStartX=
   event.changedTouches[0].clientX;

 },
 {passive:true}
);

canvas.addEventListener(
 "touchend",
 event=>{

   if(!gameRunning||paused)return;

   const endX=
   event.changedTouches[0].clientX;

   const difference=
   endX-touchStartX;

   if(Math.abs(difference)<35)return;

   if(difference>0){

     playerLane=
     Math.min(1,playerLane+1);

   }else{

     playerLane=
     Math.max(-1,playerLane-1);

   }

 },
 {passive:true}
);

document.addEventListener(
 "keydown",
 event=>{

   if(!gameRunning||paused)return;

   if(event.key==="ArrowLeft"){

     playerLane=
     Math.max(-1,playerLane-1);

   }

   if(event.key==="ArrowRight"){

     playerLane=
     Math.min(1,playerLane+1);

   }

 }
);


/* =====================================================
   PAUSE
===================================================== */

pauseButton.onclick=()=>{

 if(!gameRunning)return;

 paused=true;

 pauseButton.style.display="none";

 pauseScreen.classList.remove("hidden");

};

resumeButton.onclick=()=>{

 paused=false;

 pauseScreen.classList.add("hidden");

 pauseButton.style.display="block";

 lastTime=performance.now();

};

quitButton.onclick=async()=>{

 paused=false;

 pauseScreen.classList.add("hidden");

 await endGame();

};


/* =====================================================
   GAME OVER
===================================================== */

async function endGame(){

 if(!gameRunning)return;

 gameRunning=false;

 profile.coins+=runCoins;

 const finalScore=Math.floor(score);

 if(finalScore>profile.highscore){

   profile.highscore=finalScore;

 }

 await saveProfile();

 hud.style.display="none";
 pauseButton.style.display="none";

 gameOverText.innerHTML=`

   <p>
     🏆 Punkte:
     <b>${finalScore.toLocaleString()}</b>
   </p>

   <p>
     🪙 Münzen:
     <b>${runCoins}</b>
   </p>

   <p>
     ⭐ Highscore:
     <b>${profile.highscore.toLocaleString()}</b>
   </p>

 `;

 gameOverScreen.classList.remove("hidden");

}

retryButton.onclick=()=>{

 gameOverScreen.classList.add("hidden");

 startGame();

};


/* =====================================================
   AUTOMATISCH EINLOGGEN
===================================================== */

(async function(){

 const session=
 await supabaseClient.auth.getSession();

 if(session.data.session){

   await loadProfile();

   showMenu();

 }

})();

</script>

</body>
</html>
