---
layout: single
title: "[2018_AceBear_CTF] STEg Challenge(996)"
description:
headline:
modified: 2018-02-09
category: [CTF]
tags: [2018_acebear, Forensic, MISC, Writeup, steganography]
imagefeature:
mathjax:
chart:
comments: true
featured: true
toc: true
toc_sticky: true
---

## Exercise

> Download: Link
Service: nc 35.200.176.244 33338
author: komang4130

![](/assets/images/2018-02-09-AceBear-CTF-STEg-Challenge-996/exercise.png)
<p align='center'><i>[그림] exercise</i></p>


## Solution

AceBear CTF 에서 나왔던 Forensic/MISC 분야 중, 마지막 풀이 문제. JS 문제랑 마찬가지로 대회 당시에는 해결하지 못했다. 문제 제목에 대해서 조금만 더 고찰을 했었더라면 해결할 수 있었을텐데...

link를 통해, 받은 rar 파일을 압축 해제하면, `flag_enc.png` 파일을 하나 확인할 수 있다.

![](/assets/images/2018-02-09-AceBear-CTF-STEg-Challenge-996/flag_enc.png)
<p align='center'><i>[그림] flag_enc.png</i></p>


이미지를 자세히 보면, 좌측 상단에 검정색 pixel 일부를 볼 수 있다. 당연히, pixel을 이용한 steganography 방식의 LSB 로 인식하고 접근했었는데 별짓을 다해도 의미있는 문자열을 확인할 수 없었던게 큰 문제였던 것 같다.


문제를 보면, nc 로 socket 접속할 수 있는 server 가 하나 주어지는데, 접속해보면 아래와 같이 16byte의 data를 받아 base64로  encode 된 data를 receive할 수 있었다.
![](/assets/images/2018-02-09-AceBear-CTF-STEg-Challenge-996/data_recieve.png)
<p align='center'><i>[그림] encode data</i></p>


decode 하여, 확인해보면 flag_enc와 동일한 이미지인 것을 확인할 수 있는데, 자세히 보면 상단의 pixel 위치가 조금씩 다르다.
![](/assets/images/2018-02-09-AceBear-CTF-STEg-Challenge-996/file1.png)
<p align='center'><i>[그림] decode data</i></p>


여기서 생각의 전환이 조금 필요했다. server로 부터 어떤 유의미한 데이터를 얻어낼 수 있는가 고민을 하다가 결국, 원점으로 돌아가 flag_enc 로부터 정보를 얻어내려 했던 것이 발목을 잡았다..

결과적으로, 문제 제목에서 알 수 있듯이 `STEg`, `flag_enc` 라는 문자열에 주목해보자. enc라는 문자열은 무엇인가 encrypt 되었다는 것을 의미한다. 그렇다면, 어떤 로직을 통해 data가 encrypt 되었다는 것인데 pixel과 Image를 Steganography 적용하는 방식에서 사용될 수 있는 항목은 주로 lsb일 것이다.

기존에 알고 있던 lsb 방식은 모두 이용해 보았지만 별 소득은 없었고, 구글을 뒤지다가 Python 과 관련된 도구에서 `Stepic`라는 것을 찾을 수 있었다. (Python Steganography 라는 언급만 있었어도...ㅂㄷㅂㄷ)

![](/assets/images/2018-02-09-AceBear-CTF-STEg-Challenge-996/stepic.png)
<p align='center'><i>[그림] stepic stegano</i></p>


연속된 3개의 pixel 에 data를 은닉하는 방식의 lsb 인데, code 그대로 사용해 unhide 할 수 있다. (https://stackoverflow.com/questions/16838341/steganography-in-python-stepic)
```python
from PIL import Image

def decode_imdata(imdata):
    '''Given a sequence of pixels, returns an iterator of characters
    encoded in the image'''

    imdata = iter(imdata)
    while True:
        pixels = list(imdata.next()[:3] + imdata.next()[:3] + imdata.next()[:3])
        byte = 0
        for c in xrange(7):
            byte |= pixels[c] & 1
            byte <<= 1
        byte |= pixels[7] & 1
        yield chr(byte)
        if pixels[-1] & 1:
            break


im = Image.open('test')
data  = ''.join(decode_imdata(im.getdata())).decode('hex')
print data
```
<br>

위 code로 서버로부터 받은 `encode data`와 `flag_enc.png`를 unhide 했을 때, 아래와 같은 문자열을 얻을 수 있었다. 무엇인가 복호화가 된다는 사실이 일단 실마리이다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/STEg
 python ./steg.py
|vohenrbuq{nxe
```
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/STEg
 python ./steg.py
53:)~>1&v1z4hu
```


굳이, server로부터 encode data를 주는 이유는 무엇일까? 해당 data로부터 알아내야할 것이 있다는 말이다. `flag_enc.png`로부터 unhide 했을 때, 별 소득이 없었으므로 해당 결과 값에 영향을 미칠만한 항목을 찾아냈어야 했다. (이 부분에서도 꽤 많은 시간을 쏟았다.)

여러 data 를 server 로 send 하다가, `\x00` 로만 16byte를 채워보내고 받은 encode data에 대해서 unhide 한 결과 식별할 수 있는 string(`OMG_YOU_BREAK_IT`)을 얻을 수 있었다.

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/STEg
 python -c “print ‘\x00’*16” | nc localhost 33338 | base64 -D > tmp.png

jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/STEg
 python ./steg.py
OMG_YOU_BREAK_IT
```


또 여기서, 삽질했던게 Null로 얻은 문자열 길이는 16byte인데 flag_enc.png 에서 얻은 문자열 길이는 14byte이다.. 기왕 내는거 길이 좀 맞춰서 내지;; 14byte 까지만 각각의 문자열을 xor 할 경우 flag를 확인할 수 있었다;;;
```python
from PIL import Image

def decode_imdata(imdata):
    '''Given a sequence of pixels, returns an iterator of characters
    encoded in the image'''

    imdata = iter(imdata)
    while True:
        pixels = list(imdata.next()[:3] + imdata.next()[:3] + imdata.next()[:3])
        byte = 0
        for c in xrange(7):
            byte |= pixels[c] & 1
            byte <<= 1
        byte |= pixels[7] & 1
        yield chr(byte)
        if pixels[-1] & 1:
            break

def strxor(a,b):
    return ''.join(chr(ord(x)^ord(y)) for (x,y) in zip(a,b))


im = Image.open('flag_enc.png')
data  = ''.join(decode_imdata(im.getdata())).decode('hex')
print data


im2 = Image.open('tmp.png')
data2 = ''.join(decode_imdata(im2.getdata())).decode('hex')
print data2


print ''.join(chr(ord(x)^ord(y)) for (x,y) in zip(data,data2))
```

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/STEg
 python ./steg.py
53:)~>1&v1z4hu
OMG_YOU_BREAK_IT
zStep1k_st3p1k!!
```


> 솔직히, 맞추라고 낸 문제인가 애매하다.. Forensic이 아니라 그냥 MISC 느낌.. 어거지로 끼워 만든 문제 느낌이 너무 강하고, 쓸데 없는 삽질이 너무 많이 필요했다...;;

<p align='right'><strong>AceBear{zStep1k_st3p1k!!}</strong></p>
