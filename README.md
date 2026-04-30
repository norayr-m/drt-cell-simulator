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

The `digital_twin.html` companion file is a more biology-leaning variant, same engine.

## References

- Distributed Reconstruction work — v0.1 in preparation, by N. Matevosyan and A. Petrosyan.
- BWV 847 — Bach, Fugue No. 2 in C minor, *Well-Tempered Clavier*, Book I (1722).
- HPA axis biology — standard endocrinology references.

Visualizations co-authored with Claude (Anthropic).

## Author

Norayr Matevosyan

## License

GPLv3
