# DRT_Cell_Simulator — receptor cells on graph topology

> **Humble disclaimer.** Amateur engineering project. We are not HPC professionals and make no competitive claims. Errors are likely. The simulator is a *visualisation*, not a quantitative biological model — see [What is not in the paper](#not).

GPLv3.

---

## Contents

- [What it is](#what)
- [Why it matters — bio digital twins](#why)
- [How it works — graph cells under a tick](#how)
- [Reading the output — HUD and what to watch](#reading)
- [The bio digital twin prototype — `digital_twin.html`](#prototype)
- [What is not in the paper](#not)
- [References and the trio](#refs)

---

## <a id="what"></a>What it is

A browser demo where a population of small *cells* sit on a graph (not a regular grid), each cell holds a small piece of internal state, and at every tick each cell updates its state from the states of its immediate neighbours. There is no global controller. The whole-screen picture is whatever the local rules add up to over many ticks.

A second layer wraps the cellular substrate in a piece of human endocrine biology — the hypothalamic–pituitary–adrenal (HPA) stress-response loop — and uses Bach's BWV 847 fugue as the timing source that drives the simulation. So the demo simultaneously shows three things on one screen: cellular automata on a non-grid graph, a toy reconstruction of a biological feedback loop, and music as the clock that drives both.

The repository ships a second, related visualisation in the same browser tab: `digital_twin.html` — the bio digital twin prototype with an infinite level-of-detail (LOD) zoom-out camera. That file is described in [its own section below](#prototype).

**[Live demo →](https://norayr-m.github.io/drt-cell-simulator/)**

---

## <a id="why"></a>Why it matters — bio digital twins

A bio digital twin is a computational model of a specific biological system — a heart chamber, a hepatic lobule, a section of cortex — wired tightly enough that it can predict the system's behaviour under a stimulus. Most realistic biological tissues are *not* organised as regular grids; they are graphs, with each cell coupled to a small number of irregular neighbours determined by tissue topology rather than by a Cartesian lattice.

The cell simulator addresses one specific slice of the bio digital twin question: *what does cellular dynamics look like when the underlying topology is a graph rather than a grid, and the per-cell rule is a small local function over the neighbours' states?* That is the structural shape that hepatocytes in a hepatic lobule, neurons in a cortical microcolumn, and cardiomyocytes in a ventricular wall all share. Building a useful bio digital twin requires being able to compute on that shape.

The HPA-axis layer adds one specific concrete use: a small endocrine feedback loop with three named glands (hypothalamus, pituitary, adrenal) and a central receptor that integrates their outputs. The demo is not a quantitative endocrine model — it is a visual playground for the shape "small graph of receptors with a feedback loop driven by a periodic signal." The Bach fugue plays the role of the periodic signal because BWV 847 is mathematically clean (well-defined entries, inversions, augmentations) and because hearing it makes the cellular dynamics easier to follow.

The framing for the trio (generator + cell simulator + scanner) is the [trio white paper](https://norayr-m.github.io/drt-generator/whitepaper.html). This paper drills into the cell simulator specifically.

---

## <a id="how"></a>How it works — graph cells under a tick

The substrate is a graph $\G = (V, E)$ where $V$ is the set of cells and $E$ is the local-coupling edge set. Each cell $i \in V$ holds:

- a state value $s_i$ (a small number — colour, energy, activity);
- a local rule $f_i$ that maps the states of its neighbours $N(i)$ to its next state.

At every tick the engine applies the local rule synchronously across all cells:

```
for each cell i:
    s_i_new = f_i( { s_j : j in N(i) } )
update all s_i = s_i_new at the tick boundary
```

The synchronous update is structurally important: every cell sees the *previous* tick's neighbour states, never a partially-updated mix. That is the same discipline that synchronous logic uses on FPGAs and that the BACK_EDGE primitive uses in the companion DagDB engine — the tick boundary is a hard barrier between "the values everyone agrees were true last tick" and "the values being computed for this tick."

The HPA layer adds four named cells on top of the substrate: a hypothalamus cell (CRH at 150 Hz), a pituitary cell (ACTH at 250 Hz), an adrenal cell (cortisol at 350 Hz), and a central receptor cell that integrates their outputs. Each named cell has its own glow colour and its own audio frequency contribution. Pressing the gland buttons in the live demo causes that gland to "secrete" — its output rises, and the receptor's integrated signal rises in response.

The Bach BWV 847 audio drives the global tick rate. As notes fire in the score, the corresponding cellular activity peaks: the demo's audio and visual layers run on the same time index. This is for legibility, not for biological accuracy — biological circadian and ultradian rhythms are not BWV 847.

---

## <a id="reading"></a>Reading the output — HUD and what to watch

The HUD shows three things continuously:

- **Active cells** — how many cells in the substrate are currently above the activity threshold.
- **Glands** — per-gland activity for hypothalamus, pituitary, adrenal, with the receptor's integrated value.
- **Tick** — the simulation tick counter.

On the screen, three things are visible:

- **The graph** — a clear picture of the cell topology, with active cells lit and inactive cells dim.
- **The four named cells** — the HPA glands and the central receptor, drawn larger than the substrate cells, with their own canvases showing their per-tick output traces.
- **The audio** — Bach BWV 847 driving the timing. There is a mute toggle for those who prefer to watch silently.

Press a gland button to inject a transient. Watch the receptor integrate. Watch the substrate ripple as activity propagates out. Watch the HUD numbers settle as the system returns to its baseline.

---

## <a id="prototype"></a>The bio digital twin prototype — `digital_twin.html`

The same repository ships a second visualisation file, `digital_twin.html`, intended as a prototype of what a tissue-scale bio digital twin viewer might look like. It uses the same HPA-axis layer but adds a Socratic infinite-LOD zoom-out camera over a much larger cellular substrate.

Two things distinguish it from the main `index.html` demo:

- **Camera.** The user can zoom out to see the entire substrate at once; the renderer switches automatically from polygon drawing to point-cloud drawing once the per-cell pixel size drops below a threshold (Datashader-style LOD). This makes it possible to see a single coherent picture across a wide range of zoom levels without rendering cost exploding.
- **Scale framing.** The substrate is drawn as if it were a tissue cross-section, with the four named glands sitting at fixed positions and the cellular ripples visible at every zoom level.

Open the prototype directly at **[`digital_twin.html`](https://norayr-m.github.io/drt-cell-simulator/digital_twin.html)**. The same anti-claim discipline applies: this is a *prototype* of a viewer, not a validated bio digital twin. The biology layer is illustrative; the LOD camera is the engineering point.

---

## <a id="not"></a>What is not in the paper

The honest list of what this paper deliberately does not claim:

- **A quantitative endocrine model.** The HPA-axis layer is a four-cell visual playground. Real endocrine modelling involves continuous-valued ODEs, individualised parameter fitting, and clinical validation. None of that is here.
- **A norm-growth claim.** Earlier prose around this family of demos asserted that the aggregate of many partial views had bigger norm than the original signal — that $\|R(f)\| > \|f\|$. That inequality has been retracted in the v0.1 draft. The current claim is conditional and weaker: under four hypotheses and a bounded computational budget, the aggregate exposes structural features that are not accessible from the original signal alone within the same budget. The cell simulator is a visualisation of cellular dynamics on a graph, not a proof of the conditional claim.
- **A tissue-scale bio digital twin.** The prototype is a viewer, not a tissue model. Building a useful tissue-scale twin requires per-cell biological parameters, validated coupling rules, and external boundary conditions from clinical data. The demo provides none of those.
- **A speedup benchmark.** The simulator is browser JavaScript on a single CPU thread, not a benchmarked GPU compute kernel. The companion engine in the DagDB repository runs the same shape of computation on the GPU at much higher throughput; that is the path to real-time tissue-scale work.

---

## <a id="refs"></a>References and the trio

This repository is one of three that snap together into one workflow:

1. **[drt-generator](https://github.com/norayr-m/drt-generator)** — produces sparse projection matrices from a clock tick.
2. **drt-cell-simulator** (this repo) — receives the projections as cellular automata on graph topology. Each cell is a *receptor* with local rules over its neighbours' edges.
3. **[drt-scanner](https://github.com/norayr-m/drt-scanner)** — runs the transpose pass for inversion-fidelity verification.

End to end is *generator → cell simulator → scanner* — forward pass plus backward pass through the same matrices. Every step is sparse matrix-vector multiplication on a graph topology.

Trio framing in detail: **[Generator, Cell Simulator, Scanner — a sparse matrix-vector trio for bio digital twins](https://norayr-m.github.io/drt-generator/whitepaper.html)** ([markdown source](https://norayr-m.github.io/drt-generator/whitepaper.md)).

Distributed Reconstruction work — v0.1 in preparation by N. Matevosyan and A. Petrosyan.

Visualization co-authored with Claude (Anthropic).

---

> **Humble disclaimer.** Amateur engineering project. We are not HPC professionals and make no competitive claims. Numbers speak; ego doesn't. Errors likely.

GPLv3. See [`LICENSE`](LICENSE).
