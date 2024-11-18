---
layout: post
title:  "Ağ/Trafik İzleme (nc-netcat-socat-rawcap-tcpdump-netstat)"
date:   2024-02-26 11:57:24 +0000
categories: Networking
tags: rawcap tcpdump netstat nc socat netstat
---
Ağ izleme araçları ve örneklerini göreceğiz

[rawcap](https://www.netresec.com/?page=rawcap) ve ncat ile Trafik İzleme
=========================================================================

> **Rawcap** Windows için çalışan .Net çatısının 4.7.2 sürümü üstünde koşan ağ trafiğini izleyen ücretsiz bir uygulamadır. Rawcap’i indirmek için [bu adrese](https://www.netresec.com/?download=RawCap) tıklayabilirsiniz.

Rawcap ile trafiği

*   ister stdOut ile **_terminal çıktısına_**,
*   ister **_dosyaya_**,
*   ister boru hattına (**_pipeline_**)
*   isterseniz doğrudan **_Wireshark_** aracına aktarabiliriz.

Tcpdump, aynı işi yapan hem Linux hem Windows üstünde koşabilen bir başka ücretsiz araçtır.

*   `-` Argümanıyla yakaladığı trafiği standart çıktıya (standard output yani terminale veya `stdOut` çıktısını yönlendirdiğiniz yere) aktarabilirsiniz. _Anlaşılır bir çıktı üretmez ama hareketi görebilirsiniz._

```shell
c:\\> RawCap.exe -q 127.0.0.1 -
```

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*nFczvxR36USggWcwi6HdvQ.gif)

*   Varsayılan olarak topladığı verileri “[**pcap**” formatı](https://en.wikipedia.org/wiki/Pcap)nda dosyaya yazar.

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*iRfDl5XhN4vRF7qpso1OBg.gif)

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*McY6qEAmF9ZFFNi6ioIPgQ.gif)

*   Yakaldığı trafiği Wireshark uygulamasına | operatörüyle hemen geçirebilir

```shell
c:\\> RawCap.exe -q 127.0.0.1 - | Wireshark.exe -i - -k
```

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*McY6qEAmF9ZFFNi6ioIPgQ.gif)

*   veya isimli borular ile (`named pipe`) veriyi hattın adını bilen uygulamalarla paylaşabilir.

![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*59HS-sicpdj7Lw23atilTA.gif)

tcpdump ve netcat
=================

tcpdump ile ağ cihazlarını listeleyelim

```shell
$ tcpdump.exe -D
```

![alt text]({{ BASE_PATH }}/assets/images/1_91WIiLl8lhzWkK1_q6rTpw-1.webp)

ncat ve [socat](https://linux.die.net/man/1/socat) ile port yönlendirme (port forwarding)
=========================================================================================

Hızlısından nasıl yönlendirebiliriz, komutları yazalım:

`ncat` ile `nc` arasındaki farkı yardım argümanı ile aşağıda görebiliyoruz.

![alt text]({{ BASE_PATH }}/assets/images/0_wtjeUPQQUU3l5e8d.webp)

![alt text]({{ BASE_PATH }}/assets/images/0_Yaivwk7K6bNaNx4U.webp)

socat’in özelliklerini de gözümüzün önünde tutalım

![alt text]({{ BASE_PATH }}/assets/images/0_kpfIpVU5bUP1Oy2O.webp)

![alt text]({{ BASE_PATH }}/assets/images/0_5gzrjBPRZAth3Ik9.webp)


netstat İle Hangi Proses Hangi Portu Kullanılıyor
=================================================

Ref: [www.tecmint.com](https://www.tecmint.com/find-out-which-process-listening-on-a-particular-port/)

Önce netstat yüklü olmalı:

```shell
$ sudo yum install net-tools    #RHEL/CentOS   
$ sudo apt install net-tools    #Debian/Ubuntu  
$ sudo dnf install net-tools    #Fedora 22+
```

Şimdi portları listeleyebiliriz:

```shell
$ netstat -ltnp | grep -w ':80'
```

l : netstat’a yalnızca dinlenen soketleri göstermesini söyler.

t : tcp bağlantılarını görüntülemesini söyler.

![alt text]({{ BASE_PATH }}/assets/images/0_GDz-FekOOPGZG8tk.webp)

Sadece TCP leri listeler:

![alt text]({{ BASE_PATH }}/assets/images/0_bY212sU02ATcCIBb.webp)

n: sayısal adresleri göstermesi talimatını verir.

p : Hangi işlemin bu port üstünde dinleme yaptığını ve işlem adının gösterilmesini sağlar.

![alt text]({{ BASE_PATH }}/assets/images/0_BUqlHS7yl1VAZK1i.webp)

grep -w : tam dizeyle eşleşmeyi gösterir (“: 80” ).

![alt text]({{ BASE_PATH }}/assets/images/0_0W27Vv3k86mk_s1C.webp)

Varsayılan olarak bağlı olduğu portları gösterir (CONNECTED)

*   l Anahtarı sadece dinlediklerimizi listeler.