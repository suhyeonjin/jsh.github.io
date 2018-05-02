---
layout: post
title: "[2018_AceBear_CTF] My idol(975)"
description:
headline:
modified: 2018-02-09
category: [CTF, 2018_acebear]
tags: [Forensic, MISC, Qr, mp3, deepsound]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---

## Exercise
> Challenge Description: Trying to listen and maybe you know his name, and you'll know one of his secret weapons . Something that he deleted from Michael Hansen may help you get in.
Download: Link
Author: komang4130

![](/images/2018-02-09-AceBear-CTF-My-idol-975/exercise.png)
<p align='center'>[그림] exercise</p>

## Solution

Download link 를 통해 받은 파일을 확인해보면, 하나의 ape 파일을 확인할 수 있다. (`call_him.ape`)
![](/images/2018-02-09-AceBear-CTF-My-idol-975/exiftool.png)
<p align='center'>[그림] exiftool - call_him.ape</p>

audio/x-monkeys-audio 형식의 audio 파일이며, 재생이 가능하다. 재생을 해서, 들리는 소리를 들어보면 독백의 대사를 읊는 남자의 목소리를 확인할 수 있는데, 중간에 앨리엇이라는 단어를 듣고 예전에 봤던, `Mr.Robot`이라는 미드에 나오는 대사 부분임을 알 수 있었다.


일단, 문제로 주어진 binary가 audio 파일이기 떄문에, 생각해볼 수 있는 접근 방식은 morse, spectrum, steganography, funky file format 정도로 세웠다. morese는 특별한 연관 내용이 보이지 않아 넘겼고, spectrum을 확인했을 때, 아래와 같이 특별히 식별되는 정보는 보이지 않았다.
![](/images/2018-02-09-AceBear-CTF-My-idol-975/spectrum.png)
<p align='center'>[그림] check spectrum</p>


내부적으로 딱히, file format을 가지고 장난을 친 흔적도 안보이고, 엔트로피 확인했을 때, 튀는 구간도 보이지 않게 분포가 일정했다. MP3 openStego를 이용해, hiding 된 Text가 있는지 살펴보았으나 마찬가지로 유의미한 정보는 없었으며 audio와 관련된 Steganography 에 대해 좀 더 찾아볼 필요가 있었다.
```Bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/My idol
 binwalk ./call_him.ape

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
```

![](/images/2018-02-09-AceBear-CTF-My-idol-975/entrophy.png)
<p align='center'>[그림] check entropy</p>

```cmd
C:\Users\jsh05042\Desktop\MP3Stego_1_1_18\MP3Stego>Decode.exe ./call_him.ape -X
MP3StegoEncoder 1.1.17
See README file for copyright info
Input file = './call_him.ape'  output file = './call_him.ape.pcm'
Will attempt to extract hidden information. Output: ./call_him.ape.txt
Enter a passphrase:
Confirm your passphrase: ******
Enter a passphrase: **********
Confirm your passphrase: **********
the bit stream file ./call_him.ape is a BINARY file
HDR: s=FFF, id=0, l=3, ep=off, br=B, sf=0, pd=1, pr=1, m=2, js=3, c=0, o=0, e=0
alg.=MPEG-2 LSF, layer=III, tot bitrate=112, sfrq=22.1
mode=dual-ch, sblim=32, jsbd=32, ch=2
[Frame    0]Not enough main data to decode frame 0.  Frame discarded.
[Frame    1]Got 24048 bits = 751 slots plus 16
[Frame    2]Got 5336 bits = 166 slots plus 24
[Frame    3]Got 8568 bits = 267 slots plus 24
[Frame    4]Side info bad: block_type == 0 in split block.
```

Forensic wiki 에서 audio steganography 항목에 대해서 추가적으로 찾아보다, 아래와 같이 `deepsound` 라는 항목에 대해 확인할 수 있었다. Audio 에 데이터를 숨기고, 이를 추출할 수 있는 Windows 용 Steganography 도구라고 명시되어 있는데 해당 도구를 찾아 이용해 보았다.
![](/images/2018-02-09-AceBear-CTF-My-idol-975/deepsound.png)
<p align='center'>[그림] Forensic wiki - deepsound</p>

`deepsound`를 설치하고, `call_him.ape` 파일을 로드하니, 아래와 같이 password 요구 화면을 확인할 수 있었다.
![](/images/2018-02-09-AceBear-CTF-My-idol-975/deepsound2.png)
<p align='center'>[그림] deepsound</p>


Password 에 대해 고민하던 찰나, 풀이자가 워낙 적었는지 hint 로 주어진 항목에, "Can u find his wikia?"라는 문장이 있었고 Elliot의 wiki를 찾아볼 수 있었다. http://mrrobot.wikia.com/wiki/Elliot 에서 내용을 볼 수 있으며, password로 쓰일만한 단서가 무엇인가 찾아보던 중 전화번호를 하나 확인할 수 있었다. `(212) 555-0179` --> `2125550179`로 password를 주었을 때, deepsound 로부터 hide data 를 식별할 수 있었다.
![](/images/2018-02-09-AceBear-CTF-My-idol-975/deepsound4.png)
![](/images/2018-02-09-AceBear-CTF-My-idol-975/deepsound3.png)
<p align='center'>[그림] deepsound - hide data & extract data</p>


좀 문제가 풀리는가 했는데, 여기서 또 한번 난관을 맞았다.. extract 한 파일의 data 가 도통 뭔지 식별할 수가 없는 문제였다. 이쯤 되면, Forensic 문제로 치부하기엔 Steganography 하나만 인정 해줄수 있을 것 같다... 아래는 해당 decode extract file. 숫자가 나오는 구간, alphabet이 나오는 구간 이렇게 구분지어지는 것은 확인했으나 무슨 의미를 가지는지 전혀 감을 못잡던 찰나, 주최측에서 새로운 힌트를 게시해뒀더라..
![](/images/2018-02-09-AceBear-CTF-My-idol-975/extractfile.png)
![](/images/2018-02-09-AceBear-CTF-My-idol-975/hint.png)
<p align='center'>[그림] hint & file data</p>

Base64가 아니며, Image file임을 알 수 있고, length, width를 잘 확인하고, charset, Black & White가 주어져있다. hint를 보고난 뒤, File size를 보니 48620 bytes 이다. "220x220", "221x221"을 했을 때, 48400 size가 나오는데, 해당 파일 크기와 상당히 유사한 값을 띄는 것을 확인하고는 가로 세로 길이가 220인 Image를 base 로 접근했다.


이어서, 위 `weird` 파일 data를 보면, 숫자 구간과 alphabet 구간으로 구분지어진 것을 확인할 수 있는데, `Black & White`라는 hint 가 주어졌으므로 offset data 가 숫자일 경우, 알파벳일 경우를 나눠 Black과 White 의 색을 표현하는 code를 짜보았다.
```python
from PIL import Image

img = Image.new( 'RGB', (220,220), "black")
pixels = img.load()
with open ("weird", "r") as f:
    data=f.read().replace('\n', '')

for i in range(img.size[0]):
    for j in range(img.size[1]):
        pixels[i,j] = (0,0,0)
k = 0
for i in range(img.size[0]):
    for j in range(img.size[1]):
        if k < len(data):
            if data[k] in '0123456789':
                pixels[i,j] = (255,255,255)
            else:
                pixels[i,j] = (0,0,0)

            k = k +1

img.save("./result.png")
```


몇번 시행착오 겪다가, 올바르게 뽑아낸 결과 파일을 확인해 보면, QR Code 임을 알 수 있으며 아래와 같다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/My idol
 python my.py
```
![](/images/2018-02-09-AceBear-CTF-My-idol-975/qr.png)
<p align='center'>[그림] result - convert file</p>

qr Code 가 나오는데, 색을 굳이 반전 시키지 않아도 정상인식이 가능하다. 인식을 Mobile app으로 진행했으며, 결과 Flag를 확인할 수 있다.
![](/images/2018-02-09-AceBear-CTF-My-idol-975/flag.jpeg)
<p align='center'>[그림] QR - decode with mobile app</p>

<p align='right'><strong>AceBear{2512_Ng0\`i_nh4\`_r4_d3\`!!!!k0_c00l}</strong></p>
