---
title:  "Robotics algorithms, derivations, concepts"
use_math: true
---

Mostly notes from stuff I used in interviews. 

### Collision checking

1. Broad phase(s) : get a list of entities that *could* be colliding. This could be with some tree structure, and/or something cheap like axis-aligned bounding boxes.
2. Narrow phase : detailed check to see if entities are in collision.

Some ideas for narrow phase:

* Represent polyhedron as set of linear inequalities. To check if a vertex is inside polyhedron, check if it is on the same side of every inequality as the center of the polyhedron. (This works for 2D shapes, where edge intersection can't happen without vertex being inside other shape. Doesn't apply to 2D)
* _More general method_: check for line-line intersections. Solve for point along infinite lines on which the two lines intersect, then check if this is on edge (i.e. between the two vertices).

Checking if point is inside polygon:

* Inequality method works only if the shape is convex
* Shooting ray method: shoot a ray out in a random direction and count intersections with edges. If even, outside. If odd, inside.

### Control/estimation

#### Linear quadratic regulator/estimator (LQR, Kalman filter)

Both assume linear system, Gaussian noise.

$x_{t+1} = Ax_t+Bu_t+w_t,\ z_t = Hx_t + v_t$

$p(w) = N(0, Q), p(v) = N(0, R)$

Kalman filter: predict, update (fix $A,B,H, Q,R$. carry state and covariance matrix)


[**LQR derivation**](https://stanford.edu/class/ee363/lectures/dlqr.pdf)

Minimize value function:

$V_t = \sum_{\tau}^{N-1}(x_\tau^T Q x_\tau + u_\tau^T R u_\tau) + x_N^T Q_f x_N$

Assume V is quadratic: $V = z^T P_t z$, where P positive semi-definite

Then by dynamic programming, minimize cost

$u_t^\text{lqr} = \text{argmin}_w(w^T R w + V_{t+1}(Az + Bw))$

$V_t(z) = z^T Q z + \text{min}_w(w^T R w + (Az + Bw)^T P_{t+1} (Az+Bw))$

Set derivative to zero:

$2w^TR + 2(Az+Bw)^T P_{t+1}B = 0$

$w_{opt} = -(R + B^T P_{t+1} B)^{-1} B^T P_{t+1} A_z$

**Kalman filter**

Estimation error is $e_k = x_k - \hat{x}$

Minimize error covariance $P_k = E(e_k e_k^T)$ (outer product of vectors makes matrix)

### Factor graphs 

* Loop closures are factors (constraints between poses derived by aligning measurements)
* To avoid factoring entire matrix each time a new factor is added:
  - Method 1
    - incrementally add factors with ___ rotations
    - batch re-factorization less often
    - variable re-ordering to maintain sparsity
  - To avoid batch re-factorization altogether: iSAM2 variable elimination Bayes net with chordal structure (Bayes tree)
    - Factor graph stored in Bayes tree. Just need to re-factorize part of Bayes tree that factor is added to
    - Related to square root information matrix

References:

* Factor graphs for Robot Perception, Daellert and Kaess

### Planning

Sampling-based better for high-dimensional spaces, but no guarantees of optimality (except RRT* in the limit). Search-based algorithms can provide guarantees, but need to expand more states so they're slow.

Construct & multiple-query (e.g. PRM) vs. one-shot (RRT)

_RRT_

1. Sample $q_\text{rand}$
2. Find $q_\text{near}$
3. Extend towards it to get $q_\text{new}$
4. Repeat until you reach goal

Other tricks: goal biasing, check if goal is reachable from $q_\text{new}$

_RRT-star_

Steps 1~3 same as RRT, with two steps after that

1. Sample $q_\text{rand}$
2. Find $q_\text{near}$
3. Extend towards it to get $q_\text{new}$
4. Check neighborhood of $q_\text{new}$ to see if it's cheaper to get _to_ $q_\text{new}$ from another $q_\text{nearest}$. If so, rewire (add/remove edge, basically change parent pointer)
5. Check neighborhood of $q_\text{new}$ if it's cheaper to get _from_ $q_\text{new}$ to $q_\text{neighbor}$ if so, rewire

_A-star_

* Best-first-search
* Heuristic must be an underestimate of the real cost-to-go
* In practice, design of suitable heuristics for tasks is important

### Vision

_Canny edge detector_

Input of Canny is output of Sobel edge detector, which does RGB to greyscale conversion, Gaussian blur, compute gradients in x and y directions (separately, with kernel convolution).

Canny does:

1. Thin edges to 1 pixel wide (find maximum gradient on direction perpendicular to edge)
2. Hysteresis thresholding - two values. Automatically accept gradients above top threshold, reject gradients above bottom threshold. Accept gradients in between two thresholds if they are connected to edges above top threshold.
