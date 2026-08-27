---
title: Generating Functions
tags:
  - math
  - combinatorics
  - generating-functions
---

# Introduction

방학 동안 과학고 학생을 대상으로 심화 확률과 통계 강의를 하며 흥미로운 수학적 아이디어를 한 가지 착안할 수 있었다. 다름아닌 이항계수의 합과 관련한 것으로, 아래와 같은 특별한 이항계수의 부분합을 구하는 문제와 관련된 부분이다.

$$
\binom{n}{0}+\binom{n}{3}+\binom{n}{6}+\cdots = ?
$$

수학의 재미는 이러한 초등적 아이디어가 때때로 학부 전공의 상위 개념의 출발점이 될 수 있다는 데서 촉발된다. 이 경우에도 그러하다. 얼핏 별 쓸모없어 보이는 이항계수의 부분합을 구하는 고등 경시 수준의 아이디어는 이산수학의 핵심 주제인 **Generating Functions**와 연계되어 있다. 그러나 실상 Generating Functions는 조합론의 편의성과 체계성을 돋보이게 해주는 하나의 수단에 지나지 않아 보이기도 한다.

다만 개념의 유용성 및 심화성과는 별개로, 이 개념이 나에게 상위 개념과 초등적 생각 사이의 다리를 놓아 주었다는 점에서, 천천히 이 주제의 motivation과 활용 가능성에 대한 글을 써보고자 한다.

고등학교에서 이항정리를 배울 때 다음과 같은 식을 자주 만난다.

$$
\binom{n}{0}+\binom{n}{1}+\cdots+\binom{n}{n}=2^n
$$

이 식은 이항계수의 합에 관한 하나의 공식처럼 보인다. 하지만 그 출발점은 이항정리이다.

$$
(1+x)^n
=
\binom{n}{0}
+
\binom{n}{1}x
+
\binom{n}{2}x^2
+\cdots+
\binom{n}{n}x^n
$$

여기에 $x=1$을 대입하면,

$$
(1+1)^n
=
\binom{n}{0}+\binom{n}{1}+\cdots+\binom{n}{n}
$$

즉,

$$
2^n
=
\binom{n}{0}+\binom{n}{1}+\cdots+\binom{n}{n}
$$

을 얻는다.

중요한 점은 $\binom{n}{0},\binom{n}{1},\ldots,\binom{n}{n}$이 단순히 나열된 수들이 아니라는 것이다. 이들은 $(1+x)^n$이라는 하나의 다항식에 각 차수의 계수로 저장되어 있다. 이 다항식에 특정 값을 대입하거나, 곱셈과 미분 같은 연산을 적용하면 이항계수에 대한 새로운 성질을 얻을 수 있다.

예를 들어 $x=-1$을 대입하면,

$$
\binom{n}{0}-\binom{n}{1}+\binom{n}{2}-\cdots+(-1)^n\binom{n}{n}=0
\qquad (n\geq 1)
$$

이 된다. 이는 짝수 번째 이항계수의 합과 홀수 번째 이항계수의 합이 서로 같다는 사실을 뜻한다.

이제 자연스러운 질문이 생긴다.

> 이항계수(또는 조합수)는 위와 같은 다항식의 계수로 저장되어 있다. 조합론의 다른 수들, 이를테면 자연수의 분할, 피보나치 수열, 카탈란 수열이나 임의의 수열도 다항식 또는 급수의 계수로 저장할 수 있을까? 수열의 일반항을 찾아야 한다는 강박에서 벗어나, 어떤 수열의 계수를 담고 있는 명료한 다항식만이 제공된다는 점에서, 우리는 이 관점을 중요한 수의 나열을 바라보는 새로운 관점이라고 말할 수 있을 것이다.

# Generating Function

수열

$$
a_0,a_1,a_2,\ldots
$$

에 대하여

$$
A(x)
=
a_0+a_1x+a_2x^2+\cdots
=
\sum_{n=0}^{\infty}a_nx^n
$$

와 같은 식을 생각하자. 이때 $A(x)$를 수열 $\{a_n\}$의 **Generating Function**이라고 부른다. 보다 정확히는 이것을 보통 생성함수(ordinary generating function)라고 한다.

Generating Function의 핵심 아이디어는 간단하다. 수열의 $n$번째 항 $a_n$을 $x^n$의 계수로 옮겨 적는 것이다.

$$
a_n=[x^n]A(x)
$$

여기서 $[x^n]A(x)$는 $A(x)$에서 $x^n$의 계수를 뜻한다.

다시 이항계수의 경우로 돌아가 보자. $k=0,1,\ldots,n$에 대해

$$
a_k=\binom{n}{k}
$$

라고 두고, $k>n$에서는 $a_k=0$이라고 생각하자. 이 수열의 Generating Function은

$$
A(x)
=
\binom{n}{0}
+
\binom{n}{1}x
+
\binom{n}{2}x^2
+\cdots+
\binom{n}{n}x^n
$$

이다. 이항정리에 의해 이는 정확히

$$
A(x)=(1+x)^n
$$

과 같다.

따라서 $(1+x)^n$은 단지 이항정리의 결과가 아니다. 이것은 $n$번째 이항계수 행

$$
\binom{n}{0},\binom{n}{1},\ldots,\binom{n}{n}
$$

을 하나의 식 안에 담아 둔 Generating Function이다.

Generating Function은 이처럼 수열을 함수의 계수로 번역한다. 이 번역을 통해 수열의 합, 점화식, 조합적 선택의 구조처럼 원래는 항마다 따로 다뤄야 했던 문제들을 함수의 연산으로 다룰 수 있게 된다. 이 글에서는 이 기본적인 정의에서 출발하여, Generating Function이 왜 조합론과 수열 문제에서 강력한 도구가 되는지 살펴보려 한다.

# Examples

Generating Function이라는 표현은 다소 추상적으로 들릴 수 있다. 하지만 실제로는 매우 다양한 조합론적 수열이 각자의 방식으로 Generating Function과 연결되어 있다. 여기서는 자연수의 분할과 피보나치 수열을 통해 그 모습을 먼저 살펴보자.

## 자연수의 분할

자연수 $n$의 **분할(partition)** 이란 $n$을 양의 정수들의 합으로 나타내는 방법을 말한다. 단, 덧셈의 순서는 구별하지 않는다.

예를 들어 $4$의 분할은 다음 다섯 가지이다.

$$
4
$$

$$
3+1
$$

$$
2+2
$$

$$
2+1+1
$$

$$
1+1+1+1
$$

따라서 $4$의 분할의 개수는 $5$이다. 일반적으로 $n$의 분할의 개수를 $p(n)$이라고 하자. 그러면 처음 몇 항은 다음과 같다.

$$
p(0)=1,\quad p(1)=1,\quad p(2)=2,\quad p(3)=3,\quad p(4)=5,\quad p(5)=7,\ldots
$$

여기서 $p(0)=1$이라고 두는 것은, 아무것도 더하지 않는 방법을 하나의 분할로 보기 때문이다.

이 수열의 Generating Function은 놀랍게도 다음과 같은 무한곱의 형태를 갖는다.

$$
\sum_{n=0}^{\infty}p(n)x^n
=
\prod_{k=1}^{\infty}\frac{1}{1-x^k}
$$

처음 보면 이 식은 다소 갑작스러워 보인다. 그러나 각각의 항은 매우 직접적인 조합적 의미를 지닌다.

예를 들어 $1$을 분할에 몇 번 사용할지는 다음과 같이 표현된다.

$$
1+x+x^2+x^3+\cdots
=
\frac{1}{1-x}
$$

여기서 $x^r$은 $1$을 $r$개 사용한다는 뜻이다. 마찬가지로 $2$를 몇 번 사용할지는

$$
1+x^2+x^4+x^6+\cdots
=
\frac{1}{1-x^2}
$$

로 표현할 수 있다. 일반적으로 $k$를 몇 번 사용할지는

$$
1+x^k+x^{2k}+x^{3k}+\cdots
=
\frac{1}{1-x^k}
$$

로 표현된다.

따라서 가능한 모든 양의 정수 $k$에 대해 이 식들을 곱하면,

$$
\left(1+x+x^2+\cdots\right)
\left(1+x^2+x^4+\cdots\right)
\left(1+x^3+x^6+\cdots\right)
\cdots
$$

를 얻는다. 이 곱에서 $x^n$의 계수는 합이 $n$이 되도록 각 자연수를 몇 번씩 선택하는 방법의 수가 된다. 바로 이것이 $p(n)$이다.

즉, 자연수의 분할 문제는 다음과 같은 하나의 무한곱으로 정리된다.

$$
\prod_{k=1}^{\infty}\frac{1}{1-x^k}
$$

이 예시는 Generating Function의 곱셈이 단순한 대수적 연산이 아니라, 여러 선택을 동시에 결합하는 조합론적 의미를 가진다는 사실을 보여준다.

## 피보나치 수열

이번에는 전혀 다른 방식으로 정의되는 피보나치 수열을 생각해 보자.

$$
F_0=0,\qquad F_1=1
$$

그리고 $n\geq 2$에 대해

$$
F_n=F_{n-1}+F_{n-2}
$$

로 정의한다. 그러면 수열은

$$
0,1,1,2,3,5,8,13,\ldots
$$

이 된다.

피보나치 수열의 Generating Function을

$$
F(x)=\sum_{n=0}^{\infty}F_nx^n
$$

이라고 하자. 이를 전개하면

$$
F(x)=x+x^2+2x^3+3x^4+5x^5+\cdots
$$

이다.

피보나치 수열의 점화식은 Generating Function의 식으로 그대로 옮길 수 있다. 실제로,

$$
F_n=F_{n-1}+F_{n-2}
$$

라는 관계로부터 다음을 얻는다.

$$
F(x)=xF(x)+x^2F(x)+x
$$

따라서

$$
F(x)-xF(x)-x^2F(x)=x
$$

이고,

$$
F(x)(1-x-x^2)=x
$$

이므로 피보나치 수열의 Generating Function은

$$
F(x)=\frac{x}{1-x-x^2}
$$

가 된다.

이 결과는 중요한 관점을 보여준다. 피보나치 수열은 점화식으로 정의되지만, 그 전체 정보는

$$
\frac{x}{1-x-x^2}
$$

라는 하나의 유리함수에 담길 수 있다.

자연수의 분할에서는 Generating Function의 **곱셈**이 조합적 선택을 나타냈다. 반면 피보나치 수열에서는 Generating Function이 **점화식**을 대수방정식으로 바꾸어 준다. 이처럼 Generating Function은 수열의 성격에 따라 서로 다른 모습으로 등장하지만, 결국 수열 전체의 구조를 하나의 식에 담아낸다는 공통점을 가진다.

# Generating Function을 왜 알아야 할까?

여기까지의 논의만 보면 자연스러운 의문이 남는다.

> Generating Function을 만들었다고 해서 무엇이 달라지는가?

가령 피보나치 수열의 Generating Function은

$$
F(x)=\frac{x}{1-x-x^2}
$$

라는 유리함수로 정리된다. 물론 이 함수를 Taylor expansion하면 피보나치 수열의 각 항을 다시 얻을 수 있다. 하지만 여전히 특정한 $n$에 대해 $F_n$이 무엇인지 직접 알 수는 없는 것처럼 보인다. 단지 수열을 다른 모습으로 써 놓은 것뿐이라면, 굳이 Generating Function을 도입할 이유가 있을까?

핵심은 Generating Function이 수열의 정보를 단순히 저장하는 데서 멈추지 않는다는 데 있다. 수열 자체에서는 잘 보이지 않던 구조가 함수의 연산을 통해 드러난다. 경우에 따라서는 점화식이 대수방정식으로 바뀌고, 복잡한 조합적 선택이 곱셈으로 표현되며, 특정 조건을 만족하는 항들만 골라내는 일도 가능해진다.

가장 먼저 글의 처음에 등장했던 문제로 돌아가 보자.

$$
\binom{n}{0}+\binom{n}{3}+\binom{n}{6}+\cdots
$$

이 합을 어떻게 구할 수 있을까?

## 단위원근 필터: 원하는 계수만 골라내기

이항정리에 의해

$$
(1+x)^n
=
\sum_{j=0}^{n}\binom{n}{j}x^j
$$

이다. 여기서 우리가 원하는 것은 지수가 $3$의 배수인 항의 계수만 더한 값이다.

이를 위해 $x^3=1$의 복소해를 사용하자. $1$이 아닌 한 근을

$$
\omega=e^{2\pi i/3}
$$

라고 두면,

$$
\omega^3=1,
\qquad
1+\omega+\omega^2=0
$$

을 만족한다. 따라서 $x^3=1$의 세 근은

$$
1,\omega,\omega^2
$$

이다.

이제 다음 합을 생각하자.

$$
\frac{1}{3}\left(F(1)+F(\omega)+F(\omega^2)\right)
$$

여기서

$$
F(x)=(1+x)^n
$$

이다. 이를 전개하면

$$
\frac{1}{3}
\sum_{j=0}^{n}
\binom{n}{j}
\left(1+\omega^j+\omega^{2j}\right)
$$

를 얻는다.

그런데 $j$가 $3$의 배수일 때에는

$$
1+\omega^j+\omega^{2j}=3
$$

이고, $j$가 $3$의 배수가 아닐 때에는

$$
1+\omega^j+\omega^{2j}=0
$$

이다. 따라서 위 식에서는 $3$의 배수 차수를 가진 항만 살아남는다.

결국,

$$
\binom{n}{0}+\binom{n}{3}+\binom{n}{6}+\cdots
=
\frac{1}{3}
\left(
F(1)+F(\omega)+F(\omega^2)
\right)
$$

이다. $F(x)=(1+x)^n$을 다시 대입하면,

$$
\binom{n}{0}+\binom{n}{3}+\binom{n}{6}+\cdots
=
\frac{1}{3}
\left(
2^n+(1+\omega)^n+(1+\omega^2)^n
\right)
$$

을 얻는다.

한편,

$$
1+\omega=e^{i\pi/3},
\qquad
1+\omega^2=e^{-i\pi/3}
$$

이므로 이는 다음과 같이도 쓸 수 있다.

$$
\binom{n}{0}+\binom{n}{3}+\binom{n}{6}+\cdots
=
\frac{1}{3}
\left(
2^n+2\cos\frac{n\pi}{3}
\right)
$$

처음의 문제는 정수와 이항계수만 등장하는 문제였지만, 이를 해결하는 과정에서는 복소수와 단위원근이 자연스럽게 등장한다. Generating Function은 여기서 이항계수 전체를 하나의 함수로 묶어 주고, 단위원근은 그중 원하는 차수의 계수만 걸러 내는 필터 역할을 한다.

일반적으로 $k$차 단위원근

$$
\omega=e^{2\pi i/k}
$$

을 이용하면, $k$로 나눈 나머지가 $r$인 이항계수만의 합도 구할 수 있다.

$$
\sum_{\substack{0\leq j\leq n\\j\equiv r\pmod{k}}}
\binom{n}{j}
=
\frac{1}{k}
\sum_{m=0}^{k-1}
\omega^{-rm}(1+\omega^m)^n
$$

이 기법을 **단위원근 필터(roots of unity filter)** 라고 한다.

이 아이디어는 [3Blue1Brown의 *Olympiad level counting (Generating functions)*](https://www.youtube.com/watch?v=bOXCLR3Wric&t=1839s)에서 매우 직관적으로 소개된다. 특히 이 영상은 Generating Function과 복소수가, 겉으로는 전혀 관련 없어 보이는 조합론 문제를 어떻게 풀어내는지 잘 보여 준다.

## 피보나치 수열의 일반항도 얻을 수 있다

피보나치 수열의 Generating Function으로 다시 돌아가 보자.

$$
F(x)=\frac{x}{1-x-x^2}
$$

이 식은 단지 피보나치 수열을 다시 표현한 것처럼 보이지만, 분모를 인수분해하면 새로운 정보가 나타난다.

황금비를

$$
\varphi=\frac{1+\sqrt{5}}{2},
\qquad
\psi=\frac{1-\sqrt{5}}{2}
$$

라고 하면,

$$
1-x-x^2=(1-\varphi x)(1-\psi x)
$$

이다. 따라서 부분분수 분해를 통해

$$
F(x)
=
\frac{1}{\sqrt{5}}
\left(
\frac{1}{1-\varphi x}
-
\frac{1}{1-\psi x}
\right)
$$

를 얻는다.

한편 기하급수 공식에 의해

$$
\frac{1}{1-ax}
=
\sum_{n=0}^{\infty}a^nx^n
$$

이므로,

$$
F(x)
=
\frac{1}{\sqrt{5}}
\sum_{n=0}^{\infty}
\left(
\varphi^n-\psi^n
\right)x^n
$$

이다. $x^n$의 계수를 비교하면,

$$
F_n
=
\frac{\varphi^n-\psi^n}{\sqrt{5}}
$$

를 얻는다. 이것이 바로 피보나치 수열의 일반항인 Binet 공식이다.

즉, Generating Function은 점화식으로만 주어졌던 수열에서 일반항을 추출하는 데 실제로 사용될 수 있다.

## Catalan 수열과 재귀적 구조

Generating Function은 재귀적으로 정의된 조합적 대상에도 자연스럽게 등장한다. 대표적인 예가 Catalan 수열이다.

Catalan 수열

$$
C_0,C_1,C_2,\ldots
$$

은 괄호를 올바르게 여닫는 방법의 수, 다각형의 삼각분할 방법의 수, 이진 탐색 트리의 모양의 수 등 다양한 대상의 개수를 세는 수열이다. 처음 몇 항은

$$
1,1,2,5,14,42,\ldots
$$

이다.

Catalan 수열은 다음 점화식을 만족한다.

$$
C_0=1,
\qquad
C_{n+1}
=
\sum_{k=0}^{n}C_kC_{n-k}
$$

여기서 합

$$
\sum_{k=0}^{n}C_kC_{n-k}
$$

은 $C_n$과 자기 자신 사이의 **convolution**이다. 일반적으로 두 수열 $\{a_n\}$, $\{b_n\}$에 대하여

$$
c_n=\sum_{k=0}^{n}a_kb_{n-k}
$$

로 정의되는 수열 $\{c_n\}$를 두 수열의 convolution이라고 한다. 즉, 첨자의 합이 $n$이 되는 모든 항의 곱을 더하는 연산이다.

이 연산이 중요한 이유는 Generating Function의 곱셈과 정확히 대응하기 때문이다. 두 Generating Function을

$$
A(x)=\sum_{n=0}^{\infty}a_nx^n,
\qquad
B(x)=\sum_{n=0}^{\infty}b_nx^n
$$

라고 하면,

$$
A(x)B(x)
=
\sum_{n=0}^{\infty}
\left(
\sum_{k=0}^{n}a_kb_{n-k}
\right)x^n
$$

이 성립한다.

왜냐하면 $A(x)B(x)$에서 $x^n$ 항은 $x^k$와 $x^{n-k}$를 곱하는 모든 경우에서 나오기 때문이다. 따라서 $x^n$의 계수는 정확히

$$
a_0b_n+a_1b_{n-1}+\cdots+a_nb_0
$$

가 된다.

이제 Catalan 수열의 Generating Function을

$$
C(x)=\sum_{n=0}^{\infty}C_nx^n
$$

라고 하자. 위의 관찰에 의해,

$$
C(x)^2
=
\sum_{n=0}^{\infty}
\left(
\sum_{k=0}^{n}C_kC_{n-k}
\right)x^n
$$

이다. Catalan 수열의 점화식을 대입하면,

$$
C(x)^2
=
\sum_{n=0}^{\infty}C_{n+1}x^n
$$

를 얻는다.

한편,

$$
C(x)-1
=
C_1x+C_2x^2+C_3x^3+\cdots
$$

이고, 이를 한 칸 앞으로 당기면

$$
C(x)-1
=
x\sum_{n=0}^{\infty}C_{n+1}x^n
$$

이다. 따라서

$$
C(x)-1=xC(x)^2
$$

이고, 결국

$$
C(x)=1+xC(x)^2
$$

라는 간단한 대수방정식을 얻는다.

복잡해 보이는 재귀적 조합 구조가 하나의 이차방정식으로 바뀐 셈이다. 이를 풀면,

$$
C(x)
=
\frac{1-\sqrt{1-4x}}{2x}
$$

를 얻는다. 여기서 다른 해가 아니라 위의 해를 택하는 이유는 $C(x)$가 상수항 $C_0=1$을 가져야 하기 때문이다.

이 식을 전개하면 Catalan 수의 일반항

$$
C_n=\frac{1}{n+1}\binom{2n}{n}
$$

까지 얻을 수 있다.

# Euler의 오각수 정리: 분할의 상쇄와 Generating Function

앞에서 자연수 \(n\)의 분할 개수를 \(p(n)\)이라고 하고, 그 Generating Function이

$$
P(x)
=
\sum_{n=0}^{\infty}p(n)x^n
=
\prod_{k=1}^{\infty}\frac{1}{1-x^k}
$$

로 주어진다는 사실을 살펴보았다.

이번에는 이 함수의 역수,

$$
\frac{1}{P(x)}
=
\prod_{k=1}^{\infty}(1-x^k)
$$

에 주목해 보자. 얼핏 보면 모든 차수의 항이 복잡하게 섞인 무한곱처럼 보이지만, 놀랍게도 이 곱은 매우 희소한 형태로 전개된다.

$$
\prod_{k=1}^{\infty}(1-x^k)
=
1-x-x^2+x^5+x^7-x^{12}-x^{15}+x^{22}+x^{26}-\cdots
$$

즉, 대부분의 차수에서는 계수가 \(0\)이고, 특정한 지수에서만 \(1\) 또는 \(-1\)이 남는다. 이 지수들은

$$
\frac{m(3m-1)}{2},
\qquad
\frac{m(3m+1)}{2}
$$

꼴이며, 이를 **일반화된 오각수(generalized pentagonal numbers)** 라고 한다.

이 항등식이 바로 **Euler의 오각수 정리**이다.

$$
\prod_{k=1}^{\infty}(1-x^k)
=
\sum_{m=-\infty}^{\infty}
(-1)^m x^{m(3m-1)/2}
$$

> 왜 수없이 많은 항이 섞여 있는 무한곱에서 대부분의 계수가 사라지고, 일반화된 오각수 차수에서만 계수가 남을까?

이 질문의 답은 \(\prod_{k=1}^{\infty}(1-x^k)\)을 서로 다른 부분으로의 분할로 해석하고, 그 분할들을 서로 상쇄시키는 Franklin involution에 있다.

## \(\prod_{k=1}^{\infty}(1-x^k)\)의 조합론적 의미

각 인수

$$
1-x^k
$$

에서는 두 항 중 하나를 고른다.

- \(1\)을 고르면 \(k\)를 사용하지 않는 것이다.
- \(-x^k\)를 고르면 \(k\)를 정확히 한 번 사용하는 것이다.

따라서

$$
\prod_{k=1}^{\infty}(1-x^k)
$$

를 전개할 때 \(x^n\)이 만들어지는 하나의 방법은 \(n\)을 **서로 다른 자연수의 합**으로 나타내는 하나의 분할에 대응한다.

예를 들어 \(x^5\)를 만드는 서로 다른 부분으로의 분할은

$$
5,\qquad 4+1,\qquad 3+2
$$

이다. 각각에 대응하는 항은 다음과 같다.

$$
5
\quad\longleftrightarrow\quad
-x^5
$$

$$
4+1
\quad\longleftrightarrow\quad
(-x^4)(-x)=+x^5
$$

$$
3+2
\quad\longleftrightarrow\quad
(-x^3)(-x^2)=+x^5
$$

따라서 \(x^5\)의 계수는

$$
-1+1+1=1
$$

이다.

일반적으로 서로 다른 수 \(r\)개를 사용한 분할은 \(-x^k\)를 \(r\)번 고른 것이므로 부호

$$
(-1)^r
$$

를 갖는다. 즉,

$$
[x^n]\prod_{k=1}^{\infty}(1-x^k)
$$

는 \(n\)을 서로 다른 자연수의 합으로 분할하는 방법들을 세되,

- 사용한 항의 개수가 짝수이면 \(+1\),
- 사용한 항의 개수가 홀수이면 \(-1\)

을 부여하여 모두 더한 값이다.

예를 들어 \(6\)의 서로 다른 부분으로의 분할은

$$
6,\qquad 5+1,\qquad 4+2,\qquad 3+2+1
$$

이다. 부호를 고려하면

$$
-1+1+1-1=0
$$

이므로 \(x^6\)의 계수는 \(0\)이다.

그렇다면 일반적으로도 짝수 개의 항을 쓰는 분할과 홀수 개의 항을 쓰는 분할이 서로 짝지어질 수 있을까? 그리고 짝지어지지 않는 예외는 왜 일반화된 오각수에서만 나타날까?

## Franklin involution

서로 다른 자연수의 합으로 이루어진 분할을 큰 수부터 적어

$$
\lambda=(\lambda_1>\lambda_2>\cdots>\lambda_\ell)
$$

라고 하자.

여기서 두 수를 정의한다.

$$
r=\lambda_\ell
$$

는 가장 작은 항의 크기이고,

$$
s
$$

는 맨 앞에서부터 \(1\)씩 연속해서 감소하는 항의 개수이다.

예를 들어

$$
\lambda=(9,8,7,4)
$$

에서는

$$
9,8,7
$$

이 연속해서 \(1\)씩 감소하므로

$$
s=3
$$

이고, 가장 작은 항은 \(4\)이므로

$$
r=4
$$

이다.

Franklin involution은 분할의 합은 보존하면서, 항의 개수를 정확히 하나 늘리거나 줄이는 조작이다.

### 가장 작은 항을 위쪽으로 흡수하기

가장 작은 항 \(r\)을 제거하고, 그 \(r\)개의 단위를 위쪽의 처음 \(r\)개 항에 하나씩 더한다.

예를 들어

$$
(8,7,6,3)
$$

을 생각하자. 이 분할에서는

$$
s=3,\qquad r=3
$$

이다. 맨 아래의 \(3\)을 제거하고, 그 세 단위를 위 세 항에 하나씩 더하면

$$
(8,7,6,3)
\longrightarrow
(9,8,7)
$$

이 된다.

합은 보존된다.

$$
8+7+6+3=24
$$

$$
9+8+7=24
$$

하지만 항의 개수는 \(4\)개에서 \(3\)개로 줄었다. 따라서 두 분할이 만드는 항의 부호는 반대이다.

$$
(-1)^4=+1,
\qquad
(-1)^3=-1
$$

### 위쪽 계단에서 새 항 만들기

반대로 맨 앞의 연속된 \(s\)개 항에서 하나씩 빼고, 그렇게 떼어 낸 \(s\)개의 단위로 새로운 항 \(s\)를 만든다.

예를 들어

$$
(9,8,7,4)
$$

에서는 \(s=3\)이다. 맨 앞 세 항에서 각각 하나씩 빼면

$$
(9,8,7,4)
\longrightarrow
(8,7,6,4)
$$

가 된다. 떼어 낸 단위는 모두 세 개이므로 새로운 항 \(3\)을 붙인다.

$$
(9,8,7,4)
\longrightarrow
(8,7,6,4,3)
$$

역시 합은 보존된다.

$$
9+8+7+4=28
$$

$$
8+7+6+4+3=28
$$

이번에는 항의 개수가 \(4\)개에서 \(5\)개로 늘어났다. 따라서 부호가 바뀐다.

이 두 조작은 서로 역연산이다.

$$
(9,8,7,4)
\longleftrightarrow
(8,7,6,4,3)
$$

한 번 변환한 뒤 다시 변환하면 원래 분할로 돌아온다. 그래서 이 변환을 involution이라고 부른다.

결국 대부분의 서로 다른 부분으로의 분할은 다음처럼 짝지어진다.

$$
\text{짝수 개 항의 분할}
\longleftrightarrow
\text{홀수 개 항의 분할}
$$

두 분할은 같은 \(x^n\)에 기여하지만 부호가 반대이므로 서로 상쇄된다.

## 왜 오각수에서만 상쇄가 실패하는가?

Franklin involution이 실패하는 예외는 정확히 두 종류다.

첫 번째는

$$
(2m-1,2m-2,\ldots,m)
$$

꼴의 분할이다.

예를 들면,

$$
(1),\qquad (3,2),\qquad (5,4,3),\qquad (7,6,5,4),\ldots
$$

이다. 이 분할의 합은

$$
m+(m+1)+\cdots+(2m-1)
=
\frac{m(3m-1)}{2}
$$

이다.

두 번째는

$$
(2m,2m-1,\ldots,m+1)
$$

꼴의 분할이다.

예를 들면,

$$
(2),\qquad (4,3),\qquad (6,5,4),\qquad (8,7,6,5),\ldots
$$

이다. 이 분할의 합은

$$
(m+1)+(m+2)+\cdots+2m
=
\frac{m(3m+1)}{2}
$$

이다.

이 두 종류는 전체가 정확한 계단 모양을 이루고 있어, 위의 두 조작 모두 허용되는 서로 다른 부분으로의 분할을 만들지 못한다.

예를 들어

$$
(3,2)
$$

에서 위 두 항에서 각각 한 칸씩 떼어 새로운 항을 만들려고 하면,

$$
(3,2)
\longrightarrow
(2,1,2)
$$

가 된다. 이를 크기순으로 정리하면

$$
(2,2,1)
$$

인데, \(2\)가 중복된다. 따라서 이는 서로 다른 부분으로의 분할이 아니다.

반대 방향도 불가능하다. 맨 아래의 \(2\)를 제거한 뒤 그 두 단위를 서로 다른 위쪽 행들에 하나씩 나누어 주려 해도, 실제로 남아 있는 위쪽 행은 하나뿐이다.

따라서

$$
(3,2)
$$

는 짝을 찾지 못하고 남는다. 이 분할은 두 항을 사용하므로 부호는

$$
(-1)^2=+1
$$

이며, 실제로 \(x^5\)의 계수는 \(+1\)이다.

마찬가지로

$$
(5,4,3)
$$

은 합이 \(12\)인 예외 분할이다. 세 항을 사용하므로

$$
(-1)^3=-1
$$

을 남기고, 따라서 \(x^{12}\)의 계수는 \(-1\)이 된다.

이처럼 상쇄되지 않고 남는 분할들의 합이 정확히

$$
\frac{m(3m-1)}{2},
\qquad
\frac{m(3m+1)}{2}
$$

꼴이므로, 일반화된 오각수 차수에서만 계수가 남는다.

## 오각수 정리는 왜 중요한가?

오각수 정리는 분할수 \(p(n)\)를 계산하는 점화식을 만든다.

분할수의 Generating Function과 Euler의 오각수 정리를 곱하면,

$$
\left(
\sum_{n=0}^{\infty}p(n)x^n
\right)
\left(
1-x-x^2+x^5+x^7-x^{12}-x^{15}+\cdots
\right)
=
1
$$

을 얻는다.

\(n>0\)일 때 \(x^n\)의 계수는 \(0\)이어야 하므로,

$$
p(n)-p(n-1)-p(n-2)+p(n-5)+p(n-7)-\cdots=0
$$

이다. 따라서

$$
p(n)
=
p(n-1)+p(n-2)-p(n-5)-p(n-7)+p(n-12)+p(n-15)-\cdots
$$

라는 점화식을 얻는다.

일반식으로는 다음과 같다.

$$
p(n)
=
\sum_{m=1}^{\infty}
(-1)^{m+1}
\left[
p\left(n-\frac{m(3m-1)}{2}\right)
+
p\left(n-\frac{m(3m+1)}{2}\right)
\right]
$$

단,

$$
p(0)=1,
\qquad
p(n)=0\quad(n<0)
$$

으로 약속한다.

예를 들어 \(p(8)\)을 구해 보자. \(8\) 이하의 일반화된 오각수는

$$
1,2,5,7
$$

이므로,

$$
p(8)=p(7)+p(6)-p(3)-p(1)
$$

이다. 이미 알고 있는 값

$$
p(7)=15,\qquad p(6)=11,\qquad p(3)=3,\qquad p(1)=1
$$

을 대입하면,

$$
p(8)=15+11-3-1=22
$$

를 얻는다.

즉, 오각수 정리는 다음과 같은 흐름을 보여 준다.

$$
\text{분할의 Generating Function}
\longrightarrow
\text{희소한 역수}
\longrightarrow
\text{분할수의 점화식}
$$

분할의 정의만으로 \(p(n)\)을 구하려면 모든 분할을 직접 세어야 할 것처럼 보인다. 그러나 Euler의 오각수 정리는 일반화된 오각수만큼 이전으로 이동한 항들만 이용해 \(p(n)\)을 계산할 수 있게 해 준다.

더 나아가 이 무한곱은 Dedekind eta 함수와 modular form의 이론으로 이어지고, Ramanujan이 발견한 분할수의 합동식과도 연결된다. 대표적으로,

$$
p(5n+4)\equiv0\pmod 5
$$

가 성립한다.

실제로,

$$
p(4)=5,\qquad p(9)=30,\qquad p(14)=135
$$

는 모두 \(5\)의 배수이다.

Euler의 오각수 정리는 Generating Function이 단지 수열을 기록하는 표기법이 아니라, 조합적 대상 사이의 상쇄 구조를 드러내고 새로운 점화식과 정수론적 성질까지 이끌어 내는 도구임을 보여 주는 대표적인 사례이다.

## References

### Generating Functions 전반

- Herbert S. Wilf, [*generatingfunctionology*](https://www.sciencedirect.com/book/9780127519555/generatingfunctionology).  
  Generating Function의 기본 연산, 점화식, 계수 추출을 폭넓게 다루는 고전적인 입문서.

- Richard P. Stanley, *Enumerative Combinatorics, Volume 1*, 2nd ed., Cambridge University Press.  
  Catalan 수열, 분할, 조합론적 Generating Function을 더 엄밀하게 공부하기 좋은 참고서.

### 단위원근 필터와 조합론적 활용

- 3Blue1Brown, [*Olympiad level counting (Generating functions)*](https://www.youtube.com/watch?v=bOXCLR3Wric).  
  이항계수, Generating Function, 단위원근 필터를 활용해 “합이 특정 수의 배수인 부분집합”을 세는 과정을 직관적으로 설명한다.

- 3Blue1Brown, [*Olympiad level counting (Generating functions)* 수업 페이지](https://www.3blue1brown.com/lessons/subsets-puzzle/).

### 자연수 분할과 Euler의 오각수 정리

- F. Franklin, [*Franklin’s Proof of Euler’s Pentagonal Number Theorem*](https://garsia.math.yorku.ca/~zabrocki/math5020fw1516/documents/PeulersPNT.pdf).  
  오각수 정리와 Franklin involution을 통해 서로 다른 부분으로의 분할이 어떻게 상쇄되는지 설명한다.

- MIT OpenCourseWare, [*Franklin’s combinatorial proof of Euler’s pentagonal number theorem*](https://ocw.mit.edu/courses/18-212-algebraic-combinatorics-spring-2019/resources/mit18_212s19_lec21/).  
  Ferrers diagram을 이용한 Franklin involution의 시각적 설명과 분할 이론의 맥락을 제공한다.

- George E. Andrews, *The Theory of Partitions*, Cambridge University Press, 1998.  
  자연수 분할과 \(q\)-series 이론의 대표적인 참고서.

### 분할수의 점화식, 점근식, 합동식

- G. H. Hardy and S. Ramanujan, [*Asymptotic Formulae in Combinatory Analysis*](https://londmathsoc.onlinelibrary.wiley.com/doi/pdf/10.1112/plms/s2-17.1.75), *Proceedings of the London Mathematical Society*, 1918.  
  분할수 \(p(n)\)의 유명한 Hardy--Ramanujan 점근식을 다룬 원 논문.

- Srinivasa Ramanujan, [*Congruence Properties of Partitions*](https://ramanujan.sirinudi.org/Volumes/published/ram30.html), *Mathematische Zeitschrift*, 1921.  
  다음과 같은 Ramanujan의 분할수 합동식이 등장하는 원전이다.

  $$
  p(5n+4)\equiv0\pmod 5
  $$

  $$
  p(7n+5)\equiv0\pmod 7
  $$

  $$
  p(11n+6)\equiv0\pmod{11}
  $$

- NIST Digital Library of Mathematical Functions, [Unrestricted Partitions](https://dlmf.nist.gov/27.14).  
  분할수의 Generating Function, 점화식, Ramanujan 합동식 등을 간결하게 확인할 수 있는 신뢰도 높은 참고 자료.