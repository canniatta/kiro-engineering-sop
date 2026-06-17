# [Nama Feature] — Task Breakdown

> [!NOTE]
> **Source of Truth**
>
> - Workflow SDD: #[[file:14-spec-driven-development.md]]
> - Requirements: `./requirements.md`
> - Design: `./design.md`

---

## Referensi

- **Requirements:** [Link ke requirements.md dalam folder spec ini]
- **Design:** [Link ke design.md dalam folder spec ini]
- **Estimasi Total:** [X] jam

---

## Phase 1: Setup & Infrastructure

- [ ] Buat folder structure sesuai Clean Architecture (estimated: 1 hour)
- [ ] Setup project/module baru di solution (estimated: 1 hour)
- [ ] Konfigurasi DI registration untuk module baru (estimated: 0.5 hour)
- [ ] Setup database migration initial (estimated: 1 hour)

## Phase 2: Backend — Domain & Application Layer

- [ ] Buat domain entities dan value objects (estimated: 2 hours)
- [ ] Buat domain events (estimated: 1 hour)
- [ ] Buat repository interfaces (estimated: 0.5 hour)
- [ ] Buat MediatR Commands + Handlers (estimated: 3 hours)
- [ ] Buat MediatR Queries + Handlers (estimated: 2 hours)
- [ ] Buat FluentValidation validators (estimated: 1.5 hours)
- [ ] Buat DTOs (Request/Response) (estimated: 1 hour)
- [ ] Konfigurasi mapping (Mapster/AutoMapper) (estimated: 0.5 hour)

## Phase 3: Backend — Infrastructure Layer

- [ ] Implementasi EF Core entity configurations (estimated: 1.5 hours)
- [ ] Implementasi repository classes (estimated: 2 hours)
- [ ] Buat database seed data jika diperlukan (estimated: 1 hour)
- [ ] Implementasi external service integrations (estimated: 2 hours)

## Phase 4: Backend — Presentation Layer

- [ ] Buat API Controllers / Minimal API endpoints (estimated: 2 hours)
- [ ] Konfigurasi middleware khusus jika diperlukan (estimated: 1 hour)
- [ ] Tambahkan Swagger/OpenAPI documentation (estimated: 0.5 hour)

## Phase 5: Frontend

- [ ] Buat TypeScript interfaces/types dari API contract (estimated: 1 hour)
- [ ] Buat API client service (Axios) (estimated: 1 hour)
- [ ] Buat TanStack Query hooks (estimated: 2 hours)
- [ ] Buat komponen UI utama (estimated: 4 hours)
- [ ] Buat form dengan React Hook Form + Zod (estimated: 2 hours)
- [ ] Integrasi state management (Zustand jika perlu) (estimated: 1 hour)

## Phase 6: Database

- [ ] Finalize schema DDL (estimated: 1 hour)
- [ ] Buat migration scripts (estimated: 1 hour)
- [ ] Buat indexes sesuai query patterns (estimated: 0.5 hour)
- [ ] Validasi backward compatibility migration (estimated: 0.5 hour)

## Phase 7: Testing

- [ ] Unit tests — Domain layer (estimated: 2 hours)
- [ ] Unit tests — Application layer (Handlers) (estimated: 3 hours)
- [ ] Unit tests — Frontend components (estimated: 2 hours)
- [ ] Integration tests — API endpoints (estimated: 3 hours)
- [ ] Verify coverage targets terpenuhi (estimated: 0.5 hour)

## Phase 8: Documentation & Cleanup

- [ ] Update API documentation / Swagger annotations (estimated: 0.5 hour)
- [ ] Update README jika ada perubahan setup (estimated: 0.5 hour)
- [ ] Code cleanup — remove TODO, unused imports (estimated: 0.5 hour)
- [ ] Self-review menggunakan PR checklist (estimated: 0.5 hour)

## Phase 9: Deployment Preparation

- [ ] Verify environment variables untuk Staging/Production (estimated: 0.5 hour)
- [ ] Prepare rollback script (estimated: 0.5 hour)
- [ ] Test migration di Staging environment (estimated: 1 hour)
- [ ] Smoke test di Staging (estimated: 0.5 hour)

---

> [!TIP]
> Tandai task sebagai complete (`[x]`) saat selesai dikerjakan. Gunakan checklist ini sebagai progress tracker selama implementasi.
