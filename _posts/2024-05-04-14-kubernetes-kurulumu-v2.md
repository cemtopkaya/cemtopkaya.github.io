---
layout: post
title:  "Kubernetes Kurulumu v2"
date:   2024-04-14 11:57:24 +0000
categories: Kubernetes
tags: Kubernetes
---

Kubernetes’ın yapısı, master/control plane ve worker olmak üzere iki bileşenden oluşur.

Control Plane, _cluster yönetiminin yapıldığı yerdir,_ Kubernetes cluster içinde merkezi yönetim birimi olarak hizmet verir. Control Plane içeren sunucuyu `{hostname}-cp1` veya `{hostname}-master1` şeklinde isimlendirebilirsiniz.

![alt text]({{ BASE_PATH }}/assets/images/1_9srv5dc-RCCZ-7FFf2WzuQ.webp)

Aşağıdaki bileşenlerinden oluşur:

*   API Server : Kubernetes cluster’ındaki tüm bileşenlerin iletişim kurmasını sağlar. Kullanıcılar veya araçlar, API Server’a erişerek Kubernetes objelerini yönetebilir.
*   etcd : Control Plane bileşenlerinin durumunu ve yapılandırmalarını saklamak için kullanılan hafif, dağıtılmış bir anahtar-değer veritabanıdır. Tutarsızlıkları önlemek için kritik bir bileşendir.
*   Scheduler : Pod’ların hangi node üzerinde çalışacağını belirler. Yeni bir Pod oluşturulduğunda, Scheduler uygun bir node seçer.
*   Controller Manager : Node, replica, endpoints, service account gibi bileşenlerin durumlarını kontrol eder ve yönetir.

Worker node, _uygulamaların çalıştığı ve Kubernetes objelerinin gerçekten barındığı yerdir_. Node içeren sunucuyu `{hostname}-node1` şeklinde isimlendirebilirsiniz.

![alt text]({{ BASE_PATH }}/assets/images/1_jh1F3gBMouDuT5m2KDpIOA.png)

Aşağıdaki bileşenlerden oluşur:

*   kubelet,
*   kube-proxy
*   ve container runtime

> _Kubernetes, Container Runtime Interface (CRI) arayüzünü kullanarak farklı container runtime’ları destekler. Bu arayüz, Kubernetes’ın farklı container runtime’larıyla iletişim kurmasını sağlar ve Kubernetes’in birçok farklı container runtime’ı destekleyebilmesini sağlar._

*   Docker (Containerd) : En çok kullanılan container runtime’larından biridir. Docker, kapsayıcılı uygulamaların oluşturulması ve çalıştırılması için kullanılan bir çok araç ve kütüphane içerir.  
    Containerd, Docker’ın yönetim bileşenidir ve container runtime’ın yönetimini gerçekleştirir.
*   Containerd, bir kapsayıcı yönetim bileşeni ve Docker’ın yönetim bileşenidir. Containerd, kapsayıcıların oluşturulmasını, başlatılmasını, durdurulmasını ve yönetimini sağlar.
*   CRI-O, Kubernetes’in Container Runtime Interface (CRI) standardını uygulayan bir container runtime’dır. CRI-O, Kubernetes ile doğrudan entegre edilmiştir ve Kubernetes’in kapsayıcılarını yönetmesini sağlar.
*   runc, Open Container Initiative (OCI) standardını uygulayan bir container runtime’dır. runc, kapsayıcıların oluşturulmasını ve çalıştırılmasını sağlar.
*   Kata Containers, bir kapsayıcının içine sanallaştırılmış bir ortam sağlar. Kata Containers, kapsayıcıların yavaş başlatılmasını önlemek için kullanılabilir.
*   gVisor , bir kapsayıcının içinde bir sandbox sağlar. gVisor, kapsayıcıların güvenliğini sağlamak için kullanılabilir.
*   Nabla Containers , bir kapsayıcının içine sanallaştırılmış bir ortam sağlar. Nabla Containers, kapsayıcıların yavaş başlatılmasını önlemek için kullanılabilir.

Control Plane veya Worker modda çalışan tüm node’lar bir araya gelerek ise Kubernetes cluster’ı meydana getirirler.

Kubernetes Kümenizdeki Düğüm Sayısına Bağlı Olarak
==================================================

Tek Düğümlü Kubernetes Kümesi Kurmak
====================================

1.  Eğer “control plane” ile “worker nodes” bir küme oluşturmayacak, tek bir düğümde bu ikisini birlikte kullanacaksak sunucunun adlarında bir değişiklik yapmamıza gerek yok.
2.  Varsayılan olarak k8s kümeniz, güvenlik nedeniyle kontrol düzlemi düğümlerinde Pod’lar oluşturmaz. Ancak tek bir makineyle Kubernetes kümesi oluşturacaksanız yahut kontrol düzlemi düğümlerinde de Pod’lar oluşturmak istiyorsanız kontrol düzlemi kurulumunu tamamladığınızda şunu çalıştırın [\*](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#control-plane-node-isolation):

```
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

Çok Düğümlü Kubernetes Kümesi Kurmak
====================================

Her düğümün MAC adresinin ve urun\_uuid’nin benzersiz olduğunu doğrulamamız gerekiyor.

*   MAC adresleri için: `ip link` veya `ifconfig -a` komutlarını kullanarak
*   product\_uuid için: `sudo cat /sys/class/dmi/id/product_uuid` dosyasının içeriğine bakarak

1.  Küme içinde birden fazla düğümleriniz olacaksa aşağıdaki `hostname` değişimlerini yapmak, birbirlerine erişebilmeleri için `/etc/hosts` dosyasındaki ayarları yapmak yerinde olacak.
2.  Control Plane sunucusunu şöyle ayarlayalım:

*   `# Sunucuya "master-node" adını verelim sudo hostnamectl set-hostname master-node # Hangi IP adresleri üzerinden iletişim kuracaksa düğümler o ağ arayüzünün IP adresini görelim ip -br -c a`

1.  Worker Node sunucusunu şöyle ayarlayalım:

*   `# Sunucuya "workernode0" adını verelim sudo hostnamectl set-hostname workernode0 # Hangi IP adresleri üzerinden iletişim kuracaksa düğümler o ağ arayüzünün IP adresini görelim ip -br -c a`

1.  Düğümler birbirlerine ulaşsın diye bu ayarları `/etc/hosts` dosyasına aşağıdaki gibi adreslerini girelim:

*   `... 10.10.21.131 master-node 10.10.21.107 worker01 ...`

Control Plane/MASTER NODE KURULUMU
==================================

Sistemimizi güncelleyelim. `apt-transport-https` paketi, yalnızca HTTPS üzerinden erişilebilen depolardan yazılım paketleri yüklemek için, `curl` paketiyleyse komut satırından URL'ler aracılığıyla veri alışverişi yapmamızı, web sitelerinden veri indirmemizi, dosya transferi ve API çağrıları gibi işlemlerini yapabiliriz.

```
sudo apt-get update  
sudo apt-get upgrade -y  
sudo apt install apt-transport-https curl
```

containerd Kuralım
==================

Ubuntu 16.04 için [docker yükleme](https://docs.docker.com/engine/install/ubuntu/)

Diğer linux dağıtımları için [containerd yükleme](https://docs.docker.com/engine/install/ubuntu/)

> _Container runtime olarak Docker Engine,_ `_containerd_` _ve_ `_runc_`_'a bağlıdır ve bu bağımlılıkları tek bir pakette toplar:_ `_containerd.io_`

![alt text]({{ BASE_PATH }}/assets/images/1_mrxc_T9WmdwLXg55HpyIQw.webp)

/etc/apt/keyrings Dizini
------------------------

`/etc/apt/keyrings` dizinini oluşturur. İşlevi, Debian tabanlı Linux dağıtımlarında apt paket yöneticisine ait bir güvenlik önlemidir. Bu dizin, apt deposundan gelen paketlerin imzalarını doğrulamak için kullanılan gizli anahtar halkalarını içerir.

> _Debian 12 ve Ubuntu 22.04'ten daha eski sürümlerde,_ `_/etc/apt/keyrings_` _dizini varsayılan olarak mevcut değildir ve curl komutundan önce oluşturulması gerekir._

`install -m 0755 -d /etc/apt/keyrings` komutu 0755 izinlerine (rwxr-xr-x) sahip dizin yaratır. Bu dizinin sahibinin (genellikle root kullanıcısı) tam erişimi olduğunu, diğer kullanıcıların sadece dizine erişebileceğini ancak içeriğini değiştiremeyeceğini gösterir.

> `_install_` _komutu, dosya ve dizinleri kopyalamak için kullanılabilir ve ayrıca dosya izinlerini ayarlamak için de kullanılabilir._ `_mkdir_` _ile çalışma ortamınıza bağlı varsayılan izinlerle bir dizini oluşturursunuz. Bu, güvenlik gereksinimlerine uyum sağlamak için veya belirli bir kullanıcı veya grubun özel erişim gereksinimlerini karşılamak için faydalı olabilir._

```
sudo install -m 0755 -d /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
echo "deb \[arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg\] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  
  
sudo apt-get update  
sudo apt-get install containerd.io -y
```

**asc Dosyasıyla Alternatif Containerd Kurulumu**

```
install -m 0755 -d /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc  
chmod a+r /etc/apt/keyrings/docker.asc  
echo \\  
  "deb \[arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc\] https://download.docker.com/linux/ubuntu \\  
  $(. /etc/os-release && echo "$VERSION\_CODENAME") stable" | \\  
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  
  
apt-get update  
apt install containerd.io -y
```

containerd Ayarlarını Yapılandıralım

Hem konteyner çalışma zamanı (container runtime) hem de kubelet, Linux makinelerindeki cgroup’ların yönetimi için önemli olan “cgroup driver” adlı özelliğe sahiptirler. `kubeadm` tabanlı k8s kurulumlarda `kubelet` bir `systemd hizmeti` olarak yönetilir. Konteyner çalışma zamanları (containerd gibi), kubelet'in varsayılan `cgroupfs sürücüsü` yerine, kubeadm tabanlı kurulumlar için `systemd sürücüsünün` önerir.

> _Yani kubeadm tabanlı kubernetes kurulumlarında kubelet hizmetlerini yöneten systemd’nin, konteyener çalışma zamanlarının cgroup sürücüsü olarak da systemd kullanması önerilir._

`SystemdCgroup = true` satırı, Containerd container runtime'nın, `systemd` tarafından yönetilen `cgroups` (control groups) sistemi üzerinde çalıştığını belirtir. Bu, container runtime'ın systemd ile uyumlu olarak çalıştığını ve cgroups yönetimi için systemd'ye güvendiğini gösterir.

Control groups (cgroups), Linux işletim sisteminde, işlem gruplarını ve bu grupların sistem kaynaklarına (CPU, bellek, disk giriş/çıkışı vb.) erişimini yönetmek için kullanılan bir mekanizmadır. `systemd`, `cgroups`'ı işlem izolasyonunu ve kaynak yönetimini sağlamak için kullanır. Container runtime'lar da genellikle bu cgroups mekanizmasını kullanarak, konteynerlerin kaynak kullanımını sınırlamak ve izole etmek için systemd ile etkileşime geçer.

```
\# \`/etc/containerd/config.toml\` Dosyasını varsa silip, düzenleyelim:  
sudo rm -rf /etc/containerd/config.toml  
sudo mkdir -p /etc/containerd  
\# containerd'yi varsayılan ayarlarını dosyaya yazalım:  
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

Önce systemd’nin cGroup yöneteceğini ayar dosyamıza ekleyelim:

```
\# /etc/containerd/config.toml dosyasının sonuna \`SystemdCgroup = true\` satırını ekleyelim  
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```

Sonra kendi konteyner registry adresimizi de ekleyelim. Ancak dizilimi anlamaya çalışalım önce:

```
plugins."io.containerd.grpc.v1.cri".registry  
  ...  
  plugins."io.containerd.grpc.v1.cri".registry.auths  
    ...  
  plugins."io.containerd.grpc.v1.cri".registry.headers  
    ...  
  plugins."io.containerd.grpc.v1.cri".registry.configs  
    plugins."io.containerd.grpc.v1.cri".registry.configs."registry.ulakhaberlesme.com.tr".tls  
      insecure\_skip\_verify = true  
  plugins."io.containerd.grpc.v1.cri".registry.mirrors  
    plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.ulakhaberlesme.com.tr"  
      endpoint = \["http://registry.ulakhaberlesme.com.tr"\]  
...
```

Şimdi kabuk betikleriyle bu değişimi dosyalarımıza yansıtalım:

```
\# plugins."io.containerd.grpc.v1.cri".registry.mirrors gördüğümüz yeri aşağıdaki satırlarla değiştirelim:  
\# ------------------------------------------------------------------------------------------------  
\#   \[plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.ulakhaberlesme.com.tr"\]  
\#   endpoint = \["http:\\/\\/registry.ulakhaberlesme.com.tr"\]  
\# ------------------------------------------------------------------------------------------------  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.mirrors\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.mirrors\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.ulakhaberlesme.com.tr"\]\\n          endpoint = \["http:\\/\\/registry.ulakhaberlesme.com.tr"\]/igs' /etc/containerd/config.toml  
  
\# \[plugins."io.containerd.grpc.v1.cri".registry.configs\]   satırını da HTTPs bağlantısında SSL sertifikasının güvenilirliğini denemeden kabul etsin diye   
\# şu satırlarla değiştirelim  
\# ------------------------------------------------------------------------------------------------  
\#    plugins."io.containerd.grpc.v1.cri".registry.configs."registry.ulakhaberlesme.com.tr".tls  
\#      insecure\_skip\_verify = true  
\# ------------------------------------------------------------------------------------------------  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.configs\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.configs\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.configs."registry.ulakhaberlesme.com.tr".tls\]\\n          insecure\_skip\_verify = true/igs' /etc/containerd/config.toml  
        
sudo systemctl restart containerd
```

`config.toml` dosyasını bu kadar değiştirdikten sonra her şeyin doğru olduğundan emin olmak için containerd hizmetinin günlüklerine bakarak düzgün `config.toml` dosyasından kaynaklı bir hata olup olmadığını kontrol etmek için `sudo journalctl -f -u containerd` komutunu çalıştırıp çıktıyı görelim:

```
$ sudo journalctl -f -u containerd  
Apr 13 17:05:07 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:07.830864409+03:00" level=info msg="Start subscribing containerd event"  
Apr 13 17:05:07 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:07.830975037+03:00" level=info msg="Start recovering state"  
Apr 13 17:05:07 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:07.831530705+03:00" level=info msg=serving... address=/run/containerd/containerd.sock.ttrpc  
Apr 13 17:05:07 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:07.831671495+03:00" level=info msg=serving... address=/run/containerd/containerd.sock  
Apr 13 17:05:08 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:08.162110922+03:00" level=info msg="Start event monitor"  
Apr 13 17:05:08 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:08.162231217+03:00" level=info msg="Start snapshots syncer"  
Apr 13 17:05:08 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:08.162262601+03:00" level=info msg="Start cni network conf syncer for default"  
Apr 13 17:05:08 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:08.162281271+03:00" level=info msg="Start streaming server"  
Apr 13 17:05:08 master-cp1 containerd\[2661\]: time="2024-04-13T17:05:08.162434410+03:00" level=info msg="containerd successfully booted in 0.448518s"  
Apr 13 17:05:08 master-cp1 systemd\[1\]: Started containerd container runtime.
```

containerd kuruldu…

Kubernetes kurulumuna TAKAS ALANIN KAPATILMASIYLA devam edeceğiz
================================================================

```
\# --------------------- DISABLING SWAPOFF -------------------------------  
\# İşletim sistemi sistemlerinin RAM'miş gibi kullanmaya çalıştığı sabit sürücülerdeki takas alanını biliyor olabilirsiniz.  
\# İşletim sistemleri, daha acil görevler için RAM'de yer açmak amacıyla daha az erişilen verileri takas alanına taşımaya çalışır.  
\# Ancak takastaki verilere erişim, RAM'deki verilere erişimden çok daha yavaştır çünkü sabit sürücüler RAM'den daha yavaştır.  
\# Kubernetes, mevcut kaynakların anlaşılmasına dayalı olarak çalışmayı planlar.  
\# İş yükleri takas kullanmaya başlarsa Kubernetes'in doğru planlama kararları alması zorlaşabilir.  
\# Bu nedenle Kubernetes'i kurmadan önce takasın devre dışı bırakılması önerilir.  
sudo swapoff -a  
  
\# swapoff'un her yeniden başlatıldığında tekrar aktif olmaması için /etc/fstab'da aşağıdaki değişiklik yapılır  
sudo sed -i '/ swap / s/^\\(.\*\\)$/#\\1/g' /etc/fstab
```

overlay ve br\_netfilter Linux Çekirdek Modüllerini Yükleyelim
==============================================================

Kubernetes kontrol düzlemi üzerinde Containerd’yi kullanmak için gereklidir. Bu adımlar, Containerd’yi ve Kubernetes CRI (Container Runtime Interface) ile entegre etmek için gereken çekirdek modüllerini ve ayarları yüklemeyi içerir.

*   overlay: OverlayFS, birleştirme dosya sistemi olarak bilinen ve özellikle konteynerlerin dosya sistemleri için kullanılan bir dosya sistemi tipidir. OverlayFS, birden çok katmanı birleştirerek bir dosya sistemi görüntüsü oluşturur ve değişiklikleri geçici bir katmana kaydeder. Bu, özellikle konteyner teknolojilerinde kullanılan katmanlı dosya sistemleri için yaygın bir seçenektir.
*   br\_netfilter: Bu kernel modülü, Linux köprü ağları (bridge networks) için paket filtreleme işlevselliğini sağlar. Kubernetes ve diğer konteyner orkestrasyon platformlarında, bu modül genellikle ağ izolasyonu ve yönlendirme gibi ağ işlevlerini sağlamak için kullanılır. Bu modül, Kubernetes ağ modelinin doğru çalışması için gereklidir.

Bu komutlar, Kubernetes kontrol düzlemi üzerinde Containerd’yi kullanırken, Containerd’nin gereksinim duyduğu bu kernel modüllerini yüklemek için kullanılır. Bu modüllerin yüklenmesi, Kubernetes’in düzgün bir şekilde çalışması ve konteynerler arasındaki ağ trafiğinin doğru yönlendirilmesi için önemlidir.

`/etc/modules-load.d/containerd.conf` dosyasına `overlay` ve `br_netfilter` çekirdek modüllerini ekleyerek başlayın. Bu modüller, containerd'nin çalışması için gerekli olan modüllerdir.

Ancak bu modülleri her tekrar başladığında yüklemek için:

```
cat << EOL | sudo tee /etc/modules-load.d/containerd.conf  
overlay  
br\_netfilter  
EOL
```

`overlay` ve `br_netfilter` modüllerini Linux çekirdeğine işletim sistemini tekrar başlatmadan yüklemek için:

```
sudo modprobe overlay  
sudo modprobe br\_netfilter
```

Kubernetes CRI tarafından kullanılan ağ ayarlarını yapılandırmak için `/etc/sysctl.d/99-kubernetes-cri.conf` dosyasına aşağıdaki ağ ayarlarını ekleyin:

```
cat << EOL | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf  
net.bridge.bridge-nf-call-iptables  = 1  
net.bridge.bridge-nf-call-ip6tables = 1  
net.ipv4.ip\_forward                 = 1  
EOL
```

Yukarıdaki çekirdek modülleri ayarlarını sunucuyu yeniden başlatmadan etkinleştirmek için:

```
sudo sysctl --system
```

Kubernetes Kuralım
==================

Kubernetes’i bir Ubuntu sistemine kurmak, Kubernetes deposunun APT kaynakları listesine eklenmesini ve ilgili araçların kurulmasını içerir.

> _15 Ağustos 2023'te Kubernetes projesi,_ [_pkgs.k8s.io_](https://kubernetes.io/blog/2023/08/31/legacy-package-repository-deprecation/) _adresinde bulunan Debian ve RPM paketleri için topluluğa ait paket depolarının genel kullanılabilirliğini duyurdu. Google tarafından barındırılan eski kod depoları 4 Mart 2024'te kullanımdan kaldırıldı. Google tarafından barındırılan eski paket depolarından (_`_apt.kubernetes.io_` _ve_ `_yum.kubernetes.io_`_) Kubernetes paketlerinin yüklenmesi artık mümkün değildir._

Kubernetes standart olmayan bir depodan geldiğinden, yazılımın orijinal olduğundan emin olmak için imzalama anahtarını indirin.

*   Jammy için:

```
\# Yukarıda yaptığımız için tekrar yapmayabilirsiniz ama tekrar yapsanız da aynı dizini boş yaratmaz:   
sudo mkdir -p /etc/apt/keyrings     
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg   
sudo echo "deb \[signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg\] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list    
sudo apt update   
sudo apt install -y kubelet kubeadm kubectl
```

*   Xenial için:

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add   
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"   
sudo apt install -y kubeadm kubelet kubectl
```

> _Yeni sürümlerde güncelleme almasın diye kubernetes paketlerini askıya alıyoruz._ `_apt upgrade -y_` _ile kontrolsüz alınacak yeni değişimler sorun yaratabilir:_

```
sudo apt-mark hold kubelet kubeadm kubectl  
\# Kurulu versiyonları görüntüleyelim  
kubectl version  
kubeadm version  
kubelet --version
```

Artık kurulumunuz tamamlandı ancak kubelet hizmeti çalışmadığı için bir günlük kaydı oluşmadı:

![alt text]({{ BASE_PATH }}/assets/images/1_phSvUYkCDy9sQmF8veq78A.webp)

O halde kubelet hizmetini `sudo systemctl enable --now kubelet` komutuyla aktif edelim ve henüz `/var/lib/kubelet/config.yaml` ayar dosyası oluşmadığı için tekrar tekrar hizmetin başlayıp, hata aldığını görelim:

> _Kubelet artık birkaç saniyede bir yeniden başlatılıyor ve bir kilitlenme döngüsünde kubeadm’in kendisine ne yapması gerektiğini söylemesini bekliyor._

```
Apr 14 01:42:49 master-node systemd\[1\]: Started kubelet: The Kubernetes Node Agent.  
Apr 14 01:42:49 master-node kubelet\[2472\]: E0414 01:42:49.247778    2472 run.go:74\] "command failed" err="failed to load kubelet config file, path: /var/lib/kubelet/config.yaml, error: failed to load Kubelet config file /var/lib/kubelet/config.yaml, error failed to read kubelet config file \\"/var/lib/kubelet/config.yaml\\", error: open /var/lib/kubelet/config.yaml: no such file or directory"  
Apr 14 01:42:49 master-node systemd\[1\]: kubelet.service: Main process exited, code=exited, status=1/FAILURE  
Apr 14 01:42:49 master-node systemd\[1\]: kubelet.service: Failed with result 'exit-code'.
```

![alt text]({{ BASE_PATH }}/assets/images/1_UrLvcWVqI2usqLFetF6saQ.webp)

aktif kullanıcının `~/.kube` dizini olmadığı için `kubectl get all` komutu aşağıdaki gibi bir hata verecek:

```
ubuntu@cem:~$ kubectl get all  
E0413 10:54:47.026495   22501 memcache.go:265\] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp \[::1\]:8080: connect: connection refused  
E0413 10:54:47.027315   22501 memcache.go:265\] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp \[::1\]:8080: connect: connection refused  
E0413 10:54:47.030398   22501 memcache.go:265\] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp \[::1\]:8080: connect: connection refused  
E0413 10:54:47.031465   22501 memcache.go:265\] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp \[::1\]:8080: connect: connection refused  
E0413 10:54:47.033831   22501 memcache.go:265\] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp \[::1\]:8080: connect: connection refused  
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

Kontrol Düzlemi Düğümünü Başlatalım
-----------------------------------

Pod’larınızın birbirleriyle konuşabilmesi için her k8s kümesine yalnızca bir POD Ağı Tanımlamalayız ([CNI](https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy)) kurabilirsiniz.

[\*](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#pod-network) Pod ağınızın herhangi bir ana bilgisayar ağıyla çakışmamasına dikkat edin : herhangi bir çakışma olması durumunda sorun yaşamanız muhtemeldir. (Ağ eklentinizin tercih ettiği Pod ağı ile bazı ana makine ağlarınız arasında bir çakışma bulursanız, bunun yerine kullanılacak uygun bir CIDR bloğu düşünmeli, ardından bunu ağ eklentinizin YAML’sinin yerine ve onunla birlikte kullanmalısınız) `kubeadm init. --pod-network-cidr`

Önce kubernetes’i `10.244.0.0/16` ağ bloğunda başlatalım ve konsol çıktısını `--v=5` bayrağıyla en kalabalık (VERBOSE, DEBUG) seviyesinde görelim. Eğer hataları yok saymak istersek `--ignore-preflight-errors=all` bayrağını da ekleyebiliriz komuta (`sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --v=5`)

Tek bir kontrol düzlemi düğümüne ait API sunucusunun tanıtım adresini ayarlamak için `--apiserver-advertise-address` kullanılabilirken, tüm kontrol düzlemi düğümleri için _yüksek erişilebilirlik yapılabilen uç noktasını_ ayarlamak için `--control-plane-endpoint` kullanılabilir.

Kontrol düzlemi düğümümüzü `master-node` ismiyle tanıtıp örneğin `10.10.21.128` gibi bir adreste çalıştığı için `/etc/hosts` dosyasına bu kaydı girmiştik. Burada 10.10.21.128, bu düğümün IP adresidir ve küme uç noktası, bu IP ile eşleşen özel bir DNS adıdır (`master-node`). Bu, `--control-plane-endpoint=master-node` bayrağını kubeadm init'e aktarmanıza ve aynı DNS adını kubeadm join'e aktarmanıza olanak tanır. Daha sonra yüksek kullanılabilirlik (high availabilitiy) senaryosunda küme uç noktasını yük dengeleyicinizin adresini işaret edecek şekilde değiştirebilirsiniz.  
`--control-plane-endpoint`, IP adresleriyle eşleşebilen hem IP adreslerine hem de DNS adlarına izin verir.

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=master-node --v=5
```

Bu komutun çıktısını içinden cımbızlayarak inceleyelim:

```
...  
...pulling: registry.k8s.io/kube-apiserver:v1.28.8  
...pulling: registry.k8s.io/kube-controller-manager:v1.28.8  
...pulling: registry.k8s.io/kube-scheduler:v1.28.8  
...pulling: registry.k8s.io/kube-proxy:v1.28.8  
...pulling: registry.k8s.io/pause:3.9  
...pulling: registry.k8s.io/etcd:3.5.12-0  
...pulling: registry.k8s.io/coredns/coredns:v1.10.1  
...  
\[control-plane\] Using manifest folder "/etc/kubernetes/manifests"  
\[control-plane\] Creating static Pod manifest for "kube-apiserver"  
...  
\[control-plane\] Creating static Pod manifest for "kube-controller-manager"  
...  
\[control-plane\] Creating static Pod manifest for "kube-scheduler"  
...  
\[kubelet-start\] Starting the kubelet  
...  
\[bootstrap-token\] Using token: ig7iao.t1rm2exxox7u2zhm  
...  
...loading admin kubeconfig  
...copying the cluster from admin.conf to the bootstrap kubeconfig  
...  
...creating the RBAC rules for exposing the cluster-info ConfigMap in the kube-public namespace  
...\[kubelet-finalize\] Assuming that kubelet client certificate rotation is enabled: found "/var/lib/kubelet/pki/kubelet-client-current.pem"  
...\[kubelet-finalize\] Restarting the kubelet to enable client certificate rotation  
\[addons\] Applied essential addon: CoreDNS  
\[addons\] Applied essential addon: kube-proxy  
Your Kubernetes control-plane has initialized successfully!  
To start using your cluster, you need to run the following as a regular user:  
  mkdir -p $HOME/.kube  
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
  sudo chown $(id -u):$(id -g) $HOME/.kube/config  
Alternatively, if you are the root user, you can run:  
  export KUBECONFIG=/etc/kubernetes/admin.conf  
You should now deploy a pod network to the cluster.  
Run "kubectl apply -f \[podnetwork\].yaml" with one of the options listed at:  
  https://kubernetes.io/docs/concepts/cluster-administration/addons/  
Then you can join any number of worker nodes by running the following on each as root:  
kubeadm join 10.10.21.107:6443 --token ig7iao.t1rm2exxox7u2zhm \\  
        --discovery-token-ca-cert-hash sha256:59a21a956cef335ab92cbb812bc144d5a8a1d79e7c6f204e396c8981d48f2b70
```

Yukarıdaki çıktıya göre kubectl komutlarını çalıştırmak için şunları yapacağız:

*   Kök kullanıcısı (`root`) değilsek, kullanıcının konsolundan `kubectl` komutlarını çalıştırmak için ev dizinimiz içinde `.kube` dizini ve kubernetes kümesine nasıl bağlanacağıyla ilgili ayarları içeren dosyaları oluşturacağız:

```
mkdir -p $HOME/.kube  
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

*   Kök kullanıcısıysak:

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

Kurulum sonrası çıktıları bir görelim:

![alt text]({{ BASE_PATH }}/assets/images/1_F-YqQZr_2CoFSokON28emg.webp)

Pod’larınızın birbirleriyle iletişim kurabilmesi için Konteyner Ağ Arayüzü (CNI) tabanlı bir Pod ağ eklentisi dağıtmanız gerekir. Bir ağ kurulmadan CoreDNS başlatılmayacaktır. Varsayılan olarak kubeadm, kümenizi RBAC (rol tabanlı erişim kontrolü) kullanacak ve kullanımını zorunlu kılacak şekilde ayarlar. Pod ağ eklentinizin ve onu dağıtmak için kullandığınız tüm bildirimlerin RBAC’yi desteklediğinden emin olun.  
POD Ağlarını hangi CNI ile yöneteceksek (flannel, calico, cillium ve diğerleri) onu `kubectl apply -f <add-on.yaml>` komutuyla kurmamız gerekiyor.

Bir Pod ağı kurulmadan önce, `kubectl get pods --all-namespaces` çıktısında CoreDNS Pod'un çalıştMADIĞINI göreceksiniz.

![alt text]({{ BASE_PATH }}/assets/images/1_F-YqQZr_2CoFSokON28emg-1.webp)


Biz flannel kuralım:

```
kubectl --kubeconfig .kube/config apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

> _Kaldırmak için şunu yapmamız gerekirdi:_
> 
> `_kubectl --kubeconfig .kube/config delete -f_ [_https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml_](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)`
> 
> _Şimdi Flannel’ı kullanan NetworkPolicy’leri, ServiceAccount’ları ve ClusterRole’leri (bunlar, Flannel’ın kurulumunu sırasında oluşturulmuş olabilir) gibi kaynakları temizlemek için:_
> 
> `_kubectl --kubeconfig .kube/config delete -f_ [_https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml_](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml)`

POD Ağ eklentisinin kurulduğunu görelim:

```
ubuntu@master-node:~$ kubectl get pods --all-namespaces  
NAMESPACE      NAME                                  READY   STATUS    RESTARTS      AGE  
kube-flannel   kube-flannel-ds-b52kc                 1/1     Running   0             81s
```

Bir Pod ağı kurulduktan sonra, `kubectl get pods --all-namespaces` çıktısında CoreDNS Pod'un çalıştığını göreceksiniz.

![alt text]({{ BASE_PATH }}/assets/images/1_pytidcDN2NHLdFZl0AHrzQ.webp)

Esasen yukarıdaki çıktı işimizi görür ama biraz daha olayları kavramak istersek şu ekran çıktısını okumamız yeterli olacaktır.

![alt text]({{ BASE_PATH }}/assets/images/1_Yj23N0doLets1eBYyIhZEw.webp)

1.  Flannel eklentisi kurulur
2.  `kube-flannel` İsim uzayında `kube-flannel-ds-2595b` POD'u başlatılır (`kube-flannel kube-flannel-ds-2595b 0/1 Init:1/2 0 10s`)
3.  `kube-system` İsim uzayındaki CoreDNS kapsülleri `Pending` durumunda (`kube-system coredns-5dd5756b68-l5ndn 0/1 Pending`)
4.  `kube-flannel-ds-2595b` Kapsülü önce `PodInitializing` durumuna, sonra `Running` durumuna kavuşunca
5.  CoreDNS podları sırasıyla `Pending`, `ContainerCreating`, `Running` durumlarına gelir

> _Bir PDO’un açık açık durumu, bilgileri, olayları görüntülemek için_ `_kubectl describe pods/coredns-5dd5756b68-l5ndn -n kube-system_` _komutunu çalıştırabiliriz_

Şimdi master düğümün durumunu ve hatta k8s kümemize dahil tüm düğümlerin durumlarını görelim:

```
ubuntu@master-node:~$ kubectl get nodes  
NAME          STATUS   ROLES           AGE   VERSION  
master-node   Ready    control-plane   18m   v1.28.8
```

CoreDNS Podu çalışır durumda olduğunda worker düğümlerimizi k8s kümemize katmaya devam edebiliriz.

kubectl cluster-info
--------------------

Kümemizin durumunu görmek için `kubectl cluster-info` komutunu çalıştıralım:
![alt text]({{ BASE_PATH }}/assets/images/1_Tsz59X3QysjykQvWffCusA.webp)

Worker Node’un Kontrol Düzlemine Katılmasını Sağlayalım
-------------------------------------------------------

Worker node yaratıp az önce tamamladığımız “control plane”/”master node” yönetimine vererek kümemize ekleyeceğiz:

```
\# kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>  
kubeadm join 10.10.21.107:6443 --token ig7iao.t1rm2exxox7u2zhm \\  
        --discovery-token-ca-cert-hash sha256:59a21a956cef335ab92cbb812bc144d5a8a1d79e7c6f204e396c8981d48f2b70
```


Bu komutun içinde

*   `hedef control plane IP adresi ve port bilgisi`,
*   bağlantı için kullanacağımız `token`,
*   sertifikanın `hash özeti`

Burada gördüğümüz çıktıda yer alan token listesini `kubeadm token list` komutuyla da erişebiliriz:

```
ubuntu@cem:~$ kubeadm token list  
TOKEN                     TTL         EXPIRES                USAGES                   DESCRIPTION                                                EXTRA GROUPS  
ig7iao.t1rm2exxox7u2zhm   22h         2024-04-14T15:34:01Z   authentication,signing   The default bootstrap token generated by 'kubeadm init'.   system:bootstrappers:kubeadm:default-node-token
```

Eğer token’ın süresi dolarsa yenisini üretmek için `kubeadm token create` komutunu kullanabilirsiniz.

`/etc/kubernetes/pki/ca.crt` dosyasının özetini çıkarmak için:

```
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.\* //'
```

İşte bu ilk üretilen kubernetes kümesine katılım komutundaki token yerine yeniden token yaratarak worker node içinde kullanmak istersek aşağıdaki komutu kontrol düzlemi (master node) sunucusunda çalıştırabiliriz:

```
ubuntu@cem:~$ kubeadm token create --print-join-command  
kubeadm join 10.10.21.107:6443 --token qba343.5ph1wzitot2kmlf4 --discovery-token-ca-cert-hash sha256:59a21a956cef335ab92cbb812bc144d5a8a1d79e7c6f204e396c8981d48f2b70
```

Oluşan tokenları listelemek için `kubeadm token list` veya silmek istersek `kubeadm token delete <token id>` komutlarını çalıştırabiliriz:

```
kubeadm token list
```

Şimdi bize bir WORKER NODE gerekiyor…

CONTROL PLANE
=============

```
\# ----------- Hostname ve /etc/hosts Ayarlanıyor ----------------  
sudo hostnamectl set-hostname master-node  
\# sudo nano /etc/hosts ile dosyaya master-node'a ulaşılabilecek IP adresini aşağıdaki gibi ekle:  
\# 10.10.21.128  master-node  
\# ----------- containerd Kuruluyor ----------------  
sudo install -m 0755 -d /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
echo "deb \[arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg\] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  
sudo apt-get update  
sudo apt-get install containerd.io -y  
\# ----------- containerd Ayarlanıyor ----------------  
\# \`/etc/containerd/config.toml\` Dosyasını varsa silip, düzenleyelim:  
sudo rm -rf /etc/containerd/config.toml  
sudo mkdir -p /etc/containerd  
\# containerd'yi varsayılan ayarlarını dosyaya yazalım:  
sudo containerd config default | sudo tee /etc/containerd/config.toml  
\# /etc/containerd/config.toml dosyasının sonuna \`SystemdCgroup = true\` satırını ekleyelim  
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.mirrors\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.mirrors\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.ulakhaberlesme.com.tr"\]\\n          endpoint = \["http:\\/\\/registry.ulakhaberlesme.com.tr"\]/igs' /etc/containerd/config.toml  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.configs\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.configs\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.configs."registry.ulakhaberlesme.com.tr".tls\]\\n          insecure\_skip\_verify = true/igs' /etc/containerd/config.toml  
sudo systemctl restart containerd  
  
\# --------------------- K8s Kuruluyor -------------------------------  
\# Yukarıda yaptığımız için tekrar yapmayabilirsiniz ama tekrar yapsanız da aynı dizini boş yaratmaz:  
sudo mkdir -p /etc/apt/keyrings   
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg  
sudo echo "deb \[signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg\] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list  
sudo apt update  
sudo apt install -y kubelet kubeadm kubectl  
\# --------------------- DISABLING SWAPOFF -------------------------------  
sudo swapoff -a  
\# swapoff'un her yeniden başlatıldığında tekrar aktif olmaması için /etc/fstab'da aşağıdaki değişiklik yapılır  
sudo sed -i '/ swap / s/^\\(.\*\\)$/#\\1/g' /etc/fstab   
\# --------------------- Çekirdek Modülleri Ayarlanıyor -------------------------------  
cat << EOL | sudo tee /etc/modules-load.d/containerd.conf  
overlay  
br\_netfilter  
EOL  
sudo modprobe overlay  
sudo modprobe br\_netfilter  
cat << EOL | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf  
net.bridge.bridge-nf-call-iptables  = 1  
net.bridge.bridge-nf-call-ip6tables = 1  
net.ipv4.ip\_forward                 = 1  
EOL  
sudo sysctl --system  
  
\# --------------------- Master Node Başlatılıyor -------------------------------  
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --control-plane-endpoint=master-node --v=5  
\# --------------------- POD Ağı Eklentisi Kuruluyor -------------------------------  
kubectl --kubeconfig .kube/config apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

WORKER NODE
===========

```
\# Containerd yükle  
sudo mkdir -p /etc/apt/keyrings  
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg  
echo "deb \[arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg\] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null  
sudo apt-get update  
sudo apt-get install containerd.io -y  
sudo rm -rf /etc/containerd/config.toml  
sudo mkdir -p /etc/containerd  
  
\# containerd'yi varsayılan ayarlarını dosyaya yazalım:  
sudo containerd config default | sudo tee /etc/containerd/config.toml  
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.mirrors\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.mirrors\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.mirrors."registry.ulakhaberlesme.com.tr"\]\\n          endpoint = \["http:\\/\\/registry.ulakhaberlesme.com.tr"\]/igs' /etc/containerd/config.toml  
sudo perl -i -p0e 's/      \\\[plugins.\\"io.containerd.grpc.v1.cri\\".registry.configs\\\]/      \[plugins."io.containerd.grpc.v1.cri".registry.configs\]\\n        \[plugins."io.containerd.grpc.v1.cri".registry.configs."registry.ulakhaberlesme.com.tr".tls\]\\n          insecure\_skip\_verify = true/igs' /etc/containerd/config.toml  
sudo systemctl restart containerd  
  
\# Takas alanı kapat  
sudo swapoff -a  
\# swapoff'un her yeniden başlatıldığında tekrar aktif olmaması için /etc/fstab'da aşağıdaki değişiklik yapılır  
sudo sed -i '/ swap / s/^\\(.\*\\)$/#\\1/g' /etc/fstab  
  
\# Linux çekirdek modülleri yükle  
cat << EOL | sudo tee /etc/modules-load.d/containerd.conf  
overlay  
br\_netfilter  
EOL  
sudo modprobe overlay  
sudo modprobe br\_netfilter  
  
cat << EOL | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf  
net.bridge.bridge-nf-call-iptables  = 1  
net.bridge.bridge-nf-call-ip6tables = 1  
net.ipv4.ip\_forward                 = 1  
EOL  
sudo sysctl --system  
  
\# K8s kurulumu yap  
sudo mkdir -p /etc/apt/keyrings  
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg  
sudo echo "deb \[signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg\] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list  
sudo apt update  
sudo apt install -y kubelet kubeadm kubectl   
sudo apt-mark hold kubelet kubeadm kubectl
```

Kurulum ve ayarlar tamamlandıktan sonra artık worker düğümü, kontrol düzlemine (master’a) daha önce master üstünde yaratılmış token ile ekleyebiliriz. Aşağıdaki kubeadm komutu sunucuda oluşturulmuştu ve örnek olarak aşağıdaki gibi olacaktır:

```
kubeadm join 10.10.21.107:6443 --token ig7iao..... --discovery-token-ca-cert-hash sha256:59a21a956c.....
```

Örnek çıktı şöyle olacak:

```
ubuntu@worker01:~$ sudo kubeadm join 10.10.21.131:6443 --token tcdxte.hdv3zoa9cxvwvan7  --discovery-token-ca-cert-hash sha256:085d7f79e52627307a341e28beea53e26934d3ba41ddcec029041aa61a35d414  
\[preflight\] Running pre-flight checks  
\[preflight\] Reading configuration from the cluster...  
\[preflight\] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'  
\[kubelet-start\] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"  
\[kubelet-start\] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"  
\[kubelet-start\] Starting the kubelet  
\[kubelet-start\] Waiting for the kubelet to perform the TLS Bootstrap...
``````
This node has joined the cluster:  
\* Certificate signing request was sent to apiserver and a response was received.  
\* The Kubelet was informed of the new secure connection details.Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

POD Oluşturma & Hangi Düğümde Olduğunu Görüntüleme
==================================================

Kontrol düzleminde pod oluştur diyeceğiz ve kapsülün ayrıntılarında nerede oluştuğunu göreceğiz:

```
kubectl run nginx --image=nginx  
kubectl get pods -o wide
```

![alt text]({{ BASE_PATH }}/assets/images/1_9srv5dc-RCCZ-7FFf2WzuQ.png)


HATA GİDERME
============

Worker Node tekrar kümeye eklenmek istenirse

Önceki kurulumlardan varsa dosyalar silinir ve çalışıyorsa 10250 portunda `kubelet` hizmeti kapatılır. Sonrasında `kubeadm join` ile tekrar eklemeye çalışabiliriz:

```
sudo rm -rf /etc/kubernetes/pki/ca.crt /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf && sudo kill -9 \`sudo lsof -t -i:10250\`
```

Önceki kurulumu iptal edip tekrar başlatmak

Aşağıdaki hataya göre süreçleri durdur, dosya ve dizinleri sil:

*   2379, 2380, 10250, 6443, 10259 ve 10257 portlarında çalışan uygulamalar var bunları durdurun

```
sudo netstat -pltn | grep -E '(2379|2380|10250|6443|10259|10257)'   
sudo kill -9 $(sudo lsof -t -i:2379) $(sudo lsof -t -i:2380) $(sudo lsof -t -i:10250) $(sudo lsof -t -i:10257) $(sudo lsof -t -i:10259) $(sudo lsof -t -i:6443)
```

*   Tekrar yaratılması için önceden oluşturulmuş bu dosyaları silin:
*   /etc/kubernetes/manifests/kube-apiserver.yaml
*   /etc/kubernetes/manifests/kube-controller-manager.yaml
*   /etc/kubernetes/manifests/kube-scheduler.yaml
*   /etc/kubernetes/manifests/etcd.yaml  
    `sudo rm -rf /etc/kubernetes/manifests/kube-*.yaml /etc/kubernetes/manifests/etcd.yaml /var/lib/etcd`

```
ubuntu@master-cp1:~$ sudo kubeadm init --control-plane-endpoint=master-cp1  
I0413 17:10:02.054895    2697 version.go:256\] remote version is much newer: v1.29.3; falling back to: stable-1.28  
\[init\] Using Kubernetes version: v1.28.8  
\[preflight\] Running pre-flight checks  
error execution phase preflight: \[preflight\] Some fatal errors occurred:  
        \[ERROR Port-6443\]: Port 6443 is in use  
        \[ERROR Port-10259\]: Port 10259 is in use  
        \[ERROR Port-10257\]: Port 10257 is in use  
        \[ERROR FileAvailable--etc-kubernetes-manifests-kube-apiserver.yaml\]: /etc/kubernetes/manifests/kube-apiserver.yaml already exists  
        \[ERROR FileAvailable--etc-kubernetes-manifests-kube-controller-manager.yaml\]: /etc/kubernetes/manifests/kube-controller-manager.yaml already exists  
        \[ERROR FileAvailable--etc-kubernetes-manifests-kube-scheduler.yaml\]: /etc/kubernetes/manifests/kube-scheduler.yaml already exists  
        \[ERROR FileAvailable--etc-kubernetes-manifests-etcd.yaml\]: /etc/kubernetes/manifests/etcd.yaml already exists  
        \[ERROR Port-10250\]: Port 10250 is in use  
        \[ERROR Port-2379\]: Port 2379 is in use  
        \[ERROR Port-2380\]: Port 2380 is in use  
        \[ERROR DirAvailable--var-lib-etcd\]: /var/lib/etcd is not empty  
\[preflight\] If you know what you are doing, you can make a check non-fatal with \`--ignore-preflight-errors=...\`  
To see the stack trace of this error execute with --v=5 or higher
```

Worker Node Kurulumunu Yeniden Başlatmak

Worker node üzerindeyiz ve master düğüme eklemek isterken şu hatayı alıyoruz:

```
ubuntu@worker-node1:~$ sudo kubeadm join 10.10.21.107:6443 --token qba343.5ph1wzitot2kmlf4 --discovery-token-ca-cert-hash sha256:59a21a956cef335ab92cbb812bc144d5a8a1d79e7c6f204e396c8981d48f2b70  
sudo: unable to resolve host worker-node1: Temporary failure in name resolution  
\[preflight\] Running pre-flight checks  
        \[WARNING Hostname\]: hostname "worker-node1" could not be reached  
        \[WARNING Hostname\]: hostname "worker-node1": lookup worker-node1 on 192.168.10.12:53: server misbehaving  
error execution phase preflight: \[preflight\] Some fatal errors occurred:  
        \[ERROR FileAvailable--etc-kubernetes-pki-ca.crt\]: /etc/kubernetes/pki/ca.crt already exists  
\[preflight\] If you know what you are doing, you can make a check non-fatal with \`--ignore-preflight-errors=...\`  
To see the stack trace of this error execute with --v=5 or higher
```

*   Önce isim çözümleyemediği hataya bakalım:

```
sudo: unable to resolve host worker-node1: Temporary failure in name resolution
```

*   Bu hata, DNS çözümlemesinde geçici bir başarısızlık olduğunu belirtiyor. Çözüm olarak, `/etc/hosts` dosyasında düğümünüzün doğru IP adresi ve ana bilgisayar adını belirtmek iyi bir ilk adımdır. Örneğin:

```
127.0.0.1 localhost worker-node1
```

*   Ayrıca, sunucunuzun DNS yapılandırmasında `/etc/resolv.conf` dosyasında doğru DNS sunucularının eklendiği ve etki alanınızın doğru bir şekilde ayarlandığından emin olun.
*   İkinci hatamız `/etc/kubernetes/pki/ca.crt` dosyasının zaten var ve `kubeadm join` komutu, bu dosyanın varlığını beklemiyor ve dosya zaten varsa devam etmeyecek.
*   Ya aşağıdaki komutla bu hatayı yok sayabiliriz

```
sudo kubeadm join 10.10.21.107:6443 --token qba343.5ph1wzitot2kmlf4 --discovery-token-ca-cert-hash sha256:59a21a956cef335ab92cbb812bc144d5a8a1d79e7c6f204e396c8981d48f2b70 --ignore-preflight-errors=FileAvailable--etc-kubernetes-pki-ca.crt
```

*   Yahut `/etc/kubernetes/pki/ca.crt` dosyasını silip tekrar `kubeadm join` komutunu çalıştırabiliriz.

Avoid error “/proc/sys/net/bridge/bridge-nf-call-iptables does not exist” on kubeinit (reference [https://github.com/kubernetes/kubeadm/issues/1062](https://github.com/kubernetes/kubeadm/issues/1062) ). This is not necessary if docker is also installed in step 6.

1.  `sudo modprobe br_netfilter`
2.  `sudo nano /proc/sys/net/ipv4/ip_forward` Edit entry in ip\_forward file and change to 1

kubeinit for use with Flannel

1.  `sudo kubeadm init --pod-network-cidr=10.244.0.0/16`

Below error is due to the version mismatch between kubernetes and containerd. See the suitable versions at [https://github.com/containerd/containerd/blob/main/RELEASES.md#kubernetes-support](https://github.com/containerd/containerd/blob/main/RELEASES.md#kubernetes-support)

```
root@ubuntu:/home/ubuntu# kubeadm init  
\[init\] Using Kubernetes version: v1.27.3  
\[preflight\] Running pre-flight checks  
error execution phase preflight: \[preflight\] Some fatal errors occurred:  
\[ERROR CRI\]: container runtime is not running: output: time="2023-06-21T08:06:36-04:00" level=fatal msg="validate service connection: CRI v1 runtime API is not implemented for endpoint "unix:///var/run/containerd/containerd.sock": rpc error: code = Unimplemented desc = unknown service runtime.v1.RuntimeService"  
, error: exit status 1  
\[preflight\] If you know what you are doing, you can make a check non-fatal with \`--ignore-preflight-errors=...\`  
To see the stack trace of this error execute with --v=5 or higher
```

Copy to config as kubadm command says

1.  `mkdir -p $HOME/.kube`
2.  `sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`
3.  `sudo chown $(id -u):$(id -g) $HOME/.kube/config`

Apply Flannel (reference [https://github.com/flannel-io/flannel](https://github.com/flannel-io/flannel) )

1.  `kubectl apply -f [https://raw.githubusercontent.com/flannel-io/flannel/v0.20.2/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/flannel-io/flannel/v0.20.2/Documentation/kube-flannel.yml)`

All should be running now:

`kubectl get pods --all-namespaces`

Oluşturulan Master Node makinesinde token generate edilir:

`kubeadm token create --print-join-command`

Sonrasında bu token dosya içinden kopyalanır:

`cd /home/ubuntu/.kube/config`

`cat /home/ubuntu/.kube/config`

Worker Node kurulmak istenen makinede config dosyası oluşturulur ve Master makinesinden alınan config bilgileri .kube/config içine kopyalanır:

`mkdir -p $HOME/.kube`

`sudo vi /home/cnrusr/.kube/config`

Ardından aşağıdaki gibi komut ile Worker Node Oluşturulur:

`kubeadm join 10.10.44.151:6443 --token 2oq5tc.z7zojhjefxo90qsh --discovery-token-ca-cert-hash sha256:e8c2679c196337d6cf997e4b`