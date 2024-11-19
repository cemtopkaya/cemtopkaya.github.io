---
layout: post
title:  "nginx proxy management"
date:   2022-08-28 11:57:24 +0000
categories: SSH
tags: ssh jump-node vscode
---
Nginx’i proxy olarak kullanımını kolaylaştıran bir arayüz. Kaynak adresi [https://nginxproxymanager.com/](https://nginxproxymanager.com/).

## Kurulumu

Aşağıdaki docker-compose dosyası 1 nginx proxy management konteyneri ve 2 web sunucu ayaklandıracaktır.

```yaml
version: '2'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    # Eğer proxy manager'a ekleyeceğimiz host'lar proxy ile aynı 
    # makinada çalışıyorlarsa yani üzerine gelen trafiği proxy
    # manager'ın host sunucusunun farklı portlarındaki konteynerlere 
    # yönlendirecekse network_mode:host seçilebilir.
    # proxy manager böylece 127.0.0.1 adresine yönlendirdiğinde 
    # host'un kendisinde port mapping yapılan
    # konteynerlere trafiği akıtır. 
    # network_mode: host
    #
    # Eğer docker desktop kurulu windows host'un üstündeki 
    # konteynerlere trafiği yönlendirecekse
    # örneğin host.docker.internal:8002 veya 
    # host.docker.internal:8003 gibi proxy host ve port yazılabilir.
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt# Aşağıdaki konteynerler 8002 ve 8003 portunda çalışıp nginx-proxy 
# http://debian.local
# http://registry.local
# adreslerinden uygulamalara yönlendirme yapar
  web1:
    image: nginxdemos/hello
    container_name: web-sunucu-8002
    ports:
      - '8002:80'
  
  web2:
    container_name: web-sunucu-8003
    image: nginxdemos/hello
    ports:
      - '8003:80'
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*-ZXkv3Y91nssOpp7p3EfSQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_-ZXkv3Y91nssOpp7p3EfSQ.webp)

## Proxy Host Tanımlar

localhost:81 adresinde ayaklanmış olacak nginx proxy manager’da host tanımları yapacağız.

```
Hosts -> Proxy Hosts
```

<!-- ![](https://miro.medium.com/v2/resize:fit:483/1*_5mxI2YituxRETzNnfQwNg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1__5mxI2YituxRETzNnfQwNg.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*6qrXPZOuzCfvvhl8pofcHQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_6qrXPZOuzCfvvhl8pofcHQ.webp)

Örneğin bir alt alan adımız yönetimin, diğeri ise kullanıcıların erişiminde olacak iki farklı adresi işaret etsin.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*JWfKlaoD0wJiq7WJqqXHhg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_JWfKlaoD0wJiq7WJqqXHhg.webp)

8003 Portunda çalışan web sunucumuz kullanici.alanadi.com.tr adresine hizmet versin.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*i0n5HLbnU_AOwgTr-BJU1A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_i0n5HLbnU_AOwgTr-BJU1A.webp)


Listemiz tamamlandı.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*h3cEtkKH6m03GLibYVVHrQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_h3cEtkKH6m03GLibYVVHrQ.webp)

Hedef sunucu adresi olarak host.docker.internal adresini kullanıyoruz çünkü windows üstünde çalışan docker desktop ile windows makinasına erişmek için bu adı kullanmalıyız.

Eğer nginx proxy management konteyneri için — network=host ayarını yapmış olsaydık bu kez 127.0.0.1 adresini göstermemiz yeterli olacaktı.

Şimdi yonetim.alanadi.com.tr ve kullanici.alanadi.com.tr adreslerinin nginx proxy management kurulu olan sunucuya yönlendirilmesi gerekiyor. Uyguladığımız örnekte hepsi aynı sunucu yani 127.0.0.1 adresinde çalıştığı için ayar şöyle olacak:

```
127.0.0.1 yonetim.alanadi.com.tr
127.0.0.1 kullanici.alanadi.com.tr
```

![alt text]({{ BASE_PATH }}/assets/images/1_2zsKhTvwC1BjCZf1MB_hVQ.webp)

![alt text]({{ BASE_PATH }}/assets/images/1_feqUSsGVmRkh6bjJYkVOtQ.webp)

![alt text]({{ BASE_PATH }}/assets/images/1_feqUSsGVmRkh6bjJYkVOtQ-1.webp)