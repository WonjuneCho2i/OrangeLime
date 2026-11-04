---
title: Universal Approximation and Barron Approximation
tags:
  - deep-learning
  - approximation-theory
  - neural-networks
  - functional-analysis
  - fourier-analysis
---

> **Definitions, Proofs, and Interpretation**  
> Expanded English Notes Based on `lecture1021.pdf`

## Abstract

These notes explain why a neural network with one hidden layer and a continuous sigmoidal activation can approximate every continuous function on a compact cube. The proof proceeds through continuous linear functionals, signed measures, and the discriminatory property of sigmoids. The second part introduces Fourier-based Barron classes and proves an $O(N^{-1})$ mean squared approximation bound for networks with $N$ hidden neurons.

**Source and scope.** The source is a nine-page handwritten lecture, cited by PDF page number below. Definitions and arguments are reorganized for readability. Proofs omitted in the lecture are supplied where useful; the detailed quantitative proof in Section 8 is a supplementary derivation. Hahn–Banach, Riesz representation, and uniqueness of the Fourier transform of finite measures are used as standard background theorems.

**Conventions.** We write $I^n=[0,1]^n$, use the uniform norm on $C(I^n)$, and work with real-valued targets unless a Fourier-space definition explicitly allows complex values. For the quantitative theorem we explicitly allow a scalar output bias, which does not count as a hidden neuron. This makes the architecture precise where the handwritten statement leaves it unspecified.

## 1. The approximation question

*Lecture: pp. 1 and 3.*

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

The motivating diagram in the lecture connects three different issues:

- **Approximation:** whether the chosen function class can represent the target sufficiently accurately.
- **Generalization:** whether performance measured on finite data reflects performance on the underlying input distribution.
- **Optimization:** whether a learning procedure finds suitable parameters.

The theorems developed here concern approximation. An existence theorem for good parameters does not itself specify a training algorithm or a sample size.

## 2. Function spaces and density

*Lecture: p. 2.*

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

*Lecture: pp. 1, 4, and 5.*

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

*Lecture: pp. 3 and 4.*

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

**Supplementary proof.** Suppose $\mu\in M(I^n)$ satisfies the integral condition in (4.1). Fix $w\in\mathbb R^n$, $\theta\in\mathbb R$, and $a\in\mathbb R$. For every $\lambda>0$,

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

*Lecture: pp. 4–7.*

### 5.1. A separation lemma

**Lemma 5.1 (A non-dense subspace has a nonzero annihilator).** Let $W$ be a non-dense linear subspace of a real normed space $X$. There exists $L\in X^*$ such that

$$
L\ne0,\qquad L(w)=0\quad(w\in W).
$$

**Supplementary proof using Hahn–Banach.** Set $M=\overline W$. Choose $x_0\in X\setminus M$. Since $M$ is closed,

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

*Lecture: pp. 7 and 8.*

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

### 6.2. The Barron class used in the lecture

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

For $s\ge0$, the extension-based quantity appearing in the lecture is

$$
\|f\|_{B^s(\Omega)}=\inf_{\substack{\widetilde f|_\Omega=f\\\widetilde f\ \mathrm{admissible}}}\int_{\mathbb R^d}(1+|\xi|)^s|\widehat{\widetilde f}(\xi)|\,d\xi.\tag{6.3}
$$

Here admissible extensions have a well-defined Fourier transform; one may work with $\widetilde f\in L^1(\mathbb R^d)$ with integrable Fourier transform. Define $B^s(\Omega)$ by finiteness of this quantity.

Different extensions agree inside $\Omega$ but can have different Fourier costs. The infimum assigns the smallest possible cost to the function on the region of interest. It does not assert that an optimal extension exists.

For example, abruptly extending a nonzero boundary value by zero can create a jump. A smoother continuation outside the domain can have a smaller high-frequency cost. This explains the extension sketch on lecture p. 8.

When $s=1$, the weight separates as

$$
\int_{\mathbb R^d}(1+|\xi|)|\widehat{\widetilde f}(\xi)|\,d\xi
=\|\widehat{\widetilde f}\|_{L^1}+\int_{\mathbb R^d}|\xi|\,|\widehat{\widetilde f}(\xi)|\,d\xi.
$$

Thus the $B^1$ condition controls both Fourier integrability and the first frequency moment. It is closely related to the conditions defining $\Gamma_{C_1}$, with the displayed normalization differences.

## 7. A quantitative Barron approximation theorem

*Lecture: pp. 8 and 9.*

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

**Remark 7.2 (Architecture convention).** The scalar $b_0$ is an output bias, not an extra hidden neuron. It is explicit in the supplementary proof. The lecture’s quantitative statement specifies a network with $N$ neurons without detailing this bias. The UAT proved earlier uses the original form (1.1), so its statement is unaffected by this convention.

## 8. Supplementary proof of the Barron bound

The lecture states the quantitative estimate without a proof. The derivation here makes its $N^{-1}$ rate and the constant $4C_1^2$ explicit. It has three steps: represent the target using halfspace indicators, approximate their integral by a finite average, and replace the indicators by steep sigmoids.

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

*Lecture: p. 9, with explanatory consequences.* Define the normalized mean squared error

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

## 10. Comparison and review

| | Universal approximation | Barron approximation |
|---|---|---|
| **Question** | Can every target be approximated arbitrarily well? | How does error depend on width? |
| **Targets** | Every $f\in C(I^n)$ | Targets with controlled Fourier Barron complexity |
| **Error** | Uniform error | Normalized mean squared error |
| **Conclusion** | For every $\varepsilon>0$, a finite network exists | For every $N$, a network exists with error $<c/N$ |
| **Main tools** | Separation, signed measures, discrimination | Fourier representation and finite-average approximation |

Uniform and mean squared errors should not be interchanged. For a probability measure $P$, a uniform bound implies

$$
\|f-G\|_{L^2(P)}\le\|f-G\|_\infty.
$$

A small mean squared error alone does not imply a small error at every point.

### 10.1. Questions for checking understanding

1. **Why allow every finite width?** This permits the width to depend on the desired accuracy and makes the network union closed under addition.
2. **Where is continuity of $\sigma$ used in the UAT argument?** It places every network function inside $C(I^n)$.
3. **What does non-density produce?** A nonzero continuous linear functional that vanishes on the network space.
4. **Why use Riesz representation?** It converts that functional into a signed measure, to which the discriminatory condition applies.
5. **Why must the annihilating measure be nonzero?** Only then does the conclusion $\mu=0$ create a contradiction.
6. **Why are signed measures needed?** General continuous linear functionals need not be positive.
7. **What is the role of the sigmoid limits?** Steep sigmoids converge to halfspace indicators away from their boundary hyperplanes.
8. **What does the UAT say about a prescribed width?** It supplies no quantitative width bound in terms of the target tolerance.
9. **What decays like $1/N$ in the Barron theorem?** The normalized mean squared error. Its square root decays like $1/\sqrt N$.
10. **Why take an infimum over extensions in $B^s(\Omega)$?** Different continuations outside $\Omega$ have different Fourier costs; the definition uses the smallest attainable limiting cost.

### 10.2. Source notes and clarifications

The lecture’s page references are PDF page numbers, not separately printed page labels.

- The lecture identifies the UAT with Cybenko (1989). The present document derives the stated version rather than quoting an external paper.
- The measure-annihilator statement on p. 4 is read with the necessary condition $\mu\ne0$, which follows from its proof.
- The final identity on p. 5 is written consistently as $L(h)=\int h\,d\mu=0$ for $h\in W$.
- The uniform norm and the convention $I^n=[0,1]^n$ are made explicit.
- Theorems on signed measures use the finite regular Borel measures appropriate to Riesz representation on a compact metric space.
- Fourier inversion is interpreted using the continuous representative when both $f$ and $\widehat f$ are integrable.
- The output bias and the supplementary proof of the quantitative bound are stated explicitly; neither is detailed in the handwritten quantitative theorem.
