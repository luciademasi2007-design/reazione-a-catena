<!DOCTYPE html>
<html lang="it">
<head>
<meta charset="UTF-8">
<title>Reazione a Catena – Cervelli in Play</title>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
<style>
body {
  font-family: 'Poppins', sans-serif;
  background: linear-gradient(120deg, #1f2937, #111827);
  color: white;
  text-align: center;
  margin: 0;
  animation: gradientBG 10s ease infinite alternate;
}

@keyframes gradientBG {
  0% { background: linear-gradient(120deg, #1f2937, #111827); }
  50% { background: linear-gradient(120deg, #111827, #1f2937); }
  100% { background: linear-gradient(120deg, #1f2937, #111827); }
}

.container { padding: 40px; }
h1 { margin-bottom: 5px; }
h2 { margin-top: 0; color: #22c55e; }

#parola {
  font-size: 3em;
  margin: 30px 0;
  min-height: 2em;
  text-shadow: 2px 2px 8px #22c55e;
  transition: transform 0.2s;
}
#parola:hover { transform: scale(1.1); }

.info { font-size: 1.2em; margin-bottom: 20px; }

button {
  font-size: 1.1em;
  padding: 12px 25px;
  margin: 8px;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}
button:hover { transform: scale(1.05); box-shadow: 0px 0px 15px rgba(255,255,255,0.3); }

#genera { background: linear-gradient(90deg, #22c55e, #16a34a); color: white; }
#reset { background: linear-gradient(90deg, #facc15, #eab308); color: black; }
#stop { background: linear-gradient(90deg, #ef4444, #b91c1c); color: white; }
#fullscreen { background: linear-gradient(90deg, #38bdf8, #0ea5e9); color: white; }
#mazzoSelector { margin-bottom: 20px; padding: 8px; font-size: 1.1em; border-radius: 8px; }
</style>
</head>
<body>

<div class="container">
<h1>Reazione a Catena</h1>
<h2>Cervelli in Play</h2>

<select id="mazzoSelector" onchange="selezionaMazzo()">
  <option value="facile">Facile – Qualificazioni</option>
  <option value="medio">Medio – Semifinale</option>
  <option value="difficile">Difficile – Finale</option>
</select>

<div class="info">
⏱ Tempo: <span id="tempo">60</span>s | 🔢 Parole: <span id="conteggio">0</span> | 📦 Rimaste: <span id="rimaste">—</span>
</div>

<div id="parola">Seleziona un mazzo e premi “Genera parola”</div>

<button id="genera" onclick="generaParola()">Genera parola ▶️</button>
<button id="stop" onclick="stopTimer()">Stop ⏸️</button>
<button id="reset" onclick="resetGioco()">Reset 🔄</button>
<button id="fullscreen" onclick="fullscreen()">Schermo intero 🖥️</button>
</div>

<script>
// Tre mazzi bilanciati con 500 parole uniche ciascuno (qui esempio ridotto, completare fino a 500 parole)
let mazzi = {
facile: [
"Acqua","Albero","Aereo","Amico","Anello","Animale","Aria","Asilo","Auto","Bambino",
"Barca","Bicicletta","Blocco","Borsa","Casa","Cielo","Città","Computer","Cucchiaio","Cuore",
"Dado","Denaro","Dentista","Fiore","Finestra","Fiaba","Frutta","Gioco","Gatto","Gelato",
"Giardino","Lampada","Letto","Libro","Luna","Macchina","Matita","Medico","Mela","Montagna",
"Orologio","Pane","Penna","Pesce","Pianeta","Porta","Sedia","Scuola","Sole","Strada"
 // continuare fino a 500
],
medio: [
"Avventura","Battaglia","Curiosità","Decisione","Emozione","Esplorazione","Esperimento","Fantasia","Felicità","Forza",
"Giocattolo","Idea","Immaginazione","Indagine","Influenza","Invenzione","Laboratorio","Lezione","Memoria","Motivazione",
"Notizia","Obiettivo","Organizzazione","Passione","Pensiero","Percorso","Progetto","Riflessione","Risultato","Sfida",
"Simbolo","Strategia","Successo","Tecnologia","Teoria","Tempo","Universo","Valore","Vittoria","Viaggio"
 // continuare fino a 500
],
difficile: [
"Abisso","Allegoria","Analogia","Astrazione","Causalità","Coerenza","Complessità","Congettura","Contemplazione","Correlazione",
"Deduzione","Dilemma","Discrepanza","Elaborazione","Enigma","Epistemologia","Equilibrio","Evoluzione","Esperienza","Fantasia",
"Fluidità","Globalizzazione","Immaginazione","Inferenza","Innovazione","Intuizione","Metamorfosi","Paradosso","Pluralità","Prospettiva",
"Riflessione","Sperimentazione","Strategia Avanzata","Sublime","Trasformazione","Universalità","Visione","Volontà","Zigzag","Ottimizzazione"
 // continuare fino a 500
]
};

let parole = [], tempo = 60, timer = null, punteggio = 0;

function selezionaMazzo() {
  let selezione = document.getElementById("mazzoSelector").value;
  parole = [...mazzi[selezione]];
  parole.sort(()=>Math.random()-0.5);
  tempo = 60; punteggio = 0; timer = null;
  document.getElementById("tempo").textContent = tempo;
  document.getElementById("conteggio").textContent = punteggio;
  document.getElementById("rimaste").textContent = parole.length;
  document.getElementById("parola").textContent = "Premi “Genera parola”";
}

function generaParola() {
  if(!timer) avviaTimer();
  if(parole.length > 0 && tempo > 0){
    let idx = Math.floor(Math.random() * parole.length);
    let parola = parole.splice(idx, 1)[0];
    let parolaDiv = document.getElementById("parola");
    parolaDiv.textContent = parola;
    parolaDiv.style.transform = "scale(1.3)";
    setTimeout(()=>{ parolaDiv.style.transform = "scale(1)"; }, 150);
    punteggio++;
    document.getElementById("conteggio").textContent = punteggio;
    document.getElementById("rimaste").textContent = parole.length;
  }
}

function avviaTimer() {
  let start = Date.now() - (60 - tempo)*1000;
  timer = setInterval(()=>{
    let elapsed = Math.floor((Date.now() - start)/1000);
    let rimanente = 60 - elapsed;
    tempo = rimanente;
    if(rimanente >= 0){
      document.getElementById("tempo").textContent = rimanente;
    }
    if(rimanente <= 0) fine();
  }, 200);
}

function stopTimer() {
  clearInterval(timer);
  timer = null;
  document.getElementById("parola").textContent += " ⏸️";
}

function fine(){
  clearInterval(timer);
  timer = null;
  document.getElementById("parola").textContent = "⏹ TEMPO SCADUTO";
}

function resetGioco(){
  tempo = 60; punteggio = 0; parole.sort(()=>Math.random()-0.5);
  document.getElementById("tempo").textContent = tempo;
  document.getElementById("conteggio").textContent = punteggio;
  document.getElementById("rimaste").textContent = parole.length;
  document.getElementById("parola").textContent = "Premi “Genera parola”";
  clearInterval(timer); timer = null;
}

function fullscreen(){
  if(!document.fullscreenElement){document.documentElement.requestFullscreen();}
  else{document.exitFullscreen();}
}
</script>

</body>
</html>
