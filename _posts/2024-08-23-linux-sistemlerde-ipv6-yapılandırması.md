---
layout: post
title:  "Linux Sistemlerde IPv6 Yapılandırması"
date:   2024-08-23 11:57:24 +0000
categories: linux networking
tags: linux networking sysctl ipv6
---
## sysctl.conf

`sysctl.conf` dosyası, Linux ve Unix benzeri işletim sistemlerinde çekirdek parametrelerini yapılandırmak için kullanılan bir konfigürasyon dosyasıdır. Bu dosya genellikle `/etc/sysctl.conf` konumunda bulunur ve sistem yöneticilerine çekirdeğin çalışma zamanı davranışını değiştirme imkanı sağlar.

sysctl.conf dosyası içinde çeşitli sistem ayarları bulunabilir, örneğin:

1.  Ağ ayarları
2.  Bellek yönetimi
3.  Dosya sistemi parametreleri
4.  Güvenlik ayarları

IPv6 ile ilgili olarak, `sysctl.conf` dosyasında şu tür satırlar bulunabilir:

1.  `net.ipv6.conf.all.disable_ipv6 = 0`  
    Bu satır, tüm ağ arayüzlerinde IPv6'yı etkinleştirir (0) veya devre dışı bırakır (1).
2.  `net.ipv6.conf.default.forwarding = 0`  
    Bu, varsayılan olarak IPv6 paket yönlendirmesini kontrol eder. 1 olarak ayarlanırsa, sistem bir yönlendirici gibi davranır.
3.  `net.ipv6.conf.all.accept_ra = 1`  
    Bu ayar, Yönlendirici Duyurularını ( _Router Advertisements_ RA ) kabul edip etmeyeceğini belirler.
4.  `net.ipv6.conf.default.accept_ra_defrtr = 1`  
    Bu, varsayılan yönlendirici olarak RA ile alınan yönlendiricinin kabul edilip edilmeyeceğini kontrol eder.
5.  `net.ipv6.conf.all.autoconf = 1`  
    Bu ayar, durum bilgisiz adres otomatik yapılandırmasını (SLAAC) etkinleştirir veya devre dışı bırakır.

Bu ayarlar, IPv6 ağ davranışını ve güvenliğini önemli ölçüde etkiler. Sistem yöneticileri, ağ gereksinimlerine ve güvenlik politikalarına göre bu parametreleri dikkatle ayarlamalıdır.

## Statik IPv6 Atamalarında sysctl.conf’un Alabileceği Değerler

Statik IPv6 atamaları için tipik bir `/etc/sysctl.conf` konfigürasyonu:

```conf
# IPv6 ayarları: IPv6'yı etkinleştiriyoruz.
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

```conf
# Router Advertisement (RA) ayarları: Router Advertisement'ları (RA) kabul etmiyoruz.
net.ipv6.conf.all.accept_ra = 0
net.ipv6.conf.default.accept_ra = 0# Adres otomatik yapılandırması: Otomatik adres yapılandırmasını devre dışı bırakıyoruz.
net.ipv6.conf.all.autoconf = 0
net.ipv6.conf.default.autoconf = 0# Varsayılan yönlendirici kabulü: RA'lardan gelen varsayılan yönlendirici bilgisini kabul etmiyoruz.
net.ipv6.conf.all.accept_ra_defrtr = 0
net.ipv6.conf.default.accept_ra_defrtr = 0# Paket yönlendirme (gerekirse): IPv6 paket yönlendirmesini devre dışı bırakıyoruz (sunucu bir yönlendirici değilse).
net.ipv6.conf.all.forwarding = 0
net.ipv6.conf.default.forwarding = 0
```

Router Advertisement’ın (RA) IP atamasındaki etkisine gelince:

1.  Statik IP ataması: RA’nın doğrudan bir etkisi yoktur. IP adresi manuel olarak atanır ve RA’lar bu atamayı etkilemez.
2.  Otomatik IP ataması: Normal şartlarda, RA’lar otomatik IP ataması için çok önemlidir:

-   SLAAC (Stateless Address Autoconfiguration): RA’lar, ağ önekini ve diğer yapılandırma bilgilerini sağlar.
-   DHCPv6: RA’lar, sistemin DHCPv6 kullanması gerektiğini belirtir.

Ancak, yukarıdaki konfigürasyonda:

-   `accept_ra = 0` ayarı, sistemin RA'ları tamamen yok saymasını sağlar.
-   `autoconf = 0` ayarı, otomatik adres yapılandırmasını devre dışı bırakır.

Bu ayarlarla, sistem RA’ları dikkate almaz ve otomatik IP ataması yapmaz. Bu, statik IP ataması için idealdir çünkü:

1.  IP adresi değişmez.
2.  Ağdaki potansiyel kötü niyetli RA’lara karşı koruma sağlar.
3.  Ağ yapılandırmasının tam kontrolünü sağlar.

Ancak, bu ayarların bazı dezavantajları da olabilir:

-   Ağ yapılandırması değişirse manuel müdahale gerektirir.
-   Ağ yönetimini daha karmaşık hale getirebilir.

Bu nedenle, statik IP kullanırken bile bazı durumlarda `accept_ra = 2` ayarı tercih edilebilir. Bu ayar, RA'ları kabul eder ama sadece ek bilgi için kullanır, otomatik adres yapılandırmasını etkilemez.

Sonuç olarak, statik IPv6 atamalarında RA’ların IP ataması üzerinde doğrudan bir etkisi yoktur, ancak ağ yapılandırmasının diğer yönlerini etkileyebilir. Ayarlarınızı, ağınızın ihtiyaçlarına ve güvenlik gereksinimlerinize göre dikkatle seçmelisiniz.

## IPv6'nın Faal Edilmesi

IPv6'nın faal olup olmadığını öğrenmek için `cat /proc/sys/net/ipv6/conf/all/disable_ipv6` komutunu çalıştırın. Çıktı "1" ise, IPv6 devre dışı bırakılmış demektir.

`/etc/sysctl.conf` Dosyasında aşağıdaki satırlar

```conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```

Bu dosyadaki bir değişimin etkili olması için aşağıdakilerden birini yapmanız gerekir:

-   Bilgisayarı yeniden başlatmak
-   `sudo /sbin/sysctl -p /etc/sysctl.conf` komutunu çalıştırmak
-   Kernel ayarlarını bilinçli olarak sizin aşağıki komutlardan birini kullanarak değiştirmeniz:
-   `sudo echo "0" > /proc/sys/net/ipv6/conf/all/disable_ipv6`
-   veya `sudo sysctl -w net.ipv6.conf.all.disable_ipv6=0`

Ayrıca sistemin özellikle IPv6'yı boot ederken devre dışı bırakmadığından emin olmak için GRUB (Grand Unified Bootloader) ayarlarına (`/etc/default/grub` dosyasında bulunur) bakmanız gerekir.

GRUB üzerinden IPv6'yı devre dışı bırakmak için, verdiğiniz GRUB konfigürasyon dosyasında `GRUB_CMDLINE_LINUX_DEFAULT` veya `GRUB_CMDLINE_LINUX` satırını düzenleyebilirsiniz:

```ini
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash ipv6.disable=1"
```

veya

```ini
GRUB_CMDLINE_LINUX="ipv6.disable=1"
```

GRUB konfigürasyonunu güncellemek için `sudo update-grub` komutunu çalıştırmalısınız.

## Statik Yapılandırma

DHCP üzerinden IP adresi almaması için `/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg` dosyasında aşağıdaki ayarın json/yaml formatında yapılması gerek:

```
network: {config: disabled}
```

yahut

```yaml
network:
  config: disabled
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*6pEuuWw3CW-vSczvg-TMbw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/disable-network-config.png)

Bu ayarın ardından IPv6 bilgilerini `/etc/network/interfaces.d/50-cloud-init.cfg` dosyasına aşağıdaki gibi girmemiz gerekecek ancak satır satır anlamaya çalışalım:

```shell
root@snap:/etc/network/interfaces.d
auto loiface lo inet loopback
```

-   `auto lo` satırı, sistem önyüklemesi sırasında loopback ağ arayüzünün otomatik olarak etkinleştirilmesini sağlar.  
    Loopback Arayüzü (lo): lo arayüzü inet (IPv4) protokolü üzerinden loopback olarak yapılandırılmıştır.
-   `iface lo inet loopback` satırı, sistemin loopback ağ arayüzünü IPv4 protokolü üzerinden loopback olarak yapılandırır.