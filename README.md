# GeoHealth-Naples

### Localizzazione Ottimale di un Presidio Ospedaliero tramite Analisi Multicriterio (MCDA), Mappatura Satellitare (GEE) e Analisi di Rete (QGIS)

---

## 📄 Descrizione del Progetto e Obiettivi

**GeoHealth-Naples** è un progetto di Geoinformatica applicata che ridefinisce i criteri standard per la pianificazione e la localizzazione di infrastrutture sanitarie critiche (nello specifico, un nuovo **polo ospedaliero**) all'interno della Provincia di Napoli. 

### Il Problema Tradizionale
I modelli classici di *facility siting* si basano quasi esclusivamente su due fattori: la densità demografica (dove c'è più popolazione) e la geometria della rete stradale (il centro geometrico del bacino). Questo approccio presenta però un grave limite: i nodi urbani più densi e accessibili coincidono spesso con le aree a maggior tasso di inquinamento atmosferico e congestione.

### La Soluzione e l'Obiettivo di GeoHealth
L'obiettivo di questo progetto è invertire questo paradigma introducendo la **salubrità ambientale come vincolo primario, stringente e non negoziabile**. Il fine ultimo è individuare un sito che rappresenti il perfetto punto di equilibrio tra:
1. **Isolamento dai vettori tossici:** Garantire un'elevata qualità dell'aria per accelerare e proteggere la degenza e la guarigione dei pazienti.
2. **Accessibilità infrastrutturale:** Assicurare che la struttura sia perfettamente integrata nella rete viaria principale per minimizzare i tempi di intervento delle ambulanze e facilitare l'afflusso extra-urbano.
3. **Copertura territoriale:** Colmare le "zone d'ombra" lasciate scoperte dall'attuale rete ospedaliera della provincia.

---

## 🛠️ Come lo abbiamo fatto: Framework Metodologico

Il progetto è stato sviluppato integrando tecniche di **Telerilevamento (Remote Sensing)** in ambiente Cloud e analisi spaziali vettoriali in ambiente desktop, strutturando il flusso di lavoro in tre macro-fasi sequenziali:

### Fase 1: Estrazione del Proxy Ambientale (Google Earth Engine)
Per mappare il livello di inquinamento, abbiamo scelto di non focalizzarci su un singolo gas (come solo l'NO₂ o solo il CO), ma di elaborare un **Indice di Inquinamento Generale** che facesse da tracciante cumulativo dello smog urbano, dei fumi industriali e delle emissioni marittime del comparto portuale.
* **Raccolta Dati:** Abbiamo interrogato la costellazione satellitare **Sentinel-5P (sensore TROPOMI)**, filtrando i prodotti offline (OFFL) per un intero anno solare così da eliminare le fluttuazioni stagionali.
* **Elaborazione Cloud-Based:** Tramite script in GEE, abbiamo applicato una media geometrica temporale per ripulire il dataset dalla copertura nuvolosa e dalle anomalie transitorie. 
* **Normalizzazione:** I valori di densità colonnare dei gas sono stati normalizzati tramite un algoritmo di Min-Max per ottenere un indice adimensionale puro (da 0 a 1), dove lo 0 rappresenta l'aria più pulita della provincia e l'1 l'hotspot più inquinato. Il raster finale è stato ritagliato (*clipped*) sui confini ISTAT della Provincia di Napoli ed esportato in formato GeoTIFF.

### Fase 2: Overlay Spaziale e "Strategia di Evitamento" (QGIS)
Il raster dell'Indice di Inquinamento Generale è stato importato in ambiente QGIS per essere integrato con i dati territoriali e vincolistici attraverso l'**Algebra Raster** e i principi dell'Analisi Multicriterio (MCDA).
* **Definizione dei pesi e dei vincoli:** Abbiamo applicato una "strategia di evitamento" (*avoidance strategy*). Le aree contrassegnate dai pixel rossi (corrispondenti all'hinterland metropolitano di Napoli e alla fitta griglia industriale/autostradale a nord) sono state categorizzate come zone ad altissima vulnerabilità e scartate dal modello.
* **Individuazione del Quadrante Target:** Incrociando i dati sulla densità abitativa, i vincoli paesaggistici e la mappa degli inquinanti, il modello ha identificato il **quadrante est / sud-est della provincia** come l'area ideale: una zona caratterizzata da correnti d'aria stabili, bassissimo carico emissivo di fondo, ma strategicamente adiacente a bacini di utenza che richiedono una nuova copertura sanitaria.

### Fase 3: Network Analysis e Scelta del Centroide (Striano)
Definito il quadrante geografico ottimale, l'esatta localizzazione dell'ospedale è stata determinata analizzando la rete stradale reale tramite i dati open-source di **OpenStreetMap (OSM)**.
* **Calcolo delle Isocrone:** Abbiamo modellato i tempi di percorrenza stradale calcolando aree di accessibilità a 15, 30 e 45 minuti, simulando la viabilità d'emergenza e ordinaria.
* **La scelta di Striano:** L'algoritmo di network analysis ha eletto il comune di **Striano** come il baricentro infrastrutturale perfetto. 
* **Verifica del Risultato:** Striano è posizionato in un'area atmosfericamente "pulita" rispetto agli hotspot tossici della provincia, ma gode di una connettività autostradale e stradale eccellente. La sua scelta permette di coprire una macro-area periferica storicamente distante dai grandi poli ospedalieri cittadini, abbattendo i tempi di trasporto d'emergenza per i comuni limitrofi.

---

## 🗂️ Struttura del Repository

Il progetto è stato organizzato in modo rigoroso per garantire l'ordine della documentazione ed evitare il caricamento su cloud di dataset geografici grezzi e ridondanti:

* **`📁 sources/`**
    * Contiene gli script di elaborazione per Google Earth Engine (utilizzati per generare l'Indice di Inquinamento Generale).
    * Include il file di progetto QGIS (`.qgz`), strutturato con i layer d'analisi, le legende e i pesi dell'algebra raster.
* **`📁 presentazione/`**
    * Contiene il codice sorgente in **LaTeX Beamer** (`main.tex`) configurato con un tema accademico formale per l'esposizione orale.
    * `/images`: Raccolta delle mappe tematiche esportate, dei grafici di concentrazione dei gas e dei diagrammi di flusso.
    * `Presentazione_GeoHealth.pdf`: Il documento finale compilato, pronto per essere proiettato in sede d'esame.
* **`📄 .gitignore`**
    * File fondamentale per lo sviluppo: esclude automaticamente dal tracciamento Git i file vettoriali locali pesanti (come i database stradali `.dbf`, gli shapefile comunali completi e i pacchetti di geopackage `.gpkg` $>50\text{MB}$), mantenendo il repository leggero e focalizzato sul codice e sui risultati.
* **`📄 README.md`**
    * Questo file di documentazione.

---

## 🚀 Replicabilità del Flusso di Lavoro

Per riprodurre l'analisi spaziale:
1. Si elabora il flusso satellitare su **Google Earth Engine** selezionando l'intervallo temporale d'interesse ed esportando il raster normalizzato dell'inquinamento su Google Drive.
2. Si carica il raster in **QGIS** all'interno del progetto presente nella cartella `sources/`.
3. Si applicano i criteri di pesatura tramite il Calcolatore Raster per generare la mappa di idoneità.
4. Si esegue lo strumento di Analisi di Rete (*Network Analysis*) sfruttando i grafi stradali di OpenStreetMap per verificare l'accessibilità dei tempi di soccorso sul centroide identificato.

---

