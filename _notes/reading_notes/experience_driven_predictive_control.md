# Experience-driven Predictive Control (EPC)

Vishnu Desaraju's work at CMU in Nathan Michael's lab

##### EPC thesis

_Notes_

* In NPE, how is a certain state matched to a local controller? KKT conditions - active Lagrange multipliers
* Note that optimal controller depends on state, reference, and (sometimes changing?) constraints

* EPC learns error in dynamics model via some online estimator similar to Gaussian Process Regression (but approximate and faster)
* What kind of noise does EPC learn? I guess having two separate EPCs (in position and attitude) makes sense, since some dynamics model noise is a result of environment (position), and some are inherent to the robot (attitude). Wind, motor time constant, prop wash
* Tube MPC contains an ancillary controller that tries to drive the uncertain state to the state mean sequence produced by optimization
* What are come constraints that we would want robust EPC to consider/not violate? velocity, control. don’t hit obstacles (as velocity constraint, or position)?

* EPC conveniently gets around one annoying (i.e. heuristic-driven) part of explicit MPC: making sure all possible areas of possible states are covered
* The polyhedral regions in explicit MPC and EPC only depends on state and control input constraints. Cost function can change, and we can just insert a different Hessian into the closed-form linear equation

_Questions_

* Check way that polyhedrons are defined, since it's a nonlinear model

_Concepts_

* Locally Weighted Projection Regression (68), ISSGPR (69, 82)
    * 83: Gaussian Processes for Machine Learning
    * This work uses these two methods because they take a big advantage of Gaussian Processes (sample/data-efficient), and mitigate the main disadvantage (poor scaling to high-dimensions. is it exponential? )
* L1 adaptive (23), H-infinity
* Tube MPC (71, 78)

Also read: MIQP stuff from Tedrake's lab (how is their problem different? because their dynamics model is hybrid from contacts?)

* Kostas Alexis paper on robust MPC
  * https://www.youtube.com/watch?v=6TkLvK7IR9Q
  * https://github.com/unr-arl/rmpc_mav
* Barrier certificates: http://abarry.org/Barry12.pdf
* http://groups.csail.mit.edu/robotics-center/public_papers/Marcucci17.pdf

_Libraries/solvers they use_

* qpOASES (99)
* NLopt (100)
* Boost odeint (101) for numerical integration
* Armadillo for linear algebra

##### Explicit MPC

Main idea: precompute optimal solutions for polyhedral regions of parameters, so that optimal control just becomes lookup table of linear controllers

* Given a linear dynamics model and convex cost function, optimal policies are piecewise affine
* Convert parametric QP problem to parametric linear complementarity problem using KKT conditions
    - convex class can be solved with linear solve. non-convex class becomes MILP, MIQP (e.g. Tedrake's group stuff)
* Number of regions in explicit MPC grows exponentially with number of inequality constraints (note: not parameters)
    - Reducing number of regions: if neighboring regions have optimal policies that are almost the same (by some definition), then combine them
* Finding all feasible regions: exploit facet-to-facet property. Step $\epsilon$ outside each facet (active constraint?), re-compute
* Search methods for appropriate polyhedral portion (query): linear search, logarithmic search (find hyperplane that separates remaining areas into two)
* Unconstrained MPC == LQR
* Solve problem: $\begin{bmatrix} H & F \\ F^T & Y \end{bmatrix} \begin{bmatrix}x \\ \lambda \end{bmatrix} =$ TODO
    - $H$ is Hessian of cost function, $F$ corresponds to active constraints
* Idea for future work: slightly suboptimal solutions are ok, since cost function weights are set/"tuned" roughly  

_KKT conditions_

* stationarity, primal feasibility, dual feasibility, linear complementarity
* Interpretations
    - Lagrange multipliers define a "normal cone" (normals defined by inequality constraints)
    - gradient is in the normal cone, optimal point must be feasible, normal cone contains only active constraints
- linear solve involves stationarity and primal feasibility
