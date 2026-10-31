# 🔦 Laser Cutting Machine - DXF Cutting Simulator

A single-file, zero-dependency, browser-based **laser cutting pre-flight simulator**.
Drop in a DXF, watch the toolpath run, and catch drawing and process errors **before**
the sheet hits the bed.

![no dependencies](https://img.shields.io/badge/dependencies-none-brightgreen)
![single file](https://img.shields.io/badge/build-single%20HTML%20file-blue)
![license](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Why simulate before cutting

Laser cutting is a one-way process. Once the sheet is pierced, the material is spent.
A simulator moves error detection from the machine (expensive) back to the office
(nearly free).

| Where the error is found | Cost |
|---|---|
| In the simulator | Seconds. Re-export, re-run. |
| At CAM / nesting | Minutes. No material lost. |
| At the machine | Sheet + machine hour + operator + rework + schedule slip. |
| At the customer | All of the above + scrap warranty + reputation. |

### What this tool validates

- **DXF integrity** — skipped-entity report in the status bar (e.g. `Skipped
  unsupported: HATCH x3, TEXT x12`). If your outline is a HATCH instead of a
  polyline, you find out here.
- **Units and scale** — `$INSUNITS` is honored and the `W / H` readout shows the
  real extents. Catches the classic "inch drawing read as millimeters" (25.4×
  too large).
- **Malformed geometry** — zero-radius circles, single-point polylines and
  non-finite coordinates are dropped. If you expect 40 objects and get 37,
  three are broken.
- **Block / INSERT integrity** — blocks are walked recursively with a depth
  limit; missing geometry is visibly absent in the viewport.
- **Curve fidelity** — LWPOLYLINE/POLYLINE bulges, ELLIPSE arcs and SPLINE
  control points are tessellated. A slot that downstream CAM flattens to a
  chord shows its true shape here.
- **Origin sanity** — geometry sitting at `X0 = 1,000,000 mm` is an origin or
  nesting problem, visible immediately in the `X / Y` fields.
- **Layer / power assignment** — toggle `Output` per layer and audit feature
  sets one at a time. A hole that belongs on a cut layer but sits on the
  engrave layer will not come out as a hole.
- **Cut order** — the strongest check. Blue dashed rapids and red cuts show the
  sequence; `Up / Down` reorders layers. Confirms **inner features cut before
  the outer contour** (otherwise the part drops and subsequent holes land in
  the wrong place), and exposes clustered cutting that concentrates heat.
- **Cycle time as a cost proxy** — the progress bar total, and the difference
  between *Path optimize* on and off, gives a comparable machine-hour figure.

### What it does **not** validate

This is a first gate, not a replacement for CAM or nesting software.

| Not validated | Consequence |
|---|---|
| **Kerf compensation** | The viewport draws the *centerline*, not an offset path. A 0.2 mm kerf turns a 10 mm hole into 10.2 mm. |
| **Flat pattern / bend deduction** | A flat pattern 1.5 mm short only shows up at the press brake. |
| **Manufacturability rules** | Min hole ≈ material thickness, min web ≈ 1×t, hole-to-bend-line clearance. |
| **Nesting** | No sheet boundary, clamps, slats or remnant logic. |
| **Machine collision** | No head or body model. |
| **Self-intersections / duplicates** | Parse fine, but double-cut the same path: excess heat, dross. |
| **Tolerance stack-up** | Mating part fits and clearances. |

---

## Features

- **DXF import** — drag-and-drop onto the viewport, or the file picker
- **Toolpath generation** — rapid moves + cut moves, per-layer feed rates
- **Path optimization** — nearest-neighbor segment ordering (toggleable)
- **Layer management** — reorder, recolor from an 18-swatch palette, toggle
  output, edit speed and power inline
- **Live simulation** — animated laser head with power-scaled glow, speed
  multiplier of 1× / 5× / 20× / 60×, elapsed/total time display
- **Interactive viewport** — drag to pan, wheel to zoom at cursor, double-click
  to fit, live coordinate readout
- **Adaptive grid and rulers** — millimetre graduations that rescale with zoom
- **Embedded demo** — loads automatically on first open
- **Zero dependencies** — one HTML file, works offline
