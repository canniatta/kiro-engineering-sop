---
inclusion: manual
---

# Release Readiness Checklists

> [!NOTE]
> **Source of Truth**
>
> - Checklist lengkap dengan penjelasan: #[[file:23-release-readiness-checklists.md]]

## 1. Pre-Pull Request Checklist

Pastikan semua poin berikut terpenuhi sebelum assign reviewer pada PR.

### Kualitas Kode & Standardisasi

- [ ] Penamaan mengikuti PascalCase (.NET) dan camelCase (ReactJS)
- [ ] Tidak ada dead code (baris yang di-comment out)
- [ ] SOLID principles dipatuhi (terutama SRP)
- [ ] File-scoped namespaces pada semua file C# baru
- [ ] Primary Constructors digunakan untuk DI di service baru
- [ ] Async/await konsisten — semua method async return `Task` atau `Task<T>`

### Database & API Compatibility

- [ ] Script migrasi backward-compatible (kolom baru NULLable atau punya DEFAULT)
- [ ] Indexing strategy sesuai standar (no table scans)
- [ ] Endpoint baru menyertakan versioning URL (`/api/v1/`)
- [ ] DTO response tidak membocorkan data sensitif
- [ ] FluentValidation ditambahkan untuk request body

### Pengujian & Keamanan

- [ ] Unit Test ditambahkan (minimal 80% coverage layer Application/Domain)
- [ ] Integration Test berhasil di lokal
- [ ] Seluruh test pass (0 failed tests frontend & backend)
- [ ] Tidak ada hardcoded secrets — gunakan `appsettings.json` atau env vars
- [ ] Input UI ReactJS di-sanitize untuk mencegah XSS

---

## 2. Pre-Production Release Checklist

Tech Lead / Release Manager memastikan kriteria berikut sebelum deploy ke Production.

### Infrastruktur & Konfigurasi

- [ ] Koneksi database production teruji, credential di Key Vault / Secrets Manager
- [ ] Environment variables dikonfigurasi benar untuk production
- [ ] Auto-scaling rules diatur sesuai estimasi load
- [ ] CORS policies dikunci hanya untuk domain frontend resmi

### Database & Data Safety

- [ ] Script DDL/DML migrasi sudah dijalankan dan diuji di Staging
- [ ] Backup database production berjalan normal (Full daily + TLog 15 menit)
- [ ] Rollback script disiapkan dan divalidasi

### Monitoring & Alerting

- [ ] Endpoint `/health` terdaftar dan return HTTP 200
- [ ] Health check terintegrasi dengan Load Balancer / Traffic Manager
- [ ] Alerting rule aktif: notifikasi jika HTTP 5xx > 1%
- [ ] Serilog mengirim structured log ke Elasticsearch/Seq

---

## 3. Post-Release Smoke Testing Checklist

Lakukan segera setelah deployment production selesai.

- [ ] **Smoke Test Alur Inti** — Jalankan satu transaksi end-to-end (login/checkout) di production
- [ ] **Verify SSL & CDN** — HTTPS aktif, file frontend tersaji via CDN
- [ ] **Check Error Rate** — Pantau dashboard log 15 menit, pastikan tidak ada lonjakan exception
- [ ] **Inform Stakeholders** — Kirim notifikasi rilis sukses ke tim bisnis/produk
