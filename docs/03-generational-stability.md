# 03 — Why copies do not degrade

Self-replicating systems are supposed to drift: every copy inherits the errors of the
machine that made it, the next copy inherits those plus its own, and quality decays
geometrically until the lineage dies. Von Neumann called it mutation catastrophe, and it
is the classic objection to any replicator design.

The answer here has two layers, one physical and one algorithmic. Neither is a hope; both
are standard practice in semiconductor manufacturing, applied deliberately.

## 1. Physical layer: optical demagnification compresses defects

Industrial steppers (ASML, Nikon) work at 4× or 5× **reduction**, not 1:1 — and the reason
is exactly this: the mask can be coarse, the image is fine.

Concretely, take a generation-1 machine with a defect: a micro-mirror edge is off by
**2 µm**.

- At 10× reduction, that edge lands on the generation-2 substrate as an error of
  **0.2 µm**.
- At a process node of 2–5 µm, 0.2 µm is well below the node, and near or below the
  diffraction limit of the optics (1.6 µm at NA 0.25 and 405 nm).

So the physical error of the parent is **shrunk out of relevance** for the child. The
error does not survive as an error; it survives as a smaller number. The same mechanism
applies at every generation, which is why the chain is self-limiting rather than
self-amplifying: absolute geometric errors are compressed by the imaging system, and only
errors that scale with the whole machine (wafer thickness, furnace temperature, chemical
purity) persist.

## 2. Algorithmic layer: fault tolerance in the mask engine

A generation-1 DMD can have 2–3% of its micro-mirrors stuck open or closed — dust, a
defective suspension, a broken via. A mirror is a mechanical part, and mechanical parts
fail.

The replicator handles it the same way a memory controller handles bad cells:

1. The machine projects light onto its own camera and performs **self-diagnosis**.
2. Software builds a **bad-mirror map**.
3. During exposure, instead of a single pass it does a **multi-exposure with shift**: if a
   point has to be exposed under broken mirror #105, the stage shifts by one step and a
   healthy neighbour, mirror #106, exposes it.

The mask engine therefore does not need to be perfect; it needs to be *known*. A
known-bad element is a routing problem, not a defect.

## 3. Algorithmic layer: closed-loop overlay

The mechanical stage (for example an OpenFlexure-type flexure stage) has backlash,
thermal drift and vibration.

- A microscope camera reads **overlay alignment marks** on the wafer in real time.
- If the mechanics have shifted 150 nm to the left, the computer **shifts the image on the
  DMD by 150 nm to the right** before the UV flash fires.

Software compensates for imperfect hardware. This is what makes a cheap stage usable at
all in a process whose overlay budget is a few micrometres.

## 4. And something the vertical stacks cannot do: test flat, bin early

The cube is probed **flat, before folding** ([02-cube-from-wafer.md](02-cube-from-wafer.md) §2).
A defective net is scrapped before the expensive steps; a partially bad one is binned and
its bad blocks are recorded in a table in the CPU. Defects are therefore converted into
*yield loss*, which is measurable and bounded, rather than into a slowly decaying
population.

## 5. Service and immunity

Failures after assembly are handled by an immune-system analogue: telemetry plus a
periodic built-in test (continuity probing of neighbours through the edge pads) →
detection → a service cell travels along the matrix → undocking by retracting the pins →
extraction → replacement from stock → test.

The hard rule that makes this workable: **a soldered joint is not repaired.** A failed
cell inside a soldered assembly condemns the whole organ, so hot and vacuum organs are
designed as replaceable units, while only cold reversible structures are designed to be
serviced. Recycling of a dead cube (desoldering, silicon return) is a later wave, together
with raw material handling.

## 6. Where the chain really does stop

It would be dishonest to present the mechanism above as unlimited. At fixed equipment
there are physical walls:

| Stage | Realistic result | What the loop can do |
|---|---|---|
| First exposure | 10–50 µm on dummy samples | find dose, focus, development |
| First working PMOS | about 5–10 µm | optimize diffusion, oxide, Al, contacts |
| Qualified start process | **2–5 µm** | reduce spread, improve overlay and yield |
| Next limit | about 1–2 µm | needs new optics, etch and doping modes |
| Below 1 µm | separate technology branch | design and qualify new equipment |

The specific limits:

- **Lithography.** A 405 nm DMD with reduction optics can deliver a working PMOS at
  2–5 µm. Below roughly 1–2 µm, diffraction, aberrations, focus, DMD pitch and resist
  quality dominate. Optimization cannot extract a sub-micron line from optics that
  physically do not transmit it.
- **Etch.** Wet etching is convenient for a first PMOS but usually isotropic; as
  geometries shrink, lateral undercut becomes comparable to the feature itself.
  Anisotropic dry etching (RIE class) is a different tool class, not a smarter recipe.
- **Doping.** Diffusion from spin-on dopant or a solid source suits large P+ regions. For
  short channels, junction depth and lateral spread become the constraint; ion
  implantation is a separate, expensive equipment class.
- **Overlay and metrology.** A 2–5 µm process needs interlayer alignment substantially
  better than the node. A camera, fiducials and good algorithms can bring a manual stage
  to a working state, but thermal drift, backlash and particles still require mechanical
  improvements.
- **Vibration.** Open-source fabrication benches report 1.2–1.8 µm of vibration
  sensitivity even on rigid structures. A printed and bolted assembly will do no better.
  This is why the plan targets **2–5 µm on a series**, not 1 µm.

What can still be improved without any new principle of equipment: dose, time and
exposure profile; focus and DMD field map; clean/resist/develop/dry recipes; oxidation
and boron diffusion profiles; wet-etch rate and selectivity; Al thickness and uniformity;
manual and then automatic alignment; particle and contamination control; electrical
PASS/FAIL criteria; CAD, pattern levels, test coupons and the PDK; statistical design of
experiments and selection of the next run.

## 7. Net result

The replicator does not accumulate errors, because:

1. **geometric errors are optically compressed** at every generation — the child's
   critical dimensions are set by the imaging system, not by the parent's absolute
   accuracy;
2. **equipment faults are mapped and routed around** — bad mirrors, drift and backlash are
   compensated in software, not accepted in hardware;
3. **defects are binned, not inherited** — flat probe and bad-block tables convert them
   into yield, and service replaces what fails in the field;
4. **the process floor is physical and known** — 2–5 µm at the starting equipment, and
   anything below 1–2 µm is explicitly a new branch of hardware rather than a promise.

Within that envelope each generation can be *better* than the one before it, because
every generation's errors get compressed by the copy step itself — and only the walls in
§6 are permanent.

## See also

- the copy step itself: folding a flat net into a cube → [02-cube-from-wafer.md](02-cube-from-wafer.md)
- the tool that does the demagnification → [04-diy-lithography-path.md](04-diy-lithography-path.md)
- gates for replacing macro hardware with micro hardware → [01-concept.md](01-concept.md) §7
