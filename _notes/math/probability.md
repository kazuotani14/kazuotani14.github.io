---
title:  "Probability"
use_math: true
---

### Gaussian

#### Why Gaussians?

* <https://stackoverflow.com/questions/12616406/anyone-can-tell-me-why-we-always-use-the-gaussian-distribution-in-machine-learni>
* <http://www.askamathematician.com/2010/02/q-whats-so-special-about-the-gaussian-distribution-a-k-a-a-normal-distribution-or-bell-curve/>

> The idea behind it is central limit theorem, which is a very important theorem in statistics. In its most general form, under some conditions (which include finite variance), it states that averages of random variables independently drawn from independent distributions converge in distribution to the normal, that is, become normally distributed when the number of random variables is sufficiently large. Physical quantities that are expected to be the sum of many independent processes (such as measurement errors) often have distributions that are nearly normal.

> Among all distributions with standard deviation 1 and mean 0, what is the distribution with maximum entropy?" The answer is the Gaussian.

* It makes the math easy!

#### Covariance 

* Covariance matrix: $\Sigma = E[(X-\mu_X)(X-\mu_X)^T]=E(XX^T)-\mu\mu^T$
    * Always positive semi-definite and symmetric
* Standard deviation: $\sigma_x^2 = E[(x-E[x])^2]$

#### Conditional distributions

Given: 
$$\mu_{X,Y} = \begin{bmatrix} \mu_X ; \\ \mu_Y \end{bmatrix}, \Sigma_{X,Y} = \begin{bmatrix} \Sigma_{XX} & \Sigma_{XY} ; \\ \Sigma_{YX} & \Sigma_{YY} \end{bmatrix}$$

TODO fill from here: https://en.wikipedia.org/wiki/Covariance_matrix#Block_matrices

#### Covariance vs correlation

$$\text{covariance} = \sigma_{XY} = E[(X-\mu_X)(Y-\mu_Y)]$$
$$\text{correlation} = \rho_{XY} = E[(X-\mu_X)(Y-\mu_Y)]/(\sigma_X \sigma_Y)$$
$$\sigma_{XY}=\rho_{XY}\sigma_X\sigma_Y$$

Covariance indicates how two variables are related (can be any positive/negative number). 

Correlation indicates whether variables are positively/negatively related, plus the degrees to which they tend to move together. It is unitless (vs. units of $x$ times units of $y$ for covariance), and always takes on a value between -1 and 1. Can be considered the "normalized" version of covariance, and thus is not affected by scale.

##### Mahalanobis distance

* Distance normalized by information matrix / covariance
  - higher covariance in one direction $\rightarrow$ smaller scaling
  - smaller covariance $\rightarrow$ high scaling
  - The more sure we are about a direction, the larger the Euclidean distance is scaled

$D_m(x) = \sqrt{(x-\mu)^T\Sigma^{-1}(x-\mu})$

* $\Sigma$ : covariance, $\Lambda=\Sigma^{-1}$ : information
  - Information = certainty, covariance = uncertainty
  - Information matrix: "how much information we have about each dimension". If we have higher uncertainty in a direction, we get less information from measurements in that direction

#### Sampling from a Gaussian distribution

* Given 1-D Gaussian $y~N(\mu_y, \sigma_y^2)$, generate Gaussian noise $w~N(0,1)$, return $y=\sigma_y w + \mu_y$
* Given multi-variate Gaussian $y~N(\mu_y, \Sigma_y)$, Factor $\Sigma_y = LL^T$, generate Gaussian noise $w~N(0,I)$, return $y=Lw+\mu_y$
    * See Gaussian Processes notebook for details/demo for why this works

#### References 

* <https://en.wikipedia.org/wiki/Covariance_and_correlation>
* <https://www.countbayesie.com/blog/2015/2/21/variance-co-variance-and-correlation>
* <https://en.wikipedia.org/wiki/Covariance_matrix>

### Covariance propagation

Given: $x~\mu_x, \Sigma_xx$, where $\mu_x=E(x), \Sigma_{xx}=E[(x-E[x])(x-E[x])^T], y=Ax+b$

Want: $\mu_y, \Sigma_{yy} = E[(y-E[y])(y-E[y])^T]$

#### Properties of expectation

$\mu_x = E[x] = \int_{-\infty}^{\infty} x p(x)dx$

* $E[\alpha] = \alpha$
* $E[\alpha x] = \alpha E[x]$
* $E[\alpha + x] = \alpha + E[x]$
* $E[x+y] = E[x]+E[y]$

#### Derivation

$\mu_y = A \mu_x + b$

$E[Ax+b] = A E[x]+b$, using properties of expectations

$(Ax+b) - E(Ax+b) = Ax+b - A E[x]-b = A(x-E[x])$

$\Sigma_{yy} = E\left[(A(x-E[x])) (A(x-E[x]))^T  \right]$

$= E\left(A (x-E[x])(x-E[x])^T A \right)$

$= A \Sigma_{xx} A^T $

### Probability distributions in high-dimensions

Gaussian distributions in high dimensions end up being a "shell" around zero with radius of square root of the number of dimensions: $\sigma \sqrt{d}$. (Uniform distribution has a similar property, with smaller radius.)

The probability density is still highest near the mean, but the rest of the distribution is mostly in this shell. This comes from the definition of the Gaussian.

[Proof](https://www.cs.cmu.edu/~venkatg/teaching/CStheory-infoage/chap1-high-dim-space.pdf), [Some more informal analysis](https://ontopo.wordpress.com/2009/03/10/reasoning-in-higher-dimensions-measure/), [another](http://www.cs.columbia.edu/~djhsu/coms4772-f16/lectures/gaussians.md.handout.pdf)

Matlab code:

```
% Testing properties of random distributions in high-dimensional spaces

n_samples = 10000;
n_dims = 50000;

% The norm of a high-dimensional Gaussian is on average the square root of
% the number of dimensions
gaussian_norms = zeros(n_samples,1);
for i = 1:n_samples
    v = randn(n_dims, 1);
    gaussian_norms(i) = norm(v);
end
figure(1);
hist(gaussian_norms, 30);

uniform_norms = zeros(n_samples,1);
for i = 1:n_samples
    v = rand(n_dims, 1)*2 - 1; % shift (0,1) to (-1,1)
    uniform_norms(i) = norm(v);
end
figure(2);
hist(uniform_norms, 30);

disp(['sqrt(n_dims): ', sqrt(n_dims)]);

```

### Information theory

* Entropy of a variable $X$, with discrete options and probabilities $x, p(x)$: $H(X) = - \log \sum_i p(x_i)\log p(x_i)$
* Maximum entropy when all $p(x_i)$ equal; i.e. uniform distribution
* Mutual information between $X$ and $Y$, $I(X;Y) = H(X,Y) - (H(X)+H(Y))$
  * Positive if and only if the probability of at least some joint events $P(x,y)$ does not equal the product of the individual probabilities $P(x)P(y)$
  * $H(X|Y) = H(X,Y)-H(Y)$

### Misc 

#### Importance sampling

Used to find the expected value of a target distribution $p(x)$ with samples from a sampling distribution $q(x)$. Account for difference in probability distributions by weighting samples by ratio $\frac{p(x)}{q(x)}$. 

Intuition: as a number (or range of numbers) is sampled more often, the average (expected value) moves toward that range. 
