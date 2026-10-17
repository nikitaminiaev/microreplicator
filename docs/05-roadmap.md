# 05 — Roadmap and horizons

Point of reference: September 2026, at 20 h/week of personal work on the line. Two-photon
printing is deferred, and the freed hours go to the DLP exposure / lithography line. The
furnace and the acid (HF/BOE) work happen in parallel at a colleague's facility.

Estimates are engineering projections, not commitments. The realistic band is given
alongside the optimistic one, because the historic failure of DIY fab projects is
schedule optimism, not physics.

## 1. Near term: lithography to 2–5 µm

| Milestone | Target |
|---|---|
| G2: 10 µm stable | November–December 2026 (2–3 months) |
| G4: ladder 5 → 2 → 1 µm, system limit found | February–March 2027 (end of the 6-month window) |
| Reproducible 2–5 µm on a series + overlay | spring 2027 |

Etching is not part of this sub-project: NaOH/TMAH work continues in parallel on the
parent line (gate at week 8). By spring 2027 the stack is "lithography at 2–5 µm plus a
calibrated etch".

The practical floor of a rigid DIY assembly is vibration at 1.2–1.8 µm, so 1.5–2 µm on a
series is closer to a limit than to a guarantee.

## 2. First transistor from a whole wafer

- Optimistic: 2–3 months without the furnace, 2–4 months with it → PMOS around spring 2027.
- Realistic: **6–12 months at 8–20 h/week → March–September 2027**.
- Lever: the 1100 °C furnace (USD 100–300, 2–4 weeks to build) does not depend on
  lithography resolution below ~10 µm, so it is procured and built **in parallel** with
  the bench gates instead of after them. This moves PMOS from "autumn 2027" to "summer
  2027".
- Chain of dependencies: furnace (2–4 weeks) → liftoff spin-on dopant (2–3 iterations) →
  Na⁺ drift of Vt → overlay at small gate lengths.

Net: **first working PMOS, mid-2027 optimum, early 2028 as a plausible worst case.**

Risk ordering matters: the B-milestone (liftoff-SOD plus diffusion, checked on a pn diode)
runs **before** the transistor. A diode tells you whether the doping and the oxide are
alive without spending a whole transistor lot on it.

## 3. With strong AI assistance in the loop

AI removes the intellectual work: experiment design, TCAD modelling, pattern-level
generation, dose/focus calibration, photo analysis, defect-cause search, PDK upkeep, SOP
writing. It does not remove materials, supplies or physics.

The first loop it closes is

```
hypothesis → recipe → run → measurement → score → decision on the next run
```

Goal of the first stage is not an autonomous factory; it is a **parent macro-fab** with a
reproducible PMOS and the ability to make the first components of future micro modules.

| Period | Step |
|---|---|
| 2027 | PMOS + PDK + nightly automated runs |
| 2027–2028 | **CMOS transition (phase 2)** — well mask, second dopant, acid line; first inverter, then gates and latches ([06-cmos-transition.md](06-cmos-transition.md)) |
| 2027–2028 | MEMS modules on purchased wafers (micro furnace, micro pump, micro actuator, micro needles) — replacing macro functions one at a time |
| 2028–2030 | first micro module with external tanks and pump; compared against its macro analogue |
| 2030–2033 | prototype micro-cube: e-beam in ~1 mm³ (vacuum cell, 1–10 kV, cathode) — separate R&D; the first cube requires hybrid assembly |

The macro-fab keeps producing parent wafers and large assemblies throughout.

## 4. When micro-replication actually starts

Reaching "everything micro" is not gated on lithography resolution. There are two axes:

- **Axis A — structure (shell cubes):** lithography at 10–50 µm + bulk etch (TMAH/DRIE) +
  hinges + folding. **No furnace required.** Start as soon as bulk MEMS (cantilever) and
  DLP calibration are done; the first cubes are 2–5 mm **without a chip**
  ([02-cube-from-wafer.md](02-cube-from-wafer.md) §5).
- **Axis B — smart chip (payload):** lithography at 1–5 µm + furnace + PMOS/PDK. The
  "cube with electronics inside" horizon (generation 2).

Then:

| Milestone | Target |
|---|---|
| Micro-cell assembled from proven micro modules | ~2030 |
| Sustained micro-replication (no manual intervention in the cycle) | 2032–2035+ |
| Organ architecture (cells → tissues → organs → factory) | 2030+ |

The last line is the hardest and unproven leap in the program: "the machine prints its own
1 mm organ" is a change of level, not a linear continuation of a millimetre-scale fab.

## 5. The direction of miniaturization

Macro-to-micro transition does not happen by shrinking the whole rig at once. It happens
by **replacing functions one at a time**, in three waves:

1. **Wave 1 — liquid loop.** Wet etching (microfluidic bath insert), resist application and
   development (capillary dispenser instead of a spin coater), plating instead of PVD for
   metal, probe inserts and DAQ for test and metrology. Scale laws help here: diffusion and
   heat scale as `t ~ L²`, so a 1000× shrink accelerates a process by 10⁶.
2. **Wave 2 — hot path.** Furnace and anneal as a heater insert inside a 3×3×3 cluster used
   as a thermal shield, or anodized oxide at room temperature as a substitute; PVD in a
   4×4×4 cluster.
3. **Wave 3 — feedstock.** Raw material handling, liquid precursors, recycling of failed
   cubes.

And one permanent exception, restated because it is easy to forget: **lithography does not
miniaturize.** Diffraction is scale-free. The lithography tower is an organ that produces
cubes; it is never a candidate for shrinking into one.

## 6. Assumptions and risks that move the dates

- All estimates assume 8–20 h/week of personal work.
- Biggest movers: the furnace, liftoff-SOD, Na⁺ drift, overlay.
- The acid gate stays closed while the safe route is green: the safe route remains primary
  as long as the B-milestone (liftoff-SOD) converges within N iterations **and** Vt drift
  on a working PMOS stays below threshold. If it opens: RCA with an HCl step first (safer,
  removes Na⁺), dilute BOE only if liftoff does not work, and the full HF protocol with
  calcium gluconate.
- AI accelerates the recipe → print → measure loop, but not orders, supplies or hardware
  iterations. Buying takes as long as buying takes.

## 7. What to do next (the next three months)

1. Bench gates G0 → G2: first light, 50/100 µm lines, then 10 µm lines.
2. Bulk MEMS: free one cantilever by TMAH on a purchased oxidized wafer.
3. Furnace in parallel: design, procure, build, verify 1100 °C and the ramp rate.
4. Journal every run: recipe, dose, focus, photo, measurement, one row in a CSV. No entry
   means the run did not happen.
5. Do not buy priorities 2–4 beyond this list until G2 is passed.

## See also

- the bench and its stages in detail → [04-diy-lithography-path.md](04-diy-lithography-path.md)
- why the copy chain stays stable → [03-generational-stability.md](03-generational-stability.md)
- the cubes themselves → [02-cube-from-wafer.md](02-cube-from-wafer.md)
