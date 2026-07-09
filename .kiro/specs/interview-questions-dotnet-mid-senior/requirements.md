# Requirements Document

## Introduction

Dokumen ini mendefinisikan requirements untuk **28-interview-questions-dotnet-mid-senior.md** — sebuah dokumen Standard Operating Procedure (SOP) yang berisi kumpulan pertanyaan interview untuk kandidat .NET Developer level Mid to Senior.

Dokumen ini akan menjadi bagian dari kategori "Strategi Tim & Pengukuran Metrik" dalam SOP dan ditujukan untuk membantu Tech Lead, Hiring Manager, dan Engineering Manager dalam melakukan assessment kandidat secara konsisten dan komprehensif.

## Glossary

- **Interview_Questions_Document**: Dokumen SOP yang berisi kumpulan pertanyaan interview untuk .NET Developer
- **Mid_Level_Developer**: Kandidat dengan pengalaman 2-4 tahun, mampu bekerja mandiri dengan guidance minimal
- **Senior_Level_Developer**: Kandidat dengan pengalaman 5+ tahun, mampu lead technical decisions dan mentoring
- **Tech_Stack**: Kombinasi teknologi yang digunakan tim (.NET 8, C# 12, SQL Server 2022, Entity Framework Core 8.0, ReactJS 18)
- **Scoring_Rubrik**: Panduan penilaian untuk membedakan ekspektasi Mid vs Senior
- **EARS**: Easy Approach to Requirements Syntax — metodologi penulisan requirements

## Requirements

### Requirement 1: Dokumen Menyediakan Pendahuluan yang Jelas

**User Story:** Sebagai Tech Lead, saya ingin membaca pendahuluan yang menjelaskan tujuan dan cara penggunaan dokumen, sehingga saya dapat menggunakan pertanyaan secara efektif.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan section "Pendahuluan" yang menjelaskan tujuan dokumen
2. THE Interview_Questions_Document SHALL mendefinisikan target level kandidat (Mid vs Senior) dengan kriteria yang terukur
3. THE Interview_Questions_Document SHALL menjelaskan cara penggunaan dokumen dalam konteks interview flow
4. THE Interview_Questions_Document SHALL mereferensikan tech stack yang relevan (.NET 8, C# 12, SQL Server 2022, Entity Framework Core 8.0, ReactJS 18)

### Requirement 2: Dokumen Menyediakan Pertanyaan C# & .NET Fundamentals

**User Story:** Sebagai Hiring Manager, saya ingin memiliki 15-20 pertanyaan tentang C# & .NET Fundamentals, sehingga saya dapat menguji pemahaman dasar kandidat secara mendalam.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 15-20 pertanyaan di kategori "C# & .NET Fundamentals"
2. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan level kesulitan (Mid/Senior)
3. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan jawaban yang diharapkan atau poin-poin kunci
4. THE Interview_Questions_Document SHALL mencakup topik: memory management, async/await, LINQ, delegates, generics, reflection, attributes

### Requirement 3: Dokumen Menyediakan Pertanyaan Clean Architecture & Design Patterns

**User Story:** Sebagai Tech Lead, saya ingin memiliki 10-15 pertanyaan tentang Clean Architecture dan Design Patterns, sehingga saya dapat menguji kemampuan arsitektural kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 10-15 pertanyaan di kategori "Clean Architecture & Design Patterns"
2. THE Interview_Questions_Document SHALL mencakup topik: SOLID principles, Dependency Injection, Repository Pattern, CQRS, MediatR
3. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan contoh kode atau skenario implementasi
4. THE Interview_Questions_Document SHALL membedakan ekspektasi jawaban antara Mid dan Senior level

### Requirement 4: Dokumen Menyediakan Pertanyaan Entity Framework Core & Database

**User Story:** Sebagai Tech Lead, saya ingin memiliki 10-15 pertanyaan tentang Entity Framework Core dan Database, sehingga saya dapat menguji pemahaman data access kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 10-15 pertanyaan di kategori "Entity Framework Core & Database"
2. THE Interview_Questions_Document SHALL mencakup topik: DbContext lifecycle, tracking, migrations, query optimization, N+1 problem, transaction management
3. THE Interview_Questions_Document SHALL mencakup pertanyaan terkait SQL Server 2022 sebagai database target
4. WHEN pertanyaan tentang performance disajikan, THE Interview_Questions_Document SHALL menyertakan strategi mitigasi yang diharapkan

### Requirement 5: Dokumen Menyediakan Pertanyaan API Design & REST

**User Story:** Sebagai Tech Lead, saya ingin memiliki 8-10 pertanyaan tentang API Design dan REST, sehingga saya dapat menguji kemampuan kandidat dalam membangun API yang baik.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 8-10 pertanyaan di kategori "API Design & REST"
2. THE Interview_Questions_Document SHALL mencakup topik: RESTful conventions, HTTP methods, status codes, versioning, authentication/authorization, rate limiting
3. THE Interview_Questions_Document SHALL menyertakan pertanyaan tentang input validation dengan FluentValidation
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan contoh endpoint design

### Requirement 6: Dokumen Menyediakan Pertanyaan Performance & Optimization

**User Story:** Sebagai Tech Lead, saya ingin memiliki 8-10 pertanyaan tentang Performance dan Optimization, sehingga saya dapat menguji kemampuan kandidat dalam mengoptimasi aplikasi.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 8-10 pertanyaan di kategori "Performance & Optimization"
2. THE Interview_Questions_Document SHALL mencakup topik: caching strategies, async optimization, memory profiling, garbage collection, benchmarking
3. THE Interview_Questions_Document SHALL menyertakan pertanyaan tentang tools profiling (dotnet-counters, BenchmarkDotNet)
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan skenario performance bottleneck yang realistis

### Requirement 7: Dokumen Menyediakan Pertanyaan Testing & Quality

**User Story:** Sebagai Tech Lead, saya ingin memiliki 8-10 pertanyaan tentang Testing dan Quality, sehingga saya dapat menguji mindset kualitas kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 8-10 pertanyaan di kategori "Testing & Quality"
2. THE Interview_Questions_Document SHALL mencakup topik: unit testing dengan xUnit, mocking dengan NSubstitute, assertions dengan FluentAssertions, test coverage, TDD/BDD
3. THE Interview_Questions_Document SHALL menyertakan pertanyaan tentang testability dan dependency injection
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan contoh test case yang baik

### Requirement 8: Dokumen Menyediakan Pertanyaan Security

**User Story:** Sebagai Tech Lead, saya ingin memiliki 6-8 pertanyaan tentang Security, sehingga saya dapat menguji awareness keamanan kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 6-8 pertanyaan di kategori "Security"
2. THE Interview_Questions_Document SHALL mencakup topik: authentication, authorization, JWT, OWASP vulnerabilities, secure coding practices
3. THE Interview_Questions_Document SHALL menyertakan pertanyaan tentang SQL injection prevention dan input validation
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan contoh vulnerability dan mitigasinya

### Requirement 9: Dokumen Menyediakan Pertanyaan DevOps & Deployment

**User Story:** Sebagai Tech Lead, saya ingin memiliki 6-8 pertanyaan tentang DevOps dan Deployment, sehingga saya dapat menguji pemahaman operational kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 6-8 pertanyaan di kategori "DevOps & Deployment"
2. THE Interview_Questions_Document SHALL mencakup topik: Docker containerization, CI/CD pipelines (GitHub Actions/Azure DevOps), configuration management, logging dengan Serilog
3. THE Interview_Questions_Document SHALL menyertakan pertanyaan tentang health checks dan monitoring
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan skenario deployment yang realistis

### Requirement 10: Dokumen Menyediakan Pertanyaan Soft Skills & Problem Solving

**User Story:** Sebagai Hiring Manager, saya ingin memiliki 5-7 pertanyaan tentang Soft Skills dan Problem Solving, sehingga saya dapat menguji kemampuan interpersonal dan analitis kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 5-7 pertanyaan di kategori "Soft Skills & Problem Solving"
2. THE Interview_Questions_Document SHALL mencakup topik: technical decision making, mentoring approach, conflict resolution, code review mindset
3. THE Interview_Questions_Document SHALL membedakan ekspektasi jawaban antara Mid dan Senior level
4. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan indicator jawaban yang baik

### Requirement 11: Dokumen Menyediakan Pertanyaan Scenario-Based

**User Story:** Sebagai Tech Lead, saya ingin memiliki 5-7 pertanyaan scenario-based, sehingga saya dapat menguji kemampuan kandidat dalam menghadapi situasi nyata.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan 5-7 pertanyaan di kategori "Scenario-Based Questions"
2. THE Interview_Questions_Document SHALL mencakup skenario: production incident, legacy code refactoring, architecture decision, performance troubleshooting
3. WHEN pertanyaan disajikan, THE Interview_Questions_Document SHALL menyertakan konteks yang cukup untuk kandidat memberikan jawaban komprehensif
4. THE Interview_Questions_Document SHALL menyertakan expected follow-up questions untuk menggali lebih dalam

### Requirement 12: Dokumen Menyediakan Scoring Rubrik

**User Story:** Sebagai Hiring Manager, saya ingin memiliki scoring rubrik yang jelas, sehingga saya dapat membedakan ekspektasi Mid vs Senior secara objektif.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan section "Scoring Rubrik" yang mendefinisikan kriteria penilaian
2. THE Interview_Questions_Document SHALL membedakan ekspektasi jawaban untuk setiap level (Mid vs Senior) per kategori
3. THE Interview_Questions_Document SHALL menyertakan scoring guideline (contoh: scale 1-5 dengan deskripsi per level)
4. THE Interview_Questions_Document SHALL menyediakan contoh jawaban yang memenuhi kriteria Mid dan Senior

### Requirement 13: Dokumen Menyediakan Tips untuk Interviewer

**User Story:** Sebagai Tech Lead baru, saya ingin membaca tips untuk interviewer, sehingga saya dapat melakukan interview secara efektif.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan section "Tips untuk Interviewer"
2. THE Interview_Questions_Document SHALL mencakup tips: cara mengajukan follow-up questions, red flags yang perlu diwaspadai, green flags yang dicari
3. THE Interview_Questions_Document SHALL menyertakan panduan durasi interview dan distribusi pertanyaan
4. THE Interview_Questions_Document SHALL menyertakan sumber referensi untuk persiapan lebih lanjut

### Requirement 14: Dokumen Menyediakan Referensi

**User Story:** Sebagai Hiring Manager, saya ingin memiliki daftar referensi, sehingga saya dapat mempelajari topik lebih lanjut atau memvalidasi jawaban kandidat.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menyediakan section "Referensi" di akhir dokumen
2. THE Interview_Questions_Document SHALL menyertakan link ke dokumentasi resmi Microsoft untuk .NET 8 dan C# 12
3. THE Interview_Questions_Document SHALL menyertakan referensi ke dokumen SOP terkait (Clean Architecture, Testing Strategy, Code Review Checklist)
4. THE Interview_Questions_Document SHALL menyertakan referensi ke best practices dan community resources

### Requirement 15: Dokumen Mengikuti Konvensi Penulisan SOP

**User Story:** Sebagai Engineering Manager, saya ingin dokumen mengikuti konvensi penulisan SOP yang sudah ditetapkan, sehingga konsistensi dokumen terjaga.

#### Acceptance Criteria

1. THE Interview_Questions_Document SHALL menggunakan format penamaan `28-interview-questions-dotnet-mid-senior.md`
2. THE Interview_Questions_Document SHALL menggunakan Bahasa Indonesia untuk prosa naratif dan English untuk istilah teknis
3. THE Interview_Questions_Document SHALL menggunakan GitHub Alerts (`> [!NOTE]`, `> [!TIP]`, `> [!IMPORTANT]`, `> [!WARNING]`) untuk highlight informasi penting
4. THE Interview_Questions_Document SHALL menggunakan fenced code block dengan language tag untuk semua contoh kode
5. THE Interview_Questions_Document SHALL memiliki line length maksimal 120 karakter
