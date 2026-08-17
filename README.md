# My Projects - Modular Robot Prototype

**Addison B. — embedded software engineer**

A prototype modular robot built on the utility fog paradigm, and the engineering program behind
roughly 50,000 lines of simulation, analysis and tooling code written to design it. On the recent
work, **every line of that code was written by AI**, to my specification and under my review.

## What I'm building

A **utility foglet**: one module of a programmable-matter system. The utility fog paradigm calls for
very large numbers of small identical units that bond to their neighbours and push and pull on one
another, so that the aggregate behaves as material which can change its own shape and do work.

My unit is a rhombic dodecahedron sitting in an FCC lattice, so it meets twelve neighbours
face-to-face. Its frame is not machined — it is **beads and tubes threaded on a tensioned string**.
Pulling the string tight squeezes each column together until it behaves as a rod, which makes every
strut a pair of unilateral members sharing one line: a tension-only string and a compression-only
column. Neither is a bar alone; superposed, they are.

The architecture has to survive an end state of billions of units, so every part must be
manufacturable at scale and nothing may be hand-tuned. The current phase is **simulate → verify →
build**, with scope deliberately narrowed to a single unit and a printable 100 mm test cube as the
next physical milestone.

---

## The engineering program

The design's parameters were not known at the outset and could not be guessed. That single fact
shaped everything: the software had to **search for a design** rather than implement one. Three
codebases were built to do that, each superseding the last as the problem clarified.

### ProGUI & Chainmaker — C++, MuJoCo

An interactive GUI for assembling and simulating chained block structures, built as a separate
target inside a MuJoCo fork. Proximity-based collision detection, joint-limit handling, directional
lighting and a checker floor, camera focus, spawn-height and root-fixed controls, text-based
save/load, simulation speed presets, and automated GUI testing.

| Period | Commits | Module | Docs |
|---|---|---|---|
| Jul 2025 – Jul 2026 | 218 | ~2,600 lines | PRD + architecture |

### StringBot — Python, MuJoCo bindings

Software to plan, simulate and prepare the manufacture of robots made of compression-only beads on
tension-only strings. Graph schema and derivation, compilation to MuJoCo, rigidity analysis
(rigidity matrix, pebble game, self-stress), a capstan friction budget, a planner that decomposes
and splits string circuits, settling simulation, bill-of-materials sizing, and an interactive
structure builder with an FCC and right-angle snap grid.

| Period | Commits | Code | Tests |
|---|---|---|---|
| Jul 2026 | 362 | 29,727 lines | 574 in 82 files |

**Headline result.** The 24-cell's single-string circuit needs Θ≈108.6 rad of turning against a
friction budget of Θ_max≈30.2 rad — proving it genuinely un-tightenable as one string. The planner
then split it into **K=4** friction-feasible strings, the theoretical minimum, with rigidity
cross-checked as unchanged.

### FogBot — Python, zero dependencies

A design-space explorer, not an implementation of a design. Every unmeasurable input is declared as
an interval and swept as a band; any check whose verdict flips inside its band returns `AMBIGUOUS`,
which is not a pass. Rigidity is decided by the rank of the bar-joint rigidity matrix at the true
coordinates in exact rational arithmetic — Maxwell counting is only a lower bound, and pebble games
decide the generic case while these frames are symmetric. The method and its geometry-free kernel
are published separately as `fogbot-method`.

| Period | Code | Tests | Decisions |
|---|---|---|---|
| Aug 2026 | 17,427 lines | 376 | 15 ADRs |

**Headline result.** Six strategies for bracing the frame against buckling — guy nets, strut
bundles, battens, tensegrity retrofits, added prestress, subdivision — were analysed and eliminated,
each with a number. The pattern only appeared with all six on the table: every one was bracing an
error that a longer part does not accumulate. Replacing bead stacks with single extruded tubes
**removed the failure mode outright, by roughly 450×**, and buckling stopped governing the design.

---

## How the work was done

The model writes the code — all of it. The engineering sits in the process built around it:
specification, gating, verification, and the discipline to catch what it got wrong. None of the
practices below is held informally; each is written into a process document that is loaded at the
start of every session and enforced before anything is committed.

- **Plan before execute.** The implementation plan for a feature is written to a task-list document
  and committed *on its own*, containing no code, before execution begins. Plan changes get their
  own commits too.

- **Verification is a test, not a look.** Every finding is captured as a deterministic assertion
  before it is confirmed by hand — so the finding survives the session that produced it.

- **Pre-commit gating.** Review the full staged diff rather than the stat, enumerate and run every
  test directory rather than the ones that seem relevant, lint, and route changes in load-bearing
  modules through a domain-specific review checklist.

- **Decisions are recorded.** Fifteen architecture decision records, each carrying its reasoning and
  its consequences — including the ones that reversed earlier decisions.

- **Corrections are logged, with supersession chains resolved.** Seventy-eight numbered corrections,
  and a best-practices document written *from* them rather than from general advice. Every rule in
  it has a worked example attached.

- **Negative results are kept.** Eliminated approaches stay in the repository with their numbers
  intact. The 450× result above was only visible because six failures were recorded side by side.

### Where human oversight was the control

- **Radians against degrees.** The model computed joint limits in radians; MuJoCo defaults to
  degrees. Caught by reading the simulator's own defaults rather than trusting the derivation.

- **A units error inside a study.** A section was modelled as a 5 mm cylinder when it is a 10 mm
  square, overstating a structural gain as 16× when it is 6×. It survived several sessions, and
  every number downstream of it was computed correctly.

- **A mesher wrong by 8×.** A third of its faces were wound backwards. Nothing in the output looked
  wrong — 280,000 triangles, plausible file size, opened fine. It was caught only by meshing a
  sphere of known radius and comparing against 4πr³/3.

- **Two limits that were one.** Two failure modes were carried separately, with separate levers, for
  four sessions, and an entire design paradigm was built on the split. A single question dissolved
  it — they are two intercepts of one line.


| Codebases | Lines of code | Automated tests | Commits |
|:---:|:---:|:---:|:---:|
| **3** | **~50,000** | **950** | **613** |

---

> ### I no longer hand-write production code. I specify it, gate it, and verify it.
>
> On StringBot and FogBot — the two most recent codebases, **47,000 lines and 950 tests between
> them** — every line was written by Claude. My work sits upstream and downstream of the model:
> defining the problem, choosing the architecture, deciding what is allowed to be committed, and
> proving that what came back is correct. The process documents described below exist because that
> division of labour only works when it is enforced rather than intended.

---

---

<sub>Figures verified against the repositories on 17 Aug 2026; line counts cover package and test
sources. StringBot and FogBot are written entirely by Claude under direction — 228 of StringBot's
362 commits and nearly all of FogBot's carry Claude co-author trailers, across Opus, Sonnet and
Haiku. The earlier MuJoCo GUI work predates this workflow and used mixed assistants; it is included
here for the engineering, not as an example of the method.</sub>
