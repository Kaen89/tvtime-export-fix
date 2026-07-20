🇬🇧 English | [🇮🇹 Italiano](README.it.md)

# 🩹 Fix TV Time Export

A tiny, single-file web tool that realigns a **TV Time** export to the **TMDB**
catalog — fixing broken episode numbering, **without ever losing a "watched"**.

Everything runs in your browser. Your export files never leave your computer:
the page talks only to TMDB, to look up shows and episodes. No server, no
account, no upload.

👉 **[Open the tool](https://kaen89.github.io/tvtime-export-fix/)**

---

## Why

TV Time is shutting down, and its data uses TVDB numbering, which is often
inconsistent with other catalogs. When you export your history you get JSON
where seasons and episodes don't line up with anywhere else — recaps sharing a
number with real episodes, anime lumped into one giant "Season 1", and so on.

This tool cleans that up against TMDB so your history is portable, while
treating your watch history as sacred.

## What it does

For every series in the export it queries TMDB and:

- **Removes duplicates** — TV Time sometimes numbers recaps/movies with the
  same number as a regular episode; they're merged, keeping the "watched".
- **Realigns seasons** — where TVDB and TMDB split seasons differently, it
  remaps episodes onto the TMDB structure, carrying the watch history over
  position by position, then healing by key.
- **Splits lumped-together series** — if TV Time merged episodes from
  different shows into a single entry (common with spin-offs/anime), orphan
  episodes are resolved by their TVDB id and split out into their own,
  correctly matched TMDB series.
- **Never invents anything** — if TMDB knows fewer episodes than your export,
  the series is kept exactly as it was.
- **Respects specials** — special seasons and specials stay separate from the
  regular count.
- **Extracts movie specials** — some "specials" are actually feature films
  (e.g. *"Blue Lock The Movie"*), recognizable by name and tied to the series
  on TMDB; these are pulled out and moved to the movies output instead of
  staying mixed in with the episodes.

Movies get matched to TMDB too, correcting the entry's ids and pulling in any
film that was missing.

Custom **lists** are fixed as well: when a lumped-together series gets split,
the detached series are added to the list right after the original. Items are
matched to TMDB (series via their TVDB id, movies cross-referenced by `uuid`
with the movies file, or looked up by name) so the report can show what each
one resolves to; unresolved items are left untouched.

When the fix is done you get a **summary of every correction** right on the
page, plus a downloadable **HTML report** with the full detail per series,
movie and list — including a watched-episodes before/after count proving
nothing was lost, and the resolved TMDB IDs for every item.

The output keeps **exactly the same schema** as the original export — no extra
fields are added — so it stays importable anywhere. The enriched TMDB data
(TMDB IDs, air dates, release dates) lives only in the report, since importers
don't read those fields and, on lists, an unexpected field can prevent the list
from importing.

The interface is available in 8 languages (EN, IT, ES, FR, DE, PT, JA, ZH),
auto-detected from your browser.

---

## How to use it

### 1. Get your export

TV Time has no official export. Use the free Chrome extension
**[TV Time Out by Refract](https://chromewebstore.google.com/detail/tv-time-out-by-refract/pmejpdpjbkjklfceogdkolmgclldogbi)**:

1. Install the extension.
2. Open [app.tvtime.com](https://app.tvtime.com) and log in.
3. Click the extension icon and choose the **JSON** format.
4. Hit **Export my data** — you get files for series, movies and lists (or a
   single ZIP with everything).

### 2. Get a free TMDB API key

The tool needs a TMDB **v3** API key to look up shows (free):

1. Create an account on [themoviedb.org](https://www.themoviedb.org/signup) and
   confirm it via email.
2. Signed in, open [Settings → API](https://www.themoviedb.org/settings/api).
3. Click **Create** (or **Request an API key**) and pick **Developer**.
4. Accept the terms and fill the short form — app name/URL can be anything.
5. Copy the value labeled **API Key (v3 auth)** and paste it into the tool.

The key is kept only in your browser (`localStorage`).

### 3. Fix

Open the tool, paste your key, drag your JSON files onto the drop zone —
series, movies and/or lists, detected automatically, or simply the whole
Refract **ZIP** — and click **Fix**. Any one of the three files is enough; the
tool just notes which ones are missing and skips them. When it's done, download
the `-fixed.json` files.

---

## Privacy

Your export files are read and processed entirely in your browser and are never
uploaded. The only network requests go to `api.themoviedb.org` to resolve shows,
episodes and movies. Your TMDB API key is stored only in your browser.
