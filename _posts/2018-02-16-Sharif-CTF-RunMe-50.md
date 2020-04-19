---
layout: single
title: "[2018_Sharif_CTF] RunMe(50)"
description:
headline:
modified: 2018-02-16
category: [CTF]
tags: [2018_Sharif, Reversing, Writeup, string]
imagefeature:
mathjax:
chart:
comments: true
featured: true
toc: true
toc_sticky: true
---

## Exercise

> Run the attached file. If you can, you will capture the flag.<br>
> Note: Apply the minimum changes to make the file executable. Then, the mentioned hash function is md5. Be sure to run it on a real Windows OS (not Wine, etc.)

![](/assets/images/2018-02-16-Sharif-CTF-RunMe-50/exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

binary를 정상적으로 실행시키는 것이 전부인 문제. PE 파일임을 알 수 있다. real Windows OS 에서 실행해야 한다고 명시되어 있지만, 가상 환경에서 문제 없이 돌아간다.

```bash
 ✘ jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Reversing/50_Run me
exiftool ./RunMe2.exe
ExifTool Version Number         : 10.55
File Name                       : RunMe2.exe
Directory                       : .
File Size                       : 70 kB
File Modification Date/Time     : 2018:02:11 19:13:17+09:00
File Access Date/Time           : 2018:02:16 15:36:30+09:00
File Inode Change Date/Time     : 2018:02:11 19:13:17+09:00
File Permissions                : rw-r--r--
File Type                       : Win32 EXE
File Type Extension             : exe
MIME Type                       : application/octet-stream
Machine Type                    : Intel 386 or later, and compatibles
Time Stamp                      : 2018:01:30 13:22:02+09:00
PE Type                         : PE32
Linker Version                  : 14.0
Code Size                       : 41984
Initialized Data Size           : 31744
Uninitialized Data Size         : 0
Entry Point                     : 0x1250
OS Version                      : 5.1
Image Version                   : 0.0
Subsystem Version               : 5.1
Subsystem                       : Native
```


실행을 해보면, 정상적으로 동작하지 않는 것을 확인할 수 있다. 정상적인 PE binary와 비교했을 때, 차이점을 살펴보다. `Subsystem` 항목이 Native 로 설정되어 있는 것을 확인할 수 있었는데 관련 정보를 찾아보니 보통, `2`,`3` 으로 지정되어 있는 것을 확인.
![](/assets/images/2018-02-16-Sharif-CTF-RunMe-50/error.png)
<p align='center'><i>[그림] execution error</i></p>

```
NATIVE : 1  -->  Doesn't require a subsystem
Windows GUI : 2 --> Runs in the Windows GUI subsystem
WINDOWS CUI : 3 --> Runs in console subsystem
```


아래와 같이, `Subsystem` offset을 수정해주었다. 그 결과, 실행에 성공했다는 string 과 함께, file의 hash 값이 flag임을 명시하고 있다.
![](/assets/images/2018-02-16-Sharif-CTF-RunMe-50/fix.png)
<p align='center'><i>[그림] fix subsystem</i></p>




```cmd
C:\Users\jsh05042\Desktop\sharif\reversing>python
Python 2.7.6 (default, Nov 10 2013, 19:24:18) [MSC v.1500 32 bit (Intel)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import hashlib
>>> f = open('RunMe2.exe','rb')
>>> hashlib.md5(f.read()).hexdigest()
'3de0531cb6069ff18543fe767e384b83'
>>>
```

<p align='right'><strong>SharifCTF{3de0531cb6069ff18543fe767e384b83}</strong></p>
