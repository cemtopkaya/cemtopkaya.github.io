---
layout: post
title:  "virtio ve vhost ne demektir, farkları nelerdir?"
date:   2024-03-22 11:57:24 +0000
categories: Virtualization
tags: virtio vhost
---
Önce Türkçelerini öğrenelim:  
_öykünme_ = Birinin yaptığı gibi yapmak, birine veya bir şeye benzemeye çalışmak, taklit etmek  
_emulation_ = taklit etme = _öykünme_

> _– Bir sanatçıdan öykündüğü belli; çünkü eserlerinde özgünlük yok._  
> _– Çok para kazanmak için, dayısına öykünmeye çalışıyor._  
> _– Oğlum, abisine öykünüp agresif davranışlarda bulunuyor.  
> – Bu otomobilin tasarımı, bir İsveç firmasından öykünülmüş.  
> – Toplumumuzda hunharca batıya öykünme söz konusu.  
> – Konuşurken öğretmenine öykündüğünü hissediyorum._

chatGPT’ye sorduk: Virtio ve vhost arasındaki farklar nedir?

## **virtio = Virtual I/O** [**\***](https://wiki.libvirt.org/Virtio.html#:~:text=Virtio%20is%20a%20virtualization%20standard,and%20cooperates%20with%20the%20hypervisor.)

> **Virtio, sanal donanımı yönetmek için kullanılan bir teknolojidir.**

Virtio’nun açılımı “**Virtual I/O**” veya “**Virtual Input/Output**” olarak bilinir. Virtio, sanal makine (VM) ile ana bilgisayar arasındaki disk ve ağ üstündeki I/O işlemlerini sanal donanım üzerinden gerçekleştirmek için tasarlanmış bir **arabirim standardıdır**. Sanal makine (VM) kendinin sanal bir ortamda koştuğunu bilir ve sanallaştırmayla birlikte çalışarak sanal donanımı üzerinden gerçek donanım kaynaklarına (ağ ve disk) erişir. **Virtio, sanal donanım için özel olarak tasarlanmış bir sürücü (**_virtio-blk, virtio-net, virtio-pcl, virtio-ballon, virtio-console_**) ve arabirim seti sağlar.**

<!-- ![](https://miro.medium.com/v2/resize:fit:578/1*IEQ3yZXiniOc5IRmi4v7xA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_IEQ3yZXiniOc5IRmi4v7xA.webp)

## vhost = Virtual Host

> VHost, bir sanal makineye ait sanal ağ arabirimlerinin, fiziki ağ arabirimlerine doğrudan erişimini sağlayan **bir Linux kernel modülüdür**.

Vhost, sanal ağ makinelerdeki ağ trafiğini yönetmek için kullanılır ve ana bilgisayarın işlemci gücüne yük bindirmeyi azaltır. Vhost, sanal ağ arabirimlerini doğrudan yöneten bir arabirim değildir; bunun yerine, bu işlemi gerçekleştirmek için bir sanal makineye monte edilir.

## virtio ve vhost farkları

Farklarına gelince, **virtio donanım arayüzüdür** ve sanal donanımı yönetirken **vhost bir yazılım arayüzüdür** ve sanal ağ arabirimlerinin ağ trafiğini yönetir. Virtio, sanal makine işlemcilerinin belirli bir donanım üzerinde doğrudan çalışmasına izin verirken, vhost sanal ağ arabirimlerinin ağ trafiğini yöneterek ana bilgisayarın işlemci gücüne yük bindirmeyi azaltır.

Özetle, virtio, sanal donanımı yönetmek için kullanılırken vhost, sanal ağ trafiğini yönetmek için kullanılır. Virtio donanım arayüzüdür ve vhost bir yazılım arayüzüdür.

## Biraz Daha Ayrıntılı Çalışalım

## QEMU ile başlayalım

QEMU, bir makine için işletim sistemlerini ve programları farklı bir makinede çalıştırabilen bir makine öykünücüsüdür. Ancak, daha çok KVM çekirdek bileşenleriyle işbirliği içinde bir sanallaştırıcı olarak kullanılır. Konuk işletim sistemlerini sanallaştırabilmek için **donanım sanallaştırma teknolojisi**ni (**virtio**) kullanır.

<!-- ![](https://miro.medium.com/v2/resize:fit:509/1*ZjwMH7puIjfmyyABl4ahIA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ZjwMH7puIjfmyyABl4ahIA.webp)

libvirt (XML formatlı yapılandırmaları qemu CLI çağrılarına çeviren bir arayüz)

QEMU, konuk işletim sistemi sanallaştırma platformu sağlamanın yanı sıra tüm sistemin (PCI ana bilgisayar denetleyicisi, disk, ağ, video donanımı, USB denetleyicisi ve diğer donanım öğeleri) öykünmesini (emulation) sağlayan bir sistem öykünücüsüdür (emulator). Öykünme yoluyla konuk makine için farklı donanım ve cihaz modelleri sağlar. QEMU KVM ile birlikte kullanılabilir ve donanım uzantılarını kullanarak sanal makineleri neredeyse yerel hızda çalıştırabilir ..

[
![alt text]({{ BASE_PATH }}/assets/images/0_v5XMYVD9Yx2y1O1y.webp)
](https://insujang.github.io/2021-03-15/virtio-and-vhost-architecture-part-2/)

Aşağıdaki çizimde **Guest (User|Kernel) Space** ile konuk sanal makinayı, **Linux (User|Kernel) Space** ile ev sahibi makinayı görüyoruz. Sanal makina içinde fiziki donanım olmadığı için donanıma ait gerçek sürücüler de yok! Ancak misafir sanal makina içindeki virtio aygıt sürücülerini (**_virtio Driver_**) kullanarak -> QEMU içindeki virtio sanal aygıtlarına (**_virtio Device_**) ulaşır ve ev sahibi bilgisayarın

[
![alt text]({{ BASE_PATH }}/assets/images/0_R7EGY5cmVNWCiGA2.gif)
](https://developer.ibm.com/articles/l-virtio/)

Vhost protokolü ise virtio veri düzlemi uygulamasının performansı arttırmak için başka bir öğeye (kullanıcı işlemi veya çekirdek modülü) devredilmesine izin veren bir protokoldür

## Ön & Arka Uç Kavramı

Virtio, ön (front-end) ve arka uç (back-end) sürücülerden oluşur. Sanallaştırmada front-end ve back-end terimleri genellikle sanal makine (VM) ve ana makine (host) arasındaki etkileşimi tanımlamak için kullanılır. Front-end, sanal makinede çalışan uygulamaları ve işletim sistemini ifade ederken, back-end ise ana makinede çalışan hipervizörü ve diğer bileşenleri ifade eder. Virtio ve vhost protokolü bu iki taraf arasında veri iletişimini sağlamak için kullanılır. Virtio, sanal makinelerin (front-end) ana makineye (back-end) bağlı cihazlara erişmesini sağlar. Vhost protokolü ise virtio veri düzlemi uygulamasının performansı arttırmak için başka bir öğeye (kullanıcı işlemi veya çekirdek modülü) devredilmesine izin verir.

Sanallaştırmada front-end sürücüler genellikle sanal makine (VM) içinde çalışır ve sanal cihazların işletim sistemi tarafından tanınmasını sağlar. Örneğin, virtio-net bir front-end sürücüdür ve sanal makine içinde çalışarak sanal ethernet kartının işletim sistemi tarafından tanınmasını sağlar.

Back-end sürücüler ise ana makine (host) tarafında çalışır ve sanal cihazların fiziksel cihazlara erişimini sağlar. Örneğin, vhost-net bir back-end sürücüdür ve ana makine tarafında çalışarak virtio-net veri düzlemi uygulamasının performansını arttırmak için kullanılır.

Bu iki tür sürücü arasındaki iletişim genellikle paylaşılan bellek alanları veya diğer IPC mekanizmaları aracılığıyla gerçekleştirilir.

![alt text]({{ BASE_PATH }}/assets/images/0_6NT1toDTXw6JSi8t.webp)
## Paravirtualization, virtio’ya Karşı

Paravirtualization ve virtio, sanallaştırma teknolojileridir ve ücretsizdir. Paravirtualization, fiziksel donanımın kaynaklarını sanal makineler arasında paylaştıran bir sanallaştırma yöntemidir. Virtio ise sanal makinelerin disk, ağ ve diğer aygıtlara erişimi için kullanılan bir sanal aygıt sürücüsüdür.

Paravirtualization ve virtio, özellikle KVM (Kernel-based Virtual Machine) gibi açık kaynaklı sanallaştırma platformlarında yaygın olarak kullanılır. KVM, Linux çekirdeğine dayanan bir hipervizördür ve virtio sürücülerini kullanarak sanal aygıtların performansını artırır.

Paravirtualization ve virtio, diğer sanallaştırma platformlarına da entegre edilebilir, ancak kullanım durumlarına ve yapılandırmaya bağlı olarak farklılık gösterebilir. Örneğin, VMWare gibi bazı sanallaştırma platformları, paravirtualization ve virtio sürücülerini desteklemektedir.

## DPDK

DPDK (Data Plane Development Kit), hızlı paket işleme için bir dizi kitaplık ve sürücüdür. Linux kullanıcı alanında (user space) çalışır ve “Ortam Soyutlama Katmanı” (EAL-environment abstraction layer) olarak adlandırılan bir dizi kitaplık sağlar. EAL, ortamın ayrıntılarını gizler ve standart bir programlama arayüzü sağlar.

> Fiziki ağ anahtarı (physical network switch), bir ağ donanımıdır ve bilgisayarların ve diğer ağ öğelerinin birbirlerine bağlanmasına olanak verir.

![alt text]({{ BASE_PATH }}/assets/images/1_d_BZ43r_pG6izbfTkay-RQ.webp)

> Fiziki ağ anahtarları genellikle birden fazla Ethernet portuna sahiptir ve bu portlar aracılığıyla ağ cihazlarını birbirine bağlar. Ağ anahtarları, gelen veri paketlerini işleyerek hedef MAC adresini belirler ve paketi sadece hedef kapıya gönderir. Bu sayede ağ trafiği optimize edilmiş olur ve aynı zamanda güvenlik sağlanmış olur.  
> Sanal ağ anahtarları ise bu işlevi yazılım tabanlı olarak yapabilenlerdir:

![alt text]({{ BASE_PATH }}/assets/images/1_uEEme68JFbMKHrPGr8CZww.webp)

> Yazılım tabanlı sanal ağ anahtarı (software-based virtual network switch), fiziki bir ağ anahtarı gibi çalışan ancak yazılım olarak uygulanan bir ağ anahtarıdır. Bu tür ağ anahtarları genellikle sanallaştırma ortamlarında kullanılır ve sanal makinelerin (VM’ler) birbirleriyle ve dış dünya ile iletişim kurmasına olanak tanır. Open vSwitch gibi yazılım tabanlı sanal ağ anahtarları, gelişmiş ağ işlevleri sağlar ve esnek yapılandırmaya izin verir.
> 
> Örneğin, Docker bridge ağı bir tür yazılım tabanlı sanal ağ anahtarıdır. Docker bridge ağı, Docker konteynerlerinin birbirleriyle ve dış dünya ile iletişim kurmasına olanak tanır. Bir Docker host üzerinde birden fazla konteyner çalıştığında, bu konteynerler varsayılan olarak “bridge” adlı bir ağa bağlanır. Bu ağ sayesinde konteynerler birbirleriyle iletişim kurabilir ve dış dünya ile bağlantı kurabilir.
> 
> Linux makinesinde sanal bir ağ anahtarı oluşturmak için birden fazla yöntem bulunmaktadır.
> 
> 1\. YÖNTEM: **Linux köprüleme (bridging)** özelliğini kullanmaktır.  
> Linux köprüleme özelliği sayesinde birden fazla ağ arayüzünü bir araya getirerek sanal bir ağ anahtarı oluşturabilirsiniz. Öncelikle “bridge-utils” paketini kurmanız gerekmektedir:  
> apt-get install bridge-utils
> 
> Daha sonra “brctl” komutunu kullanarak yeni bir köprü oluşturabilirsiniz:  
> brctl addbr **mybridge**
> 
> Bu komut ile “mybridge” adında yeni bir köprü oluşturulmuş olur. Daha sonra bu köprüye ağ arayüzlerini ekleyebilirsiniz:  
> brctl addif **mybridge** eth0  
> brctl addif **mybridge** eth1
> 
> Bu komutlar ile “eth0” ve “eth1” adlı ağ arayüzlerini “mybridge” adlı köprüye eklemiş olduk. Artık bu iki ağ arayüzü arasında veri iletişimi sağlanacaktır.
> 
> 2\. YÖNTEM: **Open vSwitch** gibi yazılım tabanlı sanal ağ anahtarları da Linux makinesinde kurulabilir ve kullanılabilir.  
> Open vSwitch’i Linux makinesine kurmak için :  
> 1\. Öncelikle Open vSwitch’i indirin ve kurun. İndirmek için aşağıdaki komutu kullanabilirsiniz: wget [http://openvswitch.org/releases/openvswitch-2.15.0.tar.gz](http://openvswitch.org/releases/openvswitch-2.15.0.tar.gz)  
> 2\. İndirdiğiniz dosyayı açın ve kurulum dizinine gidin:  
> tar -xvf openvswitch-2.15.0.tar.gz  
> cd openvswitch-2.15.0  
> 3\. Gerekli bağımlılıkları kurun:  
> apt-get install build-essential libssl-dev  
> 4\. Open vSwitch’i yapılandırın ve kurun:  
> ./configure --prefix=/usr --localstatedir=/var --sysconfdir=/etc  
> make  
> make install  
> 5\. Open vSwitch servisini başlatın:  
> /etc/init.d/openvswitch-switch start

DPDK, özel çözümler için yaygın olarak kullanılır; örneğin ağ işlevi sanallaştırma ve gelişmiş yüksek verimli ağ anahtarlama (network switch) gibi. DPDK, hızlı veri düzlemi performansı için tamamlanmış modelini kullanır ve cihazlara anket yaparak kesme işleminin gecikmesini ortadan kaldırır. Ancak bu durum daha yüksek CPU tüketimi ile sonuçlanır. Herhangi bir işlemci üzerinde çalışacak şekilde tasarlanmıştır. İlk desteklenen CPU Intel x86 idi ve şimdi IBM PPC64 ve ARM64’e genişletilmiştir.
