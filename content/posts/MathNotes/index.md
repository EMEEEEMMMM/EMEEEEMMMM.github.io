---
title: "Math Notes"
description: My notes about limit, derivative, integral.
date: 2026-04-28T17:40:36+08:00
image: cover.png
math: true
license: CC BY-NC-SA 4.0
comments: true
categories:
    - Notes
tags:
    - Limit 
    - Math 
    - Derivative 
    - Trigonometry 
    - Integral 
    - Calculus
build:
    list: always    # Change to "never" to hide the page from the list
---
# Trigonometry:

$$
\begin{align}
& \sin x = \frac{1}{\csc x} \\
& \cos x = \frac{1}{\sec x} \\
& \tan x = \frac{1}{\cot x} \\
& \tan^2 \theta + 1 = \sec^2 \theta \\
& \cot^2 \theta + 1 = \csc^2 \theta \\
& \arcsin x + \arccos x = \frac{\pi}{2} \\
&f(x)=\arctan x + \arctan \frac{1}{x}=\begin{cases}
\frac{\pi}{2}, x > 0 \\
-\frac{\pi}{2}, x < 0
\end{cases}
\end{align}
$$
# Limits:
$$
\begin{align}
\lim_{x \to 0} \frac{\sin (x)}{x} &= 1  \\
e = \lim_{x \to \infty} (1+\frac{1}{x})^x&=\lim_{x \to 0^+} (1+x)^{\frac{1}{x}} \\
\end{align}
$$


# Derivative and Integral:

| Derivative                                                             | Integral                                                          |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------- |
| $$\frac{d}{dx}x^n = nx^{n-1} $$                                        | $$\int x^n dx= \frac{1}{n+1} x^{n+1} + C$$                        |
| $$\frac{d}{dx} \sin x = \cos x$$                                       | $$\int \cos x \ dx = \sin x + C$$                                 |
| $$\frac{d}{dx} \cos x = - \sin x$$                                     | $$\int \sin x \ dx = - \cos x + C$$                               |
| $$\frac{d}{dx}\ln(x) = \frac{1}{x}$$                                   | $$\int \frac{1}{x} dx = \ln \lvert x \rvert + C$$                 |
| $$\frac{d}{dx} e^x = e^x$$                                             | $$\int e^x dx = e^x + C$$                                         |
| $$\frac{d}{dx} a^x = a^x \times \ln a$$                                | $$\int a^x dx=\frac{a^x}{\ln(a)} + C, a>0,a\neq1$$                |
| $$\frac{d}{dx} \tan x=\sec^2 x$$                                       | $$\int \sec^2 x \ dx = \tan x + C$$                               |
| $$\frac{d}{dx} \cot x = -csc^2x$$                                      | $$\int \csc^2 x \ dx = - \cot x + C$$                             |
| $$\frac{d}{dx} \sec x = \sec x \cdot \tan x$$                          | $$\int \sec x \cdot \tan x \ dx = \sec x + C$$                    |
| $$\frac{d}{dx} \csc x = - \csc x \cdot \cot x$$                        | $$\int \csc x \cdot \cot x \ dx = - \csc x + C $$                 |
| $$\frac{d}{dx} \arcsin x = \frac{1}{\sqrt{ 1-x^2 }}$$                  | $$\int \frac{1}{\sqrt{1-x^2}}dx=\arcsin x + C$$                   |
| $$\frac{d}{dx} \arccos x = - \frac{1}{\sqrt{ 1-x^2 }}$$                | $$\int -\frac{1}{\sqrt{1-x^2}}dx = \arccos x + C$$                |
| $$\frac{d}{dx} \arctan x = \frac{1}{1+x^2}$$                           | $$\int \frac{1}{1+x^2}dx = \arctan x + C$$                        |
| $$\frac{d}{dx} sec^{-1} x = \frac{1}{\lvert x \rvert \sqrt{x^2 - 1}}$$ | $$\int \frac{1}{\lvert x \rvert \sqrt{x^2-1}}dx = sec^{-1}x + C$$ |
## More Integration Formula:
$$
\begin{align}
\int \tan x \ dx &= \ln \lvert \sec x \rvert + C \ or \ -\ln\lvert \cos x \rvert + C \\
\int \cot x \ dx &= \ln \lvert \sin x \rvert + C \ or \ -\ln\lvert \csc x \rvert + C \\
\int \sec x \ dx &= \ln \lvert \sec x + \tan x \rvert + C \\
\int \csc x \ dx &= \ln \lvert \csc x - \cot x \rvert + C \\
\int \ln x \ dx &= x \ \ln \lvert x \rvert  - x + C \\
\int \frac{1}{\sqrt{a^2-x^2}} dx &= \arcsin(\frac{x}{a}) + C \\
\int \frac{1}{a^2+x^2} dx &= \frac{1}{a} \arctan (\frac{x}{a}) + C \\
\int \frac{1}{x{\sqrt{x^2-a^2}}} dx &= \frac{1}{a} \sec^{-1}\lvert\frac{x}{a} \rvert + C \ or \ \frac{1}{a} \arccos \lvert \frac{a}{x} \rvert + C \\
\int \sin^2 x \ dx &= \frac{x}{2} - \frac{\sin 2x}{4} + C
\end{align}
$$

## U-Substitution Method:
If $f(g(x))$ and $f \prime$ are continuous and $F \prime = f$, then
$$\int f(g(x))g\prime(x)dx = F(g(x)) + C$$
Let $u=g(x)$, then $du = g \prime (x) dx$:
$$\int f(g(x))g\prime(x)dx = \int f(u) du = F(u) + C = F(g(x)) + C$$
## Integration by Parts:
According to the product rule for differentiation:
$$
\frac{d}{dx} (uv) = u \frac{dv}{dx} + v \frac{du}{dx}
$$
Integrating tells us that:
$$
uv = \int u \frac{dv}{dx} + \int v \frac{du}{dx}
$$
Therefore:
$$
\int u \frac{dv}{dx} = uv - \int v \frac{du}{dx}
$$
## Trigonometrical substitution:
Find: 
$$I = \int \sqrt{a^2-x^2}dx, a \geq 0$$
First, solve the inequality:
$$
\begin{align*} \\
a^2 - x^2 &\ge 0 \\
\implies x^2 &\le a^2 \\
\implies -a &\le x \le a
\end{align*}
$$

Second, substitute x with a trigonometric function and solve the integral:
Let $x=a \sin t$, $t \in \left[-\frac{\pi}{2},\frac{\pi}{2} \right]$, then $dx = a \cdot \cos t dt$
$$
\begin{align}
I &= \int \sqrt{a^2 - (a \sin t)^2} a \cdot \cos t dt \\
&= \int \sqrt{a^2(1-\sin^2 t)} a \cdot \cos t dt \\
&= \int a^2 \cdot \cos^2 t \ dt \\
&= a^2 \int \frac{\cos (2t) + 1}{2} dt \\
&= a^2 \int \frac{1}{2} \cos 2t + \frac{1}{2} dt \\
&= \frac{a^2}{2} \sin t \cdot \cos t + \frac{a^2}{2} \cdot t + C
\end{align}
$$
Third, substitute x back into the expression by imagining a "fake" triangle:
$$
\begin{align}
I &= \frac{x}{2} \sqrt{a^2 - x^2} + \frac{a^2}{2} \arcsin (\frac{x}{a}) + C
\end{align}
$$
## Integration by Partial Fractions:
Factor the denominator of the function, list the equations and solve them using the undetermined coefficient method. The big function will be split into two or more small functions which are easier to integrate than a whole big function.

# First-Fundamental Theorems of Calculus:
If $f$ is continuous on $[a, b]$ and $F$ is an anti derivative of  $f$ on $[a, b]$, then
$$
\begin{align}
&\int_a^b f(x) dx = F(b) - F(a) \\
&Note: \ F(b) - F(a) \ is \ often \ denoted \ as [F(x)]^b_a
\end{align}
$$

# Area Between Two curves:
$$
\begin{align}
A = \int^b_a(upper \ curve - lower \ curve) dx
\end{align}
$$

# Volumes and Definite Integrals:
## The Disc Method:
Revolving about a line $y=k$:
$$
V = \pi \int^b_c(f(x) - k)^2 dx, where \ \lvert f(x) - k \rvert = radius
$$
Revolving about a line $x=b$:
$$
V = \pi \int^d_c (g(y) - h)^2 dy, where \ \lvert g(y) - h \rvert = radius
$$
## The Washer Method:
The volume of a solid (with a hole in the middle) generated by revolving a region bounded by 2 curves:
About a line $x=h$:
$$
V = \pi \int^b_a \left[ (f(x) - h)^2 - (g(x) - h)^2 \right]dx
$$
About a line $y=k$:
$$
V = \pi \int^d_c \left[(p(y) - k)^2 - (q(y) - k)^2 \right]dy
$$

# Integration of Parametric, Polar Curves:
## Parametric Curves:
### Area:
For a curve defined parametrically by $x=f(t)$ and $y=g(t)$, the area bounded by the curve between $t=\alpha$ and $t=\beta$ is $A = \int^\beta_\alpha g(t)f\prime(t)dt$.
### Arc Length for Parametric Curves:
The length of that arc is $L = \int^\beta_\alpha \sqrt{(\frac{dx}{dt})^2+(\frac{dy}{dt})^2} dt$.

Proof:
Divide the parametric interval $\left[\alpha, \beta \right]$ into $n$ intervals.
$$
\alpha = t_0 < t_1 < t_2 < \cdots < t_n = \beta
$$
The corresponding delta t for every interval is $\Delta t_i=t_i - t_{i-1}$,  correspond to the point on the curve is $P_i(x_i, y_i)$, where $x_i=f(t_i), y_i=g(t_i)$.
Connect the neighboring points $P_{i-1}$ and $P_{i}$ with a straight line, the total length of which is the sum of the lengths of all the segments:
$$
L_n = \Sigma_{i=1}^n \lvert P_{i-1}P_{i} \rvert
$$
When the maximum length of the intervals $\lambda = \max (\Delta t_1, \Delta t_2, \cdots, \Delta t_n) \to 0$, the limit of $L_n$ exist, this limit is the length of the curve:
$$
L = \lim_{\lambda \to 0} \Sigma_{i=1}^n \lvert P_{i-1} P_{i} \rvert
$$

According to Pythagorean theorem, every segment $\lvert P_{i-1} P_{i} \rvert$ can be expressed as:
$$
\begin{align}
&\lvert P_{i-1} P_{i} \rvert = \sqrt{(\Delta x_i)^2 + (\Delta y_{i})^2} \\
&where \ \Delta x_i = x_i - x_{i-1} = f(t_i) - f(t_{i-1}), \Delta y_i = y_i - y_{i-1} = g(t_i) - g(t_{i-1})
\end{align}
$$

According to the Lagrange mean value theorem, for every interval $\left[t_{i-1} , t_i \right]$, there must exist $\xi_i \in (t_{i-1}, t_i)$, such that $\Delta x_i = f\prime(\xi_i) \cdot \Delta t_i$. Similarly, there must also exist $\eta_i \in (t_{i-1}, t_i)$, such that $\Delta y_i = g\prime(\eta_i) \cdot \Delta t_i$.
Substitute them into the segments' expression:
$$
\begin{align}
\lvert P_{i-1} P_{i} \rvert &= \sqrt{(f\prime (\xi_i)\Delta t_i)^2 + (g\prime(\eta_i)\Delta t_i)^2} \\
\lvert P_{i-1} P_{i} \rvert &= \sqrt{(f\prime (\xi_i))^2 + (g\prime(\eta_i))^2} \ \cdot \Delta t_i
\end{align}
$$
So the expression of the length of the curve becomes:
$$
L = \lim_{\lambda \to 0} \Sigma_{i=1}^n \sqrt{(f\prime (\xi_i))^2 + (g\prime(\eta_i))^2} \ \cdot \Delta t_i
$$
Since when  $\lambda \to 0$, the difference between $\eta_i$ and $\xi_i$ is also approaching 0 $\eta_i - \xi_i \to 0$, replacing $\eta_i$ by $\xi_i$ will remain the limit unchanged.
According to the definition of the definite integral, when $\lambda \to 0$, the limit of Riemann sum is the definite integral on interval $\left[\alpha, \beta \right]$:
$$
L = \lim_{\lambda \to 0} L_n =  \int ^\beta_\alpha \sqrt{(f\prime (t))^2 + (g\prime(t))^2} \ dt = \int^\beta_\alpha \sqrt{(\frac{dx}{dt})^2 + (\frac{dy}{dt})^2} \ dt
$$

### Surface Area for Parametric Curves:
The surface area created when that arc is revolved about the x-axis is
$$
S = \int^\beta_\alpha 2 \pi y \sqrt{(\frac{dx}{dt})^2 + (\frac{dy}{dt})^2} dt
$$

## Polar Curves
### Area for Polar Curves:
If $r=f(\theta)$ is a continuous polar curve on the interval $\alpha \leq \theta \leq \beta$ and $\alpha < \beta < \alpha + 2 \pi$, then the area enclosed by the polar curve is 
$$
A = \frac{1}{2} \int^\beta_\alpha \left[f(\theta)\right]^2 d\theta = \frac{1}{2} \int^\beta_\alpha r^2 d\theta
$$

### Arc Length for Polar Curves:
For a polar graph defined on a interval $\left(\alpha, \beta \right)$, if the graph does not retrace itself in that interval and if $\frac{dr}{d\theta}$ is continuous, then the length of the arc from $\theta=\alpha$ to $\theta = \beta$ is 
$$
L = \int^\beta_\alpha \sqrt{r^2 + (\frac{dr}{d\theta})^2} d\theta
$$
# Differential Equations
## Separable Differential Equations:
1. Separate the variables: $g(y)dy = f(x) dx$
2. Integrate both sides: $\int g(y) dy = \int f(x) dx$
3. Solve for $y$ to get a general solution
4. Substitute given conditions to get a particular solution
5. Verify your result by differentiating
## Logistic Differential Equations:
Logistic growth is represented by the differential equation
$$
\frac{dP}{dt} = kP ( 1 - \frac{P}{K})
$$
where $P$ is the population, $K$ is the carrying capacity, and $k$ is the proportional constant.
Derivation:
This is a separable differential equation so
$$
\begin{align}
\frac{dP}{dt} &= \frac{kP(K-P)}{K} \\
\frac{K}{P(K-P)} dP &= k dt \\
\int \frac{KdP}{P(K-P)}dP &= \int k dt \\
\int (\frac{1}{P} + \frac{1}{K-P})dP &= \int k dt \\
\ln{\lvert P \rvert} - \ln{\lvert K-P \rvert} &= kt + C_1 \\
\ln{\left\lvert \frac{P}{K-P} \right\rvert} &= kt + C_1 \\
e^{kt+C_1} &= \frac{P}{K-P} \\
let \ C_2&=e^{C_{1}} \\
C_2 e^{kt} (K - P) &= P \\
C_2 e^{kt} K &= P (C_2 e^{kt} + 1) \\
P &= \frac{C_2 e^{kt} K}{C_2 e^{kt} + 1} \\
P(t) &= \frac{K}{(\frac{1}{C_2})e^{-kt}+1}
\end{align}
$$
