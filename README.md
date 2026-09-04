# GutCheck Matrix

A weighted micro-decision tool with a roast engine, a timed gut sprint, and a
dramatic blur reveal. One self-contained HTML file — no build step, no
dependencies, no server. Open `index.html` in a browser.

## The idea

Filling in a decision matrix is homework, and homework is the opposite of a gut
check. So the matrix here is the **output**, not the input.

You name 2–4 options and pick the criteria that matter, then run the **Gut
Sprint**: one snap call at a time, five big buttons, keyboard `1`–`5`,
auto-advancing. Every answer is timed. The grid that falls out the other end is
a read-only heatmap — evidence, not a form.

## What the timing is for

How long you stall on a call is the behavioural read, and it runs through the
whole app:

- A stall counts only if it is both over 2.5s **and** 1.8× your own median, so
  the threshold adapts to fast and slow people instead of being fixed.
- The roast engine's timing rules outrank its score-pattern rules — a stall gets
  called out by name.
- The reveal warns you when your slowest call was *not* the winner, which
  pre-loads the disappointment before you feel it.
- The gut-check override flags that option in the list.
- The Receipt of Destiny records the whole timing spread.

## Features

- **Setup** — 2–4 options, five criteria packages (Work / Weekend / Food Fight /
  Life Ops / Custom), per-criterion weights (Low ×1, Med ×1.5, High ×2), and
  **Quick Chaos** for when you are too stuck to even name the options.
- **Roast engine** — twelve rule families that read your criterion *names* for
  keywords. High fun + high cost, high effort + low fun, all-identical scores,
  landslides, photo finishes, and the two timing rules.
- **The reveal** — result card at `blur(16px)`, full-screen countdown with a Web
  Audio tick-tock, optional Web Speech "Voice of Fate", screen shake on the final
  second, then unblur and a synthesised triumph chime.
- **Surrender to Chaos** — same theatre, math inverted, picks the worst option.
- **Gut check** — accept fate for canvas confetti and a fanfare, or admit
  disappointment for a synthesised record scratch and the override flow.
- **Receipt of Destiny** — copyable monospace receipt plus a downloadable PNG
  drawn on canvas.
- **Persistence** — matrix state, timings and settings in `localStorage`.
- **Themes** — postal stamp (default) and retro/cyberpunk.

All sound is synthesised at runtime through the Web Audio API. There are no
audio files.

## Implementation notes

A few things that are load-bearing and easy to break:

- **Text is never masked, filtered or rotated.** The perforated stamp paper and
  its scalloped shadow are painted on two text-free pseudo layers behind the
  content, and the dream-board tilt is applied to those layers only. Putting any
  of it on the text itself forces the glyphs into a rasterized layer and they go
  soft.
- **The result card drops `filter` to `none`** after the unblur transition
  finishes. A `blur(0px)` still allocates a filter layer and leaves the winner
  name permanently fuzzy.
- **The wordmark is optically justified** by `fitWordmark()`, which tracks out
  the shorter line to match the longer one. It divides by `chars - 1` (the
  visible gaps) and pulls the browser's trailing gap back off with a negative
  margin. It re-runs on font load, resize and theme change, because the two
  themes use different display faces.
- **Bungee is the display face, and only above ~1.3rem.** It ships a single
  weight (400), so no display rule may ask for 700/800 — the browser would
  synthesise a faux-bold. It is also a wide signage face, so its sizes run
  considerably smaller than a normal-width face would at the same visual
  weight; that is why the display sizes look low. UI-sized text uses Karla.
- **Solid buttons use `--cta` / `--cta-red`**, darker siblings of `--teal` and
  `--red`, so light text on them clears WCAG AA (4.5:1). The bright tints stay
  for bars, fills and heat cells.

## Browser support

Needs `mask-composite` (for the perforation), the Web Audio API, and
`color-mix()`. Current Chrome, Edge, Firefox and Safari are fine. Web Speech is
feature-detected and optional; `localStorage` failures are caught, so the app
still runs in private windows with persistence disabled.
