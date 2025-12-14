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
  background: linear-gradient(135deg, #1f2937, #111827);
  color: #ffffff;
  text-align: center;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  animation: gradientBG 12s ease infinite alternate;
}
@keyframes gradientBG {
  0% { background: linear-gradient(135deg, #1f2937, #111827); }
  50% { background: linear-gradient(135deg, #111827, #1f2937); }
  100% { background: linear-gradient(135deg, #1f2937, #111827); }
}
h1 {
  font-family: 'Poppins', sans-serif;
  font-size: 3em;
  margin: 20px 0 5px 0;
  color: #22c55e;
}
h2 {
  font-family: 'Poppins', sans-serif;
  font-size: 1.5em;
  margin: 0 0 20px 0;
  color: #a5f3fc;
}
#parola {
  font-size: 3.5em;
  font-weight: 600;
  margin: 20px 20px;
  min-height: 2em;
  color: #facc15;
  transition: transform 0.3s;
}
#parola:hover { transform: scale(1.1); }
.info { font-size: 1.2em; margin-bottom: 20px; }
button {
  font-size: 1.05em;
  padding: 10px 20px;
  margin: 5px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-family: 'Poppins', sans-serif;
  transition: transform 0.2s, box-shadow 0.3s;
}
button:hover { transform: scale(1.05); box-shadow: 0 0 10px rgba(255,255,255,0.3); }
#genera { background: #22c55e; color: white; }
#indovinata { background: #16a34a; color: white; }
#scarta { background: #dc2626; color: white; }
#stop { background: #ef4444; color: white; }
#reset { background: #facc15; color: black; }
#fullscreen { background: #38bdf8; color: white; }
#mazzoSelector { margin-bottom: 20px; padding: 8px; font-size: 1.1em; border-radius: 8px; }
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
  background: rgba(0,0,0,0.4);
  padding: 10px;
  border-radius: 10px;
}
.lista-box h3 { margin-top: 0; font-family: 'Poppins', sans-serif; }
.lista-box ul { list-style: none; padding-left: 5px; text-align: left; }
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
// ——— Liste da 500 parole ciascuna ———

// — Facile (500 parole)
let facile = [
"acqua","albero","amico","aria","animale","asilo","auto","bagno","bambino","barca",
"bici","bicicletta","borsa","cane","casa","cielo","città","colore","computer","cuore",
"cucchiaio","dado","dentista","fiore","finestra","fiume","frutta","gas","gatto","gelato",
"gioco","giardino","igloo","isola","labbro","lago","lampada","leone","letto","libro",
"luna","macchina","mano","mela","montagna","nave","notte","occhio","ombrello","pane",
"penna","pesce","piede","porta","rosa","sedia","sole","spiaggia","stella","strada",
"tavolo","telefono","tempesta","tenda","torre","torta","treno","tulipano","una","vento",
"verità","vino","vita","volpe","zaino","zebra","zuppa","zucchero","amore","attimo",
…continua fino a 500 parole uniche per livello, qui solo per esempio
];

// — Medio (500 parole)
let medio = [
"avventura","battaglia","cammino","campagna","casello","cassetto","centralina","chimica",
"cittadino","citazione","classe","clima","colazione","comando","commercio","comunità",
"competizione","compito","conferenza","confine","connessione","conoscenza","conquista",
"consapevole","costruzione","creatività","cultura","curiosità","decisione","dialogo",
"dirigente","disciplina","documento","domanda","duello","educazione","effetto","emozione",
"energia","esperienza","esplorazione","espressione","fantasia","fiducia","figura","filosofia",
…continua fino a 500 parole uniche per livello, qui solo per esempio
];

// — Difficile (500 parole)
let difficile = [
"abisso","allegoria","analogia","apprendimento","astensione","astrazione","ateismo",
"attenzione","causalità","cognizione","collegamento","complementare","complessità",
"congettura","contemplazione","contraddizione","coordinamento","deprivazione","derivazione",
"determinazione","dichiarazione","dissonanza","elaborazione","epistemologia","equilibrio",
"essenzialità","etimologia","evoluzione","esperimento","explicitazione","fenomenologia",
"formulazione","frammentazione","globalizzazione","ideologia","implicazione","interscambio",
"interpretazione","metodologia","mitigazione","ontologia","paradigma","preponderanza",
"proporzionalità","quantificazione","rappresentazione","razionalizzazione","semiotica",
…continua fino a 500 parole uniche per livello, qui solo per esempio
];

// IMPORTANTE: queste liste di esempio vanno *completate fino a 500 parole ciascuna* seguendo lo stesso formato,
// cioè stringhe senza duplicati in ogni array.

// ——— Funzioni di gioco ———
let parole = [], tempo = 60, timer = null, punteggio = 0;
let parolaCorrente = "";

function selezionaMazzo() {
  let sel = document.getElementById("mazzoSelector").value;
  if(sel === "facile") parole = [...facile];
  if(sel === "medio") parole = [...medio];
  if(sel === "difficile") parole = [...difficile];
  parole.sort(()=>Math.random() - 0.5);
  tempo = 60;
  punteggio = 0;
  timer = null;
  parolaCorrente = "";
  document.getElementById("tempo").textContent = tempo;
  document.getElementById("conteggio").textContent = punteggio;
  document.getElementById("rimaste").textContent = parole.length;
  document.getElementById("parola").textContent = "Premi 'Genera parola'";
  document.getElementById("listaIndovinate").innerHTML = "";
  document.getElementById("listaScartate").innerHTML = "";
}

function generaParola() {
  if(!timer) avviaTimer();
  if(parole.length > 0 && tempo > 0){
    let idx = Math.floor(Math.random() * parole.length);
    parolaCorrente = parole.splice(idx,1)[0];
    document.getElementById("parola").textContent = parolaCorrente;
    document.getElementById("rimaste").textContent = parole.length;
  }
}

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

function segnaScartata() {
  if(parolaCorrente){
    let li = document.createElement("li");
    li.textContent = parolaCorrente;
    document.getElementById("listaScartate").appendChild(li);
    parolaCorrente = "";
    document.getElementById("parola").textContent = "Premi 'Genera parola'";
  }
}

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
