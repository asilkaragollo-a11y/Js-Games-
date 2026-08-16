<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">

<meta name="viewport"
      content="width=device-width,
      initial-scale=1.0,
      maximum-scale=1.0,
      user-scalable=no">

<title>City Runner 3D</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

html,
body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #111;
    font-family: Arial, sans-serif;
    touch-action: none;
}

canvas {
    display: block;
}

#hud {
    position: fixed;
    top: 14px;
    left: 14px;
    right: 14px;
    display: flex;
    justify-content: space-between;
    z-index: 10;
    pointer-events: none;
}

.hud {
    color: white;
    background: rgba(0,0,0,.55);
    padding: 10px 14px;
    border-radius: 14px;
    font-size: 17px;
    font-weight: bold;
    backdrop-filter: blur(8px);
    text-shadow: 0 2px 4px black;
}

#menu,
#gameover,
#shop {
    position: fixed;
    inset: 0;
    z-index: 20;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    color: white;
    text-align: center;

    background:
        linear-gradient(
            rgba(0,0,0,.48),
            rgba(0,0,0,.82)
        );
}

#gameover,
#shop {
    display: none;
}

h1 {
    font-size: 42px;
    margin-bottom: 12px;
}

.subtitle {
    font-size: 17px;
    opacity: .9;
    margin-bottom: 10px;
}

button {
    border: none;
    border-radius: 14px;

    padding: 14px 25px;
    margin-top: 12px;

    font-size: 17px;
    font-weight: bold;

    background: #ffd400;
    color: #111;

    cursor: pointer;
}

button:active {
    transform: scale(.94);
}

.shopGrid {
    display: flex;
    gap: 12px;
    margin: 15px;
}

.character {
    width: 85px;
    height: 100px;

    border-radius: 15px;

    background: rgba(255,255,255,.12);
    border: 2px solid rgba(255,255,255,.35);

    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    font-size: 34px;
}

.character small {
    font-size: 12px;
    margin-top: 6px;
}

.locked {
    opacity: .5;
}

#shopCoins {
    margin-bottom: 8px;
}

@media(max-width:600px) {

    h1 {
        font-size: 32px;
    }

    .hud {
        font-size: 14px;
        padding: 8px 10px;
    }

    .character {
        width: 72px;
        height: 90px;
    }
}
</style>
</head>

<body>

<div id="hud">

    <div class="hud">
        🪙 <span id="coinDisplay">0</span>
    </div>

    <div class="hud">
        🏆 <span id="scoreDisplay">0</span>
    </div>

    <div class="hud">
        ⭐ <span id="bestDisplay">0</span>
    </div>

</div>


<div id="menu">

    <h1>🏃 CITY RUNNER</h1>

    <div class="subtitle">
        Dein eigener 3D Endless Runner
    </div>

    <p>⬅️ Wischen = links</p>
    <p>➡️ Wischen = rechts</p>
    <p>⬆️ Wischen = springen</p>

    <button id="startButton">
        SPIEL STARTEN
    </button>

    <button id="shopButton">
        👤 CHARAKTERE
    </button>

</div>


<div id="shop">

    <h1>👤 Charaktere</h1>

    <div id="shopCoins">
        🪙 Münzen:
        <span id="shopCoinDisplay">0</span>
    </div>

    <div class="shopGrid">

        <div class="character">
            🧑
            <small>START</small>
        </div>

        <div
            class="character"
            id="character2">
            🥷
            <small>100 🪙</small>
        </div>

        <div
            class="character"
            id="character3">
            🤖
            <small>250 🪙</small>
        </div>

    </div>

    <button id="backButton">
        ZURÜCK
    </button>

</div>


<div id="gameover">

    <h1>💥 GAME OVER</h1>

    <p>
        Punkte:
        <strong id="finalScore">0</strong>
    </p>

    <p>
        Münzen:
        <strong id="finalCoins">0</strong>
    </p>

    <p>
        Highscore:
        <strong id="finalBest">0</strong>
    </p>

    <button id="restartButton">
        NOCHMAL
    </button>

    <button id="menuButton">
        MENÜ
    </button>

</div>


<script src="https://cdn.jsdelivr.net/npm/three@0.161.0/build/three.min.js"></script>

<script>

"use strict";

/* =========================
   SPIEL
========================= */

let scene;
let camera;
let renderer;

let player;

let running = false;

let lane = 1;

const lanes = [-3, 0, 3];

let targetLaneX = 0;

let playerY = 0;
let velocityY = 0;

const gravity = -0.032;
const jumpPower = 0.68;

let gameSpeed = 0.42;

let score = 0;
let coins = 0;

let totalCoins =
    Number(
        localStorage.getItem("cityRunnerCoins")
    ) || 0;

let highScore =
    Number(
        localStorage.getItem("cityRunnerHighScore")
    ) || 0;

let spawnTimer = 0;
let coinTimer = 0;

let worldDistance = 0;

const obstacles = [];
const coinObjects = [];
const worldObjects = [];


/* =========================
   HTML
========================= */

const coinDisplay =
    document.getElementById(
        "coinDisplay"
    );

const scoreDisplay =
    document.getElementById(
        "scoreDisplay"
    );

const bestDisplay =
    document.getElementById(
        "bestDisplay"
    );

const menu =
    document.getElementById(
        "menu"
    );

const gameover =
    document.getElementById(
        "gameover"
    );

const shop =
    document.getElementById(
        "shop"
    );


/* =========================
   START
========================= */

init();

function init() {

    scene =
        new THREE.Scene();

    scene.background =
        new THREE.Color(
            0x87ceeb
        );

    scene.fog =
        new THREE.Fog(
            0x87ceeb,
            25,
            150
        );


    camera =
        new THREE.PerspectiveCamera(
            65,
            window.innerWidth /
            window.innerHeight,
            0.1,
            500
        );

    camera.position.set(
        0,
        5.5,
        11
    );


    renderer =
        new THREE.WebGLRenderer({
            antialias: true
        });

    renderer.setSize(
        window.innerWidth,
        window.innerHeight
    );

    renderer.setPixelRatio(
        Math.min(
            window.devicePixelRatio,
            2
        )
    );

    renderer.shadowMap.enabled =
        true;

    document.body.appendChild(
        renderer.domElement
    );


    createLights();

    createPlayer();

    createInitialWorld();

    setupControls();

    window.addEventListener(
        "resize",
        resize
    );

    updateUI();

    animate();
}


/* =========================
   LICHT
========================= */

function createLights() {

    const ambient =
        new THREE.HemisphereLight(
            0xffffff,
            0x444444,
            2.2
        );

    scene.add(ambient);


    const sun =
        new THREE.DirectionalLight(
            0xffffff,
            2.5
        );

    sun.position.set(
        15,
        30,
        20
    );

    sun.castShadow = true;

    scene.add(sun);
}


/* =========================
   SPIELER
========================= */

function createPlayer() {

    player =
        new THREE.Group();


    const bodyMaterial =
        new THREE.MeshStandardMaterial({
            color: 0x1769ff,
            roughness: .6
        });


    const skinMaterial =
        new THREE.MeshStandardMaterial({
            color: 0xffc49a,
            roughness: .7
        });


    const body =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                1.05,
                1.6,
                .7
            ),
            bodyMaterial
        );

    body.position.y = 1.55;

    body.castShadow = true;

    player.add(body);


    const head =
        new THREE.Mesh(
            new THREE.SphereGeometry(
                .45,
                20,
                20
            ),
            skinMaterial
        );

    head.position.y = 2.65;

    head.castShadow = true;

    player.add(head);


    const legGeometry =
        new THREE.BoxGeometry(
            .32,
            1.05,
            .38
        );


    const leftLeg =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    leftLeg.position.set(
        -.28,
        .5,
        0
    );

    leftLeg.castShadow = true;

    player.add(leftLeg);


    const rightLeg =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    rightLeg.position.set(
        .28,
        .5,
        0
    );

    rightLeg.castShadow = true;

    player.add(rightLeg);


    player.position.set(
        0,
        0,
        5
    );

    scene.add(player);
}


/* =========================
   BODEN
========================= */

function createTrackSegment(z) {

    const road =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                11,
                .2,
                10
            ),
            new THREE.MeshStandardMaterial({
                color: 0x343434,
                roughness: .9
            })
        );

    road.position.set(
        0,
        -.15,
        z
    );

    road.receiveShadow = true;

    scene.add(road);

    worldObjects.push(road);


    /* Seiten */

    for (
        let side of [-1, 1]
    ) {

        const sidewalk =
            new THREE.Mesh(
                new THREE.BoxGeometry(
                    4,
                    .4,
                    10
                ),
                new THREE.MeshStandardMaterial({
                    color: 0x777777
                })
            );

        sidewalk.position.set(
            side * 7.5,
            -.05,
            z
        );

        scene.add(sidewalk);

        worldObjects.push(
            sidewalk
        );
    }
}


/* =========================
   GEBÄUDE
========================= */

function createBuilding(
    side,
    z
) {

    const height =
        7 +
        Math.random() * 14;

    const width =
        4 +
        Math.random() * 4;

    const depth =
        7 +
        Math.random() * 5;


    const building =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                width,
                height,
                depth
            ),
            new THREE.MeshStandardMaterial({
                color:
                    new THREE.Color(
                        .25 +
                        Math.random() * .2,
                        .25 +
                        Math.random() * .2,
                        .25 +
                        Math.random() * .2
                    ),
                roughness: .8
            })
        );


    building.position.set(
        side *
        (10 +
        Math.random() * 3),
        height / 2,
        z
    );

    building.castShadow = true;

    scene.add(building);

    worldObjects.push(
        building
    );
}


/* =========================
   WELT
========================= */

function createInitialWorld() {

    for (
        let z = 0;
        z >= -150;
        z -= 10
    ) {

        createTrackSegment(z);

        createBuilding(
            -1,
            z
        );

        createBuilding(
            1,
            z
        );
    }
}


/* =========================
   NEUES WELTSTÜCK
========================= */

function createNewWorldSegment() {

    const z =
        worldDistance - 10;

    createTrackSegment(z);

    createBuilding(
        -1,
        z
    );

    createBuilding(
        1,
        z
    );

    worldDistance = z;
}


/* =========================
   HINDERNIS
========================= */

function createObstacle() {

    const laneIndex =
        Math.floor(
            Math.random() * 3
        );

    const x =
        lanes[laneIndex];


    const height =
        .9 +
        Math.random() * .7;


    const obstacle =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                2.15,
                height,
                1.8
            ),
            new THREE.MeshStandardMaterial({
                color: 0xd62828,
                roughness: .5
            })
        );


    obstacle.position.set(
        x,
        height / 2,
        -90
    );

    obstacle.castShadow = true;

    scene.add(obstacle);

    obstacles.push(
        obstacle
    );
}


/* =========================
   MÜNZE
========================= */

function createCoin() {

    const laneIndex =
        Math.floor(
            Math.random() * 3
        );

    const coin =
        new THREE.Mesh(
            new THREE.TorusGeometry(
                .48,
                .14,
                12,
                24
            ),
            new THREE.MeshStandardMaterial({
                color: 0xffd700,
                metalness: .9,
                roughness: .15
            })
        );


    coin.position.set(
        lanes[laneIndex],
        1.5,
        -90
    );

    coin.rotation.y =
        Math.PI / 2;

    scene.add(coin);

    coinObjects.push(
        coin
    );
}


/* =========================
   SPRINGEN
========================= */

function jump() {

    if (
        playerY <= .001
    ) {

        velocityY =
            jumpPower;
    }
}


/* =========================
   SPUREN
========================= */

function moveLeft() {

    if (!running)
        return;

    lane--;

    if (lane < 0)
        lane = 0;

    targetLaneX =
        lanes[lane];
}


function moveRight() {

    if (!running)
        return;

    lane++;

    if (lane > 2)
        lane = 2;

    targetLaneX =
        lanes[lane];
}


/* =========================
   TOUCH
========================= */

let touchStartX = 0;
let touchStartY = 0;

function setupControls() {

    window.addEventListener(
        "touchstart",
        function(e) {

            const touch =
                e.changedTouches[0];

            touchStartX =
                touch.clientX;

            touchStartY =
                touch.clientY;
        },
        {
            passive: true
        }
    );


    window.addEventListener(
        "touchend",
        function(e) {

            if (!running)
                return;

            const touch =
                e.changedTouches[0];

            const dx =
                touch.clientX -
                touchStartX;

            const dy =
                touch.clientY -
                touchStartY;


            const distance =
                Math.max(
                    Math.abs(dx),
                    Math.abs(dy)
                );


            if (distance < 35)
                return;


            if (
                Math.abs(dx) >
                Math.abs(dy)
            ) {

                if (dx > 0)
                    moveRight();
                else
                    moveLeft();

            } else {

                if (dy < 0)
                    jump();
            }
        },
        {
            passive: true
        }
    );


    window.addEventListener(
        "keydown",
        function(e) {

            if (
                e.key === "ArrowLeft"
            ) {
                moveLeft();
            }

            if (
                e.key === "ArrowRight"
            ) {
                moveRight();
            }

            if (
                e.key === "ArrowUp" ||
                e.key === " "
            ) {
                jump();
            }
        }
    );
}


/* =========================
   SPIEL STARTEN
========================= */

function startGame() {

    running = true;

    score = 0;

    coins = 0;

    gameSpeed = .42;

    lane = 1;

    targetLaneX = 0;

    player.position.set(
        0,
        0,
        5
    );

    playerY = 0;

    velocityY = 0;

    spawnTimer = 0;

    coinTimer = 0;


    /* Alte Objekte entfernen */

    for (
        const obstacle
        of obstacles
    ) {

        scene.remove(
            obstacle
        );
    }

    obstacles.length = 0;


    for (
        const coin
        of coinObjects
    ) {

        scene.remove(
            coin
        );
    }

    coinObjects.length = 0;


    menu.style.display =
        "none";

    gameover.style.display =
        "none";

    shop.style.display =
        "none";
}


/* =========================
   SPIELER UPDATE
========================= */

function updatePlayer() {

    player.position.x +=
        (
            targetLaneX -
            player.position.x
        ) * .18;


    velocityY +=
        gravity;

    playerY +=
        velocityY;


    if (playerY < 0) {

        playerY = 0;

        velocityY = 0;
    }


    player.position.y =
        playerY;


    /* leichte Bewegung */

    if (running) {

        player.rotation.z =
            (
                targetLaneX -
                player.position.x
            ) * -.04;
    }
}


/* =========================
   OBJEKTE
========================= */

function updateObjects() {

    for (
        let i =
        obstacles.length - 1;
        i >= 0;
        i--
    ) {

        const obstacle =
            obstacles[i];

        obstacle.position.z +=
            gameSpeed;


        if (
            obstacle.position.z >
            18
        ) {

            scene.remove(
                obstacle
            );

            obstacles.splice(
                i,
                1
            );

            continue;
        }


        const dx =
            Math.abs(
                obstacle.position.x -
                player.position.x
            );

        const dz =
            Math.abs(
                obstacle.position.z -
                player.position.z
            );


        /* Kollision */

        if (
            dx < 1.25 &&
            dz < 1.25 &&
            playerY < 1.45
        ) {

            endGame();

            return;
        }
    }


    for (
        let i =
        coinObjects.length - 1;
        i >= 0;
        i--
    ) {

        const coin =
            coinObjects[i];

        coin.position.z +=
            gameSpeed;

        coin.rotation.z +=
            .09;


        if (
            coin.position.z >
            18
        ) {

            scene.remove(
                coin
            );

            coinObjects.splice(
                i,
                1
            );

            continue;
        }


        const dx =
            Math.abs(
                coin.position.x -
                player.position.x
            );

        const dz =
            Math.abs(
                coin.position.z -
                player.position.z
            );

        const dy =
            Math.abs(
                coin.position.y -
                (playerY + 1.4)
            );


        if (
            dx < 1.25 &&
            dz < 1.3 &&
            dy < 1.4
        ) {

            coins++;

            scene.remove(
                coin
            );

            coinObjects.splice(
                i,
                1
            );
        }
    }
}


/* =========================
   ENDLOSER WEG
========================= */

function updateWorld() {

    for (
        const object
        of worldObjects
    ) {

        object.position.z +=
            gameSpeed;
    }


    for (
        let i =
        worldObjects.length - 1;
        i >= 0;
        i--
    ) {

        const object =
            worldObjects[i];

        if (
            object.position.z >
            25
        ) {

            scene.remove(
                object
            );

            worldObjects.splice(
                i,
                1
            );
        }
    }


    /*
       Wenn hinten zu wenig Welt
       vorhanden ist, wird neue Welt
       erzeugt.
    */

    while (
        worldDistance >
        -250
    ) {

        createNewWorldSegment();
    }


    worldDistance +=
        gameSpeed;
}


/* =========================
   GAME UPDATE
========================= */

function updateGame() {

    if (!running)
        return;


    updatePlayer();

    updateObjects();

    updateWorld();


    spawnTimer += 1;

    coinTimer += 1;


    if (
        spawnTimer >
        Math.max(
            38,
            75 -
            gameSpeed * 50
        )
    ) {

        createObstacle();

        spawnTimer = 0;
    }


    if (
        coinTimer > 30
    ) {

        createCoin();

        coinTimer = 0;
    }


    /* Geschwindigkeit steigt */

    gameSpeed +=
        .00008;


    /* Punkte */

    score +=
        gameSpeed * 2;


    updateUI();
}


/* =========================
   UI
========================= */

function updateUI() {

    coinDisplay.textContent =
        totalCoins + coins;

    scoreDisplay.textContent =
        Math.floor(score);

    bestDisplay.textContent =
        highScore;
}


/* =========================
   GAME OVER
========================= */

function endGame() {

    running = false;


    const final =
        Math.floor(score);


    if (
        final >
        highScore
    ) {

        highScore =
            final;

        localStorage.setItem(
            "cityRunnerHighScore",
            String(highScore)
        );
    }


    totalCoins +=
        coins;


    localStorage.setItem(
        "cityRunnerCoins",
        String(totalCoins)
    );


    document.getElementById(
        "finalScore"
    ).textContent =
        final;


    document.getElementById(
        "finalCoins"
    ).textContent =
        coins;


    document.getElementById(
        "finalBest"
    ).textContent =
        highScore;


    gameover.style.display =
        "flex";


    updateUI();
}


/* =========================
   SHOP
========================= */

document.getElementById(
    "shopButton"
).addEventListener(
    "click",
    function() {

        menu.style.display =
            "none";

        shop.style.display =
            "flex";

        document.getElementById(
            "shopCoinDisplay"
        ).textContent =
            totalCoins;
    }
);


document.getElementById(
    "backButton"
).addEventListener(
    "click",
    function() {

        shop.style.display =
            "none";

        menu.style.display =
            "flex";
    }
);


/* =========================
   CHARAKTER FREISCHALTEN
========================= */

let character2Unlocked =
    localStorage.getItem(
        "character2"
    ) === "true";


let character3Unlocked =
    localStorage.getItem(
        "character3"
    ) === "true";


document.getElementById(
    "character2"
).addEventListener(
    "click",
    function() {

        if (
            character2Unlocked
        )
            return;


        if (
            totalCoins >= 100
        ) {

            totalCoins -= 100;

            character2Unlocked =
                true;

            localStorage.setItem(
                "character2",
                "true"
            );

            localStorage.setItem(
                "cityRunnerCoins",
                String(totalCoins)
            );

            updateShop();
        }
    }
);


document.getElementById(
    "character3"
).addEventListener(
    "click",
    function() {

        if (
            character3Unlocked
        )
            return;


        if (
            totalCoins >= 250
        ) {

            totalCoins -= 250;

            character3Unlocked =
                true;

            localStorage.setItem(
                "character3",
                "true"
            );

            localStorage.setItem(
                "cityRunnerCoins",
                String(totalCoins)
            );

            updateShop();
        }
    }
);


function updateShop() {

    document.getElementById(
        "shopCoinDisplay"
    ).textContent =
        totalCoins;


    const c2 =
        document.getElementById(
            "character2"
        );

    const c3 =
        document.getElementById(
            "character3"
        );


    if (
        character2Unlocked
    ) {

        c2.classList.remove(
            "locked"
        );

        c2.innerHTML =
            "🥷<small>FREIGESCHALTET</small>";
    }


    if (
        character3Unlocked
    ) {

        c3.classList.remove(
            "locked"
        );

        c3.innerHTML =
            "🤖<small>FREIGESCHALTET</small>";
    }
}


updateShop();


/* =========================
   BUTTONS
========================= */

document.getElementById(
    "startButton"
).addEventListener(
    "click",
    startGame
);


document.getElementById(
    "restartButton"
).addEventListener(
    "click",
    startGame
);


document.getElementById(
    "menuButton"
).addEventListener(
    "click",
    function() {

        gameover.style.display =
            "none";

        menu.style.display =
            "flex";
    }
);


/* =========================
   RESIZE
========================= */

function resize() {

    camera.aspect =
        window.innerWidth /
        window.innerHeight;

    camera.updateProjectionMatrix();


    renderer.setSize(
        window.innerWidth,
        window.innerHeight
    );
}


/* =========================
   LOOP
========================= */

function animate() {

    requestAnimationFrame(
        animate
    );


    updateGame();


    renderer.render(
        scene,
        camera
    );
}

</script>

</body>
</html>
