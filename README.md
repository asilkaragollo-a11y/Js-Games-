# Js-Games-
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width, initial-scale=1.0,
      maximum-scale=1.0, user-scalable=no">

<title>Runner 3D</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

html, body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #79c9ff;
    font-family: Arial, sans-serif;
    touch-action: none;
}

canvas {
    display: block;
    width: 100%;
    height: 100%;
}

#hud {
    position: fixed;
    top: 14px;
    left: 12px;
    right: 12px;
    z-index: 5;

    display: none;
    justify-content: space-between;

    pointer-events: none;
}

.hudBox {
    padding: 9px 13px;
    border-radius: 12px;

    color: white;
    background: rgba(0,0,0,.58);

    font-weight: bold;
    font-size: 15px;

    text-shadow: 0 2px 3px black;
}

.screen {
    position: fixed;
    inset: 0;
    z-index: 10;

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
    margin-bottom: 15px;
    text-shadow: 0 4px 10px black;
}

.info {
    line-height: 1.7;
}

button {
    margin-top: 22px;

    border: none;
    border-radius: 15px;

    padding: 15px 30px;

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

<canvas id="game"></canvas>

<!-- HUD -->

<div id="hud">

    <div class="hudBox">
        🪙 <span id="coins">0</span>
    </div>

    <div class="hudBox">
        🏆 <span id="score">0</span>
    </div>

    <div class="hudBox">
        ⭐ <span id="highscore">0</span>
    </div>

</div>


<!-- START -->

<div id="menu" class="screen">

    <h1>🏃 RUNNER 3D</h1>

    <div class="info">
        ⬅️ Nach links wischen<br>
        ➡️ Nach rechts wischen<br>
        ⬆️ Nach oben wischen = Springen
    </div>

    <button id="start">
        SPIEL STARTEN
    </button>

</div>


<!-- GAME OVER -->

<div id="gameover" class="screen hidden">

    <h1>💥 GAME OVER</h1>

    <p>
        Punkte:
        <b id="endScore">0</b>
    </p>

    <p>
        Münzen:
        <b id="endCoins">0</b>
    </p>

    <p>
        Highscore:
        <b id="endHighscore">0</b>
    </p>

    <button id="restart">
        NOCHMAL SPIELEN
    </button>

    <button id="menuButton">
        MENÜ
    </button>

</div>


<script>

/* =====================================================
   GRUNDSETUP
===================================================== */

const canvas =
    document.getElementById("game");

const ctx =
    canvas.getContext("2d");

let W;
let H;
let DPR;


/* =====================================================
   SPEICHER
===================================================== */

let highscore =
    Number(
        localStorage.getItem("runnerHighscore")
    ) || 0;

let savedCoins =
    Number(
        localStorage.getItem("runnerCoins")
    ) || 0;


/* =====================================================
   SPIELVARIABLEN
===================================================== */

let running = false;

let score = 0;
let currentCoins = 0;


/*
   -1 = links
    0 = mitte
    1 = rechts
*/

let lane = 0;

let playerLane = 0;
let targetLane = 0;


/* Springen */

let playerJump = 0;
let jumpVelocity = 0;

const JUMP_FORCE = 0.018;
const GRAVITY = 0.000055;


/* Geschwindigkeit */

let speed = 0.00022;

const START_SPEED = 0.00022;
const MAX_SPEED = 0.00048;


/* Objekte */

let obstacles = [];
let coinObjects = [];


/* Zeit */

let lastTime = 0;
let obstacleTimer = 0;
let coinTimer = 0;


/* =====================================================
   ELEMENTE
===================================================== */

const menu =
    document.getElementById("menu");

const gameover =
    document.getElementById("gameover");

const hud =
    document.getElementById("hud");


/* =====================================================
   CANVAS
===================================================== */

function resize() {

    DPR =
        Math.min(
            window.devicePixelRatio || 1,
            2
        );

    W =
        window.innerWidth;

    H =
        window.innerHeight;

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


/* =====================================================
   PERSPEKTIVE
===================================================== */

/*
   Der Horizont bleibt immer an derselben Stelle.
   Die Straße bewegt sich NICHT als Ganzes.

   z = 1  -> weit entfernt
   z = 0  -> beim Spieler
*/

const horizon =
    0.42;

function roadHorizon() {
    return H * horizon;
}


/*
   Position eines Objektes
*/

function project(z) {

    /*
       z wird zwischen 0 und 1 gehalten.
    */

    const depth =
        1 - z;

    const y =
        roadHorizon() +
        depth *
        (H * 0.50);

    /*
       Je näher das Objekt kommt,
       desto größer wird es.
    */

    const scale =
        0.12 +
        depth * 1.15;

    return {
        y: y,
        scale: scale
    };
}


/* =====================================================
   HIMMEL
===================================================== */

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
        "#4db8ff"
    );

    gradient.addColorStop(
        0.45,
        "#a9e4ff"
    );

    gradient.addColorStop(
        1,
        "#e9f8ff"
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


/* =====================================================
   STADT
===================================================== */

function drawCity() {

    const hy =
        roadHorizon();

    const buildings = [

        [0.01, 75, 140],
        [0.10, 100, 180],
        [0.23, 70, 120],

        [0.73, 80, 140],
        [0.84, 105, 185],
        [0.96, 75, 130]

    ];


    buildings.forEach(
        function(building, index) {

            const x =
                W * building[0];

            const width =
                building[1];

            const height =
                building[2];


            ctx.fillStyle =
                index % 2 === 0
                ? "#69737e"
                : "#56616c";


            ctx.fillRect(
                x,
                hy - height,
                width,
                height
            );


            /* Fenster */

            ctx.fillStyle =
                "rgba(255,220,100,.65)";


            for (
                let y =
                    hy - height + 15;
                y <
                    hy - 10;
                y += 20
            ) {

                for (
                    let xx =
                        x + 10;
                    xx <
                        x + width - 8;
                    xx += 18
                ) {

                    ctx.fillRect(
                        xx,
                        y,
                        6,
                        9
                    );
                }
            }

        }
    );
}


/* =====================================================
   STRASSE
===================================================== */

function drawRoad() {

    const hy =
        roadHorizon();

    /*
       Hintergrund / Gras
    */

    ctx.fillStyle =
        "#4d9a48";

    ctx.fillRect(
        0,
        hy,
        W,
        H - hy
    );


    /*
       Straße
    */

    ctx.fillStyle =
        "#303238";

    ctx.beginPath();

    ctx.moveTo(
        W * 0.43,
        hy
    );

    ctx.lineTo(
        W * 0.57,
        hy
    );

    ctx.lineTo(
        W * 0.98,
        H
    );

    ctx.lineTo(
        W * 0.02,
        H
    );

    ctx.closePath();

    ctx.fill();


    /*
       Straßenränder
    */

    ctx.strokeStyle =
        "#eeeeee";

    ctx.lineWidth = 5;

    ctx.beginPath();

    ctx.moveTo(
        W * 0.43,
        hy
    );

    ctx.lineTo(
        W * 0.02,
        H
    );

    ctx.moveTo(
        W * 0.57,
        hy
    );

    ctx.lineTo(
        W * 0.98,
        H
    );

    ctx.stroke();


    /*
       Spur links
    */

    drawLaneLine(
        0.43,
        0.33
    );


    /*
       Spur rechts
    */

    drawLaneLine(
        0.57,
        0.67
    );
}


function drawLaneLine(
    topPercent,
    bottomPercent
) {

    const hy =
        roadHorizon();


    ctx.strokeStyle =
        "rgba(255,255,255,.65)";

    ctx.lineWidth = 3;

    ctx.beginPath();

    ctx.moveTo(
        W * topPercent,
        hy
    );

    ctx.lineTo(
        W * bottomPercent,
        H
    );

    ctx.stroke();
}


/* =====================================================
   SPUR X POSITION
===================================================== */

function laneX(
    laneValue,
    z
) {

    /*
       Bei z=1:
       alle Spuren liegen dicht zusammen.

       Bei z=0:
       Spuren sind weit auseinander.
    */

    const perspective =
        1 - z;

    return (
        W / 2 +
        laneValue *
        W *
        0.22 *
        perspective
    );
}


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer() {

    /*
       Spieler bleibt unten.
    */

    const x =
        W / 2 +
        playerLane *
        W *
        0.22;


    const groundY =
        H * 0.80;


    const y =
        groundY -
        playerJump *
        H *
        0.30;


    /*
       Schatten
    */

    ctx.fillStyle =
        "rgba(0,0,0,.32)";

    ctx.beginPath();

    ctx.ellipse(
        x,
        groundY + 30,
        32,
        10,
        0,
        0,
        Math.PI * 2
    );

    ctx.fill();


    /*
       Beine
    */

    ctx.fillStyle =
        "#123b9b";

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
        "#1670ff";

    ctx.fillRect(
        x - 29,
        y - 58,
        58,
        70
    );


    /*
       Arme
    */

    ctx.fillStyle =
        "#1260e0";

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


/* =====================================================
   BLOCK
===================================================== */

function drawObstacle(
    object
) {

    const p =
        project(
            object.z
        );


    const x =
        laneX(
            object.lane,
            object.z
        );


    /*
       Block wird mit zunehmender Nähe größer.
    */

    const size =
        35 +
        65 *
        p.scale;


    const bottom =
        p.y;


    const top =
        bottom -
        size;


    if (
        object.z < 0 ||
        object.z > 1
    )
        return;


    /*
       Schatten
    */

    ctx.fillStyle =
        "rgba(0,0,0,.25)";

    ctx.fillRect(
        x - size * .55,
        bottom - size * .10,
        size * 1.1,
        size * .12
    );


    /*
       Hauptseite
    */

    ctx.fillStyle =
        "#d52b2b";

    ctx.fillRect(
        x - size / 2,
        top,
        size,
        size
    );


    /*
       obere Seite
    */

    ctx.fillStyle =
        "#ff5b5b";

    ctx.beginPath();

    ctx.moveTo(
        x - size / 2,
        top
    );

    ctx.lineTo(
        x - size * .32,
        top - size * .16
    );

    ctx.lineTo(
        x + size * .68,
        top - size * .16
    );

    ctx.lineTo(
        x + size / 2,
        top
    );

    ctx.closePath();

    ctx.fill();


    /*
       rechte Seite
    */

    ctx.fillStyle =
        "#a91f1f";

    ctx.beginPath();

    ctx.moveTo(
        x + size / 2,
        top
    );

    ctx.lineTo(
        x + size * .68,
        top - size * .16
    );

    ctx.lineTo(
        x + size * .68,
        bottom - size * .16
    );

    ctx.lineTo(
        x + size / 2,
        bottom
    );

    ctx.closePath();

    ctx.fill();
}


/* =====================================================
   MÜNZE
===================================================== */

function drawCoin(
    object
) {

    const p =
        project(
            object.z
        );


    const x =
        laneX(
            object.lane,
            object.z
        );


    /*
       Münze schwebt etwas über der Straße.
    */

    const y =
        p.y -
        55 *
        p.scale;


    const radius =
        Math.max(
            5,
            8 *
            p.scale
        );


    if (
        object.z < 0 ||
        object.z > 1
    )
        return;


    /*
       Goldener Rand
    */

    ctx.fillStyle =
        "#e5a900";

    ctx.beginPath();

    ctx.arc(
        x,
        y,
        radius + 2,
        0,
        Math.PI * 2
    );

    ctx.fill();


    /*
       Münze
    */

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


    /*
       Glanz
    */

    ctx.fillStyle =
        "#fff3a0";

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


/* =====================================================
   OBJEKTE ERZEUGEN
===================================================== */

function createObstacle() {

    /*
       Block kommt immer weit vorne.
    */

    const randomLane =
        Math.floor(
            Math.random() * 3
        ) - 1;


    obstacles.push({
        lane: randomLane,
        z: 1
    });
}


function createCoinLine() {

    const randomLane =
        Math.floor(
            Math.random() * 3
        ) - 1;


    /*
       5 Münzen hintereinander
    */

    for (
        let i = 0;
        i < 5;
        i++
    ) {

        coinObjects.push({
            lane: randomLane,
            z:
                1 -
                i * 0.075
        });
    }
}


/* =====================================================
   STEUERUNG
===================================================== */

function moveLeft() {

    if (!running)
        return;


    lane--;

    if (
        lane < -1
    )
        lane = -1;


    targetLane =
        lane;
}


function moveRight() {

    if (!running)
        return;


    lane++;

    if (
        lane > 1
    )
        lane = 1;


    targetLane =
        lane;
}


function doJump() {

    if (!running)
        return;


    if (
        playerJump <= 0.001
    ) {

        jumpVelocity =
            JUMP_FORCE;
    }
}


/* =====================================================
   TOUCH STEUERUNG
===================================================== */

let touchX = 0;
let touchY = 0;


canvas.addEventListener(
    "touchstart",
    function(event) {

        const touch =
            event.changedTouches[0];

        touchX =
            touch.clientX;

        touchY =
            touch.clientY;

    },
    {
        passive: true
    }
);


canvas.addEventListener(
    "touchend",
    function(event) {

        if (!running)
            return;


        const touch =
            event.changedTouches[0];


        const dx =
            touch.clientX -
            touchX;


        const dy =
            touch.clientY -
            touchY;


        const distance =
            Math.max(
                Math.abs(dx),
                Math.abs(dy)
            );


        if (
            distance < 35
        )
            return;


        if (
            Math.abs(dx) >
            Math.abs(dy)
        ) {

            if (
                dx > 0
            ) {

                moveRight();

            } else {

                moveLeft();
            }

        } else {

            if (
                dy < 0
            ) {

                doJump();
            }
        }

    },
    {
        passive: true
    }
);


/* =====================================================
   TASTATUR
===================================================== */

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

            doJump();
        }
    }
);


/* =====================================================
   SPIEL START
===================================================== */

function startGame() {

    running = true;


    score = 0;
    currentCoins = 0;


    speed =
        START_SPEED;


    lane = 0;

    playerLane = 0;
    targetLane = 0;


    playerJump = 0;
    jumpVelocity = 0;


    obstacles = [];
    coinObjects = [];


    obstacleTimer = 0;
    coinTimer = 0;


    /*
       Erste Münzen sichtbar platzieren.
    */

    coinObjects.push({
        lane: -1,
        z: 0.82
    });

    coinObjects.push({
        lane: -1,
        z: 0.74
    });

    coinObjects.push({
        lane: -1,
        z: 0.66
    });


    /*
       Erstes Hindernis befindet sich
       auf der rechten Spur.
    */

    obstacles.push({
        lane: 1,
        z: 0.88
    });


    menu.classList.add(
        "hidden"
    );

    gameover.classList.add(
        "hidden"
    );

    hud.style.display =
        "flex";


    updateHUD();
}


/* =====================================================
   UPDATE
===================================================== */

function update(dt) {

    if (!running)
        return;


    /*
       Langsame Beschleunigung
    */

    speed +=
        dt *
        0.000000012;


    if (
        speed >
        MAX_SPEED
    ) {

        speed =
            MAX_SPEED;
    }


    /*
       Spieler gleitet sauber
       zur nächsten Spur.
    */

    playerLane +=
        (
            targetLane -
            playerLane
        ) *
        Math.min(
            1,
            dt * 0.012
        );


    /*
       SPRUNG
    */

    if (
        playerJump > 0 ||
        jumpVelocity > 0
    ) {

        playerJump +=
            jumpVelocity *
            dt;


        jumpVelocity -=
            GRAVITY *
            dt;


        if (
            playerJump <= 0
        ) {

            playerJump = 0;
            jumpVelocity = 0;
        }
    }


    /*
       HINDERNISSE
    */

    for (
        let i =
            obstacles.length - 1;
        i >= 0;
        i--
    ) {

        const object =
            obstacles[i];


        object.z -=
            speed *
            dt;


        /*
           Kollisionszone direkt
           vor dem Spieler.
        */

        if (
            object.z < 0.105 &&
            object.z > 0.035
        ) {

            /*
               Nur gleiche Spur
               kann kollidieren.
            */

            if (
                Math.abs(
                    object.lane -
                    playerLane
                ) < 0.35
            ) {

                /*
                   Springen verhindert
                   die Kollision.
                */

                if (
                    playerJump < 0.35
                ) {

                    endGame();
                    return;
                }
            }
        }


        /*
           Objekt ist vorbei.
        */

        if (
            object.z < 0
        ) {

            obstacles.splice(
                i,
                1
            );
        }
    }


    /*
       MÜNZEN
    */

    for (
        let i =
            coinObjects.length - 1;
        i >= 0;
        i--
    ) {

        const object =
            coinObjects[i];


        object.z -=
            speed *
            dt;


        /*
           Münze wird eingesammelt,
           wenn sie beim Spieler ist
           und die Spur stimmt.
        */

        if (
            object.z < 0.13 &&
            object.z > 0.025
        ) {

            if (
                Math.abs(
                    object.lane -
                    playerLane
                ) < 0.38
            ) {

                currentCoins++;

                coinObjects.splice(
                    i,
                    1
                );

                continue;
            }
        }


        if (
            object.z < 0
        ) {

            coinObjects.splice(
                i,
                1
            );
        }
    }


    /*
       NEUE HINDERNISSE
    */

    obstacleTimer +=
        dt;


    /*
       Genug Abstand zwischen
       den Blöcken.
    */

    if (
        obstacleTimer > 1900
    ) {

        createObstacle();

        obstacleTimer = 0;
    }


    /*
       NEUE MÜNZEN
    */

    coinTimer +=
        dt;


    if (
        coinTimer > 1200
    ) {

        createCoinLine();

        coinTimer = 0;
    }


    /*
       SCORE
    */

    score +=
        dt * 0.012;


    updateHUD();
}


/* =====================================================
   HUD
===================================================== */

function updateHUD() {

    document.getElementById(
        "coins"
    ).textContent =
        savedCoins +
        currentCoins;


    document.getElementById(
        "score"
    ).textContent =
        Math.floor(score);


    document.getElementById(
        "highscore"
    ).textContent =
        highscore;
}


/* =====================================================
   GAME OVER
===================================================== */

function endGame() {

    running = false;


    const finalScore =
        Math.floor(score);


    savedCoins +=
        currentCoins;


    /*
       Highscore speichern
    */

    if (
        finalScore >
        highscore
    ) {

        highscore =
            finalScore;

        localStorage.setItem(
            "runnerHighscore",
            highscore
        );
    }


    /*
       Münzen speichern
    */

    localStorage.setItem(
        "runnerCoins",
        savedCoins
    );


    document.getElementById(
        "endScore"
    ).textContent =
        finalScore;


    document.getElementById(
        "endCoins"
    ).textContent =
        currentCoins;


    document.getElementById(
        "endHighscore"
    ).textContent =
        highscore;


    hud.style.display =
        "none";


    gameover.classList.remove(
        "hidden"
    );
}


/* =====================================================
   ZEICHNEN
===================================================== */

function draw() {

    drawSky();

    drawCity();

    drawRoad();


    /*
       Weit entfernte Objekte zuerst.
       Nahe Objekte zuletzt.
    */

    const objects = [];


    obstacles.forEach(
        function(object) {

            objects.push({
                type: "obstacle",
                object: object
            });
        }
    );


    coinObjects.forEach(
        function(object) {

            objects.push({
                type: "coin",
                object: object
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


/* =====================================================
   GAME LOOP
===================================================== */

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
       Falls das Handy kurz
       hängen bleibt, verhindert
       das einen riesigen Sprung.
    */

    if (
        dt > 40
    ) {

        dt = 40;
    }


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


/* =====================================================
   BUTTONS
===================================================== */

document
    .getElementById("start")
    .addEventListener(
        "click",
        startGame
    );


document
    .getElementById("restart")
    .addEventListener(
        "click",
        startGame
    );


document
    .getElementById("menuButton")
    .addEventListener(
        "click",
        function() {

            gameover.classList.add(
                "hidden"
            );

            menu.classList.remove(
                "hidden"
            );

            hud.style.display =
                "none";
        }
    );

</script>

</body>
</html>
