# Architecture Decision Record (ADR) Template & Examples

Dokumen ini berisi template Architecture Decision Record (ADR) standar serta 10 contoh keputusan arsitektur nyata yang umum ditemui pada proyek dengan stack **.NET 8 + ReactJS + SQL Server**.

---

## 1. ADR Markdown Template (MADR Format)

```markdown
# [ADR-XXXX]: [Judul Keputusan Singkat]

* **Status:** [Draft | Proposed | Accepted | Rejected | Superseded by ADR-XXXX]
* **Deciders:** [Nama Tim / Arsitek / Lead Engineer]
* **Date:** [YYYY-MM-DD]

## 1. Context & Problem Statement
*Jelaskan latar belakang teknis atau kebutuhan bisnis yang melatarbelakangi keputusan ini. Masalah apa yang sedang kita coba pecahkan? Sertakan metrik atau konstrain jika ada.*

## 2. Decision Drivers
* 1. [Driver 1, misal: Performance latency di bawah 100ms]
* 2. [Driver 2, misal: Skalabilitas dan kemudahan deployment]
* 3. [Driver 3, misal: Biaya infrastruktur di Azure Cloud]

## 3. Considered Options
* **Option 1:** [Nama Opsi 1]
* **Option 2:** [Nama Opsi 2]
* **Option 3:** [Nama Opsi 3]

## 4. Decision Outcome
Chosen option: **[Option X]**, karena [alasan utama yang mendukung].

### Consequence (Konsekuensi)
* **Positive (Pros):**
  * [Pros 1, misal: Meningkatkan kecepatan query secara drastis]
  * [Pros 2]
* **Negative (Cons / Trade-offs):**
  * [Cons 1, misal: Memerlukan setup Redis server tambahan]
  * [Cons 2]

---

## 5. Pros & Cons of Options

### Option 1: [Nama Opsi 1]
* **Pros:**
  * [Keuntungan]
* **Cons:**
  * [Kerugian]

### Option 2: [Nama Opsi 2]
* **Pros:**
  * [Keuntungan]
* **Cons:**
  * [Kerugian]
```

---

## 2. 10 Contoh ADR Nyata (Stack .NET 8 + ReactJS + SQL Server)

### ADR-0001: Memilih Clean Architecture untuk Desain Solusi .NET 8
* **Status:** Accepted
* **Context:** Kami membutuhkan struktur kode backend yang rapi, modular, dan memiliki testability tinggi untuk sistem enterprise berskala besar.
* **Decision:** Memilih **Clean Architecture** (misal membagi project menjadi Domain, Application, Infrastructure, dan Web API).
* **Cons:** Overhead class-class DTO dan mapping yang cukup banyak di awal, namun mempermudah pemeliharaan jangka panjang.

### ADR-0002: Menggunakan Entity Framework Core sebagai ORM Utama & Dapper untuk Query Read-Heavy
* **Status:** Accepted
* **Context:** Mengurangi waktu development untuk fitur transaksional (write) tetapi memerlukan query dengan performa tinggi pada report.
* **Decision:** Gunakan **Entity Framework Core 8** untuk operational transactional logic (Write/Update) dan **Dapper** untuk read-heavy reporting query yang membutuhkan optimasi execution plan tingkat lanjut.

### ADR-0003: State Management ReactJS Menggunakan Zustand dan TanStack Query
* **Status:** Accepted
* **Context:** Redux Toolkit dinilai terlalu banyak boilerplate code untuk project ini, sedangkan kami membutuhkan sinkronisasi data API client-server yang handal.
* **Decision:** Gunakan **TanStack Query** untuk manajemen data server-state (fetching, caching, invalidation) dan **Zustand** untuk client-side UI global state (dark mode, menu toggle, user preferences).

### ADR-0004: Autentikasi Menggunakan JSON Web Token (JWT) dengan HttpOnly Cookies
* **Status:** Accepted
* **Context:** Kami perlu mengamankan REST API .NET 8 dari celah XSS dan CSRF.
* **Decision:** Token JWT disimpan di dalam **HttpOnly Secure SameSite=Strict Cookies**, bukan di LocalStorage, untuk meminimalisir risiko pencurian token via XSS.

### ADR-0005: Strategi API Versioning Menggunakan URL Path Segment
* **Status:** Accepted
* **Context:** Memungkinkan pembaruan API tanpa merusak (breaking changes) aplikasi frontend versi lama yang masih berjalan.
* **Decision:** Implementasikan API Versioning berbasis URL path (misal: `/api/v1/users` dan `/api/v2/users`) menggunakan library `Asp.Versioning.Http` di .NET 8.

### ADR-0006: Redis sebagai Distributed Caching System
* **Status:** Accepted
* **Context:** Database SQL Server mengalami load tinggi akibat query berulang pada data master yang statis.
* **Decision:** Gunakan Redis cache untuk menyimpan hasil query katalog produk dan data setup sistem.

### ADR-0007: Apache Kafka untuk Event-Driven Communication
* **Status:** Accepted
* **Context:** Komunikasi sinkronus antar microservices menyebabkan eratnya ketergantungan (tight coupling) dan memperlambat response time API. Kami membutuhkan event streaming platform dengan throughput tinggi dan skalabilitas horizontal yang mumpuni.
* **Decision:** Integrasikan Apache Kafka sebagai event broker terdistribusi untuk mempublikasikan dan berlangganan event secara asinkronus menggunakan pola produser dan konsumen background service.

### ADR-0008: Logging Framework Menggunakan Serilog & OpenTelemetry
* **Status:** Accepted
* **Context:** Sulit melakukan pelacakan error secara end-to-end ketika error terjadi di production server yang ter-load balancer.
* **Decision:** Gunakan Serilog dengan sink Elasticsearch untuk structured logging, serta OpenTelemetry untuk distributed tracing.

### ADR-0009: Database Migration Menggunakan EF Core Migrations
* **Status:** Accepted
* **Context:** Perubahan skema database harus ter-version control dan terintegrasi dengan pipeline CI/CD Azure DevOps.
* **Decision:** Gunakan EF Core Migrations dengan mode `dotnet ef database update` yang dijalankan di dalam pipeline CD release.

### ADR-0010: Frontend Integration Testing dengan Jest & Playwright
* **Status:** Accepted
* **Context:** Kami memerlukan jaminan bahwa alur checkout utama berjalan dengan baik dari sisi UI hingga DB.
* **Decision:** Gunakan Jest + React Testing Library untuk Unit Test component UI, dan Playwright untuk pengujian End-to-End (E2E) UI flow utama.
