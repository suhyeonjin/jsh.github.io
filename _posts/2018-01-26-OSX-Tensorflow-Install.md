---
layout: post
title: "OSX Tensorflow Install"
description:
headline:
modified: 2017-12-31
category: etc
tags: [etc,tensorflow,error]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---


Lab3 예제 code를 Docker에서 실습하던 중에, gvs optimizer 가 type error 뜨는 것을 확인.. 고치려고 별 짓을 다했으나 결국 version 문제인것으로 확인했다… Docker 끄고.. 로컬에 설치 시작..

* 아래처럼 하면, 정상적으로 설치 되지 않는다...

```bash
sudo pip3 install tensorflow
```

문제를 찾아보면, Max OS에서 정상적으로 version이 호환되지 않는 문제인데,, 아래와 같은 error 를 볼 수 있다.

```bash
python3
Python 3.6.3 (default, Oct  4 2017, 06:09:15)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.37)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow
/usr/local/Cellar/python3/3.6.3/Frameworks/Python.framework/Versions/3.6/lib/python3.6/importlib/_bootstrap.py:219: RuntimeWarning: compiletime version 3.5 of module 'tensorflow.python.framework.fast_tensor_util' does not match runtime version 3.6
  return f(*args, **kwds)
>>> quit()
```

좀 뒤져보면, (https://github.com/tensorflow/tensorflow/issues/14273)

![](/images/2018-01-26-OSX-Tensorflow-Install/issue.png)

* https://github.com/lakshayg/tensorflow-build/releases/download/v1.4.1-macosx_10_12-py27-py36-avx-sse41-sse42/tensorflow-1.4.1-cp36-cp36m-macosx_10_12_x86_64.whl

* 위 github 주소에서 직접, version에 맞는 tensor flow를 빌드할 수 있는 whl을 제공하고 있다. 내  Mac 버전과, Python3 버전이 동일한 항목을 찾아 직접 다운로드 해주도록 한다.

```bash
sudo pip3 install --ignore-installed --upgrade https://github.com/lakshayg/tensorflow-build/raw/master/tensorflow-1.4.0-cp36-cp36m-macosx_10_12_x86_64.whl
```

* 위에서 안되던, 예제 Code가 잘~ 돌아가는 것을 확인할 수 있다.



