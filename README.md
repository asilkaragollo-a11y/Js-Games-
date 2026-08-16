<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

<style>
*{
    box-sizing:border-box;
    margin:0;
    padding:0;
    -webkit-tap-highlight-color:transparent;
}

html,body{
    width:100%;
    height:100%;
    overflow:hidden;
    background:#02040a;
    font-family:Arial,sans-serif;
    touch-action:none;
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
    z-index:20;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    text-align:center;
    color:white;
    background:
    radial-gradient(circle at 50% 25%,rgba(0,220,255,.20),transparent 40%),
    #02040a;
}

.hidden{
    display:none!important;
}

.logo{
    font-size:45px;
    font-weight:1000;
    letter-spacing:4px;
    color:white;
    text-shadow:
    0 0 10px #00eaff,
    0 0 30px #008cff,
    0 0 60px #005eff;
}

.subtitle{
    color:#66eaff;
    letter-spacing:5px;
    font-size:11px;
    margin-top:7px;
}

button{
    border:0;
    border-radius:14px;
    padding:14px 24px;
    margin-top:14px;
    background:#00d9ff;
    color:#001018;
    font-weight:1000;
    font-size:13px;
    box-shadow:0 0 25px rgba(0,220,255,.45);
}

button:active{
    transform:scale(.94);
}

#hud{
    position:fixed;
    z-index:10;
    top:12px;
    left:12px;
    right:12px;
    display:none;
    justify-content:space-between;
    gap:7px;
    pointer-events:none;
}

.hud{
    color:white;
    background:rgba(0,8,18,.85);
    border:1px solid rgba(0,220,255,.4);
    border-radius:12px;
    padding:8px 11px;
    font-size:12px;
    font-weight:bold;
}

#pauseBtn,
#shopBtn{
    display:none;
    position:fixed;
    z-index:15;
    margin:0;
}

#pauseBtn{
    top:55px;
    right:12px;
    background:rgba(0,12,22,.9);
    color:white;
    border:1px solid #00dfff;
    padding:8px 11px;
    font-size:10px;
}

#shopBtn{
    bottom:12px;
    right:12px;
    padding:10px 13px;
    font-size:10px;
}

#shop{
    position:fixed;
    inset:0;
    z-index:50;
    display:none;
    overflow:auto;
    padding:20px 12px;
    background:
    radial-gradient(circle at top,#063c5c,#02040a 70%);
    color:white;
}

#shop.active{
    display:block;
}

.shopTitle{
    text-align:center;
    font-size:34px;
    font-weight:1000;
    text-shadow:0 0 20px #00eaff;
}

.shopCoins{
    text-align:center;
    margin-top:8px;
    color:#ffe000;
    font-weight:bold;
}

.grid{
    max-width:900px;
    margin:20px auto;
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(145px,1fr));
    gap:12px;
}

.card{
    min-height:205px;
    padding:10px;
    text-align:center;
    border-radius:18px;
    background:rgba(255,255,255,.055);
    border:1px solid rgba(0,220,255,.25);
}

.card canvas{
    position:static;
    width:100px;
    height:100px;
}

.cardName{
    font-weight:1000;
    margin-top:3px;
}

.price{
    color:#ffe000;
    margin-top:5px;
    font-weight:bold;
}

.card button{
    padding:8px 11px;
    font-size:10px;
}

.close{
    display:block;
    margin:10px auto 25px;
}

.adminInfo{
    margin-top:20px;
    color:#778899;
    font-size:10px;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">

    <div class="hud">
        🪙 <span id="coins">0</span>
    </div>

    <div class="hud">
        🏆 <span id="score">0</span>
    </div>

    <div class="hud">
        ◆ <span id="characterName">BLUE</span>
    </div>

</div>

<button id="pauseBtn">⏸ PAUSE</button>
<button id="shopBtn">◆ CHARAKTERE</button>


<!-- HAUPTMENÜ -->

<div id="menu" class="screen">

    <div class="logo">
        CUBE RUSH
    </div>

    <div class="subtitle">
        NEON CITY
    </div>

    <div style="margin-top:22px;color:#ffe000;font-weight:bold">
        🪙 <span id="menuCoins">0</span>
    </div>

    <button id="startBtn">
        SPIEL STARTEN
    </button>

    <button id="menuShopBtn">
        ◆ CHARAKTERE
    </button>

    <button id="adminBtn">
        🔐 ADMIN CODE
    </button>

    <div class="adminInfo">
        Admin-Code-Funktion
    </div>

</div>


<!-- GAME OVER -->

<div id="gameOver" class="screen hidden">

    <div class="logo" style="font-size:36px">
        CRASH
    </div>

    <p style="margin-top:20px">
        Punkte: <b id="finalScore">0</b>
    </p>

    <p style="margin-top:8px">
        Münzen: <b id="finalCoins">0</b>
    </p>

    <p style="margin-top:8px">
        Highscore: <b id="finalHighscore">0</b>
    </p>

    <button id="againBtn">
        NOCHMAL
    </button>

    <button id="backBtn">
        MENÜ
    </button>

</div>


<!-- PAUSE -->

<div id="pauseMenu" class="screen hidden">

    <div class="logo" style="font-size:36px">
        PAUSE
    </div>

    <button id="resumeBtn">
        ▶ WEITERSPIELEN
    </button>

    <button id="quitBtn">
        ✕ AUFGEBEN
    </button>

</div>


<!-- SHOP -->

<div id="shop">

    <div class="shopTitle">
        CHARAKTERE
    </div>

    <div class="shopCoins">
        🪙 <span id="shopCoins">0</span>
    </div>

    <div id="characterGrid" class="grid"></div>

    <button id="closeShop" class="close">
        ZURÜCK
    </button>

</div>


<script>

/* =========================
   CANVAS
========================= */

const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");

let W=innerWidth;
let H=innerHeight;
let DPR=1;

function resize(){

    W=innerWidth;
    H=innerHeight;

    DPR=Math.min(
        devicePixelRatio||1,
        2
    );

    canvas.width=W*DPR;
    canvas.height=H*DPR;

    canvas.style.width=W+"px";
    canvas.style.height=H+"px";

    ctx.setTransform(
        DPR,0,0,DPR,0,0
    );
}

addEventListener("resize",resize);
resize();


/* =========================
   CHARAKTERE
========================= */

const characters=[

    {
        name:"BLUE",
        price:0,
        main:"#008cff",
        light:"#00f5ff",
        dark:"#003d88",
        aura:"#00eaff"
    },

    {
        name:"VOLT",
        price:300,
        main:"#ffe000",
        light:"#ffffff",
        dark:"#806d00",
        aura:"#ffe000"
    },

    {
        name:"FIRE",
        price:700,
        main:"#ff3300",
        light:"#ffb000",
        dark:"#801000",
        aura:"#ff2500"
    },

    {
        name:"POISON",
        price:1200,
        main:"#54ff00",
        light:"#d8ff9e",
        dark:"#287000",
        aura:"#55ff00"
    },

    {
        name:"ICE",
        price:2000,
        main:"#00dfff",
        light:"#ffffff",
        dark:"#006c8a",
        aura:"#00eaff"
    },

    {
        name:"PURPLE",
        price:3000,
        main:"#9b35ff",
        light:"#e7c2ff",
        dark:"#431080",
        aura:"#a52fff"
    },

    {
        name:"MAGMA",
        price:4500,
        main:"#ff4b00",
        light:"#fff000",
        dark:"#801700",
        aura:"#ff4000"
    },

    {
        name:"THUNDER",
        price:6500,
        main:"#e9df00",
        light:"#ffffff",
        dark:"#686300",
        aura:"#ffff00"
    },

    {
        name:"VOID",
        price:9000,
        main:"#171725",
        light:"#a88cff",
        dark:"#050509",
        aura:"#744cff"
    },

    {
        name:"OMEGA",
        price:15000,
        main:"#ffffff",
        light:"#00ffff",
        dark:"#4b4bff",
        aura:"#00ffff"
    }

];


/* =========================
   SPEICHER
========================= */

let totalCoins=0;
let highscore=0;
let unlocked=[0];
let selected=0;

function loadData(){

    totalCoins=
        Number(
            localStorage.getItem(
                "cubeRushCoins"
            )
        )||0;

    highscore=
        Number(
            localStorage.getItem(
                "cubeRushHighscore"
            )
        )||0;

    try{

        const u=
            JSON.parse(
                localStorage.getItem(
                    "cubeRushUnlocked"
                )
            );

        if(Array.isArray(u)){
            unlocked=u;
        }

    }catch(e){}

    const s=
        Number(
            localStorage.getItem(
                "cubeRushSelected"
            )
        );

    if(
        Number.isInteger(s)&&
        s>=0&&
        s<characters.length
    ){
        selected=s;
    }

    if(!unlocked.includes(0)){
        unlocked.push(0);
    }
}

function saveData(){

    localStorage.setItem(
        "cubeRushCoins",
        totalCoins
    );

    localStorage.setItem(
        "cubeRushHighscore",
        highscore
    );

    localStorage.setItem(
        "cubeRushUnlocked",
        JSON.stringify(unlocked)
    );

    localStorage.setItem(
        "cubeRushSelected",
        selected
    );
}

loadData();

addEventListener(
    "beforeunload",
    saveData
);

document.addEventListener(
    "visibilitychange",
    ()=>{
        if(
            document.visibilityState===
            "hidden"
        ){
            saveData();
        }
    }
);


/* =========================
   SPIELVARIABLEN
========================= */

let running=false;
let paused=false;

let score=0;
let runCoins=0;

let currentLane=0;
let targetLane=0;

let playerX=0;

let objects=[];

let speed=240;

let carTimer=0;
let coinTimer=700;


/* =========================
   STRASSE
========================= */

function road(){

    const width=
        Math.min(
            W*.82,
            600
        );

    const left=
        (W-width)/2;

    return{
        left,
        width,
        right:left+width
    };
}

function laneX(lane){

    const r=road();

    const laneWidth=
        r.width/3;

    return(
        r.left+
        laneWidth*
        (lane+1.5)
    );
}


/* =========================
   WELT
========================= */

function drawWorld(time){

    const bg=
        ctx.createLinearGradient(
            0,0,0,H
        );

    bg.addColorStop(
        0,
        "#01030a"
    );

    bg.addColorStop(
        .5,
        "#071c2d"
    );

    bg.addColorStop(
        1,
        "#02060b"
    );

    ctx.fillStyle=bg;
    ctx.fillRect(0,0,W,H);

    /* Gebäude */

    for(
        let i=0;
        i<24;
        i++
    ){

        const width=
            30+
            (i*17)%50;

        const height=
            110+
            (i*61)%230;

        const x=
            i*70-20;

        const y=
            H*.38-height;

        ctx.fillStyle=
            "#040d19";

        ctx.fillRect(
            x,
            y,
            width,
            height
        );

        for(
            let wy=y+18;
            wy<y+height-10;
            wy+=27
        ){

            ctx.fillStyle=
                "rgba(0,220,255,.28)";

            ctx.fillRect(
                x+7,
                wy,
                4,
                9
            );
        }
    }

    /* Boden */

    ctx.fillStyle="#02070c";

    ctx.fillRect(
        0,
        H*.38,
        W,
        H*.62
    );

    const r=road();

    ctx.fillStyle="#111a22";

    ctx.fillRect(
        r.left,
        0,
        r.width,
        H
    );

    /* Rand */

    ctx.fillStyle="#00eaff";

    ctx.shadowColor="#00eaff";
    ctx.shadowBlur=18;

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

    ctx.shadowBlur=0;

    /* Drei Spuren */

    const laneWidth=
        r.width/3;

    const dash=55;
    const gap=38;

    const offset=
        (score*3)%
        (dash+gap);

    for(
        let y=-dash+offset;
        y<H;
        y+=dash+gap
    ){

        for(
            let line=1;
            line<=2;
            line++
        ){

            ctx.fillStyle=
                "rgba(0,220,255,.45)";

            ctx.fillRect(
                r.left+
                laneWidth*line-2,
                y,
                4,
                dash
            );
        }
    }
}


/* =========================
   WÜRFEL
========================= */

function drawCube(
    x,
    y,
    size,
    c,
    rotation=0
){

    ctx.save();

    ctx.translate(x,y);
    ctx.rotate(rotation);

    /* Aura */

    const aura=
        ctx.createRadialGradient(
            0,0,3,
            0,0,size*1.8
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

    ctx.fillStyle=aura;

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

    /* Schatten */

    ctx.fillStyle=
        "rgba(0,0,0,.5)";

    ctx.beginPath();

    ctx.ellipse(
        0,
        size*.72,
        size*.7,
        size*.14,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();

    /* Körper */

    ctx.fillStyle=c.main;

    ctx.shadowColor=c.aura;
    ctx.shadowBlur=25;

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

    /* Seite */

    ctx.fillStyle=c.dark;

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

    /* Oberseite */

    ctx.fillStyle=c.light;

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

    /* Lichtpunkt */

    ctx.fillStyle="#fff";

    ctx.shadowColor="#fff";
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

    ctx.restore();
}


/* =========================
   SPIELER
========================= */

function drawPlayer(){

    playerX+=
        (
            laneX(targetLane)-
            playerX
        )*.20;

    drawCube(
        playerX,
        H*.72,
        58,
        characters[selected],
        (targetLane-currentLane)*.12
    );
}


/* =========================
   AUTO ERZEUGEN
========================= */

function spawnCar(){

    const lanes=[
        -1,
        0,
        1
    ];

    const free=
        lanes.filter(
            l=>
                !objects.some(
                    o=>
                        o.type==="car"&&
                        o.lane===l&&
                        o.y<180
                )
        );

    if(!free.length)return;

    const l=
        free[
            Math.floor(
                Math.random()*free.length
            )
        ];

    objects.push({

        type:"car",
        lane:l,
        y:-160,
        size:
            58+
            Math.random()*10,

        color:[
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


/* =========================
   AUTO ZEICHNEN
========================= */

function drawCar(o){

    const x=laneX(o.lane);
    const y=o.y;

    const w=o.size;
    const h=w*1.45;

    ctx.save();

    ctx.translate(x,y);

    ctx.fillStyle=
        "rgba(0,0,0,.55)";

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

    ctx.fillStyle=o.color;

    ctx.shadowColor=o.color;
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

    /* Fenster */

    ctx.fillStyle="#102936";

    ctx.beginPath();

    ctx.roundRect(
        -w*.30,
        -h*.36,
        w*.60,
        h*.30,
        7
    );

    ctx.fill();

    /* Lichter */

    ctx.fillStyle="#fff";

    ctx.shadowColor="#fff";
    ctx.shadowBlur=15;

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


/* =========================
   MÜNZEN
========================= */

function spawnCoins(){

    const lanes=[
        -1,
        0,
        1
    ];

    const free=
        lanes.filter(
            l=>
                !objects.some(
                    o=>
                        o.type==="car"&&
                        o.lane===l&&
                        o.y>-180&&
                        o.y<100
                )
        );

    if(!free.length)return;

    const lane=
        free[
            Math.floor(
                Math.random()*free.length
            )
        ];

    /*
      MINIMUM: 3
      NORMAL: 4–5
      MAXIMUM: 6
    */

    const amount=
        Math.floor(
            Math.random()*4
        )+3;

    for(
        let i=0;
        i<amount;
        i++
    ){

        const y=
            -60-
            i*48;

        /* niemals in ein Auto */

        const carThere=
            objects.some(
                o=>
                    o.type==="car"&&
                    o.lane===lane&&
                    Math.abs(
                        o.y-y
                    )<65
            );

        if(!carThere){

            objects.push({

                type:"coin",
                lane:lane,
                y:y

            });
        }
    }
}


/* =========================
   MÜNZE ZEICHNEN
========================= */

function drawCoin(
    o,
    time
){

    const x=
        laneX(o.lane);

    const y=o.y;

    const spin=
        Math.abs(
            Math.cos(
                time*.007
            )
        );

    ctx.save();

    ctx.translate(x,y);

    ctx.scale(
        .4+
        spin*.6,
        1
    );

    ctx.shadowColor="#ffe000";
    ctx.shadowBlur=22;

    ctx.fillStyle="#b77900";

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        18,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.fillStyle="#ffdf1b";

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

    ctx.fillStyle="#a06d00";

    ctx.font=
        "bold 15px Arial";

    ctx.textAlign="center";
    ctx.textBaseline="middle";

    ctx.fillText(
        "C",
        0,
        1
    );

    ctx.restore();
}


/* =========================
   UPDATE
========================= */

function update(dt){

    if(!running||paused)return;

    const sec=dt/1000;

    currentLane+=
        (
            targetLane-
            currentLane
        )*
        Math.min(
            1,
            sec*14
        );

    playerX+=
        (
            laneX(targetLane)-
            playerX
        )*
        Math.min(
            1,
            sec*14
        );

    /* Spiel wird langsam schneller */

    speed+=sec*4;

    speed=
        Math.min(
            speed,
            650
        );

    for(
        let i=objects.length-1;
        i>=0;
        i--
    ){

        const o=objects[i];

        o.y+=
            speed*sec;

        /*
          Alles was hinter dem Spieler
          ist, verschwindet sofort.
        */

        if(
            o.y>
            H*.72+100
        ){

            objects.splice(i,1);
            continue;
        }

        const playerY=
            H*.72;

        /* AUTO */

        if(
            o.type==="car"
        ){

            const sameLane=
                Math.abs(
                    playerX-
                    laneX(o.lane)
                )<30;

            const close=
                Math.abs(
                    o.y-
                    playerY
                )<58;

            if(
                sameLane&&
                close
            ){

                gameOver();
                return;
            }
        }

        /* MÜNZE */

        if(
            o.type==="coin"
        ){

            const sameLane=
                Math.abs(
                    playerX-
                    laneX(o.lane)
                )<32;

            const close=
                Math.abs(
                    o.y-
                    playerY
                )<45;

            if(
                sameLane&&
                close
            ){

                runCoins++;

                objects.splice(
                    i,
                    1
                );
            }
        }
    }

    carTimer+=dt;

    const carDelay=
        Math.max(
            600,
            1450-
            score*1.5
        );

    if(
        carTimer>
        carDelay
    ){

        spawnCar();

        carTimer=0;
    }

    coinTimer+=dt;

    if(
        coinTimer>
        1750
    ){

        spawnCoins();

        coinTimer=0;
    }

    score+=sec*10;

    updateHUD();
}


/* =========================
   GAME OVER
========================= */

function gameOver(){

    running=false;
    paused=false;

    const finalScore=
        Math.floor(score);

    totalCoins+=runCoins;

    if(
        finalScore>
        highscore
    ){
        highscore=
            finalScore;
    }

    saveData();

    document.getElementById(
        "finalScore"
    ).textContent=
        finalScore;

    document.getElementById(
        "finalCoins"
    ).textContent=
        runCoins;

    document.getElementById(
        "finalHighscore"
    ).textContent=
        highscore;

    document.getElementById(
        "hud"
    ).style.display="none";

    document.getElementById(
        "pauseBtn"
    ).style.display="none";

    document.getElementById(
        "shopBtn"
    ).style.display="none";

    document.getElementById(
        "gameOver"
    ).classList.remove(
        "hidden"
    );
}


/* =========================
   PAUSE
========================= */

function pauseGame(){

    if(!running)return;

    paused=true;

    document.getElementById(
        "pauseMenu"
    ).classList.remove(
        "hidden"
    );

    document.getElementById(
        "pauseBtn"
    ).style.display="none";
}

function resumeGame(){

    if(!running)return;

    paused=false;

    document.getElementById(
        "pauseMenu"
    ).classList.add(
        "hidden"
    );

    document.getElementById(
        "pauseBtn"
    ).style.display="block";

    lastTime=
        performance.now();
}

function quitGame(){

    paused=false;
    running=false;

    totalCoins+=runCoins;

    if(
        Math.floor(score)>
        highscore
    ){
        highscore=
            Math.floor(score);
    }

    saveData();

    objects=[];

    document.getElementById(
        "pauseMenu"
    ).classList.add(
        "hidden"
    );

    document.getElementById(
        "hud"
    ).style.display="none";

    document.getElementById(
        "pauseBtn"
    ).style.display="none";

    document.getElementById(
        "shopBtn"
    ).style.display="none";

    document.getElementById(
        "menu"
    ).classList.remove(
        "hidden"
    );

    updateHUD();
}


/* =========================
   HUD
========================= */

function updateHUD(){

    document.getElementById(
        "coins"
    ).textContent=
        totalCoins+
        runCoins;

    document.getElementById(
        "score"
    ).textContent=
        Math.floor(score);

    document.getElementById(
        "characterName"
    ).textContent=
        characters[selected].name;

    document.getElementById(
        "menuCoins"
    ).textContent=
        totalCoins;

    document.getElementById(
        "shopCoins"
    ).textContent=
        totalCoins;
}


/* =========================
   SHOP
========================= */

function drawPreview(
    canvas,
    c
){

    const p=
        canvas.getContext("2d");

    p.clearRect(
        0,
        0,
        100,
        100
    );

    p.save();

    p.translate(
        50,
        50
    );

    p.shadowColor=
        c.aura;

    p.shadowBlur=25;

    p.fillStyle=
        c.main;

    p.fillRect(
        -35,
        -35,
        70,
        70
    );

    p.shadowBlur=0;

    p.fillStyle=
        c.dark;

    p.beginPath();

    p.moveTo(
        35,-35
    );

    p.lineTo(
        49,-19
    );

    p.lineTo(
        49,33
    );

    p.lineTo(
        35,35
    );

    p.closePath();

    p.fill();

    p.fillStyle=
        c.light;

    p.globalAlpha=.8;

    p.beginPath();

    p.moveTo(
        -35,-35
    );

    p.lineTo(
        -19,-49
    );

    p.lineTo(
        49,-19
    );

    p.lineTo(
        35,-35
    );

    p.closePath();

    p.fill();

    p.globalAlpha=1;

    p.fillStyle="#fff";

    p.beginPath();

    p.arc(
        -10,
        -10,
        6,
        0,
        Math.PI*2
    );

    p.fill();

    p.restore();
}


function renderShop(){

    const grid=
        document.getElementById(
            "characterGrid"
        );

    grid.innerHTML="";

    characters.forEach(
        (c,index)=>{

            const card=
                document.createElement(
                    "div"
                );

            card.className="card";

            const preview=
                document.createElement(
                    "canvas"
                );

            preview.width=100;
            preview.height=100;

            drawPreview(
                preview,
                c
            );

            card.appendChild(
                preview
            );

            const name=
                document.createElement(
                    "div"
                );

            name.className=
                "cardName";

            name.textContent=
                c.name;

            card.appendChild(
                name
            );

            const price=
                document.createElement(
                    "div"
                );

            price.className="price";

            price.textContent=
                c.price===0
                ?"STARTER"
                :"🪙 "+c.price;

            card.appendChild(
                price
            );

            const btn=
                document.createElement(
                    "button"
                );

            if(
                unlocked.includes(index)
            ){

                btn.textContent=
                    selected===index
                    ?"✓ AUSGEWÄHLT"
                    :"AUSWÄHLEN";

                btn.onclick=()=>{

                    selected=index;

                    saveData();

                    renderShop();

                    updateHUD();
                };

            }else{

                btn.textContent=
                    "KAUFEN";

                btn.onclick=()=>{

                    if(
                        totalCoins<
                        c.price
                    ){

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

                    totalCoins-=
                        c.price;

                    unlocked.push(
                        index
                    );

                    selected=index;

                    saveData();

                    renderShop();

                    updateHUD();
                };
            }

            card.appendChild(btn);

            grid.appendChild(card);
        }
    );
}


/* =========================
   START
========================= */

function startGame(){

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
        "pauseMenu"
    ).classList.add(
        "hidden"
    );

    document.getElementById(
        "hud"
    ).style.display=
        "flex";

    document.getElementById(
        "pauseBtn"
    ).style.display=
        "block";

    document.getElementById(
        "shopBtn"
    ).style.display=
        "block";

    running=true;
    paused=false;

    score=0;
    runCoins=0;

    currentLane=0;
    targetLane=0;

    playerX=
        laneX(0);

    objects=[];

    speed=240;

    carTimer=0;
    coinTimer=700;

    lastTime=
        performance.now();

    updateHUD();
}


/* =========================
   SWIPE
========================= */

let touchStartX=0;
let touchStartY=0;

canvas.addEventListener(
    "touchstart",
    e=>{

        if(
            !running||
            paused
        )return;

        const t=
            e.changedTouches[0];

        touchStartX=
            t.clientX;

        touchStartY=
            t.clientY;

    },
    {
        passive:true
    }
);

canvas.addEventListener(
    "touchend",
    e=>{

        if(
            !running||
            paused
        )return;

        const t=
            e.changedTouches[0];

        const dx=
            t.clientX-
            touchStartX;

        const dy=
            t.clientY-
            touchStartY;

        if(
            Math.abs(dx)<40
        )return;

        if(
            Math.abs(dx)<
            Math.abs(dy)
        )return;

        if(dx>0){

            targetLane++;

        }else{

            targetLane--;
        }

        targetLane=
            Math.max(
                -1,
                Math.min(
                    1,
                    targetLane
                )
            );

    },
    {
        passive:true
    }
);


/* Tastatur */

addEventListener(
    "keydown",
    e=>{

        if(
            !running||
            paused
        )return;

        if(
            e.key==="ArrowLeft"||
            e.key.toLowerCase()==="a"
        ){
            targetLane--;
        }

        if(
            e.key==="ArrowRight"||
            e.key.toLowerCase()==="d"
        ){
            targetLane++;
        }

        targetLane=
            Math.max(
                -1,
                Math.min(
                    1,
                    targetLane
                )
            );
    }
);


/* =========================
   LOOP
========================= */

let lastTime=
    performance.now();

function loop(time){

    const dt=
        Math.min(
            time-lastTime,
            40
        );

    lastTime=time;

    update(dt);

    ctx.clearRect(
        0,
        0,
        W,
        H
    );

    drawWorld(time);

    const sorted=
        [...objects].sort(
            (a,b)=>a.y-b.y
        );

    for(
        const o of sorted
    ){

        if(
            o.type==="car"
        ){
            drawCar(o);
        }else{
            drawCoin(
                o,
                time
            );
        }
    }

    drawPlayer();

    requestAnimationFrame(
        loop
    );
}

requestAnimationFrame(
    loop
);


/* =========================
   BUTTONS
========================= */

document.getElementById(
    "startBtn"
).onclick=
    startGame;

document.getElementById(
    "againBtn"
).onclick=
    startGame;

document.getElementById(
    "pauseBtn"
).onclick=
    pauseGame;

document.getElementById(
    "resumeBtn"
).onclick=
    resumeGame;

document.getElementById(
    "quitBtn"
).onclick=
    quitGame;

document.getElementById(
    "backBtn"
).onclick=()=>{

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

    updateHUD();
};


/* =========================
   SHOP ÖFFNEN
========================= */

function openShop(){

    if(running)return;

    renderShop();

    document.getElementById(
        "shop"
    ).classList.add(
        "active"
    );
}

document.getElementById(
    "menuShopBtn"
).onclick=
    openShop;

document.getElementById(
    "shopBtn"
).onclick=
    openShop;

document.getElementById(
    "closeShop"
).onclick=()=>{

    saveData();

    document.getElementById(
        "shop"
    ).classList.remove(
        "active"
    );
};


/* =========================
   ADMIN CODE
========================= */

document.getElementById(
    "adminBtn"
).onclick=()=>{

    const code=
        prompt(
            "🔐 ADMIN CODE EINGEBEN"
        );

    if(code==="110"){

        totalCoins+=100000;

        unlocked=[];

        for(
            let i=0;
            i<characters.length;
            i++
        ){
            unlocked.push(i);
        }

        selected=0;

        saveData();

        updateHUD();

        renderShop();

        alert(
            "✅ ADMIN CODE AKTIVIERT!\n\n"+
            "🪙 +100.000 Münzen\n"+
            "◆ Alle Charaktere freigeschaltet!"
        );

    }else{

        if(
            code!==null
        ){

            alert(
                "❌ Falscher Code"
            );
        }
    }
};


/* =========================
   STARTWERTE
========================= */

updateHUD();
renderShop();

</script>

</body>
</html>
