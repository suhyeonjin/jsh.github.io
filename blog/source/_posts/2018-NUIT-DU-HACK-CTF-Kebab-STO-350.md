---
title: '[2018_NUIT_DU_HACK_CTF] Kebab STO(350)'
categories:
  - CTF
  - 2018 NUIT DU HACK CTF
tags: [Network]
date: 2018-04-02 00:29:52
thumbnail: /images/NDH.png
---


## Exercise

> Description
> The challenge consists in finding the flag into a network capture.
> Challenge is listening on port 8888
> Url : tcp://kebabsto.challs.malice.fr/

![](exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

Network 문제, Pcap 파일(`kebabsto.pcapng`)을 확인할 수 있다. HTTP Object 를 먼저 확인했을 때, "kdsqfkpdsdf" 파일을 download 한 것을 확인.

![](1.png)
<p align='center'>[그림] HTTP Object</p>


save 하고, ZIP 확장자를 가지며 압축을 풀었을 때 "lkdjflknezcz"라는 tcp packet file를 획득할 수 있었다. file signature 를 통해서, 802.11 packet file임을 알 수 있다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Nu_it_du_hack/Network
 file ./kdsqfkpdsdf
./kdsqfkpdsdf: Zip archive data, at least v2.0 to extract
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Nu_it_du_hack/Network
 unzip ./kdsqfkpdsdf
Archive:  ./kdsqfkpdsdf
  inflating: lkdjflknezcz
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Nu_it_du_hack/Network
 file ./lkdjflknezcz
./lkdjflknezcz: tcpdump capture file (little-endian) - version 2.4 (802.11, capture length 65535)
```


하지만, 802.11 packet 내용에 대해서는 아래와 같이 식별할 수 있는 정보들이 존재하지 않았다. WPA 방식의 Encrypt를 사용하고 있었으며, 이를 decrypt할 필요가 보였다.
![](2.png)
<p align='center'>[그림] lkdjflknezcz pcap</p>



802.11 packet decrypt를 위해서는 wireshark preference의 802.11 항목에 Password:SSID를 넣어주어야 하는데 이를 구하기 위해 `aircrack-ng`과 사전파일을 이용해 WPA 방식의 인증을 풀어내었다. password = "abcdefgh" (간단하게 해결될 수 있는 부분이었는데 아래에 TCP 통신을 통해 Decrypt Message와 관련된 부분을 이용해 진행하려다 시간을 많이 허비)
![](3.png)
<p align='center'>[그림] aircrack-ng</p>


아래와 같이, preference 에서 ssid, pw 정보를 이용해 decrypt 하겠다고 명시해준 후, packet 을 확인해보면, zip 파일의 전송을 확인할 수 있다. raw 로 type 변환 후, 확인한 결과 압축 해제시 Password 를 요구하는 것을 볼 수 있었다. (Password와 관련된 항목을 찾아내야 하는데, 이 부분에서 좀 헤맸던 것 같다.)
![](4.png)
<p align='center'>[그림] 802.11 decrypt</p>

![](5.png)
<p align='center'>[그림] 802.11 packet transfer</p>

![](6.png)
<p align='center'>[그림] pk password</p>



결과적으로 첫번째, pcap file ("kebabsto")에서 관련 항목을 찾아낼 수 있었는데, frame contains 을 통해, "flag", "zip", "PK" 등의 packet filtering 을 한 결과 SMTP를 통해 전송된 아래 데이터를 확인할 수 있었고, "docs.zip"라는 파일을 추가적으로 식별할 수 있었다.

```
* OK [CAPABILITY IMAP4rev1 LITERAL+ SASL-IR LOGIN-REFERRALS ID ENABLE IDLE AUTH=PLAIN AUTH=LOGIN] Dovecot ready.
1 authenticate PLAIN
+
AHRvbWF0ZUBkZWJpYW4ueW8AdG9tYXRlMTIz
1 OK [CAPABILITY IMAP4rev1 LITERAL+ SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS SPECIAL-USE BINARY MOVE] Logged in
2 ID ("name" "Thunderbird" "version" "52.4.0")
* ID ("name" "Dovecot")
2 OK ID completed.
3 select "Sent"
* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
* 17 EXISTS
* 0 RECENT
* OK [UIDVALIDITY 1513610478] UIDs valid
* OK [UIDNEXT 18] Predicted next UID
3 OK [READ-WRITE] Select completed (0.001 secs).
4 UID fetch 1:* (FLAGS)
* 1 FETCH (UID 1 FLAGS (\Seen))
* 2 FETCH (UID 2 FLAGS (\Seen))
* 3 FETCH (UID 3 FLAGS (\Seen))
* 4 FETCH (UID 4 FLAGS (\Seen))
* 5 FETCH (UID 5 FLAGS (\Seen))
* 6 FETCH (UID 6 FLAGS (\Seen))
* 7 FETCH (UID 7 FLAGS (\Seen))
* 8 FETCH (UID 8 FLAGS (\Seen))
* 9 FETCH (UID 9 FLAGS (\Seen))
* 10 FETCH (UID 10 FLAGS (\Seen))
* 11 FETCH (UID 11 FLAGS (\Seen))
* 12 FETCH (UID 12 FLAGS (\Seen))
* 13 FETCH (UID 13 FLAGS (\Seen))
* 14 FETCH (UID 14 FLAGS (\Seen))
* 15 FETCH (UID 15 FLAGS (\Seen))
* 16 FETCH (UID 16 FLAGS (\Seen))
* 17 FETCH (UID 17 FLAGS (\Seen))
4 OK Fetch completed.
5 UID fetch 17 (UID RFC822.SIZE FLAGS BODY.PEEK[HEADER.FIELDS (From To Cc Bcc Subject Date Message-ID Priority X-Priority References Newsgroups In-Reply-To Content-Type Reply-To)])
* 17 FETCH (UID 17 RFC822.SIZE 844 FLAGS (\Seen) BODY[HEADER.FIELDS (FROM TO CC BCC SUBJECT DATE MESSAGE-ID PRIORITY X-PRIORITY REFERENCES NEWSGROUPS IN-REPLY-TO CONTENT-TYPE REPLY-TO)] {406}
Subject: Re: Private
To: salade <salade@debian.yo>
References: <6c9268e7-035b-0fa4-0486-c6624af41265@debian.yo>
 <5f8e93f0-9b6a-522f-f35a-3225bc919df7@debian.yo>
From: tomate <tomate@debian.yo>
Message-ID: <9468245b-cfd1-4756-7fe4-0912efceaf9b@debian.yo>
Date: Thu, 21 Dec 2017 17:09:22 +0100
In-Reply-To: <5f8e93f0-9b6a-522f-f35a-3225bc919df7@debian.yo>
Content-Type: text/plain; charset=utf-8

)
5 OK Fetch completed.
6 IDLE
+ idling
* OK Still here
DONE
6 OK Idle completed.
7 noop
7 OK NOOP completed.
8 UID fetch 18:* (FLAGS)
* 17 FETCH (UID 17 FLAGS (\Seen))
8 OK Fetch completed.
9 IDLE
+ idling
DONE
9 OK Idle completed.
10 append "Sent" (\Seen) {2227+}
To: salade <salade@debian.yo>
From: tomate <tomate@debian.yo>
Subject: Private
Message-ID: <3b6a11fe-60fb-f7b3-421e-29b5c0699eb9@debian.yo>
Date: Thu, 21 Dec 2017 17:12:57 +0100
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:52.0) Gecko/20100101
 Thunderbird/52.4.0
MIME-Version: 1.0
Content-Type: multipart/mixed;
 boundary="------------821D8D192BDE01EA2B1C5E07"
Content-Language: en-US

This is a multi-part message in MIME format.
--------------821D8D192BDE01EA2B1C5E07
Content-Type: text/plain; charset=utf-8
Content-Transfer-Encoding: 7bit

They also retrieved a public key and an interesting cipher text
(attached documents).

Besides, they also found a service at mydomainndh.ndh (port 55555) which
decrypts every text encrypted with the public key, apart from the
interesting one.


--------------821D8D192BDE01EA2B1C5E07
Content-Type: application/zip;
 name="docs.zip"
Content-Transfer-Encoding: base64
Content-Disposition: attachment;
 filename="docs.zip"

UEsDBAoAAAAAAFZ6lUsAAAAAAAAAAAAAAAAFABwAZG9jcy9VVAkAA8PCO1rdwjtadXgLAAEE
AAAAAAQAAAAAUEsDBBQAAAAIALd5lUvQbcUR4wAAABABAAAPABwAZG9jcy9wdWJrZXkucGVt
VVQJAAOawTtamsE7WnV4CwABBAAAAAAEAAAAAGXPvVaDMABA4Z2n6M7hlAoKjEmIIWDTRsQD
bBESaKAHqBZant6f1Tve7bOsnyAmlG2OGXyhaJPg4vdZxp4StQc2QelEUvrhhBxDwDMAXMJA
iOCJJ7DhyI9Zy6iQHS91tfO3c1HK3dO8xEZeTcr/fNflBXHlicfbpSZjdOg7hL8EVolIUG76
7EwCoO/SC1yXVlHkyb7QrT1Kaqy79PU6d+71efRANYAgdtb6EGdi3T6oocl7YS5qPca3KRig
NxdAuW+lduwwS9WY0co4iWaxdat13Y5nqbC50BBwAI0/IWbhP/Q3UEsDBBQAAAAIAFZ6lUus
24QeoQAAADUBAAAPABwAZG9jcy9jaXBoZXJUZXh0VVQJAAPDwjtaw8I7WnV4CwABBAAAAAAE
AAAAABWPyREAQQgC/xuNFwL5J7bOS6pstWWJTYxoe/3qYopgqq6OQruta0ieSC8nAmvPMGJU
dmics9wyyrkHpTRV0wVPZoYboT5GDCYmr+GHtrBhLuuGW4llFBXLTFgBbJPOEurBXQ6c0m1c
xGlI0ZF9wq0L1SeoSkY/n+jtPLcLieaBhiYBAtmsZutOmad7MwzEiXVy4+6Z991gvMPi2ewk
6/sBUEsBAh4DCgAAAAAAVnqVSwAAAAAAAAAAAAAAAAUAGAAAAAAAAAAQAO1BAAAAAGRvY3Mv
VVQFAAPDwjtadXgLAAEEAAAAAAQAAAAAUEsBAh4DFAAAAAgAt3mVS9BtxRHjAAAAEAEAAA8A
GAAAAAAAAQAAAKSBPwAAAGRvY3MvcHVia2V5LnBlbVVUBQADmsE7WnV4CwABBAAAAAAEAAAA
AFBLAQIeAxQAAAAIAFZ6lUus24QeoQAAADUBAAAPABgAAAAAAAEAAACkgWsBAABkb2NzL2Np
cGhlclRleHRVVAUAA8PCO1p1eAsAAQQAAAAABAAAAABQSwUGAAAAAAMAAwD1AAAAVQIAAAAA

--------------821D8D192BDE01EA2B1C5E07--

* 18 EXISTS
* 1 RECENT
10 OK [APPENDUID 1513610478 18] Append completed.
11 noop
11 OK NOOP completed.
12 IDLE
+ idling

```


해당 tcp frame 에는 아래와 같이, docs.zip 파일이 base64 encoding 되어 첨부되어 있는 것을 확인할 수 있으며, 이를 decode 한 뒤 동일하게 추출할 수 있었다.

```
--------------821D8D192BDE01EA2B1C5E07
Content-Type: application/zip;
 name="docs.zip"
Content-Transfer-Encoding: base64
Content-Disposition: attachment;
 filename="docs.zip"

UEsDBAoAAAAAAFZ6lUsAAAAAAAAAAAAAAAAFABwAZG9jcy9VVAkAA8PCO1rdwjtadXgLAAEE
AAAAAAQAAAAAUEsDBBQAAAAIALd5lUvQbcUR4wAAABABAAAPABwAZG9jcy9wdWJrZXkucGVt
VVQJAAOawTtamsE7WnV4CwABBAAAAAAEAAAAAGXPvVaDMABA4Z2n6M7hlAoKjEmIIWDTRsQD
bBESaKAHqBZant6f1Tve7bOsnyAmlG2OGXyhaJPg4vdZxp4StQc2QelEUvrhhBxDwDMAXMJA
iOCJJ7DhyI9Zy6iQHS91tfO3c1HK3dO8xEZeTcr/fNflBXHlicfbpSZjdOg7hL8EVolIUG76
7EwCoO/SC1yXVlHkyb7QrT1Kaqy79PU6d+71efRANYAgdtb6EGdi3T6oocl7YS5qPca3KRig
NxdAuW+lduwwS9WY0co4iWaxdat13Y5nqbC50BBwAI0/IWbhP/Q3UEsDBBQAAAAIAFZ6lUus
24QeoQAAADUBAAAPABwAZG9jcy9jaXBoZXJUZXh0VVQJAAPDwjtaw8I7WnV4CwABBAAAAAAE
AAAAABWPyREAQQgC/xuNFwL5J7bOS6pstWWJTYxoe/3qYopgqq6OQruta0ieSC8nAmvPMGJU
dmics9wyyrkHpTRV0wVPZoYboT5GDCYmr+GHtrBhLuuGW4llFBXLTFgBbJPOEurBXQ6c0m1c
xGlI0ZF9wq0L1SeoSkY/n+jtPLcLieaBhiYBAtmsZutOmad7MwzEiXVy4+6Z991gvMPi2ewk
6/sBUEsBAh4DCgAAAAAAVnqVSwAAAAAAAAAAAAAAAAUAGAAAAAAAAAAQAO1BAAAAAGRvY3Mv
VVQFAAPDwjtadXgLAAEEAAAAAAQAAAAAUEsBAh4DFAAAAAgAt3mVS9BtxRHjAAAAEAEAAA8A
GAAAAAAAAQAAAKSBPwAAAGRvY3MvcHVia2V5LnBlbVVUBQADmsE7WnV4CwABBAAAAAAEAAAA
AFBLAQIeAxQAAAAIAFZ6lUus24QeoQAAADUBAAAPABgAAAAAAAEAAACkgWsBAABkb2NzL2Np
cGhlclRleHRVVAUAA8PCO1p1eAsAAQQAAAAABAAAAABQSwUGAAAAAAMAAwD1AAAAVQIAAAAA

--------------821D8D192BDE01EA2B1C5E07--
```


docs.zip 파일은 아래와 같이 `cipherText`, `private.pem` 2개의 파일을 포함하고 있었는데, cipherText 내용은 아래와 같으며, 결과적으로 문제에서 제시된 서버로, 해당 cipherText를 보낼경우, Decrypt Message를 return하는 것을 확인할 수 있다.
```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Nu_it_du_hack/Network/docs
 nc kebabsto.challs.malice.fr 8888
What message do you want to decrypt: ^C
 ✘ jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Nu_it_du_hack/Network/docs
 cat ./cipherText| nc kebabsto.challs.malice.fr 8888
72873754879996948796542757182427480866384878894019674005699447004829908491467629529161961884224325941110935083467870715412599916138560976722953815670278067115980556377912852138532905866093650699880301357138301236748217037629036311469031537013958415575513723738671978421707050599317605219729945496472798064172
What message do you want to decrypt:

Here is the cleartext of your input :


123360975347216093033775350245751721746535757669936
What message do you want to decrypt: %
```


return 된 "123360975347216093033775350245751721746535757669936"를 hex 로 변환 후, 2글자씩 string 으로 decode 하면 아래와 같은 Password 문자열을 얻을 수 있다.
```python
Python 2.7.10 (default, Jul 15 2017, 17:16:57)
[GCC 4.2.1 Compatible Apple LLVM 9.0.0 (clang-900.0.31)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> a = "123360975347216093033775350245751721746535757669936"
>>> a = 123360975347216093033775350245751721746535757669936
>>> a = hex(a)[2:]
>>> a
'54683173317332504073735f57307264254d306630L'
>>> a = a[:-1]
>>> result = ""
>>> for i in range(len(a)/2):
...     result += chr(int(a[i*2:i*2+2],16))
...
>>> result
'Th1s1s2P@ss_W0rd%M0f0'
```


위에서 얻은 `Th1s1s2P@ss_W0rd%M0f0`를 이용해 이전에 압축 해제시 password 로 인해 풀지 못했던 `slkfdsflj` 파일의 압축을 해제하면 아래와 같이 flag를 확인할 수 있었다.

![](flag.png)
<p align='center'>[그림] flag</p>

<p align='right'><strong>ndh2k18{M4k3M4tr10cHKa9r34T4g41n}</strong></p>
