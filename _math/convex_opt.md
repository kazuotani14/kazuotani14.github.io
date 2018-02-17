---
title:  "Convex optimization"
---

### Interior point method

The interior point method builds on the fact that a QP that is only equality-constrained has a closed-form solution. Complexity is $O(n^6)$ for computing a step direction (because it's a second order method)

##### [Lagrange multiplier](https://en.wikipedia.org/wiki/Lagrange_multiplier) method for equality-constrained QP

$\text{minimize } \frac{1}{2}x^TQx+c^Tx, \text{ subject to } Ax=b$

Lagrangian is as below. Minimizer of lagrangian is also minimizer of original solution.

${L(x,\lambda) = \frac{1}{2}x^TQx + c^Tx+\lambda^T(Ax-b)}$

$\frac{\delta L}{\delta x} = Qx+\lambda^TA=-c$

Combining with equality constraint $Ax=b$,

$\begin{bmatrix} Q & A^T \\ A & 0 \end{bmatrix}\begin{bmatrix}x\\ \lambda \end{bmatrix} = \begin{bmatrix}-c \\ b \end{bmatrix}$

##### Converting inequality constraints to costs with [barrier method](http://www.stat.cmu.edu/~ryantibs/convexopt-S15/scribes/15-barr-method-scribed.pdf)

We can convert inequality constraints $h_i(x)\leq 0$ to costs with log barrier:

$\phi(x) = -(1/t)\sum_{i=1}^{m}log(-h_i(x))$

So cost is $f(x) + \phi(x)$, where $f(x)$ is original cost function.

To solve the QP (with inequality constraints turned into costs), set $t=t_0$, then iteratively solve barrier problem and set $t^{(k+1)}=\mu t$, where $\mu>1$. This takes the solution along the "central path," towards the true solution (stop when within tolerance).

### Alternating directions

The alternating direction method of multipliers (ADMM) is an algorithm that solves convex optimization problems by breaking them into smaller pieces, each of which are then easier to handle.

### Misc

**TODO** put convex optimization forms + common tricks for reformulating here.

* JRL has some nice QP solver interfaces for Eigen: <https://github.com/jrl-umi3218>
* A cone is defined as a set in which positive linear combination of any two elements in the set are also within the set (ex. positive semidefinite matrices)
* Generalized inequalities are with respect to a proper cone: For $x,y\in K$, $x\geq_Ky$ if $x-y\in K$
* LP $\subset$ QP $\subset$ QCQP $\subset$ SOCP $\subset$ SDP
	* [Reference from EE364A](https://see.stanford.edu/materials/lsocoee364a/04ConvexOptimizationProblems.pdf)
	* Solvers like CVX put everything in SOCP form

* [Brief explanation of solver algorithms](https://math.stackexchange.com/questions/2264021/what-is-the-difference-between-interior-point-methods-active-set-methods-cutti)
* Any convex optimization problem can be transformed into minimizing (or maximizing) a linear function over a convex set by converting to the epigraph form

* References on duality
	* <https://math.stackexchange.com/questions/223235/please-explain-the-intuition-behind-the-dual-problem-in-optimization>
	* <http://gpfreitas.net/static/convexopt.pdf>

* KKT conditions
	* Necessary conditions for a solution in nonlinear programming to be optimal (sufficient if convex and Slater's holds)
	* stationarity, primal feasibility, dual feasibility, complementary slackness
	* References
		* <https://www.cs.cmu.edu/~ggordon/10725-F12/slides/16-kkt.pdf> TODO move stuff from these slides to notes

	* subgradient methods: slow, simple, but rugged

* Decomposition: think of it in terms of circuits or economics
	* Primal decomposition: fix public variables, each unit comes back with lambdas that tell you how much better they could do if given a bit more of public variable
	* Dual decomposition: fix prices of violation, everyone optimizes individually and tells you what they got
	* Optimization is analogous to: fixing voltage (with capacitor) or current (with inductor), and waiting for equilibrium
	* any linear function is separable in variables! so if objective is separable and there are linear constraints, dual decomposition will work

* cardinality $card(v)$ = number of non-zeros in a vector $v$. Can use this function as a constraint or an objective: useful for sparse modeling, design, etc.
	* heuristic for solving: replace $card(x)$ with $\gamma \|\|x\|\|_1$ ($l_1$ norm)
	* analog for matrices: rank

* Levenberg-Marquardt / Damped Least-Squares
	* Interpolation between gradient-descent and Gauss-Newton (specialization of Newton's method for minimizing squared costs)
	* Higher damping/lambda $\rightarrow$ Hessian is more positive definite. Curvature increases, which means that the step size towards the minimum of the local quadratic approximation gets smaller.

* Newton's method intuition: gradient method applied after a change of coordinates that makes the local curvature isotropic (scale gradient based on Hessian)

* Every convex optimization solver will return dual-feasible points as "certificate". These $\lambda$s and $\mu$s provide certificate of solution, and also the sensitivity of the optimal value of the function to the constraints

* Finding a point in intersection of convex sets (which you can project onto) - subgradient method, alternating projections. project onto farthest set at each iteration
	* e.g. filling in missing values - in data, or missing values in matrix that needs to be positive definite and symmetric
	* subgradient methods are very slow to converge

* At any point in convex set defined by polyhedron, ellipsoid defined by the Hessian of the log barrier function is guaranteed to be inside the set.
	* $\epsilon = \{x\text{ \| }(x-z)^TH(x-z)\leq1\}$
	* There's a closed-form solution for the Hessian!
		* $H=\triangledown^2\sum_i^m \log(b_i-a_i^Tz)\|_{z=x} = \sum_i^m\frac{a_ia_i^T}{(b_i-a_i^Tx)^2}$ This equation might be a bit off, double-check when using.
* Hierarchy of methods for solving $Ax=b$ : direct dense, direct sparse, iterative (ex. conjugate gradient)
* Conjugate gradient method: with luck (and good preconditioning), can solve very large problems
	* Uses controllability matrix/Krylov subspace to slowly expand search space
	* Main point: instead of holding $A$ matrix in memory like in direct methods, just need method to compute matrix-vector product $Av$.

##### Examples

* Stochastic robust Least Squares with $A=\overline{A}+U$, U random, $E(U)=0$, $E(U^TU)=P$. Turns into an equivalent normal least squares problem
	* $E\|\|Ax-b\|\|^2_2 = E\|\|\overline{A}x-b+Ux\|\|^2_2$
	* $=\|\|Ax-b\|\|^2_2+E(x^TU^TUx)$
	* $=\|\|Ax-b\|\|^2_2 + x^TPx$

* Worst case robust least squares: minimize supremum of least squares across an ellipsoid of $A$ matrices. This can be written as an SDP

* Projection of a point $z$ onto the set ${x:\ Ax=b}$ is a least-squares problem! ([ref](https://math.stackexchange.com/questions/1320363/projection-of-z-onto-the-affine-set-x-mid-ax-b))
	* Solution of $min.\ \frac{1}{2}||x-z||^2$, subject to $Ax=b$. Intuition: orthogonal projection of $z$ onto $Ax=b$ is closest point in affine set
	* KKT system is a necessary (since constraints are linear) and sufficient (since this is a convex problem) condition:
		* $Ax=b$
		* $x-z+A^T\lambda=0$
	1. Multiply second equation by A: $Ax-Az+A^TA\lambda=0\rightarrow b-Az+A^TA\lambda=0$
	2. Solve for lambda: $\lambda=(A^TA)^{-1}(Az-b)$
	3. Plug into second equation again: $x-z+A^T(A^TA)^{-1}(Az-b)=0 \rightarrow x = z-A^T(A^TA)^{-1}(Az-b)$
