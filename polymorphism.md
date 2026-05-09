## Polymorphism (Çok Biçimlilik) ve Abstraction (Soyutlama) 

Bu iki kavram, Nesne Yönelimli Programlamanın (OOP) temel yapı taşlarındandır.

---

## 1. Abstraction (Soyutlama)

**Tanım:** Bir nesnenin sadece gerekli özelliklerini ve davranışlarını dışarıya gösterip, gereksiz ayrıntıları gizleme prensibidir. "Ne yapıldığı" önemlidir, "nasıl yapıldığı" gizlenir.

### C#’ta soyutlama iki ana yolla sağlanır:

#### a) Abstract Class (Soyut Sınıf)
- `abstract` anahtar kelimesiyle tanımlanır.
- Kendisinden nesne üretilemez (new’lenemez).
- İçinde **abstract** metotlar (gövdesiz) veya normal metotlar (gövdeli) bulunabilir.
- Abstract metotlar, türeyen sınıflar tarafından **zorunlu olarak** implement edilir.

#### b) Interface (Arayüz)
- `interface` anahtar kelimesiyle tanımlanır.
- Sadece metot, property, event imzaları içerir (gövde olmaz – C# 8.0 öncesi, sonrasında default implementasyon eklenebilir).
- Bir sınıf birden fazla interface implement edebilir.
- - Interface = polymorphism değildir, polymorphism inheritance ile yapılmak zorunda değildir.

### Örnek (Abstract Class ile Abstraction):

```csharp
// Soyut sınıf: Çalışanın ne yaptığını tanımlar, nasıl yaptığını söylemez
abstract class Calisan
{
    public string Ad { get; set; }
    
    // Soyut metot: Gövdesi yok, türeyen sınıflar bunu doldurmak zorunda
    public abstract void IsYap();
    
    // Normal metot: Ortak davranış
    public void BilgiVer()
    {
        Console.WriteLine($"Ben {Ad}, bir çalışanım.");
    }
}

// Somut sınıf
class Yazilimci : Calisan
{
    public override void IsYap()
    {
        Console.WriteLine($"{Ad} yazılım geliştiriyor.");
    }
}

class Muhasebeci : Calisan
{
    public override void IsYap()
    {
        Console.WriteLine($"{Ad} faturaları kesiyor.");
    }
}

// Kullanım
Yazilimci yaz = new Yazilimci { Ad = "Ahmet" };
yaz.IsYap();   // Ahmet yazılım geliştiriyor.
yaz.BilgiVer(); // Ben Ahmet, bir çalışanım.
```

---

## 2. Polymorphism (Çok Biçimlilik)

**Tanım:** Aynı isimdeki bir metot veya davranışın, farklı nesnelerde farklı şekillerde çalışabilmesidir. "Bir interface birden çok form".

### C#’ta polymorphism iki türlüdür:

#### a) Static Polymorphism (Derleme Zamanı) – Method Overloading
- Aynı isimde fakat farklı parametre listelerine sahip metotlar.

#### b) Dynamic Polymorphism (Çalışma Zamanı) – Method Overriding (Sanal metotlar)
- `virtual` ve `override` anahtar kelimeleriyle yapılır.
- Taban sınıf referansı, türemiş sınıf nesnesini gösterir ve doğru metot çalışma zamanında belirlenir.

### Örnek (Dynamic Polymorphism):

```csharp
class Hayvan
{
    public virtual void SesCikar()
    {
        Console.WriteLine("Hayvan ses çıkarır.");
    }
}

class Kopek : Hayvan
{
    public override void SesCikar()
    {
        Console.WriteLine("Hav hav!");
    }
}

class Kedi : Hayvan
{
    public override void SesCikar()
    {
        Console.WriteLine("Miyav!");
    }
}

// Polymorphism sayesinde:
Hayvan hayvan1 = new Kopek();
Hayvan hayvan2 = new Kedi();

hayvan1.SesCikar(); // Hav hav!
hayvan2.SesCikar(); // Miyav!
```

Burada `Hayvan` tipindeki referanslar, aslında `Kopek` ve `Kedi` nesnelerini gösterir ve `SesCikar()` metodu **override edilmiş haliyle** çalışır.

---

## Soyutlama vs Çok Biçimlilik – Farklar

| Özellik | Soyutlama (Abstraction) | Polymorphism |
|---------|------------------------|--------------|
| Amaç | Gereksiz detayları gizlemek, arayüz sunmak | Aynı arayüzle farklı davranışlar sergilemek |
| Anahtar araçlar | `abstract`, `interface` | `virtual`, `override`, `new`, overloading |
| Zamanı | Tasarım/geliştirme aşamasında | Genelde çalışma zamanında (dynamic) |
| Örnek | `Hayvan` abstract sınıfının `SesCikar()` metodu | `Kopek` ve `Kedi`’nin farklı `SesCikar()` implementasyonları |

---

## Gerçek hayat benzetmesi

- **Soyutlama:** Araba kullanırken direksiyon, gaz, fren – iç mekanizmayı bilmene gerek yok.
- **Çok biçimlilik:** Farklı marka arabaların (BMW, Mercedes) gaz pedalına basınca farklı motor sesi çıkarması, ama hepsi "gaz ver" komutuna uyması.

---

## Kısa kodla özet (interface + polymorphism)

```csharp
interface ISekil
{
    double AlanHesapla(); // Abstraction
}

class Daire : ISekil
{
    public double Yaricap { get; set; }
    public double AlanHesapla() => Math.PI * Yaricap * Yaricap;
}

class Kare : ISekil
{
    public double Kenar { get; set; }
    public double AlanHesapla() => Kenar * Kenar;
}

// Polymorphism
ISekil[] sekiller = { new Daire { Yaricap = 3 }, new Kare { Kenar = 4 } };
foreach (var sekil in sekiller)
    Console.WriteLine(sekil.AlanHesapla()); // Her biri kendi alanını hesaplar
```

Bu iki prensip birlikte kullanıldığında esnek, bakımı kolay ve genişletilebilir yazılımlar yazılır.

## Değişkenlerle Polymorphism & Abstraction Kullanımı – Kritik Noktalar

 **değişkenler** (variable) ile kullanıldığında önemli farklılıklar ve kritik noktalar vardır.

---

## 1. Referans Tipi vs Nesne Tipi (Reference Type vs Object Type)

**En kritik kural:** Değişkenin **derleme zamanı tipi** (reference type) ile nesnenin **çalışma zamanı tipi** (object type) farklı olabilir.

```csharp
// Örnek hiyerarşi
class Hayvan { public virtual void Ses() => Console.WriteLine("?"); }
class Kopek : Hayvan { public override void Ses() => Console.WriteLine("Hav"); }
class Kedi : Hayvan { public override void Ses() => Console.WriteLine("Miyav"); }

// Değişken kullanımı
Hayvan hayvan;              // Referans tipi: Hayvan
hayvan = new Kopek();       // Nesne tipi: Kopek

hayvan.Ses();               // Hangi metot çalışır? "Hav" (nesne tipine göre)
```

### Kritik Nokta 1:
- **Derleme zamanı:** Derleyici sadece `hayvan` değişkeninin `Hayvan` tipinde olduğunu bilir. Bu yüzden `hayvan` üzerinden sadece `Hayvan` sınıfında tanımlı üyelere erişebilirsiniz.
- **Çalışma zamanı:** Metot çağrısı yapıldığında, nesnenin gerçek tipine (`Kopek`) gidilir ve `override` edilmiş metot çalışır.

```csharp
hayvan.UykuSuresi();   // HATA! Hayvan sınıfında yoksa, Kopek'te olsa bile erişemezsiniz

// Çözüm: Cast işlemi
if (hayvan is Kopek kopek)
{
    kopek.UykuSuresi();  // Artık erişebilirsiniz
}
```

---

## 2. Abstract Class Değişkeni

```csharp
abstract class Sekil
{
    public abstract double Alan();
    public void Bilgi() => Console.WriteLine("Bir şekil");
}

class Daire : Sekil
{
    public double R { get; set; }
    public override double Alan() => Math.PI * R * R;
    public void DaireyeOzel() => Console.WriteLine("Çember çiz");
}

// Kullanım
Sekil sekil = new Daire { R = 5 };  // Abstract tip değişken, somut nesne
Console.WriteLine(sekil.Alan());    // Çalışır - 78.54
sekil.Bilgi();                      // Çalışır - "Bir şekil"
// sekil.DaireyeOzel();             // HATA! Sekil değişkeninde bu metot yok
```

### Kritik Nokta 2:
**Abstract tip değişken, sadece abstract sınıfta tanımlı üyeleri görür.** Alt sınıfa özel üyelere erişmek için cast gerekir.

---

## 3. Interface Değişkeni

```csharp
interface ICalistir
{
    void Calistir();
    string Ad { get; set; }
}

class Uygulama : ICalistir
{
    public string Ad { get; set; }
    public void Calistir() => Console.WriteLine($"{Ad} çalışıyor");
    public void LogYaz() => Console.WriteLine("Log alındı");
}

// Kullanım
ICalistir uyg = new Uygulama { Ad = "Test" };
uyg.Calistir();     // Çalışır
uyg.Ad = "Yeni";    // Çalışır (property)
// uyg.LogYaz();     // HATA! Interface'de yok
```

### Kritik Nokta 3:
**Interface değişkeni, sadece interface sözleşmesindeki üyelere erişir.** Nesnenin kendi ek metotlarına doğrudan erişemezsiniz.

---

## 4. Virtual/Override ile Değişken Davranışı

```csharp
class Base
{
    public virtual void Metot1() => Console.WriteLine("Base Metot1");
    public void Metot2() => Console.WriteLine("Base Metot2");
}

class Derived : Base
{
    public override void Metot1() => Console.WriteLine("Derived Metot1");
    public new void Metot2() => Console.WriteLine("Derived Metot2");  // 'new' ile gizleme
}

// Test
Base obj = new Derived();
obj.Metot1();   // "Derived Metot1" (override → çalışma zamanı)
obj.Metot2();   // "Base Metot2" (new → derleme zamanı tipine göre)
```

### Kritik Nokta 4:
- **`virtual/override`:** Çalışma zamanı tipi belirleyicidir (polymorphism).
- **`new` (gizleme):** Derleme zamanı tipi belirleyicidir (polymorphism yok).
- **Normal metotlar:** Derleme zamanı tipi belirleyicidir.

---

## 5. Değişken Atamaları (Covariance & Contravariance)

### Covariance (Çıkış tipi dönüşümü)

```csharp
IEnumerable<Hayvan> hayvanlar = new List<Kopek>();  // OK! Kopek listesi, Hayvan listesi olarak kullanılabilir

// Kendi örneğiniz (delegate ile)
Func<Hayvan> uretici = () => new Kopek();  // Hayvan dönen metot, Kopek dönebilir
```

### Contravariance (Giriş tipi dönüşümü)

```csharp
Action<Kopek> kopekAksiyon = (k) => k.Ses();
Action<Hayvan> hayvanAksiyon = (h) => h.Ses();

hayvanAksiyon = kopekAksiyon;  // HATA! Normalde olmaz
// Action<in T> sayesinde:
Action<Hayvan> hayvanAksiyon2 = (h) => h.Ses();
Action<Kopek> kopekAksiyon2 = hayvanAksiyon2;  // OK! (contravariance)
```

### Kritik Nokta 5:
- **Covariance:** `T` sadece **çıkış** (return) pozisyonunda (`out T`)
- **Contravariance:** `T` sadece **giriş** (parametre) pozisyonunda (`in T`)
- **Değişken tipleri atanırken bu kurallar geçerlidir**

---

## 6. Değişken Tipi Karar Tablosu

| Durum | Değişken Tipi | Nesne Tipi | Erişilebilen Üyeler | Çalışan Metotlar |
|-------|--------------|------------|-------------------|------------------|
| **Normal** | `Hayvan` | `Hayvan` | Sadece Hayvan'dakiler | Hayvan'dakiler |
| **Upcasting** | `Hayvan` | `Kopek` | Sadece Hayvan'dakiler | Override edildiyse Kopek'tekiler |
| **Downcasting** | `Kopek` | `Kopek` | Hayvan + Kopek'tekiler | Kopek'tekiler |
| **Interface** | `IHayvan` | `Kopek` | Sadece interface üyeleri | Implement edilenler |
| **Abstract** | `Hayvan` (abstract) | `Kopek` | Sadece abstract üyeler + normal | Override edilenler |

---

## 7. Pratikte Kritik Hatalar ve Çözümleri

### Hata 1: Tip dönüşümü kontrolü yapmamak
```csharp
Hayvan h = new Hayvan();
Kopek k = (Kopek)h;  // InvalidCastException fırlatır!

// Doğrusu:
if (h is Kopek kopek)
{
    // güvenli kullanım
}

// veya
Kopek k2 = h as Kopek;
if (k2 != null) { }
```

### Hata 2: Değişken tipinin yanlış anlaşılması
```csharp
List<Hayvan> hayvanlar = new List<Kopek>();  // Derlemez!
// Çözüm:
List<Hayvan> hayvanlar = new List<Hayvan>();
hayvanlar.Add(new Kopek());  // Her eklemede upcast yapılır

// veya covariance ile:
IEnumerable<Hayvan> hayvanlar = new List<Kopek>();  // OK
```

### Hata 3: Abstract sınıf değişkenine new() yapmaya çalışmak
```csharp
Sekil s = new Sekil();  // HATA! Abstract class instantiate edilemez
Sekil s = new Daire();  // DOĞRU
```

---

## 8. Özet Tablo – Kritik Farklar

| Özellik | Normal Sınıf | Abstract Sınıf | Interface |
|---------|-------------|----------------|-----------|
| **Değişken tanımlama** | `Hayvan h;` | `Sekil s;` | `IHayvan i;` |
| **new ile nesne üretme** | `new Hayvan()` | ❌ Yapılamaz | ❌ Yapılamaz |
| **Alt sınıf atama** | `h = new Kopek()` | `s = new Daire()` | `i = new Kopek()` |
| **Erişim kısıtı** | Yok | Sadece abstract sınıftakiler | Sadece interface'dekiler |
| **Polymorphism** | virtual/override ile | Aynı (abstract zaten virtual) | Tüm metotlar polymorphic |
| **Multiple inheritance** | ❌ | ❌ | ✅ (birden fazla) |

---

## En Önemli 3 Kritik Nokta

1. **Değişkenin tipi, o değişken üzerinden hangi üyelere erişebileceğinizi BELİRLER** (derleme zamanı)
2. **Metot çağrılarının hangi implementasyonun çalışacağını nesnenin gerçek tipi BELİRLER** (çalışma zamanı - sadece virtual/override için)
3. **Abstract ve interface tipindeki değişkenler, asla doğrudan new'lenemez; somut bir alt sınıf nesnesi atanmalıdır.**

Bu farklılıkları anlamak, polymorphism ve abstraction'ı değişkenlerle doğru kullanmanın anahtarıdır.

## Örnekler - Polymorphism & Abstraction


---


## Örnek 1: Abstract Class ile Soyutlama (Temel)

```csharp
using System;

abstract class Calisan
{
    public string Ad { get; set; }
    public string Soyad { get; set; }
    
    // Soyut metot - Her çalışan maaşını farklı hesaplar
    public abstract decimal MaasHesapla();
    
    // Normal metot - Herkes için aynı
    public void BilgiVer()
    {
        Console.WriteLine($"{Ad} {Soyad} - Maaş: {MaasHesapla():C2}");
    }
}

class TamZamanli : Calisan
{
    public decimal AylikSabitMaas { get; set; }
    
    public override decimal MaasHesapla()
    {
        return AylikSabitMaas;
    }
}

class YarimZamanli : Calisan
{
    public decimal SaatlikUcret { get; set; }
    public int CalismaSaati { get; set; }
    
    public override decimal MaasHesapla()
    {
        return SaatlikUcret * CalismaSaati;
    }
}

class Komisyonlu : Calisan
{
    public decimal SabitMaas { get; set; }
    public decimal SatisTutari { get; set; }
    public decimal KomisyonOrani { get; set; }
    
    public override decimal MaasHesapla()
    {
        return SabitMaas + (SatisTutari * KomisyonOrani);
    }
}

// Kullanım
class Program
{
    static void Main()
    {
        // Abstract sınıf değişkenleri - Farklı nesneler atanabilir
        Calisan calisan1 = new TamZamanli 
        { 
            Ad = "Ahmet", 
            Soyad = "Yılmaz", 
            AylikSabitMaas = 5000 
        };
        
        Calisan calisan2 = new YarimZamanli 
        { 
            Ad = "Ayşe", 
            Soyad = "Kaya", 
            SaatlikUcret = 150, 
            CalismaSaati = 80 
        };
        
        Calisan calisan3 = new Komisyonlu 
        { 
            Ad = "Mehmet", 
            Soyad = "Demir", 
            SabitMaas = 3000,
            SatisTutari = 20000,
            KomisyonOrani = 0.05m
        };
        
        // Polymorphism: Aynı metot farklı davranışlar sergiler
        calisan1.BilgiVer();  // Ahmet Yılmaz - Maaş: ₺5.000,00
        calisan2.BilgiVer();  // Ayşe Kaya - Maaş: ₺12.000,00 (150*80)
        calisan3.BilgiVer();  // Mehmet Demir - Maaş: ₺4.000,00 (3000 + 20000*0.05)
        
        // KRİTİK NOKTALAR:
        // 1. calisan1, calisan2, calisan3 değişkenleri Calisan tipinde ama farklı nesneler tutuyor
        // 2. calisan1.SabitMaas yazamazsınız - çünkü değişken tipi Calisan
        // 3. Her nesne kendi MaasHesapla() metodunu çalıştırıyor (polymorphism)
    }
}
```

---

## Örnek 2: Interface ile Soyutlama (Çoklu Yetenekler)

```csharp
using System;

// Yetenekleri (abilities) interface olarak tanımlayalım
interface IMaasHesaplanabilir
{
    decimal MaasHesapla();
}

interface IPerformansDegerlendirilebilir
{
    void PerformansDegerlendir(int puan);
}

interface IYonetici
{
    void RaporAl();
    void ToplantiYonet();
}

// Farklı çalışan tipleri farklı yeteneklere sahip
class Yazilimci : Calisan, IMaasHesaplanabilir, IPerformansDegerlendirilebilir
{
    public decimal SaatlikUcret { get; set; }
    public int CalismaSaati { get; set; }
    
    public decimal MaasHesapla()
    {
        return SaatlikUcret * CalismaSaati;
    }
    
    public void PerformansDegerlendir(int puan)
    {
        Console.WriteLine($"{Ad} performans puanı: {puan}/100 - Kod kalitesi değerlendirildi");
    }
    
    public void KodYaz()
    {
        Console.WriteLine($"{Ad} C# kodu yazıyor...");
    }
}

class Mudur : Calisan, IMaasHesaplanabilir, IYonetici, IPerformansDegerlendirilebilir
{
    public decimal SabitMaas { get; set; }
    public decimal Prim { get; set; }
    
    public decimal MaasHesapla()
    {
        return SabitMaas + Prim;
    }
    
    public void RaporAl()
    {
        Console.WriteLine($"{Ad} haftalık raporları inceledi");
    }
    
    public void ToplantiYonet()
    {
        Console.WriteLine($"{Ad} toplantıyı yönetiyor...");
    }
    
    public void PerformansDegerlendir(int puan)
    {
        Console.WriteLine($"{Ad} ekip performans puanı: {puan}/100");
    }
}

class Stajyer : Calisan, IMaasHesaplanabilir
{
    public int StajSuresiGun { get; set; }
    public decimal GunlukUcret { get; set; }
    
    public decimal MaasHesapla()
    {
        return StajSuresiGun * GunlukUcret;
    }
}

// Kullanım
class Program
{
    static void Main()
    {
        // Farklı interface tiplerinde değişkenler
        IMaasHesaplanabilir maasli1 = new Yazilimci 
        { 
            Ad = "Ali", 
            SaatlikUcret = 200, 
            CalismaSaati = 160 
        };
        
        IMaasHesaplanabilir maasli2 = new Mudur 
        { 
            Ad = "Zeynep", 
            SabitMaas = 8000, 
            Prim = 2000 
        };
        
        IYonetici yonetici = new Mudur 
        { 
            Ad = "Zeynep", 
            SabitMaas = 8000, 
            Prim = 2000 
        };
        
        IPerformansDegerlendirilebilir performansli = new Yazilimci 
        { 
            Ad = "Ali", 
            SaatlikUcret = 200, 
            CalismaSaati = 160 
        };
        
        // Polymorphism: Aynı interface farklı davranışlar
        Console.WriteLine($"Ali'nin maaşı: {maasli1.MaasHesapla():C2}");    // 200*160 = 32000
        Console.WriteLine($"Zeynep'in maaşı: {maasli2.MaasHesapla():C2}");   // 8000+2000 = 10000
        
        // KRİTİK NOKTALAR:
        yonetici.RaporAl();      // Çalışır
        yonetici.ToplantiYonet(); // Çalışır
        // yonetici.MaasHesapla(); // HATA! IYonetici'de bu metot yok
        
        performansli.PerformansDegerlendir(85); // Çalışır
        
        // Cast ile diğer özelliklere erişim
        if (maasli1 is Yazilimci yazilimci)
        {
            yazilimci.KodYaz();  // Artık KodYaz() metoduna erişebilirim
        }
    }
}
```

---

## Örnek 3: Değişkenlerin Davranış Farkı (Çok Kritik!)

```csharp
using System;

class Personel
{
    public string Ad { get; set; }
    
    public virtual void Calis()
    {
        Console.WriteLine($"{Ad} genel personel gibi çalışıyor");
    }
    
    public void MolaVer()
    {
        Console.WriteLine($"{Ad} mola verdi");
    }
}

class YazilimMuhendisi : Personel
{
    public override void Calis()
    {
        Console.WriteLine($"{Ad} yazılım geliştiriyor (8 saat kod yazıyor)");
    }
    
    public new void MolaVer()  // 'new' ile gizleme - polymorphism YOK!
    {
        Console.WriteLine($"{Ad} kahve içiyor ve kod review yapıyor");
    }
    
    public void DebugYap()
    {
        Console.WriteLine($"{Ad} hata ayıklıyor");
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine("===== SENARYO 1: Personel tipinde değişken =====\n");
        
        Personel p = new YazilimMuhendisi();
        p.Ad = "Murat";
        
        // Polymorphism: override edilmiş metot
        p.Calis();   // "Murat yazılım geliştiriyor (8 saat kod yazıyor)"
                     // Çalışma zamanında YazilimMuhendisi.Calis() çalışır
        
        // Polymorphism YOK: new ile gizlenmiş metot
        p.MolaVer(); // "Murat mola verdi"
                     // Derleme zamanı tipi (Personel) baz alınır
        
        // HATA: Personel tipinde değişkende DebugYap() yok
        // p.DebugYap();
        
        Console.WriteLine("\n===== SENARYO 2: YazilimMuhendisi tipinde değişken =====\n");
        
        YazilimMuhendisi y = new YazilimMuhendisi();
        y.Ad = "Murat";
        
        y.Calis();     // "Murat yazılım geliştiriyor (8 saat kod yazıyor)"
        y.MolaVer();   // "Murat kahve içiyor ve kod review yapıyor" (YENİ metot)
        y.DebugYap();  // Çalışır
        
        Console.WriteLine("\n===== SENARYO 3: Koleksiyon ile Polymorphism =====\n");
        
        // Farklı tipleri aynı koleksiyonda tutabilirim
        Personel[] personeller = new Personel[]
        {
            new Personel { Ad = "Mehmet" },
            new YazilimMuhendisi { Ad = "Ayşe" },
            new Personel { Ad = "Fatma" },
            new YazilimMuhendisi { Ad = "Ali" }
        };
        
        foreach (var personel in personeller)
        {
            // Her biri kendi Calis() metodunu çalıştırır
            personel.Calis();  // Polymorphism burada devreye giriyor!
            
            // MolaVer() ise değişken tipine göre çalışır (Personel.MolaVer)
            personel.MolaVer();
            
            Console.WriteLine();
        }
    }
}
```

**ÇIKTISI:**
```
===== SENARYO 1: Personel tipinde değişken =====

Murat yazılım geliştiriyor (8 saat kod yazıyor)
Murat mola verdi

===== SENARYO 2: YazilimMuhendisi tipinde değişken =====

Murat yazılım geliştiriyor (8 saat kod yazıyor)
Murat kahve içiyor ve kod review yapıyor
Murat hata ayıklıyor

===== SENARYO 3: Koleksiyon ile Polymorphism =====

Mehmet genel personel gibi çalışıyor
Mehmet mola verdi

Ayşe yazılım geliştiriyor (8 saat kod yazıyor)
Ayşe mola verdi

Fatma genel personel gibi çalışıyor
Fatma mola verdi

Ali yazılım geliştiriyor (8 saat kod yazıyor)
Ali mola verdi
```

---

## Örnek 4: Gerçek Uygulama - Maaş Bordrosu Sistemi

```csharp
using System;
using System.Collections.Generic;

// ABSTRACTION katmanı
abstract class Calisan
{
    public int Id { get; set; }
    public string Ad { get; set; }
    public string Soyad { get; set; }
    public DateTime IseGirisTarihi { get; set; }
    
    public abstract decimal MaasHesapla();
    
    public int KidemYili()
    {
        return DateTime.Now.Year - IseGirisTarihi.Year;
    }
    
    public virtual void RaporYaz()
    {
        Console.WriteLine($"ID: {Id} - {Ad} {Soyad} (Kıdem: {KidemYili()} yıl)");
    }
}

// FARKLI ÇALIŞAN TİPLERİ
class Mudur : Calisan
{
    public decimal SabitMaas { get; set; }
    public decimal YoneticiTazminati { get; set; }
    
    public override decimal MaasHesapla()
    {
        return SabitMaas + YoneticiTazminati + (KidemYili() * 500);
    }
    
    public override void RaporYaz()
    {
        base.RaporYaz();
        Console.WriteLine($"  - Müdür, Sabit Maaş: {SabitMaas:C2}");
    }
}

class SatisTemsilcisi : Calisan
{
    public decimal SaatlikUcret { get; set; }
    public int CalismaSaati { get; set; }
    public decimal AylikSatis { get; set; }
    public decimal KomisyonOrani { get; set; }
    
    public override decimal MaasHesapla()
    {
        decimal baseMaas = SaatlikUcret * CalismaSaati;
        decimal komisyon = AylikSatis * KomisyonOrani;
        return baseMaas + komisyon;
    }
    
    public override void RaporYaz()
    {
        base.RaporYaz();
        Console.WriteLine($"  - Satış Temsilcisi, Satış: {AylikSatis:C2}, Komisyon Oranı: %{KomisyonOrani*100}");
    }
}

class MudurYardimcisi : Calisan
{
    public decimal SabitMaas { get; set; }
    public int YonettigiPersonelSayisi { get; set; }
    
    public override decimal MaasHesapla()
    {
        return SabitMaas + (YonettigiPersonelSayisi * 250);
    }
}

// Bordro hesaplama servisi - Polymorphism kullanımı
class BordroServisi
{
    // Polymorphism sayesinde tek bir metot her tür çalışanın maaşını hesaplar
    public void MaaslariHesapla(List<Calisan> calisanlar)
    {
        Console.WriteLine("======= AYLIK BORDRO =======\n");
        decimal toplamMaas = 0;
        
        foreach (var calisan in calisanlar)
        {
            decimal maas = calisan.MaasHesapla();
            toplamMaas += maas;
            
            calisan.RaporYaz();  // Polymorphism: Her çalışan kendi raporunu yazar
            Console.WriteLine($"  Maaş: {maas:C2}\n");
        }
        
        Console.WriteLine($"TOPLAM BORDRO: {toplamMaas:C2}");
        Console.WriteLine($"Ortalama Maaş: {toplamMaas / calisanlar.Count:C2}");
    }
    
    // KRİTİK: Sadece Mudur tipindeki çalışanlara özel işlem
    public void MudurToplantisi(List<Calisan> calisanlar)
    {
        Console.WriteLine("\n======= MÜDÜRLER TOPLANTISI =======");
        foreach (var calisan in calisanlar)
        {
            // Tip kontrolü ve downcasting
            if (calisan is Mudur mudur)
            {
                Console.WriteLine($"Müdür {mudur.Ad} {mudur.Soyad} toplantıya katıldı");
                // mudur.YoneticiTazminati üzerinde işlem yapabilirim
            }
        }
    }
}

// ANA PROGRAM
class Program
{
    static void Main()
    {
        // Farklı tiplerde çalışanlar oluştur
        var calisanlar = new List<Calisan>
        {
            new Mudur 
            { 
                Id = 1, 
                Ad = "Ali", 
                Soyad = "Yılmaz", 
                IseGirisTarihi = new DateTime(2015, 1, 1),
                SabitMaas = 15000,
                YoneticiTazminati = 5000
            },
            new SatisTemsilcisi 
            { 
                Id = 2, 
                Ad = "Ayşe", 
                Soyad = "Demir", 
                IseGirisTarihi = new DateTime(2020, 6, 15),
                SaatlikUcret = 200,
                CalismaSaati = 160,
                AylikSatis = 50000,
                KomisyonOrani = 0.03m
            },
            new MudurYardimcisi 
            { 
                Id = 3, 
                Ad = "Mehmet", 
                Soyad = "Kaya", 
                IseGirisTarihi = new DateTime(2018, 3, 10),
                SabitMaas = 10000,
                YonettigiPersonelSayisi = 5
            },
            new SatisTemsilcisi 
            { 
                Id = 4, 
                Ad = "Zeynep", 
                Soyad = "Şahin", 
                IseGirisTarihi = new DateTime(2021, 9, 20),
                SaatlikUcret = 180,
                CalismaSaati = 150,
                AylikSatis = 75000,
                KomisyonOrani = 0.04m
            }
        };
        
        // Bordro servisini çalıştır
        var bordro = new BordroServisi();
        bordro.MaaslariHesapla(calisanlar);
        bordro.MudurToplantisi(calisanlar);
        
        // KRİTİK GÖSTERİM: Değişken tipinin önemi
        Console.WriteLine("\n======= DEĞİŞKEN TİPİ GÖSTERİMİ =======");
        
        // Case 1: Abstract tip değişken
        Calisan c = new Mudur { Ad = "Test", SabitMaas = 10000 };
        Console.WriteLine($"c.MaasHesapla(): {c.MaasHesapla():C2}");  // Çalışır
        // c.SabitMaas - HATA! Calisan tipinde SabitMaas yok
        
        // Case 2: Somut tip değişken
        Mudur m = new Mudur { Ad = "Test2", SabitMaas = 10000 };
        Console.WriteLine($"m.SabitMaas: {m.SabitMaas:C2}");  // Çalışır
    }
}
```

---

## ÖZET: Kritik Noktalar ve Akılda Kalıcı Kurallar

### 🔴 ALTIN KURAL 1: "Değişken tipi NE GÖSTERİR, nesne tipi NE YAPAR"
```csharp
Calisan x = new Mudur();  // x'in tipi Calisan (gösterdiği üyeler)
                          // x'in içindeki nesne Mudur (yaptığı işlemler)
```

### 🔴 ALTIN KURAL 2: "virtual/override ZINCİRİ kırar, new ZİNCİRLEMEZ"
- `virtual/override` → Çalışma zamanı (gerçek nesne tipi)
- `new` → Derleme zamanı (değişken tipi)

### 🔴 ALTIN KURAL 3: "Interface ve Abstract new'LENMEZ, SOMUTLAŞTIRILMAZ"
```csharp
ICalisan i = new ICalisan();     // HATA
Calisan c = new Calisan();       // HATA (abstract ise)
Calisan c = new Mudur();         // DOĞRU
```

### 🔴 ALTIN KURAL 4: "is ve as KULLAN, cast yapmadan önce KONTROL ET"
```csharp
if (calisan is Mudur m)  // C# 7+ pattern matching
{
    m.ToplantiYap();
}
```


