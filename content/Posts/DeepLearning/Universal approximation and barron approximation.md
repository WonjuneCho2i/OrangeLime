---
title: Universal Approximation and Barron Approximation
tags:
  - deep-learning
  - approximation-theory
  - neural-networks
  - functional-analysis
  - fourier-analysis
---

## Abstract

These notes explain why a neural network with one hidden layer and a continuous sigmoidal activation can approximate every continuous function on a compact cube. The proof proceeds through continuous linear functionals, signed measures, and the discriminatory property of sigmoids. The second part introduces Fourier-based Barron classes and proves an $O(N^{-1})$ mean squared approximation bound for networks with $N$ hidden neurons.

**Conventions.** We write $I^n=[0,1]^n$, use the uniform norm on $C(I^n)$, and work with real-valued targets unless a Fourier-space definition explicitly allows complex values. For the quantitative theorem we explicitly allow a scalar output bias, which does not count as a hidden neuron. This architecture convention is used explicitly in the quantitative proof.

## 1. The approximation question

A network with one hidden layer produces a function of the form

$$
G(x)=\sum_{j=1}^{N}\alpha_j\sigma(w_j^\top x+\theta_j),\qquad x\in\mathbb R^n. \tag{1.1}
$$

Here $w_j\in\mathbb R^n$ is an input weight, $\theta_j\in\mathbb R$ is a hidden bias, $\alpha_j\in\mathbb R$ is an output weight, and $N$ is the number of hidden neurons. One hidden layer can contain arbitrarily many neurons.

The universal approximation question asks whether, for every continuous target $f$ and every tolerance $\varepsilon>0$, some network of this form satisfies

$$
\sup_{x\in I^n}|f(x)-G(x)|<\varepsilon.
$$

A quantitative approximation theorem asks a further question: how large must $N$ be to achieve a prescribed accuracy?

Three distinct issues arise in neural-network theory:

- **Approximation:** whether the chosen function class can represent the target sufficiently accurately.
- **Generalization:** whether performance measured on finite data reflects performance on the underlying input distribution.
- **Optimization:** whether a learning procedure finds suitable parameters.

The theorems developed here concern approximation. An existence theorem for good parameters does not itself specify a training algorithm or a sample size.

## 2. Function spaces and density

**Definition 2.1 (Continuous function space).** For a compact set $K\subset\mathbb R^n$, let

$$
C(K)=\{f:K\to\mathbb R:f\text{ is continuous}\}.
$$

Addition and scalar multiplication are defined pointwise. If $f,g\in C(K)$ and $a,b\in\mathbb R$, then $af+bg\in C(K)$. Thus $C(K)$ is a real vector space whose vectors are functions.

**Definition 2.2 (Norm).** A norm on a real vector space $X$ is a map $\|\cdot\|:X\to[0,\infty)$ such that

$$
\|u\|=0\iff u=0,\qquad \|au\|=|a|\|u\|,\qquad \|u+v\|\le\|u\|+\|v\|.
$$

A vector space equipped with a norm is a normed linear space. On $C(K)$ we use the uniform norm

$$
\|f\|_\infty=\sup_{x\in K}|f(x)|.
$$

Compactness ensures that this quantity is finite for every continuous $f$. The space $C(K)$ is complete in this norm, although completeness is not needed for the separation lemma below.

**Definition 2.3 (Density).** A subset $D$ of a normed space $X$ is dense in $X$ if

$$
\forall f\in X,\quad\forall\varepsilon>0,\quad\exists g\in D:\quad\|f-g\|<\varepsilon.
$$

Equivalently, its closure satisfies $\overline D=X$.

For the uniform norm, the inequality $\|f-G\|_\infty<\varepsilon$ controls the error at every point of the domain simultaneously. Density does not require exact equality with the target.

**Remark 2.4 (The width is allowed to vary).** The approximation space contains networks of every finite width $N$. A fixed width need not suffice for every target and every tolerance. Allowing the width to vary also makes the union of these network classes a linear space: adding two networks concatenates their hidden neurons.

## 3. Measures and continuous linear functionals

### 3.1. A measure assigns a size to a set

Start with length on the real line. The interval $[2,5]$ has length $3$, and two disjoint intervals of lengths $2$ and $4$ have total length $6$. A measure generalizes this assignment: its input is a set and its output is a nonnegative size. The size may mean length, area, probability, or mass. It is not necessarily geometric length.

There are three distinct objects to keep track of:

| Object | Role | Example |
|---|---|---|
| $K$ | The ambient space | $[0,1]$ |
| $\mathcal A$ | The collection of measurable subsets | $\mathcal B([0,1])$ |
| $\mu$ | The rule assigning sizes | $\mu([a,b])=b-a$ |

A set $A$ is a subset of $K$, whereas a collection $\mathcal A$ is a set whose elements are themselves subsets of $K$. In particular, $A\in\mathcal A$ means that $A$ is one of the sets to which the measurement rule applies.

### 3.2. Why introduce a sigma-algebra?

A measure is defined on a specified collection of sets, not automatically on every conceivable subset of the ambient space. This collection should allow complements and countable combinations, so that limits of sets and events remain meaningful.

**Definition 3.1 (Sigma-algebra).** A collection $\mathcal A\subseteq 2^K$ is a sigma-algebra if:

1. $K\in\mathcal A$;
2. $A\in\mathcal A$ implies $K\setminus A\in\mathcal A$;
3. $A_1,A_2,\ldots\in\mathcal A$ implies $\bigcup_{j=1}^{\infty}A_j\in\mathcal A$.

These rules also give $\varnothing\in\mathcal A$ and closure under countable intersections, since

$$
\bigcap_{j=1}^{\infty} A_j=K\setminus\bigcup_{j=1}^{\infty}(K\setminus A_j).
$$

For example, $\{\varnothing,K\}$ is the smallest sigma-algebra, while $2^K$, the collection of all subsets of $K$, is the largest. The term *countable* includes finite lists and sequences indexed by $1,2,3,\ldots$; it does not grant closure under arbitrary uncountable unions.

### 3.3. Borel sets are generated by open sets

The Borel sigma-algebra $\mathcal B(K)$ is the smallest sigma-algebra containing every open subset of $K$. A Borel set is an individual member of $\mathcal B(K)$. When $K\subset\mathbb R^n$, openness is relative to $K$.

On $\mathbb R$, open intervals are Borel. Closed sets are Borel because their complements are open. Half-open intervals, singletons, and countable sets are also Borel. For example,

$$
\{a\}=\bigcap_{m=1}^{\infty}(a-1/m,a+1/m),\qquad
\mathbb Q=\bigcup_{q\in\mathbb Q}\{q\}.
$$

The second union is countable because the rational numbers are countable. Equivalently, $\mathcal B(\mathbb R)$ is generated by open intervals. Every open subset of $\mathbb R$ is a countable union of intervals from the countable base with rational endpoints.

The word *smallest* means that no sets are included beyond those forced by the sigma-algebra requirements. It does not mean the collection contains only intervals. Borel sets include much more complicated sets, such as the Cantor set.

### 3.4. Borel measures: choosing a measurement rule

**Definition 3.2 (Measure and Borel measure).** A nonnegative measure on $(K,\mathcal A)$ is a map $\mu:\mathcal A\to[0,\infty]$ satisfying $\mu(\varnothing)=0$ and

$$
\mu\left(\bigcup_{j=1}^{\infty} A_j\right)=\sum_{j=1}^{\infty}\mu(A_j)
$$

for pairwise disjoint sets $A_j\in\mathcal A$. A Borel measure is a measure defined on $\mathcal A=\mathcal B(K)$.

The Borel qualification specifies the measurable sets. It does not select one unique assignment of sizes. For example, on the same Borel sets of $K=[0,1]$, one can use any of the following rules:

- **Length:** $\lambda(A)$ is the Lebesgue length of $A$.
- **Point mass at $a$:** $\delta_a(A)=1$ if $a\in A$ and $0$ otherwise.
- **A density:** $\mu(A)=\int_A 2x\,dx$.

For $A=[0,1/2]$ these give, respectively,

$$
\lambda(A)=\frac12,\qquad \delta_{1/4}(A)=1,\qquad \int_A 2x\,dx=\frac14.
$$

All three have total mass $1$ on $[0,1]$, so all three are probability measures, despite assigning different masses to the same set. In particular, a point has length zero but may have positive mass for another Borel measure.

Countable additivity requires disjointness to prevent double counting. It is stronger than additivity for only finitely many sets. For length, the countable Borel set $\mathbb Q\cap[0,1]$ has measure zero because each singleton has length zero. One cannot apply this reasoning to the uncountable union of all points of $[0,1]$.

### 3.5. Integrating with respect to different measures

The notation $\int f\,d\mu$ uses $\mu$ to weight locations in the domain. For an indicator function,

$$
\int 1_A\,d\mu=\mu(A).
$$

For a point mass and a density, respectively,

$$
\int_K f\,d\delta_a=f(a),\qquad
\int_K f\,d\mu=\int_K f(x)\rho(x)\,dx
\quad\text{if}\quad \mu(A)=\int_A\rho(x)\,dx.
$$

Thus measure integration unifies evaluation at a point, weighted averages, and ordinary integration. This is why a measure can represent a linear functional in the universal approximation proof.

### 3.6. Finite signed measures used in the proof

We write $M(K)$ for the finite regular signed Borel measures on $K$. Such a measure has a Jordan decomposition $\mu=\mu^+-\mu^-$ and a finite total variation measure $|\mu|=\mu^++\mu^-$. In particular,

$$
\left|\int_K f\,d\mu\right|\le\int_K|f|\,d|\mu|\le\|f\|_\infty|\mu|(K).
$$

**Example 3.3 (Zero total mass does not imply a zero measure).** Let $a\ne b$ be points of $K$ and set $\mu=\delta_a-\delta_b$. Then $\mu(K)=0$, but $\mu\ne0$. For any continuous function,

$$
\int_K f\,d\mu=f(a)-f(b).
$$

The statement $\mu=0$ means that $\mu(A)=0$ for every Borel set $A$.

### 3.7. Bounded linear functionals

**Definition 3.4.** A linear functional on a normed space $X$ is a linear map $L:X\to\mathbb R$. It is bounded if there is $M<\infty$ such that

$$
|L(u)|\le M\|u\|\qquad(u\in X).
$$

For a linear functional, boundedness is equivalent to continuity. The continuous dual space is denoted by $X^*$.

The following form of the Riesz representation theorem is the bridge between linear functionals and measures.

**Theorem 3.5 (Riesz representation).** Let $K\subset\mathbb R^n$ be compact. For every bounded linear functional $L:C(K)\to\mathbb R$, there is a unique $\mu\in M(K)$ such that

$$
L(f)=\int_K f\,d\mu\qquad(f\in C(K)).
$$

Moreover, $\|L\|=|\mu|(K)$. If $L$ is positive, meaning $f\ge0\Rightarrow L(f)\ge0$, then $\mu$ is nonnegative.

The signed version is needed because a general linear functional need not be positive.

## 4. Sigmoids and discriminatory functions

**Definition 4.1 (Continuous sigmoid).** A continuous function $\sigma:\mathbb R\to[0,1]$ is sigmoidal if

$$
\lim_{t\to-\infty}\sigma(t)=0,\qquad\lim_{t\to+\infty}\sigma(t)=1.
$$

The logistic function $\sigma(t)=(1+e^{-t})^{-1}$ is an example. The argument below uses continuity, boundedness, and the two limits. Differentiability and monotonicity are not required.

**Definition 4.2 (Discriminatory activation).** An activation $\sigma$ is discriminatory on $I^n$ if, for every $\mu\in M(I^n)$,

$$
\left[\int_{I^n}\sigma(w^\top x+\theta)\,d\mu(x)=0\quad\text{for all }w\in\mathbb R^n,\theta\in\mathbb R\right]\Longrightarrow\mu=0.\tag{4.1}
$$

Equivalently, every nonzero signed measure can be detected by at least one neuron:

$$
\mu\ne0\Longrightarrow\exists w,\theta:\int_{I^n}\sigma(w^\top x+\theta)\,d\mu(x)\ne0.
$$

This is a property of an entire family of test functions, rather than a claim about classification accuracy.

**Lemma 4.3 (Continuous sigmoids are discriminatory).** Every continuous sigmoid is discriminatory on $I^n$.

**Proof.** Suppose $\mu\in M(I^n)$ satisfies the integral condition in (4.1). Fix $w\in\mathbb R^n$, $\theta\in\mathbb R$, and $a\in\mathbb R$. For every $\lambda>0$,

$$
\int_{I^n}\sigma\bigl(\lambda(w^\top x+\theta)+a\bigr)\,d\mu(x)=0,
$$

because this is another admissible choice of input weights and bias. Define

$$
H=\{x\in I^n:w^\top x+\theta>0\},\qquad
P=\{x\in I^n:w^\top x+\theta=0\}.
$$

As $\lambda\to\infty$, the integrand converges pointwise to

$$
\mathbf1_H(x)+\sigma(a)\mathbf1_P(x).
$$

It is bounded by $1$, and $|\mu|(I^n)<\infty$. Dominated convergence, applied to the positive and negative parts of $\mu$, gives

$$
\mu(H)+\sigma(a)\mu(P)=0.\tag{4.2}
$$

There are $a_1,a_2$ with $\sigma(a_1)\ne\sigma(a_2)$, since the limits of $\sigma$ at the two ends of the real line differ. Subtracting the two identities (4.2) yields $\mu(P)=0$, and then $\mu(H)=0$.

Also $\mu(I^n)=0$: set $w=0$ in the original hypothesis and choose a bias $a$ with $\sigma(a)\ne0$.

To justify that these facts determine the entire measure, fix $v\in\mathbb R^n$ and let $\nu_v$ be the pushforward of $\mu$ under $x\mapsto v^\top x$. For every $t\in\mathbb R$,

$$
\nu_v((t,\infty))=\mu(\{x:v^\top x>t\})=0.
$$

Together with $\nu_v(\mathbb R)=0$, this implies $\nu_v=0$ by uniqueness of finite signed measures on the Borel sets of $\mathbb R$.

Extend $\mu$ by zero outside $I^n$. For every $\xi\in\mathbb R^n$,

$$
\widehat\mu(\xi)
=\int_{I^n}e^{-2\pi i\xi^\top x}\,d\mu(x)
=\int_{\mathbb R}e^{-2\pi it}\,d\nu_\xi(t)=0.
$$

Uniqueness of the Fourier transform of finite measures implies $\mu=0$.

**Remark 4.4 (Why the extra shift is useful).** Using only $\sigma(\lambda(w^\top x+\theta))$ leaves a contribution $\sigma(0)\mu(P)$ on the boundary hyperplane. Varying the additional shift $a$ separates the hyperplane mass from the halfspace mass.

## 5. Separation and the universal approximation theorem

### 5.1. A separation lemma

**Lemma 5.1 (A non-dense subspace has a nonzero annihilator).** Let $W$ be a non-dense linear subspace of a real normed space $X$. There exists $L\in X^*$ such that

$$
L\ne0,\qquad L(w)=0\quad(w\in W).
$$

**Proof using Hahn–Banach.** Set $M=\overline W$. Choose $x_0\in X\setminus M$. Since $M$ is closed,

$$
d=\operatorname{dist}(x_0,M)>0.
$$

On $Y=M+\operatorname{span}\{x_0\}$ define

$$
L_0(m+ax_0)=a.
$$

This is well-defined because $x_0\notin M$. For $a\ne0$,

$$
\|m+ax_0\|=|a|\|x_0+m/a\|\ge|a|d.
$$

The resulting inequality is immediate for $a=0$. Thus

$$
|L_0(y)|\le d^{-1}\|y\|\qquad(y\in Y).
$$

Hahn–Banach extends $L_0$ to a bounded linear functional $L$ on $X$. It vanishes on $M$, hence on $W$, while $L(x_0)=1$.

**Lemma 5.2 (Measure annihilator).** If $W$ is a non-dense linear subspace of $C(I^n)$, there is a nonzero $\mu\in M(I^n)$ such that

$$
\int_{I^n}h\,d\mu=0\qquad(h\in W).
$$

**Proof.** Lemma 5.1 gives a nonzero bounded linear functional $L$ vanishing on $W$. Apply Theorem 3.5 to write $L(f)=\int_{I^n}f\,d\mu$. If $\mu=0$, then $L=0$, a contradiction. The required identity follows from $L|_W=0$.

**Remark 5.3 (A necessary condition implicit in the handwriting).** The condition $\mu\ne0$ must be included in the measure-annihilator lemma. The zero measure annihilates every function class and would give no information about density.

### 5.2. Density from discrimination

**Theorem 5.4 (Continuous discriminatory activations give density).** Let $\sigma:\mathbb R\to\mathbb R$ be continuous and discriminatory on $I^n$. Then the set

$$
W_\sigma=\left\{\sum_{j=1}^{N}\alpha_j\sigma(w_j^\top x+\theta_j):N\in\mathbb N,\ \alpha_j,\theta_j\in\mathbb R,\ w_j\in\mathbb R^n\right\}
$$

is dense in $C(I^n)$ under the uniform norm.

**Proof.** Every member of $W_\sigma$ is continuous. The class is a linear subspace: scalar multiplication changes the output weights, and addition concatenates two finite lists of neurons.

Suppose for contradiction that $W_\sigma$ is not dense. Lemma 5.2 provides $\mu\ne0$ such that

$$
\int_{I^n}h\,d\mu=0\qquad(h\in W_\sigma).
$$

For arbitrary $w$ and $\theta$, the single-neuron function $h(x)=\sigma(w^\top x+\theta)$ belongs to $W_\sigma$. Hence

$$
\int_{I^n}\sigma(w^\top x+\theta)\,d\mu(x)=0\qquad\text{for every }w,\theta.
$$

The discriminatory property implies $\mu=0$, contradicting its construction.

**Corollary 5.5 (Universal approximation; Cybenko, 1989).** Let $\sigma:\mathbb R\to[0,1]$ be a continuous sigmoid. For every $f\in C(I^n)$ and every $\varepsilon>0$, there exist a finite $N$ and parameters $\alpha_j,\theta_j\in\mathbb R$, $w_j\in\mathbb R^n$, such that

$$
\sup_{x\in I^n}\left|f(x)-\sum_{j=1}^{N}\alpha_j\sigma(w_j^\top x+\theta_j)\right|<\varepsilon.
$$

**Proof.** Combine Lemma 4.3 and Theorem 5.4.

The complete logical chain is:

1. Assume the network space is not dense.
2. **Hahn–Banach:** a nonzero continuous linear functional annihilates it.
3. **Riesz representation:** a nonzero signed measure annihilates every neuron.
4. **Discriminatory property:** $\mu=0$, a contradiction.

### 5.3. What the theorem guarantees

The quantifier order matters:

$$
\forall f\in C(I^n),\quad\forall\varepsilon>0,\quad\exists N,\ \exists\text{ parameters}.
$$

The width and parameters may depend on both $f$ and $\varepsilon$. The theorem gives uniform approximation on the compact domain, but no explicit dependence of $N$ on $\varepsilon$. It also gives no guarantee that a specified optimization method will discover the approximating parameters.

## 6. Fourier-based function classes

### 6.1. Fourier transform and Sobolev regularity

For $f\in L^1(\mathbb R^d)$, use the convention

$$
\widehat f(\xi)=\int_{\mathbb R^d}f(x)e^{-2\pi i\xi^\top x}\,dx.\tag{6.1}
$$

The vector $\xi$ is a frequency variable. Large $|\xi|$ describes rapidly varying components. The Fourier characterization of the Sobolev space is

$$
f\in H^s(\mathbb R^d)\iff\int_{\mathbb R^d}(1+|\xi|^2)^s|\widehat f(\xi)|^2\,d\xi<\infty,\tag{6.2}
$$

with the Fourier transform interpreted in the $L^2$ or distributional sense where necessary. Equivalent conventions may place $4\pi^2$ inside the weight. Larger $s$ penalizes high frequencies more strongly.

### 6.2. The Fourier Barron class

**Definition 6.1 (Fourier Barron class).** For $C_1>0$, let

$$
\Gamma_{C_1}=\left\{f\in L^1(\mathbb R^d;\mathbb R):\widehat f\in L^1(\mathbb R^d),\quad C_f:=\int_{\mathbb R^d}2\pi|\xi|\,|\widehat f(\xi)|\,d\xi\le C_1\right\}.
$$

We use the continuous representative supplied by Fourier inversion:

$$
f(x)=\int_{\mathbb R^d}\widehat f(\xi)e^{2\pi i\xi^\top x}\,d\xi.
$$

The continuous-representative convention is needed because an $L^1$ function is initially defined only up to equality almost everywhere.

The quantity $C_f$ is a weighted Fourier $L^1$ quantity. It penalizes high frequencies and controls the approximation constant in the theorem below. It differs from the weighted Fourier $L^2$ quantity in (6.2).

### 6.3. Barron spaces on a restricted domain

Suppose $f$ is given only on $\Omega\subset\mathbb R^d$. To use a whole-space Fourier transform, consider extensions $\widetilde f$ with $\widetilde f|_\Omega=f$.

For $s\ge0$, an extension-based Fourier quantity is

$$
\|f\|_{B^s(\Omega)}=\inf_{\substack{\widetilde f|_\Omega=f\\\widetilde f\ \mathrm{admissible}}}\int_{\mathbb R^d}(1+|\xi|)^s|\widehat{\widetilde f}(\xi)|\,d\xi.\tag{6.3}
$$

Here admissible extensions have a well-defined Fourier transform; one may work with $\widetilde f\in L^1(\mathbb R^d)$ with integrable Fourier transform. Define $B^s(\Omega)$ by finiteness of this quantity.

Different extensions agree inside $\Omega$ but can have different Fourier costs. The infimum assigns the smallest possible cost to the function on the region of interest. It does not assert that an optimal extension exists.

For example, abruptly extending a nonzero boundary value by zero can create a jump. A smoother continuation outside the domain can have a smaller high-frequency cost. This motivates considering extensions with controlled high-frequency behavior.

When $s=1$, the weight separates as

$$
\int_{\mathbb R^d}(1+|\xi|)|\widehat{\widetilde f}(\xi)|\,d\xi
=\|\widehat{\widetilde f}\|_{L^1}+\int_{\mathbb R^d}|\xi|\,|\widehat{\widetilde f}(\xi)|\,d\xi.
$$

Thus the $B^1$ condition controls both Fourier integrability and the first frequency moment. It is closely related to the conditions defining $\Gamma_{C_1}$, with the displayed normalization differences.

## 7. A quantitative Barron approximation theorem

Let

$$
B_1^d=\{x\in\mathbb R^d:|x|\le1\},\qquad P=\operatorname{Unif}(B_1^d).
$$

Write

$$
\|u\|_{L^2(P)}^2=\frac{1}{|B_1^d|}\int_{B_1^d}|u(x)|^2\,dx.
$$

**Theorem 7.1 (Barron approximation on the unit ball).** Let $\sigma:\mathbb R\to[0,1]$ be a continuous sigmoid and $f\in\Gamma_{C_1}$. For every $c>4C_1^2$ and every $N\in\mathbb N$, there is a network with $N$ hidden neurons,

$$
\Phi_N(x)=b_0+\sum_{j=1}^{N}\alpha_j\sigma(w_j^\top x+\theta_j),\tag{7.1}
$$

such that

$$
\frac{1}{|B_1^d|}\int_{B_1^d}|f(x)-\Phi_N(x)|^2\,dx<\frac{c}{N}.\tag{7.2}
$$

**Remark 7.2 (Architecture convention).** The scalar $b_0$ is an output bias, not an extra hidden neuron. It is explicit in the proof below. The UAT proved earlier uses the original form (1.1), so its statement is unaffected by this convention.

## 8. Supplementary proof of the Barron bound

The following derivation makes the $N^{-1}$ rate and the constant $4C_1^2$ explicit. It has three steps: represent the target using halfspace indicators, approximate their integral by a finite average, and replace the indicators by steep sigmoids.

### 8.1. A finite-average approximation lemma

**Lemma 8.1 (Hilbert-space sampling).** Let $H$ be a real Hilbert space, and let $Z$ be a strongly measurable random element with $\|Z\|_H\le1$ almost surely. If $u=M\mathbb EZ$ for some $M>0$, then for each $N\in\mathbb N$ there are realizations $z_1,\ldots,z_N$ such that

$$
\left\|u-\frac{M}{N}\sum_{j=1}^{N}z_j\right\|_H^2\le\frac{M^2}{N}.
$$

**Proof.** Take independent copies $Z_1,\ldots,Z_N$ of $Z$. Independence and centering remove the mixed terms, so

$$
\begin{aligned}
\mathbb E\left\|\frac{M}{N}\sum_{j=1}^{N}(Z_j-\mathbb EZ)\right\|_H^2
&=\frac{M^2}{N}\mathbb E\|Z-\mathbb EZ\|_H^2\\
&=\frac{M^2}{N}\left(\mathbb E\|Z\|_H^2-\|\mathbb EZ\|_H^2\right)\\
&\le\frac{M^2}{N}.
\end{aligned}
$$

At least one realization has squared error no larger than this upper bound.

The proof shows why a squared error scales like $1/N$: it is the variance of an average of $N$ independent bounded elements.

### 8.2. A halfspace representation controlled by the Fourier moment

Set $H(t)=\mathbf1_{\{t>0\}}$. For $\xi\ne0$, define

$$
v_\xi=\frac{\xi}{|\xi|},\qquad g_\xi(s)=\operatorname{Re}\bigl(\widehat f(\xi)e^{2\pi i|\xi|s}\bigr).
$$

Then

$$
|g_\xi'(s)|\le2\pi|\xi|\,|\widehat f(\xi)|.
$$

Since $|v_\xi^\top x|\le1$ on $B_1^d$, the fundamental theorem of calculus gives

$$
g_\xi(v_\xi^\top x)=g_\xi(-1)+\int_{-1}^{1}g_\xi'(t)H(v_\xi^\top x-t)\,dt.
$$

The value of $H$ at the single threshold does not affect the integral. Fourier inversion and Fubini now imply

$$
f(x)=b_0+\int_{\mathbb R^d\setminus\{0\}}\int_{-1}^{1}q(\xi,t)H(v_\xi^\top x-t)\,dt\,d\xi,\tag{8.1}
$$

where

$$
q(\xi,t)=g_\xi'(t),\qquad b_0=\int_{\mathbb R^d}g_\xi(-1)\,d\xi.
$$

The value assigned at $\xi=0$ is immaterial to these Lebesgue integrals. All terms are well-defined because $\widehat f\in L^1$ and $C_f<\infty$.

The absolute mass of the coefficient density is bounded by

$$
M:=\int_{\mathbb R^d}\int_{-1}^{1}|q(\xi,t)|\,dt\,d\xi\le2C_f\le2C_1.\tag{8.2}
$$

If $M=0$, (8.1) is constant on the ball and an output bias represents it exactly. Assume henceforth that $M>0$. Normalize $|q|$ to a probability density and attach its sign to the atom:

$$
Z_{\xi,t}(x)=\operatorname{sgn}(q(\xi,t))H(v_\xi^\top x-t).
$$

Then, as elements of the real Hilbert space $H=L^2(P)$,

$$
f-b_0=M\mathbb EZ,\qquad\|Z\|_{L^2(P)}\le1.
$$

Lemma 8.1 yields signs $s_j\in\{-1,1\}$, unit vectors $v_j$, and thresholds $t_j\in[-1,1]$ such that

$$
F_N(x)=b_0+\frac{M}{N}\sum_{j=1}^{N}s_j H(v_j^\top x-t_j)
$$

satisfies

$$
\|f-F_N\|_{L^2(P)}^2\le\frac{M^2}{N}\le\frac{4C_1^2}{N}.\tag{8.3}
$$

### 8.3. Replacing the halfspaces by sigmoids

For $\lambda>0$, set

$$
\Phi_{N,\lambda}(x)=b_0+\frac{M}{N}\sum_{j=1}^{N}s_j\sigma\bigl(\lambda(v_j^\top x-t_j)\bigr).
$$

This has exactly the form (7.1), with

$$
\alpha_j=\frac{M}{N}s_j,\qquad w_j=\lambda v_j,\qquad\theta_j=-\lambda t_j.
$$

For each fixed neuron, the sigmoid converges to the corresponding halfspace indicator away from the hyperplane $v_j^\top x=t_j$. That hyperplane has Lebesgue measure zero. Bounded convergence therefore gives

$$
\|\Phi_{N,\lambda}-F_N\|_{L^2(P)}\longrightarrow0.
$$

Using (8.3) and the triangle inequality,

$$
\limsup_{\lambda\to\infty}\|f-\Phi_{N,\lambda}\|_{L^2(P)}\le\frac{2C_1}{\sqrt N}.
$$

Since $c>4C_1^2$, the number

$$
\frac{\sqrt c-2C_1}{\sqrt N}
$$

is positive. Choose a finite $\lambda$ large enough that

$$
\|\Phi_{N,\lambda}-F_N\|_{L^2(P)}<\frac{\sqrt c-2C_1}{\sqrt N}.
$$

Then

$$
\|f-\Phi_{N,\lambda}\|_{L^2(P)}^2<\frac{c}{N},
$$

which proves Theorem 7.1.

**Remark 8.2 (Why the theorem allows $c>4C_1^2$).** The halfspace approximation has the bound $4C_1^2/N$. Replacing discontinuous halfspaces by finite-slope sigmoids introduces an arbitrarily small additional $L^2$ error. The strict slack in $c$ absorbs it. This argument does not establish a bound on the slopes or on the parameter magnitudes needed for that replacement.

## 9. Interpreting the quantitative rate

Define the normalized mean squared error

$$
E_N=\frac{1}{|B_1^d|}\int_{B_1^d}|f(x)-\Phi_N(x)|^2\,dx.
$$

The theorem gives

$$
E_N<\frac{c}{N}.
$$

Consequently,

$$
\sqrt{E_N}<\sqrt{\frac{c}{N}}.
$$

Thus the squared error has rate $O(N^{-1})$, while the root mean squared error has rate $O(N^{-1/2})$. For the unnormalized Lebesgue $L^2$ norm,

$$
\|f-\Phi_N\|_{L^2(B_1^d)}<\sqrt{\frac{c|B_1^d|}{N}}.
$$

### 9.1. Choosing a sufficient width

For a target mean squared error $\varepsilon>0$, it is sufficient to take

$$
N\ge\frac{c}{\varepsilon}.
$$

For a target root mean squared error $\varepsilon>0$, it is sufficient to take

$$
N\ge\frac{c}{\varepsilon^2}.
$$

An integer width is obtained by rounding upward. These are sufficient widths from an upper bound, rather than necessary lower bounds.

Multiplying the width by four divides the displayed squared-error bound by four and the root-error bound by two. A particular trained network need not follow these ratios exactly.

### 9.2. Dependence on dimension

The exponent of $N$ in the bound does not explicitly involve $d$. However, the Fourier quantity $C_f$, and hence an admissible $C_1$, can depend on the target and on dimension. The theorem applies with a useful constant when the target’s Barron complexity is controlled. It is not a uniform assertion that every high-dimensional function is easy to approximate.

The number of trainable parameters also grows with the input dimension: each of the $N$ neurons carries a vector in $\mathbb R^d$, in addition to its bias and output weight.

## 10. October 26 lecture: from approximation to generalization

The October 26 lecture proves the Barron approximation rate and then introduces generalization. The central idea is to represent a target as a mixture of bounded basic functions, approximate that mixture by an average of $N$ selected functions, and replace the selected halfspace indicators by sigmoid neurons.

This continuation expands the nine-page lecture and the calculations behind its compressed proof. The Gaussian example and the generalization statements with explicit assumptions are supplementary explanations. References to **Lecture Lemma 1** and **Lecture Lemma 2** below mean the lemmas in the October 26 lecture; they are different from the earlier UAT lemmas.

| Stage | Question answered | Lecture pages |
|---|---|---|
| Bounded-mixture approximation | How accurately can $N$ selected functions approximate a mixture? | 1–3 |
| Fourier-to-halfspace representation | Why does a Barron target belong to the required mixture class? | 4–7 |
| Sigmoid replacement | How does this produce an $N$-neuron network? | 7–8 |
| Generalization | How are training error and population error related? | 8–9 |

The proof of approximation follows this sequence:

$$
f-f(0)
\ \longrightarrow\ \text{a mixture of cosine profiles}
\ \longrightarrow\ \text{a mixture of halfspace indicators}
\ \longrightarrow\ \text{an average of }N\text{ indicators}
\ \longrightarrow\ \text{an }N\text{-neuron sigmoid network}.
$$

Barron's original paper develops this convex-mixture approach in Section IV, particularly pp. 934–935. The calculations below use the lecture's $2\pi$ Fourier convention and its unit-ball setting. [Barron (1993), Section IV](https://pages.cs.wisc.edu/~brecht/cs838docs/93.Barron.Universal.pdf)

### 10.1. Symbols that must be kept separate

| Symbol | Meaning |
|---|---|
| $d$ | Input dimension |
| $N$ | Number of hidden neurons or selected approximation terms |
| $n$ | Number of training examples |
| $C_f$ | Fourier first-moment cost of the target |
| $C_1$ | A fixed upper bound on $C_f$ |
| $B$ | A bound on the norm of each basic function in Lecture Lemma 1 |
| $c$ | A final error constant, chosen larger than $4C_1^2$ |

The letter $B$ in the sampling lemma is a number. The notation $B_1^d$ denotes a set; these are different objects.

### 10.2. What the unit ball and its normalization mean

$$
B_1^d=\{x\in\mathbb R^d:\|x\|_2\le1\}.
$$

The subscript $1$ is the radius, and the superscript $d$ is the dimension, not a power. For $d=1$, this is $[-1,1]$; for $d=2$, it is the filled unit disk; for $d=3$, it is the solid unit ball. Its volume is written $|B_1^d|$.

Let $P$ be the uniform probability measure on this ball. Then

$$
\|u\|_{L^2(P)}^2
=\frac1{|B_1^d|}\int_{B_1^d}|u(x)|^2\,dx.
$$

The integral adds squared values over the ball; division by volume turns that total into an average. In one dimension, it is simply

$$
\|u\|_{L^2(P)}^2=\frac12\int_{-1}^{1}|u(x)|^2\,dx.
$$

## 11. What a Barron function is

In this lecture, a Barron function is a real-valued target whose Fourier transform has a finite weighted first moment:

$$
C_f:=\int_{\mathbb R^d}2\pi|\xi|\,|\widehat f(\xi)|\,d\xi<\infty.
$$

The lecture also assumes $f\in L^1(\mathbb R^d)$ and $\widehat f\in L^1(\mathbb R^d)$. These assumptions allow us to use the continuous representative given by Fourier inversion. The class $\Gamma_{C_1}$ imposes the additional budget $C_f\le C_1$.

The magnitude $|\widehat f(\xi)|$ describes how much of frequency $\xi$ is present. The factor $2\pi|\xi|$ charges more for higher frequencies. A function can contain arbitrarily high frequencies and still satisfy the condition if their magnitudes decay sufficiently quickly.

This is a condition on a function, not a particular formula for a function. It is also more specific than continuity. The reason for this particular condition will become concrete in Section 14: it bounds the absolute coefficients needed in a halfspace representation.

### 11.1. A concrete example: the Gaussian

In one dimension, let

$$
f(x)=e^{-\pi x^2}.
$$

With the Fourier convention of these notes,

$$
\widehat f(\xi)=e^{-\pi\xi^2}.
$$

This transform pair and its differential-equation proof are also presented in Brad Osgood's Stanford EE261 lecture on the Gaussian. [Osgood, EE261, Lecture 7](https://see.stanford.edu/materials/lsoftaee261/transcripts/TheFourierTransformAndItsApplications-Lecture07.html)

Here is the calculation. Write $F(\xi)=\widehat f(\xi)$. Since $f'(x)=-2\pi x f(x)$, differentiation under the integral sign gives

$$
\begin{aligned}
F'(\xi)
&=\int_{\mathbb R}(-2\pi i x)f(x)e^{-2\pi i\xi x}\,dx\\
&=i\int_{\mathbb R}f'(x)e^{-2\pi i\xi x}\,dx\\
&=i(2\pi i\xi)F(\xi)=-2\pi\xi F(\xi).
\end{aligned}
$$

The boundary term in integration by parts vanishes because $f$ decays at both ends of the line. Consequently,

$$
\frac{d}{d\xi}\left(e^{\pi\xi^2}F(\xi)\right)=0,
\qquad F(\xi)=F(0)e^{-\pi\xi^2}.
$$

To compute $F(0)$, put $I=\int_{\mathbb R}e^{-\pi x^2}\,dx$. Squaring and using polar coordinates gives

$$
I^2=\int_{\mathbb R^2}e^{-\pi(x^2+y^2)}\,dx\,dy
=\int_0^{2\pi}\int_0^\infty e^{-\pi r^2}r\,dr\,d\theta=1.
$$

Since $I>0$, $F(0)=I=1$, proving the transform identity. The Gaussian's Barron cost is

$$
\begin{aligned}
C_f
&=\int_{\mathbb R}2\pi|\xi|e^{-\pi\xi^2}\,d\xi\\
&=4\pi\int_0^\infty\xi e^{-\pi\xi^2}\,d\xi=2.
\end{aligned}
$$

Both $f$ and $\widehat f$ are integrable. Thus this Gaussian belongs to $\Gamma_{C_1}$ for every $C_1\ge2$ under the non-strict convention used here.

## 12. Lecture Lemma 1: replacing a mixture by a finite average

### 12.1. Convex combinations and their closure

A convex combination is a weighted average:

$$
f^*=\sum_{j=1}^m\alpha_jh_j,
\qquad \alpha_j\ge0,\qquad\sum_{j=1}^m\alpha_j=1.
$$

For a set $G$ of basic functions, $\operatorname{co}(G)$ is the collection of all finite convex combinations of elements of $G$. Its closure contains all limits of such combinations in the chosen norm. Therefore

$$
f\in\overline{\operatorname{co}}(G)
$$

means that for every $\varepsilon>0$, some finite convex combination $f^*$ satisfies $\|f-f^*\|<\varepsilon$.

For example, $0.3h_1+0.7h_2$ is the average of ten functions obtained by choosing $h_1$ three times and $h_2$ seven times. For arbitrary mixing coefficients, a finite equal-weight average need not match the mixture exactly. Lecture Lemma 1 controls the resulting approximation error.

### 12.2. The precise statement

**Lemma 12.1 (Lecture Lemma 1).** Let $\mathcal H$ be a real Hilbert space, let $G\subset\mathcal H$, and suppose $\|g\|_{\mathcal H}\le B$ for every $g\in G$. If $f\in\overline{\operatorname{co}}(G)$, then for every $N\in\mathbb N$ and every $c>B^2$, there are $g_1,\ldots,g_N\in G$ such that

$$
\left\|f-\frac1N\sum_{i=1}^N g_i\right\|_{\mathcal H}^2<\frac cN.
$$

Repeated selections are allowed. The statement guarantees a good selection; it does not say that every selection works. Linear independence of the functions is not required.

### 12.3. Why probabilities reproduce the original mixture

First suppose $f^*=\sum_j\alpha_jh_j$ is a finite convex combination. Define a random function $X$ by

$$
\mathbb P(X=h_j)=\alpha_j.
$$

Its expectation is exactly the mixture:

$$
\mathbb EX=\sum_j\alpha_jh_j=f^*.
$$

Draw independent copies $X_1,\ldots,X_N$. Their sample average is an equal-weight average of $N$ basic functions. To find a good deterministic average, bound the expected error of these random averages.

### 12.4. Where the factor $1/N$ comes from

Put $Y_i=X_i-f^*$. Then $\mathbb EY_i=0$. Expanding the squared Hilbert norm gives

$$
\begin{aligned}
\mathbb E\left\|\frac1N\sum_iY_i\right\|_{\mathcal H}^2
&=\frac1{N^2}\sum_i\mathbb E\|Y_i\|_{\mathcal H}^2
+\frac1{N^2}\sum_{i\ne j}\mathbb E\langle Y_i,Y_j\rangle_{\mathcal H}.
\end{aligned}
$$

For $i\ne j$, independence and zero means give

$$
\mathbb E\langle Y_i,Y_j\rangle_{\mathcal H}
=\langle\mathbb EY_i,\mathbb EY_j\rangle_{\mathcal H}=0.
$$

There are $N$ remaining diagonal terms, all with the same expectation. Hence

$$
\begin{aligned}
\mathbb E\left\|f^*-\frac1N\sum_iX_i\right\|_{\mathcal H}^2
&=\frac1N\mathbb E\|X-f^*\|_{\mathcal H}^2\\
&=\frac1N\left(\mathbb E\|X\|_{\mathcal H}^2-\|f^*\|_{\mathcal H}^2\right)\\
&\le\frac{B^2}{N}.
\end{aligned}
$$

The negative term is the usual centering correction in a variance identity. At least one realized selection has error no larger than this expectation bound. Calling that selection $g_1,\ldots,g_N$ proves the finite-mixture case.

### 12.5. Why the closure requires a small margin

For general $f\in\overline{\operatorname{co}}(G)$, choose $f^*$ with $\|f-f^*\|_{\mathcal H}<\varepsilon$. The triangle inequality gives

$$
\left\|f-\frac1N\sum_i g_i\right\|_{\mathcal H}
<\varepsilon+\frac B{\sqrt N}.
$$

Since $c>B^2$, choose

$$
0<\varepsilon<\frac{\sqrt c-B}{\sqrt N}.
$$

The resulting norm is less than $\sqrt{c/N}$, proving the lemma. One should not simply set $\varepsilon=0$: the selected functions can depend on $\varepsilon$, and an exact limiting selection need not exist.

## 13. Why Fourier inversion is rewritten using cosines

The purpose of this step is to turn a Barron target into a mixture of bounded basic functions. Fourier inversion first provides a mixture of waves. Their derivatives then provide a controlled representation by halfspace indicators.

### 13.1. Subtracting the reference value

Fourier inversion gives

$$
f(x)=\int\widehat f(\xi)e^{2\pi i\xi^\top x}\,d\xi,
\qquad
f(0)=\int\widehat f(\xi)\,d\xi.
$$

Subtracting yields

$$
f(x)-f(0)=\int\widehat f(\xi)\left(e^{2\pi i\xi^\top x}-1\right)\,d\xi.
$$

The reference value will be supplied by an output bias. The remaining function vanishes at the origin, which lets us recover it by integrating its derivative from zero.

### 13.2. Separating amplitude and phase

Write the complex Fourier coefficient as

$$
\widehat f(\xi)=|\widehat f(\xi)|e^{i\kappa(\xi)}.
$$

The magnitude $|\widehat f(\xi)|$ becomes a nonnegative mixing weight. The phase $\kappa(\xi)$ remains in the wave profile. Its value can be assigned arbitrarily where $\widehat f(\xi)=0$.

Since $f$ is real-valued, taking the real part of the entire integral and using $\operatorname{Re}(e^{iu})=\cos u$ gives

$$
f(x)-f(0)
=\int |\widehat f(\xi)|
\left[\cos(2\pi\xi^\top x+\kappa(\xi))-\cos\kappa(\xi)\right]d\xi.
$$

Individual Fourier coefficients need not be real. Taking real parts here is justified by linearity of the integral and by the real-valued left-hand side.

### 13.3. Reducing each wave to a one-dimensional profile

For $\xi\ne0$, let

$$
v_\xi=\frac\xi{|\xi|},\qquad
h_\xi(s)=\cos(2\pi|\xi|s+\kappa(\xi))-\cos\kappa(\xi).
$$

Then $\xi^\top x=|\xi|v_\xi^\top x$, and

$$
f(x)-f(0)=\int |\widehat f(\xi)|h_\xi(v_\xi^\top x)\,d\xi.
$$

On the unit ball, $|v_\xi^\top x|\le1$. Thus every wave is a function of a single variable $s\in[-1,1]$, even when the input dimension is large. Furthermore,

$$
h_\xi(0)=0,\qquad
h_\xi'(s)=-2\pi|\xi|\sin(2\pi|\xi|s+\kappa(\xi)),
$$

so

$$
|h_\xi'(s)|\le2\pi|\xi|.
$$

The factor $2\pi|\xi|$ in the Barron condition is exactly the derivative bound appearing here. The zero-frequency point contributes no mass to these Lebesgue integrals and can be omitted.

## 14. From cosine profiles to bounded halfspace mixtures

### 14.1. A curve can be assembled from steps

Let $H(s)=\mathbf1_{\{s>0\}}$. For a continuously differentiable function $h$ with $h(0)=0$ and $0\le x\le1$,

$$
\int_0^1h'(t)H(x-t)\,dt
=\int_0^x h'(t)\,dt=h(x).
$$

The indicator switches on only those terms with $t<x$. For example,

$$
x^2=\int_0^1 2t\,H(x-t)\,dt
\qquad(0\le x\le1).
$$

A finite-sum approximation makes the coefficients visible:

$$
h(x)\approx\sum_k
\underbrace{h'(t_k)\Delta t}_{\text{coefficient}}
\underbrace{H(x-t_k)}_{\text{step function}}.
$$

The sum of the absolute coefficients tends to $\int_0^1|h'(t)|\,dt$. Absolute values count the full amount of coefficients used, without cancellation between upward and downward steps.

### 14.2. Why the factor $2$ appears on the unit ball

The one-dimensional coordinate $s=v_\xi^\top x$ can be negative. For $s\in[-1,1]$, both sides of the origin are handled by

$$
h(s)
=\int_0^1h'(t)H(s-t)\,dt
-\int_{-1}^0h'(t)H(t-s)\,dt.
$$

For $s\ge0$, the second term is zero and the first integrates from $0$ to $s$. For $s\le0$, the first is zero and the second equals $-\int_s^0h'(t)\,dt=h(s)$.

Both terms are allowed halfspace functions after substituting $s=v_\xi^\top x$:

$$
H(s-t)=H(v_\xi^\top x-t),\qquad
H(t-s)=H((-v_\xi)^\top x+t).
$$

The absolute coefficient mass needed for one cosine profile is bounded by

$$
\begin{aligned}
\int_{-1}^{1}|h_\xi'(t)|\,dt
&\le\int_{-1}^{1}2\pi|\xi|\,dt\\
&=2\cdot2\pi|\xi|.
\end{aligned}
$$

The leading $2$ is the length of $[-1,1]$. Each wave is used with amplitude $|\widehat f(\xi)|$, so the total absolute coefficient mass in this constructed representation satisfies

$$
\begin{aligned}
M
&=\int_{\mathbb R^d}|\widehat f(\xi)|
\left(\int_{-1}^{1}|h_\xi'(t)|\,dt\right)d\xi\\
&\le2\int_{\mathbb R^d}2\pi|\xi|\,|\widehat f(\xi)|\,d\xi\\
&=2C_f\le2C_1.
\end{aligned}
$$

This is the calculation behind the coefficient budget $2C_1$.

### 14.3. How a coefficient budget becomes a convex mixture

The next step is a rescaling. Suppose first that a finite step representation has the form

$$
u=\sum_j\beta_jH_j,\qquad\sum_j|\beta_j|\le2C_1.
$$

For nonzero coefficients, define

$$
p_j=\frac{|\beta_j|}{2C_1},\qquad
g_j=2C_1\operatorname{sgn}(\beta_j)H_j.
$$

Then $\beta_jH_j=p_jg_j$, $p_j\ge0$, and $\sum_jp_j\le1$. Add the zero function with weight $p_0=1-\sum_jp_j$ to obtain a convex combination. Every $g_j$ has height of absolute value $2C_1$, and the zero function has height zero.

For example, if $C_1=1$,

$$
0.8H_1-0.4H_2
=0.4(2H_1)+0.2(-2H_2)+0.4(0).
$$

The mixing proportions are nonnegative and sum to one. This explains the dictionary

$$
G_{C_1}
=\left\{x\mapsto\gamma H(a^\top x+b):
a\in\mathbb R^d,\ b\in\mathbb R,\ |\gamma|\le2C_1\right\}.
$$

The height bound is a convenient choice of representation obtained by rescaling the coefficients. It does not claim that every possible representation of the function has bounded individual heights.

For the integral representation, the same construction uses the absolute coefficient density divided by $2C_1$ as a mixing measure, with any missing mass assigned to the zero function. Its total mass is $M/(2C_1)\le1$. The atoms are bounded, measurable $L^2(P)$ functions, so their probability-weighted integral belongs to the closed convex hull. One way to see the closure statement is to approximate that integral by finite sample averages, whose expected squared error tends to zero by the variance calculation of Section 12.

This proves the lecture's second lemma:

**Lemma 14.1 (Lecture Lemma 2).** If $f\in\Gamma_{C_1}$, then

$$
f|_{B_1^d}-f(0)\in\overline{\operatorname{co}}(G_{C_1}),
$$

where the closure is in $L^2(P)$.

Section 8 used a related representation integrating each profile from $-1$ and obtained an output constant $b_0$. Here the lecture centers at $0$ and splits the positive and negative intervals, so the output constant is $f(0)$. Both constructions yield the same absolute coefficient bound $2C_f$.

### 14.4. A normalization detail in the handwritten proof

The density written in the lecture as

$$
d\Lambda(\xi)=\frac{2\pi|\xi|\,|\widehat f(\xi)|}{C_1}\,d\xi
$$

has total mass $C_f/C_1\le1$, not necessarily one. To obtain a probability measure, divide by $C_f$ when $C_f>0$, or keep the denominator $C_1$ and assign the missing mass to a zero atom. If $C_f=0$, the centered target is zero and requires no nonzero approximation terms.

## 15. Combining the lemmas and tracking the error constant

### 15.1. Substituting into the sampling lemma

The correspondence is now explicit:

| Sampling lemma ingredient | Barron proof ingredient |
|---|---|
| Target in the closed convex hull | $u=f-f(0)$ |
| Basic functions $G$ | Halfspace functions $G_{C_1}$ |
| Hilbert space $\mathcal H$ | $L^2(P)$ |
| Basic-function norm bound $B$ | $2C_1$ |

Indeed, every $g\in G_{C_1}$ satisfies $|g(x)|\le2C_1$, hence

$$
\|g\|_{L^2(P)}^2
=\frac1{|B_1^d|}\int_{B_1^d}|g(x)|^2\,dx
\le(2C_1)^2.
$$

Fix the desired final constant $c>4C_1^2$. Choose an intermediate constant $c_0$ with

$$
4C_1^2<c_0<c.
$$

Lecture Lemma 1 provides $N$ selected step functions such that

$$
S_N(x)=f(0)+\frac1N\sum_{i=1}^N\gamma_iH(a_i^\top x+b_i)
$$

satisfies

$$
\|f-S_N\|_{L^2(P)}^2<\frac{c_0}{N}.
$$

The numerical constant comes from $B^2=(2C_1)^2=4C_1^2$. It is guaranteed for a suitable selection of terms, not for arbitrary coefficients inserted into the displayed network form.

### 15.2. Replacing indicators by sigmoid neurons

Fix the continuous sigmoid from the earlier sections, with values in $[0,1]$. For a selected nonconstant halfspace,

$$
\sigma(\lambda(a_i^\top x+b_i))\longrightarrow H(a_i^\top x+b_i)
$$

away from its boundary as $\lambda\to\infty$. The boundary is a hyperplane and has zero uniform volume. Dominated convergence gives convergence in $L^2(P)$. Zero atoms can be represented by zero output weights. The Fourier construction uses nonzero directions; any constant component can instead be absorbed into the output bias.

Set

$$
\Phi_{N,\lambda}(x)
=f(0)+\sum_{i=1}^N\frac{\gamma_i}{N}
\sigma\bigl(\lambda(a_i^\top x+b_i)\bigr).
$$

The network parameters are $\alpha_i=\gamma_i/N$, $w_i=\lambda a_i$, and $\theta_i=\lambda b_i$. This has at most $N$ nonzero hidden units, and may be padded to $N$ units using zero output weights.

### 15.3. Where $4C_1^2$ goes when the bound is written as $c/N$

The constant is included inside the larger $c$. It is useful to write $c=4C_1^2+\eta$ with $\eta>0$.

The two approximation errors combine through the triangle inequality for the norm:

$$
\|f-\Phi_{N,\lambda}\|_{L^2(P)}
\le\|f-S_N\|_{L^2(P)}
+\|S_N-\Phi_{N,\lambda}\|_{L^2(P)}.
$$

Choose finite $\lambda$ large enough that

$$
\|S_N-\Phi_{N,\lambda}\|_{L^2(P)}
<\frac{\sqrt c-\sqrt{c_0}}{\sqrt N}.
$$

The right side is positive because $c_0<c$. Consequently,

$$
\|f-\Phi_{N,\lambda}\|_{L^2(P)}
<\frac{\sqrt{c_0}}{\sqrt N}
+\frac{\sqrt c-\sqrt{c_0}}{\sqrt N}
=\sqrt{\frac cN}.
$$

Squaring proves

$$
\boxed{
\frac1{|B_1^d|}\int_{B_1^d}|f(x)-\Phi_{N,\lambda}(x)|^2\,dx
<\frac cN,
\qquad c>4C_1^2.
}
$$

One must use the triangle inequality before squaring. In general, the two squared errors cannot simply be added. For example,

$$
\left(\frac{2C_1}{\sqrt N}+\delta\right)^2
=\frac{4C_1^2}{N}+\frac{4C_1\delta}{\sqrt N}+\delta^2.
$$

The margin in $c$ accommodates these additional terms and the closed-convex-hull approximation.

### 15.4. Reading the rate correctly

For an admissible fixed constant $c$, the bounds are

$$
\text{mean squared error}<\frac cN,
\qquad
\text{root mean squared error}<\sqrt{\frac cN}.
$$

For example, if $C_1=1$, one may choose $c=5$. At $N=100$, the theorem guarantees a network with mean squared error below $0.05$. Choosing $c=4.1$ is also allowed, but requires the extra approximations to be more accurate. The actual error need not equal the upper bound.

The exponent of $N$ does not explicitly depend on $d$. However, $C_f$ may depend on dimension, and an $N$-neuron network has $(d+2)N+1$ parameters with an output bias. The result is most useful when the target's Fourier complexity remains controlled as dimension grows.

## 16. From approximation to learning from data

### 16.1. Population risk and empirical risk

Approximation asks whether a suitable network exists. Learning starts with a sample

$$
S=\{(X_i,Y_i)\}_{i=1}^n,
\qquad (X_i,Y_i)\ \text{i.i.d. from }P_{XY},
$$

and tries to choose a predictor $h$ from a class $\mathcal F$. Here $P_{XY}$ is the unknown joint distribution of inputs and outputs; it need not be the uniform measure $P$ used in the unit-ball approximation theorem.

Given a loss $\ell$, define the population risk

$$
R(h)=\mathbb E_{(X,Y)\sim P_{XY}}[\ell(Y,h(X))]
$$

and the empirical risk

$$
\widehat R_S(h)=\frac1n\sum_{i=1}^n\ell(Y_i,h(X_i)).
$$

The first evaluates performance on the actual distribution. The second evaluates performance on the observed training examples. Empirical risk minimization selects a predictor with small $\widehat R_S(h)$; generalization asks whether its $R(h)$ is also small.

For a data-independent fixed predictor, its training average is an unbiased estimate of its risk. A learned predictor depends on the same data used to compute that average, so this fixed-predictor observation alone does not justify its generalization. This motivates bounds that hold uniformly over the class. [Cornell CS4783, Lecture 3](https://www.cs.cornell.edu/courses/cs4783/2022sp/notes03.pdf)

### 16.2. The two sample sizes answer different questions

| Quantity | Role |
|---|---|
| $N$ hidden neurons | Controls which target shapes the network can approximate |
| $n$ training examples | Controls how accurately training performance can represent population performance |

Increasing $N$ can improve approximation while changing the complexity of the candidate class. Increasing $n$ provides more information about the distribution. The Barron existence theorem alone does not say that a specified training procedure will find the approximating weights.

## 17. Rademacher complexity and generalization

### 17.1. Independent random signs

Rademacher random variables $\varepsilon_1,\ldots,\varepsilon_n$ are independent and satisfy

$$
\mathbb P(\varepsilon_i=1)=\mathbb P(\varepsilon_i=-1)=\frac12.
$$

They create a deliberately random sign pattern with no predictive structure. A flexible function class may still correlate strongly with these signs.

For fixed points $S_z=(z_1,\ldots,z_n)$, define the empirical Rademacher complexity using the convention

$$
\widehat{\mathfrak R}_{S_z}(\mathcal F)
=\mathbb E_\varepsilon\left[
\sup_{h\in\mathcal F}\frac1n\sum_{i=1}^n\varepsilon_i h(z_i)
\right].
$$

The expected complexity also averages over the sample:

$$
\mathfrak R_n(\mathcal F)
=\mathbb E_{S_z}\widehat{\mathfrak R}_{S_z}(\mathcal F).
$$

The supremum is inside the expectation over signs: the function is chosen after the sign pattern is known. This measures the class's ability to fit noise, rather than the correlation of one fixed function with noise. Definitions in the literature may include an absolute value or a factor of two, so constants must be compared using the chosen convention. [Bartlett and Mendelson (2002), Definition 2](https://www.jmlr.org/papers/volume3/bartlett02a/bartlett02a.pdf)

### 17.2. Two simple examples

Suppose the sample points are distinct and the class can assign $h(z_i)=\varepsilon_i$ for every sign pattern while remaining in $[-1,1]$. Then the supremum is

$$
\frac1n\sum_i\varepsilon_i^2=1,
$$

so its empirical Rademacher complexity is $1$.

In contrast, for the class of constant functions $h(z)=a$ with $|a|\le1$,

$$
\begin{aligned}
\widehat{\mathfrak R}_{S_z}(\mathcal F)
&=\frac1n\mathbb E_\varepsilon\left|\sum_i\varepsilon_i\right|\\
&\le\frac1n\sqrt{\mathbb E_\varepsilon\left(\sum_i\varepsilon_i\right)^2}
=\frac1{\sqrt n}.
\end{aligned}
$$

A constant can choose an overall sign, but cannot follow unrelated signs at different sample points. A singleton class containing one fixed function has complexity zero under this convention, because the signed average has expectation zero.

### 17.3. Why the loss class matters

Risk depends on loss values. Define

$$
\mathcal L_{\mathcal F}
=\{(x,y)\mapsto\ell(y,h(x)):h\in\mathcal F\}.
$$

Uniform risk bounds are expressed through the complexity of this class. Bounds for predictor complexity can be transferred to the loss class under appropriate Lipschitz and range assumptions. A complexity bound for predictions does not automatically give the same numerical bound for every loss.

### 17.4. A generalization bound with explicit assumptions

Assume i.i.d. data and $0\le\ell(y,h(x))\le1$ for every candidate and every example. Assume the suprema are measurable, as is automatic for a countable class. With the Rademacher convention above, a standard one-sided uniform bound is: with probability at least $1-\delta$,

$$
\boxed{
R(h)\le\widehat R_S(h)
+2\mathfrak R_n(\mathcal L_{\mathcal F})
+\sqrt{\frac{\log(1/\delta)}{2n}}
\qquad\text{for every }h\in\mathcal F.
}
$$

The bounded-loss condition and the confidence parameter are essential to this statement. For unbounded squared loss, additional assumptions or a different theorem are needed. Generalization results of this type are developed in Bartlett and Mendelson; the convention and constants here are made explicit through the derivation below. [Bartlett and Mendelson (2002), Section 2](https://www.jmlr.org/papers/volume3/bartlett02a/bartlett02a.pdf)

To see the two ingredients, write $P_{XY}\ell=\mathbb E\ell(Z)$ for the joint data distribution and $P_n\ell=n^{-1}\sum_i\ell(Z_i)$, and set

$$
A(S)=\sup_{\ell\in\mathcal L_{\mathcal F}}(P_{XY}\ell-P_n\ell).
$$

An independent sample $S'$ and random signs give the symmetrization calculation

$$
\begin{aligned}
\mathbb E_S A(S)
&\le\mathbb E_{S,S'}\sup_{\ell\in\mathcal L_{\mathcal F}}
\frac1n\sum_i\bigl(\ell(Z_i')-\ell(Z_i)\bigr)\\
&=\mathbb E_{S,S',\varepsilon}\sup_{\ell\in\mathcal L_{\mathcal F}}
\frac1n\sum_i\varepsilon_i\bigl(\ell(Z_i')-\ell(Z_i)\bigr)\\
&\le2\mathfrak R_n(\mathcal L_{\mathcal F}).
\end{aligned}
$$

The equality uses the fact that exchanging $Z_i$ and $Z_i'$ does not change their joint distribution. The last inequality separates the supremum of a sum into a sum of suprema. Replacing one training example changes $A(S)$ by at most $1/n$. McDiarmid's inequality therefore adds $\sqrt{\log(1/\delta)/(2n)}$ to its expectation bound. [Sutherland, CPSC 532D, Section 4](https://www.cs.ubc.ca/~dsuth/532D/23w1/notes/5-rademacher.pdf)

This displayed result is one-sided and uses expected Rademacher complexity. A two-sided absolute deviation or a bound using empirical complexity requires its corresponding confidence terms; these versions should not be interchanged without checking constants.

### 17.5. A concrete source of a $Q/\sqrt n$ rate

Consider linear predictors

$$
\mathcal F_Q=\{x\mapsto w^\top x:\|w\|_2\le Q\},
\qquad \|x_i\|_2\le1.
$$

Then

$$
\begin{aligned}
\widehat{\mathfrak R}_S(\mathcal F_Q)
&=\frac Qn\mathbb E_\varepsilon\left\|\sum_i\varepsilon_i x_i\right\|_2\\
&\le\frac Qn\sqrt{\mathbb E_\varepsilon\left\|\sum_i\varepsilon_i x_i\right\|_2^2}\\
&=\frac Qn\sqrt{\sum_i\|x_i\|_2^2}
\le\frac Q{\sqrt n}.
\end{aligned}
$$

The middle equality again follows because independent random signs cancel the cross terms. The final inequality uses both the parameter-norm constraint and the input-norm constraint. [Sutherland, CPSC 532D, Section 3.2](https://www.cs.ubc.ca/~dsuth/532D/23w1/notes/5-rademacher.pdf)

This is a fully specified example of the rate suggested at the end of the lecture. Extending it to a neural-network class requires appropriate constraints on the network and its base functions; arbitrary unconstrained network coefficients do not give this bound. Structural rules for convex combinations and norm-controlled classes are discussed in [Cornell CS4783, Lecture 6](https://www.cs.cornell.edu/courses/cs4783/2022sp/notes06.pdf).

## 18. Connections and points to check

### 18.1. Approximation complexity and statistical complexity

| Quantity | What it describes |
|---|---|
| Target Fourier cost $C_f$ | The coefficient budget in a representation of one target function |
| Rademacher complexity of $\mathcal F$ | The ability of an entire candidate class to correlate with random sample signs |
| Approximation error | How closely a suitable network in the class can match the target |
| Generalization gap | The difference between population and training performance |

These objects are connected in learning theory, but are not interchangeable. A small target Barron cost supplies a good approximating network. Statistical control also requires a suitably constrained class and enough data, and optimization remains the task of finding useful parameters.

### 18.2. Clarifications to the handwritten argument

- **Normalization of the Fourier mixing measure:** division by $C_1$ gives mass $C_f/C_1\le1$. Normalize by the actual mass or add a zero atom.
- **The passage from cosines to halfspaces:** the derivative bound and the two intervals $[-1,0]$ and $[0,1]$ establish the coefficient budget $2C_f$. A Fourier average of cosines alone does not yet prove the halfspace-mixture claim.
- **Individual heights versus total coefficients:** $|\gamma|\le2C_1$ describes the bounded dictionary after rescaling; it follows from constructing a convex mixture using the total budget.
- **The error constant:** $(2C_1)^2=4C_1^2$. Writing $c/N$ retains that dependence through the requirement $c>4C_1^2$.
- **Norm and squared norm:** $1/N$ controls mean squared error; its square root scales as $1/\sqrt N$.
- **Probability measure versus volume:** dividing by $|B_1^d|$ is required when converting a volume integral into the uniform mean used here.
- **Generalization inequalities:** state the loss range, sampling assumptions, confidence level, and Rademacher normalization. The final schematic inequality in the lecture needs these details to become a precise theorem.

### 18.3. Questions for checking the proof

1. Why does drawing $h_j$ with probability $\alpha_j$ reproduce the convex combination as an expectation?
2. Why do cross terms vanish for independent centered random functions?
3. Why is the remaining squared-error bound proportional to $N/N^2=1/N$?
4. Why does subtracting $f(0)$ make the derivative-to-step representation convenient?
5. Where does the derivative factor $2\pi|\xi|$ come from?
6. Why does integrating its bound over $[-1,1]$ introduce a factor of two?
7. How do absolute coefficient weights become nonnegative convex weights?
8. Why must the triangle inequality be applied to the root mean squared error before squaring?
9. Which assumptions allow a risk bound to use Rademacher complexity?
10. Why do $N$ and $n$ represent different resources?

## References

1. Cybenko, G. (1989). **Approximation by superpositions of a sigmoidal function.** *Mathematics of Control, Signals and Systems*, **2**, 303–314. [https://doi.org/10.1007/BF02551274](https://doi.org/10.1007/BF02551274)

2. Barron, A. R. (1993). **Universal approximation bounds for superpositions of a sigmoidal function.** *IEEE Transactions on Information Theory*, **39**(3), 930–945. [https://doi.org/10.1109/18.256500](https://doi.org/10.1109/18.256500)

3. Bartlett, P. L., and Mendelson, S. (2002). **Rademacher and Gaussian Complexities: Risk Bounds and Structural Results.** *Journal of Machine Learning Research*, **3**, 463–482. [Journal page](https://www.jmlr.org/papers/v3/bartlett02a.html), [full text](https://www.jmlr.org/papers/volume3/bartlett02a/bartlett02a.pdf). Definition 2 uses a different normalization from Section 17 above; constants must be adjusted accordingly.

4. Osgood, B. **The Fourier Transform and Its Applications, EE261, Lecture 7.** Stanford Engineering Everywhere. [Lecture transcript](https://see.stanford.edu/materials/lsoftaee261/transcripts/TheFourierTransformAndItsApplications-Lecture07.html). Supports the Gaussian transform pair and its differential-equation derivation.

5. Sutherland, D. J. (2023). **Rademacher Complexity.** CPSC 532D, University of British Columbia. [Lecture notes](https://www.cs.ubc.ca/~dsuth/532D/23w1/notes/5-rademacher.pdf). Sections 3.2 and 4 treat bounded-norm linear classes and concentration.

6. Cornell University (2022). **CS4783 Lecture 3: ERM, Uniform Convergence and Rademacher Complexity.** [Lecture notes](https://www.cs.cornell.edu/courses/cs4783/2022sp/notes03.pdf).

7. Cornell University (2022). **CS4783 Lecture 6: Properties of Rademacher Complexity and Examples.** [Lecture notes](https://www.cs.cornell.edu/courses/cs4783/2022sp/notes06.pdf).

8. **October 26 lecture, handwritten notes** (nine pages, supplied as `1026_lec.pdf`). Primary lecture source for Sections 10–18. The detailed derivations explain the lecture's notation and fill in its proof steps; the external references above support the corresponding approximation and learning-theory results.
