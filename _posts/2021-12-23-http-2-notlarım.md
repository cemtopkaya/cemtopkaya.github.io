---
layout: post
title:  "Http 2 Notlarım"
date:   2021-12-23 11:57:24 +0000
categories: http2
tags: http2
---
2015 Yılında Chrome Dev Summit’in [videosunu](https://www.youtube.com/watch?v=r5oT_2ndjms) izlemenizi kronolojik bilgi için salık veririm.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*FgM6nrU3D7sp-6wIgg3c5g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_FgM6nrU3D7sp-6wIgg3c5g.webp)

Terimler

-   h2 : http/2
-   h1 : http/1.1
-   h2c : Şifrelenmemiş http2 trafiği
-   [hpack](https://youtu.be/r5oT_2ndjms?t=843) : Başlık bilgisi için sıkıştırma. Sıkıştırma ve açma için notasyonu belirler. http/1.x için başlık bilgileri soldaki gibiyken h2'de aşağıdaki resimde olduğu gibi ilk 62 satıra anahtar değer ikililerinden oluşan bir arama tablosu halinde geliyor.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*DYPQgodw5pAOQq84OaGNBA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_DYPQgodw5pAOQq84OaGNBA.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*gixUpHdlPHQSxqSeDpV5Rg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_gixUpHdlPHQSxqSeDpV5Rg.webp)

<!-- ![](https://miro.medium.com/v2/resize:fit:466/1*WC4_48DeLw1Ig-mnOFUwsQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_WC4_48DeLw1Ig-mnOFUwsQ.webp)

Http2'nin avantajları:

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*3vMzcw6w8wj46cB6o32yAg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_3vMzcw6w8wj46cB6o32yAg.webp)

-   Tek bir bağlantı üstünde çoklu veri akışı (akışları `Stream ID` ile etiketliyor). Bu akışlarda istemci bağımlılık belirleyebiliyor.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*JfJAtHh_L3qdvr0rQgxM6A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_JfJAtHh_L3qdvr0rQgxM6A.webp)

-   Başlıkları ve veriyi [sıkıştırıyor](https://youtu.be/r5oT_2ndjms?t=1163). SPDY sürümünde sıkıştırma varsayılan olarak herşey için yapılıyordu ancak sıkıştırılmış dosyaları (jpeg, gif vs.) da sıkıştırmanın anlamsız olduğu görülerek seçimli hale getirildi.
-   Sunucu veriyi istemciye [basabiliyor](https://youtu.be/r5oT_2ndjms?t=1060) (istemcinin çekmesinden -pull- farklı). Buradaki push klasik anlamda bildirim push’u değil, yapılmayan isteklerin cevaplarının push ile verilmesidir. Örneğin index.html sayfası için istek yapıldığında, sunucunun “sen şimdi istemedin ama `main.css` ve `resim.png` dosyalarına da ihtiyacın olur, hepsini push ile göndereyim” demesidir. Elbette istemci bu push’ları iptal edebilir. Çünkü sunucu push mekanizmasını istismar ediyor olabilir!
-   TLS yapısında extension desteği verir. Böylece TLS kurulurken ek olarak işler yaptırabiliriz. Application Layer Protocol Negotiation bu uzantılardandır ve TLS kurulurken çalışabilir. Sunucunun Http2 desteklediğini ilk istekte bilemeyiz ama TLS el sıkışması sırasında bunu öğrenebiliriz. Browser (uygulama) ile sunucu arasında http2 veri transferini ALPN ile yapabiliriz.