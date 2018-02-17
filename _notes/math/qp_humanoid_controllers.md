---
title:  "Quadratic-program controllers for humanoids"
use_math: true
---

### Motivation

Although detailed formulations differ, most active research has converged to formulating the floating base inverse dynamics as a quadratic programming (QP) problem

### Introduction

_Problem definition_

Atkeson: "All do QP to find acceleration and contact forces. Then compute torques using inverse dynamics."

TODO Dynamics equation: free-floating and joint space configurations (show decoupled)

The problem we are trying to solve fits under the category of [inverse dynamics](https://en.wikipedia.org/wiki/Inverse_dynamics). Wikipedia says: "Inverse rigid-body dynamics is a method for computing forces and/or moments of force (torques) based on the kinematics (motion) of a body and the body's inertial properties (mass and moment of inertia)". Forward dynamics is "given the current state and control input, calculate next state": $FD(x_t,u_t)=x_{t+1}$. Inverse dynamics is roughly "given current state and desired next state, find control input to get us there": $ID(x_t, x_{t+1})=u_t$.

For simple systems, this process can be done in closed-form. Even on humanoids, this was the dominant method before QP methods were introduced (Sentis and other Jacobian-based controllers). QPs are popular now because they provide more expressiveness, flexibility, robustness (constraints, cost functions, etc).

We have

* A dynamics model of the robot (approximate, with some assumptions)
* High-level goals for the robot, defined as the motion of some links
* Some constraints on what the robot can physically do, based on dynamics model and contact points

We just encode all of these things into a quadratic program, and out pops the control

### Main components

Costs and constraints - how they are formulated

#### Tasks

compute desired acceleration using PD control law

#### Dealing with contacts

_No-slip conditions_

We want to encode the reality that a robot link that is in contact with the ground will not slip with respect to it (note that "ground" could be replaced with any other static thing in the environment, e.g. stair rails, ladder).

We use the contact Jacobian $J_\text{contact}$, which maps the robot's joint velocities to end effector velocities $\dot{p}_c=J_c\dot{q}$. We want everything to be formulated in terms of $\ddot{q}$ (or $\dot{v}$ in some papers, which is more correct because angular velocity is not equal to the time derivative of angle), so that all of the terms (in constraints and costs) can be linear in the decision variables. So we take the time derivative of $\dot{p}_c$, and using the product rule for derivatives we get $\ddot{p}_c=J_c\ddot{q}+\dot{J}_c\dot{q}$. Most of the derivations of no-slip condition constraints/costs are based on this (except MIT's slack one, which is looser).

_Friction cone constraints_

All contacts that a humanoid encounters while walking are unilateral contacts. This means that, as opposed to bilateral contacts (e.g. gripping with hands), forces can only be transmitted in one direction.

To model the forces that can occur at the contact points, we use a friction cone. Linearized friction cone so that it fits in QP formulation. SOCP can handle full friction cone but it's too slow. Friction cone model itself is an approximation of the real world anyways so it's ok.

Note that contact between planar surfaces is approximated as point contacts at four corners.

### Implementation details / variations

_hard vs soft constraints_

* One perspective: things that can't be compromised go into constraints (dynamics equations, contact points), everything else can go in objectives  

- trading off physical reality vs computational feasibility
	- it's all an approximation anyways!
- e.g. contact constraints
	- hard constraint - set contact point acceleration to zero (Karim, Abe, most others)
	- soft constraint
		- allow bounded slack in equality constraint (MIT)
		- high-weight cost on acceleration (Drake)

"high weight soft penalties is much more forgiving when given conflicting desired motions and numerically more stable"

_hard vs soft prioritization_

approaches typically ensure low priority objectives are within the null space of higher priority ones.

* Lasa et al use prioritized optimization because "Although weighted optimization is sufficient for control with a small set of objectives, it can behave poorly when many objectives are enabled/disabled at runtime." This may be something to consider if the multiobjective QP doesn't work well. Authors claim that prioritization leads to less tuning of weights.
	* Disadvantages of prioritized optimization vs weighted
		* Slower and more work to implement – each priority level requires solving both a QP and a SVD  
		* For some tasks (ex. Locomotion), prioritized opt requires double-precision arithmetic instead of single-precision to prevent rank-deficient (to machine precision) Jacobians.  
		* Reaching example - there's no soft compromise between balance and end effector tasks

_other_

* open-source implementations: `qp_inverse_dynamics` in Drake
	- Tasks from JRL
	- OpenSOT from IIT
	- anywhere else?

- solvers that deal with ill-conditioned matrices well - gurobi
- taking torque out of decision variables

* structural change every time a contact changes - see Siyuan feng thesis

* Posture task ensures that Jacobian of tasks, and thus Q matrix, is full rank/well-conditioned
	* e.g. neck joint may be all zeros in Jacobian for end effector task

### Papers

TODO include comments on each one

* Originals: de Lasa, Abe
* QP robot control: Karim, MIT, WPI-CMU, Schaal and Rigetti, ...
* Extensions: MIT's fast active set solver, multi-robot QP

* <http://www.cs.cmu.edu/~sfeng/s_feng_robotics_2016.pdf>

### Multi-robot controller  

* In this framework everything is modeled as a robot, including manipulated objects (doors, floating-base rigid body objects)  
* Dimensionality increases significantly when an entire human is introduced. Multi-robot QPs are sparse (contact pairs) -> use QP solver that exploits this. MIT used SNOPT (mentioned as future work in one of Karim's papers)

### Other references

* Stephen Boyd's course on Convex Optimization - awesome resource for learning all about convex optimization, although definitely overkill for just understanding QP controllers
* [Comparision of Atlas controllers at DRC](http://www.cs.cmu.edu/~cga/drc/atlas-control/)

### Random 

_Continuity_

We make the implicit assumption in this formulation that accelerations and contact forces can change instantaneously. This has proven to be an approximation that is good enough for this purpose, but it's definitely not true in real life. At the lowest level, contact forces are caused by repulsion forces between atoms, which is a continuous quantity. This means that all derivatives of position can't be discontinuous. However, the window in which contact forces "ramp up" is so small that we perceive/approximate contact to be a discrete event. Since accelerations depend on forces by Newton's law (and torques by some electrical law), accelerations can't change instantaneously either. Note: most physics simulators use some kind of "soft" contact simulation
