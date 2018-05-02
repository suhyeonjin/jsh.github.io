---
layout: post
title: "[2018_AceBear_CTF] Forensic/MISC Welcome(100)"
description:
headline:
modified: 2018-01-31
category: [CTF,2018_AceBear]
tags: [ctf,Forensic, MISC, xor]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---

## Exercise

> Welcome to AceBear Security Contest:<br>
> Cipher:

```bash
172d330d21283133037c65101220703c187a3b1033202f24092c33103021261721273821773b3e
```
> author: komang4130

![](/images/2018-01-31-AceBear-CTF-Welcome-100/exercise.png)


## Solution

Cipher Text가 하나 주어진다. 해당 Cipher 를 Plain Text 로써 의미있는 문자열로 복호화 해내는 문제.
감이 모자란지라,, 처음엔 ROT, Hex Decoding 등으로 다르게 접근 했었는데 flag format 과 xor을 이용해서 해결할 수 있었다.

> 1. cipher를 hex 형태로 convert 해야한다.

> 2. flag format이 'AceBear{' 임을 이미 알고 있기 때문에, 해당 길이만큼 hex로 decode 한 cipher에 대해 xor 하여 flag format text 가 생성되는 xor key를 찾을 수 있다. (VNVODICH)

> 3. cipher 와 찾은 xor key를 이용하여 다시 xor을 수행함으로써, flag를 확인할 수 있다.

```python
>>> a
'172d330d21283133037c65101220703c187a3b1033202f24092c33103021261721273821773b3e'
>>> a = a.decode('hex')
>>> a
"\x17-3\r!(13\x03|e\x10\x12 p<\x18z;\x103 /$\t,3\x100!&\x17!'8!w;>"
>>> b
'\x17-3\r!(13'
>>> plain
'AceBear{'
>>> xor(plain,b)
'VNVODICH'
>>> xor(a,"VNVODICH")
'AceBear{U23_Vi3tN4m_will_be_the_winn3r}'
```

<p align='right'><strong>AceBear{U23_Vi3tN4m_will_be_the_winn3r}</strong></p>
