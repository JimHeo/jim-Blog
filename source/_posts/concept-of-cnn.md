---
title: Convolution Neural Network의 개념
date: 2018-10-10 17:55:40
tags:
    - Convolution Neural Network
    - Convolution
    - Image Processing
    - Computer Vision
categories:
    - Deep Learning
author: Jim
mathjax: true
---


#### Multi Layer Perceptron의 문제점

해를 거듭할 수록, 정형의 데이터보다는 비정형 데이터가 늘어나는 추세다. 비정형의 데이터를 기존의 MLP로 처리하는 것은 몇가지 단점이 존재한다.
이미지를 예로 들어보자. 이미지는 픽셀단위로 이루어진 행렬이며, 일반적인 이미지의 해상도는 최소 64x64부터 시작하는 경우가 많다.
Grayscale이 아닌 경우에는 채널값을 추가적으로 고려해야 한다. 그렇기 때문에, MLP로 64x64의 컬러 이미지를 처리할 경우에 12,288개의 인풋이 들어가게 된다.
이에 따라, 기존의 MLP에서 Neural Net의 크기는 더 커지게 되며, 학습시간이 굉장히 늘어난다는 단점이 있다. 또한, 이미지의 회전이나 크기 확장 및 축소, 이동과 같은 변환이 일어날 때, 이에 대한 새로운 학습이 필요하다.
그렇기 때문에, 비정형 데이터는 각각의 특성을 고려한 발전된 Neural Net이 필요하다. 비정형 데이터를 효과적으로 학습하기 위해 등장한 것이 CNN, RNN이다. CNN은 이미지에 특화된 Neural Net이며, RNN은 음성이나 자연어와 같은 Sequential한 모델에 특화된 Neural Net이다.

#### Convolution Neural Network의 등장

<center>
<img src="https://t1.daumcdn.net/cfile/tistory/276FC94357AB43B00D">
</center>

CNN의 위의 연구에서 부터 시작한다.
실험 결과, 고양이가 특정 사물을 볼 때, 고양이는 사물의 전체를 한번에 보지 않고 부분적으로 나뉘어 관찰하며, 이 때 고양이의 뇌의 뉴런은 전체가 활성화 되지 않고 부분의 특징마다 서로 다른 시냅스를 통해 일부만 활성화 된다고 한다.
이와 동일하게 CNN은 이미지의 부분별로 Filter를 사용하여 인식을 한다.

<!--more-->

<center>
<img src="https://t1.daumcdn.net/cfile/tistory/2517944D57AB45F018">
</center>

위 그림은 CNN의 일반적인 구조를 시각화한 것이다. CNN은 여러층의 Convolution Layer, Activation Fuction (ReLU 혹은 그와 유사한)이 먼저 구성되며, Pooling Layer를 통해 이미지를 Sampling하게 된다. 이 층들이 몇 번 반복된 후에는 Fully-Connected Layer로 이동 하는데 Fully-Connected Layer는 우리가 앞서 보았던 MLP와 동일하다. Fully-Connected에서는 최종적으로 확률이 결과물로 나와야 하므로 Softmax를 사용한다.
Convolution Layer는 영상처리분야에서 필수 요소로 작용하는 Convolution을 기반으로 한 Layer이다. Convolution은 신호처리에서 먼저 등장한 개념이지만, 영상처리에서 이산적인 Convolution 연산을 통해 우리가 카메라 어플 등에서 흔히 보는 Filtering 부터 Edge Detection 등의 작업을 수행할 수 있다.

#### 이미지의 Convolution

**`Convolution`**, 한글로 번역하면 합성곱이라는 뜻이다. Convolution 연산은 연산을 하고자 하는 픽셀의 주변 픽셀들이 출력에 영향을 미치는 것이다. 다시 말하면, 픽셀 자신과 주변 픽셀들의 Weighted Sum이다.

<center>
<img src="http://i.stack.imgur.com/GvsBA.jpg">
</center>

위 그림은 Convolution의 가장 적절한 시각화 예시 중 하나이다. 그림을 보면, Source Pixel과 주변의 픽셀들을 특정한 행렬과 Element-Wise 곱을 한 후 모두 더해 출력을 낸다.
Source와 곱을 하는 특정 행렬을 Filter(혹은 Convolution Matrix, Kernel, Window)라 부른다. 이 Filter를 Source의 Pixel들에 순차적으로 Sweeping 하면서 계산하여 하나의 Output Matrix를 만드는 것이다. Filter의 크기는 사용자가 지정하며, 일반적으로 3x3 혹은 5x5 Filter를 사용한다.
위를 수식으로 일반화하면 다음과 같다.

$$h[i, j] = f[i, j] * g[i, j] = \sum_{k=1}^n \sum_{l=1}^m f[k, l] g[i - k, j - l]$$

영상처리에서 Filter 내부의 값은 사용자가 직접 설계하며, 이때 값의 총 합은 주로 1이 되도록 하며, Edge Detection의 경우 0이 되도록 한다.
그러나, 딥러닝에서는 컴퓨터가 학습을 통하여 Filter의 값들을 획득한다. 즉, Filter는 MLP에서의 가중치와 동일한 개념이다.
다시 위 그림을 보자. 위 그림은 한번의 Convolution 연산이 끝난 상태이다. 그렇다면, 그 다음의 Convolution 연산을 위해 Filter의 이동이 필요하다. 여기서 Filter를 몇 칸 이동할 것인가를 Stride라고 한다. Stride를 몇으로 설정하는가에 따라 출력데이터의 크기가 달라지는데 출력크기는 입력크기가 NxN이고 필터크기가 FxF 일 때, (N - F) / Stride + 1이 된다.
또한, 그림에서 Source는 7x7의 Matrix이나 출력물은 5x5가 나올 것으로 보인다. 이는 Border 부분의 Pixel은 계산이 안되기 때문이다. 이렇게 되면, Convolution Layer를 한번 거칠 때마다 이미지가 Down Sampling 되어 Layer가 깊어질 수록 데이터가 소실되는 문제가 발생한다.
그래서 Border에 Padding을 생성하여 Down Sampling을 방지한다. Padding의 값은 주로 0으로 설정한다.
이렇게 Convolution을 거쳐 나온 결과물을 Channel이라 한다. 즉, 1개의 필터 당 1개의 채널이 생성되며, 최종 결과물은 각각 다른 가중치의 여러개의 필터로 여러 채널을 생성하여 결과물을 두껍게 만든다. 이렇게 최종적으로 생성된 것을 Activation Map이라 부른다.
이 과정까지가 Convolution Layer 층에서 하는 역할이며, 그 후 ReLU 등의 Activation Fuction을 거친다. 여러개의 Convolution Layer를 구성하여 이 과정들을 반복한다.

#### Pooling Layer

여러 번의 Convoltion Layer를 거치는 중간중간에 Pooling Layer를 한번 씩 넣어 주는데 Pooling은 Sampling, Resizing과 같은 말이다. Pooling을 통해 Resolution을 작게 만들면서 선명했던 이미지의 형체를 점점 뭉뚱그리면 이미지의 Object 들을 더 쉽게 그룹지을 수 있다.
Pooling 기법은 여러가지가 존재하며, 대표적인 기법은 Max Pooling이다. 여러개의 값 중에서 가장 큰 값들로 구성하는 기법이다. 예를 들어, 9x9 행렬은 9개의 3x3 행렬로 쪼갤 수 있다. 3x3 행렬 각각의 element들 중 가장 큰 값을 대푯값으로 내놓는 것이다. 그렇게되면 Pooling을 거친 9x9 행렬은 하나의 3x3 행렬이 된다. 9x9 행렬이 Pooling을 거치면 항상 3x3이 되는 것은 아니고 여기서도 Stride를 설정하여 출력행렬의 크기를 정할 수 있다. Pooling은 그 외에도 최솟값이나 표준편차 등을 사용하는 기법이 존재한다. 핵심은 어떤 한 값을 대푯값으로 정하는 것이다.

CNN의 가장 대표적인 예시로는 LeNet부터 AlexNet, GoogLeNet 혹은 최근에는 YOLO등이 있으며, 성킴 교수님의 블로그를 참조한다.
Ref: <http://pythonkim.tistory.com/54?category=573319>
