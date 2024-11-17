---
layout: post
title:  "Openstack Notlarım"
date:   2024-08-27 11:57:24 +0000
categories: Openstack
tags: openstack ubuntu
---
Biraz gecikmeli oldu bu notlar ama artık biriktirmenin zamanı geçmişti bile.

## OpenStack CLI [*](https://docs.redhat.com/en/documentation/red_hat_openstack_platform/10/html/command-line_interface_reference_guide/index)

* Açıklama: openstack komut satırı aracı, OpenStack API'lerine erişim sağlamak için kullanılan bir genel arayüzdür. Kullanıcıların bulut kaynaklarını yönetmesine olanak tanır ve diğer OpenStack bileşenlerine erişim sağlar. Bu araç, OpenStack'ın tüm API'lerini kapsayan bir yapıdadır ve belirli bileşenlere özgü komutları içerir.

* Kapsam: openstack komutu, Nova (hesaplama), Glance (görüntü), Cinder (depolama) gibi bileşenlerle etkileşime giren komutları içerir. Bu, kullanıcıların bu bileşenlerle ilgili işlemleri tek bir komutla gerçekleştirmesine olanak tanır.

* Özellikler: Kullanıcılar, openstack komutunu kullanarak sunucu oluşturma, görüntü yönetimi, depolama alanı ayarlama gibi birçok işlemi gerçekleştirebilir. Komut yapısı tutarlıdır ve kullanıcıların farklı OpenStack bileşenleri arasında geçiş yapmasını kolaylaştırır

Otomatik tamamlama özelliğini açmak için [*](https://docs.openstack.org/python-openstackclient/stein/cli/command-objects/complete.html):

    openstack complete | sudo tee /etc/bash_completion.d/osc.bash_completion > /dev/null
    source /etc/bash_completion.d/osc.bash_completion

### openstack server create [*](http://openstack server create)

    openstack --debug server create \
    --flavor 2bc5a700-31b7-41be-b5d3-d7c2eabf0793 \
    --key-name cemsil \
    --availability-zone "cnr206" \
    --image 874f75a8-2614-4b0a-8b5d-53a03eea4d72 \
    --security-group default \
    --user-data cloud-config.yaml \
    --config-drive False \
    --property description="My Server" \
    --nic net-id="70d9fcaf-e39d-4405-91ec-b06702ff0831" \
    silCem

Yeni bir sunucu oluşturur.

    usage: openstack server create [-h] [-f {json,shell,table,value,yaml}]
                                   [-c COLUMN] [--max-width <integer>]
                                   [--noindent] [--prefix PREFIX]
                                   (--image <image> | --volume <volume>) --flavor
                                   <flavor>
                                   [--security-group <security-group-name>]
                                   [--key-name <key-name>]
                                   [--property <key=value>]
                                   [--file <dest-filename=source-filename>]
                                   [--user-data <user-data>]
                                   [--availability-zone <zone-name>]
                                   [--block-device-mapping <dev-name=mapping>]
                                   [--nic <net-id=net-uuid,v4-fixed-ip=ip-addr,v6-fixed-ip=ip-addr,port-id=port-uuid>]
                                   [--hint <key=value>]
                                   [--config-drive <config-drive-volume>|True]
                                   [--min <count>] [--max <count>] [--wait]
                                   <server-name>

**Positional arguments**

**<server-name>**

New server name

**Optional arguments**

**-h, — help**

show this help message and exit

**— image <image>**

Create server from this image (name or ID)

**— volume <volume>**

Create server from this volume (name or ID)

**— flavor <flavor>**

Create server with this flavor (name or ID)

**— security-group <security-group-name>**

Security group to assign to this server (name or ID)
 (repeat option to set multiple groups)

**— key-name <key-name>**

Keypair to inject into this server (optional
 extension)

**— property <key=value>**

Set a property on this server (repeat option to set
 multiple values)

**— file <dest-filename=source-filename>**

File to inject into image before boot (repeat option
 to set multiple files)

**— user-data <user-data>**

User data file to serve from the metadata server

**— availability-zone <zone-name>**

Select an availability zone for the server

**— block-device-mapping <dev-name=mapping>**

Map block devices; map is
```
 <id>:<type>:<size(GB)>:<delete_on_terminate> (optional
 extension)
```

**— nic <net-id=net-uuid,v4-fixed-ip=ip-addr,v6-fixed-ip=ip-addr,port-id=port-uuid>**

Sunucuda bir ağ kartı (NIC) oluşturur. Birden çok NIC oluşturmak için seçeneği birden çok kez belirtin. net-id veya port-id’den birinin sağlanması gerekir, ancak her ikisinin de sağlanması gerekmez. **net-id: NIC**’yi bu UUID ile ağa ekleyin, **port-id: NIC**’yi bu UUID ile bağlantı noktasına ekleyin, **v4-fixed-ip: NIC** için IPv4 sabit adresi (isteğe bağlı), **v6-fixed-ip: NIC** için IPv6 sabit adresi (isteğe bağlı).

**— hint <key=value>**

Hints for the scheduler (optional extension)

**— config-drive <config-drive-volume>|True**

Use specified volume as the config drive, or ‘True’ to
 use an ephemeral drive

**— min <count>**

Minimum number of servers to launch (default=1)

**— max <count>**

Maximum number of servers to launch (default=1)

**— wait**

Wait for build to complete

### openstack server add fixed ip [*](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/server.html#server-add-fixed-ip)

Sunucuya sabit bir IP adresi ekler.

    openstack server add fixed ip
        [--fixed-ip-address <ip-address>]
        <server>
        <network>

**--fixed-ip-address <ip-address> **İstenen sabit IP adresi

**server **Sabit IP adresini (isim veya kimlik) alacak sunucu

**network **Sabit IP adresinin tahsis edileceği ağ (ad veya kimlik)

### openstack server add floating ip [*](https://docs.openstack.org/python-openstackclient/pike/cli/command-objects/server.html#server-add-floating-ip)

Sunucuya değişken IP adresi ekler.

    openstack server add floating ip
        [--fixed-ip-address <ip-address>]
        <server>
        <ip-address>

## Nova — Hesaplama

Nova, OpenStack’ın en bilinen ve en karmaşık bileşenidir. Nova, kullanıcı API isteklerini çalışan sanal makinelere dönüştürmek için birçok sürecin işbirliği yapmasını sağlar. Nova’nın ana bileşenleri ve işlevleri şunlardır:

* nova-api: OpenStack bulutunu kullanmak için gelen komutları kabul eden RESTful API web servisi

* nova-compute: hipervisor API’leri aracılığıyla sanal makine örneklerini oluşturan ve sonlandıran bir çalışan daemon

* nova-scheduler: kuyruktaki bir isteği alır ve hangi hesaplama sunucusu ana bilgisayarında çalıştırılacağını belirler

* nova-conductor: nova-compute için uzun süren görevleri tamamlama gibi hizmetler sağlar

* nova veritabanı: bir bulut altyapısı için oluşturma zamanları ve çalışma zamanı durumlarının çoğunu depolar

Nova, Keystone kimlik doğrulama, Glance görüntüler ve Horizon web arayüzü gibi diğer OpenStack hizmetleriyle de etkileşime girer.

### Nova CLI

* Açıklama: nova komut satırı aracı, OpenStack'ın hesaplama hizmeti olan Nova ile etkileşimde bulunmak için kullanılır. Bu araç, sanal makineleri oluşturma, silme ve yönetme gibi işlemleri gerçekleştirmek için özel komutlar sunar.

* Kapsam: Nova, yalnızca hesaplama kaynaklarıyla ilgili işlemleri yönetir. Örneğin, sanal makine örneklerini başlatma veya durdurma gibi işlemler için nova komutları kullanılır.

### nova list [*](https://docs-ospc.rackspace.com/cloud-servers/v2/getting-started/create-server/listing-servers-nova.html)

Openstack üzerinde yaratılmış sanal makinelerin listesini çeker.

    $ nova list

nova console-log

ccc isimli sanal makinenin 100 satır gelecek şekilde günlüklerini çeker.

    nova console-log --length 100 ccc

![alt text]({{ BASE_PATH }}/assets/images/nova console-log--length.png)

## Glance — Görüntü Deposu

Glance, disk ve sunucu görüntüleri için bulma, kayıt ve teslim hizmetleri sağlar. Glance’in ana bileşenleri ve işlevleri şunlardır:

* **glance-api**: görüntü bulma, görüntü alma ve görüntü depolama için Görüntü API çağrılarını kabul eder

* **glance-registry**: görüntülerle ilgili meta verileri (boyut, tür vb.) depolar, işler ve alır

* **glance veritabanı**: görüntü meta verilerini depolamak için bir veritabanı

* Asıl görüntü dosyaları için bir depolama deposu. Glance, normal dosya sistemlerini, Ceph blok aygıtlarını, Amazon S3, HTTP ve Swift’i destekler

Glance, kullanıcılardan veya Nova bileşenlerinden görüntüler (veya görüntü meta verileri) için API istekleri kabul eder ve disk dosyalarını nesne depolama hizmeti Swift veya diğer depolama depolarında saklayabilir.

### Glance CLI

* Açıklama: glance komut satırı aracı, OpenStack'ın görüntü hizmeti olan Glance ile etkileşimde bulunmak için kullanılır. Bu araç, disk görüntülerini yönetmek için özel komutlar sunar.

* Kapsam: Glance, görüntülerin kaydedilmesi, alınması ve yönetilmesi gibi işlemleri gerçekleştirir. Örneğin, yeni bir görüntü yüklemek veya mevcut bir görüntüyü silmek için glance komutları kullanılır.

glance komut satırı uygulamasını kullanmak için önce kuralım sudo apt install python3-glanceclient ve koşturalım:

    $ glance --os-username=cemt \
             --os-password=sifre123 \
             --os-auth-url=http://10.10.0.198:5000/ \
             --os-project-name=Development \
             image-list

<!-- ![](https://cdn-images-1.medium.com/max/2000/1*P9hpcihJTCc_gPaGtY7xAA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/glance_image-list.png)

    export OS_USERNAME=cemt
    export OS_PASSWORD=sifre123
    export OS_PROJECT_NAME=Development
    export OS_AUTH_URL=http://10.10.0.198:5000
    $ glance image-create \
             --name "Ubuntu 22.04 Server 20240626 VMDK" \
             --disk-format vmdk --container-format bare \
             --file ubuntu-22.04-server-cloudimg-amd64.vmdk

<!-- ![](https://cdn-images-1.medium.com/max/2648/1*WpBrxaPjCAz6cYWxZBEVkA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/image_create.png)

Yansıları bir biçimden diğerine dönüştürmek için [buraya](https://docs.openstack.org/image-guide/convert-images.html) bakabilirsiniz

`**image.img**` to a qcow2 > `qemu-img convert -f raw -O qcow2 image.img image.qcow2`

vmdk image file to a raw image file > `qemu-img convert -f vmdk -O raw image.vmdk image.img`

vmdk image file to a qcow2 image file > `qemu-img convert -f vmdk -O qcow2 image.vmdk image.qcow2`