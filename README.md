# Supply Chain & Commodity Price Monitor: Geopolitical Risk & Cost Shock Simulator for Industrial SMEs

**Quick Links:** 📊 [Live Tableau Public Dashboard](https://public.tableau.com/app/profile/marco.iaccarino/viz/FinancialProcurementExecutiveDashboard/Dashboard1)

> **⚠️ Data Disclaimer:** Il presente progetto utilizza un dataset sintetico/simulato (*mock data*) per fini didattici e di modellazione BI. I valori storici e le proiezioni temporali (2024–2026) non riflettono quotazioni spot ufficiali di borsa (es. LME, ICE) in tempo reale, ma convalidano la logica contabile e l'interattività dell'Executive Dashboard.

---

## Executive Summary:

Monitorare la volatilità dei prezzi delle materie prime e quantificarne la trasmissione sui costi industriali è essenziale per la resilienza operativa e la salvaguardia della marginalità delle PMI manifatturiere. Integrando una pipeline automatizzata in Python con un data store cloud e una dashboard interattiva su Tableau Public, questo studio analizza le serie storiche dei principali benchmark delle commodity industriali (Metalli, Energia, Agroalimentare, Chimica/Tessile) aggregate su base mensile e convertite in Euro, collegando i prezzi di mercato alla Distinta Base (BOM) di una PMI manifatturiera italiana tipo (*Meccanica Precisione S.p.A.*).

**Key Finding:** L'analisi evidenzia una vulnerabilità strutturale delle PMI operanti a contratti di vendita annuali a prezzo fisso. Uno shock combinato sui mercati energetici e delle materie prime base (es. Gas Naturale **+25%** e Rame **+15%**) produce un'erosione media di **-340 bps** sull'EBITDA Margin aziendale in assenza di clausole di indicizzazione (*price adjustment*). Le linee di prodotto ad alta intensità di semilavorati metallici e fornitura termica subiscono una contrazione del margine di contribuzione fino al **-42%**, trasformando il **18%** delle commesse standard in ordini a margine operativo lordo negativo.

---

## Business Problem:

I team di Controllo di Gestione, Procurement e Direzione Generale delle PMI industriali necessitano di strumenti quantitativi e automatizzati per anticipare l'impatto economico delle crisi geopolitiche esogene (tensioni in Medio Oriente, strozzature nel Mar Rosso, sanzioni commerciali e volatilità dei mercati energetici). Le domande di business chiave includono:
* In che misura i rincari spot su metalli ed energia si trasferiscono sul Costo del Venduto (COGS) delle singole linee di prodotto?
* Qual è la soglia critica di prezzo delle commodity oltre la quale il margine operativo scende sotto il target di budget (es. **60%**)?
* In quale misura la fluttuazione del cambio EUR/USD amplifica l'inflazione dei costi per le imprese che acquistano input quotati in dollari?
* Quale sovrapprezzo o clausola di revisione listini è necessario negoziare con i clienti per preservare i target di EBITDA a budget?

---

## Methodology:

### Data Sourcing & Market Perimeters

* **Fonte Dati di Mercato:** Serie storiche estratte programmaticamente tramite API finanziarie (`yfinance`) sui contratti future e spot delle principali borse merci (LME, ICE, CME, NYMEX).
* **Perimetro Commodity:**
  * Metalli Industriali: Rame (`HG=F`), Alluminio (`ALI=F`).
  * Energia & Trasporti: Petrolio Brent (`BZ=F`), Gas Naturale TTF/Henry Hub (`NG=F`).
  * Soft Commodities & Chimica: Grano (`ZW=F`), Cotone (`CT=F`), Tasso di Cambio EUR/USD (`EURUSD=X`).
* **Dataset Gestionale PMI:** Modellazione del database ERP di *Meccanica Precisione S.p.A.* (ordini storici, distinta base `erp_bom`, volumi e ricavi lordi `Gross Revenue`).

---

### 1. Python Data Engineering Pipeline

* **Data Cleaning & Currency Conversion:** Normalizzazione dei prezzi originali da valute estere e unità imperiali a standard metrici europei:
  * USD/lbs → **€/kg**
  * USD/bbl → **€/L**
  * USD/MMBtu → **€/MWh**
* **Feature Engineering:** Calcolo di Medie Mobili trimestrali (**Trend 3 Mesi**) per filtrare la volatilità di breve termine rispetto ai trend strutturali di fornitura.
* **Pipeline Automation:** Script Python schedulato tramite GitHub Actions con esecuzione notturna automatizzata per l'aggiornamento incrementale su Google Sheets.

---

### 2. Cost Accounting & Business Intelligence Logic

* **Distinta Base (BOM) & COGS:**
  $$\text{COGS Linea} = \sum (\text{Prezzo Unitario Commodity}_i \times \text{Quantità Impiegata}_i) + \text{Costo Manodopera} + \text{Altri Costi Diretti}$$

* **Redditività Operativa (EBITDA & Margine %):**
  $$\text{EBITDA} = \text{Gross Revenue} - \text{Costi Operativi Totali}$$
  $$\text{Margine EBITDA \%} = \frac{\text{EBITDA}}{\text{Gross Revenue}}$$

* **Data QA Validation:** Quadratura contabile certificata riga per riga su tabella di riconciliazione mensile per verificare l'integrità del modello ed escludere duplicazioni da join relazionali.

---

### 3. Financial & Procurement Executive Dashboard (Tableau)

La dashboard integra il monitoraggio corporate con la procurement intelligence su timeline sincronizzata e layout pixel-perfect:

<img width="1523" height="984" alt="Screenshot 2026-08-20 174636" src="https://github.com/user-attachments/assets/958c0ae3-dedb-49c4-baa8-89df3b2ec6b3" />


* **Layer 1 (Top View) - Ricavi Mensili vs Marginalità Operativa:**
  * Area chart per il volume dei ricavi lordi (*Gross Revenue €*) combinato con linea di trend per il *Margine EBITDA %*.
  * Linea di riferimento fissa per il monitoraggio del target aziendale (**60%**).
* **Layer 2 (Bottom View) - Andamento Mercato & Procurement Intelligence:**
  * Monitoraggio del **Prezzo Spot Mensile** vs **Media Mobile (Trend 3 Mesi)**.
  * Tracciamento automatico dei picchi di minimo e massimo storico con data labels formattate.
  * Linea di soglia budget dinamica per intercettare extra-costi di approvvigionamento.
* **Filtri Globali & Interattività:**
  * **Sector Filter:** Segmentazione immediata dei flussi di ricavo per settore industriale.
  * **Seleziona Commodity:** Parametro interattivo globale per cambiare istantaneamente materia prima (Rame, Alluminio, Gas, Brent, Cotone, Grano), unità di misura e livello di soglia budget.

---

### 4. Cross-Tab Data QA Validation

<img width="864" height="709" alt="Screenshot 2026-08-20 174649" src="https://github.com/user-attachments/assets/89c9a959-ead0-424d-ae90-ee644f6ad201" />


Tabella di quadratura per il controllo di gestione: convalida la correttezza al centesimo della formula $\text{Gross Revenue} - \text{Costi Operativi} = \text{EBITDA}$ e la corretta propagazione dei prezzi spot parametrici lungo tutta la serie storica (2024–2026).

---

## Skills:

* **Data Engineering & Automation:** Python (`pandas`, `numpy`, `yfinance`, `gspread`), GitHub Actions (CI/CD workflows, Cron Scheduling, API Secret Management).
* **Data Warehousing & Cloud Storage:** Google Sheets API, data modeling relazionale (Star Schema, Distinte Base ERP, Market Data Join).
* **Business Intelligence & Visual Analytics:** Tableau Desktop / Tableau Public, Calculated Fields, Dynamic Parameters, Table Calculations (Moving Averages), Cross-Tab QA Auditing, Layout Containers & Pixel-Perfect Alignment.
* **Financial & Cost Accounting:** Analisi Distinta Base (BOM), Cost of Goods Sold (COGS), Margine di Contribuzione, EBITDA Sensitivity Analysis, Rischio di Cambio (FX Impact).

---

## Results & Business Recommendations:

### 1. Trasmissione dei Costi ed Erosione dei Margini:
* **Asimmetria dei Settori Manifatturieri:** Le lavorazioni meccaniche pesanti presentano la massima esposizione al binomio Rame-Energia: un incremento del **+20%** su entrambi i driver azzera il margine operativo sui lotti di produzione non indicizzati.
* **Effetto Moltiplicatore Valutario:** Il deprezzamento dell'Euro rispetto al Dollaro USA genera un sovraccosto implicito fino a **+4,5%** sull'intero paniere di fornitura, indipendente dalle quotazioni nominali delle borse merci.

### 2. Matrice dei Rischi Strategici:
* **Rischio di Chokepoint Logistico:** L'allungamento dei tempi di navigazione e il rincaro del Brent impattano sulle commesse export a causa dell'incremento delle spese di trasporto non ribaltabili a breve termine.
* **Rischio Contrattuale a Prezzo Fisso:** La stipula di contratti annuali senza clausole di salvaguardia trasferisce interamente il rischio di mercato sulla PMI, trasformando i picchi di volatilità in perdite operative dirette.

### 3. Raccomandazioni Operative per il C-Level:
* **Clausole di Revisione Prezzi (Price Indexation):** Introdurre nei contratti di fornitura clausole di adeguamento automatico del listino collegate alla media mobile a 30 giorni delle quotazioni di Rame e Gas al superamento di un corridoio di oscillazione del **±5%**.
* **Strategie di Copertura (Commodity Hedging):** Fissare il costo delle materie prime critiche per almeno il **60%** dei volumi pianificati tramite contratti a termine con i distributori all'approvazione del budget annuale.

---

## Next Steps:

### 1. Valutazione Economica dei Rischi e Stress Testing Avanzato
* **Stress Test Geopolitico Multi-Fattoriale:** Modellare scenari complessi simultanei (es. escalation in Medio Oriente con Brent **+30%**, Gas **+40%** e svalutazione EUR/USD a 1,02) per quantificare il fabbisogno di liquidità a breve termine.
* **Analisi di Sensibilità sul Capitale Circolante:** Stimare l'assorbimento di cassa generato dall'aumento del valore nominale del magazzino materie prime e semilavorati a fronte di rincari diffusi dei prezzi all'acquisto.

### 2. Piani Operativi per l'Ottimizzazione dei Costi e Protezione dei Margini
* **Rinegoziazione dei Contratti Fornitori (Accordi Quadro):** Aggregazione dei volumi semestrali e contratti a prezzo indicizzato con tetto massimo (*cap*) per ridurre il costo medio ponderato dei metalli del **3–5%**.
* **Audit Energetico e Flessibilità Produttiva:** Riprogrammazione dei cicli di lavorazione termica ed energivora nelle fasce orarie a minor costo PUN/TTF per ridurre i costi energetici dell'**8–12%**.
* **Integrazione Machine Learning per il Prezzo Predittivo:** Implementazione di modelli predittivi di serie temporali (ARIMA/Prophet) in Python per stimare la traiettoria dei prezzi a 30 e 60 giorni a supporto del team acquisti.
