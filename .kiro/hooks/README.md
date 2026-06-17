# Kiro Hooks

> [!NOTE]
> **Source of Truth**
>
> - Kiro setup & konfigurasi: #[[file:docs/02-kiro-setup-and-configuration.md]]
> - Dokumentasi hooks: Kiro internal documentation

## Apa Itu Hooks?

Hooks adalah file JSON yang men-trigger aksi otomatis berdasarkan event di IDE Kiro. Hooks memungkinkan otomasi repetitive tasks tanpa intervensi manual — seperti menjalankan linter setelah save, atau mengingatkan developer untuk update indeks setelah membuat file baru.

## Lokasi Penyimpanan

```text
.kiro/hooks/
├── README.md          # File ini
├── <id>.json          # Satu file per hook
└── ...
```

Setiap hook disimpan sebagai file JSON terpisah dengan ID unik sebagai nama file.

## Available Triggers

| Trigger | Kapan Dijalankan |
|---|---|
| `PostFileSave` | Setelah file disimpan |
| `PostFileCreate` | Setelah file baru dibuat |
| `PostFileDelete` | Setelah file dihapus |
| `PreToolUse` | Sebelum tool dieksekusi |
| `PostToolUse` | Setelah tool selesai dieksekusi |
| `SessionStart` | Saat sesi Kiro dimulai |
| `Stop` | Saat sesi Kiro dihentikan |
| `UserPromptSubmit` | Saat user mengirim prompt |
| `PreTaskExec` | Sebelum task dieksekusi |
| `PostTaskExec` | Setelah task selesai dieksekusi |

## Tipe Action

| Action Type | Deskripsi | Contoh Penggunaan |
|---|---|---|
| `command` | Menjalankan shell command | Linter, formatter, build check |
| `agent` | Meng-inject prompt ke Kiro agent | Reminder, validation check, auto-documentation |

## Format JSON Hook

```json
{
  "id": "lint-markdown-on-save",
  "trigger": "PostFileSave",
  "pattern": "**/*.md",
  "action": {
    "type": "command",
    "command": "npx markdownlint {{filePath}}"
  },
  "description": "Run markdownlint on saved Markdown files"
}
```

| Field | Required | Deskripsi |
|---|---|---|
| `id` | Ya | Identifier unik hook |
| `trigger` | Ya | Event yang men-trigger hook (lihat tabel di atas) |
| `pattern` | Tidak | Glob pattern untuk filter file (berlaku untuk file-based triggers) |
| `action` | Ya | Object berisi `type` dan detail aksi |
| `action.type` | Ya | `"command"` atau `"agent"` |
| `action.command` | Untuk `command` | Shell command yang dijalankan. Supports `{{filePath}}` placeholder |
| `action.prompt` | Untuk `agent` | Prompt yang di-inject ke Kiro agent |
| `description` | Tidak | Deskripsi human-readable |

## Contoh Use Cases untuk Repo SOP Ini

### 1. Lint Markdown on Save (Optional)

```json
{
  "id": "lint-markdown-on-save",
  "trigger": "PostFileSave",
  "pattern": "**/*.md",
  "action": {
    "type": "command",
    "command": "npx markdownlint {{filePath}} --fix"
  },
  "description": "Auto-fix Markdown style issues on save"
}
```

**Kapan berguna:** Menjaga konsistensi format Markdown di seluruh dokumen SOP.

### 2. Remind Update Master Index on New File

```json
{
  "id": "remind-update-index",
  "trigger": "PostFileCreate",
  "pattern": "[0-9][0-9]-*.md",
  "action": {
    "type": "agent",
    "prompt": "File SOP baru telah dibuat. Ingatkan user untuk: 1) Update tabel Table of Contents di 00-master-index.md, 2) Update changelog, 3) Tambahkan ke Index A-Z jika topik baru."
  },
  "description": "Remind to update 00-master-index.md when a new SOP document is created"
}
```

**Kapan berguna:** Mencegah lupa update indeks saat menambah dokumen SOP baru — sesuai aturan di `02-structure` steering file.

### 3. Verify Writing Style After Task Execution

```json
{
  "id": "verify-writing-style",
  "trigger": "PostTaskExec",
  "action": {
    "type": "agent",
    "prompt": "Task selesai. Verifikasi bahwa perubahan yang dilakukan mengikuti standar penulisan: Bahasa Indonesia untuk prosa, English untuk istilah teknis, heading tidak skip level, dan code blocks menggunakan language tag yang sesuai."
  },
  "description": "Remind to verify changes against writing style guide after task completion"
}
```

**Kapan berguna:** Menjaga konsistensi gaya penulisan saat Kiro mengedit atau membuat dokumen SOP.

## Catatan Penting

> [!TIP]
> Hooks bersifat **opsional**. Mulai tanpa hooks, lalu tambahkan saat tim menemukan pola repetitif yang layak diotomasi.

> [!WARNING]
> - Hook dengan action `command` memerlukan tool yang terinstall di environment (misal: `markdownlint`)
> - Pastikan command yang dijalankan bersifat non-destructive
> - Test hook di environment lokal sebelum commit ke repository

## Kapan Menambah Hook Baru

Pertimbangkan membuat hook baru saat:

- Ada langkah yang selalu lupa dilakukan setelah aksi tertentu
- Ada validasi yang perlu dijalankan setiap kali file diubah
- Tim sepakat ada proses yang bisa diotomasi tanpa mengurangi kontrol developer
