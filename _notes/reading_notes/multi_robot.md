Features

* Centralized vs decentralized
* Global optimum vs local optimum (of cost function)
    * This amounts to fast vs slow

* [Review of optimization-based methods](http://multirobotsystems.org/sites/default/files/slides/2015_RSS_MRS_Alonso-Mora.pdf)
* Optimization method
    * Convex continuous - fast, guaranteed
    * Non-convex continuous - fast but local (no guarantees)
    * Non-convex with binary variables - Slow but will find global optimum
* Task assignment - usually becomes a traveling salesman problem
    * [Assignment problem](https://en.wikipedia.org/wiki/Assignment_problem) - maximum weight matching in a bipartite graph
    * ex. [Hungarian algorithm](https://en.wikipedia.org/wiki/Hungarian_algorithm)
* Other categories: swarm (purely local planners), sampling-based

* Augugliaro, Federico, Angela P. Schoellig, and Raffaello D'Andrea. "Generation of collision-free trajectories for a quadrocopter fleet: A sequential convex programming approach." 2012
    * Fairly simple algorithm
    * Represent trajectory as point mass with position and velocity, and place constraints on position, acceleration (thrust), jerk (continuity)
        * Assume there's a controller than can track this - this is kind of a limitation
    * Collision avoidance constraint is encoded as minimum L2 distance between two points, which is non-convex. Use Sequential Convex Programming to solve this (i.e. just linearize the non-convex constraint at each timestep). In R3 this probably isn't a bad approximation at all
        * With this linear approximation, it's a QP
        * Note: SCP is a heuristic which may fail - it also depends heavily on initial guess
        * Affine is the closest convex approximation of a concave function, and simplest approximation for any other non-convex function)
    * SCP procedure:
        * Solve without non-convex collision constraint. Use this as initialization.
        * Formulate local QP around current trajectory with linear approximation of collision constraint
        * Check stopping conditions - non-convex collision constraints, convergence
    * Fast - finds feasible trajectories in average of 0.31s. [Video](https://www.youtube.com/watch?v=wwK7WvvUvlI&feature=youtu.be)

* Mixed-Integer planning
    * Obstacle constraint - define robots and obstacles as convex polyhedrons, defined by hyperplanes. For a robot (point? or hyperplane) to not be hitting anything, at least one of the hyperplane inequalities must be satisfied. (If all of the inequalities are broken, that means point is inside polyhedron)
    * Optimal, but computationally expensive

* [dRRT*](https://arxiv.org/pdf/1706.09932.pdf)
    * Similar to RRT* - after a node is added, search neighborhood and rewire if lower-cost paths are found
        * Find lowest-cost connection to q_new, add that vertex (and cost)
        * Then, look at cost of going to all neighbors of q_new thru q_new and if the cost is lower than the neighbor's current cost, remove neighbor's current parent and draw vertex from q_new to neighbor
        * Each node keeps a running cost
        * Closest node to q_rand may not have the best running cost
    * dRRT: Searches an implicit tensor product roadmap of roadmaps constructed for each robot individually

* "A General Framework for Multi-vehicle Cooperative Localization Using Pose Graph" Daniela Rus and others
    * Vehicles broadcast new nodes and vertices in the graph. Includes individual robot poses, plus relative observations
        * All vehicles build their own pose graph? Or is there a global graph
    * To be robust to communication failures, they send timestamps and poses relative to an agreed-upon origin rather than relative measurements. Then they use factor decomposition to find the relative measurement
    * "Cooperative Localization by Factor Composition over a Faulty Low-Bandwidth Communication Channel"
        * Method for robustly sharing "local chains" of factor graphs to other robots
        * Basic idea seems to be that constraints in pose graphs are just linear compositions of transformations, and thus they can be composed/decomposed. So if there's a link from 1 to 2 to 3, this can be approximated with 1 to 3

* "Distributed Optimization with Pairwise Constraints and Its Application to Multi-robot Path Planning" Subhrajit Bhattacharya, Vijay Kumar, Max Likhachev
    * Separable optimization problems with linear constraints are well-studied; "In the present work we investigate a distributed implementation of a separable optimization problem with non-linear [maybe non-convex] constraints arising from coupling between pairs of robots."
        * any constraint in the form of $f_{i,j}(x_i, x_j)=0$
    * Their method tldr: solve the global unconstrained problem, which is completely decoupled. Then gradually turn up the penalty weights for constraints, which are modeled as soft constraints.
        * When one robot is planning, it assumes other robots are executing their most recent plans


* General methods in multi-robot graph SLAM
    * graph sparsification - refactor subgraph into set of smaller number of poses

---

* Quadrotors are _differentially flat_ with respect to $x,y,z$ of CoM, and yaw angle. (shown in Mellinger and Kumar 2011). This means that the entire state of the system can be expressed as a function of the instantaneous value of $\{x,y,z,\psi\}$, as long as the trajectories are sufficiently smooth

* "Efficient Mixed-Integer Planning for UAVs in Cluttered Environments" Robin Deits and Russ Tedrake
    * Really well-written paper!
    * Key contribution: In other works, obstacles are represented by hyperplanes defining polyhedron. This scales poorly because we need to have lots of faces for each obstacle. Instead, they split up the collision-free region into convex sets, and have one binary variable per set.
        * "encode the assignment of each polynomial piece of the trajectory to a safe region using a matrix of binary integer variables" - linear constraint on H so that sum of each row is 1
    * Cool part - encode "inside convex polytope" constraint as a linear inequality on polynomial, which can then be written as sums-of-squares constraints
        * For low-degree polynomials, this can be written as Mixed Integer Second Order Cone Program
    * "We define a trajectory as a piecewise polynomial function in time with vector-valued coefficients, mapping time to position in 2D or 3D space.Our optimization problem is a matter of choosing the coefficients of each polynomial in order to ensure that the trajectory reaches a desired goal state, avoids collisions, and satisfies an objective function of our choosing."
    * "The problem of obstacle avoidance is particularly challenging because the set of points outside a closed, bounded obstacle is non-convex."
    * They had numerical difficulties with higher-order polynomials
    * [Big-M procedure](https://yalmip.github.io/tutorial/bigmandconvexhulls/) - convert logic or nonconvex constraints into set of constraints using auxiliary binary variables
        * Basic idea: "y implies x==0" can be written as $(1-y)m\leq x \leq(1-y)M$ with very small $m$ and large $M$. Same reformulation possible for inequalities
        * Rule of thumb: big-M is nearly always an option, but look for something better. big-M often leads to poor relaxations (from [these slides](http://acl.mit.edu/milp/MILP_for_Control.pdf))

* IRIS - MIT's method for segmenting space into convex regions
    * "IRIS alternates between two optimizations. The first optimization is a quadratic program that finds hyperplanes that separate a convex region of space from the obstacles [like SVM]. The second optimization is a semidefinite program that takes the intersection of those hyperplanes, and finds an ellipsoid of maximum volume inscribed inside those hyperplanes [Lowner-John]"
* Mixed integer programs can be used to encode logic (0/1 decisions), approximation of non-convex regions

* Sums-of-squares
    * provides a sufficient condition for a polynomial to be non-negative
    *  [review slides](https://docs.google.com/presentation/d/1ASfjB1TdLJmYxT0b6rnyGh9eLbMc-66bTOt3_3yvc90/edit#slide=id.g1f502a466d_0_5)
    * [notes](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-972-algebraic-techniques-and-semidefinite-optimization-spring-2006/lecture-notes/lecture_10.pdf)
    * Equivalent to the existence of a semidefinite matrix, subject to linear constraints on entries - Semidefinite optimization feasibility problem
