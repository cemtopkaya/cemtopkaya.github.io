---
layout: post
title:  "VS Code ile Uzak Bağlantı"
date:   2022-11-29 11:57:24 +0000
categories: SSH
tags: ssh jump-node vscode
---
Aslında genel bilgileri [bu adresten](https://code.visualstudio.com/docs/remote/ssh#_remote-ssh-limitations) bulabilirsiniz ben sadece kendime notları alacağım ve belki bulmakta zorlandığım bilgileri burada tutacağım.

## SSH Uzak Bağlantı

```shell
Host jenkins-192.168.57.58-proxyli
HostName 192.168.57.58
TCPKeepAlive yes
User baglanilacak-sunucudaki-kullanici-adi
# ProxyCommand ncat.exe - proxy-type socks4 - proxy 192.168.13.130:5555 %h %p
# ProxyCommand "C:\Program Files\Git\mingw64\bin\connect.exe" -H 192.168.13.130:22 %h %p
ProxyCommand ssh -W %h:%p proxy-kullanici-adi@192.168.13.130
```