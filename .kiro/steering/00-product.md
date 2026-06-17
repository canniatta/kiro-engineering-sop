# Produk: Kiro Engineering SOP

> [!NOTE]
> **Source of Truth**
>
> - Indeks lengkap dan deskripsi proyek: #[[file:docs/00-master-index.md]]
> - Mindset & filosofi: #[[file:docs/01-executive-summary-and-mindset.md]]
>
> File ini ringkasan untuk Kiro. Untuk konteks bisnis lengkap, baca dokumen sumber.

## Apa Repo Ini

Repo ini adalah **single source of truth** untuk Standard Operating Procedure (SOP) tim engineering yang menggunakan stack **.NET 8 + ReactJS 18 + SQL Server 2022** dengan asisten AI **Kiro**.

Berisi 26 dokumen Markdown yang mencakup seluruh siklus pengembangan:

| Kategori | Dokumen |
|---|---|
| Inti | `00-master-index`, `01-executive-summary-and-mindset` |
| Setup | `02-kiro-setup-and-configuration`, `03-prompt-library` |
| Template | `04-template-prd-...` sampai `13-template-reactjs-frontend-standard` |
| Workflow | `14-spec-driven-development` sampai `18-workflow-daily-weekly-monthly` |
| Playbook | `19-playbook-performance-tuning-sql`, `24-refactoring-legacy-systems` |
| Strategi | `20-technical-debt-management` sampai `25-roi-measurement-kpi` |

## Audiens

| Peran | Fokus Utama |
|---|---|
| **Engineering Manager** | ROI, change management, metrics |
| **Tech Lead / Senior Developer** | Arsitektur, code quality, standardisasi |
| **Developer (Junior–Mid)** | Setup environment, prompt mastery |
| **QA, DevOps, Product Manager** | Referensi lintas peran |

## Tujuan SOP

1. Mengurangi inkonsistensi code style antar developer
2. Memangkas waktu onboarding dari 2-4 minggu menjadi < 1 minggu
3. Menyediakan library prompt siap pakai (120+ prompt)
4. Menyamakan standar code review, SQL review, dan API review
5. Menyediakan template untuk artefak engineering (PRD, SRS, TDD, ADR)

## Prinsip Kerja Repo

> [!IMPORTANT]
> **Aturan emas**: dokumen di root (`00-` sampai `25-`) adalah deliverable utama. Folder `.kiro/` hanya **mereferensikan** dokumen root, bukan menduplikasi.

- Dokumen di root adalah produk akhir yang dibaca tim
- Folder `.kiro/` berisi instruksi tipis untuk Kiro yang merujuk balik ke root
- Single source of truth: jangan menyalin isi dokumen root ke dalam steering
- Setiap perubahan SOP melalui PR dengan minimal 1 reviewer dari Tech Lead

## Yang Bukan Tujuan Repo Ini

> [!WARNING]
> Repo ini **bukan** codebase aplikasi. Tidak ada `package.json`, `*.csproj`, atau dependency runtime. Jangan jalankan `dotnet build`, `npm install`, atau setup infra apa pun di sini.

- Bukan dokumentasi API spesifik proyek tertentu — melainkan template untuk membuatnya
- Bukan onboarding tool interaktif — melainkan handbook referensi
