---
title: Logistic regression
date: 2026-09-28
tags:
  - math
  - machine learning
  - optimization theory
  - Logistic regression
  - Softmax
---

# Logistic regression

Logistic regression models class probabilities using a linear score. Binary classification uses a sigmoid; multiclass classification uses softmax. In both cases, maximum likelihood leads to a cross-entropy objective.

Vectors are column vectors, $\log$ denotes the natural logarithm, and losses are summed over observations unless stated otherwise.

## 1. The binary model

Given observations

$$
\mathcal D=\{(x_i,y_i)\}_{i=1}^N,
\qquad x_i\in\mathbb R^d,
\qquad y_i\in\{0,1\},
$$

define the score

$$
z_i=w^\top x_i+b=\theta^\top\widetilde x_i,
\qquad
\widetilde x_i=\begin{bmatrix}1\\x_i\end{bmatrix},
\qquad
\theta=\begin{bmatrix}b\\w\end{bmatrix}.
$$

Here, $N$ is the number of observations, $d$ the number of features, $w$ the weight vector, and $b$ the intercept. The score can take any real value; it is not yet a probability.

### Odds, logit, and sigmoid

For $p\in(0,1)$, the odds and logit are

$$
o=\frac{p}{1-p},
\qquad
\operatorname{logit}(p)=\log\frac{p}{1-p}.
$$

For example, $p=0.8$ gives odds of $4$: the event is four times as likely as its complement. Conversely, $p=o/(1+o)$.

| Probability | Odds | Logit |
| --- | --- | --- |
| $0.2$ | $1/4$ | $-\log4$ |
| $0.5$ | $1$ | $0$ |
| $0.8$ | $4$ | $\log4$ |

The logit ranges over all of $\mathbb R$. Logistic regression assumes that it is linear in the features:

$$
\log\frac{p_i}{1-p_i}=z_i.
$$

Solving for $p_i$ gives

$$
\frac{p_i}{1-p_i}=e^{z_i}
\quad\Longrightarrow\quad
p_i=\Pr(y_i=1\mid x_i;\theta)
=\frac{1}{1+e^{-z_i}}=\sigma(z_i).
$$

The sigmoid is the inverse of the logit. Its useful identities are

$$
1-\sigma(z)=\sigma(-z),
\qquad
\sigma'(z)=\sigma(z)(1-\sigma(z))>0.
$$

$y_i$ is the observed label, $z_i$ the model score, and $p_i$ the predicted probability. Training fits the probabilities to the labels; it does not try to make $z_i=y_i$.

### Prediction and coefficient interpretation

At threshold $1/2$,

$$
\widehat y_i=\mathbf1\{p_i\ge1/2\}
=\mathbf1\{w^\top x_i+b\ge0\}.
$$

The indicator is $1$ when its condition holds and $0$ otherwise. Since $\sigma(0)=1/2$, the decision boundary is $w^\top x+b=0$. For instance, $z=2x_1+x_2-4$ gives the line $x_2=4-2x_1$.

For a general threshold $t\in(0,1)$,

$$
p\ge t
\quad\Longleftrightarrow\quad
w^\top x+b\ge\log\frac{t}{1-t}.
$$

The boundary is linear in the given features. With fixed parameters and $w\neq0$, changing the threshold shifts it parallel to itself.

Increasing $x_j$ by one unit, holding other features fixed, increases the log-odds by $w_j$. Hence

$$
o_{\mathrm{new}}=e^{z+w_j}=o\,e^{w_j}.
$$

The odds are multiplied by $e^{w_j}$; the probability does not increase by $w_j$. If $w_j=\log2$ and the original probability is $1/2$, the new odds are $2$ and the new probability is $2/3$.

## 2. Maximum likelihood and cross-entropy

The Bernoulli probability mass function is

$$
\Pr(y_i\mid x_i;\theta)=p_i^{y_i}(1-p_i)^{1-y_i}.
$$

This selects $p_i$ when $y_i=1$ and $1-p_i$ when $y_i=0$. Assuming conditional independence of the labels given the inputs and parameters,

$$
\mathcal L(\theta)=\prod_{i=1}^N p_i^{y_i}(1-p_i)^{1-y_i}.
$$

The data are fixed while $\theta$ varies. A higher likelihood means a higher joint probability assigned to the observed labels; it is not a probability distribution over $\theta$.

For labels $(1,0,1)$, predictions $(0.9,0.2,0.8)$ give likelihood $0.9(1-0.2)0.8=0.576$, whereas $(0.6,0.4,0.5)$ give $0.18$.

Taking logarithms converts the product into a sum. Since the logarithm is increasing, maximizing likelihood is equivalent to minimizing negative log-likelihood:

$$
J(\theta)=-\log\mathcal L(\theta)
=-\sum_{i=1}^N\left[y_i\log p_i+(1-y_i)\log(1-p_i)\right].
$$

This is binary cross-entropy. For one observation,

$$
\ell_i=
\begin{cases}
-\log p_i,&y_i=1,\\
-\log(1-p_i),&y_i=0.
\end{cases}
$$

If $y_i=1$, predictions of $0.99$, $0.5$, and $0.01$ incur losses of approximately $0.0101$, $0.6931$, and $4.6052$. Assigning little probability to the observed class incurs a large loss.

Using $\log p_i=z_i-\log(1+e^{z_i})$ and $\log(1-p_i)=-\log(1+e^{z_i})$, the same loss becomes

$$
\ell_i=\log(1+e^{z_i})-y_i z_i
=\log(1+e^{-z_i})+(1-y_i)z_i.
$$

## 3. Gradient descent

The dependence of the loss on the parameters is $\theta\to z_i\to p_i\to\ell_i$. By the chain rule,

$$
\frac{\partial\ell_i}{\partial p_i}
=-\frac{y_i}{p_i}+\frac{1-y_i}{1-p_i},
\qquad
\frac{\partial p_i}{\partial z_i}=p_i(1-p_i),
$$

$$
\begin{aligned}
\frac{\partial\ell_i}{\partial z_i}
&=\left(-\frac{y_i}{p_i}+\frac{1-y_i}{1-p_i}\right)p_i(1-p_i)\\
&=-y_i(1-p_i)+(1-y_i)p_i\\
&=p_i-y_i.
\end{aligned}
$$

Since $\nabla_\theta z_i=\widetilde x_i$,

$$
\nabla_\theta\ell_i=(p_i-y_i)\widetilde x_i,
\qquad
\nabla_\theta J=\sum_i(p_i-y_i)\widetilde x_i.
$$

Each observation contributes its probability error multiplied by its input vector.

Stacking inputs as rows of $X\in\mathbb R^{N\times(d+1)}$ gives

$$
X=\begin{bmatrix}\widetilde x_1^\top\\\vdots\\\widetilde x_N^\top\end{bmatrix},
\qquad
\boldsymbol z=X\theta,
\qquad
\boldsymbol p=\sigma(X\theta).
$$

The sigmoid acts componentwise. The gradient and update are

$$
\nabla J=X^\top(\boldsymbol p-\boldsymbol y),
$$

$$
\theta^{(t+1)}=\theta^{(t)}
-\eta X^\top(\boldsymbol p^{(t)}-\boldsymbol y).
$$

Here, $t$ is the iteration index and $\eta>0$ is the learning rate. Probabilities are recomputed after each update.

### One update by hand

For $x=2$, $y=1$, and zero initial parameters,

$$
X=\begin{bmatrix}1&2\end{bmatrix},
\quad
\boldsymbol y=\begin{bmatrix}1\end{bmatrix},
\quad
\theta^{(0)}=\begin{bmatrix}0\\0\end{bmatrix}.
$$

The initial score is $0$, so $p^{(0)}=0.5$. Then

$$
\nabla J(\theta^{(0)})
=\begin{bmatrix}1\\2\end{bmatrix}\begin{bmatrix}-0.5\end{bmatrix}
=\begin{bmatrix}-0.5\\-1\end{bmatrix}.
$$

With $\eta=0.1$,

$$
\theta^{(1)}
=\begin{bmatrix}0\\0\end{bmatrix}
-0.1\begin{bmatrix}-0.5\\-1\end{bmatrix}
=\begin{bmatrix}0.05\\0.1\end{bmatrix}.
$$

The new score is $X\theta^{(1)}=0.25$, giving $p^{(1)}\approx0.5622$. The loss decreases from approximately $0.6931$ to $0.5759$.

For the mean loss $\overline J=J/N$, divide the gradient by $N$. Without regularization, the minimizers are unchanged, but a fixed learning rate produces a smaller update.

## 4. Convexity and regularization

### The Hessian

Differentiating the $j$th gradient component with respect to $\theta_k$ gives

$$
\frac{\partial}{\partial\theta_k}
\left[(p_i-y_i)\widetilde x_{ij}\right]
=p_i(1-p_i)\widetilde x_{ij}\widetilde x_{ik}.
$$

Thus,

$$
\nabla^2\ell_i=p_i(1-p_i)\widetilde x_i\widetilde x_i^\top.
$$

The transpose appears because the second derivatives form an outer product. For $\widetilde x=[1,x]^\top$,

$$
\widetilde x\widetilde x^\top
=\begin{bmatrix}1\\x\end{bmatrix}\begin{bmatrix}1&x\end{bmatrix}
=\begin{bmatrix}1&x\\x&x^2\end{bmatrix}.
$$

In contrast, $\widetilde x^\top\widetilde x=1+x^2$ is a scalar. Summing the Hessians over observations,

$$
\nabla^2J=\sum_i p_i(1-p_i)\widetilde x_i\widetilde x_i^\top=X^\top SX,
$$

$$
S=\operatorname{diag}\left(p_1(1-p_1),\ldots,p_N(1-p_N)\right).
$$

For any $v\in\mathbb R^{d+1}$,

$$
\begin{aligned}
v^\top\nabla^2Jv
&=(Xv)^\top S(Xv)\\
&=\sum_i p_i(1-p_i)(\widetilde x_i^\top v)^2\ge0.
\end{aligned}
$$

The Hessian is positive semidefinite, so $J$ is convex. The quadratic form above is the second derivative of $J(\theta+tv)$ at $t=0$: it measures curvature along direction $v$.

For the previous example at initialization,

$$
H=0.25\begin{bmatrix}1&2\\2&4\end{bmatrix},
\qquad
v^\top Hv=0.25(a+2c)^2\ge0
\quad\text{for }v=\begin{bmatrix}a\\c\end{bmatrix}.
$$

### What convexity guarantees

Every local minimum is a global minimum. In particular, if $\nabla J(\theta^*)=0$, the first-order inequality for a convex function gives

$$
J(\theta)\ge J(\theta^*)
+\nabla J(\theta^*)^\top(\theta-\theta^*)
=J(\theta^*)
$$

for every $\theta$.

This rules out suboptimal local minima. It does not guarantee a unique finite minimizer or convergence with an arbitrary learning rate. Minimizing training loss also does not guarantee good performance on new data.

If $X$ has full column rank, then $Xv\neq0$ for every $v\neq0$. Since $p_i(1-p_i)>0$ at finite parameters, the Hessian is positive definite and the loss is strictly convex. A finite minimizer, if it exists, is unique.

Rank deficiency can leave flat directions. If every input equals $2$, the score is $b+2w$. Replacing $(b,w)$ with $(b-2t,w+t)$ leaves every prediction unchanged.

### A finite minimizer need not exist

Consider $(x_1,y_1)=(-1,0)$ and $(x_2,y_2)=(1,1)$, with

$$
X=\begin{bmatrix}1&-1\\1&1\end{bmatrix},
\qquad
\theta=\begin{bmatrix}0\\c\end{bmatrix}.
$$

Although $X$ has full column rank,

$$
J(\theta)=2\log(1+e^{-c})\longrightarrow0
\qquad\text{as }c\to\infty.
$$

No finite parameter vector attains zero loss. On strictly separable data, scaling a separating vector can keep reducing the loss without reaching a finite optimum.

### L2 regularization

Penalizing all parameters, including the intercept, gives

$$
J_\lambda(\theta)=J(\theta)+\frac\lambda2\|\theta\|_2^2,
\qquad\lambda>0,
$$

$$
\nabla J_\lambda=\nabla J+\lambda\theta,
\qquad
\nabla^2J_\lambda=X^\top SX+\lambda I.
$$

The Hessian is positive definite, and the objective tends to infinity as $\|\theta\|_2\to\infty$. Hence there is a unique finite minimizer. If the intercept is unpenalized, this conclusion requires additional conditions.

## 5. Multiclass logistic regression

Suppose there are $K$ mutually exclusive classes. Let $c_i$ be the observed class and use a one-hot vector

$$
y_{ik}=\mathbf1\{c_i=k\},
\qquad \sum_{k=1}^K y_{ik}=1.
$$

For example, class 2 among three classes is represented by $\boldsymbol y_i=[0,1,0]^\top$.

Assign each class a parameter vector and stack them as rows:

$$
W=\begin{bmatrix}\theta_1^\top\\\vdots\\\theta_K^\top\end{bmatrix}
\in\mathbb R^{K\times(d+1)},
\qquad
\boldsymbol z_i=W\widetilde x_i.
$$

### Softmax

Softmax converts the class scores into probabilities:

$$
p_{ik}=\frac{e^{z_{ik}}}{\sum_{j=1}^K e^{z_{ij}}}.
$$

All probabilities are positive and sum to one. For example,

$$
\boldsymbol z_i=\begin{bmatrix}\log2\\0\\0\end{bmatrix}
\quad\Longrightarrow\quad
\boldsymbol p_i=\begin{bmatrix}1/2\\1/4\\1/4\end{bmatrix}.
$$

The predicted class is $\widehat c_i=\operatorname*{arg\,max}_k p_{ik}$, equivalently the class with the largest score.

Softmax couples the probabilities through a shared denominator. Increasing one score while holding the others fixed increases that class's probability and decreases the others. This differs from fitting independent binary classifiers.

For two classes,

$$
p_1=\frac{e^{z_1}}{e^{z_1}+e^{z_2}}=\sigma(z_1-z_2),
$$

recovering the sigmoid model through the score difference.

### Cross-entropy and gradient

The one-hot exponents select the observed-class probability:

$$
\Pr(c_i\mid x_i;W)=\prod_k p_{ik}^{y_{ik}}=p_{i,c_i}.
$$

The negative log-likelihood is

$$
J(W)=-\sum_i\sum_k y_{ik}\log p_{ik}.
$$

For one observation,

$$
\ell_i=-\log p_{i,c_i}
=\log\left(\sum_k e^{z_{ik}}\right)-\boldsymbol y_i^\top\boldsymbol z_i.
$$

Differentiation gives

$$
\frac{\partial\ell_i}{\partial z_{im}}
=\frac{e^{z_{im}}}{\sum_k e^{z_{ik}}}-y_{im}
=p_{im}-y_{im}.
$$

The same result follows from the softmax derivative

$$
\frac{\partial p_{ik}}{\partial z_{im}}=p_{ik}(\delta_{km}-p_{im}),
$$

where $\delta_{km}=1$ if $k=m$ and $0$ otherwise:

$$
\frac{\partial\ell_i}{\partial z_{im}}
=-\sum_k y_{ik}(\delta_{km}-p_{im})
=-y_{im}+p_{im}\sum_k y_{ik}
=p_{im}-y_{im}.
$$

Since $\boldsymbol z_i=W\widetilde x_i$,

$$
\nabla_W\ell_i=(\boldsymbol p_i-\boldsymbol y_i)\widetilde x_i^\top.
$$

This is a $K\times(d+1)$ matrix, matching $W$. For a batch, stack probabilities and labels as rows of $P,Y\in\mathbb R^{N\times K}$:

$$
Z=XW^\top,
\qquad P=\operatorname{softmax}_{\mathrm{row}}(Z),
\qquad \nabla_WJ=(P-Y)^\top X.
$$

For the mean loss, the update becomes

$$
W^{(t+1)}=W^{(t)}-\frac\eta N(P^{(t)}-Y)^\top X.
$$

## 6. A multiclass example

Consider three observations with two features:

| Sample | First feature | Second feature | Class |
| --- | --- | --- | --- |
| 1 | $1$ | $0$ | 1 |
| 2 | $0$ | $1$ | 2 |
| 3 | $1$ | $1$ | 3 |

Use the mean loss without regularization. The input and label matrices are

$$
X=\begin{bmatrix}1&1&0\\1&0&1\\1&1&1\end{bmatrix},
\qquad
Y=\begin{bmatrix}1&0&0\\0&1&0\\0&0&1\end{bmatrix}.
$$

Let the current parameters be

$$
W^{(0)}=\begin{bmatrix}0&\log2&0\\0&0&\log2\\0&0&0\end{bmatrix}.
$$

Each row corresponds to a class. The columns contain the intercept and the two feature coefficients.

### Scores and loss

$$
Z^{(0)}=X(W^{(0)})^\top
=\begin{bmatrix}\log2&0&0\\0&\log2&0\\\log2&\log2&0\end{bmatrix}.
$$

Applying softmax to each row gives

$$
P^{(0)}=\begin{bmatrix}0.5&0.25&0.25\\0.25&0.5&0.25\\0.4&0.4&0.2\end{bmatrix}.
$$

The observed-class probabilities are $0.5$, $0.5$, and $0.2$, so

$$
\overline J(W^{(0)})
=-\frac13(\log0.5+\log0.5+\log0.2)
\approx0.998577.
$$

### Gradient and update

$$
P^{(0)}-Y=\begin{bmatrix}-0.5&0.25&0.25\\0.25&-0.5&0.25\\0.4&0.4&-0.8\end{bmatrix}.
$$

Therefore,

$$
\begin{aligned}
G^{(0)}
&=\frac13(P^{(0)}-Y)^\top X\\
&=\frac13
\begin{bmatrix}-0.5&0.25&0.4\\0.25&-0.5&0.4\\0.25&0.25&-0.8\end{bmatrix}
\begin{bmatrix}1&1&0\\1&0&1\\1&1&1\end{bmatrix}\\
&=\frac1{60}\begin{bmatrix}3&-2&13\\3&13&-2\\-6&-11&-11\end{bmatrix}.
\end{aligned}
$$

For example, the entry in the first row and second column is

$$
\frac13\left[(-0.5)(1)+(0.25)(0)+(0.4)(1)\right]=-\frac1{30}.
$$

This is the derivative with respect to the first feature coefficient of class 1. With $\eta=0.3$,

$$
W^{(1)}=W^{(0)}-0.3G^{(0)}
=\begin{bmatrix}
-0.015&\log2+0.010&-0.065\\
-0.015&-0.065&\log2+0.010\\
0.030&0.055&0.055
\end{bmatrix}.
$$

Recomputing the scores and probabilities gives

$$
Z^{(1)}\approx\begin{bmatrix}
0.688147&-0.080000&0.085000\\
-0.080000&0.688147&0.085000\\
0.623147&0.623147&0.140000
\end{bmatrix},
$$

$$
P^{(1)}\approx\begin{bmatrix}
0.497275&0.230672&0.272053\\
0.230672&0.497275&0.272053\\
0.382140&0.382140&0.235719
\end{bmatrix}.
$$

The new mean loss is

$$
\overline J(W^{(1)})
\approx-\frac13\left(2\log0.497275+\log0.235719\right)
\approx0.947446.
$$

The overall loss decreases, although the first two observations receive slightly lower probabilities for their correct classes. A batch update need not improve every observation individually. The third observation remains misclassified, so a loss decrease need not immediately improve classification accuracy.

## 7. Multiclass convexity and regularization

The Hessian of a single-observation loss with respect to its scores is

$$
C_i=\operatorname{diag}(\boldsymbol p_i)-\boldsymbol p_i\boldsymbol p_i^\top.
$$

For any $a\in\mathbb R^K$, let $\mu=\sum_k p_{ik}a_k$. Then

$$
a^\top C_i a
=\sum_k p_{ik}a_k^2-\mu^2
=\sum_k p_{ik}(a_k-\mu)^2\ge0.
$$

The loss is convex in the scores. Since the scores depend linearly on $W$, it is also convex in $W$.

Softmax is invariant to a common shift:

$$
\frac{e^{z_k+c}}{\sum_j e^{z_j+c}}=\frac{e^{z_k}}{\sum_j e^{z_j}}.
$$

Adding the same vector $a$ to every class parameter adds $a^\top\widetilde x_i$ to all scores for observation $i$, leaving its probabilities unchanged. Thus this unrestricted parameterization does not have a unique unregularized minimizer, even when $X$ has full column rank. A finite minimizer may also fail to exist under separation.

An L2 penalty on all entries of $W$ gives

$$
\overline J_\lambda(W)
=-\frac1N\sum_{i,k}y_{ik}\log p_{ik}
+\frac\lambda2\|W\|_F^2,
$$

$$
\nabla_W\overline J_\lambda
=\frac1N(P-Y)^\top X+\lambda W.
$$

$\|W\|_F^2$ is the sum of squared matrix entries. With $\lambda>0$ and all entries penalized, the objective has a unique finite minimizer. Changing from summed to mean cross-entropy requires rescaling the regularization coefficient to preserve the same relative penalty.

## 8. Numerical evaluation

The identity

$$
\log(1+e^z)=\max(z,0)+\log(1+e^{-|z|})
$$

avoids directly exponentiating a large positive score in binary cross-entropy.

For softmax, subtract the largest score in each row, $m_i=\max_k z_{ik}$:

$$
p_{ik}=\frac{e^{z_{ik}-m_i}}{\sum_j e^{z_{ij}-m_i}}.
$$

This leaves probabilities unchanged and makes every exponent nonpositive. Log probabilities can be evaluated directly as

$$
\log p_{ik}=(z_{ik}-m_i)-\log\left(\sum_j e^{z_{ij}-m_i}\right).
$$

The main matrix formulas, using mean losses without regularization, are:

| Quantity | Binary | Multiclass |
| --- | --- | --- |
| Parameters | $\theta\in\mathbb R^{d+1}$ | $W\in\mathbb R^{K\times(d+1)}$ |
| Scores | $X\theta$ | $XW^\top$ |
| Probabilities | $\sigma(X\theta)$ | $\operatorname{softmax}_{\mathrm{row}}(XW^\top)$ |
| Gradient | $X^\top(\boldsymbol p-\boldsymbol y)/N$ | $(P-Y)^\top X/N$ |

The transpose positions follow from the storage convention: $X$ contains observations as rows, and $W$ contains class parameters as rows.

---

Source: Youngjoon Hong, *Mathematical Foundations of Deep Neural Networks*, Week 3 Monday lecture. Numerical examples and notes on uniqueness, existence, and regularization supplement the lecture material.
