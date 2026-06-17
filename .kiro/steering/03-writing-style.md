# Gaya Penulisan SOP

> [!NOTE]
> **Source of Truth**
>
> - Konvensi penulisan formal: #[[file:00-master-index.md]] (section "Konvensi Penulisan")
> - Contoh penerapan: dokumen `01-25` di root adalah referensi gaya hidup

## Bahasa

| Konteks | Bahasa | Contoh |
|---|---|---|
| Prosa naratif & deskripsi | **Bahasa Indonesia** | "Dokumen ini menjelaskan..." |
| Istilah teknis | **English** | "Repository pattern", "Eventual consistency" |
| Code, identifier, command | **English** | `userId`, `git push`, `OrderService` |
| Heading | **Bahasa Indonesia** dengan istilah teknis English boleh | "## Konfigurasi MediatR" |

> [!IMPORTANT]
> Jangan menerjemahkan istilah teknis yang sudah baku (Repository, CQRS, Middleware, Dependency Injection). Penerjemahan paksa malah membingungkan.

## Format Heading

| Level | Penggunaan |
|---|---|
| `#` (H1) | Judul dokumen — **hanya satu per file** |
| `##` (H2) | Section utama |
| `###` (H3) | Sub-section |
| `####` (H4) | Detail dalam sub-section — hindari kalau bisa |

> [!WARNING]
> Jangan skip level (langsung dari H1 ke H3). Jangan pakai bold sebagai pengganti heading.

## Code Blocks

Selalu pakai **fenced code block dengan language tag**:

````markdown
```csharp
public class OrderService { }
```

```sql
SELECT * FROM Orders;
```

```bash
dotnet build
```
````

> [!TIP]
> Language tag yang umum di repo ini: `csharp`, `typescript`, `tsx`, `sql`, `bash`, `yaml`, `json`, `mermaid`, `markdown`, `xml`, `text` (untuk plain).

## GitHub Alerts

Pakai alerts untuk highlight informasi penting:

```markdown
> [!NOTE]      — Informasi tambahan, latar belakang
> [!TIP]       — Saran praktis, shortcut
> [!IMPORTANT] — Wajib dipahami pembaca
> [!WARNING]   — Tindakan yang perlu kehati-hatian
> [!CAUTION]   — Risiko serius, destruktif, irreversible
```

> [!IMPORTANT]
> Jangan over-pakai alerts. Maksimal 3-4 alerts per dokumen panjang. Kalau semua di-highlight, tidak ada yang stand out.

## Tabel

Pakai tabel untuk:
- Membandingkan opsi/pendekatan
- Daftar konfigurasi/parameter
- Mapping tipe ↔ penggunaan
- Severity matrix, decision matrix

> [!TIP]
| Format | Saat Dipakai |
|---|---|
| Tabel | Data terstruktur dengan multiple atribut |
| Bullet list | Daftar item tanpa atribut tambahan |
| Numbered list | Langkah berurutan yang harus dilakukan |

## Diagram Mermaid

Diagram lebih baik daripada deskripsi panjang. Tipe yang sering dipakai di SOP ini:

````markdown
```mermaid
graph TD          — Flow / hierarchy
flowchart LR      — Process flow horizontal
sequenceDiagram   — Interaction antar komponen
stateDiagram-v2   — State machine
gantt             — Timeline
quadrantChart     — Comparison matrix
gitGraph          — Git workflow
```
````

## Panjang & Keterbacaan

| Aspek | Aturan |
|---|---|
| Line length | Max 120 karakter |
| Paragraph length | Max 4-5 kalimat |
| Section length | Pecah jika > 200 baris |
| Acronym pertama kali | Tulis lengkap, lalu (singkatan) |

## Tone

> [!NOTE]
> Tone SOP harus **knowledgeable tapi tidak menggurui**, **direct tapi tidak dingin**.

- ✅ "Gunakan parameterized query untuk mencegah SQL injection."
- ❌ "Anda harus selalu menggunakan parameterized query karena tanpa itu Anda akan terkena SQL injection."
- ❌ "Tolong gunakan parameterized query."

Pakai imperative voice untuk instruksi, pasif untuk deskripsi netral.

## Konsistensi Tipografi

| Elemen | Format |
|---|---|
| Nama produk/tool | **Bold** saat pertama disebut: **Kiro**, **MediatR** |
| File path | `inline code`: `appsettings.json` |
| Command | `inline code`: `git push` |
| Identifier kode | `inline code`: `OrderService.CreateAsync` |
| Emphasis kuat | **Bold** |
| Emphasis lembut | *Italic* — jarang dipakai |

## Yang Dihindari

> [!CAUTION]
> Hindari:
>
> - Emoji di heading kecuali sudah jadi konvensi dokumen tertentu
> - Singkatan tidak standar (kalau tidak yakin, tulis lengkap)
> - Filler words: "sebenarnya", "pada dasarnya", "intinya"
> - "Mudah" / "sederhana" untuk hal yang belum tentu mudah bagi pembaca
> - Hyperbole: "sangat penting", "sangat krusial" — pakai `> [!IMPORTANT]` saja
