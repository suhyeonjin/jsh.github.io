---
title: '[2018_Sharif_CTF] Crashed DB(50)'
categories:
  - CTF
  - 2018 Sharif CTF
tags: [Forensic, sqlite3, db]
date: 2018-02-15 19:47:05
thumbnail: /images/sharif_CTF.png
---

## Exercise

> We lost some data when we were delivering our DB.
Can you recover it??
Hint: SQLite

![](exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

Forensic 첫번째 문제, DB 전송 과정에서 일부 데이터를 손실했으며, 이를 복구할 수 있는지 묻고 있다. SQLite 가 hint로 주어진 것을 보아, sqlite db 관련 문제임을 확인.


sqlite3 를 통해, 해당 binary를 읽어보면, db 정보가 전혀 식별되지 않는 것을 확인할 수 있으며 table 조회 시, error를 볼 수 있다.

```bash
jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/50_Crashed DB
 sqlite3 ./db0.db
SQLite version 3.16.0 2016-11-04 19:09:39
Enter ".help" for usage hints.
sqlite> .dbinfo
database page size:  0
write format:        0
read format:         0
reserved bytes:      0
file change counter: 0
database page count: 0
freelist page count: 0
schema cookie:       0
schema format:       0
default cache size:  0
autovacuum top root: 0
incremental vacuum:  0
text encoding:       0
user version:        0
application id:      0
software version:    0
number of tables:    0
number of indexes:   0
number of triggers:  0
number of views:     0
schema size:         0
```

```bash
sqlite> .table
Error: file is encrypted or is not a database
```


binary 확인 시, signature를 포함한 header 정보가 날아가 있는 것을 확인할 수 있다.
![](init.png)
<p align='center'><i>[그림] Header information</i></p>


적당한, sqlite db 파일을 하나 만들고 `db0.db`의 0번째 offset 인 0x0D 로 구분되는 지점까지의 header offset을 가져다 넣어준다.
![](tmp_header.png)
<p align='center'><i>[그림] tmp Header</i></p>

![](fix_header.png)
<p align='center'><i>[그림] fixed Header</i></p>


remake 한 db 파일의 정보를 확인하여, table 정보를 식별하려 하면, 아래와 같은 error message를 확인할 수 있다.

```bash
 jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/50_Crashed DB
 sqlite3 ./remake_db0.db
SQLite version 3.16.0 2016-11-04 19:09:39
Enter ".help" for usage hints.
sqlite> .dbinfo
database page size:  1024
write format:        1
read format:         1
reserved bytes:      0
file change counter: 1
database page count: 2
freelist page count: 0
schema cookie:       1
schema format:       4
default cache size:  0
autovacuum top root: 0
incremental vacuum:  0
text encoding:       1 (utf8)
user version:        0
application id:      0
software version:    3008004
number of tables:    0
number of indexes:   0
number of triggers:  0
number of views:     0
schema size:         0

sqlite> .table
Error: database disk image is malformed
```

Error 정보를 확인해보면, page 와 관련이 있었다. 주어진 db 의 크기는 8kb size를 가진다. 아래와 같이 page의 크기를 0x1000 (4096) 으로 명시하고, 하나의 page 크기가 0x1000일 때, 해당 db는 8kb 이므로 2 page로 구성된다. 따라서 offset을 0x02로 수정한다.
![](fixed.png)
<p align='center'><i>[그림] fixed Header Offset</i></p>



이후, table과 table schema를 정상적으로 식별할 수 있었다.

```bash
sqlite> .schema
CREATE TABLE tbl (Glaf varchar(15), Flag varchar(1), Lfag varchar(15));

sqlite> .table
tbl
```


`tbl`이라는 table 내에서 `Flag`라는 column을 하나 확인할 수 있는데 해당  정보를 출력하면 flag를 확인할 수 있다.
```bash
✘ jsh05042@Macs-MacBook-Pro  ~/Desktop/CTF/2018_Sharif_CTF/Forensic/50_Crashed DB
 sqlite3 ./remake_db0.db
SQLite version 3.16.0 2016-11-04 19:09:39
Enter ".help" for usage hints.
sqlite> select Flag from tbl;
S
h
a
r
i
f
C
T
F
{
7
d
9
e
d
4
a
5
8
6
7
f
6
b
d
3
7
6
9
2
8
a
3
e
d
7
8
3
7
a
0
7
}
sqlite>
```

<p align='right'><strong>SharifCTF{7d9ed4a5867f6bd376928a3ed7837a07}</strong></p>
