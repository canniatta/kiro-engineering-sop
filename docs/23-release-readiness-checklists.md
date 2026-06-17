# Release Readiness & Pre-PR Checklists

Dokumen ini mendefinisikan daftar periksa (checklists) yang wajib diisi dan diverifikasi oleh tim engineer sebelum melakukan merge kode ke branch utama (Pull Request) maupun sebelum merilis aplikasi ke Production environment.

---

## 1. Pre-Pull Request (PR) Checklist

Setiap developer wajib memastikan poin-poin di bawah ini terpenuhi sebelum meminta review (assigning reviewers) pada Pull Request:

### 1.1 Kualitas Kode & Standardisasi C# / TS
- [ ] Penamaan class, method, variable mengikuti gaya PascalCase (.NET) dan camelCase (ReactJS).
- [ ] Tidak ada baris kode yang di-comment out (dead code). Gunakan Git history jika ingin memulihkan kode lama.
- [ ] SOLID principles dipatuhi (terutama Single Responsibility pada class controller/service).
- [ ] File-scoped namespaces digunakan pada seluruh file C# baru (.NET 8).
- [ ] Primary Constructors dimanfaatkan pada dependency injection di file-file service baru.
- [ ] Penggunaan async/await konsisten, semua metode async mengembalikan `Task` atau `Task<T>`.

### 1.2 Database & API Compatibility
- [ ] Script migrasi database bersifat backward-compatible (kolom baru bernilai NULLable atau memiliki DEFAULT constraint).
- [ ] Skema database SQL Server mematuhi aturan indexing strategy yang terdokumentasi (no table scans).
- [ ] Endpoint REST API baru menyertakan versioning URL (e.g., `/api/v1/`).
- [ ] DTO response API tidak membocorkan data sensitif (password hash, internal ID, system trace).
- [ ] FluentValidation ditambahkan untuk memvalidasi request body sisi server.

### 1.3 Pengujian & Keamanan
- [ ] Unit Test ditambahkan untuk mencakup minimal **80% code coverage** pada layer Application/Domain.
- [ ] Integration Test berhasil dijalankan di lokal dengan database test instance.
- [ ] Seluruh pengujian lokal (frontend & backend) berhasil lolos tanpa kegagalan (0 failed tests).
- [ ] Variabel rahasia (API Keys, DB Connection) dilarang keras ditulis secara hardcode; harus ditarik dari `appsettings.json` atau environment variables.
- [ ] Input parameter UI ReactJS dibersihkan (sanitized) sebelum di-render untuk mencegah serangan XSS.

---

## 2. Pre-Production Release Checklist

Pimpinan tim (Tech Lead/Release Manager) wajib memastikan kriteria di bawah ini terpenuhi sebelum memulai deployment rilis ke Production environment:

### 2.1 Infrastruktur & Konfigurasi
- [ ] Koneksi database production telah diuji dan credential-nya tersimpan aman di Azure Key Vault atau AWS Secrets Manager.
- [ ] Environment variables (seperti URL Web API backend di ReactJS client) dikonfigurasi dengan benar untuk target production.
- [ ] Auto-scaling rules untuk container/app service diatur sesuai estimasi load pengguna.
- [ ] CORS policies di backend dikunci hanya untuk subdomain resmi frontend di Production.

### 2.2 Database & Data Safety
- [ ] Script DDL/DML migrasi SQL Server sudah dijalankan dan diuji di lingkungan Staging.
- [ ] Skema backup database production saat ini dipastikan berjalan normal (Full backup harian dan Transaction Log backup setiap 15 menit).
- [ ] Skema pemulihan data (rollback script) disiapkan dan divalidasi.

### 2.3 Monitoring & Alerting
- [ ] Endpoint `/health` backend API terdaftar dan merespon status HTTP 200 dengan benar.
- [ ] Health check terintegrasi dengan Azure Traffic Manager atau Load Balancer.
- [ ] Alerting Rule di Grafana atau Azure Monitor dikonfigurasi untuk mengirim notifikasi ke Slack jika HTTP 5xx error rate melebihi 1%.
- [ ] Log telemetry (Serilog) dipastikan mengirim data log terstruktur ke Elasticsearch/Seq.

---

## 3. Post-Release & Smoke Testing Checklist

Segera lakukan pengecekan berikut setelah deployment Production selesai:

- [ ] **Smoke Test Alur Inti:** Jalankan satu transaksi nyata di production (checkout / login) untuk memastikan integrasi frontend-backend-database bekerja 100%.
- [ ] **Verify SSL & CDN:** Pastikan sertifikat HTTPS aktif dan file frontend ReactJS tersaji cepat melalui server CDN (Cloudflare / Azure Front Door).
- [ ] **Check Error Rate Logs:** Pantau real-time dashboard log selama 15 menit pasca-rilis; pastikan tidak ada lonjakan unhandled exception.
- [ ] **Inform Stakeholders:** Berikan notifikasi rilis sukses ke tim bisnis/produk menggunakan template rilis standar.
