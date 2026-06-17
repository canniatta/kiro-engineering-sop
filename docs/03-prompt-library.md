# Kiro Prompt Library - .NET 8, ReactJS, and SQL Server Enterprise Edition

Dokumen ini berisi kumpulan prompt Kiro siap pakai yang dirancang khusus untuk mempercepat proses pengembangan, review, dan pemecahan masalah pada stack teknologi **.NET 8 Web API**, **ReactJS (Vite, TypeScript, Zustand)**, dan **SQL Server (High Volume)**.

---

## 1. Architecture & System Design Prompts

### Prompt 1.1: DDD & Clean Architecture Setup
* **Kapan digunakan:** Saat menginisialisasi modul baru atau mikroservis baru menggunakan prinsip Clean Architecture dan Domain-Driven Design (DDD).
* **Prompt Text:**
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
* **Contoh Output:** Kode C# untuk Aggregate Root, MediatR Command/Query Handler, Fluent Validation, dan EF Configuration yang siap diimplementasikan.
* **Tips:** Tambahkan spesifikasi detail model bisnis Anda di bagian `[Nama Fitur]` agar Kiro mengerti konteks domainnya.

### Prompt 1.2: State Management Strategy Decision
* **Kapan digunakan:** Saat mendesain arsitektur state management di ReactJS (Zustand vs React Query).
* **Prompt Text:**
  ```text
  We are building a ReactJS frontend with TypeScript. We need to decide the state management architecture for a highly interactive [Nama Modul] that involves real-time updates and heavy local filtering.
  Analyze the trade-offs between:
  1. Zustand (Client-side global state)
  2. TanStack Query / React Query (Server-state caching)
  3. Context API (native React state)
  
  Write a decision record based on our stack (ReactJS + .NET 8 REST APIs). Then, provide a complete TypeScript example showing how to combine TanStack Query for API caching and Zustand for local state management (e.g., UI preferences, selection states).
  ```

### Prompt 1.3: Solution Analysis & Architecture Mapping
* **Kapan digunakan:** Meminta Kiro AI untuk menganalisis seluruh solusi/codebase dan membuat diagram visual arsitektur, dependensi, alur database, serta integrasi API eksternal.
* **Prompt Text:**
  ```text
  Pelajari seluruh solution ini.

  Buatkan:
  1. Architecture Diagram
  2. Dependency Diagram
  3. Database Flow
  4. External API Flow

  Gunakan format diagram Mermaid untuk visualisasinya dan berikan penjelasan singkat untuk setiap diagram.
  ```

### Prompt 1.4: Feature Software Requirement Specification (SRS) Generator
* **Kapan digunakan:** Menyusun spesifikasi fungsional dan teknis lengkap untuk sebuah fitur baru menggunakan stack .NET 8 + SQL Server + ReactJS.
* **Prompt Text:**
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
* **Contoh Penggunaan:** `Buatkan Software Requirement Specification untuk fitur Single Tarif Kesepakatan.`

### Prompt 1.5: Architectural Refactoring Plan
* **Kapan digunakan:** Menyusun rencana migrasi dan menulis ulang proyek lama agar menggunakan standar arsitektur modern (Clean Architecture, CQRS, MediatR, Repository Pattern).
* **Prompt Text:**
  ```text
  Refactor project ini menjadi:

  - Clean Architecture
  - CQRS
  - MediatR
  - Repository Pattern
  ```

---

## 2. .NET 8 Backend Development Prompts

### Prompt 2.1: Implement CQRS Command with MediatR & FluentValidation
* **Kapan digunakan:** Menulis command baru untuk memproses logic bisnis write (create/update/delete) di .NET 8.
* **Prompt Text:**
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

### Prompt 2.2: High-Performance Dapper Query for Reports
* **Kapan digunakan:** Saat query EF Core terlalu lambat dan Anda perlu menulis query SQL raw yang aman menggunakan Dapper di .NET 8.
* **Prompt Text:**
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

### Prompt 2.3: Coding Standard Compliance Generator
* **Kapan digunakan:** Menulis atau memperbarui komponen C# backend agar mematuhi standar pengembangan resmi tim.
* **Prompt Text:**
  ```text
  Buatkan komponen/refactor kode berikut agar mematuhi standar tim.

  Coding Standard:
  - .NET 8
  - Repository Pattern
  - MediatR
  - Serilog
  - FluentValidation

  Sediakan struktur folder yang diusulkan dan kode lengkap yang siap pakai.
  ```

### Prompt 2.4: Code Decomposition (Service Splitting)
* **Kapan digunakan:** Memecah kelas/service C# yang terlalu besar (melebihi batas maksimal 500 baris kode sesuai checklist G-50) menjadi service-service kecil yang lebih modular.
* **Prompt Text:**
  ```text
  Pisahkan service yang melebihi
  500 line menjadi service yang lebih kecil.
  ```

---

## 3. ReactJS Frontend Development Prompts

### Prompt 3.1: Form Integration with React Hook Form & Zod
* **Kapan digunakan:** Membuat form kompleks dengan validasi sisi client menggunakan TypeScript di React.
* **Prompt Text:**
  ```text
  Create a React functional component using TypeScript and Vite for [Nama Form].
  The form needs to:
  1. Use `react-hook-form` for state management and submission.
  2. Use `@hookform/resolvers/zod` with a Zod schema to enforce validation rules.
  3. Contain field validation (e.g., Email, required fields, minimum length, custom validation logic).
  4. Integrate with TailwindCSS or custom CSS modules for modern styling.
  5. Show clear validation error messages under each field and highlight invalid inputs.
  6. Disable the submit button during submission state.
  ```

### Prompt 3.2: Custom React Hook for API Integration (TanStack Query)
* **Kapan digunakan:** Menghubungkan UI React dengan endpoint REST API .NET 8 menggunakan React Query.
* **Prompt Text:**
  ```text
  Write a custom React Hook using TypeScript and `@tanstack/react-query` to manage CRUD operations for resource [Nama Resource] (e.g., Products).
  The hook should:
  1. Define query keys correctly.
  2. Implement `useQuery` for fetching list and detail.
  3. Implement `useMutation` for Create, Update, and Delete actions.
  4. Automatically invalidate the query cache on successful mutation (optimistic updates optional).
  5. Implement error handling and loading indicators.
  6. Call an Axios API client instance that includes Authorization headers.
  ```

---

## 4. SQL Server Database Prompts

### Prompt 4.1: Query Optimization with Execution Plan Analysis
* **Kapan digunakan:** Saat Anda memiliki query SQL Server yang lambat pada tabel berukuran jutaan baris.
* **Prompt Text:**
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

### Prompt 4.2: Idempotent Migration Script
* **Kapan digunakan:** Membuat script perubahan schema database yang aman dideploy berulang kali.
* **Prompt Text:**
  ```text
  Write an idempotent SQL migration script to: [Aksi, misal: Add Column 'IsVerified' to Table 'Users'].
  The script must:
  1. Check if the column/table already exists before applying changes (to prevent runtime errors).
  2. Run inside a transaction block with TRY/CATCH error handling.
  3. Set XACT_ABORT ON.
  4. Include rollback scripts.
  5. Update metadata tracking tables or history if applicable.
  ```

### Prompt 4.3: Stored Procedure Optimization Audit
* **Kapan digunakan:** Menganalisis dan mengoptimalkan stored procedure SQL Server yang memiliki performa lambat.
* **Prompt Text:**
  ```text
  Analisa stored procedure berikut.

  Fokus:
  - Index recommendation
  - Table scan
  - Missing covering index
  - Temp table usage
  - Parameter sniffing
  - Query cost

  Berikan versi optimisasi.
  ```

---

## 5. Security & Code Review Prompts

### Prompt 5.1: API Security Review
* **Kapan digunakan:** Sebelum melakukan deploy API baru ke Production, untuk mendeteksi celah keamanan.
* **Prompt Text:**
  ```text
  Review the following .NET 8 API Controller/Endpoint code for security vulnerabilities:
  ```csharp
  [Paste C# Code Here]
  ```
  Check for:
  1. Mass Assignment (Overposting) vulnerabilities.
  2. SQL Injection / NoSQL Injection.
  3. Broken Object Level Authorization (BOLA / IDOR).
  4. Cross-Site Scripting (XSS) / Input validation flaws.
  5. Sensitive data exposure in logs or response payloads.
  6. Rate limiting and CORS configurations.
  Provide actionable fixes for any discovered issues.
  ```

### Prompt 5.2: React Performance Audit
* **Kapan digunakan:** Mengaudit component React yang terasa lambat atau sering re-render.
* **Prompt Text:**
  ```text
  Audit this React component for performance bottlenecks:
  ```tsx
  [Paste React/TypeScript Code Here]
  ```
  Identify:
  1. Unnecessary re-renders.
  2. Expensive calculations that should be memoized using `useMemo`.
  3. Functions passed as props that should use `useCallback`.
  4. Opportunities for code-splitting / dynamic imports.
  5. Virtualization needs if rendering large lists.
  Provide the refactored code with explanations.
  ```

### Prompt 5.3: API Performance Review
* **Kapan digunakan:** Mengaudit dan meninjau seluruh API Controller untuk mendeteksi bottleneck kinerja dan masalah alokasi memori sebelum rilis.
* **Prompt Text:**
  ```text
  Review seluruh API Controller.

  Cari:
  - N+1 query
  - Multiple DbContext call
  - Async issue
  - Unnecessary LINQ
  - Serialization issue
  - Memory allocation issue

  Sediakan temuan lengkap beserta baris kode yang bermasalah dan rekomendasi perbaikannya.
  ```

### Prompt 5.4: Full Solution Code Audit
* **Kapan digunakan:** Mengaudit seluruh solusi/codebase untuk mendeteksi berbagai jenis masalah kualitas kode, arsitektur, SOLID, memori, database, performa, dan celah keamanan.
* **Prompt Text:**
  ```text
  Analisa seluruh solution ini.

  Fokus:
  1. Performance bottleneck
  2. Security issue
  3. Code duplication
  4. Clean Architecture violation
  5. SOLID principle violation
  6. Potential memory leak
  7. Database optimization

  Buatkan report dalam format:
  - Critical
  - High
  - Medium
  - Low
  ```

### Prompt 5.5: Unit Test Generator & Coverage Audit
* **Kapan digunakan:** Membuat automated test terstandar menggunakan xUnit, Moq, dan FluentAssertions, serta mengaudit cakupan metode publik.
* **Prompt Text:**
  ```text
  Prompt:

  Generate xUnit test
  coverage minimum 90%.

  Gunakan:
  - Moq
  - FluentAssertions

  Lalu:

  Cari seluruh public method
  yang belum memiliki unit test.
  ```

### Prompt 5.6: Logging & Observability Audit
* **Kapan digunakan:** Memeriksa dan mengaudit implementasi pencatatan log (observabilitas) serta kepatuhan perlindungan data pada seluruh proyek.
* **Prompt Text:**
  ```text
  Review seluruh logging implementation.

  Cari:
  - Missing correlation id
  - Missing audit log
  - Sensitive data exposure
  - PII leakage
  - Error logging issue

  Berikan improvement plan.
  ```

---

## 6. Daily Workflows & Automation Prompts

### Prompt 6.1: Daily Standup Summary Generator
* **Kapan digunakan:** Membuat ringkasan pekerjaan Anda dengan bantuan Kiro berdasarkan git log.
* **Prompt Text:**
  ```text
  Here is my Git commit log for today:
  ```text
  [Paste Git Commits]
  ```
  Summarize this commit log into a professional Daily Standup update in Bahasa Indonesia using this structure:
  1. *What I did yesterday*: (Summary of achievements)
  2. *What I'm doing today*: (Next actions)
  3. *Blockers*: (Any potential delays or dependencies)
  ```

### Prompt 6.2: Automated PR Description Generator
* **Kapan digunakan:** Mengisi template Pull Request secara otomatis dari code diff.
* **Prompt Text:**
  ```text
  Here is the git diff for my current feature branch:
  ```diff
  [Paste Git Diff Here]
  ```
  Act as a Lead Engineer. Write a comprehensive Pull Request description based on this diff:
  1. **Summary of Changes**: Bullet points of main changes.
  2. **Type of Change**: (Feature, Bugfix, Refactoring, performance, etc.)
  3. **Technical Impact**: Any database migrations, config changes, or breaking API changes.
  4. **How Has This Been Tested**: Describe unit test, integration test, or manual validation.
  ```

---

*(Catatan: Anda dapat menaruh library prompt ini ke folder `.kiro/prompts/` di repository Anda agar dapat dipanggil cepat.)*
