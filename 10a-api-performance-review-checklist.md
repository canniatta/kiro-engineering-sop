# API Performance Review Checklist (.NET 8 & EF Core)

Dokumen ini mendefinisikan panduan audit performa API untuk mendeteksi bottleneck I/O, pemborosan memori CPU, dan masalah konkurensi pada backend **.NET 8 Web API** yang terintegrasi dengan **SQL Server**.

---

## 1. N+1 Query Problem

### 1.1 Masalah (Problem)
N+1 query terjadi ketika aplikasi melakukan 1 query utama untuk mendapatkan daftar entitas, kemudian untuk setiap entitas yang dikembalikan, aplikasi menjalankan query tambahan untuk mengambil entitas relasinya. Hal ini memicu ratusan round-trip I/O ke SQL Server.

### 1.2 Contoh Kode & Rekomendasi
* **Bad (N+1 Query Triggered):**
  ```csharp
  // Mengambil orders, lalu melakukan loop untuk memuat Customer Name satu per satu
  var orders = await dbContext.Orders.ToListAsync(); 
  foreach (var order in orders)
  {
      // TRIGGER: EF Core memicu query ke DB secara terpisah untuk setiap baris!
      order.CustomerName = (await dbContext.Customers.FindAsync(order.CustomerId))?.Name;
  }
  ```
* **Good (Eager Loading with Include & Projection):**
  ```csharp
  // Mereduksi menjadi hanya 1 query tunggal menggunakan JOIN di SQL Server
  var orders = await dbContext.Orders
      .AsNoTracking()
      .Select(o => new OrderDto(
          o.Id,
          o.TotalAmount,
          o.Customer.Name // Otomatis diterjemahkan menjadi LEFT JOIN di SQL
      ))
      .ToListAsync(cancellationToken);
  ```

---

## 2. Multiple DbContext Calls in Single Transaction

### 2.1 Masalah (Problem)
Instansiasi atau pemanggilan `SaveChanges()` pada `DbContext` berkali-kali dalam satu request transaksi yang sama memicu pembukaan/penutupan koneksi database berulang kali (network latency overhead) serta risiko hilangnya transaksi atomik (rollback fail).

### 2.2 Contoh Kode & Rekomendasi
* **Bad (Multiple SaveChanges):**
  ```csharp
  public async Task CreateOrderAsync(Order order, OrderItem item)
  {
      dbContext.Orders.Add(order);
      await dbContext.SaveChangesAsync(); // Panggilan 1 (Koneksi dibuka & ditutup)
      
      item.OrderId = order.Id;
      dbContext.OrderItems.Add(item);
      await dbContext.SaveChangesAsync(); // Panggilan 2 (Koneksi dibuka & ditutup lagi!)
  }
  ```
* **Good (Unit of Work / Batch SaveChanges):**
  ```csharp
  public async Task CreateOrderAsync(Order order, List<OrderItem> items)
  {
      dbContext.Orders.Add(order);
      foreach(var item in items)
      {
          order.Items.Add(item);
      }
      
      // Dipanggil CUKUP 1 KALI di akhir proses transaksi. 
      // EF Core secara cerdas melakukan batching SQL insert dalam 1 round-trip.
      await dbContext.SaveChangesAsync(cancellationToken); 
  }
  ```

---

## 3. Asynchronous Issues (Thread Pool Starvation)

### 3.1 Masalah (Problem)
Pencampuran kode synchronous dan asynchronous (Sync-over-Async) seperti menggunakan `.Result`, `.GetAwaiter().GetResult()`, atau `.Wait()` akan mengunci thread utama (blocking) dan dengan cepat memicu *Thread Pool Starvation* yang mengakibatkan request API menjadi timeout secara massal.

### 3.2 Contoh Kode & Rekomendasi
* **Bad (Deadlock Risk / Blocking):**
  ```csharp
  [HttpGet("{id}")]
  public IActionResult GetOrder(Guid id)
  {
      // TRIGGER: Thread terkunci menunggu hasil async (.Result)
      var order = dbContext.Orders.FindAsync(id).Result; 
      return Ok(order);
  }
  ```
* **Good (Pure Async & Propagation):**
  ```csharp
  [HttpGet("{id}")]
  public async Task<IActionResult> GetOrder(Guid id, CancellationToken ct)
  {
      // Thread dibebaskan kembali ke thread pool selama menunggu DB merespon
      var order = await dbContext.Orders.FindAsync(new object[] { id }, ct); 
      return Ok(order);
  }
  ```

---

## 4. Unnecessary LINQ to Objects (Memory vs DB)

### 4.1 Masalah (Problem)
Memindahkan data mentah (raw data) dalam jumlah besar dari SQL Server ke memori server API (.NET RAM) dengan memicu pemanggilan `.ToList()` sebelum memfilter data menggunakan LINQ.

### 4.2 Contoh Kode & Rekomendasi
* **Bad (Filter di Memory):**
  ```csharp
  // Database mengirim JUTAAN record ke RAM server API, baru kemudian difilter
  var users = dbContext.Users.ToList() 
      .Where(u => u.RegisterDate > cutoffDate)
      .ToList();
  ```
* **Good (Filter di SQL Server - IQueryable):**
  ```csharp
  // EF Core mengeksekusi filter WHERE di SQL Server. Hanya data yang cocok yang dikirim ke API.
  var users = await dbContext.Users
      .Where(u => u.RegisterDate > cutoffDate)
      .AsNoTracking()
      .ToListAsync(cancellationToken);
  ```

---

## 5. JSON Serialization Bottlenecks

### 5.1 Masalah (Problem)
Proses serialisasi payload JSON berukuran besar ke respons HTTP HTTP menggunakan reflection runtime memakan resource CPU tinggi.

### 5.2 Contoh Kode & Rekomendasi
* **Rekomendasi Utama (.NET 8 Source Generators):**
  Gunakan **System.Text.Json Source Generator** untuk menghilangkan overhead reflection saat runtime (mengompilasi serialization code saat build time).

```csharp
using System.Text.Json.Serialization;

namespace App.Application.Contexts;

// Definisikan context serialisasi JSON secara statis
[JsonSerializable(typeof(List<OrderDto>))]
[JsonSerializable(typeof(OrderDto))]
public partial class OrderJsonContext : JsonSerializerContext
{
}

// Konfigurasi di Program.cs
// builder.Services.ConfigureHttpJsonOptions(options => {
//    options.SerializerOptions.TypeInfoResolverChain.Insert(0, OrderJsonContext.Default);
// });
```

---

## 6. Memory Allocation & Boxing Issues

### 6.1 Masalah (Problem)
Alokasi memori berlebih yang terus-menerus memicu kerja *Garbage Collector (GC)* berulang kali, yang memperlambat kinerja aplikasi (GC Pause). Masalah ini dipicu oleh manipulasi string berulang dalam loop (string immutable) atau boxing (tipe data value ke object).

### 6.2 Contoh Kode & Rekomendasi
* **Bad (Heavy Memory Allocation):**
  ```csharp
  // String concatenation di dalam loop membuat objek string baru di RAM setiap perulangan
  string logMessage = "";
  foreach (var item in orderItems)
  {
      logMessage += $"Item: {item.Name}, Qty: {item.Quantity}; "; 
  }
  ```
* **Good (StringBuilder Allocation Saver):**
  ```csharp
  // Menggunakan internal buffer terpusat untuk meminimalkan beban Garbage Collector
  var sb = new StringBuilder();
  foreach (var item in orderItems)
  {
      sb.Append("Item: ").Append(item.Name).Append(", Qty: ").Append(item.Quantity).Append("; ");
  }
  string logMessage = sb.ToString();
  ```
