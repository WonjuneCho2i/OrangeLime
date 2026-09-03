---
title: Linkage disequilibrium
tags:
  - genetics
  - population genetics
  - biostatistics
  - GWAS
  - pharmacogenomics
---

# Linkage disequilibrium

유전체 연구를 보다 보면 “이 SNP가 질병과 연관됐다”, “두 변이가 강한 LD에 있다”, “이 변이는 tag SNP다”라는 표현을 자주 만난다. 처음에는 어려운 유전학 용어처럼 보이지만, linkage disequilibrium, 줄여서 LD의 본질은 비교적 단순하다.

LD는 두 유전자 위치의 대립유전자가 집단에서 독립적으로 조합되지 않는, 즉 통계적 상관을 보이는 현상이다.

이번 글에서는 재조합이 LD를 만드는 이유부터 D, D-prime, r-squared의 의미, 그리고 GWAS와 약물유전체학에서 LD가 실제로 어떻게 활용되는지까지 연결해 본다.

---

## 1. Recombination: 함께 있던 SNP 조합을 섞는 과정

사람은 부모에게서 각각 한 벌의 염색체를 받는다. 감수분열 과정에서는 상동염색체 사이에 교차가 일어나며 DNA 조각이 서로 교환된다. 이를 recombination이라고 한다.

예를 들어 한 염색체에 SNP 두 개가 있고, 한쪽 염색체에는 A-B, 다른 쪽에는 a-b가 있다고 하자.

```text
부모형 haplotype:  AB, ab
재조합형 haplotype: Ab, aB
```

두 SNP 사이에 교차가 일어나지 않으면 AB, ab가 유지된다. 반대로 두 SNP 사이에서 교차가 일어나면 Ab, aB 같은 새 조합이 생긴다.

재조합률을 c라고 하면 값은 0에서 0.5 사이이다.

$$
0 \le c \le 0.5
$$

- c가 0에 가까우면 두 SNP가 가까워 잘 분리되지 않는다.
- c가 0.5에 가까우면 서로 다른 염색체에 있거나 같은 염색체에서 매우 멀어 유전적으로 독립처럼 행동한다.

중요한 점은 재조합률이 50%를 넘지 않는다는 것이다. 멀리 떨어진 두 위치 사이에서는 교차가 여러 번 일어날 수 있지만, 짝수 번의 교차는 표지자 조합을 다시 부모형처럼 보이게 만들 수 있다.

따라서 가까운 SNP는 재조합으로 분리될 기회가 적고, 같은 haplotype 조합으로 함께 전달되기 쉽다. 이것이 가까운 SNP에서 LD가 흔한 이유다. HapMap 프로젝트는 이런 SNP 간 상관 구조를 지도화해 tag SNP를 고르는 기반을 마련했다. [International HapMap Project](https://www.nature.com/articles/nature02168)

다만 물리적으로 가깝다는 것과 LD는 같은 말이 아니다.

- 가까워도 재조합 hotspot, 오래된 변이, 집단의 역사 때문에 LD가 약할 수 있다.
- 멀거나 다른 염색체에 있어도 집단 혼합이나 선택의 영향으로 통계적 연관이 관찰될 수 있다.

즉, linkage는 물리적·유전적 거리의 문제이고, LD는 집단에서 관찰되는 통계적 연관의 문제다.

---

## 2. LD는 2×2 분할표의 독립성 검정이다

두 SNP에 각각 A/a, B/b 대립유전자가 있다고 하자. 한 염색체 사본에서 가능한 haplotype은 네 가지다.

|  | B | b | 합 |
|---|---:|---:|---:|
| A | AB | Ab | A allele frequency |
| a | aB | ab | a allele frequency |
| 합 | B allele frequency | b allele frequency | 1 |

LD 분석의 귀무가설은 다음과 같다.

$$
H_0: P_{AB}=p_Ap_B
$$

즉, A의 존재와 B의 존재가 독립적이라는 가설이다. 이 상태를 linkage equilibrium, 줄여서 LE라고 부른다.

반대로 다음이 성립하면 LD가 있다고 말한다.

$$
P_{AB}\ne p_Ap_B
$$

---

## 3. D: LD의 가장 기본적인 값

LD의 가장 단순한 척도는 다음과 같다.

$$
D=P_{AB}-p_Ap_B
$$

첫 항은 실제로 관찰된 AB haplotype의 빈도이고, 둘째 항은 두 대립유전자가 독립이라면 기대되는 AB haplotype의 빈도다.

통계학적으로 보면 D는 공분산이다. 한 염색체에서 A가 있으면 1, 없으면 0인 변수 X와 B가 있으면 1, 없으면 0인 변수 Y를 정의하면 다음이 성립한다.

$$
\operatorname{Cov}(X,Y)
=E[XY]-E[X]E[Y]
=P_{AB}-p_Ap_B
=D
$$

따라서 D는 두 대립유전자 지표가 독립일 때의 기대값에서 얼마나 함께 증가하거나 감소하는지를 보여 준다.

- D가 양수이면 AB와 ab 조합이 독립일 때보다 많다.
- D가 음수이면 Ab와 aB 조합이 독립일 때보다 많다.
- D가 0이면 두 SNP가 독립적으로 조합된다.

재조합은 이 공분산을 줄이는 방향으로 작용한다. 선택, 돌연변이, 이주, 유전적 부동이 없고 무작위 교배가 일어난다는 단순 모델에서는 다음이 성립한다.

$$
D_{t+1}=(1-c)D_t
$$

따라서 재조합률이 작으면 LD는 천천히 사라지고, 재조합률이 크면 빠르게 약해진다.

---

## 4. Why do we need D-prime and r-squared?

D는 직관적이지만 대립유전자 빈도에 따라 가능한 범위가 달라진다. 따라서 서로 다른 SNP쌍의 D를 그대로 비교하기는 어렵다.

### 4.1 D-prime: 가능한 최대 LD 대비 현재 위치

$$
D'=\frac{D}{D_{\max}}
$$

D-prime은 주어진 대립유전자 빈도에서 가능한 최대 LD 중 현재 LD가 어느 정도인지 보여 준다.

$$
-1\le D'\le1
$$

절댓값이 1이면 네 haplotype 중 하나의 빈도가 0이라는 뜻이다. 즉, haplotype 구조가 매우 제한적이라는 점을 파악하는 데 유용하다.

하지만 D-prime의 절댓값이 1이라고 해서 SNP 하나가 다른 SNP를 완벽하게 예측한다는 뜻은 아니다. 특히 희귀 변이에서는 D-prime이 높아도 실제 예측력은 낮을 수 있다.

### 4.2 r-squared: SNP 하나가 다른 SNP를 얼마나 잘 대변하는가

$$
r^2=
\frac{D^2}
{p_A(1-p_A)p_B(1-p_B)}
$$

r-squared는 두 대립유전자 지표의 상관계수 제곱이다.

- r-squared가 0이면 한 SNP로 다른 SNP를 예측할 수 없다.
- r-squared가 1이면 한 SNP가 다른 SNP를 완벽하게 대변한다.
- r-squared가 높을수록 tag SNP로 쓰기 좋다.

정리하면 D-prime은 haplotype 조합의 구조적 제약을, r-squared는 예측력과 정보의 중복 정도를 더 잘 보여 준다. 대립유전자 빈도가 낮을 때 두 척도가 크게 달라질 수 있다는 점이 특히 중요하다. [LD 척도 비교 연구](https://pmc.ncbi.nlm.nih.gov/articles/PMC1665459/)

---

## 5. Chi-square test: LD가 우연인지 묻는 방법

실제 연구에서는 관찰된 haplotype 분할표가 독립을 가정했을 때의 기대값과 얼마나 다른지 카이제곱 검정으로 확인할 수 있다.

$$
\chi^2=\sum\frac{(O-E)^2}{E}
$$

여기서 O는 관찰값이고 E는 LD가 없다는 귀무가설 아래에서의 기대값이다.

두 SNP의 2×2 haplotype 표에서는 Pearson 카이제곱 통계량이 다음과 연결된다.

$$
\chi^2=nr^2
$$

여기서 n은 분석한 haplotype 수다.

따라서 다음을 반드시 구분해야 한다.

- r-squared는 연관의 크기, 즉 효과크기다.
- p값 또는 카이제곱값은 그 연관이 우연으로 보기 어려운지를 보여 준다.

표본이 매우 크면 r-squared가 작아도 통계적으로 유의할 수 있다. 이 경우 LD가 존재한다는 말은 맞지만, 해당 SNP가 실무적으로 좋은 tag SNP라는 뜻은 아닐 수 있다.

반대로 유의하지 않다고 LD가 없다고 단정할 수도 없다. 표본 수 부족, 희귀 대립유전자, 부정확한 phasing 때문에 검정력이 낮을 수 있기 때문이다.

또한 LD 카이제곱 검정과 Hardy-Weinberg equilibrium 검정은 다르다.

- HWE 검정은 한 locus 안에서 genotype 분포가 기대값과 맞는지 본다.
- LD 검정은 두 locus 사이에서 haplotype 조합이 독립적인지 본다.

---

## 6. Why is LD important in GWAS?

GWAS에서는 수백만 개의 SNP를 동시에 검사한다. 하지만 모든 변이를 직접 완벽히 측정하지 못하는 경우도 많다. 이때 LD가 매우 유용하다.

어떤 SNP A가 질병과 통계적으로 연관되었다고 하자. 가능한 해석은 세 가지다.

```text
1. SNP A 자체가 기능 변이이자 원인이다.
2. SNP A는 실제 원인 변이 B와 LD에 있는 표지자다.
3. 집단구조나 기술적 편향이 연관처럼 보이게 했다.
```

실제로는 두 번째 경우가 흔하다. GWAS hit는 원인 변이를 직접 찾았다기보다, 원인 변이가 있을 가능성이 높은 LD block을 찾은 것인 경우가 많다.

그래서 GWAS 후에는 보통 다음 과정이 이어진다.

```text
연관 SNP 발견
→ 주변 LD block 확인
→ 가능한 기능 변이 후보 좁히기
→ fine-mapping
→ 세포·분자 수준의 기능 검증
```

LD는 원인 자체가 아니라, 원인 변이로 향하는 지도다.

---

## 7. LD의 연구·실무 활용

### 7.1 Tag SNP 선정

강한 LD에 있는 SNP들은 서로 비슷한 정보를 담고 있다. 따라서 모든 SNP를 다 검사하지 않고도 대표 SNP 몇 개를 선택해 주변 변이를 간접적으로 포착할 수 있다.

이것이 SNP chip과 초기 GWAS 설계가 가능해진 중요한 이유다.

### 7.2 Imputation

일부 SNP만 직접 genotyping한 뒤, 참조 패널의 LD 구조를 이용해 측정하지 않은 변이의 유전자형을 확률적으로 추정할 수 있다.

이때 해당 변이가 주변 표지 SNP와 높은 r-squared를 가질수록 추정의 신뢰도도 높아진다.

### 7.3 LD pruning과 clumping

상관된 SNP를 모두 분석에 넣으면 같은 유전 신호를 여러 번 세는 문제가 생길 수 있다. 그래서 PCA, polygenic risk score, 회귀모형, GWAS 후속 분석에서는 높은 LD의 SNP를 정리하거나 대표 SNP를 고르는 작업을 한다.

### 7.4 Pharmacogenomics

약물대사효소, 수송체, HLA 유전자 주변의 변이를 볼 때도 LD는 중요하다.

어떤 SNP가 약물 독성과 연관되더라도, 그 SNP가 실제 원인인지 아니면 기능 변이와 함께 유전되는 표지자인지 구분해야 한다. 인종과 조상집단에 따라 LD 구조가 다르므로, 한 집단에서 발견한 약물유전체 표지가 다른 집단에서도 같은 정확도로 작동한다고 가정하면 안 된다.

### 7.5 Population history and evolution

LD는 단순한 질병 연구 도구가 아니다. 재조합 hotspot, 자연선택, 집단 병목, 이주와 혼합, 변이의 나이 같은 집단의 역사를 반영한다.

같은 유전체 구간이라도 집단마다 LD block의 길이와 형태가 달라질 수 있다. 따라서 LD 결과를 해석할 때는 분석 집단의 조상과 표본 구성을 함께 봐야 한다.

---

## Conclusion

LD는 유전학의 특수한 개념처럼 보이지만, 통계학적으로는 두 이진 변수의 결합분포를 분석하는 문제다.

$$
D=\text{covariance}
$$

$$
r^2=\text{standardized association strength and predictive power}
$$

$$
\chi^2=nr^2=\text{independence test adjusted for sample size}
$$

재조합은 이 상관을 세대마다 약화시키고, 유전체 연구는 남아 있는 상관 구조를 이용해 아직 직접 보이지 않는 변이를 추적한다.

결국 LD는 같이 유전되는 SNP의 현상을 넘어, GWAS와 정밀의학이 유전정보를 읽는 핵심 언어라고 할 수 있다.
