---
layout: post
title:  "SCTP Sunucu ve İstemci Komutları"
date:   2024-04-19 11:57:24 +0000
categories: 5G
tags: SCTP
---

## Kernel Modülü Olarak SCTP Desteğinin Faal Edilmesi

Bazı dağıtımlarda, SCTP desteği bir kernel modülü olarak sunulur. Bu durumda, SCTP modülünün yüklenmesi gerekebilir. Örneğin, modprobe veya insmod komutlarıyla SCTP modülünü yükleyebilirsiniz.

```shell
# Ubuntu 22.04
ubuntu@cem:~$ cat /proc/net/sctp/eps
cat: /proc/net/sctp/eps: No such file or directory

ubuntu@cem:~$ netstat -lS
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
netstat: no support for `AF INET (sctp)' on this system.

ubuntu@cem:~$ sudo modprobe sctp
```

SCTP’yi faal ettikten sonra artık çıktılar müspet:

```shell
ubuntu@cem:~$ cat /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS

ubuntu@cem:~$ netstat -lS
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
```

<!-- ![](https://miro.medium.com/v2/resize:fit:459/1*nCo34_Sz5NQGR6b1P1g3vw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_nCo34_Sz5NQGR6b1P1g3vw.webp)

> _SCTP Kernel modülü bağımlı olduğu için ev sahibi bilgisayarın işletim sistemiyle konteynerin çalışacağı base image aynı olmalıdır!_

sctp\_test adlı bir program [lksctp-tools](https://manpages.ubuntu.com/manpages/trusty/man1/sctp_test.1.html) paketi ile gelir ve hem istemci hem sunucu olarak aşağıdaki işlemleri gerçekleştirebiliriz.

İndirmek için [command-not-found](https://command-not-found.com/sctp_test) adresine bakabilirsiniz.

Kurulum yaparak:

```shell
apt-get install -y lksctp-tools
```

## sctp\_status Uygulamasıyla Sunucu & İstemci

<!-- ![](https://miro.medium.com/v2/resize:fit:504/1*KY6c1USvy6h4oTJ3-HuiSQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_KY6c1USvy6h4oTJ3-HuiSQ.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*jKRa-g7fxWF9pKCcBkc-eA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_jKRa-g7fxWF9pKCcBkc-eA.webp)

## Sunucu

<!-- ![](https://miro.medium.com/v2/resize:fit:482/1*7aGbSC8tf_0qk1nO2ZTuDg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_7aGbSC8tf_0qk1nO2ZTuDg.webp)

```shell
ubuntu@cem:~$ sctp_status -H 0.0.0.0 -P 30000 -l &
[1] 33801
ubuntu@cem:~$
Starting tests...

ubuntu@cem:~$ tail /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
       0        0 2   10  46   30000  1000 150332 0.0.0.0
```

## İstemci Olarak sctp\_status

```shell
ubuntu@cem:~$ sctp_status -H 0.0.0.0 -P 31000 -h 0.0.0.0 -p 30000 -s
```

Eğer SCTP bağlantısını olmayan bir uzak sunucuya yapıyorsa:

<!-- ![](https://miro.medium.com/v2/resize:fit:695/1*qRfBtV-aVaVnRZg6sRAiNg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_qRfBtV-aVaVnRZg6sRAiNg.webp)

## İstemci Olarak NCat

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*cH-4-syFDdMxrRoM5aQ_9A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_cH-4-syFDdMxrRoM5aQ_9A.webp)

```shell
# --sctp    : SCTP Protokolüyle
# -4        : IPv4 ile
# -v        : Verbose çıktı
# localhost : "localhost" Hedef sunucusuna bağlan
# 30000     : 30000 Hedef portundan bağlan
ubuntu@cem:~$ ncat --sctp -4 -v localhost 30000
```

## NCat Uygulamasıyla Sunucu & İstemci

İlk deneme ekran çıktısı:

<!-- ![](https://miro.medium.com/v2/resize:fit:629/1*eR_CYYWB_ZazdMt0Oc35cA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_eR_CYYWB_ZazdMt0Oc35cA.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*nX7ePbeBJK_A5wQW_ysO7g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_nX7ePbeBJK_A5wQW_ysO7g.webp)

## Sunucu

```shell
# --sctp    : SCTP Protokolüyle
# -4        : IPv4 ile
# -k        : Bir istemci bağlandıktan sonra sunucuyu kapatma, yeni bağlantılara açık ol
# -v        : Verbose çıktı
# -l        : Dinleyici (sunucu) olarak çalış
# localhost : "localhost" Hedef sunucusuna bağlan
# 30000     : 30000 Hedef portundan bağlan
ubuntu@cem:~$ ncat --sctp -kv -4 -l 40000 &
[1] 34368
ubuntu@cem:~$ Ncat: Version 7.80 ( https://nmap.org/ncat )
Ncat: Listening on 0.0.0.0:40000

ubuntu@cem:~$ tail /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
       0        0 2   10  62   40000  1000 160390 0.0.0.0
```

<!-- ![](https://miro.medium.com/v2/resize:fit:499/1*vXtUy-imQbV5WhmrLAr40w.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_vXtUy-imQbV5WhmrLAr40w.webp)

## İstemci

```shell
$ ncat --sctp -4 -v localhost 40000
```

## Docker Alternatifler

1.  Ev sahibi bilgisayarda SCTP protokolü herhangi bir porttan dinlenmiyor
2.  Konteynerin 202 portunda SCTP protokolüyle ev sahibinin 200 portuna bağlanıyor ( -p 200:202 )
3.  Konteyner içinde 202 portundan SCTP başlatılıyor ve konteyner içinde IPv4 0.0.0.0 adreslerini dinlediği görülüyor
4.  Ev sahibi bilgisayarın 200 portunda tüm ağ kartlarının IPv4 ve IPv6 adreslerinde SCTP başlatıldığı görülür
5.  Netstat ile ev sahibi sunucuda dinlemenin (LISTEN) başlatıldığı görülüyor
6.  Ev sahibi sunucudan -> konteynere SCTP bağlantısı kuruluyor
7.  İstemciden bağlantı kapatılınca konteyner sunucu kapanır
8.  Ev sahibi bilgisayardan hem cat hem netstat ile bakılınca SCTP dinlenen bir portun olmadığı görülüyor

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*o8baPLqxjJxClBZJCEgeEQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_o8baPLqxjJxClBZJCEgeEQ.webp)

```shell
ubuntu@cem:~$ cat /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
ubuntu@cem:~$
ubuntu@cem:~$ docker run -d -p 200:202/sctp sctp_image sctp_status -H 0.0.0.0 -P 202 -l
819592aa86d63a0183e8de2d5f16c5be885b2df0c3b4eb0a29eef771c4fa5018
ubuntu@cem:~$ docker exec -it 81 bash
root@819592aa86d6:/# cat /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
       0        0 2   10  22   202       0 302792 0.0.0.0
root@819592aa86d6:/# ubuntu@cem:~$ docker exec -it 81 bash
Error response from daemon: container 819592aa86d63a0183e8de2d5f16c5be885b2df0c3b4eb0a29eef771c4fa5018 is not running
ubuntu@cem:~$
ubuntu@cem:~$ cat /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
       0        0 2   10  6    200       0 305563 0000:0000:0000:0000:0000:0000:0000:0000
       0        0 2   10  6    200       0 304618 0.0.0.0
ubuntu@cem:~$
ubuntu@cem:~$ netstat -lS
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
sctp                [::]:200                                        LISTEN
sctp                0.0.0.0:200                                     LISTEN
ubuntu@cem:~$
ubuntu@cem:~$ sudo sctp_status -H 0.0.0.0 -P 210 -h 127.0.0.1 -p 200 -s

Starting tests...
     Client: Sending packets.(1/10)
NO. ASSOC-ID STATE             RWND     UNACKDATA PENDDATA INSTRMS OUTSTRMS FRAG-POINT SPINFO-STATE SPINFO-CWDN SPINFO-SRTT SPINFO-RTO SPINFO-MTU
1   33       ESTABLISHED       106496   0         0        65535   10       65484      ACTIVE       131064      0           3000       65532
Client: Sending packets.(2/10)
Client: Sending packets.(3/10)
Client: Sending packets.(4/10)
Client: Sending packets.(5/10)
2   33       ESTABLISHED       45213    2         0        65535   10       65484      PF           65532       6           2000       65532
Client: Sending packets.(6/10)
Client: Sending packets.(7/10)
Client: Sending packets.(8/10)

                *** sendmsg: No such process ***

ubuntu@cem:~$
ubuntu@cem:~$ tail /proc/net/sctp/eps
 ENDPT     SOCK   STY SST HBKT LPORT   UID INODE LADDRS
ubuntu@cem:~$
ubuntu@cem:~$ netstat -lS
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
ubuntu@cem:~$
```

Docker alternatif 1:

```shell
evsahibi~$ docker run -it pawanwavenet/lksctp-tools-image bashroot:konteyner~
$ sctp_test -H 0.0.0.0 -P 30100 -l
```

Docker alternatif 2:

```shell
evsahibi~$ docker run -it registry.ulakhaberlesme.com.tr/3rdparty/sctp_test:xenial bash
root:konteyner~$ sctp_test -H 0.0.0.0 -P 30100 -l
```

```shell
ubuntu@cem:~$ docker run -d -p 20000:20200/sctp registry.ulakhaberlesme.com.tr/3rdparty/sctp_test:xenial sctp_status -H 0.0.0.0 -P 20200 -l
```

## Kubernetes İle

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*TArs-tfX_rzrJLZLidIh6g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_TArs-tfX_rzrJLZLidIh6g.webp)

Kubernetes alternatif:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sctp-pod
spec:
  containers:
    - name: sctp-pod
      command:
        - bash
        - -c
        - "sctp_test -H 0.0.0.0 -P 30100 -l"
      image: registry.ulakhaberlesme.com.tr/3rdparty/sctp_test:xenial
#      image: sctp-image
#      image: pawanwavenet/lksctp-tools-image
      ports:
        - containerPort: 30100
          name: sctpserver
          protocol: SCTP
---
apiVersion: v1
kind: Service
metadata:
  name: sctp-service
spec:
  selector:
    name: sctp-pod
  ports:
    - protocol: SCTP
      port: 31100
      targetPort: 30100
      nodePort: 32100  # Düğümdeki istediğiniz port numarası
  type: NodePort

# port:
#   Bu, servisin içinde çalıştığı pod içinde kullanılan port numarasıdır.
#   Yani, podun içindeki uygulamanın ulaşılabilir olduğu porttur.
#   Bu örnekte, pod içindeki uygulamanın 30100 portunu kullandığını belirtiyor.
# targetPort:
#   Bu, servisin trafik yönlendirmesinde hedef olarak kullanılan pod içindeki port numarasıdır.
#   Yani, servise gelen trafiğin hangi portta çalışan uygulamaya yönlendirileceğini belirtir.
#   Bu örnekte, podun içindeki uygulamanın 30100 portunu hedef alır.
# nodePort:
#   Bu, düğümün dışındaki trafiğin erişebileceği port numarasıdır.
#   NodePort türü bir serviste, düğüme gelen trafiğin hangi port numarasında düğümün dışına açılacağını belirtir.
#   Yani, düğümün IP adresi ve bu port numarası kullanılarak servise erişilebilir.
#   Bu örnekte, düğümdeki istenen port numarasını belirtir ve düğümün dışından erişilebilecek portu tanımlar.
```

## sctp\_test Komut Satırı Örnekleri

Komut satırında hem SUNUCU hem İSTEMCİ oluşturabilir, aralarında SCTP paketleri akışı sağlayabilirsiniz.

```shell
# SUNUCU ayaklandırmak için
sctp_test -H local_addr -P local_port -l

# İSTEMCİ yaratmak için
sctp_test -H local_addr -P local_port -h remote_addr -p remote-port -s

# Eğer sctp_test ile olmazsa sctp_darn kullanılabilir.
sctp_darn -H local_addr -P local_port -h remote_addr -p remote-port -s
```