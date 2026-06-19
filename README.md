# Mythos Daily

A small, self-contained website that shows **one passage from Greek and Roman myth each day**, quoted from a famous translator, with a deep-dive page for every story — context, who's who, reception and afterlife, connections to other myths, the full list of notable translations, and a link to read the source for free. Built for GitHub Pages with no servers and a daily auto-rebuild.

The look is **"Dawn & Constellation"**: a pre-dawn sky warming to first light, because the site is a morning ritual and because so many of these heroes ended up as constellations. Today's myth is the star catching first light; the collection is a navigable star map.

---

## The passages

Each day's passage is a **complete episode** — a full scene to read with your coffee, running roughly **3–11 minutes** depending on the source — quoted from a public-domain translation and credited to its translator: Pope's *Iliad*, Dryden's *Aeneid*, Butler's *Odyssey*, Riley's *Metamorphoses*, Evelyn-White's Hesiod and *Homeric Hymns*. The translator's words are unchanged; only footnote markers, line-range labels, and the edition's editorial brackets are removed so it reads cleanly. Prose is set as an article with a drop cap; verse keeps its line breaks. Each entry shows an estimated reading time.

Every entry also carries a **Translators panel** — the notable English versions of that work across the centuries (e.g. Chapman 1611 → Pope 1726 → Butler 1900 → Lattimore 1965 → Fagles 1996 → Wilson 2017). Public-domain editions link straight to Project Gutenberg; modern translations are named so you can find them in print. Copyrighted translations are never reproduced — only named and pointed to.

---

## What's in here

```
index.html              The site (HTML + CSS + JS in one file). This is what you deploy.
data/passages.json      The corpus - the single source of truth.
data/passages.js        Generated; loaded by index.html. Don't edit by hand.
data/today.json         Generated; today's pick as a small JSON artifact.
data/queue.json         Your "to add" list for the auto-pull (starts empty).
preview.html            Generated; a self-contained copy you can open by double-clicking.
scripts/build.mjs       Regenerates passages.js / today.json / preview.html.
scripts/ingest.mjs      Auto-pull: fetches verbatim passages from the queue, drafts entries.
.github/workflows/daily.yml    Rebuilds every morning and commits any changes.
.github/workflows/ingest.yml   Runs the auto-pull and opens a Pull Request for review.
```

No build tools, no dependencies, no framework. The site computes "today" in the **viewer's own time zone**, so it flips at local midnight regardless of when the Actions run.

---

## Deploy it (about 4 minutes)

1. **Push these files** to the `main` branch of a new repo.
2. **Turn on Pages:** Settings > Pages > Source: **Deploy from a branch** > **main** / **/(root)**. Live at `https://<you>.github.io/<repo>/` within a minute.
3. **Allow the Actions to write back:** Settings > Actions > General > Workflow permissions > **Read and write**, and tick **Allow GitHub Actions to create and approve pull requests** (needed for auto-pull).

To test automation now: Actions tab > **Daily myth** > Run workflow. Prefer local-only? Just open `preview.html`.

---

## Auto-pulling new passages (review-gated)

The honest split: a myth entry is a **passage** plus a **deep dive**. The *passage* (source text) can be pulled automatically from public-domain editions. The *deep dive* (context, characters, reception) is the editorial value and isn't scraped — you write it, or have Claude draft it, then review.

So the pipeline pulls text and **opens a Pull Request** rather than publishing. You review, write the analysis, merge.

**To queue a passage,** add an object to `data/queue.json`:

```jsonc
{
  "id": "pygmalion",
  "title": "Pygmalion and the Statue",
  "tradition": "Greek myth, Roman source",
  "author": "Ovid", "work": "Metamorphoses", "locus": "Book X",
  "translator": "Henry T. Riley", "year": 1893,
  "gutenberg": 26073,
  "repo": "The-Metamorphoses-of-Ovid-Books-VIII-XV_26073",  // GITenberg repo name
  "file": "26073.txt",
  "match": "The pressed ivory becomes soft",   // a phrase that appears verbatim in the text
  "words": 22,                                  // approx excerpt length to keep
  "star": { "x": 0.40, "y": 0.55, "mag": 2 },
  "themes": ["art", "longing", "creation"]
}
```

Then run **Actions > Propose new passages > Run workflow** (or `node scripts/ingest.mjs` locally). It fetches the edition, verifies your `match` phrase really appears, grabs a clean verbatim excerpt, and drafts the entry with the analysis fields marked `TODO`.

**Finding the `repo`/`file`/`gutenberg` for a work:** every public-domain edition on Project Gutenberg is mirrored at `github.com/GITenberg`. Search the org for the translator + work, open the repo, and the text file is usually `<id>.txt`. Editions already wired up here: Pope *Iliad* (6130), Dryden *Aeneid* (228), Butler *Odyssey* (1727), Riley *Metamorphoses* I-VII (21765) and VIII-XV (26073), Evelyn-White Hesiod & *Homeric Hymns* (348).

### Want the analysis auto-drafted too?

The build can call the Anthropic API to draft the `TODO` commentary for review (you'd add an `ANTHROPIC_API_KEY` repo secret). Ask and I'll wire it in - it stays review-gated through the same Pull Request.

---

## Add or edit a myth by hand

Everything lives in `data/passages.json`. Copy an entry, fill it in (the `passage`, `summary`, `context`, `characters`, `reception`, `connections`, `translations`, and a `star` position), then `node scripts/build.mjs` or just push. The new star appears in the constellation.

---

## How "daily" works

`index.html` derives the day number from the viewer's local date, runs it through a fixed shuffle of the corpus, and picks deterministically - same myth for everyone on a given day, stable across reloads, cycling through the whole collection before repeating. `daily.yml` is the auto-update layer: it rebuilds each morning and commits `data/today.json`, which doubles as a tiny endpoint if you ever want today's myth elsewhere.

---

## Credits

Quoted source texts are public domain: Homer (*Iliad*, trans. Alexander Pope, 1715; *Odyssey*, trans. Samuel Butler, 1900), Virgil (*Aeneid*, trans. John Dryden, 1697), Ovid (*Metamorphoses*, trans. Henry T. Riley, 1893), Hesiod and the *Homeric Hymns* (trans. Hugh G. Evelyn-White, 1914), via Project Gutenberg. Modern translations named in the Translators panels remain in copyright and are referenced, not reproduced. Fonts: Cardo and IBM Plex Sans. The site code is yours to modify freely.
