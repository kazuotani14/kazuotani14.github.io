# iLQR / DDP

* Efficient, linear complexity in degrees of freedom, gives you both control policy around nominal trajectory as result of optimization

### Algorithm

* For iLQR, undesired states are usually encoded as repulsive "potential fields" in the cost function. We can't do this for the control input limits, which is why the boxQP is necessary.

* Remember the Bellman equation: at each timestep, choose the control input that minimizes the current cost + value function at the next step. This is why the value function from the next step is used in the backward pass

* iLQR constructs a quadratic cost function along the trajectory, and slides down it at each "knot point"

_Pros/cons vs other trajectory optimization algorithms_

* iLQR/DDP searches in space of control trajectories; it solves an $m$-dimensional problem $N$ times instead of solving a single problem of size $mN$ (as a direct method would). So complexity is $O(Nm^3)$ vs. $O(N^3m^3)$.
    * Note that this ignores the cost of calculating the derivatives - this takes up the bulk of the time in iLQR if finite-differences is used. Ideally, we would have analytic derivatives of dynamics model.
* Intermediate results of shooting methods (of which iLQR is one) are always strictly feasible and "dynamics constraints" are unnecessary.
* Direct methods tend to find optima easier than indirect/shooting methods. Shooting methods are much more sensitive to local minima.
* Shooting methods make it easy to use warm-starts.

### MRSD project

Our approach was:

1. iLQR on nonlinear model with smooth (read: differentiable) tire dynamics
    * obstacle costs as repulsive potential fields
    * cost for change in control input to account for actuator delay
2. Server-client nodes, with planner as client and trajectory executer as server that keeps executing previously planned trajectory until a new one is given.
    * This same architecture was used to switch between controllers (clients) with the same interface to the actuators.
3. "Local planner" that did PID on the steering angle to account for errors in heading wrt planned trajectory. This was kind of a hack, but we thought we needed it to stabilize the system at high control rates. Months later, we realized that the correct way to do this was with the local linear feedback controller around each timestep that is provided by iLQR as a result of the trajectory optimization.

### Misc

* [Eigen FAQ](http://eigen.tuxfamily.org/index.php?title=FAQ)

### Links

* [Yuval Tassa's Matlab code](https://www.mathworks.com/matlabcentral/fileexchange/52069-ilqg-ddp-trajectory-optimization)
* [Good explanation of iLQR](https://studywolf.wordpress.com/2016/02/03/the-iterative-linear-quadratic-regulator-method/)
* [DDP-Generator](https://github.com/jgeisler0303/DDP-Generator) - best iLQR implementation I've found. Generates problem-specific code with analytic gradients for dynamics and cost functions.

### References

* Tassa, Yuval, Nicolas Mansard, and Emo Todorov. "Control-limited differential dynamic programming." Robotics and Automation (ICRA), 2014 IEEE International Conference on. IEEE, 2014.
* Li, Weiwei, and Emanuel Todorov. "Iterative linear quadratic regulator design for nonlinear biological movement systems." ICINCO (1). 2004.
