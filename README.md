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
  -webkit-tap-highlight-color:transparent;
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

.logo{
  font-size:44px;
  font-weight:900;
  color:#00eaff;
  text-shadow:0 0 25px #00eaff;
  margin-bottom:5px;
  text-align:center;
}

h1{
  color:#00eaff;
  text-shadow:0 0 20px #00eaff;
  text-align:center;
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
  box-shadow:0 0 15px #00eaff55;
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

.msg{
  min-height:25px;
  max-width:340px;
  color:#a9c5d0;
  text-align:center;
  margin:8px;
}

.coins{
  color:#ffd900;
  font-weight:bold;
  font-size:20px;
  margin:10px;
}

.card{
  display:inline-flex;
  flex-direction:column;
  justify-content:space-between;
  vertical-align:top;
  width:160px;
  min-height:180px;
  margin:5px;
  padding:12px;
  background:#091522;
  border:1px solid #00eaff55;
  border-radius:15px;
  text-align:center;
}

.card h3{
  margin:5px 0;
}

.preview{
  width:80px;
  height:80px;
  margin:5px auto 10px;
  border-radius:18px;
  display:flex;
  align-items:center;
  justify-content:center;
  font-size:32px;
  font-weight:bold;
  box-shadow:0 0 20px #00eaff55;
}

#characterList,
#cityList{
  max-width:900px;
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
  gap:5px;
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

.small{
  font-size:13px;
  color:#9db0ba;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
  <div class="hud">COINS: <span id="coins">0</span></div>
  <div class="hud">SCORE: <span id="score">0</span></div>
  <div class="hud"><span id="cityName">Neon City</span></div>
</div>

<button id="pause">PAUSE</button>


<!-- ================= LOGIN ================= -->

<div id="login" class="screen">

  <div class="logo">CUBE RUSH</div>

  <p>ONLINE EDITION</p>

  <input
    id="username"
    placeholder="Benutzername"
    maxlength="18"
    autocomplete="username">

  <input
    id="email"
    type="email"
    placeholder="E-Mail"
    autocomplete="email">

  <input
    id="password"
    type="password"
    placeholder="Passwort"
    minlength="6"
    autocomplete="current-password">

  <button id="registerBtn">
    REGISTRIEREN
  </button>

  <button id="loginBtn">
    ANMELDEN
  </button>

  <div id="loginMsg" class="msg"></div>

</div>


<!-- ================= MENU ================= -->

<div id="menu" class="screen hidden">

  <div class="logo">CUBE RUSH</div>

  <p>
    Spieler:
    <b id="player"></b>
  </p>

  <div class="coins">
    COINS:
    <span id="menuCoins">0</span>
  </div>

  <button id="startBtn">
    STARTEN
  </button>

  <button id="charactersBtn">
    CHARAKTERE
  </button>

  <button id="citiesBtn">
    STÄDTE
  </button>

  <button id="rankingBtn">
    RANGLISTE
  </button>

  <button id="codeBtn">
    CODE
  </button>

  <button id="logoutBtn">
    ABMELDEN
  </button>

</div>


<!-- ================= CHARACTERS ================= -->

<div id="characters" class="screen hidden">

  <h1>CHARAKTERE</h1>

  <div class="coins">
    COINS:
    <span id="charCoins">0</span>
  </div>

  <div id="characterList"></div>

  <button id="charactersBack">
    ZURÜCK
  </button>

</div>


<!-- ================= CITIES ================= -->

<div id="cities" class="screen hidden">

  <h1>STÄDTE</h1>

  <div id="cityList"></div>

  <button id="citiesBack">
    ZURÜCK
  </button>

</div>


<!-- ================= RANKING ================= -->

<div id="ranking" class="screen hidden">

  <h1>RANGLISTE</h1>

  <div class="small">
    Rangliste nach Coins
  </div>

  <div id="rankingList">
    Lade Rangliste...
  </div>

  <button id="rankingBack">
    ZURÜCK
  </button>

</div>


<!-- ================= CODE ================= -->

<div id="codes" class="screen hidden">

  <h1>CODE</h1>

  <input
    id="secretCode"
    inputmode="numeric"
    maxlength="3"
    placeholder="Code">

  <button id="redeemBtn">
    EINLÖSEN
  </button>

  <div id="codeMsg" class="msg"></div>

  <button id="codeBack">
    ZURÜCK
  </button>

</div>


<!-- ================= PAUSE ================= -->

<div id="pauseScreen" class="screen hidden">

  <h1>PAUSE</h1>

  <button id="resumeBtn">
    WEITERSPIELEN
  </button>

  <button id="quitBtn">
    MENÜ
  </button>

</div>


<!-- ================= GAME OVER ================= -->

<div id="gameOver" class="screen hidden">

  <h1>CRASH!</h1>

  <div id="gameOverText"></div>

  <button id="restartBtn">
    NOCHMAL
  </button>

  <button id="gameOverMenuBtn">
    MENÜ
  </button>

</div>


<script>

/* =====================================================
   SUPABASE
===================================================== */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

const SUPABASE_KEY =
"sb_publishable_LaQ9ibF7A_s6pZUucnnuaw_yHVf0lvc";

const supabaseClient =
window.supabase.createClient(
  SUPABASE_URL,
  SUPABASE_KEY
);


/* =====================================================
   SPIELER
===================================================== */

let playerData = {
  id:null,
  username:"",
  email:"",
  coins:0,
  highscore:0,
  characters:[0],
  cities:[0],
  character:0,
  city:0
};

let currentUser = null;


/* =====================================================
   STÄDTE
===================================================== */

const cityData = [

  {
    name:"Neon City",
    price:0,
    min:3,
    max:6,
    sky:"#061b30"
  },

  {
    name:"Tokyo Night",
    price:5000,
    min:7,
    max:10,
    sky:"#19062c"
  },

  {
    name:"Cyber Dubai",
    price:12000,
    min:10,
    max:14,
    sky:"#301507"
  },

  {
    name:"Future New York",
    price:25000,
    min:14,
    max:18,
    sky:"#071c20"
  },

  {
    name:"Galaxy City",
    price:50000,
    min:18,
    max:25,
    sky:"#12052b"
  }

];


/* =====================================================
   CHARAKTERE
===================================================== */

const charData = [

  ["Blue",0,"#00eaff"],
  ["Volt",300,"#00ff88"],
  ["Fire",700,"#ff3b1f"],
  ["Ice",1200,"#66ccff"],
  ["Purple",2000,"#9b4dff"],
  ["Poison",3000,"#77ff00"],
  ["Magma",4500,"#ff5a00"],
  ["Thunder",6500,"#ffff00"],
  ["Void",9000,"#5c5c7a"],
  ["Omega",12000,"#ffffff"],
  ["Cyber",16000,"#00ffcc"],
  ["Gold",22000,"#ffd700"],
  ["Ruby",28000,"#ff1744"],
  ["Diamond",35000,"#aaf7ff"],
  ["Galaxy",45000,"#a855ff"],
  ["Phantom",55000,"#707070"],
  ["Solar",70000,"#ff9900"],
  ["Nebula",85000,"#ff55dd"],
  ["Aurora",100000,"#55ffaa"],
  ["Cube God",150000,"#ffffff"]

];


/* =====================================================
   HILFSFUNKTIONEN
===================================================== */

function showMessage(text){

  document.getElementById(
    "loginMsg"
  ).textContent = text;

}


function hideAll(){

  document
    .querySelectorAll(".screen")
    .forEach(x =>
      x.classList.add("hidden")
    );

}


function showMenu(){

  hideAll();

  document
    .getElementById("menu")
    .classList.remove("hidden");

  document
    .getElementById("player")
    .textContent =
    playerData.username;

  document
    .getElementById("menuCoins")
    .textContent =
    playerData.coins.toLocaleString();

}


/* =====================================================
   PROFILE AUS SUPABASE LADEN
===================================================== */

async function loadProfile(){

  if(!currentUser)
    return;

  const { data, error } =
    await supabaseClient
      .from("profiles")
      .select("*")
      .eq("id", currentUser.id)
      .maybeSingle();

  if(error){

    console.error(error);

    showMessage(
      "Profil konnte nicht geladen werden: "+
      error.message
    );

    return;

  }

  if(data){

    playerData.id =
      data.id;

    playerData.username =
      data.username || "";

    playerData.email =
      currentUser.email || "";

    playerData.coins =
      Number(data.coins || 0);

  }else{

    playerData.id =
      currentUser.id;

    playerData.username =
      currentUser.user_metadata?.username ||
      "Spieler";

    playerData.email =
      currentUser.email || "";

    playerData.coins = 0;

    await saveProfile();

  }

}


/* =====================================================
   PROFILE SPEICHERN
===================================================== */

async function saveProfile(){

  if(!currentUser)
    return;

  const { error } =
    await supabaseClient
      .from("profiles")
      .upsert({

        id:currentUser.id,

        username:
          playerData.username,

        coins:
          playerData.coins

      });

  if(error){

    console.error(
      "Profil speichern:",
      error
    );

  }

}


/* =====================================================
   REGISTRIEREN
===================================================== */

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

    showMessage(
      "Benutzername muss mindestens 3 Zeichen haben."
    );

    return;

  }


  if(!email){

    showMessage(
      "Bitte E-Mail eingeben."
    );

    return;

  }


  if(password.length < 6){

    showMessage(
      "Passwort muss mindestens 6 Zeichen haben."
    );

    return;

  }


  showMessage(
    "Registrierung läuft..."
  );


  const { data, error } =
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

    showMessage(
      "Fehler: "+
      error.message
    );

    return;

  }


  if(!data.user){

    showMessage(
      "Registrierung fehlgeschlagen."
    );

    return;

  }


  currentUser =
    data.user;


  playerData.id =
    data.user.id;

  playerData.username =
    name;

  playerData.email =
    email;


  /*
    Falls E-Mail-Bestätigung aktiviert ist,
    gibt Supabase möglicherweise noch keine Session zurück.
  */

  if(!data.session){

    showMessage(
      "Registriert! Prüfe deine E-Mail und bestätige dein Konto."
    );

    return;

  }


  await saveProfile();

  showMenu();

}


/* =====================================================
   ANMELDEN
===================================================== */

async function loginUser(){

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


  if(!name){

    showMessage(
      "Bitte Benutzernamen eingeben."
    );

    return;

  }


  if(!email){

    showMessage(
      "Bitte E-Mail eingeben."
    );

    return;

  }


  if(!password){

    showMessage(
      "Bitte Passwort eingeben."
    );

    return;

  }


  showMessage(
    "Anmeldung läuft..."
  );


  const { data, error } =
    await supabaseClient.auth.signInWithPassword({

      email:email,

      password:password

    });


  if(error){

    showMessage(
      "Anmeldung fehlgeschlagen: "+
      error.message
    );

    return;

  }


  currentUser =
    data.user;


  await loadProfile();


  if(
    !playerData.username
  ){

    playerData.username =
      name;

    await saveProfile();

  }


  showMenu();

}


/* =====================================================
   ABMELDEN
===================================================== */

async function logout(){

  await supabaseClient
    .auth
    .signOut();

  currentUser = null;

  location.reload();

}


/* =====================================================
   CHARAKTERE
===================================================== */

function charactersMenu(){

  hideAll();

  const list =
    document.getElementById(
      "characterList"
    );

  list.innerHTML = "";

  document
    .getElementById("charCoins")
    .textContent =
    playerData.coins.toLocaleString();


  charData.forEach(
    (char,index)=>{

      const card =
        document.createElement(
          "div"
        );

      card.className =
        "card";


      const unlocked =
        playerData.characters
          .includes(index);


      card.innerHTML = `

        <div
          class="preview"
          style="
            background:${char[2]};
            color:#06121d;
            box-shadow:0 0 25px ${char[2]};
          "
        >
          C
        </div>

        <h3>${char[0]}</h3>

        <p>
          ${
            unlocked
              ? "FREIGESCHALTET"
              : char[1].toLocaleString()+" Coins"
          }
        </p>

      `;


      const button =
        document.createElement(
          "button"
        );


      if(unlocked){

        button.textContent =
          playerData.character === index
            ? "AKTIV"
            : "AUSWÄHLEN";


        button.onclick = ()=>{

          playerData.character =
            index;

          charactersMenu();

        };

      }else{

        button.textContent =
          "KAUFEN";


        button.onclick = async ()=>{

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


          await saveProfile();

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
    .getElementById("characters")
    .classList.remove("hidden");

}


/* =====================================================
   STÄDTE
===================================================== */

function citiesMenu(){

  hideAll();

  const list =
    document.getElementById(
      "cityList"
    );

  list.innerHTML = "";


  cityData.forEach(
    (city,index)=>{

      const card =
        document.createElement(
          "div"
        );

      card.className =
        "card";


      const unlocked =
        playerData.cities
          .includes(index);


      card.innerHTML = `

        <div
          class="preview"
          style="
            background:${city.sky};
            border:2px solid #00eaff;
          "
        >
          ${index+1}
        </div>

        <h3>${city.name}</h3>

        <p>
          ${city.min}-${city.max} Coins
        </p>

        <p>
          ${
            city.price===0
              ? "KOSTENLOS"
              : city.price.toLocaleString()+" Coins"
          }
        </p>

      `;


      const button =
        document.createElement(
          "button"
        );


      if(unlocked){

        button.textContent =
          playerData.city === index
            ? "AKTIV"
            : "AUSWÄHLEN";


        button.onclick = ()=>{

          playerData.city =
            index;

          citiesMenu();

        };

      }else{

        button.textContent =
          "FREISCHALTEN";


        button.onclick =
          async ()=>{

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


            await saveProfile();

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
    .getElementById("cities")
    .classList.remove("hidden");

}


/* =====================================================
   CODE
===================================================== */

function codeMenu(){

  hideAll();

  document
    .getElementById("secretCode")
    .value = "";

  document
    .getElementById("codeMsg")
    .textContent = "";

  document
    .getElementById("codes")
    .classList.remove(
      "hidden"
    );

}


async function redeemCode(){

  const code =
    document
      .getElementById(
        "secretCode"
      )
      .value;

  const msg =
    document.getElementById(
      "codeMsg"
    );


  if(code === "110"){

    playerData.coins +=
      100000;

    playerData.characters =
      charData.map(
        (_,i)=>i
      );

    playerData.cities =
      cityData.map(
        (_,i)=>i
      );

    msg.textContent =
      "Code 110 aktiviert!";

  }

  else if(code === "112"){

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

    return;

  }


  await saveProfile();

}


/* =====================================================
   RANGLISTE
===================================================== */

async function rankingMenu(){

  hideAll();

  const list =
    document.getElementById(
      "rankingList"
    );

  list.innerHTML =
    "Lade Rangliste...";


  const { data, error } =
    await supabaseClient
      .from("profiles")
      .select(
        "username,coins"
      )
      .order(
        "coins",
        {
          ascending:false
        }
      )
      .limit(50);


  if(error){

    list.innerHTML =
      "Rangliste konnte nicht geladen werden.";

    console.error(error);

    document
      .getElementById("ranking")
      .classList.remove(
        "hidden"
      );

    return;

  }


  list.innerHTML = "";


  if(!data || data.length===0){

    list.innerHTML =
      "Noch keine Spieler.";

  }


  data.forEach(
    (p,index)=>{

      const row =
        document.createElement(
          "div"
        );

      row.className =
        "rank";


      row.innerHTML = `

        <span>
          ${index+1}.
          ${p.username || "Spieler"}
        </span>

        <b>
          ${Number(
            p.coins || 0
          ).toLocaleString()}
        </b>

      `;


      list.appendChild(
        row
      );

    }
  );


  document
    .getElementById("ranking")
    .classList.remove(
      "hidden"
    );

}


/* =====================================================
   SPIEL
===================================================== */

const canvas =
  document.getElementById(
    "game"
  );

const ctx =
  canvas.getContext("2d");


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


let running = false;
let paused = false;

let score = 0;
let runCoins = 0;

let lane = 0;
let playerX = 0;

let objects = [];

let speed = 250;

let last =
  performance.now();

let magnet = 0;
let doubleCoins = 0;

let powerTimer = 10;


/* =====================================================
   ROAD
===================================================== */

function road(){

  const width =
    Math.min(
      innerWidth*.84,
      600
    );

  return {

    left:
      (innerWidth-width)/2,

    width

  };

}


function laneX(n){

  const r =
    road();

  return r.left +
    r.width *
    (n+1.5)/3;

}


/* =====================================================
   START
===================================================== */

function startGame(){

  hideAll();

  running = true;
  paused = false;

  score = 0;
  runCoins = 0;

  lane = 0;

  playerX =
    laneX(0);

  objects = [];

  speed = 250;

  magnet = 0;
  doubleCoins = 0;

  powerTimer = 10;

  document
    .getElementById("hud")
    .style.display =
    "flex";

  document
    .getElementById("pause")
    .style.display =
    "block";

  last =
    performance.now();

}


/* =====================================================
   WELT
===================================================== */

function drawWorld(){

  const r =
    road();


  const city =
    cityData[
      playerData.city
    ];


  ctx.fillStyle =
    city.sky;

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


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer(){

  const y =
    innerHeight*.72;

  const color =
    charData[
      playerData.character
    ][2];


  ctx.save();

  ctx.translate(
    playerX,
    y
  );


  ctx.fillStyle =
    color;

  ctx.shadowColor =
    color;

  ctx.shadowBlur =
    25;


  ctx.beginPath();

  ctx.roundRect(
    -28,
    -28,
    56,
    56,
    12
  );

  ctx.fill();


  ctx.shadowBlur = 0;


  ctx.fillStyle =
    "#06121d";


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


/* =====================================================
   AUTOS
===================================================== */

function spawnCar(){

  /*
    Autos bekommen eine eigene Spur
    und werden nicht zusammen mit Coins
    am gleichen Punkt erzeugt.
  */

  objects.push({

    type:"car",

    lane:
      Math.floor(
        Math.random()*3
      )-1,

    y:-160,

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

  ctx.shadowBlur =
    18;


  ctx.beginPath();

  ctx.roundRect(
    -32,
    -50,
    64,
    100,
    10
  );

  ctx.fill();


  ctx.shadowBlur = 0;


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


  ctx.fillStyle =
    "#fff";


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


/* =====================================================
   COINS
===================================================== */

function spawnCoins(){

  const city =
    cityData[
      playerData.city
    ];


  const amount =
    city.min +
    Math.floor(
      Math.random() *
      (
        city.max-city.min+1
      )
    );


  /*
    Coins werden auf einer anderen
    Spur als das zuletzt erzeugte Auto
    erzeugt.
  */

  let randomLane =
    Math.floor(
      Math.random()*3
    )-1;


  const cars =
    objects.filter(
      o =>
        o.type==="car" &&
        o.y < 150
    );


  if(
    cars.some(
      c =>
        c.lane === randomLane
    )
  ){

    randomLane =
      [-1,0,1].find(
        l =>
          !cars.some(
            c =>
              c.lane === l
          )
      ) ?? randomLane;

  }


  for(
    let i=0;
    i<amount;
    i++
  ){

    objects.push({

      type:"coin",

      lane:randomLane,

      y:-60-i*75

    });

  }

}


function drawCoin(o,time){

  let x =
    laneX(
      o.lane
    );

  let y =
    o.y;


  if(magnet>0){

    x +=
      (playerX-x)*.18;

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

  ctx.shadowBlur =
    20;


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


/* =====================================================
   POWERUPS
===================================================== */

function spawnPower(){

  objects.push({

    type:
      Math.random()<.5
        ? "magnet"
        : "double",

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


  ctx.fillStyle =
    o.type==="magnet"
      ? "#00eaff"
      : "#ffd900";


  ctx.shadowColor =
    ctx.fillStyle;

  ctx.shadowBlur =
    20;


  ctx.beginPath();

  ctx.arc(
    0,
    0,
    22,
    0,
    Math.PI*2
  );

  ctx.fill();


  ctx.fillStyle =
    "#06121d";

  ctx.font =
    "bold 16px Arial";

  ctx.textAlign =
    "center";

  ctx.textBaseline =
    "middle";


  ctx.fillText(
    o.type==="magnet"
      ? "M"
      : "x2",
    0,
    0
  );


  ctx.restore();

}


/* =====================================================
   UPDATE
===================================================== */

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
    magnet -= seconds;


  if(doubleCoins>0)
    doubleCoins -= seconds;


  powerTimer -= seconds;


  if(
    powerTimer<=0
  ){

    spawnPower();

    powerTimer =
      15+
      Math.random()*15;

  }


  for(
    let i=objects.length-1;
    i>=0;
    i--
  ){

    const o =
      objects[i];


    o.y +=
      speed*seconds;


    if(
      o.y >
      innerHeight+150
    ){

      objects.splice(
        i,
        1
      );

      continue;

    }


    /*
      POWERUP
    */

    if(
      (
        o.type==="magnet" ||
        o.type==="double"
      ) &&
      Math.abs(
        o.y-
        innerHeight*.72
      )<55 &&
      Math.abs(
        laneX(o.lane)-
        playerX
      )<50
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


    /*
      COIN
    */

    if(
      o.type==="coin"
    ){

      let coinX =
        laneX(o.lane);


      if(magnet>0){

        coinX =
          playerX;

      }


      if(
        Math.abs(
          o.y-
          innerHeight*.72
        )<50 &&
        Math.abs(
          coinX-
          playerX
        )<55
      ){

        runCoins +=
          doubleCoins>0
            ? 2
            : 1;


        objects.splice(
          i,
          1
        );

        continue;

      }

    }


    /*
      AUTO
    */

    if(
      o.type==="car" &&
      Math.abs(
        o.y-
        innerHeight*.72
      )<65 &&
      Math.abs(
        laneX(o.lane)-
        playerX
      )<45
    ){

      endGame();

      return;

    }

  }


  /*
    Autos etwas kontrollierter erzeugen.
  */

  if(
    Math.random() <
    dt/1000/1.4
  ){

    spawnCar();

  }


  if(
    Math.random() <
    dt/1000/2.0
  ){

    spawnCoins();

  }


  document
    .getElementById("score")
    .textContent =
    Math.floor(
      score
    ).toLocaleString();


  document
    .getElementById("coins")
    .textContent =
    (
      playerData.coins+
      runCoins
    ).toLocaleString();


  document
    .getElementById("cityName")
    .textContent =
    cityData[
      playerData.city
    ].name;

}


/* =====================================================
   LOOP
===================================================== */

function loop(time){

  const dt =
    Math.min(
      40,
      time-last
    );

  last =
    time;


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
        drawCoin(o,time);

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


/* =====================================================
   TASTATUR
===================================================== */

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


/* =====================================================
   TOUCH / WISCHEN
===================================================== */

let touchStart = 0;


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


/* =====================================================
   PAUSE
===================================================== */

document
  .getElementById("pause")
  .onclick = ()=>{

    if(!running)
      return;


    paused = true;


    document
      .getElementById("pause")
      .style.display =
      "none";


    document
      .getElementById("pauseScreen")
      .classList.remove(
        "hidden"
      );

  };


function resumeGame(){

  paused = false;


  document
    .getElementById("pauseScreen")
    .classList.add(
      "hidden"
    );


  document
    .getElementById("pause")
    .style.display =
    "block";


  last =
    performance.now();

}


function quitGame(){

  running = false;


  document
    .getElementById("pauseScreen")
    .classList.add(
      "hidden"
    );


  document
    .getElementById("hud")
    .style.display =
    "none";


  showMenu();

}


/* =====================================================
   GAME OVER
===================================================== */

async function endGame(){

  if(!running)
    return;


  running = false;


  playerData.coins +=
    runCoins;


  await saveProfile();


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


  document
    .getElementById("hud")
    .style.display =
    "none";


  document
    .getElementById("pause")
    .style.display =
    "none";


  document
    .getElementById(
      "gameOverText"
    )
    .innerHTML = `

      <p>
        SCORE:
        <b>
          ${finalScore.toLocaleString()}
        </b>
      </p>

      <p>
        COINS:
        <b>
          ${runCoins}
        </b>
      </p>

      <p>
        GESAMT:
        <b>
          ${playerData.coins.toLocaleString()}
        </b>
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


/* =====================================================
   BUTTONS
===================================================== */

document
  .getElementById("registerBtn")
  .onclick =
  register;


document
  .getElementById("loginBtn")
  .onclick =
  loginUser;


document
  .getElementById("startBtn")
  .onclick =
  startGame;


document
  .getElementById("charactersBtn")
  .onclick =
  charactersMenu;


document
  .getElementById("citiesBtn")
  .onclick =
  citiesMenu;


document
  .getElementById("rankingBtn")
  .onclick =
  rankingMenu;


document
  .getElementById("codeBtn")
  .onclick =
  codeMenu;


document
  .getElementById("logoutBtn")
  .onclick =
  logout;


document
  .getElementById("charactersBack")
  .onclick =
  showMenu;


document
  .getElementById("citiesBack")
  .onclick =
  showMenu;


document
  .getElementById("rankingBack")
  .onclick =
  showMenu;


document
  .getElementById("codeBack")
  .onclick =
  showMenu;


document
  .getElementById("redeemBtn")
  .onclick =
  redeemCode;


document
  .getElementById("resumeBtn")
  .onclick =
  resumeGame;


document
  .getElementById("quitBtn")
  .onclick =
  quitGame;


document
  .getElementById("restartBtn")
  .onclick =
  restartGame;


document
  .getElementById("gameOverMenuBtn")
  .onclick =
  showMenu;


/* =====================================================
   ENTER-TASTE
===================================================== */

document
  .getElementById("password")
  .addEventListener(
    "keydown",
    e=>{

      if(
        e.key==="Enter"
      ){

        loginUser();

      }

    }
  );


/* =====================================================
   AUTOMATISCH EINLOGGEN
===================================================== */

async function checkLogin(){

  const {
    data
  } =
    await supabaseClient
      .auth
      .getSession();


  if(
    data &&
    data.session
  ){

    currentUser =
      data.session.user;


    await loadProfile();

    showMenu();

  }

}


checkLogin();

</script>

</body>
</html>
