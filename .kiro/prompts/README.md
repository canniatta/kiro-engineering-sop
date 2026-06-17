# Prompt Library — Quick Access

> [!NOTE]
> **Source of Truth**
>
> - Library lengkap (120+ prompt): #[[file:03-prompt-library.md]]
> - Setup & konfigurasi: #[[file:02-kiro-setup-and-configuration.md]]

## Tentang Folder Ini

Folder `.kiro/prompts/` berisi prompt pilihan yang diekstrak dari master library untuk akses cepat. Setiap file dikelompokkan berdasarkan kategori penggunaan sehingga developer dapat langsung menemukan prompt yang relevan tanpa membuka dokumen utama yang panjang.

## Daftar File

| File | Kategori | Use Case | Prompt Count |
|---|---|---|---|
| `architecture.md` | Architecture & System Design | Setup Clean Architecture, pilih state management | 2 |
| `dotnet-backend.md` | .NET 8 Backend Development | CQRS command, Dapper query | 2 |
| `react-frontend.md` | ReactJS Frontend Development | Form + validasi, custom hooks TanStack Query | 2 |
| `sql-database.md` | SQL Server Database | Query optimization, migration script | 2 |
| `security-review.md` | Security & Code Review | API security audit, React performance audit | 2 |
| `workflows.md` | Daily Workflows & Automation | Standup summary, PR description | 2 |

## Cara Menggunakan

1. Buka file sesuai kategori yang dibutuhkan
2. Copy prompt text dari code block
3. Ganti placeholder `[...]` dengan konteks spesifik project kamu
4. Paste ke Kiro chat atau editor prompt

> [!TIP]
> Placeholder ditandai dengan kurung siku `[Nama Fitur]`, `[Paste Code Here]`, dll. Ganti seluruh teks dalam kurung siku termasuk kurungnya.

## Menambah Prompt Baru

Saat menambah prompt baru ke folder ini:

1. Tambahkan ke file kategori yang sesuai
2. Update tabel di `README.md` ini (kolom Prompt Count)
3. Pastikan prompt juga tercatat di `03-prompt-library.md` sebagai source of truth

> [!IMPORTANT]
> File di folder ini adalah **subset** dari master library. Source of truth tetap di `03-prompt-library.md`. Jika ada perbedaan, dokumen root yang berlaku.
