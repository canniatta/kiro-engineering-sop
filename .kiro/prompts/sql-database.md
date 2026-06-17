# Prompts: SQL Server Database

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 4: #[[file:docs/03-prompt-library.md]] (Section "SQL Server Database Prompts")
> - SQL review checklist: #[[file:docs/09-template-sql-review-checklist.md]]
> - Performance playbook: #[[file:docs/19-playbook-performance-tuning-sql.md]]

---

## 1. Query Optimization dengan Execution Plan Analysis

**Kapan digunakan:** Saat memiliki query SQL Server yang lambat pada tabel berukuran jutaan baris dan perlu rekomendasi index serta query rewrite.

```text
Here is a slow-running SQL query:
```sql
[Paste SQL Query Here]
```
The table [Nama Tabel] contains [Jumlah Record] million records.
Act as a Database Administrator and Senior SQL Engineer. Analyze this query and recommend:
1. Indexing strategy (Clustered, Non-Clustered, Filtered, or Covering indexes).
2. Query rewrite improvements (e.g., replacing subqueries, optimizing JOINs, using CTEs, avoiding local variables in WHERE clause).
3. Execution plan warnings to look out for (e.g., Index Scan vs Index Seek, Key Lookup, Implicit Conversion).
4. Provide the optimized SQL query side-by-side with the original query.
```

> [!TIP]
> - Paste query yang lengkap termasuk semua JOIN dan WHERE clause
> - Sertakan informasi jumlah record di setiap tabel yang terlibat
> - Jika punya actual execution plan (XML), sertakan untuk analisis yang lebih akurat
> - Setelah mendapat rekomendasi, validasi dengan menjalankan di environment non-production

> [!WARNING]
> Rekomendasi index dari AI harus divalidasi terhadap workload keseluruhan. Index baru bisa mempercepat SELECT tapi memperlambat INSERT/UPDATE. Gunakan `19-playbook-performance-tuning-sql.md` sebagai panduan validasi.

---

## 2. Idempotent Migration Script

**Kapan digunakan:** Membuat script perubahan schema database yang aman di-deploy berulang kali tanpa error — penting untuk CI/CD pipeline.

```text
Write an idempotent SQL migration script to: [Aksi, misal: Add Column 'IsVerified' to Table 'Users'].
The script must:
1. Check if the column/table already exists before applying changes (to prevent runtime errors).
2. Run inside a transaction block with TRY/CATCH error handling.
3. Set XACT_ABORT ON.
4. Include rollback scripts.
5. Update metadata tracking tables or history if applicable.
```

> [!TIP]
> - Ganti `[Aksi]` dengan deskripsi perubahan yang spesifik (nama kolom, tipe data, constraint)
> - Sebutkan environment target (dev, staging, production) karena memengaruhi safety level
> - Untuk perubahan besar (rename column, change data type), minta juga backward-compatibility strategy
> - Selalu test script di environment non-production sebelum apply ke production

> [!CAUTION]
> Migration script untuk production database termasuk area **wajib verifikasi manual**. Jangan langsung deploy tanpa review oleh DBA atau Tech Lead.

---

## 3. Stored Procedure Optimization

**Kapan digunakan:** Menganalisis dan mengoptimalkan stored procedure SQL Server yang memiliki performa lambat di production.

```text
Analisa stored procedure berikut.

Fokus:
- Index recommendation
- Table scan
- Missing covering index
- Temp table usage
- Parameter sniffing
- Query cost

Berikan versi optimisasi.
```

> [!TIP]
> - Tempelkan definisi lengkap DDL Stored Procedure beserta tipe parameter inputnya.
> - Rujuk berkas checklist di #[[file:docs/09-template-sql-review-checklist.md]] dan playbook tuning di #[[file:docs/19-playbook-performance-tuning-sql.md]] sebagai standar perbaikan.
> - Mintalah AI memberikan analisis estimasi execution plan perbagian query di dalam stored procedure.

> [!IMPORTANT]
> Selalu uji fungsionalitas stored procedure yang baru dioptimalkan dengan data pengujian non-production untuk memastikan integritas data (TRY/CATCH block, transactional safety) tetap terjaga.

---

## 4. SQL Query Performance Audit (Expert Review)

**Kapan digunakan:** Menganalisis query SQL Server secara mendalam dengan fokus spesifik pada scan, missing index, sorting, key lookup, dan TempDB.

```text
Act as SQL Server Performance Expert.

Review query berikut.

Cari:
- Scan
- Missing Index
- Sort
- Key Lookup
- TempDB Issue

Berikan query optimisasi.
```

> [!TIP]
> - Rujuk berkas checklist di #[[file:docs/09-template-sql-review-checklist.md]] dan playbook tuning di #[[file:docs/19-playbook-performance-tuning-sql.md]] sebagai standar perbaikan.
> - Sebutkan perkiraan jumlah data/volume baris pada tabel-tabel terkait jika memungkinkan.

