# RUNS Standard Library

🏠 **[DGS Overview](https://github.com/decentralized-game-standard)**  
· 🏃 **[RUNS](https://github.com/decentralized-game-standard/runs-standard)**  
· 📦 **[AEMS](https://github.com/decentralized-game-standard/aems-standard)**  
· ⚡ **[WOCS](https://github.com/decentralized-game-standard/wocs-standard)**  
· 🔤 **[Ludic](https://github.com/decentralized-game-standard/ludic-notation-standard)**

> **Status**: Community-Curated Examples / RFC  
> **Version**: 0.1.0

## The Shared Palette for RUNS

The **RUNS Standard Library** is a collection of **recommended, optional primitives**—data shapes (Fields) and granular Processor examples—that accelerate interoperability across games, mods, and runtimes.

It is **not** part of the mandatory RUNS Protocol. You can ignore it entirely and still build fully compliant RUNS games with custom schemas. But targeting these primitives unlocks instant composability: a movement Processor from one game drives transforms in another, mods drop in seamlessly, and ecosystems share "pigments" without negotiation.

Curated openly by the community, this library evolves organically. Widely adopted suggestions become the de facto starting point for new projects—lowering barriers while preserving full freedom.

For permissionless distribution (e.g., via Nostr relays), all examples here are plain-text: serializable schemas, declarative Processor definitions, and bundle graphs.

## Why Use the Standard Library?

- **Instant Interoperability** — Shared Fields let unrelated packages compose without custom bridges.
- **Multi-Scale Mixing** — Start with syscall-granular ops, wire into bundles, bundle those into systems—all uniform.
- **Lower Onboarding** — Beginners prototype quickly; experts extend or replace.
- **Ecosystem Momentum** — Popular primitives attract more tools, runtimes, and mods.
- **Honest Longevity** — Conventions separate enduring gameplay rules from transient performance tricks.

Using the library is a convention, not a requirement. It’s the "shared palette" that makes decentralized composition practical.

## Core Philosophy

- **Minimal and Neutral** — No genre assumptions, no performance tricks baked in.
- **Plain-Text First** — Examples in human-readable, distributable formats.
- **Granular to Hierarchical** — Primitives are tiny atoms; bundling builds complexity.
- **Community-Driven** — Anyone can propose additions/changes via PR or WOCS bounty.

## Recommended Fields (The Vocabulary)

These suggested Field schemas provide a common tongue. Define them on Records as needed.

| Prefix | Field Name          | Type                                      | Description                          |
|--------|---------------------|-------------------------------------------|--------------------------------------|
| `runs` | transform           | struct { position: vec3, rotation: quat } | Spatial placement                    |
| `runs` | velocity            | vec3                                      | Linear velocity                      |
| `runs` | angular_velocity    | vec3                                      | Rotational velocity                  |
| `runs` | delta_time          | float                                     | Frame timestep (provided by runtime) |
| `runs` | input_intent        | struct { move: vec2, look: vec2, jump: bool } | Player/controller intent          |
| `runs` | health              | float                                     | Generic damageable value             |
| `runs` | team_id             | u32                                       | Affiliation for rules                |

Schemas are versioned JSON-like for serialization:

```json
{
  "runs:transform": {
    "position": { "type": "vec3", "default": [0,0,0] },
    "rotation": { "type": "quat", "default": [0,0,0,1] }
  }
}
```

Custom Fields remain fully supported—mix freely.

## Primitive Processors (The Pigments)

Granular, pure operations—suggested in a simple declarative plain-text format for readability and Nostr distribution.

Format (`.runs-prim`):

```
processor add_vec3
inputs:
  a: vec3
  b: vec3
outputs:
  result: vec3

result.x = a.x + b.x
result.y = a.y + b.y
result.z = a.z + b.z
```

Examples:

- `mul_vec3_scalar` — Scale vectors.
- `integrate_velocity` — Basic Euler: `transform.position += velocity * delta_time`.
- `apply_input_intent` — Map intent to velocity.
- `select_entities` — Basic query primitives (has_field, etc.).

These are starting atoms—wire them freely in Networks.

## Bundling Examples (Mixing Paints)

Bundles are sub-Networks acting as higher-scale Processors.

Example: Simple movement bundle (`.runs-bundle` graph):

```yaml
bundle basic_movement
inputs:
  transform: runs:transform
  velocity: runs:velocity
  delta_time: runs:delta_time
outputs:
  transform: runs:transform

wires:
  - mul: mul_vec3_scalar(velocity, delta_time)
  - add: add_vec3(transform.position, mul.result)
output:
  transform.position = add.result
```

Higher levels:
- Character controller bundles movement + grounding + jump.
- Physics island bundles multiple controllers + resolution.

Every bundle remains a first-class Processor—recursively composable.

## Processor Authoring Styles (Semantic vs. Realization)

To support extreme longevity while enabling performance, the library distinguishes two **conventions** for Processor definitions—all plain-text, all composable:

**Gameplay Logic Processors (Recommended for Shared Primitives)**  
- Style: Constrained, SSA-like declarative syntax.  
- Focus: Pure semantic intent (rules, state transitions)—close to math, no hardware assumptions.  
- Horizon: Millennia—readable/reimplementable by hand centuries later.  
- Use for: Core rules (integration, collision resolution, input→intent).  

Example (`integrate_velocity.runs-prim`):
```
processor integrate_velocity
inputs:
  position: runs:vec3
  velocity: runs:vec3
  delta_time: float
outputs:
  position: runs:vec3

position += velocity * delta_time
```

**Execution Realization Processors (Optional for Fidelity)**  
- Style: Extended declarative with hint sections (or more expressive graphs).  
- Focus: Platform guidance (SIMD, approximations, GPU offload)—ignored by minimal runtimes.  
- Horizon: Decades—evolves with hardware; cleverness applied by runtimes.  
- Use for: Optimized variants of semantic logic.  

Example (`integrate_velocity_realized.runs-prim`):
```
processor integrate_velocity_realized
inputs:
  position: runs:vec3
  velocity: runs:vec3
  delta_time: float
outputs:
  position: runs:vec3

core:
  position += velocity * delta_time

hints:
  vectorize: simd
  approximate: fast_mul optional
  target: gpu_compute if_available
```

Both styles target the same Fields. Runtimes interpret core semantics universally; advanced ones apply hints for perf. Prefer pure declarative for library contributions—realizations compete in Ecosystem packages.

## Authoring Conventions

- Declarative core for all shared examples.
- Hints optional and runtime-specific.
- Plain-text distribution always.

## Contributing

This library thrives on community input:
- Propose new Fields/Primitives via issues/PRs.
- Use WOCS for bountied additions.
- Fork and specialize—adoption decides what sticks.

Target these for sharing; innovate beyond for uniqueness.

## Summary

The RUNS Standard Library is your optional starter kit: shared Fields and granular primitives that make decentralized, enduring games easier to build and compose.

Mix the pigments. Build something timeless.

**MIT License** — Fork, extend, share freely.