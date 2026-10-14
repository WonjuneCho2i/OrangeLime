---
title: "Error Decomposition: Approximation, Estimation, and Optimization"
date: 2026-10-14
tags:
  - math
  - machine-learning
  - learning-theory
  - error-decomposition
---

# Introduction

In a recent lecture on the mathematical foundations of deep neural networks, I encountered a deceptively simple question: why does a trained model fail to achieve the best possible prediction error?

There are several plausible answers. Perhaps the model is not expressive enough. Perhaps the training data do not adequately represent the population. Perhaps the optimization algorithm has not found a sufficiently good solution. These explanations sound different, but they can easily become mixed together when we look only at a single training curve.

**Error decomposition** gives these explanations a mathematical form. It separates the limitations of a model class from the uncertainty caused by finite data and the effects of imperfect optimization.

What interested me was that the main calculation is elementary: add and subtract a few carefully chosen quantities. The difficulty lies in understanding what those quantities represent.

> Which model is being compared with which? Which risk is being minimized? And when we write an error as a sum of terms, are we stating an exact identity or only an upper bound?

This article develops the decomposition from those questions. Along the way, we will examine why convergence for a fixed function does not automatically explain learning, how dimension affects sample requirements, and why a smaller surrogate loss need not mean a smaller classification error.

# Population Risk and Empirical Risk

Suppose that input-output pairs are drawn from an unknown probability distribution $P$. Our training sample is

$$
S=\{(X_i,Y_i)\}_{i=1}^{n}\sim P^n,
$$

where the observations are independent and identically distributed.

A predictor $f$ maps an input to a prediction, and a loss function $\ell$ measures the discrepancy between that prediction and the observed output.

The quantity we ultimately care about is the **population risk**:

$$
R(f)
=
\mathbb E_{(X,Y)\sim P}\left[\ell(f(X),Y)\right].
$$

This is the expected loss on a fresh observation from the same distribution. It is a property of the predictor, the loss, and the underlying distribution.

Unfortunately, we do not know $P$. What we can calculate is the **empirical risk**:

$$
\widehat R_S(f)
=
\frac{1}{n}\sum_{i=1}^{n}\ell(f(X_i),Y_i).
$$

Training typically minimizes this observable quantity. The hope is that doing well on the sample also leads to doing well on the population.

That hope is precisely where learning theory begins. Minimizing the empirical risk is an optimization task; explaining why the resulting model has low population risk is a statistical task.

# Four Predictors with Different Roles

Before decomposing an error, we need to identify the predictors involved.

Let $\mathcal F$ be a broad reference class of predictors, and let $\mathcal H$ be the hypothesis class we actually use:

$$
\mathcal H\subseteq\mathcal F.
$$

For example, $\mathcal H$ might consist of neural networks with a particular architecture and a fixed size.

We distinguish four predictors:

$$
\begin{aligned}
f^\star
&\in
\operatorname*{arg\,min}_{f\in\mathcal F}R(f),
\\[4pt]
f_{\mathcal H}^\star
&\in
\operatorname*{arg\,min}_{f\in\mathcal H}R(f),
\\[4pt]
f_S
&\in
\operatorname*{arg\,min}_{f\in\mathcal H}\widehat R_S(f),
\\[4pt]
\widehat f
&=A(S).
\end{aligned}
$$

The first predictor is optimal in the broad reference class. The second is optimal within the chosen hypothesis class. The third minimizes the training loss within that class. The fourth is the predictor actually returned by our learning algorithm.

The third predictor is an **empirical risk minimizer**, or ERM. The fourth may only approximate an ERM because training takes place under a finite computational budget.

For readability, I assume that the risks are finite and the minima used to name these predictors are attained. The bounds written below with infima do not require the existence of a minimizing predictor. I also suppress algorithmic randomness in the notation; random initialization can be included as an additional random variable.

Our main quantity of interest is the **excess risk**:

$$
\mathcal E_{\mathrm{excess}}
=
R(\widehat f)-R(f^\star).
$$

This measures how much worse the learned predictor is than the reference optimum. It does not require the optimal risk itself to be zero.

For instance, when labels contain irreducible noise, even the best predictor may incur positive expected loss. If $\mathcal F$ contains all relevant measurable predictors, its optimal risk is often called the Bayes risk.

# Approximation and Estimation: An Exact Decomposition

First, imagine that we can solve the empirical optimization problem exactly and obtain $f_S$.

Adding and subtracting the best population risk within $\mathcal H$ gives

$$
\begin{aligned}
R(f_S)-R(f^\star)
={}&
\underbrace{
R(f_{\mathcal H}^\star)-R(f^\star)
}_{\mathcal E_{\mathrm{app}}}
\\
&+
\underbrace{
R(f_S)-R(f_{\mathcal H}^\star)
}_{\mathcal E_{\mathrm{est}}}.
\end{aligned}
$$

This is an exact identity.

## Approximation Error

The **approximation error** measures the cost of restricting ourselves to a particular hypothesis class:

$$
\mathcal E_{\mathrm{app}}
=
\inf_{f\in\mathcal H}R(f)
-
\inf_{f\in\mathcal F}R(f)
\ge 0.
$$

Even if we knew the data distribution and had unlimited computation, this gap would remain as long as we kept the same hypothesis class.

If the target relationship is curved but we allow only linear predictors, the best available linear predictor may still be far from the reference optimum. Collecting more observations cannot remove that representational restriction.

For a fixed distribution, loss, and hypothesis class, approximation error does not depend on the particular sample we draw. It reflects the assumptions built into our choice of models.

There is also a simple monotonicity property:

$$
\mathcal H_1\subseteq\mathcal H_2
\quad\Longrightarrow\quad
\mathcal E_{\mathrm{app}}(\mathcal H_2)
\le
\mathcal E_{\mathrm{app}}(\mathcal H_1).
$$

Enlarging a class cannot increase its best achievable population performance gap. However, convergence of this gap to zero requires an appropriate approximation property; increasing the size of a model alone is not a complete argument.

## Estimation Error

The **estimation error** measures the difference between the population performance of an ERM and the best population performance available in its class:

$$
\mathcal E_{\mathrm{est}}
=
R(f_S)-R(f_{\mathcal H}^\star)
\ge 0.
$$

The ERM minimizes an average over the observed sample, whereas the population minimizer minimizes an expectation over the unknown distribution. Those are different objectives.

A predictor may fit accidental patterns in the sample rather than features that persist in the population. Estimation error captures the population cost of choosing a model through finite data.

This leads to the familiar approximation-estimation tradeoff. A richer class can represent more relationships, but selecting a good predictor from it may require more data. This is a statement about the statistical difficulty of the class, not a rule that every larger model must generalize worse on every dataset.

# Bringing Optimization into the Picture

In practice, the learning algorithm returns $\widehat f$, which need not be an exact ERM.

To measure the remaining optimization gap, define

$$
\mathcal E_{\mathrm{opt}}
=
\widehat R_S(\widehat f)
-
\inf_{f\in\mathcal H}\widehat R_S(f)
\ge 0.
$$

This is an **empirical optimization residual**. It asks how far the current training loss lies above the best training loss achievable in the class.

Notice that the best achievable training loss need not be zero. Reaching the empirical optimum and perfectly interpolating the sample are different statements.

To connect empirical optimization with population performance, define the uniform deviation

$$
\Delta_S(\mathcal H)
=
\sup_{f\in\mathcal H}
\left|R(f)-\widehat R_S(f)\right|.
$$

It measures the largest discrepancy between empirical and population risk across the hypothesis class.

Now add and subtract the empirical quantities:

$$
\begin{aligned}
R(\widehat f)-\inf_{f\in\mathcal H}R(f)
={}&
\left[R(\widehat f)-\widehat R_S(\widehat f)\right]
\\
&+
\left[
\widehat R_S(\widehat f)
-
\inf_{f\in\mathcal H}\widehat R_S(f)
\right]
\\
&+
\left[
\inf_{f\in\mathcal H}\widehat R_S(f)
-
\inf_{f\in\mathcal H}R(f)
\right].
\end{aligned}
$$

The middle term is exactly the optimization residual. The first term is at most the uniform deviation.

The last term is also at most the uniform deviation: every empirical risk is bounded above by the corresponding population risk plus $\Delta_S(\mathcal H)$, so the same inequality holds after taking infima.

Consequently,

$$
R(\widehat f)-\inf_{f\in\mathcal H}R(f)
\le
\mathcal E_{\mathrm{opt}}+2\Delta_S(\mathcal H).
$$

Adding the approximation error gives the central bound:

$$
\boxed{
\begin{aligned}
R(\widehat f)-R(f^\star)
\le{}&
\mathcal E_{\mathrm{app}}
+
\mathcal E_{\mathrm{opt}}
\\
&+
2\Delta_S(\mathcal H).
\end{aligned}
}
$$

This result separates three obstacles: the restriction imposed by the model class, the remaining training optimization gap, and the discrepancy between training and population risk.

It is important that this is an **upper bound**. With the definitions above, writing the excess risk as exactly equal to these three nonnegative terms would generally be incorrect.

## Estimation Error Is Not the Generalization Gap

For a particular trained predictor, the signed generalization gap is

$$
G_S(\widehat f)
=
R(\widehat f)-\widehat R_S(\widehat f).
$$

This compares population risk with training risk. Estimation error instead compares an ERM with the best population predictor in its class.

The uniform deviation controls the magnitude of the generalization gap for every predictor in the class. It also bounds the estimation error of an exact ERM:

$$
\mathcal E_{\mathrm{est}}
\le
2\Delta_S(\mathcal H).
$$

These quantities are related, but they answer different questions.

## Why Some References Write an Exact Three-Term Sum

We can also insert the population risk of an exact ERM into an exact decomposition:

$$
\begin{aligned}
R(\widehat f)-R(f^\star)
={}&
\mathcal E_{\mathrm{app}}
+
\mathcal E_{\mathrm{est}}
\\
&+
\left[R(\widehat f)-R(f_S)\right].
\end{aligned}
$$

The last term describes the effect of approximate optimization on population performance. Unlike the empirical optimization residual, it can be negative: an approximate empirical solution can generalize better than an exact ERM.

Bottou and Bousquet use an expected population-risk difference of this kind in their three-term decomposition. Their optimization term therefore differs from the nonnegative empirical residual used in the bound above. The definitions determine whether we have an identity or a bound. See [The Tradeoffs of Large Scale Learning, Sections 2 and 3.1](https://leon.bottou.org/publications/pdf/nips-2007.pdf).

# Why a Fixed-Function Argument Is Not Enough

The law of large numbers suggests that empirical averages should approach their expectations. Why is this not already a complete explanation of learning?

Start with a predictor $f$ chosen independently of the training sample. Define

$$
\begin{aligned}
Z_i
&=
\ell(f(X_i),Y_i)-R(f),
\\[4pt]
\widehat R_S(f)-R(f)
&=
\frac1n\sum_{i=1}^{n}Z_i.
\end{aligned}
$$

Assume that the loss has finite variance. Independence gives

$$
\begin{aligned}
\mathbb E[Z_i]&=0,
&
\operatorname{Var}(Z_i)&=\sigma_f^2<\infty,
\\[4pt]
\mathbb E_S[\widehat R_S(f)-R(f)]&=0,
&
\operatorname{Var}_S(\widehat R_S(f)-R(f))
&=\frac{\sigma_f^2}{n}.
\end{aligned}
$$

Chebyshev's inequality then yields

$$
\Pr_S\left(
|\widehat R_S(f)-R(f)|\ge\varepsilon
\right)
\le
\frac{\sigma_f^2}{n\varepsilon^2}.
$$

For any fixed positive tolerance, the probability of a large discrepancy tends to zero as the sample size grows.

But the learned predictor is not fixed independently of the sample. We choose it after inspecting that same sample. A learning algorithm actively searches for a function with a favorable empirical risk, and it may exploit unusually optimistic empirical estimates.

The pointwise calculation above does not justify substituting a sample-dependent predictor into the result.

**Uniform convergence** addresses this selection problem by controlling all predictors in the class simultaneously. If the largest discrepancy is small, then the discrepancy remains small for whichever member the algorithm chooses.

This explains the supremum in the central bound. It also explains why the complexity of the hypothesis class matters.

A statement such as "generalization error vanishes as the sample size tends to infinity" needs conditions ensuring the relevant convergence. Pointwise convergence alone does not establish uniform convergence over an arbitrary class.

# PAC: Accuracy Together with Confidence

The PAC framework makes the desired statistical guarantee explicit. PAC stands for **Probably Approximately Correct**.

In an agnostic PAC guarantee, we specify an accuracy tolerance $\varepsilon$ and a failure probability $\delta$. A learner should return a predictor whose population risk is close to the best risk in its hypothesis class:

$$
\Pr_{S\sim P^n}\left(
R(\widehat f)
-
\inf_{f\in\mathcal H}R(f)
\le\varepsilon
\right)
\ge 1-\delta.
$$

For the usual distribution-free notion, there must be a sample-size function that makes this guarantee hold for every distribution under consideration:

$$
n\ge n_{\mathcal H}(\varepsilon,\delta).
$$

The probability concerns the random training sample. For a randomized learner, it also includes the algorithm's randomness.

The connection with uniform convergence is direct. If the learner is an exact ERM, then

$$
\Delta_S(\mathcal H)\le\frac{\varepsilon}{2}
\quad\Longrightarrow\quad
R(f_S)
-
\inf_{f\in\mathcal H}R(f)
\le\varepsilon.
$$

Thus, a high-probability uniform convergence guarantee produces a high-probability excess-risk guarantee for ERM.

There are two distinctions worth keeping clear.

First, PAC learning compares the learner with the best predictor **inside its class**. A small estimation error does not remove a large approximation error.

Second, a guarantee about the generalization gap is not itself the same statement as a guarantee about excess population risk. The ERM property is what connects the two in this argument.

# Two Examples of Sample Requirements

The decomposition becomes more concrete when we ask what assumptions make learning possible with a given amount of data.

## A Noiseless Linear Model

Suppose that the target is linear and the labels contain no noise:

$$
f^\star(x)=\langle x,\theta^\star\rangle,
\qquad
\theta^\star\in\mathbb R^d,
\qquad
Y_i=\langle X_i,\theta^\star\rangle.
$$

Let $\mathbf X$ be the matrix whose rows are the training inputs, and let $\mathbf y$ collect their labels:

$$
\mathbf X=
\begin{bmatrix}
X_1^\top\\
\vdots\\
X_n^\top
\end{bmatrix},
\qquad
\mathbf y=\mathbf X\theta^\star.
$$

If the design matrix has full column rank, the parameter is uniquely determined:

$$
n\ge d,
\qquad
\operatorname{rank}(\mathbf X)=d,
\qquad
\theta^\star
=
(\mathbf X^\top\mathbf X)^{-1}\mathbf X^\top\mathbf y.
$$

The rank condition matters. Having at least as many observations as parameters is not enough if the inputs fail to explore all relevant directions.

This example is unusually favorable because the target belongs to a known finite-dimensional class and observations are exact. With noisy labels, a finite sample generally does not recover the true parameter exactly.

## Lipschitz Interpolation

Now suppose that the input domain is the unit cube and the target is only assumed to be Lipschitz:

$$
\mathcal X=[0,1]^d,
\qquad
|f^\star(x)-f^\star(z)|
\le B\|x-z\|.
$$

Again, assume noiseless labels. Choose an interpolating function with the smallest possible Lipschitz constant:

$$
\begin{aligned}
Y_i&=f^\star(X_i),
\\[4pt]
\widehat f
&\in
\operatorname*{arg\,min}_{
f:\,f(X_i)=Y_i,\ i=1,\ldots,n
}
\operatorname{Lip}(f).
\end{aligned}
$$

The true target is a feasible interpolant. Therefore, the selected function has Lipschitz constant at most $B$.

For a fresh input $x$, let $X_{i^\star}$ be its nearest training input:

$$
i^\star
\in
\operatorname*{arg\,min}_{1\le i\le n}\|x-X_i\|.
$$

Insert the values of both functions at that nearby observation:

$$
\begin{aligned}
|\widehat f(x)-f^\star(x)|
\le{}&
|\widehat f(x)-\widehat f(X_{i^\star})|
\\
&+
|\widehat f(X_{i^\star})-f^\star(X_{i^\star})|
\\
&+
|f^\star(X_{i^\star})-f^\star(x)|
\\[4pt]
\le{}&
B\|x-X_{i^\star}\|
+
0
+
B\|x-X_{i^\star}\|
\\[4pt]
={}&2B\|x-X_{i^\star}\|.
\end{aligned}
$$

The middle term vanishes because the two functions agree at every training input. The other terms are controlled by their Lipschitz constants.

This calculation is useful because it turns a prediction problem into a geometric one: how far is a new point from the observed sample?

For independent uniform inputs on the unit cube, the expected squared nearest-neighbor distance admits a bound of the following form:

$$
\mathbb E_{S,X}\left[
\|X-X_{i^\star}\|^2
\right]
\le c_d n^{-2/d}.
$$

Here $X$ is a fresh independent input, and $c_d$ is a constant that may depend on dimension.

The exponent has a geometric interpretation. A neighborhood of radius $r$ has volume proportional to the $d$th power of its radius. Finding roughly one observation in such a neighborhood suggests a distance scale of the inverse $d$th root of the sample size.

Under squared loss and noiseless labels, the risk therefore satisfies

$$
\begin{aligned}
\mathbb E_S[R(\widehat f)]
&=
\mathbb E_{S,X}
\left[
|\widehat f(X)-f^\star(X)|^2
\right]
\\
&\le
4B^2c_d n^{-2/d}.
\end{aligned}
$$

To make this upper bound at most a desired tolerance, it is sufficient to choose

$$
4B^2c_d n^{-2/d}\le\varepsilon
\quad\Longleftrightarrow\quad
n\ge
\left(
\frac{4B^2c_d}{\varepsilon}
\right)^{d/2},
$$

for positive $\varepsilon$ and $B$.

As dimension increases, this guarantee demands rapidly more data for a fixed accuracy. This is one manifestation of the **curse of dimensionality**.

There are two qualifications. The calculation assumes noiseless observations and a specified input distribution. Also, rearranging an upper bound gives a sufficient sample size for that guarantee; it does not, by itself, prove a lower bound on every possible learning algorithm.

# The Loss Function Changes What “Good” Means

So far, we have kept the loss fixed. But a decomposition is always a decomposition of risk **with respect to a particular loss**.

This matters when training uses a surrogate loss while evaluation uses a different metric.

Consider binary classification with a real-valued score function:

$$
Y\in\{-1,+1\},
\qquad
g:\mathcal X\to\mathbb R,
\qquad
\widehat Y=\operatorname{sign}(g(X)).
$$

The **0–1 loss** records whether the predicted class is wrong:

$$
\ell_{01}(g(x),y)
=
\mathbf 1\left\{
\operatorname{sign}(g(x))\ne y
\right\}.
$$

The **hinge loss** instead uses the signed margin:

$$
\ell_{\mathrm{hinge}}(g(x),y)
=
\max\{1-yg(x),0\}.
$$

A positive margin means that the predicted sign is correct. However, hinge loss continues to penalize a correct prediction if its margin is below one.

At a score of zero, a tie-breaking convention must be specified for classification. The example below contains no zero scores.

Pointwise, and hence after taking expectations, we have

$$
\begin{aligned}
\ell_{01}(g(x),y)
&\le
\ell_{\mathrm{hinge}}(g(x),y),
\\[4pt]
R_{01}(g)
&\le
R_{\mathrm{hinge}}(g).
\end{aligned}
$$

This does not imply that hinge loss and classification error rank every pair of predictors in the same order.

## Two Predictors with Opposite Rankings

Suppose that there are two possible inputs, both with positive labels:

$$
P(X=x_1)=0.99,
\qquad
P(X=x_2)=0.01,
\qquad
Y=+1.
$$

Consider the following scores:

$$
\begin{aligned}
g_A(x_1)&=0.1,
&
g_A(x_2)&=0.1,
\\[4pt]
g_B(x_1)&=1,
&
g_B(x_2)&=-0.1.
\end{aligned}
$$

Predictor A classifies both inputs correctly, but with small margins. Predictor B achieves a margin of one on the common input and misclassifies the rare input.

Their classification risks are

$$
R_{01}(g_A)=0,
\qquad
R_{01}(g_B)=0.01.
$$

Their hinge risks are

$$
\begin{aligned}
R_{\mathrm{hinge}}(g_A)
&=
0.99(1-0.1)+0.01(1-0.1)
\\
&=0.9,
\\[6pt]
R_{\mathrm{hinge}}(g_B)
&=
0.99(0)+0.01(1+0.1)
\\
&=0.011.
\end{aligned}
$$

| Predictor | Classification risk | Hinge risk |
|---|---:|---:|
| A | 0 | 0.9 |
| B | 0.01 | 0.011 |

Hinge risk prefers B, whereas classification error prefers A.

There is no contradiction with the upper-bound property. Each predictor's classification risk is below its own hinge risk. The inequality says nothing about whether comparing two hinge risks preserves the ordering of the corresponding classification risks.

This example also does not contradict classification calibration of hinge loss. Calibration concerns approaching the optimal surrogate risk over a sufficiently rich class; arbitrary pairwise rankings need not agree. Here, a score of one at both inputs would achieve zero loss under both criteria. For the broader connection between surrogate and classification risk, see [Bartlett, Jordan, and McAuliffe, Convexity, Classification, and Risk Bounds](https://www.stat.berkeley.edu/~jordan/638.pdf).

# Reading the Decomposition as a Diagnostic Tool

The decomposition is most useful when it changes the question we ask about a poorly performing model.

| Source of difficulty | Question to investigate | Possible direction |
|---|---|---|
| Approximation | Does the class contain predictors suited to the task? | Change the representation or enlarge the class. |
| Estimation | Does the sample support reliable selection within the class? | Gather more data or control the effective complexity. |
| Optimization | Has training approached the best empirical objective value available? | Revisit the optimization procedure or computational budget. |
| Choice of loss | Does improving the training loss address the evaluation criterion? | Examine the relation between the surrogate and the desired risk. |

These are directions to investigate, not conclusions that can be read off from one number. The unknown population distribution prevents us from directly measuring most terms, and changing an architecture or training procedure can affect several of them at once.

In particular, making the empirical optimization residual smaller does not force population performance to improve monotonically. The statistical and computational parts of learning interact.

The decomposition tells us which comparisons a mathematical explanation must address. It does not replace the work of choosing a useful model class, proving a statistical guarantee, or checking what the evaluation loss actually measures.

# References and Conventions

The framework above belongs to standard statistical learning theory. The particular linear, Lipschitz, and classification examples are worked illustrations of that framework.

Terminology varies across references. In particular, some authors use “approximation error” for the best risk within a class, including irreducible risk; others subtract the reference optimum, as this article does. An “optimization error” may likewise refer to an empirical residual or to its effect on population risk.

- Shai Shalev-Shwartz and Shai Ben-David, [*Understanding Machine Learning: From Theory to Algorithms*](https://www.cs.huji.ac.il/~shais/UnderstandingMachineLearning/understanding-machine-learning-theory-algorithms.pdf), Cambridge University Press, 2014.  
  Section 5.2 is explicitly titled “Error Decomposition.” Chapters 3–4 cover PAC learning and uniform convergence, and Section 12.3 introduces surrogate losses. The approximation-error convention in Section 5.2 differs from the excess-risk convention used here.

- Léon Bottou and Olivier Bousquet, [*The Tradeoffs of Large Scale Learning*](https://leon.bottou.org/publications/pdf/nips-2007.pdf), *Advances in Neural Information Processing Systems 20*, 2008, pp. 161–168.  
  Sections 2.1–2.3 develop the approximation-estimation-optimization perspective. Section 3.1 relates empirical optimization accuracy to population performance.

- Peter L. Bartlett, Michael I. Jordan, and Jon D. McAuliffe, [*Convexity, Classification, and Risk Bounds*](https://www.stat.berkeley.edu/~jordan/638.pdf), Technical Report 638, University of California, Berkeley, 2003.  
  This earlier technical-report version studies how convex surrogate risk relates to classification risk. The journal article appeared in the *Journal of the American Statistical Association* in 2006.
