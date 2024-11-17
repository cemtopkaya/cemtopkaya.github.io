---
layout: post
title:  "Kubernetes Kurulumu"
date:   2024-04-09 11:57:24 +0000
categories: Kubernetes
tags: Kubernetes
---

*   Hostname ve Machine ID Bilgilerini Güncelleyin;
*   Kubernetes paket depolarını ekleyin ve kurun;
*   IPv4 iletmeyi ve iptables’ı yapılandırma;
*   containerd İle Konteynerleştirme Motorunu Kuralım;
*   Disabling swap;
*   Initializing the Kubernetes cluster;
*   Configuring Kubectl and Calico;
*   Adding worker nodes.

Hostname ve Machine ID Bilgilerini Güncelleyin
==============================================

Üzerine Kubernetes kurduğunuz makinelerin “sanal makineler” olma halinde, klonlama veya OVA aracılığıyla oluşturduysanız, makinenin _benzersiz bir makine kimliğine_ sahip olduğundan emin olmanız gerekir:

```shell
sudo rm -rf /etc/machine-id  
sudo dbus-uuidgen --ensure=/etc/machine-id
```

Her ana bilgisayar adını (master, wrk-0x gibi) aşağıdaki komutla yapılandırın:

```shell
sudo hostnamectl set-hostname {{HOST\_NAME}}.kubernetes.lab
```

Tüm sunucuların `/etc/hosts` dosyasında birbirlerine isimlerinden erişebilmeleri için aşağıdaki gibi uygun IP adresiyle güncellemelerini yapın:

```shell
sudo cat <<EOF>> /etc/hosts   
10.30.10.3 master.kubernetes.lab   
10.30.10.4 wrk-01.kubernetes.lab  
10.30.10.5 wrk-02.kubernetes.lab  
10.30.10.6 wrk-03.kubernetes.lab  
EOF
```

Sunucuları Tekrar Başlatın
--------------------------

`sudo init 6` komutu, Linux sistemlerinde yeniden başlatma işlemini başlatır. Bu komut, sistemde çalışan tüm işlemleri durdurur, ardından sistem yeniden başlatılır. `init 6` komutu, `/etc/inittab` dosyasında tanımlı olan "runlevel 6" yani "yeniden başlatma" işlemini başlatır.

Ancak, modern Linux dağıtımlarında genellikle `shutdown -r now` veya `reboot` gibi daha basit ve doğrudan komutlar kullanılarak sistem yeniden başlatılır. Bu komutlar, daha kolay anlaşılır ve kullanımı daha yaygın olan alternatiflerdir.

```shell
reboot
```

Kubernetes paket depolarını ekleyin ve kurun
============================================

```shell
sudo apt-get install -y apt-transport-https ca-certificates curl  
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add  
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" >> ~/kubernetes.list  
sudo mv ~/kubernetes.list /etc/apt/sources.list.d  
sudo apt-get update
```

Kubernetes v1.26'yı hedefleyen aşağıdaki paketleri her makineye yükleyin:

*   **kubeadm:** Kubernetes kümesini önyüklemek için kullanacağımız komut aracı.
*   **kubelet:** kümenizdeki tüm makinelerde çalışan, bölmelerin ve kapsayıcıların başlatılmasından sorumlu olan bileşen.
*   **kubectl:** kümenizle etkileşime geçmek için kullanılan komut satırı aracı.

```shell
export VERSION = "1.26.0-00"  
sudo apt-get install -y kubelet=$VERSION kubeadm=$VERSION kubectl=$VERSION kubernetes-cni  
sudo apt-mark hold kubelet kubeadm kubectl
```

IPv4 iletmeyi ve iptables’ı yapılandırma
========================================

Önce ne yaptığımızı sonra neden yaptığımızı inceleyelim:

```shell
sudo tee /etc/sysctl.d/kubernetes.conf << EOF  
net.bridge.bridge-nf-call-ip6tables = 1  
net.bridge.bridge-nf-call-iptables = 1  
net.ipv4.ip\_forward = 1  
EOF
```

Etkinleştirmek için yukarıdaki ayarları `sudo sysctl --system` komutunu çalıştırırız

Kubernetes’in gereksinim duyduğu ve kullanımı yaygın olan Linux çekirdeği modüllerini yüklemek zamanı. İlk olarak, `cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf` komutu, k8s.conf adında bir dosya oluşturur veya varsa üzerine yazar ve ardından bu dosyaya Kubernetes'in çalışması için gerekli olan `overlay` ve `brfilter` iki modülünün adını yazar.

*   **overlay:** Overlay dosya sistemi, Kubernetes Pod’larının çalıştığı özel bir sanal dosya sistemidir. _OverlayFS (Overlay File System)_, birden fazla dosya sistemi katmanını tek bir dosya sistemi olarak birleştiren bir dosya sistemi teknolojisidir. Bu, Linux’ta mevcut olan diğer dosya sistemlerinin (genellikle ext4 veya XFS gibi) üzerine yerleştirilmiş bir katman olarak çalışır. OverlayFS, dosyaların ve dizinlerin birleştirilmiş bir görüntüsünü oluşturur ve bu görüntüyü kullanarak verilerin okunması ve yazılması sağlanır.

Kubernetes tarafından kullanıldığında, OverlayFS, konteynerlerin dosya sistemlerini oluşturmak ve yönetmek için kullanılır. Her bir Kubernetes Pod’u, bir veya daha fazla konteyneri içeren ve kendi dosya sistemini oluşturan bir sanal ortam sağlar. OverlayFS, bu Pod’ların dosya sistem… ve bu temel katmana üzerinde yapılan değişikliklerin katmanlarından oluşur.

*   **br\_netfilter:** Bu modül, Linux kernel’inin ağ katmanında bulunan ve köprü cihazlarının (bridge device) veri paketlerini işlerken kullanılır. Linux köprüsü üzerindeki ağ trafiğini _filtrelemek_ ve _işlemek_ için gereken özellikleri sağlar.

**Linux köprüsü**, farklı ağ arabirimlerini (network interfaces) birbirine bağlamak ve yönlendirmek için kullanılan **bir ağ katmanı aygıtıdır**:

*   ağ trafiğini alır
*   gelen paketleri hedefine yönlendirir
*   ve aynı ağdaki diğer cihazlara iletir.

`br_netfilter` **modülü**, köprü cihazı üzerindeki ağ trafiğini filtrelemek için kullanılan iptables tabanlı bir güvenlik duvarı sistemini etkinleştirir. Bu sayede, köprü üzerinden geçen paketlerin yönlendirilme ve filtreleme kuralları uygulanabilir. Örneğin, gelen paketlerin belirli bir kaynaktan veya belirli bir hedefe gitmesini engelleme veya izin verme kuralları oluşturulabilir.

Bu modül aynı zamanda Köprü Etki Alanı İletimini (Bridge Netfilter) destekler. Bu özellik, bridge cihazlarının, üzerinden geçen ağ trafiğini filtrelemek ve yönlendirmek için güvenlik duvarı kurallarını etkinleştirmesine olanak tanır. Bu, köprü cihazının ağ trafiğini daha etkili bir şekilde kontrol etmesini sağlar ve ağ güvenliğini artırır.

1.  `**net.bridge.bridge-nf-call-ip6tables = 1**` **ve** `**net.bridge.bridge-nf-call-iptables = 1**`**:** Bu ayarlar, IPv6 (ip6tables) ve IPv4 (iptables) için köprü modülüne paket işleme çağrılarının yapılmasını etkinleştirir. Linux kernel'in ağ paketlerini işlemek için köprü modülüne (bridge module) yönlendirmesine izin verir. Kubernetes ve diğer konteyner orkestrasyon sistemleri, ağ trafiklerini yönlendirmek ve ağ kurallarını uygulamak için bu modülü kullanır. İptables ve ip6tables, bu köprü modülü aracılığıyla ağ trafiğini yönlendirir ve filtreler. Bu ayarlar, köprü modülünün paketleri işlemesi için bu tabloları çağırmasını sağlar.
2.  `**net.ipv4.ip_forward = 1**`**:** Bu ayar, Linux kernel'in paketleri doğrudan bir ağ arabiriminden diğerine yönlendirmesine izin verir. Konteynerler arasında iletişim kurulması için ve ağ trafiğinin dış dünyaya iletilmesi için gereklidir. Özellikle Kubernetes gibi konteyner orkestrasyon sistemleri, bu özelliği kullanarak farklı ağlar arasında trafiği yönlendirir ve iletişim kurar. Bu, içeride çalışan konteynerlerin dış dünyayla iletişim kurmasını sağlar.

Ardından, `sudo modprobe overlay` ve `sudo modprobe br_netfilter` komutları, bu modülleri anında yükler. modprobe komutu, Linux çekirdeğine bir modül yüklemek için kullanılır. Bu komutlar, çekirdek modüllerini yükleyerek Kubernetes'in gereksinimlerini karşılar ve sistemde düzgün çalışmasını sağlar.

containerd İle Konteyner Motorunu Kuralım
=========================================

Konteyner işlemlerini yapabilmek için Docker’ın alt uygulamlarından `containerd` kuracağız. **Containerd**, ilk olarak Docker tarafından geliştirilmiş bir konteyner çalıştırma motorudur. Ancak Docker, Containerd'i `Docker 1.11` sürümünden itibaren ayrı bir bileşen olarak çıkardı ve onu bağımsız bir proje haline getirdi. Containerd'in geliştirilmesi ve bakımı artık Docker'dan ziyade, Containerd topluluğu ve diğer katkıda bulunan şirketler ve bireyler tarafından yürütülmektedir.

Containerd, Open Container Initiative (OCI) tarafından tanımlanan standartlara uygun bir şekilde konteyner çalıştırma işlevselliğini sağlayan bir runtime olarak hizmet vermektedir. Kubernetes gibi birçok konteyner orkestrasyon sistemi, Containerd’i kullanarak konteynerleri yönetir ve çalıştırır.

```shell
sudo apt-get update  
sudo apt-get install \\  
    ca-certificates \\  
    curl \\  
    gnupg \\  
    lsb-release -y  
sudo mkdir -p /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
echo \\  
  "deb \[arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg\] https://download.docker.com/linux/ubuntu \\  
  $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Takas Alanını Kapatıyoruz
=========================

> _İşletim sistemi sistemlerinin RAM’miş gibi kullanmaya çalıştığı sabit sürücülerdeki takas alanını biliyor olabilirsiniz. İşletim sistemleri, daha acil görevler için RAM’de yer açmak amacıyla daha az erişilen verileri takas alanına taşımaya çalışır. Ancak takastaki verilere erişim, RAM’deki verilere erişimden çok daha yavaştır çünkü sabit sürücüler RAM’den daha yavaştır. Kubernetes, mevcut kaynakların anlaşılmasına dayalı olarak çalışmayı planlar. İş yükleri takas kullanmaya başlarsa Kubernetes’in doğru planlama kararları alması zorlaşabilir. Bu nedenle Kubernetes’i kurmadan önce takasın devre dışı bırakılması önerilir._

```shell
\# /etc/fstab Dosyasını değiştirmezsek takas ayarının devre dışı bırakılması her yeniden başlatmanın ardından sıfırlanacaktır!  
sudo sed -ri '/\\sswap\\s/s/^#?/#/' /etc/fstab  
sudo swapoff -a
```

Kaynaklar
=========…
