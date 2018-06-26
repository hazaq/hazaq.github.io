---
layout: page
title: Cheat Sheet
permalink: /CheatSheet
---

## Yum

![yum](/assets/images/yumcheat.png)

## VIM

![vim](/assets/images/vimCheatSheet.jpg)

## Bash
![Bash](/assets/images/bashcheatSheet.png)

![Bash2](/assets/images/bashcheatSheet2.png)

##openSSL
```
#Get ssl certificate information of a website
openssl s_client -connect slushpool.com:443
#Get local crt/pem key info 
openssl x509 -in acs.qacafe.com.pem -text
```

##ncat
```
#Simple Chat
ncat -l 1234 --chat
ncat localhost 1234
#copy files with UDP
ncat -l 2202 --udp
ncat --udp localhost 2202 < happy.py
#Bind Shell on a port
ncat -l 2202 -e /bin/sh
ncat localhost 2202
```
#bashcolor
```
 export PS1="[\u@\[\e[1;31m\]comp\[\e[0;31m\]-mail\[\e[0m\] \W]\\$ "

```
