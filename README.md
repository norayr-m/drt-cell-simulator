# DRT Cell Simulator

> This is an amateur engineering project. We are not HPC professionals and make no competitive claims. Errors likely.

A browser-based cellular automaton on a graph: each cell is an "observer" with a small local rule, and the global picture emerges from many simultaneous local steps. The biological skin around it is the HPA stress-response axis (hypothalamus → pituitary → adrenal), with Bach's BWV 847 fugue scoring the cellular activity.

**[Live demo →](https://norayr-m.github.io/drt-cell-simulator/)**

## What it does

You watch a population of small cells, arranged on a graph, update themselves at every tick. Each cell only sees its immediate neighbors. There is no global controller; the global picture is whatever the local rules add up to.

The biology layer maps the cell graph onto the HPA axis — a real human stress-response loop — and re-uses a Bach fugue as the timing signal. So the demo simultaneously shows: (i) cellular automata on a non-grid graph, (ii) a toy reconstruction of a biological feedback loop, (iii) music as the clock that drives both.

## Why it matters (DRT angle)

This is the **biological observer-family slice** of the Distributed Reconstruction work. Each cell is one of the partial observers in the framework — a tiny window onto the system, with a small local "completion" rule. The interesting question is which features of the global state become visible from the aggregate of all these partial views, and which do not.

The paper this is informally illustrating is in v0.1 draft. The honest current claim is *not* that the aggregate has bigger norm than the original signal — that earlier formula has been retracted. The current claim is conditional and weaker: under a bounded computational budget and four explicit hypotheses, the aggregate exposes structural features that are not accessible from the original signal alone within the same budget. This demo is a visual playground for those words, not a proof of them.

## How to run

Clone and open `index.html` in a browser. No build step, no server, no dependencies beyond a modern browser. The audio is opt-in — there is an unmute button.

```
git clone https://github.com/norayr-m/drt-cell-simulator.git
open drt-cell-simulator/index.html
```

The `digital_twin.html` companion file is a more biology-leaning variant, same engine — a [zoom-out bio digital twin](https://norayr-m.github.io/drt-cell-simulator/digital_twin.html) where bio-brain-like structures tile millions of times all working at the same time. That demo was a Savanna prototype: the chromatic parallel scheduling pattern at 17 billion ops per second on M5 Max, the ternary metabolic states, the sense → compute → emit per cell — those choices informed Savanna's architecture.

## The trio — generator, cell simulator, scanner

This repo is one of three that snap together into one workflow:

1. **[drt-generator](https://github.com/norayr-m/drt-generator)** — produces sparse projection matrices from a clock tick.
2. **drt-cell-simulator** (this repo) — receives the projections as cellular automata on graph topology. Each cell is a *receptor* with local rules over its neighbours' edges. The signal arrives at each cell as a per-tick projection; the cell's local rules combine the projection with its own state to produce the next-tick state.
3. **[drt-scanner](https://github.com/norayr-m/drt-scanner)** — runs the transpose pass for reconstruction-fidelity verification.

End to end is *generator → cell simulator → scanner* — forward pass plus backward pass through the same matrices. Every step is sparse matrix-vector multiplication. The applied target is **bio digital twins** at tissue scale: the cell simulator's receptor field is exactly where biological cells receive signals through tissue connectivity (gap junctions, sinusoid topology, synaptic connectivity), and where local rules become Hill functions, Michaelis-Menten saturations, small ODE steps.

Full write-up: **[Generator, Cell Simulator, Scanner — a sparse matrix-vector trio for bio digital twins](https://norayr-m.github.io/drt-generator/whitepaper.html)** ([markdown source](https://norayr-m.github.io/drt-generator/whitepaper.md)).

## References

- Distributed Reconstruction work — v0.1 in preparation, by N. Matevosyan and A. Petrosyan.
- BWV 847 — Bach, Fugue No. 2 in C minor, *Well-Tempered Clavier*, Book I (1722).
- HPA axis biology — standard endocrinology references.

Visualizations co-authored with Claude (Anthropic).

## Author

Norayr Matevosyan

## License

GPLv3
