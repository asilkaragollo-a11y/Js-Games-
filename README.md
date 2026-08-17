<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#02040a;
    color:#fff;
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
    z-index:100;
    display:flex;
    flex-direction:column;
    align-items:center;
    justify-content:center;
    padding:20px;
    overflow:auto;
    background:
        radial-gradient(circle at 50% 20%,#08344c,#02040a 70%);
}

.hidden{display:none!important}

.logo{
    font-size:46px;
    font-weight:900;
    color:#00eaff;
    text-shadow:0 0 25px #00eaff;
    margin-bottom:10px;
    text-align:center;
}

.subtitle{
    color:#b8eaf2;
    text-align:center;
    margin-bottom:18px;
}

input{
    width:340px;
    max-width:95%;
    height:54px;
    margin:5px;
    padding:0 16px;
    border-radius:13px;
    border:2px solid #00eaff;
    background:#071522;
    color:white;
    font-size:16px;
    outline:none;
    z-index:200;
}

input:focus{
    box-shadow:0 0 20px #00eaff66;
}

button{
    width:340px;
    max-width:95%;
    min-height:52px;
    margin:5px;
    padding:12px 18px;
    border:0;
    border-radius:13px;
    background:#00eaff;
    color:#001018;
    font-size:16px;
    font-weight:800;
    cursor:pointer;
    z-index:200;
    touch-action:manipulation;
}

button:active{
    transform:scale(.97);
}

.secondary{
    background:#101e29;
    color:white;
    border:1px solid #00eaff66;
}

.message{
    width:340px;
    max-width:95%;
    min-height:28px;
    margin-top:8px;
    text-align:center;
    color:#a9efff;
}

.card{
    width:350px;
    max-width:95%;
    padding:16px;
    margin:6px;
    border-radius:16px;
    background:#071522;
    border:1px solid #00eaff44;
    text-align:center;
}

.cityBox{
    width:100%;
    height:55px;
    border-radius:12px;
    margin-bottom:10px;
}

.neon{
    background:linear-gradient(90deg,#001522,#00eaff,#001522);
    box-shadow:0 0 20px #00eaff;
}

.tokyo{
    background:linear-gradient(90deg,#180014,#ff0066,#180014);
}

.dubai{
    background:linear-gradient(90deg,#211000,#ff9900,#211000);
}

.newyork{
    background:linear-gradient(90deg,#00142b,#0077ff,#00142b);
}

.galaxy{
    background:linear-gradient(90deg,#16002c,#8b00ff,#001b33);
}

.rankRow{
    display:flex;
    justify-content:space-between;
    padding:11px;
    margin:4px 0;
    border-radius:10px;
    background:#10212d;
}

#hud{
    position:fixed;
    top:10px;
    left:8px;
    right:8px;
    z-index:50;
    display:none;
    justify-content:space-between;
    pointer-events:none;
}

.hudBox{
    background:#061522dd;
    border:1px solid #00eaff66;
    border-radius:10px;
    padding:9px 11px;
    font-weight:bold;
}

#pauseButton{
    position:fixed;
    top:60px;
    right:10px;
    width:auto;
    min-width:90px;
    z-index:60;
    display:none;
}

#jumpButton{
    position:fixed;
    bottom:25px;
    right:20px;
    width:100px;
    z-index:60;
    display:none;
}

.controlsHint{
    color:#9fd9e5;
    font-size:13px;
    text-align:center;
    margin:8px;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<!-- HUD -->
<div id="hud">
    <div class="hudBox">🪙 <span id="hudCoins">0</span></div>
    <div class="hudBox">🏆 <span id="hudScore">0</span></div>
    <div class="hudBox">🌆 <span id="hudCity">Neon City</span></div>
</div>

<button id="pauseButton">⏸ Pause</button>
<button id="jumpButton">⬆ Springen</button>


<!-- LOGIN -->
<section id="loginScreen" class="screen">

    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
        Melde dich an, um deinen Fortschritt zu speichern.
    </div>

    <input id="loginEmail"
           type="email"
           placeholder="📧 E-Mail"
           autocomplete="email">

    <input id="loginPassword"
           type="password"
           placeholder="🔒 Passwort"
           autocomplete="current-password">

    <button id="loginButton">
        ANMELDEN
    </button>

    <button id="openRegisterButton" class="secondary">
        ACCOUNT ERSTELLEN
    </button>

    <div id="loginMessage" class="message"></div>
</section>


<!-- REGISTRIERUNG -->
<section id="registerScreen" class="screen hidden">

    <div class="logo">CUBE RUSH</div>

    <h2>Account erstellen</h2>

    <input id="registerUsername"
           type="text"
           maxlength="20"
           placeholder="👤 Benutzername"
           autocomplete="username">

    <input id="registerEmail"
           type="email"
           placeholder="📧 E-Mail"
           autocomplete="email">

    <input id="registerPassword"
           type="password"
           placeholder="🔒 Passwort"
           autocomplete="new-password">

    <button id="registerButton">
        REGISTRIEREN
    </button>

    <button id="backLoginButton" class="secondary">
        ZURÜCK
    </button>

    <div id="registerMessage" class="message"></div>
</section>


<!-- MENÜ -->
<section id="menuScreen" class="screen hidden">

    <div class="logo">CUBE RUSH</div>

    <h2>
        Willkommen,
        <span id="menuUsername"></span> 👋
    </h2>

    <div class="card">
        🪙 Coins:
        <b id="menuCoins">0</b>
        <br><br>
        🏆 Highscore:
        <b id="menuHighscore">0</b>
    </div>

    <button id="startButton">
        ▶ SPIEL STARTEN
    </button>

    <button id="citiesButton">
        🌆 STÄDTE
    </button>

    <button id="rankingButton">
        🏆 RANGLISTE
    </button>

    <button id="logoutButton" class="secondary">
        ABMELDEN
    </button>
</section>


<!-- STÄDTE -->
<section id="citiesScreen" class="screen hidden">

    <div class="logo">STÄDTE</div>

    <div id="citiesList"></div>

    <button id="citiesBackButton" class="secondary">
        ZURÜCK
    </button>
</section>


<!-- RANGLISTE -->
<section id="rankingScreen" class="screen hidden">

    <div class="logo">RANGLISTE</div>

    <div id="rankingList" class="card">
        Lade Rangliste...
    </div>

    <button id="rankingBackButton" class="secondary">
        ZURÜCK
    </button>
</section>


<!-- PAUSE -->
<section id="pauseScreen" class="screen hidden">

    <div class="logo">PAUSE</div>

    <button id="resumeButton">
        ▶ WEITERSPIELEN
    </button>

    <button id="quitButton" class="secondary">
        MENÜ
    </button>
</section>


<!-- GAME OVER -->
<section id="gameOverScreen" class="screen hidden">

    <div class="logo">CRASH!</div>

    <div class="card">

        🏆 Score:
        <b id="finalScore">0</b>

        <br><br>

        🪙 Münzen:
        <b id="finalCoins">0</b>

        <br><br>

        👑 Highscore:
        <b id="finalHighscore">0</b>
    </div>

    <button id="restartButton">
        🔄 NOCHMAL
    </button>

    <button id="gameOverMenuButton" class="secondary">
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
=========================================================
HIER DEINEN ANON/PUBLIC KEY EINSETZEN.

NICHT den service_role-Key verwenden.
=========================================================
*/

const SUPABASE_ANON_KEY =
"DEIN_ANON_KEY_HIER";


let authToken = null;


/* =========================================================
   SPIELER
========================================================= */

let player = {
    id: null,
    username: "",
    email: "",
    coins: 0,
    highscore: 0,
    city: 0,
    unlockedCities: [0]
};


/* =========================================================
   STÄDTE UND MÜNZEN
=========================================================

   WICHTIG:
   Neon City = 3–6 Münzen pro Münzgruppe.

   Jede Welt hat ihre eigene Anzahl.
========================================================= */

const CITIES = [

    {
        name: "Neon City",
        price: 0,
        minCoins: 3,
        maxCoins: 6,
        css: "neon"
    },

    {
        name: "Tokyo Night",
        price: 5000,
        minCoins: 4,
        maxCoins: 8,
        css: "tokyo"
    },

    {
        name: "Cyber Dubai",
        price: 12000,
        minCoins: 5,
        maxCoins: 10,
        css: "dubai"
    },

    {
        name: "Future New York",
        price: 25000,
        minCoins: 6,
        maxCoins: 12,
        css: "newyork"
    },

    {
        name: "Galaxy City",
        price: 50000,
        minCoins: 7,
        maxCoins: 14,
        css: "galaxy"
    }
];


/* =========================================================
   HILFSFUNKTION
========================================================= */

function $(id){
    return document.getElementById(id);
}


function showScreen(id){

    document
        .querySelectorAll(".screen")
        .forEach(screen =>
            screen.classList.add("hidden")
        );

    $(id).classList.remove("hidden");
}


function message(id,text){
    $(id).textContent = text;
}


/* =========================================================
   SUPABASE REQUEST
========================================================= */

async function supabaseRequest(path, options = {}){

    if(
        !SUPABASE_ANON_KEY ||
        SUPABASE_ANON_KEY === "DEIN_ANON_KEY_HIER"
    ){
        throw new Error(
            " eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfi"
        );
    }

    const headers = {
        "apikey": SUPABASE_ANON_KEY,
        "Content-Type": "application/json"
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
                headers:{
                    ...headers,
                    ...(options.headers || {})
                }
            }
        );

    const text =
        await response.text();

    let data = null;

    try{
        data = text ? JSON.parse(text) : null;
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
            "Supabase Fehler"
        );
    }

    return data;
}


/* =========================================================
   REGISTRIERUNG
========================================================= */

$("registerButton").addEventListener(
    "click",
    register
);


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

        message(
            "registerMessage",
            "❌ Benutzername: mindestens 3 Zeichen."
        );

        return;
    }


    if(username.length > 20){

        message(
            "registerMessage",
            "❌ Benutzername: maximal 20 Zeichen."
        );

        return;
    }


    if(!email.includes("@")){

        message(
            "registerMessage",
            "❌ Bitte eine gültige E-Mail eingeben."
        );

        return;
    }


    if(password.length < 6){

        message(
            "registerMessage",
            "❌ Passwort: mindestens 6 Zeichen."
        );

        return;
    }


    message(
        "registerMessage",
        "⏳ Account wird erstellt..."
    );


    try{

        /*
        Der Benutzername wird direkt in
        den Supabase-Auth-Metadaten gespeichert.
        */

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


        if(!data.user){

            throw new Error(
                "Account konnte nicht erstellt werden."
            );
        }


        player.id =
            data.user.id;

        player.username =
            username;

        player.email =
            email;


        /*
        Wenn E-Mail-Bestätigung ausgeschaltet ist,
        können wir direkt ins Spiel.
        */

        if(data.access_token){

            authToken =
                data.access_token;

            await createPlayer();

            saveLocal();

            updateMenu();

            showScreen(
                "menuScreen"
            );

            return;
        }


        /*
        Wenn Supabase E-Mail-Bestätigung verlangt.
        */

        message(
            "registerMessage",
            "✅ Account erstellt! Bestätige zuerst deine E-Mail und melde dich danach an."
        );


    }
    catch(error){

        console.error(error);

        message(
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

        await supabaseRequest(
            "/rest/v1/Players",
            {
                method:"POST",

                headers:{
                    "Prefer":"return=minimal"
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

        /*
        Falls der Player schon existiert,
        wird das Spiel nicht gestoppt.
        */

        console.log(
            "Players-Eintrag:",
            error.message
        );
    }
}


/* =========================================================
   LOGIN
========================================================= */

$("loginButton").addEventListener(
    "click",
    login
);


async function login(){

    const email =
        $("loginEmail")
        .value
        .trim();

    const password =
        $("loginPassword")
        .value;


    if(!email){

        message(
            "loginMessage",
            "❌ Bitte E-Mail eingeben."
        );

        return;
    }


    if(!password){

        message(
            "loginMessage",
            "❌ Bitte Passwort eingeben."
        );

        return;
    }


    message(
        "loginMessage",
        "⏳ Anmeldung..."
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


        authToken =
            data.access_token;


        player.id =
            data.user.id;

        player.email =
            data.user.email;


        /*
        Username zuerst aus Auth laden.
        */

        player.username =
            data.user.user_metadata?.username ||
            data.user.email.split("@")[0];


        /*
        Danach Players-Tabelle laden.
        */

        await loadPlayer();


        loadLocal();


        updateMenu();

        showScreen(
            "menuScreen"
        );


    }
    catch(error){

        console.error(error);

        message(
            "loginMessage",
            "❌ " + error.message
        );
    }
}


/* =========================================================
   PLAYER LADEN
========================================================= */

async function loadPlayer(){

    try{

        const data =
            await supabaseRequest(
                "/rest/v1/Players" +
                "?id=eq." +
                encodeURIComponent(player.id) +
                "&select=*"
            );


        if(data.length > 0){

            player.username =
                data[0].username ||
                player.username;

            player.highscore =
                Number(
                    data[0].highscore || 0
                );

        }
        else{

            await createPlayer();
        }

    }
    catch(error){

        console.log(
            "Player konnte nicht geladen werden:",
            error.message
        );
    }
}


/* =========================================================
   HIGHSCORE SPEICHERN
========================================================= */

async function saveHighscore(){

    try{

        await supabaseRequest(
            "/rest/v1/Players?id=eq." +
            encodeURIComponent(player.id),

            {
                method:"PATCH",

                headers:{
                    "Prefer":"return=minimal"
                },

                body:JSON.stringify({

                    username:
                        player.username,

                    highscore:
                        player.highscore

                })
            }
        );

        return true;

    }
    catch(error){

        console.error(
            "Highscore speichern:",
            error.message
        );

        return false;
    }
}


/* =========================================================
   RANGLISTE
========================================================= */

$("rankingButton").addEventListener(
    "click",
    loadRanking
);


async function loadRanking(){

    showScreen(
        "rankingScreen"
    );

    $("rankingList").textContent =
        "⏳ Rangliste wird geladen...";


    try{

        const data =
            await supabaseRequest(
                "/rest/v1/Players" +
                "?select=username,highscore" +
                "&order=highscore.desc" +
                "&limit=100"
            );


        $("rankingList").innerHTML = "";


        if(!data.length){

            $("rankingList").textContent =
                "Noch keine Spieler.";

            return;
        }


        data.forEach(
            (item,index)=>{

                const row =
                    document.createElement("div");

                row.className =
                    "rankRow";

                row.innerHTML =
                    `
                    <span>
                        ${index + 1}.
                        ${escapeHTML(
                            item.username || "Spieler"
                        )}
                    </span>

                    <b>
                        ${Number(
                            item.highscore || 0
                        ).toLocaleString()}
                    </b>
                    `;

                $("rankingList")
                    .appendChild(row);
            }
        );

    }
    catch(error){

        $("rankingList").textContent =
            "❌ " + error.message;
    }
}


function escapeHTML(value){

    return String(value)
        .replaceAll("&","&amp;")
        .replaceAll("<","&lt;")
        .replaceAll(">","&gt;")
        .replaceAll('"',"&quot;")
        .replaceAll("'","&#039;");
}


/* =========================================================
   STÄDTE
========================================================= */

$("citiesButton").addEventListener(
    "click",
    showCities
);


function showCities(){

    showScreen(
        "citiesScreen"
    );

    $("citiesList").innerHTML = "";


    CITIES.forEach(
        (city,index)=>{

            const card =
                document.createElement("div");

            card.className =
                "card";


            const unlocked =
                player.unlockedCities
                    .includes(index);


            const active =
                player.city === index;


            card.innerHTML =
                `
                <div class="cityBox ${city.css}"></div>

                <h2>${city.name}</h2>

                <p>
                    🪙 ${city.minCoins}
                    – ${city.maxCoins}
                    Münzen pro Gruppe
                </p>

                <p>
                    ${
                        city.price === 0
                        ? "KOSTENLOS"
                        : city.price.toLocaleString()
                          + " Coins"
                    }
                </p>
                `;


            const button =
                document.createElement("button");


            if(unlocked){

                button.textContent =
                    active
                    ? "✓ AKTIV"
                    : "AUSWÄHLEN";


                button.onclick = ()=>{

                    player.city =
                        index;

                    saveLocal();

                    showCities();
                };

            }
            else{

                button.textContent =
                    "FREISCHALTEN";


                button.onclick = ()=>{

                    if(
                        player.coins <
                        city.price
                    ){

                        alert(
                            "❌ Nicht genug Coins."
                        );

                        return;
                    }


                    player.coins -=
                        city.price;


                    player.unlockedCities
                        .push(index);


                    player.city =
                        index;


                    saveLocal();

                    showCities();
                };
            }


            card.appendChild(
                button
            );

            $("citiesList")
                .appendChild(card);
        }
    );
}


/* =========================================================
   LOKALE DATEN
========================================================= */

function saveLocal(){

    localStorage.setItem(
        "cubeRushCoins",
        String(player.coins)
    );

    localStorage.setItem(
        "cubeRushCity",
        String(player.city)
    );

    localStorage.setItem(
        "cubeRushUnlockedCities",
        JSON.stringify(
            player.unlockedCities
        )
    );
}


function loadLocal(){

    player.coins =
        Number(
            localStorage.getItem(
                "cubeRushCoins"
            ) || 0
        );


    player.city =
        Number(
            localStorage.getItem(
                "cubeRushCity"
            ) || 0
        );


    try{

        const saved =
            JSON.parse(
                localStorage.getItem(
                    "cubeRushUnlockedCities"
                ) || "[0]"
            );


        if(Array.isArray(saved)){
            player.unlockedCities =
                saved;
        }

    }
    catch{

        player.unlockedCities =
            [0];
    }


    if(
        !player.unlockedCities.includes(0)
    ){

        player.unlockedCities.unshift(0);
    }
}


/* =========================================================
   MENÜ
========================================================= */

function updateMenu(){

    $("menuUsername").textContent =
        player.username || "Spieler";

    $("menuCoins").textContent =
        player.coins.toLocaleString();

    $("menuHighscore").textContent =
        player.highscore.toLocaleString();
}


/* =========================================================
   MENÜ BUTTONS
========================================================= */

$("openRegisterButton").onclick =
    ()=>{
        showScreen("registerScreen");
    };


$("backLoginButton").onclick =
    ()=>{
        showScreen("loginScreen");
    };


$("citiesBackButton").onclick =
    ()=>{
        showScreen("menuScreen");
    };


$("rankingBackButton").onclick =
    ()=>{
        showScreen("menuScreen");
    };


$("gameOverMenuButton").onclick =
    ()=>{
        updateMenu();
        showScreen("menuScreen");
    };


$("logoutButton").onclick =
    ()=>{
        authToken = null;

        player = {
            id:null,
            username:"",
            email:"",
            coins:0,
            highscore:0,
            city:0,
            unlockedCities:[0]
        };

        showScreen(
            "loginScreen"
        );
    };


/* =========================================================
   SPIEL
========================================================= */

const canvas =
    $("game");

const ctx =
    canvas.getContext("2d");


let gameRunning = false;
let gamePaused = false;

let score = 0;
let roundCoins = 0;

let lane = 0;

let playerX = 0;

let playerJump = 0;
let playerJumpVelocity = 0;

let speed = 280;

let objects = [];

let lastTime = 0;

let coinTimer = 0;
let carTimer = 0;


/* =========================================================
   CANVAS
========================================================= */

function resize(){

    canvas.width =
        window.innerWidth;

    canvas.height =
        window.innerHeight;
}


resize();

window.addEventListener(
    "resize",
    resize
);


/* =========================================================
   STRASSENPOSITION
========================================================= */

function getRoad(){

    const width =
        Math.min(
            window.innerWidth * .84,
            620
        );

    return {

        left:
            (window.innerWidth - width) / 2,

        width:width
    };
}


function getLaneX(number){

    const r =
        getRoad();

    const laneWidth =
        r.width / 3;


    return (
        r.left +
        laneWidth * (number + 1) +
        laneWidth / 2
    );
}


/* =========================================================
   SPIEL STARTEN
========================================================= */

$("startButton").onclick =
    startGame;


function startGame(){

    showScreen("menuScreen");

    document
        .querySelectorAll(".screen")
        .forEach(s =>
            s.classList.add("hidden")
        );


    $("hud").style.display =
        "flex";

    $("pauseButton").style.display =
        "block";

    $("jumpButton").style.display =
        "block";


    gameRunning = true;
    gamePaused = false;

    score = 0;
    roundCoins = 0;

    lane = 0;

    speed = 280;

    objects = [];

    playerJump = 0;
    playerJumpVelocity = 0;

    coinTimer = .8;
    carTimer = 1;

    playerX =
        getLaneX(lane);

    lastTime =
        performance.now();
}


/* =========================================================
   HINTERGRUND
========================================================= */

function drawBackground(){

    let top =
        "#02040a";

    if(player.city === 1)
        top = "#190018";

    if(player.city === 2)
        top = "#211000";

    if(player.city === 3)
        top = "#00172b";

    if(player.city === 4)
        top = "#17002b";


    const gradient =
        ctx.createLinearGradient(
            0,0,
            0,canvas.height
        );


    gradient.addColorStop(
        0,
        top
    );

    gradient.addColorStop(
        1,
        "#02040a"
    );


    ctx.fillStyle =
        gradient;

    ctx.fillRect(
        0,0,
        canvas.width,
        canvas.height
    );


    const r =
        getRoad();


    ctx.fillStyle =
        "#101820";

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
        r.left + r.width - 3,
        0,
        3,
        canvas.height
    );


    const laneWidth =
        r.width / 3;


    const offset =
        (score * 6) % 90;


    ctx.fillStyle =
        "#ffffff55";


    for(
        let y = -90 + offset;
        y < canvas.height;
        y += 90
    ){

        ctx.fillRect(
            r.left + laneWidth - 2,
            y,
            4,
            50
        );

        ctx.fillRect(
            r.left + laneWidth * 2 - 2,
            y,
            4,
            50
        );
    }
}


/* =========================================================
   AUTO
========================================================= */

function canSpawnCar(testLane){

    return !objects.some(
        object =>
            object.type === "car" &&
            object.lane === testLane &&
            object.y < 250
    );
}


function spawnCar(){

    const possible =
        [-1,0,1]
        .filter(canSpawnCar);


    if(!possible.length)
        return;


    const chosen =
        possible[
            Math.floor(
                Math.random() *
                possible.length
            )
        ];


    objects.push({

        type:"car",

        lane:chosen,

        y:-130,

        color:[
            "#ff1744",
            "#008cff",
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


/* =========================================================
   MÜNZEN
=========================================================

   HIER WIRD DIE WELT-REGEL EINGEHALTEN.

   Neon City = 3–6.
========================================================= */

function spawnCoinGroup(){

    const city =
        CITIES[player.city];


    const amount =
        city.minCoins +
        Math.floor(
            Math.random() *
            (
                city.maxCoins -
                city.minCoins +
                1
            )
        );


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
                -60 -
                i * 65
        });
    }
}


/* =========================================================
   SPIELER
========================================================= */

function drawPlayer(){

    const baseY =
        canvas.height * .76;

    const y =
        baseY - playerJump;


    ctx.save();

    ctx.translate(
        playerX,
        y
    );


    ctx.shadowColor =
        "#00eaff";

    ctx.shadowBlur =
        25;


    ctx.fillStyle =
        "#00eaff";


    ctx.fillRect(
        -27,
        -27,
        54,
        54
    );


    ctx.shadowBlur = 0;


    ctx.fillStyle =
        "#ffffff";


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
   OBJEKTE ZEICHNEN
========================================================= */

function drawObjects(){

    objects.forEach(
        object => {

            const x =
                getLaneX(
                    object.lane
                );


            if(
                object.type === "coin"
            ){

                ctx.save();

                ctx.fillStyle =
                    "#ffd900";

                ctx.shadowColor =
                    "#ffd900";

                ctx.shadowBlur =
                    18;


                ctx.beginPath();

                ctx.arc(
                    x,
                    object.y,
                    14,
                    0,
                    Math.PI * 2
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
                    15;


                ctx.fillStyle =
                    object.color;


                ctx.beginPath();

                ctx.roundRect(
                    -31,
                    -52,
                    62,
                    104,
                    10
                );

                ctx.fill();


                ctx.shadowBlur = 0;


                ctx.fillStyle =
                    "#122633";


                ctx.fillRect(
                    -20,
                    -30,
                    40,
                    27
                );


                ctx.fillStyle =
                    "#fff";

                ctx.fillRect(
                    -22,
                    30,
                    12,
                    7
                );

                ctx.fillRect(
                    10,
                    30,
                    12,
                    7
                );


                ctx.restore();
            }
        }
    );
}


/* =========================================================
   SPRINGEN
========================================================= */

function jump(){

    if(!gameRunning || gamePaused)
        return;


    if(playerJump <= 1){

        playerJumpVelocity =
            780;
    }
}


$("jumpButton").onclick =
    jump;


/* =========================================================
   UPDATE
========================================================= */

function update(delta){

    if(!gameRunning || gamePaused)
        return;


    const seconds =
        delta / 1000;


    score +=
        seconds * 10;


    speed =
        Math.min(
            700,
            speed + seconds * 4
        );


    playerX +=
        (
            getLaneX(lane) -
            playerX
        ) *
        Math.min(
            1,
            seconds * 12
        );


    /* Sprungphysik */

    playerJumpVelocity -=
        1900 * seconds;

    playerJump +=
        playerJumpVelocity * seconds;


    if(playerJump <= 0){

        playerJump = 0;
        playerJumpVelocity = 0;
    }


    /* Münzen */

    coinTimer -=
        seconds;


    if(coinTimer <= 0){

        spawnCoinGroup();

        /*
        Nicht zu viele Gruppen.
        */

        coinTimer =
            3 +
            Math.random() * 2;
    }


    /* Autos */

    carTimer -=
        seconds;


    if(carTimer <= 0){

        spawnCar();

        carTimer =
            1.35 +
            Math.random() * .9;
    }


    const playerY =
        canvas.height * .76 -
        playerJump;


    for(
        let i = objects.length - 1;
        i >= 0;
        i--
    ){

        const object =
            objects[i];


        object.y +=
            speed * seconds;


        if(
            object.y >
            canvas.height + 150
        ){

            objects.splice(i,1);

            continue;
        }


        /* Münze einsammeln */

        if(
            object.type === "coin" &&
            object.lane === lane &&
            Math.abs(
                object.y - playerY
            ) < 55
        ){

            roundCoins++;

            objects.splice(i,1);

            continue;
        }


        /* Auto */

        if(
            object.type === "car" &&
            object.lane === lane &&
            Math.abs(
                object.y - playerY
            ) < 70
        ){

            /*
            Beim Springen kann der Spieler
            über ein Auto springen.
            */

            if(playerJump < 75){

                endGame();

                return;
            }
        }
    }


    $("hudCoins").textContent =
        (
            player.coins +
            roundCoins
        ).toLocaleString();


    $("hudScore").textContent =
        Math.floor(score)
        .toLocaleString();


    $("hudCity").textContent =
        CITIES[player.city].name;
}


/* =========================================================
   SPIEL LOOP
========================================================= */

function loop(time){

    const delta =
        Math.min(
            40,
            time - lastTime
        );


    lastTime =
        time;


    drawBackground();

    drawObjects();

    drawPlayer();

    update(delta);


    requestAnimationFrame(loop);
}


requestAnimationFrame(loop);


/* =========================================================
   TASTATUR
========================================================= */

document.addEventListener(
    "keydown",
    event => {

        if(!gameRunning)
            return;


        if(
            event.key === "ArrowLeft" ||
            event.key.toLowerCase() === "a"
        ){

            lane =
                Math.max(
                    -1,
                    lane - 1
                );
        }


        if(
            event.key === "ArrowRight" ||
            event.key.toLowerCase() === "d"
        ){

            lane =
                Math.min(
                    1,
                    lane + 1
                );
        }


        if(
            event.key === "ArrowUp" ||
            event.key === " "
        ){

            jump();
        }
    }
);


/* =========================================================
   HANDY SWIPE
========================================================= */

let touchStartX = 0;


canvas.addEventListener(
    "touchstart",
    event => {

        if(!gameRunning)
            return;


        touchStartX =
            event.changedTouches[0]
            .clientX;

    },
    {passive:true}
);


canvas.addEventListener(
    "touchend",
    event => {

        if(!gameRunning)
            return;


        const endX =
            event.changedTouches[0]
            .clientX;


        const difference =
            endX - touchStartX;


        if(
            Math.abs(difference) < 30
        )
            return;


        if(difference > 0){

            /*
            RECHTS:
            -1 -> 0 -> 1
            */

            lane =
                Math.min(
                    1,
                    lane + 1
                );

        }
        else{

            /*
            LINKS:
            1 -> 0 -> -1
            */

            lane =
                Math.max(
                    -1,
                    lane - 1
                );
        }
    },
    {passive:true}
);


/* =========================================================
   PAUSE
========================================================= */

$("pauseButton").onclick =
    pauseGame;


function pauseGame(){

    if(!gameRunning)
        return;


    gamePaused = true;

    $("pauseButton").style.display =
        "none";

    $("jumpButton").style.display =
        "none";


    showScreen(
        "pauseScreen"
    );
}


$("resumeButton").onclick =
    ()=>{
        gamePaused = false;

        $("pauseButton").style.display =
            "block";

        $("jumpButton").style.display =
            "block";

        showGameOnly();

        lastTime =
            performance.now();
    };


function showGameOnly(){

    document
        .querySelectorAll(".screen")
        .forEach(screen =>
            screen.classList.add("hidden")
        );
}


$("quitButton").onclick =
    ()=>{
        gameRunning = false;
        gamePaused = false;

        hideHUD();

        updateMenu();

        showScreen(
            "menuScreen"
        );
    };


function hideHUD(){

    $("hud").style.display =
        "none";

    $("pauseButton").style.display =
        "none";

    $("jumpButton").style.display =
        "none";
}


/* =========================================================
   GAME OVER
========================================================= */

async function endGame(){

    if(!gameRunning)
        return;


    gameRunning = false;


    hideHUD();


    player.coins +=
        roundCoins;


    const finalScore =
        Math.floor(score);


    if(
        finalScore >
        player.highscore
    ){

        player.highscore =
            finalScore;


        /*
        Highscore wird in Supabase gespeichert.
        */

        await saveHighscore();
    }


    saveLocal();


    $("finalScore").textContent =
        finalScore.toLocaleString();


    $("finalCoins").textContent =
        roundCoins.toLocaleString();


    $("finalHighscore").textContent =
        player.highscore.toLocaleString();


    showScreen(
        "gameOverScreen"
    );
}


/* =========================================================
   NEUSTART
========================================================= */

$("restartButton").onclick =
    startGame;


/* =========================================================
   ABMELDEN
========================================================= */

$("logoutButton").onclick =
    ()=>{
        authToken = null;

        gameRunning = false;

        hideHUD();

        showScreen(
            "loginScreen"
        );
    };


/* =========================================================
   START
========================================================= */

loadLocal();

showScreen(
    "loginScreen"
);

</script>

</body>
</html>
