# Js-Games-
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>Endless Runner 3D</title>

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
    background: #111;
    font-family: Arial, sans-serif;
    touch-action: none;
}

#game {
    position: fixed;
    inset: 0;
}

#hud {
    position: fixed;
    top: 15px;
    left: 15px;
    right: 15px;
    z-index: 10;
    display: flex;
    justify-content: space-between;
    color: white;
    font-size: 18px;
    font-weight: bold;
    text-shadow: 0 2px 4px black;
    pointer-events: none;
}

.hudBox {
    background: rgba(0,0,0,.45);
    padding: 9px 13px;
    border-radius: 12px;
    backdrop-filter: blur(5px);
}

#menu,
#gameOver {
    position: fixed;
    inset: 0;
    z-index: 20;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    color: white;
    background:
        linear-gradient(
            rgba(0,0,0,.55),
            rgba(0,0,0,.75)
        );
}

#gameOver {
    display: none;
}

h1 {
    font-size: 42px;
    margin-bottom: 15px;
    text-align: center;
}

p {
    margin: 7px;
    text-align: center;
}

button {
    border: none;
    margin-top: 20px;
    padding: 15px 30px;
    border-radius: 14px;
    background: #ffcc00;
    color: #111;
    font-size: 18px;
    font-weight: bold;
    cursor: pointer;
}

button:active {
    transform: scale(.95);
}

#characters {
    margin-top: 20px;
    display: flex;
    gap: 10px;
}

.character {
    width: 75px;
    height: 75px;
    border-radius: 15px;
    border: 2px solid rgba(255,255,255,.4);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 35px;
    background: rgba(255,255,255,.15);
}

.locked {
    opacity: .45;
}
</style>
</head>

<body>

<div id="game"></div>

<div id="hud">
    <div class="hudBox">
        🪙 <span id="coins">0</span>
    </div>

    <div class="hudBox">
        🏆 <span id="score">0</span>
    </div>

    <div class="hudBox">
        👤 <span id="characterName">Runner</span>
    </div>
</div>

<div id="menu">

    <h1>ENDLESS RUNNER 3D</h1>

    <p>Wische ⬅️ ➡️ zum Ausweichen</p>
    <p>Wische ⬆️ zum Springen</p>
    <p>PC: ← → und Leertaste</p>

    <div id="characters">

        <div class="character" id="char1">
            🧑
        </div>

        <div class="character locked" id="char2">
            🔒
        </div>

        <div class="character locked" id="char3">
            🔒
        </div>

    </div>

    <button id="startButton">
        SPIEL STARTEN
    </button>

</div>

<div id="gameOver">

    <h1>GAME OVER</h1>

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
        <strong id="highScore">0</strong>
    </p>

    <button id="restartButton">
        NOCHMAL SPIELEN
    </button>

</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.161.0/build/three.min.js"></script>

<script>

let scene;
let camera;
let renderer;

let player;

let obstacles = [];
let coinsObjects = [];

let running = false;

let lane = 0;

const lanes = [-3, 0, 3];

let targetX = 0;

let playerY = 0;
let velocityY = 0;

const gravity = -0.025;
const jumpPower = 0.55;

let speed = 0.25;

let score = 0;
let collectedCoins = 0;

let highScore =
    Number(localStorage.getItem("runnerHighScore")) || 0;

let savedCoins =
    Number(localStorage.getItem("runnerCoins")) || 0;

let spawnTimer = 0;
let coinTimer = 0;

let clock;

const coinsText =
    document.getElementById("coins");

const scoreText =
    document.getElementById("score");

const menu =
    document.getElementById("menu");

const gameOver =
    document.getElementById("gameOver");

const startButton =
    document.getElementById("startButton");

const restartButton =
    document.getElementById("restartButton");

const finalScore =
    document.getElementById("finalScore");

const finalCoins =
    document.getElementById("finalCoins");

const highScoreText =
    document.getElementById("highScore");

init();

function init() {

    scene = new THREE.Scene();

    scene.background =
        new THREE.Color(0x87ceeb);

    scene.fog =
        new THREE.Fog(0x87ceeb, 30, 180);

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
        6,
        12
    );

    camera.rotation.x =
        -0.15;

    renderer =
        new THREE.WebGLRenderer({
            antialias: true
        });

    renderer.setSize(
        window.innerWidth,
        window.innerHeight
    );

    renderer.setPixelRatio(
        Math.min(window.devicePixelRatio, 2)
    );

    renderer.shadowMap.enabled = true;

    document
        .getElementById("game")
        .appendChild(renderer.domElement);

    clock = new THREE.Clock();

    createLights();

    createWorld();

    createPlayer();

    window.addEventListener(
        "resize",
        resize
    );

    setupControls();

    animate();
}

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

function createWorld() {

    const groundGeometry =
        new THREE.PlaneGeometry(
            40,
            500
        );

    const groundMaterial =
        new THREE.MeshStandardMaterial({
            color: 0x303030,
            roughness: .8
        });

    const ground =
        new THREE.Mesh(
            groundGeometry,
            groundMaterial
        );

    ground.rotation.x =
        -Math.PI / 2;

    ground.position.z =
        -180;

    ground.receiveShadow = true;

    scene.add(ground);

    createTrack();

    createBuildings();
}

function createTrack() {

    for (let i = 0; i < 70; i++) {

        const z =
            -i * 8;

        for (let x of lanes) {

            const geometry =
                new THREE.BoxGeometry(
                    2.7,
                    .15,
                    7
                );

            const material =
                new THREE.MeshStandardMaterial({
                    color: 0x555555
                });

            const block =
                new THREE.Mesh(
                    geometry,
                    material
                );

            block.position.set(
                x,
                -.1,
                z
            );

            scene.add(block);
        }
    }
}

function createBuildings() {

    for (let i = 0; i < 80; i++) {

        const height =
            5 + Math.random() * 20;

        const width =
            4 + Math.random() * 7;

        const depth =
            4 + Math.random() * 7;

        const material =
            new THREE.MeshStandardMaterial({
                color:
                    new THREE.Color(
                        Math.random() * .35 +
                        .25,
                        Math.random() * .35 +
                        .25,
                        Math.random() * .35 +
                        .25
                    )
            });

        const building =
            new THREE.Mesh(
                new THREE.BoxGeometry(
                    width,
                    height,
                    depth
                ),
                material
            );

        const side =
            Math.random() > .5
                ? 1
                : -1;

        building.position.set(
            side *
            (10 + Math.random() * 9),
            height / 2,
            -i * 12
        );

        scene.add(building);
    }
}

function createPlayer() {

    const group =
        new THREE.Group();

    const bodyMaterial =
        new THREE.MeshStandardMaterial({
            color: 0x2266ff,
            roughness: .6
        });

    const skinMaterial =
        new THREE.MeshStandardMaterial({
            color: 0xffc49a
        });

    const body =
        new THREE.Mesh(
            new THREE.BoxGeometry(
                1.1,
                1.7,
                .7
            ),
            bodyMaterial
        );

    body.position.y =
        1.6;

    body.castShadow = true;

    group.add(body);

    const head =
        new THREE.Mesh(
            new THREE.SphereGeometry(
                .48,
                20,
                20
            ),
            skinMaterial
        );

    head.position.y =
        2.75;

    head.castShadow = true;

    group.add(head);

    const legGeometry =
        new THREE.BoxGeometry(
            .35,
            1.1,
            .4
        );

    const leftLeg =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    leftLeg.position.set(
        -.3,
        .55,
        0
    );

    group.add(leftLeg);

    const rightLeg =
        new THREE.Mesh(
            legGeometry,
            bodyMaterial
        );

    rightLeg.position.set(
        .3,
        .55,
        0
    );

    group.add(rightLeg);

    group.position.set(
        0,
        0,
        5
    );

    scene.add(group);

    player = group;
}

function createObstacle() {

    const laneIndex =
        Math.floor(
            Math.random() * 3
        );

    const x =
        lanes[laneIndex];

    const height =
        .8 + Math.random() * 1.3;

    const geometry =
        new THREE.BoxGeometry(
            2.2,
            height,
            2
        );

    const material =
        new THREE.MeshStandardMaterial({
            color: 0xcc2222,
            roughness: .5
        });

    const obstacle =
        new THREE.Mesh(
            geometry,
            material
        );

    obstacle.position.set(
        x,
        height / 2,
        -100
    );

    obstacle.castShadow = true;

    scene.add(obstacle);

    obstacles.push(obstacle);
}

function createCoin() {

    const laneIndex =
        Math.floor(
            Math.random() * 3
        );

    const coin =
        new THREE.Mesh(
            new THREE.TorusGeometry(
                .55,
                .15,
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
        lanes[laneIndex],
        .9,
        -100
    );

    coin.rotation.x =
        Math.PI / 2;

    scene.add(coin);

    coinsObjects.push(coin);
}

function jump() {

    if (
        playerY <= .01
    ) {

        velocityY =
            jumpPower;
    }
}

function moveLeft() {

    lane--;

    if (lane < 0)
        lane = 0;

    targetX =
        lanes[lane];
}

function moveRight() {

    lane++;

    if (lane > 2)
        lane = 2;

    targetX =
        lanes[lane];
}

function setupControls() {

    let startX = 0;
    let startY = 0;

    window.addEventListener(
        "touchstart",
        e => {

            const touch =
                e.changedTouches[0];

            startX =
                touch.clientX;

            startY =
                touch.clientY;
        },
        { passive: true }
    );

    window.addEventListener(
        "touchend",
        e => {

            if (!running)
                return;

            const touch =
                e.changedTouches[0];

            const dx =
                touch.clientX -
                startX;

            const dy =
                touch.clientY -
                startY;

            const minSwipe =
                40;

            if (
                Math.abs(dx) >
                Math.abs(dy)
            ) {

                if (dx > minSwipe)
                    moveRight();

                if (dx < -minSwipe)
                    moveLeft();

            } else {

                if (dy < -minSwipe)
                    jump();
            }
        },
        { passive: true }
    );

    window.addEventListener(
        "keydown",
        e => {

            if (e.key === "ArrowLeft")
                moveLeft();

            if (e.key === "ArrowRight")
                moveRight();

            if (
                e.key === " " ||
                e.key === "ArrowUp"
            )
                jump();
        }
    );
}

function startGame() {

    running = true;

    score = 0;

    collectedCoins = 0;

    lane = 1;

    targetX = 0;

    player.position.x = 0;

    player.position.y = 0;

    playerY = 0;

    velocityY = 0;

    speed = .25;

    spawnTimer = 0;

    coinTimer = 0;

    obstacles.forEach(
        o => scene.remove(o)
    );

    coinsObjects.forEach(
        c => scene.remove(c)
    );

    obstacles = [];

    coinsObjects = [];

    menu.style.display =
        "none";

    gameOver.style.display =
        "none";
}

function endGame() {

    running = false;

    const currentScore =
        Math.floor(score);

    if (
        currentScore >
        highScore
    ) {

        highScore =
            currentScore;

        localStorage.setItem(
            "runnerHighScore",
            highScore
        );
    }

    savedCoins +=
        collectedCoins;

    localStorage.setItem(
        "runnerCoins",
        savedCoins
    );

    finalScore.textContent =
        currentScore;

    finalCoins.textContent =
        collectedCoins;

    highScoreText.textContent =
        highScore;

    gameOver.style.display =
        "flex";
}

function updatePlayer() {

    player.position.x +=
        (targetX -
        player.position.x) *
        .2;

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

    player.rotation.z =
        (targetX -
        player.position.x) *
        -.05;
}

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
            speed;

        if (
            obstacle.position.z >
            15
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

        const dy =
            playerY;

        if (
            dx < 1.35 &&
            dz < 1.4 &&
            dy < 1.4
        ) {

            endGame();

            return;
        }
    }

    for (
        let i =
        coinsObjects.length - 1;
        i >= 0;
        i--
    ) {

        const coin =
            coinsObjects[i];

        coin.position.z +=
            speed;

        coin.rotation.y +=
            .08;

        if (
            coin.position.z >
            15
        ) {

            scene.remove(coin);

            coinsObjects.splice(
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
            dx < 1.3 &&
            dz < 1.5 &&
            dy < 1.5
        ) {

            collectedCoins++;

            scene.remove(coin);

            coinsObjects.splice(
                i,
                1
            );
        }
    }
}

function updateGame() {

    if (!running)
        return;

    updatePlayer();

    updateObjects();

    spawnTimer++;

    coinTimer++;

    if (
        spawnTimer >
        Math.max(
            35,
            75 -
            speed * 80
        )
    ) {

        createObstacle();

        spawnTimer = 0;
    }

    if (
        coinTimer >
        35
    ) {

        createCoin();

        coinTimer = 0;
    }

    speed +=
        .00008;

    score +=
        speed;

    scoreText.textContent =
        Math.floor(score);

    coinsText.textContent =
        savedCoins +
        collectedCoins;
}

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

startButton.addEventListener(
    "click",
    startGame
);

restartButton.addEventListener(
    "click",
    startGame
);

</script>

</body>
</html>
