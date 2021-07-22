---
title: Segmentation Neural Network List Up
date: 2019-03-12 20:52:45
tags:
    - CVPR
    - MICCAI
    - ICCV
    - ECCV
    - Semantic Segmentation
    - Instance Segmentation
    - Image Processing
    - Computer Vision
categories:
    - Deep Learning
author: Jim
---

> ***Migration to [Meta Sheet](https://jiminthebox.notion.site/Meta-Sheet-0482e3e50a144710b25331689d187e16/)***

* * *

### U-Net: Convolutional Networks for Biomedical Image Segmentation

MICCAI 2015
* * *

#### Paper

- <https://arxiv.org/pdf/1505.04597.pdf>

#### Source Code Repository

- Official Model: <https://lmb.informatik.uni-freiburg.de/resources/opensource/unet/>
  - Framework: Caffe
- Reproduce Model: <https://github.com/jakeret/tf_unet>
  - Framework: Tensorflow
  - Paper: <https://arxiv.org/pdf/1609.09077.pdf> in Astronomy and Computing 2017
  - Documentation: <https://tf-unet.readthedocs.io/en/latest/installation.html>
- Reproduce Model: <https://github.com/zhixuhao/unet>
  - Framework: Keras
- Reproduce Model: <https://github.com/milesial/Pytorch-UNet>
  - Framework: PyTorch

<!--more-->

#### Train Model Code, Test Model Code

- Train Model Code
  - Reproduce Model(Tensorflow) Supports API
    - <https://tf-unet.readthedocs.io/en/latest/usage.html>
  - Reproduce Model(Keras)
    - <https://github.com/zhixuhao/unet/blob/master/trainUnet.ipynb>
- Test Model Code:
  - Reproduce Model(Tensorflow) Supports API
    - <https://tf-unet.readthedocs.io/en/latest/usage.html>
  - Reproduce Model(Keras)
    - <https://github.com/zhixuhao/unet/blob/master/trainUnet.ipynb>

**REMARK** Reproduce Model(Keras) supports data augmentation docs

- <https://github.com/zhixuhao/unet/blob/master/dataPrepare.ipynb>

#### Pre-Trained Model

- Unsupported

#### Prediction Examples

![UNET](/images/segmentation-neuralnet-listup/unet_ex.png)

Ref: <https://arxiv.org/pdf/1505.04597.pdf>

#### Citation

```
@article{akeret2017radio,
  title={Radio frequency interference mitigation using deep convolutional neural networks},
  author={Akeret, Joel and Chang, Chihway and Lucchi, Aurelien and Refregier, Alexandre},
  journal={Astronomy and Computing},
  volume={18},
  pages={35--39},
  year={2017},
  publisher={Elsevier}
}
```
* * *

</br>

* * *

### RefineNet: Multi-Path Refinement Networks for High-Resolution Semantic Segmentation

CVPR 2017
* * *

#### Paper

- <https://arxiv.org/pdf/1611.06612.pdf>

#### Source Code Repository

- Official Model: <https://github.com/guosheng/refinenet>
  - Framework: MATLAB
- Reproduce Model: <https://github.com/DrSleep/refinenet-pytorch>
  - Framework: PyTorch
- Reproduce Model: <https://github.com/DrSleep/light-weight-refinenet>
  - Light Weight RefineNet
  - Framework: PyTorch
- Reproduce Model: <https://github.com/eragonruan/refinenet-image-segmentation>
  - Framework: Tensorflow

#### Train Model Code, Test Model Code

- Train Model Code: Supported
- Test Model Code: Supported

#### Pre-Trained Model

- Supported
  - <https://github.com/guosheng/refinenet/blob/master/libs/matconvnet/doc/site/docs/pretrained.md>

#### Prediction Examples

![REFINENET](/images/segmentation-neuralnet-listup/refinenet_ex.png)

Ref: <https://arxiv.org/pdf/1611.06612.pdf>

<center>
<img src="https://camo.githubusercontent.com/8acf71017ce0d3cb059f01b8bfedcd6792db3f9e/687474703a2f2f696d672e796f75747562652e636f6d2f76692f4c3056367a6d47505f6f512f302e6a7067">
</center>

Ref: <https://github.com/guosheng/refinenet>

#### Citation

```
@inproceedings{Lin:2017:RefineNet,
  title = {Refine{N}et: {M}ulti-Path Refinement Networks for High-Resolution Semantic Segmentation},
  shorttitle = {RefineNet: Multi-Path Refinement Networks},
  booktitle = {CVPR},
  author = {Lin, G. and Milan, A. and Shen, C. and Reid, I.},
  month = jul,
  year = {2017}
}
```
* * *

</br>

* * *

### PSPNet: Pyramid Scene Parsing Network

CVPR 2017, The Winner in 2016 ILSVRC Scene Parsing Challenge
* * *

#### Paper

- <https://arxiv.org/pdf/1612.01105.pdf>

#### Source Code Repository

- Official Model: <https://github.com/hszhao/PSPNet>
  - Framework: Caffe
- Reproduce Model: <https://github.com/Vladkryvoruchko/PSPNet-Keras-tensorflow>
  - Framework: Keras
- Reproduce Model: <https://github.com/hellochick/PSPNet-tensorflow>
  - Framework: Tensorflow

#### Train Model Code, Test Model Code

- Train Model Code: Supported in Reproduce Model
  - <https://github.com/Vladkryvoruchko/PSPNet-Keras-tensorflow>
- Test Model Code: Supported in Reproduce Model
  - <https://github.com/Vladkryvoruchko/PSPNet-Keras-tensorflow>

#### Pre-Trained Model

- Supported
  - Offical Model
    - <https://github.com/hszhao/PSPNet>
- Supported
  - Reproduce Model
    - <https://github.com/Vladkryvoruchko/PSPNet-Keras-tensorflow>

#### Prediction Examples

<center>
<img src="https://hszhao.github.io/projects/pspnet/figures/voc2012_visual.png">
</center>

Ref: <https://hszhao.github.io/projects/pspnet/>

#### Citation

```
@inproceedings{zhao2017pspnet,
  author = {Hengshuang Zhao and
            Jianping Shi and
            Xiaojuan Qi and
            Xiaogang Wang and
            Jiaya Jia},
  title = {Pyramid Scene Parsing Network},
  booktitle = {Proceedings of IEEE Conference on Computer Vision and Pattern Recognition (CVPR)},
  year = {2017}
}
```
* * *

</br>

* * *

### Large Kernel Matters: Improve Semantic Segmentation by Global Convolutional Network

CVPR 2017
* * *

#### Paper

- <https://arxiv.org/pdf/1612.01105.pdf>

#### Source Code Repository

- Official Model: None

**REMARK** Large Kernel Matters를 Tensorflow로 구현한 코드 및 설명이 기술된 한국 블로그

- <http://research.sualab.com/practice/2018/11/23/image-segmentation-deep-learning.html>

#### Train Model Code, Test Model Code

- Train Model Code: Unsupported
- Test Model Code: Unsupported

#### Pre-Trained Model

- Unsupported

#### Prediction Examples

![GCN](/images/segmentation-neuralnet-listup/GCN_ex.png)

Ref: <https://arxiv.org/pdf/1703.02719.pdf>
* * *

</br>

* * *
  
### DeepLab v3(+): Atrous SeparableConvolution for Semantic Image Segmentation

ECCV 2018
* * *

#### Paper

- DeepLab v3: <https://arxiv.org/pdf/1706.05587.pdf>
- DeepLab v3+: <https://arxiv.org/pdf/1802.02611.pdf>

#### Source Code Repository

- Official Model: <https://github.com/tensorflow/models/tree/master/research/deeplab>
  - Framework: Tensorflow

#### Train Model Code, Test Model Code

- Train Model Code: Supported
- Test Model Code: Supported

#### Pre-Trained Model

- Supported

#### Prediction Examples

<center>
<img src="https://raw.githubusercontent.com/tensorflow/models/master/research/deeplab/g3doc/img/vis1.png">
</center>

Ref: <https://github.com/tensorflow/models/tree/master/research/deeplab>

#### Citation

```
@inproceedings{deeplabv3plus2018,
  title={Encoder-Decoder with Atrous Separable Convolution for Semantic Image Segmentation},
  author={Liang-Chieh Chen and Yukun Zhu and George Papandreou and Florian Schroff and Hartwig Adam},
  booktitle={ECCV},
  year={2018}
}
```
* * *


</br>


* * *

### Inplace ABN: In-Place Activated BatchNorm for Memory-Optimized Training of DNNs

CVPR 2018
* * *

#### Paper

- <https://arxiv.org/pdf/1712.02616v3.pdf>

#### Source Code Repository

- Official Model: <https://github.com/mapillary/inplace_abn>
  - Framework: PyTorch

#### Train Model Code, Test Model Code

- Train Model Code: Supported
- Test Model Code: Supported

#### Pre-Trained Model

- Supported

#### Citation

```
@inproceedings{rotabulo2017place,
  title={In-Place Activated BatchNorm for Memory-Optimized Training of DNNs},
  author={Rota Bul\`o, Samuel and Porzi, Lorenzo and Kontschieder, Peter},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  year={2018}
}
```
* * *

</br>

* * *

### TernausNetV2: Fully Convolutional Network for Instance Segmentation

2nd place in CVPR 2018 DeepGlobe Building Extraction Challenge
* * *

#### Paper

- <http://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w4/Iglovikov_TernausNetV2_Fully_Convolutional_CVPR_2018_paper.pdf>

#### Source Code Repository

- Official Model: <https://github.com/ternaus/TernausNetV2>
  - Framework: PyTorch

#### Train Model Code, Test Model Code

- Train Model Code: Unsupported
- Test Model Code: Unsupported

#### Pre-Trained Model

- Unsupported

#### Prediction Example

<center>
<img src="https://camo.githubusercontent.com/c3c8b42313d139c6d562880cb4725f2b453e4ec7/68747470733a2f2f686162726173746f726167652e6f72672f776562742f6b6f2f62322f74772f6b6f62327477686a7a6a666e61756978376c6a74656430376761382e706e67">
</center>

Ref: <https://github.com/ternaus/TernausNetV2>

#### Citation

```
@InProceedings{Iglovikov_2018_CVPR_Workshops,
     author = {Iglovikov, Vladimir and Seferbekov, Selim and Buslaev, Alexander and Shvets, Alexey},
      title = {TernausNetV2: Fully Convolutional Network for Instance Segmentation},
  booktitle = {The IEEE Conference on Computer Vision and Pattern Recognition (CVPR) Workshops},
      month = {June},
       year = {2018}
}
```
* * *


</br>

* * *

### Automatic Instrument Segmentation in Robot-Assisted Surgery Using Deep Learning

ICMLA 2018, Wining solution and its improvement for MICCAI 2017 Robotic Instrument Segmentation Sub-Challenge
* * *

#### Paper

- <https://arxiv.org/pdf/1803.01207.pdf>

#### Source Code Repository

- Official Model: <https://github.com/ternaus/TernausNetV2>
  - Framework: PyTorch

#### Train Model Code, Test Model Code

- Train Model Code: Supported
- Test Model Code: Supported

#### Pre-Trained Model

- Supported in <https://drive.google.com/drive/folders/13e0C4fAtJemjewYqxPtQHO6Xggk7lsKe>

#### Prediction Example

<center>
<img src="https://raw.githubusercontent.com/ternaus/robot-surgery-segmentation/master/images/grid-1-41.png">
</center>

Ref: <https://github.com/ternaus/robot-surgery-segmentation>

#### Citation

```
@article{shvets2018automatic,
title={Automatic Instrument Segmentation in Robot-Assisted Surgery Using Deep Learning},
author={Shvets, Alexey and Rakhlin, Alexander and Kalinin, Alexandr A and Iglovikov, Vladimir},
journal={arXiv preprint arXiv:1803.01207},
year={2018}
}
```
* * *
