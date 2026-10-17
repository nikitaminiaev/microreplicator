# 04 — A DIY lithography bench, and the path from it to the cube

You do not start a micro-fab by buying a fab. You start by building **one projection
lithography bench**, and you grow the rest of the process around it. Every step below is
something a person with a bench, a fume hood, a hotplate and a multimeter can attempt in
order.

The bench is the entry point of the whole project: lithography makes the cubes
(see [01-concept.md](01-concept.md) §6 — optics does not miniaturize).

## 1. Optical train

```
[405 nm UV LED] → [condenser] → [DMD at datasheet tilt] → [tube lens f150–200] → [Plan 10× LWD objective] → [substrate]
```

The length of a telecentric train is **not** `reduction × f_obj`. From the geometry of a
DMD in the front focal plane of the tube lens:

```
L = 2·f_tube + f_obj + WD
```

So **L grows linearly with f_tube, not with reduction**: doubling f_tube doubles L.

| Reduction | f_tube | L | Envelope (Y × Z) | Volume | Gain |
|---|---|---|---|---|---|
| **10.1×** (f182) | 182 mm | **399 mm** | 316 × 248 mm | 11.8 L | ×3.5 |
| 7.5× | 135 mm | 306 mm | 269 × 202 mm | 8.1 L | ×5.1 |
| **5.0×** (f90) | 90 mm | **216 mm** | 200 × 151 mm | **4.9 L** | **×8.5** |
| 3.75× | 67.5 mm | 171 mm | 202 × 134 mm | 4.1 L | ×10.3 |
| 2.5× | 45 mm | 126 mm | 179 × 112 mm | 3.0 L | ×13.9 |

The physical floor is fixed by diffraction: at NA 0.25 and 405 nm it is about **1.6 µm**,
independent of reduction. That single number drives the design.

| Parameter | 10.1× | 5.0× |
|---|---|---|
| Pixel on sample (1080p, pitch 7.56 µm) | 0.75 µm | **1.5 µm** |
| Pixel on sample (SVGA, pitch 13.68 µm) | 1.35 µm | 2.7 µm |
| Field on sample | 1.4 × 0.8 mm | **2.8 × 1.6 mm** |
| Diffraction limit (NA 0.25, 405 nm) | 1.6 µm | 1.6 µm |
| Tiling | needed | needed, half as often |

**Why 5× is the right choice for the compact branch.** Halving the reduction doubles the
pixel from 0.75 to 1.5 µm — and 1.5 µm is still *better* than the 1.6 µm diffraction
limit. We would be paying for resolution the system cannot deliver anyway, while buying
184 mm of train length and an 8× smaller envelope. The 2.8 × 1.6 mm field also covers a
1 × 1 mm qualification die with no tiling, so entering PMOS does not get harder.

The 10× bench remains the main line until the gate G2 is passed; the 5× branch comes
after, and reduces the envelope from 538 × 336 × 422 mm (91 bodies) to
150 × 200 × 163 mm (20 bodies) — 76.3 L down to **4.9 L**.

## 2. Two mechanical decisions that decide whether it works

**A flexure stage, not a screw stage.** A screw stage has 0.1–0.2 mm of backlash in the
nut, which reappears as hysteresis on every direction change. The overlay budget is a few
micrometres. A flexure stage has no gap — it relies on the elasticity of the material.
Reference design: OpenFlexure block stage.

**One printed monolith, not bolted printed parts.** A printed-part tolerance of
0.1–0.2 mm over a ~200 mm train gives about 0.05° of tilt and a 0.2 mm beam shift —
unacceptable at a few micrometres of overlay. The compact branch replaces the whole
optical node (base, portal frame, beam, tower, mirror mounts, lens seats, DMD seat, camera
seat, heatsink fins) with a **single SLA monolith**: alignment is baked into the seats at
print time and then structurally rigid. Screws and thumbscrews disappear. A monolith also
has an order of magnitude higher natural frequency and no joint gaps, which is exactly
what the vibration problem needs.

An optical breadboard is not necessary. The reference open-source stepper design uses a
flat base plate with a 25 mm grid of Ø6.35 mm holes, stiff posts and printed brackets.

**Calibrate the shrink first.** Photopolymer shrinkage is 0.2–0.5%, which on a 10 mm part
is 20–50 µm — exactly the magnitude of the radial clearances in the monolith. If you print
"as drawn" without compensation, the lens will not enter its bore and the DMD board will
not enter its slot. Measure real shrinkage after washing and curing before committing.

## 3. Components (start list)

| Item | What to get | Price guide | Note |
|---|---|---|---|
| Used DLP projector | XGA 1024×768 or 1080p, DLP, with access to the DMD | $50–150 | DMD marking is mandatory; keep 2–3 donors in reserve if the budget allows |
| UV LED 405 nm | 1 W chip on a star PCB + heatsink | $15–40 | set power by **current** through a CC driver, never by voltage |
| LED driver | buck constant-current 350 mA with PWM dimming, 5–35 V input | $1–2 | dose stability; PWM for exposure time; + 12 V supply |
| Objective | 10× LWD infinity, **non-phase**, marking ~`L Plan 10X/0.25 ∞/- wd17.7` | $30–80 | RMS thread M20×0.705; NA 0.25, WD 17.7 mm, ~10 µm depth of focus |
| Tube lens | achromatic doublet Ø50, f 150–200 (f182 → reduction 10.1×) | ~$15 | reduction = f_tube / 18; chromatic correction is irrelevant for monochromatic light |
| Base plate | aluminium 150×200×6 mm, 25 mm grid of Ø6.35 mm holes | $15–30 | optics bolt to the plate, never to the projector body |
| XYZ stage | manual screw stage + a fine Z; later steppers | $0–40 | printed + M6 screws to start |
| Safety glasses | 405 nm, OD ≥ 2 | $5–10 | wear whenever the LED is on |
| Dark enclosure | printed PETG housing with a lid interlock | filament | UV must not escape |
| Camera | USB microscope, 50–200× | $30–80 | plus a calibration grating |
| Resist chemistry | AZ / S1818, Na₂CO₃ developer, DI water, IPA 99%, PTFE baths and tweezers, hotplate ≤150 °C | $60–140 | Priority 1 as well |

**Start cost: about $195–490** excluding shipping. Priorities 2–4 (NaOH baths, vacuum,
Arduino, oscilloscope, spin coater, furnace) are not bought before gate G2.

### Wavelength: 405 nm

Ordinary BK7/K9 glass transmits 92–95% at 405–410 nm, so no quartz is needed, and
AZ-family / S1818 / SU-8 resists work. At 365 nm you would need quartz or UV achromats and
an ordinary DMD may not pass the light — not worth it in v1.

### Donor projector rules

1. DLP only. LCD/LCoS is out.
2. Verify the stock image before disassembly: focus, cooling, colour wheel, UV filter.
3. Keep the stock light engine (condenser + TIR prism to the DMD) and replace **only the
   source** with a 405 nm LED — then a separate condenser is not needed.
4. Find the DMD marking, then determine pitch, window and allowable power at 405 nm.
   Illumination tilt must come from the datasheet of that specific DMD (±12° or ±17°;
   never transfer between models).
5. Only then remove the stock lens and mount the reduction optics.
6. Do not extrapolate other people's numbers onto your build: published DIY results range
   from ">10 µm" for an early 5× machine to a claim of 2 µm optics with a reliable step of
   8–12 µm.

If the stock engine is kept, the first light can be tested with a flashlight before the
LED swap — a working image on the wall means the DMD and drive electronics are alive.

## 4. Bench gates

| Gate | What must hold |
|---|---|
| G0 | First light: a projected image with the stock engine, field rectangular, no keystone |
| G1 | Lines of 50 and 100 µm in resist on glass, deviation < 30% |
| G2 | Lines of 10 µm, deviation < 10%, spread < 20% |
| G3 | Tiling: two frames stitched with overlay within budget (≤3 µm) |
| G4 | Ladder 5 → 2 → 1 µm, to find the real limit of the bench |

Everything in §5–§8 starts from a passed G2.

## 5. Stage 1 — first MEMS without a furnace

Available today, no thermal budget, no vacuum:

1. Si wafer 4″ N-type (100) with ~100 nm of thermal SiO₂ (bought, $5–10) — or any silicon
   with an oxide.
2. DMD lithography → windows in the SiO₂ (resist + NaOH etch of the oxide).
3. Strip the resist.
4. **TMAH 5–10% at 80 °C** etches silicon anisotropically through the windows; SiO₂ is the
   mask. NaOH is a cheaper alternative for early trials.
5. Undercut to free the beam or cantilever (this can take hours to days).
6. DI rinse and dry — carefully, because of stiction.

Structures: cantilever, bridge, diaphragm/membrane over a cavity, mechanical resonator.
Geometry is limited by crystallography (54.7° sidewalls on (100)); free thin structures
are possible but fragile. Output of the stage: exactly one free-standing beam or bridge,
visible under the microscope.

This is the **bulk path** — no sacrificial layer, no HF. It is what makes MEMS possible
before there is a single transistor.

## 6. Stage 2 — a tube furnace (the key lever)

One furnace, about **USD 100–300**, unlocks thermal oxide, boron diffusion and contact
anneal — that is the entire PMOS gate — and it widens the MEMS menu.

DIY construction: quartz or ceramic tube Ø20–40 mm, hot zone 200–300 mm, nichrome wire or
a ceramic heater, firebrick/ceramic-fibre insulation, Arduino + SSR + K-type thermocouple
PID controller, optional N₂ feed.

Characteristics: 500–1100 °C, ramp 100–300 °C/h, ±5% stability in the zone.

Safety: the furnace burns. Insulated enclosure, gloves, fire extinguisher. Boron comes
from a spin-on dopant, not from gas, so there is no toxic gas chemistry.

Because the furnace does not depend on lithography resolution below ~10 µm, it is built
**in parallel** with the bench gates, not after them.

## 7. Stage 3 — PMOS

Route:

```
clean → field oxide (furnace) → P+ pattern (DMD) →
boron diffusion from spin-on dopant (950–1100 °C) → clean →
backside body contact (I-V qualification) → channel window →
thin gate oxide (furnace) → contact windows →
Al evaporation → metal pattern → Al etch (NaOH) →
anneal in N₂ (furnace) → test
```

Shortcut that shortens and de-risks the route for a first discrete transistor: skip the
field oxide entirely and diffuse P+ from a **liftoff spin-on dopant** (no HF, no oxide
window opening), strip the BSG with NaOH (p⁺ etch stop), and do the metallization by
lift-off.

Extra equipment over the furnace: spin-on boron source $20–50; N-type low-ohm 4″ silicon
$5–15/wafer; a DIY probe station $50–150; oscilloscope / multimeter / LCR $50–150 used;
a test board $5.

**Acceptance criterion:** working I-V switching — gate voltage changes channel current.

The next step after PMOS is not more PMOS. It is **CMOS**, because a single-channel
process cannot supply the logic a cube needs: see
[06-cmos-transition.md](06-cmos-transition.md).

### What the bench can and cannot do today

| Operation | Status |
|---|---|
| Clean (NaOH + DI + IPA) | works |
| Resist + development (AZ + Na₂CO₃) | works |
| DMD lithography at 2–5 µm | works |
| Alkaline SiO₂ etch (NaOH) | works, slow: 1–10 nm/min |
| Alkaline silicon etch (TMAH) | works, anisotropic, safe |
| Al etch (NaOH) | works |
| Al evaporation (thermal) | works |
| Metrology (USB microscope + OpenCV) | works |
| Gate oxidation, boron diffusion, contact anneal | **needs the furnace** |
| Surface-MEMS release on sacrificial SiO₂ | **needs HF — excluded** |

Without the furnace you can still do all test structures that need no diffusion
(Al resistors, MOSCAP topologies, meanders) and MEMS level A by bulk TMAH etching.

## 8. Stage 4 — extended MEMS

After the furnace: thermal oxide as a mask or a layer, nitride/oxide films (after
LPCVD/PECVD), optionally a plasma etcher for anisotropic dry etching.

The safety constraint stays: classical surface release needs HF and is excluded. Safe
alternatives are purchased structural wafers with a pre-deposited sacrificial layer (only
if the sacrificial layer is alkaline-soluble — often it is not), the sacrificial-free bulk
TMAH path (the main safe method), and XeF₂ dry release of a-Si/poly-Si, which is a later
and more exotic step.

## 9. Budget to the first PMOS

| Stage | Item | Sum |
|---|---|---|
| 0 | base bench (priority 1+2) | $320–780 |
| 2 | tube furnace | $100–300 |
| 3 | electrical metrology + probe station | $130–400 |
| **Total** | | **$550–1480** |

## 10. Safety boundaries

Excluded on purpose, permanently, from the safe route:

- HF / BOE surface release;
- piranha cleaning;
- gaseous diffusion (PH₃/BF₃) — replaced by a spin-on boron source;
- LPCVD/PECVD with toxic gases — deferred, only safe alternatives;
- e-beam lithography — a separate micro-cube project, not part of this bench.

Rules that are not negotiable: UV glasses and a lid interlock whenever UV is on; no UV
with the lid open; furnace in an insulated enclosure; wet processes in a fume hood with
gloves and the correct neutralizer for the chemistry in use.

A supervised acid path exists as a separate interface (dilute BOE at a colleague's
facility, with a calcium gluconate protocol and an HCl-step RCA first). It is not part of
this bench, and the safe route is what this document optimizes for.

## 11. What not to do

- Do not buy priorities 2–4 before G2.
- Do not build the optical train from separately printed, bolted parts — that reintroduces
  the gaps and vibration the monolith exists to remove.
- Do not switch to the 5× branch before the 10× bench has passed G2.
- Do not put cube electronics in the optical train (except metrology and safety cells).
- Do not print a monolith without measuring real SLA shrinkage first.
- Do not expect micrometre features from a $150 projector: plan 2–5 µm on a series.

## See also

- what the bench is for, ultimately → [01-concept.md](01-concept.md), [02-cube-from-wafer.md](02-cube-from-wafer.md)
- how errors are kept from accumulating across copies → [03-generational-stability.md](03-generational-stability.md)
