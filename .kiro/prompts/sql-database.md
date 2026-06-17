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
