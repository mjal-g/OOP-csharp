# S.O.L.I.D. Prensipleri 

**SOLID** prensipleri Nesneye yönelik programlamada (OOP) yazılımın daha *anlaşılır*, *esnek*, *sürdürülebilir* ve *genişletilebilir* olmasını sağlayan beş temel tasarım ilkesidir. 
Robert C. Martin (Uncle Bob) tarafından ortaya atılmıştır. 
Her harf bir prensibi temsil eder.


---

### 1. S: Single Responsibility Principle (Tek Sorumluluk Prensibi)

**Tanım:** Bir sınıfın (class) değişmesi için yalnızca bir nedeni olmalıdır. Yani bir sınıf, sadece bir işten veya aktörden sorumlu olmalıdır.

**Açıklama:** Bir sınıfa birden fazla sorumluluk yüklediğinizde, bu sorumluluklardan birinde yapılacak bir değişiklik diğerlerini de etkileyebilir. Bu da istenmeyen yan etkilere, hatalara ve bakım zorluğuna yol açar. Her sınıf sadece bir işi yapmalı ve o işi mükemmel şekilde yerine getirmelidir.

**Örnek:** Hem çalışan verisini tutan, hem de bu veriyi veritabanına kaydeden bir `Calisan` sınıfı yerine; yalnızca çalışan özelliklerini tutan bir `Calisan` sınıfı ve veritabanı işlemlerinden sorumlu ayrı bir `CalisanRepository` sınıfı olmalıdır.

---

### 2. O: Open/Closed Principle (Açık/Kapalı Prensibi)

**Tanım:** Yazılım varlıkları (sınıflar, modüller, fonksiyonlar) genişletmeye **açık**, değişikliğe **kapalı** olmalıdır.

**Açıklama:** Bir sınıfın davranışını, mevcut kodunu değiştirmeden genişletebilmelisiniz. Bu genellikle soyutlamalar (abstract sınıflar veya arayüzler/interface) kullanarak sağlanır. Yeni bir özellik eklendiğinde mevcut çalışan kodun bozulmaması hedeflenir.

**Örnek:** Farklı şekillerin (daire, kare) alanını hesaplayan bir sınıf düşünün. Her yeni şekil eklendiğinde, bu sınıfın içindeki `alanHesapla()` metodunu değiştirmek yerine; tüm şekillerin uygulayacağı bir `ISekil` arayüzü tanımlayıp, her şekil kendi alan hesaplamasını bu arayüz üzerinden yapmalıdır. Böylece ana sınıf yeni şekillere kapalı olur ama genişletmeye açık hale gelir.

---

### 3. L: Liskov Substitution Principle (Liskov Yerine Geçme Prensibi)

**Tanım:** Bir alt sınıftan (derived class) türetilmiş nesneler, üst sınıflarının (base class) yerine geçebilmeli ve programın doğruluğunu bozmamalıdır.

**Açıklama:** Türetilmiş bir sınıf, üst sınıfının davranışını bozmadan, onun yerine kullanılabilmelidir. Eğer bir alt sınıf, üst sınıfın metodlarını geçersiz kıldığında (override) bu metodu çalıştırmak istemediği için hata fırlatıyor veya gereksiz kısıtlamalar getiriyorsa, bu prensip ihlal edilir. Başka bir deyişle: "Kalıtım, doğru şekilde kullanılmalıdır."

**Örnek:** Bir `Kus` sınıfınız ve ondan türeyen `Penguen` alt sınıfınız var. `Kus` sınıfında `uc()` metodu varsa, `Penguen` bu metodu geçersiz kılınca `exception` fırlatmak zorunda kalır. Oysa ki uçamayan bir kuş, `Kus` yerine geçemez. Bu durumda daha doğru yaklaşım, `UcabilenKus` gibi bir arayüz kullanmaktır.

---

### 4. I: Interface Segregation Principle (Arayüz Ayırma Prensibi)

**Tanım:** Genel, "şişman" arayüzler (interface) yerine, istemciye özel birden fazla küçük arayüz tercih edilmelidir.

**Açıklama:** Hiçbir sınıf, kullanmadığı metotları içeren bir arayüzü uygulamaya (implement) zorlanmamalıdır. Eğer bir arayüz çok fazla sorumluluk içeriyorsa, o arayüzü uygulayan sınıflar gereksiz metotları boş bırakmak veya hata fırlatmak zorunda kalır. Bu da Liskov prensibini ihlal eder. Arayüzler küçük, odaklı ve istemciye özel olmalıdır.

**Örnek:** Bir `IHemUcanHemYuzebilen` arayüzü yerine; `IUcabilen` ve `IYuzebilen` adında iki ayrı arayüz tanımlayın. Yalnızca uçabilen bir `Kartal` sınıfı yalnızca `IUcabilen` arayüzünü uygular, yüzmek zorunda kalmaz.

---

### 5. D: Dependency Inversion Principle (Bağımlılıkların Tersine Çevrilmesi Prensibi)

**Tanım:** Üst seviye modüller, alt seviye modüllere doğrudan bağlı olmamalıdır. Her ikisi de soyutlamalara (abstractions) bağımlı olmalıdır. Ayrıca, soyutlamalar detaylara bağımlı olmamalı; detaylar soyutlamalara bağımlı olmalıdır.

**Açıklama:** Bu prensip, sınıflar arasındaki sıkı bağımlılığı (tight coupling) azaltmayı amaçlar. Yüksek seviyeli iş kurallarını içeren sınıflar, doğrudan düşük seviyeli veri erişim veya dış servis sınıflarına bağlı olmamalıdır. Bunun yerine bir arayüz (soyutlama) tanımlanır ve bağımlılıklar bu arayüz üzerinden yönetilir. Bu sayede alt seviyedeki değişiklikler üst seviyeyi etkilemez.

**Örnek:** Bir `RaporOlusturucu` sınıfı doğrudan bir `MySQLVeritabani` sınıfına bağlı olmamalıdır. Bunun yerine `IVeriTabani` adında bir arayüz tanımlanır, `MySQLVeritabani` ve ileride eklenecek `PostgreSQLVeritabani` bu arayüzü uygular. `RaporOlusturucu` sadece `IVeriTabani` arayüzüne bağımlı olur. Hangi veritabanının kullanılacağı dışarıdan (constructor veya bir konfigürasyon ile) verilir.

---

**Özetle SOLID**, yazılım projelerinde bağımlılıkları yönetmek, kodu okunabilir kılmak, test edilebilirliği artırmak ve uzun vadede teknik borcu azaltmak için esastır.

### 1. Composition over Inheritance (Kalıtım Yerine Bileşim Kullanma Prensibi)

**Nedir?** Sınıflar arasında davranış paylaşımı için **kalıtım ("is-a" ilişkisi)** yerine **bileşim ("has-a" ilişkisi)** tercih edilmelidir.

**Neden önemli?** Kalıtım, yanlış kullanıldığında "kırılgan üst sınıf" (fragile base class) problemine yol açar. Üst sınıfta yapılan küçük bir değişiklik tüm alt sınıfları etkileyebilir. Bileşim ise daha esnektir; davranışlar çalışma zamanında değiştirilebilir.

**Örnek:** Bir `Araba` sınıfı `Motor` sınıfından türememeli (çünkü araba bir motor değildir). Bunun yerine `Araba`, `IMotor` arayüzünü uygulayan bir nesneyi **içinde barındırmalıdır** (has-a). Böylece benzinli, elektrikli motorları kolayca değiştirebilirsiniz.

---

### 2. Cohesion (Bağımlılık/Uyum) & Coupling (Gevşek/Eşleme)

Bu iki kavram SOLID'in temelindeki metriklerdir:

- **High Cohesion (Yüksek İçsel Uyum):** Bir sınıfın metotları ve alanları, tek ve iyi tanımlanmış bir amaca hizmet etmeli. (Single Responsibility'nin ölçülebilir hali)
- **Low Coupling (Gevşek Eşleme):** Sınıflar birbirini mümkün olduğunca az bilmeli. Bir sınıftaki değişiklik diğerlerini minimum etkilemeli.

**Örnek:** Bir `Rapor` sınıfı hem veri çekiyor, hem formatlıyor, hem de dosyaya yazıyorsa *düşük uyum + yüksek eşleme* vardır. Her bir iş için ayrı sınıflar (VeriKaynagi, Formatter, Yazici) yüksek uyum sağlar.

---

### 3. Design Patterns (Tasarım Kalıpları) – Özellikle 4 Kritik Tanıtım

SOLID’i sindirdikten sonra, sık karşılaşılan problemlere **kanıtlanmış çözümler** olan tasarım kalıpları gelir. En kritik olanlardan birkaçı:

| Kalıp Adı  | Kategorisi | Ne İşe Yarar? |
| --- | --- | --- |
| **Factory (Fabrika)** | Creational (Yaratıcı) | Hangi sınıftan nesne üreteceğine karar verme mantığını istemciden gizler. |
| **Strategy (Strateji)** | Behavioral (Davranışsal) | Algoritmaları arayüz arkasına alıp çalışma anında değiştirir. (DIP’in somut halidir.) |
| **Observer (Gözlemci)** | Behavioral | Bir nesnenin durumu değiştiğinde, ona bağımlı diğer nesnelerin otomatik güncellenmesini sağlar. |
| **Dependency Injection (Bağımlılık Enjeksiyonu)** | Structural (Yapısal) | Bağımlılıkları sınıfın içinde `new` ile oluşturmak yerine dışarıdan verme tekniğidir (DIP'i uygular). |

---

### 4. Polymorphism’in İki Farklı Türü: Static vs Dynamic (Derleme Zamanı Çok Biçimlilik ve Çalışma Zamanı Çok Biçimlilik)

Genellikle sadece sanal metotlar (runtime polymorphism) anlatılır. Oysa iki türü vardır:

- **Static Polymorphism (Derleme Zamanı):**
    - **Method Overloading (Aşırı Yükleme):** Aynı isimde, farklı parametre yapılarında birden fazla metot.
    - **Operator Overloading (C#, C++ gibi dillerde):** `+`, `-` gibi operatörlerin sınıflara özel davranması.
    - Avantajı: Performanslıdır (sanal tablo yoktur). Dezavantajı: Esnek değildir.
- **Dynamic Polymorphism (Çalışma Zamanı):**
    - **Method Overriding (Geçersiz Kılma):** `virtual/override` anahtar kelimeleri.
    - **Avantajı:** Esneklik, bağımlılıkları azaltır, ama ufak bir runtime maliyeti vardır.

---

### 5. Object Lifetime & Memory Management (Nesne Yaşam Döngüsü ve Bellek Yönetimi)

OOP’de bir nesnenin doğumundan ölümüne kadar ne olduğunu bilmek, özellikle performans ve bellek sızıntısı (memory leak) için şarttır.

- **Stack vs Heap (Yığın ve Öbek):** Değer tipleri (int, struct) stack’te, referans tipleri (class, interface) heap’te yaşar. `new` anahtar sözcüğü heap’te yer açar.
- **Garbage Collector (Çöp Toplayıcı - GC):** Heap’te artık referansı kalmayan nesneleri otomatik temizler. Ancak GC ne zaman çalışır? Kesin garantisi yoktur.
- **Dispose Pattern (IDisposable Arayüzü):** Dosya, veritabanı bağlantısı, soket gibi **yönetilmeyen kaynaklar (unmanaged resources)** için `Dispose()` metodu ile kaynakları elle serbest bırakma zorunluluğu.
- **Weak References (Zayıf Referanslar):** GC’nin bir nesneyi toplamasını istiyor ama yine de nesneye erişmeye devam mı etmek istiyorsunuz? `WeakReference` sınıfı bu işe yarar (önbellekleme, gözlemci kalıpları için idealdir).

---