<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>Cube Rush Neon</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    -webkit-tap-highlight-color: transparent;
}

html, body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #02040a;
    font-family: Arial, sans-serif;
    touch-action: none;
}

canvas {
    position: fixed;
    inset: 0;
    width: 100%;
    height: 100%;
}

#hud {
    display: none;
    position: fixed;
    top: 12px;
    left: 12px;
    right: 12px;
    z-index: 10;

    justify-content: space-between;
    gap: 8px;

    pointer-events: none;
}

.box {
    padding: 9px 12px;
    border-radius: 13px;

    color: white;
    font-size: 13px;
    font-weight: bold;

    background: rgba(0, 8, 18, .75);
    border: 1px solid rgba(0, 220, 255, .35);

    box-shadow: 0 0 18px rgba(0, 210, 255, .15);
}

.screen {
    position: fixed;
    inset: 0;
    z-index: 30;

    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    text-align: center;
    color: white;

    background:
        radial-gradient(
            circle at 50% 25%,
            rgba(0, 180, 255, .20),
            transparent 40%
        ),
        #02040a;
}

.hidden {
    display: none !important;
}

.logo {
    font-size: 43px;
    font-weight: 1000;
    letter-spacing: 4px;

    text-shadow:
        0 0 10px #00eaff,
        0 0 30px #008cff,
        0 0 60px #005eff;
}

.subtitle {
    margin-top: 8px;
    color: #65ddff;
    font-size: 11px;
    letter-spacing: 4px;
}

button {
    border: none;
    border-radius: 14px;

    margin-top: 18px;
    padding: 14px 25px;

    background: #00d9ff;
    color: #001018;

    font-size: 14px;
    font-weight: 1000;

    box-shadow:
        0 0 20px rgba(0, 220, 255, .45);
}

button:active {
    transform: scale(.94);
}

#shopButton {
    display: none;

    position: fixed;
    bottom: 13px;
    right: 13px;

    z-index: 20;

    margin: 0;
    padding: 10px 13px;

    font-size: 11px;
}

#shop {
    position: fixed;
    inset: 0;

    display: none;

    z-index: 50;

    overflow-y: auto;

    padding: 22px 14px;

    color: white;

    background:
        radial-gradient(
            circle at top,
            #063b5d,
            #02040a 70%
        );
}

#shop.active {
    display: block;
}

.shopTitle {
    text-align: center;

    font-size: 34px;
    font-weight: 1000;

    text-shadow:
        0 0 20px #00eaff;
}

.shopCoins {
    text-align: center;
    margin-top: 8px;
    color: #ffe52e;
}

.grid {
    max-width: 900px;
    margin: 22px auto;

    display: grid;

    grid-template-columns:
        repeat(
            auto-fit,
            minmax(145px, 1fr)
        );

    gap: 14px;
}

.card {
    min-height: 205px;

    padding: 12px;

    border-radius: 20px;

    text-align: center;

    background: rgba(255,255,255,.055);

    border:
        1px solid
        rgba(0,220,255,.25);
}

.card.locked {
    opacity: .6;
}

.preview {
    width: 105px;
    height: 105px;
}

.name {
    font-weight: 1000;
}

.price {
    margin-top: 6px;
    color: #ffe52e;
    font-weight: bold;
}

.card button {
    padding: 8px 12px;
    margin-top: 8px;
    font-size: 10px;
}

.close {
    display: block;
    margin: 20px auto;
}
</style>
</head>

<body>

<canvas id="game"></canvas>


<!-- HUD -->

<div id="hud">

    <div class="box">
        🪙 <span id="coins">0</span>
    </div>

    <div class="box">
        🏆 <span id="score">0</span>
    </div>

    <div class="box">
        ◆ <span id="characterName">
            BLUE CORE
        </span>
    </div>

</div>


<button id="shopButton">
    ◆ WÜRFEL
</button>


<!-- STARTMENÜ -->

<div id="menu" class="screen">

    <div class="logo">
        CUBE RUSH
    </div>

    <div class="subtitle">
        NEON CITY RUN
    </div>

    <div style="
        margin-top:22px;
        color:#ffe52e;
    ">
        🪙 <span id="menuCoins">0</span>
    </div>

    <button id="start">
        SPIEL STARTEN
    </button>

    <button id="openMenuShop">
        ◆ WÜRFEL
    </button>

</div>


<!-- GAME OVER -->

<div id="gameOver"
class="screen hidden">

    <div class="logo"
    style="font-size:36px">
        CRASH
    </div>

    <p style="margin-top:20px">
        Punkte:
        <b id="finalScore">0</b>
    </p>

    <p style="margin-top:8px">
        Münzen:
        <b id="finalCoins">0</b>
    </p>

    <p style="margin-top:8px">
        Highscore:
        <b id="finalBest">0</b>
    </p>

    <button id="again">
        NOCHMAL
    </button>

    <button id="backMenu">
        MENÜ
    </button>

</div>


<!-- SHOP -->

<div id="shop">

    <div class="shopTitle">
        WÜRFEL
    </div>

    <div class="shopCoins">
        🪙 <span id="shopCoins">0</span>
    </div>

    <div
        id="characterGrid"
        class="grid">
    </div>

    <button
        id="closeShop"
        class="close">
        ZURÜCK
    </button>

</div>


<script>

/* =====================================================
   CANVAS
===================================================== */

const canvas =
    document.getElementById("game");

const ctx =
    canvas.getContext("2d");

let W = innerWidth;
let H = innerHeight;
let DPR = 1;

function resize() {

    W = innerWidth;
    H = innerHeight;

    DPR =
        Math.min(
            devicePixelRatio || 1,
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

addEventListener(
    "resize",
    resize
);

resize();


/* =====================================================
   CHARAKTERE
===================================================== */

const characters = [

{
    name:"BLUE CORE",
    price:0,
    main:"#008cff",
    light:"#00f0ff",
    dark:"#004a9e",
    aura:"#00dfff"
},

{
    name:"VOLT",
    price:300,
    main:"#ffe000",
    light:"#ffffaa",
    dark:"#9b7400",
    aura:"#ffe000"
},

{
    name:"FIRE",
    price:700,
    main:"#ff3200",
    light:"#ffb000",
    dark:"#8d1000",
    aura:"#ff2500"
},

{
    name:"POISON",
    price:1200,
    main:"#55ff00",
    light:"#d7ff9c",
    dark:"#287500",
    aura:"#55ff00"
},

{
    name:"ICE",
    price:2000,
    main:"#00d8ff",
    light:"#e8ffff",
    dark:"#00749c",
    aura:"#00eaff"
},

{
    name:"PURPLE",
    price:3000,
    main:"#9b35ff",
    light:"#e6baff",
    dark:"#481080",
    aura:"#a83aff"
},

{
    name:"MAGMA",
    price:4500,
    main:"#ff4b00",
    light:"#fff000",
    dark:"#861800",
    aura:"#ff4b00"
},

{
    name:"THUNDER",
    price:6500,
    main:"#f1e900",
    light:"#ffffff",
    dark:"#706900",
    aura:"#ffff00"
},

{
    name:"VOID",
    price:9000,
    main:"#171727",
    light:"#9d8aff",
    dark:"#050509",
    aura:"#744dff"
},

{
    name:"OMEGA",
    price:15000,
    main:"#ffffff",
    light:"#00ffff",
    dark:"#5050ff",
    aura:"#00ffff"
}

];


/* =====================================================
   SPEICHER
===================================================== */

let totalCoins =
    Number(
        localStorage.getItem(
            "cubeCoins"
        )
    ) || 0;

let highscore =
    Number(
        localStorage.getItem(
            "cubeHighscore"
        )
    ) || 0;

let unlocked =
    JSON.parse(
        localStorage.getItem(
            "cubeUnlocked"
        ) || "[0]"
    );

let selected =
    Number(
        localStorage.getItem(
            "cubeSelected"
        )
    ) || 0;

function save() {

    localStorage.setItem(
        "cubeCoins",
        totalCoins
    );

    localStorage.setItem(
        "cubeHighscore",
        highscore
    );

    localStorage.setItem(
        "cubeUnlocked",
        JSON.stringify(
            unlocked
        )
    );

    localStorage.setItem(
        "cubeSelected",
        selected
    );
}


/* =====================================================
   SPIEL
===================================================== */

let running = false;

let score = 0;

let runCoins = 0;


/*
    -1 = links
     0 = mitte
     1 = rechts

    Es existieren NUR diese 3 Spuren.
*/

let lane = 0;

let targetLane = 0;

let playerX = 0;

let objects = [];

let speed = 230;

let carTimer = 0;

let coinTimer = 0;


/* =====================================================
   NEUE STRECKE
===================================================== */

function road() {

    /*
       Die Strecke ist zentral
       und hat exakt 3 Spuren.
    */

    const width =
        Math.min(
            W * .82,
            600
        );

    const left =
        (W - width) / 2;

    return {
        left:left,
        width:width,
        right:left + width
    };
}


function laneX(number) {

    /*
       NIEMALS außerhalb
       von -1 / 0 / 1.
    */

    number =
        Math.max(
            -1,
            Math.min(
                1,
                number
            )
        );

    const r =
        road();

    const laneWidth =
        r.width / 3;

    return (
        r.left +
        laneWidth *
        (number + 1.5)
    );
}


/* =====================================================
   HINTERGRUND
===================================================== */

function drawWorld(time) {

    /*
       Himmel
    */

    const sky =
        ctx.createLinearGradient(
            0,
            0,
            0,
            H
        );

    sky.addColorStop(
        0,
        "#01030a"
    );

    sky.addColorStop(
        .45,
        "#071b2d"
    );

    sky.addColorStop(
        1,
        "#02050a"
    );

    ctx.fillStyle =
        sky;

    ctx.fillRect(
        0,
        0,
        W,
        H
    );


    /*
       futuristische Gebäude
    */

    for(
        let i=0;
        i<20;
        i++
    ) {

        const buildingWidth =
            30 +
            (i * 17) % 45;

        const buildingHeight =
            100 +
            (i * 63) % 220;

        const x =
            i * 70 -
            20;

        const y =
            H * .38 -
            buildingHeight;

        ctx.fillStyle =
            "rgba(5,15,28,.95)";

        ctx.fillRect(
            x,
            y,
            buildingWidth,
            buildingHeight
        );


        /*
           Fenster
        */

        for(
            let wy=y+15;
            wy<y+buildingHeight-10;
            wy+=25
        ) {

            ctx.fillStyle =
                "rgba(0,210,255,.30)";

            ctx.fillRect(
                x+7,
                wy,
                4,
                9
            );

            if(
                buildingWidth > 50
            ) {

                ctx.fillRect(
                    x+buildingWidth-12,
                    wy,
                    4,
                    9
                );
            }
        }
    }


    /*
       Boden
    */

    ctx.fillStyle =
        "#02070c";

    ctx.fillRect(
        0,
        H*.38,
        W,
        H*.62
    );


    const r =
        road();


    /*
       Straße
    */

    ctx.fillStyle =
        "#101820";

    ctx.fillRect(
        r.left,
        0,
        r.width,
        H
    );


    /*
       Neon-Außenränder
    */

    ctx.fillStyle =
        "#00dfff";

    ctx.shadowColor =
        "#00dfff";

    ctx.shadowBlur =
        20;

    ctx.fillRect(
        r.left,
        0,
        3,
        H
    );

    ctx.fillRect(
        r.right-3,
        0,
        3,
        H
    );

    ctx.shadowBlur = 0;


    /*
       EXAKT 3 SPUREN

       2 Linien erzeugen 3 Spuren.
    */

    const laneWidth =
        r.width / 3;


    const dash =
        48;

    const gap =
        32;


    const offset =
        (
            score * 3
        ) %
        (
            dash + gap
        );


    for(
        let y =
            -dash + offset;

        y<H;

        y+=dash+gap
    ) {

        for(
            let line=1;
            line<=2;
            line++
        ) {

            ctx.fillStyle =
                "rgba(0,210,255,.5)";

            ctx.shadowColor =
                "#00dfff";

            ctx.shadowBlur =
                8;

            ctx.fillRect(
                r.left +
                laneWidth*line -
                2,
                y,
                4,
                dash
            );

            ctx.shadowBlur=0;
        }
    }


    /*
       Seitenlichter
    */

    const lightOffset =
        (
            score * 2
        ) %
        80;


    for(
        let y=-80+lightOffset;
        y<H;
        y+=80
    ) {

        ctx.fillStyle =
            "rgba(0,220,255,.7)";

        ctx.shadowColor =
            "#00dfff";

        ctx.shadowBlur =
            12;

        ctx.fillRect(
            r.left-18,
            y,
            5,
            28
        );

        ctx.fillRect(
            r.right+13,
            y,
            5,
            28
        );

        ctx.shadowBlur=0;
    }
}


/* =====================================================
   WÜRFEL
===================================================== */

function drawCube(
    x,
    y,
    size,
    c,
    rotation=0
) {

    ctx.save();

    ctx.translate(
        x,
        y
    );

    ctx.rotate(
        rotation
    );


    /*
       Aura
    */

    const aura =
        ctx.createRadialGradient(
            0,
            0,
            3,
            0,
            0,
            size*1.8
        );

    aura.addColorStop(
        0,
        c.aura
    );

    aura.addColorStop(
        1,
        "transparent"
    );

    ctx.globalAlpha=.25;

    ctx.fillStyle =
        aura;

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        size*1.8,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.globalAlpha=1;


    /*
       Schatten
    */

    ctx.fillStyle =
        "rgba(0,0,0,.5)";

    ctx.beginPath();

    ctx.ellipse(
        0,
        size*.7,
        size*.7,
        size*.15,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();


    /*
       Hauptwürfel
    */

    ctx.shadowColor =
        c.aura;

    ctx.shadowBlur =
        25;

    ctx.fillStyle =
        c.main;

    ctx.beginPath();

    ctx.roundRect(
        -size/2,
        -size/2,
        size,
        size,
        size*.12
    );

    ctx.fill();

    ctx.shadowBlur=0;


    /*
       Rechte Seite
    */

    ctx.fillStyle =
        c.dark;

    ctx.beginPath();

    ctx.moveTo(
        size/2,
        -size/2
    );

    ctx.lineTo(
        size*.68,
        -size*.28
    );

    ctx.lineTo(
        size*.68,
        size*.45
    );

    ctx.lineTo(
        size/2,
        size/2
    );

    ctx.closePath();

    ctx.fill();


    /*
       Oberseite
    */

    ctx.fillStyle =
        c.light;

    ctx.globalAlpha=.8;

    ctx.beginPath();

    ctx.moveTo(
        -size/2,
        -size/2
    );

    ctx.lineTo(
        -size*.28,
        -size*.68
    );

    ctx.lineTo(
        size*.68,
        -size*.28
    );

    ctx.lineTo(
        size/2,
        -size/2
    );

    ctx.closePath();

    ctx.fill();

    ctx.globalAlpha=1;


    /*
       Lichtpunkt
    */

    ctx.fillStyle =
        "#ffffff";

    ctx.shadowColor =
        "#ffffff";

    ctx.shadowBlur=12;

    ctx.beginPath();

    ctx.arc(
        -size*.13,
        -size*.13,
        size*.08,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.shadowBlur=0;


    /*
       Blitz
    */

    if(
        c.name==="THUNDER" ||
        c.name==="OMEGA"
    ) {

        ctx.strokeStyle =
            c.light;

        ctx.lineWidth=2;

        ctx.shadowColor =
            c.light;

        ctx.shadowBlur=15;

        ctx.beginPath();

        ctx.moveTo(
            -size*.25,
            size*.28
        );

        ctx.lineTo(
            -size*.04,
            size*.02
        );

        ctx.lineTo(
            size*.07,
            size*.23
        );

        ctx.lineTo(
            size*.28,
            -size*.25
        );

        ctx.stroke();

        ctx.shadowBlur=0;
    }


    ctx.restore();
}


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer() {

    const c =
        characters[selected];


    playerX +=
        (
            laneX(targetLane) -
            playerX
        ) * .22;


    const y =
        H - 115;


    const rotation =
        (
            targetLane -
            lane
        ) * .15;


    drawCube(
        playerX,
        y,
        58,
        c,
        rotation
    );
}


/* =====================================================
   AUTOS
===================================================== */

function spawnCar() {

    /*
       NUR diese drei Werte
       sind erlaubt.
    */

    const lanes =
        [-1,0,1];


    /*
       Zufällige der drei Spuren.
    */

    let possible =
        lanes.filter(
            l => {

                return !objects.some(
                    o =>
                        o.type==="car" &&
                        o.lane===l &&
                        o.y<160
                );
            }
        );


    if(
        possible.length===0
    )
        return;


    const selectedLane =
        possible[
            Math.floor(
                Math.random() *
                possible.length
            )
        ];


    objects.push({

        type:"car",

        lane:selectedLane,

        y:-150,

        size:
            58 +
            Math.random()*10,

        color:
            [
                "#ff2148",
                "#009fff",
                "#ffffff",
                "#ffb000",
                "#9b3cff"
            ][
                Math.floor(
                    Math.random()*5
                )
            ]
    });
}


function drawCar(o) {

    const x =
        laneX(o.lane);

    const y =
        o.y;

    const w =
        o.size;

    const h =
        w*1.45;


    ctx.save();

    ctx.translate(
        x,
        y
    );


    /*
       Schatten
    */

    ctx.fillStyle =
        "rgba(0,0,0,.5)";

    ctx.beginPath();

    ctx.ellipse(
        0,
        h*.48,
        w*.7,
        9,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();


    /*
       Reifen
    */

    ctx.fillStyle =
        "#030506";

    ctx.fillRect(
        -w*.62,
        -h*.30,
        w*.20,
        h*.28
    );

    ctx.fillRect(
        w*.42,
        -h*.30,
        w*.20,
        h*.28
    );

    ctx.fillRect(
        -w*.62,
        h*.10,
        w*.20,
        h*.28
    );

    ctx.fillRect(
        w*.42,
        h*.10,
        w*.20,
        h*.28
    );


    /*
       Karosserie
    */

    ctx.fillStyle =
        o.color;

    ctx.shadowColor =
        o.color;

    ctx.shadowBlur=16;

    ctx.beginPath();

    ctx.roundRect(
        -w/2,
        -h/2,
        w,
        h,
        10
    );

    ctx.fill();

    ctx.shadowBlur=0;


    /*
       Fenster
    */

    ctx.fillStyle =
        "#102936";

    ctx.beginPath();

    ctx.roundRect(
        -w*.30,
        -h*.36,
        w*.60,
        h*.30,
        7
    );

    ctx.fill();


    /*
       Licht
    */

    ctx.fillStyle =
        "#ffffff";

    ctx.shadowColor =
        "#ffffff";

    ctx.shadowBlur=18;

    ctx.fillRect(
        -w*.35,
        h*.20,
        w*.18,
        h*.09
    );

    ctx.fillRect(
        w*.17,
        h*.20,
        w*.18,
        h*.09
    );

    ctx.shadowBlur=0;


    ctx.restore();
}


/* =====================================================
   COINS
===================================================== */

function spawnCoins() {

    /*
       Auch hier:
       NUR -1, 0, 1.
    */

    const lanes =
        [-1,0,1];


    const selectedLane =
        lanes[
            Math.floor(
                Math.random()*3
            )
        ];


    /*
       Eine saubere Münzlinie.
    */

    for(
        let i=0;
        i<4;
        i++
    ) {

        objects.push({

            type:"coin",

            lane:selectedLane,

            y:
                -40 -
                i*70
        });
    }
}


function drawCoin(o,time) {

    const x =
        laneX(o.lane);

    const y =
        o.y;


    const spin =
        Math.abs(
            Math.cos(
                time*.007
            )
        );


    ctx.save();

    ctx.translate(
        x,
        y
    );

    ctx.scale(
        .4+spin*.6,
        1
    );


    ctx.shadowColor =
        "#ffe000";

    ctx.shadowBlur=22;


    ctx.fillStyle =
        "#b77900";

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        18,
        0,
        Math.PI*2
    );

    ctx.fill();


    ctx.fillStyle =
        "#ffdf1b";

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        14,
        0,
        Math.PI*2
    );

    ctx.fill();


    ctx.shadowBlur=0;


    ctx.fillStyle =
        "#a06d00";

    ctx.font =
        "bold 15px Arial";

    ctx.textAlign =
        "center";

    ctx.textBaseline =
        "middle";

    ctx.fillText(
        "C",
        0,
        1
    );


    ctx.restore();
}


/* =====================================================
   UPDATE
===================================================== */

function update(dt) {

    if(!running)
        return;


    const seconds =
        dt/1000;


    /*
       Sanfte Bewegung.
    */

    lane +=
        (
            targetLane -
            lane
        ) *
        Math.min(
            1,
            seconds*14
        );


    playerX +=
        (
            laneX(targetLane) -
            playerX
        ) *
        Math.min(
            1,
            seconds*14
        );


    /*
       Geschwindigkeit langsam erhöhen.
    */

    speed +=
        seconds*4;

    speed =
        Math.min(
            speed,
            650
        );


    /*
       Objekte bewegen.
    */

    for(
        let i=
            objects.length-1;

        i>=0;

        i--
    ) {

        const o =
            objects[i];


        o.y +=
            speed*seconds;


        /*
           Hinter dem Spieler:
           sofort löschen.
        */

        if(
            o.y >
            H+130
        ) {

            objects.splice(
                i,
                1
            );

            continue;
        }


        /*
           AUTO-KOLLISION
        */

        if(
            o.type==="car"
        ) {

            const playerY =
                H-115;


            const sameLane =
                Math.abs(
                    playerX -
                    laneX(o.lane)
                ) < 30;


            const near =
                Math.abs(
                    o.y -
                    playerY
                ) < 57;


            if(
                sameLane &&
                near
            ) {

                gameOver();

                return;
            }
        }


        /*
           COIN
        */

        if(
            o.type==="coin"
        ) {

            const playerY =
                H-115;


            const sameLane =
                Math.abs(
                    playerX -
                    laneX(o.lane)
                ) < 31;


            const near =
                Math.abs(
                    o.y -
                    playerY
                ) < 45;


            if(
                sameLane &&
                near
            ) {

                runCoins++;

                objects.splice(
                    i,
                    1
                );
            }
        }
    }


    /*
       Auto-Spawning.
    */

    carTimer += dt;


    const carDelay =
        Math.max(
            600,
            1450 -
            score*1.5
        );


    if(
        carTimer >
        carDelay
    ) {

        spawnCar();

        carTimer=0;
    }


    /*
       Münzen.
    */

    coinTimer += dt;


    if(
        coinTimer >
        1250
    ) {

        spawnCoins();

        coinTimer=0;
    }


    /*
       Score.
    */

    score +=
        seconds*10;


    updateHUD();
}


/* =====================================================
   GAME OVER
===================================================== */

function gameOver() {

    running=false;


    const finalScore =
        Math.floor(score);


    totalCoins +=
        runCoins;


    if(
        finalScore >
        highscore
    ) {

        highscore =
            finalScore;
    }


    save();


    document.getElementById(
        "finalScore"
    ).textContent =
        finalScore;

    document.getElementById(
        "finalCoins"
    ).textContent =
        runCoins;

    document.getElementById(
        "finalBest"
    ).textContent =
        highscore;


    document.getElementById(
        "hud"
    ).style.display =
        "none";

    document.getElementById(
        "shopButton"
    ).style.display =
        "none";


    document.getElementById(
        "gameOver"
    ).classList.remove(
        "hidden"
    );
}


/* =====================================================
   HUD
===================================================== */

function updateHUD() {

    document.getElementById(
        "coins"
    ).textContent =
        totalCoins+
        runCoins;

    document.getElementById(
        "score"
    ).textContent =
        Math.floor(score);

    document.getElementById(
        "characterName"
    ).textContent =
        characters[
            selected
        ].name;

    document.getElementById(
        "menuCoins"
    ).textContent =
        totalCoins;

    document.getElementById(
        "shopCoins"
    ).textContent =
        totalCoins;
}


/* =====================================================
   DRAW
===================================================== */

function draw(time) {

    ctx.clearRect(
        0,
        0,
        W,
        H
    );


    drawWorld(time);


    /*
       Hintere Objekte zuerst.
    */

    const sorted =
        [...objects].sort(
            (a,b)=>
                a.y-b.y
        );


    for(
        const o of sorted
    ) {

        if(
            o.type==="car"
        ) {

            drawCar(o);

        } else {

            drawCoin(
                o,
                time
            );
        }
    }


    drawPlayer();
}


/* =====================================================
   GAME LOOP
===================================================== */

let last =
    performance.now();

function loop(time) {

    const dt =
        Math.min(
            time-last,
            40
        );

    last=time;

    update(dt);

    draw(time);

    requestAnimationFrame(
        loop
    );
}

requestAnimationFrame(
    loop
);


/* =====================================================
   TOUCH / SWIPE
===================================================== */

let startX=0;
let startY=0;


canvas.addEventListener(
    "touchstart",
    e=>{

        const t =
            e.changedTouches[0];

        startX =
            t.clientX;

        startY =
            t.clientY;

    },
    {passive:true}
);


canvas.addEventListener(
    "touchend",
    e=>{

        if(!running)
            return;


        const t =
            e.changedTouches[0];


        const dx =
            t.clientX-startX;

        const dy =
            t.clientY-startY;


        if(
            Math.abs(dx)<45
        )
            return;


        if(
            Math.abs(dx)<
            Math.abs(dy)
        )
            return;


        if(dx>0) {

            targetLane++;

        } else {

            targetLane--;
        }


        /*
           HARTE BEGRENZUNG:

           -1 links
            0 mitte
            1 rechts
        */

        targetLane =
            Math.max(
                -1,
                Math.min(
                    1,
                    targetLane
                )
            );
    },
    {passive:true}
);


/* =====================================================
   TASTATUR
===================================================== */

addEventListener(
    "keydown",
    e=>{

        if(!running)
            return;


        if(
            e.key==="ArrowLeft" ||
            e.key.toLowerCase()==="a"
        ) {

            targetLane--;
        }


        if(
            e.key==="ArrowRight" ||
            e.key.toLowerCase()==="d"
        ) {

            targetLane++;
        }


        targetLane =
            Math.max(
                -1,
                Math.min(
                    1,
                    targetLane
                )
            );
    }
);


/* =====================================================
   SHOP
===================================================== */

function drawPreview(
    p,
    c
) {

    p.clearRect(
        0,
        0,
        210,
        210
    );


    p.save();

    p.translate(
        105,
        110
    );


    p.shadowColor =
        c.aura;

    p.shadowBlur=25;


    p.fillStyle =
        c.main;

    p.fillRect(
        -42,
        -42,
        84,
        84
    );


    p.shadowBlur=0;


    p.fillStyle =
        c.dark;

    p.beginPath();

    p.moveTo(
        42,
        -42
    );

    p.lineTo(
        59,
        -23
    );

    p.lineTo(
        59,
        40
    );

    p.lineTo(
        42,
        42
    );

    p.closePath();

    p.fill();


    p.fillStyle =
        c.light;

    p.globalAlpha=.8;

    p.beginPath();

    p.moveTo(
        -42,
        -42
    );

    p.lineTo(
        -23,
        -59
    );

    p.lineTo(
        59,
        -23
    );

    p.lineTo(
        42,
        -42
    );

    p.closePath();

    p.fill();

    p.globalAlpha=1;


    p.fillStyle =
        "#fff";

    p.beginPath();

    p.arc(
        -11,
        -11,
        7,
        0,
        Math.PI*2
    );

    p.fill();


    p.restore();
}


function renderShop() {

    const grid =
        document.getElementById(
            "characterGrid"
        );


    grid.innerHTML="";


    characters.forEach(
        (c,i)=>{

            const card =
                document.createElement(
                    "div"
                );


            card.className =
                "card";


            if(
                !unlocked.includes(i)
            ) {

                card.classList.add(
                    "locked"
                );
            }


            const preview =
                document.createElement(
                    "canvas"
                );

            preview.className =
                "preview";

            preview.width=210;
            preview.height=210;


            drawPreview(
                preview.getContext("2d"),
                c
            );


            card.appendChild(
                preview
            );


            const name =
                document.createElement(
                    "div"
                );

            name.className="name";

            name.textContent =
                c.name;

            card.appendChild(
                name
            );


            const price =
                document.createElement(
                    "div"
                );

            price.className="price";

            price.textContent =
                c.price===0
                ? "STARTER"
                : "🪙 "+c.price;

            card.appendChild(
                price
            );


            const btn =
                document.createElement(
                    "button"
                );


            if(
                unlocked.includes(i)
            ) {

                btn.textContent =
                    selected===i
                    ? "✓ AUSGEWÄHLT"
                    : "AUSWÄHLEN";


                btn.onclick=()=>{

                    selected=i;

                    save();

                    renderShop();

                    updateHUD();
                };

            } else {

                btn.textContent="KAUFEN";


                btn.onclick=()=>{

                    if(
                        totalCoins<c.price
                    ) {

                        alert(
                            "Du brauchst noch "+
                            (
                                c.price-
                                totalCoins
                            )+
                            " Münzen."
                        );

                        return;
                    }


                    totalCoins -=
                        c.price;

                    unlocked.push(i);

                    selected=i;

                    save();

                    renderShop();

                    updateHUD();
                };
            }


            card.appendChild(btn);

            grid.appendChild(card);
        }
    );
}


/* =====================================================
   START
===================================================== */

function startGame() {

    document.getElementById(
        "menu"
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
    ).style.display=
        "flex";


    document.getElementById(
        "shopButton"
    ).style.display=
        "block";


    running=true;

    score=0;

    runCoins=0;

    lane=0;

    targetLane=0;

    playerX=
        laneX(0);

    objects=[];

    speed=230;

    carTimer=0;

    coinTimer=500;


    updateHUD();
}


document.getElementById(
    "start"
).onclick =
    startGame;


document.getElementById(
    "again"
).onclick =
    startGame;


document.getElementById(
    "backMenu"
).onclick =
    ()=>{

        document.getElementById(
            "gameOver"
        ).classList.add(
            "hidden"
        );

        document.getElementById(
            "menu"
        ).classList.remove(
            "hidden"
        );
    };


/* =====================================================
   SHOP BUTTONS
===================================================== */

function openShop() {

    renderShop();

    document.getElementById(
        "shop"
    ).classList.add(
        "active"
    );
}


document.getElementById(
    "openMenuShop"
).onclick =
    openShop;


document.getElementById(
    "shopButton"
).onclick =
    openShop;


document.getElementById(
    "closeShop"
).onclick =
    ()=>{

        document.getElementById(
            "shop"
        ).classList.remove(
            "active"
        );
    };


/* =====================================================
   INIT
===================================================== */

renderShop();

updateHUD();

</script>

</body>
</html>
