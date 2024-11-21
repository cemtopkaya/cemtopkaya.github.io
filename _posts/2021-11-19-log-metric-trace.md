---
layout: post
title:  "Log — Metric — Trace"
date:   2021-11-29 11:57:24 +0000
categories: log metric trace
tags: log metric trace
---
Diagnostics yani tanılamanın ne olduğu hakkında konuşarak başlayalım. Teşhis, pratik veya yöntemlere denir ve bir hastalığın veya sorunun kesin nedenini keşfetme veya tanımlama eylemidir.

Yazılım geliştirme açısından, uygulamalar her zaman beklediğimiz gibi davranmaz. Bu beklenmedik davranışın nedenini teşhis edebilmeliyiz. Bunu yapmak için, çalışan kodu görmeli ve sorunun nerede meydana geldiğini ve hangi faktörlerin soruna dahil olduklarını belirlemeliyiz. Geliştirme sırasında, bu oldukça basittir. Ancak bir uygulama dağıtıldığında, çok daha zor hale gelir ve özellikle tanılama için fazladan kod ekleyerek bunu takip edebilir hale gelirsiniz.

İhtiyacımız olan bilgi türü, `call stack` \`da dahil olmak üzere istisna verileri olabilir, ancak genellikle, o sırada uygulamada neler olup bittiğine dair daha büyük bir bağlama ihtiyacımız var; bu, sorun zamanında belirli veri parçalarının değerlerini içerebilir:

-   Hatanın bilgisi veya stack trace bilgileri,
-   Verinin o anda ne olduğu bilgisi,
-   Kodun izlediği yolu anlamamız gerekebilir
-   Karmaşık bir süreçteki bir performans sorununu izlememiz gerekebilir,
-   Belki birden çok sunucuyu veya işlemi kapsayan bir süreç bu sorunun nedeni olabilir.

Bu durumda, faal durumdaki yazılımın sürecinde hangi işlemin sürdüğünü belirlemek isteyeceğiz.

> Tanılama bilgilerini toplamanıza izin veren kodun oluşturulmasına, **kodunuzun enstrümantasyonu** denir.

**Neden Trace Etmeliyiz?**

Uygulama canlı ortamına yüklendikten sonra enstrümantasyon özellikle önemlidir. Uygulamanız, beklemediğiniz verileri giren ve uygulamayı tahmin etmediğiniz şekillerde kullanan birçok kullanıcı tarafından kullanılan bir ortama dağıtıldığında, genellikle beklenmeyen ve test edilmeyen uç durumlar söz konusu olur. Kodunuzu denetlemeyi, sorun oluştuğunda kodun içinde neler olduğunu öğrenmenin bir yolunu “trace” sunacaktır.

Günlükleri gerçek zamanlı olarak yayınlamak mümkün olsa da, bazı durumlarda (örneğin Azure App Service’te çalışan bir uygulama gibi), gelen günlükler ve izlemeler (`traces`), ister sunucuda ister merkezi bir konumda olsun, genellikle bir yerde depolanır ve bu size _bir sorunu araştırmak için zamanda geriye gitme imkanı_ verir.

Ancak tanılama (`diagnostic`), yalnızca canlı ortam uygulamaları için değildir. Geliştirme sırasında da önemlidir. Elbette, geliştirirken sorunları keşfetmek ve düzeltmek çok daha kolaydır ve bu bazen kodda kesme noktaları (`breakpoints`) ayarlamak ve ardından Visual Studio gibi entegre geliştirme ortamında bir hata ayıklayıcı (`debugger`) ile adım adım çözmek kadar kolay olabilir. Ardından değişkenleri inceleyebilir, hesaplamaları kontrol edebilir ve kodun izlediği yolu görebilirsiniz. Ancak geliştirme sırasında bile bu hızla yapılamayacak kadar karmaşık hale gelebilir. Çok iş parçacıklı uygulamalarda hata ayıklayıcıyı doğru parçacığa iliştirmek (`attach`) de zor olabilir. Hata ayıklayıcıyı iliştirmek, özellikle diğer hizmetleri çağırırken zaman aşımı hatalarına da neden olabilir.

Uygulamaların ve sistemlerin gözlemlenebilirliği hakkında konuşalım.

> Gözlenebilirlik (`observability`), yalnızca izlemek istediğiniz bir sistemin içinden verileri kullanılabilir hale getirmek anlamına gelir.

Bu genellikle üç gözlemlenebilirlik sütunu olarak tanımlanır:

1.  Günlükler (`logs`)
2.  Ölçümler (`metrics`)
3.  İzlemeler (`traces`)

Bunlar, izlenen bir sistemin yeterli gözlemlenebilirliğini sağlamak için toplanması ve analiz edilmesi gereken temel olarak farklı veri türleridir.

**Günlükler**, bir sistem içindeki _olaylardır_. Bir zaman damgası ve bir olayla ilgili bağlamı içeren veri yükü vardır. Yani olayın verisini taşırlar.

> Günlükler, çalışan bir uygulamanın davranışına ilişkin görünürlük sağlar. Sunucu tabanlı ortamlarda genellikle diskteki bir dosyaya (“günlük dosyası”) yazılırlar; ancak bu yalnızca bir çıktı biçimidir.[\*](https://12factor.net/logs)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*naP8kSZLVUWNYVE78GtkYw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_naP8kSZLVUWNYVE78GtkYw.webp)

Her satır bir günlük kaydıdır

Genellikle düz metin veya bölümlenmiş alanlara (; tab | veya farklı ayıraçlara bölümlenmiş alanlara) sahip yapılandırılmış biçimde gelirler. Bu alanlar günlükleri aramayı ve filtrelemeyi kolaylaştırır.

Bir günlük içindeki veri blokları:

-   Zaman damgası
-   Kategori
-   Günlük seviyesi
-   Veri

> Sunucu; arka plan programları (PostgreSQL veya Nginx gibi) ve diğer uygulamalar (Rails veya Django uygulamaları gibi) bazen programın günlük dosyasına giden yolu bir yapılandırma parametresi olarak ister. Bu parametreye bakarak günlükleri dosya olarak düşünseniz de daha iyi bir kavram, günlükleri **zamana göre sıralanmış akışlar** olarak ele almaktır: başlangıç veya sonu yoktur, bunun yerine gerçek zamanlı olarak (örneğin `tail -f` aracılığıyla) görmek isteyebileceğimiz yerden itibaren devam eden, harmanlanmış bir olaylar koleksiyonudur).[\*](https://adam.herokuapp.com/past/2011/4/1/logs_are_streams_not_files/)

Günlük seviyeleri çeşitli anlamlar içerir. [.Net](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel?view=dotnet-plat-ext-6.0) için, [Java Spring](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/logging/LogLevel.html) için ve [C](https://gist.github.com/fclairamb/7441750) [+](http://cplusplus.com/forum/beginner/45541/) [+](https://www.sentinelone.com/blog/getting-started-quickly-cplusplus-logging/) için ilgili bağlantılara tıklayabilirsiniz.

Günlükler çeşitli hedeflere yazılabilir. Geliştirme sırasında, bu yalnızca konsola veya Visual Studio’daki hata ayıklama penceresine olabilir, ancak metin dosyaları ve XML dosyaları gibi dosyalara veya bir veritabanı gibi diğer işlemlere veya Windows olay günlüğüne (`event logs`) günlükler de yazabilirsiniz.

<!-- ![](https://miro.medium.com/v2/resize:fit:619/1*7tkrA4UXPAd5Pi8O6S500A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_7tkrA4UXPAd5Pi8O6S500A.webp)

Çeşitli hedefe günlükler kayıt edilebilir (Dosya, Veritabanı, Günlük hizmetleri vs.)

<!-- ![](https://miro.medium.com/v2/resize:fit:459/0*93QJicA69mpXmKyR.gif) -->
![alt text]({{ BASE_PATH }}/assets/images/0_93QJicA69mpXmKyR.gif)

Windows içinde en temel günlük kaydetme yeri olay günlük yöneticisidir (event log manager)

Ayrıca günlüklerinizi merkezi bir konumda depolamak için yazabileceğiniz günlük sistemleri vardır, böylece günlüklerin içinde arama yapabilir ve günlüğe kaydedilen olaylar ilişkilendirilebilirsiniz.

Günlük tutma ihtiyacımız aşağıdaki 4 başlıktan kaynaklanabilir:

-   Hata giderme (uygulama işlevini yerine getirirken oluşan hatayı takip bulmak)
-   Denetim (sisteme girişler, yetkisiz erişimlerin raporlanması vs.)
-   Ölçümleme (örneğin kullanıcı girişinde aktif oturum sayısını arttırarak görüntülemek gibi çeşitli metrikleri görmek isteyebilirsiniz)
-   İstatistik (hangi modülün ne kadar kullanıldığını görmek gibi çeşitli istatistikler tutabilirsiniz)

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*4CjS_9uOVLFDsn2fyOeR7Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_4CjS_9uOVLFDsn2fyOeR7Q.webp)

**Metrikler**, belirli bir zamanda bir sistemin bazı yönlerini tanımlayan sayılarla durumu inceleyebilmenizi sağlayan değerlerdir. Genellikle düzenli aralıklarla toplanırlar ve bir **ad**, **değer**, **zaman damgası** ve **etiketlerle** tanımlanırlar. Genellikle zaman içindeki eğilimler için toplanır ve analiz edilirler. Metrikler genellikle temel alınan ortamla veya uygulamanın içinde bulunduğu çatıların kendisiyle ilgilidir. Windows performans sayaçları buna iyi bir örnektir ve performans sayaçlarıyla dış araçlar veya doğrudan kod aracılığıyla etkileşim kurabilirsiniz.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/0*J3ZYqhrX6zeMxVDx) -->
![alt text]({{ BASE_PATH }}/assets/images/0_J3ZYqhrX6zeMxVDx.webp)

**İzler (**`**traces**`**)**, dağıtılmış bir sistem aracılığıyla bir kullanıcı isteğini izleyen bir dizi ilgili olaydır. Bu, kodun davranışını ve farklı işlemlerin performansını belirlemeye yardımcı olur. Günlüklere benzerler, ancak günlükler genellikle tek bir bileşen tarafından oluşturulurken izleme fikri, tüm işlemin daha büyük bir resmini elde etmek için bileşenler arasındaki tanılama verilerini birbirine bağlamasıdır. Yani sisteminiz bir mikroservisler bütünü olsun ve tüm izlemeleri topladığınızda en basit haliyle kullanıcı yetkilendirme işlemini uçtan uca tüm bileşenlerden gelen izleme bilgileriyle sıralı olarak görebilir, performans kaybının nerede olduğunu gözlemleyebilir, istisna fırlatılması durumunda tüm istisna bilgilerine erişebilirsiniz.

Modern sistemler genellikle ön uç (`front-end`), arka uç (`back-end`) ve bir veritabanı gibi farklı katmanlara dağıtılır. Veya bir uygulamanın bileşenlerinin farklı sunuculara ayrı ayrı dağıtılabileceği ve tüm uygulamayı etkilemeden değişen yükün üstesinden gelmek için bağımsız olarak ölçeklendirilebileceği mikro hizmetlere dağıtılabilirler.

## Günlük (Log)

Sorun giderme sırasında hangi bilgilere ihtiyaç duyacağınızı bilmek zor. Bu nedenle, günlüğe kaydetme konusuna girmeden önce, bazı en iyi uygulama yöntemleri (`best practices`) hakkında konuşalım.

Mesajları günlüğe kaydetmenin farklı nedenleri vardır:

**Sorun giderme bariz bir nedendir.** Bir sorun bildirildiğinde kodunuzdaki sorunları tam olarak belirleyebilmek veya kullanıcılar bunları bildirmeden önce kodunuzdaki sorunlar hakkında bilgilendirilmek isteyeceksiniz, ancak denetim amacıyla günlük girişlerinizi de yazabilirsiniz.

**Denetime dair bilgiler tutmanız gerekebilir.** Kimin, nereden giriş yaptığı veya bir kullanıcının belirli bir dosyayı silip silmediği veya belirli bilgilere erişip erişmediği gibi yönetim veya denetim için önemli olan önemli olayları da yakalamak isteyebilirsiniz. Bunu gerçekleştirmek için günlüğe kaydetme altyapısını kullanabilir veya bu girişleri doğrudan raporlama veritabanına derecelendirerek kaydedebilirsiniz.

**Bir uygulamanın işleve başladığı-bitirdiği zaman aralığını, işi ne kalitede yaptığı bilgisini yani profilini çıkarmak için günlük girişleri oluşturabilirsiniz.** _Günlük girişleri_ **_zaman damgalı_** _olmalıdır_, bu nedenle bir işlemin başlangıç ve bitiş zamanlarını günlüğe kaydetmek bazı performans ölçümleri çıkarmanıza yardımcı olabilir. Elbette, kritik işlemler için, muhtemelen kodunuzu bu metrikleri dahili olarak yakalamak ve bunları tek bir günlük mesajında çıkarmak için kullanacaksınız. .Net çatısı altında `System.Diagnostics`’te buna yardımcı olacak `Kronometre` sınıfı var.

**İstatistik toplamak için günlükleri de kullanabilirsiniz.** Belirli olaylar gerçekleştiğinde günlüğe kaydetme, kullanıcıların kodun hangi alanlarına eriştiği veya bir uygulamada hangi yolları izledikleri gibi program hakkında ilginç istatistikler toplamanıza da olanak tanır. Ayrıca yeni özelliklerin kullanılıp kullanılmadığını veya kullanıcı tarafından bu değişikliklerin kabul görüp görmediğini anlamanıza yardımcı olabilir.

İhtiyacınız olan bilgiyi almak için günlükleri nasıl gözden geçireceğinizi düşünün, **zaman damgaları** bunu yapmanıza yardımcı olacaktır, ancak günlük çatıları/kütüphaneleri genellikle girişlere ek bilgi ekleme yeteneğine sahiptir.

**Kategoriler**, günlüklerinizi daha sonra filtrelemenize yardımcı olabilir. Kategoriler, girişleri bir arada gruplamak veya günlükleri okurken ek bağlam eklemek için bulduğunuz dizelerdir.

> Kategori ile bir şeyin bir teşhis girişi yerine bir denetim girişi olduğunu belirtebileceğiniz veya girişin geldiği kod alanı veya işlem türü hakkında daha belirli olabileceğiniz yerdir.

**Günlük seviyeleri (**`**log levels**`**)** başka bir kategorizasyon türüdür ve bunlar genellikle daha statiktir ve sabit kodlanmış seçeneklerin bir listesi kullanılarak koddaki girişe eklenirler.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*ucu65Y61lbNeti4G7RAxZQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_ucu65Y61lbNeti4G7RAxZQ.webp)

syslog Standartına göre günlük seviyesi:

[![alt text]({{ BASE_PATH }}/assets/images/1_ueaMtVN8byKRZbRZROxKcA.webp)](https://datatracker.ietf.org/doc/html/rfc5424)

[https://datatracker.ietf.org/doc/html/rfc5424](https://datatracker.ietf.org/doc/html/rfc5424)

```
Numerical         Severity
  Code
   0       Emergency     : sistem kullanılamaz
   1       Alert         : bir an önce önlem alınmalı
   2       Critical      : kritik durumu oluştur
   3       Error         : hata durumu
   4       Warning       : uyarı durumu
   5       Notice        : normal ama önemli durum
   6       Informational : bilgilendirme mesajları
   7       Debug         : hata ayıklama düzeyindeki mesajlar
```

**Kritik (**`**critical**`**) seviyesi**, günlük girişleri, acil müdahale gerektiren bir kilitlenmeyi veya yıkıcı bir hatayı tanımlar.

**Hata (**`**error**`**) mesajları**, bir arıza nedeniyle mevcut akış durduğunda vurgulanır. Bu tür girişlerin her ikisinde de, istisna bilgileri ve varsa yığın izleme (`stack trace`) gibi mümkün olduğunca çok bağlam bilgisi eklemek isteyeceksiniz, ancak aynı zamanda, beklenmeyen değerler gibi başarısızlığa neden olabilecek diğer koşullar hakkında bazı bağlamlar da eklemek isteyeceksiniz.

**Uyarı (**`**warning**`**) düzeyi**, uygulama akışında uygulamanın durmasına neden olmayan anormal veya beklenmeyen olayları vurgular.

**Bilgi (**`**info**`**) düzeyindeki** olaylar, uygulamadaki genel akışı takip eder.

**Hata ayıklama (**`**debug**`**) düzeyindeki** olaylar araştırma için kullanılır ve uzun vadeli bir değeri yoktur. Bu nedenle, bilgi mesajları kullanım kalıpları gibi şeyleri anlamak için kullanılabilirken, hata ayıklama mesajları gerçekten belirli sorunları teşhis etmek içindir. `Debug` seviyesi geliştirme sırasında kullanılır ancak mesaj seviyesini değiştirebildiğiniz mekanizmalar sayesinde bir üretim uygulamasında da hata ayıklama düzeyindeki mesajları yalnızca daha derinlemesine ihtiyaç duyduğunuzda etkinleştirmek mümkündür.

**İzleme (**`**trace**`**) düzeyindeki** mesajlar en fazla veriyi içerir.

Kullandığınız API’ye veya günlük kaydı çerçevesine bağlı olarak, tüm bu düzeylere sahip olmayabilirsiniz veya kendi özel seviyelerinizi tanımlayabilirsiniz, ancak günlük düzeylerini nasıl kullanırsanız kullanın, bunlar bir dizi girdiyi filtrelemek için önemli olabilir.

> _Tüm bu_ **_günlük kaydı, uygulama performansını etkileyebilir_**_. Çok sayıda günlük kaydının yazılması gerekmediği durumlarda yavaşlığa neden olabilir._

Günlüğe kaydetme yönteminize bağlı olarak, günlüklere hangi mesaj günlük düzeylerinin yazılacağını yapılandırmak için bir yapılandırma dosyasını doğrudan değiştirebilirsiniz veya bunu kod kullanarak yapmanız gerekebilir, bu durumda okumak için kendi kodunuzu yazabilirsiniz.

Kullanıcıların uygulamayı nasıl kullandığını izlemeniz gerektiğinden bu girişleri görmeniz gerekiyorsa, bu günlük girişlerini geçici olarak etkinleştirebilir ve üzerinde çalışacak daha fazla veriye sahip olabilir ve ardından bunları tekrar kapatabilirsiniz. Bu, daha sonra ne tür girişlere ihtiyacınız olduğuna karar vermenize veya yalnızca uygulamanızın her zaman günlüğe kaydetmese bile ihtiyaç duyabileceğiniz kadar bilgi sağlayacak şekilde donatıldığını bilmenize olanak tanır.

## Günlük Tutmada Öneriler

-   Hatayla ilgili bilgileri dahil etmek isteyeceksiniz
-   İstisna türünden daha fazlasını, istisnaya dair bağlam da dahil olmak üzere istisnanın nedeni hakkındaki bilgileri tutmak isteyeceksiniz
-   Bir veritabanı çağrısı durumunda işlemi yapan kullanıcının kimliği gibi ayrıntıları dahil etmelisiniz.
-   Bir veritabanı sorgusu veya uzak web araması gibi diğer işlemlere çağrı yaptığınızda veya bir Web API’sine gelen istekte olduğu gibi
-   Mesaj aldığınızda günlüğe kaydetmek isteyeceksiniz,
-   Kodunuzdaki darboğazları belirlemek için işlemin ne kadar sürdüğünü de kaydedebilirsiniz.
-   Belirli yöntemlere girildiğinde ve çıkıldığında günlük kaydı oluşturabilirsiniz ancak bunun çok sayıda girişle sonuçlanabileceğini ve alınan yolu belirlemek için daha sonra bu kod alanını izlemek için bir nedeniniz olmadıkça bunca günlüğün önemsiz olabileceğini unutmayın. Bunu yaparsanız, gerekirse bu tür izlemeyi devre dışı bırakabilmeniz için günlük düzeylerini kullandığınızdan emin olun.
-   Muhtemelen, gerekli olduğunu düşündüğünüzde, kodunuza sürekli olarak kayıt noktaları ekliyor veya kaldırıyor olacaksınız. Bir uygulama üretime geçtiğinde, sorunlar olduğunu görebilir ve tanılamada size yardımcı olacak günlük kaydı yapılmayabilir!
-   Kodu faktoring yaparken, hatalı günlük girişlerine dayalı sorunların peşine düşmemek ve koddaki değişikliği yansıtmak için gerekirse günlüğü yeniden düzenlediğinizden emin olun.
-   Anlamlı günlük girişleri yazmaya çalışın ve bunları kimin okuyacağını düşünün. **Sistem yöneticileri için yazılan günlükler**, kodun iç işleyişini anlayan **geliştiriciler için yazılanlardan farklı bir ayrıntı düzeyine sahip olacaktır**.
-   Son öneri, hassas verileri asla günlüğe kaydetmemenizdir. Bu, parolalar, kredi kartı numaraları, sosyal güvenlik numaraları ve hatta yetkilendirme jetonları gibi şeylerdir, ancak kullanıcılar hakkında kişisel olarak tanımlanabilir bilgileri de dikkate alır. Bunun için göz önünde bulundurmanız gereken KVKK gibi kanunlar ve düzenlemeler olabilir.
-   Bir sorunu araştırmak için zamanda geriye gidin. Ancak tanılama, yalnızca üretim uygulamaları için bir şey değildir. Geliştirme sırasında da önemlidir. Elbette, geliştirirken sorunları keşfetmek ve düzeltmek çok daha kolaydır ve bu bazen kodda kesme noktaları ayarlamak ve ardından Visual Studio gibi entegre geliştirme ortamında bir hata ayıklayıcı ile adım atmak kadar kolay olabilir. Ardından değişkenleri inceleyebilir, hesaplamaları kontrol edebilir ve kodun izlediği yolu görebilirsiniz. Ancak geliştirme sırasında bile bu, hızla manuel olarak yapılamayacak kadar karmaşık hale gelebilir. Çok iş parçacıklı uygulamalarda hata ayıklayıcı eklemek de zor olabilir. Hata ayıklayıcı eklemek, özellikle diğer hizmetleri çağırırken zaman aşımı hatalarına da neden olabilir. Bu nedenle, eklediğiniz kodun hiçbir zaman üretime geçmesi amaçlanmasa bile, geliştirme sırasında bile uygulamanızı denetlemek faydalı olabilir. Bu modülün ilerleyen bölümlerinde, iddialar için kod eklemenin, kodunuzun diğer geliştiriciler tarafından da düzgün bir şekilde kullanılmasını nasıl sağlayabileceğini göreceksiniz. Tanılama için .NET’teki API’lerin ve sınıfların özelliklerine girmeden önce, uygulamaların ve sistemlerin gözlemlenebilirliğinin büyük resmi hakkında konuşalım. Gözlenebilirlik, yalnızca izlemek istediğiniz bir sistemin içinden verileri kullanılabilir hale getirmek anlamına gelir. Bu genellikle üç gözlemlenebilirlik sütunu olarak tanımlanır. Bu, günlüklerden, ölçümlerden ve dağıtılmış izlerden oluşur. Bunlar, izlenen bir sistemin yeterli gözlemlenebilirliğini sağlamak için toplanması ve analiz edilmesi gereken temel olarak farklı veri türleridir. Günlükler, bir sistem içindeki olaylardır. Bir zaman damgası ve bir bağlam yükü var. Genellikle düz metin veya ayrı alanlara sahip yapılandırılmış günlükler biçiminde gelirler ve bu alanlar günlükleri aramayı ve filtrelemeyi kolaylaştırır. Günlükler çeşitli hedeflere yazılabilir. Geliştirme sırasında, bu yalnızca konsola veya Visual Studio’daki hata ayıklama penceresine olabilir, ancak metin dosyaları ve XML dosyaları gibi dosyalara veya bir veritabanı gibi diğer işlemlere veya Windows olay günlüğüne günlükler de yazabilirsiniz. Ayrıca günlüklerinizi merkezi bir konumda depolamak için yazabileceğiniz günlük sistemleri vardır, böylece bunlar aranabilir ve günlüğe kaydedilen olaylar ilişkilendirilebilir. Kurs boyunca tüm bu farklı günlük türlerine bakacağız. Metrikler, belirli bir zamanda bir sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Genellikle düzenli aralıklarla toplanırlar ve bir ad, değer, zaman damgası ve etiketlerle tanımlanırlar. Genellikle zaman içindeki eğilimler için toplanır ve analiz edilirler. Metrikler genellikle temel alınan ortamla veya .NET Framework’ün kendisiyle ilgilidir. Windows performans sayaçları buna iyi bir örnektir ve performans sayaçlarıyla dış araçlar veya doğrudan kod aracılığıyla etkileşim kurabilirsiniz. Bu yazıda bu tür metriklere odaklanmayacağız.

## Linux Üstünde Günlük Oluşturma

Linux sistem logları, işletim sistemi, uygulamalar ve sistem için bir olay zaman çizelgesi sağlar ve sorunlarla karşılaştığınızda da en değerli sorun giderme aracımız olmaktadır. Esasen, bir sorun tespit edildiğinde, bir yöneticinin yapması gereken ilk şey, günlük dosyalarını analiz etmektir.

Günlük dosyaları, düz metin olarak depolanır ve **/var/log** dizininin alt dizinlerinde bulunur. Sistem üzerindeki her etkinlik için Linux logları tutulur: sistem, kernel, paket yöneticileri, önyükleme işlemleri, Xorg, Apache, MySQL vb.

Görüntülenecek en önemli loglardan biri, kimlik doğrulamayla ilgili mesajlar dışında her şeyi günlüğe kaydeden **syslog**‘dur. Log dosyalarını görüntülemek için, genel olarak _more, less, dmesg_ ve _tail_ gibi komutlar kullanılır.

Logları, çekirdek halka arabelleğine (kernel ring buffer) yazdıran **dmesg** aracılığıyla da görüntüleyebilirsiniz. Her şeyi yazdırır ve sizi dosyanın sonuna gönderir. Oradan çıktıyı kaydırmak için **_dmesg | less_** komutunu kullanabilirsiniz.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*WswDrtcO4luhGEHt7Qyo8Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_WswDrtcO4luhGEHt7Qyo8Q.webp)

Linux sistem logları, 4 kategoriden oluşur.

1.  Uygulama Günlükleri
2.  Olay Günlükleri
3.  Hizmet Günlükleri
4.  Sistem Günlükleri

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*X6niF-bmZppkKBzNUhlacw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_X6niF-bmZppkKBzNUhlacw.webp)

## syslogd ve rsyslog

Tarihi olarak, Linux’taki uygulamalarınızdan günlükleri toplama işi bir arka plan programının sorumluluğundadı. Birçok eski dağıtımda, bu görev **syslogd** adlı bir arka plana atanmıştır, ancak son dağıtımlarda yerini **rsyslog** arka plan programı almıştır.

## systemd-journal

**Systemd**, son dağıtımlarda mevcut init işleminin yerini aldığında, günlükleri alma ve depolama konusunda kendi yöntemiyle geldi: **systemd-journal**.

Şimdi, iki sistem bir arada var oluyor, ancak bir arada bulunmalarının, geçmişte kullanılan günlük mimarileriyle geriye doğru uyumlu olduğu düşünülüyordu. **rsyslog** ve **systemd-journal** arasındaki temel fark, rsyslog’un günlükleri `/var/log`’da bulunan günlük dosyalarında tutması, journald’nin bunu yapacak şekilde yapılandırılmadıkça verileri saklamamasıdır.

**systemd-journal** yardımcı programı sisteminizdeki loglama aktivitelerini de takip eder. Hizmet olarak yapılandırılan bazı uygulamalar (örneğin bir Apache HTTP Sunucusu) doğrudan sistem günlüğüyle konuşabilir. Systemd günlüğü, günlükleri merkezi bir şekilde depolayan `/run/log/journal` dizinidir. **Günlük dosyaları systemd tarafından ikili dosyalar olarak saklanır, bu nedenle, normal cat veya less komutlarını kullanarak dosyaları inceleyemezsiniz.** Bunun yerine, systemd-journal tarafından oluşturulan günlük dosyalarını incelemek için “`journalctl`” komutunu kullanmamız gerekir.

## /var/log/syslog veya /var/log/messages

Sistemle ilgili bilgiler ve genel mesajları barındırır. Esasen bu günlükler, sistem genelindeki tüm etkinlik verilerini depolar. CentOS veya Rhel gibi Redhat tabanlı sistemler için faaliyetlerin **/var/log/messages**’da, Ubuntu ve diğer Debian tabanlı sistemlerin **/var/log/syslog**‘da depolandığını unutmayın.

## /var/log/auth.log veya /var/log/secure

Hem başarılı hem de başarısız oturum açma ve kimlik doğrulama yöntemleri dahil olmak üzere, kimlik doğrulama günlükleri burada depolanır.

## /var/log/boot.log

Önyükleme ile ilgili tüm bilgilerin ve başlatma sırasında kaydedilen tüm mesajların kaydedildiği günlük dosyasıdır. **cat** ile görüntülerseniz, tüm tarihlerde olan etkinlikleri görürsünüz.

## /var/log/mail.log veya /var/log/maillog

E-posta sunucularıyla ilgili tüm logları depolar. Postfix, smtpd veya sunucunuzda çalışan e-posta ile ilgili hizmetler hakkında bilgiye ihtiyaç duyduğunuzda oldukça kullanışlıdır.

## /var/log/kern

Kernel (çekirdek) günlüklerini ve uyarı verilerini depolar. Bu log dosyası, özelleştirilmiş çekirdeklerde sorun gidermede önemlidir.

## /var/log/dmesg

Aygıt sürücüleriyle ilgili mesajlar bulunur. **dmesg**, komutu bu dosyadaki mesajları görüntülemek için kullanılabilir.

## /var/log/faillog

Debian-Ubuntu linux sistemlerinde, tüm başarısız oturum açma girişimleri hakkında bilgiler içerir. Ayrıca, brute force saldırıları ile oturum açma kimlik bilgilerini ele geçirmeye çalışanlar gibi, teşebbüs edilen güvenlik ihlalleri hakkında veri elde etmek için yararlıdır.

## /var/log/cron

**cron** arka plan programının bir işi ne zaman başlattığı, ilgili hata mesajları vb. gibi **crond** hizmetiyle ilgili tüm mesajları (zamanlanmış işleri) depolar.

## /var/log/yum.log

yum komutunu kullanarak paketleri kurarsanız, bu günlük ilgili tüm bilgileri depolar. Bir paketin ve tüm bileşenlerin doğru şekilde kurulup kurulmadığını belirlemede yararlı olabilir.

## /var/log/httpd/

Apache hizmeti olarak arka planda çalışan **httpd** programının, **error\_log** ve **access\_log** dosyalarını içeren bir dizin.

-   **error\_log**, httpd tarafından karşılaşılan tüm hataları içerir. Bu hatalar, bellek sorunlarını ve sistemle ilgili diğer hataları içerir.
-   **access\_log**, HTTP üzerinden alınan tüm isteklerin kaydını içerir.

## /var/log/mysqld.log ya da /var/log/mysql.log

Eğer veri tabanı uygulaması MySQL sisteminizde kuruluysa, tüm hata ayıklama (debug), başarılı (success) veya başarısız (failure) mesajlarını kaydeden MySQL hizmetinin günlük dosyası olacaktır. MySQL daemon **mysqld**’nin başlatılması, durdurulması ve yeniden başlatılması hakkında bilgi içerir.

_/var/log/mysqld.log_ : CentOS-RHEL tabanlı sistemlerde

_/var/log/mysql.log :_ Debian-Ubuntu tabanlı sistemlerde

## Kabuk Betiği Uygulamasının Günlüğü

Hemen hemen her yaygın programlama dili, sistem günlüklerine giriş yapma yeteneğine sahiptir.

Bir shell scripti çalıştırdığınızda, neler gerçekleştiğini kaydetmek istiyorsanız **logger** kullanmanız gerekir. Loglar istediğiniz herhangi bir formattaki bilgiyi kaydedebilir. Genellikle kim, ne, ne zaman, nerede ve niçin sorularına cevap veren bilgileri barındırır.

Loglar, shell scriptleri çeşitli aksiyonlar aldığında kullanışlıdır.

Günlük girişlerinin oluşturulma şekli, uygulamanızın yazıldığı dile bağlı olacaktır. Bunu çok basit tutalım ve bunun kabuktan nasıl yapılabileceğini gösterelim. Kullanılacak yardımcı program, genellikle **/usr/bin/logger** konumunda bulunan **logger**’dır.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*Dz9SOSDBW4Eh5Y5EEqjWQw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_Dz9SOSDBW4Eh5Y5EEqjWQw.webp)

## Logger[\*](http://www2.phys.canterbury.ac.nz/dept/docs/manuals/unix/DEC_4.0e_Docs/HTML/MAN/MAN1/0268____.HTM)

`**logger**` aracının özel ayrıcalıkları yoktur. Bu sadece küçük bir araç ve `syslog` arka plan programınıza mesaj göndermenize yardımcı olur.

“logger -h” ile nasıl kullanılacağını görüntüleyebilirsiniz:

<!-- ![](https://miro.medium.com/v2/resize:fit:602/1*xoPuyt1j8qFu9WZmgeZ2Yw.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_xoPuyt1j8qFu9WZmgeZ2Yw.webp)

en basit komut olarak,

**_logger “Message”_**

kullanılabilir.

Varsayılan olarak, **user** facility ve **notice** severity parametreleri girilir.

Facility ve severity değerini kendimiz belirleyebilmemiz için;

**_logger -p facility.severity “Message”_** _kullanılır._

Mesajı taglamak için ise;

**_logger -t myscrpit -p facility.severity “Message”_**

Bu şekilde log dosyasından sadece taglediğiniz scripte ait mesajları çekebilirsiniz.

Eğer process id’yi mesaja eklemek istiyorsanız;

**_logger -i -t myscript “message”_**

Örnekler;

```shell
$ logger “Message”
Aug 19 12:03:34 linuxsvr mylinux: Message

$ logger -p local0.info "Message"
Aug 19 12:03:34 linuxsvr mylinux: Message

$ logger -s -p local0.info "Message"
mylinux: Message # ← displayed on screen
```

\-s ile mesajı ekranda görüntüleyebiliriz.

```shell
$ logger -t myscript -p local0.info “Message”
Aug 19 12:04:03 linuxsvr myscript: Message

$ logger -i -t myscript “Message”
Aug 19 12:05:23 linuxsvr myscript[12986]:
Message
```

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*46QxPv6JBBC9GaAIfJGHug.gif) -->
![alt text]({{ BASE_PATH }}/assets/images/1_46QxPv6JBBC9GaAIfJGHug.gif)

Örneklere devam:

```shell
$ logger -p local0.debug "Mesajım"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:406/1*AVphXCG_-MSSVcLn-pZnwA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_AVphXCG_-MSSVcLn-pZnwA.webp)

```shell
$ logger -p local0.notice "Mesajım"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:397/1*C1Z4c3aupDn29laMIv-a2Q.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_C1Z4c3aupDn29laMIv-a2Q.webp)

```shell
$ logger -p local0.warning "Mesajım"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:395/1*wHk6lNWmdGzXmq61_YoZZg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_wHk6lNWmdGzXmq61_YoZZg.webp)

```shell
$ logger -p local0.error "Mesajım"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:393/1*bBE2F1cYT3r51KM6OoU49A.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_bBE2F1cYT3r51KM6OoU49A.webp)

```shell
$ logger -p local0.panic "Mesajım -> emerg veya panic"
$ logger -p local0.crit "Mesajım -> crit - critical"
$ logger -p local0.err "Mesajım -> err veya error"
$ logger -p local0.alert "Mesajım -> alert"
$ logger -p local0.warning "Mesajım -> warn - warning"
$ logger -p local0.notice  "Mesajım -> notice"
$ logger -p local0.normal  "Mesajım -> normal"
$ logger -p local0.info  "Mesajım -> info"
$ logger -p local0.debug  "Mesajım -> debug"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:574/1*6pzI119bUL-vbSr45FzUqQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_6pzI119bUL-vbSr45FzUqQ.webp)

```shell
$ logger -p user.debug  "Mesajım -> debug"
```

<!-- ![](https://miro.medium.com/v2/resize:fit:478/1*-d8121JTjHu7kwYDHPuQCQ.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_-d8121JTjHu7kwYDHPuQCQ.webp)

## Fonksiyon Oluşturma ve çağırma

```bash
logit () { # fonksiyon adı logit

  local LOG_LEVEL=$1 # log level parametresini değişken olarak atama

  shift #parametrenin pozisyonunu kaydırmak(sola)

  MSG=$@  # her şeyi içerir ama ilk parametreyi kullanır. ( log_level)

  TIMESTAMP=$(date + "%Y-%m-%d %T") # date komutundan değerler timestamp değişkenine atanır.

  if [ $LOG_LEVEL = 'ERROR' ] || $VERBOSE # log_level errorsa ve verbose global değişkeni 1 ise

  then

    echo "${TIMESTAMP} ${HOST} # timestamp ve mesagge output olarak verilsin.

${PROGRAM_NAME} [${PID}] : ${LOG_LEVEL} ${MSG}"

  fi
}
```

Fonksiyonu çağırmak için;

```shell
logit INFO "Processing data."

# iki komut || operatörüyle çalışınca
fetch-data $HOST || logit ERROR "Could not fetch data from $HOST" 
```

## logrotate İle Günlüklerin Yönetilmesi

Günlük dosyalarını düzenli aralıklarla veya disk alanının azaldığı durumlarda silmek için bir kural ayarlayabilirsiniz. Bunun için **logrotate** programı kullanılabilir.

Logrotate komutu çoğunlukla `/usr/sbin` klasörü altında olup bu komut ile aşağıdaki işlemler yapılabilir.

-   Dosya boyutu belirli bir büyüklüğe ulaştığında log dosyasını rotate(çevrim) etme
-   Rotate işleminden sonra yeni log bilgileri için dosya yaratma
-   Rotate edilmiş log dosyalarını sıkıştırma
-   Eski log dosyalarını kaldırma
-   Log dosyalarını tarihe göre saklama

Logrotate komutunun çalışma yapısı aslında zamanlanmış görevlerin (cron jobs) çalışma yapısıdır. `/etc/cron.daily/logrotate` dizininde günlük olarak çalışan bir shell betiğidir (script).

Komutun genel yapısı aşağıdaki gibidir.

```shell
logrotate [-dv] [-f| — force] [-s| — state file] config_file
```

Ayrıca aşağıdaki gibi uygun bir dosya düzenleyici kullanarak günlükleri döndürme işini yönetebilirsiniz ancak her uygulama için ayrı ayrı ayar dosyaları oluşturmak karmaşayı azaltacaktır.

```shell
$ nano /etc/logrotate.conf
```

<!-- ![](https://miro.medium.com/v2/resize:fit:581/1*bp2WawLBX5TQQVbxQ_9fSA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_bp2WawLBX5TQQVbxQ_9fSA.webp)

`/etc/logrotate.d` dizini altında çeşitli programlar için oluşturulmuş logrotate konfigurasyonlarının yer aldığını görüyoruz. Bu dizinde MongoDB için konfigurasyon dosyası yer almıyorsa oluşturmamız gerekir. MongoDB `/var/log/mongodb` dizini altındaki günlük dosyalarını tutar.

```shell
cat << EOF >> /etc/logrotate.d/mongo
/var/log/mongodb/mongodb.log {
   daily
   rotate 7
   delaycompress
   compress
   dateext
   missingok
   copytruncate
}
EOF
```

Bu ayarlar ile

-   mongodb.log dosyası günlük olarak saklanacak
-   Eski log dosyaları varsayılan olarak gzip şeklinde tutulacak
-   rotate 7 ile son 7 güne ait log dosyaları saklanacak.Bunun için eski log dosyaları tarih sırasına göre silinecek.

Kullandığımız parametreleri açıklayalım.

-   **daily:** Rotate işlemini günlük olarak gerçeleştirir.
-   **compress:** Eski log dosyalarını varsayılan olarak gzip olarak sıkıştırır.
-   **dateext:** Arşivlenen log dosyalarına YYYYMMDD şeklinde tarih formatı ekler.
-   **missingok:** Eğer log dosyası yoksa hata mesajı vermeden bir sonrakine geçer.
-   **copytruncate:** Dosyanın kopyasını aldıktan sonra,dosyanın boyutunu sıfırlamak için silme işlemini gerçekleştirir.

Yapmak istediğimiz işlemlere göre copy, dateformat, create, ifempty, mail, maxsize gibi parametreleri de kullanabiliriz. Parametrelerle ilgili ayrıntılı bilgilere `man logrotate` komutu ile ulaşabiliriz.

Ayrıca `postrotate/endscript` ifadelerini kullanarak her rotate işleminden sonra yaptırmak istediğimiz işlemleri aşağıdaki gibi yazabiliriz. `prerotate/endscript` ise rotate yapmadan önce komutu çalıştırır.

```
postrotate
    kill <mongod process id>
endscript
```

`sudo logrotate -f /etc/logrotate.d/mongodb`

komutunu terminalde çalıştırınca `/var/log/mongodb` dizini altında tutulan log dosyasında işlemlerin gerçekleştiğini görüyoruz.

```shell
-rw-r--r-- 1 mongodb nogroup    0 Tem 19 12:25 mongodb.log
-rw-r--r-- 1 mongodb nogroup 2423 Tem 21 13.00 mongodb.log-20140721
-rw-r--r-- 1 mongodb nogroup 1395 Tem 20 11.56 mongodb.log-20140720.gz
```

Bu komutu çalıştırmadan da bir sonraki gün rotate işleminin gerçekleştiğini gözlemleyebiliriz. Diğer programlar için de logrotate kullanarak log dosyalarını kontrol altına alabiliriz.

## Dikkat Edilmesi Gerekenler

Geliştiriciler, neler olup bittiğini görmek için genellikle geçici günlük dosyaları (örneğin, _/tmp/benim-sirin-uygulamam.log_) oluşturur. Bu iyi bir fikir değil, çünkü bu tür geçici günlük kayıtları genellikle amaçlanandan daha kalıcı hale gelir. İkinci olarak, günlük dosyaları disk alanını hızla doldurur ve bunları kaldırmak veya boyutlarını küçültmek için işlem yapılmadığı sürece bu durum devam eder.

Günlük dosyaları, genellikle diğer sistem günlüklerinin yanında (veya bunlarla birlikte) tutarlı bir konumda olmalıdır.

Günlük dosyaları otomatik olarak “döndürülecektir” (log rotation). Diğer bir deyişle, günlük dosyası periyodik olarak kapatılır, yenisi başlatılır ve eskisi isteğe bağlı olarak sıkıştırılır. Önceden belirlenmiş bir süre sonunda eski günlük dosyaları kaldırılır. Bu da tamamen otomatiktir: eski veya büyük günlük dosyalarını elle kaldırmanıza gerek yoktur. Sistem yöneticisi, uygulama günlüğünüzü bozmadan günlük dosyalarını farklı sunucu/yazılım/ortamlara taşıyabilir.

## Syslog Nedir?

Linux işletim sistemi, günlük kayıtları için **syslog** standartlarını kullanır.

Bu şekilde program ve uygulamaların mesajları **syslogger** tarafından yakalanarak depolanır.

Her uygulamanın kendi günlük tutma mekanizması vardır.

Syslog, mesajları standart bir hale getirmek için **facilities** (işlev) ve **severities** (önem derecesi) parametrelerini kullanır.

**Facilities**: kern, user, mail, daemon, auth, local0, local7

**Severities**: emerg, alert, crit, err, warning, notice, info, debug

Her mesaj bir facility kodu ve severity seviyesi ile etiketlenir.

Çeşitli kombinasyonlar ile mesajın nasıl ele alınacağı ile ilgili detaylar belirlenir.

Facility hangi tipte bir programdan ya da sistemin neresinden mesaj geleceğini belirler.

Örneğin;

Facility parametresi mail ile etiketlenmiş olan bir mesaj, uygulamanın mail kısmından gelir.

Eğer hangi facility parametresini kullanacağınızı bilmiyorsanız **user** facility kullanılabilir.

Facility menzili, **local0**'dan **local7**'ye kadardır. Custom loglar için kullanılır.

Severityler arasında en yüksek acil mesaj **emerg**, en düşük acil mesaj **debugging**’dir.

Mesajlar çoğunlukla bir dosyaya yazılır. her dağıtımın farklı bir dosyası mevcuttur. Ancak bu değiştirilebilir.

Çoğu dağıtımda;

\- **/var/log/messages**

\- **/var/log/syslog**

dizinleri kullanılır.

## Tanılama ve .NET

İşte .NET’te bulunan araç kategorileri. Yönetilen hata ayıklayıcılar, Visual Studio’da ve diğer tümleşik geliştirme ortamlarında bulunan araçları ifade eder.

Günlüğe kaydetme ve izleme buraya kadar gördüğümüz ana başlıktı.

Metrikler, çöp toplama (`garbage collector`) bilgileri, dağıtım platformundaki CPU kullanımı ve web sunucularındaki istek sayısı gibi .NET’in kendisi tarafından yayınlanan tanılamalara atıfta bulunur.

Birim testi (`unit test`), geliştirmenin önemli bir yönüdür, ancak bu daha çok yazılımın geliştirme aşamasına odaklanır ve DevOps süreçlerinde önemli bir rol oynar.

Dökümler (`dumps`), döküm oluşturulduğunda sürecin anlık görüntüsünü içeren dosyalardır. Bu, belirli bir zamanda konuşlandırılmış bir sürecin tüm resmini yeniden oluşturmak için iyi olabilir. .NET tarafından sağlanan araçları kullanarak bir döküm dosyası oluşturabilirsiniz

Semboller (`symbols`), hata ayıklayıcıların derlenmiş bir ikili dosyadaki davranışı kaynak koda nasıl çözdüğüdür. Sembolleri tipik olarak bir uygulamanın canlı ortam sürümünde dağıtmazsınız. Canlı ortam için günlüğe kaydetme ve izleme ile ilgili başlıkları kullanabilirsiniz.

**System.Console**, yazma ve yazma satırı gibi yöntemler sağlar. Bu, konsol ekranına basit verilerin çıktısını almak için yalnızca geliştirme sırasında kullanacağınız gerçekten en basit yaklaşımdır. `System.Console`’un yalnızca konsol uygulamaları için olması gerekmez. Web uygulamanızı komut satırından `dotnet run` kullanarak çalıştırıyorsanız, uygulamadan konsola çıktı yazabilir ve aynısını Visual Studio’da Web API projeleri ile yapabilirsiniz.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*HRO4gYBRW25woJqjtKqHSA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_HRO4gYBRW25woJqjtKqHSA.webp)

[https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events](https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events)

**System.Diagnostics** isim uzayı, tanılama API’lerinin çoğunun bulunduğu yerdir. İzleme ve hata ayıklama, `System.Console`’dan daha fazla işlevse sahiptir. Sınırlandırılmış metin dosyaları, Windows EventLog’daki XML dosyaları gibi çeşitli biçimlere ve hedeflere çıktı yazmanıza izin verirler.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*Smdqb39KXigZX2QmqdvlEg.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_Smdqb39KXigZX2QmqdvlEg.webp)


[https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events](https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events)

**System.Diagnostics.Debug** sınıfı, geliştirme sırasında gerçekten kullanışlıdır, çünkü hata ayıklama ifadeleri uygulamanın yayın sürümünde derlenmez, bu nedenle asla üretim koduna girmezler. Yani bu sınıf kullanılarak yazılan kodları uygulamanın `release` sürümü derlenirken oluşturulan exe’ye dahil edilmeden derleme yapılır. Hata ayıklama ayrıca uygulamadaki verilerin beklenen değerlerini doğrulamak için `assert` ifadeleri ekleme yeteneğini de içerir.

**System.Diagnostics.Trace** sınıfı, hata ayıklamayla neredeyse aynıdır, ancak canlı sistemlerdeki uygulamaların izlenmesine izin vermek için dağıtılan kodun üretim ortamı sürümünde yer bulur. İzleme, üretim kodunu enstrümantal etmenin en eski yollarından biridir ve bunun **TraceSource** adında daha fazla işlev ekleyen daha yeni bir uygulaması vardır.

**System.Diagnostics** ad alanı, olayları yakalamanın yanı sıra onları oluşturmak için başka yaklaşımlar içerir.

**System.Diagnostics.Tracing.EventSource**, .NET’in kendisinde kullanılan düşük seviyeli bir izleme API’sidir. `[dotnet‑trace](https://docs.microsoft.com/en-us/dotnet/core/diagnostics/dotnet-trace)` ve `[perfview](https://github.com/microsoft/perfview)` gibi araçları kullanarak uygulamanızın dışındaki olayları kullanabilmenize olanak tanır. Ayrıca, .NET tarafından oluşturulan ve işletim sisteminden gelen olaylarla kolayca ilişkilendirilebilmeleri için uygulamanızda kendi olaylarınızı da oluşturabilirsiniz, ancak bu, standart günlük kaydı ve izlemeden daha karmaşıktır. Bu nedenle, uygulama geliştirmede kullanmak mümkün olsa da, uygulama geliştiricileri tarafından kullanılan kitaplıkları geliştiriyorsanız kullanmanız daha olasıdır.

<!-- ![](https://miro.medium.com/v2/resize:fit:700/1*43Y1zS6zjdB51EPYYFsGuA.png) -->
![alt text]({{ BASE_PATH }}/assets/images/1_43Y1zS6zjdB51EPYYFsGuA.webp)

[https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events](https://docs.microsoft.com/en-us/dotnet/core/diagnostics/logging-tracing#logging-events)

**DiagnosticSource**, uygulamalarınızda DiagnosticSource olayları oluşturma açısından bu açıdan benzerdir.

**EventSource ve DiagnosticSource arasındaki fark**, EventSource olaylarının, çalışan uygulamaları dinleyen araçlar tarafından işlem dışında tüketilmesi gerektiğidir; bu nedenle, olayların işlem sınırını aşmak için serileştirilebilir olması gerekir, ancak DiagnosticSource olayları, nesnelerin kullanılmaması için işlem sırasında tüketilir, serileştirilebilir olması gerekmez.

.NET’in kendisi **DiagnosticSource** kullanarak olaylar oluşturur, böylece kitaplıkların içinde neler olup bittiğini öğrenmek için .Net çatısındaki olayları dinleyebilirsiniz. Bu, `HttpClient` ve `SqlClient` gibi .NET sınıflarını kullanırken koddaki darboğazları belirlemenize yardımcı olabilir.

Ancak **EventSource**’a benzer şekilde, uygulamalar tarafından tüketilecek kitaplıklar geliştiriyorsanız, **DiagnosticSource** kullanarak kendi olaylarınızı oluşturma olasılığınız daha yüksektir.

**DiagnosticSource** **olaylarını doğrudan uygulamanın kendisinde oluşturamayabilirsiniz**.

`System.Diagnostics.EventLog`, olayları yazma, olayları sorgulama ve yazılan olayları dinleme dahil olmak üzere Windows EventLog ile etkileşime girmenizi sağlar.

`ILogger` arabirimi, bağımlılık ekleme (**dependency injection**) ile kullanılabilen ortak bir günlük kaydı arabirimidir. Bu, `Microsoft.Extensions.Logging` ad alanında bulunur, bu nedenle `System.Diagnostics`’in dışındadır, ancak aslında System.Diagnostics isim uzayındaki `Debug`, `Consol`, `EventSource` ve `EventLog` gibi sınıflarda işlem yapabilmek için tek bir arabirim kullanmanıza izin veren bir soyutlamadır.

`ILogger`, uygulamanız boyunca tüm enstrümantasyon kodunu değiştirmek zorunda kalmadan arka uçta popüler üçüncü taraf günlük kaydı çerçevelerini kullanmanıza da olanak tanır. Günlüğe kaydetmeye yönelik daha yeni bir yaklaşımdır ve ASP.NET’te yaygın olarak kullanılır.

## Prometheus Metrikleri

Prometheus Operator, varsayılan olarak bir hizmetin `/metrics` uç noktasını her 30 saniyede bir kazıyacak ve bu metrikleri dahili zaman serisi veritabanına alacak şekilde yapılandırılmıştır.

Prometheus verileri istatistikseldir, yani örneklenmiştir. Prometheus, verilerdeki eğilimleri belirlemede iyidir.

Prometheus’taki metriklerin şu özellikleri vardır:

-   bir adı,
-   bir veya daha fazla etiketi
-   ve bir türü ([metric types](https://prometheus.io/docs/concepts/metric_types/))

```
metrik-adı{etiket-adı="etiketin değeri",baska-etiket="32"} 123
```

Prometheus’da dört standart metrik türü vardır:

1.  Gauge,
2.  Counter,
3.  Summary
4.  ve Histogram.

Metrik türlerine tanım düzeyinde bakalım:

1.  **Sayaç**, bir arabada kat edilen kilometre sayısına benzer şekilde, bir gözlem sayısını temsil eden, giderek artan bir metriktir. Örneğin, ağdan indirilen paket miktarı sürekli artıyor ve sonsuza gider. Bu yüzden sayaç (counter) bu metriğin türü olacaktır.
2.  **Gösterge**, bir arabanın mevcut hızına benzer şekilde keyfi olarak aşağı ve yukarı gidebilen bir metrik. Mesela 4GB geçici belleğiniz (RAM) varsa an itibarıyla toplam kullanım oranını gösterge (gauge) metrik türüyle gösterebiliriz. Yani 0 ile 4 GB arasında aşağı yukarı giden değerdir.
3.  **Histogram** ve **özet (summary)**, gözlemleri istatistiksel kümeler halinde özetlemek için kullanılabilen iki metrik; bir histogram metriği için seçtiğiniz grup sayısı, metriğin önemliliğini etkiler

> Prometheus’tan kesin değerler beklemeyin. Size herhangi bir şeyin tam sayısını vermek için tasarlanmamıştır, bunun yerine neler olduğuna dair bir genel bakış sunar.

Prometheus bir **zaman serisi** (_timeseries_) **veritabanı** olduğundan, tüm veriler bir **zaman damgası** bağlamındadır.

-   Bir zaman damgasını kayıtlı verilere eşleyen seriye **zaman serisi** denir.
-   Yani zaman damgalı kayıtlar serisine **zaman serisi** denir.
-   Prometheus’da, bir dizi ilgili zaman serisine **vektör** denir.

`http_requests_total` öğesinin bir hizmet tarafından alınan “toplam http isteklerinin sayısını” temsil eden bir **vektör** olduğunu varsayalım. Vektörler, verileri bu şekilde işaretleyebilmemiz için “etiketler” adı verilen daha fazla boyut belirlememize izin verir.

```
http_requests_total{code="200"}
http_requests_total{handler="/api/v1/query"}
```

2 Tip vektör ve bu vektörler için 2 sorgulama türü vardır:

1.  instant vector (anlık vektör) için [instant query](https://prometheus.io/docs/prometheus/latest/querying/api/#instant-queries)
2.  range vector (aralık belirten vektör) için [range query](https://prometheus.io/docs/prometheus/latest/querying/api/#range-queries)

Anlık vektör; zaman damgasının **o an içindeki** tek bir veri noktasına eşlendiği bir dizi zaman dizisi. Başka bir deyişle, bir metrik; adı, etiketleri ve tek bir sayının birleşimiyle temsil ediliyorsa, bu bir **anlık vektör**dür.

```shell
curl 'http://localhost:9090/api/v1/query' \
  --data 'query=http_requests_total{code="200"}' \
  --data time=1608481001
{
  "metric": {"__name__": "http_requests_total", "code": "200"},
  "value": [1608481001, "881"]
}
```

Aralık vektörü; bir metriğin zaman içinde nasıl değiştiğini anlamak için kullanabiliriz. **Her zaman damgasının bir dizi veri noktasıyla eşleştiği** zaman dizisidir. Her zaman damgasıyla eşleşen değerler listesini oluşturmak için kullanılan “zaman aralığı vermemiz (`_[30s]_`)” gerekir.

-   **her zaman damgasının bir dizi veri noktasıyla eşleştiği** zaman dizisidir. Her zaman damgasıyla eşleşen değerler listesini oluşturmak için kullanılan “zaman aralığı vermemiz (`_[30s]_`)” gerekir.

```shell
curl 'http://localhost:9090/api/v1/query' \
  --data 'query=http_requests_total{code="200"}[30s]' \
  --data time=1608481001
{
  "metric": {"__name__": "http_requests_total", "code": "200"},
  "values": [
    [1608480978, "863"],
    [1608480986, "874"],
    [1608480094, "881"]
  ]
}
```

> **Anlık vektörlerin grafiği çizilebilir; ama aralık vektörlerin grafiği olamaz!**

Bunun nedeni; bir şeyin grafiğini çizmek için x ve y ekseninde bir noktayı işaret etmesi gerekir. Zaman damgasını (`timestamp`) x ekseninde, metriğin değerini (`value`) ise y ekseninde bir veri noktası olarak işaretleyerek grafik elde edilebilir.

Anlık vektörler, her zaman damgası için tek bir değere (`value`) sahipken, aralık vektörlerinin birden fazla değeri (`value**s**`) vardır. Bir metriğin grafiğini çıkarmak amacıyla, bir zaman serisinde tek bir zaman damgası için birden çok veri noktasının nasıl gösterileceği tanımsızdır.

-   Anlık vektörler **karşılaştırılabilir** ve üzerlerinde aritmetik işlemler yapılabilir;
-   Aralık vektörlerinde bunları yapamayız.

Bu aynı zamanda karşılaştırma ve aritmetik operatörlerin tanımlanma biçiminden de kaynaklanmaktadır. Her zaman damgası için, birden fazla değerimiz varsa, bunları benzer nitelikteki başka bir zaman dizisiyle nasıl toplayacağımızı veya karşılaştıracağımızı bilmiyoruz.

Artık “Aralık Vektörlerinin” grafik gösterim veya toplama işlemleri için kullanılamayacağını anlıyoruz. O halde, neden “aralık vektörleri” var? Cevap basit: sayaçlar. Sayaç temel türlerden biridir. Daha önceki örneğimize devam ederek, sayaçların ve aralık vektörlerinin nasıl etkileştiğini anlamaya çalışacağız.

Diyelim ki web servisimizin şu anda kaç isteğe cevap verdiğini öğrenmek istiyoruz. `http_requests_total{code=”200",handler=”/api/v1/query”}` metriğimiz, teker teker artan bir sayacı temsil eden değerlere sahip anlık bir vektördür. Bu sayaç, hizmetimizin aldığı toplam istek sayısını ölçer.

> **scrape** : kazımak anlamındadır. Kazıdığımız yerde hiç değer kalmıyor (değeri sıfıra iniyor) ancak tekrar birer birer artıyor ve sonra tekrar kazınıyor (sıfırlanıyor).

Prometheus’un geçmişte çeşitli zamanlarda bu sayacı “kazıdığını” biliyoruz, bu yüzden basitçe sayacın değerini öğrenmek istediğimizde, değerine 0 atayarak başlatıyoruz diyebiliriz.

```shell
curl 'http://localhost:9090/api/v1/query' \
  --data 'query=http_requests_total{code="200",handler="/api/v1/query"}'
{
  "metric": {
      "__name__": "http_requests_total", 
      "code": "200", 
      "handler":"/api/v1/query"
  },
  "value": [1608437313, "881"]
}
```

İstek **bir aralık içermiyor**: `'query=http_requests_total{code="...}'`  
Yanıtta, bu zamana kadarki toplam istek sayısı (`881`) döner.

Geçmişte belirli bir süre boyunca aldığı isteklerin sayısını önemsiyoruz, örneğin: son on beş dakika (`[15m]`).

Sahip olduğumuz tek şey sürekli artan bir sayaçken, bu sayıyı nasıl elde ederiz? Daha iyi bir yol, sayacın mevcut değerini almak ve on beş dakika önce görüldüğü gibi sayacın değerini çıkarmaktır. Bu bize, örneğin o süre içinde aldığı isteklerin tam sayısını verir.

Bunu PromQL’de temsil etmek için, anlık vektörü alır ve süremizi `[15m]` ekleriz. Bu parçaya **aralık seçici (range selector)** denir ve _anlık vektörü bir aralık vektörüne dönüştürür_. Ardından, aralığın başlangıcındaki veri noktasını sondakinden etkin bir şekilde çıkaran artırma (`increase()`) gibi bir işlev kullanırız.

```shell
curl 'http://localhost:9090/api/v1/query' \
  --data 'query=increase(http_requests_total{code="200",h.."}[15m])'
{
  "metric": {
     "__name__": "http_requests_total", 
     "code": "200", 
     "handler":"/api/v1/query"
  },
  "values": [
    [1608437313, "18.4"]
  ]
}
```

Sorguyu kelimelerle açıklamak gerekirse: “son on beş dakikadaki toplam istek sayısındaki artıştır”. Yanıt aynı zamanda yanıtı temsil eden tek bir sayı içeriyor, ki bu da beklediğimiz şeydi. Sonuç, şimdi daha fazla çizelgelenebilen veya toplanabilen bir anlık vektör biçimindedir.

`[increase(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)`, işlevine benzer olarak aşağıdaki fonksiyonlar da aralık vektörünü alıp, anlık vektöre dönüştürür, böylece grafik oluşturulabilir hale getirir:

-   `[changes(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#changes)`
-   `[absent_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#absent_over_time)`
-   `[delta(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#delta)`
-   `[deriv(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#deriv)`
-   `[holt_winters(range-vector, scalar, scalar)](https://prometheus.io/docs/prometheus/latest/querying/functions/#holt_winters)`
-   `[idelta(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#idelta)`
-   `[irate(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)`
-   `[predict_linear(range-vector, scalar)](https://prometheus.io/docs/prometheus/latest/querying/functions/#predict_linear)`
-   `[rate(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#resets)`
-   `[resets(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#resets)`
-   `[avg_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[min_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[max_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[sum_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[count_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[quantile_over_time(scalar, range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[stddev_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`
-   `[stdvar_over_time(range-vector)](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)`

Sayaçlarla ilgili ilkelere ve Prometheus’un diğer izleme sistemlerinden nasıl farklı olduğuna bakalım.

Sayaç bir şeyi sayar. Örneğin adımlarınızı, eskiden Cumhuriyet Altınlarınızı, “Sübhanallah” demelerinizi ve diğer her şeyi sayarlar.

> Prometheus belgelerinde **sayaç (**[**counter**](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)**)**; teker teker artan, birikimli bir ölçümdür. Yani birer birer artabilir. Kümülatif bir metrik olduğundan her zaman toplam değeri içerir.

Gerçekten bilmek istediğiniz şey, şu anda saniyede 12 istek aldığınızı bilmek gibi, ne kadar hızlı sayıldığıdır. Her istekte sayacı artırırsanız, bunu izleme sisteminizde grafik olarak gösterilebilmesi ve uyarılabilmesi için nasıl anlarsınız?

-   Üç yaygın yaklaşım vardır.

1düzenli olarak, örneğin dakikada bir, izleme sisteminize giden mevcut değeri çıkarırsınız, nihayetinde sayacı da 0'a getirirsiniz. Bu durum bir sorunla bitebilir! Eğer çektiğiniz veriyi push ederken başarısız olursanız, sayacı çektiğiniz metrik kaynağında veriyi 0'a getirdiğiniz ve ancak hedef metrik sunucusuna veriyi basamadığınız için tüm bilgileri kaybedersiniz. Bu, mikro trafik patlamasına karşı kör olmanıza neden olabilir. Başkaca, fazlalık için sayaçtan veri çeken iki sisteminiz varsa, her biri artışların yalnızca yarısını görecektir. Doğal olarak bu, istediğimiz durum olmayacak.

2 genellikle üstel olan bir tür [Hareketli Ortalama](https://en.wikipedia.org/wiki/Moving_average#Weighted_moving_average) kullanmaktır. Bu, son veri noktalarının eski veri noktalarından daha fazla öneme sahip olduğu anlamına gelir. Artış modelinin fazına ve frekansına bağlı olarak, izleme sisteminin bilgileri ne zaman örneklediğine bağlı olarak, tüm veri noktaları eşit olmadığından farklı sonuçlar alırsınız. Bu yaklaşım, numune alan birden fazla sistemi işleyebilir, ancak numune alınamazsa bilgileri kaybeder. Bu daha iyi, ancak mükemmel olmaktan uzak.

> Bir sayacın (`counter`) mevcut değeri önemli değildir. Önemli olan zamanla nasıl arttığıdır.
> 
> [rate](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) İşlevi; aralık vektöründeki zaman serisinin saniye başına ortalama artış oranını hesaplayan hız fonksiyonudur.

3. yaklaşımı Prometheus benimser. Bir sayaç 0'dan başlar ve artırılır. İstemci başka bir hesaplama yapmaz. Her veri çekilmesi aşamasında Prometheus bu durumun bir örneğini alır. **Prometheus’taki** `**rate()**` **işlevi, bir zaman aralığındaki zaman serilerinin geçmişine bakar ve saniyede ne kadar hızlı arttığını hesaplayarak bir oran sunar.** Bu, numune alan birden fazla Prometheus sunucusunu işleyebilir ve bir scrape başarısız olursa çözünürlüğü kaybedersiniz, ancak bir sonraki başarılı scrape işleminde verileri kaybetmezsiniz, artımlar kaybolmaz veya ortalaması alınmaz.

Yaygın bir soru, süreç yeniden başladığında ve sayaç 0'a sıfırlandığında ne olur? `rate()` bunu otomatik olarak halleder. Bir sayacın azaldığı her görüldüğünde, ilk veri noktasından hemen sonra 0'a sıfırlanmış gibi işlem görecektir. Bu, Sayaçların azaltılmasının mümkün olmamasını önemli kılar, azalma potansiyeline sahip bir Sayaç gerçekte bir Göstergedir. Graphite’in Negatif Türev olmayan işlevi gibi, diğer izleme sistemleri genellikle bir sayaç sıfırlaması gerçekleştiğinde bilgileri atar. `rate()` ayrıca, `rate()` öğesine ilettiğiniz tüm zaman aralığını tam olarak kapsamayan örnekleri işleme mantığına sahiptir.

> Artırma işlevi (`[increase](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)`), argüman olarak bir aralık vektörü alır ve bu aralıktaki sayaç değerinin artışını hesaplar.

`rate()`’e ek olarak, sayaçlarda çalışan diğer iki işlev olan `increase()` ve `irate()` vardır. `rate()` saniye başına değerleri döndürürken, `boost()` zaman aralığı boyunca toplamı döndüren bir kolaylık işlevidir. Örneğin, `increase(my_counter_total[1h])`, sayaçtaki artışı saat başına döndürür. `irate()` yalnızca son iki örneğe bakar ve kısa zaman dilimlerinde yüksek hassasiyetli grafikler için kullanışlıdır. `resets()` size sayacın ne sıklıkta sıfırlandığını söyler, bu da hata ayıklama için faydalı olabilir.

Son olarak, burada sadece isteklerin sayılmasından bahsetmiş olsak da, Sayaçlar, negatif olmayan herhangi bir kayan nokta değeriyle artırılabilir. Bunları, harcanan zamanı, işlenen baytları, atılan istisnaları veya yürünen kayıtları izlemek için kullanabilirsiniz.

## Komutlar

```shell
curl http://localhost:9090/api/v1/status/runtimeinfo | json_pp
curl http://localhost:9090/api/v1/status/buildinfo | json_pp
curl http://localhost:9090/api/v1/status/config | json_pp
curl http://localhost:9090/api/v1/status/targets | json_pp# Prometheus ayarlarını faal hale getirebilmek için yeniden başlatabilir veya kendiliğinden tekrar başlatmak için kill komutuna SIGHUP bayrağını, Pormetheus'un aktif proses ID değerine geçebilirzi
$ kill -HUP <prometheus-proses-id>
$ kill -s SIGHUP prometheus
$ sudo killall -HUP prometheus# HTTP isteğiyle tekrar başlatmak:
curl -X POST http://localhost:9090/-/reload# Komut satırından tekrar başlatmak:
/usr/local/bin/prometheus \
  --config.file /etc/prometheus/prometheus.yml \
  --storage.tsdb.path /monitoring/prometheus/prometheus_data/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.external-url=https://prom.example.com \
  --web.listen-address=:9090 \
  --web.enable-lifecycle \
  --web.enable-admin-api \
  --log.level=info# servis olarak çalışıyorsa:
sudo systemctl restart prometheus
```

-   **\--config.file** the directory containing the Prometheus configuration file
-   **\--storage.tsdb.path** Where Prometheus writes its database
-   **\--web.console.templates** Prometheus Console templates path
-   **\--web.console.libraries** Prometheus Console libraries path
-   **\--web.external-url** Prometheus External URL
-   **\--web.listen-addres** Prometheus running port
-   **\--web.enable-lifecycle** If you use the Prometheus /-/reload HTTP endpoint to automatically reload your Prometheus config when it changes, these endpoints are disabled by default for security reasons in Prometheus 2.0. To enable them, set the — web.enable-lifecycle flag
-   **\--web.enable-admin-api** controls access to the administrative HTTP API

## Kaynaklar:

-   [https://www.innoq.com/en/blog/prometheus-counters/](https://www.innoq.com/en/blog/prometheus-counters/)
-   [promql](https://prometheus.io/docs/prometheus/latest/querying/basics/)
-   [https://satyanash.net/software/2021/01/04/understanding-prometheus-range-vectors.html](https://satyanash.net/software/2021/01/04/understanding-prometheus-range-vectors.html)
-   [https://petermalmgren.com/prometheus-101/](https://petermalmgren.com/prometheus-101/)
-   [https://blog.fourninecloud.com/what-is-prometheus-and-how-to-install-and-configure-it-on-linux-server-9b5f88685451](https://blog.fourninecloud.com/what-is-prometheus-and-how-to-install-and-configure-it-on-linux-server-9b5f88685451)
-   [https://www.robustperception.io/reloading-prometheus-configuration](https://www.robustperception.io/reloading-prometheus-configuration)