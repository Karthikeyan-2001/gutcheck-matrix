# GutCheck Matrix

A weighted micro-decision tool with a roast engine, a timed gut sprint, and a
dramatic blur reveal. One self-contained HTML file — no build step, no
dependencies, no server. Open `index.html` in a browser.

## The idea

Filling in a decision matrix is homework, and homework is the opposite of a gut
check. So the matrix here is the **output**, not the input.

It opens on three criteria that fit any choice at all — **Want it**, **Costs me**,
**Regret** — so "cappuccino or mocha" needs no setup. Type two options, run the
**Gut Sprint** (one snap call at a time, five big buttons, keyboard `1`–`5`,
auto-advancing), get a verdict. Six taps. Categories for Travel, Buying, Weekend
and Work sit quietly below the criteria for when the decision is bigger than a
coffee.

Every answer is timed. The grid that falls out the other end is a read-only
heatmap — evidence, not a form.

## The decision engine

Three layers of real multi-criteria decision analysis, all pure client-side
maths in one file with no libraries.

**Criterion polarity.** Every criterion knows whether more is better (Want it)
or worse (Costs me), inferred from its name and overridable with a ↑/↓ toggle.
Without this the arithmetic rewards the expensive, exhausting, regret-inducing
option — which is precisely what it used to do.

**TOPSIS** replaces the weighted sum. Each criterion column is vector-normalised,
weighted, and compared against the best and worst achievable values; the score
is *closeness to the ideal*, `d⁻ / (d⁺ + d⁻)`, in [0,1]. Because it measures
distance from the worst case too, one lopsided high score no longer carries an
option.

**AHP** derives the weights from pairwise duels — "which matters more, Fun or
Cost?" — via the geometric-mean approximation of the principal eigenvector. It
also yields a **consistency ratio**: say Fun beats Cost, Cost beats Effort and
Effort beats Fun, and the app will tell you your preferences contradict each
other. Entirely optional; skip it and the manual Low/Med/High weights stand.

**Monte Carlo robustness.** After scoring, the whole decision is re-run 2000
times with ratings nudged by up to ±1 and weights jittered, counting how often
the same option still wins — "holds up in 87% of nearby worlds". A 55% winner is
a coin flip in a suit, and the app says so. It takes about 9ms.

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
- **Roast engine** — fourteen rule families that read your criterion *names* for
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
- **Every colour is flat.** The palette is old-ticket: aged manila card stock on
  a dark counter, printed in the inks vintage tickets actually used (oxblood,
  slate, bottle green, ochre, dusty plum). There are deliberately **no colour
  gradients** anywhere — the only `linear-gradient()` left in the stylesheet
  draws 1px ruled lines and the notch mask, which are patterns, not fades.
- **Shadows are shallow.** Old card stock does not float, so the shadow tokens
  are 1–3px and the notch drop-shadow is a 4px blur rather than a 16px one.
- **Two tints exist only to carry text.** `--cta` / `--cta-red` sit under light
  button labels and `--gold-deep` under dark ones (plain `--gold` is too light
  to carry text at label size, and too light to read as a lit star on manila).
  Every text/background pair in the UI clears WCAG AA.

## Browser support

Needs `mask-composite` (for the perforation), the Web Audio API, and
`color-mix()`. Current Chrome, Edge, Firefox and Safari are fine. Web Speech is
feature-detected and optional; `localStorage` failures are caught, so the app
still runs in private windows with persistence disabled.
