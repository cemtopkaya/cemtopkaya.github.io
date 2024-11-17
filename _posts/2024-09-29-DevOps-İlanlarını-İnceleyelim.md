---
layout: post
title:  "DevOps İlanlarını İnceleyelim"
date:   2024-09-29 17:57:24 +0000
categories: DevOps
tags: DevOps SecOps
---


DevOps mühendisinden yazılımın kodlarını alarak derle, test et, entegre et, paketle, dağıt yeteneklerine sahip olması beklenir. Bunu yaparken hem geliştirme hem sistem yeteneklerinin bu rolün sahibinde olması beklenir.

experience implementing CI/CD pipelines using tools like …
----------------------------------------------------------

CI/CD (Continuous Integration/Continuous Deployment) pipeline’ları söz konusu olduğunda, aşağıdaki popüler uygulamalar göze çarpar:

1\. **Jenkins**:  
— Açık kaynaklı ve oldukça esnek bir otomasyon sunucusu.  
— Geniş eklenti ekosistemi ile en eski ve en yaygın kullanılan CI/CD araçlarından biri.  
— Neredeyse her türlü otomasyon görevini gerçekleştirebilecek esneklikte.

2\. **GitLab CI/CD**:  
— GitLab’in entegre CI/CD çözümü.  
— Git repolarıyla doğrudan entegrasyon sunar.  
— Kod deposu, issue takibi ve CI/CD süreçlerini tek bir platformda birleştirir.  
— Otomatik DevOps özellikleri ile hızlı kurulum imkanı sunar.

3\. **GitHub Actions**:  
— GitHub’ın kendi CI/CD çözümü.  
— GitHub repoları için kolay entegrasyon sağlar.  
— GitHub ekosistemi içinde sorunsuz entegrasyon sağlar.  
— Geniş bir marketplace ile özelleştirilebilir iş akışları sunar.

4\. CircleCI:  
— Bulut tabanlı CI/CD platformu.  
— Hızlı kurulum ve kolay konfigürasyon özellikleri ile öne çıkar.  
— Paralel iş çalıştırma ve Docker desteği gibi güçlü özelliklere sahip.

5\. Travis CI:  
— Açık kaynak projeler için ücretsiz olan popüler bir CI aracı.

6\. **Azure DevOps (eski adıyla VSTS)**:  
— Microsoft’un sunduğu kapsamlı DevOps çözümü.  
— Proje yönetimi, kod depoları, CI/CD, test yönetimi gibi birçok aracı tek çatı altında toplar.  
— Özellikle Microsoft teknolojileri ile çalışan ekipler için tercih edilir.

7\. TeamCity:  
— JetBrains tarafından geliştirilen güçlü bir CI/CD sunucusu.

8\. Bamboo:  
— Atlassian’ın CI/CD çözümü, özellikle Jira ve Bitbucket ile iyi entegre olur.

9\. GoCD:  
— ThoughtWorks tarafından geliştirilen açık kaynaklı bir CI/CD sunucusu.

10\. Drone:  
— Container-native bir CI/CD platformu.

Proficiency using version control systems (Git, Github etc.) and branching strategies (e.g., GitFlow)
-----------------------------------------------------------------------------------------------------

Bu başlıkla ilgili güzel bir [bağlantı](https://jobya.com/library/roles/LakfNmvt/automotive_software_developer/junior/questions/clqpei5m90rfv18rw29aygzky/describe_your_experience_with_version_control_systems_like_git) buldum. Türkçesine bakarak vermek istediği mesajı anlayabiliriz:

> Kariyerim boyunca Git ile ilgili kapsamlı deneyim kazandım ve yazılım geliştirme süreçlerini kolaylaştırmak için yeteneklerinden yararlandım. Yazılım mühendisi olarak önceki rolümde Git, ekip içinde kod kalitesini ve iş birliğini sağlamada önemli bir rol oynadı. Örneğin, özellik dalları (feature branches), çekme istekleri (pull requests) ve kod incelemeleri (code review) içeren bir Git iş akışı uyguladım. Bu, bilgi paylaşımını ve iş birliğini teşvik ederken temiz ve kararlı bir kod tabanını korumamızı sağladı. Ayrıca, kod kalitesi kontrollerini otomatikleştirmek ve her gönderimde testler çalıştırmak için Git kancalarını ve sürekli entegrasyonu kullandım. Bu, sorunları erken yakalamamıza ve yüksek düzeyde kod kararlılığı sağlamamıza yardımcı oldu. Ayrıca, sorunsuz entegrasyonlar oluşturmak ve ekip üretkenliğini artırmak için Git’i JIRA ve Bitbucket gibi diğer araçlarla kullanma deneyimim var. Genel olarak, Git ile ilgili kapsamlı deneyimim, yazılım geliştirme sürecini optimize etmek için özelliklerini kullanma konusunda beni yetenekli hale getirdi.

Dallanma Stratejileri (Branching Strategies)
--------------------------------------------

Bir yöntemin farklı isimlendirmelerini göreceksiniz ve aklınız karışacak. Her zaman olduğu gibi bir tabloyla aklımız karışmadan bilelim kim kimdir diye:

```
| Term                    | Equivalent                  |
|-------------------------|-----------------------------|
| **Git Flow**            | **Release Branching**       |
| GitHub Flow             | Feature Branching           |
| ***GitLab Flow***       | ***Environment Branching*** |
| *Forking Strategy*      | *Forking Strategy*          |
| Trunk Based Development | Trunk Based Development     |
```


*   **Trunk-based development:** Tüm geliştirmelerin tek bir ana dalda yapıldığı bir yöntemdir.

[https://youtu.be/U\_IFGpJDbeU?t=79&si=VFl4WBnF58cZNrgd](https://youtu.be/U_IFGpJDbeU?t=79&si=VFl4WBnF58cZNrgd)

*   **Git Flow:** Sürekli dağıtım, yazılım geliştirme sürecinde yapılan her değişikliğin, otomatik olarak ve güvenilir bir şekilde üretim ortamına (yani kullanıcıların erişebileceği ortama) dağıtılması anlamına gelir. Yani feature dallarınızı otomasyon araçları ve sıkı bir kalite güvence sistemiyle test ederek master dalınıza sürekli birleştiriyor olmanız gerekir.  
    Temel branch’ler şunlardır:  
    — **Master**: Yayınlanmış ve stabil sürümleri temsil eder.  
    — **Develop**: Geliştirme sürecindeki en son kodu barındırır.  
    **— Feature**: Yeni özelliklerin geliştirilmesi için oluşturulan geçici dallardır.  
    **— Release**: Bir sürüm için hazırlık aşamasında kullanılan dallardır.  
    **— Hotfix:** Canlı sistemdeki acil hataların düzeltilmesi için kullanılır

*   **Feature branching (GitHub Flow):** Her özellik için ayrı bir dal oluşturulan bir yöntemdir. GitHub Flow, karmaşık dallanma modellerinden uzak durur. Her yeni özellik veya hata düzeltmesi için ana daldan (genellikle `main` veya `master` olarak adlandırılır) yeni bir dal oluşturulur. Bu, dallanma süreçlerini oldukça basit hale getirir.

*   **Release branching:** Git flow’a benzer olmasına rağmen **Release branch**, belirli bir sürüm için geçici bir çözüm sunarken, **Git Flow** daha geniş kapsamlı bir dal yönetim modelidir. Her sürüm için ayrı bir dal oluşturulan bir yöntemdir. Release branch, özellikle bir sürümün son aşamasında oluşturulur. Geliştirme süreci tamamlandıktan sonra, bu branch üzerinden yalnızca hata düzeltmeleri ve son düzenlemeler yapılır. Bu süreç, yeni özelliklerin geliştirilmesini durdurur ve mevcut sürümün stabil hale getirilmesine odaklanır. Release branch oluşturulduğunda, bu branch’teki değişiklikler test edilerek onaylandıktan sonra master ve develop branch’lerine birleştirilir.
*   **Environment branching (Gitlab Flow):** Artık tercih edilmeyen, her bir çalışma ortamı (geliştirme, test, üretim vb.) için ayrı bir kod dalı oluşturma yaklaşımıdır.

*   **Forking:** Bu strateji, açık kaynak projeleri için yaygın olarak kullanılır. Bu strateji, herkesin projenin bir kopyasını oluşturabileceği ve kendi değişikliklerini yapabileceği anlamına gelir. Örneğin, bir açık kaynak projesine katkıda bulunmak istiyorsanız, projenin bir kopyasını oluşturabilirsiniz. Kopyanızda değişiklikler yapabilirsiniz ve değişikliklerinizi ana projeye geri birleştirebilirsiniz.

Dallanma stratejileri, kod değişikliklerinin nasıl organize edileceğini, test edileceğini ve ana kod tabanına nasıl birleştirileceğini belirler. GIT dallanma stratejileri, teknoloji ekiplerinin kodlarını bir GIT sistemindeki farklı dallar aracılığıyla düzenlemek ve yönetmek için kullandıkları kalıplar veya yaklaşımlardır.

Sürüm kontrol sisteminize bağlı olarak ana dal, `mainline`, `default` veya `trunk` olarak adlandırılabilir. Geliştiriciler ana kod satırından kendi dallarını oluşturabilir ve bunun üzerinde bağımsız olarak çalışabilirler. Her strateji, iş birliğini, istikrarı ve sürüm yönetimini kolaylaştırmak için dalların oluşturulması, adlandırılması ve birleştirilmesine ilişkin kuralları ve yönergeleri tanımlar. Bir geliştirici bir dal oluşturduğunda, sürüm kontrol sistemi o noktada kod tabanının bir kopyasını oluşturur. Daldaki değişiklikler ekipteki diğer geliştiricileri etkilemez. Bu iyi bir şeydir, açıkçası, çünkü geliştirilmekte olan özellikler istikrarsızlık yaratabilir ve tüm çalışmalar ana kod satırında gerçekleşiyorsa bu oldukça yıkıcı olur. Geliştiriciler, özellikler üzerinde işbirliği yapmak ve özel dallarının ana daldan çok uzaklaşmamasını sağlamak için diğer geliştiricilerden değişiklikleri kolayca çekebilirler.

Tek bir ana dala odaklanma ve sürekli entegrasyona önem vermesiyle **Trunk-Based Development (TBD)**, çevik geliştirmede popülerlik kazanmıştır.

[](https://trunkbaseddevelopment.com/)

Trunk-Based Development (Gövde Tabanlı Geliştirme) yaklaşımı daha iyi dir çünkü,

1\. Tek bir paylaşılan dal kullanılır (`Trunk` veya `main`, `master` olarak adlandırılır).  
2\. Geliştiriciler doğrudan bu ana dala küçük ve sık commitler yaparlar.  
3\. Sürüm dalları oluşturulmaz, bu da karmaşıklığı azaltır.  
4\. Sürekli entegrasyon sağlanır, çünkü herkes aynı dal üzerinde çalışır.  
5\. Her commit öncesi bir ön entegrasyon adımı vardır (build’in geçmesi gerekir).

**Feature Branching -FB-** (Uzun ömürlü özellik dalları):

1.  Ana daldan (`mainline`) ayrılan uzun ömürlü özellik dalları kullanılır.  
    2\. Bu yaklaşım “neredeyse her zaman bir felakettir” olarak tanımlanır.  
    3\. Sürüm `12.1` ve `12.2` arasında birleştirme sorunları yaşanır.  
    4\. Ekip, bu dallarda geliştirme yaparken kendini yalıtır ve entegrasyon zorlukları yaşar.  
    5\. Birleştirme işlemi ağrılı ve zor olabilir (“**Merge pain**” olarak belirtilmiş).

**Senaryoda İnceleyelim:**

*   **3 Yazılımcı, 1 Ana Dal (main):** TBD’de bu senaryo, tüm geliştiricilerin aynı anda ana dal üzerinde çalıştığı anlamına gelir. Bu, sık sık çakışmalara (confliction) yol açabilir. Ancak TBD, bu çakışmaları sık sık ve küçük adımlarla çözmeyi hedefler. Yani, her geliştirici sık sık ana dala birleştirme yapar ve çakışmalar hemen çözülür.
*   **Feature Branching:** FB’de ise her özellik için ayrı bir dal oluşturulur. Bu, çakışmaları geciktirebilir, ancak birleştirme (merge) zamanı daha büyük çakışmalarla karşılaşabilirsiniz. Özellikle uzun süreli geliştirmelerde veya büyük özellikler için bu durum daha olasıdır.
*   **Hangi Strateji Daha İyi?**

TBD ve FB’nin her birinin kendine göre avantajları ve dezavantajları vardır. Hangisinin daha iyi olduğu, takımın büyüklüğü, projenin karmaşıklığı, sürekli entegrasyon süreçleri gibi birçok faktöre bağlıdır.

**TBD’nin Avantajları:**

*   **Daha sık entegrasyon:** Çakışmalar daha erken tespit edilir ve çözülür.
*   **Daha iyi kod kalitesi:** Sık sık yapılan kod incelemeleri sayesinde kod kalitesi artar.
*   **Daha hızlı geri bildirim:** Değişiklikler daha hızlı bir şekilde üretime yansır.

**TBD’nin Dezavantajları:**

*   **Sık sık çakışmalar:** Özellikle büyük ekiplerde sık sık çakışmalar yaşanabilir.
*   **Daha fazla dikkat gerektirir:** Geliştiricilerin sürekli olarak ana dala birleştirme yapması ve çakışmaları çözmesi gerekir.

**FB’nin Avantajları:**

*   **Daha az sıklıkta çakışma:** Büyük özellikler için daha az sıklıkta çakışma yaşanır.
*   **Daha bağımsız çalışma:** Geliştiriciler kendi dallarında daha bağımsız çalışabilirler.

**FB’nin Dezavantajları:**

*   **Birleştirme sorunları:** Büyük özellikler için birleştirme sırasında büyük çakışmalar yaşanabilir.
*   **Eski kodlarla çalışma:** Geliştiriciler, ana dalda yapılan değişiklikleri kendi dallarına birleştirmek zorunda kalabilirler.

Sonuç olarak;

*   **Sık sık küçük değişiklikler yapıyorsanız ve sürekli entegrasyona önem veriyorsanız:** TBD daha uygun olabilir.
*   **Büyük ve karmaşık özellikler geliştiriyorsanız ve daha bağımsız çalışma istiyorsanız:** FB daha uygun olabilir.

Trunk-Based Development, daha sık entegrasyon, daha az karmaşıklık ve daha sık birleştirme sorunu sağladığı için tercih edilir. Bu yöntem, ekibin daha verimli çalışmasına ve kod tabanının daha istikrarlı olmasına yardımcı olur.

Bu görsel, ölçeklendirilmiş Trunk-Based Development (Gövde Tabanlı Geliştirme) yaklaşımını açıklamaktadır.

1\. Ana Dal (`Trunk` veya `main`, `master`):  
— Tüm geliştirme ekibi tarafından paylaşılan tek bir ana dal kullanılır.  
— Geliştiriciler bu **ana dala doğrudan commit yapmazlar**.

2\. Kısa Ömürlü Özellik Dalları:  
— Ölçeklendirilmiş Trunk-Based Development, kısa ömürlü özellik dalları kullanır.  
— Bu dallar genellikle **bir kişi tarafından birkaç gün içinde (en fazla) geliştirilir.**  
— Pull Request tarzı kod incelemesi ve otomatik build süreçlerinden geçer.  
— İnceleme ve testlerden sonra ana dala (`trunk` veya `master`) birleştirilir.

3\. Sürüm Yönetimi:  
— Geliştiriciler **doğrudan sürüm dallarına commit yapmazlar**.  
— **Sürümler** (örn. 12.1, 12.2) **ana daldan oluşturulur**.

4\. Sürekli Entegrasyon ve Teslimat:  
— Bu yaklaşım, Sürekli Entegrasyon (Continuous Integration) ve Sürekli Teslimat (Continuous Delivery) için temel oluşturur.  
— Ekip üyeleri gün içinde birden fazla kez ana dala değişiklik yapabilir.  
— Kod tabanı her zaman yayınlanabilir durumda tutulur.

5\. Ölçeklenebilirlik:  
— Küçük ekiplerden büyük ekiplere geçiş için uygun bir yöntemdir.  
— Ekip büyüklüğü ve commit sıklığına göre uyarlanabilir.

6\. Uygulama Detayları:  
— Geliştiriciler, değişikliklerini göndermeden önce kendi iş istasyonlarında testleri çalıştırır.  
— Diğer geliştiricilerin (veya botların) incelemesi için değişiklikleri paylaşırlar.

Bu yaklaşım, hızlı geliştirme, sürekli entegrasyon ve yüksek kaliteli kod üretimi arasında bir denge kurmayı amaçlar.

Environment Branching Üzerine Detaylı Bir Açıklama ve Örnekler
--------------------------------------------------------------

**Environment Branching** (Çevre Dallanması), yazılım geliştirmede her bir çalışma ortamı (geliştirme, test, üretim vb.) için ayrı bir kod dalı oluşturma yaklaşımıdır. Bu, farklı ortamlarda farklı yapılandırmalara ihtiyaç duyulan projelerde yaygın olarak kullanılır. Ancak, günümüzde genellikle daha esnek ve yönetilebilir yöntemler tercih edildiğinden, bu yaklaşımın popülaritesi azalmıştır.

**Environment Branching Örneği:**

Örneğin, bir e-ticaret uygulaması geliştiren bir ekip düşünelim. Bu uygulama, geliştirme, test ve üretim olmak üzere üç farklı ortamda çalışacak. Her ortamda farklı bir veritabanı bağlantısı, farklı API anahtarları ve farklı yapılandırmalar kullanılacak. Bu durumda, environment branching şu şekilde uygulanabilir:

*   **main** (Ana Dal): Tüm geliştirmelerin yapıldığı temel dal.
*   **develop** (Geliştirme Dalı): Geliştiricilerin yeni özellikler ekleyip test ettiği dal.
*   **test** (Test Dalı): Uygulamanın test edildiği dal. Üretim ortamına benzer bir yapıya sahiptir.
*   **production** (Üretim Dalı): Canlı olarak kullanılan uygulamaya ait dal.

**Diğer Örnekler:**

*   **Farklı Müşteriler İçin Farklı Dallar:** Bir şirket, farklı müşteriler için özelleştirilmiş uygulamalar geliştiriyorsa, her müşteri için ayrı bir dal oluşturabilir. Örneğin:
*   main
*   customerA
*   customerB
*   **Farklı Özellik Seti İçin Farklı Dallar:** Bir uygulamada farklı özellik setleri (örneğin, ücretsiz ve ücretli versiyonlar) varsa, her özellik seti için ayrı bir dal oluşturulabilir. Örneğin:
*   main
*   free\_version
*   premium\_version

**Environment Branching’in Dezavantajları:**

*   **Karmaşıklık:** Çok sayıda dal yönetmek zor olabilir.
*   **Bakım Zorluğu:** Her dalın güncel tutulması gerekir.
*   **Kaynak İsrafı:** Her dal için ayrı bir ortam oluşturmak maliyetli olabilir.

**Neden Günümüzde Az Kullanılıyor?**

*   **Configuration Management:** Günümüzde, Ansible, Puppet gibi araçlarla yapılandırmalar kodda tutulur ve farklı ortamlara otomatik olarak uygulanır. Bu sayede, her ortam için ayrı bir dal oluşturmaya gerek kalmaz.
*   **Feature Flags:** Özellikler, kodda kalır ancak feature flags (özellik bayrakları) kullanılarak farklı ortamlarda aktif veya pasif hale getirilebilir.
*   **Continuous Integration/Continuous Deployment (CI/CD):** CI/CD süreçleri, kodun otomatik olarak test edilmesini ve farklı ortamlara dağıtılmasını sağlar. Bu sayede, dallanma ihtiyacı azalır.

**Özetle:**

*   Environment branching, her çalışma ortamı için ayrı bir dal oluşturma yaklaşımıdır.
*   Günümüzde daha az kullanılır çünkü daha iyi alternatifler mevcuttur.
*   Configuration management, feature flags ve CI/CD gibi araçlar, environment branching’e göre daha esnek ve yönetilebilirdir.

Proficiency in scripting languages like Python, Shell scripting, or Ruby, and understanding of programming languages such as Java, .Net, JavaScript, C, C++, or Go for automation and tool development
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

CompTIA Security+ (or equivalent) DoD 8 570.01-M IAT Level II Certification
---------------------------------------------------------------------------

CompTIA Security+ (veya eşdeğeri) sertifikası, özellikle ABD Savunma Bakanlığı’nın (DoD — [United States Department of Defense](https://en.wikipedia.org/wiki/United_States_Department_of_Defense)) 8570.01-M standardına uygun olarak belirlenen IAT (Information Assurance Technician) Level II sertifikası olarak kabul edilir. Bu sertifika, temel siber güvenlik becerilerini doğrulamak ve bilgi güvenliği alanında kariyer yapmak isteyen bireyler için kritik bir adımdır.

CompTIA Security+ sertifikası, güvenlik kavramları, ağ güvenliği, kimlik yönetimi ve erişim kontrolü, tehditler ve zayıflıklar, kriptografi, güvcenlik politikaları ve uyumluluk gibi alanlarda bilgi ve becerileri değerlendirir.

**“Security as Code”** kavramı, güvenlik uygulamalarının ve süreçlerinin yazılım geliştirme ve dağıtım süreçlerine entegre edilmesi anlamına gelir. Bu yaklaşım, güvenliğin bir tasarım ilkesi olarak ele alınmasını ve otomatikleştirilmiş süreçler aracılığıyla sürekli olarak uygulanmasını sağlar.

1\. **Güvenlik Kavramları**: Temel güvenlik terminolojisi, tehditler, zayıflıklar ve risk yönetimi.  
2\. **Ağ Güvenliği**: Güvenli ağ mimarisi, güvenlik protokolleri ve ağ saldırı yöntemleri.  
3\. **Kimlik Yönetimi ve Erişim Kontrolü**: Kullanıcı kimlik doğrulama yöntemleri ve yetkilendirme süreçleri.  
4\. **Tehditler ve Zayıflıklar:** Saldırı türleri, zararlı yazılımlar ve güvenlik açıklarının analizi.  
5\. **Kriptografi**: Şifreleme yöntemleri ve veri koruma teknikleri.  
6\. **Güvenlik Politikaları ve Uyumluluk:** Yasal gereklilikler, standartlar ve en iyi uygulamalar.

Sertifikayı elde etmek için kurs ve kitaplar takip edilebilir.

[**https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-601-exam-objectives-(2-0).pdf**](https://comptiacdn.azureedge.net/webcontent/docs/default-source/exam-objectives/comptia-security-sy0-601-exam-objectives-\(2-0\).pdf)

CompTIA Security+ sertifikası, siber güvenlik alanında kariyer yapmak isteyenler için önemli bir başlangıç noktasıdır. Gerekli bilgi birikimine sahip olmak ve uygun kaynaklarla çalışmak, bu sertifikayı başarıyla almanıza yardımcı olacaktır.

Linkedin Learning ~24 saatlik [Prepare for the CompTIA Security+ (SY0–601) Certification Exam](https://www.linkedin.com/learning/paths/prepare-for-the-comptia-security-plus-sy0-601-certification-exam?ysclid=m27ozn3q61188995684) eğitimi

Integrate and manage security tooling into CI/CD processes for continuous security feedback; including SAST, DAST and other automated vulnerability analysis tool sets.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

Bir yazılım geliştirme sürecinde sadece E2E (uçtan uca) testler veya birim testleri ile uygulamanın işlevlerini yerine getirip getirmediği sınanırken güvenlik açıkları gözden kaçırabilir. Güvenlik SAST, DAST, IAST, RASP

**Statik Uygulama Güvenliği Testi (SAST)** ve **Dinamik Uygulama Güvenliği Testi (DAST)** birbirini tamamlayan özelliklere sahiptir. SAST kaynak kodunu analiz ederek, geliştiricilerin erken aşamalarda kodu en iyi güvenlik uygulamalarına uyumlandırmasını sağlarken, DAST uygulamanın yalnızca çalışma anında tespit edilebilecek güvenlik açıklarını ortaya çıkarmak için saldırı simülasyonları gerçekleştirip, nasıl davrandığını kontrol eder. Bu kombinasyon, güvenlik açıklarının hem geliştirme hem de üretim aşamalarında tespit edilmesini sağlar.

*   Bandit ile Python kodunuzda,
*   CppCheck ile C++ kodunuzda,
*   Ticari uygulama olan SonarQube ile çok daha zengin programlama dillerinizde SAST denetimleri yapabilirsiniz.
*   Ayrıca OWASP ZAP ile dinamik uygulama güvenliği. Örnek iş ilanı maddesi: _OWASP Top 10: Experience and expertise of security concepts_
*   Trivy ile konteynerlerinizin görüntülerinin zararlı tespitlerini sağlayabilirsiniz.
*   [HCL AppScan](https://github.com/marketplace/actions/hcl-appscan-codesweep)’in SAST topluluk sürümü herkesin kullanımı için ücretsiz olup Python, Ruby, JS (Vue, Node, Angular, JQuery, React, vb.), PHP, Perl, Go, TypeScript ve daha fazlasını destekler ve sıklıkla yeni diller eklenir.

1.  DevSecOps, **yazılım geliştirme** \+ **güvenlik** + ve **operasyon** süreçlerini entegre eden bir yaklaşımdır.
2.  Bu yöntem, yazılım geliştirme yaşam döngüsü boyunca güvenliği sağlamak için tasarlanmıştır ve tüm ekip üyelerinin güvenlik sorumluluğunu paylaşmasını hedefler.
3.  Uygulama dağıtıldıktan sonra, güvenlik sorunları için sürekli izleme yaparak potansiyel tehditlerin erken tespit edilmesine yardımcı olur.

İkinci cümleyi açıklayalım:

1\. `“**güvenliği sağlamak” :**` DevSecOps, güvenliği **yazılım geliştirme sürecinin her aşamasına entegre eder**ek, güvenlik açıkları erken aşamalarda tespit edilir ve düzeltme fırsatları sağlanır.

2\. `**“yaşam döngüsü boyunca” :**` Sürekli entegrasyon ve sürekli teslimat (CI/CD) süreçlerine güvenlik testleri ve kontrolleri eklenir, böylece insan hataları azaltılır.

3\. `**“herkesin güvenlik sorumluluğunu paylaşması” :**` Geliştiriciler, güvenlik uzmanları ve operasyon ekipleri arasında iletişimi güçlendirir ve güvenliğin ortak bir sorumluluk haline gelmesini teşvik eder. Bunun için ekipler arasında güvenlik farkındalığını artırarak ortak hedefler doğrultusunda çalışma kültürünü destekleyecek güvenlik eğitimi verir, bu da genel güvenlik kültürünü güçlendirir. Örnek ilan “_Security training and outreach for internal development teams_”.

Diğer Güvenlik Testleri:

*   **IAST (İnteraktif Uygulama Güvenlik Testi):** Hem statik hem dinamik testleri birleştirerek uygulama içindeki güvenlik açıklarını gerçek zamanlı olarak tespit eder. Uygulamanın içindeki bir ajan aracılığıyla, kodun çalışma zamanındaki davranışını, veri akışını ve yapılandırma bilgilerini analiz eder.  
    — Contrast Security  
    — HCL AppScan  
    — Seeker by Synopsys
*   **RASP (Runtime Application Self-Protection):** Uygulama çalışırken gerçek zamanlı güvenlik korumaları sağlar ve saldırıları anında engelleyebilir. Uygulamanın performansını etkileyebilecek güvenlik sorunlarını izler.  
    — Contrast Protect  
    — Signal Sciences (şimdi Fastly)

**Kaynaklar**

*   [https://www.youtube.com/watch?v=l3pRhfAbMZ4](https://www.youtube.com/watch?v=l3pRhfAbMZ4)
*   [https://www.securefors.com/sast-dast-guvenli-kod-analizi-nedir/](https://www.securefors.com/sast-dast-guvenli-kod-analizi-nedir/)
*   [OWASP’tan Kaynak Kod Analiz Araçları](https://owasp.org/www-community/Source_Code_Analysis_Tools)

Experience in virtualization technology, including, but not limited to, VMware
------------------------------------------------------------------------------

En az bir tane sanallaştırma platformunu bilmek farz arkadaşlar. Gerek ki 3–5 tanesini kendiniz denemiş en az 1 tanesinde profesyonel hayatınızda tecrübe edinmiş olmalısınız. Hyper-V, VmWare, KVM gibi OS sanallaştırmalarından konteyner teknolojilerine (Docker, K8s, Openshift, Podman vs.) heybenizde bir şeyler bulunmalı.

\- Experience with automated testing, continuous delivery, and Git-based workflows.  
\- Proficiency in automation and continuous delivery methods  
\- You will participate in all phases of the development process — writing the code, configuring the servers, building the continuous delivery pipeline, all phases of testing, and finally, deploying the new service to the users. (IBM-Full stack developer)  
\- You will be involved in increasing operational maturity, scaling systems, and enhancing their continuous delivery infrastructure. Additionally, you will contribute to the development and management of their modern cloud-based microservice architecture. (Cyber Search-Sen. Soft. Eng.)  
\- DevOps, Automated Testing and Continuous Delivery (Full Stack Dev.)  
\- Work to develop and maintain a highly automated Continuous Delivery pipeline. (Tata Java Full Stack Dev.)  
\- While there are many tools in the toolbox, we are passionate about Test Driven Development, Continuous Integration, Continuous Delivery, Culture of Experimentation and Lean Agile as a way of operating.(Microsoft — Senior Soft. Eng.)  
\- Experience in Continuous Integration CI and Delivery CD concepts, and capabilities to support automation, pipelines, virtualization, and containerization (Mastercard — Sen. Soft. Eng.)