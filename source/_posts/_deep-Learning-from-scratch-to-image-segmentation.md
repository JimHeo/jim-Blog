---
title: 딥러닝, 기초에서 영상 분할까지
date: 2021-07-27 16:33:59
tags:
    - Deep Learning
    - Convolutional Neural Network
    - Image Processing
    - Computer Vision
    - Semantic Segmentation
categories:
    - Machine Learning
author: Jim
mathjax: true
---

## 들어가며

올해 상반기, 나의 최대 관심사는 무엇보다도 **`졸업(!!!)`**이었다. 많은 대학원생들이 으레 그렇듯 예상치 못한 사정으로 졸업이 예정보다 반년 늦어졌고 그마저도 더 늦어지겠다 싶어 모든 우선 순위를 제쳐두고 학위 논문을 준비하게 되었다.

그렇게 1월 말부터 논문을 구상하고 작성하며 시간이 흘렀고, 실험 과정에서 굉장히 많은 시행 착오도 겪었다. 두 번의 발표와 도장을 받기위해 교수님들께 메일을 보내고 돌아다니며 모든 서류 제출을 끝내고 나니 6월이 되어있었다. 그렇게 학위 논문을 모두 제출하고 대학원과 관련된 모든 것을 털어내고서야 드디어 석사(진)이 되었다.

그래도 8월까지는 학생이니만큼 학생 신분의 마지막이 지나기 전에 자축의 의미로 본 포스팅을 작성하기로 하였다. 사실, 2년 반 동안의 지난했던 기간의 회고를 할까도 생각했지만 너무 할 말~~(못 할 말)~~이 많아지기 때문에 다소 식상하지만 학위 논문을 작성하면서 정리했던 딥러닝의 발전사와 개략적인 원리를 포스팅에 옮겨보기로 했다.

<!--more-->

<br>
<center>
<img src="/images/deep-learning-from-scratch-to-image-segmentation/simpson_graduate_student.jpeg" width="50%">
<font size="2" color="gray"> 아..아앗...아니야...!!<font>
</center>
<br>

사실, 이 블로그는 루비콘 사람들과 머신러닝 스터디를 진행하며 내용을 정리하기 위한 목적으로 처음 개설했었다. 그래서 이 블로그의 초반에는 지금 할 얘기와 꽤 겹치는 얘기가 많다.*(스터디가 오래 이어지지는 않았어서 내용이 많지는 않다.)* 아직 많은 포스팅을 하지 않았지만, 당시 글을 썼을 때보다 몇 년의 시간이 지났기 때문에 일종의 리워크로써 본 포스팅을 작성한다.

- [Machine Learning study 1주차](https://jimheo.github.io/2018/10/10/ml-study-week1/)
- [Chain Rule에 대한 추가적인 고찰](https://jimheo.github.io/2018/10/10/extra-consideration-of-chain-rule/)
- [Convolutional Neural Network의 개념](https://jimheo.github.io/2018/10/10/concept-of-cnn/)