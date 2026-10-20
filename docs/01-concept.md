# 01 — Operating principles

The microreplicator is a modular micro-fab that builds copies of itself. The machine is
assembled from many identical modules, and those modules are themselves the product: the
fab's job is to make more modules and more tools out of the same parts.

The base module is a hollow silicon cube, roughly **1 mm** on a side. Six faces is enough
for six functions, and six faces is exactly what a folded flat net gives you for free.

## 1. The cube as a building block

Each cube is two layers of design:

- **Chassis** — a universal shell: a folded net, six standard faces, standard power /
  data / fluid / optical bus, rigid outer skin. Three chassis types cover the space:
  1. **solid wall** — structure, insulation, protection;
  2. **hollow chamber** — internal volume for a payload or a reaction chamber;
  3. **window** — optical port, feedthrough, permeable membrane.
- **Payload insert** — the differentiation gene: heater, electrode, lens, nozzle or
  valve, probe, sensor, crucible, getter. The same chassis with a different insert is a
  different tissue.

Inserts are also products of the replicator (lithography + folding + assembly), so the
insert library has to stay inside the same process envelope as the cubes themselves.

Outward-facing side: a hard protective skin plus standard connectors on all six faces.
Inside: chips, electronics, payload. The exception is hot organs, where the electronics
must live outside the insulation (see §4).

### Why the cube is universal — and why one cube is not a replicator

The cube is universal for three reasons, and none of them is functional: one process
(every cube and every insert comes out of the same flat net and the same lithography
envelope), one interface (six standard faces, edge-pad bus, kinematic docking), one
differentiation axis (the insert, or, more generally, the organ recipe). A few chassis
types times a library of inserts cover the whole organ space, and because cells are
identical and binned, the same part can be swapped, re-tasked or moved to another organ.

That combinatorics is not self-sufficiency. A single cube carries one function on its
faces; it cannot pattern a wafer, grow an oxide or cut a net — it cannot make another
cube. Replication is a property of the colony, not of a cube: it appears when many cubes
form organs — the lithography organ first of all, plus the furnace, the wet bench, the
assembly line and the matrix logistics (§4). One cube is a part; a cluster of cubes
around a lithography organ is a factory (§6).

## 2. Six faces, seven roles

Functional specialization of faces — design decision: spread functions across faces
instead of stacking everything on one.

| Face | Role | Process level |
|---|---|---|
| Top | logic / SoC, antennas, **recessed optical window** for the laser bus | 6 (hardest) |
| Center | CPU / compute core | 6 |
| Left | MEMS sensors: pressure, acceleration, temperature | 4 |
| Right | dense memory | 5 |
| Bottom | interfaces, power, I/O **+ piezo drive legs** | 5 / 7 |
| Rear | MEMS diaphragm / gate (port, valve) | 2 |

Six faces, seven roles: two roles share a face. The optical window is part of the top
face — it is standard equipment, not an option. The drive legs live on the bottom face;
a face that carries bus pads cannot be used as a road, because walking abrades the
contacts (fretting).

### Why CPU and memory sit on different faces

This is chiplet logic, and it buys four things:

- **area** — two faces instead of one means twice the transistors at the same cube size;
- **yield** — a memory defect does not kill the CPU, which matters a lot at DIY process
  nodes of 2–5 µm;
- **heat** — the CPU runs hot, memory does not like heat; separating them decouples both;
- **independent optimization** — memory can be denser and simpler, logic faster, with no
  single compromise process.

## 3. Interfaces between cubes

### The bus lives on the butt ends

**Metal cannot cross a fold** — traces through the hinge crack during folding. So the
bus is not routed through the hinges. After folding, the faces meet edge to edge, mating
pads press together, and contact closes by the simple fact of assembly: self-alignment,
no manual aiming. Solder, when reflowed, is pulled by surface tension and delivers three
things at once — mechanical seam, seal, electrical contact.

Three working options, used in combination:

1. **Edge pads + solder (baseline).** Power delivery needs a DC contact anyway, so the
   base bus rides with it. On a 5 mm edge at 20–50 µm pitch, **hundreds of pads** fit —
   a wide, slow, parallel bus is physically possible.
2. **Serial link over 4–8 contacts (smart).** SPI/LVDS-like over millimetre distances at
   tens of MHz on plain CMOS. Fewer pads means a more reliable joint, but SERDES logic is
   needed on both faces — hundreds of transistors, which is cheap at tens of thousands.
3. **Contactless coupling between folded faces** — capacitive or optical (LiFi). No DC
   contact at all, but transceivers are required. Fallback option.

Recommended: DC edge pads + solder for power and the base bus, serial link for data.
Start with the wide slow parallel version because it is easier to debug, and shrink to
serial as confidence grows.

### Docking pins: kinematic coupling

Precision docking uses **three pins + three V-grooves** — an exact constraint. It
self-centres and repeats to **0.1–1 µm**. The grooves are etched in TMAH on (100) silicon:
atomically smooth 54.7° walls, a free gift of anisotropic etching. Pins are electroplated
Ni, Ø50–100 µm.

Pins extend from the cube's own faces, driven by a MEMS actuator — an electrostatic comb
plus a spring: force on the order of tens to hundreds of µN, throw 20–50 µm against a
spring of about 1 N/m. A retracted pin is a disconnect, which is what service uses.

Pins carry only slow, service-grade signals: presence ("seated — contact closed"), ID /
cell type, and interlock ("locked — power to the bus and enable the laser"). Pin contact
is tens of mΩ at tens of mA, which covers power and service with margin. Retracted most
of the time, the pins only work during docking and undocking.

Magnets are not used for docking: no positioning geometry, ±0.5 mm, and they snap.

### Optical bus for speed

After docking and a pin handshake, bulk data goes over a short-range laser link, face to
face, 0–100 µm.

Link budget:

- geometry: 1 mW VCSEL, ~15° divergence → about a **Ø13 µm spot** at 50 µm; a
  100×100 µm photodiode captures practically all of it;
- photocurrent about 0.5 mA against a receiver sensitivity of about 10 µW at 100 Mbit/s —
  roughly **20 dB of margin**;
- even a home-made ZnO LED (Lambertian, 1 mW) delivers about 0.5 mW into such a diode, so
  the transmitter can eventually become our own;
- spectral pair: Si photodiode + GaAs VCSEL at 850–980 nm (silicon cuts off at 1100 nm) —
  well matched;
- rates: LED class, tens of Mbit/s (enough for coordination); VCSEL, Gbit/s.

The main win is tolerance relaxation: optics need 20–50 µm alignment (the spot width),
edge pads need about 5 µm. The laser bus **loosens the docking accuracy requirement by an
order of magnitude**, which is why the 0.1–1 µm Kelvin pins are more than sufficient.
Secondary wins: no wear (contactless, unlike rubbing pads) and galvanic isolation. Cost:
transceivers on the optical faces plus a few milliwatts of TX power per link.

Crosstalk is handled by recessed optical wells (the window on the vision figure — an
AI-generated concept illustration, approximate by nature — is standard) plus time slots
or distinct carriers on adjacent links.

### Drive face

Locomotion is piezo-based at the start, on a dedicated drive face.

- Cube of 1 mm silicon ≈ **2.3 mg (23 µN of weight)**. A mN-class clamp (piezo or
  electrostatic) holds it with ×100 margin, including on a vertical wall.
- Inchworm: step 0.2–1 µm at ~100 Hz = **20–100 µm/s**. Slow, but enough for local
  rearrangement.
- Order of adoption: **stick-slip** first (a single piezo and asymmetric friction, simpler),
  then **inchworm** (clamp–extend–release, more precise).
- Material: **ZnO** (piezo d33 ~12 pm/V) is already ours — the same material as a light
  emitter. One material, two functions: legs and light. Alternative: AlN (CMOS-clean,
  sputterable). PZT is avoided for now (lead, process temperature).
- Long range is the matrix (§4): rails, coils, a carrier cart. On-board drive is for local
  moves only; a cube sits in a dock 99% of the time.

## 4. Cell, tissue, organ, factory

```
Cell    (1 mm cube)   base building block
Tissue  (volume)      cells of one functional class, held by the matrix
Organ   (cluster)     a working unit: furnace, CZ puller, lithographer
Factory (organism)    organs + logistics, closed loop
```

**The tissue is volumetric, and the matrix holds it.** Cubes are not spread in a flat
lawn of parts: they sit in a three-dimensional **matrix**, the engineered analogue of the
extracellular matrix of living tissue. The matrix is an organ in its own right — the layer
that deliberately does not miniaturize away — and it does the housekeeping that no single
1 mm cube can do:

- **mechanics** — it holds cells in a fixed 3D arrangement; a cell docks into a slot and
  is released on command (the chassis-plus-insert cell of §1 is its unit);
- **power, data, transport** — long-range power and the bus ride in the matrix (§3:
  rails, coils, a carrier cart); a free slot holds nothing but a landing pad;
- **service, an immune loop** — telemetry spots a dead cell, a service cell drives along
  the matrix, undocks it and installs a spare. That is what keeps every cube replaceable
  without dismantling the tissue;
- **addressing** — the slot says where a cell is (address from the matrix), while the cell
  keeps its own identity.

An organ is a recipe, not a piece of hardware. Any organ = a set of cells plus their
configuration. A cell's purpose is defined by the organ recipe, not by the cell itself —
digital spatial assembly. A low-temperature organ is disassembled back into cells and
rebuilt for another job; a hot or vacuum organ stays soldered.

**Two mating classes** — this splits the architecture into two repairability classes and
must be fixed when each organ is designed:

| Class | Mechanism | Where |
|---|---|---|
| Reversible | magnet / piezo / friction, ≤300 °C | logistics, cold organs, reconfiguration |
| Irreversible | solder seam / diffusion weld (same solder as in folding) | hot and vacuum organs, sealing |

Magnets lose strength near the Curie point and piezo depolarizes, so a hot path cannot be
held by reversible latches. A hot organ is assembled as a single soldered unit.

**Hot organ carrier** — three levels:

```
Core     reaction volume (molten Si, zone melting)
Tissues  concentric functional shells: crucible → heater → reflector →
         vacuum gap / aerogel → getter → sealed housing
Contour  infrastructure OUTSIDE the insulation: buses, pumps, cooling,
         measurement rings — and only here the electronics and control
```

The vacuum gap between shells is the main thermal shield on the way to 1420 °C. Reaction
volume is formed either by merging cavities of adjacent cells, or by walls forming a
chamber between faces (preferred for CZ pulling: a cold ~2×2×3 cm chamber with concentric
walls servicing the growing ingot; the feedstock enters from above through a through
channel).

Hard rule: **a soldered joint is not repaired.** A failed cell inside a soldered assembly
(hot / vacuum organ) condemns the whole organ — which is why the organ is designed as a
replaceable unit. Only cold, reversible structures are designed to be serviceable.

### The replication cycle

Each arrow is an organ, and no arrow fits inside a cube:

```
feedstock (silica, water, dopants, aluminium, polymer)
     ↓  crystal organ (CZ puller, hot)
ingot → wafer (slicing, lapping, polishing)
     ↓  lithography organ (405 nm LED, DMD, reduction optics) + furnace + wet bench
patterned net on a wafer
     ↓  etch + fold + solder + flat probe
cube
     ↓  assembly organ, matrix logistics
cube docked into the colony
     ↓  test, service loop
replaced or returned to service
```

The loop closes only at colony scale. Every stage needs macroscopically large, flat
equipment — optics above all — so the minimum replicating unit is a cluster of cubes
around a lithography organ, not a cube. This is why the cube is the last stage of the
program ([02-cube-from-wafer.md](02-cube-from-wafer.md) §6) and why one cube, alone,
can only be a part.

## 5. Bootstrapping: seed and vitamins

Self-replication is not "grow silicon crystals from sand on day one". It is the
**seed and vitamins** model: the starting seed carries a stock of the hardest components
(transistor chiplets, laser diodes, piezo elements), and the colony assembles new
functional modules out of them.

Proposed seed contents:

1. **Master control** — a microcontroller with a fixed initial deployment algorithm,
   the "genetic code" of the factory.
2. **Vitamin vault** — a passive container with ready-made semiconductor dies (logic
   chiplets, GaAs diodes, piezo elements).
3. **Starter printer** — a micro-laser with a positioning system for printing the first
   elastic joints from the initial polymer stock.
4. **Capillary micro-pump** — draws external liquid feedstock into the deployment zone.
5. **Starter path layer** — electrochemically deposits the first power buses on the raw
   substrate.

The colony grows the rest of its organs as it goes.

## 6. Why the production system is not inside the cube

The production of a cube never fits inside a cube. **The net is always larger than the
cube it becomes**, and even a small net needs lithography, etch, deposition and metrology
that are physically flat and macroscopically large. What the cube gives is a rigid
process control: standard geometry, standard interfaces, replaceable units.

Corollary, and it is a hard design boundary: **optics does not miniaturize.** Diffraction
is scale-free, so lithography is never "shrunk into a cell". Lithography makes cubes; it
is the entry point, not a miniaturization candidate. The same reasoning puts the DMD board
(45×32 mm) outside the cube class entirely — see the lithography document.

## 7. Criteria for replacing a macro module with a micro one

A micro module may replace a macro analogue only after it is verified to:

- perform the same function;
- have a measured range and repeatability;
- not degrade the yield of the whole process;
- survive the required chemistry, temperature, vacuum and electrical load;
- have a safe failure state and a safe mode;
- be replaceable or re-manufacturable;
- leave the macro module available as reference or backup for several successful
  generations.

This is the gate that keeps "we miniaturized something" from being confused with
"we replicated something". Reducing the envelope is not self-replication.

## 8. Hidden ace: flat probe test before folding

Because all faces are flat before folding, **the cube can be tested flat, before
assembly**. Bad memory is mapped out (bad-block table in the CPU), a bad net is scrapped
before the expensive folding and sealing steps. A classical 3D stack cannot do this: its
defects are buried inside. A mandatory route step:

```
flat probe test → folding → final test
```

## 9. Sizing: no fixed numbers, a direction

The step size is not frozen yet — the principle is: **go from a larger cube down to a
smaller one**, and only step down when folding is stable, the seam is hermetic, and
face logic works at the current size.

Face electronics are limited by the process node (2–5 µm), which is fixed by the
lithography tower. An estimate for reachability (PMOS at 2–5 µm, a transistor with
contacts is about 15×15 µm):

| Face | Usable area | What fits |
|---|---|---|
| 5 mm | ~16 mm² | tens of thousands of transistors — an Intel 4004-class CPU (2300 transistors) plus KB of SRAM on one face |
| 2–3 mm | ~3–5 mm² | 4004-class is tight, simple logic is comfortable |
| 1 mm | ~0.5 mm² | hundreds of gates — a simple state machine, hundreds of bits of configuration memory, a sensor with its support circuitry |

Consequence: a full "CPU core + embedded memory" on a 1 mm face at 2–5 µm does not fit.
A full SoC at 1 mm needs 65 nm-class nodes and e-beam — a distant horizon. Mechanics also
argue for a larger start: a thicker wall means a stronger seam, easier folding, more etch
tolerance.

## Reading order

- how a cube is physically made → [02-cube-from-wafer.md](02-cube-from-wafer.md)
- why copies stay good → [03-generational-stability.md](03-generational-stability.md)
- what to build first → [04-diy-lithography-path.md](04-diy-lithography-path.md)
