## Kapsülleme (Encapsulation) Nedir?

Kapsülleme, nesne yönelimli programlamanın temel prensiplerinden biridir. **Verileri (fields) ve bu veriler üzerinde işlem yapan metotları bir arada tutarak, verilere doğrudan erişimi kısıtlayıp kontrollü erişim sağlama prensibidir.**

Basitçe: **"Gizlemesi gerekeni gizle, açması gerekeni aç."**

---

## C# ile Örnekler

###  Kapsülleme olmadan;

```csharp
public class Ogrenci
{
    public string Ad { get; set; }      // Herkes okuyup yazabilir
    public int Yas { get; set; }         // Yaş negatif olabilir, kontrol yok!
}
```

Burada herkes `Yas` özelliğine `-5` gibi geçersiz bir değer atayabilir.

---

###  Kapsülleme ile;

```csharp
using System;

public class Ogrenci
{
    // Field'lar private -> dış dünyaya kapalı
    private string ad; 
    private int yas;

    // Property'ler ile kontrollü erişim
    public string Ad
    {
        get { return ad; }
        set 
        { 
            if(!string.IsNullOrWhiteSpace(value))
                ad = value;
            else
                throw new ArgumentException("Ad boş olamaz!");
        }
    }

    public int Yas
    {
        get { return yas; }
        set
        {
            if(value >= 0 && value <= 35)
                yas = value;
            else
                throw new ArgumentException("Yaş 0-35 arasında olmalı!");
        }
    }

    // Sadece okunabilir property (set yok)
    public string BilgiAl()
    {
        return $"{Ad} - {Yas} yaşında";
    }
}

class Program
{
    static void Main()
    {
        Ogrenci ogr = new Ogrenci(); //nesne oluşturuldu
        ogr.Ad = "Ali"; // Set çalışır
        ogr.Yas = 25; // Set çalışır
        //ogr.Yas = -10;  // HATA fırlatır // Bunu denemek istersen başındaki // işaretini sil
        Console.WriteLine(ogr.BilgiAl());
    }
}
```

---

## Kapsülleme Sağlayan C# Yapıları

| Yapı | Erişim |
|------|--------|
| `private` | Sadece aynı sınıf |
| `protected` | Sınıf + miras alanlar |
| `internal` | Aynı assembly |
| `public` | Herkes | En açık |
| `private set` | Okuma herkese açık, yazma sadece sınıf içinde |


## Field ve Property Nedir? Neden Kullanılır?

### Field (Alan)

```csharp
public class Araba
{
    // Field - Veriyi saklayan değişkendir
    private string _model;      // private field - dışarıdan direkt erişilemez
    public int hiz;             // public field - önerilmez!
}
```

**Field'lar** bir sınıfın **iç verilerini tutan değişkenlerdir**. Genellikle `private` yapılırlar.

### Property (Özellik)

```csharp
public class Araba
{
    private string _model;  // field (veri saklama)
    
    // Property (kontrollü erişim kapısı)
    public string Model
    {
        get { return _model; }   // okuma
        set { _model = value; }  // yazma
    }
}
```

**Property'ler** field'lara **kontrollü erişim** sağlayan özel metotlardır (`get` ve `set`).

### Neden Property Kullanmalıyız?

| Field (Direkt Erişim) | Property (Kontrollü Erişim) |
|----------------------|----------------------------|
| Kontrol mekanizması yok | Geçerlilik kontrolü yapılabilir |
| Bağımlılık yaratır | İç implementasyon değişse bile dışarıya aynı arayüz sunulur |
| Loglama eklenemez | get/set içinde loglama yapılabilir |
| Hesaplanmış değer döndürülemez | get içinde hesaplama yapılabilir |

---

## Benzetme ile Anlatım: Araba ve Gaz Pedalı

> Bir araba düşünün; hızlanmak için gaz pedalına (Property/Method) basarsınız. Motorun içine girip yakıt püskürtme memesini elinizle açmazsınız. Motorun iç işleyişi "kapsüllenmiştir". Siz sadece size sunulan arayüzü kullanırsınız.

```
Araba Nesnesi
┌─────────────────────────────────┐
│  private int _yakıtMiktarı;     │ ← Gizli field (motor içi)
│  private int _devir;            │
│                                 │
│  public void GazVer()           │ ← Public property (gaz pedalı)
│  {                              │
│      if(_yakıtMiktarı > 0)      │ ← Kontrol mekanizması
│          _devir += 500;         │
│  }                              │
└─────────────────────────────────┘
          ↑
          │ Sadece buraya erişirsin
    [Sürücü (Kod)]
```


### Kısa Property Söz Dizimi (Auto-Property)

Arka planda özel bir alan (field) tanımlamadan, doğrudan `get` ve `set` metotlarını kullanarak kod karmaşasını azaltır. public erişim belirleyicisi, veri tipi ve özellik adının ardına `{ get; set; } `yazılarak tanımlanır.

Söz Dizimi: `public <VeriTipi> <OzellikAdi> { get; set; }`
Örnek: `public string Ad { get; set; }`

```csharp
public class Araba
{
    public string Model { get; private set; }  // Dışarıdan set edilemez
    public int Hiz { get; set; }               // Herkes set edebilir
    
    public Araba(string model)
    {
        Model = model;  // Sadece constructor'da set edilebilir
    }
}
```

---

## Neden Kapsülleme Kullanmalıyız?

| Fayda | Açıklama |
|-------|----------|
| **Kontrollü erişim** | Geçersiz veri girişini engeller |
| **Güvenlik** | Hassas verileri gizler |
| **Esneklik** | İç implementasyon değişse bile dışarıya aynı arayüz sunulur |
| **Bağımlılık azaltma** | Nesneler birbirinin iç detaylarını bilmez |
| **Bakım kolaylığı** | Hata ayıklama ve değişiklik yapma kolaylaşır |

---

## Özet

**Kapsülleme = Veri Saklama + Metotlarla Kontrollü Erişim**

C#'ta bunu `private` field'lar ve `public`/`internal` property'ler/metotlar ile sağlarız. Böylece nesneler kendi verilerinin bütünlüğünü koruyabilir.

---

## Kapsülleme Uygulama Stratejileri

### 1. **Read-Only Property (Sadece Okunabilir)**

```csharp
private DateTime _olusturulmaTarihi;

public DateTime OlusturulmaTarihi
{
    get { return _olusturulmaTarihi; }
    // Set yok - sadece constructor'da belirlenir
}
```

### 2. **Write-Only Property (Sadece Yazılabilir) - Nadir**

```csharp
private string _sifre;

public string Sifre
{
    set 
    { 
        _sifre = Hashle(value);  // Sadece set var
    }
    // Get yok - kimse şifreyi okuyamaz
}
```

### 3. **Computed Property (Hesaplanmış Özellik)**

```csharp
private decimal _fiyat;
private double _kdvOrani;

public decimal KdvliFiyat
{
    get { return _fiyat * (1 + (decimal)_kdvOrani); }
    // Set yok - hesaplanıyor
}
```

### 4. **Validation Property (Doğrulamalı)**

```csharp
private string _email;

public string Email
{
    get { return _email; }
    set
    {
        if (value.Contains("@") && value.Contains("."))
            _email = value;
        else
            throw new ArgumentException("Geçerli bir email adresi giriniz!");
    }
}
```

### 5. **Lazy Initialization (Tembel Başlatma)**

```csharp
private List<string> _items;
private bool _itemsLoaded = false;

public List<string> Items
{
    get
    {
        if (!_itemsLoaded)
        {
            _items = VeritabanindanYukle();  // İlk erişimde yükle
            _itemsLoaded = true;
        }
        return _items;
    }
}
```

---

---

## Diyelim ki bilerek Kapsülleme uygulamadık?

### bir bankacılık uygulaması düşünelim;

```csharp
public class BankaHesabi
{
    public decimal Bakiye { get; set; }  // Herkes okuyup yazabilir!
    public string HesapSahibi { get; set; }
}

// Kullanım - FELAKET!
class Program
{
    static void Main()
    {
        BankaHesabi hesap = new BankaHesabi();
        hesap.HesapSahibi = "Ali";
        hesap.Bakiye = 1000;
        
        // Sorun 1: Negatif bakiye atanabiliyor
        hesap.Bakiye = -500;  // Bu mümkün olmamalı!
        
        // Sorun 2: Limit kontrolü yok
        hesap.Bakiye = 10000000;  // Limit aşımı kontrolü yok!
        
        // Sorun 3: İşlem logu tutulamıyor
        hesap.Bakiye = 0;  // Kim, ne zaman, nereden çekti?
        
        // Sorun 4: Herkes istediği gibi değiştirebiliyor
        hesap.Bakiye = hesap.Bakiye - 1000000;  // Bakiye negatif olabilir!
    }
}
```

### Field ve property sağlayarak kapsülleme yapalım;

```csharp
public class BankaHesabi
{
    // Field'lar private - gizli
    private decimal _bakiye;
    private string _hesapSahibi;
    private readonly decimal _maxLimit = 50000;
    private List<string> _islemGecmisi = new List<string>();
    
    // Property'ler kontrollü
    public decimal Bakiye
    {
        get { return _bakiye; }  // Sadece okuma izni (set yok!)
    }
    
    public string HesapSahibi
    {
        get { return _hesapSahibi; }
        private set { _hesapSahibi = value; }  // Sadece sınıf içinden set edilebilir
    }
    
    // Constructor ile güvenli başlangıç
    public BankaHesabi(string sahip, decimal ilkBakiye)
    {
        if (ilkBakiye < 0)
            throw new ArgumentException("İlk bakiye negatif olamaz!");
            
        _hesapSahibi = sahip;
        _bakiye = ilkBakiye;
        _islemGecmisi.Add($"Hesap açıldı: {ilkBakiye} TL");
    }
    
    // Kontrollü para yatırma
    public void ParaYatir(decimal miktar)
    {
        if (miktar <= 0)
        {
            Console.WriteLine("Hata: Yatırılacak miktar pozitif olmalı!");
            return;
        }
        
        if (_bakiye + miktar > _maxLimit)
        {
            Console.WriteLine($"Hata: Limit aşımı! Max limit: {_maxLimit} TL");
            return;
        }
        
        _bakiye += miktar;
        _islemGecmisi.Add($"+{miktar} TL yatırıldı. Yeni bakiye: {_bakiye} TL");
        Console.WriteLine($"Başarılı: {miktar} TL yatırıldı.");
    }
    
    // Kontrollü para çekme
    public void ParaCek(decimal miktar)
    {
        if (miktar <= 0)
        {
            Console.WriteLine("Hata: Çekilecek miktar pozitif olmalı!");
            return;
        }
        
        if (miktar > _bakiye)
        {
            Console.WriteLine($"Hata: Yetersiz bakiye! Bakiyeniz: {_bakiye} TL");
            return;
        }
        
        _bakiye -= miktar;
        _islemGecmisi.Add($"-{miktar} TL çekildi. Yeni bakiye: {_bakiye} TL");
        Console.WriteLine($"Başarılı: {miktar} TL çekildi.");
    }
    
    public void HesapOzetiGoster()
    {
        Console.WriteLine($"\nHesap Sahibi: {_hesapSahibi}");
        Console.WriteLine($"Güncel Bakiye: {_bakiye} TL");
        Console.WriteLine("İşlem Geçmişi:");
        foreach (var islem in _islemGecmisi)
        {
            Console.WriteLine($"  - {islem}");
        }
    }
}
```

---


---

## Kapsülleme için Örnek

### Örnek 1: Öğrenci Not Sistemi

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Ogrenci
{
    // Field - Kimse doğrudan bu veriye dokunamaz
    private int _not;
    private string _isim;
    private List<int> _notGecmisi = new List<int>();
    
    // Property - Veriye erişim kapısı
    public int Not
    {
        get { return _not; }
        set 
        { 
            // Kontrol katmanı: Mantıksız veri girişi engelleniyor
            if (value >= 0 && value <= 100)
            {
                _not = value;
                _notGecmisi.Add(value);
                Console.WriteLine($"Not güncellendi: {value}");
                
                // Harf notunu otomatik hesapla
                if (value >= 90)
                    Console.WriteLine("Harf Notu: AA");
                else if (value >= 80)
                    Console.WriteLine("Harf Notu: BB");
                else if (value >= 70)
                    Console.WriteLine("Harf Notu: CC");
                else if (value >= 60)
                    Console.WriteLine("Harf Notu: DD");
                else
                    Console.WriteLine("Harf Notu: FF (Kaldı!)");
            }
            else
            {
                Console.WriteLine($"Hata: Not 0-100 arasında olmalı! Girilen: {value}");
            }
        }
    }
    
    public string Isim
    {
        get { return _isim; }
        set
        {
            if (!string.IsNullOrWhiteSpace(value) && value.Length >= 2)
                _isim = value;
            else
                Console.WriteLine("Hata: İsim en az 2 karakter olmalı!");
        }
    }
    
    public double Ortalama
    {
        get 
        { 
            // Hesaplanmış property - sadece okunabilir
            if (_notGecmisi.Count == 0) return 0;
            return _notGecmisi.Average();
        }
    }
}

class Program
{
    static void Main()
    {
        Console.WriteLine("=== Öğrenci Not Sistemi ===\n");
        
        Ogrenci ogr = new Ogrenci();
        
        // İsim girişi
        Console.WriteLine("İsim denemesi:");
        ogr.Isim = "Ayşe";
        Console.WriteLine($"İsim başarıyla ayarlandı: {ogr.Isim}\n");
        
        // Not girişleri
        Console.WriteLine("Not girişleri:");
        ogr.Not = 85;   // Geçerli
        Console.WriteLine();
        
        ogr.Not = 150;  // Hata mesajı
        Console.WriteLine();
        
        ogr.Not = -20;  // Hata mesajı
        Console.WriteLine();
        
        ogr.Not = 75;   // Geçerli
        Console.WriteLine();
        
        // Ortalama göster
        Console.WriteLine($"Toplam not ortalaması: {ogr.Ortalama:F2}");
        
        Console.WriteLine("\nProgramı kapatmak için bir tuşa basın...");
        Console.ReadKey();
    }
}
```

---

### Örnek 2: Kullanıcı Şifre Yönetimi

```csharp
using System;
using System.Linq;  // Any() metodu için gerekli

/// <summary>
/// Kullanıcı yönetimi sınıfı - Güvenli giriş sistemi
/// </summary>
public class Kullanici
{
    // ============= PRIVATE FIELDS (Gizli Alanlar) =============
    private string _kullaniciAdi;
    private string _sifreHash;  // Hash'lenmiş şifre (düz metin tutulmaz!)
    private int _basarisizGirisSayisi;
    private DateTime _hesapKilitlenmeZamani;
    private bool _hesapAktif;
    
    // ============= PUBLIC PROPERTIES (Dışarıya Açık Özellikler) =============
    
    /// <summary>
    /// Kullanıcı adı property'si - Kontrollü erişim sağlar
    /// </summary>
    public string KullaniciAdi
    {
        get { return _kullaniciAdi; }
        set
        {
            // Kullanıcı adı boş mu kontrol et
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("Kullanıcı adı boş olamaz!");
            
            // Kullanıcı adı uzunluk kontrolü (3-20 karakter)
            if (value.Length < 3 || value.Length > 20)
                throw new ArgumentException("Kullanıcı adı 3-20 karakter olmalı!");
            
            _kullaniciAdi = value;
        }
    }
    
    /// <summary>
    /// Hesabın kilitli olup olmadığını kontrol eden property
    /// Kilit süresi dolduysa otomatik olarak hesabı açar
    /// </summary>
    public bool HesapKilitliMi
    {
        get 
        { 
            // Kilit süresi dolduysa otomatik aç
            if (_hesapAktif == false && DateTime.Now > _hesapKilitlenmeZamani)
            {
                _hesapAktif = true;
                _basarisizGirisSayisi = 0;
                Console.WriteLine("✓ Hesap kilidi otomatik açıldı.");
            }
            return !_hesapAktif;  // true ise kilitli, false ise açık
        }
    }
    
    // ============= CONSTRUCTOR (Yapıcı Metot) =============
    
    /// <summary>
    /// Yeni bir kullanıcı oluşturur
    /// </summary>
    /// <param name="kullaniciAdi">Kullanıcının adı</param>
    /// <param name="sifre">Kullanıcının şifresi</param>
    public Kullanici(string kullaniciAdi, string sifre)
    {
        KullaniciAdi = kullaniciAdi;  // Property üzerinden set et (kontroller çalışır)
        SifreBelirle(sifre);          // Şifreyi hash'le ve kaydet
        _hesapAktif = true;           // Hesap başlangıçta aktif
        _basarisizGirisSayisi = 0;    // Başarısız giriş sayısı sıfır
    }
    
    // ============= PUBLIC METHODS (Dışarıya Açık Metotlar) =============
    
    /// <summary>
    /// Kullanıcının şifresini değiştirir (hash'leyerek güvenli şekilde saklar)
    /// </summary>
    /// <param name="yeniSifre">Yeni şifre</param>
    public void SifreBelirle(string yeniSifre)
    {
        // Şifre boş mu kontrol et
        if (string.IsNullOrWhiteSpace(yeniSifre))
            throw new ArgumentException("Şifre boş olamaz!");
        
        // Şifre uzunluk kontrolü (en az 6 karakter)
        if (yeniSifre.Length < 6)
            throw new ArgumentException("Şifre en az 6 karakter olmalı!");
        
        // Şifre karmaşıklık kontrolü (büyük harf ve rakam)
        bool hasUpper = yeniSifre.Any(char.IsUpper);  // En az bir büyük harf var mı?
        bool hasDigit = yeniSifre.Any(char.IsDigit);  // En az bir rakam var mı?
        
        if (!hasUpper || !hasDigit)
            throw new ArgumentException("Şifre en az bir büyük harf ve bir rakam içermeli!");
        
        // Şifreyi hash'le ve kaydet (gerçek uygulamada SHA256 kullanılır)
        _sifreHash = Hashle(yeniSifre);
        Console.WriteLine("✓ Şifre başarıyla değiştirildi.");
    }
    
    /// <summary>
    /// Kullanıcı giriş işlemini gerçekleştirir
    /// </summary>
    /// <param name="sifre">Girilen şifre</param>
    /// <returns>Giriş başarılı mı?</returns>
    public bool GirisYap(string sifre)
    {
        // Önce hesap kilitli mi kontrol et
        if (HesapKilitliMi)
        {
            Console.WriteLine(" Hesabınız geçici olarak kilitlenmiştir.");
            return false;
        }
        
        // Girilen şifre ile kayıtlı hash'i karşılaştır
        if (_sifreHash == Hashle(sifre))
        {
            // Başarılı giriş: başarısız sayısını sıfırla
            _basarisizGirisSayisi = 0;
            Console.WriteLine($" Hoş geldiniz, {_kullaniciAdi}!");
            return true;
        }
        else
        {
            // Başarısız giriş: sayacı artır
            _basarisizGirisSayisi++;
            Console.WriteLine($" Hatalı şifre! ({_basarisizGirisSayisi}/3)");
            
            // 3 başarısız denemeden sonra hesabı kilitle
            if (_basarisizGirisSayisi >= 3)
            {
                _hesapAktif = false;
                _hesapKilitlenmeZamani = DateTime.Now.AddMinutes(15);  // 15 dakika kilitli
                Console.WriteLine(" 3 başarısız giriş nedeniyle hesap 15 dakika kilitlenmiştir.");
            }
            return false;
        }
    }
    
    // ============= PRIVATE METHODS (Gizli Yardımcı Metotlar) =============
    
    /// <summary>
    /// Metni hash'ler (güvenlik için - gerçekte SHA256 kullanılmalı)
    /// </summary>
    /// <param name="metin">Hash'lenecek metin</param>
    /// <returns>Hash'lenmiş metin</returns>
    private string Hashle(string metin)
    {
        // Basit hash simülasyonu (gerçek uygulamada SHA256 kullanılır)
        // Bu metot şifreyi Base64 formatında döndürür
        return Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(metin));
    }
}

/// <summary>
/// Programın ana sınıfı - Kullanıcı giriş sistemini test eder
/// </summary>
class Program
{
    static void Main()
    {
        Console.WriteLine("===== GÜVENLİ KULLANICI GİRİŞ SİSTEMİ =====\n");
        
        // Örnek 1: Başarısız giriş denemeleri ve hesap kilidi
        Console.WriteLine("--- ÖRNEK 1: Başarısız Giriş Denemeleri ---");
        try
        {
            // Yeni kullanıcı oluştur (kullanıcı adı: ahmet123, şifre: Sifre123)
            Console.WriteLine("Yeni kullanıcı oluşturuluyor...");
            Kullanici kullanici = new Kullanici("ahmet123", "Sifre123");
            Console.WriteLine("Kullanıcı başarıyla oluşturuldu!\n");
            
            // 1. başarısız giriş denemesi
            Console.WriteLine("1. Giriş denemesi:");
            kullanici.GirisYap("yanlis");
            Console.WriteLine();
            
            // 2. başarısız giriş denemesi
            Console.WriteLine("2. Giriş denemesi:");
            kullanici.GirisYap("yanlis2");
            Console.WriteLine();
            
            // 3. başarısız giriş denemesi (hesap kilitlenecek)
            Console.WriteLine("3. Giriş denemesi:");
            kullanici.GirisYap("yanlis3");
            Console.WriteLine();
            
            // 4. deneme - hesap kilitli olduğu için başarısız olacak
            Console.WriteLine("4. Giriş denemesi (hesap kilitli):");
            kullanici.GirisYap("Sifre123");
            Console.WriteLine();
            
            Console.WriteLine("Not: 15 dakika sonra hesap otomatik açılacaktır.\n");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Hata: {ex.Message}");
        }
        
        // Örnek 2: Başarılı giriş senaryosu
        Console.WriteLine("\n--- ÖRNEK 2: Başarılı Giriş ---");
        try
        {
            Kullanici kullanici2 = new Kullanici("mehmet_demir", "GucluSifre99");
            Console.WriteLine("Kullanıcı başarıyla oluşturuldu!\n");
            
            // Doğru şifre ile giriş
            bool sonuc = kullanici2.GirisYap("GucluSifre99");
            
            if (sonuc)
                Console.WriteLine("\n✓ Giriş işlemi başarıyla tamamlandı!");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Hata: {ex.Message}");
        }
        
        // Örnek 3: Geçersiz kullanıcı adı oluşturma (hata fırlatır)
        Console.WriteLine("\n--- ÖRNEK 3: Hata Yakalama (Geçersiz Kullanıcı Adı) ---");
        try
        {
            Console.WriteLine("Geçersiz kullanıcı adı deneniyor: 'a'");
            Kullanici hataliKullanici = new Kullanici("a", "Sifre123");
            // Bu satıra asla ulaşılamaz çünkü üst satır hata fırlatır
        }
        catch (ArgumentException ex)
        {
            Console.WriteLine($"✓ Hata başarıyla yakalandı: {ex.Message}");
        }
        
        // Örnek 4: Geçersiz şifre oluşturma
        Console.WriteLine("\n--- ÖRNEK 4: Hata Yakalama (Geçersiz Şifre) ---");
        try
        {
            Console.WriteLine("Geçersiz şifre deneniyor: '123' (çok kısa)");
            Kullanici hataliSifre = new Kullanici("deneme", "123");
        }
        catch (ArgumentException ex)
        {
            Console.WriteLine($"✓ Hata başarıyla yakalandı: {ex.Message}");
        }
        
        Console.WriteLine("\n===== PROGRAM SONU =====");
        Console.WriteLine("\nProgramı kapatmak için bir tuşa basın...");
        Console.ReadKey();
    }
}
```

---

### Örnek 3: Sıcaklık Kontrol Sistemi

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Termometre
{
    // Field'lar (private - dışarıdan doğrudan erişilemez)
    private double _sicaklik;
    private double _minSicaklik = -20;      // Termometrenin ölçebileceği minimum deger
    private double _maxSicaklik = 50;       // Termometrenin ölçebileceği maksimum deger
    private List<double> _sicaklikGecmisi = new List<double>();  // Tum olcumleri saklar
    
    // Property - Sicaklik degerini kontrollu olarak alip set eder
    public double Sicaklik
    {
        get { return _sicaklik; }
        set
        {
            double yeniDeger = value;
            
            // Girilen deger minimum limitin altinda mi?
            if (yeniDeger < _minSicaklik)
            {
                Console.WriteLine($"UYARI: Sicaklik cok dusuk! ({yeniDeger}C) Min: {_minSicaklik}C");
                yeniDeger = _minSicaklik;   // Degeri minimuma cek
            }
            // Girilen deger maksimum limitin ustunde mi?
            else if (yeniDeger > _maxSicaklik)
            {
                Console.WriteLine($"UYARI: Sicaklik cok yuksek! ({yeniDeger}C) Max: {_maxSicaklik}C");
                yeniDeger = _maxSicaklik;   // Degeri maksimuma cek
            }
            
            _sicaklik = yeniDeger;
            _sicaklikGecmisi.Add(yeniDeger);   // Gecmise ekle
            
            // Kritik sicaklik degerleri icin anlik uyari
            if (yeniDeger < 0)
                Console.WriteLine("DONMA RISKI!");
            else if (yeniDeger > 35)
                Console.WriteLine("ASIRI SICAK!");
        }
    }
    
    // Hesaplanmis property - Tum olcumlerin ortalamasini dondurur
    public double OrtalamaSicaklik
    {
        get
        {
            if (_sicaklikGecmisi.Count == 0) return 0;
            return _sicaklikGecmisi.Average();
        }
    }
    
    // Gecmisteki en yuksek sicaklik
    public double MaxSicaklik
    {
        get { return _sicaklikGecmisi.Max(); }
    }
    
    // Gecmisteki en dusuk sicaklik
    public double MinSicaklik
    {
        get { return _sicaklikGecmisi.Min(); }
    }
    
    // Mevcut sicakliga gore durum metni dondurur
    public string Durum
    {
        get
        {
            if (_sicaklik < 0) return "DONMA";
            if (_sicaklik < 10) return "COK SOGUK";
            if (_sicaklik < 20) return "SOGUK";
            if (_sicaklik < 30) return "NORMAL";
            if (_sicaklik < 40) return "SICAK";
            return "ASIRI SICAK";
        }
    }
}

// Programin ana sinifi
class Program
{
    static void Main()
    {
        // Termometre nesnesi olustur
        Termometre t = new Termometre();
        
        // Normal sicaklik girisi
        Console.WriteLine("1. Olcum: 25 C");
        t.Sicaklik = 25;
        Console.WriteLine($"   Durum: {t.Durum}\n");
        
        // Donma riski olan sicaklik girisi
        Console.WriteLine("2. Olcum: -5 C");
        t.Sicaklik = -5;
        Console.WriteLine($"   Durum: {t.Durum}\n");
        
        // Asiri sicak girisi (limitin ustunde)
        Console.WriteLine("3. Olcum: 45 C");
        t.Sicaklik = 45;
        Console.WriteLine($"   Durum: {t.Durum}\n");
        
        // Istatistikleri goster
        Console.WriteLine("=== OLCUM ISTATISTIKLERI ===");
        Console.WriteLine($"Ortalama Sicaklik: {t.OrtalamaSicaklik:F2} C");
        Console.WriteLine($"En Yuksek Sicaklik: {t.MaxSicaklik} C");
        Console.WriteLine($"En Dusuk Sicaklik: {t.MinSicaklik} C");
        Console.WriteLine($"Mevcut Durum: {t.Durum}");
        
        // Programin kapanmasini engelle (konsol hemen kapanmasin diye)
        Console.WriteLine("\nProgrami kapatmak icin bir tusa basin...");
        Console.ReadKey();
    }
}
```

---

### Örnek 4: Stok Yönetim Sistemi

```csharp
using System;

public class Urun
{
    // Field'lar - Dışarıdan doğrudan erişilemez
    private string _urunKodu;
    private string _ad;
    private int _stokMiktari;
    private decimal _fiyat;
    private int _minimumStokSeviyesi = 10;
    
    // UrunKodu property - Sadece constructor'dan set edilebilir (private set)
    public string UrunKodu
    {
        get { return _urunKodu; }
        private set  // Sadece sinif icinden set edilebilir
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("Urun kodu bos olamaz!");
            
            if (value.Length != 8)
                throw new ArgumentException("Urun kodu 8 karakter olmali!");
            
            _urunKodu = value.ToUpper();
        }
    }
    
    // Urun adi property
    public string Ad
    {
        get { return _ad; }
        set
        {
            if (string.IsNullOrWhiteSpace(value))
                throw new ArgumentException("Urun adi bos olamaz!");
            
            _ad = value;
        }
    }
    
    // Stok miktari property - Private set ile disaridan dogrudan degistirilemez
    public int StokMiktari
    {
        get { return _stokMiktari; }
        private set  // Disaridan direkt set edilemez, sadece sinif icinden
        {
            _stokMiktari = value;
            
            // Stok kritik seviye kontrolleri
            if (_stokMiktari <= 0)
                Console.WriteLine($"KRITIK: {_ad} urunu TUKENDI!");
            else if (_stokMiktari < _minimumStokSeviyesi)
                Console.WriteLine($"UYARI: {_ad} stok kritik seviyede! ({_stokMiktari} adet)");
        }
    }
    
    // Fiyat property
    public decimal Fiyat
    {
        get { return _fiyat; }
        set
        {
            if (value < 0)
                throw new ArgumentException("Fiyat negatif olamaz!");
            
            if (value > 10000)
                Console.WriteLine($"DIKKAT: {_ad} fiyati cok yuksek! ({value:C})");
            
            _fiyat = value;
        }
    }
    
    // Sadece okunabilir property - Stokta urun var mi?
    public bool StoktaVarMi
    {
        get { return _stokMiktari > 0; }
    }
    
    // Constructor (Yapici Metot) - Urun nesnesi olusturulurken calisir
    public Urun(string kod, string ad, decimal fiyat, int stok)
    {
        UrunKodu = kod;     // Private set ile calisir
        Ad = ad;
        Fiyat = fiyat;
        StokMiktari = stok; // Private set ile calisir
    }
    
    // Stok ekleme metodu
    public void StokEkle(int miktar)
    {
        if (miktar <= 0)
        {
            Console.WriteLine("Hata: Eklenecek stok pozitif olmali!");
            return;
        }
        
        StokMiktari += miktar;  // Private set tetiklenir
        Console.WriteLine($"{miktar} adet {_ad} eklendi. Yeni stok: {_stokMiktari}");
    }
    
    // Stok azaltma metodu (satis yapma)
    public bool StokAzalt(int miktar)
    {
        if (miktar <= 0)
        {
            Console.WriteLine("Hata: Azaltilacak stok pozitif olmali!");
            return false;
        }
        
        if (miktar > _stokMiktari)
        {
            Console.WriteLine($"Hata: Yetersiz stok! Mevcut: {_stokMiktari}, Istenen: {miktar}");
            return false;
        }
        
        StokMiktari -= miktar;  // Private set tetiklenir
        Console.WriteLine($"{miktar} adet {_ad} satildi. Kalan stok: {_stokMiktari}");
        return true;
    }
    
    // Urun bilgilerini ekrana yazdirir
    public void BilgiGoster()
    {
        Console.WriteLine($"\n=== Urun Bilgisi ===");
        Console.WriteLine($"Kod: {_urunKodu}");
        Console.WriteLine($"Ad: {_ad}");
        Console.WriteLine($"Fiyat: {_fiyat:C}");
        Console.WriteLine($"Stok: {_stokMiktari} adet");
        Console.WriteLine($"Durum: {(StoktaVarMi ? "Stokta var" : "Stokta yok")}");
    }
}

// Programin ana sinifi
class Program
{
    static void Main()
    {
        Console.WriteLine("=== URUN STOK YONETIM SISTEMI ===\n");
        
        // Yeni bir urun olustur
        // Kod 8 karakter olmali, fiyat yuksek oldugu icin uyari verecek
        Console.WriteLine("Yeni urun olusturuluyor: Laptop");
        Urun urun = new Urun("ABC12345", "Laptop", 15000m, 25);
        
        // Urun bilgilerini goster
        urun.BilgiGoster();
        
        Console.WriteLine("\n--- Stok Islemi: Satis ---");
        // Basarili satis (stok yeterli)
        urun.StokAzalt(10);
        
        Console.WriteLine("\n--- Stok Islemi: Yetersiz Stok ---");
        // Yetersiz stok hatasi (20 adet satilmak isteniyor ama 15 kaldi)
        urun.StokAzalt(20);
        
        Console.WriteLine("\n--- Stok Islemi: Stok Ekleme ---");
        // Stok ekleme
        urun.StokEkle(5);
        
        Console.WriteLine("\n--- Son Durum ---");
        urun.BilgiGoster();
        
        // Asagidaki satir hata verir (private set oldugu icin)
        // urun.StokMiktari = 100;
        Console.WriteLine("\nNot: StokMiktari property'si private set oldugu icin");
        Console.WriteLine("disaridan direkt olarak 'urun.StokMiktari = 100' yapilamaz.");
        Console.WriteLine("Sadece StokEkle() veya StokAzalt() metotlari ile degistirilebilir.");
        
        // Farkli urun ornegi - Kritik stok uyarisi alacak
        Console.WriteLine("\n\n=== Ikinci Urun Ornegi (Kritik Stok) ===");
        Urun urun2 = new Urun("XYZ98765", "Mouse", 250m, 5);
        urun2.BilgiGoster();
        
        // Stok tuketme ornegi
        Console.WriteLine("\n--- Stok Tuketme Testi ---");
        urun2.StokAzalt(3);  // Kalan: 2 adet (kritik stok uyarisi verecek)
        urun2.StokAzalt(2);  // Kalan: 0 adet (tukendi uyarisi verecek)
        urun2.StokAzalt(1);  // Yetersiz stok hatasi verecek
        
        Console.WriteLine("\nProgrami kapatmak icin bir tusa basin...");
        Console.ReadKey();
    }
}
```

---


## Kapsülleme ile Sağlanan Kazanımlar

| Kazanım | Açıklama | Örnek |
|---------|----------|-------|
| **Veri Bütünlüğü** | Geçersiz değerler engellenir | Not 0-100 arası |
| **Güvenlik** | Hassas veriler gizlenir | Şifre hash'leme |
| **Kontrol** | Her değişiklik loglanabilir | İşlem geçmişi |
| **Esneklik** | İç yapı değişse de dış aynı | Fiyat hesaplama değişimi |
| **Bakım** | Hata ayıklama kolaylaşır | Breakpoint koyma |
| **SoY (Separation of Concerns)** | İş kuralları tek yerde | Stok kontrolü |

---
