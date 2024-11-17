---
layout: post
title:  "NodeJs ile Socks Proxy Üzerinden Http istekleri"
date:   2024-05-14 11:57:24 +0000
categories: Android
tags: nodejs socks proxy
---

Önce ağ bilgilerini görelim ki nereye vekil sunucu kuruyor, hangi sanal makineden hangi http\[s/2\] sunucularına istek yaptığımızı görelim.

Kodlarım şurada: [https://github.com/cemtopkaya/nodejs-http-2-s-over-proxy](https://github.com/cemtopkaya/nodejs-http-2-s-over-proxy)

Windows 11 içinde WSL olarak Ubuntu koşturuyorum.

Windows 11:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*jTPTSCxE6mHIS3CB1i-yJg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_jTPTSCxE6mHIS3CB1i-yJg.webp)

WSL makinelerinin ev sahibi bilgisayarla iletişimini sağlyacak sanal switch (anahtar)’in win11 içindeki durumu:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*hgOHrq0tVgh8iur98lX2jw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_hgOHrq0tVgh8iur98lX2jw.webp)

`vEthernet (WSL (Hyper-V firewall))`, adından da anlaşılacağı gibi, Hyper-V'nin sağladığı bir güvenlik duvarı yapılandırması sağlayan sanal Ethernet adaptörüdür. WSL2, Linux çekirdeğini Hyper-V sanallaştırma teknolojisi ile çalıştırır. Hyper-V sanal makineleri gibi WSL 2 instance'ları da sanal ağ adaptörleriyle instance’larının ağ trafiğini yönetmek ve WSL 2'nin internete veya yerel ağa erişimini sağlamaktır.

WSL içinden ağ ayarlarımıza bakalım:

```shell
cemt@PC-CEM-TOPKAYA:~$ ip -br -c a
lo               UNKNOWN        127.0.0.1/8 ::1/128
eth0             UP             192.168.188.36/20 fe80::215:5dff:feb9:4d52/64
docker0          DOWN           172.17.0.1/16 2001:db8:1::1/64 fe80::42:5ff:fef5:73c4/64 fe80::1/64
```

`eth0` Sanal linux makinemdeki sanal ağ kartım ve adresi: `192.168.188.36`

```shell
cemt@PC-CEM-TOPKAYA:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.176.1   0.0.0.0         UG    0      0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
192.168.176.0   0.0.0.0         255.255.240.0   U     0      0        0 eth0
```

Özellikle IP bloğu verilmemiş adreslere (internete — `0.0.0.0`) doğru yaptığım istekler `eth0` kartının üzerinden `192.168.176.1` ağ geçidine yönlendirileceğini rotalarda görüyoruz.

Bir istek yapalım ve sanal makinenin varsayılan ağ geçidinden (`192.168.176.1`) ev sahibi makinem olan windows 11'in sanal ağ adaptörüne gelecek. Oradan windows 11'in ağ geçidi olan modemime (`192.168.1.1`) ve internete olacak.

```shell
cemt@PC-CEM-TOPKAYA:~$ traceroute  google.com
traceroute to google.com (172.217.17.110), 30 hops max, 60 byte packets
 1  192.168.176.1 (192.168.176.1)  0.332 ms  0.303 ms  0.295 ms
 2  192.168.1.1 (192.168.1.1)  2.062 ms  5.310 ms  3.981 ms
 3  100.123.128.1 (100.123.128.1)  7.773 ms  8.163 ms  8.555 ms
 4  .... kalanı gereksiz ip adresleri
```

Önce basit bir nodejs http istemcisi görelim. Bu sadece bize bir isteğin iskeletini çıkarmak için fikir versin.

## NodeJS İle Basit Http İstemci

```js
// http_client.js

// 1. HTTP ve URL Modüllerini İçe Aktarma
// HTTP ve URL işlemleri için gerekli olan modülleri içe aktarır.
import http from 'http'; // HTTP modülü
import url from 'url'; // URL modülü

// 2. Hedef URL Tanımlama (İstek yapılacak hedef URL'yi tanımlar.)
const targetUrl = 'http://example.com:80';

// 3. URL'yi Ayrıştırma
// Hedef URL'yi ayrıştırarak parçalarına böler (hostname, path, port, vb.).
const urlParts = url.parse(targetUrl);

// 4. İstek Seçeneklerini Tanımlama
// HTTP isteği için gerekli seçenekleri (hostname, path, port, method) belirler.
const options = {
  hostname: urlParts.hostname, // Hedef sunucu adı
  path: urlParts.path, // Hedef yol
  port: urlParts.port, // Hedef port (80)
  method: 'GET', // İstek yöntemi (GET)
};

// 5. HTTP İsteği Oluşturma
// HTTP isteği oluşturur ve yanıtı işlemek için bir geri çağırma fonksiyonu tanımlar.
const req = http.request(options, (res) => {
  // 6. Yanıt Durum Kodunu Yazdırma
  // Sunucudan gelen yanıtın durum kodunu konsola yazdırır.
  console.log(`Yanıt Durumu Kodu: ${res.statusCode}`);

  // 7. Yanıt Başlıklarını Yazdırma
  // Sunucudan gelen yanıt başlıklarını konsola yazdırır.
  console.log('Başlıklar:');
  const headers = res.headers;
  for (const key in headers) {
    console.log(`${key}: ${headers[key]}`);
  }

  // 8. Yanıt Verilerini Alma ve Yazdırma
  // Yanıt verilerini parça parça alır ve konsola yazdırır.
  res.on('data', (chunk) => {
    console.log(chunk.toString());
  });

  // 9. Yanıt Hatalarını Ele Alma
  // Yanıt sırasında oluşabilecek hataları ele alır ve konsola yazdırır.
  res.on('error', (err) => {
    console.error('Hata:', err);
  });

  // 10. Yanıt Tamamlandığında Mesaj Yazdırma
  res.on('end', () => {
    console.log('Yanıt Alındı.');
  });
});

// 11. İstek Hatalarını Ele Alma
// İstek sırasında oluşabilecek hataları ele alır ve konsola yazdırır.
req.on('error', (err) => {
  console.error('İstek Sırasında Hata:', err);
});

// 12. İsteği Gönderme
// HTTP isteğini gönderir.
req.end();
```

```
+-------------------------------------------------------------------------------------+
|             HTTP ve URL Modüllerini İçe Aktarma                                     |
+-------------------------------------------------------------------------------------+
| +-------------------------------------------------------------------+               |
| |                 Hedef URL Tanımlama                               |               |
| | +---------------------------------------------------------------+ |               |
| | |                      URL'yi Ayrıştırma                        | |               |
| | |   +---------------------------------------------------------+ | |               |
| | |   |                İstek Seçeneklerini Tanımlama            | | |               |
| | |   |   +---------------------------------------------------+ | | |               |
| | |   |   |              HTTP İsteği Oluşturma                | | | |               |
| | |   |   |   +---------------------------------------------+ | | | |               |
| | |   |   |   |     Yanıt Durum Kodunu Yazdırma             | | | | |               |
| | |   |   |   |   +---------------------------------------+ | | | | |               |
| | |   |   |   |   |   Yanıt Başlıklarını Yazdırma         | | | | | |               |
| | |   |   |   |   |   +---------------------------------+ | | | | | |               |
| | |   |   |   |   |   |  Yanıt Verilerini Al ve Yazdır    | | | | | |               |
| | |   |   |   |   |   |   +-----------------------------+ | | | | | |               |
| | |   |   |   |   |   |   |  Yanıt Hatalarını Ele Alma  | | | | | | |               |
| | |   |   |   |   |   |   +-----------------------------+ | | | | | |
```

## whistle İle Yerel Proxy

<!-- ![](https://miro.medium.com/v2/resize:fit:700/0*wjwn_DBbyZGxxNgH.png) -->
![alt text]({{ BASE_PATH }}/assets/images/0_wjwn_DBbyZGxxNgH.webp)

Tüm kodu buraya yazayım sonra parça parça anlatacağım:

```shell
$ sudo su
$ npm install -g whistle
$ w2 start -H 192.168.188.36 --uiport 891 --httpPort 777 --httpsPort 888 --socksPort 999
$ curl -x http://192.168.188.36:8899 -L http://example.com
```

-   whistle Kuracağız
-   whistle Vekil sunucuyu ayaklandıracağız
-   curl İle whistle üzerinden http isteği yapacağız

Kurulum sonrasında yetkili bir kullanıcıyla çalıştırılacağı için `sudo su` ile kök kullanıcı olarak kurulumu yapın derim:

```shell
npm install -g whistle
```

Çalıştırmayı ister terminal ekranında günlük kayıtlarını takip edebilmek için `w2 run` ile isterseni arka planda çalışması için `w2 start` ile başlatabilirsiniz. Yardım için `w2 --help` !

`-H 192.168.188.36` ile WSL olarak Windows içinde koşturduğum Ubuntu 22.04'ün IP adresini verdim. Yoksa `any IP address` diye tanımlı olan `0.0.0.0` adresinde koşacaktı

`--ui-port 891` ile Web GUI ayaklandırın ki isteklerinizi internet gezgininde görebilesiniz,

`--httpPort 777` ile `http` isteklerini karşılayacak proxy hizmetinin 777 portunda çalışacağını,

`--httpsPort 888` ile `https` isteklerini karşılayacak proxy hizmetinin 888 portunda çalışacağını,

`--socksPort 999` ile `socks` protokolünde çalışacak vekil sunucu hizmetinin 999 portunda çalışacağını belirlemiş olursunuz

```shell
w2 start -H 192.168.188.36 --uiport 891 --httpPort 777 --httpsPort 888 --socksPort 999
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*6PV7n5Dr71Ujj9R6C_tEmQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_6PV7n5Dr71Ujj9R6C_tEmQ.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*ZgjFwDuhy-3XdCiXyKnuhA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ZgjFwDuhy-3XdCiXyKnuhA.webp)

Şimdi bir curl isteği atarak web arayüzünde isteğimizi görelim:

```shell
$ curl -x http://192.168.188.36:777 -L http://example.com
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*a5_Sgwmv-yW5cpdCG-_d4w.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_a5_Sgwmv-yW5cpdCG-_d4w.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*bqx4acY1xKJuGGxcqhMSgw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_bqx4acY1xKJuGGxcqhMSgw.webp)

## https İstekleri İçin Sertifika Ayarları

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*jaXf20j4XVJ6uRR05cQjhQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_jaXf20j4XVJ6uRR05cQjhQ.webp)

Ubuntu’da trust uygulaması olmadığı için yükledim `sudo apt install p11-kit` ve indirdiğim `rootCA.crt` dosyasını güvenilir sertifikaların arasına kattım (`echo “rootCA.crt” >> /etc/ca-certificates.conf)` sertifikaları güncelledim (`update-ca-certificates`) ve eklendiğini `trust list | grep -i whistle` komutuyla gördüm:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*cSiayv6ezxSKanC2OSYRcw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_cSiayv6ezxSKanC2OSYRcw.webp)

Karekod resimdeki açık iletişim kutusunda bir onay kutusu bulunur: `Capture HTTPS CONNECTS`.: HTTPS müdahale işlevini açın. Yalnızca bu onay kutusunu işaretleyerek ve kök sertifikayı yükleyerek Whistle, HTTPS ve Websocket isteklerini görebilir.

## socks Vekil Sunucu Kullanımı

Bilgisayarımızdan bir sunucuya tüm TCP isteklerini akıtmak için tünel açarız. Aşağıdaki örnekte `192.168.188.36` adresimizin `6000` portumuza gelen tüm istekleri uzak sunucu adresine yönlendireceğimizi `-D` anahtarıyla belirtiyoruz. Ve bu tünele `curl` isteklerini [bu bağlantı](https://everything.curl.dev/usingcurl/proxies/socks.html)dan faydalanarak akıtabiliriz:

```shell
$ ssh -D 192.168.188.36:6000 uzakSunucudakiKullaniciAdi@uzakSunucununIPAdresi
$ curl -x socks5://192.168.188.36:6000  https://www.turkiye.gov.tr/ -vvv
```

Kural Ekleyerek Trafiği Yönlendirmek

Bu konuda epeyce yapılabilecekler var ancak ben belirli bir adrese gelen istekleri socks vekil sunucu olarak açtığım ssh tüneline yönlendirmeyi göstereceğim.

Önce ssh komutuyla tünelimizi açalım (`-vvv` anahtarıyla tüneldeki akışları konsolda görelim ve `-N` anahtarıyla ssh komutunun terminali bırakmamasını sağlıyorum -[sanırım](https://explainshell.com/explain?cmd=ssh+-L+-N+-f+-l)\-) :

```shell
$ sshpass -p sifre123 ssh -D 192.168.188.36:6000 ubuntu@192.168.13.71 -vvv -N
```

Sonra whistle vekil sunucumuzu çalıştıralım:

```shell
$ w2 run -H 192.168.188.36 --uiport 891 --httpPort 777
```

Sonra whistle WEB GUI üzerinde turkiye.gov.tr adresine gelen istekleri az önce açtığımız ssh tüneline yönlendirme kuralını girelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*zn_9_FMIKKTIquR-STVRGg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_zn_9_FMIKKTIquR-STVRGg.webp)

Sonra curl isteğimizi aşağıdaki gibi çalıştıralım:

```shell
$ curl --proxy http://192.168.188.36:777 https://turkiye.gov.tr
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*cCT9LSXmgnW4UIi-TRXQHg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_cCT9LSXmgnW4UIi-TRXQHg.webp)

Whistle GUI üzerinde isteğimizin ssh tüneline gittiğini görebiliyoruz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*ixZvuWmXllmT5NUv8oeLqw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ixZvuWmXllmT5NUv8oeLqw.webp)

Şimdi kuralımızın dışındaki bir adrese istek yapalım `$ curl — proxy [http://192.168.188.36:777](http://192.168.188.36:777/) [https://example.co](https://example.com/)m` ve

-   SSH tünelinde bir hareket olmadığını boşluk satırlarının bıraktığımız gibi kaldığını görerek,
-   WEB GUI’de cevabın 6000 portundan gelmediğini görerek

ispat edelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*NyRb4cWtzqZelJ_2uwWKyQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_NyRb4cWtzqZelJ_2uwWKyQ.webp)

443 Portundan cevabın geldiğini ve hatta SOCKS üzerine yönlendirdiğimiz önceki isteğin lacivert bir metin rengiyle yazdırıldığını da görebilirsiniz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*zGd9qo7f93WxxaApQpOsCw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_zGd9qo7f93WxxaApQpOsCw.webp)

## NodeJS ile Basit HTTPs İstemcisi

Aşağıdaki nodejs kodu ile https paketini kullanarak turkiye.gov.tr adresine istek yapıyoruz.

```js
//calisan/nodejs-https.js

import https from 'https'; // HTTPS modülü
import url from 'url'; // URL modülü

// Hedef URL
const targetUrl = 'https://turkiye.gov.tr';

// URL'yi ayrıştırma
const urlParts = url.parse(targetUrl);

// İstek seçenekleri
const options = {
  hostname: urlParts.hostname,
  path: urlParts.path,
  port: urlParts.port || 443, // HTTPS varsayılan olarak 443 portu kullanır
  method: 'GET', // İstek yöntemi (GET, POST, vb.)
};

// HTTPS istek oluşturma
const req = https.request(options, (res) => {

  // Yanıt durumu kodunu yazdırma
  console.log(`Yanıt Durumu Kodu: ${res.statusCode}`);

  // Başlıkları yazdırma
  console.log('Başlıklar:');
  for (const key in res.headers) {
    console.log(`${key}: ${headers[key]}`);
  }

  // Çıktı akışı (stream) zaten mevcuttur, direkt olarak okuma yapabilirsiniz
  res.on('data', (chunk) => {
    console.log(chunk.toString());
  });

  // Hata durumlarını ele alma
  res.on('error', (err) => {
    console.error('Hata:', err);
  });

  // Çıktı akışı tamamlandığında mesaj yazdırma
  res.on('end', () => {
    console.log('Yanıt Alma İşlemi Tamamlandı.');
  });
});

// Hata durumlarını ele alma
req.on('error', (err) => {
  console.error('İstek Sırasında Hata:', err);
});

// İsteği gönderme
req.end();
```

Bu isteğin whistle vekil sunucusuna düştüğünü bir sonraki örnekte göreceğiz.

## NodeJS ile HTTPs İstemcisi ve Vekil Sunucu Ayarları

Whistle vekil sunucusunu ayaklandıralım:

```shell
$ w2 run -H 192.168.188.36 \
     --uiport 891 \
     --httpsPort 888
```

https Vekil sunucusuna erişmesi için kodu elden geçiriyoruz

```js
// https_client-over-https_proxy.js
import https from 'https'; // HTTPS modülü
import url from 'url'; // URL modülü
import { HttpProxyAgent } from 'http-proxy-agent'; 

// Proxy sunucu bilgileri
const PROXY_HOST = '192.168.188.36'; // Proxy sunucu adresi veya IP adresi
const PROXY_PORT = 888; // HTTPS Proxy sunucu portu
const PROXY_PROTOCOL = 'https:'; // HTTPS protokolü
// Proxy ayarları
const proxyOptions = {
  hostname: PROXY_HOST, // Proxy'nin IP adresi
  port: PROXY_PORT, // Proxy'nin port numarası
  protocol: PROXY_PROTOCOL, // Proxy'nin protokolü (http veya https)
};
// Proxy için HttpProxyAgent oluştur
const proxyAgent = new HttpProxyAgent(proxyOptions);


// Hedef URL
const targetUrl = 'https://example.com';
// URL'yi ayrıştırma
const urlParts = url.parse(targetUrl);
// HTTPS istek seçenekleri
const options = {
  hostname: urlParts.hostname,
  path: urlParts.path,
  port: urlParts.port || 443, // HTTPS varsayılan olarak 443 portu kullanır
  method: 'GET', // İstek yöntemi (GET, POST, vb.)
  agent: proxyAgent, // Proxy ajanı ekleme
};

// Güvenlik nedeniyle yapılan sertifika doğrulamasını ihmal ediyoruz
process.env["NODE_TLS_REJECT_UNAUTHORIZED"]=0

// HTTPS istek oluşturma
const req = https.request(options, (res) => {
  // Yanıt durumu kodunu yazdırma
  console.log(`Yanıt Durumu Kodu: ${res.statusCode}`);

  // Başlıkları yazdırma
  console.log('Başlıklar:');
  const headers = res.headers;
  for (const key in headers) {
      console.log(`${key}: ${headers[key]}`);
  }

  // Veriyi parça parça aldıkça yazacağız
  res.on('data', (chunk) => {
    console.log(chunk.toString());
  });

  // Hata durumlarını ele alma
  res.on('error', (err) => {
    console.error('Hata:', err);
  });

  // Çıktı akışı tamamlandığında mesaj yazdırma
  res.on('end', () => {
    console.log('Yanıt Alındı.');
  });
});

// Hata durumlarını ele alma
req.on('error', (err) => {
  console.error('İstek Sırasında Hata:', err);
});

// İsteği gönderme
req.end();
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*tQa1PlKBLkNym7UEz_1YnA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_tQa1PlKBLkNym7UEz_1YnA.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*-gnYnuJK7dClNGZm4be2Aw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_-gnYnuJK7dClNGZm4be2Aw.webp)

Web arayüzünde isteği görebiliriz

Aynı isteği vekil sunucu üzerinde example.com adresine gelen istekler için socks vekil sunucusuna yönlendirerek tekrar deneyeceğiz

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*oI6AatmpPzAp8RRHXm665A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_oI6AatmpPzAp8RRHXm665A.webp)

example.com socks://192.168.188.36:6000

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*Ozz-UdjrDuY-8rRlgxYTuA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_Ozz-UdjrDuY-8rRlgxYTuA.webp)

## NodeJS İle Basit Http2 İstemci

```js
// nodejs-http2-client.js

import http2 from 'http2';

(async () => {
  try {
    // Bir tünel için HTTP/1.1 CONNECT isteği oluştur:
    const client = http2.connect('http://192.168.13.71:8207');
    client
      .on('error', (err) => console.error(err))
      .on('connect', (res, socket) => { console.error(res) });

    /* İstemciyi kullan */
    const postbody = 'afId=Netflix&grant_type=client_credentials&scope=3gpp-monitoring-event';
    const headers = {
      ':method': 'POST',
      ':path': '/oauth2/token',
      'content-type': 'application/x-www-form-urlencoded',
      'content-length': Buffer.byteLength(postbody),
    };

    /* İstek yap */
    const req = client.request(headers);

    req
      .on('error', (err) => {
        console.error('HTTP/2 isteği hatası:', err);
      })
      .on('response', (headers, flags) => {
        console.log('HTTP/2 yanıt başlıkları:', headers);
      })
      .on('data', (chunk) => {
        console.log('HTTP/2 yanıt parçası:', chunk);
      })
      .on('end', () => {
        client.close();
      });

    req.setEncoding('utf8');
    req.write(postbody);
    req.end();

  } catch (error) {
    console.error(error);
  }
})();
```

```json
// package.json

{
  "name": "http2-client",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
  },
  "type": "module"
}
```

Çıktısı:

```shell
cemt@PC-CEM-TOPKAYA:~/_HTTP2/http2-client$ node nodejs-http2-client.js 
Http2Session {
  type: 1,
  closed: false,
  destroyed: false,
  state: {
    effectiveLocalWindowSize: 65535,
    effectiveRecvDataLength: 0,
    nextStreamID: 1,
    localWindowSize: 65535,
    lastProcStreamID: 0,
    remoteWindowSize: 65535,
    outboundQueueSize: 1,
    deflateDynamicTableSize: 0,
    inflateDynamicTableSize: 0
  },
  localSettings: {
    headerTableSize: 4096,
    enablePush: true,
    initialWindowSize: 65535,
    maxFrameSize: 16384,
    maxConcurrentStreams: 4294967295,
    maxHeaderListSize: 4294967295,
    maxHeaderSize: 4294967295,
    enableConnectProtocol: false
  },
  remoteSettings: {
    headerTableSize: 4096,
    enablePush: true,
    initialWindowSize: 65535,
    maxFrameSize: 16384,
    maxConcurrentStreams: 100,
    maxHeaderListSize: 4294967295,
    maxHeaderSize: 4294967295,
    enableConnectProtocol: false
  }
}
HTTP/2 yanıt başlıkları: [Object: null prototype] {
  ':status': 200,
  date: 'Tue, 14 May 2024 10:51:28 GMT',
  'cache-control': '',
  'content-type': 'application/json',
  pragma: '',
  [Symbol(nodejs.http2.sensitiveHeaders)]: []
}
HTTP/2 yanıt parçası: {"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJkN2QyZTM2Yi1kYmU1LTRmNzktYmJjZC1jMTVmZjIxMzdiY2IiLCJjb25zdW1lclBsbW5JZCI6Int9IiwiZXhwIjoxNzE1NjkzODg4LCJpc3MiOiJkN2QyZTM2Yi1kYmU1LTRmNzktYmJjZC1jMTVmZjIxMzdiY2IiLCJwcm9kdWNlclBsbW5JZCI6Int9Iiwic2NvcGUiOiIzZ3BwLW1vbml0b3JpbmctZXZlbnQiLCJzdWIiOiJOZXRmbGl4In0.rCFIipgA36URqJYwbjRTj6FZDQBH5yg_s7uiyheJd4fGpcAGtAo_74X6LtXr2C8wJgvLc60gj5Y6LKoLRnuDjTZPlSlwmiadTn9zbBKSZwTZ3ZPX6IERIr05-N8qI_Sp7R1YFptpqwZgBH_wy86fam77MhV3dsw24Vowcdsksg5wrPrfNIvZ7muDALPKC8OnPz9F215AO40GUeQGKxWXPcIT2ATbgoOxxXj0uWMFh8_atK9takwWYN5cc8ox39eFfL-vDgaCQAGRKv_irmaDmV9ZGMao0qEaxVRNLypTUIboK9ZG0_pvBCBzP6HvcQEyY_MC2If5voAjNIxDRAFFOg","expires_in":10000,"token_type":"Bearer"}
```

## http2 İsteğini http Vekil -> Socks Proxy Üzerinden Geçirelim

Önce vekil sunucuyu ayaklandıralım:

```shell
w2 run -H 192.168.188.36 --uiport 891 --httpPort 777 --httpsPort 888 --socksPort 999
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*P6-QFeaI1-vTGv4wySbkuQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_P6-QFeaI1-vTGv4wySbkuQ.webp)

w2 run -H 192.168.188.36 — uiport 891 — httpPort 777 — httpsPort 888 — socksPort 999

Şimdi HTTP/2 isteklerini vekil sunucu içinden geçireceğimizi ve Tünel bağlantılarını yakalayacağımızı HTTPS düğmesine basarak açılan ekranda işaretleyelim:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*DtBiuszYlndLGvyWySrNFg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_DtBiuszYlndLGvyWySrNFg.webp)

Her iki seçeneği aktif ederek http2 trafiğinin faal edilmesi ve tünellenmesini mümkün kılalım

Şimdi kuralımızı yazalım ve böylece 10.10.23.53 adresli sunucuya ancak VPN ile bağlanabildiğim için `ssh -D 6000 ubuntu@192.168.13.53` ile socks (_TCP/UDP isteklerini yönlendirebileceğimiz_) vekil sunucu olarak çalıştıralım.

```shell
ssh -D 192.168.188.36:6000 ubuntu@192.168.13.71 -vvv -N
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*ftVSkfP-s8xIb9i7_pc-2A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ftVSkfP-s8xIb9i7_pc-2A.webp)

`10.10.23.53` Adresinde ngHttp2 kütüphanesini kullanarak http2 çalışan sunucumuza yapacağımız http2 isteklerini aşağıdaki kural ile WSL olarak çalışan Ubuntu sanal makinemizin (`192.168.188.36`) IP adresinin 6000 portuna yönlendirmek istediğimizi kurallara ekleyelim.

> [http://10.10.23.53:8207/](http://10.10.23.53:8207/oauth2/token) socks://192.168.188.36:6000

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*nFkgIzXvXCbC1_BSfh3hww.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_nFkgIzXvXCbC1_BSfh3hww.webp)

Nodejs ile http2 istekleri yapabileceğimiz, içinde vekil sunucu ayarları olan haliyle kodumuzu koşturalım:

```js
import http2 from 'http2';
import http from 'http';
import url from 'url';

// Proxy URL ve Hedef URL
const proxyUrl = 'http://192.168.188.36:777';
const targetUrl = 'http://10.10.23.53:8207/oauth2/token';

// Proxy ve Hedef URL'leri parse et
const proxy = url.parse(proxyUrl);
const target = url.parse(targetUrl);

// Proxy ayarları
const proxyOptions = {
  host: proxy.hostname,
  port: proxy.port,
  headers: {
    // 'Proxy-Authorization': `Basic ${auth}` // Kimlik doğrulaması gerekmiyorsa yoruma alınabilir
  }
};

// HTTP proxy üzerinden CONNECT yöntemiyle bir tünel oluşturuyoruz
const req = http.request({
  ...proxyOptions,
  method: 'CONNECT',
  path: `${target.hostname}:${target.port}` // Proxy'ye hedef sunucu bilgilerini veriyoruz
});

req.on('error', (err) => {
  console.error(`Proxy request error: ${err.message}`);
});

req.on('connect', (res, socket, head) => {
  if (res.statusCode !== 200) {
    console.error(`Proxy connection failed with status code: ${res.statusCode}`);
    return;
  }

  // HTTP/2 istemcisi oluşturma
  const client = http2.connect(`http://${target.hostname}:${target.port}`, {
    createConnection: () => socket
  });

  client.on('error', (err) => {
    console.error(err);
  });

  const payload = 'afId=Netflix&grant_type=client_credentials&scope=3gpp-monitoring-event';

  // HTTP/2 POST isteği oluşturma
  const req2 = client.request({
    ':method': 'POST',
    ':path': target.path,
    'content-type': 'application/x-www-form-urlencoded',
    'content-length': Buffer.byteLength(payload)
  });

  req2.setEncoding('utf8');
  req2.write(payload);

  req2.on('response', (headers, flags) => {
    console.log('Yanıt Başlıkları:', headers);

    req2.on('data', (chunk) => {
      console.log('Yanıt Verisi:', chunk.toString());
    });

    req2.on('end', () => {
      console.log('Yanıt Alındı.');
      client.close();
    });
  });

  req2.on('error', (err) => {
    console.error(`Server request error: ${err.message}`);
  });

  req2.end();
});

req.end();
```

İşte sonuç. İstek bir tünel ile vekil sunucu üzerinden socks tüneline aktığını görüyoruz:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*I4wyuFdK_M-vMHoSXvuWbg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_I4wyuFdK_M-vMHoSXvuWbg.webp)

## got Kütüphanesiyle Socks Proxy Üzerinden

got Kütüphanesi ile istekler yapılacak socks-proxy-agent ile socks tüneline istek yönlendirilecek

```js
/**
 * Öncesinde `ssh -D 6000 ubuntu@192.168.13.71` bir terminalde açılmalı
 */

import got from 'got';
import { SocksProxyAgent } from 'socks-proxy-agent';

const proxyOptions = {
  protocol: 'socks5',
  hostname: 'localhost',
  port: 6000,
  username: null,
  password: null
};

let agent = {
  // HTTP request
  http: new SocksProxyAgent(proxyOptions, { tunnel: true, timeout: 5000 })
};

const options = {
  url: 'http://10.10.23.53:81', // HTTP request
  agent: agent
};

(async () => {
  try {
    const response = await got.get(options);
    console.log(response.body);
  } catch (error) {
    console.error(error);
  }
})();
```

## NOTLAR

Kendinden imzalı sertifika oluşturmak:

```shell
$ openssl req -x509 \
     -newkey rsa:4096 \
     -keyout key.pem \
     -out cert.pem \
     -sha256 \
     -days 3650 \
     -nodes \
     -subj "/C=XX/ST=StateName/L=CityName/O=CompanyName/OU=CompanySectionName/CN=CommonNameOrHostname"
```