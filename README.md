<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width, initial-scale=1.0,
      maximum-scale=1.0, user-scalable=no">

<title>Runner Game</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
}

html, body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    touch-action: none;
    font-family: Arial, sans-serif;
    background: #7dccff;
}

canvas {
    display: block;
    width: 100%;
    height: 100%;
}

#hud {
    position: fixed;
    top: 12px;
    left: 12px;
    right: 12px;

    display: none;
    justify-content: space-between;

    z-index: 20;

    pointer-events: none;
}

.hudBox {
    background: rgba(0,0,0,.58);
    color: white;

    padding: 9px 13px;
    border-radius: 12px;

    font-weight: bold;
    font-size: 15px;
}

.screen {
    position: fixed;
    inset: 0;

    z-index: 50;

    display: flex;
    flex-direction: column;

    justify-content: center;
    align-items: center;

    color: white;
    text-align: center;

    background: rgba(0,0,0,.52);
}

.hidden {
    display: none !important;
}

h1 {
    font-size: 42px;
    margin-bottom: 18px;
    text-shadow: 0 4px 10px black;
}

.instructions {
    line-height: 1.8;
    font-size: 16px;
}

button {
    border: none;
    border-radius: 14px;

    padding: 15px 30px;
    margin-top: 22px;

    background: #ffd000;
    color: #111;

    font-size: 18px;
    font-weight: bold;

    box-shadow: 0 5px 15px rgba(0,0,0,.35);
}

button:active {
    transform: scale(.94);
}
</style>
</head>

<body>

<canvas id="gameCanvas"></canvas>


<!-- ==============================
     SPIEL HUD
============================== -->

<div id="gameHud">

    <div class="hudBox">
        🪙 <span id="coinText">0</span>
    </div>

    <div class="hudBox">
        🏆 <span id="scoreText">0</span>
    </div>

    <div class="hudBox">
        ⭐ <span id="bestText">0</span>
    </div>

</div>


<!-- ==============================
     STARTMENÜ
============================== -->

<div id="startScreen" class="screen">

    <h1>🏃 RUNNER</h1>

    <div class="instructions">
        ⬅️ Nach links wischen<br>
        ➡️ Nach rechts wischen<br>
        ⬆️ Nach oben wischen = Springen
    </div>

    <button id="startGameButton">
        SPIEL STARTEN
    </button>

</div>


<!-- ==============================
     GAME OVER
============================== -->

<div id="gameOverScreen" class="screen hidden">

    <h1>💥 GAME OVER</h1>

    <p>
        Punkte:
        <b id="finalScore">0</b>
    </p>

    <p>
        Münzen:
        <b id="finalCoins">0</b>
    </p>

    <p>
        Highscore:
        <b id="finalHighscore">0</b>
    </p>

    <button id="restartButton">
        NOCHMAL SPIELEN
    </button>

    <button id="backToMenuButton">
        ZUM MENÜ
    </button>

</div>


<script>

/* ==========================================
   CANVAS
========================================== */

const canvas =
    document.getElementById("gameCanvas");

const ctx =
    canvas.getContext("2d");


let width = 0;
let height = 0;
let pixelRatio = 1;


function resizeCanvas() {

    width =
        window.innerWidth;

    height =
        window.innerHeight;

    pixelRatio =
        Math.min(
            window.devicePixelRatio || 1,
            2
        );

    canvas.width =
        width * pixelRatio;

    canvas.height =
        height * pixelRatio;

    canvas.style.width =
        width + "px";

    canvas.style.height =
        height + "px";

    ctx.setTransform(
        pixelRatio,
        0,
        0,
        pixelRatio,
        0,
        0
    );
}


window.addEventListener(
    "resize",
    resizeCanvas
);

resizeCanvas();


/* ==========================================
   MENÜ-ELEMENTE
========================================== */

const startScreen =
    document.getElementById(
        "startScreen"
    );

const gameOverScreen =
    document.getElementById(
        "gameOverScreen"
    );

const gameHud =
    document.getElementById(
        "gameHud"
    );

const startGameButton =
    document.getElementById(
        "startGameButton"
    );

const restartButton =
    document.getElementById(
        "restartButton"
    );

const backToMenuButton =
    document.getElementById(
        "backToMenuButton"
    );


/* ==========================================
   SPEICHER
========================================== */

let highScore =
    Number(
        localStorage.getItem(
            "runnerHighScore"
        )
    ) || 0;


let savedCoins =
    Number(
        localStorage.getItem(
            "runnerCoins"
        )
    ) || 0;


/* ==========================================
   SPIELVARIABLEN
========================================== */

let gameRunning = false;

let score = 0;

let collectedCoins = 0;


/*
   -1 = links
    0 = mitte
    1 = rechts
*/

let currentLane = 0;
let targetLane = 0;
let playerLane = 0;


/* ==========================================
   SPRUNG
========================================== */

let jumpHeight = 0;
let jumpVelocity = 0;

const JUMP_POWER = 0.020;
const GRAVITY = 0.000060;


/* ==========================================
   GESCHWINDIGKEIT
========================================== */

let speed = 0.00018;

const START_SPEED = 0.00018;
const MAX_SPEED = 0.00040;


/* ==========================================
   OBJEKTE
========================================== */

let obstacles = [];
let coins = [];


/* ==========================================
   TIMER
========================================== */

let obstacleTimer = 0;
let coinTimer = 0;

let lastTime = 0;


/* ==========================================
   PERSPEKTIVE
========================================== */

function getHorizon() {

    return height * 0.40;
}


function getRoadWidth(z) {

    const farWidth =
        width * 0.30;

    const nearWidth =
        width * 1.30;

    return (
        farWidth +
        (nearWidth - farWidth)
        * (1 - z)
    );
}


function project(z) {

    const depth =
        1 - z;

    return {

        y:
            getHorizon() +
            depth *
            height *
            0.52,

        width:
            getRoadWidth(z)
    };
}


function getLaneX(
    lane,
    z
) {

    const roadWidth =
        getRoadWidth(z);

    return (
        width / 2 +
        lane *
        roadWidth *
        0.27
    );
}


/* ==========================================
   HIMMEL
========================================== */

function drawSky() {

    const gradient =
        ctx.createLinearGradient(
            0,
            0,
            0,
            height
        );

    gradient.addColorStop(
        0,
        "#42b9ff"
    );

    gradient.addColorStop(
        1,
        "#e1f7ff"
    );

    ctx.fillStyle =
        gradient;

    ctx.fillRect(
        0,
        0,
        width,
        height
    );
}


/* ==========================================
   BODEN
========================================== */

function drawGround() {

    ctx.fillStyle =
        "#58a34d";

    ctx.fillRect(
        0,
        getHorizon(),
        width,
        height
    );
}


/* ==========================================
   STADT
========================================== */

function drawCity() {

    const horizon =
        getHorizon();

    const buildings = [

        [0.02, 80, 150],
        [0.13, 100, 180],
        [0.26, 70, 120],

        [0.74, 80, 140],
        [0.85, 105, 190],
        [0.97, 75, 140]

    ];


    buildings.forEach(
        function(building, index) {

            const x =
                width * building[0];

            const buildingWidth =
                building[1];

            const buildingHeight =
                building[2];


            ctx.fillStyle =
                index % 2 === 0
                ? "#66717c"
                : "#555f69";


            ctx.fillRect(
                x,
                horizon -
                buildingHeight,
                buildingWidth,
                buildingHeight
            );


            ctx.fillStyle =
                "rgba(255,220,100,.65)";


            for (
                let y =
                    horizon -
                    buildingHeight +
                    15;

                y <
                    horizon - 10;

                y += 22
            ) {

                for (
                    let x2 =
                        x + 10;

                    x2 <
                        x +
                        buildingWidth -
                        8;

                    x2 += 20
                ) {

                    ctx.fillRect(
                        x2,
                        y,
                        7,
                        10
                    );
                }
            }
        }
    );
}


/* ==========================================
   STRASSE
========================================== */

function drawRoad() {

    const top =
        getHorizon();

    const topWidth =
        width * 0.30;

    const bottomWidth =
        width * 1.30;


    /*
       Große, gerade, symmetrische Fläche.
       Keine Außenränder.
    */

    ctx.fillStyle =
        "#37393d";


    ctx.beginPath();

    ctx.moveTo(
        width / 2 -
        topWidth / 2,
        top
    );

    ctx.lineTo(
        width / 2 +
        topWidth / 2,
        top
    );

    ctx.lineTo(
        width / 2 +
        bottomWidth / 2,
        height
    );

    ctx.lineTo(
        width / 2 -
        bottomWidth / 2,
        height
    );

    ctx.closePath();

    ctx.fill();


    /*
       Nur die beiden
       inneren Spurmarkierungen.
    */

    drawLaneLine(-1);
    drawLaneLine(1);
}


function drawLaneLine(side) {

    const top =
        getHorizon();

    const topWidth =
        width * 0.30;

    const bottomWidth =
        width * 1.30;


    const topX =
        width / 2 +
        side *
        topWidth *
        0.27;


    const bottomX =
        width / 2 +
        side *
        bottomWidth *
        0.27;


    ctx.strokeStyle =
        "rgba(255,255,255,.45)";

    ctx.lineWidth = 3;


    ctx.beginPath();

    ctx.moveTo(
        topX,
        top
    );

    ctx.lineTo(
        bottomX,
        height
    );

    ctx.stroke();
}


/* ==========================================
   SPIELER
========================================== */

function drawPlayer() {

    const x =
        width / 2 +
        playerLane *
        width *
        0.27;


    const ground =
        height * 0.81;


    const y =
        ground -
        jumpHeight *
        height *
        0.30;


    /* Schatten */

    ctx.fillStyle =
        "rgba(0,0,0,.30)";

    ctx.beginPath();

    ctx.ellipse(
        x,
        ground + 28,
        32,
        9,
        0,
        0,
        Math.PI * 2
    );

    ctx.fill();


    /* Beine */

    ctx.fillStyle =
        "#143d99";

    ctx.fillRect(
        x - 17,
        y + 10,
        13,
        48
    );

    ctx.fillRect(
        x + 5,
        y + 10,
        13,
        48
    );


    /* Körper */

    ctx.fillStyle =
        "#176cff";

    ctx.fillRect(
        x - 29,
        y - 58,
        58,
        70
    );


    /* Arme */

    ctx.fillRect(
        x - 42,
        y - 50,
        12,
        50
    );

    ctx.fillRect(
        x + 30,
        y - 50,
        12,
        50
    );


    /* Kopf */

    ctx.fillStyle =
        "#ffc49a";

    ctx.beginPath();

    ctx.arc(
        x,
        y - 82,
        23,
        0,
        Math.PI * 2
    );

    ctx.fill();


    /* Haare */

    ctx.fillStyle =
        "#222";

    ctx.beginPath();

    ctx.arc(
        x,
        y - 91,
        22,
        Math.PI,
        Math.PI * 2
    );

    ctx.fill();
}


/* ==========================================
   ROTER BLOCK
========================================== */

function drawObstacle(
    obstacle
) {

    const p =
        project(
            obstacle.z
        );


    const x =
        getLaneX(
            obstacle.lane,
            obstacle.z
        );


    const size =
        30 +
        p.width * 0.055;


    const bottom =
        p.y;

    const top =
        bottom - size;


    /* Schatten */

    ctx.fillStyle =
        "rgba(0,0,0,.25)";

    ctx.fillRect(
        x - size / 2,
        bottom - 5,
        size,
        8
    );


    /*
       Vorderseite:
       komplett gerade.
    */

    ctx.fillStyle =
        "#d92828";

    ctx.fillRect(
        x - size / 2,
        top,
        size,
        size
    );


    /*
       Helle Oberkante.
    */

    ctx.fillStyle =
        "#ff5555";

    ctx.fillRect(
        x - size / 2,
        top,
        size,
        size * 0.12
    );


    /*
       Dunkle rechte Seite.
    */

    ctx.fillStyle =
        "#a51d1d";

    ctx.fillRect(
        x + size * 0.34,
        top,
        size * 0.16,
        size
    );
}


/* ==========================================
   MÜNZE
========================================== */

function drawCoin(
    coin
) {

    const p =
        project(
            coin.z
        );


    const x =
        getLaneX(
            coin.lane,
            coin.z
        );


    const y =
        p.y -
        p.width * 0.045;


    const radius =
        Math.max(
            5,
            p.width * 0.018
        );


    ctx.fillStyle =
        "#d99f00";

    ctx.beginPath();

    ctx.arc(
        x,
        y,
        radius + 2,
        0,
        Math.PI * 2
    );

    ctx.fill();


    ctx.fillStyle =
        "#ffd21a";

    ctx.beginPath();

    ctx.arc(
        x,
        y,
        radius,
        0,
        Math.PI * 2
    );

    ctx.fill();


    ctx.fillStyle =
        "#fff4a0";

    ctx.beginPath();

    ctx.arc(
        x - radius * .3,
        y - radius * .3,
        radius * .25,
        0,
        Math.PI * 2
    );

    ctx.fill();
}


/* ==========================================
   OBJEKTE
========================================== */

function createObstacle() {

    const randomLane =
        Math.floor(
            Math.random() * 3
        ) - 1;


    obstacles.push({

        lane:
            randomLane,

        z:
            1

    });
}


function createCoins() {

    const randomLane =
        Math.floor(
            Math.random() * 3
        ) - 1;


    for (
        let i = 0;
        i < 5;
        i++
    ) {

        coins.push({

            lane:
                randomLane,

            z:
                1 -
                i * 0.08

        });
    }
}


/* ==========================================
   STEUERUNG
========================================== */

function moveLeft() {

    if (!gameRunning)
        return;


    currentLane--;

    if (
        currentLane < -1
    ) {
        currentLane = -1;
    }


    targetLane =
        currentLane;
}


function moveRight() {

    if (!gameRunning)
        return;


    currentLane++;

    if (
        currentLane > 1
    ) {
        currentLane = 1;
    }


    targetLane =
        currentLane;
}


function jumpPlayer() {

    if (!gameRunning)
        return;


    if (
        jumpHeight <= 0.001
    ) {

        jumpVelocity =
            JUMP_POWER;
    }
}


/* ==========================================
   TOUCH
========================================== */

let touchStartX = 0;
let touchStartY = 0;


canvas.addEventListener(
    "touchstart",
    function(event) {

        const touch =
            event.changedTouches[0];

        touchStartX =
            touch.clientX;

        touchStartY =
            touch.clientY;

    },
    {
        passive: true
    }
);


canvas.addEventListener(
    "touchend",
    function(event) {

        if (!gameRunning)
            return;


        const touch =
            event.changedTouches[0];


        const dx =
            touch.clientX -
            touchStartX;


        const dy =
            touch.clientY -
            touchStartY;


        if (
            Math.max(
                Math.abs(dx),
                Math.abs(dy)
            ) < 35
        ) {
            return;
        }


        if (
            Math.abs(dx) >
            Math.abs(dy)
        ) {

            if (dx > 0) {

                moveRight();

            } else {

                moveLeft();
            }

        } else {

            if (dy < 0) {

                jumpPlayer();
            }
        }

    },
    {
        passive: true
    }
);


/* ==========================================
   TASTATUR
========================================== */

window.addEventListener(
    "keydown",
    function(event) {

        if (
            event.key === "ArrowLeft"
        ) {
            moveLeft();
        }


        if (
            event.key === "ArrowRight"
        ) {
            moveRight();
        }


        if (
            event.key === "ArrowUp" ||
            event.key === " "
        ) {
            jumpPlayer();
        }
    }
);


/* ==========================================
   SPIEL START
========================================== */

function startGame() {

    /*
       WICHTIG:
       Startmenü sofort verstecken.
    */

    startScreen.classList.add(
        "hidden"
    );


    gameOverScreen.classList.add(
        "hidden"
    );


    gameHud.style.display =
        "flex";


    gameRunning = true;


    score = 0;

    collectedCoins = 0;


    speed =
        START_SPEED;


    currentLane = 0;
    targetLane = 0;
    playerLane = 0;


    jumpHeight = 0;
    jumpVelocity = 0;


    obstacles = [];
    coins = [];


    obstacleTimer = 0;
    coinTimer = 0;


    /*
       Erste Münzen.
    */

    coins.push({
        lane: 0,
        z: 0.82
    });

    coins.push({
        lane: 0,
        z: 0.74
    });

    coins.push({
        lane: 0,
        z: 0.66
    });


    /*
       Erstes Hindernis rechts.
    */

    obstacles.push({
        lane: 1,
        z: 0.90
    });


    updateHUD();
}


/* ==========================================
   UPDATE
========================================== */

function update(delta) {

    if (!gameRunning)
        return;


    /*
       Langsame Beschleunigung.
    */

    speed +=
        delta *
        0.000000010;


    if (
        speed > MAX_SPEED
    ) {

        speed =
            MAX_SPEED;
    }


    /*
       Flüssiger Spurwechsel.
    */

    playerLane +=
        (
            targetLane -
            playerLane
        ) *
        Math.min(
            1,
            delta * 0.012
        );


    /*
       Sprung.
    */

    if (
        jumpHeight > 0 ||
        jumpVelocity > 0
    ) {

        jumpHeight +=
            jumpVelocity *
            delta;


        jumpVelocity -=
            GRAVITY *
            delta;


        if (
            jumpHeight <= 0
        ) {

            jumpHeight = 0;
            jumpVelocity = 0;
        }
    }


    /* ======================================
       BLÖCKE
    ====================================== */

    for (
        let i =
            obstacles.length - 1;

        i >= 0;

        i--
    ) {

        const obstacle =
            obstacles[i];


        obstacle.z -=
            speed *
            delta;


        /*
           Kollision direkt vor dem Spieler.
        */

        if (
            obstacle.z < 0.11 &&
            obstacle.z > 0.03
        ) {

            if (
                Math.abs(
                    obstacle.lane -
                    playerLane
                ) < 0.35
            ) {

                /*
                   Springen hilft.
                */

                if (
                    jumpHeight < 0.35
                ) {

                    endGame();

                    return;
                }
            }
        }


        if (
            obstacle.z < 0
        ) {

            obstacles.splice(
                i,
                1
            );
        }
    }


    /* ======================================
       MÜNZEN
    ====================================== */

    for (
        let i =
            coins.length - 1;

        i >= 0;

        i--
    ) {

        const coin =
            coins[i];


        coin.z -=
            speed *
            delta;


        /*
           Einsammelbereich.
        */

        if (
            coin.z < 0.14 &&
            coin.z > 0.02
        ) {

            if (
                Math.abs(
                    coin.lane -
                    playerLane
                ) < 0.38
            ) {

                collectedCoins++;


                coins.splice(
                    i,
                    1
                );


                continue;
            }
        }


        if (
            coin.z < 0
        ) {

            coins.splice(
                i,
                1
            );
        }
    }


    /* ======================================
       NEUE BLÖCKE
    ====================================== */

    obstacleTimer +=
        delta;


    if (
        obstacleTimer > 2100
    ) {

        createObstacle();

        obstacleTimer = 0;
    }


    /* ======================================
       NEUE MÜNZEN
    ====================================== */

    coinTimer +=
        delta;


    if (
        coinTimer > 1400
    ) {

        createCoins();

        coinTimer = 0;
    }


    /*
       Punkte.
    */

    score +=
        delta *
        0.01;


    updateHUD();
}


/* ==========================================
   HUD
========================================== */

function updateHUD() {

    document.getElementById(
        "coinText"
    ).textContent =
        savedCoins +
        collectedCoins;


    document.getElementById(
        "scoreText"
    ).textContent =
        Math.floor(score);


    document.getElementById(
        "bestText"
    ).textContent =
        highScore;
}


/* ==========================================
   GAME OVER
========================================== */

function endGame() {

    gameRunning = false;


    const finalScore =
        Math.floor(score);


    savedCoins +=
        collectedCoins;


    if (
        finalScore >
        highScore
    ) {

        highScore =
            finalScore;


        localStorage.setItem(
            "runnerHighScore",
            String(highScore)
        );
    }


    localStorage.setItem(
        "runnerCoins",
        String(savedCoins)
    );


    document.getElementById(
        "finalScore"
    ).textContent =
        finalScore;


    document.getElementById(
        "finalCoins"
    ).textContent =
        collectedCoins;


    document.getElementById(
        "finalHighscore"
    ).textContent =
        highScore;


    gameHud.style.display =
        "none";


    gameOverScreen.classList.remove(
        "hidden"
    );
}


/* ==========================================
   ZEICHNEN
========================================== */

function draw() {

    drawSky();

    drawGround();

    drawCity();

    drawRoad();


    /*
       Entfernte Objekte zuerst.
    */

    const objects = [];


    obstacles.forEach(
        function(obstacle) {

            objects.push({

                type:
                    "obstacle",

                object:
                    obstacle

            });
        }
    );


    coins.forEach(
        function(coin) {

            objects.push({

                type:
                    "coin",

                object:
                    coin

            });
        }
    );


    objects.sort(
        function(a, b) {

            return (
                b.object.z -
                a.object.z
            );
        }
    );


    objects.forEach(
        function(item) {

            if (
                item.type ===
                "obstacle"
            ) {

                drawObstacle(
                    item.object
                );

            } else {

                drawCoin(
                    item.object
                );
            }
        }
    );


    drawPlayer();
}


/* ==========================================
   GAME LOOP
========================================== */

function gameLoop(time) {

    if (
        lastTime === 0
    ) {

        lastTime =
            time;
    }


    let delta =
        time -
        lastTime;


    /*
       Schutz vor großen Zeitsprüngen.
    */

    if (
        delta > 40
    ) {

        delta = 40;
    }


    lastTime =
        time;


    update(delta);

    draw();


    requestAnimationFrame(
        gameLoop
    );
}


requestAnimationFrame(
    gameLoop
);


/* ==========================================
   BUTTONS
========================================== */

startGameButton.addEventListener(
    "click",
    function() {

        startGame();

    }
);


restartButton.addEventListener(
    "click",
    function() {

        startGame();

    }
);


backToMenuButton.addEventListener(
    "click",
    function() {

        gameRunning = false;


        gameOverScreen.classList.add(
            "hidden"
        );


        startScreen.classList.remove(
            "hidden"
        );


        gameHud.style.display =
            "none";
    }
);

</script>

</body>
</html>
