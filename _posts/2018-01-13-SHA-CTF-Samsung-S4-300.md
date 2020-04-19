---
layout: single
title: "[2017_SHA_CTF] Samsung S4 (300)"
description:
headline:
modified: 2018-01-13
category:
  - CTF
tags: [2017_SHA, Forensic, Writeup, JTAG]
imagefeature:
mathjax:
chart:
comments: true
featured: true

toc: true
toc_sticky: true
sidebar_main: true
---

## Exercise

> A forenic investigator was wondering how hacker boxes like RIFF and Z3X use JTAG on a Samsung S4 phone while the main cores have debugging disabled.

> Challenge created by the Digital and Biometric Traces division of the Netherlands Forensic Institute.


![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/0_exercise.png)


- 2017년 8월에 참가했었던 SHA CTF 에서 나온 Forensic 문제. Samsung Galaxy S4 JTAG 관련 문제이다. 대회 당시에 이게 뭔가.. 하고 살펴보다가 벙쪄서 못 풀었었다. Forensic CTF 문제에서 보기 드문 형태로 나온 유형이라 기억에 남아서 `"나중에 Writeup이 나오면 보거나, 꼭 다시 풀어봐야지"`하고 아껴(?)왔다. 참가했던 CTF 중 못 푼 문제들 스택 처리를 계속 계획에 염두해오고 있던터라, 이번 기회에 바로 다시 풀어보았다.


- 사실, 대회가 끝나고 바로 재도전 했었지만 도저히 어떻게 풀어나가야 할지 감이 잡히질 않아 해당 문제를 풀이 했던 `HackingForSoju` 팀의 `zeta-two`에게 팀원 중 해당 문제를 푼 사람을 통해 어떻게 접근한 건지 알려줄 수 있냐고 요청 메일까지 보내며 풀어보고자 했던 문제이다.. (아래는 친철하게 힌트를 알려준 `zeta two`의 메일)

> Hi!

> For Forensics 300 my teammate is working on a write-up but I can tell you that you can load the data in siggrok and use the JTAG parser. From there you will see that it is reading the data register (DR) over and over. So just decode each 34(!) bit value and you have the flag.<br>

> Greetings<br>
> Calle "Zeta Two" Svensson<br>
> HackingForSoju<br>


## Solution

먼저, 문제에서 언급된 내용을 살펴보면 Forensic 연구원이 `RIFF`, `Z3X`와 같은 도구를 사용해 Samsung S4 기기의 `JTAG` 를 사용하는 방식에 대해 궁금했다고 한다. 첨부 파일을 확인하기 전에 문제를 풀 당시, JTAG을 제외하곤 생소한 단어였기 때문에 정리가 필요했다.

### JTAG?
> `Joint Test Action Group`, 임베디드 시스템 개발 시에 사용하는 디버깅 장비.

> Interface : 아래와 같은 핀으로 칩 내에 구성되어 있다.

- TDI (데이터 입력) : Test하기 위한 데이터 신호. TMS에 의해 전이된 TAP state에 따라, TDI가 command/data 가 결정됨
- TDO (데이터 출력) : Test한 결과를 외부에서 모니터링 하기 위한 pin, 이 역시 TAP state에 따라 address/data가 될 수 있음.
- TCK (클럭) : Test clock
- TMS (모드) : Test Mode 전환하기 위한 제어 신호
- TRST (리셋)

![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/1_jtag_interface.png)
<p align='center'>[그림] JTAG interface</p>
<!--<figcaption align='center'>[figure] JTAG interface</figcaption>-->

### RIFF?
> JTAG 신호를 PC 에서 조작 가능하도록 변환해주는 장비.
> 스마트폰 Flash Memory Dump 용도로 사용할 수 있다.

![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/2_riff.png)
<p align='center'>[그림] RIFF (출처 : JTAG를 이용한 스마트폰 플래시 메모리 덤프 - 박상호)</p>



### Z3X?
> RIFF 와 같이 Flash Dump 를 위해 사용되는 장비로써, 찾아보면 Samsung 스마트폰 기종에 대해서 JTAG 를 손쉽게 활성화하여 Root, Unlock 작업을 할 수 있다고 나와았다. (구매 욕구가..)

![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/3_z3x.png)
<p align='center'>[그림] Z3X </p>


<br>
문제에서 주어진 첨부파일을 확인해보면, `samsungs4.tgz` 파일을 확인할 수 있다. 해당 파일의 압축을 풀면 `CTF_SamsungS4.csv` 파일을 확인할 수 있는데 아래와 같은 내용을 포함하고 있다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/4_csv.png)
<p align='center'>[그림] CTF_SamsungS4.csv</p>

문제에서 언급한 장비로 s4 기기에 대한 JTAG 신호를 기록한 것으로 보이며, 이를 의미있는 데이터로 변환해야하는 것으로 보였다. 대회 당시에는 JTAG 신호를 어떻게 분석해야 할지 하드웨어적인 지식이 전혀 없어 여러 삽질을 했었는데 결과적으로 `zeta-two`로 부터 얻은 Hint 를 통해 `sigrok`이라는 도구를 이용해 문제를 해결했다.


CSV 내용을 보면, TimeStamp 별로 TMS, TCK, TDI, TDO 핀의 신호가 기록되있는 것을 확인할 수 있다. 이를 어떻게 분석할 수 있는 시각적 데이터로 변환해야하는가? 가 관건이었는데, 처음 내가 접근했던 방식은 "http://www2.lauterbach.com/pdf/training_jtag.pdf" 을 보고 JTAG data 가 가지는 의미 등에 대해 파악하는 것이었다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/5_jtag_training.png)
<p align='center'>[그림] JTAG core process</p>


Shift-IR, DR 등의 자료가 나오고 JTAG 신호를 처리하는 Process 등에 대해 살펴보고 해당 CSV 데이터를 동일하게 처리하는 방식으로 하려 했지만, 생판 모르는 JTAG 자료를 보고 따라하려니 뭐가 뭔지 도통 알아먹을 수 없었다.... 그렇게 해당 방식은 뒤로하고, `sigrok` 도구를 이용하기로 했다. `sigrok`은 신호 분석용 오픈소스 도구이다. 다양한 플랫폼, 신호 분석과 디코더를 지원한다. CSV 를 바로 업로드 할 수 있는 기능이 있어 편하게 해당 데이터를 신호로 변환하여 열어볼 수 있었다. 본래 CSV 에 포함되어 있는 TimeStamp는 분석이 되지 않으므로, Header Row 와 TimeStamp Column 을 제거했다.
```bash
cat ./CTF_SamsungS4_@.csv | tail -n+2 | awk '{print $2 $3 $4 $5}' > ./reData.csv
```



데이터만 남겨놓은 파일로 대체한 뒤, `sigrok`에서 QT 로 GUI를 지원하는 `pulseview`를 이용해 해당 CSV 데이터를 분석할 수 있다. `pulseview`로 로드하면 아래와 같은 시그널을 확인할 수 있다. 좌측에 해당 시그널의 명칭이 빠져있는데, 각각 TMS, TCK, TDI, TDO 순이므로 클릭하여 입력하면 수정할 수 있다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/6_pulseview.png)
<p align='center'>[그림] pulseview signal</p>


해당 시그널이 의미하는 바와, 담긴 정보를 찾기 위해 JTAG 신호를 Decode 해야하며 Decoder 탭에서, 사용할 Decoder 로 JTAG 를 선택할 수 있다. 확대해서 확인해보면, SHIFT-IR, SHIFT-DR 등의 구간이 정상적으로 식별되는 것을 확인할 수 있다. (원래 Decoder 를 누른 뒤, TDI, TDO, TMS 등의 시그널을 맞춰 주어야하는데 알아서, 잘 맞춰져서 넘어갔다.)
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/7_jtag_decoder.png)
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/8_jtag_decoder.png)
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/9_jtag_decoder.png)
<p align='center'>[그림] JTAG decode</p>



여기까지 진행하고 난 뒤에도 살짝 당황스러웠던게, Decoder 라해서 signal을 식별가능한 Text 로 변환해주는 줄 알았는데 JTAG spec 으로 변환해주는 것이었다..
TDI는 디바이스로 들어가는 데이터를 표시하며, TDO는 디바이스로부터 나오는 데이터를 표시한다. 여기에서 또, 상당이 애먼 시간을 쏟았는데 결과적으로 JTAG의 TMS, TCK, TDI, TDO 시그널을 SPI decoder 를 이용해 Bitstream 으로 변환할 수 있다는 사실을 알 수 있었다. (SPI가 정확하게 어떤 무엇인지, 각 정보들을 정확하게 이해하고 싶었지만, 너무 까다로운 정보들이라.. 정확한 이해는 하지 못했다.)
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/10_spi.png)
<p align='center'>[그림] JTAG to SPI</p>



마침, `pulseview`에서도 `SPI` decoder 를 지원하고 있었고, 아래와 같이 decoder를 추가한 뒤, Setting을 거쳐 작업을 수행할 수 있었다. 설정한 부분은 아래와 같은데 각각 다음과 같다.
- CLK : `clock` 을 의미하는데 `TCK`가 `test clock`을 의미하므로 `TCK`로 설정했다.
- MISO : `Master in, Slave out`으로써, slave out은 SPI decoder 로 들어가는 데이터를 의미하기 때문에, JTAG에서 디바이스로부터 나오는 데이터인 `TDO`로 설정했다.
- CS# : `Chip Select`를 의미하는데, 제어 신호로써 `TMS`가 사용되기 때문에 `TMS`를 설정했다.
- Word size : 변환되는 구간을 의미하는 것 같다. 처음에 8로 설정되어 있는데 아래 그림에 표시된 decode 결과가 TMS pulse 가 1인 구간 내에서 일부분만 변환을 하기에 해당 구간 내 모든 데이터를 변환하기 위해 구간 길이인 35로 설정했다.

![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/11_spi_decoder.png)
<p align='center'>[그림] SPI decoder setting</p>


위 설정 값을 지정할 때마다, 디코딩 결과를 실시간으로 확인할 수 있었는데 위와 같은 설정을 했을때, 아래 그림과 같이 눈에 익는 Bitstream 을 확인할 수 있었다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/12_spi_decoder.png)
<p align='center'>[그림] SPI decoder result</p>


바로, ASCII 범위 내 값이 반환 된 것인데 아래 표시된 bitstream을 ASCII 값으로 변환 해보면 "galf" 즉 "flag" 인 것을 알 수 있다.(little endian) ASCII 값이 식별되는 sampling 범위는 약 +2500s ~ +4200s 구간이다. \x00 로 끝임을 예상할 수 있다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/13_ascii_area.png)
<p align='center'>[그림] ASCII bitstream area</p>


해당 bitstream 을 적은 뒤, Python으로 Hex decoding 을 할 경우 아래와 같이 Flag 를 확인할 수 있었다.
![](/assets/images/2018-01-13-SHA-CTF-Samsung-S4-300/14_flag.png)
<p align='center'>[그림] Flag</p>


<p align='right'><strong>flag{9a0e5c94ac3e38719130ab133cfbfe18}</strong></p>

## Finish
> 계속 염두하고 있던, 문제 하나를 해결해서 개운한 느낌이다. 하지만, 아직 궁금증이 해결되지 않은 부분이 너무 많다. SPI며, JTAG, RIFF Box, Signal Analysis 등 하드웨어 분야 또한 상당히 매료되는데, 기회가 되면 한번 제대로 공부를 해보고 싶다.. Mobile Forensic 공부할 때, Z3X 구매해서 한번 실습해 볼까...싶다..

> 내가 푼 방식 말고, 처음에 JTAG Process 를 이해하고 이를 이용해 문제를 해결한 방식으로 푼 writeup이 CTFtime에 올라와 있길래 첨부해 둔다. 도구 쓴게 하나도 없고 오로지 Instruction 문서랑 Python으로만 해결한 것 같다. 이렇게 풀고 싶었는데 보고나니 뭔가 아쉽다..
- http://karabut.com/sha2017-ctf-samsung-s4-writeup.html

<br>

## Reference
- [Using SPI Flash with 7 Series FPGAs Application Note](https://www.xilinx.com/support/documentation/application_notes/xapp586-spi-flash.pdf)
- [Training jtag interface](http://www2.lauterbach.com/pdf/training_jtag.pdf)
- [JTAG work](http://www.fpga4fun.com/JTAG2.html)
- [JTAG를 이용한 스마트폰 플래시 메모리 덤프](http://for-md.org/wp-content/uploads/2015/01/JTAG%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%8A%A4%EB%A7%88%ED%8A%B8%ED%8F%B0-%ED%94%8C%EB%9E%98%EC%8B%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EB%8D%A4%ED%94%84.pdf)

