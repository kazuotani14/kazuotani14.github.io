---
title:  "Misc math notes"
use_math: true
---

# Useful tid-bits 

### Logarithms 

* $e=2.718\dots$ is the constant for continuous 100% compounding
    * $e^x = e^{rt}$ is the amount of continuous growth after $x$ amount of time 
    * $ln(x)$ is the amount of time needed to reach a certain level of continuous growth 
    * Continuous growth is contrasted with discrete growth (e.g. interest every month from saving account)
    * Reference: https://betterexplained.com/articles/an-intuitive-guide-to-exponential-functions-e/

_Log rules_

* $\log_b(mn) = \log_b(m)+\log_b(n)$
* $\log_b(m/n) = \log_b(m)-\log_b(n)$
* $\log_b(m^n) = n \log_b(m)$
* Change of base: $\log_b(x)=\frac{\log_d(x)}{\log_d(b)}$
    * changing the base is just a linear scaling 
* $\log(x)=y \rightarrow e^{\log(x)} = e^y \rightarrow e^y=x$

### Incremental mean 

$\mu_k = \frac{1}{k}\sum_{j=1}^{k} x_j$

$=\frac{1}{k}\left(x_k + \sum_{j=1}^{k-1}x_j \right)$

$=\frac{1}{k}(x_k + (k-1)\mu_{k-1})$

$=\mu_{k-1}+\frac{1}{k}(x_k - \mu_{k-1})$

### Numerical integration

For $\frac{dy}{dt}=f(t,y)$:

* Forward Euler: $y_{k+1}=y_k+hf(t_{k},y_{k})$
* Backward Euler: $y_{k+1}=y_k+hf(t_{k+1},y_{k+1})$
	* Note that $y_{k+1}$ appears on both sides of the equation. Thus this is an implicit method and must be solved iteratively
* [Newmark-beta method](https://en.wikipedia.org/wiki/Newmark-beta_method)

### Derivatives

_Multivariate chain rule_

For function $z(x(t),y(t))$, $\frac{\delta z}{\delta t} = \frac{\delta z}{\delta x}\frac{\delta x}{\delta t} + \frac{\delta z}{\delta y}\frac{\delta y}{\delta t}$

### Geometry

* Finding the angle between 2 vectors in 2D
    - use $atan2$, which returns values in the range $[-\pi, \pi]$
    - $\theta = atan2(v2.y, v2.x) - atan2(v1.y, v1.x)$
- Dot product
    - $v_1\cdot v_2 = v_{1x}v_{2x} + v_{1y}v_{2y}=\|v_1\| \|v_2\|cos(\theta)$
    - Projection onto vector
* Averaging rotations: project into Euclidean vectors on unit ball, average the vectors and find rotation again
* Hyperplanes in n-dimensions: $a^T x = b$ or $a^T x - b = 0$, where $a,x$ are vectors and $b$ is scalar
    * $a$ is direction (normal vector of plane), $b/||w||$ is shortest/perpendicular distance between origin and hyperplane
    * Distance between hyperplane and point: take projection of point onto plane
        * $d = \frac{|w \cdot x + b|}{ ||w|| }$
        * Intuition: $w \cdot x = \|w\|\|x\|cos\theta$, unnormalized projection of $x$ onto $w$. $w \cdot x - b$ is distance between hyperplane and $x$ in unnormalized units. "Normalized" projection (in Euclidean units) would be $\frac{\|w \cdot x - b\|}{\|\|w\|\|}$. 
        * https://math.stackexchange.com/questions/1210545/distance-from-a-point-to-a-hyperplane


### Support vector machine

Goal: Find a "maximum margin hyperplane" that divides the data into correct sets. 

* Hyperplane can be written as: $\vec{w}\vec{x}+b = 0$ or $\vec{w}\vec{x}=0$ with bias trick (add $1$ to bottom of $x$)
* For linearly separable training data, we can try to find two parallel hyperplanes that are max distance ("margin") apart, which separate data: $\vec{w}\vec{x}+b=1, \vec{w}\vec{x}+b=-1$. The distance between the two hyperplanes is $\frac{2}{\||\vec{w}\||}$, so to maximize distance we want to minimize $\||\vec{w}\||$. Adding constraints to keep the two sets on different sides of the two hyperplanes, the whole thing can be written as a convex optimization problem (primal formulation): 
$$\begin{eqnarray}\min_{w,b} \|\|w\|\|^2 \\ \text{subject to } y_i(w^T x_i - b) \geq 1 \text{, for } i=1, \ldots, n \end{eqnarray}$$
* Support vectors: data points $x_i$ close to the separating hyperplane that determine the maximum margin
* Note that SVMs in their normal form can only do binary classification

#### Data that's not linearly separable

* For data that is not linearly separable, use hinge loss plus regularization/margin size: $max(0, 1-y_i(w^T x_i - b)) + \lambda \|w\|^2$
    * Use sub-gradients

* Can also do it with slack variable: $y_i(w^T x_i - b) \geq 1 - \xi_i$, where $\xi_i$ is distance from the margin for mis-classified points. $\xi_i \geq 0$. 
$$\begin{eqnarray}\min_{w,\xi} \|\|w\|\|^2+C\sum_i \xi_i,  \\ \text{subject to } y_i(w^T x_i - b) \geq 1 - \xi_i \text{, for } i=1, \ldots, n \end{eqnarray}$$

#### Kernel trick

Replace dot products with nonlinear kernel functions to transform the data into an implicit feature space, in which a linear separating hyperplane can be computed. The idea is that we want to do normal linear SVM on a set of transformed data points $\psi(x_i)$. We are given a kernel function that satisfies $k(x_i, x_j)= \psi(x_i) \cdot \psi(x_j)$. 

Instead of explicitly calculating $\psi(x_i)$ for each data point, which may be expensive for a large amount of high-dimensional data points, we can calculate the kernel $k(x_i, x_j)$ directly. It's cheaper because dot product between two vectors is a scalar, so instead of transforming $N$ vectors of dimension $D$ we can just find $N$ scalars (each one "compared" to vector $w$).  

Shortest/perpendicular distance from a hyperplane to a point: $d = \frac{\|w \cdot x + b\|}{ \|\|w\|\| }$. So with bias trick, $w \cdot x$ encodes "distance" (unnormalized by $\|\|w\|\|$) and the side of the separator that $x$ is on. What the kernel does is define a new "distance metric" between the hyperplane and the data point. This distance metric can implicitly be defined in a higher-dimensional space by the kernel function. See [Stanford notes](http://cs229.stanford.edu/notes/cs229-notes3.pdf) for examples. 

* For matrix $K$ where $K_{ij} = K(x_i, x_j)$, necessary and sufficient condition for the kernel to be valid is that it is positive semi-definite

#### References

* <http://cs231n.github.io/linear-classify/>
* <https://en.wikipedia.org/wiki/Support_vector_machine>
* <http://cs229.stanford.edu/notes/cs229-notes3.pdf>    
