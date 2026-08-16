<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Cube Rush</title>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
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

#hud{
    display:none;
    position:fixed;
    top:12px;
    left:12px;
    right:12px;
    z-index:20;
    justify-content:space-between;
    gap:7px;
    pointer-events:none;
}

.hudBox{
    color:white;
    background:rgba(0,8,18,.82);
    border:1px solid rgba(0,220,255,.4);
    padding:9px 12px;
    border-radius:13px;
    font-size:12px;
    font-weight:bold;
    box-shadow:0 0 18px rgba(0,220,255,.15);
}

.screen{
    position:fixed;
    inset:0;
    z-index:50;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    text-align:center;
    color:white;
    background:
        radial-gradient(circle at 50% 25%,rgba(0,200,255,.2),transparent 40%),
        #02040a;
}

.hidden{
    display:none!important;
}

.logo{
    font-size:44px;
    font-weight:1000;
    letter-spacing:4px;
    text-shadow:
        0 0 10px #00eaff,
        0 0 30px #008cff,
        0 0 60px #005eff;
}

.sub{
    margin-top:7px;
    color:#62ddff;
    letter-spacing:5px;
    font-size:10px;
}

button{
    border:0;
    border-radius:14px;
    margin-top:17px;
    padding:14px 25px;
    background:#00d9ff;
    color:#001018;
    font-weight:1000;
    font-size:13px;
    box-shadow:0 0 22px rgba(0,220,255,.5);
}

button:active{
    transform:scale(.94);
}

#pauseButton{
    display:none;
    position:fixed;
    top:58px;
    right:12px;
    z-index:30;
    margin:0;
    padding:9px 12px;
    font-size:10px;
    background:rgba(0,15,25,.92);
    color:white;
    border:1px solid rgba(0,220,255,.5);
    box-shadow:0 0 15px rgba(0,220,255,.25);
}

#shopButton{
    display:none;
    position:fixed;
    bottom:13px;
    right:13px;
    z-index:30;
    margin:0;
    padding:10px 13px;
    font-size:10px;
}

#shop{
    position:fixed;
    inset:0;
    z-index:100;
    display:none;
    overflow-y:auto;
    padding:20px 13px;
    color:white;
    background:
        radial-gradient(circle at top,#064263,#02040a 70%);
}

#shop.active{
    display:block;
}

.shopTitle{
    text-align:center;
    font-size:35px;
    font-weight:1000;
    text-shadow:0 0 20px #00eaff;
}

.shopCoins{
    margin-top:8px;
    text-align:center;
    color:#ffe22e;
    font-weight:bold;
}

.grid{
    max-width:900px;
    margin:22px auto;
    display:grid;
    grid-template-columns:repeat(auto-fit,minmax(145px,1fr));
    gap:13px;
}

.card{
    min-height:210px;
    padding:10px;
    border-radius:20px;
    text-align:center;
    background:rgba(255,255,255,.055);
    border:1px solid rgba(0,220,255,.25);
}

.card.locked{
    opacity:.58;
}

.preview{
    width:105px;
    height:105px;
}

.charName{
    font-weight:1000;
}

.price{
    margin-top:5px;
    color:#ffe22e;
    font-weight:bold;
}

.card button{
    padding:8px 12px;
    margin-top:8px;
    font-size:10px;
}

.close{
    display:block;
    margin:18px auto;
}

#pauseMenu button{
    min-width:200px;
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
        ◆ <span id="charName">BLUE CORE</span>
    </div>

</div>

<!-- Pause -->
<button id="pauseButton">⏸ PAUSE</button>

<!-- Charakter-Shop Button -->
<button id="shopButton">◆ CHARAKTERE</button>

<!-- Hauptmenü -->
<div id="menu" class="screen">

    <div class="logo">CUBE RUSH</div>

    <div class="sub">NEON CITY</div>

    <div style="margin-top:20px;color:#ffe22e;font-weight:bold;">
        🪙 <span id="menuCoins">0</span>
    </div>

    <button id="start">
        SPIEL STARTEN
    </button>

    <button id="menuShop">
        ◆ CHARAKTERE
    </button>

</div>

<!-- Game Over -->
<div id="gameOver" class="screen hidden">

    <div class="logo" style="font-size:35px;">
        CRASH
    </div>

    <p style="margin-top:20px;">
        Punkte:
        <b id="finalScore">0</b>
    </p>

    <p style="margin-top:8px;">
        Münzen:
        <b id="finalCoins">0</b>
    </p>

    <p style="margin-top:8px;">
        Highscore:
        <b id="finalHigh">0</b>
    </p>

    <button id="again">
        NOCHMAL
    </button>

    <button id="menuBack">
        MENÜ
    </button>

</div>

<!-- PAUSE MENÜ -->
<div id="pauseMenu" class="screen hidden">

    <div class="logo" style="font-size:35px;">
        PAUSE
    </div>

    <p style="margin-top:15px;color:#8eeaff;">
        Das Spiel ist pausiert.
    </p>

    <button id="resumeButton">
        ▶ WEITERSPIELEN
    </button>

    <button id="quitButton">
        ✕ AUFGEBEN
    </button>

</div>

<!-- SHOP -->
<div id="shop">

    <div class="shopTitle">
        CHARAKTERE
    </div>

    <div class="shopCoins">
        🪙 <span id="shopCoinAmount">0</span>
    </div>

    <div id="characterGrid" class="grid"></div>

    <button id="closeShop" class="close">
        ZURÜCK
    </button>

</div>

<script>

/* =====================================================
   CANVAS
===================================================== */

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


/* =====================================================
   CHARAKTERE
===================================================== */

const characters=[

    {
        name:"BLUE CORE",
        price:0,
        main:"#008cff",
        light:"#00f4ff",
        dark:"#00458e",
        aura:"#00eaff"
    },

    {
        name:"VOLT",
        price:300,
        main:"#ffe000",
        light:"#ffffbc",
        dark:"#947300",
        aura:"#ffe000"
    },

    {
        name:"FIRE",
        price:700,
        main:"#ff3200",
        light:"#ffb000",
        dark:"#8d1100",
        aura:"#ff2500"
    },

    {
        name:"POISON",
        price:1200,
        main:"#55ff00",
        light:"#d5ff9c",
        dark:"#287300",
        aura:"#55ff00"
    },

    {
        name:"ICE",
        price:2000,
        main:"#00d8ff",
        light:"#efffff",
        dark:"#00749b",
        aura:"#00eaff"
    },

    {
        name:"PURPLE",
        price:3000,
        main:"#9c32ff",
        light:"#e8c0ff",
        dark:"#481080",
        aura:"#a83cff"
    },

    {
        name:"MAGMA",
        price:4500,
        main:"#ff4b00",
        light:"#fff000",
        dark:"#851800",
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
        light:"#a08aff",
        dark:"#050509",
        aura:"#704dff"
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


/* =====================================================
   SPEICHER
===================================================== */

let totalCoins=0;
let highscore=0;
let unlocked=[0];
let selected=0;

function loadGame(){

    try{

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

        const savedUnlocked=
            localStorage.getItem(
                "cubeRushUnlocked"
            );

        if(savedUnlocked){

            const data=
                JSON.parse(savedUnlocked);

            if(Array.isArray(data)){

                unlocked=
                    data.filter(
                        n =>
                            Number.isInteger(n) &&
                            n>=0 &&
                            n<characters.length
                    );
            }
        }

        const savedSelected=
            Number(
                localStorage.getItem(
                    "cubeRushSelected"
                )
            );

        if(
            Number.isInteger(savedSelected) &&
            savedSelected>=0 &&
            savedSelected<characters.length
        ){

            selected=savedSelected;
        }

        if(!unlocked.includes(0)){
            unlocked.push(0);
        }

        if(!unlocked.includes(selected)){
            selected=0;
        }

    }catch(error){

        totalCoins=0;
        highscore=0;
        unlocked=[0];
        selected=0;
    }
}

function saveGame(){

    try{

        localStorage.setItem(
            "cubeRushCoins",
            String(totalCoins)
        );

        localStorage.setItem(
            "cubeRushHighscore",
            String(highscore)
        );

        localStorage.setItem(
            "cubeRushUnlocked",
            JSON.stringify(unlocked)
        );

        localStorage.setItem(
            "cubeRushSelected",
            String(selected)
        );

    }catch(error){

        console.log(
            "Speichern fehlgeschlagen"
        );
    }
}

loadGame();

window.addEventListener(
    "beforeunload",
    saveGame
);

document.addEventListener(
    "visibilitychange",
    ()=>{
        if(
            document.visibilityState==="hidden"
        ){
            saveGame();
        }
    }
);


/* =====================================================
   SPIELVARIABLEN
===================================================== */

let running=false;
let paused=false;

let score=0;
let runCoins=0;

let lane=0;
let targetLane=0;
let playerX=0;

let objects=[];

let speed=230;

let carTimer=0;
let coinTimer=0;


/* =====================================================
   STRASSEN
===================================================== */

function getRoad(){

    const width=Math.min(
        W*.82,
        600
    );

    const left=
        (W-width)/2;

    return{
        left:left,
        width:width,
        right:left+width
    };
}

function laneX(number){

    number=Math.max(
        -1,
        Math.min(1,number)
    );

    const r=getRoad();

    const laneWidth=
        r.width/3;

    return r.left+
        laneWidth*(number+1.5);
}


/* =====================================================
   WELT
===================================================== */

function drawWorld(time){

    const sky=
        ctx.createLinearGradient(
            0,0,0,H
        );

    sky.addColorStop(
        0,
        "#01030a"
    );

    sky.addColorStop(
        .5,
        "#071c2e"
    );

    sky.addColorStop(
        1,
        "#02060b"
    );

    ctx.fillStyle=sky;

    ctx.fillRect(
        0,0,W,H
    );


    /* Gebäude */

    for(let i=0;i<22;i++){

        const bw=
            30+(i*19)%48;

        const bh=
            100+(i*57)%240;

        const x=
            i*70-20;

        const y=
            H*.39-bh;

        ctx.fillStyle=
            "rgba(4,13,25,.95)";

        ctx.fillRect(
            x,y,bw,bh
        );

        for(
            let wy=y+15;
            wy<y+bh-10;
            wy+=25
        ){

            ctx.fillStyle=
                "rgba(0,210,255,.3)";

            ctx.fillRect(
                x+7,
                wy,
                4,
                9
            );

            if(bw>50){

                ctx.fillRect(
                    x+bw-12,
                    wy,
                    4,
                    9
                );
            }
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


    /* Straße */

    const r=getRoad();

    ctx.fillStyle="#101820";

    ctx.fillRect(
        r.left,
        0,
        r.width,
        H
    );


    /* Straßenrand */

    ctx.fillStyle="#00dfff";

    ctx.shadowColor="#00dfff";
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


    /* 3 Spuren */

    const laneWidth=
        r.width/3;

    const dash=50;
    const gap=35;

    const offset=
        (score*3)%(dash+gap);

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
                "rgba(0,210,255,.5)";

            ctx.shadowColor="#00dfff";
            ctx.shadowBlur=8;

            ctx.fillRect(
                r.left+
                laneWidth*line-2,
                y,
                4,
                dash
            );

            ctx.shadowBlur=0;
        }
    }


    /* Seitenlichter */

    const lightOffset=
        (score*2)%80;

    for(
        let y=-80+lightOffset;
        y<H;
        y+=80
    ){

        ctx.fillStyle=
            "rgba(0,220,255,.75)";

        ctx.shadowColor="#00dfff";
        ctx.shadowBlur=14;

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
){

    ctx.save();

    ctx.translate(x,y);
    ctx.rotate(rotation);


    const aura=
        ctx.createRadialGradient(
            0,0,2,
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
        0,0,size*1.8,
        0,Math.PI*2
    );

    ctx.fill();

    ctx.globalAlpha=1;


    /* Schatten */

    ctx.fillStyle=
        "rgba(0,0,0,.5)";

    ctx.beginPath();

    ctx.ellipse(
        0,
        size*.7,
        size*.7,
        size*.15,
        0,0,Math.PI*2
    );

    ctx.fill();


    /* Würfel */

    ctx.shadowColor=c.aura;
    ctx.shadowBlur=25;

    ctx.fillStyle=c.main;

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


    /* Licht */

    ctx.fillStyle="#fff";

    ctx.shadowColor="#fff";
    ctx.shadowBlur=12;

    ctx.beginPath();

    ctx.arc(
        -size*.13,
        -size*.13,
        size*.08,
        0,Math.PI*2
    );

    ctx.fill();

    ctx.shadowBlur=0;


    /* Blitz */

    if(
        c.name==="THUNDER" ||
        c.name==="OMEGA"
    ){

        ctx.strokeStyle=c.light;
        ctx.lineWidth=2;

        ctx.shadowColor=c.light;
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

function drawPlayer(){

    const c=
        characters[selected];

    playerX+=
        (laneX(targetLane)-playerX)*.22;

    const y=H-115;

    drawCube(
        playerX,
        y,
        58,
        c,
        (targetLane-lane)*.15
    );
}


/* =====================================================
   AUTOS
===================================================== */

function spawnCar(){

    const lanes=[
        -1,
        0,
        1
    ];

    const possible=
        lanes.filter(
            l =>
                !objects.some(
                    o =>
                        o.type==="car" &&
                        o.lane===l &&
                        o.y<170
                )
        );

    if(
        possible.length===0
    )
        return;

    const l=
        possible[
            Math.floor(
                Math.random()*possible.length
            )
        ];

    objects.push({

        type:"car",

        lane:l,

        y:-160,

        size:
            58+
            Math.random()*9,

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


/* =====================================================
   AUTO ZEICHNEN
===================================================== */

function drawCar(o){

    const x=laneX(o.lane);
    const y=o.y;

    const w=o.size;
    const h=w*1.45;

    ctx.save();

    ctx.translate(x,y);


    /* Schatten */

    ctx.fillStyle=
        "rgba(0,0,0,.5)";

    ctx.beginPath();

    ctx.ellipse(
        0,
        h*.48,
        w*.7,
        9,
        0,0,Math.PI*2
    );

    ctx.fill();


    /* Reifen */

    ctx.fillStyle="#030506";

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


    /* Karosserie */

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


    /* Scheibe */

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


    /* Scheinwerfer */

    ctx.fillStyle="#fff";

    ctx.shadowColor="#fff";
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
   MÜNZEN
===================================================== */

function spawnCoins(){

    const lanes=[
        -1,
        0,
        1
    ];


    /* Nur freie Spuren */

    const freeLanes=
        lanes.filter(
            lane =>
                !objects.some(
                    o =>
                        o.type==="car" &&
                        o.lane===lane &&
                        o.y>-180 &&
                        o.y<120
                )
        );


    if(
        freeLanes.length===0
    )
        return;


    const lane=
        freeLanes[
            Math.floor(
                Math.random()*
                freeLanes.length
            )
        ];


    /* Weniger Münzen */

    const amount=
        Math.random()<.75
        ?1
        :2;


    for(
        let i=0;
        i<amount;
        i++
    ){

        const coinY=
            -60-i*65;


        /* Extra Auto-Prüfung */

        const carThere=
            objects.some(
                o =>
                    o.type==="car" &&
                    o.lane===lane &&
                    Math.abs(
                        o.y-coinY
                    )<75
            );


        if(!carThere){

            objects.push({

                type:"coin",

                lane:lane,

                y:coinY
            });
        }
    }
}


function drawCoin(o,time){

    const x=laneX(o.lane);
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
        .4+spin*.6,
        1
    );

    ctx.shadowColor="#ffe000";
    ctx.shadowBlur=22;

    ctx.fillStyle="#b77900";

    ctx.beginPath();

    ctx.arc(
        0,0,18,
        0,Math.PI*2
    );

    ctx.fill();

    ctx.fillStyle="#ffdf1b";

    ctx.beginPath();

    ctx.arc(
        0,0,14,
        0,Math.PI*2
    );

    ctx.fill();

    ctx.shadowBlur=0;

    ctx.fillStyle="#a06d00";

    ctx.font="bold 15px Arial";

    ctx.textAlign="center";
    ctx.textBaseline="middle";

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

function update(dt){

    if(!running)
        return;

    /* Während Pause absolut nichts bewegen */

    if(paused)
        return;

    const seconds=dt/1000;


    /* Spieler */

    lane+=
        (targetLane-lane)*
        Math.min(
            1,
            seconds*14
        );

    playerX+=
        (laneX(targetLane)-playerX)*
        Math.min(
            1,
            seconds*14
        );


    /* Geschwindigkeit */

    speed+=seconds*4;

    speed=Math.min(
        speed,
        650
    );


    /* Objekte */

    for(
        let i=objects.length-1;
        i>=0;
        i--
    ){

        const o=objects[i];

        o.y+=
            speed*seconds;


        /* Hinter Spieler sofort löschen */

        if(
            o.y>H+120
        ){

            objects.splice(
                i,
                1
            );

            continue;
        }


        /* Auto-Kollision */

        if(
            o.type==="car"
        ){

            const playerY=H-115;

            const sameLane=
                Math.abs(
                    playerX-
                    laneX(o.lane)
                )<30;

            const near=
                Math.abs(
                    o.y-playerY
                )<57;


            if(
                sameLane &&
                near
            ){

                gameOver();

                return;
            }
        }


        /* Münzen */

        if(
            o.type==="coin"
        ){

            const playerY=H-115;

            const sameLane=
                Math.abs(
                    playerX-
                    laneX(o.lane)
                )<31;

            const near=
                Math.abs(
                    o.y-playerY
                )<45;


            if(
                sameLane &&
                near
            ){

                runCoins++;

                objects.splice(
                    i,
                    1
                );
            }
        }
    }


    /* Autos */

    carTimer+=dt;

    const carDelay=
        Math.max(
            600,
            1450-score*1.5
        );


    if(
        carTimer>carDelay
    ){

        spawnCar();

        carTimer=0;
    }


    /* Münzen */

    coinTimer+=dt;

    if(
        coinTimer>1700
    ){

        spawnCoins();

        coinTimer=0;
    }


    /* Punkte */

    score+=
        seconds*10;

    updateHUD();
}


/* =====================================================
   GAME OVER
===================================================== */

function gameOver(){

    running=false;
    paused=false;

    const finalScore=
        Math.floor(score);


    totalCoins+=runCoins;


    if(
        finalScore>highscore
    ){

        highscore=
            finalScore;
    }


    saveGame();


    document.getElementById(
        "finalScore"
    ).textContent=
        finalScore;

    document.getElementById(
        "finalCoins"
    ).textContent=
        runCoins;

    document.getElementById(
        "finalHigh"
    ).textContent=
        highscore;


    document.getElementById(
        "hud"
    ).style.display="none";

    document.getElementById(
        "pauseButton"
    ).style.display="none";

    document.getElementById(
        "shopButton"
    ).style.display="none";


    document.getElementById(
        "gameOver"
    ).classList.remove(
        "hidden"
    );
}


/* =====================================================
   PAUSE
===================================================== */

function pauseGame(){

    if(!running)
        return;

    paused=true;

    document.getElementById(
        "pauseMenu"
    ).classList.remove(
        "hidden"
    );

    document.getElementById(
        "pauseButton"
    ).style.display="none";
}


function resumeGame(){

    if(!running)
        return;

    paused=false;

    document.getElementById(
        "pauseMenu"
    ).classList.add(
        "hidden"
    );

    document.getElementById(
        "pauseButton"
    ).style.display="block";


    /*
       Wichtig:
       Dadurch gibt es beim Weiterlaufen
       keinen riesigen Zeit-Sprung.
    */

    last=
        performance.now();
}


function quitGame(){

    paused=false;
    running=false;


    /* Bereits gesammelte Münzen behalten */

    totalCoins+=runCoins;


    if(
        Math.floor(score)>highscore
    ){

        highscore=
            Math.floor(score);
    }


    saveGame();


    document.getElementById(
        "pauseMenu"
    ).classList.add(
        "hidden"
    );

    document.getElementById(
        "hud"
    ).style.display="none";

    document.getElementById(
        "pauseButton"
    ).style.display="none";

    document.getElementById(
        "shopButton"
    ).style.display="none";


    objects=[];


    document.getElementById(
        "menu"
    ).classList.remove(
        "hidden"
    );

    updateHUD();
}


/* =====================================================
   HUD
===================================================== */

function updateHUD(){

    document.getElementById(
        "coins"
    ).textContent=
        totalCoins+runCoins;

    document.getElementById(
        "score"
    ).textContent=
        Math.floor(score);

    document.getElementById(
        "charName"
    ).textContent=
        characters[selected].name;

    document.getElementById(
        "menuCoins"
    ).textContent=
        totalCoins;

    document.getElementById(
        "shopCoinAmount"
    ).textContent=
        totalCoins;
}


/* =====================================================
   DRAW
===================================================== */

function draw(time){

    ctx.clearRect(
        0,0,W,H
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
}


/* =====================================================
   GAME LOOP
===================================================== */

let last=performance.now();

function loop(time){

    const dt=
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

requestAnimationFrame(loop);


/* =====================================================
   SWIPE
===================================================== */

let startX=0;
let startY=0;

canvas.addEventListener(
    "touchstart",
    e=>{

        if(
            !running ||
            paused
        )
            return;

        const t=
            e.changedTouches[0];

        startX=t.clientX;
        startY=t.clientY;

    },
    {passive:true}
);


canvas.addEventListener(
    "touchend",
    e=>{

        if(
            !running ||
            paused
        )
            return;

        const t=
            e.changedTouches[0];

        const dx=
            t.clientX-startX;

        const dy=
            t.clientY-startY;


        if(
            Math.abs(dx)<40
        )
            return;

        if(
            Math.abs(dx)<
            Math.abs(dy)
        )
            return;


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
    {passive:true}
);


/* =====================================================
   TASTATUR
===================================================== */

addEventListener(
    "keydown",
    e=>{

        if(
            !running ||
            paused
        )
            return;


        if(
            e.key==="ArrowLeft" ||
            e.key.toLowerCase()==="a"
        ){

            targetLane--;
        }


        if(
            e.key==="ArrowRight" ||
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


/* =====================================================
   SHOP
===================================================== */

function drawPreview(p,c){

    p.clearRect(
        0,0,210,210
    );

    p.save();

    p.translate(
        105,
        108
    );

    p.shadowColor=c.aura;
    p.shadowBlur=30;

    p.fillStyle=c.main;

    p.fillRect(
        -42,
        -42,
        84,
        84
    );

    p.shadowBlur=0;

    p.fillStyle=c.dark;

    p.beginPath();

    p.moveTo(
        42,-42
    );

    p.lineTo(
        59,-23
    );

    p.lineTo(
        59,40
    );

    p.lineTo(
        42,42
    );

    p.closePath();

    p.fill();


    p.fillStyle=c.light;

    p.globalAlpha=.8;

    p.beginPath();

    p.moveTo(
        -42,-42
    );

    p.lineTo(
        -23,-59
    );

    p.lineTo(
        59,-23
    );

    p.lineTo(
        42,-42
    );

    p.closePath();

    p.fill();

    p.globalAlpha=1;


    p.fillStyle="#fff";

    p.beginPath();

    p.arc(
        -11,-11,
        7,
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
        (c,i)=>{

            const card=
                document.createElement(
                    "div"
                );

            card.className="card";


            if(
                !unlocked.includes(i)
            ){

                card.classList.add(
                    "locked"
                );
            }


            const preview=
                document.createElement(
                    "canvas"
                );

            preview.className="preview";

            preview.width=210;
            preview.height=210;


            drawPreview(
                preview.getContext("2d"),
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
                "charName";

            name.textContent=
                c.name;

            card.appendChild(name);


            const price=
                document.createElement(
                    "div"
                );

            price.className="price";

            price.textContent=
                c.price===0
                ?"STARTER"
                :"🪙 "+c.price;

            card.appendChild(price);


            const button=
                document.createElement(
                    "button"
                );


            if(
                unlocked.includes(i)
            ){

                button.textContent=
                    selected===i
                    ?"✓ AUSGEWÄHLT"
                    :"AUSWÄHLEN";


                button.onclick=()=>{

                    selected=i;

                    saveGame();

                    renderShop();

                    updateHUD();
                };


            }else{

                button.textContent=
                    "KAUFEN";


                button.onclick=()=>{

                    if(
                        totalCoins<c.price
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


                    if(
                        !unlocked.includes(i)
                    ){

                        unlocked.push(i);
                    }


                    selected=i;


                    saveGame();

                    renderShop();

                    updateHUD();
                };
            }


            card.appendChild(
                button
            );

            grid.appendChild(
                card
            );
        }
    );
}


/* =====================================================
   START
===================================================== */

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
    ).style.display="flex";


    document.getElementById(
        "pauseButton"
    ).style.display="block";


    document.getElementById(
        "shopButton"
    ).style.display="block";


    running=true;
    paused=false;

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


    last=
        performance.now();

    updateHUD();
}


/* =====================================================
   BUTTONS
===================================================== */

document.getElementById(
    "start"
).onclick=
    startGame;


document.getElementById(
    "again"
).onclick=
    startGame;


document.getElementById(
    "pauseButton"
).onclick=
    pauseGame;


document.getElementById(
    "resumeButton"
).onclick=
    resumeGame;


document.getElementById(
    "quitButton"
).onclick=
    quitGame;


document.getElementById(
    "menuBack"
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


/* =====================================================
   SHOP ÖFFNEN
===================================================== */

function openShop(){

    if(running){
        return;
    }

    saveGame();

    renderShop();

    document.getElementById(
        "shop"
    ).classList.add(
        "active"
    );
}


document.getElementById(
    "shopButton"
).onclick=
    openShop;


document.getElementById(
    "menuShop"
).onclick=
    openShop;


document.getElementById(
    "closeShop"
).onclick=()=>{

    saveGame();

    document.getElementById(
        "shop"
    ).classList.remove(
        "active"
    );
};


/* =====================================================
   INITIALISIERUNG
===================================================== */

updateHUD();

renderShop();

</script>

</body>
</html>
