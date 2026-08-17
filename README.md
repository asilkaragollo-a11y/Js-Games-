<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
<title>Cube Rush</title>

<style>
*{box-sizing:border-box}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#02040a;
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
    font-size:45px;
    font-weight:900;
    color:#00eaff;
    text-shadow:0 0 30px #00eaff;
    margin-bottom:5px;
    text-align:center;
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

.coins{
    color:#ffd900;
    font-weight:bold;
    font-size:20px;
    margin:10px;
}

.card{
    display:inline-block;
    width:170px;
    min-height:170px;
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

.msg{
    min-height:20px;
    color:#a9c5d0;
    text-align:center;
}

.cityPreview{
    width:250px;
    height:100px;
    border-radius:15px;
    margin:auto;
    border:2px solid #00eaff;
    box-shadow:0 0 20px #00eaff66;
}

.cityNeon{
    background:
    linear-gradient(90deg,#07182d,#00eaff22,#07182d);
}

.cityTokyo{
    background:
    linear-gradient(90deg,#190020,#ff005522,#21003b);
}

.cityDubai{
    background:
    linear-gradient(90deg,#201000,#ff9d0022,#392000);
}

.cityNewYork{
    background:
    linear-gradient(90deg,#001529,#0077ff33,#001529);
}

.cityGalaxy{
    background:
    linear-gradient(90deg,#100020,#8b00ff55,#001e38);
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
    <div class="hud">🪙 <span id="coins">0</span></div>
    <div class="hud">🏆 <span id="score">0</span></div>
    <div class="hud">🌆 <span id="cityName">Neon City</span></div>
</div>

<button id="pause">⏸</button>

<!-- LOGIN -->
<div id="login" class="screen">

    <div class="logo">CUBE RUSH</div>
    <p>ONLINE EDITION</p>

    <input id="username"
           placeholder="Benutzername"
           maxlength="18">

    <input id="email"
           type="email"
           placeholder="E-Mail">

    <input id="password"
           type="password"
           placeholder="Passwort">

    <button onclick="register()">REGISTRIEREN</button>
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
        🪙 <span id="menuCoins">0</span>
    </div>

    <button onclick="startGame()">▶ SPIEL STARTEN</button>
    <button onclick="charactersMenu()">🧊 CHARAKTERE</button>
    <button onclick="citiesMenu()">🌆 STÄDTE</button>
    <button onclick="rankingMenu()">🏆 RANGLISTE</button>
    <button onclick="codeMenu()">🔐 CODE</button>
    <button onclick="logout()">ABMELDEN</button>

</div>

<!-- CHARACTERS -->
<div id="characters" class="screen hidden">

    <h1>🧊 CHARAKTERE</h1>

    <div class="coins">
        🪙 <span id="charCoins">0</span>
    </div>

    <div id="characterList"></div>

    <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- CITIES -->
<div id="cities" class="screen hidden">

    <h1>🌆 STÄDTE</h1>

    <div id="cityList"></div>

    <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- RANKING -->
<div id="ranking" class="screen hidden">

    <h1>🏆 RANGLISTE</h1>

    <div id="rankingList">
        Lade Rangliste...
    </div>

    <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- CODE -->
<div id="codes" class="screen hidden">

    <h1>🔐 CODE</h1>

    <input id="secretCode"
           inputmode="numeric"
           maxlength="3"
           placeholder="Code">

    <button onclick="redeemCode()">EINLÖSEN</button>

    <div id="codeMsg" class="msg"></div>

    <button onclick="showMenu()">ZURÜCK</button>

</div>

<!-- PAUSE -->
<div id="pauseScreen" class="screen hidden">

    <h1>⏸ PAUSE</h1>

    <button onclick="resumeGame()">
        WEITERSPIELEN
    </button>

    <button onclick="quitGame()">
        MENÜ
    </button>

</div>

<!-- GAME OVER -->
<div id="gameOver" class="screen hidden">

    <h1>💥 CRASH!</h1>

    <div id="gameOverText"></div>

    <button onclick="restartGame()">
        🔄 NOCHMAL
    </button>

    <button onclick="showMenu()">
        MENÜ
    </button>

</div>


<script>

/* =====================================================
   SUPABASE
   ===================================================== */

const SUPABASE_URL =
"https://lfsifdmaftztykpckdsh.supabase.co";

/*
   HIER deinen ANON/PUBLIC KEY einsetzen.

   NICHT den service_role Key benutzen!
*/
const SUPABASE_ANON_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio


/* =====================================================
   SPIELER
   ===================================================== */

let playerData = {
    username:"",
    email:"",
    coins:0,
    highscore:0,
    characters:[0],
    cities:[0],
    character:0,
    city:0
};


/* =====================================================
   STÄDTE
   ===================================================== */

/*
   Jede Münzgruppe hat eine begrenzte Anzahl.

   Neon City       = 3-6
   Tokyo Night     = 7-12
   Cyber Dubai     = 12-18
   Future New York = 18-25
   Galaxy City     = 25-35

   Je teurer die Stadt,
   desto mehr Münzen kommen.
*/

const cityData = [

    {
        name:"Neon City",
        price:0,
        min:3,
        max:6,
        className:"cityNeon"
    },

    {
        name:"Tokyo Night",
        price:5000,
        min:7,
        max:12,
        className:"cityTokyo"
    },

    {
        name:"Cyber Dubai",
        price:12000,
        min:12,
        max:18,
        className:"cityDubai"
    },

    {
        name:"Future New York",
        price:25000,
        min:18,
        max:25,
        className:"cityNewYork"
    },

    {
        name:"Galaxy City",
        price:50000,
        min:25,
        max:35,
        className:"cityGalaxy"
    }
];


/* =====================================================
   CHARAKTERE
   ===================================================== */

const charData = [

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


/* =====================================================
   LOCAL SPEICHER
   ===================================================== */

function save(){

    localStorage.setItem(
        "cubeRushPlayer",
        JSON.stringify(playerData)
    );

}


function load(){

    const data =
        localStorage.getItem(
            "cubeRushPlayer"
        );

    if(!data) return false;

    try{

        playerData =
            JSON.parse(data);

        return true;

    }catch{

        return false;

    }

}


/* =====================================================
   LOGIN
   ===================================================== */

function register(){

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

    if(!email || password.length < 6){

        loginMsg(
            "E-Mail und Passwort eingeben."
        );

        return;
    }

    playerData.username = name;
    playerData.email = email;

    save();

    showMenu();
}


function loginUser(){

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

        loginMsg(
            "Bitte deinen Benutzernamen eingeben."
        );

        return;
    }

    if(!email || !password){

        loginMsg(
            "E-Mail und Passwort eingeben."
        );

        return;
    }

    if(load()){

        showMenu();

    }else{

        playerData.username = name;
        playerData.email = email;

        save();

        showMenu();
    }

}


function loginMsg(text){

    document
    .getElementById("loginMsg")
    .textContent = text;

}


function logout(){

    save();

    location.reload();

}


/* =====================================================
   MENÜ
   ===================================================== */

function hideAll(){

    document
    .querySelectorAll(".screen")
    .forEach(
        x => x.classList.add("hidden")
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
                document.createElement("div");

            card.className = "card";

            const unlocked =
                playerData.characters
                .includes(index);

            card.innerHTML = `
                <h3>${char[0]}</h3>
                <p>
                    ${
                        unlocked
                        ? "✓ FREIGESCHALTET"
                        : "🪙 " +
                          char[1].toLocaleString()
                    }
                </p>
            `;

            const button =
                document.createElement("button");

            if(unlocked){

                button.textContent =
                    playerData.character === index
                    ? "✓ AKTIV"
                    : "AUSWÄHLEN";

                button.onclick = ()=>{

                    playerData.character = index;

                    save();

                    charactersMenu();

                };

            }else{

                button.textContent = "KAUFEN";

                button.onclick = ()=>{

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

            card.appendChild(button);

            list.appendChild(card);

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
                document.createElement("div");

            card.className = "card";

            const unlocked =
                playerData.cities
                .includes(index);

            card.innerHTML = `

                <div class="cityPreview ${city.className}"></div>

                <h3>${city.name}</h3>

                <p>
                    🪙 ${city.min}-${city.max}
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
                    playerData.city === index
                    ? "✓ AKTIV"
                    : "AUSWÄHLEN";

                button.onclick = ()=>{

                    playerData.city =
                        index;

                    save();

                    citiesMenu();

                };

            }else{

                button.textContent =
                    "FREISCHALTEN";

                button.onclick = ()=>{

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

            card.appendChild(button);

            list.appendChild(card);

        }
    );

    document
    .getElementById("cities")
    .classList.remove("hidden");

}


/* =====================================================
   ADMIN / CODES
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
    .classList.remove("hidden");

}


function redeemCode(){

    const code =
        document
        .getElementById("secretCode")
        .value;

    const msg =
        document
        .getElementById("codeMsg");


    /*
       CODE 110

       1.000.000 Highscore
       1.000.000 Coins
       ALLE Charaktere
       ALLE Städte
    */

    if(code === "110"){

        playerData.coins +=
            1000000;

        playerData.highscore =
            1000000;

        playerData.characters =
            charData.map(
                (_,i)=>i
            );

        playerData.cities =
            cityData.map(
                (_,i)=>i
            );

        msg.textContent =
            "✅ Code 110 aktiviert! 1.000.000 Highscore + alle Städte + alle Charaktere.";

    }

    /*
       CODE 112

       500.000 Highscore
       500.000 Coins
       5 Charaktere
       2 Städte
    */

    else if(code === "112"){

        playerData.coins +=
            500000;

        playerData.highscore =
            500000;

        playerData.characters =
            [0,1,2,3,4];

        playerData.cities =
            [0,1];

        msg.textContent =
            "✅ Code 112 aktiviert! 500.000 Highscore + 500.000 Coins + 2 Städte.";

    }

    else{

        msg.textContent =
            "❌ Falscher Code.";

    }

    document
    .getElementById("secretCode")
    .value = "";

    save();

}


/* =====================================================
   SUPABASE RANGLISTE
   ===================================================== */

async function saveOnlineScore(){

    if(
        !SUPABASE_ANON_KEY ||
        SUPABASE_ANON_KEY ===
        "DEIN_ANON_PUBLIC_KEY_HIER"
    ){

        return;
    }

    try{

        await fetch(
            SUPABASE_URL +
            "/rest/v1/Players",
            {
                method:"POST",

                headers:{
                    "apikey":
                        SUPABASE_ANON_KEY,

                    "Authorization":
                        "Bearer " +
                        SUPABASE_ANON_KEY,

                    "Content-Type":
                        "application/json",

                    "Prefer":
                        "resolution=merge-duplicates"
                },

                body:JSON.stringify({

                    username:
                        playerData.username,

                    highscore:
                        playerData.highscore

                })
            }
        );

    }catch(error){

        console.log(
            "Online-Speicherung nicht verfügbar."
        );

    }

}


async function rankingMenu(){

    hideAll();

    const list =
        document.getElementById(
            "rankingList"
        );

    list.innerHTML =
        "⏳ Rangliste wird geladen...";


    if(
        !SUPABASE_ANON_KEY ||
        SUPABASE_ANON_KEY ===
        "DEIN_ANON_PUBLIC_KEY_HIER"
    ){

        list.innerHTML =
            "⚠️ Supabase-Key wurde noch nicht eingetragen.";

        document
        .getElementById("ranking")
        .classList.remove("hidden");

        return;
    }


    try{

        const response =
            await fetch(
                SUPABASE_URL +
                "/rest/v1/Players" +
                "?select=username,highscore" +
                "&order=highscore.desc" +
                "&limit=50",
                {
                    headers:{
                        "apikey":
                            SUPABASE_ANON_KEY,

                        "Authorization":
                            "Bearer " +
                            SUPABASE_ANON_KEY
                    }
                }
            );


        if(!response.ok){

            throw new Error(
                "Supabase Fehler"
            );

        }


        const players =
            await response.json();


        list.innerHTML = "";


        if(players.length === 0){

            list.innerHTML =
                "Noch keine Spieler.";

        }


        players.forEach(
            (p,i)=>{

                const row =
                    document
                    .createElement("div");

                row.className = "rank";

                row.innerHTML = `

                    <span>
                        ${i+1}. ${escapeHTML(p.username)}
                    </span>

                    <b>
                        ${Number(
                            p.highscore || 0
                        ).toLocaleString()}
                    </b>

                `;

                list.appendChild(row);

            }
        );


    }catch(error){

        console.error(error);

        list.innerHTML =
            "❌ Rangliste konnte nicht geladen werden.";

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


/* =====================================================
   SPIEL
   ===================================================== */

const canvas =
    document.getElementById("game");

const ctx =
    canvas.getContext("2d");


function resize(){

    canvas.width =
        window.innerWidth;

    canvas.height =
        window.innerHeight;

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
   STRASSEN
   ===================================================== */

function road(){

    const width =
        Math.min(
            innerWidth * .84,
            600
        );

    return{

        left:
            (innerWidth-width)/2,

        width:width

    };

}


/*
   WICHTIG:

   -1 = LINKS
    0 = MITTE
    1 = RECHTS

   Die Berechnung ist jetzt sauber
   auf genau drei Spuren verteilt.
*/

function laneX(n){

    const r =
        road();

    const laneWidth =
        r.width / 3;

    return (
        r.left +
        laneWidth * n +
        laneWidth / 2
    );

}


/* =====================================================
   SPIEL START
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
    .style.display = "flex";

    document
    .getElementById("pause")
    .style.display = "block";

    last =
        performance.now();

}


/* =====================================================
   STADT HINTERGRUND
   ===================================================== */

function drawCityBackground(){

    const city =
        playerData.city;

    let top =
        "#02040a";

    let bottom =
        "#050814";

    if(city === 1){

        top = "#180018";
        bottom = "#050008";

    }

    if(city === 2){

        top = "#211000";
        bottom = "#080400";

    }

    if(city === 3){

        top = "#00182e";
        bottom = "#02050a";

    }

    if(city === 4){

        top = "#16002b";
        bottom = "#02000a";

    }


    const gradient =
        ctx.createLinearGradient(
            0,
            0,
            0,
            innerHeight
        );

    gradient.addColorStop(
        0,
        top
    );

    gradient.addColorStop(
        1,
        bottom
    );

    ctx.fillStyle =
        gradient;

    ctx.fillRect(
        0,
        0,
        innerWidth,
        innerHeight
    );


    /* Gebäude */

    const r =
        road();

    const buildingWidth = 55;

    for(
        let x = 0;
        x < innerWidth;
        x += buildingWidth
    ){

        if(
            x >
            r.left - 70 &&
            x <
            r.left+r.width+70
        ) continue;


        const h =
            80 +
            ((x*17)%180);

        ctx.fillStyle =
            city === 2
            ? "#302010"
            : city === 4
            ? "#180e2e"
            : "#091522";

        ctx.fillRect(
            x,
            innerHeight-h,
            buildingWidth-8,
            h
        );

    }

}


/* =====================================================
   WELT
   ===================================================== */

function drawWorld(){

    drawCityBackground();

    const r =
        road();


    /* Straße */

    ctx.fillStyle =
        "#111820";

    ctx.fillRect(
        r.left,
        0,
        r.width,
        innerHeight
    );


    /* Straßenrand */

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


    /* Spuren */

    const lw =
        r.width / 3;

    const offset =
        (score * 4) % 100;


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

    ctx.save();

    ctx.translate(
        playerX,
        y
    );


    ctx.fillStyle =
        "#00eaff";

    ctx.shadowColor =
        "#00eaff";

    ctx.shadowBlur =
        25;


    ctx.fillRect(
        -28,
        -28,
        56,
        56
    );


    ctx.shadowBlur = 0;

    ctx.fillStyle =
        "#fff";


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


/*
   Autos bekommen jetzt einen Mindestabstand.

   Dadurch können zwei Autos nicht
   direkt ineinander spawnen.
*/

const MIN_CAR_DISTANCE = 190;


function canSpawnCar(newLane){

    for(const o of objects){

        if(
            o.type !== "car"
        ) continue;


        if(
            o.lane === newLane &&
            Math.abs(o.y + 150)
            < MIN_CAR_DISTANCE
        ){

            return false;

        }

    }

    return true;

}


function spawnCar(){

    const possible =
        [-1,0,1].filter(
            l => canSpawnCar(l)
        );


    if(
        possible.length === 0
    ){

        return;

    }


    const selected =
        possible[
            Math.floor(
                Math.random() *
                possible.length
            )
        ];


    objects.push({

        type:"car",

        lane:selected,

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
        laneX(car.lane);

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
   MÜNZEN
   ===================================================== */


/*
   Es wird immer nur EINE Münzgruppe
   erzeugt.

   Die Anzahl kommt direkt aus der Stadt.
*/

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
                city.max -
                city.min +
                1
            )
        );


    const randomLane =
        [-1,0,1][
            Math.floor(
                Math.random()*3
            )
        ];


    /*
       Eine Gruppe mit Abstand.
       Dadurch sind die Münzen sichtbar
       und nicht übereinander.
    */

    for(
        let i=0;
        i<amount;
        i++
    ){

        objects.push({

            type:"coin",

            lane:randomLane,

            y:
                -50 -
                i*55

        });

    }

}


function drawCoin(o,time){

    let x =
        laneX(o.lane);

    let y =
        o.y;


    if(magnet > 0){

        x +=
            (playerX-x)*.18;

        y +=
            (
                innerHeight*.72-y
            )*.18;

    }


    const scale =
        .5 +
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
            [-1,0,1][
                Math.floor(
                    Math.random()*3
                )
            ],

        y:-80

    });

}


function drawPower(o){

    const x =
        laneX(o.lane);

    ctx.save();

    ctx.translate(
        x,
        o.y
    );

    ctx.font =
        "34px Arial";

    ctx.textAlign =
        "center";

    ctx.textBaseline =
        "middle";

    ctx.shadowBlur =
        20;

    ctx.shadowColor =
        o.type === "magnet"
        ? "cyan"
        : "yellow";


    ctx.fillText(
        o.type === "magnet"
        ? "🧲"
        : "✕2",
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
    ) return;


    const seconds =
        dt/1000;


    score +=
        seconds*10;


    speed =
        Math.min(
            700,
            speed +
            seconds*4
        );


    /*
       Spieler folgt exakt
       der ausgewählten Spur.
    */

    playerX +=
        (
            laneX(lane) -
            playerX
        ) *
        Math.min(
            1,
            seconds*12
        );


    if(magnet>0)
        magnet-=seconds;

    if(doubleCoins>0)
        doubleCoins-=seconds;


    powerTimer-=seconds;


    if(powerTimer<=0){

        spawnPower();

        powerTimer =
            15 +
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


        /* POWERUP */

        if(

            (
                o.type === "magnet" ||
                o.type === "double"
            )

            &&

            Math.abs(
                o.y -
                innerHeight*.72
            ) < 55

            &&

            Math.abs(
                laneX(o.lane) -
                playerX
            ) < 45

        ){

            if(
                o.type === "magnet"
            )
                magnet=30;

            if(
                o.type === "double"
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
            o.type === "coin" &&
            magnet > 0
        ){

            o.lane =
                lane;

        }


        /* COIN */

        if(

            o.type === "coin"

            &&

            Math.abs(
                o.y -
                innerHeight*.72
            ) < 50

            &&

            Math.abs(
                laneX(o.lane) -
                playerX
            ) < 45

        ){

            runCoins +=
                doubleCoins > 0
                ? 2
                : 1;


            objects.splice(
                i,
                1
            );

            continue;

        }


        /* AUTO */

        if(

            o.type === "car"

            &&

            Math.abs(
                o.y -
                innerHeight*.72
            ) < 65

            &&

            Math.abs(
                laneX(o.lane) -
                playerX
            ) < 45

        ){

            endGame();

            return;

        }

    }


    /*
       Autos erscheinen nicht zu dicht.
    */

    if(
        Math.random() <
        dt/1000/1.35
    ){

        spawnCar();

    }


    /*
       Münzgruppen kommen regelmäßig,
       aber nicht permanent übereinander.
    */

    if(
        Math.random() <
        dt/1000/2.8
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
            playerData.coins +
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
   GAME LOOP
   ===================================================== */

function loop(time){

    const dt =
        Math.min(
            40,
            time-last
        );

    last = time;


    drawWorld();


    objects.forEach(
        o=>{

            if(
                o.type === "car"
            )
                drawCar(o);

            else if(
                o.type === "coin"
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


requestAnimationFrame(loop);


/* =====================================================
   STEUERUNG
   ===================================================== */

document.addEventListener(
    "keydown",
    e=>{

        if(
            !running ||
            paused
        ) return;


        if(
            e.key === "ArrowLeft"
        ){

            lane =
                Math.max(
                    -1,
                    lane-1
                );

        }


        if(
            e.key === "ArrowRight"
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
   TOUCH / SWIPE
   ===================================================== */

let touchStartX = 0;


canvas.addEventListener(
    "touchstart",
    e=>{

        if(
            !running ||
            paused
        ) return;


        touchStartX =
            e.changedTouches[0]
            .clientX;

    },
    {passive:true}
);


canvas.addEventListener(
    "touchend",
    e=>{

        if(
            !running ||
            paused
        ) return;


        const endX =
            e.changedTouches[0]
            .clientX;


        const difference =
            endX -
            touchStartX;


        if(
            Math.abs(
                difference
            ) < 30
        ) return;


        if(
            difference > 0
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
    {passive:true}
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


    const finalScore =
        Math.floor(score);


    if(
        finalScore >
        playerData.highscore
    ){

        playerData.highscore =
            finalScore;

    }


    save();


    /*
       Score online speichern
    */

    await saveOnlineScore();


    document
    .getElementById("hud")
    .style.display =
        "none";


    document
    .getElementById("pause")
    .style.display =
        "none";


    document
    .getElementById("gameOverText")
    .innerHTML = `

        <p>
            🏆 Score:
            <b>
                ${finalScore.toLocaleString()}
            </b>
        </p>

        <p>
            🪙 Coins:
            <b>
                ${runCoins}
            </b>
        </p>

        <p>
            ⭐ Highscore:
            <b>
                ${playerData.highscore.toLocaleString()}
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
   START
   ===================================================== */

if(load()){

    showMenu();

}

</script>

</body>
</html>
