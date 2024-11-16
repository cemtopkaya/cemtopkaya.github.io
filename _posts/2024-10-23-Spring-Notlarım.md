# Spring Notlarım

“Spring Cloud OpenFeign” bir Java kütüphanesidir ve Spring Boot uygulamalarında kullanılan bir teknoloji aracıdır. Bu teknoloji, mikro servis mimarisinde servislerin birbiriyle iletişim kurmasını kolaylaştırmak için tasarlanmıştır. Bu, geliştiricilerin daha az kod yazarak daha etkili ve bakımı kolay uygulamalar geliştirmesine olanak tanır.

İşte Spring Cloud OpenFeign’in temel özellikleri:

1. **Deklaratif REST İstemcisi**: Geliştiricilerin REST API çağrılarını basit Java arayüzleri kullanarak tanımlamasına olanak tanır.

2. **Spring Boot Entegrasyonu**: Spring Boot uygulamalarıyla sorunsuz bir şekilde entegre olur.

3. **Kod Basitleştirme**: HTTP isteklerini yönetmek için gereken kod miktarını azaltır.

4. **Otomatik Yapılandırma**: Spring Boot’un otomatik yapılandırma özelliklerinden yararlanır.

5. **Yük Dengeleme**: Ribbon ile entegre olarak istemci tarafı yük dengelemesi sağlar.

6. **Devre Kesici Destek**: Hystrix ile entegre olarak hata toleransı ve dayanıklılık sağlar.

“**Spring Cloud Netflix Eureka**” bir servis keşif (service discovery) ve kayıt (registration) sistemidir. Bu sistem, mikroservis tabanlı uygulamaların **ölçeklenebilirliği**ni ve **dayanıklılığı**nı artırır. Servisler dinamik olarak eklenip çıkarılabilir ve diğer servisler bu değişiklikleri otomatik olarak algılayabilir.

İşte temel özellikleri:

1. **Servis Kaydı**: Mikroservisler, çalıştıklarında kendilerini otomatik olarak Eureka sunucusuna kaydederler.

2. **Servis Keşfi**: İstemciler, ihtiyaç duydukları servisleri bulmak için Eureka sunucusuna sorgu yapabilirler.

3. **Yük Dengeleme**: **Eureka**, aynı servisin birden fazla örneği arasında yük dengelemeyi destekler.

4. **Durum İzleme**: Eureka, kayıtlı servislerin durumunu düzenli olarak kontrol eder.

5. **Yüksek Erişilebilirlik**: Birden fazla Eureka sunucusu kullanılarak yüksek erişilebilirlik sağlanabilir.

## Maven İle Proje Yönetimi

Maven, Apache Software Foundation tarafından geliştirilen, Java projeleri için

* *yapılandırma*,

* *bağımlılık yönetimi*

* *raporlama*

* *proje derleme süreçlerini otomatikleştirmek*

* *dağıtım*

amacıyla kullanılan yönetim aracıdır. Tüm bu süreçleri **P**roje **N**esne **M**odeli (**POM**) kullanarak tüm yapılandırma bilgilerini merkezi bir yerde toplar.

![](https://cdn-images-1.medium.com/max/3404/1*Y70BIlvAqubx0u-6T6OD3g.png)
![alt text]({{ BASE_PATH }}/assets/images/java-proje-dizin-yapisi.png)
<!-- <img src="{{site.baseurl | prepend: site.url}}assets/images/java-proje-dizin-yapisi.png" alt="Untitled" /> -->


### Maven’da Yaşam Döngüsü

Maven’da yaşam döngüsü, projenizin inşa edilmesi ve yönetilmesi için belirli bir dizi aşama (phase) ve bu aşamalara bağlı görevlerden (goal) oluşur. Bu yaşam döngüsünü pom.xml dosyası üzerinden tanımlayabilirsiniz.

Maven’da üç ana yaşam döngüsü vardır:

1. **Default (veya Build) Yaşam Döngüsü:** Projenin derlenmesi, test edilmesi ve paketlenmesi gibi işlemleri yönetir.

1. **Clean Yaşam Döngüsü:** Önceki derlemelerden kalan dosyaları temizler.

1. **Site Yaşam Döngüsü:** Proje belgelerini ve raporlarını oluşturur.

![](https://cdn-images-1.medium.com/max/2000/1*_4dxB9hGiDyRxW8D1UQKfQ.png)

Her yaşam döngüsü, belirli aşamalardan oluşur. Örneğin, default yaşam döngüsü aşağıdaki aşamaları içerir:

* *validate*: Projenin doğru yapılandırıldığını kontrol eder.

* *compile*: Kaynak kodunu derler.

* *test*: Birim testlerini çalıştırır.

* *package*: Derlenen kodu dağıtılabilir bir formata (örneğin JAR veya WAR) paketler.

* *install*: Paketi yerel repository’ye yükler.

* *deploy*: Paketi uzak bir repository’ye yükler.

**Her aşamanın bir hedefi vardır ve bu hedefleri eklentilerle gerçekleştiririz.**

![](https://cdn-images-1.medium.com/max/2000/1*MeUIJIBI_UTaxLvoeTA_lA.png)

Her aşama, belirli görevlerle ilişkilendirilmiştir. Örneğin, compile aşaması compiler:compile görevini çalıştırır.

```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.example</groupId>
        <artifactId>my-web-app</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>war</packaging>
    
        <build>
            <plugins>
                <!-- Maven Compiler eklentisi 
    
                     Bu eklenti, Java kaynak kodunu derlemek için kullanılır
                     ve compile aşamasında çalışır. -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.1</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
    
                <!-- JUnit 5 entegrasyonu
                     Test aşaması için surefire plugin -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>3.0.0-M5</version>
                    <executions>
                        <execution>
                            <id>default-test</id>
                            <phase>test</phase>
                            <goals>
                                <goal>test</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Kaynak filtreleme özelliği
                     Resource kopyalama için resources plugin -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-resources-plugin</artifactId>
                    <version>3.2.0</version>
                    <executions>
                        <execution>
                            <id>copy-resources</id>
                            <phase>process-resources</phase>
                            <goals>
                                <goal>copy-resources</goal>
                            </goals>
                            <configuration>
                                <outputDirectory>${project.build.outputDirectory}</outputDirectory>
                                <resources>
                                    <resource>
                                        <directory>src/main/resources</directory>
                                        <filtering>true</filtering>
                                    </resource>
                                </resources>
                            </configuration>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Maven WAR eklentisi 
    
                     Web uygulaması oluşturmak için kullanılır 
                     ve package aşamasında çalışır; 
                     bu aşamada uygulama bir WAR dosyasına paketlenir. -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>3.3.1</version>
                    <executions>
                        <execution>
                            <id>default-war</id>
                            <phase>package</phase> <!-- Paketleme aşamasında çalışacak -->
                            <goals>
                                <goal>war</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Dokümantasyon için javadoc plugin -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <version>3.3.0</version>
                    <executions>
                        <execution>
                            <id>attach-javadocs</id>
                            <phase>package</phase>
                            <goals>
                                <goal>jar</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Kaynak kodları için source plugin -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-source-plugin</artifactId>
                    <version>3.2.1</version>
                    <executions>
                        <execution>
                            <id>attach-sources</id>
                            <phase>package</phase>
                            <goals>
                                <goal>jar</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Maven Deploy eklentisi 
    
                     Uygulamanızı uzak bir repository'ye yüklemek için kullanılır 
                     ve deploy aşamasında çalışır. -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.2</version>
                    <executions>
                        <execution>
                            <id>default-deploy</id>
                            <phase>deploy</phase> <!-- Dağıtım aşamasında çalışacak -->
                            <goals>
                                <goal>deploy</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>
    
                <!-- Clean aşaması build çıktılarının temizlenmesi içindir
                     maven-clean-plugin kullanacağız -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-clean-plugin</artifactId>
                    <version>3.1.0</version>
                    <configuration>
                        <filesets>
                            <fileset>
                                <directory>${project.build.directory}</directory>
                                <includes>
                                    <include>**/*</include>
                                </includes>
                            </fileset>
                        </filesets>
                    </configuration>
                </plugin>
    
            </plugins>
        </build>
    
    
        <!-- Ortama özgü özellikleri profiles içinde belirtiyoruz
             Profiller ile farklı ortamlar için yapılandırma -->
        <profiles>
            <profile>
                <id>development</id>
                <activation>
                    <activeByDefault>true</activeByDefault>
                </activation>
                <properties>
                    <env>dev</env>
                    <debug>true</debug>
                </properties>
            </profile>
            <profile>
                <id>production</id>
                <properties>
                    <env>prod</env>
                    <debug>false</debug>
                </properties>
            </profile>
        </profiles>
    
        <!-- Uzak repository bilgilerini tanımlar; 
             burada normal ve snapshot sürümleri için depolar belirtilmiştir.
             SNAPSHOT, bir projenin henüz yayımlanmamış ve aktif olarak 
             geliştirilmekte olan bir versiyonunu ifade eder. 
             SNAPSHOT versiyonları, genellikle geliştirme aşamasındaki 
             kod değişikliklerini test etmek veya entegrasyon 
             süreçlerinde kullanmak için kullanılır.
          -->
        <distributionManagement>
            <repository>
                <id>yerel-depo</id>
                <url>http://localhost:8081/repository/maven-releases/</url>
            </repository>
            <snapshotRepository>
                <id>my-snapshot-repo</id>
                <url>http://mycompany.com/maven-repo/snapshots</url>
            </snapshotRepository>
        </distributionManagement>
    
    </project>
```

Tüm yukarıdaki aşamaları tetiklemek için:

```shell
    mvn clean # Temizleme
    mvn compile # Derleme
    mvn test # Test çalıştırma
    mvn package # JAR oluşturma
    mvn clean install # Tam build
    mvn clean install -P production # Üretim profili ile build
```

### POM Dosya Yapısı

POM Dosyasının yapısında aşağıdaki elemanlar mevcuttur:

1. **proje (***project***),** Pom.xml dosyasının kök elemanıdır.

1. **modelVersion,** kullandığınız POM modelinin sürümüdür. Maven 2 ve maven 3 için 4.0.0 sürümünü kullanın.

Aşağıdaki bağımlılık paketine bakarak diğer 3 elemanı groupId, artifactId ve version tanımlarını yapalım:

    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-lang3</artifactId>
        <version>3.12.0</version>
    </dependency>

1. **groupId,** bir projenin ait olduğu grup veya organizasyonu tanımlayan benzersiz isimdir. Genellikle, ters çevrilmiş bir alan adı biçiminde yazılır (örneğin, org.apache.commons). Çoğu zaman projenin kök Java paket adına benzer bir grup kimliği kullanırsınız. Bu, projeyi diğer projelerden ayıran benzersiz bir kimlik sağlar. Örneğin, org.apache.commons Apache Commons kütüphanelerini temsil eder ve bu grup altında birçok farklı kütüphane bulunabilir.

1. **artifactId-** belirli bir projeyi veya modülü tanımlar. Bu, genellikle kütüphanenin veya projenin adıdır ve belirli bir grup içinde benzersiz olmalıdır. Örneğin, commons-lang3 Apache Commons'un bir kütüphanesidir.

1. **version-** sürüm numarasını ifade eder. Örneğin, 3.12.0 sürüm numarasıdır.

Yukarıdaki anlatıma bir örnek olarak öğrenci, ders kayıt sistemini verelim:

Tüm modüller için ortak olan grup kimliği (com.university) kullanılmıştır.

* groupId: 
com.university

Ana POM dosyasında tüm alt modüller tanımlanmıştır. Maven, bu modülleri toplamak için bir mekanizma (reactor) kullanır. Modüller arasında bağımlılıklar varsa, Maven bu bağımlılıkları yönetir ve hangi modülün önce inşa edilmesi gerektiğini belirler. Bu mekanizma, tüm modülleri derleme sırasına göre sıralar ve gerekli bağımlılıkları göz önünde bulundurarak doğru sırayla inşa eder. Çok modüllü projelerde belirli bir modülü inşa etmek için komut satırında çeşitli seçenekler kullanılabilir. Örneğin, -pl (project list) seçeneği ile sadece belirli bir modülü inşa edebilirsiniz.

* API Modülü:
artifactId: student-registration-api

* Web Modülü:
artifactId: student-registration-web

* Mobil API Modülü:
artifactId: student-registration-mobile-api

* Veritabanı Kütüphanesi:
artifactId: student-registration-dblib

* Öğrenci Modülü:
artifactId: student-module

* Ders Modülü:
artifactId: course-module

```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.university</groupId>
        <artifactId>student-registration-parent</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>pom</packaging>
    
        <modules>
            <module>student-registration-api</module>
            <module>student-registration-web</module>
            <module>student-registration-mobile-api</module>
            <module>student-registration-dblib</module>
            <module>student-module</module>
            <module>course-module</module>
        </modules>
    
    </project>
```

POM dosyası içinde:

* *Kaynak dosyalar*

* *Bağımlılıklar
*Proje için gerekli olan harici Java kütüphaneleridir

* *Depolar
*Paketlenmiş JAR dosyalarının yerel bilgisayarınızdaki dizin. 
Yerel depo, yalnızca makinenizin sabit diskindeki bir dizindir. Bağımlılıklar yerel Maven deposunda bulunmazsa, Maven bunları merkezi bir Maven deposundan yerel deponuza indirir.

* *Eklentiler*

### Depolar — Repositories

Maven, bağımlılıkları şu sırayla çeker:

1. **Yerel Repository**: Öncelikle yerel dizinde arar.

1. **Tanımlı Repository’ler**: Eğer yerelde bulamazsa, pom.xml içinde tanımlanan repository'lerde arar.

1. **Varsayılan Uzak Repository’ler**: Eğer yine bulamazsa, Maven Central gibi varsayılan uzak repository’lerde arar.

Bu yapı sayesinde, her bağımlılığın hangi repository’den çekileceğini doğrudan kontrol edebilirsiniz. Eğer belirli bir bağımlılığı belirli bir repository’den çekmek istiyorsanız, o bağımlılığı o repository ile ilişkilendirmeniz yeterlidir.

İndirilen paketler varsayılan olaran ~/.m2/repository dizininde saklanır:

![](https://cdn-images-1.medium.com/max/2000/1*yFDmvybUrTYj5oHhLkF5mw.png)

Ancak `~/.m2/settings.xml` dosyasında farklı bir dizini belirtebilirsiniz:

![](https://cdn-images-1.medium.com/max/2000/1*CLTvppKVS2SviSD_Kts9gQ.png)

```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <localRepository>/yeni/yol/yerel/repo</localRepository>
    </settings>
```

![](https://cdn-images-1.medium.com/max/2000/1*GaKjUFAMzxClRUzS8YZO9A.png)

```xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>com.example</groupId>
        <artifactId>my-app</artifactId>
        <version>1.0-SNAPSHOT</version>
    
        <repositories>
            <repository>
                <id>dahili-repo</id>
                <url>http://dahili-repo-url.com/maven2</url> <!-- Dahili Repository -->
            </repository>
            <repository>
                <id>maven-central</id>
                <url>https://repo.maven.apache.org/maven2</url> <!-- Maven Central -->
            </repository>
            <repository>
                <id>jboss-repo</id>
                <url>https://repository.jboss.org/nexus/content/groups/public/</url>
            </repository>
        </repositories>
    
        <dependencies>
            <dependency>
                <groupId>org.hibernate</groupId>
                <artifactId>hibernate-core</artifactId>
                <version>5.4.32.Final</version>
            </dependency>
        </dependencies>
    
    </project>
```

`mvn clean install` komutu, Apache Maven isimli bir proje yönetim aracında kullanılan önemli bir komuttur. Bu komutun ne işe yaradığını anlamak için, iki ana bileşenine bakalım: clean ve install.

## Konteyner Maven Repository

[dzikoysk/reposilite](https://hub.docker.com/r/dzikoysk/reposilite) Kullanarak maven reponuzu ayaklandırabilirsiniz. Yetkilendirme için [https://v2.reposilite.com/docs/authorization](https://v2.reposilite.com/docs/authorization) adresine bakabilirsiniz.

### Belirli Bir Çıktıyı Maven Deposuna Yüklemek

```shell
    mvn deploy:deploy-file -DgroupId=<group-id> \
      -DartifactId=<artifact-id> \
      -Dversion=<version> \
      -Dpackaging=<type-of-packaging> \
      -Dfile=<path-to-file> \
      -DrepositoryId=<id-to-map-on-server-section-of-settings.xml> \
      -Durl=<url-of-the-repository-to-deploy>
```

Örnek:

```shell
    mvn deploy:deploy-file \
      -DpomFile=<path-to-pom> \
      -DgroupId=tr.com.ulak.nms.common \
      -DartifactId=security-lib \
      -Dversion=1.7.1 \
      -Dfile=./target/security-lib-1.7.1.jar \
      -Durl=http://192.168.13.47:8050/releases
```

## Java Kütüphaneleri

### Lombok

Lombok, Java’da kod tekrarını azaltmak ve daha temiz kod yazmamızı sağlayan bir kütüphanedir. En yaygın kullanım alanı, sıkça yazdığımız getter, setter, constructor gibi “boilerplate” kodları otomatik olarak oluşturmasıdır.

Basit bir örnekle açıklayayım:

Lombok olmadan bir User sınıfı yazarsak:

```java
    public class User {
        private String firstName;
        private String lastName;
        private String email;
    
        // Constructor
        public User(String firstName, String lastName, String email) {
            this.firstName = firstName;
            this.lastName = lastName;
            this.email = email;
        }
    
        // Getters
        public String getFirstName() {
            return firstName;
        }
        public String getLastName() {
            return lastName;
        }
        public String getEmail() {
            return email;
        }
    
        // Setters
        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }
        public void setLastName(String lastName) {
            this.lastName = lastName;
        }
        public void setEmail(String email) {
            this.email = email;
        }
    
        // toString metodu
        @Override
        public String toString() {
            return "User{" +
                    "firstName='" + firstName + '\'' +
                    ", lastName='" + lastName + '\'' +
                    ", email='" + email + '\'' +
                    '}';
        }
    }
```

Aynı sınıfı Lombok ile yazarsak:
```java
    import lombok.Getter;
    import lombok.Setter;
    import lombok.ToString;
    import lombok.AllArgsConstructor;
    
    @Getter
    @Setter
    @ToString
    @AllArgsConstructor
    public class User {
        private String firstName;
        private String lastName;
    // ----------------------------------------------------------------------
        @Getter(AccessLevel.PROTECTED) // Sadece bu alan için protected getter
        private String email;
    // ----------------------------------------------------------------------
        @Getter(AccessLevel.PACKAGE) // Sadece paket içinden erişilebilir
        private Double salary;
    
        // Özel getter metodu ile fiyat formatlaması
        public String getFormattedSalary() {
            return String.format("%.2f TL", salary);
        }
    // ----------------------------------------------------------------------
        // @Getter annotation'ı olmayan özel bir alan
        // Özel getter metodun varsa, o alan için Lombok'un getter oluşturmasını
        // engellemek için @Getter(AccessLevel.NONE) kullanmalısın.
        @Getter(AccessLevel.NONE)
        private String password;
        
        // Özel getter metodu
        // Özel getter metodları, Lombok'un oluşturduğu metodları override eder.
        // Özel getter metod isimleriyle Lombok'un oluşturacağı metod isimleri aynı olmalıdır
        public String getPassword() {
            return "********"; // Şifreyi gizleyerek döndür
        }
    // ----------------------------------------------------------------------
        // özel isimle getter oluşturma
        @Getter(name = "getAccountBalance") 
        private Double balance;
        
        // Oluşturulan metod: getAccountBalance() şeklinde olacak
    // ----------------------------------------------------------------------
        @Getter(value = AccessLevel.NONE) // varsayılan getter'ı devre dışı bırak
        private List<Book> books = new ArrayList<>();
        
        // İmmutable liste döndüren özel getter
        public List<Book> getBooks() {
            return Collections.unmodifiableList(books);
        }
    }
```

Gördüğünüz gibi Lombok annotations’ları kullanarak çok daha az kod yazarak aynı işlevselliği elde edebiliyoruz. Lombok’un sağladığı bazı önemli annotation’lar:

* @Getter: Tüm alanlar için getter metodları oluşturur

* @Setter: Tüm alanlar için setter metodları oluşturur

* @ToString: toString() metodunu otomatik oluşturur

* @EqualsAndHashCode: equals() ve hashCode() metodlarını oluşturur

* @NoArgsConstructor: Parametresiz constructor oluşturur

* @AllArgsConstructor: Tüm alanları parametre alan constructor oluşturur

* @Data: @Getter, @Setter, @ToString, @EqualsAndHashCode ve @RequiredArgsConstructor’ı birleştirir

* @Builder: Builder pattern implementasyonu oluşturur

Bu özellikler sayesinde:

1. Daha az kod yazarsınız

1. Kodunuz daha okunabilir olur

1. Hata yapma olasılığınız azalır

1. Bakımı daha kolay olur

Lombok’u kullanmak için projenize dependency’yi eklemeniz ve IDE’nizde Lombok plugin’ini kurmanız yeterlidir.