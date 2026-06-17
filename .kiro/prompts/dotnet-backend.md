# Prompts: .NET 8 Backend Development

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 2: #[[file:03-prompt-library.md]] (Section ".NET 8 Backend Development Prompts")
> - Template Clean Architecture: #[[file:12-template-clean-architecture-dotnet8.md]]

---

## 1. CQRS Command dengan MediatR & FluentValidation

**Kapan digunakan:** Menulis command baru untuk memproses business logic write (create/update/delete) di .NET 8 dengan pattern CQRS.

```text
Write a complete CQRS Command using MediatR in .NET 8 for: [Deskripsi Aksi, misal: UpdateUserProfile].
The command should:
1. Use .NET 8 primary constructors.
2. Use FluentValidation to validate input parameters (e.g., Email must be valid, Phone number pattern, etc.).
3. Inject `IUserRepository` and `IUnitOfWork` in the Command Handler.
4. Publish a domain event `UserProfileUpdatedEvent` upon successful update.
5. Return a `Result<T>` pattern wrapper to handle successes and failures gracefully without throwing exceptions.

Provide C# code with file-scoped namespaces, required properties, and proper async/await handling.
```

> [!TIP]
> - Ganti `[Deskripsi Aksi]` dengan aksi spesifik dan sebutkan entity yang terlibat
> - Sesuaikan nama repository interface (`IUserRepository`) dengan domain kamu
> - Tambahkan business rules yang harus divalidasi di handler (bukan hanya input validation)
> - Output ini menghasilkan 3 file: Command, Handler, dan Validator — review masing-masing

---

## 2. High-Performance Dapper Query untuk Reports

**Kapan digunakan:** Saat query EF Core terlalu lambat untuk report endpoint dan perlu raw SQL yang aman menggunakan Dapper.

```text
I need to write a read-only query using Dapper in .NET 8 for a high-performance report endpoint.
The query must retrieve data from [Nama Table Utama] joined with [Table Lain] with millions of records.
Please generate the Query object, DTO, and Repository method using:
1. Dapper `QueryMultipleAsync` or `QueryAsync` for mapped objects.
2. Pagination (OFFSET / FETCH NEXT) with parameters.
3. Safe SQL parameterized query to prevent SQL Injection.
4. Proper connection disposal using `using` blocks.
5. CancellationToken propagation.
```

> [!TIP]
> - Ganti `[Nama Table Utama]` dan `[Table Lain]` dengan nama tabel aktual beserta kolom yang dibutuhkan
> - Sebutkan jumlah record perkiraan untuk konteks optimasi
> - Tambahkan filter criteria yang umum dipakai user agar index suggestion lebih tepat
> - Pastikan review SQL yang dihasilkan terhadap standar di `09-template-sql-review-checklist.md`

> [!WARNING]
> Query SQL yang dihasilkan AI wajib di-review manual sebelum deploy ke production. Lihat panduan di #[[file:19-playbook-performance-tuning-sql.md]].
