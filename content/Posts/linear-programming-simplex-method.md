---
title: Linear Programming과 Simplex Method
tags:
  - math
  - optimization theory
  - linear programming(LP)
---

# Linear Programming과 Simplex Method

이번 글에서는 **Linear Programming**, 즉 선형계획법과 이를 풀기 위한 대표적인 알고리즘인 **Simplex Method**를 정리한다.

Linear Programming은 목적함수와 제약조건이 모두 선형인 최적화 문제를 다룬다. 예를 들어 다음과 같은 문제가 선형계획문제이다.

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
2x + y &\le 70, \\
x, y &\ge 0.
\end{aligned}
$$

여기서 $5x+4y$는 목적함수이고, 나머지 식들은 제약조건이다. 목표는 제약조건을 만족하는 $(x,y)$ 중에서 목적함수 값을 가장 크게 만드는 점을 찾는 것이다.

---

## 1. Linear Programming의 기본 형태

일반적인 선형계획문제는 다음과 같이 쓸 수 있다.

$$
\min c^\top x
$$

$$
\text{s.t. } Ax \le b.
$$

여기서 $x$는 변수 벡터이고, $c$는 목적함수의 계수 벡터이다. $A$와 $b$는 제약조건을 정의한다.

예를 들어

$$
\min 3x_1 + 2x_2
$$

$$
\begin{aligned}
x_1 + x_2 &\le 4, \\
2x_1 + x_2 &\le 5
\end{aligned}
$$

는 다음과 같이 행렬 형태로 쓸 수 있다.

$$
c =
\begin{bmatrix}
3 \\
2
\end{bmatrix},
\quad
A =
\begin{bmatrix}
1 & 1 \\
2 & 1
\end{bmatrix},
\quad
b =
\begin{bmatrix}
4 \\
5
\end{bmatrix}.
$$

따라서 문제는

$$
\min c^\top x
$$

$$
\text{s.t. } Ax \le b
$$

의 형태가 된다.

---

## 2. Standard Form

Simplex Method를 적용하기 위해서는 보통 문제를 **standard form**으로 바꾼다.

선형계획문제의 standard form은 다음과 같다.

$$
\min c^\top x
$$

$$
\text{s.t. } Ax = b,
\quad x \ge 0.
$$

즉, standard form에서는 두 가지 조건이 필요하다.

1. 모든 제약조건은 등식이어야 한다.
2. 모든 변수는 nonnegative, 즉 0 이상이어야 한다.

---

## 3. Inequality Constraint를 Equality Constraint로 바꾸기

부등식 제약조건은 **slack variable**을 추가해서 등식으로 바꿀 수 있다.

예를 들어 다음 제약조건을 보자.

$$
2x + 3y \le 150.
$$

이 식은 $2x+3y$가 150보다 작거나 같다는 뜻이다. 따라서 남는 양을 새로운 변수 $s_1$으로 두면

$$
2x + 3y + s_1 = 150
$$

으로 쓸 수 있다.

여기서 $s_1$은 slack variable이고,

$$
s_1 \ge 0
$$

이어야 한다.

즉,

$$
2x + 3y \le 150
$$

은

$$
2x + 3y + s_1 = 150,
\quad s_1 \ge 0
$$

와 동치이다.

마찬가지로

$$
2x + y \le 70
$$

은

$$
2x + y + s_2 = 70,
\quad s_2 \ge 0
$$

로 바꿀 수 있다.

---

## 4. 예시: Linear Program을 Standard Form으로 바꾸기

다음 문제를 생각하자.

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
2x + y &\le 70, \\
x, y &\ge 0.
\end{aligned}
$$

각 부등식에 slack variable $s_1, s_2$를 추가한다.

첫 번째 제약조건은

$$
2x + 3y + s_1 = 150
$$

이고, 두 번째 제약조건은

$$
2x + y + s_2 = 70
$$

이다.

따라서 standard form은 다음과 같다.

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y + s_1 &= 150, \\
2x + y + s_2 &= 70, \\
x, y, s_1, s_2 &\ge 0.
\end{aligned}
$$

---

## 5. Free Variable을 Nonnegative Variable로 바꾸기

standard form에서는 모든 변수가 0 이상이어야 한다. 하지만 어떤 변수는 음수도 될 수 있는 자유변수일 수 있다.

예를 들어 $x_j \in \mathbb{R}$이면 $x_j$는 음수, 0, 양수 모두 가능하다.

이 경우 다음과 같이 두 개의 nonnegative variable로 표현할 수 있다.

$$
x_j = x_j^+ - x_j^-
$$

단,

$$
x_j^+ \ge 0,
\quad
x_j^- \ge 0.
$$

예를 들어 $x_j = 3$이면

$$
x_j^+ = 3,
\quad
x_j^- = 0
$$

으로 둘 수 있다.

반대로 $x_j = -5$이면

$$
x_j^+ = 0,
\quad
x_j^- = 5
$$

로 둘 수 있다.

따라서 자유변수도 항상 nonnegative variable만 사용해서 표현할 수 있다.

---

## 6. Simplex Method의 기본 아이디어

Simplex Method는 feasible region의 꼭짓점들을 따라 이동하면서 목적함수 값을 개선하는 알고리즘이다.

2차원에서는 feasible region이 다각형으로 나타난다. 최적해는 보통 이 다각형의 꼭짓점 중 하나에서 발생한다.

Simplex Method는 다음 과정을 반복한다.

1. 현재 feasible solution에서 시작한다.
2. 목적함수를 증가시킬 수 있는 방향을 찾는다.
3. 해당 방향으로 이동하다가 제약조건에 걸리는 지점에서 멈춘다.
4. 새로운 꼭짓점으로 이동한다.
5. 더 이상 목적함수를 증가시킬 수 없으면 종료한다.

---

## 7. Dictionary 표현

다음 문제를 다시 보자.

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
2x + y &\le 70, \\
x, y &\ge 0.
\end{aligned}
$$

standard form으로 바꾸면

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y + s_1 &= 150, \\
2x + y + s_2 &= 70, \\
x, y, s_1, s_2 &\ge 0.
\end{aligned}
$$

여기서 slack variable들을 왼쪽에 두면 다음과 같은 dictionary를 얻는다.

$$
z = 5x + 4y
$$

$$
s_1 = 150 - 2x - 3y
$$

$$
s_2 = 70 - 2x - y
$$

현재 오른쪽에 있는 변수 $x,y$를 0으로 두면

$$
x = 0,
\quad
y = 0
$$

이고,

$$
s_1 = 150,
\quad
s_2 = 70
$$

이다.

따라서 초기해는

$$
(x,y,s_1,s_2) = (0,0,150,70)
$$

이다.

이때 $s_1,s_2$는 **basic variables**이고, $x,y$는 **non-basic variables**이다.

---

## 8. Simplex Method 예제 풀이

이제 실제로 Simplex Method를 수행해 보자.

문제는 다음과 같다.

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
2x + y &\le 70, \\
x, y &\ge 0.
\end{aligned}
$$

standard form은

$$
\max z = 5x + 4y
$$

$$
\begin{aligned}
2x + 3y + s_1 &= 150, \\
2x + y + s_2 &= 70, \\
x, y, s_1, s_2 &\ge 0.
\end{aligned}
$$

초기 dictionary는

$$
z = 5x + 4y
$$

$$
s_1 = 150 - 2x - 3y
$$

$$
s_2 = 70 - 2x - y
$$

이다.

초기해는

$$
(x,y) = (0,0)
$$

이고, 목적함수 값은

$$
z = 0
$$

이다.

---

### Step 1. Entering variable 선택

목적함수 row를 보면

$$
z = 5x + 4y
$$

이다.

$x$의 계수는 5이고, $y$의 계수는 4이다. 둘 다 양수이므로 $x$나 $y$를 증가시키면 목적함수 값이 증가한다.

여기서는 계수가 더 큰 $x$를 entering variable로 선택한다.

즉, $x$를 증가시킨다.

---

### Step 2. Leaving variable 선택

$y=0$으로 고정하고 $x$를 증가시킨다고 하자.

그러면 제약조건은

$$
s_1 = 150 - 2x
$$

$$
s_2 = 70 - 2x
$$

가 된다.

basic variables는 항상 0 이상이어야 하므로

$$
s_1 \ge 0,
\quad
s_2 \ge 0
$$

이어야 한다.

먼저

$$
150 - 2x \ge 0
$$

이므로

$$
x \le 75
$$

이다.

또한

$$
70 - 2x \ge 0
$$

이므로

$$
x \le 35
$$

이다.

따라서 $x$는 최대

$$
\min\{75,35\} = 35
$$

까지 증가할 수 있다.

이때 $s_2 = 0$이 되므로 $s_2$가 leaving variable이 된다.

---

### Step 3. Pivot 수행

기존 식 중

$$
s_2 = 70 - 2x - y
$$

에서 $x$를 왼쪽으로 풀면

$$
2x = 70 - y - s_2
$$

따라서

$$
x = 35 - \frac{1}{2}y - \frac{1}{2}s_2.
$$

이제 이 식을 다른 식에 대입한다.

목적함수:

$$
z = 5x + 4y
$$

$$
= 5\left(35 - \frac{1}{2}y - \frac{1}{2}s_2\right) + 4y
$$

$$
= 175 - \frac{5}{2}y - \frac{5}{2}s_2 + 4y
$$

$$
= 175 + \frac{3}{2}y - \frac{5}{2}s_2.
$$

즉,

$$
z = 175 + 1.5y - 2.5s_2.
$$

다음으로 $s_1$은

$$
s_1 = 150 - 2x - 3y
$$

이다.

여기에

$$
x = 35 - \frac{1}{2}y - \frac{1}{2}s_2
$$

를 대입하면

$$
s_1
= 150 - 2\left(35 - \frac{1}{2}y - \frac{1}{2}s_2\right) - 3y
$$

$$
= 150 - 70 + y + s_2 - 3y
$$

$$
= 80 - 2y + s_2.
$$

따라서 새로운 dictionary는

$$
z = 175 + 1.5y - 2.5s_2
$$

$$
s_1 = 80 - 2y + s_2
$$

$$
x = 35 - 0.5y - 0.5s_2
$$

이다.

현재 non-basic variables는 $y,s_2$이고, 이를 0으로 두면

$$
y = 0,
\quad
s_2 = 0
$$

이다.

따라서

$$
x = 35,
\quad
s_1 = 80
$$

이고, 현재해는

$$
(x,y) = (35,0)
$$

이다.

목적함수 값은

$$
z = 175
$$

이다.

---

### Step 4. 다시 entering variable 선택

현재 목적함수 row는

$$
z = 175 + 1.5y - 2.5s_2
$$

이다.

여기서 $y$의 계수는 양수이다. 따라서 $y$를 증가시키면 목적함수 값이 증가한다.

그러므로 $y$를 entering variable로 선택한다.

---

### Step 5. Leaving variable 선택

$s_2 = 0$으로 두고 $y$를 증가시킨다.

현재 basic variables는

$$
s_1 = 80 - 2y
$$

$$
x = 35 - 0.5y
$$

이다.

비음수 조건 때문에

$$
80 - 2y \ge 0
$$

이어야 하므로

$$
y \le 40
$$

이다.

또한

$$
35 - 0.5y \ge 0
$$

이어야 하므로

$$
y \le 70
$$

이다.

따라서 $y$는 최대

$$
\min\{40,70\} = 40
$$

까지 증가할 수 있다.

이때 $s_1 = 0$이 되므로 $s_1$이 leaving variable이다.

---

### Step 6. 두 번째 Pivot 수행

현재 식

$$
s_1 = 80 - 2y + s_2
$$

에서 $y$를 왼쪽으로 풀자.

$$
2y = 80 + s_2 - s_1
$$

따라서

$$
y = 40 + \frac{1}{2}s_2 - \frac{1}{2}s_1.
$$

이제 이것을 목적함수와 $x$ 식에 대입한다.

목적함수는

$$
z = 175 + 1.5y - 2.5s_2
$$

이다.

대입하면

$$
z
= 175 + 1.5\left(40 + \frac{1}{2}s_2 - \frac{1}{2}s_1\right) - 2.5s_2
$$

$$
= 175 + 60 + 0.75s_2 - 0.75s_1 - 2.5s_2
$$

$$
= 235 - 1.75s_2 - 0.75s_1.
$$

또한

$$
x = 35 - 0.5y - 0.5s_2
$$

이므로

$$
x
= 35 - 0.5\left(40 + \frac{1}{2}s_2 - \frac{1}{2}s_1\right) - 0.5s_2
$$

$$
= 35 - 20 - 0.25s_2 + 0.25s_1 - 0.5s_2
$$

$$
= 15 - 0.75s_2 + 0.25s_1.
$$

따라서 새로운 dictionary는

$$
z = 235 - 1.75s_2 - 0.75s_1
$$

$$
y = 40 + 0.5s_2 - 0.5s_1
$$

$$
x = 15 - 0.75s_2 + 0.25s_1
$$

이다.

현재 non-basic variables는 $s_1,s_2$이다.

이를 0으로 두면

$$
s_1 = 0,
\quad
s_2 = 0
$$

이고,

$$
x = 15,
\quad
y = 40
$$

이다.

따라서 현재해는

$$
(x,y) = (15,40)
$$

이고, 목적함수 값은

$$
z = 235
$$

이다.

---

### Step 7. Optimality 확인

현재 목적함수 row는

$$
z = 235 - 1.75s_2 - 0.75s_1
$$

이다.

현재 non-basic variables는 $s_1,s_2$이고, 두 변수의 계수는 모두 음수이다.

즉, $s_1$이나 $s_2$를 증가시키면 목적함수 값은 감소한다.

따라서 더 이상 목적함수 값을 증가시킬 수 없고, 현재해가 최적해이다.

결론적으로 최적해는

$$
(x,y) = (15,40)
$$

이고, 최적 목적값은

$$
z = 235
$$

이다.

---

## 9. Simplex Method의 기하학적 해석

위 예제에서 Simplex Method는 다음 세 점을 이동했다.

$$
(0,0) \to (35,0) \to (15,40)
$$

첫 번째 점 $(0,0)$은 feasible region의 꼭짓점이다.

두 번째 점 $(35,0)$은 제약조건

$$
2x+y=70
$$

위에 있는 꼭짓점이다.

세 번째 점 $(15,40)$은 두 제약조건

$$
2x+3y=150
$$

$$
2x+y=70
$$

이 동시에 tight한 점이다.

실제로 두 식을 풀면

$$
2x + 3y = 150
$$

$$
2x + y = 70
$$

이다.

두 식을 빼면

$$
2y = 80
$$

이므로

$$
y = 40
$$

이다.

이를

$$
2x+y=70
$$

에 대입하면

$$
2x+40=70
$$

이므로

$$
2x=30
$$

따라서

$$
x=15
$$

이다.

즉, 최적해는

$$
(x,y)=(15,40)
$$

이다.

---

## 10. Two-Phase Simplex Method

Simplex Method는 feasible solution에서 시작해야 한다. 그런데 초기 dictionary가 항상 feasible한 것은 아니다.

예를 들어 다음 문제를 보자.

$$
\max z = x + 2y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
-x + y &\le -25, \\
x,y &\ge 0.
\end{aligned}
$$

slack variable을 추가하면

$$
2x + 3y + s_1 = 150
$$

$$
-x + y + s_2 = -25
$$

이다.

dictionary는

$$
z = x + 2y
$$

$$
s_1 = 150 - 2x - 3y
$$

$$
s_2 = -25 + x - y
$$

이다.

여기서 $x=0,y=0$으로 두면

$$
s_1 = 150,
\quad
s_2 = -25
$$

이다.

그런데 $s_2$는 nonnegative variable이어야 하므로

$$
s_2 \ge 0
$$

이어야 한다.

하지만 $s_2=-25$이므로 초기 dictionary는 infeasible하다.

이럴 때 사용하는 방법이 **Two-Phase Simplex Method**이다.

---

## 11. Phase I: Feasible Dictionary 찾기

Phase I에서는 원래 문제의 feasible solution이 존재하는지 확인한다.

이를 위해 새로운 변수 $t$를 도입한다.

원래 제약조건은

$$
2x + 3y \le 150
$$

$$
-x + y \le -25
$$

였다.

Phase I 문제는 다음과 같다.

$$
\min t
$$

$$
\begin{aligned}
2x + 3y - t &\le 150, \\
-x + y - t &\le -25, \\
x,y,t &\ge 0.
\end{aligned}
$$

이 문제의 의미는 $t$를 이용해서 제약조건을 억지로 만족시키되, 가능한 한 $t$를 작게 만들겠다는 것이다.

만약 최적값이

$$
t^* = 0
$$

이면 원래 문제도 feasible하다.

반대로

$$
t^* > 0
$$

이면 원래 문제는 infeasible하다.

---

## 12. Phase I 예제 풀이

Phase I 문제를 standard form으로 바꾸면

$$
\min t
$$

$$
\begin{aligned}
2x + 3y - t + s_1 &= 150, \\
-x + y - t + s_2 &= -25, \\
x,y,t,s_1,s_2 &\ge 0.
\end{aligned}
$$

최소화 문제 $\min t$는 최대화 문제 $\max -t$로 바꿀 수 있다.

따라서 objective row는

$$
z = -t
$$

이다.

초기 dictionary는

$$
z = -t
$$

$$
s_1 = 150 - 2x - 3y + t
$$

$$
s_2 = -25 + x - y + t
$$

이다.

현재 $s_2=-25$이므로 infeasible하다.

가장 음수인 basic variable은 $s_2$이다. 따라서 $t$를 basic variable로 만들고, $s_2$를 non-basic variable로 바꾼다.

$s_2$ 식에서

$$
s_2 = -25 + x - y + t
$$

이므로

$$
t = 25 - x + y + s_2
$$

이다.

이를 다른 식에 대입한다.

목적함수:

$$
z = -t
$$

$$
= -(25 - x + y + s_2)
$$

$$
= -25 + x - y - s_2.
$$

$s_1$은

$$
s_1 = 150 - 2x - 3y + t
$$

이고, $t=25-x+y+s_2$를 대입하면

$$
s_1
= 150 - 2x - 3y + 25 - x + y + s_2
$$

$$
= 175 - 3x - 2y + s_2.
$$

따라서 dictionary는

$$
z = -25 + x - y - s_2
$$

$$
s_1 = 175 - 3x - 2y + s_2
$$

$$
t = 25 - x + y + s_2
$$

이다.

이제 목적함수 row에서 $x$의 계수가 양수이므로 $x$를 증가시킨다.

$s_2=0,y=0$으로 두면

$$
s_1 = 175 - 3x
$$

$$
t = 25 - x
$$

이다.

비음수 조건 때문에

$$
x \le \frac{175}{3}
$$

이고,

$$
x \le 25
$$

이다.

따라서

$$
x \le 25
$$

가 더 강한 제약이며, $t$가 leaving variable이 된다.

$t = 25 - x + y + s_2$에서 $x$를 풀면

$$
x = 25 - t + y + s_2
$$

이다.

이것을 나머지 식에 대입하면 Phase I의 최적 dictionary는

$$
z = -t
$$

$$
s_1 = 100 + 3t - 5y - 2s_2
$$

$$
x = 25 - t + y + s_2
$$

이다.

현재 objective row는

$$
z = -t
$$

이고, 더 이상 증가시킬 수 있는 변수가 없다.

또한 $t=0$이 가능하므로 Phase I의 최적값은 0이다.

따라서 원래 문제는 feasible하다.

이때 $t=0,y=0,s_2=0$으로 두면

$$
x = 25
$$

$$
s_1 = 100
$$

이다.

따라서 원래 문제의 feasible solution은

$$
(x,y)=(25,0)
$$

이다.

---

## 13. Phase II: 원래 목적함수로 돌아가기

Phase I에서 feasible dictionary를 찾았으므로 이제 원래 목적함수

$$
z = x + 2y
$$

로 돌아간다.

Phase I 결과에서

$$
x = 25 + y + s_2
$$

이고,

$$
s_1 = 100 - 5y - 2s_2
$$

이다.

원래 목적함수에 $x$를 대입하면

$$
z = x + 2y
$$

$$
= 25 + y + s_2 + 2y
$$

$$
= 25 + 3y + s_2.
$$

따라서 Phase II의 초기 dictionary는

$$
z = 25 + 3y + s_2
$$

$$
s_1 = 100 - 5y - 2s_2
$$

$$
x = 25 + y + s_2
$$

이다.

현재 objective row에서 $y$의 계수가 3으로 양수이므로 $y$를 증가시킨다.

$s_2=0$으로 두면

$$
s_1 = 100 - 5y
$$

$$
x = 25 + y
$$

이다.

비음수 조건에서 $s_1 \ge 0$이어야 하므로

$$
100 - 5y \ge 0
$$

즉,

$$
y \le 20
$$

이다.

따라서 $y$를 20까지 증가시킬 수 있고, 이때 $s_1=0$이 된다.

$s_1$이 leaving variable이고 $y$가 entering variable이다.

$s_1 = 100 - 5y - 2s_2$에서 $y$를 풀면

$$
5y = 100 - 2s_2 - s_1
$$

따라서

$$
y = 20 - 0.4s_2 - 0.2s_1.
$$

이를 목적함수에 대입하면

$$
z = 25 + 3y + s_2
$$

$$
= 25 + 3(20 - 0.4s_2 - 0.2s_1) + s_2
$$

$$
= 25 + 60 - 1.2s_2 - 0.6s_1 + s_2
$$

$$
= 85 - 0.2s_2 - 0.6s_1.
$$

또한

$$
x = 25 + y + s_2
$$

이므로

$$
x = 25 + (20 - 0.4s_2 - 0.2s_1) + s_2
$$

$$
= 45 + 0.6s_2 - 0.2s_1.
$$

따라서 새로운 dictionary는

$$
z = 85 - 0.2s_2 - 0.6s_1
$$

$$
y = 20 - 0.4s_2 - 0.2s_1
$$

$$
x = 45 + 0.6s_2 - 0.2s_1
$$

이다.

objective row에서 non-basic variables $s_1,s_2$의 계수가 모두 음수이므로 최적이다.

따라서 최적해는

$$
(x,y) = (45,20)
$$

이고, 최적 목적값은

$$
z = 85
$$

이다.

---

## 14. Infeasible Case

이번에는 실제로 infeasible한 문제를 보자.

$$
\max z = x + 2y
$$

$$
\begin{aligned}
2x + 3y &\le 150, \\
-x + y &\le -90, \\
x,y &\ge 0.
\end{aligned}
$$

먼저 Phase I 문제를 만든다.

$$
\min t
$$

$$
\begin{aligned}
2x + 3y - t &\le 150, \\
-x + y - t &\le -90, \\
x,y,t &\ge 0.
\end{aligned}
$$

standard form은

$$
2x + 3y - t + s_1 = 150
$$

$$
-x + y - t + s_2 = -90
$$

이다.

초기 dictionary는

$$
z = -t
$$

$$
s_1 = 150 - 2x - 3y + t
$$

$$
s_2 = -90 + x - y + t
$$

이다.

$s_2=-90$이므로 infeasible하다.

$s_2$ 식에서 $t$를 basic variable로 만들면

$$
t = 90 - x + y + s_2
$$

이다.

이를 대입하면

$$
z = -90 + x - y - s_2
$$

$$
s_1 = 240 - 3x - 2y + s_2
$$

$$
t = 90 - x + y + s_2
$$

이다.

이제 $x$의 objective coefficient가 양수이므로 $x$를 증가시킨다.

$y=0,s_2=0$으로 두면

$$
s_1 = 240 - 3x
$$

$$
t = 90 - x
$$

이다.

비음수 조건에 의해

$$
x \le 80
$$

그리고

$$
x \le 90
$$

이다.

따라서 $x=80$까지 증가할 수 있고, 이때 $s_1=0$이다.

$s_1$이 leaving variable이다.

계산을 정리하면 Phase I 최적 dictionary에서

$$
z = -10 - \frac{1}{3}s_1 - \frac{5}{3}y - \frac{2}{3}s_2
$$

$$
x = 80 - \frac{1}{3}s_1 - \frac{2}{3}y + \frac{1}{3}s_2
$$

$$
t = 10 + \frac{1}{3}s_1 + \frac{5}{3}y + \frac{2}{3}s_2
$$

를 얻는다.

여기서 objective row의 모든 계수는 non-positive이므로 Phase I은 최적이다.

그러나 현재

$$
t = 10
$$

이다.

즉 Phase I의 최적값이 0이 아니라 양수이다.

따라서 원래 문제는 feasible solution을 가지지 않는다.

결론적으로 이 문제는 infeasible이다.

---

## 15. Unbounded Case

이번에는 unbounded한 문제를 보자.

$$
\max z = x + y
$$

$$
\begin{aligned}
-2x + y &\le 100, \\
x - 2y &\le 100, \\
x,y &\ge 0.
\end{aligned}
$$

slack variable을 추가하면

$$
-2x + y + s_1 = 100
$$

$$
x - 2y + s_2 = 100
$$

이다.

초기 dictionary는

$$
z = x + y
$$

$$
s_1 = 100 + 2x - y
$$

$$
s_2 = 100 - x + 2y
$$

이다.

초기해는

$$
x=0,
\quad
y=0,
\quad
s_1=100,
\quad
s_2=100
$$

이다.

목적함수 row에서 $x$의 계수가 양수이므로 $x$를 증가시킨다.

$y=0$으로 두면

$$
s_1 = 100 + 2x
$$

$$
s_2 = 100 - x
$$

이다.

$s_2 \ge 0$이어야 하므로

$$
x \le 100
$$

이다.

따라서 $x=100$까지 증가시키고, 이때 $s_2=0$이 된다.

$s_2$가 leaving variable이다.

$s_2 = 100 - x + 2y$에서 $x$를 풀면

$$
x = 100 + 2y - s_2
$$

이다.

이를 목적함수에 대입하면

$$
z = x + y
$$

$$
= 100 + 2y - s_2 + y
$$

$$
= 100 + 3y - s_2.
$$

또한

$$
s_1 = 100 + 2x - y
$$

에 대입하면

$$
s_1
= 100 + 2(100 + 2y - s_2) - y
$$

$$
= 300 + 3y - 2s_2.
$$

따라서 dictionary는

$$
z = 100 + 3y - s_2
$$

$$
s_1 = 300 + 3y - 2s_2
$$

$$
x = 100 + 2y - s_2
$$

이다.

현재 $y$의 objective coefficient는 3으로 양수이다.

즉, $y$를 증가시키면 목적함수 값이 증가한다.

그런데 $s_2=0$으로 두고 $y$를 증가시키면

$$
s_1 = 300 + 3y
$$

$$
x = 100 + 2y
$$

이다.

$y$가 아무리 커져도 $s_1$과 $x$는 음수가 되지 않는다.

즉, $y$를 무한히 증가시킬 수 있고, 그에 따라

$$
z = 100 + 3y
$$

도 무한히 커진다.

따라서 이 문제는 unbounded이다.

---

## 16. Phase I의 일반적인 형태

일반적인 inequality constrained LP를 생각하자.

$$
Ax \le b,
\quad
x \ge 0.
$$

slack variable $s$를 추가하면

$$
Ax + s = b,
\quad
x \ge 0,
\quad
s \ge 0
$$

가 된다.

초기 dictionary는

$$
s = b - Ax
$$

이다.

만약 $b$의 모든 성분이 0 이상이면 $x=0$으로 두었을 때 $s=b \ge 0$이므로 초기 dictionary가 feasible하다.

하지만 어떤 $b_i$가 음수이면 초기 dictionary가 infeasible할 수 있다.

이 경우 Phase I 문제를 만든다.

$$
\min t
$$

$$
\text{s.t. } Ax - t\mathbf{1} \le b
$$

$$
x \ge 0,
\quad
t \ge 0.
$$

여기서 $\mathbf{1}$은 모든 성분이 1인 벡터이다.

이 Phase I 문제의 최적값이 0이면 원래 문제는 feasible하다.

반대로 최적값이 양수이면 원래 문제는 infeasible하다.

---

## 17. Equality Constraint의 Phase I

이번에는 제약조건이 등식인 경우를 보자.

$$
Ax = b,
\quad
x \ge 0.
$$

이 경우 Phase I 문제는 다음과 같이 만들 수 있다.

$$
\min t + \sum_{i=1}^{m} s_i
$$

$$
\text{s.t. } Ax - t\mathbf{1} + s = b
$$

$$
x \ge 0,
\quad
s \ge 0,
\quad
t \ge 0.
$$

이 문제의 최적값이 0이라는 것은

$$
t = 0
$$

이고

$$
s = 0
$$

인 해가 존재한다는 뜻이다.

그 경우

$$
Ax = b
$$

를 만족하는 $x \ge 0$가 존재하므로 원래 문제가 feasible하다.

---

## 18. Matrix Form으로 보는 Simplex Method

표준형 선형계획문제를 생각하자.

$$
\max z = c^\top x
$$

$$
\text{s.t. } Ax = b
$$

$$
x \ge 0.
$$

변수 $x$를 basic variables와 non-basic variables로 나눈다.

$$
x =
\begin{bmatrix}
x_B \\
x_N
\end{bmatrix}
$$

행렬 $A$도 이에 맞게 나눈다.

$$
A = [B \ N]
$$

여기서 $B$는 basic variables에 해당하는 열들로 이루어진 행렬이고, $N$은 non-basic variables에 해당하는 열들로 이루어진 행렬이다.

제약조건

$$
Ax = b
$$

는

$$
Bx_B + Nx_N = b
$$

로 쓸 수 있다.

만약 $B$가 invertible이면,

$$
x_B = B^{-1}b - B^{-1}Nx_N
$$

가 된다.

이것이 dictionary의 constraint row에 해당한다.

---

## 19. Reduced Cost

목적함수는

$$
z = c_B^\top x_B + c_N^\top x_N
$$

이다.

여기에

$$
x_B = B^{-1}b - B^{-1}Nx_N
$$

를 대입하면

$$
z
= c_B^\top(B^{-1}b - B^{-1}Nx_N) + c_N^\top x_N
$$

$$
= c_B^\top B^{-1}b - c_B^\top B^{-1}Nx_N + c_N^\top x_N
$$

$$
= c_B^\top B^{-1}b + \left(c_N^\top - c_B^\top B^{-1}N\right)x_N.
$$

즉, objective row는

$$
z =
c_B^\top B^{-1}b
+
\left(c_N - N^\top (B^{-1})^\top c_B\right)^\top x_N
$$

로 쓸 수 있다.

여기서

$$
c_N - N^\top (B^{-1})^\top c_B
$$

를 **reduced cost**라고 한다.

최대화 문제에서 reduced cost가 모두 0 이하이면 현재 dictionary는 optimal이다.

왜냐하면 non-basic variable을 증가시켜도 목적함수 값이 증가하지 않기 때문이다.

---

## 20. 정리

Linear Programming은 선형 목적함수와 선형 제약조건을 가진 최적화 문제이다.

Simplex Method는 이러한 문제를 풀기 위한 대표적인 알고리즘이다.

핵심 흐름은 다음과 같다.

1. 문제를 standard form으로 바꾼다.
2. slack variable을 추가해 inequality constraint를 equality constraint로 바꾼다.
3. 초기 feasible dictionary를 만든다.
4. objective coefficient가 양수인 non-basic variable을 entering variable로 선택한다.
5. ratio test를 통해 leaving variable을 선택한다.
6. pivot을 수행해 새로운 dictionary를 만든다.
7. objective row의 모든 reduced cost가 0 이하이면 최적해이다.
8. 초기 feasible dictionary가 없으면 Phase I을 수행한다.
9. Phase I 최적값이 0이면 feasible이고, Phase II로 넘어간다.
10. Phase I 최적값이 양수이면 원래 문제는 infeasible이다.
11. 어떤 entering variable을 무한히 증가시킬 수 있으면 문제는 unbounded이다.

Simplex Method는 기하학적으로 feasible region의 꼭짓점을 따라 이동하며 목적함수를 개선하는 방법이다. 이 알고리즘은 최악의 경우 exponential time이 걸릴 수 있지만, 실제 문제에서는 매우 잘 작동하기 때문에 지금도 선형계획법에서 중요한 알고리즘으로 사용된다.