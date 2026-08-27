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
