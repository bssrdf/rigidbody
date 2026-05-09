#
write javascript code for box box contact manifold generation. You can only  javascript and Three.js


# Box–Box Contact Manifold Generation (Three.js)

Here's a complete, self-contained implementation using **SAT (Separating Axis Theorem)** to find the separating axis, followed by **Sutherland–Hodgman polygon clipping** to generate the contact manifold for face contacts, plus **closest-points-on-segments** for edge–edge contacts.

---

## How it works — summary of the pipeline

### 1. **Separating Axis Theorem (SAT)** — `boxBoxManifold()`
Tests all **15 candidate axes**:
- 3 face normals of box A
- 3 face normals of box B
- 9 pairwise cross-products of edges (A.axis[i] × B.axis[j])

For each axis, compute the **projection radii** `rA`, `rB` and the **center distance** `d` along that axis. Penetration depth = `rA + rB - |d|`. If any axis yields a negative penetration → boxes are separated (early out).

Otherwise we keep the axis with the **smallest penetration** (the "least overlap" axis). Face axes get a small **tie-breaking bias** so that near-coplanar cases snap to face contact instead of edge-edge (reduces jitter).

### 2. **Contact point generation** — depends on which axis won

**Face contact** (`faceContact`):
- Identify **reference face** (on the box owning the winning axis) and **incident face** (the face of the other box most anti-parallel to the normal).
- **Sutherland–Hodgman clip** the incident face polygon against the 4 side planes of the reference face.
- Keep only clipped vertices that lie **below** the reference face plane (the penetrating ones), projecting them onto the reference plane for stability.
- Each surviving point carries its own **penetration depth**.

**Edge–edge contact** (`edgeEdgeContact`):
- Pick the supporting edges of A and B along the SAT axis (`supportEdge`).
- Compute the **closest points between the two line segments** — midpoint is the single contact.

### 3. **Manifold reduction** — `reduceManifold()`
Physics engines typically cap manifolds at **4 points**. The classic algorithm used here:
1. Keep the deepest point.
2. Keep the point farthest from (1).
3. Keep the point maximizing signed triangle area with (1)(2).
4. Keep the point forming the largest *opposite-signed* triangle (i.e., completes the best quad).

### 4. **Output**
```js
{
    normal:      Vector3,    // unit, points from A toward B
    points: [
        { position: Vector3, depth: number },
        ...  // 1..4 points
    ],
    featureType: 'face-A' | 'face-B' | 'edge-edge'
}
```

This is exactly the format a constraint solver (sequential-impulse, PGS, etc.) needs: a shared normal + multiple contact points, each with its own position and penetration depth.

### Controls in the demo
- **WASD / QE**: translate box B
- **IJKL / UO**: rotate box B
- **Mouse**: orbit camera
- Red spheres = contact points, green lines = depth, magenta = manifold polygon, yellow arrow = normal.


