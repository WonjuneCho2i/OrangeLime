---
title: Constrained optimization
date: 2026-09-29
tags:
  - math
  - optimization theory
  - constrained optimization
  - Lagrangian duality
  - Karush-Kuhn-Tucker conditions
  - support vector machine
---

# Constrained optimization

이번 글에서는 제약조건이 있는 최적화 문제를 다룬다. 특히 **Lagrangian function**, **Lagrangian duality**, **Karush-Kuhn-Tucker (KKT) conditions**가 각각 어떤 역할을 하는지 살펴보고, 이를 hard-margin support vector machine(SVM)에 적용한다.

핵심 흐름은 다음과 같다.

$$
\text{constrained primal problem}
\;\longrightarrow\;
\text{Lagrangian}
\;\longrightarrow\;
\text{dual problem}
\;\longrightarrow\;
\text{KKT conditions}.
$$

SVM은 이 흐름이 특히 잘 드러나는 예시이다. dual을 풀면 support vector가 자동으로 선택되고, 그 결과로 원래 classifier를 복원할 수 있다.

---

## 1. General form of a constrained optimization problem

다음과 같은 minimization problem을 생각하자.

$$
\begin{aligned}
\text{minimize}\quad & f(x)\\
\text{subject to}\quad & g_i(x)\le 0, \qquad i=1,\dots,m,\\
& h_j(x)=0, \qquad j=1,\dots,\ell.
\end{aligned}
$$

여기서 $x\in\mathbb R^d$는 optimization variable이고, $f(x)$는 목적함수이다. $g_i(x)\le0$는 inequality constraint, $h_j(x)=0$는 equality constraint이다.

제약조건을 모두 만족하는 점들의 집합을 feasible set이라고 하며,

$$
C=
\{x: g_i(x)\le0,\ i=1,\dots,m,\ h_j(x)=0,\ j=1,\dots,\ell\}
$$

로 쓴다. 원래 문제의 최적값은

$$
p^\star=\inf_{x\in C}f(x)
$$

이다.

제약이 없다면 미분가능한 함수의 최적점을 찾을 때 $\nabla f(x)=0$을 이용할 수 있다. 하지만 constrained problem에서는 최적점이 feasible region의 경계에 놓일 수 있다. 이때는 $\nabla f(x)$가 0일 필요가 없고, 목적함수의 기울기와 활성화된 제약조건의 기울기가 서로 균형을 이룬다. Lagrangian과 KKT conditions는 이 균형을 표현하는 도구이다.

---

## 2. Lagrangian function

각 inequality constraint에 multiplier $\lambda_i$를, 각 equality constraint에 multiplier $\mu_j$를 붙인다. Lagrangian function은

$$
\begin{aligned}
L(x,\lambda,\mu)
&=f(x)+\lambda^\top g(x)+\mu^\top h(x)\\
&=f(x)+\sum_{i=1}^m\lambda_i g_i(x)
+\sum_{j=1}^\ell\mu_j h_j(x).
\end{aligned}
$$

이다.

여기서 inequality multiplier는 반드시

$$
\lambda_i\ge0
$$

이어야 한다. 반면 equality multiplier $\mu_j$에는 부호 제약이 없다.

그 이유는 feasible한 $x$에 대해

$$
g_i(x)\le0,\qquad h_j(x)=0
$$

이므로, $\lambda\ge0$일 때

$$
L(x,\lambda,\mu)
=f(x)+\sum_i\lambda_i g_i(x)+\sum_j\mu_jh_j(x)
\le f(x)
$$

가 되기 때문이다. 즉, feasible point에서 Lagrangian은 원래 목적함수의 값을 넘지 않는다.

또 다른 관점에서, 부등식 제약 하나 $g_i(x)\le0$는

$$
\sup_{\lambda_i\ge0}\lambda_i g_i(x)
=
\begin{cases}
0, & g_i(x)\le0,\\
+\infty, & g_i(x)>0
\end{cases}
$$

로 표현할 수 있다. 제약을 만족하면 multiplier를 $0$으로 두어 값이 $0$이 되고, 제약을 위반하면 multiplier를 무한히 키워 penalty를 무한대로 만들 수 있다.

따라서 primal problem은 다음의 saddle-point 형태로도 볼 수 있다.

$$
\inf_x\sup_{\lambda\ge0,\mu}L(x,\lambda,\mu).
$$

---

## 3. Lagrangian duality

Lagrangian에서 먼저 $x$에 대해 최솟값을 취한 함수를 Lagrangian dual function이라고 한다.

$$
q(\lambda,\mu)
=\inf_x L(x,\lambda,\mu).
$$

그러면 dual problem은

$$
\begin{aligned}
\text{maximize}\quad & q(\lambda,\mu)\\
\text{subject to}\quad & \lambda\ge0
\end{aligned}
$$

가 된다. dual의 최적값을 $d^\star$라고 하자.

고정된 $x$에 대해 $L(x,\lambda,\mu)$는 $(\lambda,\mu)$에 대한 affine function이다. 그리고 $q(\lambda,\mu)$는 그러한 affine functions의 pointwise minimum이다. 따라서 원래 $f$, $g_i$, $h_j$가 어떤 형태이든 $q$는 항상 concave하다.

### 3.1 Weak duality

모든 feasible $x$와 모든 $\lambda\ge0$에 대해

$$
f(x)\ge q(\lambda,\mu)
$$

가 성립한다. 따라서

$$
\boxed{
p^\star
=\inf_{x\in C}f(x)
\ge
\sup_{\lambda\ge0,\mu}q(\lambda,\mu)
=d^\star
}
$$

이다. 이것이 weak duality이다.

동일한 사실을 minimax inequality로 쓰면,

$$
\sup_{\lambda\ge0,\mu}\inf_xL(x,\lambda,\mu)
\le
\inf_x\sup_{\lambda\ge0,\mu}L(x,\lambda,\mu)
$$

가 된다. 왼쪽이 dual problem, 오른쪽이 primal problem이다.

### 3.2 Strong duality

일반적으로는 $d^\star<p^\star$일 수 있다. 하지만 목적함수와 inequality constraints가 convex이고 equality constraints가 affine이며 Slater condition이 만족되면,

$$
\boxed{d^\star=p^\star}
$$

가 성립한다. 이를 strong duality라고 한다.

Slater condition은 대략적으로 말해, affine이 아닌 convex inequality constraints를 모두 strict하게 만족하는 feasible point가 존재한다는 조건이다. convex optimization에서 strong duality는 primal과 dual을 같은 최적값을 향하는 두 관점으로 만들어 준다.

---

## 4. KKT conditions

KKT conditions는 constrained problem의 최적해가 만족해야 하는 조건들을 모아 둔 것이다. 다음 convex problem을 생각하자.

$$
\begin{aligned}
\text{minimize}\quad & f(x)\\
\text{subject to}\quad & g_i(x)\le0, \qquad i=1,\dots,m,\\
& h_j(x)=0, \qquad j=1,\dots,\ell.
\end{aligned}
$$

적절한 constraint qualification, 예를 들어 Slater condition이 성립한다고 하자. 그러면 최적해 $x^\star$와 multipliers $(\lambda^\star,\mu^\star)$는 다음을 만족한다.

### 4.1 Primal feasibility

$$
g_i(x^\star)\le0,
\qquad
h_j(x^\star)=0.
$$

원래 제약을 실제로 만족해야 한다.

### 4.2 Dual feasibility

$$
\lambda_i^\star\ge0.
$$

부등식 제약의 multiplier는 음수가 될 수 없다.

### 4.3 Stationarity

$$
\nabla f(x^\star)
+\sum_{i=1}^m\lambda_i^\star\nabla g_i(x^\star)
+\sum_{j=1}^\ell\mu_j^\star\nabla h_j(x^\star)
=0.
$$

이는 Lagrangian을 $x$에 대해 미분한 조건,

$$
\nabla_xL(x^\star,\lambda^\star,\mu^\star)=0
$$

과 같다. unconstrained optimization의 $\nabla f(x^\star)=0$이 제약조건까지 포함하도록 확장된 모습이다.

### 4.4 Complementary slackness

$$
\lambda_i^\star g_i(x^\star)=0,
\qquad i=1,\dots,m.
$$

각 inequality constraint마다 다음 둘 중 하나가 성립한다.

$$
\begin{cases}
g_i(x^\star)<0 &\Rightarrow \lambda_i^\star=0,\\
\lambda_i^\star>0 &\Rightarrow g_i(x^\star)=0.
\end{cases}
$$

즉, 느슨한 제약은 최적해를 직접 밀지 않으며, 양의 multiplier를 가진 제약은 최적해에서 정확히 활성화되어 있다.

convex problem에서는 feasible한 점과 multipliers가 KKT conditions를 만족하면, 그 점은 전역 최적해이다. KKT conditions는 최적성을 검증하는 도구일 뿐 아니라, 실제 알고리즘이 어떤 제약이 활성화되어야 하는지 찾아가는 기준이 된다.

---

## 5. Hard-margin support vector machine

SVM은 constrained optimization과 duality가 실제로 어떻게 쓰이는지 보여 주는 대표적인 예시이다.

훈련 데이터가

$$
(x_i,y_i),\qquad i=1,\dots,N,
$$

로 주어졌다고 하자. 여기서

$$
x_i\in\mathbb R^d,
\qquad
y_i\in\{-1,+1\}.
$$

선형 classifier의 score function은

$$
f(x)=w^\top x+b
$$

이고, prediction은

$$
\hat y(x)=\operatorname{sign}(w^\top x+b)
$$

로 한다.

결정 경계는

$$
H_0:\quad w^\top x+b=0
$$

이다. $w$는 이 hyperplane에 수직인 normal vector이다.

### 5.1 Margin과 primal problem

동일한 decision boundary는 $(w,b)$를 양의 상수배해도 바뀌지 않는다. 이 scale freedom을 이용해 가장 가까운 데이터의 functional margin을 $1$로 정규화하면,

$$
y_i(w^\top x_i+b)\ge1,
\qquad i=1,\dots,N
$$

가 된다.

두 support planes는

$$
H_+: w^\top x+b=1,
\qquad
H_-: w^\top x+b=-1
$$

이며, 그 사이의 전체 폭은

$$
\frac{2}{\|w\|_2}
$$

이다. 따라서 최대 margin classifier는 다음 primal problem으로 쓸 수 있다.

$$
\begin{aligned}
\text{minimize}\quad & \frac12\|w\|_2^2\\
\text{subject to}\quad & y_i(w^\top x_i+b)\ge1,
\qquad i=1,\dots,N.
\end{aligned}
$$

목적함수는 convex이고 제약은 affine이다. 또한 데이터가 strictly linearly separable하면 strict feasibility가 가능하므로 strong duality를 적용할 수 있다.

---

## 6. Deriving the SVM dual

SVM의 제약을

$$
g_i(w,b)=1-y_i(w^\top x_i+b)\le0
$$

로 쓴다. 각 제약에 multiplier $\alpha_i\ge0$를 붙이면 Lagrangian은

$$
L(w,b,\alpha)
=\frac12\|w\|_2^2
+\sum_{i=1}^N
\alpha_i\left[1-y_i(w^\top x_i+b)\right]
$$

가 된다.

dual function을 구하려면 $w,b$에 대해 $L$을 최소화한다. 먼저 $w$에 대해 미분하면,

$$
\nabla_wL
=w-\sum_{i=1}^N\alpha_i y_i x_i=0.
$$

따라서

$$
\boxed{
w=\sum_{i=1}^N\alpha_i y_i x_i
}.
$$

다음으로 $b$에 대해 미분하면,

$$
\frac{\partial L}{\partial b}
=-\sum_{i=1}^N\alpha_i y_i=0.
$$

즉,

$$
\boxed{
\sum_{i=1}^N\alpha_i y_i=0
}.
$$

이 조건이 만족되지 않으면 $b$는 제한 없는 변수이므로 $\inf_{w,b}L$이 $-\infty$가 된다. 따라서 이 식은 dual의 equality constraint로 남는다.

$w$를 제거하면 hard-margin SVM dual은

$$
\begin{aligned}
\text{maximize}\quad
& \sum_{i=1}^N\alpha_i
-\frac12\sum_{i=1}^N\sum_{j=1}^N
\alpha_i\alpha_jy_iy_jx_i^\top x_j\\
\text{subject to}\quad
& \alpha_i\ge0,\qquad i=1,\dots,N,\\
& \sum_{i=1}^N\alpha_i y_i=0
\end{aligned}
$$

이다.

이 문제는 concave quadratic maximization problem이다. 데이터는 오직 inner product $x_i^\top x_j$를 통해서만 등장한다.

equality constraint는 계수 하나를 제거해서 처리할 수도 있다. 예를 들어 $y_N\in\{-1,+1\}$이면,

$$
\alpha_N
=-\frac{1}{y_N}\sum_{i=1}^{N-1}\alpha_i y_i.
$$

다만 원래의 조건 $\alpha_N\ge0$는 남은 변수들에 대한 새로운 linear inequality로 바뀐다. 실제 SVM solver는 이 제약을 보존하면서 dual objective를 증가시키도록 $\alpha$를 반복 갱신한다.

---

## 7. SVM KKT conditions and support vectors

SVM에 KKT conditions를 적용하면 다음과 같다.

$$
\begin{aligned}
&y_i(w^\top x_i+b)\ge1
&&\text{(primal feasibility)},\\
&\alpha_i\ge0
&&\text{(dual feasibility)},\\
&w=\sum_i\alpha_i y_ix_i,
\qquad
\sum_i\alpha_i y_i=0
&&\text{(stationarity)},\\
&\alpha_i\left[1-y_i(w^\top x_i+b)\right]=0
&&\text{(complementary slackness)}.
\end{aligned}
$$

상보성 조건은 support vector의 의미를 설명한다.

$$
\alpha_i>0
\quad\Rightarrow\quad
y_i(w^\top x_i+b)=1.
$$

즉, 양의 $\alpha_i$를 가진 데이터는 정확히 margin boundary 위에 있다. 반대로

$$
y_i(w^\top x_i+b)>1
\quad\Rightarrow\quad
\alpha_i=0.
$$

마진보다 바깥에 충분히 멀리 있는 점은

$$
w=\sum_i\alpha_i y_ix_i
$$

를 형성하는 데 직접 기여하지 않는다.

따라서 support vector는 미리 선택하는 점이 아니다. 모든 훈련 점에 대해 $\alpha_i$를 두고 dual problem을 풀었을 때, 최종적으로 $\alpha_i>0$으로 남은 점이 support vector가 된다.

KKT 조건을 점별로 보면 다음과 같이 정리할 수 있다.

$$
\begin{cases}
\alpha_i=0 &\Rightarrow y_i f(x_i)\ge1,\\
\alpha_i>0 &\Rightarrow y_i f(x_i)=1.
\end{cases}
$$

여기서 첫 번째 행에서 $y_if(x_i)<1$이면 primal feasibility를 위반한다. 두 번째 행에서 $y_if(x_i)>1$이면 complementary slackness를 위반한다. 실제 algorithm은 이러한 KKT 위반을 줄이도록 multipliers를 조정한다.

---

## 8. Recovering the classifier from the dual solution

dual problem을 풀어 $\alpha^\star$를 얻으면, 먼저

$$
w^\star=\sum_{i=1}^N\alpha_i^\star y_i x_i
$$

로 $w^\star$를 복원한다.

그다음 $\alpha_s^\star>0$인 support vector $x_s$ 하나를 고른다. 이 점은 margin 위에 있으므로

$$
y_s(w^{\star\top}x_s+b^\star)=1.
$$

$y_s^2=1$을 이용하면,

$$
\boxed{
b^\star=y_s-w^{\star\top}x_s
}.
$$

실제 수치 계산에서는 여러 support vector에서 이 값을 구해 평균내면 오차를 줄일 수 있다.

새 점 $x$에 대한 prediction은

$$
\begin{aligned}
\hat y(x)
&=\operatorname{sign}(w^{\star\top}x+b^\star)\\
&=\operatorname{sign}
\left(
\sum_{i=1}^N\alpha_i^\star y_i x_i^\top x+b^\star
\right).
\end{aligned}
$$

여기서 $\alpha_i^\star=0$인 항은 사라진다. 즉, prediction에도 support vector만 직접 남는다.

---

## 9. A two-point example

1-dimensional data 두 개를 생각하자.

$$
(x_1,y_1)=(-1,-1),
\qquad
(x_2,y_2)=(1,+1).
$$

primal constraints는

$$
w-b\ge1,
\qquad
w+b\ge1
$$

이다. 따라서

$$
w\ge1+|b|.
$$

$w^2/2$를 최소화하려면

$$
w^\star=1,
\qquad
b^\star=0
$$

이다. primal optimal value는

$$
p^\star=\frac12.
$$

dual equality constraint는

$$
-\alpha_1+\alpha_2=0.
$$

따라서 $\alpha_1=\alpha_2=a\ge0$로 둘 수 있고,

$$
w=2a.
$$

dual objective는

$$
q(a)=2a-\frac12(2a)^2=2a-2a^2.
$$

미분하면

$$
q'(a)=2-4a=0
$$

이므로

$$
a^\star=\frac12.
$$

따라서

$$
\alpha_1^\star=\alpha_2^\star=\frac12,
\qquad
q^\star=\frac12=p^\star.
$$

두 multiplier가 모두 양수이므로, 두 점 모두 support vector이다.

---

## 10. Summary

constrained optimization에서는 단순히 목적함수의 gradient를 $0$으로 두는 것만으로 충분하지 않다. feasible region의 경계에서 최적점이 나올 수 있기 때문이다.

Lagrangian은 제약을 multipliers와 함께 목적함수 안에 넣고, dual function은 그러한 Lagrangian으로부터 primal optimal value의 lower bound를 만든다. weak duality는 항상 성립하며, convexity와 Slater condition 같은 조건 아래에서는 strong duality가 성립한다.

KKT conditions는 primal feasibility, dual feasibility, stationarity, complementary slackness를 함께 요구한다. convex problem에서는 이 조건들이 최적해를 완전히 특징지을 수 있다.

hard-margin SVM에서는 KKT conditions를 통해 다음 사실이 나온다.

$$
\alpha_i>0
\quad\Rightarrow\quad
\text{$x_i$ is on a margin boundary}.
$$

즉, dual optimization은 classifier를 만들기 위한 계수 $\alpha_i$를 찾는 동시에, 결정 경계를 실제로 결정하는 support vector를 찾아내는 과정이다.

---

## References

- `OPT_2026_lecture15_note.pdf`: Lagrangian duality, weak duality, strong duality
- `OPT_2026_lecture16_note.pdf`: KKT conditions, saddle-point interpretation, primal-dual methods
- `Constrained Optimization.pdf`: constrained optimization 발표 자료
- `logistic_regression_svm_notes.pdf`: hard-margin SVM, SVM dual, KKT conditions, classifier recovery
