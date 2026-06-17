# Prompts: Architecture & System Design

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 1: #[[file:docs/03-prompt-library.md]] (Section "Architecture & System Design Prompts")
> - Template arsitektur: #[[file:docs/12-template-clean-architecture-dotnet8.md]]

---

## 1. DDD & Clean Architecture Setup

**Kapan digunakan:** Saat menginisialisasi modul baru atau mikroservis baru menggunakan prinsip Clean Architecture dan Domain-Driven Design (DDD) pada .NET 8.

```text
Act as a Senior .NET Solution Architect. I need to design a new microservice/module for [Nama Fitur] using Clean Architecture and DDD principles.
The technology stack is .NET 8, Entity Framework Core, and SQL Server.
Please generate the folder structure and define the following components:
1. Domain Layer: Define the Entity (with business rules), Value Objects, Domain Events, and Aggregate Root.
2. Application Layer: Create CQRS patterns using MediatR (Command, Query, Handler, and Validators using FluentValidation).
3. Infrastructure Layer: Configure Entity Framework Configurations (using Fluent API) and Repository Interfaces.
4. Web API Layer: Configure the Controller or Minimal API endpoint mapping.

Provide clean, production-ready C# code using .NET 8 features (such as Primary Constructors, required members, and file-scoped namespaces). Keep all logic aligned with clean code principles.
```

> [!TIP]
> - Ganti `[Nama Fitur]` dengan deskripsi detail domain bisnis (misal: "Order Management with multi-currency support")
> - Semakin spesifik konteks bisnis yang diberikan, semakin relevan output Kiro
> - Kombinasikan dengan ADR jika keputusan arsitektur perlu didokumentasikan

---

## 2. State Management Strategy Decision

**Kapan digunakan:** Saat mendesain arsitektur state management di ReactJS — memilih antara Zustand, TanStack Query, atau Context API berdasarkan kebutuhan modul.

```text
We are building a ReactJS frontend with TypeScript. We need to decide the state management architecture for a highly interactive [Nama Modul] that involves real-time updates and heavy local filtering.
Analyze the trade-offs between:
1. Zustand (Client-side global state)
2. TanStack Query / React Query (Server-state caching)
3. Context API (native React state)

Write a decision record based on our stack (ReactJS + .NET 8 REST APIs). Then, provide a complete TypeScript example showing how to combine TanStack Query for API caching and Zustand for local state management (e.g., UI preferences, selection states).
```

> [!TIP]
> - Ganti `[Nama Modul]` dengan modul spesifik (misal: "Dashboard Analytics with 10+ filter combinations")
> - Output prompt ini cocok dijadikan basis ADR (Architecture Decision Record)
> - Tambahkan constraint seperti jumlah concurrent users atau frekuensi update untuk konteks lebih kaya

---

## 3. Solution Analysis & Diagrams

**Kapan digunakan:** Meminta Kiro AI untuk menganalisis seluruh solusi/codebase dan membuat diagram visual arsitektur, dependensi, alur database, serta integrasi API eksternal.

```text
Pelajari seluruh solution ini.

Buatkan:
1. Architecture Diagram
2. Dependency Diagram
3. Database Flow
4. External API Flow

Gunakan format diagram Mermaid untuk visualisasinya dan berikan penjelasan singkat untuk setiap diagram.
```

> [!TIP]
> - Pastikan folder-folder utama (`src/` atau `Presentation/`, `Core/`, `Infrastructure/`) dapat diakses oleh AI.
> - Rujuk standar penulisan diagram arsitektur di dokumen #[[file:docs/06-template-technical-design-document.md]] dan dependensi di dokumen #[[file:docs/12-template-clean-architecture-dotnet8.md]].
> - Diagram Mermaid yang dihasilkan dapat ditempel langsung ke berkas markdown desain teknis.

> [!IMPORTANT]
> Selalu periksa akurasi penamaan proyek dan tabel database pada diagram Mermaid yang dihasilkan agar sesuai dengan kenyataan di dalam codebase.

---

## 4. Feature SRS Generator

**Kapan digunakan:** Menyusun spesifikasi fungsional dan teknis lengkap untuk sebuah fitur baru menggunakan stack .NET 8 + SQL Server + ReactJS.

```text
Buatkan Software Requirement Specification
untuk fitur [Nama Fitur].

Tech Stack:
- .NET 8 API
- SQL Server
- ReactJS

Output:
1. User Story
2. Functional Requirement
3. Non Functional Requirement
4. API Contract
5. Database Impact
6. Risk
```

> [!TIP]
> - Ganti `[Nama Fitur]` dengan fitur spesifik yang akan dibangun (misal: "Single Tarif Kesepakatan" atau "Multi-tenant Billing Cycle").
> - Rujuk standar penulisan User Story di dokumen #[[file:docs/04-template-prd-user-story.md]] dan templat SRS di #[[file:docs/05-template-srs.md]].
> - File output dapat disimpan langsung di bawah folder `.kiro/specs/<nama-fitur>/requirements.md` untuk mengawali workflow SDD.

> [!IMPORTANT]
> Pastikan kontrak API yang diusulkan oleh AI diverifikasi kelayakannya oleh tim Frontend dan Backend sebelum mulai di-breakdown menjadi tasks pekerjaan.

---

## 5. Architectural Refactoring Plan

**Kapan digunakan:** Menyusun rencana migrasi dan menulis ulang proyek lama agar menggunakan standar arsitektur modern (Clean Architecture, CQRS, MediatR, Repository Pattern).

```text
Refactor project ini menjadi:

- Clean Architecture
- CQRS
- MediatR
- Repository Pattern
```

> [!TIP]
> - Sediakan akses ke seluruh berkas proyek legacy (misal .NET Framework atau ASP.NET Web Forms) agar AI memahami strukturnya.
> - Rujuk berkas pedoman modernisasi sistem di #[[file:docs/24-refactoring-legacy-systems.md]] dan templat Clean Architecture di #[[file:docs/12-template-clean-architecture-dotnet8.md]].
> - Mintalah AI merancang strategi transisi bertahap (seperti *Strangler Fig Pattern* atau *Anti-Corruption Layer*) sebelum melakukan penulisan ulang kode.

> [!IMPORTANT]
> Proses pemisahan arsitektur berskala besar wajib melalui tahap pengajuan ADR (Architecture Decision Record) sesuai standar di #[[file:docs/07-template-adr.md]] untuk persetujuan tim Tech Lead.
