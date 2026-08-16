<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">

<meta name="viewport"
content="width=device-width,
initial-scale=1.0,
maximum-scale=1.0,
user-scalable=no,
viewport-fit=cover">

<title>Runner 3D</title>

<style>

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
}

html,
body {
    width: 100%;
    height: 100%;

    margin: 0;
    padding: 0;

    overflow: hidden;

    background: #000;

    touch-action: none;

    font-family: Arial, sans-serif;
}

body {
    position: fixed;
    inset: 0;
}

canvas {
    position: fixed;

    left: 0;
    top: 0;

    width: 100vw;
    height: 100vh;

    display: block;
}


/* =========================
   HUD
========================= */

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
    color: white;

    background:
        rgba(0,0,0,.58);

    padding: 9px 13px;

    border-radius: 12px;

    font-size: 15px;
    font-weight: bold;
}


/* =========================
   MENÜ
========================= */

.screen {
    position: fixed;

    inset: 0;

    width: 100vw;
    height: 100vh;

    display: flex;

    flex-direction: column;

    align-items: center;
    justify-content: center;

    text-align: center;

    color: white;

    background:
        rgba(0,0,0,.52);

    z-index: 50;
}

.hidden {
    display: none !important;
}

h1 {
    font-size: 42px;

    margin-bottom: 18px;

    text-shadow:
        0 4px 12px #000;
}

.instructions {
    font-size: 16px;

    line-height: 1.8;
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

    box-shadow:
        0 5px 15px
        rgba(0,0,0,.35);
}

button:active {
    transform: scale(.94);
}

</style>
</head>

<body>


<!-- =========================
     SPIEL
========================= -->

<canvas id="game"></canvas>


<!-- =========================
     HUD
========================= -->

<div id="hud">

    <div class="hudBox">
        🪙 <span id="coins">0</span>
    </div>

    <div class="hudBox">
        🏆 <span id="score">0</span>
    </div>

    <div class="hudBox">
        ⭐ <span id="best">0</span>
    </div>

</div>


<!-- =========================
     START
========================= -->

<div id="startScreen"
     class="screen">

    <h1>
        🏃 RUNNER
    </h1>

    <div class="instructions">

        ⬅️ Nach links wischen<br>
        ➡️ Nach rechts wischen<br>
        ⬆️ Nach oben wischen = Springen

    </div>

    <button id="startButton">
        SPIEL STARTEN
    </button>

</div>


<!-- =========================
     GAME OVER
========================= -->

<div id="gameOver"
     class="screen hidden">

    <h1>
        💥 GAME OVER
    </h1>

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
        <b id="finalBest">0</b>
    </p>

    <button id="restartButton">
        NOCHMAL SPIELEN
    </button>

    <button id="menuButton">
        MENÜ
    </button>

</div>


<script>

/* ==================================================
   CANVAS
================================================== */

const canvas =
document.getElementById("game");

const ctx =
canvas.getContext("2d");


let W = 0;
let H = 0;
let DPR = 1;


function resize() {

    W =
    window.innerWidth;

    H =
    window.innerHeight;


    DPR =
    Math.min(
        window.devicePixelRatio || 1,
        2
    );


    canvas.width =
    W * DPR;

    canvas.height =
    H * DPR;


    canvas.style.width =
    W + "px";

    canvas.style.height =
    H + "px";


    ctx.setTransform(
        DPR,
        0,
        0,
        DPR,
        0,
        0
    );
}


window.addEventListener(
    "resize",
    resize
);

resize();


/* ==================================================
   SPEICHER
================================================== */

let highScore =
Number(
    localStorage.getItem(
        "runnerHighScore"
    )
) || 0;


let totalCoins =
Number(
    localStorage.getItem(
        "runnerCoins"
    )
) || 0;


/* ==================================================
   SPIEL
================================================== */

let running = false;

let score = 0;

let gameCoins = 0;


/*
   -1 = links
    0 = mitte
    1 = rechts
*/

let lane = 0;

let targetLane = 0;

let playerLane = 0;


/* ==================================================
   SPRUNG
================================================== */

let jump = 0;

let jumpVelocity = 0;

const JUMP_POWER =
0.020;

const GRAVITY =
0.000060;


/* ==================================================
   GESCHWINDIGKEIT
================================================== */

let speed =
0.00018;

const START_SPEED =
0.00018;

const MAX_SPEED =
0.00036;


/* ==================================================
   OBJEKTE
================================================== */

let obstacles = [];

let coins = [];


/* ==================================================
   TIMER
================================================== */

let obstacleTimer = 0;

let coinTimer = 0;


/* ==================================================
   PERSPEKTIVE
================================================== */

function horizon() {

    /*
       Etwas höherer Horizont,
       damit man weiter sehen kann.
    */

    return H * 0.36;
}


function roadWidth(z) {

    const far =
    W * 0.25;

    const near =
    W * 1.40;


    return (
        far +
        (near - far)
        *
        (1 - z)
    );
}


function project(z) {

    const depth =
    1 - z;


    return {

        y:
        horizon() +
        depth *
        H *
        0.64,

        width:
        roadWidth(z)
    };
}


function laneX(
    lane,
    z
) {

    const width =
    roadWidth(z);


    return (
        W / 2 +
        lane *
        width *
        0.27
    );
}


/* ==================================================
   HIMMEL
================================================== */

function drawSky() {

    const gradient =
    ctx.createLinearGradient(
        0,
        0,
        0,
        H
    );


    gradient.addColorStop(
        0,
        "#42baff"
    );


    gradient.addColorStop(
        1,
        "#d9f5ff"
    );


    ctx.fillStyle =
    gradient;


    ctx.fillRect(
        0,
        0,
        W,
        H
    );
}


/* ==================================================
   BODEN
================================================== */

function drawGround() {

    ctx.fillStyle =
    "#56a24c";


    ctx.fillRect(
        0,
        horizon(),
        W,
        H
    );
}


/* ==================================================
   STRASSE
================================================== */

function drawRoad() {

    const top =
    horizon();


    const topWidth =
    W * 0.25;

    const bottomWidth =
    W * 1.40;


    /*
       Die Straße ist exakt
       symmetrisch zur Mitte.
    */

    ctx.fillStyle =
    "#37393d";


    ctx.beginPath();


    ctx.moveTo(
        W / 2 -
        topWidth / 2,
        top
    );


    ctx.lineTo(
        W / 2 +
        topWidth / 2,
        top
    );


    ctx.lineTo(
        W / 2 +
        bottomWidth / 2,
        H
    );


    ctx.lineTo(
        W / 2 -
        bottomWidth / 2,
        H
    );


    ctx.closePath();

    ctx.fill();


    /*
       Spurmarkierungen.
    */

    drawLaneLine(-1);

    drawLaneLine(1);
}


function drawLaneLine(side) {

    const top =
    horizon();


    const topWidth =
    W * 0.25;

    const bottomWidth =
    W * 1.40;


    const topX =
    W / 2 +
    side *
    topWidth *
    0.27;


    const bottomX =
    W / 2 +
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
        H
    );


    ctx.stroke();
}


/* ==================================================
   STADT
================================================== */

function drawCity() {

    const y =
    horizon();


    const buildings = [

        [0.01, 90, 150],
        [0.13, 110, 190],
        [0.27, 70, 130],

        [0.73, 70, 130],
        [0.85, 110, 190],
        [0.98, 85, 150]

    ];


    buildings.forEach(
    function(b, i) {

        const x =
        W * b[0];


        const bw =
        b[1];


        const bh =
        b[2];


        ctx.fillStyle =
        i % 2 === 0
        ? "#66717c"
        : "#555f69";


        ctx.fillRect(
            x,
            y - bh,
            bw,
            bh
        );


        ctx.fillStyle =
        "rgba(255,220,100,.6)";


        for (
            let yy =
            y - bh + 15;

            yy <
            y - 10;

            yy += 23
        ) {

            for (
                let xx =
                x + 10;

                xx <
                x + bw - 8;

                xx += 20
            ) {

                ctx.fillRect(
                    xx,
                    yy,
                    7,
                    10
                );
            }
        }
    });
}


/* ==================================================
   SPIELER
================================================== */

function drawPlayer() {

    const x =
    W / 2 +
    playerLane *
    W *
    0.27;


    const ground =
    H * 0.82;


    const y =
    ground -
    jump *
    H *
    0.30;


    /*
       Schatten
    */

    ctx.fillStyle =
    "rgba(0,0,0,.3)";


    ctx.beginPath();


    ctx.ellipse(
        x,
        ground + 25,
        32,
        9,
        0,
        0,
        Math.PI * 2
    );


    ctx.fill();


    /*
       Beine
    */

    ctx.fillStyle =
    "#153d99";


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


    /*
       Körper
    */

    ctx.fillStyle =
    "#176cff";


    ctx.fillRect(
        x - 29,
        y - 58,
        58,
        70
    );


    /*
       Arme
    */

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


    /*
       Kopf
    */

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


    /*
       Haare
    */

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


/* ==================================================
   ROTE HINDERNISSE
================================================== */

function drawObstacle(
    obstacle
) {

    const p =
    project(
        obstacle.z
    );


    const x =
    laneX(
        obstacle.lane,
        obstacle.z
    );


    const size =
    30 +
    p.width *
    0.055;


    const bottom =
    p.y;


    const top =
    bottom -
    size;


    /*
       Schatten
    */

    ctx.fillStyle =
    "rgba(0,0,0,.25)";


    ctx.fillRect(
        x - size / 2,
        bottom - 5,
        size,
        8
    );


    /*
       Roter Block.
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
       Oberseite
    */

    ctx.fillStyle =
    "#ff5555";


    ctx.fillRect(
        x - size / 2,
        top,
        size,
        size * .12
    );


    /*
       Seite
    */

    ctx.fillStyle =
    "#a51d1d";


    ctx.fillRect(
        x + size * .34,
        top,
        size * .16,
        size
    );
}


/* ==================================================
   MÜNZE
================================================== */

function drawCoin(
    coin
) {

    const p =
    project(
        coin.z
    );


    const x =
    laneX(
        coin.lane,
        coin.z
    );


    const y =
    p.y -
    p.width * .045;


    const radius =
    Math.max(
        5,
        p.width * .018
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


/* ==================================================
   OBJEKTE ERSTELLEN
================================================== */

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
            i * .08

        });
    }
}


/* ==================================================
   STEUERUNG
================================================== */

function left() {

    if (!running)
        return;


    lane--;


    if (lane < -1)
        lane = -1;


    targetLane =
    lane;
}


function right() {

    if (!running)
        return;


    lane++;


    if (lane > 1)
        lane = 1;


    targetLane =
    lane;
}


function jumpPlayer() {

    if (!running)
        return;


    if (
        jump <= .001
    ) {

        jumpVelocity =
        JUMP_POWER;
    }
}


/* ==================================================
   TOUCH
================================================== */

let touchX = 0;
let touchY = 0;


canvas.addEventListener(
"touchstart",
function(e) {

    const t =
    e.changedTouches[0];


    touchX =
    t.clientX;


    touchY =
    t.clientY;

},
{
    passive: true
});


canvas.addEventListener(
"touchend",
function(e) {

    if (!running)
        return;


    const t =
    e.changedTouches[0];


    const dx =
    t.clientX -
    touchX;


    const dy =
    t.clientY -
    touchY;


    if (
        Math.max(
            Math.abs(dx),
            Math.abs(dy)
        ) < 35
    )
        return;


    if (
        Math.abs(dx) >
        Math.abs(dy)
    ) {

        if (dx > 0)
            right();
        else
            left();

    } else {

        if (dy < 0)
            jumpPlayer();
    }

},
{
    passive: true
});


/* ==================================================
   TASTATUR
================================================== */

window.addEventListener(
"keydown",
function(e) {

    if (
        e.key === "ArrowLeft"
    )
        left();


    if (
        e.key === "ArrowRight"
    )
        right();


    if (
        e.key === "ArrowUp" ||
        e.key === " "
    )
        jumpPlayer();

});


/* ==================================================
   HUD
================================================== */

function updateHUD() {

    document.getElementById(
        "coins"
    ).textContent =
    totalCoins +
    gameCoins;


    document.getElementById(
        "score"
    ).textContent =
    Math.floor(score);


    document.getElementById(
        "best"
    ).textContent =
    highScore;
}


/* ==================================================
   SPIEL STARTEN
================================================== */

function startGame() {

    /*
       STARTMENÜ KOMPLETT WEG
    */

    document.getElementById(
        "startScreen"
    ).classList.add(
        "hidden"
    );


    document.getElementById(
        "gameOver"
    ).classList.add(
        "hidden"
    );


    document.getElementById(
        "hud"
    ).style.display =
    "flex";


    running = true;


    score = 0;

    gameCoins = 0;


    lane = 0;

    targetLane = 0;

    playerLane = 0;


    jump = 0;

    jumpVelocity = 0;


    speed =
    START_SPEED;


    obstacles = [];

    coins = [];


    obstacleTimer = 0;

    coinTimer = 0;


    /*
       Erste Münzen.
    */

    coins.push({
        lane: 0,
        z: .84
    });


    coins.push({
        lane: 0,
        z: .76
    });


    coins.push({
        lane: 0,
        z: .68
    });


    /*
       Erstes Hindernis
       rechts.
    */

    obstacles.push({
        lane: 1,
        z: .92
    });


    updateHUD();
}


/* ==================================================
   UPDATE
================================================== */

function update(dt) {

    if (!running)
        return;


    /*
       Langsame Beschleunigung.
    */

    speed +=
    dt *
    .000000008;


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
    )
    *
    Math.min(
        1,
        dt * .012
    );


    /*
       SPRUNG
    */

    if (
        jump > 0 ||
        jumpVelocity > 0
    ) {

        jump +=
        jumpVelocity *
        dt;


        jumpVelocity -=
        GRAVITY *
        dt;


        if (
            jump <= 0
        ) {

            jump = 0;

            jumpVelocity = 0;
        }
    }


    /* =========================
       HINDERNISSE
    ========================= */

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
        dt;


        /*
           Kollision.
        */

        if (
            obstacle.z < .12 &&
            obstacle.z > .03
        ) {

            if (
                Math.abs(
                    obstacle.lane -
                    playerLane
                ) < .35
            ) {

                /*
                   Wenn nicht hoch
                   genug gesprungen:
                   Game Over.
                */

                if (
                    jump < .35
                ) {

                    gameOver();

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


    /* =========================
       MÜNZEN
    ========================= */

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
        dt;


        if (
            coin.z < .14 &&
            coin.z > .02
        ) {

            if (
                Math.abs(
                    coin.lane -
                    playerLane
                ) < .38
            ) {

                gameCoins++;


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


    /* =========================
       NEUE HINDERNISSE
    ========================= */

    obstacleTimer += dt;


    if (
        obstacleTimer > 2300
    ) {

        createObstacle();

        obstacleTimer = 0;
    }


    /* =========================
       NEUE MÜNZEN
    ========================= */

    coinTimer += dt;


    if (
        coinTimer > 1500
    ) {

        createCoins();

        coinTimer = 0;
    }


    /*
       Punkte.
    */

    score +=
    dt * .01;


    updateHUD();
}


/* ==================================================
   GAME OVER
================================================== */

function gameOver() {

    running = false;


    const finalScore =
    Math.floor(score);


    totalCoins +=
    gameCoins;


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
        String(totalCoins)
    );


    document.getElementById(
        "finalScore"
    ).textContent =
    finalScore;


    document.getElementById(
        "finalCoins"
    ).textContent =
    gameCoins;


    document.getElementById(
        "finalBest"
    ).textContent =
    highScore;


    document.getElementById(
        "hud"
    ).style.display =
    "none";


    document.getElementById(
        "gameOver"
    ).classList.remove(
        "hidden"
    );
}


/* ==================================================
   ZEICHNEN
================================================== */

function draw() {

    /*
       GANZER BILDSCHIRM
    */

    ctx.clearRect(
        0,
        0,
        W,
        H
    );


    drawSky();

    drawGround();

    drawCity();

    drawRoad();


    /*
       Objekte nach Entfernung sortieren.
    */

    const objects = [];


    obstacles.forEach(
    function(o) {

        objects.push({

            type:
            "obstacle",

            object:
            o

        });
    });


    coins.forEach(
    function(c) {

        objects.push({

            type:
            "coin",

            object:
            c

        });
    });


    objects.sort(
    function(a,b) {

        return (
            b.object.z -
            a.object.z
        );
    });


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

    });


    drawPlayer();
}


/* ==================================================
   GAME LOOP
================================================== */

let lastTime = 0;


function loop(time) {

    if (
        lastTime === 0
    ) {

        lastTime =
        time;
    }


    let dt =
    time -
    lastTime;


    /*
       Verhindert Ruckler,
       wenn die App kurz pausiert.
    */

    dt =
    Math.min(
        dt,
        40
    );


    lastTime =
    time;


    update(dt);

    draw();


    requestAnimationFrame(
        loop
    );
}


requestAnimationFrame(
    loop
);


/* ==================================================
   BUTTONS
================================================== */

document
.getElementById(
    "startButton"
)
.addEventListener(
    "click",
    startGame
);


document
.getElementById(
    "restartButton"
)
.addEventListener(
    "click",
    startGame
);


document
.getElementById(
    "menuButton"
)
.addEventListener(
    "click",
    function() {

        running = false;


        document
        .getElementById(
            "gameOver"
        )
        .classList.add(
            "hidden"
        );


        document
        .getElementById(
            "startScreen"
        )
        .classList.remove(
            "hidden"
        );


        document
        .getElementById(
            "hud"
        )
        .style.display =
        "none";
    }
);

</script>

</body>
</html>
