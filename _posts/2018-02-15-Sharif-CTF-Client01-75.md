---
layout: post
title: "[2018_Sharif_CTF] Client01(75)"
description:
headline:
modified: 2018-02-15
category: [CTF, 2018_Sharif]
tags: [Forensic, string]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---

## Exercise

> Attached file is the homepage of the client01. He knows the flag.

![](/images/2018-02-15-Sharif-CTF-Client01-75/exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

첨부된, Client01의 홈페이지 내에서 Flag를 찾는 문제. 주어진 파일의 압축을 풀면 아래와 같은 디렉토리 구조를 확인할 수 있다. `ls -al`로 살펴볼 경우, 숨김 파일로 처리된 항목이 다수 존재하는 것을 확인.

```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/75_Client01
 tree ./client01
./client01
├── Desktop
├── Documents
├── Downloads
├── Music
├── Pictures
├── Public
├── Templates
└── Videos

jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/75_Client01
ls -alr ./client01
total 104
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Videos
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Templates
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Public
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Pictures
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Music
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Downloads
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Documents
drwxr-xr-x@  2 jsh05042  staff     68  1 21 16:33 Desktop
-rw-------@  1 jsh05042  staff   7046  1 21 16:44 .xsession-errors.old
-rw-------@  1 jsh05042  staff   7072  1 24 14:13 .xsession-errors
drwxr-xr-x@  6 jsh05042  staff    204  2 15 19:12 .thunderbird
-rw-r--r--@  1 jsh05042  staff    675  1 21 16:20 .profile
drwxr-xr-x@  5 jsh05042  staff    170  2 15 19:12 .mozilla
drwxr-xr-x@  4 jsh05042  staff    136  2 15 19:12 .local
drwxr-xr-x@  4 jsh05042  staff    136  2 15 19:12 .gnupg
drwx------@  2 jsh05042  staff     68  1 21 16:33 .gconf
-rw-r--r--@  1 jsh05042  staff     55  1 21 16:33 .dmrc
drwxr-xr-x@ 12 jsh05042  staff    408  2 15 19:12 .config
drwxr-xr-x@  6 jsh05042  staff    204  2 15 19:12 .cinnamon
drwxr-xr-x@  7 jsh05042  staff    238  2 15 19:11 .cache
-rw-r--r--@  1 jsh05042  staff   3526  1 21 16:20 .bashrc
-rw-r--r--@  1 jsh05042  staff    220  1 21 16:20 .bash_logout
-rw-------@  1 jsh05042  staff     51  1 24 14:09 .Xauthority
-rw-------@  1 jsh05042  staff    632  1 24 14:09 .ICEauthority
-rw-r--r--@  1 jsh05042  staff  10244  2 15 19:12 .DS_Store
drwxr-xr-x   6 jsh05042  staff    204  2 15 19:11 ..
drwxr-xr-x@ 27 jsh05042  staff    918  2 15 19:11 .
```


grep 을 이용해, flag 관련 문자열을 탐색한 결과 일부 파일 내에서 해당 문자열이 존재하는 것을 확인해 볼 수 있었다.

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/75_Client01
 grep -r "flag" ./client01
Binary file ./client01/.mozilla/firefox/c3a958fk.default/places.sqlite matches
Binary file ./client01/.mozilla/firefox/c3a958fk.default/secmod.db matches
./client01/.thunderbird/5bd7jhog.default/blocklist-addons.json:        "why": "Certain versions of this add-on contains an executable that is flagged by multiple tools as malware. Newer versions no longer use it.",
Binary file ./client01/.thunderbird/5bd7jhog.default/calendar-data/local.sqlite matches
Binary file ./client01/.thunderbird/5bd7jhog.default/global-messages-db.sqlite matches
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/All Mail.msf:  (86=date)(87=size)(88=flags)(89=priority)(8A=label)(8B=statusOfset)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/All Mail.msf:<(93=flag)>[3:^9A(^95^93)]
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/All Mail.msf:    ={"threadCol":{"visible":true,"ordinal":"1"},"flaggedCol":{"visible":true,\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Drafts.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Drafts.msf:    ={"threadCol":{"visible":true},"attachmentCol":{"visible":true},"flaggedCo\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Important.msf:  (87=size)(88=flags)(89=priority)(8A=label)(8B=statusOfset)(8C=numLines)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Important.msf:    ={"threadCol":{"visible":true,"ordinal":"1"},"flaggedCol":{"visible":true,\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Sent Mail.msf:  (88=flags)(89=priority)(8A=label)(8B=statusOfset)(8C=numLines)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Sent Mail.msf:    ={"threadCol":{"visible":true},"attachmentCol":{"visible":true},"flaggedCo\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Spam.msf:  (88=flags)(89=priority)(8A=label)(8B=statusOfset)(8C=numLines)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Spam.msf:    ={"threadCol":{"visible":true,"ordinal":"1"},"flaggedCol":{"visible":true,\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Starred.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Trash:Subject: flag
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Trash.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Trash.msf:  (AA=flag)(AB
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/[Gmail].sbd/Trash.msf:    ={"threadCol":{"visible":true,"ordinal":"1"},"flaggedCol":{"visible":true,\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/Archives.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/Drafts.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/INBOX:Subject: flag
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/INBOX.msf:  (88=flags)(89=priority)(8A=label)(8B=statusOfset)(8C=numLines)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/INBOX.msf:    ={"threadCol":{"visible":true,"ordinal":"1"},"flaggedCol":{"visible":true,\
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/Sent.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com/Templates.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/ImapMail/imap.gmail.com.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
Binary file ./client01/.thunderbird/5bd7jhog.default/kinto.sqlite matches
./client01/.thunderbird/5bd7jhog.default/Mail/Local Folders/Trash.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/Mail/Local Folders/Unsent Messages.msf:  (84=references)(85=recipients)(86=date)(87=size)(88=flags)(89=priority)
./client01/.thunderbird/5bd7jhog.default/panacea.dat:  (81=ns:msg:db:table:kind:folders)(82=key)(83=flags)(84=totalMsgs)
Binary file ./client01/.thunderbird/5bd7jhog.default/places.sqlite matches
Binary file ./client01/.thunderbird/5bd7jhog.default/secmod.db matches
./client01/.xsession-errors:    'su', (bus_name, flags)))
./client01/.xsession-errors.old:    'su', (bus_name, flags)))

 ```


.thunderbird 파일 내에서 e-mail과 관련된 항목에서 해당 flag 문자열이 식별되는 것을 확인할 수 있었다. thunderbird 파일을 살펴본다. `inbox` 항목 내에서 flag 라는 제목으로 수신된 email을 확인할 수 있으며, link 를 하나 식별할 수 있다.

![](/images/2018-02-15-Sharif-CTF-Client01-75/inbox.png)
<p align='center'><i>[그림] inbox mail</i></p>


해당 링크 `http://www.filehosting.org/file/details/720884/file`로 접속하면, 아래와 같이 download link를 redirect 할 mail을 입력할 수 있다.
![](/images/2018-02-15-Sharif-CTF-Client01-75/filedown1.png)
<p align='center'><i>[그림] file download1</i></p>


본인의 mail 주소를 넣고, inbox 함을 확인한 결과 file download를 수행할 수 있는 link를 받을 수 있었으며, 해당 파일을 정상적으로 download 할 수 있었다.
![](/images/2018-02-15-Sharif-CTF-Client01-75/filedown2.png)
<p align='center'><i>[그림] file download2</i></p>



download 받은 파일에 대해 확인해보면, data stream으로 식별되나 hex 값을 통해 signature가 손상된 PNG 파일임을 알 수 있다.
![](/images/2018-02-15-Sharif-CTF-Client01-75/file_information.png)
<p align='center'><i>[그림] file information</i></p>


올바른 signature 값으로 offset을 수정한 뒤, 해당 이미지를 열어보면 Flag을 식별할 수 있었다. (조금 잘려서 보이지만 식별하는데에는 이상이 없다.)

![](/images/2018-02-15-Sharif-CTF-Client01-75/fixed.png)
<p align='center'><i>[그림] signature fix</i></p>

![](/images/2018-02-15-Sharif-CTF-Client01-75/flag.png)
<p align='center'><i>[그림] flag</i></p>

<p align='right'><strong>SharifCTF{43215f0c5e005d4e557ddfe3f2e57df0}</strong></p>
