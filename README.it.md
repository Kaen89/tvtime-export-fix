[🇬🇧 English](README.md) | 🇮🇹 Italiano

# 🩹 Fix TV Time Export

Un piccolo tool web a file singolo che riallinea un export di **TV Time** al
catalogo **TMDB** — correggendo la numerazione degli episodi e aggiungendo
date di messa in onda e ID TMDB, **senza mai perdere un "visto"**.

Tutto gira nel tuo browser. I tuoi file di export non lasciano mai il
computer: la pagina parla solo con TMDB, per cercare serie ed episodi. Nessun
server, nessun account, nessun upload.

👉 **[Apri il tool](https://kaen89.github.io/tvtime-export-fix/)**

---

## Perché

TV Time sta chiudendo, e i suoi dati usano la numerazione TVDB, spesso
incoerente con altri cataloghi. Esportando la cronologia si ottiene un JSON
dove stagioni ed episodi non corrispondono da nessun'altra parte — recap con
lo stesso numero di episodi veri, anime ammassati in un'unica gigantesca
"Stagione 1", e così via.

Questo tool sistema tutto confrontandolo con TMDB, così la cronologia diventa
portabile, trattando la cronologia di visione come sacra.

## Cosa fa

Per ogni serie dell'export interroga TMDB e:

- **Rimuove i duplicati** — TV Time a volte numera recap/film con lo stesso
  numero di un episodio normale; vengono uniti, mantenendo il "visto".
- **Riallinea le stagioni** — dove TVDB e TMDB dividono le stagioni in modo
  diverso, rimappa gli episodi sulla struttura TMDB, portando la cronologia
  di visione posizione per posizione, poi sistemando per chiave.
- **Divide le serie accorpate** — se TV Time ha unito episodi di show diversi
  in un'unica voce (comune con spin-off/anime), gli episodi orfani vengono
  risolti tramite il loro TVDB id e separati in serie distinte, ciascuna
  abbinata correttamente su TMDB.
- **Non inventa mai nulla** — se TMDB conosce meno episodi del tuo export, la
  serie viene mantenuta esattamente com'era.
- **Aggiunge dati** — la data di messa in onda di ogni episodio (`air_date`) e
  l'ID TMDB della serie.
- **Rispetta gli special** — le stagioni special e gli special restano
  separati dal conteggio regolare.
- **Estrae i film travestiti da special** — alcuni "special" sono in realtà
  lungometraggi (es. *"Blue Lock The Movie"*), riconoscibili dal nome e
  collegati alla serie su TMDB; vengono estratti e spostati nell'output dei
  film invece di restare mischiati con gli episodi.

Anche i film vengono abbinati a TMDB, guadagnando `tmdb_id` e una
`release_date` corretta.

Anche le **liste** custom vengono sistemate: ogni elemento guadagna un
`tmdb_id` (serie risolte tramite il loro TVDB id, film incrociati per `uuid`
col file dei film, o cercati per nome su TMDB), e quando una serie accorpata
viene divisa, le serie staccate vengono aggiunte alla lista subito dopo
l'originale. Gli elementi non risolti restano intatti.

L'output mantiene **lo stesso schema** dell'export originale (più `tmdb_id` e
`air_date`), così resta importabile ovunque.

L'interfaccia è disponibile in 8 lingue (EN, IT, ES, FR, DE, PT, JA, ZH),
rilevate automaticamente dal browser.

---

## Come usarlo

### 1. Ottieni il tuo export

TV Time non ha un export ufficiale. Usa l'estensione Chrome gratuita
**[TV Time Out by Refract](https://chromewebstore.google.com/detail/tv-time-out-by-refract/pmejpdpjbkjklfceogdkolmgclldogbi)**:

1. Installa l'estensione.
2. Apri [app.tvtime.com](https://app.tvtime.com) ed effettua l'accesso.
3. Clicca sull'icona dell'estensione e scegli il formato **JSON**.
4. Premi **Export my data** — ottieni i file di serie, film e liste (o un
   unico ZIP con tutto).

### 2. Ottieni una chiave API TMDB gratuita

Il tool ha bisogno di una chiave API TMDB **v3** per cercare le serie
(gratuita):

1. Crea un account su [themoviedb.org](https://www.themoviedb.org/signup) e
   confermalo via email.
2. Una volta effettuato l'accesso, apri
   [Settings → API](https://www.themoviedb.org/settings/api).
3. Clicca **Create** (o **Request an API key**) e scegli **Developer**.
4. Accetta i termini e compila il breve modulo — nome app/URL possono essere
   qualsiasi cosa.
5. Copia il valore indicato come **API Key (v3 auth)** e incollalo nel tool.

La chiave viene conservata solo nel tuo browser (`localStorage`).

### 3. Fix

Apri il tool, incolla la tua chiave, trascina i tuoi file JSON nella zona di
drop — serie, film e/o liste, rilevati automaticamente, oppure direttamente
l'intero **ZIP** di Refract — e clicca **Fix**. Basta uno qualsiasi dei tre
file; il tool segnala quali mancano e semplicemente li salta. Al termine,
scarica i file `-fixed.json`.

---

## Privacy

I tuoi file di export vengono letti ed elaborati interamente nel browser e
non vengono mai caricati altrove. Le uniche richieste di rete vanno verso
`api.themoviedb.org` per risolvere serie, episodi e film. La tua chiave API
TMDB è conservata solo nel tuo browser.
