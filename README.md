# Ma che ne sAI? — Strumenti per il laboratorio

Quattro piccoli strumenti web per il laboratorio di sensibilizzazione sull'AI a finalità musicale. Ognuno è **una sola pagina HTML**: niente installazione, niente server, niente account. Si pubblicano come file statici (ad esempio su GitHub Pages) e si aprono dal browser del tablet.

| # | Strumento | A cosa serve | Idea di AI che mostra | Microfono | Internet |
|---|-----------|--------------|-----------------------|-----------|----------|
| 1 | **Estratti_audio_NN** | Tenere pronti 10 estratti musicali per il gioco «la rete neurale come Indovina chi» | Dati, etichette (generi), input → hidden → output | no | **sì** (grafica da CDN) |
| 2 | **Live_Sampler** | Insegnare alla macchina 4 suoni con la voce e farle suonare dei campioni | Classificazione, dataset, training, KNN | sì | no |
| 3 | **Mappa_suoni** | Disporre dei suoni su una mappa 2D per somiglianza e suonarli con il dito o la voce | Descrittori, similarità, KNN, PCA, sintesi concatenativa | opzionale | no |
| 4 | **Mappa_Suoni_Seq** | Come Mappa_suoni, più un sequencer di grani | Idem, più la differenza tra «riconoscere» e «comporre» | opzionale | no |

Ordine consigliato in aula: 1 → 2 → 3 → 4. Il gioco con l'1 introduce l'idea di imparare dai dati, il 2 la fa provare con la propria voce, il 3 e il 4 usano la stessa idea per fare musica.

---

## Requisiti e regole comuni

- **Browser:** Chrome (Android o desktop).
- **Microfono:** funziona solo da **HTTPS** o da `localhost`. Aprendo il file direttamente dal disco (`file://`) il browser lo blocca. Il modo più semplice è pubblicare su GitHub Pages.
- **Cuffie con microfono** consigliate: senza, il suono dell'altoparlante rientra nel microfono e confonde il riconoscimento. In classe con molti ragazzi che fanno suoni insieme, meglio due spazi separati per la registrazione.
- **Privacy:** nessuno strumento invia audio a un server. Quello che i ragazzi registrano o caricano resta nel browser del loro dispositivo. Dettagli per strumento sotto.
- **Esportazione audio (Live_Sampler, Mappa_suoni, Mappa_Suoni_Seq):** il pulsante «● Registra la performance» salva l'uscita della pagina in **MP3 stereo a 192 kb/s**, con audio convertito a 16 bit prima della codifica. Un solo clic per iniziare, uno per fermare (ferma da sola dopo 3 minuti). Il file si chiama `prototipo_…mp3` o `mappa_…mp3` con data e ora, e si scarica dal link sotto il lettore. La codifica avviene a fine registrazione e può richiedere qualche secondo sui tablet più lenti.
- **Formati in ingresso:** meglio mp3, wav, ogg. Le registrazioni Android in m4a o amr a volte non si decodificano: la pagina lo segnala.

---

## 1. Estratti_audio_NN

*Titolo nella pagina: «Rete Neurale Musicale – Gestore Audio»*

**Scopo.** È il pannello del docente per il gioco in cui la classe è la rete neurale: tiene pronti fino a 10 estratti musicali, con genere e titolo, da far ascoltare ai gruppi *Input* e *Hidden layer*.

**Come si usa**
1. In ogni slot (da #1 a #10) carica un file audio.
2. Scegli il **genere** dal menu (Rock, Pop, Rap / Trap, Musica Classica, Techno / EDM, Jazz / Blues, Reggaeton, Metal, Altro / Generico) e scrivi un **titolo** o una descrizione. I generi sono preassegnati a rotazione e si possono cambiare.
3. «**Ascolta 10s**» riproduce l'estratto; l'icona delle cuffie apre la **Modalità Cuffie** a schermo intero, con un grande pulsante play e la barra di avanzamento fino a 10 secondi.
4. La casella «Stop Automatico (10 sec)» in alto ferma l'ascolto dopo 10 secondi; si può disattivare.
5. «Svuota Tutto» (con conferma) rimuove tutti i file e ripristina i titoli.

**Salvataggio.** File, generi e titoli restano nel browser di quel dispositivo (IndexedDB), anche dopo la chiusura della pagina. Non si trasferiscono su un altro tablet.

**Spunti per la lezione**
- Il genere è un'etichetta che scegliamo noi: gli estratti ambigui permettono di parlare di bias e di categorie culturali con confini sfumati.
- Il **nome del file** compare nella Modalità Cuffie: se contiene il genere («rock_01.mp3») lo svela ai ragazzi. Rinomina i file in modo neutro, ad esempio `estratto_03.mp3`.

**Limiti**
- Serve **internet**: Tailwind CSS, Font Awesome e i font Inter vengono caricati da CDN. Senza rete la pagina funziona ma perde gran parte della grafica.
- L'audio parte sempre dall'**inizio** del file: prepara estratti già ritagliati (5-10 secondi).
- Le barre animate nella Modalità Cuffie sono **solo un effetto grafico** (altezze casuali): non analizzano il suono.
- Un file per slot.
- Per i brani usa materiale tuo o con licenza libera (ad esempio Creative Commons), non brani commerciali senza verificare i diritti.

---

## 2. Live_Sampler

*Titolo nella pagina: «Allena la macchina» · file di origine: `prototipo_knn.html`*

**Scopo.** I ragazzi addestrano un classificatore con la propria voce: 4 classi (ad esempio battito, «ssss», «mmm», «pa»). Quando la macchina riconosce un suono, fa partire un campione associato a quella classe.

**Come si usa**
1. «**1. Attiva il microfono**» e resta in silenzio per un secondo: la pagina misura il rumore di fondo e regola da sola la soglia.
2. Per ogni classe **tieni premuto** il pulsante colorato e fai il suono per almeno 3 secondi, variandolo un po'. Ogni frammento di circa 40 ms finisce nel dataset con l'etichetta di quella classe (fino a 300 esempi per classe).
3. Lascia il pulsante e fai uno dei suoni: la scheda della classe riconosciuta si illumina e parte un campione.
4. Per ogni classe puoi cambiare il **nome** (si salva nel browser), caricare uno o più **suoni** con «Scegli suoni» (ne parte uno a caso) e togliere i suoni con «🗑 Svuota suoni». Senza file suona una nota di riserva (Do, Mi, Sol, Do acuto). «▶ Prova il suono» riproduce la classe senza passare dal microfono.
5. «Registra la performance» salva il risultato in MP3.

**Controlli**
- **Barra bianca del livello** e **linea rossa della soglia**: il suono deve superare la linea per essere registrato o riconosciuto. Lo slider «Soglia silenzio» la regola a mano.
- **Barre colorate** di ogni classe: la probabilità che la macchina assegna a quel suono.
- «Cancella tutto» svuota il **dataset** (gli esempi vocali di tutte le classi); i campioni caricati restano.

**Come funziona (versione per i ragazzi).** La macchina ascolta 7 caratteristiche del suono: quanto è forte, quanto è «rumoroso», quanto è acuto e quanta energia ha in 4 fasce di frequenza. Per riconoscere un suono nuovo cerca i 5 esempi più simili tra quelli registrati e li fa votare (KNN). **Non c'è un training lungo**: gli esempi registrati *sono* il dataset, e ogni esempio aggiunto cambia subito il comportamento. Una classe parte quando 3 degli ultimi 4 frame concordano (circa 120 ms), con una pausa minima di 150 ms tra un suono e il successivo.

**Spunti per la lezione**
- Dataset, etichette, test e generalizzazione, vissuti in prima persona.
- «Sabota il modello»: pochi esempi, suoni troppo simili tra classi, un'altra voce, un compagno che parla accanto. Sono gli stessi errori dei sistemi reali.
- Cambiare i campioni di una classe cambia il brano a parità di riconoscimento.

**Limiti**
- Non c'è una classe «silenzio/rumore»: ciò che sta sotto la soglia viene ignorato, ma un rumore forte in aula può essere classificato.
- Non si possono cancellare gli esempi di una sola classe, solo tutto il dataset.
- Campioni caricati ed esempi non si salvano alla chiusura della pagina.
- La risposta ha una latenza di circa 150-250 ms: va bene per «un suono = un campione», non per ritmi molto veloci.
- Per partire, un suono deve restare sopra la soglia per almeno circa 120 ms. Un suono molto breve (un singolo click) può non attivare nulla: abbassa la soglia o prolunga il suono (un battito di mani con la sua coda di solito basta, ma va provato).

---

## 3. Mappa_suoni

*Titolo nella pagina: «Mappa dei suoni»*

**Scopo.** Una sintesi concatenativa semplificata (CBCS): un insieme di suoni viene tagliato in frammenti brevi (*grani*), ogni grano diventa un punto su una mappa 2D e i punti vicini sono suoni simili. Toccando la mappa, o cantando nel microfono, si suonano i grani più vicini.

**Come si usa**
1. «**1. Carica suoni**» (anche più file insieme) oppure tieni premuto il pulsante rosso per **registrare** la voce. Ogni suono diventa un'etichetta colorata con una ✕ per toglierlo.
2. La pagina analizza i suoni: i grani silenziosi vengono scartati e per ogni file se ne tengono al massimo 600.
3. Scegli gli **assi** della mappa: Brillantezza, Volume, Rumorosità, Bassi, Alti, oppure **Automatico (PCA)**, dove è la macchina a trovare le due direzioni lungo cui i suoni differiscono di più.
4. **Tieni premuto il dito** sulla mappa (anche con più dita insieme): la macchina trova i K grani più vicini al dito e li suona tutti insieme, sovrapposti.
5. «🎤 Voce» attiva la modalità specchio: ciò che canti muove un punto verde sulla mappa e suona i grani vicini.
6. «Registra la performance» salva l'uscita in MP3.

**Controlli**
- **Durata dei frammenti:** 100, 200, 400 o 800 ms (0,1 – 0,8 s). Cambiarla rifà l'analisi.
- **Grani suonati insieme (K):** da 1 a 8.
- **Loop:** spento, i grani selezionati suonano una volta (se il dito sta fermo c'è silenzio; si risuona muovendo il dito o premendo di nuovo). Acceso, si ripetono ogni «periodo» (da 40 ms a 1 s) finché tieni premuto. Il periodo è indipendente dalla durata dei grani: se è più corto, i grani si sovrappongono.
- **Polifonia:** ogni grano è una voce indipendente, fino a 48 contemporanee, con un limitatore sull'uscita per evitare distorsione.
- **Soglia voce** con barra del livello, calibrata all'attivazione.

**Cosa si vede sulla mappa.** I colori indicano il file di origine, un flash bianco il grano che suona, gli anelli bianchi i grani selezionati, il cerchio il dito.

**Spunti per la lezione**
- Qui **non ci sono etichette né training**: la macchina misura caratteristiche e organizza i suoni per somiglianza. È il contrario del Live_Sampler, dove un umano etichetta gli esempi.
- Cambiare i file, gli assi o K cambia la mappa e quindi il brano: l'intervento dei ragazzi è visibile.
- Il PCA mostra che lo stesso insieme di suoni, guardato con assi scelti da noi o dalla macchina, appare in modi diversi.

**Limiti**
- I grani sono tagliati a intervalli regolari, non sugli attacchi: un battito può finire spezzato.
- Con pochi grani (meno di una decina) la mappa è poco significativa.
- L'analisi di molti file lunghi può richiedere qualche secondo.
- Niente persistenza: chiudendo la pagina file e mappa si perdono.

---

## 4. Mappa_Suoni_Seq

*Titolo nella pagina: «Mappa dei suoni»*

**Scopo.** È Mappa_suoni con **tutto quello che c'è sopra**, più un **sequencer di grani**: invece di suonare col dito, si disegna sulla mappa un percorso di punti che la macchina ripete in ciclo.

**Come si usa il sequencer**
1. Carica o registra dei suoni, poi premi «**🎞 Sequenza**»: la mappa si circonda di un bordo arancione.
2. **Tocca i punti** che vuoi, uno dopo l'altro. Restano evidenziati con il numero del passo. Un tocco a più di 40 px da ogni punto viene ignorato.
3. La riproduzione **parte dal primo punto** e va in ciclo; ogni nuovo punto toccato si aggiunge in coda mentre suona.
4. Una **linea** unisce i punti nell'ordine (con un tratto più tenue dall'ultimo al primo, che chiude il ciclo). Il tratto in corso diventa bianco e un cursore corre verso il punto successivo: è il **playhead**.
5. **Periodo di ogni passo:** da 3 s (lento) a 0,02 s (veloce), con scala logaritmica per regolare con precisione anche i valori veloci. Il valore è mostrato in secondi.
6. Pulsanti: **Pausa / Riproduci**, **Togli ultimo**, **Svuota**.

**Memorizzare, richiamare, cancellare (nella stessa sessione)**
- «**💾 Memorizza**» salva la sequenza e fa comparire un bottone, ad esempio «▶ Seq 1 · 3 pt · 0,37 s». Si possono memorizzare fino a **8** sequenze.
- Cliccando il bottone la sequenza viene **richiamata**, con i suoi punti e il suo periodo, e riparte. Se quella attuale non è memorizzata, compare una conferma prima di sostituirla.
- La **✕** accanto al bottone la cancella.
- Le sequenze memorizzate valgono finché la pagina resta aperta.

**Altre regole**
- Spegnendo «Sequenza» la riproduzione **continua**, e si può suonare sopra con il dito.
- Se cambi la **durata dei frammenti**, i punti passano al grano più vicino nello stesso file. Se **rimuovi un file**, spariscono i suoi punti dalle sequenze. «Svuota» (in alto) cancella anche le sequenze.
- Con periodi molto brevi i grani si sovrappongono: a 0,02 s con frammenti da 200 ms ne suonano circa dieci insieme (tetto di 48 voci). Per più chiarezza usa frammenti da 100 ms.

**Spunti per la lezione**
- Il sequencer non «compone»: sceglie l'utente dove toccare e la macchina trova i suoni vicini. È un buon punto per distinguere uno strumento che **riconosce o organizza** (ML classico) da un'AI **generativa**.
- I percorsi memorizzati mostrano come piccole variazioni di punti e di periodo producano brani diversi con gli stessi suoni.

---

## Dove stanno nella mappa dei concetti

| Strumento | Tipo di tecnica | Deep learning? | AI generativa? |
|-----------|-----------------|----------------|----------------|
| Estratti_audio_NN | Supporto al gioco in classe (nessun modello) | — | — |
| Live_Sampler | Machine learning classico: descrittori + KNN, apprendimento supervisionato | no | no |
| Mappa_suoni / Mappa_Suoni_Seq | Descrittori + ricerca dei vicini (KNN) + PCA, senza etichette | no | no |

Nessuno dei quattro usa reti neurali o modelli di linguaggio: l'idea di rete neurale si vive nel gioco unplugged, mentre gli strumenti 2-4 mostrano la parte «imparare dai dati» e «organizzare per somiglianza» in forma trasparente e modificabile.

---

## Pubblicazione

- Ogni strumento è un solo file HTML. Una struttura comoda su GitHub Pages è una cartella per strumento, ciascuna con un `index.html`:
  `Estratti_audio_NN/`, `Live_Sampler/`, `Mappa_suoni/`, `Mappa_Suoni_Seq/`.
- Per aggiornare uno strumento basta sostituire il suo file. Per distribuire i link ai ragazzi un QR code per ogni cartella funziona bene.
- Per provare in locale: `python3 -m http.server` nella cartella e poi `http://localhost:8000` (il microfono funziona da `localhost`).

## Problemi frequenti

| Sintomo | Cosa controllare |
|---------|------------------|
| Live_Sampler non suona nulla | La barra bianca supera la linea rossa quando fai il suono? Se no, abbassa la soglia. «▶ Prova il suono» suona? Se no, controlla volume e modalità silenziosa del tablet. Il contatore «esempi» sale mentre tieni premuto? |
| «Microfono non disponibile» | Serve HTTPS o `localhost` e il permesso del browser. |
| Fischio o suoni che si ripetono da soli | Il suono rientra nel microfono: usa le cuffie. |
| La classe riconosce male | Più esempi e più vari, suoni più diversi tra loro, soglia adatta al rumore dell'aula. |
| «File non leggibili» | Converti in mp3, wav o ogg. |
| La mappa resta vuota | L'analisi può richiedere qualche secondo; guarda la riga di stato sopra gli assi. |
| Estratti_audio_NN senza grafica | Serve la connessione internet per Tailwind, Font Awesome e i font. |
| Il take MP3 compare dopo qualche secondo | È la codifica («Codifica MP3 in corso…»): attendi. |

## Stato dei test

- **Verificato** in Chrome desktop (senza interfaccia grafica), con microfono simulato e audio sintetico:
  - *Live_Sampler:* attivazione del microfono e calibrazione, registrazione degli esempi, riconoscimento dal vivo di un suono addestrato, cambio nome delle classi, caricamento di più file per classe, «Svuota suoni», «Cancella tutto», esportazione MP3.
  - *Mappa_suoni:* caricamento di più file, mappa, tocco, loop con periodo, polifonia, modalità voce (con un suono simulato nel microfono).
  - *Mappa_Suoni_Seq:* sequenza, aggiunta di punti durante la riproduzione, pausa, «Togli ultimo», memorizzazione, richiamo e cancellazione, periodo da 3 s a 0,02 s (misurato sui passi reali), nuova analisi dei grani, rimozione di un file.
- **Non ancora verificato su tablet Android:** microfono reale, latenza, riconoscimento di suoni molto brevi (un singolo battito), decodifica dei formati dei ragazzi, prestazioni dell'analisi con molti file. Conviene un test su un dispositivo vero, con le cuffie, prima della lezione.
- Estratti_audio_NN è descritto a partire dal suo codice e non è stato modificato.

## Crediti e licenze

- **lamejs 1.2.1** (encoder MP3, licenza **LGPL-3.0**, basato su **LAME**): incorporato nelle pagine Live_Sampler, Mappa_suoni e Mappa_Suoni_Seq. Progetto: <https://github.com/zhuker/lamejs> · LAME: <https://lame.sourceforge.net>.
- **Estratti_audio_NN** carica da CDN Tailwind CSS, Font Awesome 6.4 e i font Inter (Google Fonts), ciascuno con la propria licenza.
- Il resto del codice (analisi audio, FFT, KNN, PCA, mappa, sequencer) è scritto nelle pagine stesse, senza altre librerie.
