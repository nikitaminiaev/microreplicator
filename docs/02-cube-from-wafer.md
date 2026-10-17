# 02 — Cube from a wafer: unfold, fold, assemble

A 1 mm cube is **not** made by etching six walls out of the bulk from underneath. It is a
single **2D net** — a cross of six faces — that later folds itself into a cube
(self-folding micro-origami).

That is the central simplification: instead of six independent MEMS structures, there is
one flat one.

```
        face   face
        ┌───┐  ┌───┐
        │   │  │   │
┌───┐   ┌───┐  ┌───┐   ┌───┐   ┌───┐
│ f │   │ f │  │ f │   │ f │   │ f │   ← a cross of six faces
└───┘   └───┘  └───┘   └───┘   └───┘
        │   │  │   │
        └───┘  └───┘
                    ↓  folding along the hinges
                ┌───────┐
               /│      /│
              / │     / │
             ┌────────┐ │
             │  cube  │ /
             │  1 mm  │/
             └────────┘
```

## 1. Bulk path, not SOI

The net is part of the wafer itself. There is **no sacrificial layer at all**: the net
contour is cut through the full wafer thickness and the structure is held only by anchors
at the corners.

Why not SOI with a sacrificial oxide:

- in an SOI flow the sacrificial oxide under the whole net has to be dissolved — and
  without perforation (release holes) the HF never reaches the centre; the centre does not
  etch;
- **release holes are holes in the walls.** They ruin the inner surface of the cube, and
  that surface is exactly where the chips and MEMS of later generations have to go;
- in the bulk path the inner surface stays smooth and continuous.

## 2. Manufacturing sequence

1. **A thin wafer, verified against the wall thickness.** Wafer thickness = cube wall
   thickness (for example 100–200 µm for a hollow 1 mm cube with a 0.1–0.2 mm wall).
2. **Lithography** draws the net contour, the future hinge lines, and the pads and
   circuits on all faces at once. Every technology here is planar — that is the whole
   advantage.
3. **Through-etch of the contour** — DRIE for vertical walls, or TMAH with its sloped
   54.7° profile as an earlier, simpler route. The net stays attached on 4–6 anchors.
4. **On the single continuous inner side of the net** — chips (PMOS), MEMS sensors,
   electrodes, solder hinges. The surface has no holes, so all planar operations are
   available before assembly.
5. **Anchor release** — mechanical snap-off or trimming.
6. **Folding** — the hinges fold the flat cross into a cube:
   - capillary folding with solder or polymer: on heating, surface tension pulls the faces
     to 90° and creates a strong seam *and* electrical contact at the same time;
   - shape-memory alloys (nitinol) — bend driven by current;
   - bimetals — bend driven by temperature or UV.

Walls are never etched separately. They appear during folding, out of flat faces.
Hermeticity is a property of the assembly: the folded cube is sealed by its solder seams
(seam + electrical contact), and feedthroughs for power and optics are made through a face
by the same flat lithography.

## 3. Why this is simpler than it looks

- One release for the whole net instead of six.
- All faces are processed simultaneously on a flat wafer: lithography, deposition,
  circuits.
- Hinges are metal MEMS (level C): evaporation plus etch, no diffusion.
- The bus does not cross the hinges — it lands on the butt ends after folding
  (see [01-concept.md](01-concept.md) §3).
- Everything can be probed flat before the expensive steps.

## 4. What the unfold requires

- a thin wafer, matched to the wall thickness (100–200 µm; a thicker wafer works at the
  start, at the price of thicker walls);
- contour lithography — a DMD lithographer at 10–50 µm is enough for generation 1;
- through-etch of the contour (DRIE later, TMAH as the early simplified variant with a
  sloped profile);
- metal for hinges and solder (Al/Au evaporation);
- anchors holding the net until assembly;
- no release holes and no sacrificial layer — the surface for chips and MEMS stays intact.

## 5. Two generations

The full cube with electronics is split into two axes. Micro-scale practice starts
**before** PMOS.

### Generation 1 — shell cubes (axis A)

Structural cubes **with no payload**: hollow shells of 2–5 mm (wall 0.2–0.5 mm) with
solder seams, latches, and simple Al routing.

Requires only: contour lithography at 10–50 µm + bulk etch + hinges + folding.
**No furnace and no PMOS.** Start after bulk MEMS (cantilever) and DLP calibration.

The larger size of the first generation is deliberate: a thicker wafer gives a stronger
seam and easier folding.

### Generation 2 — cube with a smart chip (axis B)

Contour cut plus chips (PMOS / control) and MEMS on the inner side, then folding and
sealing. Requires a 1100 °C furnace and a PMOS process.

This is the "latest stage" of the cube program. The functional specialization of faces
(CPU on one face, memory on another) is the generation-2 refinement.

## 6. Staging

The cube is the **last** stage of the replicator, not the first:

1. PMOS on coupons, with a statistically reproducible process;
2. simple MEMS structures (cantilever, membrane, comb) — practice for lithography, etch
   and release on test structures;
3. only then the cube on a thin wafer: contour cut, chips and MEMS on the inner side,
   folding, sealing.

Corresponding gate: the first cubes (generation 1) can be attempted once bulk MEMS
(cantilever) and DLP-exposure calibration are through. Generation 2 waits for the furnace
and PMOS.

## 7. Open risks and caveats

- **Stiction.** After wet etching and rinsing, a free structure can collapse onto the
  substrate while drying. Handling is part of the recipe, not an afterthought.
- **Crystallography.** TMAH on (100) gives 54.7° walls; sharp vertical geometries need
  DRIE, which is a later tool class.
- **Wall thickness versus net stiffness.** A very thin net is fragile before folding and
  easy to fold; a thick net is robust and hard to fold. Generation 1 starts on the thick
  side on purpose.
- **Solder seam quality** sets both hermeticity and contact resistance; it is the single
  most important process step of the whole assembly.
- **Through-etch uniformity** across a thin wafer is the practical limit on wall
  thickness tolerance.

## See also

- face roles and the inter-cube bus → [01-concept.md](01-concept.md)
- why folding, and copies in general, do not degrade → [03-generational-stability.md](03-generational-stability.md)
- the tool that makes the net: a DIY DMD lithographer → [04-diy-lithography-path.md](04-diy-lithography-path.md)
