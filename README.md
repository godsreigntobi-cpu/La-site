# La-site
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Toby | Elite System</title>

<style>
body{
    margin:0;
    font-family:monospace;
    background:black;
    color:#00ffcc;
    overflow:hidden;
}

/* MATRIX BACKGROUND */
canvas{
    position:fixed;
    top:0;
    left:0;
    z-index:-1;
}

/* LOCKSCREEN */
#lockscreen{
    position:absolute;
    top:0;
    left:0;
    width:100%;
    height:100%;
    background:black;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    color:#00ffcc;
    transition:1s;
    z-index:10;
    text-align:center;
}

#lockscreen.swiped{
    top:-100%;
}

/* Swipe hint */
.swipe-hint{
    margin-top:20px;
    font-size:14px;
    color:gray;
    animation:blink 1.5s infinite;
}

@keyframes blink{
    0%,100%{opacity:1;}
    50%{opacity:0;}
}

/* Boot Screen */
.boot{
    height:100vh;
    display:flex;
    justify-content:center;
    align-items:center;
    flex-direction:column;
}

.hidden{display:none;}

/* Face Scan */
.scan-box{
    width:150px;
    height:150px;
    border:2px solid #00ffcc;
    position:relative;
    margin-top:20px;
}

.scan-line{
    width:100%;
    height:2px;
    background:#00ffcc;
    position:absolute;
    animation:scan 2s infinite;
}

@keyframes scan{
    0%{top:0;}
    100%{top:100%;}
}

/* OS Layout */
.os{
    padding:15px;
}

.status{
    display:flex;
    justify-content:space-between;
    font-size:12px;
    color:gray;
}

.header{
    text-align:center;
    margin:10px 0;
    color:gold;
    text-shadow:0 0 10px gold;
}

/* Apps Grid */
.apps{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:15px;
    margin-top:20px;
}

.app{
    background:rgba(0,255,204,0.1);
    border:1px solid #00ffcc;
    padding:20px;
    text-align:center;
    border-radius:10px;
    cursor:pointer;
    transition:0.3s;
}

.app:hover{
    background:#00ffcc;
    color:black;
}

/* Modal */
.modal{
    position:fixed;
    top:0;
    left:0;
    width:100%;
    height:100%;
    background:rgba(0,0,0,0.95);
    display:none;
    justify-content:center;
    align-items:center;
}

.modal-content{
    background:black;
    border:1px solid gold;
    padding:30px;
    width:300px;
    text-align:center;
    color:white;
    max-height:80vh;
    overflow:auto;
}

/* Progress bars */
.bar{
    background:#222;
    height:8px;
    margin-top:5px;
}

.fill{
    height:100%;
    background:#00ffcc;
}

/* Buttons */
button{
    background:black;
    color:#00ffcc;
    border:1px solid #00ffcc;
    padding:8px 15px;
    margin-top:10px;
    cursor:pointer;
    border-radius:5px;
}

button:hover{
    background:#00ffcc;
    color:black;
}

/* Typing cursor */
.cursor{
    display:inline-block;
    width:10px;
    background:#00ffcc;
    animation:blink 1s infinite;
}

/* Confetti */
.confetti{
    position:absolute;
    width:8px;
    height:8px;
    animation:fall 4s linear infinite;
}

@keyframes fall{
    0%{transform:translateY(-10vh);}
    100%{transform:translateY(110vh);}
}
</style>
</head>

<body>

<canvas id="matrix"></canvas>

<!-- LOCKSCREEN -->
<div id="lockscreen">
<h2>🔒 Toby.OS Secure</h2>
<p>Swipe up to unlock</p>
<div class="swipe-hint">↑ swipe ↑</div>
</div>

<!-- BOOT -->
<div class="boot" id="boot">
    <h2>Initializing Toby.OS...</h2>
    <div class="scan-box">
        <div class="scan-line"></div>
    </div>
</div>

<!-- OS -->
<div class="os hidden" id="os">

<div class="status">
<span>📶 5G</span>
<span>🔋 100%</span>
<span id="time"></span>
</div>

<h2 class="header">TOBY • TRADER • CEO</h2>

<div class="apps">
<div class="app" onclick="openApp('profile')">👤 Profile</div>
<div class="app" onclick="openApp('market')">📈 Market</div>
<div class="app" onclick="openApp('skills')">💼 Skills</div>
<div class="app" onclick="openApp('vision')">🧠 Vision</div>
<div class="app" onclick="openApp('contact')">📞 Contact</div>
<div class="app" onclick="openApp('consult')">💳 Book</div>
</div>

</div>

<!-- Modal -->
<div class="modal" id="modal">
<div class="modal-content" id="modalContent"></div>
</div>

<script>

/* MATRIX */
let canvas=document.getElementById("matrix");
let ctx=canvas.getContext("2d");
canvas.height=window.innerHeight;
canvas.width=window.innerWidth;
let letters="01";
letters=letters.split("");
let fontSize=14;
let columns=canvas.width/fontSize;
let drops=[];
for(let x=0;x<columns;x++) drops[x]=1;
function draw(){
ctx.fillStyle="rgba(0,0,0,0.05)";
ctx.fillRect(0,0,canvas.width,canvas.height);
ctx.fillStyle="#00ffcc";
ctx.font=fontSize+"px monospace";
for(let i=0;i<drops.length;i++){
let text=letters[Math.floor(Math.random()*letters.length)];
ctx.fillText(text,i*fontSize,drops[i]*fontSize);
if(drops[i]*fontSize>canvas.height && Math.random()>0.975)
drops[i]=0;
drops[i]++;
}
}
setInterval(draw,33);

/* Boot and Lockscreen sequence */
setTimeout(()=>{
document.getElementById("boot").classList.add("hidden");
document.getElementById("lockscreen").style.top="0";
},4000);

/* Swipe unlock */
let lockscreen=document.getElementById("lockscreen");
lockscreen.addEventListener("click",()=>{
    lockscreen.classList.add("swiped");
    setTimeout(()=>{
        document.getElementById("os").classList.remove("hidden");
    },1000);
});

/* Live Time */
function updateTime(){
document.getElementById("time").innerHTML=new Date().toLocaleTimeString();
}
setInterval(updateTime,1000);

/* Open Apps */
function openApp(app){
let content="";
if(app=="profile"){
content="<h3>Profile.exe</h3><p>Trader | Student</p><p>Disciplined. Strategic. Scaling.</p>";
}
if(app=="market"){
content="<h3>Market Terminal</h3><p>> Chart Analysis Active</p><p>> Risk Managed</p><p>> Capital Growing</p>";
}
if(app=="skills"){
content="<h3>Skills</h3>\
Trading<div class='bar'><div class='fill' style='width:90%'></div></div>\
Discipline<div class='bar'><div class='fill' style='width:95%'></div></div>\
Strategy<div class='bar'><div class='fill' style='width:85%'></div></div>";
}
if(app=="vision"){
content="<h3>Vision.txt</h3><p>Future Billionaire CEO.</p><p>Building systems. Creating leverage.</p>";
}
if(app=="contact"){
content="<h3>Contact.access</h3><p>07045786748</p><button onclick='alert(\"Dialing...\")'>Connect</button>";
}
if(app=="consult"){
content="<h3>Book Consultation</h3><p>Premium Strategy Session</p><button onclick='alert(\"Consultation Booked\")'>Confirm</button>";
}
document.getElementById("modalContent").innerHTML=content+"<br><button onclick='closeModal()'>Close</button>";
document.getElementById("modal").style.display="flex";
}

function closeModal(){
document.getElementById("modal").style.display="none";
}

/* Confetti for success (Book) */
function confettiBurst(){
    for(let i=0;i<80;i++){
        let c=document.createElement("div");
        c.className="confetti";
        c.style.left=Math.random()*100+"vw";
        c.style.backgroundColor=`hsl(${Math.random()*360},100%,50%)`;
        document.body.appendChild(c);
        setTimeout(()=>c.remove(),4000);
    }
}

</script>

</body>
</html>
