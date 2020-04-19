---
layout: single
title: "[2018_Codegate_CTF] RedVelvet (75)"
description:
headline:
modified: 2018-02-06
category: [CTF]
tags: [2018_Codegate, Reversing, Writeup, z3, angr]
imagefeature:
mathjax:
chart:
comments: true
featured: true
toc: true
toc_sticky: true
---

## Exercise

> Happiness:)
> Download

![](/assets/images/2018-02-06-Codegate-CTF-RedVelvet-75/exercise.png)
<p align='center'><i>[그림] exercise</i></p>


## Solution1 - z3 Solver

일본에서 1달간 진행했던 산학연수를 마무리하고 귀국하는 날이라 정신이 없어, 팀원들에게 참가율이 매우 낮을 수 있다고 미리 얘기했었다. 귀국하고 잠깐 Droid 문제 본 것 외엔 제대로 문제도 보지 못하고 흐지부지 넘어갔던 `2018 codegate` ㅠ...

이 문제는 같은 팀원 중 한명이 이미 풀었던 문제이다. z3를 이용해서 해결할 수 있었는 문제인데, z3 사용 외에도 Angr를 이용해서도 해결 할 수 있는 문제이기에 풀 수 있는 방식을 쭉 정리해보려 한다.

binary 정보를 확인해보면, 아래와 같이 64bit ELF 파일이다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Codegate_CTF/reversing/RedVelvet
 file ./RedVelvet
./RedVelvet: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=84e7ef91c33878cf9eefc00a7a450895aa573494, not stripped
```


실행해보면, 아래와 같이 input 을 받는 것을 확인할 수 있는데 별다른 incorrect message 없이 그대로 binary 가 종료 된다.
![](/assets/images/2018-02-06-Codegate-CTF-RedVelvet-75/run.png)
<p align='center'><i>[그림] run</i></p>


바로, IDA 로 binary 분석을 해보면 정상적으로 decompile 되며, 아래와 같은 구조로 이뤄진 것을 볼 수 있다.
```C
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char v3; // bl@0
  __int64 v4; // rax@1
  __int64 v5; // rcx@1
  size_t v6; // rax@2
  char v8; // t1@8
  bool v9; // zf@8
  signed int i; // [sp+8h] [bp-1B8h]@2
  char v11; // [sp+50h] [bp-170h]@2
  char s; // [sp+C0h] [bp-100h]@1
  char v13; // [sp+C1h] [bp-FFh]@1
  char v14; // [sp+C2h] [bp-FEh]@1
  char v15; // [sp+C3h] [bp-FDh]@1
  char v16; // [sp+C4h] [bp-FCh]@1
  char v17; // [sp+C5h] [bp-FBh]@1
  char v18; // [sp+C6h] [bp-FAh]@1
  char v19; // [sp+C7h] [bp-F9h]@1
  char v20; // [sp+C8h] [bp-F8h]@1
  char v21; // [sp+C9h] [bp-F7h]@1
  char v22; // [sp+CAh] [bp-F6h]@1
  char v23; // [sp+CBh] [bp-F5h]@1
  char v24; // [sp+CCh] [bp-F4h]@1
  char v25; // [sp+CDh] [bp-F3h]@1
  char v26; // [sp+CEh] [bp-F2h]@2
  char v27; // [sp+CFh] [bp-F1h]@2
  char v28; // [sp+D0h] [bp-F0h]@2
  char v29; // [sp+D1h] [bp-EFh]@2
  char v30; // [sp+D2h] [bp-EEh]@2
  char v31; // [sp+D3h] [bp-EDh]@2
  char v32; // [sp+D4h] [bp-ECh]@2
  char v33; // [sp+D5h] [bp-EBh]@2
  char v34; // [sp+D6h] [bp-EAh]@2
  char v35; // [sp+D7h] [bp-E9h]@2
  char v36; // [sp+D8h] [bp-E8h]@2
  char v37; // [sp+D9h] [bp-E7h]@2
  char v38[32]; // [sp+E0h] [bp-E0h]@2
  char s1[80]; // [sp+100h] [bp-C0h]@3
  char s2[8]; // [sp+150h] [bp-70h]@1
  __int64 v41; // [sp+158h] [bp-68h]@1
  __int64 v42; // [sp+160h] [bp-60h]@1
  __int64 v43; // [sp+168h] [bp-58h]@1
  __int64 v44; // [sp+170h] [bp-50h]@1
  __int64 v45; // [sp+178h] [bp-48h]@1
  __int64 v46; // [sp+180h] [bp-40h]@1
  __int64 v47; // [sp+188h] [bp-38h]@1
  __int64 v48; // [sp+190h] [bp-30h]@1
  __int64 v49; // [sp+198h] [bp-28h]@1
  __int64 v50; // [sp+1A0h] [bp-20h]@1
  __int64 v51; // [sp+1A8h] [bp-18h]@1
  int v52; // [sp+1B0h] [bp-10h]@1
  __int64 v53; // [sp+1B8h] [bp-8h]@1

  v53 = *MK_FP(__FS__, 40LL);
  *(_QWORD *)s2 = 3905859155515433264LL;
  v41 = 3990529441497888818LL;
  v42 = 7017565014431380534LL;
  v43 = 3977633058323522358LL;
  v44 = 7364290724313116725LL;
  v45 = 3991705742141175652LL;
  v46 = 7363494672811320633LL;
  v47 = 3847534474596595814LL;
  v48 = 0LL;
  v49 = 0LL;
  v50 = 0LL;
  v51 = 0LL;
  v52 = 0;
  printf((const char *)&loc_4016CF + 1, argv, envp);
  fgets(&s, 27, edata);
  func1((unsigned int)s, (unsigned int)v13);
  func2((unsigned int)v13, (unsigned int)v14);
  func3((unsigned int)v14, (unsigned int)v15);
  func4((unsigned int)v15, (unsigned int)v16);
  func5((unsigned int)v16, (unsigned int)v17);
  func6((unsigned int)v17, (unsigned int)v18, (unsigned int)v19);
  func7((unsigned int)v19, (unsigned int)v20, (unsigned int)v21);
  func8((unsigned int)v21, (unsigned int)v22, (unsigned int)v23);
  func9((unsigned int)v23, (unsigned int)v24, (unsigned int)v25);
  v4 = ptrace(0, 0LL, 1LL, 0LL);
  if ( v4 == -1 )
  {
    v8 = *(_BYTE *)v5;
    v9 = v3 + *(_BYTE *)(v5 + 111) == 0;
    *(_BYTE *)(v5 + 111) += v3;
    JUMPOUT(!v9, &unk_401746);
    v6C &= BYTE1(v4);
    JUMPOUT(*(_QWORD *)"ag : ");
  }
  func10((unsigned int)v25, (unsigned int)v26, (unsigned int)v27);
  func11((unsigned int)v27, (unsigned int)v28, (unsigned int)v29);
  func12((unsigned int)v29, (unsigned int)v30, (unsigned int)v31);
  func13((unsigned int)v31, (unsigned int)v32, (unsigned int)v33);
  func14((unsigned int)v33, (unsigned int)v34, (unsigned int)v35);
  func15((unsigned int)v35, (unsigned int)v36, (unsigned int)v37);
  SHA256_Init(&v11);
  v6 = strlen(&s);
  SHA256_Update(&v11, &s, v6);
  SHA256_Final(v38, &v11);
  for ( i = 0; i <= 31; ++i )
    sprintf(&s1[2 * i], "%02x", (unsigned __int8)v38[i]);
  if ( strcmp(s1, s2) )
    exit(1);
  printf("flag : {\" %s \"}\n", &s);
  return 0;
}
```

> 위 decompile code를 보고 알 수 있는 사실은 27개의 문자열을 fgets로 입력 받으며, func 함수들을 통해 일련된 작업을 거친 뒤, SHA256 비교를 진행하고, flag를 출력하는 것을 확인할 수 있다.


총 15개의 func이 존재하며, 각각의 func에는 아래와 같이 인자 값에 대한 비교 연산을 수행한다. 비교 연산 결과가 통과될 경우 `HAPPINESS:)`라는 문자열을 출력하며, 아닐 경우 바로 binary를 종료한다.

```C
int __fastcall func1(char a1, char a2)
{
  if ( a1 * 2 * (char)(a2 ^ a1) - a2 != 10858 )
    exit(1);
  if ( a1 <= 85 || a1 > 95 || a2 <= 96 || a2 > 111 )
    exit(1);
  return puts("HAPPINESS:)");
}
```


위에서 확인할 수 있듯이, 각각의 func들은 일련된 연산 작업으로 구성되어 있는데 이를 효율적으로 해결하기 위해서 `z3`를 이용할 수 있다. 각각의 func에 들어가는 인자 값들을 문자열 배열 변수로 두고, 해당 변수들에 대해서 z3 조건문을 그대로 충족 시켜준다.


총 a1~a27 까지 27개의 변수가 필요하다. `z3` 에서 연산을 위한 각 변수를 선언한다. 아래는 위 `RedVelvet` binary의 연산을 `z3` 수식으로 변환한 내용이다. 꿀팁?이라기보단, 좀 더 편한 model 만드는 방식이 있는데, 대부분 IDA 에서 Hex-Ray로 Decompile된 Code에서 복잡한 수식을 `z3`식으로 옮길 때, `a1 * 2 * (a2 ^ a1) - a2 != 0x2A6A`라는 식이 있으면, s.add(`a1 * 2 * (a2 ^ a1) - a2 == 0x2A6A`)와 같은 형태로 조건 식을 반대로 변환해서 작성한다.

즉, 본 문제에서는 `a1 * 2 * (a2 ^ a1) - a2 != 0x2A6A` 라는 식에 참이 될 경우 `exit`로 빠지기 때문에 해당 조건에 맞지 않는 값을 찾아내야하는 것이다. 이 때, 해당 조건에 맞지 않는 값을 찾기 위해서 비교 연산자를 반대로 설정하는 과정이 필요하다. 하지만, 그럴 필요 없이 IDA 에서 수식을 슥 긁어와 `Not()`으로 감싸주기만 하면 그대로 적용된다. >, < 같은 부등호 연산을 옮길 때, 특히 <= 등으로 변환하는 과정에서 종종 실수할 수 있는데 편하게 `Not()`을 적용 시키면 본래의 수식을 그대로 적용할 수 있기 때문에 굳이 바꿀 필요가 없다.

```python
from z3 import *

s = Solver()

#array = [ BitVec('a%i'%i,32) for i in range(1,27)]
for i in range(1,27):
    globals()['a%i'%i]=BitVec('a%i'%i,32)

#func1 a1, a2
s.add(Not(a1 * 2 * (a2 ^ a1) - a2 != 0x2A6A ))
s.add(Not(a1 <= 0x55))
s.add(Not(a1 > 0x5F))
s.add(Not(a2 <= 0x60 ,a2 > 0x6F))


#func2 a2, a3
s.add(Not(a2 % a3 != 7))
s.add(Not(a3 <= 0x5A))


#func3 a3, a4
s.add(Not(a3 / a4 + (a4 ^ a3) != 21))
s.add(Not(a3 > 99, a4 > 119))


#func4 a4, a5
s.add(Not(a4 <= 115))
s.add(Not(a5 != 95))

#func5 a5, a6
s.add(Not((a6 + a5) ^ (a5 ^ a6 ^ a5) != 225))
s.add(Not( a5 <= 90, a6 > 89))


#func6 a6, a7, a8
s.add(Not(a6 > a7, a7 > a8))
s.add(Not(a6 <= 85, a7 <= 110))
s.add(Not(a8 <= 115, ((a7 + a8) ^ (a6 + a7)) != 44))
s.add(Not((a7 + a8) % a6 + a7 != 161))


#func7 a8, a9, a10
s.add(Not(a8 < a9))
s.add(Not(a9 < a10))
s.add(Not(a8 > 119, a9 <= 90))
s.add(Not(a10 > 89,((a8 + a10) ^ (a9 + a10)) != 122))
s.add(Not((a8 + a10) % a9 + a10 != 101))


#func8 a10, a11, a12
s.add(Not(a10 > a11, a11 > a12))
s.add(Not(a12 > 114))
s.add(Not((a10 + a11) / a12 * a11 != 97))
s.add(Not((a12 ^ (a10 - a11)) * a11 != -10088))
s.add(Not(a12 > 114))

#func9 a12, a13, a14
s.add(Not(a12 != a13))
s.add(Not(a13 < a14))
s.add(Not(a14 > 99))
s.add(Not(a14 + a12 * (a14 - a13) - a12 != -1443 ))


#func10 a14, a15, a16
s.add(Not(a14 < a15))
s.add(Not(a15 < a16))
s.add(Not(a15 * (a14 + a16 + 1) - a16 != 15514))
s.add(Not(a15 <= 90, a15 > 99))


#func11 a16, a17, a18
s.add(Not(a17 < a16))
s.add(Not(a16 < a18))
s.add(Not(a17 <= 100, a17 > 104))
s.add(Not((a16 + (a17 ^ (a17 - a18))) - a18 != 70))
s.add(Not((a17 + a18) / a16 + a16 != 68))


#func12 a18, a19, a20
s.add(Not(a18 < a19))
s.add(Not(a19 < a20))
s.add(Not(a19 > 59, a20 > 44))
s.add(Not(a18 + (a19 ^ (a20 + a19)) - a20 != 111))
s.add(Not((a19 ^ (a19 - a20)) + a19 != 101 ))


#func13 a20, a21, a22
s.add(Not(a20 > a21))
s.add(Not(a21 > a22))
s.add(Not(a20 <= 40, a21 <= 90))
s.add(Not(a22 > 109))
s.add((a22 + (a21 ^ (a22 + a20)) - a20 != 269))
s.add(Not((a22 ^ (a21 - a20)) + a21 != 185))


#func14 a22, a23, a24
s.add(Not(a22 < a23))
s.add(Not(a23 < a24))
s.add(Not(a23 > 99, a24 <= 90))
s.add(Not(a22 + (a23 ^ (a23 + a22)) - a24 != 185))


#func15 a24, a25, a26
s.add(Not(a25 < a26))
s.add(Not(a25 < a24))
s.add(Not(a26 <= 95, a25 > 109))
s.add(Not(((a25 - a24) * a25 ^ a26) - a24 != 1214))
s.add(Not(((a26 - a25) * a26 ^ a24) + a25 != -1034))


print "[+]",s.check()
result = s.model()

flag=""
for i in range(1,27):
    strVar = globals()['a%i' % i]
    char = result[strVar].as_long()
    flag += chr(char)
print flag
```


위 z3 code를 돌리면, flag를 확인할 수 있는데 hash value 가 맞지 않아 정상적으로 인증할 수 없다. 자세히 살펴보면, ` 문자열이 있는걸 볼 수 있는데 이어서 나오는 문자가 'la'인 것을 보고 적절히 바꿔주었더니 정답임을 확인할 수 있었다. Symbolic 자체가 조건에 부합하는 다른 값이 존재할 수 있기 때문에 발생한 문제인 것 같다.

```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Codegate_CTF/문제백업/[Rev]RedVelvet
python ./solver.py
[+] sat
What_You_Wanna_By?:);l`_la
```



<br>
## Solution2 - Angr Script

- reference : http://blog.redrocket.club/2018/02/04/Codegate-2018-Preliminary-RedVelvet/

팀원이 맨처음 Binary를 봤을 때, Angr로 해결할 수 있을 것 같다고 했었는데 Angr로도 역시 해당 문제를 풀이할 수 있었다. Find address, Avoid address 적절히 찾아 배치하고 돌리면 정답이 튀어나오는 것을 볼 수 있었는데, Angr는 항상 내가 짜면 posix dump에서 Not found가 뜬다;;;  차이점을 보니, printable 범위를 지정해준 것. simulator 설정해준 것 등 여러 차이점이 있었다.. 단순하게 find, avoid address 만 지정해주는 방식으로는 해결이 되지 않는다;; angr 연습이 필요할 듯..

```python
import angr

p = angr.Project('./RedVelvet', load_options={"auto_load_libs": False})

st = p.factory.entry_state()

# in printable range
for _ in xrange(26):
    k = st.posix.files[0].read_from(1)
    st.solver.add(k >= 0x20)
    st.solver.add(k <= 0x7e)

# Constrain the last byte to be a newline
k = st.posix.files[0].read_from(1)
st.solver.add(k == 10)

# Reset the symbolic stdin's properties and set its length.
st.posix.files[0].seek(0)
st.posix.files[0].length = 27

sm = p.factory.simulation_manager(st)
sm.explore(avoid=0x004007d0, find=0x0040152d)

print(sm.found[0].posix.dumps(0))

```

```bash
 ⚡ root@RebForPwn  ~/jsh  python ./solver.py
WARNING | 2018-02-06 19:00:44,933 | angr.engines.successors | Exit state has over 257 possible solutions. Likely unconstrained; skipping. <BV64 reg_28_31_64>
What_You_Wanna_Be?:)_lc_la
```


<br>
## Solution3 - AngryIDA
- install reference : https://github.com/jinsoohyun/AngryIDA

`AngryIDA`라는 IDA Plugin을 이용한 풀이 방식인데, Angr가 설치된 x64 Windows 환경에서 IDA Plugin으로 Angr를 바로 이용할 수 있는 방식이다. 얼핏 보면 `Ponce`랑 유사한 느낌.. 아래 처럼 avoid, find offset을 직접 지정해주고, explorer 에서 run만 눌러주면 stdin에 대한 값을 찾아내는 걸 확인할 수 있다. (angr 동작이랑 동일하다, 단지 IDA 에서 수행될 뿐..)

![](/assets/images/2018-02-06-Codegate-CTF-RedVelvet-75/angryIDA.png)
<p align='center'><i>[그림] angryIDA</i></p>

<br>
## ETC - Radare2

- 풀이 올라온 걸 보다보니, Radare2를 이용해 binary 분석을 한 외쿡인도 있었다. 예전에 좀 살펴보고 IDA 에 비해 영 불편해서 안쓰고 살았는데 풀어보는 과정을 보니 역시, 장인은 도구를 탓하지 않는다는 말이 맞는것 같다 ㅋㅋ..


<p align='right'><strong>FLAG{What_You_Wanna_By?:);la_la}</strong></p>
