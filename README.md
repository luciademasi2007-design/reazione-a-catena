<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>Reazione a Catena – Cervelli in Play</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@600&family=Roboto:wght@400&display=swap" rel="stylesheet">
<style>
body {
  font-family: 'Roboto', sans-serif;
  margin: 0;
  background: linear-gradient(135deg, #a8dadc, #457b9d);
  color: #ffffff;
  text-align: center;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}
h1 {
  font-family: 'Poppins', sans-serif;
  font-size: 3em;
  margin: 20px 0 5px 0;
  color: #38bdf8;
  text-shadow: 1px 1px 6px #00000050;
}
h2 {
  font-family: 'Poppins', sans-serif;
  font-size: 1.5em;
  margin: 0 0 20px 0;
  color: #a8dadc;
  text-shadow: 1px 1px 4px #00000050;
}
#parola {
  font-size: 3em;
  font-weight: 600;
  margin: 20px;
  min-height: 2em;
  color: #ffd166;
  background: rgba(0,0,0,0.2);
  padding: 15px 25px;
  border-radius: 20px;
  display: inline-block;
  transition: transform 0.3s;
}
#parola:hover { transform: scale(1.05); }
.info {
  font-size: 1.2em;
  margin-bottom: 20px;
  background: rgba(0,0,0,0.2);
  padding: 10px 20px;
  border-radius: 15px;
}
button {
  font-size: 1.05em;
  padding: 10px 20px;
  margin: 5px;
  border: none;
  border-radius: 25px;
  cursor: pointer;
  font-family: 'Poppins', sans-serif;
  transition: transform 0.2s, box-shadow 0.3s;
}
button:hover { transform: scale(1.05); box-shadow: 0 0 10px rgba(0,0,0,0.3); }
#genera { background: #06d6a0; color: white; }
#indovinata { background: #06b58d; color: white; }
#scarta { background: #ef476f; color: white; }
#stop { background: #f77f00; color: white; }
#reset { background: #ffd166; color: black; }
#fullscreen { background: #118ab2; color: white; }
#mazzoSelector { margin-bottom: 20px; padding: 10px 15px; font-size: 1.1em; border-radius: 20px; border: none; }
#liste {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  gap: 40px;
  flex-wrap: wrap;
}
.lista-box {
  max-height: 200px;
  width: 240px;
  overflow-y: auto;
  background: rgba(0,0,0,0.2);
  padding: 15px;
  border-radius: 20px;
}
.lista-box h3 { margin-top: 0; font-family: 'Poppins', sans-serif; }
ul li { margin: 3px 0; font-size: 0.95em; }
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
⏱ Tempo: <span id="tempo">60</span>s | 🔢 Indovinate: <span id="conteggio">0</span> | 📦 Rimaste: <span id="rimaste">—</span>
</div>

<div id="parola">Seleziona un mazzo e premi “Genera parola”</div>

<div>
  <button id="genera" onclick="generaParola()">Genera parola ▶️</button>
  <button id="indovinata" onclick="segnaIndovinata()">✅ Indovinata</button>
  <button id="scarta" onclick="segnaScartata()">❌ Scarta</button>
  <button id="stop" onclick="stopTimer()">Stop ⏸️</button>
  <button id="reset" onclick="resetGioco()">Reset 🔄</button>
  <button id="fullscreen" onclick="fullscreen()">Schermo intero 🖥️</button>
</div>

<div id="liste">
  <div class="lista-box">
    <h3>Parole Indovinate</h3>
    <ul id="listaIndovinate"></ul>
  </div>
  <div class="lista-box">
    <h3>Parole Scartate</h3>
    <ul id="listaScartate"></ul>
  </div>
</div>

<script>
// --- Mazzi (estratti, vanno completati con 500 parole ciascuno) ---
let facile = ["miraggio","cascata","giostra","rifugio","eco","gemma","labirinto","arcobaleno","mosaico","libreria"];
let medio = ["metafora","intuizione","strumento","progetto","tecnica","laboratorio","strategia","analisi","simbolo","esperimento"];
let difficile = ["ontologia","epistemologia","trascendenza","astrazione","metacognizione","heuristico","quantificazione","autocoscienza","dialettica","paradosso"];

// --- Variabili di gioco ---
let parole = [], tempo = 60, timer = null, punteggio = 0;
let parolaCorrente = "";

// --- Selezione mazzo e reset ---
function selezionaMazzo() {
  let sel = document.getElementById("mazzoSelector").value;
  if(sel === "facile") parole = [...facile];
  if(sel === "medio") parole = [...medio];
  if(sel === "difficile") parole = [...difficile];
  parole.sort(()=>Math.random() - 0.5);
  tempo = 60; punteggio = 0; timer = null; parolaCorrente = "";
  document.getElementById("tempo").textContent = tempo;
  document.getElementById("conteggio").textContent = punteggio;
  document.getElementById("rimaste").textContent = parole.length;
  document.getElementById("parola").textContent = "Premi 'Genera parola'";
  document.getElementById("listaIndovinate").innerHTML = "";
  document.getElementById("listaScartate").innerHTML = "";
}

// --- Generazione parola casuale ---
function generaParola() {
  if(!timer) avviaTimer();
  if(parole.length > 0 && tempo > 0){
    let idx = Math.floor(Math.random() * parole.length);
    parolaCorrente = parole.splice(idx,1)[0];
    document.getElementById("parola").textContent = parolaCorrente;
    document.getElementById("rimaste").textContent = parole.length;
  }
}

// --- Segna parola indovinata ---
function segnaIndovinata() {
  if(parolaCorrente){
    let li = document.createElement("li");
    li.textContent = parolaCorrente;
    document.getElementById("listaIndovinate").appendChild(li);
    punteggio++;
    document.getElementById("conteggio").textContent = punteggio;
    parolaCorrente = "";
    document.getElementById("parola").textContent = "Premi 'Genera parola'";
  }
}

// --- Segna parola scartata ---
function segnaScartata() {
  if(parolaCorrente){
    let li = document.createElement("li");
    li.textContent = parolaCorrente;
    document.getElementById("listaScartate").appendChild(li);
    parolaCorrente = "";
    document.getElementById("parola").textContent = "Premi 'Genera parola'";
  }
}

// --- Timer ---
function avviaTimer() {
  let start = Date.now() - (60 - tempo)*1000;
  timer = setInterval(()=>{
    let elapsed = Math.floor((Date.now() - start)/1000);
    let rem = 60 - elapsed;
    tempo = rem;
    if(rem >= 0) document.getElementById("tempo").textContent = rem;
    if(rem <= 0) fine();
  },200);
}

function stopTimer(){
  clearInterval(timer);
  timer = null;
}

function fine(){
  clearInterval(timer);
  timer = null;
  document.getElementById("parola").textContent = "⏹ TEMPO SCADUTO";
}

function resetGioco(){
  selezionaMazzo();
}

function fullscreen(){
  if(!document.fullscreenElement) document.documentElement.requestFullscreen();
  else document.exitFullscreen();
}
</script>

</body>
</html>
