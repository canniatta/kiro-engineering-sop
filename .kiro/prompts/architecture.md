# Prompts: Architecture & System Design

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 1: #[[file:03-prompt-library.md]] (Section "Architecture & System Design Prompts")
> - Template arsitektur: #[[file:12-template-clean-architecture-dotnet8.md]]

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
