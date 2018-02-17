---
title:  "Linear Algebra"
use_math: true
---

**TODO**

* Organize: currently lots of redundancy
* Add from written notes on decompositions (minimum-norm proofs)

### References

Much of the notes here are from links below: 

* [fast.ai Numerical Linear Algebra Course](https://github.com/fastai/numerical-linear-algebra)
* [Stanford convex optimization, linear system courses](https://see.stanford.edu/)
* [3Blue1Brown youtube vids](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw)

### Operations

_Outer product_

TODO

### Properties of transpose

* $(A^T)^T = A$
* $(A+B)^T = A^T + B^T$
* $(AB)^T = B^T A^T$
* $(sA)^T = sA^T$
* $(A^{-1})^T=(A^T)^{-1}$

### Properties of Jacobians

* [Cross products](http://www-personal.umich.edu/~riboch/pubfiles/riboch-JacobianofCrossProduct.pdf)
	* $J(\vec{x} \times \vec{y}) = \vec{x} \times J(\vec{y}) - \vec{y} \times J(\vec{x})$
* [Composition](https://unapologetic.wordpress.com/2009/11/12/the-jacobian-of-a-composition/)
	* $h(x) = (g \circ f)(x) = g(f(x))$
	* $J_h = J_gJ_f$
* Addition
	* $f(x)=a(x)=b(x)$ $\rightarrow$ $J_f = J_a + J_b$

### Properties of positive (semi-)definite matrices

* $AA^T$ and $A^TA$ (Gram matrix)
	* symmetric - has a basis of eigenvectors and each eigenvalue is real
	* positive semidefinite, since $x^TAA^Tx = (A^Tx)^T(A^Tx)=\|\|A^Tx\|\|_2^2\geq0$  ._
	* positive definite if $A$ has linearly independent columns
* Every positive definite matrix has positive diagonal elements
* Every positive definite matrix is invertible
	* A square matrix is invertible iff it was no zero eigenvalues
	* If there was a zero eigenvalue, there exists a $x$ such that $Ax=0=0x$, which means $x^TAx=0$, which would contradict the definition of positive definite
* Necessary (but not sufficient) conditions for a "Hermitian matrix" (generalization of symmetric matrix) to be positive definite
	1. $a_{ii}>0$ for all $i$
	2. $a_{ii}+a_{jj}>2\|R(a_{ij})\|$ for $i\neq j$
	3. $det(A)>0$
* Inverse of PSD matrix is PSD
* For symmetric PSD matrix, eigenvalues are equal to singular values. ie. SVD = eigenvalue decomposition

### Complexity of operations

* Solving $Ax=b$ is generally $O(n^3$) (because inversion is $O(n^3)$)
	* But less if $A$ is structured!
		* banded (diagonal, tridiagonal) - $O(n)$
		* triangular - $O(n^2)$ - top or bottom element is just $a_{i,i}=x_i$ and just do forward or backward substitution
		* orthogonal matrices: $A^{-1}=A^T$
		* sparse, ...
* _Vector-vector_ operations
	* inner product: $2n$ flops
	* sum, scalar multiplications: $n$ flops
* _Matrix vector product_, w/ $A\in R^{m\times n}$
	* multiplication: $2mn$ flops
	* $2N$ if $A$ is sparse with $N$ nonzero elements
	* $2p(n+m)$ is A is given as $A=UV^T$, $U\in R^{m\times p}, V\in R^{n\times p}$
		* Only possible if $A$ is low rank
		* This would allow us to store a million x million square matrix
* _Matrix-matrix product_, $mnp$ for matrices of sizes $n\times p, p\times m$. $O(n^3)$ for $n\times n$ matrices
	* less if one or both of the matrices are sparse
	* Algorithms with better theoretical complexity:
		- [Coppersmith-Winnograd](https://en.wikipedia.org/wiki/Coppersmith%E2%80%93Winograd_algorithm) ($O(2.376)$): best big-O but rarely used in practice because constant factors make it slower
		- [Strassen](https://en.wikipedia.org/wiki/Strassen_algorithm) ($O(2.807)$): faster than naive method (in practice) for large matrices
			- Main idea: special method of recursively defining new matrices that only require 7 multiplications instead of 8
		- "However, they are generally not cache aware and ignore locality - meaning that data continually needs to be shunted around in memory, so for most modern architectures the overall algorithm is actually slower than an optimized block matrix multiplication algorithm."
	- Note that complexity can never go under $O(n^2)$, since each element in the matrices need to be touched at least once

* Matrix inversion, simplest method: Gaussian elimination on augmented matrix $[A\|I]$ to get $[I\|A^{-1}]$
	- Gaussian elimination is complexity $O(n^3)$, so inversion is the same

In general, optimized implementations of even simple operations like matrix multiplications can be 10~100x faster than naive implementation

### Solutions to linear systems

[Linear algebra view of linear regression](https://medium.com/@andrew.chamberlain/the-linear-algebra-view-of-least-squares-regression-f67044b7f39b)

#### Normal equation

Recall that we want to find $\hat{x}$ that minimizes:
$$ \big\vert\big\vert Ax - b \big\vert\big\vert_2$$

Another way to think about this is that we are interested in where vector $b$ is closest to the subspace spanned by $A$ (called the *range of* $A$).  This is the projection of $b$ onto $A$.  Since $b - A\hat{x}$ must be perpendicular to the subspace spanned by $A$, we see that

$$A^T (b - A\hat{x}) = 0 $$

(we are using $A^T$ because we want to multiply each column of $A$ by $b - A\hat{x}$

Note: $b-A\hat{x}$ is vector between $b$ and $Ax$, which is perpendicular to A.

This leads us to the *normal equations*:
$$ x = (A^TA)^{-1}A^T b $$

#### Underdetermined systems

* Underdetermined equations: $p\times n$ matrices where $p < n$.
	* Less data points than variables
	* Solutions are in the form $x = Fz + x_0$, where columns of $F$ form a basis for nullspace of $A$. QR factorization is the most common method for solving

$Ax=b$ where $A \in R^{m \times n}, m < n$. i.e. $A$ is "fat"

Infinite number of solutions in nullspace of $A$. Usually want least-squares solution that minimizes $\|\|x\|\|$. One solution uses the right-inverse $x_{LN} = A^T(AA^T)^{-1}b$. ($AA^T$ is invertible since A is full rank).

[_Proof_](https://see.stanford.edu/materials/lsoeldsee263/08-min-norm.pdf)

1. Let $x$ be any solution other than $x_{LN}$. $Ax=b\text{, so } (Ax-Ax_{LN})=(b-b)=0$.
2. Take the projection of difference between this solution and the least-norm solution, onto the least-norm solution.

$$(x- x_{LN})^T x_{LN}=(x-x_{LN})^TA^T(AA^T)^{-1}b
$$=(A(x-x_{LN}))^T(AA^T)^{-1}b=0$$
$$(x-x_{LN})\bot x_{LN}$$

3. Then, taking the norm of the solution $x$,

$$\|\|x\|\|^2 = \|\|x_{LN} + x - x_{LN}\|\|^2$$
$$=\|\|x_{LN}\|\|^2 + \|\|x-x_{LN}\|\|^2 \geq \|\|x_{LN}\|\|^2$$

i.e. $x_{LN}$ has the smallest norm of any solution, $x_{LN} \bot N(A)$ ($x_{LN}$ is projection of 0 on solution set)

$I-A^T(AA^T)^{-1}A$ gives projection onto $N(A)$

### Matrix decompositions

Why use decompositions? It makes operations faster

* Matrix Inversion: $2n^3$
* Matrix Multiplication: $n^3$
* Cholesky: $\frac{1}{3}n^3$
* QR, Gram Schmidt: $2mn^2$, $m\geq n$ (chapter 8 of Trefethen)
* QR, Householder: $2mn^2 - \frac{2}{3}n^3$ (chapter 10 of Trefethen)
* Solving a triangular system: $n^2$

Linear regression via QR has been recommended by numerical analysts as the standard method for years.  It is natural, elegant, and good for "daily use". SVD is most robust

* [eigen linear system solve reference](https://eigen.tuxfamily.org/dox/group__TutorialLinearAlgebra.html)

* Solves for the least-squares solution for under-determined systems
* For symmetric matrices, there is no fundamental difference between SVD and eigenvalue decomposition
* SVD pretty much never fails for linear system solutions
	* only totally safe option for rank-deficient problems. column-pivoted Householder QR decomposition works most of the time but no guarantees
* PCA can use SVD to find "principal components"
	- PCA is to SVD as least-squares is to Newton's method (one is analysis approach, other is numerical method)
	- PCA assumes data has mean subtracted from it

* Randomized SVD: find low-rank approximations of matrices without actually computing the SVD
	* Basic idea is to sample some portions of the range of $A$ to get a low-rank approximation of the range of $A$

* Cost of finding eigenvalues - $n^3$

#### [QR decomposition](https://en.wikipedia.org/wiki/QR_decomposition)


* Decomposes any real square matrix as $A=QR$, where $Q$ is orthonormal matrix, $R$ is upper triangular
	* orthonormal = $Q^TQ=I$. this makes sure that it doesn't change the least-squares solution. $Q$ preserves the Euclidean norm $\|Qv\|_2 = \|v\| _2$
* For rectangular matrices: $A=QR=Q[R_1; 0] = [Q_1, Q_2][R_1 0]=Q_1 R_1$
	* Then $[R; 0]x=Q^Tb$, and we can solve my multiplying $Q^Tb$, then backward substitution for upper triangular $R$. No matrix inverses!
* More numerically stable than direct matrix inverse, because of reduced condition numbers
* $O(mn^2 - n^3/3)$ (vs. $O(mn^2)$ for pseudo-inverse/normal equations using cholesky)

$$ A x = b $$
$$ A = Q R $$
$$ Q R x = b $$
$$ R x = Q^T b $$

* Pivoting: makes matrices rank-revealing and achieve better numerical stability
	* Column pivot: $AP=QR$, $P$ is a permutation matrix
	* Full pivot: $P_1 A P_2=QR$
	* [reference](https://www.irisa.fr/sage/wg-statlin/WORKSHOPS/LEMASSOL05/SLIDES/QR/Guyomarch.pdf)

* Algorithms
	* [Householder method](http://www.cs.princeton.edu/courses/archive/fall11/cos323/notes/cos323_f11_lecture09_svd.pdf)
		* main idea: zero out elements below the diagonal (in what will eventually be $R$) by performing series of orthogonal transformations ("orthogonal triangulation")
			* $Q=H_1...H_n$ (householder matrices)
	* [Gram-Schmidt](http://www.cis.upenn.edu/~cis610/Gram-Schmidt-Bjorck.pdf)
		- main idea: construct Q one column at a time, by projecting a vector $v$ onto each column that has already been filled and subtracting those components off to get the next orthogonal vector

<!-- $\begin{a}{ l | l | c }
\hline
Gram-Schmidt & Triangular\, Orthogonalization & A R_1 R_2 \cdots R_n = Q  \\
Householder  & Orthogonal\, Triangularization & Q_n \cdots Q_2 Q_1 A = R  \\
\hline
\end{array}$ -->

Gram-Schmidt gets $R$ (triangular) as a result of constructing $Q$ (orthogonal) using a succession of triangular operations, while Householder gets $Q$ (orthogonal) as a result of constructing $R$ (triangular) through orthogonal operations.

Householder reflections lead to a more nearly orthogonal matrix Q with rounding errors

Gram-Schmidt can be stopped part-way, leaving a reduced QR of 1st n columns of A

_Householder algorithm_

Householder matrices are orthogonal matrices (they are reflections) that are convenient for introducing zeros into a matrix, in the same way that Gauss transformations (used in LU) are.

A Householder matrix is defined by a nonzero vector $u$, and it's a reflection along the $u$ direction.

$$H = I - 2 \frac{u u^T}{\|u\|^2}$$

Find sequence of Householder reflections (orthogonal matrices) that can turn $A$ into an upper-triangular matrix $R$:
$$Q_n \cdots Q_2 Q_1 A = R$$
$$(AB)^T=B^TA^T \rightarrow A = Q_1Q_2 \cdots Q_n R$$
$$Q = Q_1Q_2 \cdots Q_n$$

#### [Singular Value Decomposition](http://www.cs.princeton.edu/courses/archive/fall11/cos323/notes/cos323_f11_lecture09_svd.pdf)

* $A=U \Sigma V^T = \sum_{i=1}^{p}\sigma_i u_i v_i^T$
	* $U,V$ orthonormal matrices, $\Sigma=\text{Diag}\{\sigma_1,\sigma_2, ..., \sigma_p \}$
	* $\sigma_1 \geq \sigma_2 \geq ... \sigma_p \geq 0$
* $O(min(mn^2,m^2n))$
* If $A$ is not full-rank
	* $p-r$ singular values are zero
	* Left singular vectors ($U$) form orthonormal basis for range of $A$ (eigenvectors of $AA^T$)
	* Right singular vectors ($V$) form orthonormal basis for null-space of $A$
	* Can also make a low-rank approximation by taking SVD, making bottom $n$ singular values zero, then reconstructing. This gives a compressed approximate representation
* Matrix pseudo-inverse using SVD: $A^{-1}=V\Sigma^{+} U^T$, where $\Sigma^{+}$ is formed by replacing every non-zero diagonal entry by its reciprocal $1/\sigma_i$

$$ A x = b $$
$$ A = U \Sigma V^T $$
$$ \Sigma V^T x = U^T b $$
$$ \Sigma w = U^T b $$
$$ x = V w $$

We usually talk about SVD in terms of matrices, $$A = U \Sigma V^T$$ but we can also think about it in terms of vectors.  SVD gives us sets of orthonormal vectors ${v_j}$ and ${u_j}$ such that $$ A v_j = \sigma_j u_j $$. Note that this is very similar to the definition of eigen values/vectors, except $v_j$ doesn't have to be the same as $u_j$. $\sigma_j$ are scalars, called singular values

**Relationship between SVD and Eigen Decomposition**: the left-singular vectors of A are the eigenvectors of $AA^T$. The right-singular vectors of A are the eigenvectors of $A^T A$. The non-zero singular values of A are the square roots of the eigenvalues of $A^T A$ (and $A A^T$).

SVD is a generalization of eigen decomposition. Not all matrices have eigen values, but ALL matrices have singular values.

Note: By the definition of eigen vectors/values $Av = \lambda v$, it only makes sense for square matrices to have eigen decompositions

**Vocab**: A **Hermitian** matrix is one that is equal to it's own conjugate transpose.  In the case of real-valued matrices (which is all we are considering in this course), **Hermitian** means the same as **Symmetric**.

Eigen decomposition can be derived from a fundamental property of eigenvectors:
$$Av=\lambda v \rightarrow AQ=Q\Lambda \rightarrow A = Q\Lambda Q^{-1}$$

**Relevant Theorems:**

- If A is symmetric, then eigenvalues of A are real and $A = Q \Lambda Q^T$
	- Note that the general case of eigen decomposition is: Let A be a square (N×N) matrix with N linearly independent eigenvectors, then A can be factorized as $A = Q\Lambda Q^{-1}$. For symmetric (Hermitian) matrices, $Q$ is orthonormal
	- SVD bases are orthonormal, but eigenvectors are generally not
- If A is triangular, then its eigenvalues are equal to its diagonal entries

Applications:

* semantic analysis
* collaborative filtering/recommendations (winning entry for Netflix Prize)
* calculate Moore-Penrose pseudoinverse
* data compression
* principal component analysis

#### LU factorization

* Every _nonsingular matrix_ A can be factored as $A=PLU$ with $P$ a permutation matrix, $L$ lower triangular, $U$ upper triangular
* Cost for solving $Ax=b$ with this: $(2/3)n^3+2n^2$ flops
* use forward/backward substition to solve in $O(n^2)$ flops

#### Cholesky factorization

* Every _positive definite_ matrix $A$ can be factored as $A=LL^T$, where $L$ (unique) is lower triangular with positive diagonal elements
	* L is called the Cholesky factor of A - can be interpreted as "square root" of a positive definite matrix
	* practical method for testing positive definiteness
	* $(1/3)n^3$ flops for large n

From normal equations: $A^TA x = A^T b$. If $A$ has full rank, the pseudo-inverse $(A^TA)^{-1}A^T$ is a **square, hermitian positive definite** matrix.  The standard way of solving such a system is *Cholesky Factorization*, which finds upper-triangular $R$ s.t. $A^TA = R^TR$.

We can also just solve $Ax=b$ if $A$ is symmetric positive definite:

$$ A^T A x = A^T b $$
$$ R^T R x = A^T b $$
$$ R^T w = A^T b $$
$$ R x = w $$

### [Power method](https://en.wikipedia.org/wiki/Power_iteration)

Iterative method for finding eigenvalues and eigenvectors. Doesn't compute a decomposition, so can be used for very large matrices

Eigenvectors of a matrix are: vectors that, when multiplied by the matrix, don't change direction and only get scaled: $Av=\lambda v$.

Intuition behind the power method is to repeatedly multiply a (originally random) vector, and watch how the vector changes over iterations. The component of the vector that is along the dominant (largest magnitude) eigenvector should be "scaled" the most. To make sure the vector doesn't explode in magnitude, we normalize the vector at each step.

```
y = normalized random vector

for i in range(n_iter):
	y = A*y
	normalize(y)
eigvec = y
eigval = abs((A*y).dot(y) / y.dot(y)) # Rayleigh quotient
```

This can also be used for computing spectral of a matrix, which is equal to the largest singular value (square root of largest magnitude eigenvalue).

To find smaller magnitude eigenvalues, subtract the component of vector change along known eigenvectors at every iteration (not done that often, since this method converges slowly)

- The convergence rate of the power method is the ratio of the largest eigenvalue to the 2nd largest eigenvalue.  It can be speeded up by adding *shifts*.  To find eigenvalues other than the largest, a method called *deflation* can be used.  See Chapter 12.1 of Greenbaum & Chartier for more details.

- *Krylov Subspaces*: In the Power Iteration, notice that we multiply by our matrix A each time, effectively calculating

$$Ab,\,A^2b,\,A^3b,\,A^4b\, \ldots$$

The matrix with those vectors as columns is called the *Krylov matrix*, and the space spanned by those vectors is the *Krylov subspace*.  

### Numerical conditioning

* [Slides on conditioning](http://web.mit.edu/ehliu/Public/Yelp/conditioning_and_precision.pdf): see here for explanation on some of the stuff below, as well as more formal analysis of backward/forward stability

* Machine precision = largest relative error due to round-off (floating-point nunmbers represented by sign bit, exponent, fraction)
* Condition number
	* "how much the output value of the linear function can change for a small change in the input argument. This is used to measure how sensitive a function is to changes or errors in the input, and how much error in the output results from an error in the input"
		* $\kappa(x)=\frac{x\dot{f}(x)}{f(x)}$
		* For matrix: $\kappa(A) = \|\|A\|\|\frac{\|\|x\|\|}{\|\|Ax\|\|} = \|\|A\|\| \|\|A^{-1}\|\| \frac{\sigma_{max}}{\sigma_{min}}$
* Note that using the pseudo-inverse to solve $Ax=b$ squares the condition number! This is very bad
	* $\kappa(A^TA)=\|\|A^TA\|\| \|\|A^{-1}A^{-T}\|\| \leq \|\|A^2\|\| \|\|A^{-1}\|\|^2 = \kappa(A)^2$

* Rule of thumb: If $\kappa(A)=10^k$, you may lose up to $k$ digits of accuracy on top of what would be lost to the numerical method due to loss of precision from arithmetic methods
	* If $A=A_1A_2$, $A^{-1}=A_2^{-1}A_1^{-1}$
	* SVD-based linear system solvers can help in finding and dealing with singular values, by applying a smooth cut-off to them  <https://www.mpp.mpg.de/~schieck/svd.pdf>
		* But, 9 times more expensive
* For ill-conditioned matrices (where the smallest eigenvalue is many times less than the largest, so a nearby matrix is singular) probably need an eigenvalue or singular-value decomposition. In cases like this, tiny numerical errors in the original matrix cause much larger errors in the inverse, so high precision is not achievable with any method.

### Misc

* Matrix norms
	- Spectral norm (L2 norm): largest singular value (square root of largest magnitude eigenvalue)
	- Nuclear norm: sum of singular values
* Expanding least squares: $\frac{1}{2}\|\|Ax-b\|\|_2 ^ 2 = \frac{1}{2}x^T(A^TA)x - (A^Tb)^Tx + b^Tb$
	- Taking derivative: $(A^TA)x-(A^Tb)$
	* The minus here makes more sense than $Ax+b$ because the least-squares problem comes from solving $Ax=b \rightarrow Ax-b=0$
* Moore-Penrose pseudo-inverse: $(A^TA)^{-1}A^T$
* $E(z)=0,E(zz^T)=Z \rightarrow E(z^TPz)=E(Tr(Pzz^T))=E(PZ)$
* Gradient of $logdet(A)$ (with $A$ a matrix) is $A^{-1}$
* (n - #non-zero eigenvalues) = rank
* $A^{-1}$ almost never means $inv(A)$ is actually done.

* Block elimination: exploit structure in block matrix and individual blocks
	* Basically forward/backward elimination on blocks
	* Schur complement often comes up here
* Operations on sparse matrices typically take advantage of decompositions with permutation matrices, ex. $PLL^TP^T, P_1LUP_2^T$

* [Vector space](http://www.math.niu.edu/~beachy/courses/240/06spring/vectorspace.html): collection of objects (vectors) on which two operations (vector addition, scalar multiplication) are defined, and some properties are fulfilled (e.g. commutative, associative, closure - vector in the set is still in set when scaled)
* Software
	* BLAS: linear algebra operations
	* LAPACK: higher-level linear algebra operation (e.g. matrix decompositions) using BLAS, heavily optimized for hardware
* Two matrices $A$ and $B$ are **similar** if there exists a non-singular matrix $X$ such that $$B = X^{-1}AX$$ **Theorem**: If $X$ is non-singular, then $A$ and $X^{-1}AX$ have the same eigenvalues.
* A **Schur factorization** of a matrix $A$ is a factorization:
$$ A = Q T Q^* $$
where $Q$ is unitary (for reals, orthonormal) and $T$ is upper-triangular.
**Theorem:** Every square matrix has a Schur factorization.

* Frobenius norm (Euclidean norm) of matrix: $\|A\|\_F=\sqrt{\sum_{i=1}^{m}\sum_{j=1}^{n}\|a_{ij}\|^2}$


* Schur complement: for symmetric matrix partitioned $X=\begin{bmatrix}A&B;\\ B^T&C\end{bmatrix}\rightarrow S=C-B^TA^{-1}B$. Comes up in lots of contexts
	* Solving linear equations
	* $X>0$ iff $A>0$ and $S>0$
	* If $A>0$, then $X\geq 0$ iff $S\geq 0$
	* [ref1](https://www.quora.com/Linear-Algebra-What-is-the-motivation-behind-defining-the-Schur-complement-of-a-matrix)
