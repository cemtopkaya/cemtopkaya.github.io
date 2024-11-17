---
layout: post
title:  "Netplan Notlarım"
date:   2024-08-24 11:57:24 +0000
categories: cloud-init openstack ubuntu
tags: cloud-init openstack ubuntu
---
Önce Linux ağ yapılandırmanın geçmişi:

Linux’ta ağ yönetimi, başlangıçta elle yapılandırma dosyaları ve temel komut satırı araçlarıyla yapılırken, zamanla daha otomatik, esnek ve kullanıcı dostu araçlara geçiş yapmıştır. Bu evrim, ağ yapılandırmasının daha kolay yönetilebilir hale gelmesi ve karmaşık ağ ortamlarında daha iyi bir performans sunması amacıyla gerçekleşmiştir.

Linux işletim sistemlerinde ağ yönetimi, yıllar içinde büyük bir evrim geçirdi. İşte geçmişten günümüze Linux’ta ağ yönetimi için kullanılan başlıca yöntemler, hizmetler ve araçlar:

## 1\. Elle Ağ Yapılandırması (1990'lar ve Öncesi)

-   Yapılandırma Dosyaları: Erken dönemlerde, ağ yönetimi çoğunlukla elle düzenlenen yapılandırma dosyaları aracılığıyla yapılırdı. Örneğin:
-   `/etc/hosts`: İsim çözümleme için kullanılırdı.
-   `/etc/resolv.conf`: DNS sunucularını belirtmek için kullanılırdı.
-   `/etc/network/interfaces` (Debian tabanlı sistemler): Ağ arayüzlerinin yapılandırılması için kullanılırdı.
-   ifconfig: Ağ arayüzlerini yapılandırmak ve yönetmek için kullanılan temel araç. Ağ arayüzlerini etkinleştirmek, devre dışı bırakmak, IP adreslerini ayarlamak için kullanılırdı.
-   route: Yönlendirme tablolarını yönetmek için kullanılırdı.

[Linux dağıtımları ve ağ yapılandırma dosyaları](http://redmine/projects/cinar_devops/wiki/A%C4%9F_Y%C3%B6netimi#A%C4%9F-Yap%C4%B1land%C4%B1rma-Dosyalar%C4%B1-ve-%C4%B0lgili-Linux-Da%C4%9F%C4%B1t%C4%B1mlar%C4%B1)

## 2\. Static Networking (Statik Ağ Yapılandırması)

-   Yapılandırma Dosyaları: `ifconfig` ve `route` gibi araçlar ile birlikte statik ağ yapılandırması yapılırdı.
-   `/etc/network/interfaces` ve `/etc/sysconfig/network-scripts/` gibi dosyalar, ağ arayüzlerinin statik yapılandırması için düzenlenirdi.

## 3\. Dynamic Host Configuration Protocol (DHCP) (1990'lar)

-   dhclient: Linux sistemlerine IP adreslerini dinamik olarak atamak için kullanılan DHCP istemcisi.
-   Pump: Eski bir DHCP istemcisi, Red Hat tabanlı dağıtımlarda kullanılırdı.
-   dhcpcd: DHCP istemcisi olarak yaygınca kullanılmıştır.

## 4\. Modern Ağ Yapılandırma Araçları (2000'ler)

-   iproute2 (ip): `ifconfig` ve `route` gibi eski araçların yerini almak üzere geliştirilen daha gelişmiş bir araç seti. Bu araçlar, ağ yapılandırması, yönlendirme, tünelleme gibi çok daha geniş bir fonksiyon yelpazesini yönetebilir.
-   iptables: Ağ üzerinden gelen ve giden trafiği kontrol etmek için kullanılan bir paket filtreleme aracı. Güvenlik duvarı yönetimi için kullanılır.
-   brctl: Bridge ağ yapılandırması için kullanılan araç.

## 5\. [Networkmanager](http://redmine/projects/cinar_devops/wiki/Networkmanager) (2004 — Günümüz)

-   NetworkManager: Ağ bağlantılarını otomatikleştirmek ve yönetmek için kullanılan bir hizmet. Hem masaüstü ortamlarında hem de sunucularda yaygın olarak kullanılır.
-   nmcli: NetworkManager’ın komut satırı arayüzü.
-   nmtui: Metin tabanlı kullanıcı arayüzü.
-   GNOME/KDE Network Settings: Grafik tabanlı masaüstü ortamları için kullanılan NetworkManager arayüzleri.

## 6\. [systemd-networkd](http://redmine/projects/cinar_devops/wiki/Systemd-networkd?parent=A%C4%9F_Y%C3%B6netimi) (2010'lar — Günümüz)

-   systemd-networkd: `systemd` ile entegre bir ağ yönetim hizmeti. Ağ yapılandırmasını merkezi bir şekilde yönetir ve `networkctl` aracı ile kontrol edilir.
-   networkctl: `systemd-networkd` tarafından yönetilen ağ yapılandırmalarını görmek ve yönetmek için kullanılan komut satırı aracı.

## 7\. Netplan (Ubuntu 17.10 ve Sonrası)

-   Netplan: Ubuntu gibi dağıtımlarda ağ yapılandırmalarını daha kolay ve esnek hale getirmek için kullanılan bir araçtır. `yaml` formatında yapılandırma dosyaları kullanır ve bu yapılandırmalar, `NetworkManager` veya `systemd-networkd` gibi arka plan hizmetlerine uygulanır.

## 8\. cloud-init (Bulut Ortamları)

-   cloud-init: Bulut ortamlarında çalışan sanal makinelerin ilk yapılandırmalarını yapmak için kullanılan bir araç. Ağ yapılandırması da dahil olmak üzere birçok sistem yapılandırmasını yönetir.

## Ağ Yapılandırma Dosyaları ve İlgili Linux Dağıtımları

Alpine:

```
/etc/network/interfaces/etc/resolv.conf
```

Arch:

```
/etc/systemd/network/05-eth0.network
```

CentOS, CentOS Stream, AlmaLinux, and Rocky Linux:

```
/etc/sysconfig/network-scripts/ifcfg-eth0
```

Debian:

```
/etc/network/interfaces/etc/resolv.conf
```

Fedora:

```
/etc/sysconfig/network-scripts/ifcfg-eth0
```

Gentoo:

```
/etc/conf.d/net/etc/resolv.conf
```

openSUSE Leap:

```
/etc/sysconfig/network/ifcfg-eth0/etc/sysconfig/network/routes/etc/resolv.conf
```

Slackware:

```
/etc/rc.d/rc.inet1.conf/etc/resolv.conf
```

Ubuntu 16.04 LTS (and earlier):

```
/etc/network/interfaces/etc/resolv.conf
```

Ubuntu 18.04 LTS (and later):

```
/etc/systemd/network/05-eth0.network
```

## systemd-networkd

`systemd-networkd` aracı, `systemd`'nin bir parçası olarak geliştirilen daha yeni bir araçtır ( `systemd-networkd.service` ). Ubuntu'nun Arch ve modern sürümleri (17.10 ve üzeri) şu anda varsayılan ağ yapılandırma yazılımı olarak `systemd-networkd`'yi kullanıyor.

Servisi yönetmek için `sudo systemctl restart systemd-networkd` gibi komut satırını kullanabilirsiniz.

Tıpkı Networkmanager gibi systemd-networkd de ayar dosyasına sahip `/etc/systemd/networkd.conf`.

`/etc/systemd/networkd.conf` dosyasındaki genel yapılandırma dosyası yalnızca bazı varsayılanları geçersiz kılmak için kullanılabilir. Ana yapılandırma ağ cihazı başına gerçekleştirilir. Yapılandırma dosyaları `/usr/lib/systemd/network/`, geçici çalışma zamanı ağ dizini `/run/systemd/network/` ve yerel yönetim ağı dizini `/etc/systemd/network/` konumunda bulunur. `/etc/systemd/network/` içindeki dosyalar en yüksek önceliğe sahiptir.

Ubuntu ayrıca, `systemd-networkd` veya `NetworkManager` 'ı yapılandırmak için ön uç görevi gören Netplan adlı yardımcı programa da sahiptir.

## Yapılandırma dosyaları

`systemd-networkd` için ağ yapılandırma dosyalarına ilişkin ayrıntılar, varsayılan yapılandırma dosyası konumu vs:

-   Dosya uzantısı: `.network`
-   Dosya konumu: `/etc/systemd/network/`
-   \*\*Adlandırma kuralı: \*_\[öncelik\] dosyaları sıralamak için kullanılır (dosyalar alfa-sayısal olarak işlenir_) ve \[ _arayüz\]_ kullanıcının bir dosyayı belirli bir arayüzle ilişkilendirmesi için kullanışlı bir yol sağlar.`[priority]-[interface].network`
-   Varsayılan yapılandırma dosyası: .`/etc/systemd/network/05-eth0.network`

\* `_systemd-networkd_` _ağ arayüzlerini açtığında, yapılandırma dosyaları alfanümerik olarak işlenir. Bu nedenle, dosyaların genellikle sıralanmasına yardımcı olmak için 2 basamaklı bir sayıyla ön eklendiğini ve önceliğin bu iki sayıyla verildiğini göreceksiniz. Farklı bir arayüz için bir yapılandırma dosyası oluşturmak isteseydik, altına (önce işlenecek) veya üstüne (sonra işlenecek) bir sayı ekleyebilirdik ._

## Başlangıç yapılandırması

İşte systemd-networkd için tipik bir yapılandırma dosyasının örneği. IPv4 adresini statik olarak tanımlar ve SLAAC’ın IPv6 adresini yapılandırmasına izin verir.

`/etc/systemd/network/05-eth0.network`

```ini
[Match]
Name=eth0
```

```ini
[Network]
DHCP=no
DNS=203.0.113.1 203.0.113.2 203.0.113.3
Domains=ip.linodeusercontent.com
IPv6PrivacyExtensions=falseGateway=192.0.2.1
Address=192.0.2.123/24
```

-   [Name](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#Name=): Bir VLAN kullanırken, genel internet arayüzü farklı şekilde yapılandırılabilir.`eth0`
-   [DHCP](https://www.freedesktop.org/software/systemd/man/systemd.network.html#DHCP=) :DHCP’yi devre dışı bırakan ve sonraki alanlarda ana IPv4 adresini statik olarak tanımlamanıza olanak sağlayan.`no`
-   [DNS](https://www.freedesktop.org/software/systemd/man/systemd.network.html#DNS=) : Bu örnekte verilen IP adresleri yer tutuculardır ve işlev görmezler.
-   [Domains](https://www.freedesktop.org/software/systemd/man/latest/systemd.network.html#Domains=) :, “arama alanı” olarak tanımlanır. Bu, tek etiketli ana bilgisayar adlarını FQDN’lere dönüştürmenin hızlı bir yoludur, ancak sıklıkla gerekli değildir.
-   [IPv6PrivacyExtensions](https://www.freedesktop.org/software/systemd/man/systemd.network.html#IPv6PrivacyExtensions=):, gizlilik uzantılarını devre dışı bırakır ve IPv6 SLAAC adresinizi otomatik olarak yapılandırmayla ilgili sorunları çözmenize yardımcı olur.`false`
-   [Ağ Geçidi (Gateway)](https://www.freedesktop.org/software/systemd/man/systemd.network.html#Gateway=) : IPv4 ağ geçidi adresini statik olarak yapılandırır.
-   [Adres](https://www.freedesktop.org/software/systemd/man/systemd.network.html#Gateway=) (Address) : IPv4 adresini statik olarak yapılandırır.

## Statik ağ yapılandırması

`enp2s0`, ağ arayüzüne statik IP ataması yapalım

```ini
[Match]
Name=enp2s0
```

```ini
[Network]
Address=192.168.0.15/24
Gateway=192.168.0.1
DNS=1.2.3.4
```

Ayarları `/etc/systemd/network/` dosyasına yazarak servisi tekrar başlatalım:

```shell
sudo systemctl restart systemd-networkd
```

## Birincil IPv4 adresini değiştirme

Sistemde yapılandırılan IPv4 adresini değiştirmek için ve parametrelerini yeni IP adresi ve buna karşılık gelen ağ geçidi IP adresiyle eşleşecek şekilde ayarlayın.

`/etc/systemd/network/05-eth0.network`

```ini
...
Gateway=192.0.2.1
Address=192.0.2.123/24
```

## Birincil IPv4 adresinin DHCP aracılığıyla yapılandırılması

DHCP, birincil IPv4 adresinizi otomatik olarak yapılandırmak için kullanılabilir. Birincil IPv4 adresi, sayısal olarak sıralandığında ilk sırada yer alan sisteminize atanmış IPv4 adresi olarak tanımlanır. DHCP’yi etkinleştirmek için, parametreyi olarak ayarlayın ve birincil IPv4 adresinin ve’sini tanımlayan satırları kaldırın (veya yorum satırı yapın) .

/etc/systemd/network/05-eth0.network

```ini
...
[Network]
DHCP=yes
...
# Gateway=192.0.2.1
# Address=192.0.2.123/24
```

## DNS çözücülerini değiştirme

DNS çözücüler, etki alanı adlarını karşılık gelen IPv4 adreslerine çözen varlıklardır.

```
...ini
DNS=203.0.113.1 203.0.113.2 203.0.113.3
```

Yukarıdaki örnekte, sağlanan IP adreslerini kullanmak istediğiniz DNS çözücülerinin IP adresleriyle değiştirin. Hem IPv4 hem de IPv6 adresleri birlikte kullanılabilir.

## IPv6'nın Devre Dışı Bırakılması

IPv6, çekirdek komut satırına `ipv6.disable=1` eklenerek önyükleme sırasında genel olarak devre dışı bırakılabilirken, `networkd`, IPv6'nın arayüz bazında devre dışı bırakılmasını destekler. Bir ağ ünitesinin `[Network]` bölümünde `LinkLocalAddressing=ipv4` veya `LinkLocalAddressing=no` varsa, `networkd` eşleşen arayüzlerde IPv6'yı yapılandırmaya çalışmaz.

> _Bununla birlikte, yukarıdaki seçeneği kullanırken bile, IPv6 genel olarak devre dışı bırakılmamışsa networkd’nin yine de yönlendirici reklamları almayı bekleyeceğini unutmayın. IPv6 trafiği arayüz tarafından alınmıyorsa (örneğin,_ `_sysctl_` _veya_ `_ip6tables_` _ayarları nedeniyle), yapılandırma durumunda kalacak ve ağın tamamen yapılandırılmasını bekleyen hizmetler için potansiyel olarak zaman aşımlarına neden olacaktır. Bunu önlemek için,_ `_[Network]_` _bölümünde_ `_IPv6AcceptRA=no_` _seçeneği de ayarlanmalıdır._

Bu nedenle, bir ağ birimi dosyasının `[Network]` bölümü, eşleşen arayüzlerde IPv6'yı devre dışı bırakmak için aşağıdakileri içermelidir.

```ini
[Network]
LinkLocalAddressing=no
IPv6AcceptRA=no
```

## Statik Rotalar

Bir `systemd` ağ biriminin `[Route]` bölümünde statik rotaları belirtin. Bu örnekte, `10-static.network` adında bir birim dosyası oluşturuyoruz ve bu dosyada `172.16.0.0/24` alt ağına giden statik bir rota tanımlıyoruz:

```ini
[Route]
Gateway=192.168.122.1
Destination=172.16.0.0/24
```

Bir arayüzde birden fazla IP adresini yapılandırmak için ağ ünitesinde birden fazla Adres anahtarı tanımlarız. Aşağıdaki örnekte `20-multi_ip.network` dosyasında ayrıca her IP adresi için farklı bir ağ geçidi tanımladık.

```ini
[Match]
Name=eth0
```

```ini
[Network]
DNS=8.8.8.8
Address=10.0.0.101/24
Gateway=10.0.0.1
Address=10.0.1.101/24
Gateway=10.0.1.1
```

## networkd Hata Ayıklama

`networkd` ile ilgili bazı sorunlarla karşılaştıysanız aşağıdaki talimatları izleyerek hata ayıklama modunu etkinleştirebilirsiniz.

```shell
mkdir -p /etc/systemd/system/systemd-networkd.service.d/
```

Aşağıdaki içeriğe sahip bir `/etc/systemd/system/systemd-networkd.service.d/10-debug.conf` oluşturun:

```shell
[Service]
Environment=SYSTEMD_LOG_LEVEL=debug
```

Ve systemd-networkd hizmetini yeniden başlatın:

```shell
systemctl daemon-reload
systemctl restart systemd-networkd
journalctl -b -u systemd-networkd
```

## systemd-networkd Hizmetinin Günlükleri

`journald`'ın `systemd-networkd.service`'e özel günlük girişleri daha sonra aşağıdakilerle izlenebilir:

```shell
journalctl --unit=systemd-networkd.service --follow
```

```shell
# veya kısaca
journalctl -u systemd-networkd -f
```

`[service-log-level](https://www.freedesktop.org/software/systemd/man/systemctl.html#service-log-level%20SERVICE%20%5BLEVEL%5D)` kullanarak hata ayıklama günlüğünü etkinleştirebilirsiniz.

```
# syntax:
# systemctl service-log-level SERVICE [LEVEL]
# LEVEL:
# a value in the range 0…7
# or one of the strings emerg, alert, crit, err, warning, notice, info, debug
```

```
# systemd-networkd.service'in geçerli günlük düzeyini "debug" olarak ayarlayın:
systemctl service-log-level systemd-networkd.service debug
```

Bir seviye belirtmeden systemd-networkd.service’in mevcut günlük seviyesinin ne olduğunu kontrol edelim:

```shell
systemctl service-log-level systemd-networkd.service
```

_Şimdi Netplan sırası, Netplan nedir?_

Netplan , ağ yapılandırmalarını daha kolay ve daha açıklayıcı hale getirmek için tasarlanmış bir yardımcı programdır . Gerekli ağ arayüzlerinin ve her birinin ne yapacak şekilde yapılandırılması gerektiğinin bir YAML açıklamasını oluşturmanız yeterlidir. Bu açıklamadan Netplan, seçtiğiniz oluşturucu aracı için gerekli tüm konfigürasyonu oluşturacaktır. Ubuntu 18.04 (ve daha yenisi) üzerinde çalışır ve systemd-networkd ve NetworkManager ‘daki alt düzey yapılandırmaları soyutlayarak çalışır. İstediğiniz ağ kurulumunu açıklayan bir YAML dosyası oluşturun ve Netplan bunu gerçekleştirmek için gerekli arka uç yapılandırmalarını uygular.

_Nasıl çalışır?_

Netplan yapılandırma dosyaları YAML biçimini kullanır. Netplan, `/{lib,etc,run}/netplan/*.yaml` dosyasından ağ yapılandırmasını okur.

`/run/netplan` içindeki bir dosya, `/etc/netplan` içindeki aynı ada sahip bir dosyayı tamamen gölgeler ve bu dizinlerin herhangi birindeki bir dosya, `/lib/netplan` içindeki aynı ada sahip bir dosyayı tamamen gölgeler.

Netplan Linux’ta ağ oluşturmak için iki render kullanır:

-   NetworkManager
-   Systemd-networkd  
    servisi. Hangi iki render’ı kullanacağınızı seçmelisiniz. Netplan’ın size sunduğu özellikler çoktur. Tüm özelliklerini görmek için bu bağlantıya bakın .

Varsayılan olarak Ağ Yardımcısı aracı, Ubuntu’daki ağı, Ubuntu’nun Netplan yönetim aracı yerine doğrudan systemd-networkd kullanarak yönetir. Netplan’ı kullanmaya başlamak için öncelikle varsayılan systemd-networkd yapılandırma dosyasını kaldırmalısınız.

## Yapılandırma dosyaları

Aşağıdaki ayrıntılar Netplan’ın yapılandırma dosyalarının nerede ve nasıl çalıştığını göstermektedir:

-   Dosya uzantısı :`.yaml`
-   Dosya konumu :`/etc/netplan/`
-   Adlandırma kuralı : _\[öncelik\]_ iki basamaklı bir sayı ( aracılığıyla ) olduğunda dosya sıralamasını tanımlar (alfa-sayısal sırayla işlenir) ve _\[ad\]_ kısa, açıklayıcı bir başlık olduğunda`[priority]-[name].yaml``01``99`
-   Varsayılan yapılandırma dosyası :`/etc/netplan/01-netcfg.yaml`

## Başlangıç yapılandırması

Netplan’ın yapılandırma dosyalarının nasıl çalıştığına dair bir fikir edinmek için, işte bir başlangıç yapılandırma dosyası. Dosyanın bir dökümü, her bir parçanın rolünü ayrıntılı olarak açıklayarak aşağıdadır.

/etc/netplan/01-netcfg.yaml

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
      accept-ra: yes
      ipv6-privacy: no
```

network: version: 2 renderer: networkd ethernets: eth0: dhcp4: yes accept-ra: yes ipv6-privacy: no

-   `version`: Yapılandırma biçimini belirtir. Şu anda desteklenen tek seçenek .`2`
-   `renderer`: Hangi temel ağ yapılandırma aracının kullanılacağını tanımlar, ya da . Varsayılan değer .`networkd``NetworkManager``networkd`
-   `ethernets`: Fiziksel ağ arayüzlerini yapılandırır. Daha fazla ayrıntı için resmi belgelerdeki [Aygıt türleri için ilişkili Özellikler bölümünü inceleyin.](https://netplan.readthedocs.io/en/stable/netplan-yaml/#properties-for-device-type-ethernets)

Varsayılan yapılandırmada, birincil Ethernet arabirimi için bir yapılandırma eşlemesi sunar. Bu durumda ayarlanan tek seçenek, DHCP ( ) kullanılması gerektiğini belirtir ve dinamik IP adresi atamasını etkinleştirir.`eth0``dhcp4`

Netplan’in YAML yapılandırma seçeneklerinin tam kapsamı hakkında daha fazla bilgi edinmek için [resmi belgelere](https://netplan.readthedocs.io/en/stable/netplan-yaml/) bakın

## IP adreslerini elle yapılandırma

systemd-networkd yapılandırma dosyalarını kaldırıp netplan’ın kullanacağı \*.yaml dosyalarını oluşturmamız gerekiyor.

## Birincil IPv4 adresini değiştirme

Netplan’da IP adresi yapılandırması arayüzün altındaki seçeneği kullanır. Bu nedenle, birincil IPv4 adresini statik bir IP adresine değiştirmek için aşağıdaki yaklaşımı kullanabilirsiniz:`addresses``eth0`

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      addresses:
        - [ip-address]/[prefix]
      routes:
        - to: default
          via: [gateway-ip]
```

Her giriş, alt ağ öneki uzunluğuyla birlikte bir IP adresi alır. Ek olarak, ağ geçidine bir rota da eklemeniz gerekir.

-   \[ip-address\] : Statik olarak yapılandırılacak IP adresi.
-   \[prefix\] : Adres için alt ağ öneki. Bu, eklediğiniz IPv4 adresinin türüne bağlıdır:
-   \[gateway-ip\] : Birincil IPv4 adresine karşılık gelen ağ geçidinin IPv4 adresi.

## Birincil IPv4 adresinin DHCP aracılığıyla yapılandırılması

DHCP ile IPv4 adresi otomatik olarak yapılandırılır. Birincil IPv4 adresi, sisteminize atanan ilk IPv4 adresidir.

Varsayılan Netplan yapılandırma dosyası, bir arayüzde DHCP’nin nasıl etkinleştirileceğini gösterir. Seçeneği “,” değeriyle ekleyin ve yukarıdaki bölümde gösterilen gibi statik IP adreslerini tanımlayan tüm satırları kaldırın.

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      dhcp4: yes
```

[Edit this section](http://redmine/projects/cinar_devops/wiki/Netplan/edit?section=7)

## Ek IPv4 adreslerini yapılandırma

Netplan içerisinde ek IPv4 adreslerini listeye/diziye ekleyerek yapılandırabilirsiniz.

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      addresses:
        - 192.0.2.17/24
        - [ip-address]/[prefix]
      routes:
        - to: default
          via: 192.0.2.1
```

_\[ip-address\]’ni_ ek IPv4 adresiyle ve _\[prefix\]’i_ genel adresler veya özel adresler için değiştirin.

## Birincil IPv6 adresinin SLAAC aracılığıyla yapılandırılması

Birincil IPv6 adresiniz SLAAC aracılığıyla otomatik olarak yapılandırılabilir. Bunu yapmak için, Netplan yapılandırmanızın yönlendirici reklamlarına izin vermesi ve IPv6 gizlilik uzantılarını devre dışı bırakması gerekir.

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      accept-ra: yes
      ipv6-privacy: no
```

Tersine, IPv6 SLAAC adreslemesini devre dışı bırakabilir ve bunun yerine IPv6 adresinizi statik olarak yapılandırabilirsiniz. Bunun için yönlendirici reklamlarını devre dışı bırakın ve bir sonraki bölümde ayrıntılı olarak açıklandığı gibi birincil IPv6 adresinizi alt ağ önekiyle ekleyin.`/128`

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      accept-ra: no
      addresses:
        - 2001:db8:e001:1b8c::3/128
```

## Ek IPv6 adreslerini yapılandırma

Arayüzün altına girişler ekleyerek, IPv4 adreslerine benzer şekilde ek IPv6 adresleri yapılandırabilirsiniz . Tek temel fark, IPv6 adreslerinin (ve ilişkili öneklerinin) tırnak işaretleri içine alınması gerektiğidir. Ayrıca, tüm IPv6 adresleri için varsayılan ağ geçidi olmalıdır.

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      addresses:
        - "[ip-address]/[prefix]"
     routes:
      - to: default
        via: "fe80::1"
```

Her giriş iki bölümden oluşur: _IP adresi_ ve _alt ağ öneki_.

-   \[ip-adresi\] : Statik olarak yapılandırılacak IP adresi. Adres, yukarıda gösterildiği gibi IPv6 (örneğin, ) veya IPv4 olabilir `2001:db8:e001:1b8c::2`
-   \[prefix\] : Adres için alt ağ öneki. Bu, eklediğiniz IPv6 adresinin türüne bağlıdır:  
    — IPv6 SLAAC adresi: (önceki bölümde gösterildiği gibi, bunu SLAAC aracılığıyla otomatik olarak yapılandırmanız önerilir) `/128`  
    — Bir aralıktaki IPv6 adresi: veya (aralığın boyutuna bağlı olarak) `/64` `/56`

## DNS çözücülerini değiştirme

DNS çözücüler, etki alanı adlarının karşılık gelen IP adresleriyle eşleştirilmesini sağlar.

Netplan içindeki DNS çözücülerini seçeneğini kullanarak değiştirebilirsiniz. Hem IPv4 hem de IPv6 adresleri DNS çözücü olarak kullanılabilir.

Aşağıdaki yapılandırma örneği, DHCP’yi korurken özel DNS çözücülerini tanımlamak istiyorsanız gerekli olan ek seçenekleri içerir. Seçenek DHCP dinamik IP adresi atamasını etkinleştirirken, alttaki **DHCP’nin özel DNS çözücülerinizi geçersiz kılmamasını** sağlar.

/etc/netplan/01-netcfg.yaml

```yaml
...
  ethernets:
    eth0:
      dhcp4: yes
      dhcp4-overrides:
        use-dns: no
      nameservers:
        addresses:
          - 203.0.113.1
          - 203.0.113.2
          - 203.0.113.3
```