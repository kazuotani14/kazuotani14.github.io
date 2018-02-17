---
title:  "Ellipsoids"
---

### Representations

Ellipsoid representation is: $E=\{x \text{ \| }(x-c)^TS(x-c)\leq 1\}$

Taking SVD of $S$, and since that's symmetric: $E=\{x \text{ \| }(x-c)^TU\Sigma^2 U^T(x-c)\leq 1\}$, where $\Sigma$ is diagonal matrix with elements $\Sigma_{ii}=\sigma_i$

This can be written as forward image of Euclidean unit ball under affine mapping: $E = \{U\Sigma^{-1}v + c \text{ | } ||v||_{2} \leq 1\}$_

Or inverse image of "" :
$E = x \text{ \| } \{\|\|\Sigma U^T(x-c)\|\| \leq 1\}$

If S is inverse of covariance matrix, then this is the confidence ellipsoid of covariance matrix (with confidence "value" 1, which can be changed)

Computationally, it is most efficient to use Cholesky representation of ellipsoid, with parameters $c,L$ (because we can use forward substitution to solve for stuff, instead of taking inverses). See [Algorithms for Ellipsoids](https://tcg.mae.cornell.edu/pubs/Pope_FDA_08.pdf) for more

### Examples

* Parametrization of ellipsoid (sublevel-set of quadratic, backward image of affine function on unit ball < 1, forward image) changes the convexity of a problem.
	* Polyhedron represented as vertices - easy to find min volume covering ellipsoid. Represented as inequalities - easy to find max volume ellipsoid inside.
		* Can switch between two representations, but number of vertices are exponential in number of inequalities, so switching can be very expensive
			* Are the problems that we deal with in robotics small enough that this doesn't matter?
* Ellipsoids are universal approximators of convex sets!

* Chebyshev center of a polyhedron
	* $P={x\|a_i^Tx\leq b_i, i=1,...,m}$
	* maximize $r$, subject to: $a_i^Tx_c + r\|\|a_i\|\|_ 2 \leq b_i, i=1,...,m$
	* Linear constraint: $\|\|a_i\|\|_ 2$ is just a property of the polyhedron and not a decision variable
