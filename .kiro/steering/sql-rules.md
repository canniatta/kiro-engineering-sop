---
inclusion: fileMatch
fileMatchPattern: "*.sql"
---

# Standar SQL Server Review & Performance

> [!NOTE]
> **Source of Truth**
>
> - SQL Review Checklist: #[[file:docs/09-template-sql-review-checklist.md]]
> - Performance Tuning Playbook: #[[file:docs/19-playbook-performance-tuning-sql.md]]
> - Rule 11 (SQL): #[[file:docs/02-kiro-setup-and-configuration.md]] (section "Rules")

## SARGability Rules

Query yang SARGable (Search ARGument Able) memungkinkan SQL Server menggunakan index seek alih-alih table scan.

> [!IMPORTANT]
> Jangan gunakan function pada kolom yang termasuk dalam index di `WHERE` clause.

| Pattern | Status | Penjelasan |
|---|---|---|
| `WHERE DATEPART(year, CreatedAt) = 2026` | BAD | Function membungkus kolom — index scan |
| `WHERE CreatedAt >= '2026-01-01' AND CreatedAt < '2027-01-01'` | GOOD | Range filter — index seek |
| `WHERE LEFT(CardNumber, 4) = '1234'` | BAD | Function pada kolom indexed |
| `WHERE CardNumber LIKE '1234%'` | GOOD | Prefix LIKE — index seek |

### Implicit Conversion

Tipe data parameter input (.NET) harus sesuai dengan tipe kolom SQL Server. Kolom `varchar` yang di-query dengan parameter `nvarchar` (default C# `string`) menyebabkan implicit conversion dan table scan.

```csharp
// BAD — string C# = nvarchar, kolom = varchar → implicit conversion
modelBuilder.Entity<Order>().Property(o => o.OrderNumber).HasColumnType("varchar(20)");

// GOOD — Definisikan .IsUnicode(false) di EF Core Fluent API
modelBuilder.Entity<Order>().Property(o => o.OrderNumber)
    .HasColumnType("varchar(20)")
    .IsUnicode(false);
```

## Strategi Indexing

### Clustered Index Selection

Clustered index harus bertipe data:
- **Unik** — tidak duplikat
- **Sempit** (narrow) — `INT` atau `BIGINT`, bukan `UNIQUEIDENTIFIER`
- **Berurutan monoton** — `IDENTITY` atau sequential, mencegah page fragmentation

### Covering Index dengan INCLUDE

Tambahkan kolom yang sering di-SELECT ke dalam `INCLUDE` agar SQL Server tidak perlu key lookup ke clustered index.

```sql
CREATE NONCLUSTERED INDEX IX_Orders_UserId_Includes
ON dbo.Orders (UserId)
INCLUDE (OrderNumber, TotalAmount, Status);
```

### Filtered Index

Untuk tabel besar di mana query harian menyasar subset data tertentu:

```sql
CREATE NONCLUSTERED INDEX IX_Orders_Status_Pending
ON dbo.Orders (CreatedAt)
WHERE Status = 1; -- 1: Pending
```

### Penamaan Index

Format: `IX_<Table>_<Column1>[_Column2]`

```sql
IX_Orders_UserId
IX_Orders_Status_CreatedAt
IX_OrderItems_OrderId_ProductId
```

## Query Patterns — Bad vs Good

### Date Filtering

```sql
-- BAD: Function pada kolom (table scan)
SELECT Id, CreatedAt FROM dbo.Orders
WHERE DATEPART(year, CreatedAt) = 2026 AND DATEPART(month, CreatedAt) = 6;

-- GOOD: Range filter (index seek)
SELECT Id, CreatedAt FROM dbo.Orders
WHERE CreatedAt >= '2026-06-01T00:00:00Z' AND CreatedAt < '2026-07-01T00:00:00Z';
```

### OR Optimization dengan UNION ALL

```sql
-- BAD: OR pada kolom berbeda (sering menyebabkan table scan)
SELECT Id, CustomerEmail FROM dbo.Orders
WHERE OrderNumber = 'ORD-1001' OR CustomerEmail = 'buyer@gmail.com';

-- GOOD: UNION ALL memicu dua index seek terpisah
SELECT Id, CustomerEmail FROM dbo.Orders WHERE OrderNumber = 'ORD-1001'
UNION ALL
SELECT Id, CustomerEmail FROM dbo.Orders
WHERE CustomerEmail = 'buyer@gmail.com' AND OrderNumber <> 'ORD-1001';
```

## Strategi Pagination

> [!WARNING]
> `OFFSET-FETCH` lambat pada halaman akhir karena SQL Server harus memindai semua baris sebelumnya lalu membuangnya.

### Keyset Pagination (Seek Method)

```sql
-- Halaman pertama
SELECT TOP 10 Id, OrderNumber, TotalAmount, CreatedAt
FROM dbo.Orders
ORDER BY CreatedAt DESC, Id DESC;

-- Halaman berikutnya (gunakan nilai terakhir dari halaman sebelumnya)
SELECT TOP 10 Id, OrderNumber, TotalAmount, CreatedAt
FROM dbo.Orders
WHERE CreatedAt < @LastCreatedAt
   OR (CreatedAt = @LastCreatedAt AND Id < @LastId)
ORDER BY CreatedAt DESC, Id DESC;
```

Hasil: query response time konsisten < 50ms baik di halaman 2 maupun halaman 10,000.

## Standar Stored Procedure

| Aspek | Aturan |
|---|---|
| `SET NOCOUNT ON` | Letakkan di awal — menghindari overhead network "rows affected" |
| Cursors | Hindari — gunakan operasi set-based |
| Parameter sniffing | Gunakan `OPTIMIZE FOR` hint atau local variable |
| Transaction | Gunakan `SET XACT_ABORT ON` + `TRY/CATCH` |

### Template Error Handling

```sql
CREATE PROCEDURE dbo.sp_CreateOrderTransaction
    @UserId UNIQUEIDENTIFIER,
    @TotalAmount DECIMAL(18,2)
AS
BEGIN
    SET NOCOUNT ON;
    SET XACT_ABORT ON;

    BEGIN TRY
        BEGIN TRANSACTION;

        -- Business logic here

        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        IF @@TRANCOUNT > 0
            ROLLBACK TRANSACTION;

        DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity INT = ERROR_SEVERITY();
        DECLARE @ErrorState INT = ERROR_STATE();

        RAISERROR(@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END;
```

## Data Types

| Tipe | Kapan Digunakan |
|---|---|
| `DATETIMEOFFSET` | Pencatatan waktu yang akurat antar zona waktu |
| `INT` / `BIGINT` | Primary key untuk tabel transaksi berskala besar |
| `DECIMAL(18,2)` | Nilai moneter — jangan gunakan `FLOAT` |
| `VARCHAR(n)` | Teks ASCII — hemat storage dibanding `NVARCHAR` |
| `NVARCHAR(n)` | Teks Unicode (multi-bahasa) |
| `NVARCHAR(MAX)` | Hindari jika panjang < 4000 karakter — data MAX disimpan out-of-row |

## Penamaan Objek Database

| Objek | Konvensi | Contoh |
|---|---|---|
| Tabel | PascalCase, singular | `Order`, `OrderItem`, `Customer` |
| Kolom | PascalCase | `CreatedAt`, `TotalAmount` |
| Primary Key | `PK_<Table>` | `PK_Order` |
| Foreign Key | `FK_<Table>_<RefTable>` | `FK_OrderItem_Order` |
| Index | `IX_<Table>_<Column>` | `IX_Order_CustomerId` |
| Stored Procedure | `sp_<Domain><Action>` | `sp_OrderCreate`, `sp_OrderGetById` |
| View | `vw_<Name>` | `vw_ActiveOrders` |
