---
title: "Narrow Phase: GJK and EPA"
description: Implementation of GJK and EPA algorithm for collision detection in the engine.
date: 2026-05-02T12:18:31+08:00
image: IMG_20260213_154637.jpg
math: true
license: CC BY-NC-SA 4.0
comments: true
categories:
    - AtlasPhys
tags:
    - GJK
    - EPA
    - Math
    - Vectors
    - Collisison Detection
build:
    list: always    # Change to "never" to hide the page from the list
---

# Narrow Phase Collision Detection: GJK and EPA:

The narrow phase of the engine consists of two parts: Gilbert-Johnson-Keerthi algorithm (GJK) and Expanding Polytope algorithm (EPA). GJK tells you about whether the two object is colliding and the terminal simplex of the two objects' Minkowski difference. EPA tells you about the normal of the contact point and the depth of the collision.

## GJK:
The GJK does not operate directly on the polygons themselves. Instead, it operates on the mathematical abstraction know as the Minkowski Difference.
### Minkowski Sums / Differences

#### Sum:

$${\rm{A}} \oplus B = \left\{ {a + b|a \in A,b \in B} \right\}$$
#### Difference:
$${\rm{A}} \ominus B = \left\{ {a + \left( { - {\rm{b}}} \right)|a \in A,b \in B} \right\}$$
#### Properties:
1. **Preservation of Convexity:** If objects A and B are convex, their Minkowski difference $A \ominus B$ is strictly convex.
2. **Intersection Equivalence:** Objects A and B intersect if and only if the origin vector is contained within their Minkowski difference, i.e., $(0,0) \in A \ominus B$.

### Support function(2 dimension):

Instead of explicitly calculating the entire, complex Minkowski difference which is theoretically infinite, GJK relies on a *Support Function* to sample it iteratively. 

C(Minkowski Sum):
$$C = A \oplus B $$
The furthest point on A in the direction of ${\vec d}$:
$$ {s_A}\left( {\vec d} \right) \to \left( {{x_A},{y_A}} \right) $$
The furthest point on B in the direction of ${-\vec d}$:
$${s_B}\left( {-\vec d} \right) \to \left( {{x_B},{y_B}} \right) $$
Therefore, a point $S_{A \ominus B} (d)$, we compute:
$$ {s_C}\left( {\vec d} \right) = {s_A}\left( {\vec d} \right) - {s_B}\left( {- \vec d} \right) \to \left( {{x_C},{y_C}} \right) $$
![[IMG_20260213_154637.jpg]]
To judge whether the simplex contains the origin, there is a classification for the linear, triangle, tetrahedral cases.

The logic of the implementation of the support function in my prototype of the engine follows the following steps:
1. Get the object's model matrix. The matrix contains the object's current # ! Position, Rotation and Scale
    
2. Convert the direction from the World Space -> Local Space. Create a 4D vector for the direction (w = 0 means that it is a direction instead of a point). Multiply the direction by the tranpose of the matrix. Local direction = Direction * Transpose of matrix

3. Find the furthest point in local direction

4. Convert the point from Local Space -> World Space. Create a 4D vector for the point(w = 1 means the it is a point instead of a direction). World Point = Matrix * Point

5. Repeat the above operation on two object. The result point will be Point A - Point B.

### Simplex Evolution and the Origin Check

GJK iteratively builds a simplex (a point, line segment, triangle, or tetrahedron) inside the Minkowski Difference to enclose the origin. To judge whether the simplex contains the origin, there is a distinct classification for the linear, triangle, and tetrahedral cases.

#### Linear case:
After the initialization, the simplex should contains two points. In 3D space the probability of the origin in a line segment is 0, so the method takes the role of updating the direction variable and returns *False*.
Simplex = [B, A] a line segment where A is the latest point added in.
*   If the dot product $\vec{AB} \cdot \vec{AO} > 0$, the origin lies in the region perpendicular to the segment. The search direction is updated as $\vec{AB} \times (\vec{AO} \times \vec{AB})$, which yields a vector perpendicular to $\vec{AB}$ pointing towards the origin.
*   Else, delete point A and update the direction as $\vec{AO}$. In this case, point B is not helpful to find a closer point to the origin than A. This makes the geometric result discrete. Finally, the iteration returns *False*, and a new point is searched. In 3D space, the probability of the origin lying exactly on a line segment is near 0.

#### Triangle case:
Simplex = $[C, B, A]$, forming a triangle. The goals at this stage are:
1.  Judge whether the triangle contains the origin.
2.  If not, trim the simplex into a line segment and update the direction.

The core logic requires determining the region the origin resides in:
1.  Determine which side of the plane the triangle is located relative to the origin. 
2.  Determine whether the origin is outside which specific edge of the triangle. This is achieved using edge normals (e.g., $\vec{AB}^\perp = \vec{AB} \times \vec{ABC}$ where $\vec{ABC}$ is the triangle's surface normal).
3.  If the origin is outside an edge (e.g., $\vec{AC}$), reduce the triangle to a line segment (keep A and C), update the search direction orthogonal to that edge, and continue the iteration. If it is contained within all edge boundaries, we proceed to 3D analysis or return *True* (for 2D).

#### Tetrahedral case:
Simplex = $[D, C, B, A]$, forming a tetrahedral structure.
1.  Calculate the normal vector of each of the four faces. (Ensure normals point outward from the tetrahedron's center).
2.  Determine whether the origin is on the outside of each face. (Evaluated via dot product: $\vec{Normal} \cdot \vec{AO} > 0$).
3.  If the origin is on the outside of any face, trim the point which is not on that face. Pass the newly reduced simplex (a triangle) back to the triangle case logic, and return *False*. 
4.  If the origin is on the inside of all the faces, the tetrahedral definitively contains the origin. The function returns *True*, signaling an intersection.

---

## EPA:
Once GJK returns *True*, we have an intersecting simplex. However, GJK cannot calculate penetration depth. The core logic of EPA here is to geometrically expand the simplex that GJK returns to approach the actual boundary of the Minkowski difference of the two objects. The minimum distance from the origin to this boundary represents the penetration depth and collision normal. 

### Iterative Expansion Logic:
The standard EPA pipeline proceeds as follows:

1.  **Find the Closest Feature:** Obtain the edge (in 2D) or face (in 3D) of the simplex with the shortest perpendicular distance to the origin. Denote the corresponding Minkowski difference vertices of this edge as $M_1, M_2$. Find the normal vector perpendicular to this edge and directed away from the origin.
2.  **Support Point Sampling:** Calculate a new Minkowski difference support point, $M_3$, by evaluating the support function along this normal direction. 
3.  **Collinearity/Convergence Check:** If $\left| M_1 - M_3 \right| + \left| M_2 - M_3 \right| < \epsilon$, exit the iteration. *(Academic Note: This condition checks if $M_3$ lies exactly on the segment $M_1 M_2$. In practice, standard 3D EPA checks if the dot product of $M_3$ along the normal is minimally larger than the distance to the face).*
4.  **Directional Progress Check:** If $M_3$ does not belong to the furthest point found in the direction (i.e., no significant progress is made outward), exit the iteration. The current distance to the feature is the penetration depth.
5.  **Duplication Check:** If $M_1 = M_3$ or $M_2 = M_3$, indicating a repetition in the existing simplex, exit the iteration. The algorithm has found the true boundary.
6.  **Expansion:** If the checks pass, split the closest edge/face by inserting $M_3$, creating new edges/faces, and repeat the process from Step 1 until the exact boundary is met.


## Implementation

The full implementation of GJK and EPA can be find at <https://github.com/EMEEEEMMMM/AtlasPhys/blob/main/prototype_python/utils/GJK.py> and <https://github.com/EMEEEEMMMM/AtlasPhys/blob/main/prototype_python/utils/EPA.py>.

{{< interactive-canvas id="gjk3dCanvas" js="gjk-demo.js" >}}
<div style="text-align: center; margin-top: -1rem; margin-bottom: 2rem;">
    <button id="nextStepBtn" style="padding: 0.6rem 1.5rem; background: #2563eb; color #fff; border: none; border-radius: 6px; cursor: pointer; font-weight: bold;">Next Step </button>
</div>