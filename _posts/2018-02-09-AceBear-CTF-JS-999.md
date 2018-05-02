---
layout: post
title: "[2018_AceBear_CTF] JS (999)"
description:
headline:
modified: 2018-02-09
category: [CTF, 2018_acebear]
tags: [Forensic, MISC, javascript, steganography]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---

## Exercise

> Challenge Description: Let listen to the best song of this month.
Download: Link
Author: komang4130

![](/images/2018-02-09-AceBear-CTF-JS-999/exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

Forensic/MISC 분야에서 나왔던 문제, 풀이자가 상당히 적고 나도 당시엔 풀지 못한 문제인데.. 너무 어이 없이 해결해서 당황스럽다..

link를 통해서 받은 파일을 확인해보면, mp3 파일을 하나 확인할 수 있다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/JS
 file ./Hey_Stranger.mp3
./Hey_Stranger.mp3: Audio file with ID3 version 2.4.0
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/JS
 exiftool ./Hey_Stranger.mp3
ExifTool Version Number         : 10.55
File Name                       : Hey_Stranger.mp3
Directory                       : .
File Size                       : 4.7 MB
File Modification Date/Time     : 2018:01:27 14:11:55+09:00
File Access Date/Time           : 2018:02:08 21:26:36+09:00
File Inode Change Date/Time     : 2018:01:27 14:13:20+09:00
File Permissions                : rw-r--r--
File Type                       : MP3
File Type Extension             : mp3
MIME Type                       : audio/mpeg
MPEG Audio Version              : 1
Audio Layer                     : 3
Audio Bitrate                   : 128 kbps
Sample Rate                     : 44100
Channel Mode                    : Stereo
MS Stereo                       : Off
Intensity Stereo                : Off
Copyright Flag                  : False
Original Media                  : False
Emphasis                        : None
ID3 Size                        : 1415670
Copyright                       : Vega
Title                           : Người Lạ Ơi
Genre                           : Rap / Hip Hop Việt
Artist                          : Karik, Orange
Album                           :
User Defined Text               : (author) Châu Đăng Khoa
Encoder Settings                : Lavf56.40.101
Picture MIME Type               : image/png
Picture Type                    : Other
Picture Description             : Hey Stranger - Karik - Orange
Picture                         : (Binary data 1415401 bytes, use -b option to extract)
Duration                        : 0:03:37 (approx)
```
<br>


대회 당시, binary가 mp3 파일로 주어졌기 때문에 MP3stego 등을 이용하거나, Spectrum, GoldWave 등으로 Audio Steganography로 접근했었다. 하지만 전혀 관계없다... `binwalk`로 파일 내 데이터를 추출하면, 아래와 같이 Thumbnail로 저장되어 있는 이미지 파일을 하나 확인할 수 있다.

```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Acebear_CTF/Forensic:MISC/JS
binwalk ./Hey_Stranger.mp3

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
259           0x103           PNG image, 1366 x 1000, 8-bit/color RGBA, non-interlaced
300           0x12C           Zlib compressed data, compressed
4103003       0x3E9B5B        lrzip compressed data
```

![](/images/2018-02-09-AceBear-CTF-JS-999/info.png)
<p align='center'><i>[그림] Binwalk information</i></p>


혹시 몰라, 대회 당시엔 해당 png 파일에 대해 추출하고 OpenStego 돌려보고, LSB 확인하고 등의 작업을 많이 수행했었는데 바보 같았던게 문제에서 `JS` 라는 단어를 언급하고 있던 점을 전혀 생각하지 않았던 것이다.


`javascript` image steganography 에 대해서 찾아보니, 바로 관련된 자료가 나왔고.. 해당 thumbnail을 올려 unhide 해보니.. 바로 Flag 확인..(상당히 어이가 없었다.. 풀이자도 2-3팀 정도였던 걸로 기억하는데, 너무 간단한 문제였다..)
![](/images/2018-02-09-AceBear-CTF-JS-999/unhide.png)
<p align='center'><i>[그림] unhide Thumbnail</i></p>
<p align='right'><strong>AceBear{!!!!!BoA_Luffy_1s_Real!!!!!}</strong></p>
