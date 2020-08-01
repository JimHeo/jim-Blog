---
title: Chain Rule에 대한 추가적인 고찰
date: 2018-10-10 17:54:42
tags:
    - Computational Graph
    - Chain Rule
    - Derivative
    - Backpropagation
categories:
    - Deep Learning
author: Jim
mathjax: true
---


### Chain Rule

`Deep Neural Network`에서는 Feed Forward를 하여 나온 값과 기존의 정답 레이블 사이의 에러율을 줄이기 위한 가장 기본적인 방법으로 Gradient Descent 알고리즘을 사용한다.
`Gradient Descent` 알고리즘은 이를 위해서 Back Propagation을 하면서 최적의 가중치를 구하도록 한다.
최적의 가중치는 기존의 가중치가 에러율에 미치는 영향을 구한 후 이 영향을 점차 감소시켜서 구할 수 있다.
즉, 이 과정에서 `편미분`이 사용되는 것이다. 편미분은 여러개의 파라미터가 있을 때, 하나의 파라미터가 약간 변화하면 Output이 얼마나 크게 변화하는지 확인할 수 있는 도구이다.
그러나, 딥러닝은 Multi-Layer로 구성되어 있다. 수많은 다중 함수들의 집합이라고 봐도 무방하다.
다시 말해, 가장 처음의 입력에 대한 가중치를 업데이트 해야 하는데 이 가중치의 편미분값을 구하기가 어렵다는 것이다.
이때 도입되는 개념이 `Chain Rule`이다. 정확히는 도입이 되었다기 보다는 수학적인 측면에서 보았을 때, 편미분에 당연하게 사용되는 것이다.

#### 초심자의 입장

딥러닝을 처음 접하는 혹은 수학에 대하여 큰 지식이 있지 않은 사람의 입장에서 Chain Rule은 그리 간단하지는 않다.
개념에 대해 이해를 하고 난 후라도 Multi-Layer에 대해 직접 Chain Rule을 적용한다면 굉장히 헷갈리기 시작한다.
그래서 가장 단순하게 이해를 하기 위해서 Sigmoid 함수 하나만을 가지고 Chain Rule을 적용해보기로 한다.

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

위 수식이 Sigmoid 함수다. 미분 공식을 어느정도 안다고 하더라도 위 식을 미분한 결과를 내는 것은 쉽지 않을 것이다.
그렇다면 위의 식을 잘게 쪼개보자.

<!--more-->

$$
\begin{cases}
f(z) = -z \cr
g(f) = e^f \cr
h(g) = 1 + g \cr
i(h) = \frac{1}{h}
\end{cases}
$$

위 4개의 수식으로 잘게 나눈다면,

$$\sigma(z) = i(h(g(f(z))))$$

가 성립된다.
여기서 $\sigma(z)$을 미분한다는 것은 $z$의 변화량을 확인하는 것이다.
우변의 측면에서 이를 다시보면 다음과 같이 표현이 가능하다.

$$
\frac{\partial \sigma}{\partial z} = \frac{\partial i}{\partial z} = \frac{\partial i}{\bcancel{\partial h}} * \frac{\bcancel{\partial h}}{\bcancel{\partial g}} * \frac{\bcancel{\partial g}}{\bcancel{\partial f}} * \frac{\bcancel{\partial f}}{\partial z}
$$

즉, $\frac{\partial i}{\partial h} * \frac{\partial h}{\partial g} * \frac{\partial g}{\partial f} * \frac{\partial f}{\partial z} = f^\prime(z) * g^\prime(f) * h^\prime(g) * i^\prime(h)$ 이므로,
4개의 함수에 대한 미분을 각각 한 후에 서로 곱해주기만 하면, Sigmoid 함수의 미분함수가 나온다.
4개의 함수를 미분하면 아래와 같다.

$$
\begin{cases}
f^\prime(z)=-1 \cr
g^\prime(f)=e^f \cr
h^\prime(g)=1 \cr
i^\prime(h)=-\frac{1}{h^2}
\end{cases}
$$

이제 이들을 곱해준다.

$$
\begin{matrix}
f^\prime(z)g^\prime(f)h^\prime(g)i^\prime(h) &=& (-1) * e^f * 1 * (-\frac{1}{h^2}) \cr
&=& \frac{e^f}{h^2} \cr
&=& \frac{e^{-z}}{(1+e^{-z})^2} \cr
&=& \sigma(z) * (1-\sigma(z))
\end{matrix}
$$

최종적으로 Sigmoid 함수를 Chain Rule을 이용한 값은 위와 같은 결과가 나오는데 이는 일반적으로 알려진 Sigmoid 함수의 미분 값과 동일하다.
이처럼 복잡한 다중함수의 미분은 Chain Rule을 이용하면 가장 단순하게 풀이가 가능하다.
Back Propagation의 과정 또한 Chain Rule을 적용할 수 있다.
