# Mythos Daily

A small, self-contained website that shows **one passage from Greek and Roman myth each day**, with a dedicated deep-dive page for every story — context, who's who, reception and afterlife, connections to other myths, and the source text to read in full. Built to live on GitHub Pages with zero servers and a daily auto-rebuild.

The look is **"Dawn & Constellation"**: a pre-dawn sky that warms to first light at the horizon, because the site is a morning ritual and because so many of these heroes ended up as constellations. Today's myth is the star catching first light; the whole collection is a navigable star map.

---

## What's in here

```
index.html              The site (HTML + CSS + JS in one file). This is what you deploy.
data/passages.json      The corpus — the single source of truth. Edit this to add myths.
data/passages.js        Generated from passages.json; loaded by index.html. Don't edit by hand.
data/today.json         Generated; today's pick as a small JSON artifact you can reuse.
preview.html            Generated; a self-contained copy you can open by double-clicking.
scripts/build.mjs       Regenerates the three files above from passages.json.
.github/workflows/daily.yml   Runs the build every morning and commits any changes.
```

No build tools, no dependencies, no framework. The site computes "today" in the **viewer's own time zone**, so it flips at local midnight regardless of when the Action runs.

---

## Deploy it (about 4 minutes)

1. **Create a repo** and push these files to the `main` branch.
   ```bash
   git init && git add . && git commit -m "Mythos Daily"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```
2. **Turn on Pages:** repo **Settings → Pages → Build and deployment → Source: Deploy from a branch**, then choose **`main`** / **`/ (root)`** and save. Your site appears at `https://<you>.github.io/<repo>/` within a minute.
3. **Let the daily Action write back:** **Settings → Actions → General → Workflow permissions → Read and write permissions**, save. This lets the morning build commit the regenerated `today.json`.

That's it. To check the automation now: **Actions** tab → **Daily myth** → **Run workflow**.

> Prefer to keep it local-only? Just open `preview.html` in a browser.

---

## Add or edit a myth

Everything lives in `data/passages.json`. Copy an entry and fill it in:

```jsonc
{
  "id": "pygmalion",                      // url-safe; becomes #/myth/pygmalion
  "title": "Pygmalion and the Statue",
  "tradition": "Greek myth, Roman source",
  "themes": ["art", "longing", "creation"],
  "star": { "x": 0.55, "y": 0.40, "mag": 2 },  // position on the star map (0–1), mag 1=brightest
  "source": {
    "work": "Metamorphoses", "author": "Ovid",
    "locus": "Book X, lines 243–297",
    "edition": "trans. Brookes More, 1922 (public domain)."
  },
  "passage": {
    "kind": "rendering",
    "text": "Short line to read at the start of the day.",
    "attribution": "Editor's rendering after Ovid, Metamorphoses X"
  },
  "summary": "1–3 sentences: what happens.",
  "context": "Where it sits and why it matters.",
  "characters": [ { "name": "Pygmalion", "note": "Who they are." } ],
  "reception": "Its afterlife in art, literature, language.",
  "connections": [ { "to": "narcissus-echo", "why": "Why they rhyme." } ]
}
```

Then run `node scripts/build.mjs` (or just push — the Action does it) and the new star appears in the constellation.

### A note on the passages

The daily lines are **faithful editorial renderings** after each cited source, written so they're accurate to the myth without copying any single modern translation. Each entry's **Source & Text** section names a **public-domain edition** (Perseus, Project Gutenberg, the old Loeb and Bohn translations) where you can read the real thing in full. If you'd rather show literal source text, set `passage.kind` to `"excerpt"` and paste in a public-domain translation with its translator credited in `attribution`.

---

## How "daily" works

`index.html` derives the day number from the viewer's local date, runs it through a fixed shuffle of the corpus, and picks deterministically — so everyone sees the same myth on a given day, it's stable across reloads, and it cycles through the whole collection before repeating. The GitHub Action is the "updated automatically" layer: it rebuilds each morning and commits `data/today.json`, which is also a handy little endpoint if you ever want to surface today's myth elsewhere (a widget, a shortcut, an email).

---

## Credits

Source texts are public domain: Ovid (*Metamorphoses*, trans. Brookes More, 1922), Homer (*Odyssey*, trans. Samuel Butler, 1900), Hesiod and the *Homeric Hymns* (trans. Hugh G. Evelyn-White, 1914). Fonts: Cardo and IBM Plex Sans via Google Fonts. The site code is yours to modify freely.
