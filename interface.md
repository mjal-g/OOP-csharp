# Interface (Arayüz) – C# ile Detaylı Konu Anlatımı

Interface, C# programlamanın en güçlü ve en çok kullanılan özelliklerinden biridir.

---

## 1. Interface Nedir? (Temel Tanım)

**Interface**, bir sınıfın **yapmak zorunda olduğu şeyleri** tanımlayan bir sözleşmedir (contract).

- İçinde **metot, property, event, indexer** imzaları bulunur
- **Gövdesi olmaz** (C# 8.0 öncesi, sonrasında default implementasyon gelebilir)
- Bir sınıf bir interface'i **implement** ettiğinde, interface'deki tüm üyeleri **kendi içinde doldurmak zorundadır**

### Benzetme:
Bir iş ilanı düşünün:
> "Java bilen, İngilizce konuşan, takım çalışması yapabilen kişi aranıyor"

Bu bir **interface**dir. Başvuran kişi (sınıf), bu şartların **hepsini** karşılamak zorundadır.

---

## 2. Interface Sözdizimi (Syntax)

```csharp
// Interface tanımlama
interface ICalisan
{
    // Property (gövdesiz)
    string Ad { get; set; }
    
    // Metot (gövdesiz)
    void Calis();
    
    // Sadece get olan property
    int Id { get; }
    
    // Event
    event EventHandler IsBitti;
    
    // Indexer (isteğe bağlı)
    string this[int index] { get; set; }
}

// Interface'i implement eden sınıf
class Yazilimci : ICalisan
{
    // Tüm üyeleri implement etmek ZORUNDAYIM
    public string Ad { get; set; }
    public int Id { get; private set; }
    public event EventHandler IsBitti;
    
    public string this[int index]
    {
        get => // implementasyon
        set => // implementasyon
    }
    
    public void Calis()
    {
        Console.WriteLine($"{Ad} yazılım geliştiriyor");
        IsBitti?.Invoke(this, EventArgs.Empty);
    }
}
```

### Kritik Kurallar:
- Interface isimleri genellikle **I** harfi ile başlar (`ICalisan`, `IRepository`)
- Interface üyeleri **public** olarak bildirilmez (zaten hepsi public'tir)
- Interface'de **field** (değişken) tanımlanamaz
- Interface'de **constructor** olmaz

---

## 3. Interface vs Abstract Class (Çok Önemli!)

| Özellik | Interface | Abstract Class |
|---------|-----------|----------------|
| **Çoklu kalıtım** | ✅ Bir sınıf birden fazla interface implement edebilir | ❌ Bir sınıf sadece bir abstract class'tan türeyebilir |
| **Gövde** | C# 8.0 öncesi gövde olmaz. Sonrasında default implementasyon olabilir | Gövdeli metotlar olabilir |
| **Field** | ❌ Field tanımlanamaz | ✅ Field tanımlanabilir |
| **Constructor** | ❌ Constructor olmaz | ✅ Constructor olabilir |
| **Erişim belirteçleri** | Tüm üyeler implicit public | Her türlü erişim belirteci kullanılabilir |
| **State (durum)** | ❌ Durum tutamaz (field yok) | ✅ Durum tutabilir |
| **Ne zaman kullanılır** | "Yapabilme" yetenekleri tanımlarken | "Bir türüdür" (is-a) ilişkisi kurarken |

### Örnekle Farkı Görelim:

```csharp
// Abstract Class: "Bir tür çalandır" (is-a)
abstract class Calisan
{
    public string Ad { get; set; }  // Field benzeri property tutabilir
    public abstract void Calis();    // Soyut metot
    public void MolaVer() { }        // Gövdeli metot (ortak davranış)
}

// Interface: "Yapabilme" yeteneği (can-do)
interface IYuzebilir
{
    void Yuz();  // Sadece imza
}

interface IUcabilir
{
    void Uc();
}

// Bir sınıf hem abstract'tan türeyip hem de interface'leri implement edebilir
class UcanBalik : Calisan, IYuzebilir, IUcabilir
{
    public override void Calis() { }
    public void Yuz() { }
    public void Uc() { }
}
```

---

## 4. Çoklu Interface Implementasyonu (Multiple Inheritance)

C# **çoklu kalıtımı** (bir sınıfın birden fazla sınıftan türemesini) desteklemez, ancak **bir sınıf birden fazla interface implement edebilir**.

```csharp
interface IYazdirilabilir
{
    void Yazdir();
}

interface IKopyalanabilir
{
    void Kopyala();
}

interface ITaranabilir
{
    void Tara();
}

// Bir sınıf istediği kadar interface implement edebilir
class CokluFonksiyonluYazici : IYazdirilabilir, IKopyalanabilir, ITaranabilir
{
    public void Yazdir()
    {
        Console.WriteLine("Yazdırılıyor...");
    }
    
    public void Kopyala()
    {
        Console.WriteLine("Kopyalanıyor...");
    }
    
    public void Tara()
    {
        Console.WriteLine("Taranıyor...");
    }
}

// Kullanım
CokluFonksiyonluYazici yazici = new CokluFonksiyonluYazici();
yazici.Yazdir();
yazici.Kopyala();
yazici.Tara();

// Interface referansları ile polymorphism
IYazdirilabilir yaz = yazici;
yaz.Yazdir();  // Sadece Yazdir() erişilebilir

ITaranabilir tar = yazici;
tar.Tara();    // Sadece Tara() erişilebilir
```

---

## 5. Interface Inheritance (Interface'lerin Kalıtımı)

Interface'ler birbirinden türeyebilir. Bu durumda alt interface, üst interface'teki tüm üyeleri de içerir.

```csharp
interface IHayvan
{
    void Beslen();
}

interface IMemeli : IHayvan
{
    void DogumYap();
}

interface IKopek : IMemeli
{
    void Havla();
}

// KopekImpl sınıfı, tüm interface'lerin üyelerini implement etmek zorunda
class KopekImpl : IKopek
{
    public void Beslen()
    {
        Console.WriteLine("Köpek maması yer");
    }
    
    public void DogumYap()
    {
        Console.WriteLine("Yavru doğurur");
    }
    
    public void Havla()
    {
        Console.WriteLine("Hav hav!");
    }
}

// Kullanım
IKopek kopek = new KopekImpl();
kopek.Beslen();   // Çalışır
kopek.DogumYap(); // Çalışır
kopek.Havla();    // Çalışır

IHayvan hayvan = kopek;
hayvan.Beslen();  // Sadece Beslen() erişilebilir
```

---

## 6. Explicit Interface Implementation (Açık Arayüz Implementasyonu)

Aynı imzaya sahip iki farklı interface implement edildiğinde veya interface üyesini gizlemek istediğimizde kullanılır.

```csharp
interface IBird
{
    void Fly();
}

interface IInsect
{
    void Fly();
}

class Mutant : IBird, IInsect
{
    // AÇIK implementasyon - sadece interface referansıyla erişilebilir
    void IBird.Fly()
    {
        Console.WriteLine("Kuş gibi uçar");
    }
    
    void IInsect.Fly()
    {
        Console.WriteLine("Böcek gibi uçar");
    }
    
    // Normal metot (opsiyonel)
    public void Fly()
    {
        Console.WriteLine("Normal uçar");
    }
}

// Kullanım
Mutant m = new Mutant();
m.Fly();  // "Normal uçar"

IBird bird = m;
bird.Fly();  // "Kuş gibi uçar"

IInsect insect = m;
insect.Fly();  // "Böcek gibi uçar"
```

### Neden Explicit Implementation kullanılır?
1. **İsim çakışmalarını çözmek** (aynı metot imzası)
2. **Interface üyesini gizlemek** (normal kullanımda görünmesin)
3. **Daha spesifik implementasyon sağlamak**

---

## 7. Default Interface Methods (C# 8.0+)

C# 8.0 ile birlikte interface'lerde **gövdeli metotlar** tanımlanabilir. Bu, geriye dönük uyumluluk için kullanışlıdır.

```csharp
interface ILogger
{
    // Normal interface metodu (gövdesiz)
    void Log(string message);
    
    // Default implementasyon (gövdeli) - C# 8.0+
    void LogError(string error)
    {
        Console.WriteLine($"ERROR: {error}");
        Log(error);  // Diğer metodu çağırabilirim
    }
    
    // Static metod (C# 8.0+)
    static void LogInfo(string info)
    {
        Console.WriteLine($"INFO: {info}");
    }
    
    // Static field (C# 8.0+)
    static readonly string Version = "1.0";
}

class ConsoleLogger : ILogger
{
    // Sadece Log() implement etmek zorundayım
    public void Log(string message)
    {
        Console.WriteLine($"LOG: {message}");
    }
    
    // LogError()'u override etmek zorunda DEĞİLİM (default kullanılır)
    // Ama istersem override edebilirim:
    public void LogError(string error)
    {
        Console.WriteLine($"!!! {error} !!!");
    }
}

// Kullanım
ILogger logger = new ConsoleLogger();
logger.Log("Test");           // LOG: Test
logger.LogError("Hata!");     // !!! Hata! !!! (override edildi)
ILogger.LogInfo("Başlangıç"); // INFO: Başlangıç (static)
```

---

## 8. Gerçek Dünya Örneği: Repository Pattern

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

// Generic Interface
interface IRepository<T> where T : class
{
    // CRUD operasyonları
    T GetById(int id);
    IEnumerable<T> GetAll();
    void Add(T entity);
    void Update(T entity);
    void Delete(int id);
    
    // Default metod (C# 8.0)
    bool Exists(int id)
    {
        return GetById(id) != null;
    }
}

// Entity sınıfı
class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Email { get; set; }
}

// Concrete implementasyon
class CustomerRepository : IRepository<Customer>
{
    private List<Customer> _customers = new List<Customer>();
    private int _nextId = 1;
    
    public Customer GetById(int id)
    {
        return _customers.FirstOrDefault(c => c.Id == id);
    }
    
    public IEnumerable<Customer> GetAll()
    {
        return _customers.ToList();
    }
    
    public void Add(Customer entity)
    {
        entity.Id = _nextId++;
        _customers.Add(entity);
    }
    
    public void Update(Customer entity)
    {
        var existing = GetById(entity.Id);
        if (existing != null)
        {
            existing.Name = entity.Name;
            existing.Email = entity.Email;
        }
    }
    
    public void Delete(int id)
    {
        var customer = GetById(id);
        if (customer != null)
        {
            _customers.Remove(customer);
        }
    }
}

// Servis katmanı - Interface kullanımı
class CustomerService
{
    private readonly IRepository<Customer> _repository;
    
    // Dependency Injection
    public CustomerService(IRepository<Customer> repository)
    {
        _repository = repository;
    }
    
    public void AddCustomer(string name, string email)
    {
        var customer = new Customer { Name = name, Email = email };
        _repository.Add(customer);
        Console.WriteLine($"Müşteri eklendi: {name}");
    }
    
    public void ShowAllCustomers()
    {
        Console.WriteLine("\n--- Tüm Müşteriler ---");
        foreach (var customer in _repository.GetAll())
        {
            Console.WriteLine($"ID: {customer.Id}, İsim: {customer.Name}, Email: {customer.Email}");
        }
    }
}

// Kullanım
class Program
{
    static void Main()
    {
        // Interface ile bağımlılığı düşük tutuyoruz
        IRepository<Customer> repo = new CustomerRepository();
        CustomerService service = new CustomerService(repo);
        
        service.AddCustomer("Ahmet Yılmaz", "ahmet@example.com");
        service.AddCustomer("Ayşe Demir", "ayse@example.com");
        service.ShowAllCustomers();
    }
}
```

---

## 9. Interface ile Polymorphism İlişkisi (Özet)

```csharp
// Interface tanımı
interface IShape
{
    double Area { get; }
    void Draw();
}

// Farklı implementasyonlar (polymorphism)
class Circle : IShape
{
    public double Radius { get; set; }
    public double Area => Math.PI * Radius * Radius;
    public void Draw() => Console.WriteLine($"Çember çiziliyor, Alan: {Area:F2}");
}

class Rectangle : IShape
{
    public double Width { get; set; }
    public double Height { get; set; }
    public double Area => Width * Height;
    public void Draw() => Console.WriteLine($"Dikdörtgen çiziliyor, Alan: {Area:F2}");
}

class Triangle : IShape
{
    public double Base { get; set; }
    public double Height { get; set; }
    public double Area => 0.5 * Base * Height;
    public void Draw() => Console.WriteLine($"Üçgen çiziliyor, Alan: {Area:F2}");
}

// Polymorphism sayesinde:
class DrawingApp
{
    public void DrawAllShapes(List<IShape> shapes)
    {
        foreach (var shape in shapes)
        {
            shape.Draw();  // Her shape kendi Draw() metodunu çalıştırır
        }
    }
}
```

---

## 10. Pratik Kurallar ve Best Practices

### ✅ DO (Yapılması Gerekenler)

```csharp
// 1. Interface isimlendirmesinde "I" öneki kullan
interface IUserService { }

// 2. Küçük ve odaklı interface'ler yap (Interface Segregation Principle)
interface IEmailService
{
    void SendEmail(string to, string body);
}

interface ILogService
{
    void Log(string message);
}

// 3. Interface'i implement eden sınıfların tüm üyeleri doldurduğundan emin ol
class MyService : IUserService, IEmailService
{
    // Tüm üyeler implement edilmeli
    public void SendEmail(string to, string body) { }
}

// 4. Dependency Injection için interface kullan
public class OrderController
{
    private readonly IOrderService _orderService;  // Interface bağımlılığı
    
    public OrderController(IOrderService orderService)
    {
        _orderService = orderService;
    }
}
```

### ❌ DON'T (Yapılmaması Gerekenler)

```csharp
// 1. Çok büyük interface'ler yapma (Fat Interface)
interface IMonsterInterface  // KÖTÜ!
{
    void Create();
    void Read();
    void Update();
    void Delete();
    void SendEmail();     // İlgisiz
    void Log();            // İlgisiz
    void Validate();       // İlgisiz
    void Calculate();      // İlgisiz
}

// 2. Interface'de field tanımlama (OLMAZ!)
interface IWrong
{
    int _id;  // HATA! Field olamaz
}

// 3. Interface'de erişim belirteci kullanma (GEREKSİZ)
interface IWrong2
{
    public void Metot();   // public yazmaya gerek yok (zaten public)
    private void Metot2(); // HATA! private olamaz
}

// 4. Gereksiz interface oluşturma
// Her class için interface oluşturmak zorunda değilsiniz
public class SimpleClass { }  // Bu kadar basitse interface'e gerek yok
```

---

## 11. Interface vs Delegate vs Abstract Class (Ne Zaman Ne Kullanılır?)

| Durum | Öneri | Örnek |
|-------|-------|-------|
| **Bir yetenek/kabiliyet tanımlıyorsanız** | Interface | `IDisposable`, `IComparable` |
| **"Bir türüdür" ilişkisi varsa** | Abstract Class | `Hayvan`, `Arac` |
| **Sadece tek bir metot davranışı tanımlıyorsanız** | Delegate/Action/Func | `Action<string>`, `Func<int,int>` |
| **State (durum) tutmanız gerekiyorsa** | Abstract Class | Field'ları olabilir |
| **Çoklu kalıtım ihtiyacı varsa** | Interface | Birden fazla interface implement edilebilir |
| **Geriye dönük uyumluluk sağlamak istiyorsanız (C# 8.0+)** | Interface (default metod) | Default implementasyon |

---

## 12. Özet: Interface'in Güçlü Yönleri

1. **Esneklik:** Farklı implementasyonlar aynı interface üzerinden kullanılabilir
2. **Bağımlılık Azaltma:** Concrete sınıflara değil, interface'lere bağımlı olunur (Dependency Inversion Principle)
3. **Test Edilebilirlik:** Mock objelerle birim test yapmak kolaylaşır
4. **Çoklu Kalıtım:** Bir sınıf birden fazla interface implement edebilir
5. **Sözleşme Garantisi:** Interface'i implement eden her sınıf belirli üyelere sahip olur

### Son Söz:

Interface, modern C# geliştirmenin temelidir. Dependency Injection, Unit Testing, Design Patterns (Strategy, Factory, Repository) gibi konuları öğrenmek için interface'i **çok iyi** anlamanız gerekir. 

**Unutmayın:** İyi bir yazılım mimarisi, somut implementasyonlara değil, abstraction'lara (interface/abstract class) bağımlıdır!