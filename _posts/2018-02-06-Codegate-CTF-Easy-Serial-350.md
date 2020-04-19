---
layout: single
title: "[2018_Codegate_CTF] Easy_Serial (350)"
description:
headline:
modified: 2018-02-06
category: [CTF]
tags: [2018_Codegate, Reversing, Writeup, haskell]
imagefeature:
mathjax:
chart:
comments: true
featured: true
toc: true
toc_sticky: true
---


## Exercise

> Download

너무 매정한 지문..

![](/assets/images/2018-02-06-Codegate-CTF-Easy-Serial-350/exercise.png)
<p align='center'><i>[그림] exercise</i></p>


## Solution

다운받은 binary를 확인해보면, ELFx64임을 알 수 있다.
```bash
 ⚡ root@RebForPwn  ~/jsh  file ./easy
./easy: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=4bd1814b358aaa9e61a968f20bd14ffa9cd208e8, not stripped
```

<br>
실행해보면, serial Key를 입력 받는 것을 확인해 볼 수 있다.
![](/assets/images/2018-02-06-Codegate-CTF-Easy-Serial-350/run.png)
<p align='center'><i>[그림] run</i></p>




 IDA로 열어보면 hs_main부분을 확인할 수 있는데, `Haskell`로 작성된 binary 이다.
```C
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  __int64 v3; // r8
  __int64 v4; // r9
  __int64 v5; // ST30_8

  HIDWORD(v5) = HIDWORD(defaultRtsConfig);
  LODWORD(v5) = 1;
  hs_main(argc, (__int64)argv, (__int64)&ZCMain_main_closure, (__int64)argv, v3, v4, v5, 0LL, 1LL);
}
```
<br>

예전에 Samsung CTF 본선에서 하스켈로 짜여진 문제가 나왔던 기억이 있는데, 그때 당시에도 decompiler를 찾아보려 했었지만, 딱히 정상적으로 변환이 되는 항목은 볼 수 없었다. (테이블 만들고, 브포하고 노가다 했던 기억이...) 근데, git hub 에서 다시 찾아본 결과 "https://github.com/gereeter/hsdecomp" 에서의 decompiler를 이용해 Binary --> Code 형태로 변환할 수 있다. 그대로 디컴파일 시도할 경우, 에러가 나는데 팀원이 에러나는 부분을 수정해서 제공해줬다~


변환한 Haskell Code는 아래와 같다.
```Haskell
('Main_main_closure', '=', '>> $fMonadIO
    (putStrLn (unpackCString# "Input Serial Key >>> "))
    (>>= $fMonadIO
        getLine
        (\\s1dZ_info_arg_0 ->
            >> $fMonadIO
                (putStrLn
                    (++ (unpackCString# "your serial key >>> ")
                    (++ s1b7_info (++ (unpackCString# "_")
                    (++ s1b9_info (++ (unpackCString# "_") s1bb_info))))))

                (case &&
                    (== $fEqInt (ord (!! s1b7_info loc_7172456)) (I# 70))
                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172472)) (I# 108))
                        (&& (== $fEqInt (ord (!! s1b7_info loc_7172488)) (I# 97))
                            (&& (== $fEqInt (ord (!! s1b7_info loc_7172504)) (I# 103))
                                (&& (== $fEqInt (ord (!! s1b7_info loc_7172520)) (I# 123))
                                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172536)) (I# 83))
                                        (&& (== $fEqInt (ord (!! s1b7_info loc_7172552)) (I# 48))
                                            (&& (== $fEqInt (ord (!! s1b7_info loc_7172568)) (I# 109))
                                                (&& (== $fEqInt (ord (!! s1b7_info loc_7172584)) (I# 101))
                                                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172600)) (I# 48))
                                                        (&& (== $fEqInt (ord (!! s1b7_info (I# 10))) (I# 102))
                                                            (&& (== $fEqInt (ord (!! s1b7_info (I# 11))) (I# 85))
                                                                (== $fEqInt (ord (!! s1b7_info (I# 12))) (I# 53)))))))))))))
                    of
                    <tag 1> -> putStrLn (unpackCString# ":p"),
                    c1ni_info_case_tag_DEFAULT_arg_0@_DEFAULT -> case == ($fEq[] $fEqChar)
                    (reverse s1b9_info)
                    (: (C# 103)
                        (: (C# 110)
                            (: (C# 105)
                                (: (C# 107)
                                    (: loc_7168872
                                        (: loc_7168872
                                            (: (C# 76)
                                                (: (C# 51)
                                                    (: (C# 114)
                                                        (: (C# 52) [])))))))))) of
                        False -> putStrLn (unpackCString# ":p"),
                        True -> case &&

                        (== $fEqChar (!! s1bb_info 0) (!! s1b3_info loc_7172456)) (&&
                            (== $fEqChar (!! s1bb_info 1) (!! s1b4_info (I# 19))) (&&
                                (== $fEqChar (!! s1bb_info 2) (!! s1b3_info (I# 19))) (&&
                                    (== $fEqChar (!! s1bb_info 3) (!! s1b4_info 7)) (&&
                                        (== $fEqChar (!! s1bb_info 4) (!! s1b2_info 2)) (&&
                                            (== $fEqChar (!! s1bb_info 5) (!! s1b3_info (I# 18))) (&&
                                                (== $fEqChar (!! s1bb_info 6) (!! s1b4_info (I# 19))) (&&
                                                    (== $fEqChar (!! s1bb_info 7) (!! s1b2_info 3)) (&&
                                                        (== $fEqChar (!! s1bb_info 8) (!! s1b4_info (I# 17)))
                                                            (== $fEqChar (!! s1bb_info 9) (!! s1b4_info (I# 18))))))))))) of
                    <tag 1> -> putStrLn (unpackCString# ":p"),
                    c1tb_info_case_tag_DEFAULT_arg_0@_DEFAULT -> putStrLn (unpackCString# "Correct Serial Key! Auth Flag!")
                )
        )
    )')
('s1b4_info', '=', 'unpackCString# "abcdefghijklmnopqrstuvwxyz"')
('loc_7172600', '=', 'I# 9')
('s1bb_info', '=', '!! s1b5_info loc_7172488')
('loc_7172488', '=', 'I# 2')
('s1b5_info', '=', 'splitOn $fEqChar (unpackCString# "#") s1dZ_info_arg_0')
('loc_7172584', '=', 'I# 8')
('loc_7172504', '=', 'I# 3')
('s1b2_info', '=', 'unpackCString# "1234567890"')
('loc_7172568', '=', 'I# 7')
('loc_7172552', '=', 'I# 6')
('s1b3_info', '=', 'unpackCString# "ABCDEFGHIJKLMNOPQRSTUVWXYZ"')
('loc_7172536', '=', 'I# 5')
('loc_7172520', '=', 'I# 4')
('loc_7172472', '=', 'I# 1')
('loc_7172456', '=', 'I# 0')
('loc_7168872', '=', 'C# 48')
('s1b9_info', '=', '!! s1b5_info loc_7172472')
('s1b7_info', '=', '!! s1b5_info loc_7172456')
```


문법을 조금 찾아보면, 입력 값에 대해서 `#`으로 구분하여 3부분으로 나눈다. ASCII 값 비교 구문을 통해, serial check를 수행하는데, 첫번째 구간에 해당하는 string은 아래와 같다.
```Haskell
(== $fEqInt (ord (!! s1b7_info loc_7172456)) (I# 70))
                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172472)) (I# 108))
                        (&& (== $fEqInt (ord (!! s1b7_info loc_7172488)) (I# 97))
                            (&& (== $fEqInt (ord (!! s1b7_info loc_7172504)) (I# 103))
                                (&& (== $fEqInt (ord (!! s1b7_info loc_7172520)) (I# 123))
                                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172536)) (I# 83))
                                        (&& (== $fEqInt (ord (!! s1b7_info loc_7172552)) (I# 48))
                                            (&& (== $fEqInt (ord (!! s1b7_info loc_7172568)) (I# 109))
                                                (&& (== $fEqInt (ord (!! s1b7_info loc_7172584)) (I# 101))
                                                    (&& (== $fEqInt (ord (!! s1b7_info loc_7172600)) (I# 48))
                                                        (&& (== $fEqInt (ord (!! s1b7_info (I# 10))) (I# 102))
                                                            (&& (== $fEqInt (ord (!! s1b7_info (I# 11))) (I# 85))
                                                                (== $fEqInt (ord (!! s1b7_info (I# 12))) (I# 53)))))))))))))
```

```python
>>> a = [70,108,97,103,123,83,48,109,101,48,102,85,53]
>>> for i in a:
...     print chr(i),
...
F l a g { S 0 m e 0 f U 5
```
<br>

두번째 구간에 해당 하는 비교 부분은 아래와 같다.
```Haskell
(: (C# 103)
    (: (C# 110)
        (: (C# 105)
            (: (C# 107)
                (: loc_7168872
                    (: loc_7168872
                        (: (C# 76)
                            (: (C# 51)
                                (: (C# 114)
                                    (: (C# 52) [])))))))))) of
```
```python
>>> b = [103,110,105,107,48,48,76,51,114,52]
>>> for i in b:
...     print chr(i),
...
g n i k 0 0 L 3 r 4
>>> r = "g n i k 0 0 L 3 r 4"
>>> r[::-1]
'4 r 3 L 0 0 k i n g'
```

세번째 구간에 해당하는 비교 부분은 아래와 같다.
```Haskell
(== $fEqChar (!! s1bb_info 0) (!! s1b3_info loc_7172456)) (&&
    (== $fEqChar (!! s1bb_info 1) (!! s1b4_info (I# 19))) (&&
        (== $fEqChar (!! s1bb_info 2) (!! s1b3_info (I# 19))) (&&
            (== $fEqChar (!! s1bb_info 3) (!! s1b4_info 7)) (&&
                (== $fEqChar (!! s1bb_info 4) (!! s1b2_info 2)) (&&
                    (== $fEqChar (!! s1bb_info 5) (!! s1b3_info (I# 18))) (&&
                        (== $fEqChar (!! s1bb_info 6) (!! s1b4_info (I# 19))) (&&
                            (== $fEqChar (!! s1bb_info 7) (!! s1b2_info 3)) (&&
                                (== $fEqChar (!! s1bb_info 8) (!! s1b4_info (I# 17)))
                                    (== $fEqChar (!! s1bb_info 9) (!! s1b4_info (I# 18))))))))))) of
<tag 1> -> putStrLn (unpackCString# ":p"),
c1tb_info_case_tag_DEFAULT_arg_0@_DEFAULT -> putStrLn (unpackCString# "Correct Serial Key! Auth Flag!"))
```
```python
>>> s1b3_info = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
>>> s1b2_info = "1234567890"
>>> s1b4_info = "abcdefghijklmnopqrstuvwxyz"
>>> s1b3_info[0]
'A'
>>> s1b4_info[19]
't'
>>> s1b3_info[19]
'T'
>>> s1b4_info[7]
'h'
>>> s1b2_info[2]
'3'
>>> s1b3_info[18]
'S'
>>> s1b4_info[19]
't'
>>> s1b2_info[3]
'4'
>>> s1b4_info[17]
'r'
>>> s1b4_info[18]
's'
>>> AtTh3St4rs
```
<br>

> 각 구간별, string을 합치면 `Flag{S0me0fU5#4r3L00king#AtTh3St4rs`임을 알 수 있으며, input serial로 입력할 경우 Flag를 확인할 수 있다.

![](/assets/images/2018-02-06-Codegate-CTF-Easy-Serial-350/result.png)
<p align='center'><i>[그림] result</i></p>

<p align='right'><strong>Flag{S0me0fU5_4r3L00king_AtTh3St4rs}</strong></p>
