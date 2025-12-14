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
let facile = 
["miraggio","cascata","rifugio","eco","gemma","labirinto","arcobaleno","mosaico","sentiero","aurora",
"farfalla","caverna","orizzonte","polvere","riflesso","fiaba","radura","specchio","lanterna","bussola",
"quercia","campana","goccia","chiave","scala","pensiero","candela","nuvola","tramonto","rosa",
"puzzle","clessidra","segnale","porta","cappello","quaderno","matita","righello","bottiglia","bicchiere",
"fragola","cioccolato","biscotto","finestra","lampada","cuscino","divano","uccello","coniglio","cavallo",
"pianeta","cometa","satellite","lago","fiume","deserto","grotta","collina","spiaggia","scogliera",
"sorgente","alba","notte","bagliore","sorriso","lacrima","meraviglia","sorpresa","curiosità","intuito",
"fantasia","creatività","ispirazione","progetto","strategia","ricordo","esperienza","avventura","sfida","gara",
"allenamento","vittoria","record","medaglia","trofeo","applauso","palco","concerto","orchestra","strumento",
"voce","attore","film","scena","dialogo","poesia","autore","libertà","giustizia","pace",
"speranza","fiducia","ombra","colore","giallo","viola","grigio","oro","argento","bronzo",
"telefono","computer","radio","console","microfono","pallone","bici","cerchio","scacchi","carte",
"ventaglio","ombrello","scarpa","giacca","braccialetto","borsa","valigia","piazza","borgo","confine",
"muro","ascensore","negozio","mercato","moneta","conto","scambio","favola","mito","leggenda",
"proverbio","sapore","profumo","essenza","sentimento","mente","spirito","anima","crescita","cambiamento",
"evoluzione","movimento","velocità","energia","equilibrio","spazio","dimensione","profondità","saltare","nuotare",
"guidare","ascoltare","vedere","toccare","gustare","imparare","studiare","scrivere","disegnare","leggere",
"capire","ragionare","immaginare","creare","costruire","trasformare","cercare","trovare","risposta","verità",

"sentinella","vortice","radar","binocolo","argine","viadotto","anfiteatro","loggia","baluardo","cisterna",
"scintilla","frammento","incrocio","bivio","passaggio","approdo","traccia","impronta","sigillo","percorso",
"respiro","battito","slancio","equilibrio","soglia","attesa","richiamo","segreto","enigma","indizio",
"connessione","intreccio","scenario","cornice","sfondo","sequenza","ritmo","armonia","contrasto","sfumatura",
"variazione","rifinitura","dettaglio","profilo","lineamento","struttura","forma","volume","curvatura","angolo",
"spigolo","superficie","trama","tessuto","filo","nodo","legame","ponte","varco","accesso",
"apertura","chiusura","meccanismo","ingranaggio","leva","interruttore","segnale","impulso","frequenza","eco",
"rifrazione","riflesso","ombra","penombra","bagliore","luccichio","scia","onda","vibrazione","flusso",

"equazione","formula","schema","modello","ipotesi","deduzione","intuizione","verifica","conferma","riscontro",
"analogia","connessione","associazione","categoria","insieme","sequenza","ordine","caos","equilibrio","asimmetria",
"variante","alternativa","opzione","scelta","decisione","priorità","obiettivo","traguardo","esito","risultato",
"tentativo","errore","correzione","aggiustamento","adattamento","progresso","avanzamento","passo","salto","svolta",
"momento","istante","durata","intervallo","attimo","cadenza","fretta","calma","pausa","ripresa",
"avvio","inizio","chiusura","conclusione","finale","apice","culmine","picco","base","fondamento"];
let medio = 
["intuizione","strategia","metafora","prospettiva","equilibrio","transizione","struttura","dinamica","interazione","connessione",
"sequenza","priorità","obiettivo","scenario","contesto","variante","alternativa","ipotesi","deduzione","verifica",
"analisi","sintesi","modello","schema","pattern","configurazione","meccanismo","processo","evoluzione","adattamento",
"progressione","cambiamento","trasformazione","svolta","passaggio","intervallo","momento","durata","frequenza","ritmo",
"cadenza","armonia","contrasto","sfumatura","gradiente","intensità","attenuazione","amplificazione","equilibrio","instabilità",
"tensione","rilascio","impulso","inerzia","accelerazione","resistenza","attrito","energia","potenziale","flusso",
"vibrazione","risonanza","oscillazione","onda","propagazione","rifrazione","diffusione","convergenza","divergenza","polarità",
"orientamento","direzione","traiettoria","percorso","bivio","snodo","incrocio","accesso","varco","soglia",
"confine","margine","limite","estensione","profondità","superficie","volume","densità","strato","livello",
"fondamento","struttura","impalcatura","architettura","reticolo","maglia","intreccio","legame","nodo","connessione",
"relazione","correlazione","dipendenza","interdipendenza","coerenza","incoerenza","ambiguità","paradosso","contraddizione","dubbio",
"ipotesi","probabilità","casualità","incertezza","previsione","stima","valutazione","criterio","parametro","variabile",
"costante","fattore","elemento","componente","modulo","unità","insieme","sistema","ecosistema","rete",
"interfaccia","canale","segnale","codice","messaggio","interpretazione","decodifica","significato","senso","valore",
"priorità","gerarchia","ordine","classificazione","categoria","tipologia","profilo","identità","ruolo","funzione",
"scopo","finalità","intento","motivazione","spinta","impulso","stimolo","reazione","risposta","feedback",
"controllo","regolazione","bilanciamento","ottimizzazione","efficienza","efficacia","prestazione","resa","risultato","esito",
"tentativo","esperimento","prova","verifica","conferma","smentita","errore","correzione","revisione","aggiustamento",
"adattamento","miglioramento","progresso","sviluppo","avanzamento","crescita","espansione","contrazione","riduzione","compressione",
"distensione","rilassamento","concentrazione","attenzione","focalizzazione","osservazione","percezione","consapevolezza","riflessione","ragionamento",
"deduzione","induzione","associazione","analogia","inferenza","astrazione","generalizzazione","particolarità","dettaglio","sfondo",
"cornice","inquadramento","scenario","ambientazione","contesto","situazione","circostanza","condizione","vincolo","opportunità",
"risorsa","strumento","mezzo","supporto","leva","fattore","driver","catalizzatore","ostacolo","barriera",
"resistenza","attraversamento","superamento","soluzione","compromesso","mediazione","accordo","convergenza","allineamento","sinergia",
"collaborazione","coordinamento","organizzazione","pianificazione","programmazione","tempistica","scadenza","priorità","urgenza","gestione",
"controllo","monitoraggio","valutazione","feedback","report","sintesi","resoconto","panoramica","visione","prospettiva",
"orizzonte","traguardo","obiettivo","risultato","impatto","effetto","conseguenza","ricaduta","implicazione","sviluppo",
"diramazione","ramificazione","derivazione","origine","fonte","radice","causa","motivazione","ragione","fondamento",
"presupposto","assunto","premessa","tesi","argomentazione","dimostrazione","confutazione","replica","controprova","evidenza",
"indizio","traccia","segnale","sintomo","indicatore","parametro","metrica","misura","scala","grado",
"livello","intensità","portata","ampiezza","estensione","copertura","raggio","ambito","settore","campo",
"area","dominio","spazio","dimensione","territorio","perimetro","confine","zona","regione","mappa"];
let difficile = 
["astrazione","ambivalenza","ambiguità","asimmetria","autonomia","autoriferimento","causalità","coerenza","contingenza","convergenza",
"decostruzione","determinazione","dialettica","discontinuità","discrepanza","emergenza","entropia","equivoco","epifania","euristica",
"formalizzazione","generalizzazione","idiosincrasia","immanenza","implicazione","indeterminazione","interdipendenza","interferenza","iterazione","latente",
"mediazione","metacognizione","modellizzazione","negazione","nonlinearità","ontologia","opacità","opposizione","oscillazione","paradigma",
"paradosso","permutazione","polarizzazione","presupposto","probabilismo","processualità","proiezione","ricorsività","ridondanza","riflessività",
"simmetria","singolarità","sovrapposizione","sussunzione","sintesi","stratificazione","trascendenza","trasversalità","univocità","varianza",

"affermazione","alterità","antinomia","articolazione","attribuzione","bilanciamento","categorizzazione","circolarità","coalescenza","compatibilità",
"complessità","composizione","compressione","connotazione","consistenza","contestualizzazione","continuità","correlazione","derivazione","differenziazione",
"direzionalità","dislocazione","dissociazione","distinzione","effettività","elaborazione","emulazione","equilibratura","eterogeneità","evidenziazione",
"formalismo","frammentazione","gradualità","identificazione","implementazione","inerenza","influenza","integrazione","interazione","interpolazione",
"irreversibilità","isomorfismo","localizzazione","mediazione","modulazione","mutazione","necessità","normalizzazione","opacizzazione","organizzazione",
"parametrizzazione","percezione","pertinenza","pluralità","potenzialità","predisposizione","proporzionalità","razionalizzazione","ricombinazione","riconfigurazione",
"ridistribuzione","rifunzionalizzazione","segmentazione","selezione","sensibilità","sequenzialità","significazione","sistematicità","stabilizzazione","strutturazione",

"adattività","aggregazione","allocazione","alterazione","anticipazione","applicabilità","approssimazione","artificialità","attribuzione","calibrazione",
"codificazione","collassamento","compatibilizzazione","compensazione","complessificazione","concentrazione","condizionalità","configurazione","consolidamento","contestazione",
"decontestualizzazione","delimitazione","demarcazione","depotenziamento","derivabilità","destabilizzazione","determinismo","dilatazione","dinamicità","disallineamento",
"disarticolazione","disgregazione","distanziamento","efficienza","elasticità","emersione","equilibratura","escalation","esplicitazione","estensione",
"formalizzazione","funzionalità","gerarchizzazione","ibridazione","identità","implementazione","induzione","inflessione","interconnessione","interruzione",
"legittimazione","modellazione","modificazione","normalità","ottimizzazione","permeabilità","polarità","predittività","propagazione","ricontestualizzazione",
"ricorsione","rimodulazione","saturazione","scalabilità","sincronizzazione","soggettività","standardizzazione","sussidiarietà","temporalità","validazione",

"accumulazione","additività","allineamento","ambito","ancoraggio","attribuzione","biforcazione","canalizzazione","circoscrizione","codipendenza",
"coesistenza","collocazione","combinatoria","commutazione","comparabilità","compresenza","conciliazione","condensazione","conformità","congiunzione",
"conservazione","contestualità","deriva","destrutturazione","deviazione","disposizione","distorsione","emancipazione","equilibrio","esecuzione",
"espansività","estrapolazione","formalità","generalità","incidenza","intersezione","intervento","legame","limitazione","metastruttura",
"modularità","neutralizzazione","opportunità","percorrenza","persistenza","perturbazione","prefigurazione","proiezione","rappresentazione","razionalità",
"ricaduta","riconoscibilità","riconversione","riformulazione","selettività","simultaneità","sintonia","sospensione","storicizzazione","trasformatività",

"attivazione","bilateralità","ciclicità","compatibilità","complementarità","convergenza","derivazione","dislocazione","distanza","elasticità",
"emulazione","esternalizzazione","formalismo","gradiente","impatto","incrementalità","invarianza","iteratività","legittimità","liminalità",
"modellizzazione","normalizzazione","opposizione","parzialità","polarizzazione","prevalenza","proporzione","ricombinazione","ricontestualizzazione","riduzione",
"separazione","serialità","sinergia","sovrapposizione","stabilità","transizione","uniformità","validità","variazione","verificabilità"];

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

selezionaMazzo();

</script>

</body>
</html>
