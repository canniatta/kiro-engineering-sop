# 🏗️ Kiro Engineering SOP - Complete Engineering Playbook

Selamat datang di repositori **Kiro Engineering SOP**. Repositori ini berisi standar operasional prosedur (SOP) lengkap, template spesifikasi, daftar checklist kualitas, panduan coding, serta konfigurasi kemudi (*steering files*) untuk pengembangan aplikasi menggunakan stack **.NET 8 + ReactJS + SQL Server** dengan bantuan **Kiro AI**.

Dokumen ini bertindak sebagai **Single Source of Truth (SSoT)** untuk menjaga konsistensi, standar kualitas, serta mengoptimalkan produktivitas tim engineering kita.

| Informasi Dokumen | Detail |
|---|---|
| **Nama Proyek** | Kiro Engineering Standard Operating Procedure (SOP) |
| **Versi Dokumen** | v2.0.0 |
| **Terakhir Diperbarui**| 18 Juni 2026 |
| **Status** | 🟢 Approved (Active) |
| **Author / Penyusun** | **UNiVerSe** |
| **Maintainer** | **UNiVerSe** |
| **Klasifikasi** | 🔓 Public / Open Source |

---

## 👥 Kontributor Utama
* **SOP Author & Owner**: **UNiVerSe**
* **Engineering Lead**: **UNiVerSe**
* **Technical Lead**: **UNiVerSe**
* **DBA (Database Administrator)**: **UNiVerSe**
* **DevOps Engineer**: **UNiVerSe**

---

## 📑 Peta Navigasi Modular (Berdasarkan Domain)

Seluruh berkas dokumentasi di dalam [docs/](./docs/) dan aturan kemudi di dalam [.kiro/steering/](./.kiro/steering/) dirancang secara modular agar mudah dibaca dan dipelihara. Berikut adalah pengelompokan berkas berdasarkan domain keahlian:

### 1. Core & Onboarding (Dasar SOP)
Dokumen utama untuk seluruh anggota tim baru maupun stakeholder manajemen:
* 📄 **[docs/00-master-index.md](./docs/00-master-index.md)**: Induk indeks navigasi yang menghubungkan seluruh berkas SOP.
* 🧠 **[docs/01-executive-summary-and-mindset.md](./docs/01-executive-summary-and-mindset.md)**: Visi manajemen, pergeseran mindset tim menuju *AI-assisted coding*, rencana transformasi budaya, dan analisis finansial ROI.
* ⚙️ **[docs/02-kiro-setup-and-configuration.md](./docs/02-kiro-setup-and-configuration.md)**: Panduan langkah-demi-langkah instalasi Kiro IDE, standardisasi ekstensi VS Code, standardisasi `.editorconfig` tim, dan pemecahan masalah setup.
* 📚 **[docs/03-prompt-library.md](./docs/03-prompt-library.md)**: Pustaka 120+ prompt siap pakai untuk kebutuhan arsitektur, frontend, backend, database, review, dan penulisan spec.

### 2. Database & SQL Server
Panduan khusus bagi DBA (*Database Administrator*) dan Backend Developer:
* 🗄️ **[docs/19-playbook-performance-tuning-sql.md](./docs/19-playbook-performance-tuning-sql.md)**: Playbook optimasi database, strategi indexing, analisis execution plan, tuning query lambat, dan manajemen locks/deadlocks.
* 📋 **[docs/09-template-sql-review-checklist.md](./docs/09-template-sql-review-checklist.md)**: Lembar panduan checklist tinjauan script SQL pra-commit.
* ⚙️ **[.kiro/steering/sql-rules.md](./.kiro/steering/sql-rules.md)**: Aturan persistent kemudi AI Kiro saat membuat schema, trigger, index, dan stored procedure.

### 3. Backend & REST API (.NET 8)
Panduan arsitektur dan standardisasi kode C# (.NET 8):
* 🏗️ **[docs/12-template-clean-architecture-dotnet8.md](./docs/12-template-clean-architecture-dotnet8.md)**: Standardisasi implementasi Clean Architecture (Domain, Application, Infrastructure, Presentation Layers) menggunakan C# & .NET 8.
* 📋 **[docs/10-template-api-review-checklist.md](./docs/10-template-api-review-checklist.md)**: Standar desain API RESTful, HTTP status codes, dan validasi input.
* ⚡ **[docs/10a-api-performance-review-checklist.md](./docs/10a-api-performance-review-checklist.md)**: Checklist deteksi masalah performa API (N+1 query, optimasi EF Core DbContext, async/await anti-pattern, memory leak).
* ⚙️ **[.kiro/steering/dotnet-rules.md](./.kiro/steering/dotnet-rules.md)** & **[api-rules.md](./.kiro/steering/api-rules.md)**: Aturan kemudi AI Kiro khusus C# .NET 8, CQRS MediatR, FluentValidation, dan REST API contracts.

### 4. Frontend & User Interface (ReactJS)
Panduan standar pengembangan client-side application:
* ⚛️ **[docs/13-template-reactjs-frontend-standard.md](./docs/13-template-reactjs-frontend-standard.md)**: Struktur kode ReactJS (Vite, Zustand state management, TanStack Query, styling, dan form validation).
* ⚙️ **[.kiro/steering/react-rules.md](./.kiro/steering/react-rules.md)**: Aturan kemudi Kiro untuk standardisasi React components, custom hooks, dan render optimization.

### 5. Logging & Observability
Standar pemantauan sistem di environment Staging/Production:
* 🪵 **[docs/11-template-logging-observability.md](./docs/11-template-logging-observability.md)**: Standardisasi format log menggunakan Serilog, tracing Correlation ID (HTTP Header), integrasi OpenTelemetry, dashboard metrik Grafana/Prometheus.
* ⚙️ **[.kiro/steering/07-logging.md](./.kiro/steering/07-logging.md)**: Konfigurasi kemudi agar AI Kiro selalu menuliskan log kritis di setiap handler.

### 6. Development Workflows & SDLC Playbooks
Panduan ritme kerja harian dan metodologi pengiriman fitur:
* 🎯 **[docs/14-spec-driven-development.md](./docs/14-spec-driven-development.md)**: Metodologi Spec-Driven Development (SDD) menggunakan OpenAPI contract dan Kiro.
* 🐙 **[docs/15-workflow-github-flow.md](./docs/15-workflow-github-flow.md)**: Standar git branching, Conventional Commits format, Pull Request template, dan pipeline CI/CD GitHub Actions.
* 🔄 **[docs/16-workflow-azure-devops-jira.md](./docs/16-workflow-azure-devops-jira.md)**: Integrasi sprint board Jira/Azure DevOps, standardisasi status ticket, dan panduan sync backlog.
* 🚨 **[docs/17-workflow-incident-management.md](./docs/17-workflow-incident-management.md)**: Playbook penanganan insiden SEV1-SEV4, integrasi PagerDuty, pembuatan hotfix, dan dokumen Root Cause Analysis (RCA).
* 📅 **[docs/18-workflow-daily-weekly-monthly.md](./docs/18-workflow-daily-weekly-monthly.md)**: Ritme kerja berkala (Daily Standup, Weekly Planning, Monthly Release, Quarterly Review).
* ⚙️ **[.kiro/steering/git-rules.md](./.kiro/steering/git-rules.md)** & **[release-checklist.md](./.kiro/steering/release-checklist.md)**: Aturan commit, release readiness, dan branch safety.

### 7. Templates & Specifications
Berkas contoh format dokumen untuk kebutuhan spesifikasi sistem:
* 🗒️ **[docs/04-template-prd-user-story.md](./docs/04-template-prd-user-story.md)**: Template Product Requirement Document (PRD) & Use Case.
* 📋 **[docs/05-template-srs.md](./docs/05-template-srs.md)**: Template Software Requirement Specification standar IEEE.
* ⚙️ **[docs/06-template-technical-design-document.md](./docs/06-template-technical-design-document.md)**: Template Technical Design Document (TDD).
* 🏛️ **[docs/07-template-adr.md](./docs/07-template-adr.md)**: Template Architecture Decision Record (ADR) beserta contoh konkretnya.
* 🏗️ **[docs/26-template-architecture-review.md](./docs/26-template-architecture-review.md)**: Format audit & roadmap teknologis sistem arsitektur enterprise.
* 📁 **Aturan Kemudi Templat**: Tersedia di folder [.kiro/specs/README.md](./.kiro/specs/README.md) dan folder template [.kiro/specs/_templates/](./.kiro/specs/_templates/).

### 8. Team Strategy & Metrics
Metodologi pengukuran kesuksesan dan pengelolaan kualitas jangka panjang:
* 📉 **[docs/20-technical-debt-management.md](./docs/20-technical-debt-management.md)**: Framework pencatatan debt backlog, skoring severity, dan strategi cicilan hutang teknis.
* 📖 **[docs/21-knowledge-base-strategy.md](./docs/21-knowledge-base-strategy.md)**: Manajemen strategi Docs-As-Code, pemodelan sistem C4 Model, dan eliminasi risiko knowledge silos.
* 🧪 **[docs/22-unit-testing-strategy.md](./docs/22-unit-testing-strategy.md)**: Kebijakan cakupan automated tests backend (xUnit) dan frontend (Jest/React Testing Library).
* 🚀 **[docs/23-release-readiness-checklists.md](./docs/23-release-readiness-checklists.md)**: Checklists gerbang rilis produksi.
* ⚙️ **[docs/24-refactoring-legacy-systems.md](./docs/24-refactoring-legacy-systems.md)**: Playbook migrasi sistem monolitik warisan / legacy code.
* 📊 **[docs/25-roi-measurement-kpi.md](./docs/25-roi-measurement-kpi.md)**: Implementasi DORA metrics (Deployment Frequency, Lead Time for Changes, MTTR, CFR) untuk efisiensi bisnis.

### 9. Message Broker & Event-Driven (Kafka)
Panduan integrasi event streaming dan penanganan asinkronus:
* 🪵 **[docs/27-playbook-kafka-message-broker.md](./docs/27-playbook-kafka-message-broker.md)**: Playbook arsitektur, integrasi C# .NET 8 Clean Architecture, dan prompt khusus Kafka.
* ⚙️ **[.kiro/steering/kafka-rules.md](./.kiro/steering/kafka-rules.md)**: Aturan kemudi Kiro AI untuk penulisan produser dan konsumer Kafka.

---

## ⚡ Langkah Awal Mulai Cepat (Quick Start)

Untuk berkontribusi menggunakan Kiro IDE secara optimal, ikuti langkah berikut:
1. **Instalasi Editor**: Pasang Kiro IDE dari [kiro.dev](https://kiro.dev) (silakan ikuti panduan detail di [Kiro Setup](./docs/02-kiro-setup-and-configuration.md)).
2. **Koneksi Kemudi**: Pastikan folder `.kiro/` terdeteksi di root project agar Kiro AI memuat panduan coding standar.
3. **Cari Berkas Induk**: Mulailah dari **[docs/00-master-index.md](./docs/00-master-index.md)** untuk melihat bagan arsitektur hubungan antar dokumen SOP.
4. **Verifikasi Checklists**: Sebelum men-submit Pull Request, pastikan Anda telah lulus semua checklist di dokumen **[Release Readiness](./docs/23-release-readiness-checklists.md)**.

> [!TIP]
> **Tips Checklist Interaktif di Editor**: Secara default, checkbox (`- [ ]`) pada Markdown Preview bersifat *read-only*. Agar dapat diklik langsung di preview dan otomatis mengubah isi berkas, Anda dapat memasang ekstensi **Markdown Checkbox** (oleh Philipp Kief) melalui Extension Marketplace. Jika ingin lebih cepat secara manual tanpa ekstensi, Anda dapat menggunakan shortcut keyboard **`Option + C`** (macOS) atau **`Alt + C`** (Windows) langsung di baris checklist pada editor teks untuk melakukan centang secara instan.
