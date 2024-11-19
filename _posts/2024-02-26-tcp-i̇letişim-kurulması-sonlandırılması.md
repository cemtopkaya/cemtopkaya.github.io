---
layout: post
title:  "TCP İletişiminin Kurulması & Sonlandırılması"
date:   2024-02-26 11:57:24 +0000
categories: Networking
tags: tcp tcpdump netstat nc socat netstat
---
# TCP Paketinin Yapısı

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*uLGaxXzb81vib4ZuqMa3kQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_uLGaxXzb81vib4ZuqMa3kQ.webp)

## 3‘lü El Sıkışarak TCP İletişimi Başlatmak [\*](http://intronetworks.cs.luc.edu/1/html/tcp.html) ([TCP 3-Way Handshake Process](https://www.geeksforgeeks.org/tcp-3-way-handshake-process/))

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*XhFoGJtjE6vj5uufGnrOOg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_XhFoGJtjE6vj5uufGnrOOg.webp)

İki makine arasındaki ilk TCP bağlantısı 3 hareketten oluşuyor.

1.  Paket (istek): “`benimle senkronize olur musun?`” (**SYN**),
2.  Paket (cevap)“`Senkronize olmak mı! Elbette`” (**SYN-ACK**),
3.  Paket (mühür)“`Anlaştık ciğer parem`” (**ACK**)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*Upp6fa17qnPllb9brLsiIw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_Upp6fa17qnPllb9brLsiIw.webp)

TCP, bir bağlantı açmak için üç durumlu bir sisteme sahiptir. Gönderilen paketlerin bir adı olduğu gibi tarafların durumları vardır. Örneğin A kişisi borç para ister (borç verilecek para=SYN paketi) ve durumu “isteyen” olurken parayı aldığı anda “borçlu” parayı veren ise “alacaklı” (SYN\_SENT, SYN\_RCVD ve ESTABLISHED) durumuna geçmiş olur.

1.  **SYN** Paketi ilk olarak, başlangıç noktasından (A), hedefe (B) gönderilir. Bu durumda A makinesi embriyonik (_yarı açık_) TCP bağlantısı durumunda (A makinesi: **_SYN\_SENT_**, B makinesi: **_SYN\_RCVD_** _durumunda_) ve bir yanıt bekliyor.
2.  **SYN-ACK** paketini B makinesi A’ya cevap olarak gönderir.
3.  **ACK** mesajını A makinesi cevap olarak gönderir (her iki makine **_ESTABLISHED_** durumuna geçer)

Biraz daha ayrıntılı bakalım:

1.  İstemci gidip sunucuya der ki; “_gel bir iletişim başlatalım_” ve `SYN (seq:100)` gider.
2.  Sunucu der ki; “_isteğini gördüm ve kabul ettim_” ve `SYN-ACK (seq:200)` gönderir.
3.  İstemci der ki; “_anlaştık_” ve `SYN (seq:101)` gönderir.

![alt text]({{ BASE_PATH }}/assets/images/0_RAfllxC5MFs97FDV.webp)

![alt text]({{ BASE_PATH }}/assets/images/0_rydEUCOMd-Jz9WzJ.webp)

“Normal” TCP bağlantısı koparılması 4 yönlü bir el sıkışma ile gerçekleştirilir. Özellikle, kurulu bir TCP bağlantısının sonlandırılması için aşağıdaki 4 TCP paket gönderilip alınır:

![alt text]({{ BASE_PATH }}/assets/images/0_CsLIgjuD_fvOT8gx.webp)

![alt text]({{ BASE_PATH }}/assets/images/1_tt-TnZaHBxpWWpOFA6zQ7A.webp)

## Tüm TCP Akışı

**Synchronization (SYN)**

Bağlantı kurma aşamasının ilk adımında veya iki ana bilgisayar arasında 3 yönlü el sıkışma işleminde kullanılır. Yalnızca göndericiden ve alıcıdan gelen ilk paket bu bayrak ayarına sahip olmalıdır. Bu, sıra numarasını senkronize etmek için, yani diğer uca hangi sıra numarasını hariç tutmaları gerektiğini söylemek için kullanılır.

**Acknowledgement (ACK)**

Ana bilgisayar tarafından başarıyla alınan paketleri onaylamak için kullanılır. Onay numarası alanı geçerli bir alındı numarası içeriyorsa bayrak ayarlanır. Aşağıdaki diyagramda, alıcı, gönderene ilk paketini aldığını bildirmek için bağlantı kurulumunun ikinci adımında bir ACK = 1 ve SYN = 1 gönderir.

**Finish (FIN)**

Bağlantının sonlandırılmasını talep etmek için kullanılır, yani gönderenden daha fazla veri gelmediğinde, bağlantının sonlandırılmasını talep eder. Bu, gönderen tarafından gönderilen son pakettir. Ayrılmış kaynakları serbest bırakır ve bağlantıyı nazikçe sonlandırır.

**Reset (RST)**

RST göndericisi TCP bağlantısında bir sorun olduğunu veya görüşmenin olmaması gerektiğini düşündüğünde bağlantıyı sonlandırmak için kullanılır. Paket onu beklemeyen belirli bir ana bilgisayara gönderildiğinde alıcı tarafından gönderilebilir.

**Push (PSH)**

Aktarım katmanı varsayılan olarak uygulama katmanının maksimum segment boyutuna eşit yeterli veriyi göndermesini bekler, böylece ağ üzerinden iletilen paket sayısı en aza indirilir ki bu, etkileşimli uygulamalar gibi bazı uygulamalar tarafından istenmez (sohbet). Benzer şekilde alıcı ucundaki taşıma katmanı paketleri tamponlar ve belirli kriterleri karşılıyorsa uygulama katmanına iletir. Bu sorun PSH kullanılarak çözülür. Taşıma katmanı PSH = 1'i ayarlar ve uygulama katmanından sinyal alır almaz segmenti hemen ağ katmanına gönderir. Alıcı taşıma katmanı, PSH = 1 görüldüğünde verileri hemen uygulama katmanına iletir. Genel olarak, alıcıya bu paketleri arabelleğe almak yerine alındıkları gibi işlemesini söyler.

**Urgent (URG)**

Uygulama katmanına verilecek daha fazla veri olsa bile, URG = 1 bayrağına sahip bir segment içindeki veriler hemen uygulama katmanına iletilir. Diğer tüm paketleri işlemeden önce alıcıya acil paketleri işlemesini bildirmek için kullanılır. Alıcı, bilinen tüm acil veriler alındığında bilgilendirilecektir.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/0*7eY-zZLyGnYd4v38) -->
![alt text]({{ BASE_PATH }}/assets/images/0_7eY-zZLyGnYd4v38.webp)

```shell
PS C:\> while(1) { netstat -n | grep 192.168.13.33; sleep 0.1 }  TCP    10.212.134.68:52631    192.168.13.33:22       SYN_SENT  TCP    10.212.134.68:52631    192.168.13.33:22       ESTABLISHED  TCP    10.212.134.68:52631    192.168.13.33:22       ESTABLISHED  TCP    10.212.134.68:52631    192.168.13.33:22       ESTABLISHED
```

Tabi bu hareket çekmeleri bayraklarla (`flag`) renklendirerek anlamlandırıyorlar. Peki nedir bu TCP bayrakları TCP paketinin başlığında nerededir ve ne anlama geliyorlar?

<!-- ![](https://miro.medium.com/v2/resize:fit:410/1*Z8x4fNJNz7JR1jg_91KKlA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_Z8x4fNJNz7JR1jg_91KKlA.webp)

**SYN** — Senkronizasyon — **Birlikte hareket etme** anlamına gelen TCP bayrağıdır. Bir paketin başarılı bir şekilde alındığını onaylamak için kullanılır. Alıcı, göndericiye ilk paketini aldığını bildirmek için 3 işleminin ikinci aşamasında bir _ACK_ ve bir _SYN_ gönderir. Yani SYN istediğini aldım (ACK) ve ben de birlikte hareket etmek istiyorum (SYN)

**FIN** — “**Tamamlandı**” anlamına gelen FIN bayrağı gönderende daha fazla veri olmadığı anlamına gelir. Bu nedenle, göndericiden gönderilen son pakette kullanılır.

**URG** — URG bayrağı, diğer paketleri işlemeden önce acil paketleri işleme alması için alıcıyı bilgilendirmekte kullanılır. Bilinen tüm acil veriler alındığında alıcıya bildirilecektir. PSH ve URG farkını anlamak için [tıklayınız](https://packetlife.net/blog/2011/mar/2/tcp-flags-psh-and-urg/).

**PSH** — Push anlamına gelen PSH bayrağı, URG bayrağına biraz benziyor ve alıcıya bu paketleri paketlemek yerine aldıkları gibi işleme koymasını söylüyor. URG durumunda, tüm veriler ağ katmanına (gönderen) ve uygulama katmanına (alan) gönderilmez, yalnızca acil baytlar aciliyetle ele alınırken, PSH durumunda, tüm veriler aciliyetle ele alınır. URG durumunda, veriler sırasız olarak iletilir, ancak PSH’de sırayla iletilir.

**RST** — Sıfırlama anlamına gelen RST bayrağı, bir paket beklemeyen belirli bir ana bilgisayara bir paket gönderildiğinde alıcıdan gönderene gönderilir.

**ECE** (ECHO) — Bu bayrak, TCP eşinin ECN yeteneğine sahip olup olmadığını göstermekle sorumludur .

**CWR** — Tıkanıklığın Azaltılması anlamına gelen CWR bayrağı, gönderen kişi tarafından ECE bayrağı ayarlanmış bir paket aldığını belirtmek için kullanılır.

**raw** — Paketlerin göndericiden yanlışlıkla kötü niyetli olan kişilerden gizlenmesini sağlamak için kullanılan **deneysel** bir bayraktır.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*qWo0-DQlsZXH4zsQ4PpgAw.gif) -->
![alt text]({{ BASE_PATH }}/assets/images/1_qWo0-DQlsZXH4zsQ4PpgAw.gif)


![alt text]({{ BASE_PATH }}/assets/images/0_Gv9b-vmgK5UGBDDs-1.webp)

# TCP Bağlantısını Sonlandırmak [\*\*](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#Connection_termination)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*U38e4Zv18M94eWzQefmxWQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_U38e4Zv18M94eWzQefmxWQ.webp)

Bağlantı iki uçludur (A makinesiyle B makinesi arasında). Bağlantı iki türlü sonlandırılabilir:

1.  **TCP RST**: Sadece bir uç noktası için sonlandığı (**yarı açık-**[_half-open_](https://en.wikipedia.org/wiki/TCP_half-open)_\-_ TCP bağlantısı)
2.  **RCP FIN**: Her iki uç noktasının sonlandırdığı

> **Yarı açık** (half-open) terimi, muhtemelen bir tarafın çökmesi nedeniyle, iletişim kuran iki ana bilgisayar arasındaki birlikte çalışamadıkları (senkronizasyon dışı) durumu anlatan **TCP bağlantıları**dır.
> 
> [RFC 793](https://tools.ietf.org/html/rfc793)'e göre, bir TCP bağlantısının bir ucundaki ana bilgisayar çöktüğünde veya diğer uca haber vermeden bağlantıdan ayrıldığında TCP bağlantısına **yarı açık TCP bağlantısı** denir. Kalan uç boşta ise, bağlantı sonsuza kadar yarı açık durumda kalabilir.
> 
> Ancak günümüzde, yarı açık bağlantı terimi çoğunlukla **embriyonik** bir bağlantıyı, yani kurulma sürecinde olan bir TCP bağlantısını tanımlamak için kullanılmaktadır.

Her iki uç noktası için bağlantı sonlandırma, bağlantının her iki tarafının anlaşmalı olarak dört yönlü bir el sıkışma yöntemiyle (_four-way handshake_) bağlantıyı sona erdirmesinde kullanır.

İlk FIN’i gönderen taraf, son ACK ile yanıt verdikten sonra, bağlantıyı nihai olarak kapatmadan önce bir zaman aşımı için bekler, bu sırada yerel bağlantı noktası yeni bağlantılar için kullanılamaz; bu, sonraki bağlantılar sırasında iletilen gecikmiş paketler nedeniyle karışıklığı önler.

## İncelikli/Barışçıl Sonlandırma (Graceful Termination)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*k1M5ubrEk2WqleqGmDAdQQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_k1M5ubrEk2WqleqGmDAdQQ.webp)

Bir **TCP** bağlantısı, **iki yönlü akıştır**. Örneğin, ana bilgisayarlar A ve B arasında bir TCP bağlantısı varsa, bir akış A’dan B’ye ve diğer akış B’den A’ya doğru olur.

**TCP güvenilir bir protokoldür** (yani herhangi bir aşamada mesaj kaybı olmaz), bu nedenle mesaj kaybı olmadan bağlantıyı sonlandırmak için bir protokol prosedürü gerektirir. Çünkü iki taraf için (A ve B bilgisayarları örneğindeki gibi) bağlantının dostça kapatılması gerekir tıpkı bir telefon konuşmasında her iki tarafın birbirine “güle güle” dediğini duyması gibi.

-   Veri gönderen taraf, “`akış bitti, bağlantıyı kapatmak istiyorum`” diyerek bağlantıyı kapatmak isteyebilir.
-   Veya veriyi alan taraf “`artık gönderme, bağlantıyı kapatmak istiyorum`” diye **FIN-ACK** talebi gönderebilir.
-   **Sonlandırma isteği gönderen taraf ARTIK VERİ GÖNDEREMEZ!**
-   Sonlandırma talebini alan uç nokta ACK ve FIN-ACK paketlerini gönderirse, o da artık veri gönderemez hale gelir ve bağlantı kapanır.
-   Ancak FIN-ACK isteği alan uç nokta da sonlandırma isteği göndermeyip veri göndermeye devam edebilir. Bu durumda bağlantı **yarı açık** (_half-open_) veya yarı kapalı (_half-closed_) TCP bağlantısı olarak tanımlanır.
-   Bitiş talebi **FIN** yahut **FIN-ACK** paketiyle başlar [\*](https://cs.stackexchange.com/a/76422). Her iki şekilde kapanış yapılabilir.
-   Sonunda bağlantının 2 FIN ve 2 ACK ile sonlandığını görmelisiniz. Bazen bir tarafın bağlantıyı oldukça sert bir sıfırlama bayrağıyla kapattığı FIN — ACK — RST’yi de görürsünüz. Yalnızca bir FIN bayrağınız varsa (ve RST yoksa) bağlantınız teknik olarak hala açıktır.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*abK6uK3q105__2SJ9rz6Bg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_abK6uK3q105__2SJ9rz6Bg.webp)

1.  **FIN** veya **FIN-ACK** Paketi A bilgisayarından B’ye gönderildiğinde, B “artık A’nın bana gönderecek verisi kalmamış” diye anlar ve
2.  **ACK** paketi göndererek “seni anladım” der. Ardından B bilgisayarı
3.  **FIN** veya **FIN-ACK** paketi göndererek “benim de söyleyecek sözüm kalmadı” der.
4.  **ACK** paketiyle A bilgisayarı B’ye “seni anladım” der.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*kJXeEdZojOoVixr7USK67g.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_kJXeEdZojOoVixr7USK67g.webp)

Yukarıda alış verişi yapılan paketleri gördük. Bu paket değişimlerinde tarafların durumları da değişir tıpkı birinden borç aldığınızda siz “**_borçlu_**” karşı taraf ise “**_alacaklı_**” durumuna gelirken, siz borcunuzu ödediğinizde her iki tarafın durumlarının/tanımlarının değişmesi gibi. `Netstat` komutuyla durum değişimlerini gözlemleyebilirsiniz.

<!-- ![](https://miro.medium.com/v2/resize:fit:684/0*W3mB2Vh0ZgxN9BfU.png) -->
![alt text]({{ BASE_PATH }}/assets/images/0_W3mB2Vh0ZgxN9BfU.webp)

[
![alt text]({{ BASE_PATH }}/assets/images/0_q-mgz1_KiUB862Au.webp)
](https://benohead.com/blog/2013/07/21/tcp-about-fin_wait_2-time_wait-and-close_wait/)

## Ani Kapanma (RST)

Bağlantının ev sahibi (sunucu) **_8889_** portunda `ncat` uygulaması (soldaki komut satırı) ve bağlanan istemci **_55964_** portunda (sağdaki komut satırı) çalışan `ncat` uygulaması. İstemci makine `**Ctrl+C**` ile aniden bağlantıyı kopardığında sunucuya **RST** paketi gidiyor.

<!-- ![](https://miro.medium.com/v2/resize:fit:1000/1*LB4zhcg9nVL_nlAa05oVwg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_LB4zhcg9nVL_nlAa05oVwg.webp)