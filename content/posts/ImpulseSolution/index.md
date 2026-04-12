---
title: "Impulse Solution"
description: 
date: 2026-03-31T19:30:31+08:00
image: cover.png
math: true
license: CC BY-NC-SA 4.0
comments: true
categories:
    - AtlasPhys
tags:
    - Physics
    - AtlasPhys
    - Math
    - Vectors
build:
    list: always    # Change to "never" to hide the page from the list
---

# Problem:
For a rigid body A with mass $m_{A}$ and another rigid body B with mass $m_{B}$ collide with linear velocity $v_{A},v_{B}$ respectively, solve for the scalar impulse $j$ of the two object. Friction is negligible.

# Variables:
| Symbol                  | Definition                                    |
| :---------------------- | --------------------------------------------- |
| $m_{A},m_{B}$           | Mass of object A and B                        |
| $I_{A},I_{B}$           | Moment of inertia tensor of object A and B    |
| $x_{A},x_{B}$           | Center of Mass                                |
| $P$                     | Contact point                                 |
| $r_{A},r_{B}$           | $P-x$                                         |
| $v_{A},v_{B}$           | linear velocity before impact                 |
| $\omega_{A},\omega_{B}$ | Angular velocity before impact                |
| $v_{PA},v_{PB}$         | Absolute linear velocity of the contact point |
| $v_{rel}$               | relative velocity of contact point            |
| $n$                     | Contact normal (unit vector)                  |
| $e$                     | coefficient of restitution                    |
| $J$                     | impulse vector                                |
| $j$                     | impulse scalar                                |
# Derivation:
The velocity of any point on a rigid body is the sum of its linear velocity and its tangential velocity due to rotation.
From the definition:

$$
\begin{align}
r_{A} = P - x_{A}, r_{B} = P - x_{B} 
\begin{aligned}
v_{PA} &=v_{A}+\omega_{A} \times r_{A} \\
v_{PB} &= v_{B}+\omega_{B} \times r_{B} \\
\end{aligned}
\end{align}
$$

The relative velocity of body B with respect to body A at the contact point:

$$
v_{rel} = v_{PB} - v_{PA}
$$
The impulse $J$ acts along the contact normal $n$:
$$
J = jn
$$
According to Impulse-Momentum Theorem ($\Delta v=\frac{J}{m}$):
$$
\begin{align}
v_{A}'=v_{A}-\frac{j}{m_{A}}n \\
v_{B}'=v_{B}+\frac{j}{m_{B}}n
\end{align}
$$
The impulse also applies a torque which changes the angular velocities ($\Delta \omega = I^{-1}(r \times J)$):
$$
\begin{align}
\omega_{A}'=\omega_{A} - I^{-1}_{A}(r_{A}\times jn) \\
\omega_{B}'=\omega_{B} + I^{-1}_{B}(r_{B}\times jn)
\end{align}
$$
Then the velocity of the contact point immediately after the impact can be expressed as:
$$
\begin{align}
v_{PA}'=v_{A}'+\omega_{A}'\times r_{A} \\ \\
v_{PB}'=v_{B}'+\omega_{B}'\times r_{B}
\end{align}
$$
Substitute the equations into this expression:
$$
\begin{align}
v_{PA}' &= (v_{A}-\frac{j}{m_{A}}n)+(\omega_{A} - I^{-1}_{A}(r_{A}\times jn)) \times r_{A} \\
v_{PB}' &= (v_{B}+\frac{j}{m_{B}}n) + (\omega_{B} + I^{-1}_{B}(r_{B}\times jn)) \times r_{B}
\end{align}
$$
Factor out the $j$ and simplify:
$$
\begin{align}
v_{PA}' &= v_{PA} - j[\frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A}] \\
v_{PB}' &= v_{PB} + j[\frac{n}{m_{B}} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B}]
\end{align}
$$
The new relative velocity:
$$
\begin{align}
v_{rel}'=v_{PB}'-v_{PA}'&= ((v_{B}+\frac{j}{m_{B}}n) + (\omega_{B} + I^{-1}_{B}(r_{B}\times jn)) \times r_{B}) - (v_{PA} - j[\frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A}]) \\
v_{rel}'&=v_{rel}+j(\frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A} + \frac{n}{m_{B}} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B})
\end{align}
$$
According to Newton's law of Restitution, the ratio of the relative speed of separation and the relative speed of approach when two objects collide equals to the coefficient of restitution.
$$
v_{rel}' \cdot n = -e (v_{rel} \cdot n), e \in [0, 1]
$$
Substitute $v_{rel}'$ and simplify the equation:
$$
\begin{align}
\left[ v_{rel}+j\left( \frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A} + \frac{n}{m_{B}} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B} \right) \right] \cdot n &= -e (v_{rel} \cdot n) \\
j\left( \frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A} + \frac{n}{m_{B}} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B} \right) \cdot n &= -(1+e)(v_{rel} \cdot n) \\
j = \frac{-(1+e)(v_{rel} \cdot n)}{\left( \frac{n}{m_{A}} + (I^{-1}_{A}(r_{A}\times n)) \times r_{A} + \frac{n}{m_{B}} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B} \right) \cdot n } \\
j = \frac{-(1+e)(v_{rel} \cdot n)}{ \frac{1}{m_{A}} + \frac{1}{m_{B}} + \left[(I^{-1}_{A}(r_{A}\times n)) \times r_{A} + (I^{-1}_{B}(r_{B}\times n)) \times r_{B} \right] \cdot n }
\end{align}
$$
