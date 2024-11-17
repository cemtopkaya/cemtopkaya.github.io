---
layout: post
title:  "Android Telefonunuza SSH ile Bağlanmak"
date:   2024-07-05 11:57:24 +0000
categories: Android
tags: android networking usb-tethering
---
Takip ettiğim [adres bu](https://gist.github.com/raveenb/ab3217798c827be889b83b584d70b08b).

[termux](https://d-03.winudf.com/b/APK/Y29tLnRlcm11eF8xMThfYWU4YzBiMjQ?_fn=VGVybXV4XzAuMTE4LjBfQVBLUHVyZS5hcGs&_p=Y29tLnRlcm11eA%3D%3D&download_id=1654202027978014&is_hot=true&k=2fc3805c8d368d27ccc17c806275dc1566618f05) indirilir ve harici bir dosyanın USB üzerinden kurulabileceğine izin vererek scrcpy’nin dizininden `adb install ***.apx` komutuyla kurulum yapılır.

<!-- ![](https://miro.medium.com/v2/resize:fit:436/1*gwEKz4eyjFsbWx5PzICsRQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_gwEKz4eyjFsbWx5PzICsRQ.webp)


Bilgisayarınızdan -> cep telefonunuza IP ataması yapılabilmesi için “USB tethering” özelliğini açmalısınız.

<!-- ![](https://miro.medium.com/v2/resize:fit:341/1*0K1Prm6YdWuaQSBFywOIqw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_0K1Prm6YdWuaQSBFywOIqw.webp)

Termux uygulamasını bilgisayarınızdan -> cep telefonunuza kurmak için aşağıdaki komutu çalıştırmalısınız:

```shell
D:\_portables\scrcpy-win64-v2.4\scrcpy-win64-v2.4>adb install D:\cem\Downloads\Termux_0.118.0_APKPure.apk
Performing Streamed Install
Success
```

Termux Uygulamasını açarak kurulum için şu komutları çalıştıralım:

```shell
pkg install root-repo
```

<!-- ![](https://miro.medium.com/v2/resize:fit:509/1*iT-Wdg8PaaOGBB759yfvSQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_iT-Wdg8PaaOGBB759yfvSQ.webp)

Şimdi `pkg upgrade` komutunu çalıştıralım:

<!-- ![](https://miro.medium.com/v2/resize:fit:511/1*ZhDthrfsOAydbv6VR4UZEQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ZhDthrfsOAydbv6VR4UZEQ.webp)

Artık ssh server paketimizi `pkg install openssh` komutuyla kurabiliriz:

<!-- ![](https://miro.medium.com/v2/resize:fit:507/1*P0W3n5Og12VOkjwTO7YScg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_P0W3n5Og12VOkjwTO7YScg.webp)

`passwd` komutuyla aktif kullanıcı için bir şifre tayin edelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:510/1*BHI3y5o7v_pTbtxUJUnuKA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_BHI3y5o7v_pTbtxUJUnuKA.webp)

Artık telefonumuzu USB tethering ile bilgisayarımızdan cihaza bağlanacak bir TCP bağlantısı sağlayalım.

Termux ile telefonumuzdaki IP adreslerini listeleyelim ki cep telefonumuzun IP adresini öğrenebilelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*lccvJh0otwRNbunGen6nNQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_lccvJh0otwRNbunGen6nNQ.webp)

```shell
~ $ ip -br -c a | grep UP
rndis0           UP             192.168.195.167/24 fe80::547c:5ff:fea1:c91a/64
```

> Bilgisayarımızla cep telefonumuz arasındaki ağ kartının bilgilerini ipconfig ile görüntüleyebilir, cep telefonumuzdan gelen IP adresinin ağ geçidine (ki telefonumuz oluyor) de SSH bağlantısı kurabiliriz.

Artık `ssh 192.168.195.167 -p 8022` komutuyla SSH yapabiliriz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*3BaUM93_2tzR__CzeijMlQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_3BaUM93_2tzR__CzeijMlQ.webp)


## SOCKS ve VPN İle TCP Trafiğini Yönlendirelim

Telefonumuzdan Openvpn ile VPN yapalım:

Bu kısım telefonumuzda openvpn kullandığımız fasıl ve bağlantı kurunca aşağıdaki gibi ekran görüntüsü olacak

<!-- ![](https://miro.medium.com/v2/resize:fit:438/1*9yQHwjtUOORYekpZxGr3BQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_9yQHwjtUOORYekpZxGr3BQ.webp)

`tun` İle VPN bağlantımızın olduğunu görürüz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*CZvKTY40TgXHX0XXkxpp9A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_CZvKTY40TgXHX0XXkxpp9A.webp)

Artık SSH bağlantısını bilgisayarımızdan SOCKS protokülüyle verilerin gönderilebilmesi için 5000 portunu bağlayacak şekilde (`-D 5000`) kuralım ve bu tünelde bir trafik olursa konsolda görelim diye çıktıları görelim diye `-vvv` bayrağını kullanalım.

<!-- ![](https://miro.medium.com/v2/resize:fit:360/1*ilz4tzBws5imff-3wfPFeQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ilz4tzBws5imff-3wfPFeQ.webp)

ssh -D 5000 192.168.195.167 -p 8022 -vvv

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*YzxBplx8gvMeclSYvoOgMw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_YzxBplx8gvMeclSYvoOgMw.webp)


Firefox üzerinden SOCKS proxy için ayarları yaparak artık tüm HTTP/s trafiğimizi bu tünele yönlendirebiliriz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*YZAOX3-Tky9Ucizqh8eh5A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_YZAOX3-Tky9Ucizqh8eh5A.webp)

## İnternet Trafiğinizin İstediğiniz Kart Üzerinden Gitmesini Sağlamak

Önce internete kablosuz ile bağlandım (kütüphanenin interneti)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*_-KvldntTqHPlwDu-OcyZg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1__-KvldntTqHPlwDu-OcyZg.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:206/1*bA_0IH7ZaL-vTtZ7Twkw4Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_bA_0IH7ZaL-vTtZ7Twkw4Q.webp)

Sistem çubuğunda varsayılan ağ kartımız wifi olarak gösteriliyor

Rotalara bakınca `0.0.0.0` (yani herhangi bir adrese) `172.27.38.119` IP adresli ağ kartımdan gittiğini gördüm:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*RuGN7CYqWZ0zqpClbHDiVQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_RuGN7CYqWZ0zqpClbHDiVQ.webp)

```shell
route PRINT
```

Bu da kablosuz ağımın bilgileri:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*HwANOBrEpy62Ljs-7ux9xg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_HwANOBrEpy62Ljs-7ux9xg.webp)

ipconfig

## Cep telefonumu USB Tethering ile bilgisayarıma bağladım

Sonra cep telefonumu USB Tethering ile bilgisayarıma bağladım ve `ipconfig` ile telefonumdan bilgisayara verilen ağ bilgilerini gördüm:

<!-- ![](https://miro.medium.com/v2/resize:fit:680/1*SHxzkf5VouLUZV8uKwVp5g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_SHxzkf5VouLUZV8uKwVp5g.webp)


Şimdi `route PRINT` ile rotalara tekrar bakalım:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*LaZiqxsyW9z_9cXU-z7C9g.png) -->
![]({{ BASE_PATH }}/assets/images/1_LaZiqxsyW9z_9cXU-z7C9g.webp)


Artık iki tane internet trafiği için varsayılan rota tanımlı. Ancak öncelikli tercih edilecek ağ geçidim metrik değeri 25 ile cep telefonum seçilmiş.

Sistem çubuğunda Ethernet 5'in varsayılan olduğunu görebiliyoruz:

<!-- ![](https://miro.medium.com/v2/resize:fit:298/1*IAn7dGpkR9JWLLwddFc_fw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_IAn7dGpkR9JWLLwddFc_fw.webp)

Varsayılan olarak kablosuz ağım seçilsin diye 192.168.183.16 ağ kartımın 25 olan metrik değerini 172.27.38.119 ağ kartımın 80 olan metrik değerinden daha büyük yapacağım ki öncelikli internet trafiği wifi’den geçsin ve cep telefonumla kurduğu ağ kartından gitmesin.

Bunun için önce 192.168.83.16'li ağ kartına giden trafiği siliyor sonra tekrar daha büyük bir metrikle yaratıyoruz:

```shell
route delete 0.0.0.0 mask 0.0.0.0 192.168.83.112
 OK!

route add 0.0.0.0 mask 0.0.0.0 192.168.83.112 metric 120
 OK!
```

<!-- ![](https://miro.medium.com/v2/resize:fit:593/1*e9bn3UwFtR0Y40hUNF4TqQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_e9bn3UwFtR0Y40hUNF4TqQ.webp)

Rotaları `route PRINT` komutuyla tekrar görelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*5ODOQ-qEk05qUCoRkRAj7Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_5ODOQ-qEk05qUCoRkRAj7Q.webp)


İki metrik değerini de değiştirdi ama istediğimiz oldu. Artık öncelikli ağ geçidi wifi oldu.

Windows çubuğunda varsayılan geçidin Wi-Fi olduğunu ikondan da görebiliyoruz:

<!-- ![](https://miro.medium.com/v2/resize:fit:307/1*HkYETIltNrdpqINLYy5y-g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_HkYETIltNrdpqINLYy5y-g.webp)

## Telefon Üzerinden Bilgisayar İnternet Trafiğini Geçirmeyip HTTP trafiği için SOCKS5 ve SSH bağlantıları için Jumpnode Kullanmak

Şimdi durumu tekrar görelim. Bilgisayarıma cep telefonumdan ağ paylaştım ve rotalar şöyle gözüküyor (`route PRINT`):

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*u4VfTAxpewoP5VQWt1P2YA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_u4VfTAxpewoP5VQWt1P2YA.webp)


Cep telefonumdan gelen IP bloğu `192.168.126.\*` ve bu ağ kartına genel trafik gitmesin diye silelim:

```shell
route delete 0.0.0.0 mask 0.0.0.0 192.168.126.173
 OK!
```

Şimdi trafik asla cep üzerinden gitmeyecek (`route PRINT`):

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*r5JO0o9ktxG2wSuPXGE-vw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_r5JO0o9ktxG2wSuPXGE-vw.webp)


Şimdi cep telefonumuzda Termux uygulamasında `sshd` komutunu çalıştırıp kendi bilgisayarımızdan ssh ile cep telefonumuza bağlanalım:

```shell
ssh 192.168.126.173 -p 8022
```

<!-- ![ssh 192.168.126.173 -p 8022](https://miro.medium.com/v2/resize:fit:700/1*rhzPKMkHUVD-ZH8JEDJpcw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_rhzPKMkHUVD-ZH8JEDJpcw.webp)


Şimdi cep telefonumuzdan VPN yaptığımız için şirket bilgisayarına SSH ile bilgisayarımızdan bağlanmak isteyelim. Bunun için bağlantımızı `-D 5000` ile `socks5 proxy` (`TCP`, `UDP` destekler) tünel açacak şekilde tekrar kuralım:

```shell
ssh -D 5000 192.168.126.173 -p 8022
```

> Dilerseniz `-vvv` anahtarıyla paket akışlarında konsolda çıktı alabilirsiniz

Firefox ayarını tıpkı yukarıdaki gibi 5000 portuna SOCK protokolü ile yönlendirin ve web gezgininizi kullanın.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*I3S8EnL6YbQw71NiEriI2g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_I3S8EnL6YbQw71NiEriI2g.webp)

Eğer alan adı çözümlemesi yapamazsa diye IP bilgisiyle sunuculara giriş yapabilirsiniz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*S6K659SF7wOfiP40x8JCdw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_S6K659SF7wOfiP40x8JCdw.webp)

SSH Bağlantıları için cep telefonunuza JumpNode olarak bağlanıp uzak telefonunuzla bağlandığınız ağların arkasındaki sunuculara erişin:

`ssh -J cem.topkaya@192.168.126.173:8022 ubuntu@192.168.13.47 -p 22`

Önce cep telefonunuza bağlanmak için şifre girip sonra 13.47 makinesinin şifresini girmiş olursunuz.