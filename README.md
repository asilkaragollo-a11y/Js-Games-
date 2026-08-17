<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">

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
    background:#02040a;
    color:white;
    font-family:Arial,Helvetica,sans-serif;
}

body{
    user-select:none;
}

button,input{
    font:inherit;
}

button{
    border:0;
    cursor:pointer;
    touch-action:manipulation;
}

#gameCanvas{
    position:fixed;
    inset:0;
    width:100%;
    height:100%;
    display:none;
}

/* =========================
   APP
========================= */

.screen{
    position:fixed;
    inset:0;
    z-index:100;
    display:flex;
    align-items:center;
    justify-content:center;
    padding:18px;
    overflow-y:auto;

    background:
        radial-gradient(
            circle at 50% 10%,
            #073c56 0%,
            #03101a 35%,
            #02040a 80%
        );
}

.screen.hidden{
    display:none;
}

.panel{
    width:min(430px,100%);
    padding:26px 20px;
    border-radius:26px;

    background:
        linear-gradient(
            145deg,
            rgba(10,30,43,.96),
            rgba(3,10,17,.96)
        );

    border:1px solid rgba(0,234,255,.28);

    box-shadow:
        0 0 45px rgba(0,234,255,.12),
        inset 0 1px rgba(255,255,255,.05);

    text-align:center;
}

.logo{
    font-size:44px;
    line-height:1;
    font-weight:1000;
    letter-spacing:2px;
    color:#00eaff;
    text-shadow:
        0 0 12px #00eaff,
        0 0 35px rgba(0,234,255,.45);

    margin-bottom:8px;
}

.logoSmall{
    font-size:28px;
}

.subtitle{
    color:#9fc5d0;
    font-size:14px;
    line-height:1.5;
    margin-bottom:20px;
}

.field{
    width:100%;
    height:54px;
    margin:7px 0;
    padding:0 16px;

    border-radius:14px;
    border:1px solid rgba(0,234,255,.35);

    background:#06131d;
    color:white;

    outline:none;
}

.field:focus{
    border-color:#00eaff;
    box-shadow:0 0 18px rgba(0,234,255,.16);
}

.primary{
    width:100%;
    min-height:54px;
    margin-top:10px;

    border-radius:14px;

    background:linear-gradient(
        135deg,
        #00eaff,
        #00a9ff
    );

    color:#001018;
    font-weight:900;

    box-shadow:
        0 8px 25px rgba(0,174,255,.22);
}

.secondary{
    width:100%;
    min-height:50px;
    margin-top:8px;

    border-radius:14px;

    background:#0b1d29;
    color:white;

    border:1px solid rgba(0,234,255,.2);
}

button:active{
    transform:scale(.98);
}

.message{
    min-height:25px;
    margin-top:10px;
    color:#9defff;
    font-size:14px;
}

.menuStats{
    display:grid;
    grid-template-columns:1fr 1fr;
    gap:10px;
    margin:18px 0;
}

.stat{
    padding:15px 10px;
    border-radius:16px;
    background:#06131d;
    border:1px solid rgba(0,234,255,.14);
}

.statTitle{
    font-size:12px;
    color:#7ca5b2;
}

.statValue{
    margin-top:5px;
    font-size:22px;
    font-weight:900;
}

/* =========================
   CITIES
========================= */

.cityList{
    display:flex;
    flex-direction:column;
    gap:10px;
    max-height:58vh;
    overflow-y:auto;
}

.city{
    padding:14px;
    border-radius:18px;
    border:1px solid rgba(255,255,255,.08);
    background:#07141e;
    text-align:left;
}

.city.active{
    border-color:#00eaff;
    box-shadow:0 0 18px rgba(0,234,255,.12);
}

.cityName{
    font-weight:900;
    font-size:18px;
}

.cityInfo{
    margin-top:5px;
    color:#8eb5c0;
    font-size:13px;
}

/* =========================
   RANKING
========================= */

.ranking{
    max-height:55vh;
    overflow-y:auto;
    margin:15px 0;
}

.rank{
    display:flex;
    align-items:center;
    justify-content:space-between;

    padding:13px;
    margin:6px 0;

    border-radius:13px;
    background:#071720;
}

.rankLeft{
    display:flex;
    gap:10px;
    align-items:center;
}

.rankNumber{
    width:30px;
    font-weight:900;
    color:#00eaff;
}

.rankName{
    font-weight:700;
}

.rankScore{
    font-weight:900;
}

/* =========================
   HUD
========================= */

#hud{
    position:fixed;
    top:10px;
    left:10px;
    right:10px;
    z-index:30;

    display:none;

    align-items:center;
    justify-content:space-between;

    pointer-events:none;
}

.hudItem{
    padding:9px 12px;
    border-radius:12px;

    background:rgba(3,14,22,.82);
    border:1px solid rgba(0,234,255,.25);

    font-size:14px;
    font-weight:900;
}

#pauseBtn{
    position:fixed;
    top:65px;
    right:12px;
    z-index:40;

    display:none;

    width:82px;
    height:42px;
    border-radius:12px;

    background:#071720;
    color:white;

    border:1px solid rgba(0,234,255,.3);
}

#jumpBtn{
    position:fixed;
    right:18px;
    bottom:22px;
    z-index:40;

    display:none;

    width:105px;
    height:60px;
    border-radius:18px;

    background:linear-gradient(
        135deg,
        #00eaff,
        #007cff
    );

    color:#001018;
    font-weight:1000;

    box-shadow:0 0 25px rgba(0,234,255,.2);
}

#moveButtons{
    position:fixed;
    left:18px;
    bottom:22px;
    z-index:40;

    display:none;

    gap:8px;
}

.moveBtn{
    width:58px;
    height:58px;

    border-radius:17px;

    background:#071720;
    color:white;

    border:1px solid rgba(0,234,255,.3);

    font-size:23px;
}

/* =========================
   MOBILE
========================= */

@media(max-width:500px){

    .panel{
        padding:23px 16px;
    }

    .logo{
        font-size:37px;
    }

    .field{
        height:52px;
    }
}
</style>
</head>

<body>

<canvas id="gameCanvas"></canvas>

<!-- =========================
     LOGIN
========================= -->

<section id="loginScreen" class="screen">
    <div class="panel">

        <div class="logo">CUBE RUSH</div>

        <div class="subtitle">
            Laufe durch futuristische Städte,
            sammle Coins und schlage deinen Highscore.
        </div>

        <input
            id="loginEmail"
            class="field"
            type="email"
            placeholder="E-Mail"
            autocomplete="email"
        >

        <input
            id="loginPassword"
            class="field"
            type="password"
            placeholder="Passwort"
            autocomplete="current-password"
        >

        <button id="loginBtn" class="primary">
            ANMELDEN
        </button>

        <button id="openRegisterBtn" class="secondary">
            ACCOUNT ERSTELLEN
        </button>

        <div id="loginMessage" class="message"></div>
    </div>
</section>


<!-- =========================
     REGISTER
========================= -->

<section id="registerScreen" class="screen hidden">
    <div class="panel">

        <div class="logo">CUBE RUSH</div>

        <h2>Account erstellen</h2>

        <div class="subtitle">
            Dein Benutzername erscheint später
            in der Rangliste.
        </div>

        <input
            id="registerUsername"
            class="field"
            type="text"
            maxlength="20"
            placeholder="Benutzername"
            autocomplete="username"
        >

        <input
            id="registerEmail"
            class="field"
            type="email"
            placeholder="E-Mail"
            autocomplete="email"
        >

        <input
            id="registerPassword"
            class="field"
            type="password"
            placeholder="Passwort"
            autocomplete="new-password"
        >

        <button id="registerBtn" class="primary">
            ACCOUNT ERSTELLEN
        </button>

        <button id="backLoginBtn" class="secondary">
            ZURÜCK ZUM LOGIN
        </button>

        <div id="registerMessage" class="message"></div>
    </div>
</section>


<!-- =========================
     MENU
========================= -->

<section id="menuScreen" class="screen hidden">
    <div class="panel">

        <div class="logo logoSmall">CUBE RUSH</div>

        <h2>
            Willkommen,
            <span id="usernameLabel">Spieler</span> 👋
        </h2>

        <div class="menuStats">

            <div class="stat">
                <div class="statTitle">COINS</div>
                <div id="coinsLabel" class="statValue">0</div>
            </div>

            <div class="stat">
                <div class="statTitle">HIGHSCORE</div>
                <div id="highscoreLabel" class="statValue">0</div>
            </div>

        </div>

        <button id="startBtn" class="primary">
            ▶ SPIEL STARTEN
        </button>

        <button id="citiesBtn" class="secondary">
            🌆 WELTEN
        </button>

        <button id="rankingBtn" class="secondary">
            🏆 RANGLISTE
        </button>

        <button id="logoutBtn" class="secondary">
            ABMELDEN
        </button>

    </div>
</section>


<!-- =========================
     CITIES
========================= -->

<section id="citiesScreen" class="screen hidden">
    <div class="panel">

        <div class="logo logoSmall">WELTEN</div>

        <div id="cityList" class="cityList"></div>

        <button id="citiesBackBtn" class="secondary">
            ZURÜCK
        </button>

    </div>
</section>


<!-- =========================
     RANKING
========================= -->

<section id="rankingScreen" class="screen hidden">
    <div class="panel">

        <div class="logo logoSmall">RANGLISTE</div>

        <div id="rankingList" class="ranking">
            Lade Rangliste...
        </div>

        <button id="rankingBackBtn" class="secondary">
            ZURÜCK
        </button>

    </div>
</section>


<!-- =========================
     PAUSE
========================= -->

<section id="pauseScreen" class="screen hidden">
    <div class="panel">

        <div class="logo logoSmall">PAUSE</div>

        <button id="resumeBtn" class="primary">
            ▶ WEITER
        </button>

        <button id="quitBtn" class="secondary">
            MENÜ
        </button>

    </div>
</section>


<!-- =========================
     GAME OVER
========================= -->

<section id="gameOverScreen" class="screen hidden">
    <div class="panel">

        <div class="logo">CRASH!</div>

        <div class="menuStats">

            <div class="stat">
                <div class="statTitle">SCORE</div>
                <div id="finalScore" class="statValue">0</div>
            </div>

            <div class="stat">
                <div class="statTitle">COINS</div>
                <div id="finalCoins" class="statValue">0</div>
            </div>

        </div>

        <div class="stat">
            <div class="statTitle">DEIN HIGHSCORE</div>
            <div id="finalHighscore" class="statValue">0</div>
        </div>

        <button id="restartBtn" class="primary">
            🔄 NOCHMAL
        </button>

        <button id="gameOverMenuBtn" class="secondary">
            MENÜ
        </button>

    </div>
</section>


<!-- =========================
     GAME UI
========================= -->

<div id="hud">

    <div class="hudItem">
        🪙 <span id="hudCoins">0</span>
    </div>

    <div class="hudItem">
        🏆 <span id="hudScore">0</span>
    </div>

    <div class="hudItem">
        🌆 <span id="hudCity">Neon City</span>
    </div>

</div>

<button id="pauseBtn">⏸</button>

<div id="moveButtons">
    <button id="leftBtn" class="moveBtn">←</button>
    <button id="rightBtn" class="moveBtn">→</button>
</div>

<button id="jumpBtn">⬆ SPRINGEN</button>


<script>

/* =========================================================
   SUPABASE
========================================================= */

/*
   DEIN SUPABASE PROJEKT
*/

const SUPABASE_URL =
    "https://lfsifdmaftztykpckdsh.supabase.co";


/*
   HIER NUR DEINEN
   PUBLISHABLE / ANON KEY EINSETZEN.

   NICHT service_role verwenden.
*/

const SUPABASE_ANON_KEY =
    "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfi


/* =========================================================
   APP-DATEN
========================================================= */

let authToken = null;

let player = {
    id:null,
    username:"",
    email:"",
    coins:0,
    highscore:0,
    city:0,
    unlocked:[0]
};


/* =========================================================
   WELTEN
========================================================= */

const WORLDS = [

    {
        name:"Neon City",
        minCoins:3,
        maxCoins:6,
        price:0,
        sky:"#03151e"
    },

    {
        name:"Tokyo Night",
        minCoins:4,
        maxCoins:8,
        price:5000,
        sky:"#18051c"
    },

    {
        name:"Cyber Dubai",
        minCoins:5,
        maxCoins:10,
        price:12000,
        sky:"#211000"
    },

    {
        name:"Future New York",
        minCoins:6,
        maxCoins:12,
        price:25000,
        sky:"#00162c"
    },

    {
        name:"Galaxy City",
        minCoins:7,
        maxCoins:14,
        price:50000,
        sky:"#17002d"
    }

];


/* =========================================================
   ELEMENTE
========================================================= */

const $ = id =>
    document.getElementById(id);


function show(id){

    document
        .querySelectorAll(".screen")
        .forEach(x =>
            x.classList.add("hidden")
        );

    $(id).classList.remove("hidden");
}


function setMessage(id,text){

    $(id).textContent = text;
}


/* =========================================================
   SUPABASE REQUEST
========================================================= */

async function api(path,options={}){

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

        "apikey":
            SUPABASE_ANON_KEY,

        "Content-Type":
            "application/json",

        ...(options.headers || {})

    };


    if(authToken){

        headers.Authorization =
            "Bearer " + authToken;
    }


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
            data?.msg ||
            data?.message ||
            data?.error_description ||
            text ||
            "Supabase-Fehler"
        );
    }


    return data;
}


/* =========================================================
   REGISTER
========================================================= */

$("registerBtn").onclick =
    register;


async function register(){

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

        setMessage(
            "registerMessage",
            "❌ Benutzername muss mindestens 3 Zeichen haben."
        );

        return;
    }


    if(!/^[a-zA-Z0-9_]+$/.test(username)){

        setMessage(
            "registerMessage",
            "❌ Benutzername darf nur Buchstaben, Zahlen und _ enthalten."
        );

        return;
    }


    if(!email.includes("@")){

        setMessage(
            "registerMessage",
            "❌ Ungültige E-Mail."
        );

        return;
    }


    if(password.length < 6){

        setMessage(
            "registerMessage",
            "❌ Passwort muss mindestens 6 Zeichen haben."
        );

        return;
    }


    setMessage(
        "registerMessage",
        "⏳ Account wird erstellt..."
    );


    try{

        const data =
            await api(
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

        player.email =
            email;

        player.username =
            username;


        /*
          Wenn Supabase E-Mail-Bestätigung
          verlangt, gibt es noch keinen Token.
        */

        if(!data.access_token){

            setMessage(
                "registerMessage",
                "✅ Account erstellt. Bestätige deine E-Mail und melde dich danach an."
            );

            return;
        }


        authToken =
            data.access_token;


        await createPlayer();


        saveLocal();


        updateMenu();

        show("menuScreen");


    }
    catch(error){

        console.error(error);

        setMessage(
            "registerMessage",
            "❌ " + error.message
        );
    }
}


/* =========================================================
   PLAYER ERSTELLEN
========================================================= */

async function createPlayer(){

    try{

        await api(
            "/rest/v1/Players",
            {
                method:"POST",

                headers:{
                    "Prefer":
                        "resolution=ignore-duplicates,return=minimal"
                },

                body:JSON.stringify({

                    id:player.id,

                    username:player.username,

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


/* =========================================================
   LOGIN
========================================================= */

$("loginBtn").onclick =
    login;


async function login(){

    const email =
        $("loginEmail")
        .value
        .trim();

    const password =
        $("loginPassword")
        .value;


    if(!email || !password){

        setMessage(
            "loginMessage",
            "❌ E-Mail und Passwort eingeben."
        );

        return;
    }


    setMessage(
        "loginMessage",
        "⏳ Anmeldung..."
    );


    try{

        const data =
            await api(
                "/auth/v1/token?grant_type=password",
                {
                    method:"POST",

                    body:JSON.stringify({

                        email,
                        password

                    })
                }
            );


        authToken =
            data.access_token;


        player.id =
            data.user.id;

        player.email =
            data.user.email;


        player.username =
            data.user.user_metadata?.username ||
            data.user.email.split("@")[0];


        await loadPlayer();


        loadLocal();


        updateMenu();

        show("menuScreen");

    }
    catch(error){

        console.error(error);

        setMessage(
            "loginMessage",
            "❌ " + error.message
        );
    }
}


/* =========================================================
   PLAYER LADEN
========================================================= */

async function loadPlayer(){

    const rows =
        await api(
            "/rest/v1/Players" +
            "?id=eq." +
            encodeURIComponent(player.id) +
            "&select=*"
        );


    if(rows && rows.length){

        const row =
            rows[0];


        player.username =
            row.username ||
            player.username;


        player.highscore =
            Number(
                row.highscore || 0
            );
    }
    else{

        await createPlayer();
    }
}


/* =========================================================
   HIGHSCORE
========================================================= */

async function saveHighscore(){

    try{

        await api(
            "/rest/v1/Players?id=eq." +
            encodeURIComponent(player.id),
            {
                method:"PATCH",

                headers:{
                    "Prefer":
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

        console.error(
            "Highscore:",
            error.message
        );
    }
}


/* =========================================================
   RANGLISTE
========================================================= */

$("rankingBtn").onclick =
    loadRanking;


async function loadRanking(){

    show("rankingScreen");

    $("rankingList").textContent =
        "⏳ Lade Rangliste...";


    try{

        const rows =
            await api(
                "/rest/v1/Players" +
                "?select=username,highscore" +
                "&order=highscore.desc" +
                "&limit=100"
            );


        $("rankingList").innerHTML = "";


        if(!rows.length){

            $("rankingList").textContent =
                "Noch keine Spieler.";

            return;
        }


        rows.forEach(
            (row,index)=>{

                const div =
                    document.createElement("div");

                div.className =
                    "rank";


                div.innerHTML = `

                    <div class="rankLeft">

                        <div class="rankNumber">
                            ${index + 1}
                        </div>

                        <div class="rankName">
                            ${safe(row.username)}
                        </div>

                    </div>

                    <div class="rankScore">
                        ${Number(
                            row.highscore || 0
                        ).toLocaleString()}
                    </div>
                `;


                $("rankingList")
                    .appendChild(div);
            }
        );

    }
    catch(error){

        $("rankingList").textContent =
            "❌ " + error.message;
    }
}


/* =========================================================
   HTML SICHERHEIT
========================================================= */

function safe(value){

    return String(value || "")
        .replaceAll("&","&amp;")
        .replaceAll("<","&lt;")
        .replaceAll(">","&gt;")
        .replaceAll('"',"&quot;")
        .replaceAll("'","&#039;");
}


/* =========================================================
   LOCAL STORAGE
========================================================= */

function saveLocal(){

    localStorage.setItem(
        "cubeCoins",
        player.coins
    );

    localStorage.setItem(
        "cubeCity",
        player.city
    );

    localStorage.setItem(
        "cubeUnlocked",
        JSON.stringify(
            player.unlocked
        )
    );
}


function loadLocal(){

    player.coins =
        Number(
            localStorage.getItem(
                "cubeCoins"
            ) || 0
        );


    player.city =
        Number(
            localStorage.getItem(
                "cubeCity"
            ) || 0
        );


    try{

        const saved =
            JSON.parse(
                localStorage.getItem(
                    "cubeUnlocked"
                ) || "[0]"
            );


        if(Array.isArray(saved)){

            player.unlocked =
                saved;
        }

    }
    catch{

        player.unlocked =
            [0];
    }


    if(
        !player.unlocked.includes(0)
    ){

        player.unlocked.unshift(0);
    }
}


/* =========================================================
   MENU
========================================================= */

function updateMenu(){

    $("usernameLabel").textContent =
        player.username || "Spieler";


    $("coinsLabel").textContent =
        player.coins.toLocaleString();


    $("highscoreLabel").textContent =
        player.highscore.toLocaleString();
}


/* =========================================================
   BUTTONS
========================================================= */

$("openRegisterBtn").onclick =
    ()=>{
        setMessage("loginMessage","");
        show("registerScreen");
    };


$("backLoginBtn").onclick =
    ()=>{
        setMessage("registerMessage","");
        show("loginScreen");
    };


$("citiesBackBtn").onclick =
    ()=>{
        updateMenu();
        show("menuScreen");
    };


$("rankingBackBtn").onclick =
    ()=>{
        updateMenu();
        show("menuScreen");
    };


$("citiesBtn").onclick =
    showCities;


/* =========================================================
   WELTEN
========================================================= */

function showCities(){

    show("citiesScreen");

    $("cityList").innerHTML = "";


    WORLDS.forEach(
        (world,index)=>{

            const unlocked =
                player.unlocked.includes(index);

            const active =
                player.city === index;


            const div =
                document.createElement("div");

            div.className =
                "city" +
                (active ? " active" : "");


            div.innerHTML = `

                <div class="cityName">
                    🌆 ${world.name}
                </div>

                <div class="cityInfo">
                    🪙 ${world.minCoins}–${world.maxCoins}
                    Münzen pro Gruppe
                </div>

                <div class="cityInfo">
                    ${
                        world.price === 0
                        ? "Kostenlos"
                        : world.price.toLocaleString()
                          + " Coins"
                    }
                </div>

            `;


            const button =
                document.createElement("button");


            button.className =
                unlocked
                ? "primary"
                : "secondary";


            if(unlocked){

                button.textContent =
                    active
                    ? "✓ AKTIV"
                    : "AUSWÄHLEN";


                button.onclick =
                    ()=>{
                        player.city =
                            index;

                        saveLocal();

                        showCities();
                    };

            }
            else{

                button.textContent =
                    "FREISCHALTEN";


                button.onclick =
                    ()=>{

                        if(
                            player.coins <
                            world.price
                        ){

                            alert(
                                "Nicht genug Coins."
                            );

                            return;
                        }


                        player.coins -=
                            world.price;


                        player.unlocked.push(
                            index
                        );


                        player.city =
                            index;


                        saveLocal();

                        showCities();
                    };
            }


            div.appendChild(button);

            $("cityList")
                .appendChild(div);
        }
    );
}


/* =========================================================
   GAME
========================================================= */

const canvas =
    $("gameCanvas");

const ctx =
    canvas.getContext("2d");


let running = false;
let paused = false;

let score = 0;
let runCoins = 0;

let lane = 0;

let playerX = 0;

let jumpY = 0;
let jumpVelocity = 0;

let speed = 300;

let objects = [];

let coinTimer = 0;
let carTimer = 0;

let lastTime = 0;


function resize(){

    canvas.width =
        window.innerWidth;

    canvas.height =
        window.innerHeight;
}


window.addEventListener(
    "resize",
    resize
);

resize();


/* =========================================================
   ROAD
========================================================= */

function road(){

    const width =
        Math.min(
            canvas.width * .86,
            650
        );


    return {

        left:
            (canvas.width - width) / 2,

        width

    };
}


function laneX(value){

    const r =
        road();

    const laneWidth =
        r.width / 3;


    return (
        r.left +
        laneWidth * (
            value + 1
        ) +
        laneWidth / 2
    );
}


/* =========================================================
   START
========================================================= */

$("startBtn").onclick =
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

    $("pauseBtn").style.display =
        "block";

    $("jumpBtn").style.display =
        "block";

    $("moveButtons").style.display =
        "flex";


    running = true;
    paused = false;

    score = 0;
    runCoins = 0;

    lane = 0;

    speed = 300;

    objects = [];

    jumpY = 0;
    jumpVelocity = 0;

    coinTimer = 1.5;
    carTimer = 1;


    playerX =
        laneX(lane);


    lastTime =
        performance.now();
}


/* =========================================================
   BACKGROUND
========================================================= */

function drawBackground(){

    const world =
        WORLDS[player.city];


    const gradient =
        ctx.createLinearGradient(
            0,
            0,
            0,
            canvas.height
        );


    gradient.addColorStop(
        0,
        world.sky
    );


    gradient.addColorStop(
        1,
        "#02040a"
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
      Gebäude
    */

    for(
        let i=0;
        i<18;
        i++
    ){

        const buildingWidth =
            30 + (i%4)*15;

        const buildingHeight =
            100 + ((i*53)%180);

        const x =
            i * (
                canvas.width / 18
            );


        ctx.fillStyle =
            i%2
            ? "#07141d"
            : "#0a1b26";


        ctx.fillRect(
            x,
            canvas.height -
                buildingHeight -
                160,
            buildingWidth,
            buildingHeight
        );
    }


    const r =
        road();


    ctx.fillStyle =
        "#10171d";


    ctx.fillRect(
        r.left,
        0,
        r.width,
        canvas.height
    );


    ctx.fillStyle =
        "#00eaff";


    ctx.fillRect(
        r.left,
        0,
        3,
        canvas.height
    );


    ctx.fillRect(
        r.left+r.width-3,
        0,
        3,
        canvas.height
    );


    const laneWidth =
        r.width / 3;


    const offset =
        (
            score * 8
        ) % 90;


    ctx.fillStyle =
        "rgba(255,255,255,.65)";


    for(
        let y=-90+offset;
        y<canvas.height;
        y+=90
    ){

        ctx.fillRect(
            r.left+laneWidth-2,
            y,
            4,
            48
        );


        ctx.fillRect(
            r.left+laneWidth*2-2,
            y,
            4,
            48
        );
    }
}


/* =========================================================
   AUTOS
========================================================= */

function spawnCar(){

    /*
      Niemals alle drei Spuren
      gleichzeitig blockieren.
    */

    const available =
        [-1,0,1].filter(
            testLane =>
                !objects.some(
                    o =>
                        o.type === "car" &&
                        o.lane === testLane &&
                        o.y < 250
                )
        );


    if(!available.length)
        return;


    const chosen =
        available[
            Math.floor(
                Math.random() *
                available.length
            )
        ];


    objects.push({

        type:"car",

        lane:chosen,

        y:-120,

        color:[
            "#ff1744",
            "#008cff",
            "#ff9d00",
            "#ffffff",
            "#9b4dff"
        ][
            Math.floor(
                Math.random()*5
            )
        ]

    });
}


/* =========================================================
   MÜNZEN
========================================================= */

function spawnCoinGroup(){

    const world =
        WORLDS[player.city];


    /*
      DIE REGEL DER WELT WIRD HIER
      DIREKT EINGEHALTEN.

      NEON CITY = 3 BIS 6.
    */

    const amount =
        Math.floor(
            Math.random() *
            (
                world.maxCoins -
                world.minCoins +
                1
            )
        )
        +
        world.minCoins;


    const chosenLane =
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

            lane:chosenLane,

            y:
                -70 -
                i*62
        });
    }
}


/* =========================================================
   ZEICHNEN
========================================================= */

function drawObjects(){

    objects.forEach(
        object=>{

            const x =
                laneX(object.lane);


            if(object.type === "coin"){

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
                    -32,
                    -52,
                    64,
                    104,
                    12
                );


                ctx.fill();


                ctx.shadowBlur = 0;


                ctx.fillStyle =
                    "#142c3a";


                ctx.fillRect(
                    -20,
                    -30,
                    40,
                    25
                );


                ctx.fillStyle =
                    "#fff";


                ctx.fillRect(
                    -22,
                    29,
                    12,
                    7
                );


                ctx.fillRect(
                    10,
                    29,
                    12,
                    7
                );


                ctx.restore();
            }
        }
    );
}


/* =========================================================
   SPIELER
========================================================= */

function drawPlayer(){

    const x =
        playerX;


    const y =
        canvas.height*.76 -
        jumpY;


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
        -27,
        -27,
        54,
        54,
        10
    );


    ctx.fill();


    ctx.shadowBlur = 0;


    ctx.fillStyle =
        "#001018";


    ctx.fillRect(
        -16,
        -13,
        10,
        7
    );


    ctx.fillRect(
        6,
        -13,
        10,
        7
    );


    ctx.restore();
}


/* =========================================================
   JUMP
========================================================= */

function jump(){

    if(
        !running ||
        paused
    )
        return;


    if(jumpY <= 2){

        jumpVelocity =
            820;
    }
}


$("jumpBtn").onclick =
    jump;


/* =========================================================
   MOVE
========================================================= */

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


$("leftBtn").onclick =
    moveLeft;

$("rightBtn").onclick =
    moveRight;


/* =========================================================
   UPDATE
========================================================= */

function update(dt){

    if(!running || paused)
        return;


    const s =
        dt/1000;


    score +=
        s*10;


    speed =
        Math.min(
            720,
            speed+s*4
        );


    playerX +=
        (
            laneX(lane) -
            playerX
        )
        *
        Math.min(
            1,
            s*14
        );


    /*
      Sprung
    */

    jumpVelocity -=
        1900*s;


    jumpY +=
        jumpVelocity*s;


    if(jumpY <= 0){

        jumpY = 0;
        jumpVelocity = 0;
    }


    /*
      Coins
    */

    coinTimer -= s;


    if(coinTimer <= 0){

        spawnCoinGroup();


        /*
          Genug Abstand zwischen
          Münzgruppen.
        */

        coinTimer =
            3.0 +
            Math.random()*1.5;
    }


    /*
      Autos
    */

    carTimer -= s;


    if(carTimer <= 0){

        spawnCar();


        carTimer =
            Math.max(
                .85,
                1.5 -
                score/7000
            )
            +
            Math.random()*.6;
    }


    const playerY =
        canvas.height*.76 -
        jumpY;


    /*
      Objekte
    */

    for(
        let i=objects.length-1;
        i>=0;
        i--
    ){

        const object =
            objects[i];


        object.y +=
            speed*s;


        if(
            object.y >
            canvas.height+150
        ){

            objects.splice(i,1);

            continue;
        }


        /*
          Coin einsammeln
        */

        if(
            object.type === "coin" &&
            object.lane === lane &&
            Math.abs(
                object.y-playerY
            ) < 50
        ){

            runCoins++;

            objects.splice(i,1);

            continue;
        }


        /*
          Auto-Kollision

          Springen über Auto möglich.
        */

        if(
            object.type === "car" &&
            object.lane === lane &&
            Math.abs(
                object.y-playerY
            ) < 70
        ){

            if(jumpY < 85){

                gameOver();

                return;
            }
        }
    }


    $("hudCoins").textContent =
        (
            player.coins+
            runCoins
        ).toLocaleString();


    $("hudScore").textContent =
        Math.floor(score)
        .toLocaleString();


    $("hudCity").textContent =
        WORLDS[player.city].name;
}


/* =========================================================
   GAME LOOP
========================================================= */

function loop(time){

    const dt =
        Math.min(
            40,
            time-lastTime
        );


    lastTime =
        time;


    if(
        canvas.style.display !== "none"
    ){

        drawBackground();

        drawObjects();

        drawPlayer();

        update(dt);
    }


    requestAnimationFrame(loop);
}


requestAnimationFrame(loop);


/* =========================================================
   KEYBOARD
========================================================= */

document.addEventListener(
    "keydown",
    event=>{

        if(!running)
            return;


        if(
            event.key === "ArrowLeft" ||
            event.key.toLowerCase() === "a"
        ){

            moveLeft();
        }


        if(
            event.key === "ArrowRight" ||
            event.key.toLowerCase() === "d"
        ){

            moveRight();
        }


        if(
            event.key === "ArrowUp" ||
            event.key === " "
        ){

            event.preventDefault();

            jump();
        }
    }
);


/* =========================================================
   SWIPE
========================================================= */

let touchStartX = 0;
let touchStartY = 0;


canvas.addEventListener(
    "touchstart",
    event=>{

        const touch =
            event.changedTouches[0];


        touchStartX =
            touch.clientX;

        touchStartY =
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
            touch.clientX -
            touchStartX;


        const dy =
            touch.clientY -
            touchStartY;


        /*
          Swipe nach links/rechts
        */

        if(
            Math.abs(dx) >
            Math.abs(dy) &&
            Math.abs(dx)>30
        ){

            if(dx>0)
                moveRight();
            else
                moveLeft();

            return;
        }


        /*
          Swipe nach oben
        */

        if(
            dy < -30
        ){

            jump();
        }

    },
    {passive:true}
);


/* =========================================================
   PAUSE
========================================================= */

$("pauseBtn").onclick =
    pauseGame;


function pauseGame(){

    if(!running)
        return;


    paused = true;


    hideGameControls();

    show("pauseScreen");
}


$("resumeBtn").onclick =
    ()=>{

        paused = false;

        showGameControls();

        hideScreens();

        lastTime =
            performance.now();
    };


$("quitBtn").onclick =
    ()=>{

        running = false;

        hideGameControls();

        canvas.style.display =
            "none";

        updateMenu();

        show("menuScreen");
    };


function hideScreens(){

    document
        .querySelectorAll(".screen")
        .forEach(x =>
            x.classList.add("hidden")
        );
}


function showGameControls(){

    $("hud").style.display =
        "flex";

    $("pauseBtn").style.display =
        "block";

    $("jumpBtn").style.display =
        "block";

    $("moveButtons").style.display =
        "flex";
}


function hideGameControls(){

    $("hud").style.display =
        "none";

    $("pauseBtn").style.display =
        "none";

    $("jumpBtn").style.display =
        "none";

    $("moveButtons").style.display =
        "none";
}


/* =========================================================
   GAME OVER
========================================================= */

async function gameOver(){

    if(!running)
        return;


    running = false;


    hideGameControls();


    player.coins +=
        runCoins;


    const finalScore =
        Math.floor(score);


    let newHighscore =
        false;


    if(
        finalScore >
        player.highscore
    ){

        player.highscore =
            finalScore;

        newHighscore =
            true;
    }


    saveLocal();


    if(newHighscore){

        await saveHighscore();
    }


    $("finalScore").textContent =
        finalScore.toLocaleString();


    $("finalCoins").textContent =
        runCoins.toLocaleString();


    $("finalHighscore").textContent =
        player.highscore.toLocaleString();


    canvas.style.display =
        "none";


    show("gameOverScreen");
}


/* =========================================================
   RESTART
========================================================= */

$("restartBtn").onclick =
    startGame;


$("gameOverMenuBtn").onclick =
    ()=>{
        updateMenu();
        show("menuScreen");
    };


/* =========================================================
   LOGOUT
========================================================= */

$("logoutBtn").onclick =
    ()=>{

        running = false;

        authToken = null;

        hideGameControls();

        canvas.style.display =
            "none";

        show("loginScreen");
    };


/* =========================================================
   START APP
========================================================= */

loadLocal();

show("loginScreen");

</script>

</body>
</html>
