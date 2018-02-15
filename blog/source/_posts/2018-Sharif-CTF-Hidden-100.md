---
title: '[2018_Sharif_CTF] Hidden(100)'
categories:
  - CTF
  - 2018 Sharif CTF
tags: [Forensic, memory, volatility]
date: 2018-02-15 21:00:12
thumbnail: /images/sharif_CTF.png
---


## Exercise

> Find the hidden process.

> The flag is SharifCTF{MD5(Process id)}.

![](exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

숨겨진 프로세스를 찾아내는 문제. `process` -> `Memory?` 직감에 압축을 풀고, binary를 살펴보면, memory dump 파일임을 알 수 있다. volatility를 이용해 imageinfo 결과 xp memory dump 파일임을 확인.

```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/100_ Hidden
vol -f ./dump imageinfo
Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
WARNING : volatility.debug    : Overlay structure cpuinfo_x86 not present in vtypes
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
                     AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/Users/Mac/Desktop/CTF/2018_Sharif_CTF/Forensic/100_ Hidden/dump)
                      PAE type : PAE
                           DTB : 0x359000L
                          KDBG : 0x80545c60L
          Number of Processors : 1
     Image Type (Service Pack) : 3
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2018-01-28 17:35:20 UTC+0000
     Image local date and time : 2018-01-28 21:05:20 +0330
```


process 확인을 위해서, `pslist`, `psscan` 두 명령어를 수행하고, psscan 에서 추가적으로 식별되는 항목을 확인할 수 있다.

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/100_ Hidden
 vol -f ./dump profile=WinXPSP2x86 pslist
Volatility Foundation Volatility Framework 2.6
Offset(V)  Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit
---------- -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ ------------------------------
0x81242a00 System                    4      0     57      263 ------      0
0xff391900 smss.exe                548      4      3       19 ------      0 2018-01-28 16:59:16 UTC+0000
0xff36bda0 csrss.exe               620    548     12      305      0      0 2018-01-28 16:59:19 UTC+0000
0xff39f608 winlogon.exe            644    548     19      537      0      0 2018-01-28 16:59:20 UTC+0000
0xff391488 services.exe            688    644     16      353      0      0 2018-01-28 16:59:24 UTC+0000
0xff390410 lsass.exe               700    644     20      349      0      0 2018-01-28 16:59:25 UTC+0000
0xff378798 vmacthlp.exe            856    688      1       25      0      0 2018-01-28 16:59:34 UTC+0000
0xff3a59c0 svchost.exe             900    688     17      211      0      0 2018-01-28 16:59:39 UTC+0000
0xff3bc378 svchost.exe             988    688     11      235      0      0 2018-01-28 16:59:47 UTC+0000
0xff3cb6e0 svchost.exe            1024    688     54     1107      0      0 2018-01-28 16:59:51 UTC+0000
0xff2077a8 svchost.exe            1188    688      4       57      0      0 2018-01-28 16:59:53 UTC+0000
0xff3a7878 svchost.exe            1236    688     10      167      0      0 2018-01-28 16:59:56 UTC+0000
0xff1bbcf0 spoolsv.exe            1508    688     11      140      0      0 2018-01-28 17:00:19 UTC+0000
0xff1b1020 explorer.exe           1576   1444     12      404      0      0 2018-01-28 17:00:24 UTC+0000
0xff1aa9f0 svchost.exe            1604    688      4      105      0      0 2018-01-28 17:00:25 UTC+0000
0xff197b20 svchost.exe            1692    688      3       94      0      0 2018-01-28 17:00:31 UTC+0000
0x811244c0 rundll32.exe            396   1576      4       70      0      0 2018-01-28 17:02:48 UTC+0000
0xff1c30e8 wscntfy.exe             920   1024      1       31      0      0 2018-01-28 17:05:09 UTC+0000
```


```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/100_ Hidden
 vol -f ./dump profile=WinXPSP2x86 psscan
Volatility Foundation Volatility Framework 2.6
Offset(P)          Name                PID   PPID PDB        Time created                   Time exited
------------------ ---------------- ------ ------ ---------- ------------------------------ ------------------------------
0x000000000096c0e8 wscntfy.exe         920   1024 0x007002c0 2018-01-28 17:05:09 UTC+0000
0x00000000010eb4c0 rundll32.exe        396   1576 0x007001a0 2018-01-28 17:02:48 UTC+0000
0x0000000001209a00 System                4      0 0x00359000
0x0000000001bbd488 services.exe        688    644 0x00700080 2018-01-28 16:59:24 UTC+0000
0x0000000001bbd900 smss.exe            548      4 0x00700020 2018-01-28 16:59:16 UTC+0000
0x0000000001c279c0 svchost.exe         900    688 0x00700100 2018-01-28 16:59:39 UTC+0000
0x0000000001c58798 vmacthlp.exe        856    688 0x007000c0 2018-01-28 16:59:34 UTC+0000
0x0000000001de4878 svchost.exe        1236    688 0x00700180 2018-01-28 16:59:56 UTC+0000
0x0000000001e64350 vmtoolsd.exe        404   1576 0x00700260 2018-01-28 17:02:50 UTC+0000
0x0000000001e6d608 winlogon.exe        644    548 0x00700060 2018-01-28 16:59:20 UTC+0000
0x0000000001ebe168 cmd.exe            1704   1576 0x007002a0 2018-01-28 17:30:47 UTC+0000   2018-01-28 17:34:00 UTC+0000
0x0000000001ecd378 svchost.exe         988    688 0x00700120 2018-01-28 16:59:47 UTC+0000
0x0000000001fbd6e0 svchost.exe        1024    688 0x00700140 2018-01-28 16:59:51 UTC+0000
0x0000000001fbe410 lsass.exe           700    644 0x007000a0 2018-01-28 16:59:25 UTC+0000
0x00000000021a7da0 csrss.exe           620    548 0x00700040 2018-01-28 16:59:19 UTC+0000
0x00000000025b7020 explorer.exe       1576   1444 0x007001e0 2018-01-28 17:00:24 UTC+0000
0x0000000002dbb448 wmiprvse.exe        908    900 0x00700240 2018-01-28 17:32:51 UTC+0000   2018-01-28 17:34:22 UTC+0000
0x0000000002e7eb20 svchost.exe        1692    688 0x00700220 2018-01-28 17:00:31 UTC+0000
0x000000000308d9f0 svchost.exe        1604    688 0x00700200 2018-01-28 17:00:25 UTC+0000
0x00000000031b1cf0 spoolsv.exe        1508    688 0x007001c0 2018-01-28 17:00:19 UTC+0000
0x00000000039347a8 svchost.exe        1188    688 0x00700160 2018-01-28 16:59:53 UTC+0000
```


두 결과의 차이점 항목을 확인해보면, 아래와 같이 3개의 항목을 식별할 수 있다. `cmd.exe`, `wmiprvse.exe` 두 항목은 process 가 종료되어 pslist에서 식별되지 않았음을 알 수 있지만 `vmtoolsd.exe` 항목은 종료된 기록이 없음과 동시에 pslist에서 식별되지 않았으므로 해당 항목이 은닉된 프로세스임을 알 수 있다. (자세히 보면, vmtools와 유사한 `vmtoolsd` 파일명을 사용하고 있다.)

```bash
0x0000000001ebe168 cmd.exe            1704   1576 0x007002a0 2018-01-28 17:30:47 UTC+0000   2018-01-28 17:34:00 UTC+0000
0x0000000002dbb448 wmiprvse.exe        908    900 0x00700240 2018-01-28 17:32:51 UTC+0000   2018-01-28 17:34:22 UTC+0000
0x0000000001e64350 vmtoolsd.exe        404   1576 0x00700260 2018-01-28 17:02:50 UTC+0000
```


md5 값이 flag 이므로 변환해주도록 한다.
```python
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/100_ Hidden
 python
Python 2.7.10 (default, Feb  7 2017, 00:08:15)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.34)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import hashlib
>>> hashlib.md5('404').hexdigest()
'4f4adcbf8c6f66dcfc8a3282ac2bf10a'
>>>
```

<p align='right'><strong>SharifCTF{4f4adcbf8c6f66dcfc8a3282ac2bf10a}</strong></p>
