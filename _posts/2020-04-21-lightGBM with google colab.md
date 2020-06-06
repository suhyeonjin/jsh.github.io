---
layout: single
title: "lightGBM with google colab"
description:
headline:
modified: 2020-04-21
category: [codeblock]
tags: [ml, lgbm]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---

최근 ML Challenge 할 때, Desktop만으로는 성능이 잘나오지 않아 colab을 애용 중이다. 
주로, Boosting 알고리즘을 많이 사용하는데 그 중 하나가 `lightGBM`. 
colab에서 GPU로 lightgbm을 사용하기 위해서, pip로 설치만 하면 되는 것이 아니라 GPU option으로 Compile 과정이 필요함, 

아래 Cell 그대로 실행하고 import하면 개꿀딱;

```bash
!git clone https://github.com/Microsoft/LightGBM
%cd /content/LightGBM/
!mkdir build
%cd build
!cmake -DUSE_GPU=1
!make -j$(nproc)
%cd /content/LightGBM/python-package
!python3 setup.py install --gpu #--precompile
#!sudo apt-get -y install python-pip
```
