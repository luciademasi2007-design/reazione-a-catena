<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>Reazione a Catena – Torneo Master</title>
<style>
body { font-family: Arial, sans-serif; background:#0b1220; color:white; text-align:center; margin:0; }
.container { padding:40px; }
h1 { margin-bottom:10px; }
#parola { font-size:4em; margin:40px 0; min-height:1.5em; }
.info { font-size:1.3em; margin-bottom:20px; }
button { font-size:1.1em; padding:15px 25px; margin:8px; border:none; border-radius:8px; cursor:pointer; }
#genera { background:#22c55e; } #reset { background:#eab308; } #fullscreen { background:#38bdf8; }
#mazzoSelector { margin-bottom:20px; padding:8px; font-size:1.1em; }
</style>
</head>
<body>

<div class="container">
<h1>🏆 Reazione a Catena – Torneo Master</h1>

<select id="mazzoSelector" onchange="selezionaMazzo()">
  <option value="facile">Mazzo Facile (Qualificazioni)</option>
  <option value="medio">Mazzo Medio (Semifinali)</option>
  <option value="difficile">Mazzo Difficile (Finale)</option>
</select>

<div class="info">
⏱ Tempo: <span id="tempo">60</span>s | 🔢 Parole: <span id="conteggio">0</span> | 📦 Rimaste: <span id="rimaste">—</span>
</div>

<div id="parola">Seleziona un mazzo e premi “Genera parola”</div>

<button id="genera" onclick="generaParola()">Genera parola</button>
<button id="reset" onclick="resetGioco()">Reset</button>
<button id="fullscreen" onclick="fullscreen()">Schermo intero</button>
</div>

<script>
// 🔊 TIC-TAC
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
function tic() { const osc=audioCtx.createOscillator(); osc.frequency.value=800; osc.connect(audioCtx.destination); osc.start(); osc.stop(audioCtx.currentTime+0.05); }

// 🟢 Mazzi parole
let mazzi = {
  facile: [
    "Inizio","Fine","Storia","Racconto","Segreto","Idea","Parola","Frase","Titolo","Tema",
    "Scelta","Errore","Tentativo","Successo","Sfida","Gioco","Turno","Finale","Vittoria","Sconfitta",
    "Tempo","Spazio","Notte","Giorno","Alba","Tramonto","Attesa","Momento","Occasione","Istante",
    "Strada","Viaggio","Percorso","Sentiero","Direzione","Traguardo","Confine","Limite","Centro","Margine"
  ],
  medio: [
    "Curiosità","Scoperta","Ricordo","Memoria","Crescita","Cambiamento","Svolta","Ritorno","Partenza",
    "Problema","Soluzione","Risposta","Domanda","Metodo","Strategia","Immagine","Simbolo","Segnale","Messaggio",
    "Codice","Schermo","Tecnologia","Rete","Connessione","Flusso","Energia","Forza","Equilibrio","Caos",
    "Controllo","Impulso","Azione","Reazione","Calma","Gioco di squadra","Tempo limite","Countdown",
    "Obiettivo","Sfondo","Battito","Respiro","Passaggio","Chiave"
  ],
  difficile: [
    "Metamorfosi","Interconnessione","Similitudine","Allegoria","Paradosso","Intuizione","Sperimentazione",
    "Evoluzione","Trasformazione","Universalità","Pluralità","Diversità","Inclusione","Analisi","Sintesi",
    "Interpretazione","Astrazione","Convergenza","Discrepanza","Contrapposizione","Prospettiva",
    "Complessità","Risoluzione","Deduzione","Inferenza","Ipotesi","Congettura","Validazione","Confutazione",
    "Determinismo","Probabilità","Causalità","Correlazione","Simmetria","Proporzione","Dimensione",
    "Relazione","Connessione","Reperimento","Classificazione","Organizzazione","Coordinamento",
    "Strategia Avanzata","Innovazione","Creatività Applicata","Visione Globale","Comunicazione Efficace",
    "Problem Solving","Pensiero Critico","Ragionamento Logico","Decisione Strategica","Gestione Tempo",
    "Ottimizzazione"
  ]
};

let parole=[], indice=0, tempo=60, timer=null, punteggio=0;

// Selezione mazzo
function selezionaMazzo() {
  let selezione = document.getElementById("mazzoSelector").value;
  parole = mazzi[selezione].slice();
  // mescola e porta a 500 parole
  while (parole.length<500) { parole.push(parole[Math.floor(Math.random()*parole.length)]); }
  parole.sort(()=>Math.random()-0.5);
  indice=0; tempo=60; punteggio=0; timer=null;
  document.getElementById("tempo").textContent=60;
  document.getElementById("conteggio").textContent=0;
  document.getElementById("rimaste").textContent=parole.length;
  document.getElementById("parola").textContent="Premi “Genera parola”";
  document.getElementById("genera").disabled=false;
}

function generaParola() {
  if (!timer) avviaTimer();
  if (indice<parole.length && tempo>0) {
    document.getElementById("parola").textContent=parole[indice];
    indice++; punteggio++;
    document.getElementById("conteggio").textContent=punteggio;
    document.getElementById("rimaste").textContent=parole.length-indice;
  }
}

function avviaTimer() {
  timer=setInterval(()=>{
    tempo--;
    document.getElementById("tempo").textContent=tempo;
    if (tempo<=10 && tempo>0) tic();
    if (tempo<=0) fine();
  },1000);
}

function fine() {
  clearInterval(timer);
  document.getElementById("parola").textContent="⏹ TEMPO SCADUTO";
}

function resetGioco() {
  indice=0; tempo=60; punteggio=0;
  parole.sort(()=>Math.random()-0.5);
  document.getElementById("tempo").textContent=60;
  document.getElementById("conteggio").textContent=0;
  document.getElementById("rimaste").textContent=parole.length;
  document.getElementById("parola").textContent="Premi “Genera parola”";
  clearInterval(timer); timer=null;
}

function fullscreen() {
  if(!document.fullscreenElement){document.documentElement.requestFullscreen();}
  else{document.exitFullscreen();}
}
</script>

</body>
</html>
