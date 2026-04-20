## Kalıtım (Inheritance) Nedir?

Kalıtım, bir sınıfın **başka bir sınıfın özelliklerini (field, property, method) ve davranışlarını miras almasıdır**. Nesne yönelimli programlamada kod tekrarını önleyen, hiyerarşik ilişkiler kuran ve polimorfizmi mümkün kılan temel prensiptir.

**Basitçe:** Bir üst sınıfın (base class) özelliklerini, alt sınıfın (derived class) kullanmasıdır.

---

## Temel Mantık
```
        Base Class (Temel Sınıf)
        ┌─────────────────┐
        │   Vehicle        │
        │  - brand         │
        │  + Start()       │
        └────────┬────────┘
                 │
    ┌────────────┼────────────┐
    │            │            │
    ▼            ▼            ▼
┌───────┐  ┌───────┐  ┌─────────┐
│ Car   │  │ Bike  │  │ Truck   │
│-doors │  │-type  │  │-capacity│
└───────┘  └───────┘  └─────────┘
```
```
Hayvan (Base Class)
    ↑
    ├── Kedi (Derived Class)
    ├── Köpek (Derived Class)  
    └── Kus (Derived Class)
```

Alt sınıflar, üst sınıfın tüm **public** ve **protected** üyelerini miras alır.


## 1. protected Anahtar Kelimesi: "Aile İçinde Kalsın"

`protected`, bir üyenin **sadece aynı sınıf** ve **o sınıftan türeyen sınıflar** tarafından erişilmesini sağlar. Dış dünya (Main, diğer sınıflar) erişemez.

### Erişim Belirteçleri Karşılaştırması

| Erişim | Aynı Sınıf | Miras Alan Sınıf | Dış Dünya |
|--------|-----------|-----------------|-----------|
| `private` | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ❌ |
| `internal` | ✅ | ❌ (farklı assembly) | ❌ |
| `public` | ✅ | ✅ | ✅ |

---

### Temel Kalıtım Örneği

```csharp
// Base Class (Üst Sınıf)
public class Hayvan
{
    public string Ad { get; set; }
    public int Yas { get; set; }
    
    public void NefesAl()
    {
        Console.WriteLine($"{Ad} nefes alıyor...");
    }
    
    public void Beslen()
    {
        Console.WriteLine($"{Ad} besleniyor...");
    }
}

// Derived Class (Alt Sınıf) - Hayvan sınıfından miras alır
public class Kopek : Hayvan
{
    public string Cins { get; set; }
    
    public void Havla()
    {
        Console.WriteLine($"{Ad} havlıyor: Hav hav!");
    }
}

// Kullanım
class Program
{
    static void Main()
    {
        Kopek kopek = new Kopek();
        
        // Miras alınan özellikler
        kopek.Ad = "Karabaş";
        kopek.Yas = 3;
        kopek.NefesAl();     // Karabaş nefes alıyor...
        kopek.Beslen();      // Karabaş besleniyor...
        
        // Kendi özelliği
        kopek.Havla();       // Karabaş havlıyor: Hav hav!
    }
}
```

---

## Constructor ve Kalıtım

### Alt sınıf, üst sınıfın constructor'ını çağırmak **zorundadır**.

```csharp
public class Personel
{
    public string Ad { get; set; }
    public string Soyad { get; set; }
    
    // Base class constructor
    public Personel(string ad, string soyad)
    {
        Ad = ad;
        Soyad = soyad;
        Console.WriteLine("Personel constructor çalıştı");
    }
}

public class Mudur : Personel
{
    public int TakimSayisi { get; set; }
    
    // base() ile üst sınıf constructor'ını çağır
    public Mudur(string ad, string soyad, int takimSayisi) : base(ad, soyad)
    {
        TakimSayisi = takimSayisi;
        Console.WriteLine("Müdür constructor çalıştı");
    }
}
class Program
{
    static void Main()
    {
// Kullanım
    Mudur mudur = new Mudur("Ahmet", "Yılmaz", 5);}}
// Çıktı:
// Personel constructor çalıştı
// Müdür constructor çalıştı
```

---

## Method Override (Metot Ezme)

Alt sınıflar, üst sınıftaki metotları **ezebilir** (override).

```csharp
public class Sekil
{
    // virtual: bu metot ezilebilir
    public virtual double AlanHesapla()
    {
        return 0;
    }
}

public class Daire : Sekil
{
    public double Yaricap { get; set; }
    
    // override: üst sınıftaki metodu eziyor
    public override double AlanHesapla()
    {
        return Math.PI * Yaricap * Yaricap;
    }
}

public class Kare : Sekil
{
    public double Kenar { get; set; }
    
    public override double AlanHesapla()
    {
        return Kenar * Kenar;
    }
}

// Kullanım (Polimorfizm)
Sekil sekil1 = new Daire { Yaricap = 5 };
Sekil sekil2 = new Kare { Kenar = 4 };

Console.WriteLine(sekil1.AlanHesapla());  // 78.54
Console.WriteLine(sekil2.AlanHesapla());  // 16
```

---

## Kalıtım Türleri

| Tür | Açıklama | C# Desteği |
|-----|----------|------------|
| **Tekli Kalıtım** | Bir sınıf tek bir üst sınıftan miras alır | ✅ Evet |
| **Çoklu Kalıtım** | Bir sınıf birden çok üst sınıftan miras alır | ❌ Hayır (Interface ile çözülür) |
| **Çok Seviyeli** | A→B→C şeklinde zincirleme | ✅ Evet |
| **Hiyerarşik** | Bir üst, birden çok alt | ✅ Evet |

```csharp
// Çok Seviyeli Kalıtım
public class A { }
public class B : A { }      // B, A'dan miras alır
public class C : B { }      // C, B'den miras alır (dolaylı olarak A'dan da)

// Hiyerarşik Kalıtım
public class Hayvan { }
public class Kus : Hayvan { }
public class Balik : Hayvan { }
```

---

## sealed Anahtar Kelimesi

`sealed` ile bir sınıfın **miras alınmasını engelleyebiliriz**.

```csharp
public sealed class SonSinif
{
    public void Metot() { }
}

// HATA! SonSinif'ten miras alınamaz
// public class YeniSinif : SonSinif { }  // Derleme hatası
```

---

## Base Keyword ile Üst Sınıfa Erişim

## base Anahtar Kelimesi: "Babaya Selam Gönder"

`base` anahtar kelimesi, **üst sınıfın üyelerine** (constructor, method, property) erişmek için kullanılır.

### Kullanım Alanları:

1. **Constructor Zincirleme** - Alt sınıf constructor'ından üst sınıf constructor'ını çağırma
2. **Override Edilmiş Metotları Çağırma** - Üst sınıftaki orijinal metodu kullanma
3. **Gizlenmiş Üyelere Erişim** - Alt sınıfta aynı isimle gizlenen üst sınıf üyelerine erişme

```csharp
public class Anne
{
    public virtual void MesajVer()
    {
        Console.WriteLine("Anne'den mesaj");
    }
}

public class Cocuk : Anne
{
    public override void MesajVer()
    {
        base.MesajVer();  // Anne'nin metodunu çağır
        Console.WriteLine("Çocuk'tan mesaj");
    }
}

// Kullanım
Cocuk c = new Cocuk();
c.MesajVer();
// Çıktı:
// Anne'den mesaj
// Çocuk'tan mesaj
```
## Constructor Yönetimi ve base ile İletişim

### Kritik Kural:
> **Eğer üst sınıfın parametre alan bir constructor'ı varsa, alt sınıf MECBURİYET bu parametreyi base'e göndermelidir.**

### Örnek: Personel Yönetim Sistemi

```csharp
public class Personel
{
    public string SicilNo { get; private set; }
    public string Ad { get; set; }
    public string Soyad { get; set; }
    public DateTime IseBaslamaTarihi { get; private set; }
    protected decimal Maas { get; set; }
    
    // Parametreli constructor - ZORUNLU!
    public Personel(string sicilNo, string ad, string soyad, DateTime iseBaslama)
    {
        if (string.IsNullOrWhiteSpace(sicilNo))
            throw new ArgumentException("Sicil no boş olamaz!");
        
        SicilNo = sicilNo;
        Ad = ad;
        Soyad = soyad;
        IseBaslamaTarihi = iseBaslama;
        Maas = AsgariUcretHesapla();
        
        Console.WriteLine($"Personel oluşturuldu: {Ad} {Soyad} ({SicilNo})");
    }
    
    private decimal AsgariUcretHesapla()
    {
        // Asgari ücret hesaplama mantığı
        return 17000m;
    }
    
    public virtual void BilgiGoster()
    {
        Console.WriteLine($"\n=== Personel Bilgileri ===");
        Console.WriteLine($"Sicil No: {SicilNo}");
        Console.WriteLine($"Ad Soyad: {Ad} {Soyad}");
        Console.WriteLine($"İşe Başlama: {IseBaslamaTarihi:dd.MM.yyyy}");
        Console.WriteLine($"Maas: {Maas:C}");
        Console.WriteLine($"Kıdem: {KıdemHesapla()} yıl");
    }
    
    protected int KıdemHesapla()
    {
        return DateTime.Now.Year - IseBaslamaTarihi.Year;
    }
    
    public virtual decimal ZamHesapla(decimal oran)
    {
        decimal zamMiktari = Maas * oran;
        Maas += zamMiktari;
        Console.WriteLine($"{Ad} {Soyad} %{oran * 100} zam aldı! Yeni maaş: {Maas:C}");
        return zamMiktari;
    }
}

public class Mudur : Personel
{
    public string Departman { get; set; }
    public List<Personel> Takim { get; private set; }
    private decimal _prim;
    
    // base() ile üst sınıf constructor'ını çağır
    public Mudur(string sicilNo, string ad, string soyad, DateTime iseBaslama, string departman) 
        : base(sicilNo, ad, soyad, iseBaslama)  // ZORUNLU!
    {
        Departman = departman;
        Takim = new List<Personel>();
        _prim = 0;
        Console.WriteLine($"Müdür atandı: {ad} {soyad} - {departman} Departmanı");
    }
    
    public void TakimaEkle(Personel personel)
    {
        Takim.Add(personel);
        Console.WriteLine($"{personel.Ad} {personel.Soyad}, {Departman} takımına eklendi.");
    }
    
    public void PrimVer(decimal miktar)
    {
        _prim += miktar;
        Console.WriteLine($"{Ad} {Soyad} {miktar:C} prim kazandı! Toplam prim: {_prim:C}");
    }
    
    public override void BilgiGoster()
    {
        // base ile üst sınıfın metodunu çağır
        base.BilgiGoster();
        Console.WriteLine($"Departman: {Departman}");
        Console.WriteLine($"Toplam Prim: {_prim:C}");
        Console.WriteLine($"Takım Büyüklüğü: {Takim.Count} kişi");
    }
    
    public override decimal ZamHesapla(decimal oran)
    {
        // Müdürler daha fazla zam alır
        decimal ekOran = 0.05m;  // %5 ek
        decimal toplamOran = oran + ekOran;
        
        Console.WriteLine($"Müdür {Ad} {Soyad} %{ekOran * 100} ek zam alıyor!");
        
        // base ile üst sınıfın zam metodunu çağır
        return base.ZamHesapla(toplamOran);
    }
}

public class Muhendis : Personel
{
    public string UzmanlikAlani { get; set; }
    public List<string> Sertifikalar { get; private set; }
    
    public Muhendis(string sicilNo, string ad, string soyad, DateTime iseBaslama, string uzmanlikAlani)
        : base(sicilNo, ad, soyad, iseBaslama)
    {
        UzmanlikAlani = uzmanlikAlani;
        Sertifikalar = new List<string>();
        Console.WriteLine($"Mühendis: {uzmanlikAlani} uzmanlık alanında göreve başladı.");
    }
    
    public void SertifikaEkle(string sertifika)
    {
        Sertifikalar.Add(sertifika);
        Console.WriteLine($"{Ad} {Soyad} yeni sertifika kazandı: {sertifika}");
    }
    
    public override void BilgiGoster()
    {
        base.BilgiGoster();
        Console.WriteLine($"Uzmanlık Alanı: {UzmanlikAlani}");
        Console.WriteLine($"Sertifika Sayısı: {Sertifikalar.Count}");
    }
    
    public override decimal ZamHesapla(decimal oran)
    {
        // Mühendisler performanslarına göre zam alır
        decimal performansBonus = (decimal)Sertifikalar.Count / 100;
        decimal toplamOran = oran + performansBonus;
        
        Console.WriteLine($"{Ad} {Soyad} {Sertifikalar.Count} sertifika ile %{performansBonus * 100} bonus kazandı!");
        
        return base.ZamHesapla(toplamOran);
    }
}

public class Stajyer : Personel
{
    public int StajSuresi { get; set; }  // Ay cinsinden
    public string Universite { get; set; }
    
    public Stajyer(string sicilNo, string ad, string soyad, DateTime iseBaslama, string universite, int stajSuresi)
        : base(sicilNo, ad, soyad, iseBaslama)
    {
        Universite = universite;
        StajSuresi = stajSuresi;
        Console.WriteLine($"Stajyer: {universite} üniversitesinden {stajSuresi} aylık stajyer.");
    }
    
    public override void BilgiGoster()
    {
        base.BilgiGoster();
        Console.WriteLine($"Üniversite: {Universite}");
        Console.WriteLine($"Staj Süresi: {StajSuresi} ay");
    }
    
    public override decimal ZamHesapla(decimal oran)
    {
        Console.WriteLine("Stajyerler zam alamaz!");
        return 0;
    }
}

// Kullanım
class FirmaYonetimi
{
    static void Main()
    {
        Mudur mudur = new Mudur("M001", "Ahmet", "Yılmaz", new DateTime(2010, 1, 15), "IT");
        
        Muhendis muhendis1 = new Muhendis("E001", "Ayşe", "Demir", new DateTime(2015, 3, 20), "Backend");
        Muhendis muhendis2 = new Muhendis("E002", "Mehmet", "Kaya", new DateTime(2018, 6, 10), "Frontend");
        
        Stajyer stajyer = new Stajyer("S001", "Zeynep", "Çelik", new DateTime(2024, 1, 10), "ODTÜ", 6);
        
        muhendis1.SertifikaEkle("Azure Developer");
        muhendis1.SertifikaEkle("AWS Solutions Architect");
        
        mudur.TakimaEkle(muhendis1);
        mudur.TakimaEkle(muhendis2);
        mudur.TakimaEkle(stajyer);
        
        mudur.PrimVer(15000);
        
        muhendis1.ZamHesapla(0.15m);
        muhendis2.ZamHesapla(0.10m);
        stajyer.ZamHesapla(0.05m);
        
        mudur.BilgiGoster();
        muhendis1.BilgiGoster();
    }
}
```
## Önemli Kritik Noktalar

### Nokta 1: Kalıtım Zinciri ve Constructor Sırası

```csharp
public class A
{
    public A()
    {
        Console.WriteLine("A constructor çalıştı");
    }
}

public class B : A
{
    public B()
    {
        Console.WriteLine("B constructor çalıştı");
    }
}

public class C : B
{
    public C()
    {
        Console.WriteLine("C constructor çalıştı");
    }
}

// Kullanım
C c = new C();
// Çıktı SIRASI:
// A constructor çalıştı
// B constructor çalıştı
// C constructor çalıştı
// NOT: EN ÜST sınıftan EN ALT sınıfa doğru çalışır!
```

### Nokta 2: Hiding (Gizleme) vs Overriding (Ezme)

```csharp
public class Base
{
    public virtual void Metot1()
    {
        Console.WriteLine("Base.Metot1");
    }
    
    public void Metot2()
    {
        Console.WriteLine("Base.Metot2");
    }
}

public class Derived : Base
{
    // Override - POLİMORFİZM destekler
    public override void Metot1()
    {
        Console.WriteLine("Derived.Metot1");
        base.Metot1();  // İstenirse base çağrılabilir
    }
    
    // Hiding - YENİ bir metot tanımlar (new keyword)
    public new void Metot2()
    {
        Console.WriteLine("Derived.Metot2");
    }
}

// Kullanım
Base b = new Derived();
b.Metot1();  // Derived.Metot1 (polimorfizm çalışır)
b.Metot2();  // Base.Metot2 (hiding, base referansı base'i çağırır!)

Derived d = new Derived();
d.Metot2();  // Derived.Metot2
```

### Nokta 3: sealed ile Kalıtımı Engelleme

```csharp
public sealed class SonSinif
{
    public void Metot() { }
}

// HATA! 'SonSinif' sealed olduğu için miras alınamaz
// public class Hata : SonSinif { }

public class BaseClass
{
    public virtual void Metot() { }
}

public class DerivedClass : BaseClass
{
    // sealed override - bu metot artık alt sınıflarda ezilemez
    public sealed override void Metot()
    {
        base.Metot();
    }
}

// HATA! Metot sealed olduğu için override edilemez
// public class LastClass : DerivedClass
// {
//     public override void Metot() { }  // HATA!
// }
```

### Nokta 4: abstract ve virtual Farkı

| Özellik | abstract | virtual |
|---------|----------|---------|
| Gövde (Body) | Yok (sadece imza) | Var (varsayılan implementasyon) |
| Zorunluluk | Alt sınıf IMPLEMENT ETMEK ZORUNDA | Alt sınıf ister override eder, ister etmez |
| Instance oluşturma | Abstract sınıftan instance oluşturulamaz | Normal sınıftan instance oluşturulabilir |

```csharp
public abstract class Hayvan
{
    public abstract void SesCikar();  // Her hayvan farklı ses çıkarır
    
    public virtual void NefesAl()
    {
        Console.WriteLine("Hayvan nefes alıyor...");  // Ortak davranış
    }
}

public class Kedi : Hayvan
{
    public override void SesCikar()  // ZORUNLU!
    {
        Console.WriteLine("Miyav!");
    }
    
    // NefesAl'ı override etmek ZORUNLU DEĞİL
}

public class Kopek : Hayvan
{
    public override void SesCikar()  // ZORUNLU!
    {
        Console.WriteLine("Hav hav!");
    }
    
    public override void NefesAl()  // İSTEĞE BAĞLI
    {
        base.NefesAl();
        Console.WriteLine("Köpek nefes alıyor...");
    }
}
```

---
## Daha sonra bahsedeceğiz.
## Kalıtım vs Interface Karşılaştırması

| Özellik | Kalıtım (class) | Interface |
|---------|----------------|-----------|
| Birden çok kalıtım | ❌ | ✅ (birden fazla implement edilebilir) |
| Field içerebilir | ✅ | ❌ (sadece property tanımı) |
| Method gövdesi | ✅ | ❌ (C# 8.0'dan önce) |
| Constructor | ✅ | ❌ |
| Erişim belirteçleri | ✅ (private, protected vs) | ❌ (hepsi public) |

---

## Önemli Kurallar

1. **Bir sınıf sadece bir sınıftan miras alabilir** (çoklu kalıtım yok)
2. **Constructor zinciri** en üst sınıftan başlayarak çalışır
3. **Private üyeler** miras alınmaz (alt sınıf erişemez)
4. **Protected üyeler** miras alınır ve alt sınıf erişebilir
5. **Static üyeler** de miras alınır

---

## Ürün Stok Örneği

```csharp
public class Urun
{
    public int Id { get; set; }
    public string Ad { get; set; }
    public decimal Fiyat { get; set; }
    
    public virtual decimal KdvHesapla()
    {
        return Fiyat * 0.18m;  // %18 KDV
    }
}

public class Kitap : Urun
{
    public string Yazar { get; set; }
    public string Isbn { get; set; }
    
    public override decimal KdvHesapla()
    {
        return Fiyat * 0.08m;  // Kitaplar %8 KDV
    }
}

public class Elektronik : Urun
{
    public int GarantiSuresi { get; set; }
    
    public override decimal KdvHesapla()
    {
        return Fiyat * 0.20m;  // Elektronik %20 KDV
    }
}
```

---

## Özet


| Kavram | Ne İçin Kullanılır? | Önemli Not |
|--------|-------------------|-----------|
| **protected** | Mirasçılara veri/metot açmak için | `private`'ın bir tık daha esnek halidir. Dış dünyadan gizler, aileye açar. |
| **base** | Üst sınıfa ulaşmak için | Hem metotlara hem constructor'lara erişim sağlar. |
| **virtual** | Alt sınıfların ezebileceği metotlar için | Varsayılan implementasyon sağlar. |
| **override** | virtual/abstract metodu ezmek için | `base` ile üst sınıf metodu çağrılabilir. |
| **abstract** | Zorunlu implementasyon için | Gövdesi yok, alt sınıf implement etmek zorunda. |
| **sealed** | Kalıtımı engellemek için | Sınıfa veya metoda uygulanabilir. |
| **new (hiding)** | Metot gizlemek için | Polimorfizm çalışmaz, referans tipine göre karar verilir. |

### Constructor Zinciri Kuralı

```
Üst Sınıf (Base) → parametreli constructor
        ↓
Alt Sınıf (Derived) : base(parametreler) → ZORUNLU!
```
**Kalıtım = Kod tekrarını önler + Hiyerarşik ilişki kurar**

- `:` işareti ile miras alınır
- `base` ile üst sınıfa erişilir
- `virtual`/`override` ile metotlar ezilir
- `sealed` ile miras alınması engellenir
- **IS-A** ilişkisi kurar (Köpek **bir** Hayvan'dır)

---
## Uygulama Sorusu


> **Soru:** Bir `Araba` (Ata Sınıf) ve `ElektrikliAraba` (Alt Sınıf) tasarladığını düşün. `BataryaKapasitesi` bilgisini sadece `ElektrikliAraba` içinden değiştirebilmek için hangi erişim belirleyiciyi kullanırdın? Ve `ElektrikliAraba` oluşurken marka bilgisini `Araba` sınıfına nasıl iletirsin?

**Cevap:**

1. **Erişim belirleyici:** `protected` kullanılır. Çünkü:
   - `private` olsaydı `ElektrikliAraba` erişemezdi
   - `public` olsaydı dış dünya da değiştirebilirdi (güvenlik riski)
   - `protected` ise sadece miras alan sınıfların erişmesine izin verir

2. **Marka bilgisini iletme:** `base` anahtar kelimesi ile constructor üzerinden iletilir:
   ```csharp
   public ElektrikliAraba(string marka, string model) : base(marka, model)
   {
       // ...
   }
   ```

---

Kalıtım, doğru kullanıldığında kodunuzu **esnek**, **tekrar kullanılabilir** ve **bakımı kolay** hale getirir. Ancak gereksiz kalıtım (inheritance) yerine **kompozisyon (composition)** tercih edilmelidir - bu ayrı bir konunun başlığıdır.

## 5. Gerçek Hayat Senaryoları

### Senaryo 1: Araba ve Elektrikli Araba (Uygulama Sorusunun Çözümü)
```csharp
using System;

public class Araba
{
    public string Marka { get; set; }
    public string Model { get; set; }
    public int UretimYili { get; set; }
    
    // BATARYA KAPASİTESİ İÇİN protected KULLANILIR
    // Neden? Sadece miras alan sınıflar erişebilsin, dış dünya erişemesin
    protected int BataryaKapasitesi { get; set; }
    
    public Araba(string marka, string model, int uretimYili)
    {
        Marka = marka;
        Model = model;
        UretimYili = uretimYili;
        Console.WriteLine($"Araba oluşturuldu: {marka} {model} ({uretimYili})");
    }
    
    public virtual void Calistir()
    {
        Console.WriteLine($"{Marka} {Model} çalıştırılıyor...");
    }
    
    public void BilgiGoster()
    {
        Console.WriteLine($"\n=== Araba Bilgileri ===");
        Console.WriteLine($"Marka: {Marka}");
        Console.WriteLine($"Model: {Model}");
        Console.WriteLine($"Yıl: {UretimYili}");
    }
}

public class ElektrikliAraba : Araba
{
    public int Menzil { get; private set; }
    
    // Constructor: base ile marka, model, yıl bilgilerini üst sınıfa ilet
    public ElektrikliAraba(string marka, string model, int uretimYili, int bataryaKapasitesi, int menzil) 
        : base(marka, model, uretimYili)  // İletim burada!
    {
        BataryaKapasitesi = bataryaKapasitesi;  // protected field'a erişim
        Menzil = menzil;
        Console.WriteLine($"Elektrikli araba özellikleri eklendi: {bataryaKapasitesi} kWh batarya, {menzil} km menzil");
    }
    
    public void BataryaGuncelle(int yeniKapasite)
    {
        // SADECE ElektrikliAraba sınıfı içinden değiştirilebilir
        // Dışarıdan (Main'den) direkt erişilemez
        if (yeniKapasite > 0 && yeniKapasite <= 200)
        {
            BataryaKapasitesi = yeniKapasite;
            Menzil = BataryaKapasitesi * 6;  // 1 kWh = 6 km
            Console.WriteLine($"Batarya güncellendi: {BataryaKapasitesi} kWh, Yeni menzil: {Menzil} km");
        }
        else
        {
            Console.WriteLine("Geçersiz batarya kapasitesi! (1-200 kWh arası olmalı)");
        }
    }
    
    public override void Calistir()
    {
        base.Calistir();  // Üst sınıfın metodunu çağır
        Console.WriteLine($"Elektrik motoru sessizce çalışıyor. Batarya: {BataryaKapasitesi} kWh");
        Console.WriteLine($"Tahmini menzil: {Menzil} km");
    }
    
    public void SarjEt()
    {
        Console.WriteLine($"{Marka} {Model} şarj ediliyor...");
        Console.WriteLine($"Mevcut batarya: {BataryaKapasitesi} kWh");
    }
    
    public void BilgiGoster()
    {
        base.BilgiGoster();
        Console.WriteLine($"Batarya Kapasitesi: {BataryaKapasitesi} kWh");
        Console.WriteLine($"Menzil: {Menzil} km");
    }
}

// Kullanım
class Test
{
    static void Main()
    {
        Console.WriteLine("=== ELEKTRİKLİ ARAÇ DEMOSU ===\n");
        
        // DOĞRU KULLANIM
        Console.WriteLine("1. Araba Oluşturuluyor:");
        ElektrikliAraba tesla = new ElektrikliAraba("Tesla", "Model 3", 2024, 75, 500);
        
        Console.WriteLine("\n2. Araba Bilgileri:");
        tesla.BilgiGoster();
        
        Console.WriteLine("\n3. Araba Çalıştırılıyor:");
        tesla.Calistir();
        
        Console.WriteLine("\n4. Batarya Güncelleniyor:");
        tesla.BataryaGuncelle(82);  // Metot ile güncelleme - MÜMKÜN
        
        Console.WriteLine("\n5. Şarj İşlemi:");
        tesla.SarjEt();
        
        Console.WriteLine("\n6. Güncel Bilgiler:");
        tesla.BilgiGoster();
        
        Console.WriteLine("\n7. Geçersiz Batarya Denemesi:");
        tesla.BataryaGuncelle(250);  // Hata mesajı verecek
        
        Console.WriteLine("\n8. İkinci Elektrikli Araba Örneği:");
        ElektrikliAraba bmw = new ElektrikliAraba("BMW", "i4", 2024, 80, 550);
        bmw.BilgiGoster();
        bmw.Calistir();
        
        // HATALI KULLANIM (Derleme hatası verir!)
        // Bu satırın başındaki // işaretini silerseniz derleme hatası alırsınız
        // tesla.BataryaKapasitesi = 100;  // HATA! protected, dışarıdan erişilemez!
        
        Console.WriteLine("\n=== PROTECTED ERİŞİM BELİRLEYİCİSİ ===");
        Console.WriteLine("BataryaKapasitesi protected olduğu için:");
        Console.WriteLine("✓ ElektrikliAraba sınıfı içinden erişilebilir");
        Console.WriteLine("✓ BataryaGuncelle() metodu ile değiştirilebilir");
        Console.WriteLine("✗ Test sınıfından (dışarıdan) direkt erişilemez");
        
        Console.WriteLine("\nProgramı kapatmak için bir tuşa basın...");
        Console.ReadKey();
    }
}
```
---
### Senaryo 2: Oyun Karakter Sistemi (Detaylı)

```csharp
using System;

public class Karakter
{
    public string Isim { get; set; }
    public int Seviye { get; set; }
    
    // PROTECTED: Dışarıdan direkt müdahale edilemez
    // Ancak miras alan sınıflar erişebilir
    protected int Can { get; set; }
    protected int Mana { get; set; }
    protected int Deneyim { get; set; }
    
    // Private - sadece bu sınıf içinde
    private int _kritikVurusSayisi;
    
    public Karakter(string isim)
    {
        Isim = isim;
        Can = 100;
        Mana = 50;
        Seviye = 1;
        Deneyim = 0;
        Console.WriteLine($"{Isim} adlı karakter oluşturuldu!");
    }
    
    // Protected method - sadece mirasçılar kullanabilir
    protected void SeviyeAtla()
    {
        Seviye++;
        Can = 100 + (Seviye - 1) * 20;
        Mana = 50 + (Seviye - 1) * 10;
        Console.WriteLine($"TEBRİKLER! {Isim} Seviye {Seviye} oldu!");
    }
    
    protected void DeneyimKazan(int miktar)
    {
        Deneyim += miktar;
        Console.WriteLine($"{Isim} {miktar} deneyim kazandı! Toplam: {Deneyim}");
        
        // Her 100 deneyimde seviye atla
        if (Deneyim >= 100)
        {
            Deneyim -= 100;
            SeviyeAtla();
        }
    }
    
    public void DurumGoster()
    {
        Console.WriteLine($"\n=== {Isim} Durumu ===");
        Console.WriteLine($"Seviye: {Seviye}");
        Console.WriteLine($"Can: {Can}/{100 + (Seviye-1)*20}");
        Console.WriteLine($"Mana: {Mana}/{50 + (Seviye-1)*10}");
    }
}

public class Savasci : Karakter
{
    private int _ofke;
    
    public Savasci(string isim) : base(isim)
    {
        Console.WriteLine($"{isim} savaşçı sınıfına özel yetenekler kazandı!");
        _ofke = 0;
    }
    
    public void HasarAl(int miktar)
    {
        // protected field'a erişim - MÜMKÜN
        Can -= miktar;
        _ofke += miktar / 10;
        
        Console.WriteLine($"{Isim} {miktar} hasar aldı! Kalan Can: {Can}");
        
        if (Can <= 0)
        {
            Console.WriteLine($"{Isim} savaştan yenik ayrıldı!");
            Can = 0;
        }
        
        if (_ofke >= 10)
        {
            Console.WriteLine($"{Isim} ÖFKELENDİ! Hasarı ikiye katlandı!");
        }
    }
    
    public void VurusYap()
    {
        int hasar = 20 + (Seviye - 1) * 5;
        
        if (_ofke >= 10)
        {
            hasar *= 2;
            _ofke -= 10;
            Console.WriteLine($"{Isim} ÖFKELİ VURUŞ yaptı!");
        }
        
        Console.WriteLine($"{Isim} {hasar} hasar verdi!");
        DeneyimKazan(10);  // protected method'a erişim - MÜMKÜN
    }
    
    public void CanIkinci()
    {
        if (Can <= 30)
        {
            Can += 50;
            Console.WriteLine($"{Isim} ikinci can hakkını kullandı! Can: {Can}");
        }
        else
        {
            Console.WriteLine("Can iksiri için canın 30'un altında olmalı!");
        }
    }
}

public class Buyucu : Karakter
{
    private int _buyuGucu;
    
    public Buyucu(string isim) : base(isim)
    {
        Console.WriteLine($"{isim} büyücü sınıfına özel yetenekler kazandı!");
        _buyuGucu = 15;
    }
    
    public void BuyuYap()
    {
        if (Mana >= 20)  // protected field'a erişim
        {
            Mana -= 20;
            int hasar = 30 + _buyuGucu + (Seviye - 1) * 8;
            Console.WriteLine($"{Isim} {hasar} büyü hasarı verdi!");
            DeneyimKazan(15);  // protected method'a erişim
        }
        else
        {
            Console.WriteLine($"{Isim} yeterli manası yok! Mevcut Mana: {Mana}");
        }
    }
    
    public void ManaIksir()
    {
        Mana += 30;
        Console.WriteLine($"{Isim} mana iksiri içti! Mana: {Mana}");
    }
    
    public void HasarAl(int miktar)
    {
        // Büyücüler daha az dayanıklı
        int azaltilmisHasar = miktar / 2;
        Can -= azaltilmisHasar;
        Console.WriteLine($"{Isim} {azaltilmisHasar} hasar aldı! Kalan Can: {Can}");
        
        // Can 0'in altina duserse kontrol et
        if (Can <= 0)
        {
            Console.WriteLine($"{Isim} öldü!");
            Can = 0;
        }
    }
}

// Kullanım
class Oyun
{
    static void Main()
    {
        Console.WriteLine("=== KARAKTER SAVAS SIMULASYONU ===\n");
        
        Savasci savasci = new Savasci("Conan");
        Buyucu buyucu = new Buyucu("Merlin");
        
        Console.WriteLine("\n--- SAVASCI HAREKETLERI ---");
        savasci.VurusYap();
        savasci.VurusYap();
        savasci.HasarAl(30);
        savasci.CanIkinci();
        
        Console.WriteLine("\n--- BUYUCU HAREKETLERI ---");
        buyucu.BuyuYap();
        buyucu.ManaIksir();
        buyucu.BuyuYap();
        buyucu.HasarAl(40);
        
        Console.WriteLine("\n--- SON DURUMLAR ---");
        savasci.DurumGoster();
        buyucu.DurumGoster();
        
        // AŞAĞIDAKİLER HATA VERİR:
        // savasci.Can = 100;     // HATA! protected
        // savasci.DeneyimKazan(5); // HATA! protected method
        
        Console.WriteLine("\nNot: Protected alanlar ve metotlar disaridan erisilemez,");
        Console.WriteLine("ancak miras alan siniflar (Savasci ve Buyucu) icinde kullanilabilir.");
        
        Console.WriteLine("\nProgrami kapatmak icin bir tusa basin...");
        Console.ReadKey();
    }
}


// ÇIKTI
=== KARAKTER SAVAS SIMULASYONU ===

Conan adlı karakter oluşturuldu!
Conan savaşçı sınıfına özel yetenekler kazandı!
Merlin adlı karakter oluşturuldu!
Merlin büyücü sınıfına özel yetenekler kazandı!

--- SAVASCI HAREKETLERI ---
Conan 20 hasar verdi!
Conan 10 deneyim kazandı! Toplam: 10
Conan 20 hasar verdi!
Conan 10 deneyim kazandı! Toplam: 20
Conan 30 hasar aldı! Kalan Can: 70
Conan ikinci can hakkını kullandı! Can: 120

--- BUYUCU HAREKETLERI ---
Merlin 45 büyü hasarı verdi!
Merlin 15 deneyim kazandı! Toplam: 15
Merlin mana iksiri içti! Mana: 80
Merlin 45 büyü hasarı verdi!
Merlin 15 deneyim kazandı! Toplam: 30
Merlin 20 hasar aldı! Kalan Can: 80

--- SON DURUMLAR ---

=== Conan Durumu ===
Seviye: 1
Can: 120/100
Mana: 50/50

=== Merlin Durumu ===
Seviye: 1
Can: 80/100
Mana: 60/50

Not: Protected alanlar ve metotlar disaridan erisilemez,
ancak miras alan siniflar (Savasci ve Buyucu) icinde kullanilabilir.

Programi kapatmak icin bir tusa basin...
```

### Senaryo 3: Şekil Hiyerarşisi

```csharp
public abstract class Sekil
{
    public string Renk { get; set; }
    
    public Sekil(string renk)
    {
        Renk = renk;
    }
    
    // Abstract method - her şekil farklı hesaplar
    public abstract double AlanHesapla();
    public abstract double CevreHesapla();
    
    // Virtual method - istenirse override edilebilir
    public virtual void BilgiGoster()
    {
        Console.WriteLine($"\n=== Şekil Bilgileri ===");
        Console.WriteLine($"Renk: {Renk}");
        Console.WriteLine($"Alan: {AlanHesapla():F2}");
        Console.WriteLine($"Çevre: {CevreHesapla():F2}");
    }
}

public class Daire : Sekil
{
    public double Yaricap { get; set; }
    
    public Daire(string renk, double yaricap) : base(renk)
    {
        Yaricap = yaricap;
    }
    
    public override double AlanHesapla()
    {
        return Math.PI * Yaricap * Yaricap;
    }
    
    public override double CevreHesapla()
    {
        return 2 * Math.PI * Yaricap;
    }
}

public class Dikdortgen : Sekil
{
    public double Genislik { get; set; }
    public double Yukseklik { get; set; }
    
    public Dikdortgen(string renk, double genislik, double yukseklik) : base(renk)
    {
        Genislik = genislik;
        Yukseklik = yukseklik;
    }
    
    public override double AlanHesapla()
    {
        return Genislik * Yukseklik;
    }
    
    public override double CevreHesapla()
    {
        return 2 * (Genislik + Yukseklik);
    }
}

public class Kare : Dikdortgen
{
    public Kare(string renk, double kenar) : base(renk, kenar, kenar)
    {
    }
    
    public override void BilgiGoster()
    {
        base.BilgiGoster();
        Console.WriteLine($"Kenar Uzunluğu: {Genislik}");
    }
}
```