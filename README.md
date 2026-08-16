# Js-Games-
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>City Runner 3D</title>

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
    font-family: Arial, sans-serif;
    background: #87ceeb;
    touch-action: none;
}

canvas {
    display: block;
}

#menu,
#gameover {
    position: fixed;
    inset: 0;
    z-index: 100;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: white;
    text-align: center;
    background: rgba(0,0,0,0.65);
}

#gameover {
    display: none;
}

h1 {
    font-size: 42px;
    margin-bottom: 15px;
}

p {
    margin: 5px;
}

button {
    margin-top: 18px;
    padding: 15px 30px;
    border: none;
    border-radius: 14px;
    background: #ffd400;
    color: #111;
    font-size: 18px;
    font-weight: bold;
}

button:active {
    transform: scale(.94);
}

#hud {
    position: fixed;
    top: 15px;
    left: 15px;
    right: 15px;
    z-index: 50;

    display: none;
    justify-content: space-between;

    color: white;
    font-size: 17px;
    font-weight: bold;
}

.box {
    padding: 9px 13px;
    border-radius: 12px;
    background: rgba(0,0,0,.55);
}

#loading {
    margin-top: 15px;
    color: #ffd400;
}
</style>
</head>

<body>

<div id="hud">
    <div class="box">🪙 <span id="coins">0</span></div>
    <div class="box">🏆 <span id="score">0</span></div>
    <div class="box">⭐ <span id="highscore">0</span></div>
</div>

<div id="menu">

    <h1>🏃 CITY RUNNER</h1>

    <p>⬅️ Nach links wischen</p>
    <p>➡️ Nach rechts wischen</p>
    <p>⬆️ Nach oben wischen = Springen</p>

    <button id="startButton">
        SPIEL STARTEN
    </button>

    <div id="loading"></div>

</div>

<div id="gameover">

    <h1>💥 GAME OVER</h1>

    <p>Punkte: <b id="finalScore">0</b></p>
    <p>Münzen: <b id="finalCoins">0</b></p>
    <p>Highscore: <b id="finalHighscore">0</b></p>

    <button id="restartButton">
        NOCHMAL SPIELEN
    </button>

    <button id="menuButton">
        MENÜ
    </button>

</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.161.0/build/three.min.js"></script>

<script>

let scene;
let camera;
let renderer;

let player;

let running = false;

let lane = 1;

const lanes = [-3, 0, 3];

let targetX = 0;

let playerY = 0;
let velocityY = 0;

const gravity = -0.035;
const jumpPower = 0.7;

let speed = 0.45;

let score = 0;
let coins = 0;

let highscore =
    Number(localStorage.getItem("runnerHighscore")) || 0;

let totalCoins =
    Number(localStorage.getItem("runnerCoins")) || 0;

let obstacles = [];
let coinObjects = [];
let roadPieces = [];

let spawnTimer = 0;
let coinTimer = 0;
let roadDistance = 0;


/* =========================
   ELEMENTE
========================= */

const menu =
    document.getElementById("menu");

const gameover =
    document.getElementById("gameover");

const hud =
    document.getElementById("hud");

const startButton =
    document.getElementById("startButton");

const restartButton =
    document.getElementById("restartButton");

const menuButton =
    document.getElementById("menuButton");

const loading =
    document.getElementById("loading");


/* =========================
   START BUTTON
========================= */

startButton.addEventListener("click", function() {

    startGame();

});


restartButton.addEventListener("click", function() {

    startGame();

});


menuButton.addEventListener("click", function() {

    gameover.style.display = "none";
    menu.style.display = "flex";
    hud.style.display = "none";

});


/* =========================
   THREE.JS STARTEN
========================= */

if (typeof THREE === "undefined") {

    loading.textContent =
        "3D konnte nicht geladen werden.";

} else {

    createGame();

}


/* =========================
   SPIEL ERSTELLEN
========================= */

function createGame() {

    scene = new THREE.Scene();

    scene.background =
        new THREE.Color(0x87ceeb);

    scene.fog =
        new THREE.Fog(
            0x87ceeb,
            30,
            160
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
        5,
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

    renderer.shadowMap.enabled = true;

    document.body.appendChild(
        renderer.domElement
    );


    createLights();

    createPlayer();

    createRoad();

    setupControls();

    window.addEventListener(
        "resize",
        resize
    );

    updateHUD();

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
            2
        );

    scene.add(ambient);


    const sun =
        new THREE.DirectionalLight(
            0xffffff,
            2.5
        );

    sun.position.set(
        20,
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
            color: 0x1769ff
        });


    const skinMaterial =
        new THREE.MeshStandardMaterial({
            color: 0xffc49a
        });


    const body =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                1.1,
                1.6,
                .7
            ),
            bodyMaterial
        );

    body.position.y = 1.5;

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

    head.position.y = 2.6;

    head.castShadow = true;

    player.add(head);


    const legGeometry =
        new THREE.BoxGeometry(
            .32,
            1,
            .4
        );


    const leg1 =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    leg1.position.set(
        -.3,
        .5,
        0
    );

    player.add(leg1);


    const leg2 =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    leg2.position.set(
        .3,
        .5,
        0
    );

    player.add(leg2);


    player.position.set(
        0,
        0,
        5
    );

    scene.add(player);

}


/* =========================
   STRASSE
========================= */

function createRoad() {

    for (
        let i = 0;
        i < 40;
        i++
    ) {

        createRoadPiece(
            -i * 8
        );

    }

}


/* =========================
   STRASSENTEIL
========================= */

function createRoadPiece(z) {

    const road =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                11,
                .2,
                8
            ),
            new THREE.MeshStandardMaterial({
                color: 0x333333
            })
        );

    road.position.set(
        0,
        -.1,
        z
    );

    road.receiveShadow = true;

    scene.add(road);

    roadPieces.push(road);

}


/* =========================
   HINDERNIS
========================= */

function createObstacle() {

    const randomLane =
        Math.floor(
            Math.random() * 3
        );

    const height =
        1 +
        Math.random() * .7;


    const obstacle =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                2.2,
                height,
                1.8
            ),
            new THREE.MeshStandardMaterial({
                color: 0xd62828
            })
        );


    obstacle.position.set(
        lanes[randomLane],
        height / 2,
        -90
    );

    obstacle.castShadow = true;

    scene.add(obstacle);

    obstacles.push(obstacle);

}


/* =========================
   MÜNZE
========================= */

function createCoin() {

    const randomLane =
        Math.floor(
            Math.random() * 3
        );


    const coin =
        new THREE.Mesh(
            new THREE.TorusGeometry(
                .5,
                .14,
                12,
                24
            ),
            new THREE.MeshStandardMaterial({
                color: 0xffd700,
                metalness: .8,
                roughness: .2
            })
        );


    coin.position.set(
        lanes[randomLane],
        1.5,
        -90
    );

    scene.add(coin);

    coinObjects.push(coin);

}


/* =========================
   START
========================= */

function startGame() {

    if (
        typeof THREE === "undefined"
    ) {

        loading.textContent =
            "Die 3D-Bibliothek konnte nicht geladen werden.";

        return;
    }


    running = true;

    score = 0;
    coins = 0;

    speed = .45;

    lane = 1;
    targetX = 0;

    playerY = 0;
    velocityY = 0;

    player.position.set(
        0,
        0,
        5
    );


    /* Alte Hindernisse entfernen */

    obstacles.forEach(
        function(object) {
            scene.remove(object);
        }
    );

    obstacles = [];


    /* Alte Münzen entfernen */

    coinObjects.forEach(
        function(object) {
            scene.remove(object);
        }
    );

    coinObjects = [];


    spawnTimer = 0;
    coinTimer = 0;


    menu.style.display =
        "none";

    gameover.style.display =
        "none";

    hud.style.display =
        "flex";


    updateHUD();

}


/* =========================
   SPIELER
========================= */

function updatePlayer() {

    player.position.x +=
        (
            targetX -
            player.position.x
        ) * .18;


    velocityY += gravity;

    playerY += velocityY;


    if (playerY < 0) {

        playerY = 0;
        velocityY = 0;

    }


    player.position.y =
        playerY;

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

        const object =
            obstacles[i];


        object.position.z +=
            speed;


        if (
            object.position.z > 15
        ) {

            scene.remove(object);

            obstacles.splice(i, 1);

            continue;

        }


        const dx =
            Math.abs(
                object.position.x -
                player.position.x
            );


        const dz =
            Math.abs(
                object.position.z -
                player.position.z
            );


        if (
            dx < 1.25 &&
            dz < 1.3 &&
            playerY < 1.35
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
            speed;

        coin.rotation.y += .1;


        if (
            coin.position.z > 15
        ) {

            scene.remove(coin);

            coinObjects.splice(i, 1);

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


        if (
            dx < 1.3 &&
            dz < 1.4
        ) {

            coins++;

            scene.remove(coin);

            coinObjects.splice(i, 1);

        }

    }

}


/* =========================
   ENDLOSE STRASSE
========================= */

function updateRoad() {

    roadPieces.forEach(
        function(piece) {

            piece.position.z +=
                speed;

        }
    );


    for (
        let i =
            roadPieces.length - 1;
        i >= 0;
        i--
    ) {

        const piece =
            roadPieces[i];


        if (
            piece.position.z > 15
        ) {

            piece.position.z -=
                40 * 8;

        }

    }

}


/* =========================
   SPIEL
========================= */

function updateGame() {

    if (!running)
        return;


    updatePlayer();

    updateObjects();

    updateRoad();


    spawnTimer++;

    coinTimer++;


    if (
        spawnTimer > 65
    ) {

        createObstacle();

        spawnTimer = 0;

    }


    if (
        coinTimer > 32
    ) {

        createCoin();

        coinTimer = 0;

    }


    speed += .00008;

    score += speed;


    updateHUD();

}


/* =========================
   HUD
========================= */

function updateHUD() {

    document.getElementById(
        "coins"
    ).textContent =
        totalCoins + coins;


    document.getElementById(
        "score"
    ).textContent =
        Math.floor(score);


    document.getElementById(
        "highscore"
    ).textContent =
        highscore;

}


/* =========================
   GAME OVER
========================= */

function endGame() {

    running = false;


    const finalScore =
        Math.floor(score);


    totalCoins += coins;


    if (
        finalScore > highscore
    ) {

        highscore =
            finalScore;

        localStorage.setItem(
            "runnerHighscore",
            highscore
        );

    }


    localStorage.setItem(
        "runnerCoins",
        totalCoins
    );


    document.getElementById(
        "finalScore"
    ).textContent =
        finalScore;


    document.getElementById(
        "finalCoins"
    ).textContent =
        coins;


    document.getElementById(
        "finalHighscore"
    ).textContent =
        highscore;


    gameover.style.display =
        "flex";


    hud.style.display =
        "none";

}


/* =========================
   STEUERUNG
========================= */

let startX = 0;
let startY = 0;


function setupControls() {

    window.addEventListener(
        "touchstart",
        function(event) {

            const touch =
                event.changedTouches[0];

            startX =
                touch.clientX;

            startY =
                touch.clientY;

        },
        {
            passive: true
        }
    );


    window.addEventListener(
        "touchend",
        function(event) {

            if (!running)
                return;


            const touch =
                event.changedTouches[0];


            const dx =
                touch.clientX -
                startX;


            const dy =
                touch.clientY -
                startY;


            if (
                Math.max(
                    Math.abs(dx),
                    Math.abs(dy)
                ) < 40
            )
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
        function(event) {

            if (
                event.key === "ArrowLeft"
            )
                moveLeft();


            if (
                event.key === "ArrowRight"
            )
                moveRight();


            if (
                event.key === "ArrowUp" ||
                event.key === " "
            )
                jump();

        }
    );

}


function moveLeft() {

    if (!running)
        return;


    lane--;

    if (lane < 0)
        lane = 0;


    targetX =
        lanes[lane];

}


function moveRight() {

    if (!running)
        return;


    lane++;

    if (lane > 2)
        lane = 2;


    targetX =
        lanes[lane];

}


function jump() {

    if (!running)
        return;


    if (playerY <= 0.01) {

        velocityY =
            jumpPower;

    }

}


/* =========================
   RESIZE
========================= */

function resize() {

    if (!camera || !renderer)
        return;


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
