# Daily, Weekly, and Monthly Engineering Workflows

Dokumen ini mendefinisikan jadwal rutin dan standar ritme kerja tim engineering yang memanfaatkan **Kiro** sebagai bagian integral dari proses software development harian.

---

## 1. Daily Engineering Rhythm (Ritme Harian)

### 08:30 - 09:00 WIB: Morning Routine & Kiro Warm-up
* **Tujuan:** Sinkronisasi status kerja dan mempersiapkan prioritas harian.
* **Langkah-langkah:**
  1. Periksa dashboard monitoring (Grafana/Kibana) untuk memastikan tidak ada error aneh semalam.
  2. Buka Jira/Azure DevOps, pilih tugas prioritas hari ini.
  3. Buka VS Code / Visual Studio, jalankan git pull untuk sinkronisasi repository.
  4. Lakukan pembersihan context session Kiro sebelum mulai tugas baru untuk menghindari pencemaran memory context.

### 09:00 - 12:00 WIB: Core Focus Time Part 1 (Spec-Driven Dev)
* **Tujuan:** Coding fitur baru atau memecahkan bug tanpa distraksi.
* **Langkah-langkah:**
  1. Terapkan metodologi *Spec-Driven Development* (tulis API `.yaml` atau skema SQL).
  2. Gunakan Kiro untuk menghasilkan boilerplate code dan logika backend/frontend dasar.
  3. Tulis Unit Test secara bersamaan untuk memverifikasi kode generator Kiro.

### 13:00 - 16:30 WIB: Core Focus Time Part 2 (Testing & Refactoring)
* **Tujuan:** Penyempurnaan kode dan integrasi antar layer.
* **Langkah-langkah:**
  1. Jalankan pengujian integrasi di lingkungan lokal.
  2. Gunakan prompt Kiro untuk mengaudit dan me-refactor kode yang baru dibuat (fokus pada SOLID principles, readability, dan security).

### 16:30 - 17:00 WIB: End of Day Wrap-up & PR Submission
* **Tujuan:** Menyelesaikan cabang git dan mendokumentasikan progres.
* **Langkah-langkah:**
  1. Jalankan git commits dengan standar *Conventional Commits*.
  2. Buat Pull Request (PR) ke branch main/develop. Gunakan Kiro PR description generator.
  3. Pastikan CI pipeline berjalan sukses tanpa error build/test.

---

## 2. Weekly Engineering Rhythm (Ritme Mingguan)

### Senin Pagi: Sprint Planning & Goal Alignment
* Tim mereview product backlog, mendefinisikan sprint goal, dan membagi tugas ke tim.
* Sebelum sprint planning selesai, pastikan kriteria *Definition of Ready* (DoR) dari setiap ticket/story terpenuhi (terutama ketersediaan mock spec API).

### Jumat Sore: Retrospective & Knowledge Sharing
* **Retrospective:** Bahas apa yang berjalan baik dan buruk selama seminggu ini. Evaluasi penggunaan Kiro: Prompt mana yang sangat membantu? Apakah ada bottleneck di pipeline CI/CD?
* **Knowledge Sharing Session:** Sesi 30 menit bagi anggota tim untuk mempresentasikan teknologi baru, optimasi SQL Server yang ditemukan, atau library React menarik yang baru dicoba.

---

## 3. Monthly & Quarterly Engineering Rituals (Ritme Bulanan & Kuartalan)

### 3.1 Monthly Tasks (Setiap Akhir Bulan)
1. **Technical Debt Audit:** Kumpulkan daftar hutang teknis dari repository (e.g., TODOs, deprecated APIs, unoptimized queries) dan masukkan ke Technical Debt Registry.
2. **Performance Benchmarking:** Lakukan load testing berkala pada API Inti untuk mendeteksi degradasi performa transaksi.
3. **Dependency Updates:** Upgrade minor version SDK .NET 8, package NPM ReactJS, dan library pihak ketiga lainnya untuk memastikan keamanan security patch.

### 3.2 Quarterly Tasks (Setiap 3 Bulan)
1. **Architecture & Tech Radar Review:** Evaluasi apakah keputusan arsitektur (ADR) yang telah diambil masih relevan atau perlu direvisi dengan versi terbaru.
2. **Security & Vulnerability Assessment:** Lakukan penetration testing internal atau static analysis mendalam menggunakan sonarQube.
3. **Kiro Productivity ROI Review:** Hitung penghematan waktu development, penurunan defect rate, dan peningkatan kecepatan rilis tim dengan adanya asisten AI Kiro.

---

## 4. Kiro Usage Steering Rules (Aturan Penggunaan Kiro)

* [x] **DO:** Gunakan Kiro untuk membuat struktur data berulang, boilerplate code, mock API, unit test cases, dan penjelasan legacy code yang rumit.
* [ ] **DONT:** Jangan pernah menyerahkan keputusan bisnis kritis, validasi security payment, atau enkripsi password mentah-mentah ke Kiro tanpa review manual minimal 2 mata (peer reviewer / tech lead).
* [ ] **DONT:** Jangan menaruh API Key, Connection String Database Production, atau data PII (Personal Identifiable Information) asli user ke dalam prompt Kiro.
* [x] **DO:** Gunakan file `.kiro/rules` di repository Anda untuk mendikte standar penamaan variable, struktur folder Clean Architecture, and library framework yang diizinkan agar Kiro selalu menuruti standar organisasi Anda.
