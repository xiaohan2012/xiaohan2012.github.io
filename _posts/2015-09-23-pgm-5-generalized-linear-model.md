---
layout: post
title: "Graphical Model: Exponential Family and Generalized Linear Models"
date: 2015-09-23 20:38:32
categories: course
tags: pgm exponential-family glim newton-raphson mle
---

## Exponential family

### Why do we study it?

- it's a generic form for many ML models
- if we can apply generic parameter estimation method for this generic form, we can apply it to all its instance models

### Definition

For any numerical random variable \\(X\\):

$$
\begin{eqnarray*}
p(x \vert \eta)
&=&
h(x)\exp\{\eta^T T(x) - A(\eta)\} \\
&=&
\frac{1}{Z(x)}h(x)\exp\{\eta^T T(x)\} \\
\end{eqnarray*}
$$

is an exponential family distribution with natural parameter \\(\eta\\)

- \\(T(x)\\): sufficient statistics
- \\(h(x)\\): for anything that is not interacting with the natural parameters(for example in Multivariate Gaussian)
- \\(A(\eta)=\log Z(\eta)\\): log normalizer

### Example

- Fundamental models: Bernoulli, multinomial, Gaussian, Poisson, gamma,
- More complex ones: Ising model(MRS), RBM, CRF

Benefit of CRF:

- \\(\mathbf{X}\\) is assumed to be inter-dependent
- we can use global features on \\(\mathbf{X}\\)

More examples:

- Multivariate Gaussian: moment parameters, \\(\sigma\\) and \\(\mu\\)
- Multinomial distribution: \\(\prod\limits_i^K \pi_i^{x_i} = \exp [\sum\limits_i x_i \ln \phi_i] \\)

### Properties

- **Moment generating property**: \\(\frac{dA}{d\eta} = E[T(x)] = \mu \\) and \\( \frac{d^2A}{d\eta^2} = Var[T(x)] > 0 \\)
- **one-to-one correspondence between \\(\mu\\) and \\(\eta\\)**: convexity of \\(A(\eta)\\)  => invertibily of \\(\frac{dA}{d\eta} = E[T(x)] = \mu \\) => \\(\eta = \Psi(\mu)\\)
- **moment matching**: set \\(\frac{\partial \mathcal{l}}{\partial \eta} = 0\\) leads to $$ \mu_{MLE} = \frac{1}{N} \sum\limits_n T(X_n) $$ => $$ \eta_{MLE} = \Psi(\mu_{MLE}) $$

Moment estimation leads to parameter estimation


- **Q**: why do we need such moments?
   Moment is easily defined for exponential family and once we know the moment as well as the inverse function(from moment to parameter), we can get the parameter value quite easily
- **Q**: how is the inverse function, \\(\Psi\\) defined?

### Sufficiency

Bayesian/Frequentist/Neyman View

**Q**: why this views matter?

### Exponential family and Bayesian

Exponential family for both the model and prior

$$ P(X \vert \eta) = \exp \{\eta^T T(X) - A(\eta)\}$$

$$ P( \eta) = \exp \{\xi^T T(\eta) - A(\xi)\}$$

Then the posterior:

$$ P(\eta \vert X) = \exp \{\eta^T T(X) + \xi^T T(\eta) + A^{'} + A^{''}\}$$

If \\(\eta = T(\eta)\\), then

$$ P(\eta \vert X) = \exp \{T(\eta)(T(X) + \xi) + A^{'} + A^{''}\} $$

In this case, posterior is also exponential family.

Finally, if \\( \eta = T(\eta)\\),  \\(P(\eta)\\) is a conjugate prior for exponential family distribution.

## Generalized Linear Models(GLIMs)

### Why do we study it?

It is a general form for regression and classification models.

### Definition

![GLIMs framework](/assets/images/pgm/glims-framework.png)

- \\(Y \sim exponential-Family\\)
- \\(\eta = \Psi(\mu = f(\xi = \theta^T X))\\)

GLIMs is a specific form of exponential family.

More:

- Input, \\(X\\) enter the model through linear combination: \\(\xi = \theta^T X\\)
- Conditional mean, \\(\mu=f(\xi)\\), where \\(f\\) is response function.
- Output \\(y\\) characterized by exponential family distribution, \\(p\\), with conditional mean \\(\mu\\)

**Canonical response function**: simple case where\\(f = \Psi^{-1}(.)\\). Thus, \\( \eta = \theta^T X\\).

### Examples

- Linear regression: \\( P(Y \vert X, \theta) = N(\theta^T X, \sigma^2) \\), \\(\Psi, f\\) both be identity function.
- Logistic regression: \\(P(Y \vert X, \theta) = \mu(X)^Y (1-\mu(X))^(1-Y)\\), where \\( \mu(X) = sigmoid(X)\\). \\(f\\) be sigmoid and \\(\Psi\\) be identity function.


We can improve the input by transforming \\(X\\), for example, adding non-linearity.

- **Q**: what's the connection between exponential family and GLIM?
  In GLIM, the interaction between parameter and input is **linear**, however for exponential family, there is not such constraint.

## MLE for Canonical GLIM 

Derivative of log-likelihood leads to:

$$ \frac{\mathbf{l}}{\theta} = X^T (y - \mu)$$

Need to solve it iteratively.


### Online learning

One option is SGD, the update rule is:

$$ \theta_{new} = \theta_{old} + \alpha (y_n - \mu_n)x_n $$

\\(\alpha\\) is the learning rate, which needs to be set.

### Batching learning using Iterative Reweighting Least Square(IRLS)

\\( \alpha\\) needs to be set. Hessian can be used to adjust the learning rate.

This gives rise to Newton Raphson method with cost function \\(J\\):

$$ \theta_{new} = \theta_{old} - H^{-1} \nabla_{\theta} J$$

General form of Hessian \\( H\\) for GLIMs:

$$ H = \frac{d^2 \mathbf{l}}{d\theta\theta} = -X^T W X $$

Newton Raphson method for GLIMs is called Iteratively Reweighting Least Squares(IRLS), as it takes the form:

$$ \theta_{new} = (X^TW_{old}X)^{-1}X^TW_{old}z_{old}$$, where $$ z_{old} = X \theta_{old} + W_{old}^{-1}(y-u_{old})$$


which is similar to Least Square case, where \\( \theta^* = (X^TX)^{-1}X^Ty\\).

In IRLS, we iteratively reweight objective function using \\(W\\) and solve $$ \theta^{new} = argmin\limits_{\theta} (z - X\theta_{old})^T W (z - X\theta_{old}) $$.

This method is generic for any exponential family distribution, the difference is \\(W\\).

Example:

- logistic regression
- linear regression: \\(W=1\\)

The take-home stuff is: **generic** MLE for exponential family and GLIMs using IRLS.


- **Q**: For the above, we only talked about natural/canonical response function, which limits the scope. What about other response functions(e.g, sigmoid)?

## Summary

1. Exponential family and MLE amounts to moment matching
2. GLIMs, its canonical version and MLE on it using IRLS 

