---
title: Quadratic programming
tags:
  - math
  - optimization theory
  - Type of optimization problems
  - Quadratic programming (QP)
---

# Quadratic programming

이번 글에서는 지난 글부터 다루고 있는 여러 type of optimization problem들을 계속해서 알아보는 것의 일환으로, 다항적 체계의 관점에서 선형 계획법의 연장선이라고 간주되는 **Quadrtic programming**에 대해 알아본다.

A quadratic program, 줄여서 QP는 다음과 같은 형태의 최적화 문제이다.

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z \\
\text{subject to} \quad & Az\ge b.
\end{aligned}
$$

여기서 $z$는 optimization variable이고, $Q,p,A,b$는 문제를 정의하는 데이터이다.

목적함수는

$$
\frac12 z^\top Qz+p^\top z
$$

로 주어진다. 첫 번째 항 $\frac12 z^\top Qz$는 quadratic term이고, 두 번째 항 $p^\top z$는 linear term이다. 제약조건은 $Az\ge b$처럼 linear inequality로 주어진다.

따라서 QP는 간단히 말해

$$
\text{quadratic objective}+\text{linear constraints}
$$

를 가지는 최적화 문제이다.

또한 QP가 convex하려면

$$
Q\succeq 0
$$

이어야 한다. 즉, $Q$가 positive semidefinite이어야 한다.

이번 글에서는 QP를 실제로 푸는 방법은 다루지 않는다. 대신 다음 세 가지 예시가 어떻게 QP의 일반형으로 표현되는지만 확인한다.

$$
\text{portfolio optimization},\quad
\text{support vector machine},\quad
\text{LASSO}.
$$

---

## 1. Example: portfolio optimization

첫 번째 예시는 portfolio optimization이다.

투자자가 $n$개의 자산에 돈을 나누어 투자한다고 하자. 이때 $x_i$를 $i$번째 자산에 투자하는 비율이라고 하자. 그러면 전체 투자 비율의 합은 1이어야 하므로

$$
\mathbf 1^\top x=1
$$

이다. 또한 short selling을 허용하지 않는다면

$$
x\ge 0
$$

이어야 한다.

각 자산의 기대수익률을 모은 벡터를 $\mu$라고 하고, 자산 수익률의 covariance matrix를 $\Sigma$라고 하자. 그러면 포트폴리오의 기대수익률은

$$
\mu^\top x
$$

이고, 포트폴리오의 위험은

$$
x^\top \Sigma x
$$

로 나타낼 수 있다.

따라서 포트폴리오 최적화 문제는 다음과 같이 쓸 수 있다.

$$
\begin{aligned}
\text{maximize} \quad & \mu^\top x-\gamma x^\top \Sigma x \\
\text{subject to} \quad & \mathbf 1^\top x=1,\\
& x\ge 0.
\end{aligned}
$$

여기서 $\gamma>0$는 위험을 얼마나 싫어하는지를 나타내는 parameter이다.

목적함수는

$$
\text{expected return}-\text{risk penalty}
$$

의 형태이다.

QP는 보통 minimize 형태로 쓰기 때문에 위 문제를 minimize 문제로 바꾸면

$$
\begin{aligned}
\text{minimize} \quad & \gamma x^\top \Sigma x-\mu^\top x \\
\text{subject to} \quad & \mathbf 1^\top x=1,\\
& x\ge 0.
\end{aligned}
$$

이 된다.

---

### 1.1 Numerical example

세 개의 자산이 있다고 하자.

$$
\mu=
\begin{bmatrix}
0.08\\
0.12\\
0.18
\end{bmatrix},
\quad
\Sigma=
\begin{bmatrix}
0.04 & 0.006 & 0.012\\
0.006 & 0.09 & 0.018\\
0.012 & 0.018 & 0.16
\end{bmatrix},
\quad
\gamma=2.
$$

즉, 세 자산의 기대수익률은 각각 $8\%,12\%,18\%$이다. 공분산 행렬의 대각성분은 각 자산의 variance를 나타내므로, 이 예시에서는 세 번째 자산이 가장 위험한 자산이다.

이때 문제는

$$
\begin{aligned}
\text{minimize} \quad
& 2x^\top \Sigma x-\mu^\top x\\
\text{subject to} \quad
& x_1+x_2+x_3=1,\\
& x_1,x_2,x_3\ge 0.
\end{aligned}
$$

이다.

이제 이 문제가 QP의 일반형

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z\\
\text{subject to} \quad & Az\ge b
\end{aligned}
$$

에 어떻게 들어가는지 확인하자.

먼저 optimization variable을

$$
z=x=
\begin{bmatrix}
x_1\\
x_2\\
x_3
\end{bmatrix}
$$

라고 둔다.

목적함수의 quadratic term은 $2x^\top \Sigma x$이다. QP 일반형에서는 quadratic term이 $\frac12 z^\top Qz$이므로,

$$
\frac12 x^\top Qx=2x^\top \Sigma x
$$

가 되어야 한다. 따라서

$$
Q=4\Sigma
$$

이다.

즉,

$$
Q=
\begin{bmatrix}
0.16 & 0.024 & 0.048\\
0.024 & 0.36 & 0.072\\
0.048 & 0.072 & 0.64
\end{bmatrix}.
$$

linear term은 $-\mu^\top x$이므로

$$
p=-\mu=
\begin{bmatrix}
-0.08\\
-0.12\\
-0.18
\end{bmatrix}.
$$

이제 제약조건을 $Az\ge b$ 형태로 쓰자.

제약조건 $x_1+x_2+x_3=1$은 두 개의 inequality로 쓸 수 있다.

$$
x_1+x_2+x_3\ge 1,
$$

$$
-(x_1+x_2+x_3)\ge -1.
$$

또한 $x_1,x_2,x_3\ge0$도 linear inequality이다.

따라서

$$
A=
\begin{bmatrix}
1&1&1\\
-1&-1&-1\\
1&0&0\\
0&1&0\\
0&0&1
\end{bmatrix},
\quad
b=
\begin{bmatrix}
1\\
-1\\
0\\
0\\
0
\end{bmatrix}.
$$

결국 이 portfolio optimization 문제는

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z\\
\text{subject to} \quad & Az\ge b
\end{aligned}
$$

꼴로 쓸 수 있으므로 QP이다.

---

## 2. Example: support vector machine

두 번째 예시는 support vector machine, 줄여서 SVM이다.

SVM은 classification 문제에서 자주 등장한다. 데이터가 $(x_i,y_i)$, $i=1,\dots,n$으로 주어졌다고 하자. 여기서 $y_i\in\{-1,1\}$이다.

선형 SVM에서는 다음과 같은 함수를 사용한다.

$$
f(x)=w^\top x-b.
$$

이때 soft-margin SVM은 다음과 같은 optimization problem으로 표현할 수 있다.

$$
\min_{w,b}
\lambda \|w\|_2^2+
\frac1n\sum_{i=1}^n
\max\{0,1-y_i(w^\top x_i-b)\}.
$$

첫 번째 항 $\lambda\|w\|_2^2$은 regularization term이다. 두 번째 항

$$
\max\{0,1-y_i(w^\top x_i-b)\}
$$

은 hinge loss이다.

그런데 이 식에는 max가 들어 있으므로 바로 QP 일반형처럼 보이지 않는다. 이를 QP로 바꾸기 위해 auxiliary variable $t_1,\dots,t_n$을 도입한다.

$$
t_i\ge \max\{0,1-y_i(w^\top x_i-b)\}
$$

가 되도록 하면 된다. 이는 다음 두 조건과 같다.

$$
t_i\ge 1-y_i(w^\top x_i-b),
$$

$$
t_i\ge0.
$$

따라서 SVM은 다음과 같이 쓸 수 있다.

$$
\begin{aligned}
\text{minimize} \quad
& \lambda\|w\|_2^2+\frac1n\sum_{i=1}^n t_i\\
\text{subject to} \quad
& t_i\ge 1-y_i(w^\top x_i-b),
\quad i=1,\dots,n,\\
& t_i\ge0,
\quad i=1,\dots,n.
\end{aligned}
$$

첫 번째 제약조건을 정리하면

$$
y_i x_i^\top w-y_i b+t_i\ge 1
$$

이다. 따라서 SVM은

$$
\begin{aligned}
\text{minimize} \quad
& \lambda\|w\|_2^2+\frac1n\sum_{i=1}^n t_i\\
\text{subject to} \quad
& y_i x_i^\top w-y_i b+t_i\ge 1,
\quad i=1,\dots,n,\\
& t_i\ge0,
\quad i=1,\dots,n.
\end{aligned}
$$

이 된다.

목적함수에는 $\|w\|_2^2$이라는 quadratic term이 있고, 제약조건은 모두 $w,b,t_i$에 대한 linear inequality이다. 따라서 SVM은 QP이다.

---

### 2.1 Numerical example

간단하게 1-dimensional data 5개를 보자.

$$
\begin{array}{c|cc}
i & x_i & y_i\\
\hline
1 & -2 & -1\\
2 & -1 & -1\\
3 & 0.2 & -1\\
4 & 1 & 1\\
5 & 2 & 1
\end{array}
$$

모델은

$$
f(x)=wx-b
$$

이고, $\lambda=0.1$이라고 하자. 그러면 SVM 문제는

$$
\min_{w,b}
0.1w^2+
\frac15\sum_{i=1}^5
\max\{0,1-y_i(wx_i-b)\}
$$

이다.

auxiliary variable $t_1,\dots,t_5$를 도입하면

$$
\begin{aligned}
\text{minimize} \quad
& 0.1w^2+\frac15(t_1+t_2+t_3+t_4+t_5)\\
\text{subject to} \quad
& t_i\ge 1-y_i(wx_i-b),
\quad i=1,\dots,5,\\
& t_i\ge0,
\quad i=1,\dots,5.
\end{aligned}
$$

각 데이터에 대해 제약조건을 쓰면 다음과 같다.

첫 번째 데이터는 $x_1=-2,\ y_1=-1$이므로

$$
t_1\ge 1-(-1)(-2w-b)=1-2w-b.
$$

두 번째 데이터는 $x_2=-1,\ y_2=-1$이므로

$$
t_2\ge 1-(-1)(-w-b)=1-w-b.
$$

세 번째 데이터는 $x_3=0.2,\ y_3=-1$이므로

$$
t_3\ge 1-(-1)(0.2w-b)=1+0.2w-b.
$$

네 번째 데이터는 $x_4=1,\ y_4=1$이므로

$$
t_4\ge 1-(w-b)=1-w+b.
$$

다섯 번째 데이터는 $x_5=2,\ y_5=1$이므로

$$
t_5\ge 1-(2w-b)=1-2w+b.
$$

따라서 QP는

$$
\begin{aligned}
\text{minimize} \quad
& 0.1w^2+\frac15(t_1+t_2+t_3+t_4+t_5)\\
\text{subject to} \quad
& t_1\ge 1-2w-b,\\
& t_2\ge 1-w-b,\\
& t_3\ge 1+0.2w-b,\\
& t_4\ge 1-w+b,\\
& t_5\ge 1-2w+b,\\
& t_1,t_2,t_3,t_4,t_5\ge0.
\end{aligned}
$$

이제 이 문제를 $Q,p,A,b$로 직접 써보자.

optimization variable은

$$
z=
\begin{bmatrix}
w\\
b\\
t_1\\
t_2\\
t_3\\
t_4\\
t_5
\end{bmatrix}
$$

이다.

목적함수는

$$
0.1w^2+\frac15(t_1+t_2+t_3+t_4+t_5)
$$

이다. QP 일반형의 목적함수 $\frac12 z^\top Qz+p^\top z$와 비교하면, 이차항은 $w^2$에만 존재한다. 따라서

$$
\frac12 Q_{11}w^2=0.1w^2
$$

이어야 하므로

$$
Q_{11}=0.2
$$

이다. 나머지 변수에 대한 quadratic term은 없으므로

$$
Q=
\begin{bmatrix}
0.2&0&0&0&0&0&0\\
0&0&0&0&0&0&0\\
0&0&0&0&0&0&0\\
0&0&0&0&0&0&0\\
0&0&0&0&0&0&0\\
0&0&0&0&0&0&0\\
0&0&0&0&0&0&0
\end{bmatrix}.
$$

linear term은 $\frac15(t_1+t_2+t_3+t_4+t_5)$이므로

$$
p=
\begin{bmatrix}
0\\
0\\
1/5\\
1/5\\
1/5\\
1/5\\
1/5
\end{bmatrix}
=
\begin{bmatrix}
0\\
0\\
0.2\\
0.2\\
0.2\\
0.2\\
0.2
\end{bmatrix}.
$$

이제 제약조건을 $Az\ge b$ 형태로 정리한다.

$$
t_1\ge 1-2w-b
$$

는

$$
2w+b+t_1\ge1
$$

이고,

$$
t_2\ge 1-w-b
$$

는

$$
w+b+t_2\ge1
$$

이다.

또한

$$
t_3\ge 1+0.2w-b
$$

는

$$
-0.2w+b+t_3\ge1
$$

이고,

$$
t_4\ge 1-w+b
$$

는

$$
w-b+t_4\ge1
$$

이다.

마지막으로

$$
t_5\ge 1-2w+b
$$

는

$$
2w-b+t_5\ge1
$$

이다.

여기에 $t_1,t_2,t_3,t_4,t_5\ge0$을 추가하면,

$$
A=
\begin{bmatrix}
2&1&1&0&0&0&0\\
1&1&0&1&0&0&0\\
-0.2&1&0&0&1&0&0\\
1&-1&0&0&0&1&0\\
2&-1&0&0&0&0&1\\
0&0&1&0&0&0&0\\
0&0&0&1&0&0&0\\
0&0&0&0&1&0&0\\
0&0&0&0&0&1&0\\
0&0&0&0&0&0&1
\end{bmatrix},
\quad
b=
\begin{bmatrix}
1\\
1\\
1\\
1\\
1\\
0\\
0\\
0\\
0\\
0
\end{bmatrix}.
$$

따라서 이 SVM 예시는

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z\\
\text{subject to} \quad & Az\ge b
\end{aligned}
$$

꼴로 정확히 표현된다.

---

## 3. Example: LASSO

세 번째 예시는 LASSO이다.

LASSO는 regression 문제에서 자주 사용된다. 기본적인 least squares 문제는

$$
\min_\beta \frac1n\|y-X\beta\|_2^2
$$

이다. LASSO는 여기에 $\ell_1$-penalty를 추가한다.

$$
\min_\beta
\frac1n\|y-X\beta\|_2^2+\lambda\|\beta\|_1.
$$

여기서

$$
\|\beta\|_1
=
|\beta_1|+\cdots+|\beta_d|
$$

이다.

LASSO가 QP처럼 바로 보이지 않는 이유는 absolute value가 들어 있기 때문이다. 하지만 auxiliary variable을 도입하면 $\ell_1$-penalty를 linear constraint로 표현할 수 있다.

---

### 3.1 Numerical example

두 개의 feature가 있는 regression 문제를 보자.

$$
X=
\begin{bmatrix}
1&0\\
0&1\\
1&0\\
0&1
\end{bmatrix},
\quad
y=
\begin{bmatrix}
3\\
2\\
1\\
0
\end{bmatrix},
\quad
\lambda=0.5.
$$

그러면 LASSO 문제는

$$
\min_{\beta_1,\beta_2}
\frac14
\left\|
\begin{bmatrix}
3\\
2\\
1\\
0
\end{bmatrix}
-
\begin{bmatrix}
1&0\\
0&1\\
1&0\\
0&1
\end{bmatrix}
\begin{bmatrix}
\beta_1\\
\beta_2
\end{bmatrix}
\right\|_2^2
+
0.5(|\beta_1|+|\beta_2|)
$$

이다.

먼저

$$
X\beta=
\begin{bmatrix}
\beta_1\\
\beta_2\\
\beta_1\\
\beta_2
\end{bmatrix}
$$

이므로

$$
y-X\beta=
\begin{bmatrix}
3-\beta_1\\
2-\beta_2\\
1-\beta_1\\
-\beta_2
\end{bmatrix}.
$$

따라서

$$
\|y-X\beta\|_2^2
=
(3-\beta_1)^2+
(2-\beta_2)^2+
(1-\beta_1)^2+
(-\beta_2)^2.
$$

이를 전개하면

$$
(3-\beta_1)^2+(1-\beta_1)^2
=
2\beta_1^2-8\beta_1+10
$$

이고,

$$
(2-\beta_2)^2+(-\beta_2)^2
=
2\beta_2^2-4\beta_2+4
$$

이다. 따라서

$$
\|y-X\beta\|_2^2
=
2\beta_1^2+2\beta_2^2-8\beta_1-4\beta_2+14.
$$

양변에 $\frac14$를 곱하면

$$
\frac14\|y-X\beta\|_2^2
=
\frac12\beta_1^2+
\frac12\beta_2^2
-2\beta_1-\beta_2+3.5.
$$

상수항 $3.5$는 최적해의 위치에 영향을 주지 않으므로 QP 형태를 확인할 때는 생략해도 된다.

따라서 LASSO는 다음 문제와 같은 형태이다.

$$
\min_{\beta_1,\beta_2}
\frac12\beta_1^2+
\frac12\beta_2^2
-2\beta_1-\beta_2
+
0.5(|\beta_1|+|\beta_2|).
$$

이제 absolute value를 없애기 위해 auxiliary variable $s_1,s_2$를 도입한다.

$$
s_1\ge |\beta_1|,
\quad
s_2\ge |\beta_2|
$$

가 되도록 하면 된다. 이는 각각

$$
s_1\ge \beta_1,
\quad
s_1\ge -\beta_1
$$

그리고

$$
s_2\ge \beta_2,
\quad
s_2\ge -\beta_2
$$

와 같다.

따라서 LASSO는 다음 QP로 바뀐다.

$$
\begin{aligned}
\text{minimize} \quad
& \frac12\beta_1^2+
\frac12\beta_2^2
-2\beta_1-\beta_2
+0.5s_1+0.5s_2\\
\text{subject to} \quad
& s_1\ge \beta_1,\\
& s_1\ge -\beta_1,\\
& s_2\ge \beta_2,\\
& s_2\ge -\beta_2.
\end{aligned}
$$

이제 이를 $Q,p,A,b$로 쓰자.

optimization variable은

$$
z=
\begin{bmatrix}
\beta_1\\
\beta_2\\
s_1\\
s_2
\end{bmatrix}
$$

이다.

목적함수

$$
\frac12\beta_1^2+
\frac12\beta_2^2
-2\beta_1-\beta_2
+0.5s_1+0.5s_2
$$

를 $\frac12 z^\top Qz+p^\top z$와 비교하면

$$
Q=
\begin{bmatrix}
1&0&0&0\\
0&1&0&0\\
0&0&0&0\\
0&0&0&0
\end{bmatrix}
$$

이고,

$$
p=
\begin{bmatrix}
-2\\
-1\\
0.5\\
0.5
\end{bmatrix}.
$$

제약조건은

$$
s_1\ge \beta_1,
\quad
s_1\ge -\beta_1,
\quad
s_2\ge \beta_2,
\quad
s_2\ge -\beta_2
$$

이다. 이를 $Az\ge b$ 형태로 쓰면

$$
-\beta_1+s_1\ge0,
$$

$$
\beta_1+s_1\ge0,
$$

$$
-\beta_2+s_2\ge0,
$$

$$
\beta_2+s_2\ge0.
$$

따라서

$$
A=
\begin{bmatrix}
-1&0&1&0\\
1&0&1&0\\
0&-1&0&1\\
0&1&0&1
\end{bmatrix},
\quad
b=
\begin{bmatrix}
0\\
0\\
0\\
0
\end{bmatrix}.
$$

따라서 이 LASSO 문제도

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z\\
\text{subject to} \quad & Az\ge b
\end{aligned}
$$

꼴로 표현된다.

---

## 4. Summary

이번 글에서는 QP의 일반형

$$
\begin{aligned}
\text{minimize} \quad & \frac12 z^\top Qz+p^\top z\\
\text{subject to} \quad & Az\ge b
\end{aligned}
$$

을 살펴보고, 세 가지 예시가 이 형태로 표현된다는 것을 확인했다.

| problem | optimization variable $z$ | quadratic term | linear constraints |
|---|---|---|---|
| portfolio optimization | $x$ | $x^\top\Sigma x$ | budget constraint, $x\ge0$ |
| support vector machine | $(w,b,t_1,\dots,t_n)$ | $\|w\|_2^2$ | margin constraints, $t_i\ge0$ |
| LASSO | $(\beta,s)$ | $\|y-X\beta\|_2^2$ | $s_j\ge|\beta_j|$ |

세 문제는 겉으로는 서로 다른 문제처럼 보인다. Portfolio optimization은 금융 문제이고, SVM은 classification 문제이고, LASSO는 regression 문제이다.

하지만 수학적으로 보면 모두 다음 구조를 가진다.

$$
\text{quadratic objective}
$$

와

$$
\text{linear constraints}.
$$

따라서 세 문제 모두 QP의 일반형으로 표현할 수 있다.

끝으로 다만 이번 글에서는 QP를 어떻게 푸는지는 다루지 않았다. 여기서는 실제로 자주 등장하는 여러 문제들이 QP라는 하나의 optimization framework 안에 들어간다는 점만 확인했다.