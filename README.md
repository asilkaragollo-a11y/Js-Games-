<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,user-scalable=no">
<title>Cube Rush</title>

<style>
*{box-sizing:border-box}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#050814;
    color:white;
    font-family:Arial,sans-serif;
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
    align-items:center;
    justify-content:center;
    padding:20px;
    background:linear-gradient(#07182d,#02040a);
    overflow:auto;
}

.hidden{display:none!important}

.logo{
    font-size:46px;
    font-weight:900;
    color:#00eaff;
    text-shadow:0 0 30px #00eaff;
    margin-bottom:8px;
    text-align:center;
}

h1{
    color:#00eaff;
    text-shadow:0 0 20px #00eaff;
    text-align:center;
}

input{
    width:320px;
    max-width:90%;
    padding:14px;
    margin:6px;
    border-radius:12px;
    border:1px solid #00eaff;
    background:#081522;
    color:white;
    font-size:16px;
    outline:none;
}

button{
    padding:13px 22px;
    margin:6px;
    border:0;
    border-radius:12px;
    background:#00eaff;
    color:#001018;
    font-weight:bold;
    font-size:15px;
    cursor:pointer;
}

button:active{
    transform:scale(.95);
}

.msg{
    min-height:24px;
    color:#a9c5d0;
    text-align:center;
    max-width:340px;
}

.coins{
    color:#ffd900;
    font-weight:bold;
    font-size:20px;
    margin:10px;
}

.card{
    display:inline-flex;
    vertical-align:top;
    flex-direction:column;
    justify-content:space-between;
    width:180px;
    min-height:210px;
    margin:7px;
    padding:14px;
    background:#091522;
    border:1px solid #00eaff55;
    border-radius:15px;
    text-align:center;
}

.card h3{
    margin:5px 0 10px;
}

.preview{
    height:85px;
    display:flex;
    align-items:center;
    justify-content:center;
}

.avatar{
    width:58px;
    height:58px;
    border-radius:12px;
    box-shadow:0 0 25px currentColor;
    transform:rotate(5deg);
}

.cityPreview{
    width:145px;
    height:75px;
    position:relative;
    overflow:hidden;
    border-radius:10px;
    border:1px solid #ffffff33;
}

.building{
    position:absolute;
    bottom:0;
    width:20px;
    background:#18263a;
}

.rank{
    width:350px;
    max-width:90vw;
    display:flex;
    justify-content:space-between;
    padding:12px;
    margin:4px;
    background:#091522;
    border-radius:10px;
}

#hud{
    position:fixed;
    z-index:10;
    top:10px;
    left:10px;
    right:10px;
    display:none;
    justify-content:space-between;
    gap:6px;
    pointer-events:none;
}

.hud{
    background:#06121dcc;
    border:1px solid #00eaff66;
    padding:8px 12px;
    border-radius:10px;
    font-weight:bold;
}

#pause{
    position:fixed;
    z-index:11;
    top:60px;
    right:10px;
    display:none;
}

.timer{
    font-size:14px;
    color:#fff;
    margin-top:4px;
}

.power{
    color:#00eaff;
}

.menuButtons{
    display:flex;
    flex-wrap:wrap;
    justify-content:center;
    max-width:500px;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="hud">
    <div class="hud">COINS <span id="coins">0</span></div>
    <div class="hud">SCORE <span id="score">0</span></div>
    <div class="hud">CITY <span id="cityName">Neon City</span></div>
    <div class="hud" id="powerHud"></div>
</div>

<button id="pause">PAUSE</button>

<!-- LOGIN -->
<div id="login" class="screen">
    <div class="logo">CUBE RUSH</div>
    <p>ONLINE EDITION</p>

    <input id="username"
           placeholder="Benutzername"
           maxlength="18"
           autocomplete="username">

    <input id="email"
           type="email"
           placeholder="E-Mail"
           autocomplete="email">

    <input id="password"
           type="password"
           placeholder="Passwort"
           minlength="6"
           autocomplete="current-password">

    <button onclick="register()">REGISTRIEREN</button>
    <button onclick="loginUser()">ANMELDEN</button>

    <div id="loginMsg" class="msg"></div>
</div>

<!-- MENU -->
<div id="menu" class="screen hidden">

    <div class="logo">CUBE RUSH</div>

    <p>
        Spieler:
        <b id="player"></b>
    </p>

    <div class="coins">
        COINS <span id="menuCoins">0</span>
    </div>

    <div class="menuButtons">
        <button onclick="startGame()">SPIEL STARTEN</button>
        <button onclick="charactersMenu()">CHARAKTERE</button>
        <button onclick="citiesMenu()">STÄDTE</button>
        <button onclick="rankingMenu()">RANGLISTE</button>
        <button onclick="codeMenu()">ADMIN CODE</button>
        <button onclick="logout()">ABMELDEN</button>
    </div>
</div>

<!-- CHARACTERS -->
<div id="characters" class="screen hidden">
    <h1>CHARAKTERE</h1>

    <div class="coins">
        COINS <span id="charCoins">0</span>
    </div>

    <div id="characterList"></div>

    <button onclick="showMenu()">ZURÜCK</button>
</div>

<!-- CITIES -->
<div id="cities" class="screen hidden">
    <h1>STÄDTE</h1>

    <div class="coins">
        COINS <span id="cityCoins">0</span>
    </div>

    <div id="cityList"></div>

    <button onclick="showMenu()">ZURÜCK</button>
</div>

<!-- RANKING -->
<div id="ranking" class="screen hidden">
    <h1>RANGLISTE</h1>

    <div id="rankingList">
        Noch keine Spieler.
    </div>

    <button onclick="showMenu()">ZURÜCK</button>
</div>

<!-- CODE -->
<div id="codes" class="screen hidden">
    <h1>ADMIN CODE</h1>

    <input id="secretCode"
           inputmode="numeric"
           maxlength="3"
           placeholder="Code">

    <button onclick="redeemCode()">EINLÖSEN</button>

    <div id="codeMsg" class="msg"></div>

    <button onclick="showMenu()">ZURÜCK</button>
</div>

<!-- PAUSE -->
<div id="pauseScreen" class="screen hidden">
    <h1>PAUSE</h1>

    <button onclick="resumeGame()">WEITERSPIELEN</button>
    <button onclick="quitGame()">MENÜ</button>
</div>

<!-- GAME OVER -->
<div id="gameOver" class="screen hidden">
    <h1>CRASH</h1>

    <div id="gameOverText"></div>

    <button onclick="restartGame()">NOCHMAL</button>
    <button onclick="showMenu()">MENÜ</button>
</div>

<script>

/* =====================================================
   SPIELER
===================================================== */

let playerData={
    username:"",
    email:"",
    coins:0,
    highscore:0,
    characters:[0],
    cities:[0],
    character:0,
    city:0
};


/* =====================================================
   CHARAKTERE
===================================================== */

const charData=[
    ["Blue",0,"#00eaff"],
    ["Volt",300,"#fff200"],
    ["Fire",700,"#ff3b20"],
    ["Ice",1200,"#7de8ff"],
    ["Purple",2000,"#a855ff"],
    ["Poison",3000,"#51ff00"],
    ["Magma",4500,"#ff5b00"],
    ["Thunder",6500,"#ffe600"],
    ["Void",9000,"#7b00ff"],
    ["Omega",12000,"#ffffff"],
    ["Cyber",16000,"#00ffcc"],
    ["Gold",22000,"#ffd700"],
    ["Ruby",28000,"#ff003c"],
    ["Diamond",35000,"#8ffcff"],
    ["Galaxy",45000,"#c000ff"],
    ["Phantom",55000,"#566bff"],
    ["Solar",70000,"#ff9d00"],
    ["Nebula",85000,"#ff55cc"],
    ["Aurora",100000,"#55ffcc"],
    ["Cube God",150000,"#ffffff"]
];


/* =====================================================
   STÄDTE
===================================================== */

const cityData=[

    {
        name:"Neon City",
        price:0,
        min:3,
        max:6,
        sky:"#071a35",
        road:"#111820",
        light:"#00eaff",
        building:"#102a3d"
    },

    {
        name:"Tokyo Night",
        price:5000,
        min:8,
        max:15,
        sky:"#16051f",
        road:"#15131d",
        light:"#ff2cff",
        building:"#271338"
    },

    {
        name:"Cyber Dubai",
        price:12000,
        min:15,
        max:25,
        sky:"#211006",
        road:"#191512",
        light:"#ffb52e",
        building:"#3b2814"
    },

    {
        name:"Future New York",
        price:25000,
        min:25,
        max:40,
        sky:"#07142b",
        road:"#11151c",
        light:"#5e8cff",
        building:"#182944"
    },

    {
        name:"Galaxy City",
        price:50000,
        min:40,
        max:60,
        sky:"#100020",
        road:"#0b0b15",
        light:"#c44cff",
        building:"#25104b"
    }
];


/* =====================================================
   SPEICHERN
===================================================== */

function save(){

    localStorage.setItem(
        "cubeRushPlayer",
        JSON.stringify(playerData)
    );

    updateRankingStorage();
}

function load(){

    const data=
        localStorage.getItem("cubeRushPlayer");

    if(!data) return false;

    try{
        playerData=JSON.parse(data);
        return true;
    }catch{
        return false;
    }
}


/* =====================================================
   LOGIN
===================================================== */

function register(){

    const name=
        document.getElementById("username").value.trim();

    const email=
        document.getElementById("email").value.trim();

    const password=
        document.getElementById("password").value;

    if(name.length<3){
        loginMsg("Benutzername muss mindestens 3 Zeichen haben.");
        return;
    }

    if(!email.includes("@")){
        loginMsg("Bitte eine gültige E-Mail eingeben.");
        return;
    }

    if(password.length<6){
        loginMsg("Das Passwort muss mindestens 6 Zeichen haben.");
        return;
    }

    playerData={
        username:name,
        email:email,
        coins:0,
        highscore:0,
        characters:[0],
        cities:[0],
        character:0,
        city:0
    };

    save();

    showMenu();
}


function loginUser(){

    const name=
        document.getElementById("username").value.trim();

    const email=
        document.getElementById("email").value.trim();

    const password=
        document.getElementById("password").value;

    if(!name || !email || !password){
        loginMsg("Bitte Benutzername, E-Mail und Passwort eingeben.");
        return;
    }

    const saved=
        localStorage.getItem("cubeRushPlayer");

    if(saved){

        const old=
            JSON.parse(saved);

        if(
            old.username===name &&
            old.email===email
        ){
            playerData=old;
            showMenu();
            return;
        }

        loginMsg("Benutzername oder E-Mail stimmt nicht.");
        return;
    }

    playerData.username=name;
    playerData.email=email;

    save();
    showMenu();
}


function loginMsg(text){

    document.getElementById("loginMsg").textContent=text;
}


function logout(){

    save();
    location.reload();
}


/* =====================================================
   MENÜ
===================================================== */

function hideAll(){

    document
        .querySelectorAll(".screen")
        .forEach(x=>x.classList.add("hidden"));
}


function showMenu(){

    hideAll();

    document
        .getElementById("menu")
        .classList.remove("hidden");

    document
        .getElementById("player")
        .textContent=
        playerData.username;

    document
        .getElementById("menuCoins")
        .textContent=
        playerData.coins.toLocaleString();
}


/* =====================================================
   CHARAKTERE
===================================================== */

function charactersMenu(){

    hideAll();

    const list=
        document.getElementById("characterList");

    list.innerHTML="";

    document
        .getElementById("charCoins")
        .textContent=
        playerData.coins.toLocaleString();

    charData.forEach((char,index)=>{

        const card=
            document.createElement("div");

        card.className="card";

        const unlocked=
            playerData.characters.includes(index);

        card.innerHTML=`

            <div class="preview">
                <div class="avatar"
                     style="background:${char[2]};color:${char[2]}">
                </div>
            </div>

            <h3>${char[0]}</h3>

            <p>
                ${
                    unlocked
                    ?"FREIGESCHALTET"
                    :"Preis: "+char[1].toLocaleString()+" Coins"
                }
            </p>
        `;

        const button=
            document.createElement("button");

        if(unlocked){

            button.textContent=
                playerData.character===index
                ?"AKTIV"
                :"AUSWÄHLEN";

            button.onclick=()=>{

                playerData.character=index;

                save();

                charactersMenu();
            };

        }else{

            button.textContent="KAUFEN";

            button.onclick=()=>{

                if(playerData.coins<char[1]){
                    alert("Nicht genug Coins.");
                    return;
                }

                playerData.coins-=char[1];

                playerData.characters.push(index);

                playerData.character=index;

                save();

                charactersMenu();
            };
        }

        card.appendChild(button);

        list.appendChild(card);
    });

    document
        .getElementById("characters")
        .classList.remove("hidden");
}


/* =====================================================
   STÄDTE
===================================================== */

function drawCityPreview(city){

    let buildings="";

    for(let i=0;i<7;i++){

        const height=
            25+Math.random()*45;

        buildings+=`
            <div class="building"
                 style="
                 left:${i*21}px;
                 height:${height}px;
                 background:${city.building};
                 box-shadow:0 0 8px ${city.light};
                 ">
            </div>
        `;
    }

    return `
        <div class="cityPreview"
             style="background:${city.sky}">
             ${buildings}
        </div>
    `;
}


function citiesMenu(){

    hideAll();

    const list=
        document.getElementById("cityList");

    list.innerHTML="";

    document
        .getElementById("cityCoins")
        .textContent=
        playerData.coins.toLocaleString();

    cityData.forEach((city,index)=>{

        const card=
            document.createElement("div");

        card.className="card";

        const unlocked=
            playerData.cities.includes(index);

        card.innerHTML=`

            <div class="preview">
                ${drawCityPreview(city)}
            </div>

            <h3>${city.name}</h3>

            <p>
                ${city.min}–${city.max} Coins pro Münzgruppe
            </p>

            <p>
                ${
                    city.price===0
                    ?"KOSTENLOS"
                    :city.price.toLocaleString()+" Coins"
                }
            </p>
        `;

        const button=
            document.createElement("button");

        if(unlocked){

            button.textContent=
                playerData.city===index
                ?"AKTIV"
                :"AUSWÄHLEN";

            button.onclick=()=>{

                playerData.city=index;

                save();

                citiesMenu();
            };

        }else{

            button.textContent="FREISCHALTEN";

            button.onclick=()=>{

                if(playerData.coins<city.price){

                    alert("Nicht genug Coins.");

                    return;
                }

                playerData.coins-=city.price;

                playerData.cities.push(index);

                playerData.city=index;

                save();

                citiesMenu();
            };
        }

        card.appendChild(button);

        list.appendChild(card);
    });

    document
        .getElementById("cities")
        .classList.remove("hidden");
}


/* =====================================================
   ADMIN CODES
===================================================== */

function codeMenu(){

    hideAll();

    document.getElementById("secretCode").value="";

    document.getElementById("codeMsg").textContent="";

    document
        .getElementById("codes")
        .classList.remove("hidden");
}


function redeemCode(){

    const code=
        document.getElementById("secretCode").value.trim();

    const msg=
        document.getElementById("codeMsg");

    if(code==="110"){

        playerData.coins+=1000000;

        playerData.highscore=
            Math.max(playerData.highscore,1000000);

        playerData.characters=
            charData.map((_,i)=>i);

        playerData.cities=
            cityData.map((_,i)=>i);

        msg.textContent=
            "Code 110 aktiviert.";

    }else if(code==="112"){

        playerData.coins+=50000;

        playerData.highscore=
            Math.max(playerData.highscore,500000);

        playerData.characters=[
            0,1,2,3,4
        ];

        playerData.cities=[
            0,1
        ];

        msg.textContent=
            "Code 112 aktiviert.";

    }else{

        msg.textContent=
            "Falscher Code.";
    }

    document
        .getElementById("secretCode")
        .value="";

    save();
}


/* =====================================================
   RANGLISTE
===================================================== */

function updateRankingStorage(){

    let players=
        JSON.parse(
            localStorage.getItem("cubeRushPlayers")||"[]"
        );

    const existing=
        players.find(
            p=>p.username===playerData.username
        );

    if(existing){

        existing.highscore=
            playerData.highscore;

    }else{

        players.push({
            username:playerData.username,
            highscore:playerData.highscore
        });
    }

    players.sort(
        (a,b)=>b.highscore-a.highscore
    );

    localStorage.setItem(
        "cubeRushPlayers",
        JSON.stringify(players)
    );
}


function rankingMenu(){

    hideAll();

    updateRankingStorage();

    const list=
        document.getElementById("rankingList");

    list.innerHTML="";

    let players=
        JSON.parse(
            localStorage.getItem("cubeRushPlayers")||"[]"
        );

    players.sort(
        (a,b)=>b.highscore-a.highscore
    );

    players.slice(0,50).forEach((p,i)=>{

        const row=
            document.createElement("div");

        row.className="rank";

        row.innerHTML=`
            <span>
                ${i+1}. ${p.username}
            </span>

            <b>
                ${p.highscore.toLocaleString()}
            </b>
        `;

        list.appendChild(row);
    });

    document
        .getElementById("ranking")
        .classList.remove("hidden");
}


/* =====================================================
   GAME
===================================================== */

const canvas=
    document.getElementById("game");

const ctx=
    canvas.getContext("2d");

function resize(){

    canvas.width=innerWidth;
    canvas.height=innerHeight;
}

addEventListener("resize",resize);

resize();

let running=false;
let paused=false;

let score=0;
let runCoins=0;

let lane=0;
let playerX=0;

let objects=[];

let speed=250;

let last=performance.now();

let magnet=0;
let doubleCoins=0;

let powerTimer=8;

let coinTimer=0;
let carTimer=0;


/* =====================================================
   STRASSENBREITE
===================================================== */

function road(){

    const width=
        Math.min(innerWidth*.84,600);

    return{
        left:(innerWidth-width)/2,
        width
    };
}


function laneX(n){

    const r=road();

    return r.left+
        r.width*(n+1.5)/3;
}


/* =====================================================
   SPIEL START
===================================================== */

function startGame(){

    hideAll();

    running=true;
    paused=false;

    score=0;
    runCoins=0;

    lane=0;

    playerX=laneX(0);

    objects=[];

    speed=250;

    magnet=0;
    doubleCoins=0;

    powerTimer=8;

    coinTimer=.5;
    carTimer=1;

    document
        .getElementById("hud")
        .style.display="flex";

    document
        .getElementById("pause")
        .style.display="block";

    last=performance.now();
}


/* =====================================================
   WELT ZEICHNEN
===================================================== */

function drawWorld(){

    const r=road();

    const city=
        cityData[playerData.city];

    /* HIMMEL */

    const gradient=
        ctx.createLinearGradient(
            0,0,
            0,innerHeight
        );

    gradient.addColorStop(
        0,
        city.sky
    );

    gradient.addColorStop(
        1,
        "#02040a"
    );

    ctx.fillStyle=gradient;

    ctx.fillRect(
        0,0,
        innerWidth,
        innerHeight
    );


    /* GEBÄUDE */

    const buildingColor=
        city.building;

    for(let side=0;side<2;side++){

        for(let i=0;i<10;i++){

            const w=
                35+
                ((i*17)%55);

            const h=
                80+
                ((i*53)%180);

            const x=
                side===0
                ?i*65-w
                :innerWidth-i*65;

            const y=
                innerHeight*.45-h;

            ctx.fillStyle=
                buildingColor;

            ctx.fillRect(
                x,
                y,
                w,
                h
            );

            /* FENSTER */

            ctx.fillStyle=
                city.light;

            for(
                let wy=y+12;
                wy<y+h-10;
                wy+=20
            ){

                ctx.fillRect(
                    x+8,
                    wy,
                    5,
                    7
                );

                if(w>45){

                    ctx.fillRect(
                        x+w-15,
                        wy,
                        5,
                        7
                    );
                }
            }
        }
    }


    /* STRASSE */

    ctx.fillStyle=
        city.road;

    ctx.fillRect(
        r.left,
        0,
        r.width,
        innerHeight
    );


    /* STRASSENRAND */

    ctx.fillStyle=
        city.light;

    ctx.fillRect(
        r.left,
        0,
        3,
        innerHeight
    );

    ctx.fillRect(
        r.left+r.width-3,
        0,
        3,
        innerHeight
    );


    /* SPUREN */

    const lw=
        r.width/3;

    const offset=
        (score*5)%100;

    ctx.fillStyle="#ffffff55";

    for(
        let y=-100+offset;
        y<innerHeight;
        y+=100
    ){

        ctx.fillRect(
            r.left+lw-2,
            y,
            4,
            55
        );

        ctx.fillRect(
            r.left+lw*2-2,
            y,
            4,
            55
        );
    }
}


/* =====================================================
   SPIELER
===================================================== */

function drawPlayer(){

    const y=
        innerHeight*.74;

    const char=
        charData[playerData.character];

    ctx.save();

    ctx.translate(
        playerX,
        y
    );

    ctx.fillStyle=
        char[2];

    ctx.shadowColor=
        char[2];

    ctx.shadowBlur=28;

    ctx.beginPath();

    ctx.roundRect(
        -28,
        -28,
        56,
        56,
        10
    );

    ctx.fill();

    ctx.shadowBlur=0;

    /* AUGEN */

    ctx.fillStyle="#061018";

    ctx.fillRect(
        -17,
        -13,
        11,
        7
    );

    ctx.fillRect(
        6,
        -13,
        11,
        7
    );

    ctx.restore();
}


/* =====================================================
   AUTOS
===================================================== */

function spawnCar(){

    /*
       WICHTIG:
       Autos werden nur erzeugt,
       wenn ausreichend Abstand
       zu anderen Autos besteht.
    */

    const availableLanes=[-1,0,1];

    availableLanes.sort(
        ()=>Math.random()-.5
    );

    for(const selectedLane of availableLanes){

        const tooClose=
            objects.some(o=>
                o.type==="car" &&
                o.lane===selectedLane &&
                o.y<220
            );

        if(tooClose) continue;

        objects.push({

            type:"car",

            lane:selectedLane,

            y:-150,

            color:[
                "#ff1744",
                "#168cff",
                "#ffffff",
                "#ff9900",
                "#8b4dff"
            ][
                Math.floor(Math.random()*5)
            ]
        });

        return;
    }
}


function drawCar(car){

    const x=
        laneX(car.lane);

    ctx.save();

    ctx.translate(
        x,
        car.y
    );

    ctx.fillStyle=
        car.color;

    ctx.shadowColor=
        car.color;

    ctx.shadowBlur=18;

    ctx.beginPath();

    ctx.roundRect(
        -32,
        -50,
        64,
        100,
        10
    );

    ctx.fill();

    ctx.shadowBlur=0;

    /* SCHEIBE */

    ctx.fillStyle="#102331";

    ctx.beginPath();

    ctx.roundRect(
        -21,
        -31,
        42,
        28,
        7
    );

    ctx.fill();

    /* LICHTER */

    ctx.fillStyle="#ffffff";

    ctx.fillRect(
        -22,
        28,
        14,
        7
    );

    ctx.fillRect(
        8,
        28,
        14,
        7
    );

    ctx.restore();
}


/* =====================================================
   MÜNZEN
===================================================== */

function spawnCoins(){

    const city=
        cityData[playerData.city];

    const amount=
        city.min+
        Math.floor(
            Math.random()*
            (
                city.max-
                city.min+
                1
            )
        );

    let selectedLane=
        Math.floor(Math.random()*3)-1;

    /*
       Münzen werden in mehreren Mustern erzeugt.
    */

    const pattern=
        Math.floor(Math.random()*4);

    for(let i=0;i<amount;i++){

        let coinLane=selectedLane;

        if(pattern===1){

            coinLane=
                i%2===0
                ?selectedLane
                :Math.max(-1,
                    Math.min(1,
                        selectedLane+(i%3===0?1:-1)
                    )
                );
        }

        if(pattern===2){

            coinLane=
                Math.max(
                    -1,
                    Math.min(
                        1,
                        selectedLane+
                        Math.round(
                            Math.sin(i)*1
                        )
                    )
                );
        }

        if(pattern===3){

            coinLane=
                [-1,0,1][i%3];
        }

        /*
           Münze darf nicht direkt
           auf einem Auto landen.
        */

        const coinY=
            -40-i*55;

        const blocked=
            objects.some(o=>
                o.type==="car" &&
                o.lane===coinLane &&
                Math.abs(o.y-coinY)<150
            );

        if(!blocked){

            objects.push({
                type:"coin",
                lane:coinLane,
                y:coinY
            });
        }
    }
}


function drawCoin(o,time){

    let x=
        laneX(o.lane);

    let y=o.y;

    if(magnet>0){

        x+=
            (playerX-x)*.18;

        y+=
            (innerHeight*.74-y)*.18;
    }

    const scale=
        .55+
        Math.abs(
            Math.cos(time*.008)
        )*.45;

    ctx.save();

    ctx.translate(
        x,
        y
    );

    ctx.scale(
        scale,
        1
    );

    ctx.fillStyle="#ffd900";

    ctx.shadowColor="#ffd900";

    ctx.shadowBlur=20;

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        15,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.fillStyle="#7a5200";

    ctx.fillRect(
        -4,
        -8,
        8,
        16
    );

    ctx.restore();
}


/* =====================================================
   POWERUPS
===================================================== */

function spawnPower(){

    objects.push({

        type:
            Math.random()<.5
            ?"magnet"
            :"double",

        lane:
            Math.floor(Math.random()*3)-1,

        y:-80
    });
}


function drawPower(o){

    const x=
        laneX(o.lane);

    ctx.save();

    ctx.translate(
        x,
        o.y
    );

    ctx.lineWidth=5;

    ctx.strokeStyle=
        o.type==="magnet"
        ?"cyan"
        :"yellow";

    ctx.shadowColor=
        ctx.strokeStyle;

    ctx.shadowBlur=20;

    if(o.type==="magnet"){

        ctx.beginPath();

        ctx.arc(
            0,
            0,
            20,
            0,
            Math.PI
        );

        ctx.stroke();

        ctx.fillStyle="cyan";

        ctx.fillRect(
            -20,
            -2,
            8,
            15
        );

        ctx.fillRect(
            12,
            -2,
            8,
            15
        );

    }else{

        ctx.fillStyle="#ffe000";

        ctx.font="bold 30px Arial";

        ctx.textAlign="center";

        ctx.textBaseline="middle";

        ctx.fillText(
            "x2",
            0,
            0
        );
    }

    ctx.restore();
}


/* =====================================================
   UPDATE
===================================================== */

function update(dt){

    if(!running||paused) return;

    const seconds=
        dt/1000;

    score+=seconds*10;

    speed=
        Math.min(
            700,
            speed+seconds*4
        );


    playerX+=
        (
            laneX(lane)-playerX
        )*
        Math.min(
            1,
            seconds*12
        );


    if(magnet>0)
        magnet-=seconds;

    if(doubleCoins>0)
        doubleCoins-=seconds;


    /* POWER TIMER */

    powerTimer-=seconds;

    if(powerTimer<=0){

        spawnPower();

        powerTimer=
            15+
            Math.random()*15;
    }


    /* MÜNZEN STÄNDIG */

    coinTimer-=seconds;

    if(coinTimer<=0){

        spawnCoins();

        coinTimer=
            .8+
            Math.random()*.9;
    }


    /* AUTOS MIT ABSTAND */

    carTimer-=seconds;

    if(carTimer<=0){

        spawnCar();

        /*
           Größerer Abstand bei hoher Geschwindigkeit.
        */

        carTimer=
            Math.max(
                .75,
                1.35-
                speed/1200
            );
    }


    /* OBJEKTE */

    for(
        let i=objects.length-1;
        i>=0;
        i--
    ){

        const o=
            objects[i];

        o.y+=
            speed*seconds;


        if(
            o.y>
            innerHeight+180
        ){

            objects.splice(i,1);

            continue;
        }


        /* POWERUP */

        if(
            (
                o.type==="magnet"||
                o.type==="double"
            )&&
            Math.abs(
                o.y-
                innerHeight*.74
            )<55&&
            Math.abs(
                laneX(o.lane)-
                playerX
            )<48
        ){

            if(o.type==="magnet")
                magnet=30;

            if(o.type==="double")
                doubleCoins=30;

            objects.splice(i,1);

            continue;
        }


        /* MAGNET */

        if(
            o.type==="coin"&&
            magnet>0
        ){

            o.lane=lane;
        }


        /* COIN */

        if(
            o.type==="coin"&&
            Math.abs(
                o.y-
                innerHeight*.74
            )<50&&
            Math.abs(
                laneX(o.lane)-
                playerX
            )<48
        ){

            runCoins+=
                doubleCoins>0
                ?2
                :1;

            objects.splice(i,1);

            continue;
        }


        /* AUTO */

        if(
            o.type==="car"&&
            Math.abs(
                o.y-
                innerHeight*.74
            )<65&&
            Math.abs(
                laneX(o.lane)-
                playerX
            )<45
        ){

            endGame();

            return;
        }
    }


    /* HUD */

    document
        .getElementById("score")
        .textContent=
        Math.floor(score).toLocaleString();

    document
        .getElementById("coins")
        .textContent=
        (
            playerData.coins+
            runCoins
        ).toLocaleString();

    document
        .getElementById("cityName")
        .textContent=
        cityData[playerData.city].name;


    let powers=[];

    if(magnet>0)
        powers.push(
            "MAGNET "+Math.ceil(magnet)+"s"
        );

    if(doubleCoins>0)
        powers.push(
            "COINS x2 "+Math.ceil(doubleCoins)+"s"
        );

    document
        .getElementById("powerHud")
        .textContent=
        powers.join(" | ");
}


/* =====================================================
   GAME LOOP
===================================================== */

function loop(time){

    const dt=
        Math.min(
            40,
            time-last
        );

    last=time;

    drawWorld();

    objects.forEach(o=>{

        if(o.type==="car")
            drawCar(o);

        else if(o.type==="coin")
            drawCoin(o,time);

        else
            drawPower(o);
    });

    drawPlayer();

    update(dt);

    requestAnimationFrame(loop);
}

requestAnimationFrame(loop);


/* =====================================================
   STEUERUNG
===================================================== */

document.addEventListener(
    "keydown",
    e=>{

        if(!running||paused)
            return;

        if(e.key==="ArrowLeft"){

            lane=
                Math.max(
                    -1,
                    lane-1
                );
        }

        if(e.key==="ArrowRight"){

            lane=
                Math.min(
                    1,
                    lane+1
                );
        }
    }
);


let touchStart=0;

canvas.addEventListener(
    "touchstart",
    e=>{

        if(!running||paused)
            return;

        touchStart=
            e.changedTouches[0].clientX;
    },
    {passive:true}
);


canvas.addEventListener(
    "touchend",
    e=>{

        if(!running||paused)
            return;

        const end=
            e.changedTouches[0].clientX;

        const difference=
            end-touchStart;

        if(
            Math.abs(difference)<30
        )
            return;

        if(difference>0){

            lane=
                Math.min(
                    1,
                    lane+1
                );

        }else{

            lane=
                Math.max(
                    -1,
                    lane-1
                );
        }
    },
    {passive:true}
);


/* =====================================================
   PAUSE
===================================================== */

document
    .getElementById("pause")
    .onclick=()=>{

        if(!running)
            return;

        paused=true;

        document
            .getElementById("pause")
            .style.display="none";

        document
            .getElementById("pauseScreen")
            .classList.remove("hidden");
    };


function resumeGame(){

    paused=false;

    document
        .getElementById("pauseScreen")
        .classList.add("hidden");

    document
        .getElementById("pause")
        .style.display="block";

    last=performance.now();
}


function quitGame(){

    running=false;

    document
        .getElementById("pauseScreen")
        .classList.add("hidden");

    document
        .getElementById("hud")
        .style.display="none";

    document
        .getElementById("pause")
        .style.display="none";

    showMenu();
}


/* =====================================================
   GAME OVER
===================================================== */

function endGame(){

    if(!running)
        return;

    running=false;

    playerData.coins+=runCoins;

    const finalScore=
        Math.floor(score);

    if(
        finalScore>
        playerData.highscore
    ){

        playerData.highscore=
            finalScore;
    }

    save();

    document
        .getElementById("hud")
        .style.display="none";

    document
        .getElementById("pause")
        .style.display="none";

    document
        .getElementById("gameOverText")
        .innerHTML=`

        <p>
            SCORE:
            <b>${finalScore.toLocaleString()}</b>
        </p>

        <p>
            COINS:
            <b>${runCoins.toLocaleString()}</b>
        </p>

        <p>
            HIGHSCORE:
            <b>${playerData.highscore.toLocaleString()}</b>
        </p>

        `;

    document
        .getElementById("gameOver")
        .classList.remove("hidden");
}


function restartGame(){

    document
        .getElementById("gameOver")
        .classList.add("hidden");

    startGame();
}


/* =====================================================
   START
===================================================== */

if(load()){

    showMenu();

}

</script>

</body>
</html>
