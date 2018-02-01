---
title: '[2018_AceBear_CTF] Memory (951)'
categories:
  - CTF
  - 2018 AceBear CTF
tags: [Forensic, MISC, memory, spammer]
date: 2018-02-01 00:10:13
thumbnail: /images/acebearCTF.png
---

## Exercise

> Download: Link
author: f4k3r

![](exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

Download link 를 통해 받은 파일을 확인해보면, memory dump raw 파일을 하나 확인할 수 있다. volatility를 이용해, imageinfo를 확인해보면 아래와 같은 imageinfo를 식별할 수 있다. (Windows7)

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/Memory
 vol -f ./dump.raw imageinfo
Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
          Suggested Profile(s) : Win7SP1x86_23418, Win7SP0x86, Win7SP1x86
                     AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/Users/Mac/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/Memory/dump.raw)
                      PAE type : PAE
                           DTB : 0x185000L
                          KDBG : 0x8292ec28L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0x8292fc00L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2018-01-18 02:45:02 UTC+0000
     Image local date and time : 2018-01-18 04:45:02 +0200
```
<br>

memory dump에 관련된 문제가 나왔을 때, 나같은 경우에는 가장 먼저 이상 프로세스가 없는지 확인한다. 이후, 이상 프로세스가 식별되는 경우에 해당 프로세스에 대한 덤프, 분석 등을 시도하며 별도로 이상한 프로세스가 식별되지 않을 경우 Command line, Registry, Shellbag 등 부가적인 정보, Network, Filelist 등에 대해 분석하는 식으로 보통 접근하는데 이번에도 앞서 언급한 행위 분석을 하는 과정 중 Filescan 에서 Document directory내에 아래와 같은 파일 목록을 확인할 수 있었다.

![](filescan.png)
<p align='center'>[그림] filescan list</p>

식별되는 `\Device\HarddiskVolume2\Users\Administrator\Documents` 내에서 확인되는 파일 목록 중 특이한 부분은 아래와 같다. 5개의 파일이 Document 폴더 내에 존재하는 것을 확인할 수 있는데 각각 rtf 파일 2개, python 파일 1개, gif 파일 1개, pdf 파일 1개이다.

```bash
0x000000001e0ed5d0      8      0 R--rwd \Device\HarddiskVolume2\Users\Administrator\Documents\document.rtf
0x000000001fcaca30      8      0 R--rwd \Device\HarddiskVolume2\Users\Administrator\Documents\usb_command.rtf
0x000000001fcbc4c8      7      0 R--r-d \Device\HarddiskVolume2\Users\Administrator\Documents\GIF.gif
0x000000001fccbf80      8      0 R--rwd \Device\HarddiskVolume2\Users\Administrator\Documents\hackerrank.py
0x000000001ff241d8      8      0 R--rwd \Device\HarddiskVolume2\Users\Administrator\Documents\Ethical Hacking.pdf
```
<br>

`dumpfiles` command를 이용해, 해당 offset을 가지는 파일들을 추출해내는 작업을 수행함으로써 직접 각각의 파일을 확인할 수 있다. (Python 파일은 명시된 offset 값으로 바로 추출이 되지 않았지만, 문제 푸는데에는 지장이 없었으며 이 또한 MFT Entry offset을 참조하여 추출해낼 수는 있다.)

```bash
10516  vol -f ./dump.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000001e0ed5d0 -D ./
10517  vol -f ./dump.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000001fcaca30 -D ./
10518  vol -f ./dump.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000001fcbc4c8 -D ./
10519  vol -f ./dump.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000001fccbf80 -D ./
10522  vol -f ./dump.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x000000001ff241d8 -D ./
```
<br>

document.rtf 내용은 아래와 같다. mail로 보이는 내용이 기록되어 있음을 알 수 있다. (아래쪽에서 언급하겠지만, 이 document.rtf 내용이 문제를 푸는 실마리였다.)
```
Dear Colleague , Your email address has been submitted
to us indicating your interest in our briefing ! If
you are not interested in our publications and wish
to be removed from our lists, simply do NOT respond
and ignore this mail . This mail is being sent in compliance
with Senate bill 2516 , Title 3 , Section 305 . THIS
IS NOT MULTI-LEVEL MARKETING ! Why work for somebody
else when you can become rich in 48 DAYS ! Have you
ever noticed more people than ever are surfing the
web and the baby boomers are more demanding than their
parents . Well, now is your chance to capitalize on
this ! WE will help YOU turn your business into an
E-BUSINESS & decrease perceived waiting time by 180%
! You can begin at absolutely no cost to you . But
don't believe us . Mrs Jones who resides in Rhode Island
tried us and says "I was skeptical but it worked for
me" ! This offer is 100% legal ! So make yourself rich
now by ordering immediately ! Sign up a friend and
you'll get a discount of 50% . Thank-you for your serious
consideration of our offer ! Dear Salaryman ; Thank-you
for your interest in our briefing ! If you no longer
wish to receive our publications simply reply with
a Subject: of "REMOVE" and you will immediately be
removed from our club ! This mail is being sent in
compliance with Senate bill 1627 ; Title 1 ; Section
301 . This is a ligitimate business proposal ! Why
work for somebody else when you can become rich as
few as 48 weeks ! Have you ever noticed more people
than ever are surfing the web plus nearly every commercial
on television has a .com on in it . Well, now is your
chance to capitalize on this . We will help you increase
customer response by 110% and turn your business into
an E-BUSINESS ! You are guaranteed to succeed because
we take all the risk . But don't believe us . Prof
Simpson of Pennsylvania tried us and says "I was skeptical
but it worked for me" ! We assure you that we operate
within all applicable laws . So make yourself rich
now by ordering immediately . Sign up a friend and
you'll get a discount of 80% ! Thanks ! Dear Friend
; This letter was specially selected to be sent to
you ! We will comply with all removal requests ! This
mail is being sent in compliance with Senate bill 1623
; Title 9 ; Section 303 . THIS IS NOT MULTI-LEVEL MARKETING
. Why work for somebody else when you can become rich
within 81 DAYS . Have you ever noticed how many people
you know are on the Internet plus nobody is getting
any younger . Well, now is your chance to capitalize
on this ! We will help you process your orders within
seconds and use credit cards on your website ! You
can begin at absolutely no cost to you . But don't
believe us . Mrs Anderson of Colorado tried us and
says "My only problem now is where to park all my cars"
! This offer is 100% legal ! Do not go to sleep without
ordering ! Sign up a friend and you get half off .
Warmest regards ! Dear Business person , We know you
are interested in receiving cutting-edge info ! If
you no longer wish to receive our publications simply
reply with a Subject: of "REMOVE" and you will immediately
be removed from our club ! This mail is being sent
in compliance with Senate bill 1623 ; Title 5 , Section
308 ! This is NOT unsolicited bulk mail . Why work
for somebody else when you can become rich inside 23
DAYS ! Have you ever noticed nobody is getting any
younger and society seems to be moving faster and faster
. Well, now is your chance to capitalize on this !
WE will help YOU deliver goods right to the customer's
doorstep and use credit cards on your website ! You
are guaranteed to succeed because we take all the risk
. But don't believe us ! Ms Anderson of Montana tried
us and says "Now I'm rich, Rich, RICH" ! We are a BBB
member in good standing ! If not for you then for your
loved ones - act now . Sign up a friend and you'll
get a discount of 80% . Best regards .
```
<br>

usb_command.rtf 내용은 아래와 같다. flag format 의 문자열 텍스트가 일부 기록되어 있는 것을 확인하고는, 너무 쉽게 flag를 찾은게 아닌가 생각했지만 역시나 flag는 아니다. 딱히, 문자열 중에 기록된 pcap 파일이 file list 내에 있는가 찾아보았지만 식별되지는 않았고, 그다지 중요하지 않은 데이터였다.
```
tshark -r data.pcapng -T fields -e usb.capdata > data2.txt  //trich xuat du lieu goi tin

tshark -r data.pcap -V  // xem can goi tin

tshark -r data.pcap -V | grep “Left”    // xem phan du lieu goi tin

flag[pr355-0nwards-3a10134e]c

flag[pr355_0nwards_3a10134e]

flag[pr355_0nwards_deafcb32]

FLAG{PR#%%_)NWARDS_#A!)!#$E}C


09 f F
0f l L
04 a A
0a g G
2f [ {
13 p P
15 r R
20 3 #
22 5 %
22 5 %
2d - _
27 0 )
11 n N
1a w W
04 a A
15 r R
07 d D
16 s S
2d - _
20 3 #
04 a A
1e 1 !
27 0 )
1e 1 !
20 3 #
21 4 $
08 e E
30 ] }

01
010006 c C
```
<br>

GIF 파일은 아래 이미지와 같은데, 여러 장에 기록된 GIF 를 단일 이미지로 분할하여 모두 쭉 살펴보았지만 특별히 건질만한 항목은 없었다.
![](GIF.gif)
<p align='center'>[그림] GIF.gif</p>


Ethical Hacking.pdf 파일내 분명 flag와 관련된 항목이 있을거라 생각했고, Embedded pdf 에 초점을 맞춰 분석했다. peepdf를 이용해 pdf 분석을 하였으며, 아래 초기 info 를 보더라도 의심되는 항목은 검출되지 않았다. 그럼에도 stream, object를 하나씩 쭉 살펴보았지만 역시 아무것도 도움이 되는 내용은 존재하지 않았다..
![](peepdf.png)
<p align='center'>[그림] peepdf with Ethical Hacking.pdf</p>


이번 CTF는 HackXore 친구들과 5명 정도 같이했는데 모두 외쿡인 친구들이었다. 내가 살펴본 Task 들에 대해서 알렸고, hint가 주어졌다. steganography라는 힌트가 주어졌으며, 팀원 중 한명이 document.rtf 의 내용이 Spam Message 내에 정보를 은닉시키는 steganography 기술이 적용된 것 같다는 말을 해주었다. `http://www.spammimic.com/decode.shtml`에서 spam message 에 대해 decode 작업을 수행할 수 있었으며, decode 결과 url hash 값으로 보여지는 text 를 얻을 수 있었다.
![](spamdecode.png)
<p align='center'>[그림] spam mimic decode</p>

Decode Message result : `1gwiPCJHiIrnZAwLB9q9ztZKrD9tbq65YnoZzXALnh6c`
- url hash format 으로 보여지는 해당 Decode 값을 가지고 문제를 어떻게 해결해야 생각하던 찰나, Memory dump 내에서 url 목록을 모두 뽑아보기로 했다. bulk extrator 를 이용해, 해당 Memory내에 존재하는 url histogram 등을 뽑으면서 동시에, strings & grep 조합으로 문자열 탐색을 하다가 `form`이라는 hint가 나왔고, memory dump 내 url 목록들 중, google drive 문서에서 비슷한 format 을 가지는 형태가 나오는 것을 확인할 수 있었다.
![](url.png)
<p align='center'>[그림] url format</p>


이후, 개인 계정에서 Gdrive 문서들을 하나씩 열어보면서 Form을 확인했고, 아래와 같은 Form을 가지고 있는 것을 확인할 수 있었다.
![](form.png)
![](form2.png)
<p align='center'>[그림] google drive url form</p>


docement, spreadsheet 등의 url parameter에 해당 Decode Message 결과를 대입하던 중, 아래와 같이 `spread sheet format`에서 공개된 문서를 열람할 수 있었으며, 해당 스프레드 시트 내에는 Flag가 기록되어 있었다.

> result url
https://docs.google.com/spreadsheets/d/1gwiPCJHiIrnZAwLB9q9ztZKrD9tbq65YnoZzXALnh6c/edit#gid=0

![](flag.png)
<p align='center'>[그림] 스프레드시트 flag</p>
<p align='right'><strong>AceBear{y0u_l00k_v3ry_pr3tty!}</strong></p>


