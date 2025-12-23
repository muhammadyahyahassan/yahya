# yahya
MY NAME IS MUHAMMAD YAHYA HASSAN
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>MKG STUDY</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{
  margin:0;
  font-family: Arial, sans-serif;
  background:#f5f9f7;
  color:#222;
}
header{
  text-align:center;
  padding:12px;
  font-weight:bold;
  background:#e8f5f0;
}
section{display:none;padding:15px;}
section.active{display:block;}

.bottom-nav{
  position:fixed;
  bottom:0;
  width:100%;
  display:flex;
  background:#ffffff;
  border-top:1px solid #ddd;
}
.bottom-nav button{
  flex:1;
  padding:10px;
  border:none;
  background:none;
  font-size:14px;
}

.card{
  background:#fff;
  padding:12px;
  margin:10px 0;
  border-radius:8px;
}

button.action{
  background:#4caf50;
  color:#fff;
  border:none;
  padding:8px 12px;
  margin-top:8px;
  border-radius:5px;
}

.copy-btn,.proof-btn{
  margin-right:6px;
  background:#ddd;
  border:none;
  padding:5px 8px;
  border-radius:4px;
}
textarea,input{
  width:100%;
  padding:8px;
  margin-top:6px;
}
.grid{
  display:grid;
  grid-template-columns:repeat(2,1fr);
  gap:10px;
}
.grid div{
  background:#fff;
  padding:15px;
  text-align:center;
  border-radius:8px;
}
</style>
</head>

<body>

<header>MKG STUDY</header>

<!-- SCAN -->
<section id="scan" class="active">
  <div class="card">
    <input type="file" accept="image/*" capture="environment" id="scanImage">
    <button class="action" onclick="solveImage()">Solve</button>
    <div id="scanResult"></div>
  </div>
</section>

<!-- CHAT -->
<section id="chat">
  <div class="card">
    <input id="chatInput" placeholder="Type your question">
    <button class="action" onclick="chatSolve()">Send</button>
    <div id="chatResult"></div>
  </div>
</section>

<!-- TOOLS -->
<section id="tools">
  <div class="grid">
    <div onclick="openScan()">Math</div>
    <div onclick="openScan()">All Subjects</div>
    <div onclick="openWrite()">Write</div>
    <div onclick="openHumanize()">Humanize</div>
  </div>
</section>

<!-- PROFILE -->
<section id="profile">
  <div class="card">
    <button onclick="showHistory()">History</button>
    <button onclick="showFav()">Favorites</button>
    <button onclick="clearHistory()">Clear Chat History</button>
    <button onclick="setLanguage()">Answer Language</button>
    <button onclick="showTerms()">Terms & Conditions</button>
    <button onclick="feedback()">Feedback</button>
    <button onclick="help()">Help</button>
  </div>
  <div id="profileContent"></div>
</section>

<!-- BOTTOM NAV -->
<div class="bottom-nav">
  <button onclick="nav('scan')">Scan</button>
  <button onclick="nav('chat')">Chat</button>
  <button onclick="nav('tools')">Tools</button>
  <button onclick="nav('profile')">Profile</button>
</div>

<script>
const GEMINI_API_KEY = "PASTE_YOUR_API_KEY_HERE";
let answerLanguage = "English";
let history = [];
let favorites = [];

function nav(id){
  document.querySelectorAll("section").forEach(s=>s.classList.remove("active"));
  document.getElementById(id).classList.add("active");
}

async function geminiRequest(prompt){
  const res = await fetch(
    "https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key="+GEMINI_API_KEY,
    {
      method:"POST",
      headers:{"Content-Type":"application/json"},
      body:JSON.stringify({
        contents:[{
          parts:[{text: prompt + "\nAnswer language: " + answerLanguage}]
        }]
      })
    }
  );
  const data = await res.json();
  return data.candidates?.[0]?.content?.parts?.[0]?.text || "";
}

async function chatSolve(){
  const q = document.getElementById("chatInput").value;
  const ans = await geminiRequest(q);
  history.push({q,ans});
  document.getElementById("chatResult").innerHTML = `
    <div class="card">
      ${ans}
      <br>
      <button class="copy-btn" onclick="copyText('${ans}')">Copy</button>
      <button class="proof-btn" onclick="proof('${q}')">Proof</button>
    </div>`;
}

async function solveImage(){
  const ans = await geminiRequest("Solve the question from image. Give only answer.");
  document.getElementById("scanResult").innerHTML = `
    <div class="card">
      ${ans}
      <br>
      <button class="copy-btn" onclick="copyText('${ans}')">Copy</button>
      <button class="proof-btn" onclick="proof('image question')">Proof</button>
    </div>`;
}

async function proof(q){
  const p = await geminiRequest("Explain step by step solution of: "+q);
  alert(p);
}

function copyText(t){
  navigator.clipboard.writeText(t);
}

function openScan(){ nav("scan"); }
function openWrite(){ alert("Writing section UI placeholder"); }
function openHumanize(){ alert("Humanize content placeholder"); }

function showHistory(){
  let html = "<div class='card'>";
  history.forEach(h=>{
    html+=`<p><b>${h.q}</b><br>${h.ans}</p>`;
  });
  html+="</div>";
  document.getElementById("profileContent").innerHTML=html;
}

function showFav(){
  let html="<div class='card'>";
  favorites.forEach(f=>html+=`<p>${f}</p>`);
  html+="</div>";
  document.getElementById("profileContent").innerHTML=html;
}

function clearHistory(){
  if(confirm("Clear all history?")){
    history=[];
    alert("Cleared");
  }
}

function setLanguage(){
  const l = prompt("Enter language (English, Hindi, Urdu etc)");
  if(l) answerLanguage = l;
}

function showTerms(){
  document.getElementById("profileContent").innerHTML =
  "<div class='card'>This app is for educational purposes only. No misuse allowed.</div>";
}

function feedback(){
  window.location.href="https://wa.me/923464018336?text=Hello%20I%20want%20to%20give%20feedback%20about%20MKG%20STUDY";
}

function help(){
  alert("Use Scan, Chat and Tools to solve questions.");
}
</script>

</body>
</html>
