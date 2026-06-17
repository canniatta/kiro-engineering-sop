---
inclusion: manual
---

# Prompt Library Index

> [!NOTE]
> **Source of Truth**
>
> - Library lengkap (120+ prompts): #[[file:docs/03-prompt-library.md]]
> - Prompt disimpan di: `.kiro/prompts/`

## Ringkasan

Dokumen ini adalah indeks ringkas dari seluruh kategori prompt yang tersedia di Kiro Prompt Library. Library ini dirancang untuk mempercepat pengembangan pada stack **.NET 8 + ReactJS + SQL Server**.

## Indeks Kategori

| No  | Kategori                     | Use Case                                                              | File Prompt                        |
| --- | ---------------------------- | --------------------------------------------------------------------- | ---------------------------------- |
| 1   | Architecture & System Design | DDD setup, Clean Architecture scaffolding, state management decisions | `.kiro/prompts/architecture.md`    |
| 2   | .NET 8 Backend Development   | CQRS commands, Dapper queries, MediatR handlers, FluentValidation     | `.kiro/prompts/dotnet-backend.md`  |
| 3   | ReactJS Frontend Development | React Hook Form + Zod, TanStack Query hooks, component scaffolding    | `.kiro/prompts/react-frontend.md`  |
| 4   | SQL Server Database          | Query optimization, execution plan analysis, idempotent migrations    | `.kiro/prompts/sql-database.md`    |
| 5   | Security & Code Review       | API security audit, React performance audit, vulnerability detection  | `.kiro/prompts/security-review.md` |
| 6   | Daily Workflows & Automation | Standup summary, PR description generator, git commit formatter       | `.kiro/prompts/workflows.md`       |

## Cara Penggunaan

1. Buka file prompt dari folder `.kiro/prompts/`
2. Copy prompt text yang relevan
3. Ganti placeholder `[...]` dengan konteks spesifik project
4. Paste ke Kiro chat atau inline prompt (`Ctrl+I` / `Cmd+I`)

> [!TIP]
> Untuk daftar lengkap 120+ prompts beserta contoh output dan tips penggunaan, lihat dokumen sumber #[[file:docs/03-prompt-library.md]].

## Highlight Prompt per Kategori

### 1. Architecture & System Design

- **Prompt 1.1** — DDD & Clean Architecture Setup (generate folder structure + domain layer)
- **Prompt 1.2** — State Management Strategy Decision (Zustand vs TanStack Query)

### 2. .NET 8 Backend

- **Prompt 2.1** — CQRS Command dengan MediatR & FluentValidation
- **Prompt 2.2** — High-Performance Dapper Query untuk Reports

### 3. ReactJS Frontend

- **Prompt 3.1** — Form Integration (React Hook Form + Zod)
- **Prompt 3.2** — Custom Hook untuk API Integration (TanStack Query)

### 4. SQL Server

- **Prompt 4.1** — Query Optimization dengan Execution Plan Analysis
- **Prompt 4.2** — Idempotent Migration Script

### 5. Security & Code Review

- **Prompt 5.1** — API Security Review (.NET 8 Controller)
- **Prompt 5.2** — React Performance Audit

### 6. Daily Workflows

- **Prompt 6.1** — Daily Standup Summary Generator
- **Prompt 6.2** — Automated PR Description Generator
