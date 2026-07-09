# Interview Questions .NET Mid-Senior

> [!NOTE]
> **Source of Truth**
>
> - Template pertanyaan: #[[file:.kiro/specs/interview-questions-dotnet-mid-senior/design.md]]
> - Master Index: #[[file:docs/00-master-index.md]]

---

## 1. Pendahuluan

### 1.1 Tujuan Dokumen

Dokumen ini berisi kumpulan pertanyaan interview untuk kandidat .NET Developer level Mid to Senior. Pertanyaan-pertanyaan ini dirancang untuk:

- Meng assessment pemahaman teknis kandidat secara konsisten dan objektif
- Membedakan ekspektasi jawaban antara level Mid dan Senior
- Memberikan panduan follow-up questions untuk menggali lebih dalam
- Mengidentifikasi red flags dan green flags dalam jawaban kandidat

### 1.2 Target Kandidat

| Level | Pengalaman | Karakteristik |
|---|---|---|
| **Mid** | 2-4 tahun | Mampu bekerja mandiri dengan guidance minimal, memahami konsep dasar dan bisa menjelaskan dengan contoh sederhana |
| **Senior** | 5+ tahun | Mampu lead technical decisions dan mentoring, memahami trade-off dan bisa memberikan contoh kompleks dari production |

### 1.3 Tech Stack yang Direlevankan

Pertanyaan di dokumen ini ditujukan untuk stack:

| Layer | Teknologi |
|---|---|---|
| Backend | .NET 8, C# 12 |
| Database | SQL Server 2022 |
| ORM | Entity Framework Core 8.0 |
| Frontend | ReactJS 18 |
| Testing | xUnit, NSubstitute, FluentAssertions |

### 1.4 Cara Penggunaan

1. Pilih pertanyaan sesuai level kandidat (Mid/Senior/Both)
2. Gunakan jawaban yang diharapkan sebagai panduan, bukan checklist kaku
3. Ajukan follow-up questions untuk menggali pemahaman lebih dalam
4. Perhatikan red flags dan green flags untuk assessment objektif
5. Gunakan scoring rubrik di akhir dokumen untuk evaluasi konsisten

---

## 2. C# & .NET Fundamentals

### Q-FUND-001: Value Type vs Reference Type

**Level:** Mid
**Topik:** Memory management, stack vs heap, value semantics

**Pertanyaan:**
Jelaskan perbedaan antara value type dan reference type di C#. Bagaimana perilaku keduanya saat di-assign ke variabel baru atau di-pass sebagai parameter?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan perbedaan fundamental dalam hal storage, copy behavior, dan method parameter passing.

1. **Perbedaan Storage**

   - **Value Type**: Disimpan di stack (untuk local variables), berisi data langsung
   - **Reference Type**: Disimpan di heap, variabel hanya menyimpan reference/pointer ke data
   - Value type: `int`, `double`, `bool`, `struct`, `enum`
   - Reference type: `class`, `string`, `array`, `delegate`, `interface`

   ```csharp
   // Value Type - Data disimpan langsung
   int a = 10;
   int b = a;  // b mendapat copy dari nilai a
   b = 20;     // a tetap 10, tidak terpengaruh

   // Reference Type - Variabel menyimpan reference
   int[] arr1 = new int[] { 1, 2, 3 };
   int[] arr2 = arr1;  // arr1 dan arr2 menunjuk ke object yang sama
   arr2[0] = 99;       // arr1[0] juga berubah menjadi 99
   ```

2. **Copy Behavior saat Assignment**

   - Value type: Shallow copy otomatis, nilai terpisah sepenuhnya
   - Reference type: Reference copy, kedua variabel menunjuk ke object yang sama
   - Perubahan melalui satu reference terlihat dari reference lain

3. **Parameter Passing**

   - **Pass by value** (default): Value type di-copy, reference type hanya reference-nya yang di-copy
   - **Pass by reference** (`ref`/`out`): Method mendapat akses ke variabel asli

   ```csharp
   void ModifyValue(int x)        // Pass by value
   {
       x = 100;  // Tidak mempengaruhi caller
   }

   void ModifyValueRef(ref int x) // Pass by reference
   {
       x = 100;  // Mempengaruhi variabel caller
   }

   void ModifyObject(List<int> list) // Reference type, pass by value
   {
       list.Add(1);  // Mempengaruhi object di caller
       list = new List<int>();  // Tidak mempengaruhi caller
   }
   ```

4. **String sebagai Special Case**

   - `string` adalah reference type tapi bersifat immutable
   - Setiap "modification" menciptakan string baru
   - Perilaku mirip value type dari perspektif assignment

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan perbedaan storage dan copy behavior | Menjelaskan boxing/unboxing, memory layout, GC implications |
| Contoh | Contoh sederhana assignment dan method parameter | Contoh dari production code, performance implications |

**Follow-up Questions:**
- "Apa yang terjadi saat value type di-cast ke object? Jelaskan boxing dan unboxing."
- "Kapan sebaiknya menggunakan struct vs class?"

**Red Flags:**
- ❌ Tidak bisa membedakan mana saja yang value type vs reference type
- ❌ Tidak memahami bahwa `string` adalah reference type
- ❌ Tidak bisa menjelaskan perilaku saat reference type di-pass ke method

**Green Flags:**
- ✅ Menyebutkan stack vs heap tanpa prompting
- ✅ Menjelaskan implikasi memory dan performance
- ✅ Memberikan contoh dari pengalaman debugging terkait reference semantics

---

### Q-FUND-002: Boxing dan Unboxing

**Level:** Mid
**Topik:** Performance, implicit/explicit conversion, type system

**Pertanyaan:**
Apa itu boxing dan unboxing di C#? Mengapa operasi ini berpotensi menimbulkan masalah performance dan bagaimana cara menghindarinya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan mekanisme boxing/unboxing dan implikasinya terhadap performance.

1. **Definisi Boxing dan Unboxing**

   - **Boxing**: Konversi implicit dari value type ke `object` atau interface type
   - Value type di-copy dari stack ke heap, dibungkus dalam object wrapper
   - **Unboxing**: Konversi explicit dari `object` kembali ke value type
   - Runtime memeriksa type compatibility sebelum extract value

   ```csharp
   int value = 42;

   // Boxing - implicit conversion ke object
   object boxed = value;  // Value di-copy ke heap

   // Unboxing - explicit conversion dari object
   int unboxed = (int)boxed;  // Value di-extract kembali

   // Invalid unboxing - runtime exception
   // double invalid = (double)boxed;  // InvalidCastException
   ```

2. **Mengapa Performance Issue?**

   - Boxing mengalokasikan memory di heap (GC pressure)
   - Setiap boxing operation menciptakan object baru
   - Unboxing melibatkan type check dan memory copy
   - Dalam loop, overhead bisa signifikan

   ```csharp
   // BAD - Boxing di setiap iterasi
   ArrayList list = new ArrayList();
   for (int i = 0; i < 1000000; i++)
   {
       list.Add(i);  // Boxing terjadi di sini!
   }

   // GOOD - Generic collection, no boxing
   List<int> list = new List<int>();
   for (int i = 0; i < 1000000; i++)
   {
       list.Add(i);  // No boxing
   }
   ```

3. **Cara Mendeteksi Boxing**

   - Menggunakan `object`, `dynamic`, atau interface type untuk value type
   - Legacy collections seperti `ArrayList`, `Hashtable`
   - String concatenation dengan value types
   - Method dengan `object` parameter yang di-call dengan value type

   ```csharp
   // Boxing terjadi di sini
   Console.WriteLine("Value: " + 42);  // int di-box ke object

   // Cara hindari boxing
   Console.WriteLine($"Value: {42}");  // Modern interpolation, lebih efisien
   ```

4. **Strategi Menghindari Boxing**

   - Gunakan generic collections (`List<T>`, `Dictionary<TKey, TValue>`)
   - Hindari method dengan `object` parameter untuk value types
   - Gunakan method overloads dengan specific types
   - Gunakan pattern matching untuk type-safe handling

   ```csharp
   // Generic method - no boxing
   public void Process<T>(T value) where T : struct
   {
       // T adalah value type, tidak ada boxing
   }

   // Pattern matching untuk type-safe handling
   public void Handle(object obj)
   {
       switch (obj)
       {
           case int i:
               Console.WriteLine($"Integer: {i}");
               break;
           case double d:
               Console.WriteLine($"Double: {d}");
               break;
           default:
               Console.WriteLine("Unknown type");
               break;
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan konsep dan cara menghindari dengan generics | Menjelaskan memory allocation, GC pressure, dan profiling untuk detect boxing |
| Contoh | Contoh sederhana dengan ArrayList vs List<T> | Contoh dari production optimization, benchmark results |

**Follow-up Questions:**
- "Bagaimana cara mendeteksi boxing di codebase yang sudah ada?"
- "Apakah ada scenario di mana boxing masih acceptable atau bahkan diperlukan?"

**Red Flags:**
- ❌ Tidak memahami bahwa boxing melibatkan heap allocation
- ❌ Tidak bisa memberikan contoh cara menghindari boxing
- ❌ Menganggap generics hanya untuk type safety tanpa memahami performance benefit

**Green Flags:**
- ✅ Menjelaskan GC pressure dan memory implications
- ✅ Menyebutkan tools untuk profiling atau detecting boxing
- ✅ Memberikan contoh dari production optimization

---

### Q-FUND-003: Generics dan Constraints

**Level:** Mid
**Topik:** Type safety, generic constraints, covariance/contravariance

**Pertanyaan:**
Apa itu generics di C# dan kapan sebaiknya menggunakannya? Jelaskan berbagai jenis generic constraints dan kapan masing-masing digunakan.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan manfaat generics, berbagai jenis constraints, dan konsep covariance/contravariance.

1. **Manfaat Generics**

   - **Type Safety**: Compile-time type checking, mencegah runtime type errors
   - **Code Reuse**: Satu implementasi untuk multiple types
   - **Performance**: Menghindari boxing/unboxing untuk value types

   ```csharp
   // TANPA Generics - Runtime error possible
   public class NonGenericList
   {
       private ArrayList _items = new ArrayList();

       public void Add(object item) => _items.Add(item);
       public object Get(int index) => _items[index]; // Perlu cast
   }

   var list = new NonGenericList();
   list.Add("hello");
   var num = (int)list.Get(0);  // Runtime: InvalidCastException

   // DENGAN Generics - Compile-time error
   public class GenericList<T>
   {
       private List<T> _items = new List<T>();

       public void Add(T item) => _items.Add(item);
       public T Get(int index) => _items[index];  // No cast needed
   }

   var list = new GenericList<string>();
   list.Add("hello");
   // var num = list.Get(0);  // Compile error - cannot convert string to int
   ```

2. **Generic Constraints**

   Constraints membatasi types yang bisa digunakan sebagai generic argument, memberikan akses ke members yang specific:

   | Constraint | Deskripsi | Use Case |
   |---|---|---|
   | `where T : class` | T harus reference type | Memastikan T bisa null, reference semantics |
   | `where T : struct` | T harus value type (non-nullable) | Numeric types, structs |
   | `where T : new()` | T harus memiliki parameterless constructor | Factory patterns, creating instances |
   | `where T : BaseClass` | T harus inherit dari class tertentu | Polymorphic behavior |
   | `where T : IInterface` | T harus implement interface tertentu | Ensure specific capability |
   | `where T : notnull` | T tidak boleh nullable | Prevent null values |
   | `where T : unmanaged` | T harus unmanaged type | Interop, pointers, unsafe code |

   ```csharp
   // Constraint: new() - untuk create instance
   public class Factory<T> where T : new()
   {
       public T Create() => new T();
   }

   // Constraint: class - untuk reference type operations
   public class ReferenceRepository<T> where T : class
   {
       public void Process(T item)
       {
           if (item == null) return;  // Valid karena T adalah class
           // ...
       }
   }

   // Constraint: interface - untuk memastikan capability
   public class Repository<T> where T : IEntity, IAuditable
   {
       public void Save(T entity)
       {
           entity.ModifiedAt = DateTime.UtcNow;  // Available karena IAuditable
           // ...
       }
   }

   // Multiple constraints
   public class Service<T> where T : class, IEntity, new()
   {
       public T CreateNew() => new T();  // new() constraint
       public void Validate(T entity)    // class constraint (nullable)
       {
           if (entity == null) throw new ArgumentNullException();
           // IEntity members accessible
       }
   }

   // Constraint: Base class + interface
   public class EntityRepository<T> where T : BaseEntity, IValidatable
   {
       public bool IsValid(T entity)
       {
           return entity.Validate();  // Base class dan interface members
       }
   }
   ```

3. **Contoh Implementasi Generic Repository**

   ```csharp
   // Interface definitions
   public interface IEntity
   {
       int Id { get; }
   }

   public interface IRepository<T> where T : class, IEntity
   {
       Task<T?> GetByIdAsync(int id);
       Task<IEnumerable<T>> GetAllAsync();
       Task AddAsync(T entity);
       Task UpdateAsync(T entity);
       Task DeleteAsync(int id);
   }

   // Generic implementation
   public class Repository<T> : IRepository<T> where T : class, IEntity
   {
       protected readonly DbContext _context;
       protected readonly DbSet<T> _dbSet;

       public Repository(DbContext context)
       {
           _context = context;
           _dbSet = context.Set<T>();
       }

       public async Task<T?> GetByIdAsync(int id)
       {
           return await _dbSet.FirstOrDefaultAsync(e => e.Id == id);
       }

       public async Task<IEnumerable<T>> GetAllAsync()
       {
           return await _dbSet.ToListAsync();
       }

       public async Task AddAsync(T entity)
       {
           await _dbSet.AddAsync(entity);
           await _context.SaveChangesAsync();
       }

       public async Task UpdateAsync(T entity)
       {
           _dbSet.Update(entity);
           await _context.SaveChangesAsync();
       }

       public async Task DeleteAsync(int id)
       {
           var entity = await GetByIdAsync(id);
           if (entity != null)
           {
               _dbSet.Remove(entity);
               await _context.SaveChangesAsync();
           }
       }
   }

   // Usage dengan specific entity
   public class User : IEntity
   {
       public int Id { get; set; }
       public string Name { get; set; } = string.Empty;
       public string Email { get; set; } = string.Empty;
   }

   // DI Registration
   services.AddScoped(typeof(IRepository<>), typeof(Repository<>));

   // Consumption
   public class UserService
   {
       private readonly IRepository<User> _userRepository;

       public UserService(IRepository<User> userRepository)
       {
           _userRepository = userRepository;
       }

       public async Task<User?> GetUserAsync(int id)
       {
           return await _userRepository.GetByIdAsync(id);
       }
   }
   ```

4. **Covariance dan Contravariance**

   Variance mengatur bagaimana generic types berhubungan dengan inheritance:

   - **Covariance (`out`)**: Memungkinkan `IEnumerable<Derived>` digunakan sebagai `IEnumerable<Base>`
   - **Contravariance (`in`)**: Memungkinkan `IAction<Base>` digunakan sebagai `IAction<Derived>`
   - Hanya berlaku untuk interfaces dan delegates

   ```csharp
   // Covariance - out keyword (output only)
   public interface IProducer<out T>
   {
       T Get();
   }

   public class Producer<T> : IProducer<T>
   {
       private T _value;
       public Producer(T value) => _value = value;
       public T Get() => _value;
   }

   // Penggunaan covariance
   IProducer<string> stringProducer = new Producer<string>("Hello");
   IProducer<object> objectProducer = stringProducer;  // Valid karena out
   object value = objectProducer.Get();

   // Contravariance - in keyword (input only)
   public interface IConsumer<in T>
   {
       void Process(T item);
   }

   public class Consumer<T> : IConsumer<T>
   {
       public void Process(T item) => Console.WriteLine(item);
   }

   // Penggunaan contravariance
   IConsumer<object> objectConsumer = new Consumer<object>();
   IConsumer<string> stringConsumer = objectConsumer;  // Valid karena in
   stringConsumer.Process("Hello");

   // Builtin variance examples
   IEnumerable<string> strings = new List<string> { "a", "b" };
   IEnumerable<object> objects = strings;  // Covariance (out)

   IComparer<object> objectComparer = Comparer<object>.Default;
   IComparer<string> stringComparer = objectComparer;  // Contravariance (in)
   ```

   **Rules untuk Variance:**
   - `out` (covariant): Type parameter hanya digunakan sebagai return type
   - `in` (contravariant): Type parameter hanya digunakan sebagai input parameter
   - Reference types only - tidak berlaku untuk value types

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan manfaat generics dan constraints dasar | Menjelaskan variance (in/out), constraints complex, dan trade-offs |
| Contoh | Contoh sederhana generic repository atau list | Contoh dari production dengan variance considerations, library design |

**Follow-up Questions:**
- "Mengapa `List<T>` tidak covariance sementara `IEnumerable<T>` covariance?"
- "Bagaimana cara membuat generic method dengan multiple constraints?"

**Red Flags:**
- ❌ Tidak bisa menjelaskan perbedaan `class` vs `struct` constraint
- ❌ Tidak memahami mengapa generics lebih baik dari `object` untuk type safety
- ❌ Tidak bisa memberikan contoh penggunaan constraints

**Green Flags:**
- ✅ Menjelaskan variance (covariance/contravariance) dengan contoh
- ✅ Menyebutkan `new()` constraint dan use case-nya
- ✅ Memberikan contoh dari production code dengan generic repository atau service


---

### Q-FUND-004: Delegates dan Events

**Level:** Mid
**Topik:** Callback pattern, multicast delegates, event pattern

**Pertanyaan:**
Jelaskan perbedaan antara delegate dan event di C#. Kapan sebaiknya menggunakan event dibandingkan delegate biasa?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan delegate sebagai type-safe function pointer, multicast capability, dan event sebagai encapsulation layer untuk delegates.

1. **Delegate: Type-Safe Function Pointer**

   Delegate adalah type yang merepresentasikan reference ke method dengan signature tertentu.

   ```csharp
   // Deklarasi delegate
   public delegate void NotifyHandler(string message);
   public delegate int Calculator(int a, int b);

   // Penggunaan delegate
   public class NotificationService
   {
       private NotifyHandler? _handler;

       public void SetHandler(NotifyHandler handler) => _handler = handler;
       public void Notify(string message) => _handler?.Invoke(message);
   }

   // Usage
   var service = new NotificationService();
   service.SetHandler(msg => Console.WriteLine($"Notification: {msg}"));
   service.Notify("Hello World");
   ```

2. **Multicast Delegate**

   Delegate mendukung multicast - satu delegate bisa mereferensikan multiple methods.

   ```csharp
   public delegate void EventHandler(string message);

   public class Publisher
   {
       public EventHandler? OnEvent;
       public void RaiseEvent(string message) => OnEvent?.Invoke(message);
   }

   // Multicast delegate
   var publisher = new Publisher();
   publisher.OnEvent += msg => Console.WriteLine($"Handler 1: {msg}");
   publisher.OnEvent += msg => Console.WriteLine($"Handler 2: {msg}");
   publisher.OnEvent += msg => Console.WriteLine($"Handler 3: {msg}");

   publisher.RaiseEvent("Test");
   // Output: Handler 1: Test, Handler 2: Test, Handler 3: Test
   ```

3. **Event: Encapsulation untuk Delegate**

   Event adalah wrapper di sekitar delegate yang membatasi akses:
   - Hanya class yang mendeklarasikan event yang bisa trigger (invoke)
   - External code hanya bisa subscribe (`+=`) atau unsubscribe (`-=`)
   - Tidak bisa assign langsung (`=`) dari luar class

   ```csharp
   public class TemperatureSensor
   {
       // DELEGATE TANPA EVENT - Berbahaya
       public EventHandler<string>? TemperatureChangedDelegate;

       // EVENT - Aman
       public event EventHandler<string>? TemperatureChangedEvent;

       public void SetTemperature(double temperature)
       {
           TemperatureChangedDelegate?.Invoke(this, $"Temperature: {temperature}");
           TemperatureChangedEvent?.Invoke(this, $"Temperature: {temperature}");
       }
   }

   var sensor = new TemperatureSensor();

   // Delegate - bisa di-assign langsung (MENIMPA semua handlers!)
   sensor.TemperatureChangedDelegate = (s, msg) => Console.WriteLine(msg);  // BERBAHAYA

   // Event - hanya bisa += atau -=
   sensor.TemperatureChangedEvent += (s, msg) => Console.WriteLine(msg);  // AMAN
   // sensor.TemperatureChangedEvent = ...  // COMPILE ERROR!
   ```

4. **Event Pattern dengan EventHandler<T>**

   ```csharp
   public class TemperatureChangedEventArgs : EventArgs
   {
       public double OldTemperature { get; }
       public double NewTemperature { get; }
       public DateTime Timestamp { get; }

       public TemperatureChangedEventArgs(double oldTemp, double newTemp)
       {
           OldTemperature = oldTemp;
           NewTemperature = newTemp;
           Timestamp = DateTime.UtcNow;
       }
   }

   public class TemperatureSensor
   {
       private double _temperature;
       public event EventHandler<TemperatureChangedEventArgs>? TemperatureChanged;

       protected virtual void OnTemperatureChanged(double oldTemp, double newTemp)
       {
           TemperatureChanged?.Invoke(this, new TemperatureChangedEventArgs(oldTemp, newTemp));
       }

       public double Temperature
       {
           get => _temperature;
           set
           {
               if (_temperature != value)
               {
                   var oldTemp = _temperature;
                   _temperature = value;
                   OnTemperatureChanged(oldTemp, value);
               }
           }
       }
   }

   // Usage
   var sensor = new TemperatureSensor();
   sensor.TemperatureChanged += (sender, args) =>
   {
       Console.WriteLine($"Changed from {args.OldTemperature} to {args.NewTemperature}");
   };
   sensor.Temperature = 25.5;
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan delegate dan event, perbedaan akses | Menjelaskan thread-safe invocation, event pattern, custom EventArgs |
| Contoh | Contoh sederhana subscribe/unsubscribe | Contoh dari production dengan async event handlers |

**Follow-up Questions:**
- "Bagaimana cara menangani exception di multicast delegate?"
- "Apa itu weak event pattern dan kapan diperlukan?"

**Red Flags:**
- ❌ Tidak bisa menjelaskan perbedaan delegate dan event
- ❌ Tidak memahami multicast delegate
- ❌ Menganggap event dan delegate sama saja

**Green Flags:**
- ✅ Menjelaskan encapsulation benefit dari event
- ✅ Menyebutkan EventHandler<T> pattern
- ✅ Memberikan contoh thread-safe invocation pattern


---

### Q-FUND-005: Async/Await dan Deadlock

**Level:** Both
**Topik:** Synchronization context, deadlock prevention

**Pertanyaan:**
Jelaskan bagaimana deadlock bisa terjadi saat menggunakan `async/await` di aplikasi .NET, dan bagaimana cara mencegahnya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan synchronization context, penyebab deadlock, dan best practices async programming.

1. **Penyebab Deadlock di Async/Await**

   Deadlock terjadi ketika method `async` di-call secara synchronous dengan `.Result` atau `.Wait()` di aplikasi dengan SynchronizationContext.

   ```csharp
   // KODE BERBAHAYA - Bisa deadlock di aplikasi dengan SynchronizationContext
   public ActionResult GetCustomer(int id)
   {
       // DEADLOCK RISK di ASP.NET (legacy), WPF, WinForms
       var customer = _customerService.GetCustomerAsync(id).Result;
       return Ok(customer);
   }

   // Skenario deadlock:
   // 1. UI thread memanggil GetCustomer
   // 2. UI thread blocking menunggu .Result
   // 3. GetCustomerAsync await, mencoba kembali ke UI thread via SynchronizationContext
   // 4. UI thread tidak available (sedang blocking)
   // 5. DEADLOCK - saling menunggu
   ```

2. **Synchronization Context Penjelasan**

   - **ASP.NET (legacy)**: Satu request = satu thread, SynchronizationContext memastikan continuation di thread yang sama
   - **ASP.NET Core**: TIDAK ADA SynchronizationContext, aman dari deadlock jenis ini
   - **WPF/WinForms**: UI thread memiliki SynchronizationContext, blocking async bisa deadlock

3. **Solusi: Async All The Way**

   ```csharp
   // SOLUSI 1: Async all the way (RECOMMENDED)
   public async Task<ActionResult> GetCustomer(int id)
   {
       var customer = await _customerService.GetCustomerAsync(id);
       return Ok(customer);
   }

   // SOLUSI 2: ConfigureAwait(false) di library code
   public async Task<Customer> GetCustomerAsync(int id)
   {
       return await _dbContext.Customers
           .FirstOrDefaultAsync(c => c.Id == id)
           .ConfigureAwait(false);
   }
   ```

4. **ConfigureAwait(false) Penjelasan**

   ```csharp
   // DENGAN ConfigureAwait(true) - default
   // Continuation di-schedule ke original SynchronizationContext
   public async Task ProcessAsync()
   {
       await Task.Delay(100);  // Default: ConfigureAwait(true)
       // Kode ini akan di-execute di original context (e.g., UI thread)
   }

   // DENGAN ConfigureAwait(false)
   // Continuation di-execute di thread pool thread manapun
   public async Task ProcessAsync()
   {
       await Task.Delay(100).ConfigureAwait(false);
       // Kode ini bisa di-execute di thread pool thread manapun
       // Lebih efisien, menghindari context switch
   }

   // GUIDELINES untuk ConfigureAwait(false):
   // - Library code: SELALU gunakan ConfigureAwait(false)
   // - Application code: Tidak perlu (kecuali performance critical)
   // - ASP.NET Core: Tidak diperlukan (tidak ada SynchronizationContext)
   ```

5. **Best Practices Async Programming**

   ```csharp
   // ❌ AVOID: Sync over async
   public void ProcessData()
   {
       var data = GetDataAsync().Result;  // DEADLOCK RISK
   }

   // ❌ AVOID: Async void (kecuali event handler)
   public async void ProcessData()  // Exception tidak bisa di-catch
   {
       await GetDataAsync();
   }

   // ✅ PREFERRED: Async all the way
   public async Task ProcessDataAsync()
   {
       var data = await GetDataAsync();
   }

   // ✅ PREFERRED: ConfigureAwait(false) di library
   public class CustomerService
   {
       public async Task<Customer> GetCustomerAsync(int id)
       {
           return await _repository.GetByIdAsync(id).ConfigureAwait(false);
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan deadlock dasar dan solusi ConfigureAwait(false) | Menjelaskan synchronization context detail, perbedaan ASP.NET vs ASP.NET Core |
| Contoh | Contoh kode deadlock sederhana | Contoh dari production issue, menyarankan static analyzer |

**Follow-up Questions:**
- "Kapan sebaiknya TIDAK menggunakan ConfigureAwait(false)?"
- "Bagaimana cara mendeteksi potential deadlock di existing codebase?"

**Red Flags:**
- ❌ Tidak bisa menjelaskan penyebab deadlock
- ❌ Tidak mengetahui ConfigureAwait(false)
- ❌ Menganggap async/await selalu aman tanpa memahami konteks

**Green Flags:**
- ✅ Mampu menjelaskan synchronization context
- ✅ Menyebutkan analyzer seperti Microsoft.CodeAnalysis.FxCopAnalyzers
- ✅ Memberikan contoh dari pengalaman production debugging


---

### Q-FUND-006: Task vs ValueTask

**Level:** Senior
**Topik:** Performance optimization, allocation reduction

**Pertanyaan:**
Apa perbedaan antara Task dan ValueTask di C#? Kapan sebaiknya menggunakan ValueTask dan apa trade-offs yang perlu dipertimbangkan?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami allocation overhead dari Task dan kapan ValueTask memberikan benefit signifikan.

1. **Perbedaan Fundamental**

   | Aspek | Task<T> | ValueTask<T> |
   |---|---|---|
   | Type | Reference type (class) | Value type (struct) |
   | Allocation | Heap allocation setiap instance | Stack, no allocation untuk synchronous path |
   | Use case | General-purpose async | Hot path dengan synchronous completion |
   | Awaiting | Bisa di-await multiple times | Hanya sekali await, atau konversi ke Task |

   ```csharp
   // Task<T> - Selalu heap allocation
   public async Task<int> GetValueAsync()
   {
       await Task.Delay(100);  // Allocation untuk Task
       return 42;
   }

   // ValueTask<T> - No allocation untuk synchronous path
   public ValueTask<int> GetValueOptimizedAsync()
   {
       if (_cachedValue.HasValue)
       {
           return new ValueTask<int>(_cachedValue.Value);  // No allocation
       }

       return new ValueTask<int>(FetchFromDatabaseAsync());  // Allocation jika async
   }
   ```

2. **Kapan Menggunakan ValueTask**

   ValueTask cocok ketika:
   - Method sering di-call (hot path)
   - Sering completed synchronously (cached data, validation)
   - Allocation overhead perlu diminimalkan

   ```csharp
   // SCENARIO: Cached data dengan ValueTask
   public class CachedDataService
   {
       private readonly Dictionary<int, string> _cache = new();

       public async ValueTask<string> GetDataAsync(int id)
       {
           // Synchronous path - no allocation
           if (_cache.TryGetValue(id, out var cached))
           {
               return cached;
           }

           // Async path - allocation untuk Task
           var data = await _dbService.FetchAsync(id);
           _cache[id] = data;
           return data;
       }
   }
   ```

3. **Trade-offs dan Restrictions**

   ```csharp
   // ❌ AVOID: ValueTask yang di-await multiple times
   public async Task ProcessAsync()
   {
       var valueTask = _service.GetDataAsync();
       var result1 = await valueTask;  // OK
       var result2 = await valueTask;  // BUG! Undefined behavior
   }

   // ✅ PREFERRED: Await immediately
   public async Task ProcessAsync()
   {
       var result = await _service.GetDataAsync();
   }

   // ✅ PREFERRED: Convert ke Task jika perlu multiple consumers
   public async Task BroadcastAsync()
   {
       var dataTask = _dataService.GetDataAsync().AsTask();
       await Task.WhenAll(
           ProcessDataAsync(dataTask),
           LogDataAsync(dataTask)
       );
   }
   ```

4. **Guidelines untuk Memilih**

   | Gunakan Task<T> | Gunakan ValueTask<T> |
   |---|---|
   | General-purpose async operations | Hot path dengan frequent calls |
   | API public yang bisa di-await multiple times | High probability synchronous completion |
   | Tidak ada significant allocation pressure | Profiling menunjukkan allocation pressure |
   | Simplicity lebih penting | Performance critical code paths |

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami allocation overhead, ValueTask internals, dan trade-offs |
| Contoh | Memberikan contoh dari production optimization dengan benchmark results |
| Tools | Menyebutkan BenchmarkDotNet, memory profiler |

**Follow-up Questions:**
- "Bagaimana cara mengukur apakah ValueTask memberikan benefit signifikan?"
- "Apa yang terjadi jika ValueTask di-await lebih dari sekali?"

**Red Flags:**
- ❌ Tidak memahami perbedaan allocation Task vs ValueTask
- ❌ Menggunakan ValueTask tanpa memahami restrictions
- ❌ Tidak bisa menjelaskan kapan ValueTask tidak appropriate

**Green Flags:**
- ✅ Menjelaskan ValueTask internals dengan struct wrapping
- ✅ Menyebutkan benchmark approach untuk validasi benefit
- ✅ Memberikan contoh dari production optimization


---

### Q-FUND-007: LINQ Deferred Execution

**Level:** Mid
**Topik:** IEnumerable, IQueryable, execution timing

**Pertanyaan:**
Apa itu deferred execution di LINQ? Jelaskan perbedaan antara deferred execution dan immediate execution, dan apa implikasinya terhadap behavior code?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan kapan LINQ query di-execute dan implikasinya terhadap performance dan correctness.

1. **Deferred Execution Explained**

   Deferred execution berarti query tidak di-execute saat dideklarasikan, tapi saat di-iterate.

   ```csharp
   var numbers = new List<int> { 1, 2, 3, 4, 5 };

   // Query dideklarasikan - TIDAK di-execute
   var query = numbers.Where(n => n > 2);

   // Data diubah SETELAH query dideklarasikan
   numbers.Add(6);

   // Query di-execute SAAT INI (iteration)
   foreach (var n in query)
   {
       Console.WriteLine(n);  // Output: 3, 4, 5, 6
   }
   // Perhatikan: 6 masuk hasil meskipun ditambah setelah query declaration
   ```

2. **Deferred vs Immediate Execution**

   | Method | Execution | Behavior |
   |---|---|---|
   | `Where`, `Select`, `OrderBy`, `Take`, `Skip` | Deferred | Query di-execute saat iterate |
   | `ToList()`, `ToArray()`, `ToDictionary()` | Immediate | Query di-execute langsung, cache hasil |
   | `Count()`, `Sum()`, `Any()`, `First()` | Immediate | Query di-execute untuk menghitung hasil |

   ```csharp
   var numbers = new List<int> { 1, 2, 3, 4, 5 };

   // DEFERRED - Tidak di-execute sampai iteration
   var deferred = numbers.Where(n => n > 2);

   // IMMEDIATE - Di-execute langsung
   var immediate = numbers.Where(n => n > 2).ToList();

   numbers.Add(6);

   // Deferred: 3, 4, 5, 6 (termasuk yang baru)
   // Immediate: 3, 4, 5 (tidak termasuk yang baru)
   ```

3. **Pitfalls dari Deferred Execution**

   ```csharp
   // PITFALL 1: Multiple enumeration
   var query = numbers.Where(n => n > 2);
   var count = query.Count();      // Execution #1
   var list = query.ToList();      // Execution #2

   // SOLUSI: Materialize jika akan digunakan multiple times
   var materialized = numbers.Where(n => n > 2).ToList();

   // PITFALL 2: Captured variable dengan nilai berubah
   var threshold = 3;
   var query = numbers.Where(n => n > threshold);
   threshold = 5;  // Ubah nilai setelah query declaration
   var result = query.ToList();  // Menggunakan threshold = 5!
   ```

4. **IEnumerable vs IQueryable**

   ```csharp
   // IEnumerable<T> - In-memory execution (LINQ to Objects)
   IEnumerable<int> enumerable = numbers.Where(n => n > 2);
   // Query di-execute di client side

   // IQueryable<T> - Remote execution (LINQ to Entities)
   IQueryable<User> queryable = dbContext.Users.Where(u => u.IsActive);
   // Query ditranslate ke SQL dan di-execute di database

   // Perbedaan penting dengan EF Core
   var users = dbContext.Users
       .Where(u => u.IsActive)
       .Select(u => u.Name)
       .ToList();  // SQL: SELECT Name FROM Users WHERE IsActive = 1

   // Jika pakai IEnumerable setelah AsEnumerable()
   var usersInMemory = dbContext.Users
       .AsEnumerable()  // Pull semua data ke memory
       .Where(u => u.IsActive)  // Filter di memory - TIDAK EFFISIEN
       .ToList();
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan deferred vs immediate, contoh behavior | Menjelaskan IQueryable vs IEnumerable, expression trees |
| Contoh | Contoh sederhana dengan List | Contoh dengan EF Core, query translation |

**Follow-up Questions:**
- "Bagaimana cara men-debug LINQ query untuk melihat kapan di-execute?"
- "Apa implikasi deferred execution terhadap database queries di EF Core?"

**Red Flags:**
- ❌ Tidak memahami perbedaan deferred vs immediate execution
- ❌ Tidak menyadari multiple enumeration problem
- ❌ Tidak bisa menjelaskan captured variable pitfall

**Green Flags:**
- ✅ Menjelaskan IEnumerable vs IQueryable distinction
- ✅ Menyebutkan expression trees sebagai underlying mechanism
- ✅ Memberikan contoh dari production debugging


---

### Q-FUND-008: LINQ Performance dan Optimization

**Level:** Senior
**Topik:** Query optimization, projection, materialization

**Pertanyaan:**
Bagaimana cara mengoptimasi LINQ query untuk performa terbaik? Jelaskan berbagai teknik optimization dan kapan menerapkannya.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus mampu menjelaskan optimization techniques termasuk projection, early filtering, dan memory allocation considerations.

1. **Projection Optimization**

   ```csharp
   // ❌ BAD: Select semua columns, kemudian filter di memory
   var customers = await _dbContext.Customers.ToListAsync();
   var names = customers.Select(c => c.Name).ToList();

   // ✅ GOOD: Select hanya yang diperlukan di database
   var names = await _dbContext.Customers
       .Select(c => c.Name)
       .ToListAsync();

   // ✅ BETTER: DTO projection untuk complex queries
   var customerDtos = await _dbContext.Customers
       .Where(c => c.IsActive)
       .Select(c => new CustomerDto
       {
           Id = c.Id,
           Name = c.Name,
           OrderCount = c.Orders.Count
       })
       .ToListAsync();
   ```

2. **Early Filtering**

   ```csharp
   // ❌ BAD: Filter setelah materialization
   var allOrders = await _dbContext.Orders.ToListAsync();
   var recentOrders = allOrders.Where(o => o.OrderDate > DateTime.Now.AddDays(-30));

   // ✅ GOOD: Filter sebelum materialization
   var recentOrders = await _dbContext.Orders
       .Where(o => o.OrderDate > DateTime.Now.AddDays(-30))
       .ToListAsync();
   ```

3. **Avoid N+1 dengan Eager Loading**

   ```csharp
   // ❌ BAD: N+1 query problem
   var orders = await _dbContext.Orders.ToListAsync();
   foreach (var order in orders)
   {
       var items = await _dbContext.OrderItems
           .Where(i => i.OrderId == order.Id)
           .ToListAsync();  // N queries!
   }

   // ✅ GOOD: Eager loading dengan Include
   var orders = await _dbContext.Orders
       .Include(o => o.OrderItems)
       .ThenInclude(i => i.Product)
       .ToListAsync();
   ```

4. **Cartesian Explosion Awareness**

   ```csharp
   // ⚠️ WARNING: Multiple Includes bisa menyebabkan Cartesian explosion
   var result = await _dbContext.Orders
       .Include(o => o.OrderItems)
       .Include(o => o.Payments)
       .ToListAsync();  // Result set = Orders × Items × Payments

   // ✅ BETTER: Split query untuk multiple collections
   var result = await _dbContext.Orders
       .Include(o => o.OrderItems)
       .Include(o => o.Payments)
       .AsSplitQuery()
       .ToListAsync();
   ```

5. **Compiled Queries untuk Hot Paths**

   ```csharp
   // ✅ GOOD: Compiled query untuk frequently executed queries
   private static readonly Func<AppDbContext, int, Task<Customer?>> GetCustomerById =
       EF.CompileAsyncQuery((AppDbContext db, int id) =>
           db.Customers.FirstOrDefault(c => c.Id == id));

   // Usage
   var customer = await GetCustomerById(_dbContext, customerId);
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami query translation, memory allocation, compiled queries, split query |
| Contoh | Memberikan contoh dari production optimization dengan actual performance gains |
| Tools | Menyebutkan MiniProfiler, SQL Profiler, BenchmarkDotNet |

**Follow-up Questions:**
- "Bagaimana cara memprofil LINQ query untuk menemukan bottleneck?"
- "Kapan sebaiknya menggunakan compiled queries?"

**Red Flags:**
- ❌ Tidak memahami N+1 problem
- ❌ Tidak menyadari Cartesian explosion
- ❌ Tidak bisa menjelaskan projection benefits

**Green Flags:**
- ✅ Menjelaskan split query dan compiled queries
- ✅ Menyebutkan profiling tools
- ✅ Memberikan contoh dari production optimization


---

### Q-FUND-009: Extension Methods

**Level:** Mid
**Topik:** Syntax, use cases, namespace consideration

**Pertanyaan:**
Apa itu extension methods di C#? Bagaimana cara kerjanya dan kapan sebaiknya menggunakannya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan syntax extension methods, underlying mechanism, dan best practices untuk penggunaan.

1. **Definisi dan Syntax**

   Extension method memungkinkan menambahkan method ke existing type tanpa modify type tersebut.

   ```csharp
   // Definisi extension method
   public static class StringExtensions
   {
       public static string Truncate(this string source, int maxLength)
       {
           if (string.IsNullOrEmpty(source) || source.Length <= maxLength)
               return source;
           return source.Substring(0, maxLength) + "...";
       }

       public static bool IsNullOrEmpty(this string? value)
       {
           return string.IsNullOrEmpty(value);
       }
   }

   // Penggunaan
   string text = "This is a very long text";
   string truncated = text.Truncate(20);  // "This is a very long..."

   if (text.IsNullOrEmpty()) { /* ... */ }
   ```

2. **Cara Kerja Extension Methods**

   Extension methods adalah syntactic sugar - compiler mengubah instance method call menjadi static method call.

   ```csharp
   // Yang kita tulis
   string result = text.Truncate(20);

   // Yang di-generate compiler
   string result = StringExtensions.Truncate(text, 20);
   ```

3. **Use Cases yang Tepat**

   ```csharp
   // USE CASE 1: Utility methods untuk existing types
   public static class DateTimeExtensions
   {
       public static bool IsWeekend(this DateTime date)
       {
           return date.DayOfWeek == DayOfWeek.Saturday ||
                  date.DayOfWeek == DayOfWeek.Sunday;
       }

       public static int CalculateAge(this DateTime birthDate)
       {
           var today = DateTime.Today;
           var age = today.Year - birthDate.Year;
           if (birthDate.Date > today.AddYears(-age)) age--;
           return age;
       }
   }

   // USE CASE 2: Fluent API / Builder pattern
   public static class FluentExtensions
   {
       public static StringBuilder AppendLineIf(
           this StringBuilder builder,
           bool condition,
           string value)
       {
           if (condition) builder.AppendLine(value);
           return builder;
       }
   }

   // USE CASE 3: Adapter methods untuk interfaces
   public static class IEnumerableExtensions
   {
       public static void ForEach<T>(this IEnumerable<T> source, Action<T> action)
       {
           foreach (var item in source)
               action(item);
       }
   }
   ```

4. **Best Practices**

   | Do | Don't |
   |---|---|
   | Gunakan untuk utility methods yang relevan | Gunakan untuk business logic |
   | Taruh di namespace yang logis (e.g., `System.Linq`) | Overuse yang membuat code confusing |
   | Document dengan XML comments | Extend primitive types tanpa alasan kuat |
   | Pertimbangkan conflict dengan instance methods | Buat extension dengan nama yang sama di namespace berbeda |

   ```csharp
   // ❌ AVOID: Business logic di extension method
   public static class OrderExtensions
   {
       public static decimal CalculateTotal(this Order order)
       {
           // Ini seharusnya di domain service atau entity method
           return order.Items.Sum(i => i.Price * i.Quantity);
       }
   }

   // ✅ BETTER: Extension untuk general utility
   public static class CollectionExtensions
   {
       public static bool IsNullOrEmpty<T>(this IEnumerable<T>? source)
       {
           return source == null || !source.Any();
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan syntax dan use cases dasar | Menjelaskan namespace conflicts, priority rules, library design considerations |
| Contoh | Contoh sederhana utility methods | Contoh dari library design, fluent API design |

**Follow-up Questions:**
- "Apa yang terjadi jika extension method dan instance method memiliki nama yang sama?"
- "Bagaimana cara mengorganisir extension methods di project besar?"

**Red Flags:**
- ❌ Tidak memahami bahwa extension method adalah static method
- ❌ Tidak mengetahui `this` keyword requirement
- ❌ Menggunakan extension methods untuk business logic yang seharusnya di entity

**Green Flags:**
- ✅ Menjelaskan priority: instance method > extension method
- ✅ Menyebutkan namespace consideration untuk discoverability
- ✅ Memberikan contoh dari library seperti `System.Linq`


---

### Q-FUND-010: Reflection dan Attributes

**Level:** Senior
**Topik:** Metadata inspection, dynamic invocation, custom attributes

**Pertanyaan:**
Apa itu reflection di .NET dan kapan sebaiknya menggunakannya? Jelaskan juga bagaimana membuat dan menggunakan custom attributes.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami reflection capabilities, performance implications, dan proper use cases.

1. **Apa itu Reflection**

   Reflection memungkinkan inspeksi dan manipulasi types, members, dan attributes pada runtime.

   ```csharp
   using System.Reflection;

   // Mendapatkan type information
   Type type = typeof(Customer);
   Console.WriteLine($"Type: {type.Name}");
   Console.WriteLine($"Assembly: {type.Assembly.FullName}");

   // Mendapatkan properties
   PropertyInfo[] properties = type.GetProperties();
   foreach (var prop in properties)
   {
       Console.WriteLine($"Property: {prop.Name}, Type: {prop.PropertyType.Name}");
   }

   // Mendapatkan methods
   MethodInfo[] methods = type.GetMethods(BindingFlags.Public | BindingFlags.Instance);
   foreach (var method in methods)
   {
       Console.WriteLine($"Method: {method.Name}");
   }
   ```

2. **Dynamic Invocation**

   ```csharp
   // Creating instance dynamically
   Type type = typeof(Customer);
   object? instance = Activator.CreateInstance(type);

   // Getting and setting property value
   PropertyInfo nameProp = type.GetProperty("Name");
   nameProp?.SetValue(instance, "John Doe");
   object? value = nameProp?.GetValue(instance);  // "John Doe"

   // Invoking method dynamically
   MethodInfo method = type.GetMethod("GetFullName");
   object? result = method?.Invoke(instance, null);

   // With parameters
   MethodInfo calculateMethod = type.GetMethod("Calculate");
   object? total = calculateMethod?.Invoke(instance, new object[] { 10, 20 });
   ```

3. **Custom Attributes**

   ```csharp
   // Defining custom attribute
   [AttributeUsage(AttributeTargets.Property | AttributeTargets.Class)]
   public class ExportAttribute : Attribute
   {
       public string ColumnName { get; set; }
       public int Order { get; set; } = 0;
       public bool Ignore { get; set; } = false;

       public ExportAttribute(string columnName = "")
       {
           ColumnName = columnName;
       }
   }

   // Applying attribute
   [Export("Customer", Order = 1)]
   public class Customer
   {
       [Export("Customer ID", Order = 1)]
       public int Id { get; set; }

       [Export("Full Name", Order = 2)]
       public string Name { get; set; }

       [Export(Ignore = true)]
       public string Password { get; set; }
   }

   // Reading attributes via reflection
   public class ExportService
   {
       public void ExportToCsv<T>(IEnumerable<T> items)
       {
           Type type = typeof(T);
           var properties = type.GetProperties()
               .Where(p => p.GetCustomAttribute<ExportAttribute>()?.Ignore != true)
               .OrderBy(p => p.GetCustomAttribute<ExportAttribute>()?.Order ?? 0);

           // Build CSV header
           var headers = properties.Select(p =>
               p.GetCustomAttribute<ExportAttribute>()?.ColumnName ?? p.Name);
           Console.WriteLine(string.Join(",", headers));

           // Build CSV rows
           foreach (var item in items)
           {
               var values = properties.Select(p => p.GetValue(item)?.ToString() ?? "");
               Console.WriteLine(string.Join(",", values));
           }
       }
   }
   ```

4. **Performance Considerations**

   ```csharp
   // ❌ BAD: Reflection di hot path
   public decimal Calculate(object obj)
   {
       var type = obj.GetType();
       var method = type.GetMethod("Calculate");  // SLOW!
       return (decimal)method.Invoke(obj, null);
   }

   // ✅ BETTER: Cache reflection results
   private static readonly ConcurrentDictionary<Type, MethodInfo> _methodCache = new();

   public decimal Calculate(object obj)
   {
       var type = obj.GetType();
       var method = _methodCache.GetOrAdd(type, t => t.GetMethod("Calculate"));
       return (decimal)method.Invoke(obj, null);
   }

   // ✅ BEST: Use compiled expressions (even faster)
   private static readonly ConcurrentDictionary<Type, Func<object, decimal>> _compiledCache = new();

   public decimal CalculateOptimized(object obj)
   {
       var func = _compiledCache.GetOrAdd(obj.GetType(), type =>
       {
           var method = type.GetMethod("Calculate");
           var param = Expression.Parameter(typeof(object), "obj");
           var cast = Expression.Convert(param, type);
           var call = Expression.Call(cast, method);
           var convert = Expression.Convert(call, typeof(decimal));
           return Expression.Lambda<Func<object, decimal>>(convert, param).Compile();
       });
       return func(obj);
   }
   ```

5. **Use Cases yang Tepat**

   | Use Case | Contoh |
   |---|---|
   | Serialization/Deserialization | JSON, XML converters |
   | ORM Mapping | Entity Framework, Dapper |
   | Validation frameworks | Data annotations, FluentValidation |
   | Dependency Injection containers | Service registration, resolution |
   | Plugin systems | Dynamic loading, discovery |
   | AOP frameworks | Logging, caching interceptors |

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami reflection capabilities, performance cost, caching strategies |
| Contoh | Memberikan contoh dari production use case |
| Tools | Menyebutkan compiled expressions, source generators as alternatives |

**Follow-up Questions:**
- "Apa keuntungan menggunakan source generators dibanding reflection?"
- "Bagaimana cara mengoptimasi reflection-heavy code?"

**Red Flags:**
- ❌ Tidak memahami performance overhead reflection
- ❌ Menggunakan reflection ketika ada alternative yang lebih baik
- ❌ Tidak bisa menjelaskan custom attribute usage

**Green Flags:**
- ✅ Menjelaskan caching strategies untuk reflection
- ✅ Menyebutkan compiled expressions atau source generators
- ✅ Memberikan contoh dari framework yang menggunakan reflection


---

### Q-FUND-011: Garbage Collection di .NET

**Level:** Senior
**Topik:** Generations, LOH, GC modes

**Pertanyaan:**
Jelaskan cara kerja Garbage Collection di .NET. Apa itu generasi, Large Object Heap, dan bagaimana cara tuning GC untuk aplikasi high-performance?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami GC generations, LOH behavior, dan tuning strategies.

1. **GC Generations**

   .NET GC menggunakan generational approach dengan 3 generasi:

   | Generasi | Deskripsi | Kapan Collection Terjadi |
   |---|---|---|
   | Gen 0 | Short-lived objects | Paling sering, sangat cepat |
   | Gen 1 | Medium-lived objects | Lebih jarang, buffer antara Gen 0 dan Gen 2 |
   | Gen 2 | Long-lived objects | Paling jarang, full collection (expensive) |

   ```csharp
   // Gen 0: Temporary objects
   public void ProcessData()
   {
       var temp = new byte[1000];  // Gen 0 allocation
       // temp akan di-collect cepat setelah method selesai
   }

   // Gen 2: Long-lived objects
   public class SingletonCache
   {
       private static readonly Cache _instance = new();  // Gen 2
       // Akan survive banyak Gen 0/1 collections
   }
   ```

2. **Large Object Heap (LOH)**

   Objects ≥ 85,000 bytes dialokasikan di LOH, yang memiliki behavior khusus:

   ```csharp
   // LOH allocation threshold
   const int LOHThreshold = 85_000;

   // Ini akan di-allokasi di LOH
   byte[] largeBuffer = new byte[100_000];

   // Ini di regular heap
   byte[] smallBuffer = new byte[80_000];

   // LOH characteristics:
   // - Hanya di-collect saat Gen 2 collection
   // - Tidak di-compact oleh default (sebelum .NET 8)
   // - Bisa menyebabkan memory fragmentation
   // - Di .NET 8+, LOH di-compact secara default saat server GC
   ```

   ```csharp
   // LOH fragmentation issue
   public class FragmentationDemo
   {
       private List<byte[]> _buffers = new();

       public void DemonstrateFragmentation()
       {
           for (int i = 0; i < 100; i++)
           {
               _buffers.Add(new byte[100_000]);  // LOH allocations
           }

           // Remove alternate buffers
           for (int i = _buffers.Count - 1; i >= 0; i -= 2)
           {
               _buffers.RemoveAt(i);
           }

           // LOH sekarang fragmented - holes di memory
           // Bisa menyebabkan OutOfMemoryException meskipun total memory available
       }
   }
   ```

3. **Workstation vs Server GC**

   | Mode | Karakteristik | Use Case |
   |---|---|---|
   | Workstation | Single thread GC, lower throughput | Client apps, single-core servers |
   | Server | Multi-threaded GC, higher throughput | Server apps, multi-core machines |
   | Background GC | Non-blocking untuk Gen 2 | UI responsiveness, server throughput |

   ```csharp
   // Konfigurasi di csproj
   // <PropertyGroup>
   //   <ServerGarbageCollection>true</ServerGarbageCollection>
   // </PropertyGroup>

   // Atau di runtimeconfig.json
   // {
   //   "configProperties": {
   //     "System.GC.Server": true
   //   }
   // }

   // Cek GC mode saat runtime
   Console.WriteLine($"IsServerGC: {GCSettings.IsServerGC}");
   Console.WriteLine($"GCLatencyMode: {GCSettings.LatencyMode}");
   ```

4. **GC Tuning Strategies**

   ```csharp
   // Temporary latency mode untuk sensitive operations
   public void PerformSensitiveOperation()
   {
       GCLatencyMode oldMode = GCSettings.LatencyMode;

       try
       {
           // Non-blocking mode untuk UI responsiveness
           GCSettings.LatencyMode = GCLatencyMode.LowLatency;

           // Perform time-sensitive operation
           ProcessRealTimeData();
       }
       finally
       {
           GCSettings.LatencyMode = oldMode;
       }
   }

   // Manual GC hint (NET 7+)
   public void CleanupBeforeHeavyOperation()
   {
       GC.Collect(GC.MaxGeneration, GCCollectionMode.Aggressive, true, true);
       GC.WaitForPendingFinalizers();
       GC.Collect();  // Second pass untuk finalized objects
   }

   // ArrayPool untuk mengurangi LOH pressure
   public void ProcessLargeData()
   {
       var pool = ArrayPool<byte>.Shared;
       byte[] buffer = pool.Rent(100_000);

       try
       {
           // Use buffer
           ProcessBuffer(buffer);
       }
       finally
       {
           pool.Return(buffer);  // Reuse, no new LOH allocation
       }
   }
   ```

5. **Memory Diagnostics**

   ```csharp
   // GC metrics monitoring
   Console.WriteLine($"Gen 0 Collections: {GC.CollectionCount(0)}");
   Console.WriteLine($"Gen 1 Collections: {GC.CollectionCount(1)}");
   Console.WriteLine($"Gen 2 Collections: {GC.CollectionCount(2)}");
   Console.WriteLine($"Total Memory: {GC.GetTotalMemory(false) / 1024 / 1024} MB");

   // High Gen 2 count indicates memory issues
   // Good ratio: Gen 0 >> Gen 1 >> Gen 2
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami generational GC, LOH behavior, GC modes |
| Contoh | Memberikan contoh dari production tuning |
| Tools | Menyebutkan dotnet-counters, dotnet-dump, PerfView |

**Follow-up Questions:**
- "Bagaimana cara diagnose memory leak di aplikasi .NET?"
- "Apa indikator bahwa aplikasi perlu GC tuning?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Gen 0/1/2
- ❌ Tidak mengetahui LOH dan threshold-nya
- ❌ Tidak bisa menjelaskan kapan menggunakan Server vs Workstation GC

**Green Flags:**
- ✅ Menjelaskan LOH fragmentation dan mitigasi
- ✅ Menyebutkan ArrayPool, MemoryPool untuk reduce allocations
- ✅ Memberikan contoh dari production GC tuning


---

### Q-FUND-011: Garbage Collection di .NET

**Level:** Senior
**Topik:** Generations, LOH, GC modes

**Pertanyaan:**
Jelaskan cara kerja Garbage Collection di .NET. Bagaimana generational GC bekerja dan apa saja yang perlu dipertimbangkan untuk aplikasi high-performance?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami GC generations, Large Object Heap, dan konfigurasi GC modes.

1. **Generational GC Concept**

   .NET GC menggunakan generational approach dengan 3 generasi:

   | Generasi | Deskripsi | Trigger |
   |---|---|---|
   | **Gen 0** | Short-lived objects | Allocation threshold reached |
   | **Gen 1** | Buffer antara Gen 0 dan Gen 2 | Gen 1 full, Gen 0 promotion |
   | **Gen 2** | Long-lived objects | Full GC, memory pressure |

   ```csharp
   // Object lifecycle
   public void ProcessData()
   {
       var temp = new byte[100];

---

### Q-FUND-012: IDisposable dan Using Statement

**Level:** Mid
**Topik:** Resource management, dispose pattern, finalizers

**Pertanyaan:**
Apa itu IDisposable di .NET? Kapan perlu mengimplementasikannya dan bagaimana pola yang benar?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan kapan implement IDisposable, dispose pattern, dan hubungannya dengan finalizers.

1. **Kapan Implement IDisposable**

   Implement IDisposable ketika class:
   - Memiliki unmanaged resources (file handles, database connections, network streams)
   - Memiliki managed objects yang implement IDisposable
   - Perlu explicit cleanup, bukan menunggu GC

   ```csharp
   public class FileProcessor : IDisposable
   {
       private FileStream? _fileStream;
       private StreamReader? _reader;

       public FileProcessor(string path)
       {
           _fileStream = new FileStream(path, FileMode.Open);
           _reader = new StreamReader(_fileStream);
       }

       public string ReadLine() => _reader?.ReadLine() ?? string.Empty;

       public void Dispose()
       {
           _reader?.Dispose();
           _fileStream?.Dispose();
       }
   }

   // Usage dengan using
   using var processor = new FileProcessor("data.txt");
   string line = processor.ReadLine();
   // Dispose() dipanggil otomatis saat scope berakhir
   ```

2. **Full Dispose Pattern dengan Finalizer**

   ```csharp
   public class DatabaseConnection : IDisposable
   {
       private IntPtr _handle;  // Unmanaged resource
       private SqlConnection? _connection;  // Managed resource
       private bool _disposed = false;

       public DatabaseConnection(string connectionString)
       {
           _handle = GetNativeHandle();
           _connection = new SqlConnection(connectionString);
       }

       // Public Dispose method
       public void Dispose()
       {
           Dispose(true);
           GC.SuppressFinalize(this);  // No need for finalizer
       }

       // Protected virtual for derived classes
       protected virtual void Dispose(bool disposing)
       {
           if (_disposed) return;

           if (disposing)
           {
               // Dispose managed resources
               _connection?.Dispose();
               _connection = null;
           }

           // Dispose unmanaged resources
           if (_handle != IntPtr.Zero)
           {
               ReleaseNativeHandle(_handle);
               _handle = IntPtr.Zero;
           }

           _disposed = true;
       }

       // Finalizer (destructor)
       ~DatabaseConnection()
       {
           Dispose(false);  // Only unmanaged resources
       }

       private static extern IntPtr GetNativeHandle();
       private static extern void ReleaseNativeHandle(IntPtr handle);
   }
   ```

3. **Using Statement Variations**

   ```csharp
   // C# 8.0+ - using declaration
   using var file = new StreamReader("data.txt");
   string content = file.ReadToEnd();
   // Dispose at end of scope

   // Classic using statement
   using (var file = new StreamReader("data.txt"))
   {
       string content = file.ReadToEnd();
   }  // Dispose here

   // Multiple resources
   using var file = new StreamReader("data.txt");
   using var writer = new StreamWriter("output.txt");
   // Both disposed at end of scope

   // Alternative syntax
   using (var file = new StreamReader("data.txt"))
   using (var writer = new StreamWriter("output.txt"))
   {
       // Process
   }
   ```

4. **Best Practices**

   | Pattern | When to Use |
   |---|---|
   | Simple `Dispose()` | Class hanya holds managed IDisposable resources |
   | Full pattern with finalizer | Class holds unmanaged resources |
   | `GC.SuppressFinalize()` | Always when `Dispose()` called |
   | `protected virtual Dispose(bool)` | When class bisa di-inherit |

   ```csharp
   // ❌ AVOID: Not disposing IDisposable
   public void ProcessFile(string path)
   {
       var reader = new StreamReader(path);
       string content = reader.ReadToEnd();
       // reader NOT disposed - resource leak!
   }

   // ✅ GOOD: Always dispose
   public void ProcessFile(string path)
   {
       using var reader = new StreamReader(path);
       string content = reader.ReadToEnd();
       // reader automatically disposed
   }

   // ✅ GOOD: Check if disposed before using
   public class SafeResource : IDisposable
   {
       private FileStream? _stream;
       private bool _disposed;

       public void Write(byte[] data)
       {
           if (_disposed)
               throw new ObjectDisposedException(nameof(SafeResource));

           _stream?.Write(data);
       }

       public void Dispose()
       {
           if (_disposed) return;
           _stream?.Dispose();
           _disposed = true;
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan IDisposable dan using statement | Menjelaskan full dispose pattern, finalizers, safe handles |
| Contoh | Contoh sederhana dengan Stream/Connection | Contoh dengan unmanaged resources, interop scenarios |

**Follow-up Questions:**
- "Apa perbedaan antara finalizer dan IDisposable?"
- "Kapan sebaiknya TIDAK menggunakan finalizer?"

**Red Flags:**
- ❌ Tidak memahami kapan implement IDisposable
- ❌ Tidak mengetahui using statement
- ❌ Tidak bisa menjelaskan hubungan finalizer dan Dispose

**Green Flags:**
- ✅ Menjelaskan full dispose pattern dengan `Dispose(bool)`
- ✅ Menyebutkan `GC.SuppressFinalize()` dan alasannya
- ✅ Memberikan contoh dengan unmanaged resources


---

### Q-FUND-013: Memory Management Best Practices

**Level:** Senior
**Topik:** Span<T>, Memory<T>, array pooling, stackalloc

**Pertanyaan:**
Apa saja teknik modern untuk memory management di .NET? Jelaskan penggunaan Span<T>, Memory<T>, ArrayPool, dan stackalloc.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami modern memory optimization techniques untuk high-performance scenarios.

1. **Span<T> - Stack-Only Slice**

   Span<T> adalah struct yang menyediakan view ke contiguous memory tanpa allocation.

   ```csharp
   // Span<T> characteristics:
   // - Stack-only (tidak bisa di-store di heap)
   // - Zero-allocation slice
   // - Safe dan bounds-checked

   public void ProcessData()
   {
       int[] array = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

       // Slice tanpa allocation
       Span<int> slice = array.AsSpan(2, 5);  // { 3, 4, 5, 6, 7 }

       // Modify slice (affects original)
       slice[0] = 99;  // array[2] = 99

       // String to ReadOnlySpan<char>
       ReadOnlySpan<char> text = "Hello World".AsSpan();
       ReadOnlySpan<char> world = text.Slice(6);  // "World"
   }

   // Parsing dengan Span (avoid allocation)
   public int ParseNumber(ReadOnlySpan<char> input)
   {
       int result = 0;
       foreach (char c in input)
       {
           if (char.IsDigit(c))
               result = result * 10 + (c - '0');
       }
       return result;
   }
   ```

2. **Memory<T> - Heap-Safe Slice**

   Memory<T> adalah heap-safe wrapper yang bisa di-stored dan di-passed async.

   ```csharp
   // Memory<T> characteristics:
   // - Can be stored on heap
   // - Can be used in async methods
   // - Convert to Span<T> when needed

   public class BufferProcessor
   {
       private Memory<byte> _buffer;

       public BufferProcessor(int size)
       {
           _buffer = new byte[size];
       }

       // Can be used in async - Memory<T> is safe
       public async Task ProcessAsync()
       {
           // Convert to Span for synchronous processing
           ProcessBuffer(_buffer.Span);

           await WriteToStreamAsync(_buffer);
       }

       private void ProcessBuffer(Span<byte> buffer)
       {
           // In-memory processing
           for (int i = 0; i < buffer.Length; i++)
           {
               buffer[i] = (byte)(buffer[i] * 2);
           }
       }

       private async Task WriteToStreamAsync(Memory<byte> buffer)
       {
           // Async operation with Memory<T>
           await _stream.WriteAsync(buffer);
       }
   }
   ```

3. **ArrayPool<T> - Reusable Arrays**

   ```csharp
   using System.Buffers;

   // ArrayPool untuk reduce GC pressure
   public class DataProcessor
   {
       private readonly ArrayPool<byte> _pool = ArrayPool<byte>.Shared;

       public byte[] ProcessData(int size)
       {
           byte[] buffer = _pool.Rent(size);

           try
           {
               // Use buffer
               FillBuffer(buffer.AsSpan(0, size));
               return buffer[..size].ToArray();  // Return copy if needed
           }
           finally
           {
               _pool.Return(buffer);  // Return to pool
           }
       }

       // Better: Return memory to caller
       public IMemoryOwner<byte> ProcessDataOwner(int size)
       {
           var owner = MemoryPool<byte>.Shared.Rent(size);
           FillBuffer(owner.Memory.Span);
           return owner;  // Caller owns and must dispose
       }
   }

   // Usage with using
   public void UseProcessor()
   {
       var processor = new DataProcessor();

       using var owner = processor.ProcessDataOwner(1024);
       ProcessBuffer(owner.Memory);
       // owner.Dispose() returns memory to pool
   }
   ```

4. **stackalloc - Stack Allocation**

   ```csharp
   // stackalloc allocates on stack, not heap
   // Very fast, automatically freed when method returns
   // Safe with Span<T>

   public void ProcessSmallData()
   {
       // Stack allocation for small buffers
       Span<int> buffer = stackalloc int[100];  // On stack!

       for (int i = 0; i < buffer.Length; i++)
       {
           buffer[i] = i * 2;
       }

       ProcessBuffer(buffer);
       // No GC pressure, automatically freed
   }

   // Use for parsing small strings
   public int ParseInt(ReadOnlySpan<char> input)
   {
       Span<char> buffer = stackalloc char[32];
       input.CopyTo(buffer);
       return int.Parse(buffer.Slice(0, input.Length));
   }

   // ⚠️ WARNING: Only for small, short-lived buffers
   // Stack space is limited (~1MB default)
   // ❌ BAD: Span<int> big = stackalloc int[1_000_000];  // Stack overflow!
   ```

5. **Performance Comparison**

   | Technique | Allocation | Use Case |
   |---|---|---|
   | `new T[]` | Heap | General purpose |
   | `stackalloc` | Stack | Small, short-lived buffers |
   | `ArrayPool.Rent` | Pooled | Large, frequently allocated |
   | `Span<T>` | None | Slicing, parsing |
   | `Memory<T>` | None (wrapper) | Async scenarios |

   ```csharp
   // Practical example: String parsing without allocation
   public (string FirstName, string LastName) ParseName(string fullName)
   {
       ReadOnlySpan<char> span = fullName.AsSpan();
       int spaceIndex = span.IndexOf(' ');

       if (spaceIndex < 0)
           return (fullName, string.Empty);

       // Slice without allocation
       ReadOnlySpan<char> firstName = span[..spaceIndex];
       ReadOnlySpan<char> lastName = span[(spaceIndex + 1)..];

       // Only allocate when returning strings
       return (firstName.ToString(), lastName.ToString());
   }
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami Span, Memory, ArrayPool, stackalloc dan kapan menggunakan masing-masing |
| Contoh | Memberikan contoh dari production optimization |
| Tools | Menyebutkan BenchmarkDotNet, memory profilers |

**Follow-up Questions:**
- "Apa batasan Span<T> dibanding Memory<T>?"
- "Kapan sebaiknya menggunakan ArrayPool dibanding new array?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Span dan Memory
- ❌ Tidak mengetahui stackalloc
- ❌ Tidak bisa menjelaskan kapan ArrayPool bermanfaat

**Green Flags:**
- ✅ Menjelaskan stack-only constraint Span<T>
- ✅ Menyebutkan MemoryMarshal, advanced Span operations
- ✅ Memberikan contoh dari production high-performance code


---

### Q-FUND-014: Thread Safety dan Synchronization

**Level:** Both
**Topik:** Lock, Monitor, Mutex, Semaphore, concurrent collections

**Pertanyaan:**
Apa saja primitives untuk thread synchronization di .NET? Jelaskan perbedaan antara lock, Monitor, Mutex, Semaphore, dan kapan menggunakan masing-masing.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan berbagai synchronization primitives dan cara mencegah race conditions.

1. **lock Statement**

   lock adalah yang paling sederhana untuk mutual exclusion dalam single process.

   ```csharp
   public class Counter
   {
       private readonly object _lock = new();
       private int _count;

       public void Increment()
       {
           lock (_lock)  // Enter critical section
           {
               _count++;
           }  // Exit critical section
       }

       public int GetCount()
       {
           lock (_lock)
           {
               return _count;
           }
       }
   }

   // lock adalah syntactic sugar untuk Monitor
   public void IncrementWithMonitor()
   {
       Monitor.Enter(_lock);
       try
       {
           _count++;
       }
       finally
       {
           Monitor.Exit(_lock);
       }
   }
   ```

2. **Monitor vs Mutex vs Semaphore**

   | Primitive | Scope | Cross-process | Use Case |
   |---|---|---|---|
   | `lock/Monitor` | Single process | No | In-process mutual exclusion |
   | `Mutex` | System-wide | Yes | Cross-process synchronization |
   | `Semaphore` | Single process | No | Limiting concurrent access |
   | `SemaphoreSlim` | Single process | No | Async-friendly semaphore |

   ```csharp
   // Mutex - cross-process
   public class SingleInstanceApp
   {
       private static Mutex? _mutex;

       public static bool IsAlreadyRunning()
       {
           _mutex = new Mutex(true, "MyApp_SingleInstance", out bool createdNew);
           return !createdNew;  // false jika instance sudah ada
       }
   }

   // SemaphoreSlim - async friendly
   public class RateLimiter
   {
       private readonly SemaphoreSlim _semaphore = new(3);  // Max 3 concurrent

       public async Task ProcessAsync(int id)
       {
           await _semaphore.WaitAsync();
           try
           {
               await DoWorkAsync(id);
           }
           finally
           {
               _semaphore.Release();
           }
       }
   }
   ```

3. **Concurrent Collections**

   ```csharp
   using System.Collections.Concurrent;

   // Thread-safe collections
   public class MessageQueue
   {
       private readonly ConcurrentQueue<string> _queue = new();
       private readonly ConcurrentDictionary<string, int> _processed = new();

       public void Enqueue(string message)
       {
           _queue.Enqueue(message);
       }

       public bool TryDequeue(out string? message)
       {
           return _queue.TryDequeue(out message);
       }

       public void RecordProcessed(string id)
       {
           _processed.AddOrUpdate(id, 1, (_, count) => count + 1);
       }
   }

   // Producer-Consumer with BlockingCollection
   public class ProducerConsumer
   {
       private readonly BlockingCollection<int> _collection = new(100);

       public void Start()
       {
           // Producer
           Task.Run(() =>
           {
               for (int i = 0; i < 1000; i++)
               {
                   _collection.Add(i);
               }
               _collection.CompleteAdding();
           });

           // Consumer
           Task.Run(() =>
           {
               foreach (var item in _collection.GetConsumingEnumerable())
               {
                   ProcessItem(item);
               }
           });
       }
   }
   ```

4. **Thread-Safe Patterns**

   ```csharp
   // Lazy<T> untuk lazy initialization
   public class Singleton
   {
       private static readonly Lazy<Singleton> _instance = new(() => new Singleton());
       public static Singleton Instance => _instance.Value;
   }

   // Interlocked untuk atomic operations
   public class AtomicCounter
   {
       private int _count;

       public void Increment() => Interlocked.Increment(ref _count);
       public void Decrement() => Interlocked.Decrement(ref _count);
       public int GetCount() => Interlocked.CompareExchange(ref _count, 0, 0);
   }

   // ReaderWriterLockSlim untuk read-heavy scenarios
   public class Cache
   {
       private readonly ReaderWriterLockSlim _lock = new();
       private readonly Dictionary<string, object> _cache = new();

       public object? Get(string key)
       {
           _lock.EnterReadLock();
           try
           {
               return _cache.TryGetValue(key, out var value) ? value : null;
           }
           finally
           {
               _lock.ExitReadLock();
           }
       }

       public void Set(string key, object value)
       {
           _lock.EnterWriteLock();
           try
           {
               _cache[key] = value;
           }
           finally
           {
               _lock.ExitWriteLock();
           }
       }
   }
   ```

5. **Deadlock Prevention**

   ```csharp
   // ❌ DEADLOCK RISK: Nested locks in different order
   public void Transfer(Account from, Account to, decimal amount)
   {
       lock (from)
       {
           lock (to)  // Different order in different calls = DEADLOCK
           {
               from.Balance -= amount;
               to.Balance += amount;
           }
       }
   }

   // ✅ SOLUTION: Always lock in consistent order
   public void TransferSafe(Account from, Account to, decimal amount)
   {
       Account first = from.Id < to.Id ? from : to;
       Account second = from.Id < to.Id ? to : from;

       lock (first)
       {
           lock (second)
           {
               from.Balance -= amount;
               to.Balance += amount;
           }
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan lock, Monitor, race conditions | Menjelaskan semua primitives, concurrent collections, deadlock prevention |
| Contoh | Contoh sederhana dengan lock | Contoh producer-consumer, rate limiting |

**Follow-up Questions:**
- "Bagaimana cara mendeteksi deadlock di production?"
- "Apa keuntungan SemaphoreSlim dibanding Semaphore untuk async code?"

**Red Flags:**
- ❌ Tidak memahami race conditions
- ❌ Tidak mengetahui concurrent collections
- ❌ Tidak bisa menjelaskan perbedaan Mutex vs lock

**Green Flags:**
- ✅ Menjelaskan ReaderWriterLockSlim untuk read-heavy scenarios
- ✅ Menyebutkan Interlocked untuk atomic operations
- ✅ Memberikan contoh deadlock prevention


---

### Q-FUND-015: CancellationToken dan Cooperative Cancellation

**Level:** Mid
**Topik:** Cancellation pattern, linked tokens, timeout handling

**Pertanyaan:**
Apa itu CancellationToken di .NET dan bagaimana cara mengimplementasikan cooperative cancellation dengan benar?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan CancellationToken, CancellationTokenSource, dan best practices untuk cancellation handling.

1. **CancellationToken Basics**

   CancellationToken adalah struct yang digunakan untuk signaling cancellation secara cooperative.

   ```csharp
   public class DataService
   {
       public async Task<List<string>> GetDataAsync(CancellationToken cancellationToken)
       {
           var results = new List<string>();

           for (int i = 0; i < 100; i++)
           {
               // Check for cancellation
               cancellationToken.ThrowIfCancellationRequested();

               // Or manual check
               if (cancellationToken.IsCancellationRequested)
               {
                   // Cleanup if needed
                   return results;
               }

               await FetchDataAsync(i, cancellationToken);
               results.Add($"Item {i}");
           }

           return results;
       }
   }

   // Usage
   using var cts = new CancellationTokenSource();
   var service = new DataService();

   // Cancel after 5 seconds
   cts.CancelAfter(TimeSpan.FromSeconds(5));

   try
   {
       var data = await service.GetDataAsync(cts.Token);
   }
   catch (OperationCanceledException)
   {
       Console.WriteLine("Operation was cancelled");
   }
   ```

2. **CancellationTokenSource**

   ```csharp
   // Create CTS with timeout
   using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(30));

   // Create CTS and cancel later
   using var cts2 = new CancellationTokenSource();

   // Register callback on cancellation
   cts2.Token.Register(() => Console.WriteLine("Cancelled!"));

   // Cancel programmatically
   cts2.Cancel();

   // Linked tokens - cancel if either source cancels
   using var linked = CancellationTokenSource.CreateLinkedTokenSource(
       timeoutToken.Token,
       userCancelToken.Token
   );
   ```

3. **Linked Tokens dan Timeout**

   ```csharp
   public async Task ProcessWithTimeoutAsync(
       CancellationToken userToken,
       TimeSpan timeout)
   {
       using var timeoutCts = new CancellationTokenSource(timeout);
       using var linkedCts = CancellationTokenSource.CreateLinkedTokenSource(
           userToken,
           timeoutCts.Token
       );

       try
       {
           await LongRunningOperationAsync(linkedCts.Token);
       }
       catch (OperationCanceledException) when (timeoutCts.IsCancellationRequested)
       {
           throw new TimeoutException("Operation timed out");
       }
       catch (OperationCanceledException) when (userToken.IsCancellationRequested)
       {
           // User cancelled - re-throw
           throw;
       }
   }
   ```

4. **Best Practices**

   ```csharp
   // ✅ GOOD: Pass CancellationToken to async methods
   public async Task<List<Data>> FetchAllAsync(CancellationToken cancellationToken)
   {
       return await _dbContext.Data
           .ToListAsync(cancellationToken);  // EF Core supports cancellation
   }

   // ✅ GOOD: Check cancellation in loops
   public async Task ProcessItemsAsync(List<Item> items, CancellationToken cancellationToken)
   {
       foreach (var item in items)
       {
           cancellationToken.ThrowIfCancellationRequested();
           await ProcessItemAsync(item, cancellationToken);
       }
   }

   // ✅ GOOD: Use Register for cleanup
   public async Task ProcessFileAsync(string path, CancellationToken cancellationToken)
   {
       var tempFile = Path.GetTempFileName();

       using var registration = cancellationToken.Register(() =>
       {
           if (File.Exists(tempFile))
               File.Delete(tempFile);
       });

       try
       {
           await WriteToFileAsync(tempFile, cancellationToken);
       }
       finally
       {
           registration.Unregister();
       }
   }

   // ❌ AVOID: Ignoring CancellationToken
   public async Task BadMethod(CancellationToken cancellationToken)
   {
       await Task.Delay(1000);  // Not passing token
       // If cancelled, still waits full second
   }

   // ✅ GOOD: Propagate CancellationToken
   public async Task GoodMethod(CancellationToken cancellationToken)
   {
       await Task.Delay(1000, cancellationToken);  // Early exit on cancel
   }
   ```

5. **HTTP Client Cancellation**

   ```csharp
   public class ApiClient
   {
       private readonly HttpClient _client;

       public async Task<ApiResponse> GetDataAsync(
           string endpoint,
           CancellationToken cancellationToken)
       {
           var response = await _client.GetAsync(endpoint, cancellationToken);
           response.EnsureSuccessStatusCode();

           var content = await response.Content.ReadAsStringAsync(cancellationToken);
           return JsonSerializer.Deserialize<ApiResponse>(content);
       }
   }

   // Controller with cancellation
   [HttpGet("{id}")]
   public async Task<ActionResult<Data>> GetData(
       int id,
       CancellationToken cancellationToken)  // Automatically bound from request
   {
       return await _service.GetDataAsync(id, cancellationToken);
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan CancellationToken, basic usage | Menjelaskan linked tokens, timeout handling, Register callbacks |
| Contoh | Contoh sederhana pass token | Contoh dari production dengan timeout dan cleanup |

**Follow-up Questions:**
- "Bagaimana cara menambahkan timeout ke existing CancellationToken?"
- "Apa perbedaan antara user-initiated cancellation vs timeout?"

**Red Flags:**
- ❌ Tidak memahami CancellationToken vs CancellationTokenSource
- ❌ Tidak mengetahui ThrowIfCancellationRequested()
- ❌ Mengabaikan CancellationToken di async methods

**Green Flags:**
- ✅ Menjelaskan linked tokens dan use cases
- ✅ Menyebutkan Register untuk cleanup callbacks
- ✅ Memberikan contoh dari production timeout handling


---

### Q-FUND-016: Record Types dan Pattern Matching

**Level:** Mid
**Topik:** C# 9+ features, immutable data, positional records

**Pertanyaan:**
Apa itu record types di C# dan apa keunggulannya dibanding class biasa? Jelaskan juga fitur pattern matching yang didukung.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan record types, immutability benefits, dan penggunaan pattern matching.

1. **Record Types Overview**

   Record adalah reference type dengan value-based equality dan immutability built-in.

   ```csharp
   // Positional record
   public record Person(string FirstName, string LastName, int Age);

   // Record dengan body
   public record Customer
   {
       public string Name { get; init; }
       public string Email { get; init; }
       public List<string> Orders { get; init; } = new();
   }

   // Usage
   var person = new Person("John", "Doe", 30);

   // Value-based equality
   var person2 = new Person("John", "Doe", 30);
   Console.WriteLine(person == person2);  // true (class would be false)

   // with expression untuk non-destructive mutation
   var olderPerson = person with { Age = 31 };
   ```

2. **Keunggulan Records**

   | Feature | Record | Class |
   |---|---|---|
   | Value equality | ✅ Built-in | ❌ Need override |
   | Immutability | ✅ init-only properties | ❌ Manual |
   | with expression | ✅ Supported | ❌ Not supported |
   | Deconstruction | ✅ Built-in for positional | ❌ Manual |
   | ToString | ✅ Auto-generated | ❌ Default |

   ```csharp
   public record Order(int Id, string Product, decimal Total);

   var order = new Order(1, "Laptop", 1500m);

   // Deconstruction
   var (id, product, total) = order;

   // ToString auto-generated
   Console.WriteLine(order);
   // Output: Order { Id = 1, Product = Laptop, Total = 1500 }

   // Non-destructive mutation
   var discountedOrder = order with { Total = 1350m };
   ```

3. **Pattern Matching**

   ```csharp
   // Property pattern
   public string Describe(Person person) => person switch
   {
       { Age: < 18 } => "Minor",
       { Age: >= 18 and < 65 } => "Adult",
       { Age: >= 65 } => "Senior",
       _ => "Unknown"
   };

   // Positional pattern (with deconstruction)
   public string Greet(Person person) => person switch
   {
       ("John", _, _) => "Hello John!",
       (_, "Doe", _) => "Hello a Doe!",
       var (first, last, _) => $"Hello {first} {last}"
   };

   // Tuple pattern
   public decimal CalculateDiscount(decimal total, bool isMember) =>
       (total, isMember) switch
       {
           (> 1000m, true) => 0.15m,
           (> 500m, true) => 0.10m,
           (_, true) => 0.05m,
           _ => 0m
       };

   // List pattern (C# 11+)
   public string DescribeNumbers(int[] numbers) => numbers switch
   {
       [] => "Empty",
       [var single] => $"Single: {single}",
       [var first, var second] => $"Two: {first} and {second}",
       [var first, .., var last] => $"First: {first}, Last: {last}",
       _ => "Many numbers"
   };
   ```

4. **Record vs Record Struct**

   ```csharp
   // Reference type record
   public record PersonRecord(string Name);

   // Value type record (C# 10+)
   public record struct PersonRecordStruct(string Name);

   // Positional record struct
   var personStruct = new PersonRecordStruct("Jane");

   // Key differences:
   // - record: Reference type, can be null, reference semantics
   // - record struct: Value type, cannot be null (unless nullable), value semantics
   ```

5. **Inheritance dengan Records**

   ```csharp
   public record Person(string FirstName, string LastName);
   public record Student(string FirstName, string LastName, string School) : Person(FirstName, LastName);

   var student = new Student("John", "Doe", "MIT");

   // Pattern matching dengan type check
   public string Describe(Person person) => person switch
   {
       Student { School: "MIT" } s => $"MIT student: {s.FirstName}",
       Student s => $"Student at {s.School}",
       Person p => $"Person: {p.FirstName}",
       null => "No person"
   };
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan record, immutability, with expression | Menjelaskan semua pattern types, record inheritance, when to use record vs class |
| Contoh | Contoh sederhana DTO dengan record | Contoh complex pattern matching, domain modeling |

**Follow-up Questions:**
- "Kapan sebaiknya menggunakan record dibanding class?"
- "Apa perbedaan record dan record struct?"

**Red Flags:**
- ❌ Tidak memahami value-based equality pada records
- ❌ Tidak mengetahui with expression
- ❌ Tidak bisa menjelaskan immutability benefit

**Green Flags:**
- ✅ Menjelaskan semua pattern matching types
- ✅ Menyebutkan positional records dan deconstruction
- ✅ Memberikan contoh dari domain modeling dengan records


---

### Q-FUND-017: Nullable Reference Types

**Level:** Mid
**Topik:** Null safety, compiler warnings, nullable annotations

**Pertanyaan:**
Apa itu Nullable Reference Types di C#? Bagaimana cara mengaktifkannya dan apa best practices untuk menggunakannya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan NRT, cara mengaktifkan, dan best practices untuk null safety.

1. **Apa itu Nullable Reference Types (NRT)**

   NRT adalah compiler feature yang membedakan nullable dan non-nullable reference types, memberikan warnings untuk potential null references.

   ```xml
   <!-- Enable in .csproj -->
   <PropertyGroup>
       <Nullable>enable</Nullable>
   </PropertyGroup>
   ```

   ```csharp
   // With NRT enabled
   public class Customer
   {
       // Non-nullable - MUST be assigned
       public string Name { get; set; }  // Warning if not assigned

       // Explicitly nullable
       public string? MiddleName { get; set; }  // OK to be null

       // Required property (C# 11+)
       public required string Email { get; set; }
   }

   // Compiler warnings
   Customer customer = new();
   customer.Name = "John";  // Warning: Name is non-nullable
   ```

2. **Nullable Annotations**

   ```csharp
   public class OrderService
   {
       // Non-nullable parameter - caller must provide non-null value
       public Order CreateOrder(string customerName)
       {
           // customerName is guaranteed non-null (by contract)
           return new Order(customerName);
       }

       // Nullable parameter - method must handle null
       public Order? FindOrder(int? orderId)
       {
           if (orderId is null)
               return null;  // OK - return type is nullable

           return _repository.Find(orderId.Value);
       }

       // Nullable return type
       public string? GetMiddleName(Customer customer)
       {
           return customer.MiddleName;  // Could be null
       }
   }
   ```

3. **Null-Forgiving Operator (!)**

   ```csharp
   public class CustomerService
   {
       // Use ! to suppress warning when you KNOW value is not null
       public Customer GetCustomer(int id)
       {
           Customer? customer = _repository.Find(id);
           return customer!;  // "I know it's not null, trust me"
       }

       // Better approach - proper null handling
       public Customer GetCustomerSafe(int id)
       {
           Customer? customer = _repository.Find(id);
           return customer ?? throw new NotFoundException($"Customer {id} not found");
       }
   }

   // ⚠️ WARNING: Don't overuse !
   // ❌ BAD: Suppressing warnings without validation
   string? input = GetUserInput();
   Process(input!);  // Dangerous if input actually null

   // ✅ GOOD: Validate first
   if (!string.IsNullOrEmpty(input))
   {
       Process(input);  // Compiler knows input is not null here
   }
   ```

4. **Best Practices**

   ```csharp
   // ✅ GOOD: Use null-conditional operators
   public string? GetCustomerName(int id)
   {
       var customer = _repository.Find(id);
       return customer?.Name;
   }

   // ✅ GOOD: Use null-coalescing operators
   public string GetDisplayName(Customer? customer)
   {
       return customer?.Name ?? "Unknown";
   }

   // ✅ GOOD: Pattern matching for null checks
   public string Process(string? input)
   {
       if (input is null)
           return "No input";

       if (input is { Length: > 10 })
           return "Long input";

       return input;
   }

   // ✅ GOOD: Use Guard clauses
   public void ProcessOrder(Order? order)
   {
       ArgumentNullException.ThrowIfNull(order);

       // Now order is known to be non-null
       var name = order.CustomerName;
   }

   // ✅ GOOD: Annotate generic constraints
   public T? Find<T>(int id) where T : class
   {
       return _context.Set<T>().Find(id);
   }

   public T FindOrDefault<T>(int id, T defaultValue) where T : notnull
   {
       return _context.Set<T>().Find(id) ?? defaultValue;
   }
   ```

5. **Common Patterns**

   ```csharp
   // Partial properties with backing field
   public class Product
   {
       private string? _description;
       public string Description
       {
           get => _description ?? string.Empty;
           set => _description = value;
       }
   }

   // Collections should not be null
   public class Order
   {
       public List<OrderItem> Items { get; set; } = new();  // Never null
   }

   // Nullable in method signatures
   public class CustomerRepository
   {
       // Return null if not found
       public Customer? Find(int id) => _context.Customers.Find(id);

       // Throw if not found
       public Customer Get(int id) =>
           Find(id) ?? throw new NotFoundException();
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan NRT, nullable annotations, ! operator | Menjelaskan generic constraints, best practices, library API design |
| Contoh | Contoh sederhana properties dan parameters | Contoh dari library design, null-safe patterns |

**Follow-up Questions:**
- "Kapan sebaiknya menggunakan null-forgiving operator (!)?"
- "Bagaimana cara membuat library yang NRT-friendly?"

**Red Flags:**
- ❌ Tidak memahami perbedaan `string` dan `string?` dengan NRT
- ❌ Menggunakan ! untuk suppress semua warnings tanpa validasi
- ❌ Tidak mengetahui null-conditional operators

**Green Flags:**
- ✅ Menjelaskan null-conditional dan null-coalescing operators
- ✅ Menyebutkan Guard clauses dan proper null handling
- ✅ Memberikan contoh dari production code dengan NRT


---

### Q-FUND-018: Source Generators

**Level:** Senior
**Topik:** Compile-time code generation, incremental generators

**Pertanyaan:**
Apa itu Source Generators di .NET? Bagaimana cara kerjanya dan kapan sebaiknya menggunakannya dibandingkan reflection?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami compile-time code generation, use cases, dan trade-offs vs reflection.

1. **Apa itu Source Generators**

   Source Generators adalah compiler extension yang menghasilkan code selama compile time, terintegrasi dengan Roslyn compiler.

   ```csharp
   // Source Generator menghasilkan code saat compile
   // Hasilnya menjadi bagian dari assembly, bukan runtime generation

   // Contoh: Auto-generate DTO mappings
   [AutoMapper]
   public partial class UserMapper
   {
       // Partial method - implementation generated by source generator
       public partial UserDto Map(User entity);
   }

   // Generated code (invisible to developer, visible to compiler):
   // public partial class UserMapper
   // {
   //     public partial UserDto Map(User entity) => new()
   //     {
   //         Id = entity.Id,
   //         Name = entity.Name,
   //         Email = entity.Email
   //     };
   // }
   ```

2. **Cara Kerja Source Generators**

   ```csharp
   // Source Generator adalah class yang implement IIncrementalGenerator
   [Generator]
   public class AutoMapperGenerator : IIncrementalGenerator
   {
       public void Initialize(IncrementalGeneratorInitializationContext context)
       {
           // Find classes with [AutoMapper] attribute
           var mapperClasses = context.SyntaxProvider
               .CreateSyntaxProvider(
                   predicate: static (s, _) => IsClassWithAttribute(s),
                   transform: static (ctx, _) => GetClassSymbol(ctx))
               .Where(static m => m is not null);

           // Generate source code
           context.RegisterSourceOutput(mapperClasses,
               static (spc, source) => Execute(spc, source));
       }

       private static void Execute(SourceProductionContext context, INamedTypeSymbol? mapperClass)
       {
           // Generate C# code string
           string sourceCode = GenerateMappingCode(mapperClass);

           // Add to compilation
           context.AddSource($"{mapperClass.Name}.g.cs", sourceCode);
       }
   }
   ```

3. **Incremental Generators (Recommended)**

   ```csharp
   // Incremental generators cache results and only re-run when inputs change
   // Much better performance than non-incremental generators

   [Generator]
   public class DtoGenerator : IIncrementalGenerator
   {
       public void Initialize(IncrementalGeneratorInitializationContext context)
       {
           // Step 1: Create a provider that finds all [GenerateDto] classes
           IncrementalValuesProvider<ClassDeclarationSyntax> classDeclarations =
               context.SyntaxProvider
                   .CreateSyntaxProvider(
                       predicate: static (node, _) => node is ClassDeclarationSyntax,
                       transform: static (ctx, _) => (ClassDeclarationSyntax)ctx.Node)
                   .Where(static c => HasGenerateDtoAttribute(c));

           // Step 2: Transform to symbols (only when changed)
           IncrementalValuesProvider<INamedTypeSymbol> classSymbols =
               classDeclarations.Select(static (c, ct) => GetSymbol(c, ct));

           // Step 3: Generate output (only for changed inputs)
           context.RegisterSourceOutput(classSymbols, static (spc, symbol) =>
           {
               string code = GenerateDto(symbol);
               spc.AddSource($"{symbol.Name}Dto.g.cs", code);
           });
       }
   }
   ```

4. **Use Cases yang Tepat**

   | Use Case | Example |
   |---|---|
   | Auto-generate DTO mappings | AutoMapper source generator |
   | Serialization code | System.Text.Json source generator |
   | Dependency Injection registration | Microsoft.Extensions.DependencyInjection |
   | Regex compilation | RegexGenerator for compiled regex |
   | AOT/Trimming support | Native AOT compatible code |

   ```csharp
   // Example: System.Text.Json source generator
   [JsonSerializable(typeof(User))]
   [JsonSerializable(typeof(List<Order>))]
   public partial class MyJsonContext : JsonSerializerContext
   {
   }

   // Usage - no reflection, AOT-friendly
   var json = JsonSerializer.Serialize(user, MyJsonContext.Default.User);

   // Example: Regex source generator
   [GeneratedRegex(@"\b\d{4}-\d{2}-\d{2}\b")]
   private static partial Regex DateRegex();

   // Usage
   var matches = DateRegex().Matches(input);  // Compiled, fast

   // Example: DI registration (Microsoft DI)
   [ServiceRegistration(ServiceLifetime.Scoped)]
   public class UserService
   {
   }

   // Source generator creates registration code automatically
   ```

5. **Source Generators vs Reflection**

   | Aspect | Source Generators | Reflection |
   |---|---|---|
   | Performance | Excellent (compile-time) | Slower (runtime) |
   | AOT/Trimming | ✅ Compatible | ❌ Problematic |
   | Type safety | ✅ Compile-time checked | ❌ Runtime errors |
   | Debugging | Easy (visible code) | Harder |
   | Flexibility | Limited (compile-time info) | High (runtime inspection) |

   ```csharp
   // REFLECTION approach (runtime)
   public class ReflectionMapper
   {
       public TTarget Map<TSource, TTarget>(TSource source)
       {
           var target = Activator.CreateInstance<TTarget>();
           foreach (var sourceProp in typeof(TSource).GetProperties())
           {
               var targetProp = typeof(TTarget).GetProperty(sourceProp.Name);
               targetProp?.SetValue(target, sourceProp.GetValue(source));
           }
           return target;
       }
   }
   // Issues: Slow, not AOT-friendly, runtime errors possible

   // SOURCE GENERATOR approach (compile-time)
   public partial class GeneratedMapper
   {
       public partial TTarget Map<TSource, TTarget>(TSource source);
   }
   // Generated at compile time:
   // public partial class GeneratedMapper
   // {
   //     public partial UserDto Map<User, UserDto>(User source) => new()
   //     {
   //         Id = source.Id,
   //         Name = source.Name
   //     };
   // }
   // Benefits: Fast, AOT-friendly, compile-time type checking
   ```

6. **Best Practices**

   ```csharp
   // ✅ DO: Generate partial classes/methods
   public partial class MyMapper  // User can add additional methods
   {
       public partial TDto Map<TEntity, TDto>(TEntity entity);
   }

   // ✅ DO: Use attributes to mark generation targets
   [AutoGenerate]
   public class CustomerDto
   {
       public int Id { get; set; }
       public string Name { get; set; }
   }

   // ✅ DO: Provide diagnostic errors for invalid usage
   context.ReportDiagnostic(Diagnostic.Create(
       new DiagnosticDescriptor("MG001", "Invalid usage", "...", "Usage", DiagnosticSeverity.Error, true),
       location));

   // ❌ DON'T: Generate excessive code that slows compilation
   // ❌ DON'T: Use for simple scenarios where manual code is fine
   // ❌ DON'T: Depend on runtime state (generators run at compile time)
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami compile-time generation, incremental generators, Roslyn integration |
| Contoh | Memberikan contoh dari library yang menggunakan SG (System.Text.Json, MediatR) |
| Tools | Menyebutkan Roslyn Analyzer, VS extension development |

**Follow-up Questions:**
- "Bagaimana cara debug source generator?"
- "Apa keuntungan incremental generator dibanding generator biasa?"

**Red Flags:**
- ❌ Tidak memahami compile-time vs runtime code generation
- ❌ Tidak bisa menjelaskan kapan SG lebih baik dari reflection
- ❌ Tidak mengetahui AOT/trimming benefits

**Green Flags:**
- ✅ Menjelaskan incremental generators dan caching
- ✅ Menyebutkan System.Text.Json source generator sebagai contoh
- ✅ Memberikan contoh dari production use atau library development


---

## 3. Clean Architecture & Design Patterns

### Q-ARCH-001: SOLID Principles Overview

**Level:** Mid
**Topik:** Kelima prinsip SOLID dengan contoh konkret

**Pertanyaan:**
Jelaskan kelima prinsip SOLID dan berikan contoh pelanggaran serta perbaikan untuk masing-masing prinsip.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan setiap prinsip SOLID dengan contoh kode yang menunjukkan pelanggaran dan perbaikan.

1. **Single Responsibility Principle (SRP)**

   Sebuah class harus memiliki satu alasan untuk berubah.

   ```csharp
   // ❌ PELANGGARAN: Class dengan multiple responsibilities
   public class UserService
   {
       public void CreateUser(User user) { /* ... */ }
       public void ValidateUser(User user) { /* ... */ }
       public void SendEmail(User user) { /* ... */ }
       public void LogActivity(User user) { /* ... */ }
   }

   // ✅ PERBAIKAN: Pisahkan responsibilities
   public class UserService
   {
       private readonly IUserValidator _validator;
       private readonly IEmailService _emailService;
       private readonly IActivityLogger _logger;

       public UserService(IUserValidator validator, IEmailService emailService, IActivityLogger logger)
       {
           _validator = validator;
           _emailService = emailService;
           _logger = logger;
       }

       public void CreateUser(User user)
       {
           _validator.Validate(user);
           // Create user logic
           _emailService.SendWelcomeEmail(user);
           _logger.LogUserCreated(user);
       }
   }
   ```

2. **Open/Closed Principle (OCP)**

   Software entities harus terbuka untuk ekstensi, tertutup untuk modifikasi.

   ```csharp
   // ❌ PELANGGARAN: Harus modify class untuk tambah discount type
   public class DiscountCalculator
   {
       public decimal Calculate(string customerType, decimal amount)
       {
           if (customerType == "Regular") return amount * 0.1m;
           if (customerType == "Premium") return amount * 0.2m;
           if (customerType == "VIP") return amount * 0.3m;
           return 0;
       }
   }

   // ✅ PERBAIKAN: Open for extension via abstraction
   public interface IDiscountStrategy
   {
       decimal Calculate(decimal amount);
   }

   public class RegularDiscount : IDiscountStrategy
   {
       public decimal Calculate(decimal amount) => amount * 0.1m;
   }

   public class PremiumDiscount : IDiscountStrategy
   {
       public decimal Calculate(decimal amount) => amount * 0.2m;
   }

   public class DiscountCalculator
   {
       private readonly Dictionary<string, IDiscountStrategy> _strategies;

       public DiscountCalculator(IEnumerable<IDiscountStrategy> strategies)
       {
           _strategies = strategies.ToDictionary(s => s.GetType().Name.Replace("Discount", ""));
       }

       public decimal Calculate(string customerType, decimal amount)
       {
           return _strategies.TryGetValue(customerType, out var strategy)
               ? strategy.Calculate(amount)
               : 0;
       }
   }
   ```

3. **Liskov Substitution Principle (LSP)**

   Subtype harus bisa menggantikan base type tanpa mengubah kebenaran program.

   ```csharp
   // ❌ PELANGGARAN: Penguin tidak bisa fly
   public class Bird
   {
       public virtual void Fly() { /* fly logic */ }
   }

   public class Penguin : Bird
   {
       public override void Fly() => throw new NotSupportedException("Penguins can't fly!");
   }

   // ✅ PERBAIKAN: Pisahkan abstraction
   public abstract class Bird { }

   public interface IFlyingBird
   {
       void Fly();
   }

   public class Sparrow : Bird, IFlyingBird
   {
       public void Fly() { /* fly logic */ }
   }

   public class Penguin : Bird { }  // No Fly method
   ```

4. **Interface Segregation Principle (ISP)**

   Client tidak boleh dipaksa bergantung pada interface yang tidak digunakan.

   ```csharp
   // ❌ PELANGGARAN: Interface terlalu besar
   public interface IWorker
   {
       void Work();
       void Eat();
       void Sleep();
   }

   public class Robot : IWorker
   {
       public void Work() { /* ... */ }
       public void Eat() => throw new NotSupportedException();
       public void Sleep() => throw new NotSupportedException();
   }

   // ✅ PERBAIKAN: Pecah menjadi smaller interfaces
   public interface IWorkable { void Work(); }
   public interface IFeedable { void Eat(); }
   public interface ISleepable { void Sleep(); }

   public class Human : IWorkable, IFeedable, ISleepable { /* ... */ }
   public class Robot : IWorkable { /* ... */ }
   ```

5. **Dependency Inversion Principle (DIP)**

   High-level modules tidak boleh bergantung pada low-level modules.

   ```csharp
   // ❌ PELANGGARAN: Concrete dependency
   public class NotificationService
   {
       private readonly SmtpEmailSender _emailSender = new();
   }

   // ✅ PERBAIKAN: Bergantung pada abstraction
   public interface IMessageSender { void Send(string message); }

   public class NotificationService
   {
       private readonly IEnumerable<IMessageSender> _senders;

       public NotificationService(IEnumerable<IMessageSender> senders)
       {
           _senders = senders;
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan setiap prinsip dengan contoh | Menjelaskan trade-offs, kapan bisa dilanggar |
| Contoh | Contoh textbook sederhana | Contoh dari production refactoring |

**Follow-up Questions:**
- "Apakah ada situasi di mana melanggar SOLID principles justified?"
- "Bagaimana cara memperkenalkan SOLID ke tim yang belum familiar?"

**Red Flags:**
- ❌ Tidak bisa menyebutkan kelima prinsip
- ❌ Tidak bisa memberikan contoh konkret

**Green Flags:**
- ✅ Memberikan contoh dari production refactoring
- ✅ Menjelaskan trade-offs dan pragmatic considerations


---

### Q-ARCH-002: Dependency Injection Deep Dive

**Level:** Mid
**Topik:** DI patterns, service lifetime, anti-patterns

**Pertanyaan:**
Jelaskan berbagai service lifetime di .NET DI container (Singleton, Scoped, Transient). Apa perbedaannya dan kapan menggunakan masing-masing? Jelaskan juga common anti-patterns.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami lifetime hierarchy, implikasi ke state management, dan anti-patterns yang umum.

1. **Service Lifetime Explained**

   | Lifetime | Instance Creation | Use Case |
   |---|---|---|
   | **Singleton** | Satu instance seumur aplikasi | Stateless services, caching, configuration |
   | **Scoped** | Satu instance per scope (request) | DbContext, Unit of Work, user session data |
   | **Transient** | Instance baru setiap request | Lightweight stateless services, validators |

   ```csharp
   // Registration
   services.AddSingleton<ICacheService, CacheService>();
   services.AddScoped<IUserService, UserService>();
   services.AddTransient<IValidator, OrderValidator>();

   // Singleton: Satu instance untuk seluruh application lifetime
   public class CacheService : ICacheService
   {
       private readonly ConcurrentDictionary<string, object> _cache = new();
       // Shared across all requests - must be thread-safe
   }

   // Scoped: Satu instance per HTTP request
   public class UserService : IUserService
   {
       private readonly DbContext _context;
       private readonly Guid _instanceId = Guid.NewGuid();

       public UserService(DbContext context) => _context = context;
       // _instanceId sama dalam satu request, berbeda antar request
   }
   ```

2. **Constructor Injection (Recommended)**

   ```csharp
   public class OrderService : IOrderService
   {
       private readonly IOrderRepository _orderRepository;
       private readonly IPaymentService _paymentService;
       private readonly ILogger<OrderService> _logger;

       public OrderService(
           IOrderRepository orderRepository,
           IPaymentService paymentService,
           ILogger<OrderService> logger)
       {
           _orderRepository = orderRepository ?? throw new ArgumentNullException(nameof(orderRepository));
           _paymentService = paymentService ?? throw new ArgumentNullException(nameof(paymentService));
           _logger = logger ?? throw new ArgumentNullException(nameof(logger));
       }
   }
   ```

3. **Service Locator Anti-Pattern**

   ```csharp
   // ❌ ANTI-PATTERN: Service Locator
   public class BadService
   {
       public void DoWork()
       {
           var repository = ServiceLocator.GetService<IRepository>();
           repository.Save();
       }
   }

   // ✅ CORRECT: Constructor Injection
   public class GoodService
   {
       private readonly IRepository _repository;

       public GoodService(IRepository repository) => _repository = repository;
       public void DoWork() => _repository.Save();
   }
   ```

4. **Common Anti-Patterns**

   - **God Object via DI**: Constructor dengan 20+ dependencies - violates SRP
   - **Circular Dependency**: ServiceA depend ServiceB, ServiceB depend ServiceA
   - **Ambient Context**: Global static state yang hidden dependency

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan lifetime dan constructor injection | Menjelaskan captive dependency, circular resolution |
| Contoh | Contoh sederhana registration | Contoh debugging DI issues |

**Follow-up Questions:**
- "Bagaimana cara men-debug DI container issues?"
- "Apa yang terjadi jika Scoped service di-inject ke Singleton?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Singleton/Scoped/Transient
- ❌ Menggunakan Service Locator pattern

**Green Flags:**
- ✅ Menjelaskan captive dependency dengan contoh
- ✅ Menyebutkan Scrutor untuk assembly scanning


---

### Q-ARCH-003: Captive Dependency

**Level:** Senior
**Topik:** Service lifetime mismatch, detection, solutions

**Pertanyaan:**
Apa itu captive dependency dalam Dependency Injection? Mengapa ini menjadi masalah dan bagaimana cara mendeteksi serta mengatasinya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami implikasi service lifetime mismatch terhadap resource management dan correctness.

1. **Definisi Captive Dependency**

   Captive dependency terjadi ketika service dengan lifetime lebih panjang depend pada service dengan lifetime lebih pendek.

   ```csharp
   // MASALAH: Captive Dependency
   services.AddSingleton<IReportGenerator, ReportGenerator>();
   services.AddScoped<IDatabaseContext, DatabaseContext>();

   public class ReportGenerator : IReportGenerator
   {
       private readonly IDatabaseContext _db;  // CAPTIVE DEPENDENCY!

       public ReportGenerator(IDatabaseContext db) => _db = db;
       // DatabaseContext akan tertawan seumur aplikasi!
   }
   ```

2. **Mengapa Captive Dependency Bermasalah?**

   | Masalah | Penjelasan |
   |---|---|
   | Resource Leak | Scoped service tidak di-dispose sampai app shutdown |
   | State Corruption | Scoped service di-share antar request |
   | Thread Safety | Scoped service mungkin tidak thread-safe |
   | Database Connection | DbContext tidak di-dispose, connection pool exhaustion |

3. **Solusi: Factory Pattern**

   ```csharp
   // SOLUSI: Inject IServiceScopeFactory
   public class ReportGenerator : IReportGenerator
   {
       private readonly IServiceScopeFactory _scopeFactory;

       public ReportGenerator(IServiceScopeFactory scopeFactory) => _scopeFactory = scopeFactory;

       public Report Generate()
       {
           using var scope = _scopeFactory.CreateScope();
           var db = scope.ServiceProvider.GetRequiredService<IDatabaseContext>();
           return db.Reports.ToList();
       }
   }
   ```

4. **Detection dan Validation**

   ```csharp
   // ASP.NET Core built-in validation
   builder.Services.AddOptions<ServiceProviderOptions>()
       .Configure(options =>
       {
           options.ValidateScopes = true;  // Detect captive dependency
           options.ValidateOnBuild = true;  // Detect missing registrations
       });
   ```

5. **Lifetime Hierarchy**

   ```
   Valid: Transient → Singleton/Scoped/Transient
   Valid: Scoped → Singleton/Scoped
   Valid: Singleton → Singleton only
   
   INVALID: Singleton → Scoped (Captive!)
   INVALID: Singleton → Transient (Captive!)
   INVALID: Scoped → Transient (Captive!)
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami lifetime hierarchy, factory patterns |
| Contoh | Memberikan contoh dari production debugging |
| Tools | Menyebutkan ValidateScopes, Scrutor |

**Follow-up Questions:**
- "Bagaimana cara memvalidasi DI configuration di production startup?"
- "Apa trade-off antara factory pattern vs changing service lifetime?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Singleton/Scoped/Transient
- ❌ Tidak bisa menjelaskan mengapa captive dependency berbahaya

**Green Flags:**
- ✅ Menjelaskan IServiceScopeFactory dan Func<T> factory
- ✅ Menyebutkan ValidateScopes configuration


---

### Q-ARCH-004: Repository Pattern

**Level:** Mid
**Topik:** Data access abstraction, generic repository, unit of work

**Pertanyaan:**
Apa itu Repository Pattern dan Unit of Work? Kapan sebaiknya menggunakannya dan kapan bisa di-skip?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami tujuan repository sebagai abstraction layer dan trade-offs dalam penggunaannya.

1. **Tujuan Repository Pattern**

   - Abstraction layer antara domain logic dan data access
   - Memisahkan concerns: business logic tidak tahu detail persistence
   - Memudahkan testing dengan mock repository
   - Centralized query logic

   ```csharp
   // Dengan Repository - Controller tidak tahu detail EF Core
   public class OrdersController : ControllerBase
   {
       private readonly IOrderRepository _orderRepository;

       public async Task<ActionResult<Order>> GetOrder(int id)
       {
           var order = await _orderRepository.GetOrderWithItemsAsync(id);
           return Ok(order);
       }
   }
   ```

2. **Generic Repository Implementation**

   ```csharp
   public interface IRepository<T> where T : class, IEntity
   {
       Task<T?> GetByIdAsync(int id);
       Task<IEnumerable<T>> GetAllAsync();
       Task AddAsync(T entity);
       Task UpdateAsync(T entity);
       Task DeleteAsync(int id);
       Task<IEnumerable<T>> FindAsync(Expression<Func<T, bool>> predicate);
   }

   public class Repository<T> : IRepository<T> where T : class, IEntity
   {
       protected readonly DbContext _context;
       protected readonly DbSet<T> _dbSet;

       public Repository(DbContext context)
       {
           _context = context;
           _dbSet = context.Set<T>();
       }

       public async Task<T?> GetByIdAsync(int id)
           => await _dbSet.FirstOrDefaultAsync(e => e.Id == id);

       public async Task AddAsync(T entity)
       {
           await _dbSet.AddAsync(entity);
           await _context.SaveChangesAsync();
       }
   }
   ```

3. **Unit of Work Pattern**

   ```csharp
   public interface IUnitOfWork : IDisposable
   {
       IOrderRepository Orders { get; }
       ICustomerRepository Customers { get; }
       Task<int> SaveChangesAsync();
   }

   public class UnitOfWork : IUnitOfWork
   {
       private readonly DbContext _context;

       public IOrderRepository Orders => new OrderRepository(_context);
       public ICustomerRepository Customers => new CustomerRepository(_context);

       public async Task<int> SaveChangesAsync() => await _context.SaveChangesAsync();
       public void Dispose() => _context.Dispose();
   }
   ```

4. **Kapan Repository TIDAK Diperlukan**

   - Simple CRUD tanpa complex business logic
   - EF Core sudah menyediakan abstraction (DbSet adalah repository)
   - Repository hanya menyembunyikan EF Core features tanpa menambah value

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan repository dan unit of work | Menjelaskan kapan TIDAK perlu repository |
| Contoh | Implementasi generic repository | Contoh refactoring dari repository ke CQRS |

**Follow-up Questions:**
- "Apakah repository pattern masih relevan dengan EF Core?"
- "Bagaimana repository berhubungan dengan CQRS?"

**Red Flags:**
- ❌ Tidak memahami tujuan repository sebagai abstraction
- ❌ Selalu membuat repository tanpa mempertimbangkan kebutuhan

**Green Flags:**
- ✅ Menjelaskan kapan repository tidak diperlukan
- ✅ Menyebutkan trade-offs abstraction vs complexity


---

### Q-ARCH-005: CQRS Pattern

**Level:** Senior
**Topik:** Command Query Separation, MediatR, eventual consistency

**Pertanyaan:**
Apa itu CQRS (Command Query Responsibility Segregation)? Bagaimana implementasinya dengan MediatR dan apa trade-offs yang perlu dipertimbangkan?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami CQRS concept, implementasi dengan MediatR, dan trade-offs termasuk eventual consistency.

1. **CQRS Concept**

   CQRS memisahkan operasi yang membaca data (Query) dari operasi yang mengubah data (Command).

   | Aspect | Command | Query |
   |---|---|---|
   | Purpose | Create, Update, Delete | Read data |
   | Return | void or success/failure | Data (DTO) |
   | Side Effects | Yes | No |

2. **Implementasi dengan MediatR**

   ```csharp
   // Command - mengubah state
   public record CreateOrderCommand(
       int CustomerId,
       List<OrderItemDto> Items
   ) : IRequest<int>;

   public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, int>
   {
       private readonly AppDbContext _context;

       public async Task<int> Handle(CreateOrderCommand request, CancellationToken ct)
       {
           var order = new Order { CustomerId = request.CustomerId, /* ... */ };
           _context.Orders.Add(order);
           await _context.SaveChangesAsync(ct);
           return order.Id;
       }
   }

   // Query - membaca data
   public record GetOrderByIdQuery(int OrderId) : IRequest<OrderDto?>;

   public class GetOrderByIdQueryHandler : IRequestHandler<GetOrderByIdQuery, OrderDto?>
   {
       private readonly AppDbContext _context;

       public async Task<OrderDto?> Handle(GetOrderByIdQuery request, CancellationToken ct)
       {
           return await _context.Orders
               .Where(o => o.Id == request.OrderId)
               .Select(o => new OrderDto { /* ... */ })
               .FirstOrDefaultAsync(ct);
       }
   }
   ```

3. **Pipeline Behaviors**

   ```csharp
   public class ValidationBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
       where TRequest : IRequest<TResponse>
   {
       private readonly IEnumerable<IValidator<TRequest>> _validators;

       public async Task<TResponse> Handle(
           TRequest request,
           RequestHandlerDelegate<TResponse> next,
           CancellationToken ct)
       {
           var failures = _validators
               .Select(v => v.Validate(request))
               .SelectMany(r => r.Errors)
               .Where(f => f != null)
               .ToList();

           if (failures.Any()) throw new ValidationException(failures);
           return await next();
       }
   }
   ```

4. **Trade-offs CQRS**

   | Keuntungan | Kerugian |
   |---|---|
   | Separation of concerns | Increased complexity |
   | Scalability (separate read/write) | Eventual consistency |
   | Optimized read models | More code to maintain |
   | Easier to test | Potential over-engineering |

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami CQRS, MediatR, eventual consistency |
| Contoh | Memberikan contoh dari production architecture |
| Tools | Menyebutkan MediatR, FluentValidation |

**Follow-up Questions:**
- "Kapan CQRS over-engineering dan kapan justified?"
- "Bagaimana cara handle eventual consistency di UI?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Command dan Query
- ❌ Tidak bisa menjelaskan trade-offs

**Green Flags:**
- ✅ Menjelaskan eventual consistency dan implications
- ✅ Menyebutkan pipeline behaviors dan validation


---

### Q-ARCH-006: MediatR dan Mediator Pattern

**Level:** Mid
**Topik:** Decoupling, pipeline behavior, request/response

**Pertanyaan:**
Bagaimana MediatR bekerja sebagai implementasi Mediator Pattern? Jelaskan berbagai jenis request yang didukung dan bagaimana pipeline behaviors digunakan untuk cross-cutting concerns.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami mediator pattern, MediatR request types, dan cara menggunakan pipeline behaviors.

1. **Mediator Pattern Concept**

   Mediator pattern mendefinisikan object yang mengenkapsulasi interaksi antara sekumpulan objects, mencegah mereka saling referensi secara langsung.

   ```csharp
   // TANPA Mediator - Services saling depend
   public class OrderController
   {
       private readonly IOrderService _orderService;
       private readonly IInventoryService _inventoryService;
       private readonly INotificationService _notificationService;
       // Controller harus tahu semua dependencies
   }

   // DENGAN Mediator - Controller hanya tahu MediatR
   public class OrderController
   {
       private readonly IMediator _mediator;

       public async Task<ActionResult> CreateOrder(CreateOrderCommand command)
       {
           var orderId = await _mediator.Send(command);
           return Ok(orderId);
       }
   }
   ```

2. **Request Types di MediatR**

   | Type | Return Value | Use Case |
   |---|---|---|
   | `IRequest<T>` | Returns T | Command dengan result, Query |
   | `IRequest` | No return | Fire-and-forget command |
   | `IStreamRequest<T>` | IAsyncEnumerable<T> | Streaming data |

   ```csharp
   // Request dengan return value
   public record GetCustomerQuery(int Id) : IRequest<CustomerDto?>;

   // Request tanpa return value
   public record DeleteCustomerCommand(int Id) : IRequest;

   // Streaming request
   public record GetOrderStreamQuery(DateTime From) : IStreamRequest<OrderDto>;
   ```

3. **Pipeline Behaviors untuk Cross-Cutting Concerns**

   ```csharp
   // Logging behavior
   public class LoggingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
       where TRequest : IRequest<TResponse>
   {
       private readonly ILogger<LoggingBehavior<TRequest, TResponse>> _logger;

       public async Task<TResponse> Handle(
           TRequest request,
           RequestHandlerDelegate<TResponse> next,
           CancellationToken ct)
       {
           _logger.LogInformation("Handling {RequestType}", typeof(TRequest).Name);
           try
           {
               var response = await next();
               _logger.LogInformation("Handled {RequestType}", typeof(TRequest).Name);
               return response;
           }
           catch (Exception ex)
           {
               _logger.LogError(ex, "Error handling {RequestType}", typeof(TRequest).Name);
               throw;
           }
       }
   }

   // Registration
   services.AddMediatR(cfg =>
   {
       cfg.RegisterServicesFromAssembly(typeof(Program).Assembly);
       cfg.AddBehavior(typeof(IPipelineBehavior<,>), typeof(LoggingBehavior<,>));
       cfg.AddBehavior(typeof(IPipelineBehavior<,>), typeof(ValidationBehavior<,>));
   });
   ```

4. **Notification untuk Event-Based Communication**

   ```csharp
   // Event definition
   public record OrderCreatedEvent(int OrderId, string CustomerEmail) : INotification;

   // Multiple handlers
   public class SendOrderConfirmationHandler : INotificationHandler<OrderCreatedEvent>
   {
       public async Task Handle(OrderCreatedEvent notification, CancellationToken ct)
       {
           // Send email
       }
   }

   public class UpdateInventoryHandler : INotificationHandler<OrderCreatedEvent>
   {
       public async Task Handle(OrderCreatedEvent notification, CancellationToken ct)
       {
           // Update inventory
       }
   }

   // Publish event
   await _mediator.Publish(new OrderCreatedEvent(order.Id, customer.Email));
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan request types, basic pipeline | Menjelangkan custom behaviors, performance implications |
| Contoh | Contoh sederhana handler | Contoh dari production dengan multiple behaviors |

**Follow-up Questions:**
- "Bagaimana urutan eksekusi pipeline behaviors?"
- "Apa perbedaan Send() dan Publish() di MediatR?"

**Red Flags:**
- ❌ Tidak memahami perbedaan IRequest dan INotification
- ❌ Tidak bisa menjelaskan pipeline behavior

**Green Flags:**
- ✅ Menjelaskan ordering pipeline behaviors
- ✅ Menyebutkan logging, validation, caching behaviors


---

### Q-ARCH-007: Clean Architecture Layers

**Level:** Mid
**Topik:** Domain, Application, Infrastructure, Presentation

**Pertanyaan:**
Jelaskan struktur layer di Clean Architecture. Apa tanggung jawab masing-masing layer dan bagaimana dependency direction yang benar?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami separation of concerns dan dependency inversion antar layers.

1. **Layer Structure Overview**

   ```mermaid
   flowchart TB
       subgraph "Clean Architecture"
           P[Presentation Layer]
           A[Application Layer]
           I[Infrastructure Layer]
           D[Domain Layer]
       end

       P --> A
       A --> D
       I --> D
       I --> A

       style D fill:#2e7d32,color:#fff
       style A fill:#1565c0,color:#fff
       style I fill:#ef6c00,color:#fff
       style P fill:#7b1fa2,color:#fff
   ```

2. **Domain Layer (Core)**

   Tanggung jawab: Business logic dan rules, domain entities, value objects, domain events.

   ```csharp
   // Domain Entity
   public class Order : BaseEntity
   {
       public int CustomerId { get; private set; }
       public OrderStatus Status { get; private set; }
       private readonly List<OrderItem> _items = new();
       public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();

       // Business logic in entity
       public void AddItem(OrderItem item)
       {
           if (Status != OrderStatus.Draft)
               throw new InvalidOperationException("Cannot modify non-draft order");

           _items.Add(item);
       }

       public decimal CalculateTotal() => _items.Sum(i => i.Price * i.Quantity);
   }
   ```

3. **Application Layer**

   Tanggung jawab: Use cases, application logic, DTOs, interfaces (ports).

   ```csharp
   // Application Service / Use Case
   public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, int>
   {
       private readonly IOrderRepository _orderRepository;
       private readonly ICustomerRepository _customerRepository;
       private readonly IUnitOfWork _unitOfWork;

       public async Task<int> Handle(CreateOrderCommand request, CancellationToken ct)
       {
           // Application logic
           var customer = await _customerRepository.GetByIdAsync(request.CustomerId, ct);
           if (customer == null) throw new NotFoundException("Customer not found");

           var order = new Order(request.CustomerId);
           foreach (var item in request.Items)
               order.AddItem(new OrderItem(item.ProductId, item.Quantity, item.Price));

           await _orderRepository.AddAsync(order, ct);
           await _unitOfWork.SaveChangesAsync(ct);

           return order.Id;
       }
   }

   // Interface (Port) - defined in Application, implemented in Infrastructure
   public interface IOrderRepository
   {
       Task<Order?> GetByIdAsync(int id, CancellationToken ct = default);
       Task AddAsync(Order order, CancellationToken ct = default);
   }
   ```

4. **Infrastructure Layer**

   Tanggung jawab: Data access, external services, implementation of interfaces.

   ```csharp
   // Repository Implementation
   public class OrderRepository : IOrderRepository
   {
       private readonly AppDbContext _context;

       public OrderRepository(AppDbContext context) => _context = context;

       public async Task<Order?> GetByIdAsync(int id, CancellationToken ct)
           => await _context.Orders.FindAsync(new object[] { id }, ct);

       public async Task AddAsync(Order order, CancellationToken ct)
           => await _context.Orders.AddAsync(order, ct);
   }

   // EF Core DbContext
   public class AppDbContext : DbContext
   {
       public DbSet<Order> Orders => Set<Order>();
       // Infrastructure concern - database configuration
   }
   ```

5. **Presentation Layer**

   Tanggung jawab: API controllers, UI, request/response handling.

   ```csharp
   [ApiController]
   [Route("api/orders")]
   public class OrdersController : ControllerBase
   {
       private readonly IMediator _mediator;

       public OrdersController(IMediator mediator) => _mediator = mediator;

       [HttpPost]
       public async Task<ActionResult<int>> Create([FromBody] CreateOrderRequest request)
       {
           var command = new CreateOrderCommand(request.CustomerId, request.Items);
           var orderId = await _mediator.Send(command);
           return CreatedAtAction(nameof(GetById), new { id = orderId }, orderId);
       }
   }
   ```

6. **Dependency Direction**

   ```
   Domain → No dependencies (center)
   Application → Depends on Domain only
   Infrastructure → Depends on Application and Domain
   Presentation → Depends on Application only

   ❌ WRONG: Domain depends on Infrastructure
   ✅ CORRECT: Infrastructure depends on Domain (via interfaces)
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan layers dan dependency direction | Menjelangkan ports & adapters, hexagonal architecture |
| Contoh | Contoh struktur project sederhana | Contoh dari production dengan feature folders |

**Follow-up Questions:**
- "Bagaimana cara mengorganisir project structure untuk Clean Architecture?"
- "Apa keuntungan menggunakan feature folders vs layer folders?"

**Red Flags:**
- ❌ Tidak memahami dependency inversion
- ❌ Membiarkan domain bergantung ke infrastructure

**Green Flags:**
- ✅ Menjelaskan ports and adapters pattern
- ✅ Memberikan contoh dari production project structure


---

### Q-ARCH-008: Domain-Driven Design Concepts

**Level:** Senior
**Topik:** Aggregate, Entity, Value Object, Domain Events

**Pertanyaan:**
Jelaskan konsep-konsep utama dalam Domain-Driven Design: Entity, Value Object, Aggregate, dan Domain Events. Bagaimana implementasinya di .NET?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami tactical DDD patterns dan implementasinya dalam C#.

1. **Entity**

   Object yang memiliki identity yang unik, bisa berubah sepanjang lifecycle.

   ```csharp
   public abstract class Entity
   {
       public int Id { get; protected set; }
       public override bool Equals(object? obj)
           => obj is Entity entity && Id == entity.Id;
       public override int GetHashCode() => Id.GetHashCode();
   }

   public class Order : Entity
   {
       public string OrderNumber { get; private set; }
       public OrderStatus Status { get; private set; }
       private readonly List<OrderItem> _items = new();
       public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();

       // Behavior, not just data
       public void AddItem(Product product, int quantity)
       {
           if (Status != OrderStatus.Draft)
               throw new InvalidOperationException("Cannot add items to submitted order");

           var existingItem = _items.FirstOrDefault(i => i.ProductId == product.Id);
           if (existingItem != null)
               existingItem.IncreaseQuantity(quantity);
           else
               _items.Add(new OrderItem(product.Id, quantity, product.Price));
       }

       public void Submit()
       {
           if (!_items.Any()) throw new InvalidOperationException("Order must have items");
           Status = OrderStatus.Submitted;
           AddDomainEvent(new OrderSubmittedEvent(Id, OrderNumber));
       }
   }
   ```

2. **Value Object**

   Object tanpa identity, didefinisikan oleh atributnya, immutable.

   ```csharp
   public class Money : ValueObject
   {
       public decimal Amount { get; }
       public string Currency { get; }

       public Money(decimal amount, string currency)
       {
           if (amount < 0) throw new ArgumentException("Amount cannot be negative");
           Amount = amount;
           Currency = currency ?? throw new ArgumentNullException(nameof(currency));
       }

       public Money Add(Money other)
       {
           if (Currency != other.Currency)
               throw new InvalidOperationException("Cannot add different currencies");
           return new Money(Amount + other.Amount, Currency);
       }

       protected override IEnumerable<object> GetEqualityComponents()
       {
           yield return Amount;
           yield return Currency;
       }
   }

   public abstract class ValueObject
   {
       protected abstract IEnumerable<object> GetEqualityComponents();
       public override bool Equals(object? obj)
           => obj is ValueObject vo && GetEqualityComponents().SequenceEqual(vo.GetEqualityComponents());
       public override int GetHashCode()
           => GetEqualityComponents().Aggregate(1, (current, obj) => HashCode.Combine(current, obj));
   }
   ```

3. **Aggregate**

   Cluster dari entities dan value objects yang diperlakukan sebagai unit untuk data changes.

   ```csharp
   // Order adalah Aggregate Root
   // OrderItem hanya bisa diakses melalui Order
   public class Order : Entity, IAggregateRoot
   {
       private readonly List<OrderItem> _items = new();
       public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();

       // OrderItem tidak bisa dibuat langsung, hanya melalui Order
       public void AddItem(int productId, int quantity, decimal price)
       {
           // Invariants enforcement
           if (Status == OrderStatus.Cancelled)
               throw new InvalidOperationException("Cannot modify cancelled order");

           var item = new OrderItem(productId, quantity, price);
           _items.Add(item);
       }

       // Aggregate root memastikan invariants
       public void Cancel()
       {
           if (Status == OrderStatus.Shipped)
               throw new InvalidOperationException("Cannot cancel shipped order");

           Status = OrderStatus.Cancelled;
           foreach (var item in _items)
               item.Cancel();  // Propagate to child entities
       }
   }

   // OrderItem adalah Entity tapi bukan Aggregate Root
   public class OrderItem : Entity
   {
       public int ProductId { get; private set; }
       public int Quantity { get; private set; }
       public decimal Price { get; private set; }

       internal void Cancel() { /* ... */ }  // Internal - hanya bisa diakses dari Aggregate Root
   }
   ```

4. **Domain Events**

   Event yang terjadi di dalam domain, digunakan untuk komunikasi antar aggregates.

   ```csharp
   // Domain Event definition
   public interface IDomainEvent { }

   public record OrderSubmittedEvent(int OrderId, string OrderNumber) : IDomainEvent;

   // Entity dengan domain events support
   public abstract class Entity
   {
       private readonly List<IDomainEvent> _domainEvents = new();
       public IReadOnlyCollection<IDomainEvent> DomainEvents => _domainEvents.AsReadOnly();

       protected void AddDomainEvent(IDomainEvent eventItem) => _domainEvents.Add(eventItem);
       public void ClearDomainEvents() => _domainEvents.Clear();
   }

   // Domain Event Handler
   public class OrderSubmittedEventHandler : INotificationHandler<OrderSubmittedEvent>
   {
       private readonly IEmailService _emailService;
       private readonly IInventoryService _inventoryService;

       public async Task Handle(OrderSubmittedEvent evt, CancellationToken ct)
       {
           await _emailService.SendOrderConfirmationAsync(evt.OrderId);
           await _inventoryService.ReserveInventoryAsync(evt.OrderId);
       }
   }

   // Dispatching domain events after save
   public class DomainEventDispatcher
   {
       private readonly IMediator _mediator;

       public async Task DispatchEventsAsync(Entity entity)
       {
           var events = entity.DomainEvents.ToList();
           entity.ClearDomainEvents();

           foreach (var evt in events)
               await _mediator.Publish(evt);
       }
   }
   ```

5. **Bounded Context**

   ```csharp
   // Order Context
   public class Order
   {
       public string OrderNumber { get; set; }
       public OrderStatus Status { get; set; }
       // Fokus pada order management
   }

   // Inventory Context
   public class Order
   {
       public List<OrderItem> Items { get; set; }
       // Fokus pada fulfillment
   }

   // Same concept "Order" tapi berbeda representation sesuai context
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami tactical patterns, bounded contexts |
| Contoh | Memberikan contoh dari production domain modeling |
| Tools | Menyebutkan event sourcing, integration events |

**Follow-up Questions:**
- "Bagaimana cara menentukan Aggregate Root?"
- "Apa perbedaan Domain Event dan Integration Event?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Entity dan Value Object
- ❌ Tidak bisa menjelaskan Aggregate Root

**Green Flags:**
- ✅ Menjelaskan bounded context dengan contoh
- ✅ Menyebutkan eventual consistency, integration events


---

### Q-ARCH-009: Strategy Pattern

**Level:** Mid
**Topik:** Behavioral pattern, runtime algorithm selection

**Pertanyaan:**
Apa itu Strategy Pattern? Bagaimana cara mengimplementasikannya dan bagaimana Dependency Injection mempermudah penggunaannya?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan strategy pattern dan integrasinya dengan DI container.

1. **Strategy Pattern Concept**

   Strategy pattern memungkinkan pemilihan algoritma pada runtime, mengenkapsulasi setiap algoritma, dan membuat mereka interchangeable.

   ```csharp
   // Strategy interface
   public interface IPricingStrategy
   {
       decimal CalculatePrice(Order order);
   }

   // Concrete strategies
   public class RegularPricingStrategy : IPricingStrategy
   {
       public decimal CalculatePrice(Order order)
           => order.Items.Sum(i => i.Price * i.Quantity);
   }

   public class PremiumPricingStrategy : IPricingStrategy
   {
       public decimal CalculatePrice(Order order)
           => order.Items.Sum(i => i.Price * i.Quantity) * 0.9m;  // 10% discount
   }

   public class VIPPricingStrategy : IPricingStrategy
   {
       public decimal CalculatePrice(Order order)
           => order.Items.Sum(i => i.Price * i.Quantity) * 0.8m;  // 20% discount
   }
   ```

2. **Context dengan DI**

   ```csharp
   // Context yang menggunakan strategy
   public class PricingService
   {
       private readonly Dictionary<string, IPricingStrategy> _strategies;

       public PricingService(IEnumerable<IPricingStrategy> strategies)
       {
           // DI meng-inject semua strategies
           _strategies = strategies.ToDictionary(
               s => s.GetType().Name.Replace("PricingStrategy", "").ToLower()
           );
       }

       public decimal CalculatePrice(Order order, string customerType)
       {
           var strategy = _strategies.GetValueOrDefault(customerType.ToLower())
                        ?? _strategies["regular"];
           return strategy.CalculatePrice(order);
       }
   }

   // Registration
   services.AddScoped<IPricingStrategy, RegularPricingStrategy>();
   services.AddScoped<IPricingStrategy, PremiumPricingStrategy>();
   services.AddScoped<IPricingStrategy, VIPPricingStrategy>();
   services.AddScoped<PricingService>();
   ```

3. **Alternative: Strategy Factory**

   ```csharp
   public interface IPricingStrategyFactory
   {
       IPricingStrategy GetStrategy(CustomerType customerType);
   }

   public class PricingStrategyFactory : IPricingStrategyFactory
   {
       private readonly IServiceProvider _serviceProvider;

       public PricingStrategyFactory(IServiceProvider serviceProvider)
           => _serviceProvider = serviceProvider;

       public IPricingStrategy GetStrategy(CustomerType customerType)
           => customerType switch
           {
               CustomerType.VIP => _serviceProvider.GetRequiredService<VIPPricingStrategy>(),
               CustomerType.Premium => _serviceProvider.GetRequiredService<PremiumPricingStrategy>(),
               _ => _serviceProvider.GetRequiredService<RegularPricingStrategy>()
           };
   }
   ```

4. **Real-World Example: Payment Processing**

   ```csharp
   public interface IPaymentProcessor
   {
       string Name { get; }
       Task<PaymentResult> ProcessAsync(PaymentRequest request);
   }

   public class CreditCardProcessor : IPaymentProcessor
   {
       public string Name => "CreditCard";
       public async Task<PaymentResult> ProcessAsync(PaymentRequest request)
       {
           // Credit card processing logic
           return new PaymentResult(true, "CC-123456");
       }
   }

   public class BankTransferProcessor : IPaymentProcessor
   {
       public string Name => "BankTransfer";
       public async Task<PaymentResult> ProcessAsync(PaymentRequest request)
       {
           // Bank transfer processing logic
           return new PaymentResult(true, "BT-123456");
       }
   }

   public class EWalletProcessor : IPaymentProcessor
   {
       public string Name => "EWallet";
       public async Task<PaymentResult> ProcessAsync(PaymentRequest request)
       {
           // E-wallet processing logic
           return new PaymentResult(true, "EW-123456");
       }
   }

   public class PaymentService
   {
       private readonly IEnumerable<IPaymentProcessor> _processors;

       public PaymentService(IEnumerable<IPaymentProcessor> processors)
           => _processors = processors;

       public async Task<PaymentResult> ProcessPaymentAsync(string method, PaymentRequest request)
       {
           var processor = _processors.FirstOrDefault(p => p.Name.Equals(method, StringComparison.OrdinalIgnoreCase))
                        ?? throw new NotSupportedException($"Payment method {method} not supported");

           return await processor.ProcessAsync(request);
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan pattern dan implementasi dasar | Menjelangkan DI integration, factory pattern |
| Contoh | Contoh sederhana pricing atau payment | Contoh dari production dengan multiple strategies |

**Follow-up Questions:**
- "Bagaimana cara menambah strategy baru tanpa mengubah existing code?"
- "Apa perbedaan Strategy Pattern dengan State Pattern?"

**Red Flags:**
- ❌ Tidak memahami konsep encapsulation algorithms
- ❌ Tidak bisa memberikan contoh use case

**Green Flags:**
- ✅ Menjelaskan DI integration untuk strategy selection
- ✅ Menyebutkan open/closed principle benefit


---

### Q-ARCH-010: Factory Pattern

**Level:** Mid
**Topik:** Object creation, factory method, abstract factory

**Pertanyaan:**
Apa itu Factory Pattern? Jelaskan perbedaan antara Factory Method dan Abstract Factory, dan kapan sebaiknya menggunakan masing-masing.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami berbagai jenis factory pattern dan use case-nya.

1. **Simple Factory**

   Simple factory bukan pattern resmi, tapi sering digunakan untuk encapsulation object creation.

   ```csharp
   public class NotificationFactory
   {
       private readonly IServiceProvider _serviceProvider;

       public NotificationFactory(IServiceProvider serviceProvider)
           => _serviceProvider = serviceProvider;

       public INotificationSender Create(NotificationType type)
           => type switch
           {
               NotificationType.Email => _serviceProvider.GetRequiredService<EmailSender>(),
               NotificationType.SMS => _serviceProvider.GetRequiredService<SmsSender>(),
               NotificationType.Push => _serviceProvider.GetRequiredService<PushSender>(),
               _ => throw new ArgumentException($"Unsupported type: {type}")
           };
   }
   ```

2. **Factory Method Pattern**

   Factory method mendefinisikan interface untuk membuat object, tapi membiarkan subclass memutuskan class mana yang di-instantiate.

   ```csharp
   // Creator dengan factory method
   public abstract class DocumentProcessor
   {
       // Factory method
       public abstract IDocument CreateDocument();

       public void Process()
       {
           var document = CreateDocument();
           document.Open();
           document.Save();
       }
   }

   // Concrete creators
   public class PdfProcessor : DocumentProcessor
   {
       public override IDocument CreateDocument() => new PdfDocument();
   }

   public class WordProcessor : DocumentProcessor
   {
       public override IDocument CreateDocument() => new WordDocument();
   }

   public class ExcelProcessor : DocumentProcessor
   {
       public override IDocument CreateDocument() => new ExcelDocument();
   }

   // Products
   public interface IDocument
   {
       void Open();
       void Save();
   }

   public class PdfDocument : IDocument
   {
       public void Open() => Console.WriteLine("Opening PDF");
       public void Save() => Console.WriteLine("Saving PDF");
   }
   ```

3. **Abstract Factory Pattern**

   Abstract factory menyediakan interface untuk membuat families of related objects.

   ```csharp
   // Abstract factory
   public interface IUIFactory
   {
       IButton CreateButton();
       ITextBox CreateTextBox();
       ICheckbox CreateCheckbox();
   }

   // Concrete factories
   public class WindowsFactory : IUIFactory
   {
       public IButton CreateButton() => new WindowsButton();
       public ITextBox CreateTextBox() => new WindowsTextBox();
       public ICheckbox CreateCheckbox() => new WindowsCheckbox();
   }

   public class MacFactory : IUIFactory
   {
       public IButton CreateButton() => new MacButton();
       public ITextBox CreateTextBox() => new MacTextBox();
       public ICheckbox CreateCheckbox() => new MacCheckbox();
   }

   // Products
   public interface IButton { void Render(); }
   public interface ITextBox { void Render(); }
   public interface ICheckbox { void Render(); }

   // Concrete products
   public class WindowsButton : IButton
   {
       public void Render() => Console.WriteLine("Windows Button");
   }

   public class MacButton : IButton
   {
       public void Render() => Console.WriteLine("Mac Button");
   }

   // Client
   public class Application
   {
       private readonly IButton _button;
       private readonly ITextBox _textBox;

       public Application(IUIFactory factory)
       {
           _button = factory.CreateButton();
           _textBox = factory.CreateTextBox();
       }

       public void Render()
       {
           _button.Render();
           _textBox.Render();
       }
   }
   ```

4. **Comparison**

   | Aspect | Simple Factory | Factory Method | Abstract Factory |
   |---|---|---|---|
   | Purpose | Single object creation | Delegate creation to subclass | Create families of objects |
   | Complexity | Low | Medium | High |
   | Use Case | Single type | Single type, polymorphic | Multiple related types |
   | DI Integration | Easy | Moderate | Easy |

   ```csharp
   // DI Registration for Abstract Factory
   services.AddScoped<IUIFactory, WindowsFactory>();  // Or MacFactory based on config
   services.AddScoped<Application>();

   // Usage
   var app = serviceProvider.GetRequiredService<Application>();
   app.Render();
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan simple factory dan factory method | Menjelaskan abstract factory, families of objects |
| Contoh | Contoh sederhana dengan switch statement | Contoh UI factory, database factory |

**Follow-up Questions:**
- "Kapan menggunakan Factory Pattern vs DI container?"
- "Bagaimana cara menguji code yang menggunakan factory?"

**Red Flags:**
- ❌ Tidak bisa membedakan factory method dan abstract factory
- ❌ Menggunakan factory ketika DI container sudah cukup

**Green Flags:**
- ✅ Menjelaskan families of related objects concept
- ✅ Memberikan contoh dari production cross-platform code


---

### Q-ARCH-011: Decorator Pattern

**Level:** Senior
**Topik:** Structural pattern, dynamic behavior addition

**Pertanyaan:**
Apa itu Decorator Pattern? Bagaimana cara mengimplementasikannya dan bagaimana pattern ini digunakan untuk cross-cutting concerns di .NET?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami decorator pattern dan aplikasinya di ASP.NET Core pipeline behaviors.

1. **Decorator Pattern Concept**

   Decorator menambahkan behavior ke object secara dinamis tanpa mengubah class asli, mengikuti Open/Closed Principle.

   ```csharp
   // Component interface
   public interface INotificationSender
   {
       Task SendAsync(string message);
   }

   // Concrete component
   public class EmailSender : INotificationSender
   {
       public async Task SendAsync(string message)
       {
           Console.WriteLine($"Sending email: {message}");
           await Task.CompletedTask;
       }
   }

   // Base decorator
   public abstract class NotificationDecorator : INotificationSender
   {
       protected readonly INotificationSender _wrapped;

       protected NotificationDecorator(INotificationSender wrapped)
           => _wrapped = wrapped;

       public virtual async Task SendAsync(string message)
           => await _wrapped.SendAsync(message);
   }

   // Concrete decorators
   public class LoggingDecorator : NotificationDecorator
   {
       private readonly ILogger<LoggingDecorator> _logger;

       public LoggingDecorator(INotificationSender wrapped, ILogger<LoggingDecorator> logger)
           : base(wrapped) => _logger = logger;

       public override async Task SendAsync(string message)
       {
           _logger.LogInformation("Sending notification: {Message}", message);
           await base.SendAsync(message);
           _logger.LogInformation("Notification sent successfully");
       }
   }

   public class RetryDecorator : NotificationDecorator
   {
       private readonly int _maxRetries;

       public RetryDecorator(INotificationSender wrapped, int maxRetries = 3)
           : base(wrapped) => _maxRetries = maxRetries;

       public override async Task SendAsync(string message)
       {
           for (int i = 0; i < _maxRetries; i++)
           {
               try
               {
                   await base.SendAsync(message);
                   return;
               }
               catch (Exception ex) when (i < _maxRetries - 1)
               {
                   Console.WriteLine($"Retry {i + 1} failed, retrying...");
                   await Task.Delay(1000 * (i + 1));
               }
           }
       }
   }

   // Usage - stacking decorators
   INotificationSender sender = new EmailSender();
   sender = new LoggingDecorator(sender, logger);
   sender = new RetryDecorator(sender, maxRetries: 3);
   await sender.SendAsync("Hello World");
   ```

2. **Decorator dengan DI Container**

   ```csharp
   // Registration dengan Scrutor or manual
   services.AddScoped<INotificationSender, EmailSender>();
   services.Decorate<INotificationSender, LoggingDecorator>();
   services.Decorate<INotificationSender, RetryDecorator>();

   // Manual registration
   services.AddScoped<EmailSender>();
   services.AddScoped<LoggingDecorator>();
   services.AddScoped<RetryDecorator>();

   services.AddScoped<INotificationSender>(sp =>
   {
       var logger = sp.GetRequiredService<ILogger<LoggingDecorator>>();
       var emailSender = sp.GetRequiredService<EmailSender>();
       var loggingDecorator = new LoggingDecorator(emailSender, logger);
       return new RetryDecorator(loggingDecorator, maxRetries: 3);
   });
   ```

3. **Real-World: Repository Decorators**

   ```csharp
   public class CachingRepositoryDecorator<T> : IRepository<T> where T : class, IEntity
   {
       private readonly IRepository<T> _wrapped;
       private readonly IDistributedCache _cache;
       private readonly TimeSpan _cacheDuration = TimeSpan.FromMinutes(5);

       public CachingRepositoryDecorator(IRepository<T> wrapped, IDistributedCache cache)
       {
           _wrapped = wrapped;
           _cache = cache;
       }

       public async Task<T?> GetByIdAsync(int id)
       {
           var cacheKey = $"entity_{typeof(T).Name}_{id}";
           var cached = await _cache.GetStringAsync(cacheKey);

           if (cached != null)
               return JsonSerializer.Deserialize<T>(cached);

           var entity = await _wrapped.GetByIdAsync(id);
           if (entity != null)
               await _cache.SetStringAsync(cacheKey, JsonSerializer.Serialize(entity), new DistributedCacheEntryOptions { AbsoluteExpirationRelativeToNow = _cacheDuration });

           return entity;
       }

       public async Task UpdateAsync(T entity)
       {
           await _wrapped.UpdateAsync(entity);
           var cacheKey = $"entity_{typeof(T).Name}_{entity.Id}";
           await _cache.RemoveAsync(cacheKey);
       }
   }
   ```

4. **MediatR Pipeline Behavior sebagai Decorator**

   Pipeline behaviors di MediatR adalah implementasi decorator pattern untuk cross-cutting concerns.

   ```csharp
   // Logging behavior - decorates all handlers
   public class LoggingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
       where TRequest : IRequest<TResponse>
   {
       private readonly ILogger<LoggingBehavior<TRequest, TResponse>> _logger;

       public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken ct)
       {
           _logger.LogInformation("Handling {RequestType}", typeof(TRequest).Name);
           var response = await next();
           _logger.LogInformation("Handled {RequestType}", typeof(TRequest).Name);
           return response;
       }
   }

   // Caching behavior
   public class CachingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
       where TRequest : IRequest<TResponse>
   {
       private readonly IDistributedCache _cache;

       public async Task<TResponse> Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken ct)
       {
           if (request is ICacheableQuery cacheable)
           {
               var cached = await _cache.GetStringAsync(cacheable.CacheKey);
               if (cached != null)
                   return JsonSerializer.Deserialize<TResponse>(cached)!;
           }

           var response = await next();

           if (request is ICacheableQuery cacheableQuery)
               await _cache.SetStringAsync(cacheableQuery.CacheKey, JsonSerializer.Serialize(response));

           return response;
       }
   }
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami decorator chain, DI integration, pipeline behaviors |
| Contoh | Memberikan contoh dari production cross-cutting concerns |
| Tools | Menyebutkan Scrutor, ASP.NET Core middleware |

**Follow-up Questions:**
- "Bagaimana decorator berbeda dari proxy pattern?"
- "Apa keuntungan menggunakan decorator untuk caching vs hard-coded caching?"

**Red Flags:**
- ❌ Tidak memahami perbedaan decorator dan inheritance
- ❌ Tidak bisa menjelaskan stacking decorators

**Green Flags:**
- ✅ Menjelaskan pipeline behaviors sebagai decorator implementation
- ✅ Memberikan contoh Scrutor untuk DI registration


---

### Q-ARCH-012: Adapter dan Facade Pattern

**Level:** Mid
**Topik:** Integration patterns, third-party library wrapping

**Pertanyaan:**
Jelaskan perbedaan antara Adapter Pattern dan Facade Pattern. Kapan sebaiknya menggunakan masing-masing? Berikan contoh untuk integrasi dengan external service.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami kedua pattern ini dan aplikasinya untuk integrasi dengan third-party services.

1. **Adapter Pattern**

   Adapter mengubah interface dari class menjadi interface yang client harapkan, memungkinkan class dengan interface tidak compatible untuk bekerja sama.

   ```csharp
   // External payment gateway dengan interface yang berbeda
   public class StripePaymentGateway
   {
       public async Task<StripeResponse> ChargeAsync(string customerId, decimal amount, string currency)
       {
           // Stripe-specific implementation
           return new StripeResponse { Success = true, TransactionId = "STRIPE-123" };
       }
   }

   public class StripeResponse
   {
       public bool Success { get; set; }
       public string TransactionId { get; set; } = string.Empty;
   }

   // Domain interface yang kita inginkan
   public interface IPaymentProcessor
   {
       Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request);
   }

   public record PaymentRequest(string CustomerId, decimal Amount, string Currency);
   public record PaymentResult(bool Success, string TransactionId);

   // Adapter untuk Stripe
   public class StripePaymentAdapter : IPaymentProcessor
   {
       private readonly StripePaymentGateway _stripeGateway;

       public StripePaymentAdapter(StripePaymentGateway stripeGateway)
           => _stripeGateway = stripeGateway;

       public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request)
       {
           // Translate dari domain model ke Stripe model
           var response = await _stripeGateway.ChargeAsync(
               request.CustomerId,
               request.Amount,
               request.Currency
           );

           // Translate dari Stripe response ke domain model
           return new PaymentResult(response.Success, response.TransactionId);
       }
   }

   // Adapter untuk payment gateway lain dengan interface berbeda
   public class PayPalPaymentGateway
   {
       public async Task<PayPalResult> ExecutePayment(PayPalPayment payment)
       {
           return new PayPalResult { Status = "completed", PaymentId = "PP-456" };
       }
   }

   public class PayPalPaymentAdapter : IPaymentProcessor
   {
       private readonly PayPalPaymentGateway _paypalGateway;

       public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request)
       {
           var payment = new PayPalPayment
           {
               PayerId = request.CustomerId,
               Amount = request.Amount,
               CurrencyCode = request.Currency
           };

           var result = await _paypalGateway.ExecutePayment(payment);

           return new PaymentResult(
               result.Status == "completed",
               result.PaymentId
           );
       }
   }
   ```

2. **Facade Pattern**

   Facade menyediakan interface yang disederhanakan ke subsystem yang kompleks.

   ```csharp
   // Complex subsystems untuk order processing
   public class InventoryService
   {
       public async Task<bool> CheckAvailabilityAsync(int productId, int quantity) { /* ... */ return true; }
       public async Task ReserveAsync(int productId, int quantity) { /* ... */ }
   }

   public class PaymentService
   {
       public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request) { /* ... */ return new(true, "TXN-123"); }
   }

   public class ShippingService
   {
       public async Task<string> CreateShipmentAsync(int orderId, Address address) { /* ... */ return "SHIP-123"; }
   }

   public class NotificationService
   {
       public async Task SendOrderConfirmationAsync(string email, int orderId) { /* ... */ }
   }

   // Facade - simplified interface
   public class OrderProcessingFacade
   {
       private readonly InventoryService _inventory;
       private readonly PaymentService _payment;
       private readonly ShippingService _shipping;
       private readonly NotificationService _notification;

       public OrderProcessingFacade(
           InventoryService inventory,
           PaymentService payment,
           ShippingService shipping,
           NotificationService notification)
       {
           _inventory = inventory;
           _payment = payment;
           _shipping = shipping;
           _notification = notification;
       }

       public async Task<OrderResult> ProcessOrderAsync(Order order)
       {
           // Check inventory
           foreach (var item in order.Items)
           {
               if (!await _inventory.CheckAvailabilityAsync(item.ProductId, item.Quantity))
                   return OrderResult.Failed($"Product {item.ProductId} not available");

               await _inventory.ReserveAsync(item.ProductId, item.Quantity);
           }

           // Process payment
           var paymentResult = await _payment.ProcessPaymentAsync(new PaymentRequest(
               order.CustomerId.ToString(),
               order.TotalAmount,
               "USD"
           ));

           if (!paymentResult.Success)
               return OrderResult.Failed("Payment failed");

           // Create shipment
           var shipmentId = await _shipping.CreateShipmentAsync(order.Id, order.ShippingAddress);

           // Send notification
           await _notification.SendOrderConfirmationAsync(order.CustomerEmail, order.Id);

           return OrderResult.Success(order.Id, paymentResult.TransactionId, shipmentId);
       }
   }

   // Usage - client hanya berinteraksi dengan facade
   public class OrderController
   {
       private readonly OrderProcessingFacade _orderFacade;

       public async Task<ActionResult> PlaceOrder(OrderRequest request)
       {
           var order = MapToOrder(request);
           var result = await _orderFacade.ProcessOrderAsync(order);
           return result.Success ? Ok(result) : BadRequest(result.ErrorMessage);
       }
   }
   ```

3. **Perbandingan Adapter vs Facade**

   | Aspect | Adapter | Facade |
   |---|---|---|
   | Purpose | Convert interface to expected format | Simplify complex subsystem |
   | Use Case | Integrate incompatible interfaces | Provide simple entry point |
   | Interface | Existing interface adaptation | New simplified interface |
   | Complexity | Single class wrapping | Multiple subsystems coordination |

4. **Real-World: Third-Party API Integration**

   ```csharp
   // Facade untuk external SMS provider dengan adapter
   public interface ISmsService
   {
       Task<SmsResult> SendAsync(string phoneNumber, string message);
   }

   // Adapter untuk Twilio
   public class TwilioSmsAdapter : ISmsService
   {
       private readonly TwilioClient _client;

       public async Task<SmsResult> SendAsync(string phoneNumber, string message)
       {
           var result = await _client.SendMessageAsync(phoneNumber, message);
           return new SmsResult(result.StatusCode == 200, result.MessageId);
       }
   }

   // Facade untuk notification system
   public class NotificationFacade
   {
       private readonly ISmsService _sms;
       private readonly IEmailService _email;
       private readonly IPushService _push;

       public async Task NotifyUserAsync(User user, Notification notification)
       {
           // Send via multiple channels
           var tasks = new List<Task>();

           if (!string.IsNullOrEmpty(user.PhoneNumber))
               tasks.Add(_sms.SendAsync(user.PhoneNumber, notification.Message));

           if (!string.IsNullOrEmpty(user.Email))
               tasks.Add(_email.SendAsync(user.Email, notification.Subject, notification.Message));

           if (!string.IsNullOrEmpty(user.DeviceToken))
               tasks.Add(_push.SendAsync(user.DeviceToken, notification.Message));

           await Task.WhenAll(tasks);
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan kedua pattern dengan contoh | Menjelangkan kombinasi keduanya, legacy integration |
| Contoh | Contoh sederhana adapter atau facade | Contoh integrasi multiple external services |

**Follow-up Questions:**
- "Kapan sebaiknya menggunakan Adapter vs membuat wrapper class biasa?"
- "Bagaimana cara menguji Facade yang bergantung pada banyak services?"

**Red Flags:**
- ❌ Tidak bisa membedakan Adapter dan Facade
- ❌ Tidak bisa memberikan contoh use case

**Green Flags:**
- ✅ Menjelaskan kombinasi kedua pattern
- ✅ Memberikan contoh dari production integration


---

## 4. Entity Framework Core & Database

### Q-DATA-001: DbContext Lifecycle Management

**Level:** Mid
**Topik:** Scoped lifetime, pooling, disposal, DI configuration

**Pertanyaan:**
Bagaimana cara mengkonfigurasi DbContext lifetime di ASP.NET Core? Jelaskan berbagai opsi lifetime, kapan menggunakan DbContext pooling, dan best practices untuk disposal.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan perbedaan lifetime options, DbContext pooling, dan bagaimana DI container mengelola DbContext lifecycle.

1. **DbContext Lifetime Options**

   ```csharp
   // Program.cs - Konfigurasi DbContext

   // OPTION 1: Scoped (DEFAULT - Recommended untuk web apps)
   services.AddDbContext<ApplicationDbContext>(options =>
       options.UseSqlServer(connectionString));

   // OPTION 2: Transient (New instance per injection)
   services.AddDbContext<ApplicationDbContext>(options =>
       options.UseSqlServer(connectionString),
       ServiceLifetime.Transient);

   // OPTION 3: Singleton (NOT RECOMMENDED - thread safety issues)
   // JANGAN gunakan untuk DbContext karena:
   // - DbContext tidak thread-safe
   // - Change tracking akan menumpuk
   // - Memory leak potential
   ```

   | Lifetime | Instance Created | Use Case |
   |---|---|---|
   | **Scoped** | One per HTTP request | Default untuk web apps, request-scoped unit of work |
   | **Transient** | Every injection | Edge cases, parallel processing dalam satu request |
   | **Singleton** | One per app lifetime | TIDAK COCOK untuk DbContext |

2. **DbContext Pooling**

   Pooling meningkatkan performance dengan reusing DbContext instances.

   ```csharp
   // DENGAN Pooling - Reuse instances (EF Core 2.0+)
   services.AddDbContextPool<ApplicationDbContext>(options =>
       options.UseSqlServer(connectionString),
       poolSize: 128);  // Default: 1024

   // Performance impact:
   // - Pooling mengurangi allocation overhead ~30-50%
   // - Cocok untuk high-throughput web apps
   ```


3. **DbContext Factory (EF Core 5.0+)**

   Untuk scenario non-HTTP atau multiple contexts dalam satu request:

   ```csharp
   // Register factory untuk create DbContext on-demand
   services.AddDbContextFactory<ApplicationDbContext>(options =>
       options.UseSqlServer(connectionString));

   // Usage di service
   public class ReportGenerator
   {
       private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

       public async Task GenerateReportAsync()
       {
           await using var context = await _contextFactory.CreateDbContextAsync();
           var data = await context.Orders.ToListAsync();
       }
   }
   ```

4. **Disposal Best Practices**

   ```csharp
   // ✅ CORRECT: DI container handles disposal for Scoped/Transient
   public class OrderService
   {
       private readonly ApplicationDbContext _context;
       public OrderService(ApplicationDbContext context) => _context = context;
   }

   // ❌ WRONG: Manual disposal pada injected context
   public void DoSomething() => _context.Dispose();  // WRONG!
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan Scoped lifetime dan DI basics | Menjelaskan pooling internals, factory pattern, connection management |
| Contoh | Contoh konfigurasi di Program.cs | Contoh dari production dengan multi-tenant, background workers |

**Follow-up Questions:**
- "Mengapa DbContext tidak boleh Singleton?"
- "Bagaimana cara handle multiple databases dalam satu request?"

**Red Flags:**
- ❌ Tidak memahami perbedaan Scoped vs Singleton
- ❌ Mencoba dispose injected DbContext

**Green Flags:**
- ✅ Menjelaskan DbContext pooling dan IDbContextFactory
- ✅ Memberikan contoh dari production configuration


---

### Q-DATA-002: Change Tracking di EF Core

**Level:** Mid
**Topik:** Tracking vs No-Tracking, entity states, AsNoTracking optimization

**Pertanyaan:**
Jelaskan bagaimana Change Tracker bekerja di EF Core. Kapan sebaiknya menggunakan AsNoTracking dan apa implikasinya terhadap performance?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami entity states, tracking overhead, dan kapan menggunakan no-tracking untuk optimasi.

1. **Entity States di Change Tracker**

   ```csharp
   public enum EntityState
   {
       Detached = 0,    // Not tracked by context
       Unchanged = 1,   // Tracked, no changes detected
       Added = 2,       // Will be inserted on SaveChanges
       Modified = 3,    // Will be updated on SaveChanges
       Deleted = 4      // Will be deleted on SaveChanges
   }

   // Inspect entity state
   var entry = _context.Entry(order);
   Console.WriteLine($"State: {entry.State}");
   Console.WriteLine($"Original: {entry.Property(o => o.Status).OriginalValue}");
   Console.WriteLine($"Current: {entry.Property(o => o.Status).CurrentValue}");
   ```

2. **Tracking vs No-Tracking**

   ```csharp
   // DEFAULT: Tracking enabled
   var order = await _context.Orders.FindAsync(id);
   order.Status = Status.Processed;  // Tracked, will update on SaveChanges

   // NO TRACKING: Read-only queries
   var orders = await _context.Orders
       .AsNoTracking()
       .ToListAsync();
   ```

   | Scenario | Tracking | No-Tracking |
   |---|---|---|
   | Read-only queries | Slower | Faster |
   | Memory usage | Grows with entities | Constant |
   | Update operations | Automatic | Manual attach required |



3. **AsNoTracking Optimization**

   ```csharp
   // Read-only queries - GUNAKAN AsNoTracking
   public async Task<List<OrderDto>> GetOrdersAsync()
   {
       return await _context.Orders
           .AsNoTracking()  // 30-50% faster, less memory
           .Select(o => new OrderDto { /* ... */ })
           .ToListAsync();
   }

   // Default tracking - untuk update operations
   public async Task UpdateOrderStatusAsync(int orderId, OrderStatus status)
   {
       var order = await _context.Orders.FindAsync(orderId);  // Tracked
       order.Status = status;
       await _context.SaveChangesAsync();  // Auto-detect changes
   }

   // No-Tracking dengan Attach untuk update
   public async Task UpdateOrderAsync(Order order)
   {
       _context.Orders.Attach(order);  // Attach untuk tracking
       _context.Entry(order).State = EntityState.Modified;
       await _context.SaveChangesAsync();
   }
   ```

4. **Global Query Filter dengan Tracking**

   ```csharp
   // Global no-tracking untuk read-heavy apps
   protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
   {
       optionsBuilder.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
   }

   // Override dengan tracking saat diperlukan
   var orders = await _context.Orders
       .AsTracking()  // Override global no-tracking
       .Where(o => o.Status == Status.Pending)
       .ToListAsync();
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan tracking states, AsNoTracking usage | Menjelaskan ChangeTracker internals, DetectChanges, AutoDetectChangesEnabled |
| Contoh | Contoh sederhana read vs write | Contoh dari production dengan query optimization |

**Follow-up Questions:**
- "Bagaimana cara melihat entity states saat debugging?"
- "Apa efek AutoDetectChangesEnabled terhadap performance?"

**Red Flags:**
- ❌ Tidak memahami entity states
- ❌ Tidak mengetahui AsNoTracking untuk read-only queries

**Green Flags:**
- ✅ Menjelaskan QueryTrackingBehavior global configuration
- ✅ Memberikan contoh dari production optimization


---

### Q-DATA-003: Loading Strategies - Eager vs Lazy vs Explicit

**Level:** Mid
**Topik:** Include, ThenInclude, lazy loading proxies, N+1 prevention

**Pertanyaan:**
Jelaskan perbedaan antara Eager Loading, Lazy Loading, dan Explicit Loading di EF Core. Kapan menggunakan masing-masing dan bagaimana mencegah N+1 problem?

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus mampu menjelaskan tiga loading strategies, implementasinya, dan trade-offs masing-masing.

1. **Eager Loading dengan Include**

   Data related di-load dalam satu query.

   ```csharp
   // Eager loading - load related data dalam satu query
   var orders = await _context.Orders
       .Include(o => o.Customer)
       .Include(o => o.Items)
           .ThenInclude(i => i.Product)
       .ToListAsync();

   // Generated SQL: JOIN query
   // SELECT Orders.*, Customers.*, Items.*, Products.*
   // FROM Orders
   // LEFT JOIN Customers ON ...
   // LEFT JOIN Items ON ...
   ```

2. **Lazy Loading dengan Proxies**

   Data related di-load saat pertama kali diakses.

   ```csharp
   // Konfigurasi lazy loading
   services.AddDbContext<MyContext>(options =>
       options.UseLazyLoadingProxies()
              .UseSqlServer(connectionString));

   // Entity dengan virtual navigation property
   public class Order
   {
       public int Id { get; set; }
       // Virtual navigation property untuk lazy loading
       public virtual Customer Customer { get; set; } = null!;
       public virtual ICollection<OrderItem> Items { get; set; } = new List<OrderItem>();
   }

   // Usage - related data di-load on-demand
   var order = await _context.Orders.FindAsync(id);
   var customerName = order.Customer.Name;  // Query ke database di sini!
   // ⚠️ WARNING: Bisa menyebabkan N+1 problem
   ```

3. **Explicit Loading**

   Load related data secara manual saat dibutuhkan.

   ```csharp
   var order = await _context.Orders.FindAsync(id);

   // Explicit load collection
   await _context.Entry(order)
       .Collection(o => o.Items)
       .LoadAsync();

   // Explicit load reference
   await _context.Entry(order)
       .Reference(o => o.Customer)
       .LoadAsync();

   // Query pada related data sebelum load
   var expensiveItems = await _context.Entry(order)
       .Collection(o => o.Items)
       .Query()
       .Where(i => i.Price > 100)
       .LoadAsync();
   ```

4. **Comparison Table**

   | Strategy | When to Use | Pros | Cons |
   |---|---|---|---|
   | **Eager** | Known related data needed | Single query, predictable | Over-fetching if not used |
   | **Lazy** | Optional related data, small apps | Simple code | N+1 problem, hidden queries |
   | **Explicit** | Conditional loading, fine control | Precise control | More verbose code |

5. **N+1 Problem dan Solusinya**

   ```csharp
   // ❌ N+1 PROBLEM dengan lazy loading
   var orders = await _context.Orders.ToListAsync();  // 1 query
   foreach (var order in orders)
   {
       var customer = order.Customer.Name;  // N queries!
   }

   // ✅ SOLUTION: Eager loading
   var orders = await _context.Orders
       .Include(o => o.Customer)
       .ToListAsync();  // 1 query untuk semua data
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan tiga strategies dengan contoh | Menjelaskan N+1 detection, Cartesian explosion, split query |
| Contoh | Contoh sederhana Include | Contoh dari production debugging N+1 |

**Follow-up Questions:**
- "Bagaimana cara mendeteksi N+1 problem di production?"
- "Apa itu Cartesian explosion dan bagaimana mencegahnya?"

**Red Flags:**
- ❌ Tidak memahami perbedaan tiga loading strategies
- ❌ Tidak menyadari N+1 problem dengan lazy loading

**Green Flags:**
- ✅ Menjelaskan AsSplitQuery untuk Cartesian explosion
- ✅ Memberikan contoh dari production query optimization


---

### Q-DATA-004: N+1 Query Problem Detection

**Level:** Both
**Topik:** Query analysis, logging, monitoring tools

**Pertanyaan:**
Bagaimana cara mendeteksi dan mencegah N+1 query problem di aplikasi EF Core? Jelaskan tools dan techniques yang bisa digunakan.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami cara mengidentifikasi, mendeteksi, dan memperbaiki N+1 queries.

1. **Cara Mendeteksi N+1 Problem**

   ```csharp
   // Enable sensitive data logging untuk development
   optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information)
                 .EnableSensitiveDataLogging();

   // N+1 Pattern di log:
   // info: SELECT * FROM Orders
   // info: SELECT * FROM Customers WHERE CustomerId = 1
   // info: SELECT * FROM Customers WHERE CustomerId = 2
   // info: SELECT * FROM Customers WHERE CustomerId = 3
   // ... (N queries untuk related data)
   ```

2. **Tools untuk Detection**

   ```csharp
   // MiniProfiler - visual query analysis
   services.AddMiniProfiler(options =>
   {
       options.RouteBasePath = "/profiler";
   }).AddEntityFramework();

   // Application Insights - query tracking
   optionsBuilder.UseApplicationInsights();

   // EF Core built-in logging
   optionsBuilder.LogTo(
       filter: (eventId, level) => eventId == RelationalEventId.CommandExecuted,
       logger: (eventData) =>
       {
           var command = (CommandEventData)eventData;
           Console.WriteLine($"Query: {command.Command.CommandText}");
           Console.WriteLine($"Duration: {command.Duration}");
       });
   ```

3. **Code Pattern untuk N+1 Prevention**

   ```csharp
   // ❌ N+1 PATTERN
   public async Task<List<OrderSummaryDto>> GetOrderSummariesAsync()
   {
       var orders = await _context.Orders.ToListAsync();
       var result = new List<OrderSummaryDto>();

       foreach (var order in orders)
       {
           var customer = await _context.Customers
               .FirstOrDefaultAsync(c => c.Id == order.CustomerId);  // N queries!
           result.Add(new OrderSummaryDto
           {
               OrderNumber = order.OrderNumber,
               CustomerName = customer!.Name
           });
       }
       return result;
   }

   // ✅ FIXED dengan Eager Loading
   public async Task<List<OrderSummaryDto>> GetOrderSummariesAsync()
   {
       return await _context.Orders
           .Include(o => o.Customer)
           .Select(o => new OrderSummaryDto
           {
               OrderNumber = o.OrderNumber,
               CustomerName = o.Customer.Name
           })
           .ToListAsync();
   }
   ```

4. **Monitoring di Production**

   ```csharp
   // Middleware untuk query count tracking
   public class QueryCountMiddleware
   {
       private readonly RequestDelegate _next;

       public async Task InvokeAsync(HttpContext context, AppDbContext dbContext)
       {
           var queryCountBefore = GetQueryCount(dbContext);
           await _next(context);
           var queryCountAfter = GetQueryCount(dbContext);
           var queriesExecuted = queryCountAfter - queryCountBefore;

           if (queriesExecuted > 10)
           {
               _logger.LogWarning("High query count for request {Path}: {Count} queries",
                   context.Request.Path, queriesExecuted);
           }
       }
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Mengenali N+1 pattern dan solusi dasar | Menjelaskan monitoring, alerting, dan automated detection |
| Contoh | Contoh sederhana Include fix | Contoh dari production monitoring setup |

**Follow-up Questions:**
- "Bagaimana cara setup alerting untuk N+1 detection di production?"
- "Tools apa yang bisa digunakan untuk automated N+1 detection?"

**Red Flags:**
- ❌ Tidak mengenali N+1 pattern
- ❌ Tidak bisa memberikan solusi perbaikan

**Green Flags:**
- ✅ Menjelaskan MiniProfiler, Application Insights
- ✅ Memberikan contoh dari production debugging


---

### Q-DATA-005: EF Core Migrations

**Level:** Mid
**Topik:** Migration workflow, production deployment, data seeding

**Pertanyaan:**
Bagaimana cara mengelola EF Core migrations di aplikasi production? Jelaskan workflow dari development hingga deployment dan best practices untuk zero-downtime migrations.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami migration lifecycle, deployment strategies, dan cara handle data migrations.

1. **Migration Workflow**

   ```bash
   # Create migration
   dotnet ef migrations add AddOrderStatusColumn

   # Review generated migration
   # File: Migrations/20240115123456_AddOrderStatusColumn.cs

   # Apply to database
   dotnet ef database update

   # Rollback to specific migration
   dotnet ef database update PreviousMigrationName

   # Generate SQL script for production
   dotnet ef migrations script AddOrderStatusColumn --output migration.sql
   ```

2. **Migration File Structure**

   ```csharp
   public partial class AddOrderStatusColumn : Migration
   {
       protected override void Up(MigrationBuilder migrationBuilder)
       {
           migrationBuilder.AddColumn<string>(
               name: "Status",
               table: "Orders",
               type: "nvarchar(50)",
               maxLength: 50,
               nullable: false,
               defaultValue: "Pending");

           migrationBuilder.CreateIndex(
               name: "IX_Orders_Status",
               table: "Orders",
               column: "Status");
       }

       protected override void Down(MigrationBuilder migrationBuilder)
       {
           migrationBuilder.DropIndex("IX_Orders_Status", "Orders");
           migrationBuilder.DropColumn("Status", "Orders");
       }
   }
   ```

3. **Zero-Downtime Migration Strategy**

   ```csharp
   // PHASE 1: Add column as nullable (no data loss)
   protected override void Up(MigrationBuilder migrationBuilder)
   {
       migrationBuilder.AddColumn<string>(
           name: "Status",
           table: "Orders",
           type: "nvarchar(50)",
           nullable: true);  // Nullable dulu
   }

   // PHASE 2: Deploy app yang populate new column

   // PHASE 3: Make column non-nullable
   protected override void Up(MigrationBuilder migrationBuilder)
   {
       migrationBuilder.Sql("UPDATE Orders SET Status = 'Pending' WHERE Status IS NULL");
       migrationBuilder.AlterColumn<string>(
           name: "Status",
           table: "Orders",
           type: "nvarchar(50)",
           nullable: false);
   }
   ```

4. **Data Seeding**

   ```csharp
   // Di OnModelCreating
   protected override void OnModelCreating(ModelBuilder modelBuilder)
   {
       modelBuilder.Entity<OrderStatus>()
           .HasData(
               new OrderStatus { Id = 1, Name = "Pending" },
               new OrderStatus { Id = 2, Name = "Processing" },
               new OrderStatus { Id = 3, Name = "Completed" }
           );
   }

   // Custom data migration
   public partial class SeedOrderStatuses : Migration
   {
       protected override void Up(MigrationBuilder migrationBuilder)
       {
           migrationBuilder.InsertData(
               table: "OrderStatuses",
               columns: new[] { "Id", "Name" },
               values: new object[,]
               {
                   { 1, "Pending" },
                   { 2, "Processing" },
                   { 3, "Completed" }
               });
       }
   }
   ```

5. **Production Deployment Best Practices**

   | Practice | Description |
   |---|---|
   | Generate SQL scripts | Review sebelum execute |
   | Backup database | Sebelum migration besar |
   | Test di staging | Dengan production-like data |
   | Monitor timeouts | Large tables perlu batch processing |
   | Idempotent scripts | Safe untuk re-run |

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan workflow dasar, Up/Down | Menjelaskan zero-downtime strategies, data migrations |
| Contoh | Contoh simple migration | Contoh dari production deployment |

**Follow-up Questions:**
- "Bagaimana cara handle large table migrations tanpa downtime?"
- "Apa strategi untuk rollback migration yang gagal?"

**Red Flags:**
- ❌ Tidak memahami Up dan Down methods
- ❌ Tidak bisa menjelaskan zero-downtime strategy

**Green Flags:**
- ✅ Menjelaskan multi-phase migration approach
- ✅ Memberikan contoh dari production deployment


---

### Q-DATA-006: Transactions di EF Core

**Level:** Mid
**Topik:** BeginTransaction, SaveChanges, TransactionScope, isolation levels

**Pertanyaan:**
Bagaimana cara mengelola transactions di EF Core? Jelaskan berbagai pendekatan dan isolation levels yang tersedia.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami transaction management, implicit vs explicit transactions, dan isolation levels.

1. **Implicit Transaction (Default)**

   ```csharp
   // SaveChanges otomatis membuat transaction
   public async Task CreateOrderAsync(Order order)
   {
       _context.Orders.Add(order);
       await _context.SaveChangesAsync();  // Implicit transaction
   }
   ```

2. **Explicit Transaction**

   ```csharp
   public async Task TransferStockAsync(int fromId, int toId, int quantity)
   {
       await using var transaction = await _context.Database.BeginTransactionAsync();

       try
       {
           var source = await _context.Stocks.FindAsync(fromId);
           if (source == null || source.Quantity < quantity)
               throw new InvalidOperationException("Insufficient stock");

           source.Quantity -= quantity;

           var dest = await _context.Stocks.FindAsync(toId);
           if (dest == null)
           {
               dest = new Stock { Id = toId, Quantity = 0 };
               _context.Stocks.Add(dest);
           }
           dest.Quantity += quantity;

           await _context.SaveChangesAsync();
           await transaction.CommitAsync();
       }
       catch
       {
           await transaction.RollbackAsync();
           throw;
       }
   }
   ```

3. **Isolation Levels**

   | Level | Dirty Read | Non-repeatable Read | Phantom | Performance |
   |---|---|---|---|---|
   | Read Uncommitted | Possible | Possible | Possible | Highest |
   | Read Committed | No | Possible | Possible | High |
   | Repeatable Read | No | No | Possible | Medium |
   | Serializable | No | No | No | Lowest |
   | Snapshot | No | No | No | Medium |

   ```csharp
   await using var transaction = await _context.Database
       .BeginTransactionAsync(IsolationLevel.Serializable);
   ```

4. **SavePoint untuk Nested Transactions**

   ```csharp
   await using var transaction = await _context.Database.BeginTransactionAsync();

   try
   {
       _context.Orders.Add(order);
       await _context.SaveChangesAsync();
       await transaction.CreateSavepointAsync("AfterOrderCreated");

       try
       {
           await ProcessInventoryAsync();
       }
       catch (InventoryException)
       {
           await transaction.RollbackToSavepointAsync("AfterOrderCreated");
       }

       await transaction.CommitAsync();
   }
   catch
   {
       await transaction.RollbackAsync();
       throw;
   }
   ```

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan BeginTransaction, Commit, Rollback | Menjelaskan isolation levels, distributed transactions |
| Contoh | Contoh sederhana transfer | Contoh dari production dengan multiple databases |

**Follow-up Questions:**
- "Kapan menggunakan TransactionScope vs EF Core transaction?"
- "Apa dampak isolation level terhadap concurrency?"

**Red Flags:**
- ❌ Tidak memahami commit dan rollback
- ❌ Tidak mengetahui isolation levels

**Green Flags:**
- ✅ Menjelaskan isolation level implications
- ✅ Memberikan contoh distributed transaction


---

### Q-DATA-007: Raw SQL di EF Core

**Level:** Mid
**Topik:** FromSqlRaw, ExecuteSqlRaw, SQL injection prevention, stored procedures

**Pertanyaan:**
Kapan dan bagaimana menggunakan raw SQL di EF Core? Jelaskan cara yang aman dan kapan sebaiknya menggunakannya dibandingkan LINQ.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Kandidat harus memahami kapan raw SQL diperlukan dan cara menggunakannya dengan aman.

1. **FromSqlRaw untuk Queries**

   ```csharp
   // Raw SQL query dengan parameter (SQL injection safe)
   var status = "Pending";
   var orders = await _context.Orders
       .FromSqlRaw("SELECT * FROM Orders WHERE Status = {0}", status)
       .ToListAsync();

   // Dengan FromSqlInterpolated
   var orders = await _context.Orders
       .FromSqlInterpolated($"SELECT * FROM Orders WHERE Status = {status}")
       .ToListAsync();

   // Compose dengan LINQ setelah raw SQL
   var orders = await _context.Orders
       .FromSqlInterpolated($"SELECT * FROM Orders WHERE CreatedAt > {startDate}")
       .Where(o => o.CustomerId == customerId)
       .ToListAsync();
   ```

2. **ExecuteSqlRaw untuk Non-Query Commands**

   ```csharp
   // Execute raw SQL tanpa return
   await _context.Database.ExecuteSqlRawAsync(
       "UPDATE Orders SET Status = 'Expired' WHERE CreatedAt < DATEADD(day, -30, GETDATE())");

   // Dengan stored procedure
   await _context.Database.ExecuteSqlRawAsync(
       "EXEC ProcessExpiredOrders @DaysOld = {0}", 30);

   // Stored procedure dengan output parameter
   var totalParam = new SqlParameter("@Total", SqlDbType.Int) { Direction = ParameterDirection.Output };
   await _context.Database.ExecuteSqlRawAsync(
       "EXEC GetOrderTotal @OrderId = {0}, @Total = @Total OUTPUT", orderId, totalParam);
   var total = (int)totalParam.Value;
   ```

3. **SQL Injection Prevention**

   ```csharp
   // ❌ DANGEROUS - SQL Injection vulnerability
   string userInput = Request.Query["status"];
   var orders = await _context.Orders
       .FromSqlRaw($"SELECT * FROM Orders WHERE Status = '{userInput}'")  // INJECTION!
       .ToListAsync();

   // ✅ SAFE - Parameterized query
   string userInput = Request.Query["status"];
   var orders = await _context.Orders
       .FromSqlInterpolated($"SELECT * FROM Orders WHERE Status = {userInput}")
       .ToListAsync();
   ```

4. **Kapan Menggunakan Raw SQL**

   | Use Case | LINQ or Raw SQL |
   |---|---|
   | Simple CRUD | LINQ |
   | Complex joins, subqueries | Raw SQL |
   | Database-specific functions | Raw SQL |
   | Performance-critical queries | Raw SQL |
   | Bulk operations | Raw SQL |
   | Stored procedures | Raw SQL |

**Ekspektasi Mid vs Senior:**

| Aspek | Mid Level | Senior Level |
|---|---|---|
| Kedalaman | Menjelaskan FromSqlRaw, parameter safety | Menjelaskan Dapper alternative, performance comparison |
| Contoh | Contoh sederhana parameterized query | Contoh dari production optimization |

**Follow-up Questions:**
- "Bagaimana cara memutuskan antara LINQ, raw SQL, atau Dapper?"
- "Apa keuntungan dan kerugian menggunakan stored procedures?"

**Red Flags:**
- ❌ Tidak memahami SQL injection risk
- ❌ Tidak menggunakan parameterized queries

**Green Flags:**
- ✅ Menjelaskan Dapper sebagai alternative
- ✅ Memberikan contoh complex query optimization


---

### Q-DATA-008: Complex Queries dan Projections

**Level:** Senior
**Topik:** GroupBy translation, subqueries, window functions, CTE

**Pertanyaan:**
Bagaimana cara menulis complex queries di EF Core? Jelaskan GroupBy, subqueries, dan kapan perlu menggunakan raw SQL.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami limitations EF Core query translation dan alternatives.

1. **GroupBy Translation (EF Core 7.0+)**

   ```csharp
   // GroupBy dengan aggregate - di-translate ke SQL
   var orderStats = await _context.Orders
       .GroupBy(o => o.CustomerId)
       .Select(g => new
       {
           CustomerId = g.Key,
           TotalOrders = g.Count(),
           TotalAmount = g.Sum(o => o.TotalAmount),
           AverageAmount = g.Average(o => o.TotalAmount)
       })
       .ToListAsync();
   ```

2. **Subqueries**

   ```csharp
   // Subquery dalam Select
   var ordersWithLastItem = await _context.Orders
       .Select(o => new
       {
           o.Id,
           o.OrderNumber,
           LastItem = o.Items
               .OrderByDescending(i => i.CreatedAt)
               .Select(i => i.ProductName)
               .FirstOrDefault()
       })
       .ToListAsync();

   // Subquery dalam Where
   var ordersWithHighValue = await _context.Orders
       .Where(o => o.TotalAmount > _context.Orders
           .Where(o2 => o2.CustomerId == o.CustomerId)
           .Average(o2 => o2.TotalAmount))
       .ToListAsync();
   ```

3. **Window Functions (Raw SQL)**

   ```csharp
   // Window functions tidak didukung LINQ - gunakan raw SQL
   var ranking = await _context.OrderRankings
       .FromSqlRaw(@"
           SELECT 
               Id, CustomerId, TotalAmount,
               RANK() OVER (PARTITION BY CustomerId ORDER BY TotalAmount DESC) AS Rank,
               SUM(TotalAmount) OVER (PARTITION BY CustomerId) AS CustomerTotal
           FROM Orders
           WHERE CreatedAt >= DATEADD(month, -6, GETDATE())")
       .ToListAsync();
   ```

4. **Projection Optimization**

   ```csharp
   // Project ke DTO untuk avoid loading entire entities
   var orderSummaries = await _context.Orders
       .Where(o => o.Status == OrderStatus.Completed)
       .Select(o => new OrderSummaryDto
       {
           Id = o.Id,
           OrderNumber = o.OrderNumber,
           CustomerName = o.Customer.Name,
           ItemCount = o.Items.Count,
           TotalAmount = o.Items.Sum(i => i.Price * i.Quantity)
       })
       .ToListAsync();
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami GroupBy translation, limitations, raw SQL alternatives |
| Contoh | Memberikan contoh dari production complex queries |

**Follow-up Questions:**
- "Bagaimana cara debug query translation issues?"
- "Kapan sebaiknya switch ke Dapper?"

**Red Flags:**
- ❌ Tidak memahami GroupBy translation limitations
- ❌ Tidak bisa menulis subqueries

**Green Flags:**
- ✅ Menjelaskan window functions, CTE dengan raw SQL
- ✅ Memberikan contoh dari production optimization


---

### Q-DATA-009: Concurrency Control

**Level:** Senior
**Topik:** Optimistic concurrency, row version, timestamp, pessimistic locking

**Pertanyaan:**
Bagaimana cara menghandle concurrent updates di EF Core? Jelaskan optimistic vs pessimistic concurrency dan implementasinya.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami concurrency problems dan berbagai strategi untuk mengatasinya.

1. **Optimistic Concurrency dengan RowVersion**

   ```csharp
   // Entity dengan RowVersion
   public class Product
   {
       public int Id { get; set; }
       public string Name { get; set; } = string.Empty;
       public int Stock { get; set; }
       
       [Timestamp]
       public byte[] RowVersion { get; set; } = null!;
   }

   // Atau dengan fluent API
   protected override void OnModelCreating(ModelBuilder modelBuilder)
   {
       modelBuilder.Entity<Product>()
           .Property(p => p.RowVersion)
           .IsRowVersion();
   }
   ```

2. **Handling Concurrency Conflicts**

   ```csharp
   public async Task<bool> UpdateStockAsync(int productId, int quantity)
   {
       try
       {
           var product = await _context.Products.FindAsync(productId);
           if (product == null) return false;

           product.Stock -= quantity;
           await _context.SaveChangesAsync();
           return true;
       }
       catch (DbUpdateConcurrencyException ex)
       {
           var entry = ex.Entries.Single();
           var databaseValues = await entry.GetDatabaseValuesAsync();
           
           if (databaseValues == null) return false;

           // Resolve conflict: client wins
           entry.OriginalValues.SetValues(databaseValues);
           await _context.SaveChangesAsync();
           return true;
       }
   }
   ```

3. **Pessimistic Locking**

   ```csharp
   // Pessimistic locking dengan raw SQL (SQL Server)
   public async Task<Product?> GetProductForUpdateAsync(int productId)
   {
       return await _context.Products
           .FromSqlRaw("SELECT * FROM Products WITH (UPDLOCK, ROWLOCK) WHERE Id = {0}", productId)
           .FirstOrDefaultAsync();
   }
   ```

4. **Comparison Table**

   | Approach | Pros | Cons | Use Case |
   |---|---|---|---|
   | Optimistic (RowVersion) | No locks, scalable | Conflict handling required | Web apps, low conflicts |
   | Pessimistic | No conflicts | Deadlock risk, performance | High-value operations |
   | Serializable Isolation | Strong guarantees | Poor performance | Critical operations |

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami optimistic/pessimistic, resolution strategies |
| Contoh | Memberikan contoh dari production concurrency handling |

**Follow-up Questions:**
- "Bagaimana cara test concurrency handling?"
- "Kapan menggunakan pessimistic vs optimistic locking?"

**Red Flags:**
- ❌ Tidak memahami RowVersion dan concurrency
- ❌ Tidak bisa handle DbUpdateConcurrencyException

**Green Flags:**
- ✅ Menjelaskan resolution strategies
- ✅ Memberikan contoh pessimistic locking


---

### Q-DATA-010: Index Strategy

**Level:** Senior
**Topik:** Index types, composite indexes, included columns, index maintenance

**Pertanyaan:**
Bagaimana strategi indexing yang baik untuk aplikasi EF Core? Jelaskan jenis-jenis index dan cara mengoptimalkannya.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami index types, query optimization, dan index maintenance.

1. **Index Types di EF Core**

   ```csharp
   protected override void OnModelCreating(ModelBuilder modelBuilder)
   {
       // Single column index
       modelBuilder.Entity<Order>()
           .HasIndex(o => o.OrderDate);

       // Composite index
       modelBuilder.Entity<Order>()
           .HasIndex(o => new { o.CustomerId, o.OrderDate });

       // Unique index
       modelBuilder.Entity<Customer>()
           .HasIndex(c => c.Email)
           .IsUnique();

       // Index with filter
       modelBuilder.Entity<Order>()
           .HasIndex(o => o.OrderNumber)
           .HasFilter("[Status] = 'Active'");
   }
   ```

2. **Index Naming Convention**

   ```csharp
   modelBuilder.Entity<Order>()
       .HasIndex(o => new { o.CustomerId, o.OrderDate })
       .HasDatabaseName("IX_Orders_CustomerId_OrderDate");
   ```

3. **Index Selection Guidelines**

   | Query Pattern | Index Strategy |
   |---|---|
   | Equality filter (`WHERE X = @val`) | Single column index |
   | Range filter (`WHERE X > @val`) | Column in index |
   | Multiple filters | Composite index (most selective first) |
   | Sort + filter | Filtered columns first, sort column next |
   | Covering query | Include non-key columns |

4. **Index Maintenance**

   ```sql
   -- Rebuild fragmented indexes
   ALTER INDEX IX_Orders_CustomerId ON Orders REBUILD;

   -- Update statistics
   UPDATE STATISTICS Orders;

   -- Check index usage
   SELECT * FROM sys.dm_db_index_usage_stats;
   ```

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami index types, covering indexes, maintenance |
| Contoh | Memberikan contoh dari production index optimization |

**Follow-up Questions:**
- "Bagaimana cara mengidentifikasi missing indexes?"
- "Apa dampak terlalu banyak index terhadap write performance?"

**Red Flags:**
- ❌ Tidak memahami composite indexes
- ❌ Tidak bisa menjelaskan index selection

**Green Flags:**
- ✅ Menjelaskan covering indexes dan included columns
- ✅ Memberikan contoh dari production optimization


---

### Q-DATA-011: Bulk Operations

**Level:** Senior
**Topik:** Bulk insert, update, delete, EF Core vs bulk extensions

**Pertanyaan:**
Bagaimana cara melakukan bulk operations secara efisien di EF Core? Jelaskan pendekatan untuk bulk insert, update, dan delete.

**Jawaban yang Diharapkan:**

> [!NOTE]
> Senior developer harus memahami limitations EF Core untuk bulk operations dan alternatives.

1. **EF Core Default Bulk Insert**

   ```csharp
   // Default: AddRange + SaveChanges
   // ⚠️ WARNING: Satu INSERT per entity - lambat untuk data besar
   _context.Orders.AddRange(orders);
   await _context.SaveChangesAsync();
   ```

2. **EF Core 8.0+ ExecuteUpdate/Delete**

   ```csharp
   // Bulk update tanpa load entities
   await _context.Orders
       .Where(o => o.Status == OrderStatus.Pending && o.CreatedAt < DateTime.Now.AddDays(-30))
       .ExecuteUpdateAsync(setters => setters
           .SetProperty(o => o.Status, OrderStatus.Expired));

   // Bulk delete
   await _context.OrderItems
       .Where(i => i.Order.Status == OrderStatus.Cancelled)
       .ExecuteDeleteAsync();
   ```

3. **Third-Party Bulk Extensions**

   ```csharp
   // EFCore.BulkExtensions untuk true bulk operations
   await _context.BulkInsertAsync(orders);
   await _context.BulkUpdateAsync(orders);
   await _context.BulkDeleteAsync(orders);

   // Dengan options
   await _context.BulkInsertAsync(orders, options =>
   {
       options.BatchSize = 1000;
       options.UseTempDB = true;
   });
   ```

4. **Performance Comparison**

   | Method | 1000 records | 10,000 records | 100,000 records |
   |---|---|---|---|
   | AddRange + SaveChanges | ~5s | ~50s | ~8min |
   | ExecuteUpdate/Delete | ~100ms | ~500ms | ~3s |
   | BulkExtensions | ~200ms | ~1s | ~5s |
   | Raw SQL BULK INSERT | ~50ms | ~200ms | ~1s |

**Ekspektasi Senior:**

| Aspek | Ekspektasi |
|---|---|
| Kedalaman | Memahami bulk methods, trade-offs, alternatives |
| Contoh | Memberikan contoh dari production bulk operations |

**Follow-up Questions:**
- "Kapan menggunakan bulk extensions vs raw SQL?"
- "Bagaimana cara handle bulk operations dalam transaction?"

**Red Flags:**
- ❌ Tidak mengetahui ExecuteUpdate/Delete
- ❌ Menggunakan AddRange untuk data besar tanpa alternatif

**Green Flags:**
- ✅ Menjelaskan EFCore.BulkExtensions
- ✅ Memberikan contoh dari production optimization

