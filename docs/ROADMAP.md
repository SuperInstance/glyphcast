# glyphcast — Roadmap

Every phase ends in a **receipt**: a sealed, replayable artifact naming its
inputs, outputs, and measured crossing rates. A phase without a receipt
does not count as done, whatever the commit history says.

## Phase 0 — Dataset builder (the gate)

**Deliverable:** `glyphcast dataset build` produces frame/sidecar pairs.

- Port or vendor the chiaroscuro engine set behind a pinned, hash-recorded
  interface (engine version hash travels in every frame header — the same
  determinism the feed-lab required).
- Sources, in order: the `?mock=1` synthetic scene (ground truth free
  tonight); recorded screen/streams; real camera feeds via `glyphcam`
  (sibling repo, later phase).
- Sidecar generator: run the scene/event model to *announce* births,
  deaths, flips, crossings — because exp002 measured that discrete events
  do not cross a glyph stream unless the producer announces them. The
  fleet's JEV shape is the default wire format.
- Gate receipt: dataset stats (frames, engine pins, event histograms) +
  one held-out validation split whose ground truth is sealed.

## Phase 1 — Baseline predictor (`predict`)

**Deliverable:** a few-million-parameter model that projects the next grid.

- Coarse-to-fine heads: 12×9 semantics → 48×36 glyph field → optional
  96×72 detail. Cross-entropy per cell from day one; no regression heads.
- Scheduled sampling from the start (Bengio et al. 2015) — exposure bias
  is a known disease; don't catch it and then cure it.
- Baseline arms, per fleet doctrine: (a) persistence (repeat last grid),
  (b) per-cell flow extrapolation (no learned weights), (c) the tiny
  transformer. Every claim compares against all three.
- Gate receipt: held-out next-grid accuracy per palette class, per engine
  preset, *plus* the reading score — how much projected scene crosses to a
  language-model reader (feed-lab scorer generalized).

## Phase 2 — Event-conditioned prediction

**Deliverable:** sidecar-in, better-out.

- Ablations: grids alone vs grids+events vs events alone. The hypothesis
  (wager, labeled): event tokens buy the discrete structure that cell
  churn buries — the seven checker flips exp002 failed to hear until
  queried directly.
- Gate receipt: the ablation table, sealed.

## Phase 3 — Interpolator (`interpolate`)

**Deliverable:** cell-lattice frame interpolation (glyph-RIFE).

- Per-cell bidirectional flow on the lattice; occlusion edges get the
  generative budget.
- Claim to test: at 178× fewer samples per frame than the pixel stream,
  the interpolator's effective temporal resolution overtakes what the
  pixel side can transport on the same wire. Measured, not asserted.
- Gate receipt: motion-coherence eval + reader score on interpolated
  sequences vs ground-truth frames.

## Phase 4 — Hardware lane (with `glyphcam`)

**Deliverable:** live prediction on a Pi/Jetson-class device.

- CSI capture → glyph-first pipeline (sibling repo `glyphcam` owns the
  silicon interface; glyphcast owns the model lane).
- Thermal/bandwidth receipts: frames-per-watt, tokens-per-frame, and the
  budget triangle (resolution × rate × color) under real purse settings.
- New problem, expected: glyph **noise classes** for sensor artifacts —
  the thing synthetic scenes never teach.

## Standing rules

- FAIL-first: every gate's pins must FAIL on the pre-change tree and PASS
  after, or the gate is theater.
- Numbers carry denominators: "crossed 4/8", never "crosses".
- Simulations are labeled simulations. Receipts are real runs.
- The lane diagram lives in `docs/ARCHITECTURE.md`; contributor rules in
  `docs/DEVELOPER-GUIDE.md`.
