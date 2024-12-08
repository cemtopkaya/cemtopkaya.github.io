---
layout: post
author: Cem Topkaya
title:  "Temiz Kod"
date:   2024-12-08 17:57:24 +0000
categories: code
tags: clean-code
---
# Temiz Kodlama

Robert C. Martin'in (Uncle Bob) "Clean Code: A Handbook of Agile Software Craftsmanship" kitabının ana ve alt başlıkları şu şekildedir:

1. Temiz Kod (Clean Code)
   - Kod Kalitesinin Önemi
   - İyi Yazılmış Kodun Karakteristikleri
   - Kodun Okunabilirliği ve Bakımı

2. Anlamlı İsimler (Meaningful Names)
   - Değişken İsimlendirme
   - Metot İsimlendirme
   - Sınıf İsimlendirme
   - İsimlendirmede En İyi Uygulamalar

3. Fonksiyonlar (Functions)
   - Küçük Fonksiyonlar
   - Fonksiyon Uzunluğu
   - Fonksiyon Argümanları
   - Yan Etki Olmayan Fonksiyonlar
   - Komut ve Sorgu Ayrımı

4. Yorum Satırları (Comments)
   - Gereksiz Yorumlardan Kaçınma
   - Açıklayıcı ve Yararlı Yorumlar
   - Kod Kendini Açıklamalıdır

5. Kod Formatı (Code Format)
   - Dikey Biçimlendirme
   - Yatay Biçimlendirme
   - Takım Kodlama Standartları

6. Nesneler ve Veri Yapıları (Objects and Data Structures)
   - Veri Soyutlaması
   - Nesne Yönelimli Tasarım
   - Polimorfizm

7. Hata İşleme (Error Handling)
   - İstisna Yönetimi
   - Hata Yakalama Stratejileri
   - Temiz Hata İşleme Teknikleri

8. Birim Testleri (Unit Tests)
   - Test Edilebilir Kod Yazma
   - Test Sürücü Geliştirme (TDD)
   - Test Kalitesi
   - Test Bakımı

9. Sınıflar (Classes)
   - Sınıf Organizasyonu
   - Sınıf Boyutu
   - Sınıf Bağımlılıkları
   - Sınıf Tasarım İlkeleri

10. Sistemler (Systems)
    - Yazılım Mimarisi
    - Ayrılma ve Modülerlik
    - Ölçeklenebilir Sistemler

11. Eş Zamanlılık (Concurrency)
    - Eş Zamanlı Kod Zorlukları
    - İş Parçacığı Yönetimi
   - Eş Zamanlılık Desenler

12. Progressive Refinement (Sürekli İyileştirme)
    - Kod Yeniden Düzenleme
    - Teknik Borç Yönetimi
    - Sürekli Kod İyileştirme

Bu başlıklar, Uncle Bob'un temiz kod yazma felsefesini ve yazılım mühendisliğinde kaliteli kod üretmenin önemini ele almaktadır. Her bir başlık, yazılımcıların günlük pratiklerinde uygulayabilecekleri somut öneriler ve prensipler içermektedir.

İyi yazılmış kodun karakteristikleri şunlardır:

1. **Okunabilirlik**: Kod, insanlar tarafından kolayca anlaşılabilir olmalıdır. Bu, net ve açık ifadeler kullanmak, gereksiz karmaşıklıktan kaçınmak ve kodun kendini açıklaması anlamına gelir. Başka bir geliştiricinin kodu ilk bakışta anlayabilmesi hedeflenmelidir.

2. **Bakım Kolaylığı**: İyi yazılmış kod, gelecekte değişiklik ve güncellemelere açık olmalıdır. Modüler bir yapı, düşük bağımlılık ve net sorumluluk alanları olan bir kod, bakımı kolaylaştırır.

3. **Performans**: Verimli ve optimize edilmiş kod, sistemin kaynaklarını etkin bir şekilde kullanmalıdır. Gereksiz işlemlerden kaçınılmalı ve algoritmaların en uygun şekilde uygulanması sağlanmalıdır.

4. **Test Edilebilirlik**: Birim testlere uygun, bağımsız ve yan etkisi az olan fonksiyonlar, kodun güvenilirliğini ve kalitesini artırır. Her bir kod bloğu bağımsız olarak test edilebilir olmalıdır.

5. **Tutarlılık**: Kodlama stillinde, isimlendirmede ve mimari yaklaşımda tutarlılık, kodun bütünlüğünü korur. Takım içinde ortak kodlama standartları belirlenmeli ve uygulanmalıdır.

6. **Basitlik**: Karmaşık çözümlerden ziyade, basit ve net çözümler tercih edilmelidir. "KISS" (Keep It Simple, Stupid) prensibi burada devreye girer. Kod ne kadar az ve öz olursa, anlaşılması ve bakımı o kadar kolay olur.

7. **Hata Toleransı**: İyi yazılmış kod, olası hataları önceden tahmin edebilen ve güvenli bir şekilde yöneten mekanizmalara sahip olmalıdır. Hata yakalama ve yönetme stratejileri net bir şekilde uygulanmalıdır.

8. **Genişletilebilirlik**: Kod, gelecekteki gereksinimlere uyum sağlayacak şekilde tasarlanmalıdır. Esnek ve genişletilebilir bir mimari, yazılımın uzun vadeli yaşam döngüsünü destekler.

Bu karakteristikler, Uncle Bob'un temiz kod felsefesinin temelini oluşturur ve bir yazılımcının günlük pratiklerinde sürekli olarak göz önünde bulundurması gereken unsurlardır.

### Anlamlı İsimler (Meaningful Names)
Anlamlı İsimler (Meaningful Names) başlığını inceleyeceğiz. Bu başlık altında değişken, metot ve sınıf isimlendirmelerini ele alacağız.

1. Değişken İsimlendirme:

Kötü Örnek:
```python
def calc(a, b, c):
    return a * b + c
```

İyi Örnek:
```python
def calculate_total_product_price(unit_price, quantity, discount):
    return unit_price * quantity + discount
```

2. Metot İsimlendirme:

Kötü Örnek:
```python
def do(x):
    return x > 18
```

İyi Örnek:
```python
def is_user_adult(user_age):
    return user_age >= 18
```

3. Sınıf İsimlendirme:

Kötü Örnek:
```python
class A:
    def __init__(self, x, y):
        self.p1 = x
        self.p2 = y
```

İyi Örnek:
```python
class CustomerAddress:
    def __init__(self, street, city):
        self.street_name = street
        self.city_name = city
```

İş Görüşme Sorusu Örnekleri:

1. Değişken İsimlendirme Sorusu:
```python
# Verilen kod parçasını daha anlaşılır ve açıklayıcı şekilde yeniden düzenleyin
def process(d, t):
    r = 0
    for i in d:
        if i > t:
            r += i
    return r

# Beklenen cevap ve açıklama:
# def calculate_total_over_threshold(data_list, threshold):
#     total_sum = 0
#     for item in data_list:
#         if item > threshold:
#             total_sum += item
#     return total_sum
# 
# Açıklama:
# - 'd' -> 'data_list': Veri listesinin ne olduğunu açıkça belirtir
# - 't' -> 'threshold': Eşik değerinin amacını netleştirir
# - 'r' -> 'total_sum': Toplam sonucun ne olduğunu açıklar
# - 'i' -> 'item': Liste elemanını tanımlar
```

2. Metot İsimlendirme Sorusu:
```python
# Aşağıdaki metodu daha açıklayıcı ve amacını net bir şekilde belirten 
# bir isimlendirmeyle yeniden yazın
def check(s):
    return len(s) > 5 and '@' in s

# Beklenen cevap ve açıklama:
# def is_valid_email_address(email_string):
#     return len(email_string) > 5 and '@' in email_string
# 
# Açıklama:
# - Metot adı, işlevin tam olarak ne yaptığını açıklar
# - 's' parametresi 'email_string' olarak değiştirildi
# - Metodun email adresi kontrolü yaptığı net bir şekilde anlaşılıyor
```

3. Sınıf İsimlendirme Sorusu:
```python
# Aşağıdaki sınıfı daha anlamlı ve açıklayıcı bir şekilde yeniden düzenleyin
class X:
    def __init__(self, n, a):
        self.x1 = n
        self.x2 = a

# Beklenen cevap ve açıklama:
# class Employee:
#     def __init__(self, name, age):
#         self.employee_name = name
#         self.employee_age = age
# 
# Açıklama:
# - 'X' sınıfı 'Employee' olarak değiştirildi
# - 'n' parametresi 'name' olarak açıklandı
# - 'a' parametresi 'age' olarak açıklandı
# - Sınıf değişkenleri açıklayıcı isimler aldı
```


Java dilinde "Anlamlı İsimler" ile ilgili iş görüşmesi soruları hazırlıyorum. Effective Java kitabından da ilkeleri dahil ederek, girişten ileri seviyeye doğru sorular oluşturacağım.

```java
// Verilen metodu Effective Java prensiplerine ve Clean Code kurallarına göre yeniden düzenleyin
class UserManager {
    public boolean x(String a) {
        return a != null && a.length() > 5 && a.contains("@");
    }
}

// Beklenen Gelişmiş Çözüm:
public class UserValidator {
    public boolean isValidEmailAddress(String emailAddress) {
        // Null kontrolü ve Email formatı doğrulaması
        return Optional.ofNullable(emailAddress)
            .filter(email -> email.length() > 5)
            .filter(email -> email.contains("@"))
            .isPresent();
    }
}

// Açıklama:
// - Açıklayıcı metot ve sınıf isimleri
// - Optional kullanarak null güvenliği
// - Method chain ile doğrulama
// - Effective Java'da önerilen null güvenliği ve immutability prensipleri
```

JavaScript için "Anlamlı İsimler" başlığında, modern JS özelliklerini ve temiz kod prensiplerini yansıtan iş görüşmesi soruları hazırlıyorum:

1. Giriş Seviye JavaScript Sorusu:
```javascript
// Eski versiyondaki kodu modern JavaScript ve temiz kod prensipleriyle yeniden yazın
function x(a) {
    return a != null && a.length > 5 && a.includes('@');
}

// Modern Çözüm:
const isValidEmailAddress = (emailAddress) => {
    // Optional Chaining ve Nullish Coalescing ile gelişmiş kontrol
    return emailAddress?.length > 5 && emailAddress?.includes('@') ?? false;
};

// ES2020+ Özellikleri:
// - Optional Chaining (?.)
// - Nullish Coalescing (??)
// - Arrow Function
// - Const kullanımı
```

2. Orta Seviye JavaScript Sorusu:
```javascript
// Karmaşık nesne işlemlerini modern JavaScript özellikleriyle iyileştirin
function p(d) {
    var r = [];
    for(var i = 0; i < d.length; i++) {
        if(d[i] > 10) {
            r.push(d[i] * 2);
        }
    }
    return r;
}

// Modern Çözüm:
const processHighValueItems = (dataList) => {
    // Functional programming ve modern array metodları
    return dataList
        .filter(item => item > 10)
        .map(item => item * 2);
};

// ES6+ Özellikleri:
// - Arrow Functions
// - Array.filter()
// - Array.map()
// - Const kullanımı
// - Açıklayıcı değişken ve fonksiyon isimleri
```

3. İleri Seviye JavaScript Sorusu:
```javascript
// Karmaşık nesne yönetimini modern JS özellikleriyle geliştirin
class U {
    constructor(n, a) {
        this.x1 = n;
        this.x2 = a;
    }
    
    m() {
        return this.x1 + ' ' + this.x2;
    }
}

// Modern Çözüm:
class UserProfile {
    #privateAge; // Private field (ES2022)

    constructor(name, age) {
        // TypeScript benzeri tip kontrolleri
        if (typeof name !== 'string' || typeof age !== 'number') {
            throw new TypeError('Invalid input types');
        }

        this.userName = name;
        this.#privateAge = age;
    }

    // Getter ile private alan erişimi
    get userDescription() {
        return `${this.userName}, ${this.#privateAge} yaşında`;
    }

    // Static method (ES6)
    static validateName(name) {
        return name.length > 2;
    }
}

// ES2022+ Özellikleri:
// - Private Fields (#)
// - Getter metodları
// - Static metodlar
// - Gelişmiş tip kontrolleri
// - Açıklayıcı sınıf ve metot isimleri
```

4. Dekoratör ve Gelişmiş Tip Kontrollü Örnek:
```javascript
// Deneysel Dekoratör Örneği (Stage 3 Proposal)
function logMethod(target, name, descriptor) {
    const originalMethod = descriptor.value;
    
    descriptor.value = function(...args) {
        console.log(`Calling method: ${name}`);
        return originalMethod.apply(this, args);
    };
    
    return descriptor;
}

class DataProcessor {
    @logMethod
    processUserData(userData) {
        // Gelişmiş veri işleme
        return {
            ...userData,
            processedAt: new Date()
        };
    }
}

// Modern JS Özellikleri:
// - Dekoratör kullanımı
// - Spread Operator
// - Object Property Shorthand
// - Açıklayıcı isimlendirme
```

Bu örnekler:
- Temiz Kod prensiplerini uygular
- Modern JavaScript özelliklerini sergiler
- ES6, ES2020, ES2022 ve güncel standartları kapsar
- Açıklayıcı ve anlamlı isimlendirme yapar
- Fonksiyonel ve nesne yönelimli programlama yaklaşımlarını gösterir

Bu örnekler, Uncle Bob'un "Anlamlı İsimler" prensibini açıklamaktadır. İyi bir isimlendirme, kodun okunabilirliğini artırır, diğer geliştiricilerin kodu daha hızlı anlamasına yardımcı olur ve kodun kendi kendini açıklamasını sağlar.
