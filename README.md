# Supply Chain & Commodity Price Monitor: Geopolitical Risk & Cost Shock Simulator for Industrial SMEs

**Quick Links:** 📊 [Live Tableau Public Dashboard](https://www.google.com/search?q=%23) | 🐍 [Python Data Ingestion Pipeline](https://www.google.com/search?q=%23)

---

### Executive Summary

Monitorare la volatilità dei prezzi delle materie prime e quantificarne la trasmissione sui costi industriali è un requisito essenziale per la resilienza operativa e la salvaguardia della marginalità delle PMI manifatturiere. Integrando una pipeline automatizzata in Python con un data store cloud e una dashboard interattiva su Tableau Public, questo studio analizza le serie storiche dei principali benchmark delle commodity industriali (Metalli, Energia, Agroalimentare, Chimica/Tessile) aggregate a livello giornaliero e convertite in Euro, collegando i prezzi di mercato alla Distinta Base (BOM) di una PMI manifatturiera italiana tipo (*Meccanica Precisione S.p.A.*).

**Key Finding:** L'analisi evidenzia una vulnerabilità strutturale delle PMI operanti a contratti di vendita annuali a prezzo fisso. Uno shock combinato sui mercati energetici e delle materie prime base (es. Gas Naturale $+25\%$ e Rame $+15\%$) produce un'erosione media di **-340 bps sull'EBITDA Margin** aziendale in assenza di clausole di indicizzazione (*price adjustment*). Le linee di prodotto ad alta intensità di semilavorati metallici e fornitura termica subiscono una contrazione del margine di contribuzione fino al $-42\%$, trasformando il $18\%$ delle commesse standard in ordini a margine operativo lordo negativo.

---

### Business Problem

I team di Controllo di Gestione, Procurement e Direzione Generale delle PMI industriali necessitano di strumenti quantitativi e automatizzati per anticipare l'impatto economico delle crisi geopolitiche esogene (tensioni in Medio Oriente, strozzature nel Mar Rosso, sanzioni commerciali e volatilità dei mercati energetici). Le domande di business chiave includono:

* In che misura i rincari spot su metalli ed energia si trasferiscono sul Costo del Venduto (COGS) delle singole linee di prodotto?
* Qual è la soglia critica di prezzo delle commodity oltre la quale il margine di contribuzione di una commessa diventa negativo?
* In quale misura la fluttuazione del cambio EUR/USD amplifica l'inflazione dei costi per le imprese che acquistano input quotati in dollari?
* Quale sovrapprezzo o clausola di revisione listini è necessario negoziare con i clienti per preservare i target di EBITDA a budget?

---

### Methodology

```
┌─────────────────────────────────────────────────────────────┐
│ 1. DATA EXTRACTION & INGESTION (Python / GitHub Actions)    │
│    • Estrazione giornaliera prezzi commodity (yfinance API) │
│    • Conversione FX automatica (EUR/USD) e metriche standard│
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. DATA WAREHOUSE & BRIDGE STORE (Google Sheets API)        │
│    • Tabella Prezzi Storici & Medie Mobili (SMA 50 / 200)   │
│    • Tabella ERP: Distinta Base (BOM), Volumi e Listini PMI │
└──────────────────────────────┬──────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. ANALYTICS & SIMULATION LAYER (Tableau Public)            │
│    • What-If Parameters (Shock Geopolitici & Valutari)      │
│    • Live Auto-Refresh giornaliero e monitoraggio COGS      │
└─────────────────────────────────────────────────────────────┘

```

#### Data Sourcing & Market Perimeters

* **Fonte Dati di Mercato:** Serie storiche giornaliere estratte programmaticamente tramite API finanziarie (`yfinance`) sui contratti future e spot delle principali borse merci (LME, ICE, CME, NYMEX).
* **Perimetro Commodity:**
* *Metalli Industriali:* Rame (`HG=F`), Alluminio (`ALI=F`), Acciaio/Iron Ore (`TIO=F`), Nichel (`NICKEL`).
* *Energia & Trasporti:* Petrolio Brent (`BZ=F`), Gas Naturale TTF/Henry Hub (`NG=F`).
* *Soft Commodities & Chimica:* Grano (`ZW=F`), Cotone (`CT=F`), Tasso di Cambio EUR/USD (`EURUSD=X`).


* **Dataset Gestionale PMI:** Modellazione sintetica del database ERP di *Meccanica Precisione S.p.A.* (5.000 record di ordini, prezzi di listino, volumi di vendita e coefficienti di consumo unitario per materia prima).

---

#### 1. Python Data Engineering Pipeline

* **Data Cleaning & Currency Conversion:** Normalizzazione dei prezzi originali da valute estere e unità imperiali a standard metrici europei ($/lbs $\rightarrow$ €/kg; $/bbl $\rightarrow$ €/L; $/MMBtu $\rightarrow$ €/MWh).
* **Feature Engineering:** Calcolo di Medie Mobili a 50 e 200 giorni (SMA50, SMA200) per identificare divergenze strutturali e calcolo della volatilità storica a 30 giorni.
* **Pipeline Automation:** Script Python schedulato tramite **GitHub Actions** con esecuzione cron programmata ogni notte per l'aggiornamento incrementale su **Google Sheets**.

---

#### 2. Cost Accounting & Business Intelligence Logic

* **Distinta Base (BOM) & COGS:**

$$\text{COGS Linea} = \sum (\text{Prezzo Unitario Commodity}_i \times \text{Quantità Impiegata}_i) + \text{Costo Manodopera} + \text{Altri Costi Diretti}$$


* **Simulatore di Shock Geopolitico (What-If Parameters in Tableau):**
Ricalcolo dinamico del costo del venduto e del margine al variare dei parametri di stress:

$$\Delta \text{COGS} = \sum (\text{Prezzo Base}_i \times (1 + \text{Shock}_i) \times \text{Consumo}_i) - \text{COGS Budget}$$


$$\text{EBITDA Simulato} = \text{Ricavi Totali} - (\text{COGS Simulato} + \text{OPEX Fissi})$$



---

#### 3. Tableau Public Interactive Dashboard

* **View 1: Macro Commodity Heatmap & Live Market Ticker:** Monitoraggio real-time di tutte le materie prime con delta percentuale vs budget, volatilità e indicatori tecnici (sopra/sotto SMA200).
* **View 2: Sector Cost Impact & Distinta Base:** Analisi dell'incidenza percentuale dei fattori produttivi per settore (Metalmeccanica, Ceramica/Vetro, Agroalimentare, Packaging).
* **View 3: Geopolitical What-If Shock Simulator:** Pannello di controllo interattivo per simulare crisi geopolitiche (shock petrolifero, rincaro gas, strozzature metalli) con impatto immediato su margini operativi per commessa.

---

### Skills

* **Data Engineering & Automation:** Python (`pandas`, `numpy`, `yfinance`, `gspread`), GitHub Actions (CI/CD workflows, Cron Scheduling, API Secret Management).
* **Data Warehousing & Cloud Storage:** Google Sheets API, data modeling relazionale, integrazione ETL per BI.
* **Business Intelligence & Visual Analytics:** Tableau Desktop / Tableau Public, Calculated Fields, What-If Parameters, Table Calculations, Live Web Data Refresh, UX/UI Dashboard Design.
* **Financial & Cost Accounting:** Analisi Distinta Base (BOM), Cost of Goods Sold (COGS), Margine di Contribuzione, EBITDA Sensitivity Analysis, Rischio di Cambio (FX Hedging).

---

### Results & Business Recommendations

#### 1. Trasmissione dei Costi ed Erosione dei Margini

* **Asimmetria dei Settori Manifatturieri:** Le lavorazioni meccaniche pesanti e la fonderia presentano la massima esposizione al binomio Rame-Energia: un incremento del $+20\%$ su entrambi i driver azzera il margine operativo sui lotti di produzione non indicizzati.
* **Effetto Moltiplicatore Valutario:** Il deprezzamento dell'Euro rispetto al Dollaro USA genera un sovraccosto implicito fino a $+4{,}5\%$ sull'intero paniere di fornitura, indipendente dalle quotazioni nominali delle borse merci.

#### 2. Matrice dei Rischi Strategici

* **Rischio di Chokepoint Logistico:** L'allungamento dei tempi di navigazione e il rincaro del Brent impattano sensibilmente sulle commesse destinate all'export, comprimendo il margine netto a causa dell'incremento delle spese di spedizione non ribaltabili a breve termine.
* **Rischio Contrattuale a Prezzo Fisso:** La stipula di contratti annuali senza clausole di salvaguardia trasferisce interamente il rischio di mercato sulla PMI, trasformando i picchi di volatilità in perdite operative dirette.

#### 3. Raccomandazioni Operative per il C-Level

* **Clausole di Revisione Prezzi (*Price Indexation*):** Introdurre nei contratti di fornitura clausole di adeguamento automatico del listino collegate alla media mobile a 30 giorni delle quotazioni di Rame e Gas al superamento di un corridoio di oscillazione del $\pm 5\%$.
* **Strategie di Copertura (*Commodity Hedging*):** Fissare il costo delle materie prime critiche per almeno il $60\%$ dei volumi pianificati tramite contratti a termine o acquisti a termine con i distributori locali all'approvazione del budget annuale.

---

### Next Steps

#### 1. Valutazione Economica dei Rischi e Stress Testing Avanzato

* **Stress Test Geopolitico Multi-Fattoriale:** Modellare scenari complessi simultanei (es. escalation in Medio Oriente con Brent $+30\%$, Gas $+40\%$ e svalutazione EUR/USD a 1,02) per quantificare il fabbisogno di liquidità a breve termine.
* **Analisi di Sensibilità sul Capitale Circolante:** Stimare l'assorbimento di cassa generato dall'aumento del valore nominale del magazzino materie prime e semilavorati a fronte di rincari diffusi dei prezzi all'acquisto.

#### 2. Piani Operativi per l'Ottimizzazione dei Costi e Protezione dei Margini

* **Rinegoziazione dei Contratti Fornitori (Accordi Quadro):**
* *Azione:* Aggregazione dei volumi di acquisto su base semestrale e stipula di accordi quadro a prezzo indicizzato con tetto massimo (*cap*).
* *Beneficio Economico:* Protezione contro i picchi estremi di mercato e riduzione del costo medio ponderato degli acquisti di metalli del $3–5\%$.


* **Audit Energetico e Flessibilità Produttiva:**
* *Azione:* Riprogrammazione dei cicli di lavorazione termica ed energivora (forni di fusione e trattamenti) nelle fasce orarie e nei giorni a minor costo energetico PUN/TTF.
* *Beneficio Economico:* Riduzione diretta dei costi per utenze industriali (OPEX energetici) stimata tra l' $8\%$ e il $12\%$.


* **Integrazione Machine Learning per il Prezzo Predittivo:**
* *Azione:* Implementazione di modelli predittivi di serie temporali (ARIMA/Prophet) all'interno dello script Python per stimare la traiettoria dei prezzi a 30 e 60 giorni.
* *Beneficio Economico:* Supporto analitico al team acquisti nella scelta del timing ottimale per gli ordini di rifornimento scorte.
