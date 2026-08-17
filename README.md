<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cube Rush</title>

<style>
* {
    box-sizing: border-box;
}

html, body {
    margin: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    font-family: Arial, sans-serif;
    background: #02040a;
    color: white;
}

#game {
    position: fixed;
    inset: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    pointer-events: none;
}

.screen {
    position: fixed;
    inset: 0;
    z-index: 100;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 20px;
    background:
        radial-gradient(circle at center, #06304a 0%, #02040a 70%);
}

.hidden {
    display: none !important;
}

.logo {
    font-size: 45px;
    font-weight: 900;
    color: #00eaff;
    text-shadow: 0 0 25px #00eaff;
    margin-bottom: 10px;
    text-align: center;
}

.subtitle {
    color: #b9eaf2;
    text-align: center;
    margin-bottom: 20px;
}

input {
    width: 330px;
    max-width: 95%;
    height: 54px;
    margin: 6px;
    padding: 0 16px;

    border: 2px solid #00eaff;
    border-radius: 13px;

    background: #071522;
    color: white;

    font-size: 16px;
    outline: none;

    position: relative;
    z-index: 200;
    pointer-events: auto;
}

input::placeholder {
    color: #8caab2;
}

input:focus {
    box-shadow: 0 0 20px #00eaff66;
}

button {
    width: 330px;
    max-width: 95%;
    min-height: 52px;

    margin: 6px;
    padding: 12px 20px;

    border: none;
    border-radius: 13px;

    background: #00eaff;
    color: #001018;

    font-size: 16px;
    font-weight: bold;

    cursor: pointer;

    position: relative;
    z-index: 200;
    pointer-events: auto;

    touch-action: manipulation;
}

button:active {
    transform: scale(0.97);
}

.secondary {
    background: #122431;
    color: white;
    border: 1px solid #00eaff66;
}

.message {
    width: 330px;
    max-width: 95%;
    min-height: 30px;

    margin-top: 8px;

    text-align: center;
    color: #9eefff;
    line-height: 1.4;
}

.info {
    width: 330px;
    max-width: 95%;
    padding: 12px;
    margin: 10px 0;

    border-radius: 12px;
    background: #071522;
    border: 1px solid #00eaff44;

    text-align: center;
}

#hud {
    position: fixed;
    top: 10px;
    left: 10px;
    right: 10px;

    z-index: 50;

    display: none;
    justify-content: space-between;

    pointer-events: none;
}

.hudBox {
    background: #071522e8;
    border: 1px solid #00eaff66;
    border-radius: 10px;
    padding: 9px 12px;
    font-weight: bold;
}

#pauseButton {
    position: fixed;
    top: 60px;
    right: 10px;
    z-index: 60;

    display: none;

    width: auto;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
    <div class="hudBox">🪙 <span id="coins">0</span></div>
    <div class="hudBox">🏆 <span id="score">0</span></div>
</div>

<button id="pauseButton" onclick="pauseGame()">⏸</button>


<!-- =========================
     LOGIN
========================= -->

<div id="loginScreen" class="screen">

    <div class="logo">CUBE RUSH</div>

    <div class="subtitle">
        Melde dich an und speichere deinen Highscore.
    </div>

    <input
        id="loginEmail"
        type="email"
        placeholder="📧 E-Mail"
    >

    <input
        id="loginPassword"
        type="password"
        placeholder="🔒 Passwort"
    >

    <!-- DIESER BUTTON IST DIREKT KLICKBAR -->
    <button id="loginButton" type="button">
        ANMELDEN
    </button>

    <button
        id="registerOpenButton"
        class="secondary"
        type="button"
    >
        ACCOUNT ERSTELLEN
    </button>

    <div id="loginMessage" class="message"></div>

</div>


<!-- =========================
     REGISTRIERUNG
========================= -->

<div id="registerScreen" class="screen hidden">

    <div class="logo">CUBE RUSH</div>

    <h2>Account erstellen</h2>

    <div class="info">
        👤 Wähle deinen Benutzernamen
    </div>

    <input
        id="registerUsername"
        type="text"
        maxlength="20"
        placeholder="👤 Benutzername"
    >

    <input
        id="registerEmail"
        type="email"
        placeholder="📧 E-Mail"
    >

    <input
        id="registerPassword"
        type="password"
        placeholder="🔒 Passwort"
    >

    <button id="registerButton" type="button">
        REGISTRIEREN
    </button>

    <button
        id="backLoginButton"
        class="secondary"
        type="button"
    >
        ZURÜCK
    </button>

    <div id="registerMessage" class="message"></div>

</div>


<!-- =========================
     MENÜ
========================= -->

<div id="menuScreen" class="screen hidden">

    <div class="logo">CUBE RUSH</div>

    <h2>
        Willkommen,
        <span id="playerName"></span> 👋
    </h2>

    <div class="info">
        🪙 Coins:
        <b id="menuCoins">0</b>
        <br><br>
        🏆 Highscore:
        <b id="menuHighscore">0</b>
    </div>

    <button id="startButton" type="button">
        ▶ SPIEL STARTEN
    </button>

    <button id="rankingButton" type="button">
        🏆 RANGLISTE
    </button>

    <button id="logoutButton" class="secondary" type="button">
        ABMELDEN
    </button>

</div>


<!-- =========================
     RANGLISTE
========================= -->

<div id="rankingScreen" class="screen hidden">

    <div class="logo">RANGLISTE</div>

    <div id="rankingList" class="info">
        Noch keine Rangliste geladen.
    </div>

    <button id="rankingBackButton" class="secondary" type="button">
        ZURÜCK
    </button>

</div>


<!-- =========================
     GAME OVER
========================= -->

<div id="gameOverScreen" class="screen hidden">

    <div class="logo">GAME OVER</div>

    <h2>💥 Du bist gecrasht!</h2>

    <div class="info">
        🏆 Score:
        <b id="finalScore">0</b>
        <br><br>
        🪙 Münzen:
        <b id="finalCoins">0</b>
        <br><br>
        👑 Highscore:
        <b id="finalHighscore">0</b>
    </div>

    <button id="restartButton" type="button">
        🔄 NOCHMAL
    </button>

    <button id="gameOverMenuButton" class="secondary" type="button">
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
   WICHTIG:

   Hier deinen SUPABASE ANON/PUBLIC KEY einsetzen.

   NICHT den service_role Key verwenden.
*/

const SUPABASE_ANON_KEY =
"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imxmc2lmZG1hZnR6dHlrcGNrZHNoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODY5MDczMTksImV4cCI6MjEwMjQ4MzMxOX0.dfMS0zgHO_IYU7_YmR2Si6DVkD8MWiJJUt6iSJXZfio


let authToken = null;


/* =====================================================
   SPIELER
===================================================== */

let player = {
    id: null,
    username: "",
    email: "",
    coins: 0,
    highscore: 0
};


/* =====================================================
   HILFSFUNKTIONEN
===================================================== */

function $(id) {
    return document.getElementById(id);
}


function showScreen(id) {

    document
        .querySelectorAll(".screen")
        .forEach(screen => {
            screen.classList.add("hidden");
        });

    $(id).classList.remove("hidden");
}


/* =====================================================
   SUPABASE
===================================================== */

async function supabaseRequest(path, options = {}) {

    const headers = {
        "apikey": SUPABASE_ANON_KEY,
        "Content-Type": "application/json"
    };

    if (authToken) {
        headers["Authorization"] =
            "Bearer " + authToken;
    }

    const response = await fetch(
        SUPABASE_URL + path,
        {
            ...options,
            headers: {
                ...headers,
                ...(options.headers || {})
            }
        }
    );

    const text = await response.text();

    let data = null;

    try {
        data = text ? JSON.parse(text) : null;
    } catch {
        data = null;
    }

    if (!response.ok) {

        const errorText =
            data?.msg ||
            data?.message ||
            data?.error_description ||
            text ||
            "Unbekannter Fehler";

        throw new Error(errorText);
    }

    return data;
}


/* =====================================================
   LOGIN BUTTON
===================================================== */

$("loginButton").addEventListener(
    "click",
    login
);


async function login() {

    const email =
        $("loginEmail").value.trim();

    const password =
        $("loginPassword").value;

    const message =
        $("loginMessage");


    if (!email) {
        message.textContent =
            "❌ Bitte deine E-Mail eingeben.";
        return;
    }


    if (!password) {
        message.textContent =
            "❌ Bitte dein Passwort eingeben.";
        return;
    }


    message.textContent =
        "⏳ Anmeldung...";


    try {

        const data =
            await supabaseRequest(
                "/auth/v1/token?grant_type=password",
                {
                    method: "POST",
                    body: JSON.stringify({
                        email: email,
                        password: password
                    })
                }
            );


        authToken =
            data.access_token;

        player.id =
            data.user.id;

        player.email =
            data.user.email;


        await loadPlayer();


        loadLocalData();

        updateMenu();

        showScreen("menuScreen");


    } catch (error) {

        console.error(error);

        message.textContent =
            "❌ " + error.message;
    }
}


/* =====================================================
   REGISTRIERUNG BUTTON
===================================================== */

$("registerButton").addEventListener(
    "click",
    register
);


async function register() {

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

    const message =
        $("registerMessage");


    if (username.length < 3) {

        message.textContent =
            "❌ Benutzername braucht mindestens 3 Zeichen.";

        return;
    }


    if (username.length > 20) {

        message.textContent =
            "❌ Benutzername darf maximal 20 Zeichen haben.";

        return;
    }


    if (!email.includes("@")) {

        message.textContent =
            "❌ Bitte eine gültige E-Mail eingeben.";

        return;
    }


    if (password.length < 6) {

        message.textContent =
            "❌ Passwort braucht mindestens 6 Zeichen.";

        return;
    }


    message.textContent =
        "⏳ Account wird erstellt...";


    try {

        const data =
            await supabaseRequest(
                "/auth/v1/signup",
                {
                    method: "POST",

                    body: JSON.stringify({

                        email: email,

                        password: password,

                        data: {
                            username: username
                        }

                    })
                }
            );


        if (!data.user) {

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


        if (data.access_token) {

            authToken =
                data.access_token;

            await createPlayer();

            showScreen("menuScreen");

            updateMenu();

        } else {

            message.textContent =
                "✅ Account erstellt! Prüfe deine E-Mail und melde dich danach an.";

        }


    } catch (error) {

        console.error(error);

        message.textContent =
            "❌ " + error.message;
    }
}


/* =====================================================
   PLAYER ERSTELLEN
===================================================== */

async function createPlayer() {

    await supabaseRequest(
        "/rest/v1/Players",
        {
            method: "POST",

            headers: {
                "Prefer": "return=minimal"
            },

            body: JSON.stringify({

                id: player.id,

                username: player.username,

                highscore: 0

            })
        }
    );
}


/* =====================================================
   PLAYER LADEN
===================================================== */

async function loadPlayer() {

    const data =
        await supabaseRequest(
            "/rest/v1/Players" +
            "?id=eq." +
            encodeURIComponent(player.id) +
            "&select=*"
        );


    if (data.length > 0) {

        player.username =
            data[0].username || "Spieler";

        player.highscore =
            Number(
                data[0].highscore || 0
            );

    } else {

        player.username =
            player.email.split("@")[0];

        await createPlayer();
    }
}


/* =====================================================
   MENÜ
===================================================== */

function updateMenu() {

    $("playerName").textContent =
        player.username;

    $("menuCoins").textContent =
        player.coins.toLocaleString();

    $("menuHighscore").textContent =
        player.highscore.toLocaleString();
}


/* =====================================================
   NAVIGATION
===================================================== */

$("registerOpenButton").addEventListener(
    "click",
    () => {
        showScreen("registerScreen");
    }
);


$("backLoginButton").addEventListener(
    "click",
    () => {
        showScreen("loginScreen");
    }
);


$("rankingBackButton").addEventListener(
    "click",
    () => {
        showScreen("menuScreen");
    }
);


$("gameOverMenuButton").addEventListener(
    "click",
    () => {
        showScreen("menuScreen");
        updateMenu();
    }
);


/* =====================================================
   RANGLISTE
===================================================== */

$("rankingButton").addEventListener(
    "click",
    loadRanking
);


async function loadRanking() {

    showScreen("rankingScreen");

    $("rankingList").textContent =
        "⏳ Rangliste wird geladen...";


    try {

        const data =
            await supabaseRequest(
                "/rest/v1/Players" +
                "?select=username,highscore" +
                "&order=highscore.desc" +
                "&limit=100"
            );


        $("rankingList").innerHTML = "";


        if (data.length === 0) {

            $("rankingList").textContent =
                "Noch keine Spieler.";

            return;
        }


        data.forEach(
            (item, index) => {

                const row =
                    document.createElement("div");

                row.style.padding =
                    "8px";

                row.innerHTML =
                    (index + 1) +
                    ". " +
                    escapeHTML(item.username) +
                    " — " +
                    Number(
                        item.highscore || 0
                    ).toLocaleString();

                $("rankingList")
                    .appendChild(row);
            }
        );


    } catch (error) {

        $("rankingList").textContent =
            "❌ " + error.message;
    }
}


function escapeHTML(text) {

    return String(text)
        .replaceAll("&", "&amp;")
        .replaceAll("<", "&lt;")
        .replaceAll(">", "&gt;")
        .replaceAll('"', "&quot;")
        .replaceAll("'", "&#039;");
}


/* =====================================================
   SPIEL
===================================================== */

const canvas =
    $("game");

const ctx =
    canvas.getContext("2d");


let gameRunning = false;
let score = 0;
let gameCoins = 0;

let lane = 0;

let playerX = 0;

let objects = [];

let lastTime = 0;

let speed = 280;

let coinTimer = 0;
let carTimer = 0;


function resizeGame() {

    canvas.width =
        window.innerWidth;

    canvas.height =
        window.innerHeight;
}


resizeGame();

window.addEventListener(
    "resize",
    resizeGame
);


function road() {

    const width =
        Math.min(
            window.innerWidth * 0.84,
            620
        );

    return {
        left:
            (window.innerWidth - width) / 2,
        width: width
    };
}


function laneX(number) {

    const r = road();

    const laneWidth =
        r.width / 3;

    return (
        r.left +
        laneWidth * (number + 1) +
        laneWidth / 2
    );
}


/* =====================================================
   SPIEL START
===================================================== */

$("startButton").addEventListener(
    "click",
    startGame
);


function startGame() {

    $("hud").style.display =
        "flex";

    $("pauseButton").style.display =
        "block";


    gameRunning = true;

    score = 0;

    gameCoins = 0;

    lane = 0;

    speed = 280;

    objects = [];

    coinTimer = 1;

    carTimer = 1;

    playerX =
        laneX(lane);

    lastTime =
        performance.now();
}


/* =====================================================
   SPIEL ZEICHNEN
===================================================== */

function drawGame() {

    ctx.fillStyle =
        "#02040a";

    ctx.fillRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    const r =
        road();


    ctx.fillStyle =
        "#111a22";

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


    ctx.fillStyle =
        "#ffffff55";


    for (
        let y = 0;
        y < canvas.height;
        y += 90
    ) {

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


    /* Spieler */

    const y =
        canvas.height * 0.76;


    ctx.fillStyle =
        "#00eaff";

    ctx.shadowColor =
        "#00eaff";

    ctx.shadowBlur = 25;


    ctx.fillRect(
        playerX - 27,
        y - 27,
        54,
        54
    );


    ctx.shadowBlur = 0;


    /* Objekte */

    objects.forEach(
        object => {

            const x =
                laneX(object.lane);


            if (object.type === "coin") {

                ctx.fillStyle =
                    "#ffd900";

                ctx.shadowColor =
                    "#ffd900";

                ctx.shadowBlur = 15;

                ctx.beginPath();

                ctx.arc(
                    x,
                    object.y,
                    14,
                    0,
                    Math.PI * 2
                );

                ctx.fill();

                ctx.shadowBlur = 0;

            } else {

                ctx.fillStyle =
                    "#ff1744";

                ctx.fillRect(
                    x - 30,
                    object.y - 45,
                    60,
                    90
                );
            }
        }
    );
}


/* =====================================================
   SPIEL UPDATE
===================================================== */

function updateGame(delta) {

    if (!gameRunning)
        return;


    const seconds =
        delta / 1000;


    score +=
        seconds * 10;


    speed =
        Math.min(
            650,
            speed + seconds * 4
        );


    playerX +=
        (
            laneX(lane) -
            playerX
        ) *
        Math.min(
            1,
            seconds * 12
        );


    coinTimer -= seconds;
    carTimer -= seconds;


    if (coinTimer <= 0) {

        const coinLane =
            [-1, 0, 1][
                Math.floor(
                    Math.random() * 3
                )
            ];


        for (
            let i = 0;
            i < 5;
            i++
        ) {

            objects.push({
                type: "coin",
                lane: coinLane,
                y: -60 - i * 70
            });
        }


        coinTimer = 3;
    }


    if (carTimer <= 0) {

        const carLane =
            [-1, 0, 1][
                Math.floor(
                    Math.random() * 3
                )
            ];


        objects.push({
            type: "car",
            lane: carLane,
            y: -100
        });


        carTimer =
            1.4 + Math.random() * 0.8;
    }


    for (
        let i = objects.length - 1;
        i >= 0;
        i--
    ) {

        const object =
            objects[i];


        object.y +=
            speed * seconds;


        if (
            object.y >
            canvas.height + 100
        ) {

            objects.splice(i, 1);

            continue;
        }


        const playerY =
            canvas.height * 0.76;


        if (
            object.type === "coin" &&
            Math.abs(
                object.y - playerY
            ) < 50 &&
            object.lane === lane
        ) {

            gameCoins++;

            objects.splice(i, 1);

            continue;
        }


        if (
            object.type === "car" &&
            Math.abs(
                object.y - playerY
            ) < 70 &&
            object.lane === lane
        ) {

            endGame();

            return;
        }
    }


    $("coins").textContent =
        player.coins + gameCoins;

    $("score").textContent =
        Math.floor(score);
}


/* =====================================================
   GAME LOOP
===================================================== */

function loop(time) {

    const delta =
        Math.min(
            40,
            time - lastTime
        );


    lastTime = time;


    drawGame();

    updateGame(delta);


    requestAnimationFrame(loop);
}


requestAnimationFrame(loop);


/* =====================================================
   STEUERUNG
===================================================== */

document.addEventListener(
    "keydown",
    event => {

        if (!gameRunning)
            return;


        if (
            event.key === "ArrowLeft" ||
            event.key.toLowerCase() === "a"
        ) {

            lane =
                Math.max(
                    -1,
                    lane - 1
                );
        }


        if (
            event.key === "ArrowRight" ||
            event.key.toLowerCase() === "d"
        ) {

            lane =
                Math.min(
                    1,
                    lane + 1
                );
        }
    }
);


/* =====================================================
   SWIPE
===================================================== */

let touchStart = 0;


canvas.addEventListener(
    "touchstart",
    event => {

        touchStart =
            event.changedTouches[0]
                .clientX;
    },
    { passive: true }
);


canvas.addEventListener(
    "touchend",
    event => {

        if (!gameRunning)
            return;


        const end =
            event.changedTouches[0]
                .clientX;


        if (
            end - touchStart > 30
        ) {

            lane =
                Math.min(
                    1,
                    lane + 1
                );

        } else if (
            touchStart - end > 30
        ) {

            lane =
                Math.max(
                    -1,
                    lane - 1
                );
        }
    },
    { passive: true }
);


/* =====================================================
   PAUSE
===================================================== */

function pauseGame() {

    gameRunning = false;

    $("pauseButton").style.display =
        "none";
}


/* =====================================================
   GAME OVER
===================================================== */

async function endGame() {

    gameRunning = false;

    $("pauseButton").style.display =
        "none";

    $("hud").style.display =
        "none";


    player.coins +=
        gameCoins;


    const finalScore =
        Math.floor(score);


    if (
        finalScore >
        player.highscore
    ) {

        player.highscore =
            finalScore;

        await saveHighscore();
    }


    $("finalScore").textContent =
        finalScore;

    $("finalCoins").textContent =
        gameCoins;

    $("finalHighscore").textContent =
        player.highscore;


    updateMenu();

    showScreen(
        "gameOverScreen"
    );
}


/* =====================================================
   HIGHSCORE SPEICHERN
===================================================== */

async function saveHighscore() {

    try {

        await supabaseRequest(
            "/rest/v1/Players?id=eq." +
            encodeURIComponent(player.id),
            {
                method: "PATCH",

                headers: {
                    "Prefer": "return=minimal"
                },

                body: JSON.stringify({
                    username:
                        player.username,
                    highscore:
                        player.highscore
                })
            }
        );

    } catch (error) {

        console.error(
            "Highscore konnte nicht gespeichert werden:",
            error
        );
    }
}


/* =====================================================
   BUTTONS
===================================================== */

$("restartButton").addEventListener(
    "click",
    startGame
);


$("logoutButton").addEventListener(
    "click",
    () => {

        authToken = null;

        player = {
            id: null,
            username: "",
            email: "",
            coins: 0,
            highscore: 0
        };

        showScreen(
            "loginScreen"
        );
    }
);


/* =====================================================
   LOCAL DATEN
===================================================== */

function loadLocalData() {

    player.coins =
        Number(
            localStorage.getItem(
                "cubeRushCoins"
            ) || 0
        );
}


/* =====================================================
   START
===================================================== */

loadLocalData();

showScreen(
    "loginScreen"
);

</script>

</body>
</html>
