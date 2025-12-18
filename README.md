<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>Reazione a Catena – Cervelli in Play</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@600&family=Roboto&display=swap" rel="stylesheet">

<style>
body{
  margin:0;
  font-family:'Roboto',sans-serif;
  background:linear-gradient(135deg,#a8dadc,#457b9d);
  color:white;
  text-align:center;
}
h1{font-family:'Poppins';margin-top:20px;color:#38bdf8;}
h2{margin-top:0;color:#cdeff3;}
#parola{
  font-size:3em;
  margin:20px auto;
  padding:15px 30px;
  background:rgba(0,0,0,.25);
  border-radius:20px;
  display:inline-block;
  color:#ffd166;
}
button{
  border:none;
  border-radius:25px;
  padding:10px 20px;
  margin:6px;
  font-family:'Poppins';
  cursor:pointer;
}
#genera{background:#06d6a0}
#indovinata{background:#06b58d}
#scarta{background:#ef476f}
#reset{background:#ffd166;color:black}
#stop{background:#f77f00}
.info{
  margin:15px;
  padding:10px;
  background:rgba(0,0,0,.2);
  border-radius:15px;
}
#liste{
  display:flex;
  justify-content:center;
  gap:30px;
  flex-wrap:wrap;
}
.lista-box{
  background:rgba(0,0,0,.25);
  border-radius:20px;
  padding:15px;
  width:240px;
  max-height:200px;
  overflow:auto;
}
</style>
</head>

<body>

<h1>Reazione a Catena</h1>
<h2>Cervelli in Play</h2>

<select id="mazzoSelector" onchange="selezionaMazzo()">
  <option value="facile">Facile – Qualificazioni</option>
  <option value="medio">Medio – Semifinale</option>
  <option value="difficile">Difficile – Finale</option>
</select>

<div class="info">
⏱ <span id="tempo">60</span>s |
🔢 <span id="conteggio">0</span> |
📦 <span id="rimaste">—</span>
</div>

<div id="parola">Premi “Genera parola”</div>

<div>
  <button id="genera" onclick="generaParola()">Genera ▶️</button>
  <button id="indovinata" onclick="segnaIndovinata()">Indovinata ✅</button>
  <button id="scarta" onclick="segnaScartata()">Scarta ❌</button>
  <button id="stop" onclick="stopTimer()">Stop ⏸️</button>
  <button id="reset" onclick="resetGioco()">Reset tempo 🔄</button>
</div>

<div id="liste">
  <div class="lista-box">
    <h3>Indovinate</h3>
    <ul id="listaIndovinate"></ul>
  </div>
  <div class="lista-box">
    <h3>Scartate</h3>
    <ul id="listaScartate"></ul>
  </div>
</div>

<script>
// 🔹 MAZZI (esempio – puoi ampliarli fino a 500)
let facile=["miraggio","cascata","rifugio","eco","gemma","labirinto","aurora","sentiero","mosaico","arcobaleno"];
let medio=["strategia","intuizione","metafora","equilibrio","processo","dinamica","connessione","scenario","ipotesi","analisi"];
let difficile=["astrazione","ambiguità","paradosso","contingenza","entropia","dialettica","ricorsività","trascendenza","ontologia","nonlinearità"];

let parole=[];
let parolaCorrente="";
let punteggio=0;
let tempo=60;
let timer=null;

// 🔹 Selezione mazzo (carica SOLO una volta)
function selezionaMazzo(){
  const sel=document.getElementById("mazzoSelector").value;
  if(sel==="facile") parole=[...facile];
  if(sel==="medio") parole=[...medio];
  if(sel==="difficile") parole=[...difficile];
  parole.sort(()=>Math.random()-0.5);
  document.getElementById("rimaste").textContent=parole.length;
  resetGioco();
}

// 🔹 Genera parola
function generaParola(){
  if(!timer) avviaTimer();
  if(parole.length===0||tempo<=0) return;
  parolaCorrente=parole.pop();
  document.getElementById("parola").textContent=parolaCorrente;
  document.getElementById("rimaste").textContent=parole.length;
}

// 🔹 Indovinata
function segnaIndovinata(){
  if(!parolaCorrente) return;
  punteggio++;
  document.getElementById("conteggio").textContent=punteggio;
  document.getElementById("listaIndovinate").innerHTML+=`<li>${parolaCorrente}</li>`;
  parolaCorrente="";
  document.getElementById("parola").textContent="Genera ▶️";
}

// 🔹 Scartata
function segnaScartata(){
  if(!parolaCorrente) return;
  document.getElementById("listaScartate").innerHTML+=`<li>${parolaCorrente}</li>`;
  parolaCorrente="";
  document.getElementById("parola").textContent="Genera ▶️";
}

// 🔹 Timer
function avviaTimer(){
  timer=setInterval(()=>{
    tempo--;
    document.getElementById("tempo").textContent=tempo;
    if(tempo<=0){
      clearInterval(timer);
      timer=null;
      document.getElementById("parola").textContent="⏹ TEMPO!";
    }
  },1000);
}

// 🔹 STOP
function stopTimer(){
  clearInterval(timer);
  timer=null;
}

// 🔹 RESET SOLO TEMPO (NON parole)
function resetGioco(){
  clearInterval(timer);
  timer=null;
  tempo=60;
  document.getElementById("tempo").textContent=tempo;
  document.getElementById("parola").textContent="Genera ▶️";
}

// avvio
selezionaMazzo();
</script>

</body>
</html>
