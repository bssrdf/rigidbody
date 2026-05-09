# Rigid Body Dynamics

Collection of self-contained HTML demos exploring 3D rigid-body physics (collision detection + impulse-based solving) in JavaScript + Three.js.

## Files

| File | Lines | Title | Summary |
|---|---|---|---|
| [`boxbox.html`](boxbox.html) | 563 | — | **Box-box contact manifold** visualisation. SAT narrow-phase with 15 candidate axes (3 face-A, 3 face-B, 9 edge-edge). Generates contact points via Sutherland-Hodgman clipping (face-face, up to 4 points) or closest-segment midpoint (edge-edge, 1 point). Interactive demo: move/rotate one box with keyboard to inspect the manifold. |
| [`deepseek_pbd.html`](deepseek_pbd.html) | 989 | Rigid Body Simulator — Sequential Impulse + Warm Start | Full rigid-body simulator. Narrow-phase: sphere-sphere, sphere-box (clamped local centre), box-box (SAT + face clipping). Solver: sequential impulse with warm-started normal + two-orthant friction. Semi-implicit Euler integration with quaternion update. |
| [`fixed.html`](fixed.html) | 833 | Rigid Body Dynamics Simulator | Minimal implementation using a hand-rolled `V3` vector class. Narrow-phase: sphere-sphere, sphere-box (clamped to AABB in local space), box-box (SAT, single contact at midpoint). Solver: warm-started normal impulse + two tangent impulses (Coulomb cone). Click or space to shoot spheres at boxes. |
| [`gpt_pbd.html`](gpt_pbd.html) | 1567 | Stable Sequential Impulse Physics | Advanced solver with GJK/EPA-based closest-point detection. Edge-edge contacts via closest points on segments, contact clustering to deduplicate nearby points. Cached impulses per contact for warm-starting across frames. Sphere-sphere, sphere-box, box-box support. |
| [`gpt_pbd_1tangent.html`](gpt_pbd_1tangent.html) | 1552 | Stable Rigid Body Physics | Variant of `gpt_pbd.html` that uses a **single tangent impulse** instead of two, simplifying the friction model at the cost of anisotropic friction response. |
| [`index.html`](index.html) | 1378 | Rigid Body Dynamics Simulator | Full-featured simulator with sphere-sphere, sphere-box, and box-box (SAT + face/edge clipping for manifold generation). Sequential impulse solver with warm-starting, friction cone (two orthogonal tangents), and `flipImpulse` flag for contact direction handling. Click to shoot balls, R to reset. |
| [`nudge.html`](nudge.html) | 1756 | Nudge Rigid Body Dynamics | Port of the [Nudge](https://github.com/rasmusgo/nudge) engine (MIT). Features: AABB broad-phase, sphere-sphere / sphere-box / box-box (SAT) narrow-phase, sequential impulse solver with warm-starting, friction cone, position correction bias, body sleeping via idle counters, and dynamic scene controls. |
| [`pbd.html`](pbd.html) | 745 | Box2D-Style Rigid Body Dynamics | Box2D-inspired solver: SAT for OBB-OBB, manifold generation via reference/incident face clipping, multi-contact resolution with positional correction. First contact gets restitution, secondary contacts use e=0 for rotational stability. Impulse divided by contact count to avoid over-resolving. |
| [`pbd_opus.html`](pbd_opus.html) | 468 | Box2D-Style Rigid Body Dynamics | Compact variant of `pbd.html`. SAT for OBB-OBB with single contact point (closest point on A shifted half-penetration). Angular impulse applied via `applyAngularImpulse` helper. Minimal but complete impulse-based solver. |

## Common algorithms

- **SAT (Separating Axis Theorem)** — used for box-box narrow-phase in most demos
- **Sequential impulse** — iterative constraint solver applied over multiple substeps per frame
- **Warm-starting** — carry normal/tangent impulses from the previous frame to speed convergence
- **Sutherland-Hodgman clipping** — generate multi-point contact manifolds from face-face collisions
- **Semi-implicit Euler** — integration with quaternion-based rotation update

## Running

Open any `.html` file in a modern browser (no build step needed; all dependencies loaded from CDNs).
