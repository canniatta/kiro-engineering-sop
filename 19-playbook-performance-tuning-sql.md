# SQL Server Performance Tuning Playbook (Millions of Records)

Playbook ini dirancang khusus untuk database administrator (DBA) dan engineer .NET untuk mengoptimalkan database SQL Server yang mengelola jutaan baris data, dengan fokus pada kecepatan respon query, efisiensi resource, dan skalabilitas data transaksional.

---

## 1. Query Rewriting Patterns (Bad vs Good)

### Pattern 1.1: Fungsi pada WHERE Clause (SARGability Violation)
* **Bad (Table/Index Scan):**
  ```sql
  SELECT Id, CreatedAt 
  FROM dbo.Orders 
  WHERE DATEPART(year, CreatedAt) = 2026 AND DATEPART(month, CreatedAt) = 6;
  ```
* **Good (Index Seek):**
  ```sql
  SELECT Id, CreatedAt 
  FROM dbo.Orders 
  WHERE CreatedAt >= '2026-06-01T00:00:00Z' AND CreatedAt < '2026-07-01T00:00:00Z';
  ```
* *Penjelasan:* SQL Server tidak dapat menggunakan index seek pada kolom `CreatedAt` jika kolom tersebut dibungkus di dalam function `DATEPART()`. SQL terpaksa mengevaluasi seluruh baris tabel (Scan).

### Pattern 1.2: OR dengan Kolom Berbeda
* **Bad:**
  ```sql
  SELECT Id, CustomerEmail 
  FROM dbo.Orders 
  WHERE OrderNumber = 'ORD-1001' OR CustomerEmail = 'buyer@gmail.com';
  ```
* **Good (UNION ALL):**
  ```sql
  SELECT Id, CustomerEmail FROM dbo.Orders WHERE OrderNumber = 'ORD-1001'
  UNION ALL
  SELECT Id, CustomerEmail FROM dbo.Orders WHERE CustomerEmail = 'buyer@gmail.com' AND OrderNumber <> 'ORD-1001';
  ```
* *Penjelasan:* Penggunaan operator `OR` pada kolom yang berbeda sering kali membingungkan query optimizer, yang akhirnya memilih Table Scan. Memecah query menggunakan `UNION ALL` memicu dua operasi Index Seek terpisah yang jauh lebih cepat.

---

## 2. Strategi Indexing Tingkat Lanjut

### 2.1 Covering Indexes (Index dengan INCLUDE)
Jika query Anda sering memproses pencarian berdasarkan satu kolom tetapi mengembalikan kolom lainnya, buatlah Covering Index agar SQL Server tidak perlu melakukan operasi *Key Lookup* yang mahal untuk mengambil data sisa dari clustered index.

```sql
-- Query target
SELECT OrderNumber, TotalAmount, Status FROM dbo.Orders WHERE UserId = @UserId;

-- Optimized Non-Clustered Index
CREATE NONCLUSTERED INDEX IX_Orders_UserId_Includes
ON dbo.Orders (UserId)
INCLUDE (OrderNumber, TotalAmount, Status);
```

### 2.2 Filtered Indexes (Index Tersaring)
Jika tabel Anda menampung jutaan data tetapi pencarian query harian 95% hanya menyasar baris data tertentu (misalnya, pesanan yang belum diproses), batasi cakupan index dengan klausa `WHERE`. Hal ini menghemat ukuran storage index di RAM secara drastis.

```sql
CREATE NONCLUSTERED INDEX IX_Orders_Status_Pending
ON dbo.Orders (UserId)
WHERE Status = 1; -- 1: Pending
```

---

## 3. Strategi Pagination pada Jutaan Record (Offset vs Keyset)

### 3.1 Mengapa `OFFSET-FETCH` Lambat pada Halaman Akhir?
Metode pagination tradisional `OFFSET 1000000 ROWS FETCH NEXT 10 ROWS ONLY` mengharuskan SQL Server memindai 1.000.000 baris pertama terlebih dahulu sebelum membuangnya dan menyisakan 10 baris terakhir. Ini menyebabkan pemborosan I/O disk yang parah.

### 3.2 Solusi: Keyset Pagination (Seek Method)
Ingatlah posisi ID atau timestamp baris terakhir dari halaman sebelumnya, lalu filter query berdasarkan nilai tersebut.

* **Query Halaman Pertama:**
  ```sql
  SELECT TOP 10 Id, OrderNumber, TotalAmount, CreatedAt
  FROM dbo.Orders
  ORDER BY CreatedAt DESC, Id DESC;
  ```
* **Query Halaman Kedua (menggunakan data baris ke-10: `LastCreatedAt`, `LastId`):**
  ```sql
  SELECT TOP 10 Id, OrderNumber, TotalAmount, CreatedAt
  FROM dbo.Orders
  WHERE CreatedAt < @LastCreatedAt OR (CreatedAt = @LastCreatedAt AND Id < @LastId)
  ORDER BY CreatedAt DESC, Id DESC;
  ```
* *Hasil:* SQL Server akan melakukan operasi **Index Seek** secara konsisten baik pada halaman ke-2 maupun halaman ke-10,000, menjaga query response time tetap di bawah 50ms.

---

## 4. Partisi Tabel (Table Partitioning)

Tabel transaksi dengan baris data > 50 juta harus dipartisi, umumnya berdasarkan rentang waktu (misalnya bulanan atau tahunan) untuk mempermudah manajemen arsip data dan meningkatkan performa query (Partition Pruning).

### 4.1 Implementasi Partisi Tabel Bulanan
```sql
-- 1. Create Partition Function
CREATE PARTITION FUNCTION PF_Orders_Monthly (DATETIMEOFFSET)
AS RANGE RIGHT FOR VALUES (
    '2026-05-01T00:00:00+00:00',
    '2026-06-01T00:00:00+00:00',
    '2026-07-01T00:00:00+00:00'
);

-- 2. Create Partition Scheme (linking to Filegroups)
CREATE PARTITION SCHEME PS_Orders_Monthly
AS PARTITION PF_Orders_Monthly
TO ([PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY]);

-- 3. Create Partitioned Table
CREATE TABLE dbo.Orders_Partitioned (
    Id UNIQUEIDENTIFIER DEFAULT NEWID(),
    UserId UNIQUEIDENTIFIER NOT NULL,
    CreatedAt DATETIMEOFFSET NOT NULL,
    TotalAmount DECIMAL(18,2) NOT NULL
) ON PS_Orders_Monthly(CreatedAt);
```

---

## 5. Monitoring & Maintenance Rutin

* [ ] **Update Statistics Bulanan/Mingguan:** Database dengan data dinamis memerlukan statistik yang segar agar query optimizer membuat execution plan yang akurat. Jalankan `UPDATE STATISTICS dbo.Orders WITH FULLSCAN;` secara berkala.
* [ ] **Rebuild / Reorganize Indexes:** Lakukan defragmentasi index jika fragmentation level > 30% menggunakan query rebuild:
  ```sql
  ALTER INDEX ALL ON dbo.Orders REBUILD WITH (ONLINE = ON);
  ```
