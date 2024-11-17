---
layout: post
title:  "Metin, veri mesaj akışları (Data Message Flows — DMF)"
date:   2024-09-19 11:57:24 +0000
categories: Test Otomasyonu, Testing Automation
tags: DMF Testing Fireball
---
**DMF (Data Message Flow)**, yazılım testinde, özellikle ağ ve iletişim sistemlerinin test edilmesinde kullanılan güçlü bir araçtır. Basitçe söylemek gerekirse, DMF, bir uygulama veya sistemin ağ üzerinden nasıl iletişim kurduğunu tanımlayan ve simüle eden bir modeldir.

**DMF’in temel amacı:**

*   **Veri trafiğini simüle etmek:** Bir ağ üzerindeki gerçekçi bir veri trafiğini taklit etmek için çeşitli protokoller (HTTP, FTP, SMTP vb.) ve veri türleri (metin, dosya, video vb.) kullanarak farklı senaryolar oluşturur.
*   **Performans ölçümü:** Sistemin belirli bir yük altında nasıl davrandığını (gecikme, bant genişliği kullanımı, hata oranları vb.) ölçmek için kullanılır.
*   **Hata tespiti:** Sistemde beklenmedik davranışlar veya hatalar olup olmadığını tespit etmek için kullanılır.
*   **Güvenlik testi:** Sistemin güvenlik açıklarına karşı ne kadar dirençli olduğunu test etmek için kullanılır.

**DMF’nin Kullanım Alanları:**

*   **Ağ cihazlarının testi:** Router, switch, firewall gibi ağ cihazlarının performansını ve işlevselliğini test etmek.
*   **Uygulama testleri:** Web uygulamaları, mobil uygulamalar, VoIP sistemleri gibi uygulamaların ağ performansını ve güvenliğini test etmek.
*   **Protokollerin testi:** Yeni veya mevcut protokollerin uyumluluğunu ve performansını test etmek.
*   **Sistem entegrasyon testleri:** Farklı sistemlerin birbiriyle nasıl etkileşim kurduğunu ve veri alışverişini test etmek.

1.  _Veri Mesaj Akışı (_**_DMF_**_)_ **_nedir_**_?_ DMF, bir test senaryosuna eklenebilen ve çalıştırılabilen bağımsız bir veri trafik modeli tanımıdır. Bu, ağ testleri yaparken farklı **veri protokollerini simüle etmek için kullanılır**.
2.  _DMF’lerin_ **_temel özellikleri nelerdir_**_?  
    \-_ Test Kütüphanesi’nin kendi bölümlerinde saklanırlar ve test sistemi ile birlikte çeşitli veri protokolleri için bir dizi varsayılan DMF sağlanır.  
    \- Çeşitli veri protokolleri için varsayılan DMF’ler mevcuttur.  
    \- Yeni DMF’ler oluşturabilir veya varsayılan DMF’leri değiştirebilir ve bunları kaydedebilirsiniz. Böylece herhangi bir veri işleme yeteneğine sahip test senaryosunda kullanabileceğiniz standart modeller oluşturabilirsiniz.
3.  _DMF yetenekleri nasıl sınıflandırılır?_  
    **a) Temel Veri Kapasitesi:** PING, RAW, UDP, TCP ve SCTP olmak üzere beş veri türü sağlar. Varsayılan DMF, her biri için MN ve Ağ Ana Bilgisayarı arasında sürekli olarak 64 baytlık verilerle saniyede 1 işlem hızında mesaj alışverişi yapacak şekilde yapılandırılmıştır.  
    **b)** **Gelişmiş Veri Kapasitesi:** Bu özellik, seçebileceğiniz veri protokollerini daha karmaşık, duruma bağlı veri protokolleri ve e-posta gönderme/alma, FTP dosya indirme, anlık mesajlaşma, akış verileri ve web gezinme gibi ortak etkinlikleri gerçekleştiren bir dizi varsayılan DMF ile genişletir.  
    **c)** **Lite Veri Mesaj Akışı:** Lite DMF, Gelişmiş DMF’nin basitleştirilmiş bir sürümüdür ve sonuçta Test Sunucusu’nda gelişmiş bir DMF olarak çalışır. Bu nedenle, tüm ölçümler Gelişmiş DMF ölçümleridir.  
    **d) Fireball Veri Mesaj Akışı:** Fireball modu, belirli temel DMF veri türü ve taşıma protokolleri için maksimum veri düzlemi (Data Plane) performansına yöneliktir.  
    **e) Özel Veri Mesaj Akışları (Duruma Bağlı Protokoller):** Bunlar yukarıda listelenen bölümlerin (Temel, Gelişmiş, Fireball) hiçbirine girmeyen birkaç duruma bağlı protokol mesaj akışını destekler.

[k6](https://k6.io/) İle Yük Testi
----------------------------------

[k6](https://k6.io/), ücretsiz olarak kullanılabilen, modern yük test araçlarından biri olarak, DMF (Data Message Flow) benzeri yapıları kullanarak oldukça karmaşık ve gerçekçi test senaryoları oluşturmamıza olanak tanır.

k6, JavaScript tabanlı bir araç olduğu için, oldukça esnek bir yapıya sahiptir. Bu esneklik sayesinde, HTTP isteklerini özelleştirerek, farklı veri tiplerini göndererek ve karmaşık senaryolar oluşturarak, DMF’nin sunduğu özelliklere oldukça yakın testler tasarlayabiliriz.

```
import http from 'k6/http';
export default function () {
    // Bir ürünün detay sayfasını açmak
    let res = http.get('https://www.example.com/products/123');
    // Ürünü sepete eklemek
    let res2 = http.post('https://www.example.com/cart', JSON.stringify({ productId: 123 }));
    // Sepeti görüntülemek
    let res3 = http.get('https://www.example.com/cart');
}
```
