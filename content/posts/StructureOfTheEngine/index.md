---
title: "Structure Of The Engine"
description: 
date: 2026-06-18T14:05:02+08:00
image: cover.png
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

This article i want to share about the two version in my [repository](https://github.com/EMEEEEMMMM/AtlasPhys), the first one is prototype written in python. 
## 1. Overview
1. **Integration**
2. **Broad Phase**
3. **Narrow Phase**
4. **Contact Generation**
5. **Constraint Solving**
6. **Positional Correction**
7. **Others**

## 2. Integration:
A typical semi-implicit Euler method to update the objects' velocity and position. The position is updated right after the velocity. I manually set the $\tau$ to zero so that the $\omega$ won't change by itself.
$$
\begin{align}
&a = g \\
&{v_{t + \Delta t}} = {v_t} + a\Delta t \\
&{x_{t + \Delta t}} = {x_t} + {v_{t + \Delta t}}\Delta t \\
&\tau = I\alpha \\
&{\omega _{t + \Delta t}} = {\omega _t} + I_{world}^{ - 1}\tau \Delta t \\
&{\theta _{t + \Delta t}} = {\theta _t} + {\omega _{t + \Delta t}}\Delta t \\
&I_{world}^{ - 1} = RI_{body}^{ - 1}{R^T} 
\end{align}$$
## 3. Broad Phase:
My implementation of AABB is different from the standards, the time complexity is stil $O(n^2)$ technically, its role is to filter the objects are probably not going to collide. And since there are only two shapes allowed in the scene (Cubes and Spheres), AABB will work perfectly as I expected. I generate the object's X/Y/Z_MAX/MIN when the object was created (G_Object.py line 128&133).
```python
MaxXYZ: NDArray[np.uint8] = np.argmax(self.XYZVertices, axis=0)
MinXYZ: NDArray[np.uint8] = np.argmin(self.XYZVertices, axis=0)
```

Since the XYZ values here are all in the local space, so when detection i added the object's position.
```python
if not check_aabb(
	A.X_MAX + A.Position[0],
	A.X_MIN + A.Position[0],
	A.Y_MAX + A.Position[1],
	A.Y_MIN + A.Position[1],
	A.Z_MAX + A.Position[2],
	A.Z_MIN + A.Position[2],
	B.X_MAX + B.Position[0],
	B.X_MIN + B.Position[0],
	B.Y_MAX + B.Position[1],
	B.Y_MIN + B.Position[1],
	B.Z_MAX + B.Position[2],
	B.Z_MIN + B.Position[2],
):

continue
```
## 4. Narrow Phase:
Consists of two parts: GJK and EPA. All of the code about these two can be found in GJK.py. GJK and EPA happens only when Cube and Cube are going to collide reported by the AABB method to avoid performance issues of running GJK and EPA on spheres.
### 4.1 GJK:
#### Minkowski Sums / Differences

##### Sum:

$${\rm{A}} \oplus B = \left\{ {a + b|a \in A,b \in B} \right\}$$
##### Difference:
$${\rm{A}} \ominus B = \left\{ {a + \left( { - {\rm{b}}} \right)|a \in A,b \in B} \right\}$$
##### Properties:
1. A and B convex -> ${\rm{A}} \ominus B$ convex
2. A and B intersect -> $\left( {0,0} \right) \in A \ominus
#### Support function(2 dimension):

C(Minkowski Sum):
$$C = A \oplus B $$
The furthest point on A in the direction of ${\vec d}$:
$$ {s_A}\left( {\vec d} \right) \to \left( {{x_A},{y_A}} \right) $$
The furthest point on B in the direction of ${\vec d}$:
$${s_B}\left( {\vec d} \right) \to \left( {{x_B},{y_B}} \right) $$
Therefore, the furthest point on C in the direction of ${\vec d}$:
$$ {s_C}\left( {\vec d} \right) = {s_A}\left( {\vec d} \right) + {s_B}\left( {\vec d} \right) \to \left( {{x_C},{y_C}} \right) $$
![Notes taken](IMG_20260213_154637.jpg)
To judge whether the simplex contains the origin, there is a classification for the linear, triangle, tetrahedral cases.
#### Linear case:
After the initialization, the simplex should contains two points. In 3D space the probability of the origin in a line segment is 0, so the method takes the role of updating the direction variable and returns *False*.
Simplex = [B, A] a line segment where A is the latest point added in.
If the dot product $\vec{AB} \cdot \vec{AO} > 0$, the direction is updated as the triple cross $(\vec{AB} \cdot \vec{AO}) \cdot \vec{AB}$. Else, delete point A and update the direction as $\vec{AO}$ since in this case point B is not helpful to find a closer point to origin than A and that makes the result discrete. Finally, returns *False*.
#### Triangle case:
Simplex = [C, B, A] a triangle. The goal in at this stage is 1. Judge whether the triangle contains the origin. 2. If not, trim the simplex into a line segment and update the direction. Core logic here is 1. Determine whether the origin is on which side of the plane where the triangle is located. 2. Determine whether the origin is outside which side of the plane of the triangle. 3. Reduce the triangle to a line segment and continue the iteration.
#### Tetrahedral case:
Simplex = [D, C, B, A] a tetrahedral.
1. Calculate the normal vector of each face.
2. Determine whether the origin is on the outside of each face.
3. If the origin is on the outside of any face, trim the point which is not on that face and pass the simplex to the triangle case, returns *False*. If the origin is on the inside of all the faces, the tetrahedral contains the origin, the function returns *True*.
### 4.2 EPA:
Core logic here is to expand the simplex the GJK returns to approach to the actual Minkowski difference of the two objects.
1. Obtain the edge with the shortest distance to the origin, and denote the corresponding Minkowski difference as $M_{1},M_{2}$. Find the vector perpendicular to this edge and directed away from the origin.
2. Then calculated Minkowski difference $M_{3}$.
3. If $\left\vert M_{1}-M_{3} \right\vert+\left\vert M_{2}-M_{3} \right\vert < \epsilon$, exit the iteration.
4. If $M_{3}$ not belonging to the point found in the direction, exit iteration.
5. If $M_{1}=M_{3}$ or $M_{2}=M_{3}$ if there is a repetition in the existing simplex, exit the iteration.
# 5. Contact Generation:
All manifolds of  contacts are managed by the class *ManifoldManager*, its *update()* method returns a list contains the instance of another class *PersistentContactManifold*.
## 5.1 ManifoldManager:
It has only one method *update()*, this method will be executed every physic step to update the contacts between objects:
1. It walks through the list of objects ($O(n^2)$).
2. AABB detects whether the objects are going to collide, if not continue to the next object.
3. Generate *Keys* to avoid having two identical contact manifold.
4. *generate_contacts()*
5. *update_from_collision()* method from *PersistentContactManifold*
6. Clean up the dead keys. (It seems that i had found a bug in the program which there is no method to remove keys from the *ActiveKeys*.)
7. Returns the Result list.
## 5.2 *generate_contacts()*:
### 5.2.1 generate_pc_contacts():
1. Walk through the list of vertices of the cube.
2. If $\begin{bmatrix}0.0 \\ 1.0 \\ 0.0 \end{bmatrix} \cdot \begin{bmatrix}x \\ y \\ z \end{bmatrix} - PlaneHeight < 0$, append a contact into the contacts list.
3. Returns contacts list and the normal.
### 5.2.2 generate_ps_contacts():
1. If $\begin{bmatrix} 0.0 \\ 1.0 \\ 0.0 \end{bmatrix} \cdot \begin{bmatrix} x \\ y \\ z \end{bmatrix}-PlaneHeight \geq R$, returns an empty list and the normal since didn't collide.
2. Returns $\begin{bmatrix}x \\ y-R \\ z\end{bmatrix}$,$R-(\begin{bmatrix}0.0 \\ 1.0 \\ 0.0 \end{bmatrix} \cdot \begin{bmatrix}x \\ y \\ z \end{bmatrix} - PlaneHeight)$,normal.
### 5.2.3 generate_ss_contacts():
1. If $\sqrt{(x_{B}-x_{A})^2+(y_{B}-y_{A})^2+(z_{B}-z_{A})^2}\geq R_{A}+R_{B}$, returns two empty lists since didn't collide.
2. Returns $\begin{bmatrix}x_{A} \\ y_{A} \\ z_{A}\end{bmatrix}+\begin{bmatrix}x_{B}-x_{A} \\ y_{B}-y_{A} \\ z_{B}-z_{A}\end{bmatrix}*R_{A}$,$R_{A}+R_{B} - \sqrt{(x_{B}-x_{A})^2+(y_{B}-y_{A})^2+(z_{B}-z_{A})^2}$, normal.
### 5.2.4 generate_cc_contacts():
1. GJK+EPA returns whether collide, the normal, the Depth.
2. Face clipping using Sutherland Hodgman algorithm.
### 5.2.5 generate_cs_contacts():
1. Turn the center of the sphere into the local space of the cube.
2. Determine which plane the center of the ball is closest to.
3. Choose the normal of that surface.
4. Calculate penetration.
5. Generate a contact point.
## 5.3 PersistentContactManifold:
### Warm start:
After the *update()* method in ManifoldManager, *warm_start()* method will be executed. It uses the impulse from last physic step and applies that to the object by using the method *_apply_impulse()* since the contact of objects are not instantaneous but continuous. It makes the system approach the solution more quickly and helps improve the stabilization.
### Apply Impulse:
All the impulse calculated will be applied by this function.
```python
A.Velocity -= J * A.ReciprocalMass
B.Velocity += J * B.ReciprocalMass
```
$$
\begin{align}
F&=m\frac{dv}{dt} \\
\int F dt&=m \int \frac{dv}{dt} dt \\
J &= m(v'-v) \\
\Delta v &= \frac{J}{m}
\end{align}
$$
```python
A.AngularVelocity -= A.InvInertiaWorld @ np.cross(Ra, J)
B.AngularVelocity += B.InvInertiaWorld @ np.cross(Rb, J)
```
$$
\begin{align}
F &= \frac{dp}{dt} \\
\int F dt &= \int \frac{dp}{dt}dt \\
J &= \Delta p \\ \\
 \\
\Delta L &= r \times \Delta p = r \times J \\
I \Delta \omega &= r \times J \\
\Delta \omega &= I^{-1}(r \times p)
\end{align}
$$
# 6. Constraint Solving:
## 6.1 Normal Impulse:
$$
\begin{align}
&{r_A} = P - {x_A},{r_B} = P - {x_B}\\
&{v_{P,A}} = {v_A} + {\omega _A} \times {r_A},{v_{P,B}} = {v_B} + {\omega _B} \times {r_B}\\
&{v_{rel}} = {v_{P,B}} - {v_{P,A}}\\
&{v_n} = {v_{rel}} \cdot n\\
&J = jn\\
&v_A' = {v_A} - \frac{j}{{{m_A}}}n,v_B' = {v_B} + \frac{j}{{{m_B}}}n\\
&\omega _A' = {\omega _A} - I_A^{ - 1}\left( {{r_A} \times jn} \right),\omega _B' = {\omega _B} + I_B^{ - 1}\left( {{r_B} \times jn} \right)\\
&v_{P,A}' = v_A' + \omega _A' \times {r_A},v_{P,B}' = v_B' + \omega _B' \times {r_B}\\
&v_{rel}' = v_{P,B}' - v_{P,A}'\\
&v_{re{l^\prime }} \cdot n = - e\left( {{v_{rel}} \cdot n} \right)\\
&j = - \frac{{\left( {1 + e} \right){v_{rel}} \cdot n}}{{\frac{1}{{{m_A}}} + \frac{1}{{{m_B}}} + n \cdot \left[ {\left( {I_A^{ - 1}\left( {{r_A} \times n} \right)} \right) \times {r_A} + \left( {I_B^{ - 1}\left( {{r_B} \times n} \right)} \right) \times {r_B}} \right]}}
\end{align}
$$
## 6.2 Friction:
I don't know about this, too complicated.
# 7. Positional Correction:
Used **Baumgarte Stabilization Method** to avoid objects sink into each other, a correction directly on position with weighted mass distribution.
```python
Correction: NDArray[np.float32] = (0.2 * (Penetration - SLOP) / (self.A.ReciprocalMass + self.B.ReciprocalMass) * self.Normal)
self.A.Position -= Correction * self.A.ReciprocalMass
self.B.Position += Correction * self.B.ReciprocalMass
```

$$
\begin{align}
Correction_{n}=\beta \cdot (P-slop) \cdot \frac{\vec{n}}{m_{A}^{-1}+m_{B}^{-1}} \cdot w_{n}  \\
\small where \; P \; is \; the \; max \; penetration \;,\; w_{n} =m_{n}^{-1}
\end{align}
$$

---

The version in c++ is really similar to the version in python but have some major differences.

In c++ version, I uses SAT + XPBD instead of GJK+EPA+PBD. The reason why is simple, SAT is much more easier to understand than either GJK or EPA. Just imagine a flashlight shining on two objects, if they didn't collide, there must be at least one angle that when the flashlight is on them, the shadow they create are separate. From my personal experience of using GJK+EPA, they are too complicated to notice implementational errors in them. And huge cost of performance for them. As for XPBD, it is a better solution for the engine than PBD that's for sure. But also much more complicated no matter the derivation of it or just implement it into the engine. The explanation for all these amazing algorithm can be found on almost every platform, I don't think I will post blogs about any of these algorithms.

Since this version is actually not even a half-baked product, no contact manifold in c++ version. This is probably the end of this project. I really like this project, I had been working on it for almost one year (during my school years of course), but it is time to move on.