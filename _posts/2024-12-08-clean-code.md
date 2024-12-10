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
   - Açıklayıcı ve Yararlı Yorumlar Gerektiğinde Kullanılmalıdır
   - Kod Kendini Açıklamalıdır
   - Kod Yapısı ve Mimari Yorumları
   - Hukuki ve Açıklayıcı Yorumların Yeri

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

**Örnekler:**

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

### Yorumlar
Uncle Bob'un yorum satırları hakkındaki temel felsefesi şu noktalara dayanır:

1. Kod Kendini Açıklamalıdır
2. Gereksiz Yorumlardan Kaçınılmalıdır
3. Açıklayıcı Yorumlar Gerektiğinde Kullanılmalıdır
4. Kod Yapısı ve Mimari Yorumları
5. Hukuki ve Açıklayıcı Yorumların Yeri


Java için bu felsefeyi yansıtan soru ve cevaplar:

1. Mimari ve Açıklayıcı Yorum Sorusu:

```java
// Kötü Örnek:
public class UserService {
    // User operations will be here
    public void createUser() {
        // some code
    }
}

// Gelişmiş Çözüm:
/**
 * Kullanıcı yönetimi için merkezi servis sınıfı.
 * 
 * Bu sınıf, kullanıcı yaşam döngüsü operasyonlarını 
 * yönetir ve güvenlik katmanını içerir.
 * 
 * @since 1.0
 * @author DevTeam
 */
public class UserService {
    /**
     * Yeni kullanıcı hesabı oluşturur.
     * 
     * Güvenlik kontrolleri ve validasyon işlemlerini içerir.
     * 
     * @param user Oluşturulacak kullanıcı nesnesi
     * @throws UserValidationException Geçersiz kullanıcı bilgilerinde
     */
    public void createUser(User user) {
        // Detaylı güvenlik ve validasyon kontrolleri
        validateUser(user);
        // Kullanıcı oluşturma işlemleri
    }
}

// Açıklama:
// - Sınıf ve metot seviyesinde mimari açıklamalar
// - JavaDoc standardında belgelendirme
// - Kod yapısını ve amacını açıklayan yorumlar
```

2. Kod Kokusu ve Yorum İlişkisi Sorusu:

```java
public class DataProcessor {
    // Kötü Örnek:
    public int process(int x) {
        // Eğer x 10'dan küçükse
        if (x < 10) {
            // x'i 2 ile çarp
            return x * 2;
        }
        // Değilse 3 ekle
        return x + 3;
    }

    // Gelişmiş Çözüm:
    public int transformValue(int inputValue) {
        return inputValue < THRESHOLD 
            ? multiplySmallValue(inputValue) 
            : incrementLargeValue(inputValue);
    }

    private static final int THRESHOLD = 10;

    private int multiplySmallValue(int value) {
        return value * 2;
    }

    private int incrementLargeValue(int value) {
        return value + 3;
    }
}

// Açıklama:
// - Gereksiz yorumlar yerine açıklayıcı metot ve değişken isimleri
// - Küçük, odaklanmış metodlar
// - Sabit değerler için açık tanımlamalar
// - Yorumlar yerine kodun kendini açıklaması
```

3. Hukuki ve Açıklayıcı Yorum Örneği:

```java
/**
 * Apache Lisans Bildirimi ve Telif Hakkı Yorumu
 * 
 * Bu sınıf, şirket içi veri işleme standartlarını uygular.
 * Tüm hakları saklıdır.
 */
public class SecureDataProcessor {
    // Kod içinde açıklayıcı yorumların kullanım örneği
    
    /**
     * Hassas veri işleme metodu.
     * 
     * GDPR ve KVKK düzenlemelerine uygun veri anonimleştirme
     * işlemleri gerçekleştirir.
     * 
     * @param sensitiveData İşlenecek hassas veri
     * @return Anonimleştirilmiş veri
     * @throws DataProcessingException Veri işleme hatalarında
     */
    public String processPersonalData(String sensitiveData) {
        // Veri anonimleştirme logic'i
        
        // Kritik güvenlik kontrolü
        // Detaylı log mekanizması için açıklayıcı yorum
        // Sistem güvenliği için kritik kontrol noktası
        validateDataProcessingRules(sensitiveData);
        
        return anonymizeData(sensitiveData);
    }
}

// Açıklama:
// - Hukuki ve yasal düzenlemelere atıf
// - Kritik kod blokları için açıklayıcı yorumlar
// - Metot seviyesinde detaylı dokümantasyon
```

### Kod Kokusu (Code Smell)

Kod Kokusu (Code Smell), yazılım geliştirmede bir kod parçasının kalitesiz veya sorunlu olduğunu gösteren belirtilerdir. Martin Fowler ve Kent Beck tarafından popülerleştirilen bir terimdir. Kod kokusu, kodun muhtemelen yeniden düzenlenmesi (refactoring) gerektiğini işaret eden problemlerdir.

Bazı yaygın Kod Kokusu örnekleri:
1. Uzun Metotlar
2. Yinelenen Kod
3. Büyük Sınıflar
4. Fazla Karmaşık Koşul Blokları
5. Sıkı Bağımlılıklar
6. Yetersiz İsimlendirme

JavaScript için Kod Kokusu örnekleri:

1. Uzun ve Karmaşık Metot Kokusu:

```javascript
// Kod Kokusu Örneği:
function processUserData(userData) {
    let result = {};
    
    // Çok fazla sorumluluk
    if (userData && userData.name) {
        result.name = userData.name.toUpperCase();
        
        if (userData.age && userData.age > 18) {
            result.isAdult = true;
            
            if (userData.email && userData.email.includes('@')) {
                result.email = userData.email;
                
                // Daha fazla karmaşık kontrol
                if (userData.subscription) {
                    result.hasSubscription = true;
                    // ... daha fazla kontrol
                }
            }
        }
    }
    
    return result;
}

// Temiz Kod Çözümü:

class UserDataProcessor {
    static validateName(name) {
        return name && typeof name === 'string';
    }
    
    static isAdult(age) {
        return age > 18;
    }
    
    static isValidEmail(email) {
        return email && email.includes('@');
    }
    
    static processUserData(userData) {
        if (!userData) return {};
        
        return {
            name: this.validateName(userData.name) 
                ? userData.name.toUpperCase() 
                : null,
            isAdult: this.isAdult(userData.age),
            email: this.isValidEmail(userData.email) 
                ? userData.email 
                : null,
            hasSubscription: !!userData.subscription
        };
    }
}

// Açıklama:
// - Sorumlulukları küçük metodlara böldük
// - Tek sorumluluk prensibi
// - Daha net ve okunabilir kod
// - Test edilebilirlik arttı
```

2. Yinelenen Kod Kokusu:

```javascript
// Kod Kokusu Örneği:
function calculateSalesTax(price, state) {
    let tax = 0;
    
    if (state === 'NY') {
        tax = price * 0.08;
    } else if (state === 'CA') {
        tax = price * 0.0725;
    } else if (state === 'TX') {
        tax = price * 0.0625;
    }
    
    return tax;
}

function calculateStateTotalSales(sales, state) {
    let tax = 0;
    
    if (state === 'NY') {
        tax = sales * 0.08;
    } else if (state === 'CA') {
        tax = sales * 0.0725;
    } else if (state === 'TX') {
        tax = sales * 0.0625;
    }
    
    return tax;
}

// Temiz Kod Çözümü:
const TAX_RATES = {
    NY: 0.08,
    CA: 0.0725,
    TX: 0.0625
};

class TaxCalculator {
    static calculateTax(amount, state) {
        const taxRate = TAX_RATES[state] || 0;
        return amount * taxRate;
    }
    
    static calculateStateTotalSales(sales, state) {
        return this.calculateTax(sales, state);
    }
}

// Açıklama:
// - Yinelenen kod kaldırıldı
// - Merkezi vergi oranları tanımlandı
// - Kod tekrarı önlendi
// - Bakım kolaylığı sağlandı
```

3. Büyük ve Karmaşık Sınıf Kokusu:

```javascript
// Kod Kokusu Örneği:
class UserManager {
    constructor(database) {
        this.database = database;
    }
    
    createUser(userData) {
        // Kullanıcı oluşturma
        // Doğrulama
        // Log kaydetme
        // Bildirim gönderme
        // E-posta doğrulama
    }
    
    updateUser(userData) {
        // Kullanıcı güncelleme
        // Doğrulama
        // Log kaydetme
        // Bildirim gönderme
    }
    
    deleteUser(userId) {
        // Kullanıcı silme
        // Yetkilendirme
        // Log kaydetme
    }
    
    sendNotification(user) {
        // Bildirim gönderme
    }
    
    validateUser(userData) {
        // Kullanıcı doğrulama
    }
}

// Temiz Kod Çözümü:
class UserValidator {
    static validate(userData) {
        // Kullanıcı doğrulama
    }
}

class UserRepository {
    constructor(database) {
        this.database = database;
    }
    
    create(userData) {
        // Veritabanı işlemleri
    }
    
    update(userData) {
        // Veritabanı güncelleme
    }
    
    delete(userId) {
        // Veritabanı silme
    }
}

class UserNotificationService {
    send(user) {
        // Bildirim gönderme
    }
}

class UserManager {
    constructor(repository, validator, notificationService) {
        this.repository = repository;
        this.validator = validator;
        this.notificationService = notificationService;
    }
    
    createUser(userData) {
        if (this.validator.validate(userData)) {
            const user = this.repository.create(userData);
            this.notificationService.send(user);
            return user;
        }
    }
}

// Açıklama:
// - Sorumluluklar ayrıştırıldı
// - Tek sorumluluk prensibi
// - Bağımlılıklar azaltıldı
// - Daha esnek ve test edilebilir kod
```


Uncle Bob'un "Fonksiyonlar" (Functions) başlığı için temel prensipler:

1. Küçük Fonksiyonlar
2. Tek Sorumluluk İlkesi
3. Az Parametre
4. Yan Etki Olmayan Fonksiyonlar
5. Açıklayıcı İsimler
6. DRY (Don't Repeat Yourself) Prensibi

Python Örneği:

```python
# Kötü Örnek
def process_data(data):
    result = []
    for item in data:
        if item > 10:
            # Birden fazla sorumluluk
            new_item = item * 2
            if new_item % 2 == 0:
                result.append(new_item)
    return result

# İyi Örnek
def is_large_number(number, threshold=10):
    """Sayının belirli bir eşikten büyük olup olmadığını kontrol eder."""
    return number > threshold

def double_number(number):
    """Sayıyı ikiye katlar."""
    return number * 2

def is_even(number):
    """Sayının çift olup olmadığını kontrol eder."""
    return number % 2 == 0

def filter_and_transform_numbers(numbers):
    """
    Sayıları filtreler ve dönüştürür.
    
    Args:
        numbers (list): İşlenecek sayı listesi
    
    Returns:
        list: Dönüştürülmüş sayılar
    """
    return [
        double_number(num) 
        for num in numbers 
        if is_large_number(num) and is_even(double_number(num))
    ]
```

Java Örneği:

```java
// Kötü Örnek
public class DataProcessor {
    public List<String> processUserData(List<User> users) {
        List<String> result = new ArrayList<>();
        for (User user : users) {
            // Çok fazla sorumluluk
            if (user.getAge() > 18) {
                String processedName = user.getName().toUpperCase();
                if (processedName.length() > 3) {
                    result.add(processedName);
                }
            }
        }
        return result;
    }
}

// İyi Örnek
public class UserProcessor {
    public boolean isAdult(User user) {
        return user.getAge() > 18;
    }
    
    public boolean isValidName(String name) {
        return name != null && name.length() > 3;
    }
    
    public String normalizeName(String name) {
        return name.toUpperCase();
    }
    
    public List<String> filterAndTransformUsers(List<User> users) {
        return users.stream()
            .filter(this::isAdult)
            .map(User::getName)
            .map(this::normalizeName)
            .filter(this::isValidName)
            .collect(Collectors.toList());
    }
}
```

JavaScript Örneği:

```javascript
// Kötü Örnek
function processOrderData(orders) {
    let total = 0;
    let discountedOrders = 0;
    
    for (let order of orders) {
        // Çoklu sorumluluk
        if (order.price > 100) {
            total += order.price;
            if (order.price > 500) {
                discountedOrders++;
            }
        }
    }
    
    return {
        total,
        discountedOrders
    };
}

// İyi Örnek
class OrderProcessor {
    static isHighValueOrder(order, threshold = 100) {
        return order.price > threshold;
    }
    
    static isEligibleForDiscount(order, discountThreshold = 500) {
        return order.price > discountThreshold;
    }
    
    static calculateOrderTotal(orders, threshold = 100) {
        return orders
            .filter(order => this.isHighValueOrder(order, threshold))
            .reduce((total, order) => total + order.price, 0);
    }
    
    static countDiscountedOrders(orders, discountThreshold = 500) {
        return orders
            .filter(order => this.isEligibleForDiscount(order, discountThreshold))
            .length;
    }
    
    static processOrderData(orders) {
        return {
            total: this.calculateOrderTotal(orders),
            discountedOrders: this.countDiscountedOrders(orders)
        };
    }
}
```

Temel Prensipler:
1. Her fonksiyon tek bir işi yapmalı
2. Fonksiyonlar küçük olmalı
3. Parametre sayısı minimize edilmeli
4. Fonksiyon isimleri açıklayıcı olmalı
5. Yan etkilerden kaçınılmalı
6. Kodun okunabilirliği ve bakımı ön planda tutulmalı

İş Görüşmesi Sorusu Örneği:

```javascript
// Soru: Bu fonksiyonu Uncle Bob'un fonksiyon prensipleri açısından nasıl geliştirebilirsiniz?
function process(d, t) {
    let r = 0;
    for (let i of d) {
        if (i > t) {
            r += i;
        }
    }
    return r;
}

// Beklenen Gelişmiş Çözüm
function calculateTotalAboveThreshold(dataList, threshold) {
    return dataList
        .filter(item => item > threshold)
        .reduce((total, item) => total + item, 0);
}
```

### Fonksiyonlar

Uncle Bob'un "Fonksiyonlar" (Functions) başlığı için temel prensipler:

1. Fonksiyonlar küçük olmalı
2. Tek Sorumluluk İlkesi (Her fonksiyon tek bir işi yapmalı)
3. Parametre sayısı minimize edilmeli (Az Parametre)
4. Yan Etki Olmayan Fonksiyonlar
5. Fonksiyon isimleri açıklayıcı olmalı
6. DRY (Don't Repeat Yourself) Prensibi
7. Kodun okunabilirliği ve bakımı ön planda tutulmalı

Uncle Bob'un (Robert C. Martin) Clean Code kitabındaki "Fonksiyonlar" bölümü ile fonksiyonel programlama paradigması arasındaki kesişimleri siz de fark etmiş olabilirsiniz. 

Uncle Bob, Martin Fowler ve Kent Beck'in yazılım geliştirme konusundaki yaklaşımları, özellikle **Clean Code**, **Refactoring**, ve **TDD** (Test-Driven Development) pratikleri, **functional programming paradigm** ile birçok noktada kesişiyor. Bu yazarlar, kodun temiz, anlaşılır, ve sürdürülebilir olmasına vurgu yapıyor ve bu özellikler, functional programming'in temel ilkeleriyle uyumlu.

İşte bu kesişim noktalarını ve ilgili kavramları detaylandıralım:

1. Pure Functions (Saf Fonksiyonlar):
   - Uncle Bob'un temiz kod prensiplerine göre, iyi bir fonksiyon yan etki içermemeli ve öngörülebilir davranmalıdır.
   - Fonksiyonel programlamadaki "pure function" tanımıyla birebir örtüşür.
   - Aynı girdi için her zaman aynı çıktıyı üreten, dış durumdan bağımsız fonksiyonlar.
   - **Clean Code'daki Yeri:** Fonksiyonların sadece bir iş yapması gerektiği ve yan etkilerden arınmış olması gerektiği ilkesi.
   - **Refactoring'deki Yeri:** Yan etkileri azaltmak ve saf fonksiyonlar kullanmak kodun test edilebilirliğini artırır.
   - **TDD'deki Yeri:** Test yazarken saf fonksiyonlar, öngörülebilir davranışları sayesinde test yazımını kolaylaştırır.

2. High Order Functions (Üst Düzey Fonksiyonlar):
   - Parametre olarak başka fonksiyonları alabilen veya fonksiyon döndürebilen fonksiyonlar.
   - Clean Code prensiplerine göre, fonksiyonların tek bir işi yapması ve modüler olması gerektiği fikriyle uyumlu.
   - Kod tekrarını azaltma ve abstraksiyonu artırma potansiyeli sağlar.
   - **Clean Code'daki Yeri:** Tek sorumluluk ilkesine uygun olarak, tekrar eden kodları azaltmak ve okunabilirliği artırmak için kullanılır.
   - **Refactoring'deki Yeri:** Kodun modülerliğini ve yeniden kullanılabilirliğini artırır.
   - **TDD'deki Yeri:** Daha soyut yapılar, farklı durumları daha kolay test etmeyi sağlar.

3. Immutability (Değişmezlik):
   - Uncle Bob'un kod kalitesi yaklaşımı, durumun değiştirilmesini minimize etmeyi önerir.
   - Fonksiyonel programlamadaki immutability konseptiyle doğrudan ilişkili.
   - Veri yapılarının değişmez olması, hata olasılığını ve yan etkileri azaltır.
   - **Clean Code'daki Yeri:** Değişkenlerin değişmez olması, yan etkilerin azaltılması ve hataların önlenmesi açısından önemlidir.
   - **Refactoring'deki Yeri:** Kodun daha kolay refactor edilmesini sağlar çünkü değişmez yapılar, mevcut durumu bozmaz.
   - **TDD'deki Yeri:** Değişmez yapılar, testlerin belirli bir başlangıç durumunda güvenilir bir şekilde çalışmasını sağlar.

#### **1. Pure Functions (Saf Fonksiyonlar)**
- **Soru:** Bir fonksiyonun saf fonksiyon olup olmadığını nasıl anlarsınız? Saf fonksiyonlar yazılım geliştirme sürecine nasıl katkı sağlar?
- **Cevap:**
  - Saf bir fonksiyon:
    - Aynı girdiler için her zaman aynı çıktıyı üretir.
    - Yan etkisi yoktur (örneğin, global değişkenleri değiştirmez veya I/O işlemi yapmaz).
  - Katkıları:
    - Test yazmayı kolaylaştırır çünkü sonuçlar öngörülebilirdir.
    - Kodun modülerliğini ve okunabilirliğini artırır.

#### **2. High-Order Functions (Üst Düzey Fonksiyonlar)**
- **Soru:** Üst düzey fonksiyonlar nedir? Yazılımda hangi problemleri çözmek için kullanırsınız?
- **Cevap:**
  - Üst düzey fonksiyonlar, başka fonksiyonları parametre olarak alabilir veya başka fonksiyonları döndürebilir.
  - Tekrar eden kodları azaltır, DRY (Don't Repeat Yourself) prensibini destekler, ve esneklik sağlar.
  - Örneğin, `map`, `filter`, ve `reduce` gibi fonksiyonlar, büyük veri kümelerini daha okunabilir bir şekilde işler.

#### **3. Immutability (Değişmezlik)**
- **Soru:** İmmutability nedir ve yazılım geliştirme süreçlerinde neden önemlidir?
- **Cevap:**
  - İmmutability, bir değişkenin oluşturulduktan sonra değiştirilememesi durumudur.
  - Avantajları:
    - Yan etkileri azaltır ve kodun öngörülebilirliğini artırır.
    - Paralel işlemlerde veri yarışını (race condition) önler.
    - Debugging sürecini kolaylaştırır çünkü değişkenlerin durumu daha kolay izlenir.

#### **4. Functional Programming ile Clean Code Prensiplerinin Kesişimi**
- **Soru:** Clean Code prensipleriyle fonksiyonel programlamanın hangi ortak noktaları vardır? Örnekler vererek açıklayın.
- **Cevap:**
  - Clean Code prensiplerinden "tek sorumluluk ilkesi", saf fonksiyonlarla örtüşür çünkü saf fonksiyonlar yalnızca bir iş yapar.
  - Modülerlik ve okunabilirlik vurgusu, high-order fonksiyonlarla desteklenir.
  - Yan etkilerin azaltılması ve değişmez yapıların kullanımı, Clean Code’un “yan etkileri minimize et” prensibiyle uyumludur.

#### **5. Refactoring ile Functional Programming**
- **Soru:** Refactoring sırasında functional programming tekniklerinden nasıl faydalanabilirsiniz?
- **Cevap:**
  - Karmaşık kod parçalarını saf fonksiyonlara dönüştürerek test edilebilirliği artırabilirsiniz.
  - Üst düzey fonksiyonlar kullanarak kod tekrarını önleyebilir ve kodun okunabilirliğini artırabilirsiniz.
  - Değişmezlik (immutability) ilkesiyle çalışarak refactor edilen kodun yan etkiler yaratma olasılığını azaltabilirsiniz.


#### Yan Etkileri Azaltma

Bir iş isterinde (veritabanına kaydetme, ileti gönderme, dosyaya günlük yazma, metrik üretme gibi) gereksinimler tamamen geçerli ve bu işler kesinlikle yerine getirilmesi gerekir. Fonksiyonel programlama ve yan etkisiz fonksiyonlar bu ihtiyaçları **daha iyi organize etmek** ve **yan etkileri kontrol altına almak** için güçlü araçlar sunar.

*Fonksiyonel programlama yan etkilerle başa çıkmak için tamamen yeterli mi?*
Hayır. Yan etkilerle başa çıkmak ve gerçek dünya uygulamaları geliştirmek için genel yazılım pratiklerine ihtiyaç duyulur. Örneğin, bir e-posta göndermek (yan etki) ya da bir günlük dosyasına yazmak yahut bir metrik üretmek tamamen fonksiyonel bir yapıya oturtulamaz, ama fonksiyonel araçlarla kontrol altına alınabilir.

*Fonksiyonel araçlar nerede öne çıkar?*
İşin mantıki ve saf kısımlarını (örneğin, veri işleme, hata yönetimi) çok daha temiz ve sürdürülebilir hale getirir.

*Genel araçlar neden önemli?*
İşin operasyonel kısımlarında (örneğin, veritabanı, mesajlaşma, günlük kaydı) sistemin kontrolsüz bir hale gelmesini önler.


**Tamamen Fonksiyonel Programlamadan Gelen Araçlar**
Bunlar, fonksiyonel programlama paradigmalarının özünde olan ve bu yaklaşımın yaygınlaşmasıyla doğrudan ilişkili araçlardır:

1. **Pure Functions (Saf Fonksiyonlar)**
   Fonksiyonel programlamanın temel taşıdır. Yan etkileri ortadan kaldırarak kodu daha modüler ve tahmin edilebilir hale getirir.

1. **Higher-Order Functions (Yüksek Seviyeli Fonksiyonlar)**
   Fonksiyonel programlamanın esnekliğini artırır. Örneğin, map, filter, ve reduce gibi yapılar bu yaklaşımla gelir.

1. **Monads (Monadlar)**
   Yan etkilerin yönetimi, hata işleme (Maybe, Either) veya asenkron süreçler (IO Monad) gibi durumlarda kullanılır. Fonksiyonel programlamadan doğmuştur.

1. **Immutability (Değişmezlik)**
   Fonksiyonel dillerin (ör. Haskell, Scala) temel ilkesi olarak veri yapılarının değiştirilemezliğini sağlar.

1. **Currying ve Partial Application**
   Fonksiyonları parçalara ayırarak daha esnek ve yeniden kullanılabilir hale getirir. Fonksiyonel dillerde çok yaygındır.

1. **Fonksiyon Kompozisyonu**
   Fonksiyonları birleştirerek daha karmaşık işlevsellikler oluşturur. Pipe operatörleri gibi yapıların temelini oluşturur.

1. **Reactive Programming (Tepkisel Programlama)**
   Fonksiyonel yaklaşımdan türemiştir. Özellikle asenkron veri akışlarını yönetirken çok etkilidir.


**Genel Yazılım Pratiklerinden Gelen ve Fonksiyonel Programlamayla Uyumlular**
Bunlar, aslında daha geniş yazılım dünyasında kullanılan araçlar ve prensiplerdir, ancak fonksiyonel programlama ilkelerine uyumlu oldukları için bu bağlamda etkili şekilde kullanılabilir:

1. **Dependency Injection (Bağımlılık Enjeksiyonu)**
Nesne yönelimli programlamadan gelir ama yan etkileri daha iyi organize etmek için fonksiyonel sistemlerde de kullanılır.

1. **Middleware (Ara Katman)**
Fonksiyonel olmayan sistemlerde de yaygındır (ör. Flask veya Express.js), ancak yan etkileri izole etmek için fonksiyonel bir yaklaşımla kullanılabilir.

1. **Event-Driven Architecture (Olay Tabanlı Mimari)**
Daha çok dağıtık sistemlerden gelir, ancak yan etkileri daha net şekilde ayırmak için fonksiyonel yaklaşımlarla uyumludur.

1. **Domain-Driven Design (DDD)**
İş mantığını modelleme yöntemi olarak ortaya çıkmıştır. Fonksiyonel sistemlerin güçlü soyutlama yetenekleriyle iyi çalışır.

1. **Asynchronous Task Queues (Asenkron Görev Kuyrukları)**
Mikroservis dünyasından gelen bir pratik olsa da, yan etkileri izole etmek için fonksiyonel prensiplerle birleştirilebilir.

---

### 1. **Yan Etkileri İzole Etme ve Yönetme**
Bir iş isterini yerine getirmek için yan etkileri tamamen kaldırmak mümkün değil; ama **yan etkileri izole etmek** mümkündür. İzole edilmiş yan etkiler, kodun geri kalanından ayrıldığı için:
- Test etmek kolaydır (yan etkisiz kısmı bağımsız test edebilirsiniz).
- Bakımı daha kolaydır (sorumlulukları net ayrılmıştır).

Şöyle bir yapı hayal edelim:

#### İş Adımları:
1. Kullanıcıyı listeye ekle (**yan etkisiz**).
2. Kullanıcıyı veritabanına kaydet (**yan etkili**).
3. İleti gönder (**yan etkili**).
4. Günlük kaydı oluştur (**yan etkili**).
5. Metrik üreteçlerini çalıştır (**yan etkili**).

Bu iş akışını yöneten bir **orkestrasyon fonksiyonu** yazabiliriz.

---

### 2. **Fonksiyonel Yaklaşım: Sorumlulukları Ayırma**
Her işi ayrı bir fonksiyona bölelim. İşte tam bir örnek:

```python
# Yan etkisiz işlemler
def kullanici_ekle(kullanicilar, yeni_kullanici):
    return kullanicilar + [yeni_kullanici]

# Yan etkili işlemler
def kullanici_veritabanina_kaydet(kullanici):
    # Veritabanına kaydetme işlemi
    print(f"Veritabanına kaydedildi: {kullanici}")

def mail_gonder(kullanici):
    # E-posta gönderme işlemi
    print(f"E-posta gönderildi: {kullanici['email']}")

def gunluk_yaz(kullanici):
    # Dosyaya günlük yazma işlemi
    with open("gunluk.txt", "a") as dosya:
        dosya.write(f"Kullanıcı eklendi: {kullanici}\n")

def metrik_arttir(metrik_adi):
    # Metrik artırma işlemi
    print(f"Metrik artırıldı: {metrik_adi}")

# Orkestrasyon fonksiyonu
def kullanici_ekle_ve_isle(kullanicilar, yeni_kullanici):
    # Yan etkisiz kısmı
    kullanicilar = kullanici_ekle(kullanicilar, yeni_kullanici)
    
    # Yan etkili işlemleri sırayla çalıştır
    kullanici_veritabanina_kaydet(yeni_kullanici)
    mail_gonder(yeni_kullanici)
    gunluk_yaz(yeni_kullanici)
    metrik_arttir("kullanici_ekle")
    
    return kullanicilar
```

#### Kullanım:

```python
kullanicilar = []
yeni_kullanici = {"isim": "Ali", "email": "ali@example.com"}

kullanicilar = kullanici_ekle_ve_isle(kullanicilar, yeni_kullanici)
print(kullanicilar)
```

---

### 3. **Yan Etkileri İzole Etmenin Avantajları**
1. **Test Edilebilirlik:**
   - `kullanici_ekle` fonksiyonunu tek başına test edebilirsiniz. Mesela `assert kullanici_ekle([], yeni_kullanici) == [yeni_kullanici]`.
   - Yan etkili kısımları ise birer birer test edebilirsiniz (`mock` veya `spy` kullanarak).

2. **Kodun Anlaşılabilirliği:**
   - Orkestrasyon fonksiyonunda iş akışı açıkça görülür.
   - Her yan etkili işlem bağımsız çalışır.

3. **Uzmanlaşmış Modüller:**
   - Yan etkili işlemler (veritabanı, günlükleme, metrik) farklı modüller veya sınıflar halinde organize edilebilir.

---

### 4. **Sonuç: Yan Etkiler Ortadan Kalkmaz, Ama Kontrol Edilebilir!**
Buradaki ana fikir, yan etkileri **yönetilebilir ve izlenebilir** bir şekilde organize etmektir. Böylece gereksinimler karşılanır ama kod daha **modüler**, **bakımı kolay**, ve **test edilebilir** hale gelir.



#### Örnek Kod ve Senaryolar


Örnek bir kod senaryosu ile bu kavramları birleştirebiliriz:

```python
# Pure Function örneği
def calculate_area(width, height):
    return width * height

# High Order Function örneği
def apply_operation(func, value):
    return func(value)

# Immutability örneği
def add_to_list(original_list, item):
    return original_list + [item]  # Yeni bir liste oluşturur
```

Kesişen diğer yazılım geliştirme kavramları:
- Declarative Programming
- Referential Transparency
- Function Composition
- Recursion
- Lazy Evaluation

Bu kavramlar, Uncle Bob'un Clean Code felsefesi, Martin Fowler'ın yazılım mimarisi yaklaşımı ve Kent Beck'in test odaklı geliştirme (TDD) prensipleriyle doğrudan ilişkilidir.

Örnek Soru 1: Pure function neden önemlidir?
- Test edilebilirlik artar
- Yan etkiler minimize edilir
- Kod güvenilirliği ve tahmin edilebilirliği artar

Örnek Soru 2: High order function ne işe yarar?
- Kod tekrarını azaltır
- Daha esnek ve modüler kod yapıları sağlar
- Fonksiyonları parametre olarak geçebilme imkanı verir


Her sorunun çözümü, yazılım geliştirmede modern yaklaşımların önemini vurgular: *modülerlik*, *öngörülebilirlik* ve *bakım kolaylığı*.


Aşağıdaki sorular:
- Fonksiyonel programlama prensiplerini anlama
- Clean Code prensiplerine uygun kod yazma
- Yan etkileri minimize etme
- Esnek ve yeniden kullanılabilir kod yazma yeteneklerini test eder

1. Pure Function Sorusu (Java):

```java
public class UserUtils {
    private static List<String> activeUsers = new ArrayList<>();

    public static void addUser(String username) {
        // Problematik kod: Global state'i değiştiriyor
        activeUsers.add(username);
    }

    public static List<String> getActiveUsers() {
        return activeUsers;
    }
}
```

**Beklenen Düzeltme ve Açıklama:**

```java
public class UserUtils {
    // Pure function haline getirme
    public static List<String> addUser(List<String> currentUsers, String username) {
        // Yeni bir liste oluşturarak immutability sağlanır
        List<String> updatedUsers = new ArrayList<>(currentUsers);
        updatedUsers.add(username);
        return updatedUsers;
    }
}
```

**Açıklama:**
- Orijinal kod yan etki içeriyordu (global state'i değiştiriyordu)
- Düzeltilmiş versiyon:
  - Yan etki ortadan kaldırıldı
  - Immutability prensibi uygulandı
  - Fonksiyon girdi olarak aldığı listeyi değiştirmez
  - Her çağrıda aynı girdi için aynı çıktıyı üretir

2. High Order Function Sorusu (JavaScript):

```javascript
function processNumbers(numbers) {
    // Problematik kod: Sabit bir işlem yapılıyor
    const results = [];
    for (let num of numbers) {
        results.push(num * 2);
    }
    return results;
}
```

**Beklenen Düzeltme:**

```javascript
function processNumbers(numbers, transformFunction) {
    // High Order Function ile esnek işlem yapma
    return numbers.map(transformFunction);
}

// Kullanım örneği
const doubledNumbers = processNumbers([1, 2, 3, 4], x => x * 2);
const squaredNumbers = processNumbers([1, 2, 3, 4], x => x ** 2);
```

**Açıklama:**
- Orijinal kod sadece çarpma işlemi yapabiliyordu
- Düzeltilmiş versiyon:
  - İşlem fonksiyonu parametre olarak alınır
  - Daha esnek ve yeniden kullanılabilir
  - Farklı dönüşümler kolaylıkla uygulanabilir

3. Immutability Sorusu (Python):

```python
def update_student_grades(students):
    # Problematik kod: Orijinal listeyi doğrudan değiştirir
    for student in students:
        student['grade'] += 5
    return students
```

**Beklenen Düzeltme:**

```python
def update_student_grades(students):
    # Yeni bir liste oluşturarak immutability sağlanır
    return [
        {**student, 'grade': student['grade'] + 5} 
        for student in students
    ]
```

**Açıklama:**
- Orijinal kod girdi listesini doğrudan değiştirir
- Düzeltilmiş versiyon:
  - Spread operatörü ile yeni bir liste oluşturulur
  - Orijinal veri korunur
  - Fonksiyonel programlama prensipleri uygulanır

4. Composition ve Pure Function Sorusu (Java):

```java
public class DataProcessor {
    public static String processData(String input) {
        // Çoklu sorumluluk ve yan etki içeren kod
        System.out.println("Processing: " + input);  // Yan etki
        return input.toUpperCase().trim();
    }
}
```

**Beklenen Düzeltme:**

```java
public class DataProcessor {
    public static String normalizeInput(String input) {
        return input.trim();
    }
    
    public static String transformToUppercase(String input) {
        return input.toUpperCase();
    }
    
    public static void logProcessing(String input) {
        // Logging ayrı bir sorumluluk olarak
        System.out.println("Processing: " + input);
    }
    
    // Fonksiyon kompozisyonu
    public static String processData(String input) {
        String normalized = normalizeInput(input);
        String transformed = transformToUppercase(normalized);
        logProcessing(transformed);
        return transformed;
    }
}
```

**Açıklama:**
- Sorumluluklar ayrıştırıldı
- Her fonksiyon tek bir iş yapar
- Yan etkiler minimize edildi
- Fonksiyon kompozisyonu uygulandı



Python Örneği:

```python
# Kötü Örnek
def process_data(data):
    result = []
    for item in data:
        if item > 10:
            # Birden fazla sorumluluk
            new_item = item * 2
            if new_item % 2 == 0:
                result.append(new_item)
    return result

# İyi Örnek
def is_large_number(number, threshold=10):
    """Sayının belirli bir eşikten büyük olup olmadığını kontrol eder."""
    return number > threshold

def double_number(number):
    """Sayıyı ikiye katlar."""
    return number * 2

def is_even(number):
    """Sayının çift olup olmadığını kontrol eder."""
    return number % 2 == 0

def filter_and_transform_numbers(numbers):
    """
    Sayıları filtreler ve dönüştürür.
    
    Args:
        numbers (list): İşlenecek sayı listesi
    
    Returns:
        list: Dönüştürülmüş sayılar
    """
    return [
        double_number(num) 
        for num in numbers 
        if is_large_number(num) and is_even(double_number(num))
    ]
```

Java Örneği:

```java
// Kötü Örnek
public class DataProcessor {
    public List<String> processUserData(List<User> users) {
        List<String> result = new ArrayList<>();
        for (User user : users) {
            // Çok fazla sorumluluk
            if (user.getAge() > 18) {
                String processedName = user.getName().toUpperCase();
                if (processedName.length() > 3) {
                    result.add(processedName);
                }
            }
        }
        return result;
    }
}

// İyi Örnek
public class UserProcessor {
    public boolean isAdult(User user) {
        return user.getAge() > 18;
    }
    
    public boolean isValidName(String name) {
        return name != null && name.length() > 3;
    }
    
    public String normalizeName(String name) {
        return name.toUpperCase();
    }
    
    public List<String> filterAndTransformUsers(List<User> users) {
        return users.stream()
            .filter(this::isAdult)
            .map(User::getName)
            .map(this::normalizeName)
            .filter(this::isValidName)
            .collect(Collectors.toList());
    }
}
```

JavaScript Örneği:

```javascript
// Kötü Örnek
function processOrderData(orders) {
    let total = 0;
    let discountedOrders = 0;
    
    for (let order of orders) {
        // Çoklu sorumluluk
        if (order.price > 100) {
            total += order.price;
            if (order.price > 500) {
                discountedOrders++;
            }
        }
    }
    
    return {
        total,
        discountedOrders
    };
}

// İyi Örnek
class OrderProcessor {
    static isHighValueOrder(order, threshold = 100) {
        return order.price > threshold;
    }
    
    static isEligibleForDiscount(order, discountThreshold = 500) {
        return order.price > discountThreshold;
    }
    
    static calculateOrderTotal(orders, threshold = 100) {
        return orders
            .filter(order => this.isHighValueOrder(order, threshold))
            .reduce((total, order) => total + order.price, 0);
    }
    
    static countDiscountedOrders(orders, discountThreshold = 500) {
        return orders
            .filter(order => this.isEligibleForDiscount(order, discountThreshold))
            .length;
    }
    
    static processOrderData(orders) {
        return {
            total: this.calculateOrderTotal(orders),
            discountedOrders: this.countDiscountedOrders(orders)
        };
    }
}
```


```javascript
// Soru: Bu fonksiyonu Uncle Bob'un fonksiyon prensipleri açısından nasıl geliştirebilirsiniz?
function process(d, t) {
    let r = 0;
    for (let i of d) {
        if (i > t) {
            r += i;
        }
    }
    return r;
}

// Beklenen Gelişmiş Çözüm
function calculateTotalAboveThreshold(dataList, threshold) {
    return dataList
        .filter(item => item > threshold)
        .reduce((total, item) => total + item, 0);
}
```
