---
title: "The Integrator"
description: determines the position of the objects at the next moment
date: 2026-03-13T23:07:28+08:00
image: cover.png
math: true
license: 
comments: true
categories:
    - AtlasPhys
tags:
    - Physics
    - AtlasPhys
    - Integrator
build:
    list: always    # Change to "never" to hide the page from the list
---

## The Physical Model

All integration algorithms are derived from Newton's second law of motion. For a particle subjected to a force $F$:

$$
\begin{align}
F = ma = m \frac{d^2x}{dt^2}
\end{align}
$$

We usually split it into two first-order differential equation:

$$
\left\{
\begin{aligned}
\frac{dx}{dt} & = v \\
\frac{dv}{dt} & = a = \frac{F_{net}}{m}
\end{aligned}
\right.
$$

### Explicit Euler

This is the most intuitive solution, assume the step is $\Delta t$:
$$
x_{n+1} = x_{n} + v_{n} \Delta t \\
v_{n+1} = v_{n} + a_{n} \Delta t
$$

But it is extremely unreliable since it actually assumes that in a step, the velocity and the acceleration are constant.

### Semi-Implicit Euler

This is what I used in my project and it has just a slightly change compare to Explicit Euler, it updates the velocity and uses the new velocity to update the position.

$$
v_{n+1} = v_{n} + a_{n} \Delta t \\
x_{n+1} = x_{n} + v_{n+1} \Delta t
$$

It is much more reliable than the Explicit Euler.

Note: Rk4 and Implicit Euler is not considered here since they much more complicated than the above two methods and in most cases, semi-implicit euler can do its job pretty well.

## The Moment of Inertia Matrix

In 3D space, the "resistance to rotation" of an object is different for different axis. This cannot be expressed by a constant, but rather as a matrix of 3x3, which completely describes the distribution of mass in all directions. As long as the shape of the object does not collapse, this $I_{body}$ will not change.

For the world coordinate system, the "resistance to rotation" of the object is keep changing with its posture. Which means that, we must convert the $I_{body}$ to the $I_{world}$ all the time so that we can calculate the effect of the external forces on it correctly.

We know the Angular Momentum Theorem:

$$
L = I \omega
$$

It is true for both the world space and the local space:
$$
\begin{align}
L_{local} &= I_{body} \omega_{local} \\
L_{world} &= I_{world} \omega_{world}    
\end{align}
$$

To convert a vector from the local space to the world space, multiply current rotation matrix $R$:

$$
\begin{align}
L_{world} &= R L_{local} \\
\omega_{world} &= R \omega_{local} \\
\omega_{local} &= R^{-1} \omega_{world}
\end{align}
$$

> [!NOTE] Note:
> $$
\left[
\begin{matrix}
R_{11} & R_{12} & R_{13} \\
R_{21} & R_{22} & R_{23} \\
R_{31} & R_{32} & R_{33}
\end{matrix}
\right]
\left[
\begin{matrix}
x_{local} \\
y_{local} \\
z_{local}
\end{matrix}
\right] = \left[
\begin{matrix}
x_{world} \\
y_{world} \\
z_{world}
\end{matrix}
\right]
$$

Substitutes:

$$
\begin{align}
L_{world} &= R (I_{body} \omega_{local}) \\
L_{world} &= R I_{body} R^{-1} \omega_{world}
\end{align}
$$

So $I_{world}$ is equal to:

$$
I_{world} = R I_{body} R^{-1}
$$

And in the physics engine, we care about its inverse matrix because we need it to calculate the angular acceleration.

$$
\begin{align}
I_{world}^{-1} &= (R I_{body} R^{-1})^{-1} \\ 
I_{world}^{-1} &= (R^{-1})^{-1} I_{body}^{-1} R^{-1} \\
R^{-1} &= R^T \\
I_{world}^{-1} &= R I_{body}^{-1} R^T \\
\end{align}
$$

> [!NOTE] Note: Replace the $R^{-1}$ by $R^T$ is because calculate $R^T$ is much faster than $R^{-1}$.

## The Angular Acceleration

$$
\begin{align}
\tau &= \frac{dL}{dt} \\
L &= I \omega 
\end{align}
$$

Assume $I$ stays constant in a short period of time:
$$
\tau = \frac{d(I\omega)}{dt} = I\frac{d\omega}{dt} \\
\tau = I \alpha
$$

In 3D space, $\tau$ and $\alpha$ are vectors with size 3x1. $I$ is a matrix of size 3x3.

$$
\left[
\begin{matrix}
\tau_{x} \\
\tau_{y} \\
\tau_{z}
\end{matrix}
\right]
= \left[
\begin{matrix}
I_{xx} & I_{xy} & I_{xz} \\
I_{yx} & I_{yy} & I_{yz} \\
I_{zx} & I_{zy} & I_{zz} \\
\end{matrix}
\right]
\left[
\begin{matrix}
\alpha_{x} \\
\alpha_{y} \\ 
\alpha_{z}
\end{matrix}
\right] \\
\begin{align}
I^{-1} \tau &= (I^{-1}I)\alpha \\
I^{-1} \tau &= \alpha \\
\alpha &= I_{world}^{-1} \tau \\
\omega_{n+1} &= \omega_{n} + \alpha \Delta t
\end{align}
$$

## Summary

So, for each object in every time step, these should be updated by the integrator:
$$
\begin{align}
v_{n+1} &= v_{n} + a_{n} \Delta t \\
x_{n+1} &= x_{n} + v_{n+1} \Delta t \\
I_{world}^{-1} &= R I_{body}^{-1} R^T \\
\alpha &= I_{world}^{-1} \tau \\
\omega_{n+1} &= \omega_{n} + \alpha \Delta t
\end{align}
$$