# Architecture Decision Records (ADRs)

> [!NOTE]
> **Source of Truth**
>
> - Template ADR lengkap: #[[file:docs/07-template-adr.md]]
> - Template file: `.kiro/specs/_templates/adr.md`

## Apa Itu ADR?

Architecture Decision Record (ADR) adalah dokumen pendek yang menangkap satu keputusan arsitektur penting beserta konteks dan konsekuensinya. ADR berfungsi sebagai *institutional memory* — membantu tim memahami **mengapa** suatu keputusan diambil, bukan hanya **apa** yang diputuskan.

## Mengapa ADR Penting?

- Mencegah keputusan yang sama didebatkan berulang kali
- Onboarding engineer baru lebih cepat memahami arsitektur
- Menyediakan audit trail untuk evolusi arsitektur
- Memudahkan evaluasi ulang keputusan saat konteks berubah

## Lokasi Penyimpanan

ADR untuk keputusan project-wide disimpan di:

```text
.kiro/steering/adrs/
├── README.md                          # File ini
├── adr-001-use-clean-architecture.md
├── adr-002-choose-mediatr-for-cqrs.md
└── adr-003-zustand-over-redux.md
```

## Konvensi Penamaan

Format: `adr-NNN-kebab-case-title.md`

| Komponen | Aturan | Contoh |
|---|---|---|
| Prefix | `adr-` (lowercase) | `adr-` |
| Nomor | 3 digit, zero-padded, urut | `001`, `012`, `099` |
| Separator | Strip tunggal | `-` |
| Judul | kebab-case, deskriptif | `use-clean-architecture` |
| Ekstensi | `.md` | `.md` |

## Kapan Membuat ADR Baru?

Buat ADR baru saat:

- Memilih atau mengganti framework / library utama
- Menambah service baru ke arsitektur (microservice, message broker)
- Mengubah architecture pattern (monolith → microservices, REST → gRPC)
- Memutuskan strategi database (SQL vs NoSQL, sharding)
- Mengubah authentication / authorization approach
- Memutuskan deployment strategy (containers, serverless)
- Membuat tradeoff yang signifikan (performance vs maintainability)

## Workflow

1. Identifikasi keputusan arsitektur yang perlu didokumentasikan
2. Copy template dari `.kiro/specs/_templates/adr.md`
3. Isi semua section (Context, Options, Decision, Consequences)
4. Submit sebagai PR dengan label `adr`
5. Review oleh minimal 1 Tech Lead
6. Setelah approved, merge dan update status ke `Accepted`

## Template Quick Reference

```markdown
# ADR-NNN: [Judul Keputusan]

**Status:** Proposed | Accepted | Deprecated | Superseded by ADR-XXX
**Date:** YYYY-MM-DD

## Context & Problem Statement
## Decision Drivers
## Considered Options
## Decision Outcome
## Consequences
```

> [!TIP]
> Gunakan template lengkap di `.kiro/specs/_templates/adr.md` yang mengikuti format MADR (Markdown Any Decision Records).
