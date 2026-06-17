# SQL Server Review Checklist (High Volume & Millions of Records)

Dokumen ini berisi panduan review database SQL Server yang menangani jutaan baris data, dirancang khusus untuk memastikan performa query tinggi dan mencegah degradasi database pada production environment.

---

## 1. Query Review & Optimization Checklist

### 1.1 Index Usage & SARGability (Search Argument Able)
* [ ] **No Functions on Indexed Columns:** Jangan gunakan function (seperti `LEFT()`, `CONVERT()`, `YEAR()`) pada kolom yang masuk dalam index pada `WHERE` clause.
  * *Bad:* `WHERE LEFT(CardNumber, 4) = '1234'`
  * *Good:* `WHERE CardNumber LIKE '1234%'`
* [ ] **Avoid Implicit Conversion:** Tipe data parameter input (.NET API) harus sesuai dengan tipe data kolom SQL Server.
  * *Bad:* Kolom `varchar` diquery dengan parameter C# bernilai `string` (yang defaultnya menjadi `nvarchar`), menyebabkan SQL melakukan implicit conversion dan memicu table scan.
  * *Good:* Pastikan kolom `varchar` didefinisikan dengan `.IsUnicode(false)` di Fluent API EF Core.
* [ ] **Optimize JOIN Operations:** 
  * Selalu gunakan `JOIN` dengan kolom index sebagai foreign key.
  * Hindari `JOIN` berbasis substring atau concatenations kolom.
* [ ] **Eliminate SELECT *:** Selalu sebutkan kolom yang Anda perlukan secara eksplisit untuk meminimalkan I/O overhead dan menghemat memory grant.

### 1.2 Subqueries vs CTE vs Temporary Tables
* [ ] **Common Table Expressions (CTE):** Gunakan CTE untuk kejelasan logika kode, namun ingat bahwa CTE di SQL Server hanyalah inline query views dan tidak menyimpan data secara temporer. Jika CTE di-JOIN berulang kali, query tersebut akan dijalankan berkali-kali.
* [ ] **Temp Tables (#TempTable):** Gunakan tabel temporer jika data yang diproses berukuran besar (> 50,000 baris) dan memerlukan index tambahan untuk query lanjutan di dalam stored procedure.
* [ ] **Table Variables (@TableVar):** Hindari penggunaan variabel tabel untuk data berukuran besar karena SQL Server mengasumsikan hanya ada 1 baris data di dalamnya (cardinality estimation flaw) yang sering menyebabkan optimalisasi execution plan menjadi sangat buruk.

---

## 2. Database Schema Design Checklist

### 2.1 Indexing Strategy
* [ ] **Clustered Index Selection:** Clustered index harus bertipe data yang unik, sempit (narrow), dan berurutan secara monoton (seperti `INT IDENTITY` atau `BIGINT IDENTITY` dibanding `UNIQUEIDENTIFIER` acak) guna mencegah *page fragmentation*.
* [ ] **Covering Index:** Gunakan klausul `INCLUDE` pada non-clustered index untuk menyertakan kolom yang sering dicari dalam `SELECT` namun tidak masuk dalam kriteria filter `WHERE`.
  ```sql
  CREATE NONCLUSTERED INDEX IX_Orders_Status_Include_Total
  ON dbo.Orders (Status)
  INCLUDE (TotalAmount, UserId);
  ```
* [ ] **Filtered Index:** Untuk tabel berukuran besar, jika filter query sering menyasar nilai yang konstan (misal: data transaksi aktif saja), buatlah filtered index.
  ```sql
  CREATE NONCLUSTERED INDEX IX_Orders_Unprocessed
  ON dbo.Orders (CreatedAt)
  WHERE Status = 1; -- 1: Pending / Unprocessed
  ```

### 2.2 Data Types Selection Guide
* [ ] Gunakan `DATETIMEOFFSET` untuk pencatatan waktu yang akurat antar zona waktu global.
* [ ] Hindari `NVARCHAR(MAX)` jika panjang maksimal kolom tidak melebihi 4,000 karakter, karena data bertipe `MAX` akan disimpan *out-of-row* yang memperlambat performa I/O read.
* [ ] Gunakan `INT` (hingga 2 miliar baris) atau `BIGINT` untuk tabel transaksi berskala besar.

---

## 3. Stored Procedure & Transaction Review

### 3.1 Performance Best Practices
* [ ] **SET NOCOUNT ON:** Letakkan `SET NOCOUNT ON;` di awal stored procedure untuk menghindari pengiriman pesan jumlah baris terpengaruh (e.g., "1 row affected") ke client yang meningkatkan overhead network traffic.
* [ ] **Avoid Cursors:** Selalu gunakan operasi berbasis *set-based* daripada cursor berulang-ulang (*row-by-row / RBAR*).
* [ ] **Parameter Sniffing Prevention:** Gunakan `OPTIMIZE FOR` hint atau simpan parameter ke local variable jika stored procedure menghasilkan execution plan yang tidak konsisten saat dijalankan dengan parameter berbeda.

### 3.2 Error Handling & Transaction Safety
```sql
CREATE PROCEDURE dbo.sp_CreateOrderTransaction
    @UserId UNIQUEIDENTIFIER,
    @TotalAmount DECIMAL(18,2)
AS
BEGIN
    SET NOCOUNT ON;
    SET XACT_ABORT ON; -- Automically roll back transaction on runtime error

    BEGIN TRY
        BEGIN TRANSACTION;

        -- 1. Deduct stock / perform update
        -- 2. Insert order header
        -- 3. Insert order items

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        -- Log error details
        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();
        
        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

---

## 4. Performance Diagnostics & DMVs (Dynamic Management Views)

### 4.1 Query Mendeteksi Index yang Hilang (Missing Indexes)
```sql
SELECT TOP 10
    d.statement AS TableName,
    gs.unique_compiles,
    gs.user_seeks,
    gs.user_scans,
    gs.avg_total_user_cost * gs.avg_user_impact * (gs.user_seeks + gs.user_scans) AS ImprovementValue,
    d.equality_columns,
    d.inequality_columns,
    d.included_columns
FROM sys.dm_db_missing_index_groups g
JOIN sys.dm_db_missing_index_group_stats gs ON gs.group_handle = g.index_group_handle
JOIN sys.dm_db_missing_index_details d ON d.index_handle = g.index_handle
ORDER BY ImprovementValue DESC;
```

### 4.2 Query Mencari Slow Queries Terlama
```sql
SELECT TOP 10
    total_worker_time/execution_count AS AvgCPUTime_MicroSec,
    total_elapsed_time/execution_count AS AvgElapsedTime_MicroSec,
    total_logical_reads/execution_count AS AvgLogicalReads,
    execution_count,
    SUBSTRING(st.text, (qs.statement_start_offset/2) + 1,
         ((CASE qs.statement_end_offset 
               WHEN -1 THEN DATALENGTH(st.text) 
               ELSE qs.statement_end_offset END 
           - qs.statement_start_offset)/2) + 1) AS QueryText
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) st
ORDER BY total_worker_time/execution_count DESC;
```
