# Tech Stack & Tooling

> [!NOTE]
> **Source of Truth**
>
> - Stack yang ditargetkan SOP: #[[file:00-master-index.md]] (header dokumen)
> - Detail prerequisites & versi: #[[file:02-kiro-setup-and-configuration.md]] (section "Prerequisites")
> - Konvensi penulisan repo: #[[file:00-master-index.md]] (section "Konvensi Penulisan")

## Stack yang Didokumentasikan

> [!IMPORTANT]
> Semua template, checklist, dan playbook ditulis dengan asumsi stack berikut. Jika kamu memodifikasi SOP untuk stack berbeda, dokumentasikan di ADR baru.

| Layer | Teknologi | Versi |
|---|---|---|
| Backend | .NET | 8.0 LTS |
| Bahasa Backend | C# | 12 |
| Frontend Framework | ReactJS | 18.x |
| Bahasa Frontend | TypeScript | 5.x |
| Build Tool Frontend | Vite | 5.x |
| Database | SQL Server | 2022 |
| ORM | Entity Framework Core | 8.0 |
| State (Server) | TanStack Query | latest |
| State (Client) | Zustand | latest |
| Forms | React Hook Form + Zod | latest |
| Mediator | MediatR | latest |
| Validation | FluentValidation | latest |
| Logging | Serilog + OpenTelemetry | latest |
| Testing (.NET) | xUnit + NSubstitute + FluentAssertions | latest |
| Testing (React) | Vitest + React Testing Library | latest |
| Container | Docker / Docker Compose | latest |
| CI/CD | GitHub Actions / Azure DevOps | latest |

## Stack Repo Ini Sendiri

Repo `kiro-engineering-sop` ini **Markdown-only**:

- Tidak ada build step
- Tidak ada `package.json`, `*.csproj`, atau dependency runtime
- Tidak ada test runner
- Editor utama: VS Code / Kiro
- Format file: `.md` dengan GitHub Flavored Markdown
- Diagram: Mermaid (di-render langsung oleh GitHub/Kiro)
- Alerts: GitHub-style (`> [!NOTE]`, `> [!IMPORTANT]`, `> [!WARNING]`, `> [!TIP]`, `> [!CAUTION]`)

## Tools yang Diasumsikan Tersedia

Saat membaca/mengedit repo SOP ini:

- Markdown preview (VS Code/Kiro built-in)
- Mermaid renderer (built-in di GitHub dan Kiro)
- Optional: `markdownlint` untuk konsistensi style — tidak wajib

## Tidak Berlaku di Repo Ini

> [!CAUTION]
> Hal-hal berikut sering disebut di dokumen SOP, tapi **tidak relevan** saat mengedit repo ini sendiri:

- Build pipeline (.NET/npm) — ini hanya dokumen
- Database connection — tidak ada DB
- Runtime services (Redis, RabbitMQ) — tidak dipakai
- Deployment ke server — tidak ada artefak runnable
