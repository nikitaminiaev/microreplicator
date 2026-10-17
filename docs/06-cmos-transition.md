# 06 — The CMOS transition

PMOS is the entry drug. A single p-channel transistor with an aluminium gate proves that
oxidation, boron diffusion, contact windows and metallization work at all. But **the
project cannot stay there.** Everything the cube is supposed to do — a controller on a
face, dense memory, a bus interface, low static power — needs complementary logic, and
complementary logic means CMOS.

This document explains why the transition is unavoidable and what exactly it costs: one
new mask (the well), one new dopant (phosphorus), and a set of rules about ordering and
isolation that are cheap to get wrong.

## 1. Why PMOS-only is not enough

A p-channel-only process can build ratioed logic: a PMOS pull-up against a resistor or a
saturated PMOS load. It works, and it is a legitimate teaching target. Its properties are
also exactly what a 1 mm cube cannot afford:

| | PMOS-only (ratioed) | CMOS (complementary) |
|---|---|---|
| Static current | flows whenever the output is low | essentially zero, one transistor is always off |
| Output swing | not rail-to-rail without extra stages | full swing by construction |
| Power per gate | milliwatts | nanowatts (leakage only) |
| Fan-out / drive | poor, load fights the pull-up | symmetric, both directions strong |
| Density of useful logic | low, area dominated by loads | high, no load resistors needed |
| Memory | not practical | latch, register, SRAM follow from the same masks |

The cube has no heatsink and no fan. Its whole thermal budget is the surface of a few
square millimetres. Ratioed logic dissipates continuously; complementary logic dissipates
only during switching. For a self-powered colony of millimetre cubes that is not an
optimization, it is the difference between a working controller and a permanent glow.

The same argument applies to the memory face: there is no SRAM without complementary
devices, and there is no register without a latch.

Historical cross-check: the first commercial metal-gate CMOS (RCA CD4000, 1968) was built
mirror-imaged — n-substrate with a p-well — because the company's base process was PMOS.
The physics is the same single-well junction isolation; only the starting substrate type
decided which device goes into the well.

## 2. What CMOS costs beyond PMOS

The delta is small enough to be honest about, and large enough to be a real project:

1. **A second dopant.** Phosphorus spin-on dopant (P509/P504 or equivalent) for the
   n-well and the n⁺ regions, with boron (B50/B154) for p⁺. Two sources, two bake profiles,
   and a strict rule about which goes first.
2. **A well mask.** The n-well is the deepest diffusion, and it sets the isolation.
3. **Inter-device isolation.** In the canon route this is the field oxide, kept in place
   and acting as a mask for all three diffusions plus the isolation between devices.
4. **Blocking oxide between n⁺ and p⁺.** The two regions are separated only by a grown
   oxide, so its thickness and quality are process-critical.
5. **Body contacts, front side only.** Aluminium on lightly doped p-silicon makes a
   rectifying (Schottky-like) contact, unlike on the N/N⁺ substrate of the PMOS phase.
   Both bodies therefore have to be contacted from the front: p⁺ to the substrate contact,
   n⁺ to the well contact. Without them the devices cannot even be switched on correctly.
6. **Latch-up awareness.** The parasitic thyristor p⁺(well) / n-well / p-substrate /
   n⁺(source) is live at 5–10 Ω·cm. Body contacts, isolation spacing and a low VDD at
   first power-up are the countermeasures; guard rings belong to the integration stage.
7. **Threshold control.** The fixed oxide charge shifts both thresholds in the same
   direction. The NMOS is the one at risk: at 100 nm of gate oxide and Qf/q = 10¹¹ cm⁻²
   the shift is about −0.46 V, which can push an intended enhancement NMOS into depletion
   (conducting at V_GS = 0). Levers: substrate resistivity 1–5 Ω·cm, gate oxide 50 nm
   instead of 100 nm, and a dry oxidation regime with a stabilizing anneal.
8. **BOE/HF work.** Opening wells and contacts in the oxide means buffered HF, i.e. the
   acid route. See §8 — this is an architectural constraint, not a preference.

## 3. Two routes: C6 (canon) and C5 (simplified)

| | C5 — 5 masks | C6 — 6 masks |
|---|---|---|
| Field oxide | removed entirely before the gate oxide | kept (isolation) |
| Gate (channel) windows | not needed: gate oxide grows over the whole wafer | dedicated mask #4 |
| Inter-device isolation | none (as in a PMOS-only flow) | yes (junction isolation plus field oxide) |
| Masks | 1 well · 2 n⁺ · 3 p⁺ · 4 contacts · 5 metal | 1 well · 2 n⁺ · 3 p⁺ · **4 gate windows** · 5 contacts · 6 metal |
| Use for | single inverter, test structures | anything that integrates more than one gate |

Fewer than five is not possible: a well, both S/D types, contacts and metal are all
required. C6 adds the gate-window mask, which is what lets the field oxide survive as
isolation. Start on C5 to get an inverter switching, move to C6 as soon as two gates have
to coexist on one substrate.

## 4. Mask ordering, and why it is not negotiable

**Well → n⁺ → p⁺.** Three independent reasons:

1. The well is the deepest diffusion. Its long high-temperature drive-in must not
   redistribute already-finished S/D regions. Phosphorus also diffuses more slowly than
   boron in silicon, so if the boron regions were made first they would drift during the
   well drive-in.
2. Phosphorus evaporates from quartz and is carried into subsequent furnace runs. Running
   all phosphorus steps first, then boron, then cleaning the tube, keeps the boron runs
   clean. (Single-tube operation still needs to be qualified.)
3. The n⁺/p⁺ separation relies on the blocking oxide, and phosphorus diffuses faster than
   boron in SiO₂ (about 7·10⁻¹⁶ versus 2·10⁻¹⁶ cm²/s at 1000 °C). The oxide must therefore
   block **boron**, which is why boron goes last.

## 5. Route, step by step

Substrate: **p-type boron, (100), 5–10 Ω·cm** (≈2·10¹⁵ cm⁻³). Body contacts are front
side; the back side is not qualified as a body contact.

```
[0 alignment marks] → [1 clean] → [2 field oxide] → [3 mask 1: well windows] →
[4 n-well diffusion] → [5 BSG strip + well re-oxidation] → [6 mask 2: n⁺] →
[7 n⁺ diffusion] → [8 blocking oxide] → [9 mask 3: p⁺] → [10 p⁺ diffusion] →
[11 mask 4: gate windows (C6)] → [12 gate oxide] → [13 mask 5: contacts] →
[14 Al evaporation] → [15 mask 6: metal] → [16 N₂ anneal] → [17 test]
```

Key steps with numbers:

| Step | Process | Target / control |
|---|---|---|
| 0 | Alignment marks before the field oxide; they survive all six layers | without them C6 cannot be aligned |
| 2 | Wet O₂ + H₂O, 1000–1050 °C, 1–2 h | field oxide 0.8–1.2 µm; budget the etch losses — ≥0.5 µm must remain at metallization |
| 3–4 | Mask 1 → BOE down to bare Si → phosphorus SOD spun on → predep 950–1000 °C, N₂+O₂, 20–40 min → drive-in 1100–1150 °C, N₂, 2–6 h | xj 3–5 µm, Nd 1–3·10¹⁶ cm⁻³; check Rs (~1–2 kΩ/□), junction depth, well–substrate diode |
| 5 | Strip BSG, then **mandatory re-oxidation** of the opened well, 1000–1050 °C, to 0.4–0.6 µm | without it the boron SOD p-dopes the whole well surface on the next step and destroys the PMOS channel |
| 6–7 | Mask 2 → n⁺ windows (NMOS S/D + well contact) → phosphorus predep 950–1000 °C, 15–30 min → drive-in 1000–1050 °C, 20–40 min | Rs 10–50 Ω/□, xj 0.5–1 µm |
| 8 | Dry O₂, 900–950 °C, 30–40 min | blocking oxide 100–150 nm. Boron penetrates only ~6 nm of oxide in 30 min at 1000 °C, so the margin is about ×15; the real risk is pinholes, hence dry oxide and a coupon check |
| 9–10 | Mask 3 → p⁺ windows (PMOS S/D inside the well + substrate contact) → boron predep 950–1000 °C, 15–30 min → drive-in 1000–1050 °C, 20–40 min | Rs 30–150 Ω/□, xj(p⁺) < ½·xj(well); keep p⁺ at least 20 µm from the well edge (well lateral diffusion ≈0.8·xj plus isolation margin) |
| 11 (C6) | Mask 4 → open the channel regions | channel = the whole S/D gap plus the gate-overlap allowance |
| 12 | HF dip 10–30 s → dry O₂, 900–950 °C, 30–60 min | gate oxide 50–100 nm; control on MOSCAPs on both the substrate and the well: thickness, C–V → V_FB → Qf, leakage, breakdown |
| 13 | Mask 5 → contact windows to all four p⁺/n⁺ regions | controlled BOE — the oxide over S/D is only ~150–250 nm after the gate oxidation |
| 14–15 | Thermal evaporation of Al, 0.5–1 µm → mask 6 → PAN etch (HNO₃:H₃PO₄:AcOH = 1:50:12, 40±5 °C, 15±5 min) or lift-off | gates, VDD/GND routing, input/output, probe pads |
| 16 | N₂ anneal, 400–450 °C, 15–20 min | ohmic Al–n⁺ and Al–p⁺. The Al–Si eutectic is 577 °C — do not exceed it. With shallow p⁺ watch for spiking; Al–1% Si if available |
| 17 | Metrology and test | see §7 |

Note that the metal gate is **not self-aligned**: S/D are formed *before* the gate, so the
gate must overlap the S/D regions by ≥5 µm on each side, and diffusions end up deeper than
in a poly-gate flow. That is the accepted price of skipping polysilicon, and it is why the
first devices are large.

## 6. What to expect electrically

- **NMOS on 5–10 Ω·cm.** At 100 nm of oxide and an Al gate, the ideal threshold is about
  +0.4 V; an oxide-charge shift of −0.46 V brings it to roughly zero, which is the
  depletion-mode risk. Published open-source measurements on a lightly doped substrate
  came out at Vt ≈ −0.12 V with a similar stack. Levers, in order of leverage: gate oxide
  50 nm (≈ +0.7…+0.8 V, because the channel term |Q_B|/Cox grows faster than the Qf term),
  substrate 1–5 Ω·cm, oxidation regime. Decide after the coupon measurements, not in
  advance.
- **PMOS in the well.** At Nd ≈ 2·10¹⁶ and 100 nm, Vt is estimated at −1.5…−2 V plus the
  oxide-charge shift. The well is easy to over-dope: at 10¹⁸ the threshold runs off to
  5–10 V. Target 1–3·10¹⁶ as the compromise between isolation, threshold and depth.
- **Bodies hard-wired to the rails.** Well n⁺ contact → VDD, substrate p⁺ contact → GND,
  ideally as a ring or at least beside each device. Without body contacts, latch-up cannot
  be fought at all.

## 7. First inverter: geometry and PASS criteria

Draft layout rules (to be refined, not final):

| Element | First test size | Why |
|---|---|---|
| L (both devices) | 50 µm | hand probing, alignment margin |
| W_n / W_p | 200–300 / 400–600 µm | mobility ratio 2–3 |
| n⁺ to well edge | ≥ 20 µm | lateral diffusion plus isolation |
| Body contact | 50×50 µm | one per device |
| Well around PMOS | W_p + 40 µm per side | well lateral diffusion 0.8·xj |
| Gate overlap | ≥ 5 µm per side | non-self-aligned process |

PASS criteria for phase 2:

1. **NMOS:** controlled I_D–V_GS with the body at GND, a sensible and explainable Vt sign.
2. **PMOS:** controlled I_D–V_GS with the body at VDD.
3. **Inverter:** transfer curve with an output swing ≥80% of VDD, gain >1 at the midpoint,
   no latch-up up to the VDD limit, and the static current recorded.
4. **Repeatability:** the result reproduced on at least two structures or wafers, with
   failures explained.

## 8. Coupon qualification before the full route

No coupon passes, no advance to the next stage:

| Coupon | Question it answers |
|---|---|
| **E1 — well** | process window for xj, Nd, sheet resistance; well–substrate diode leakage; lateral diffusion vs spacing |
| **E2 — n⁺** | phosphorus dose calibration (Rs 10–50 Ω/□, xj 0.5–1 µm) plus a crude Al contact I–V |
| **E3 — p⁺ + blocking check** | boron dose, and the sandwich coupon: an n⁺ strip covered by 100–150 nm of blocking oxide must show **unchanged** sheet resistance after the boron run. If it moved, boron got through |
| **E4 — gate oxide + MOSCAP** | thickness, C–V, Qf, leakage, breakdown; the depletion-mode decision is taken here |

Only then does the full C5/C6 route start.

## 9. Where CMOS physically runs

This is the point where the acid-free home line stops being sufficient, and it is worth
stating plainly:

- CMOS needs **two** dopants, and phosphorus sources are excluded by the safe-lab policy.
  That policy makes the home line PMOS-only, permanently, by design.
- CMOS also needs **BOE/HF** for well and contact windows.

So the CMOS phase runs in the acid route with a real chemistry lab: dilute BOE (HF < 10%),
a dedicated box, polypropylene/HDPE/PTFE labware, no glass, two people present, calcium
gluconate as the HF antidote, and the HF protocol as a precondition — not an appendix.
The safe line continues to do everything it can (lithography, MEMS, PMOS) and hands
coupons over through a defined interface. This is a project-level dependency that has to
be arranged, not a scheduling detail.

## 10. The horizon: poly-gate CMOS

Poly-gate (self-aligned) CMOS is a separate equipment horizon, and it is deliberately
deferred:

| | Al-gate (this document) | Poly-gate (later) |
|---|---|---|
| Gate stack | thermal SiO₂ + Al — both operations already exist in the PMOS phase | SiO₂ + polysilicon |
| Self-aligned S/D | no: gate overlaps S/D, more parasitic capacitance | yes, better matching and density |
| Gate etch | PAN or lift-off (already working) | plasma (SF₆ RIE) or a wet poly etch — a new module |
| Gate doping | not required | shared n⁺ gate (shifts the PMOS threshold) or split n⁺/p⁺ gates (+1–2 masks) |
| Gate film deposition | thermal SiO₂ grown + Al evaporated — both already in-house | in-house, **mid-flow, onto the processed wafer** (windows already etched): LPCVD SiH₄ (pyrophoric gas cabinet) or sputter a-Si + SPC ~600 °C |
| Purchased Si/SiO₂/polySi stacks | not needed | **unusable**: the stack is fixed at step 0, before the well exists; the project that relied on those wafers states plainly that this "prevents the expansion of the NMOS process into a CMOS process" — they built an in-house sputtered gate stack instead |

The decisive point is the **order of operations**. A well-based CMOS needs the well
diffused, the field oxide opened and the gate oxide grown *before* the gate material
lands — so the gate stack is deposited **mid-flow, onto a wafer whose windows are already
etched**. A wafer that arrives with its gate stack pre-fabricated cannot support that:
the well drive-in would dope and recrystallise the film, and there is no way to open
field-oxide windows through a blanket stack. Purchased Si/SiO₂/polySi wafers therefore
cap you at an NMOS-only flow — enough to replicate a proven 5 µm NMOS process, not enough
to move to CMOS. The Al-gate route in this document sidesteps the question entirely: its
gate oxide is grown and its gate metal is evaporated, both operations already qualified in
the PMOS phase.

The poly route, when it arrives, is thus a **deposition capability** rather than a
purchasing decision. That is the real content of the horizon: either LPCVD inside a gas
cabinet built for silane, or a sputtering chamber plus a 600 °C crystallisation anneal.

## 11. What CMOS unlocks

An inverter is not the goal — it is the gate the whole library passes through. From the
same mask set: NAND/NOR, latches, registers, SRAM cells, ring oscillators, level shifters,
and the bus interface that the cube's inter-cube link needs.

The arithmetic from the sizing budget: at a 2–5 µm node a transistor with contacts is
about 15×15 µm, so a 5 mm face with ~16 mm² of usable area holds tens of thousands of
transistors — an Intel 4004-class core (2300 transistors) plus KB of SRAM. A 1 mm face
holds hundreds of gates. In other words, CMOS is what turns a face of a cube into a
controller instead of a curiosity.

## See also

- the PMOS phase this follows → [04-diy-lithography-path.md](04-diy-lithography-path.md) §7
- why the copies stay good as processes get denser → [03-generational-stability.md](03-generational-stability.md)
- where CMOS sits in time → [05-roadmap.md](05-roadmap.md)
- what a face must fit physically → [01-concept.md](01-concept.md) §9
