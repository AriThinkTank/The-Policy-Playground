# The Policy Playground

**Famous social-science theories, rebuilt as hand-drawn simulations you can play with.**

Most explanations of these ideas are walls of text. This site lets you *run* them: tweak the
assumptions, hit play, and watch reasonable little people produce outcomes none of them wanted.
Each experiment pairs a plain-English, lightly humorous explainer with a live, agent-based
simulation and links straight to the original papers.

It is a static website — no build step, no backend — designed to drop onto GitHub Pages as-is.

---

## The five experiments

| Experiment | Theory | Original source |
|---|---|---|
| **The Segregation Engine** | Schelling's model of segregation | Schelling, T. (1971), *Journal of Mathematical Sociology* 1(2): 143–186 |
| **The Common Pasture** | Tragedy of the Commons (+ Ostrom's counterpoint) | Hardin, G. (1968), *Science* 162: 1243–1248; Ostrom, E. (1990), *Governing the Commons* |
| **The Trust Tournament** | The Evolution of Cooperation (iterated Prisoner's Dilemma) | Axelrod, R. (1984), *The Evolution of Cooperation*; Axelrod & Hamilton (1981), *Science* 211: 1390–1396 |
| **The Race to the Middle** | The Median Voter Theorem | Hotelling, H. (1929), *Economic Journal* 39: 41–57; Black (1948); Downs, A. (1957), *An Economic Theory of Democracy* |
| **When the Dam Breaks** | Threshold models & how norms flip | Granovetter, M. (1978), *American Journal of Sociology* 83(6): 1420–1443; Kuran, T. (1995), *Private Truths, Public Lies* |

Full citations with links live in each experiment's "Sources" section and in `js/data.js`.

---

## Run it locally

It is pure static files, so any static server works. From the project root:

```bash
# Python (already on most machines)
python3 -m http.server 8000
# then open http://localhost:8000
```

You can also just open `index.html` directly via `file://` — the scripts are plain
(non-module) scripts specifically so this works without a server.

---

## Deploy to GitHub Pages

1. Create a new GitHub repository and push these files to it (the repo root should contain
   `index.html`):

   ```bash
   git init
   git add .
   git commit -m "The Policy Playground"
   git branch -M main
   git remote add origin https://github.com/<you>/<repo>.git
   git push -u origin main
   ```

2. On GitHub: **Settings → Pages → Build and deployment**. Set **Source** to
   *Deploy from a branch*, choose branch **main** and folder **/ (root)**, and save.

3. Wait a minute, then visit `https://<you>.github.io/<repo>/`.

The included **`.nojekyll`** file tells GitHub Pages to serve the files as-is and skip Jekyll
processing (harmless here, but it avoids any surprises with folders).

> **Project page vs. user page.** On a project page the site lives at a sub-path
> (`/<repo>/`). This app uses *relative* asset paths and a hash router (`#/...`), so it works
> at any sub-path with no configuration.

---

## "Use a database if required" — why there isn't one

GitHub Pages only serves **static files**; it cannot run a server or a database. So a
traditional database would stop it from being deployable there at all. Instead this project
uses the two patterns that *are* appropriate for a static site:

- **Content "database" →** a plain JavaScript data module, [`js/data.js`](js/data.js). All
  theory text, examples, and citations live in one structured array (`PP.FRAMEWORKS`). It is
  easy to read, diff, and extend, and it loads instantly with no network round-trip.
- **Per-user saved state →** the browser's `localStorage`. Whatever you set in a simulation
  (tolerance, number of herders, electorate shape, and so on) is remembered on your own device
  between visits. Nothing is sent anywhere; there is no tracking and no server.

If you ever wanted real shared/multi-user persistence, you would add an external API
(e.g. a serverless function or a hosted database) and call it with `fetch` — the data layer is
already isolated in `data.js`, so swapping it out is straightforward.

---

## Project structure

```
policy-playground/
├── index.html              # shell: nav, #view container, footer, script load order
├── .nojekyll               # tell GitHub Pages to skip Jekyll
├── css/
│   └── style.css           # full design system (notebook aesthetic, hand-drawn)
├── vendor/
│   └── rough.js            # Rough.js 4.6.6, vendored locally (no CDN dependency)
└── js/
    ├── draw.js             # shared hand-drawn drawing layer (figures, charts, loop manager)
    ├── ui.js               # control builders (sliders, toggles, readouts)
    ├── data.js             # the content "database": theories, examples, sources
    ├── app.js              # hash router, home page, experiment page, localStorage helper
    └── sims/
        ├── segregation.js
        ├── commons.js
        ├── cooperation.js
        ├── median-voter.js
        └── norm-cascade.js
```

Each simulation registers itself as `PP.sims["<slug>"]` and exposes a small contract:
`mount(ctx)` (returns `{ destroy }`), plus `thumb(stage)` and `comic(stage, i)` for the
home-page art and the three-panel comic strip.

---

## A note on accuracy

Every simulation is a deliberately simplified, faithful illustration of a *published* model —
enough to build correct intuition, not a research-grade replication. The point is to make the
mechanism visible and tweakable. For the real thing, follow the source links.

## Credits

- Theory inspiration and a companion text library:
  [Frameworks for Public Policy](https://frameworks.pranaykotas.com/).
- Hand-drawn rendering: [Rough.js](https://roughjs.com/) (MIT).
- Typefaces via Google Fonts: Bricolage Grotesque, Caveat, Hanken Grotesk, Space Mono.
