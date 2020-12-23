---
title: CRF
tags:
---

의료 진단, 이미지 분할: 변수가 많고, 도출된 결과가 불확실하다. - PGM, Declarative Representation - 주어진 변수가 무엇인지 어떻게 상호작용 하는지 컴퓨터로 표현

**Model:** 자연법칙의 이해를 선언적으로 표현 선언적 ≈ 독립적
모델과 알고리즘은 별개로 존재 - 하나의 모델에 여러개의 알고리즘이 도출될 수 있음, 모델에 대해 상황에 맞는 알고리즘 선택
**Probablistic:** 모델이 많은 양의 불확실성을 다루는데 도움을 줌
불확실성 - 현상에 대한 부분적인 지식 + 노이즈, 오차 등의 발생 -> 모델이 현상을 완전히 표현하지 못함
모든 현상은 본질적으로 확률성을 담고 있음 -> 모델의 한계
확률 이론 - 중요하고 가치있는 것을 가져오는 원칙에 기반하여 불확실성을 처리하는 프레임워크
**Graphical:** 확률 이론 + 컴퓨터과학, 컴퓨터과학의 그래프 연결을 이용하여 많은 변수의 시스템 모델링(표현)
확률 분포 등의 그래프화 -> Random Variable $x_1 ... x_n$의 집합으로 표현 ex) 질병 증세 유무, 연속적인 결과값, 레이블링 되어있는 픽셀
무작위 변수들의 확률 분포에서 특정 상태의 불확실성을 찾거나, 공통 분배(Joint Distribution, $P(x_1 ... x_n)$)를 찾는 것
무작위 변수들은 모두 확률 분포가 존재, 발생가능한 상태의 ex) 2진 변수(참, 거짓)가 $n$개가 있으면, 상태는 $2^n$개 -> 기하급수적으로 늘어남
따라서, 모델을 효과적으로 표현하고 핸들링해야 함

Baysian Network -> 방향성 그래프, 변수는 노드로 표현, 각 노드는 확률 분포 표현 가능, 노드간 Dependency 발생, 의존성을 확률적 연결로 표현
Markov Network -> 비방향성 그래프, 이미지 분할, 픽셀 혹은 슈퍼픽셀의 레이블을 노드로 표현, 인접한 픽셀을 엣지로 연결

확률론적 그래프 모델 (PGM) - 복잡한 현상을 직관적이고 간결한 데이터 구조로 표현 (추상화?, 최적화?), 확률 분포를 Parameter로 표현 -> 고차원의 확률 분포를 몇개의 Parameter로 표현

결합 분포 테이블
1. 특정 변수로 필터링한 확률분포를 Normalization으로 조건부 확률 분포로 변경 가능 $P(I, D, G)$ -> $P(I, D, g^1)$ -> $P(I, D | g^1)$
2. $P(I, D | g^1)$에서 변수 $D$의 $d^0$ 등의 상태에 대해서 변수 $I$가 $i^0, i^1$ 두가지 상태로 있을 경우 Marginalization으로 $i^0, i^1$ 경우의 수를 합산하여 $P(D | g^1)$로 변환

Factor: 가능한 모든 결과의 조합에 대한 실수함수 - 확률분포함수는 팩터의 일종, 그러나 팩터는 항상 합이 1이 될 필요는 없음, 무작위 변수의 집합을 파라미터로 받는 함수 혹은 테이블, Scope는 무작위 변수의 집합 $\{x_1 ... x_n\}$
Factor 연산 - Product, Marginalization(위와 조금 다름), Reduction
Potential Function: Factor의 일종으로 노드 $x_i - x_j$를 잇는 링크 $\phi_{ij}(x_i, x_j)$

Normalization하기 위해서 나누는 값 $Z$가 Partition Function

Pairwise Markov Network - 인접하지 않은 두 변수는 조건부 독립
일반적인 Markov Network = Gibbs Distribution, 변수 간 Fully Connected 되어 있을 수 있음

**Conditional Random Fields:** Markov Random Fields에서 파생된 방법론 형태는 비슷하나 목적이 다름, Task 예측(입력변수로부터 출력변수를 구하는 등), 각 변수에 대한 Partition Function
입력 변수 간의 관련성은 고려하지 않음