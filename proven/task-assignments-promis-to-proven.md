# 📝 Daftar Penugasan & Catatan Use Case: Promis To Proven

> **Proyek:** Promis To Proven (Management PRO, Management Suder, & Selected Provider)  
> **Tanggal Pembuatan:** 18 Juni 2026  
> **Status:** 🟡 Draft / Active  
> **Dokumen Terkait:** [SOP Master Index](../docs/00-master-index.md)

---

## 👥 Ringkasan Distribusi Penugasan (Use Case Matrix)

Berikut adalah pembagian peran dan tanggung jawab antara Back-End (BE) dan Front-End (FE) untuk masing-masing Use Case (UC), dilengkapi dengan status, prioritas, dan target timeline.

### 1. Modul: Management PRO & Management Suder

#### 1.1 Back-End (BE) - Management PRO & Management Suder

| Use Case | Target Scope | Developer BE | Prioritas | Timeline (Target) | Status | Catatan Khusus / Tindak Lanjut |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **UC1** | Semua UC1 | 👤 Nopal | P1 | *[Isi Tanggal/Sprint]* | 🟡 Sedang Dikerjakan | - |
| **UC2** | Semua UC2 | 👤 Lee | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC3** | Semua UC3 | 👤 Nopal | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | Penyesuaian input nomor kontrak & daftar dokumen ([Lihat detail catatan](#catatan-uc3)) |
| **UC5** | Semua UC5 | 👤 Lee | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC6** | Semua UC6 | 👤 Nopal | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC7** | Semua UC7 | 👤 Lee | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC8** | Semua UC8 | 👤 Lee | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | Penyederhanaan tab poli > 10 ([Lihat detail catatan](#catatan-uc8)) |
| **UC9** | Semua UC9 | 👤 Nopal | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC10** | Semua UC10 | 👤 Lee | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC14** | Semua UC14 | 👤 Nopal | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |

#### 1.2 Front-End (FE) - Management PRO & Management Suder

| Use Case | Target Scope | Developer FE | Prioritas | Timeline (Target) | Status | Catatan Khusus / Tindak Lanjut |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **UC1** | Semua UC1 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC2** | Semua UC2 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC3** | Semua UC3 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | Penyesuaian input nomor kontrak & daftar dokumen ([Lihat detail catatan](#catatan-uc3)) |
| **UC5** | Semua UC5 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC6** | Semua UC6 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC7** | Semua UC7 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC8** | Semua UC8 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | Penyederhanaan tab poli > 10 ([Lihat detail catatan](#catatan-uc8)) |
| **UC9** | Semua UC9 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC10** | Semua UC10 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |
| **UC14** | Semua UC14 | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |

> [!TIP]
> **Panduan Indikator Status & Tingkat Prioritas:**
> * **Status:** 🔴 **Belum Mulai** (Todo) | 🟡 **Sedang Dikerjakan** (In Progress) | 🟢 **Selesai** (Done) | ⚪ **Terhambat** (Blocked)
> * **Prioritas:** **P0** (Kritikal / Blokir Alur Kerja) | **P1** (Tinggi / Alur Utama Fitur) | **P2** (Sedang-Rendah / Fitur Pendukung)

### 2. Modul: Selected Provider

| Use Case | Target Scope | Developer BE | Developer FE | Prioritas | Timeline (Target) | Status | Catatan Khusus / Tindak Lanjut |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **UC9** | Semua UC9 | 👤 Lee | 👤 Raihan | P1 | *[Isi Tanggal/Sprint]* | 🔴 Belum Mulai | - |

---

## 📌 Catatan Penting & Tindak Lanjut Teknis

<div id="catatan-uc3"></div>

> [!NOTE]
> **Catatan Pengembangan UC3 (Management PRO & Suder - BE: Nopal)**
> 1. **Nomor Kontrak:** Inputan section nomor kontrak untuk sementara dihilangkan terlebih dahulu, hingga proses *generate* perjanjian kerja sama (PKS) sudah berhasil dibuat secara dinamis oleh sistem.
> 2. **Daftar Dokumen:** Tampilan daftar dokumen perlu disesuaikan (*adjustment*) kembali, karena dokumen yang akan ditampilkan berjumlah kurang lebih 15 dokumen.

<div id="catatan-uc8"></div>

> [!IMPORTANT]
> **Catatan Pengembangan UC8 (Management PRO & Suder - BE: Lee / FE: Raihan)**
> * **Masalah UX (Tab Poli):** Jika jumlah poli melebihi 10, tampilan tab poli menjadi terlalu banyak dan kurang optimal dari sisi *user experience* (UX).
> * **Solusi/Mitigasi:** Struktur tab perlu disederhanakan (*simplify*) dan disesuaikan (*adjust*) kembali.
> * **Tindakan Lanjut:** Tim akan melakukan diskusi kolaboratif antara **Raihan, Lee, dan Dika** untuk menentukan solusi desain terbaik.

---

## 🏁 Checklist Progress Implementasi

Gunakan checklist di bawah ini untuk melacak perkembangan pengerjaan masing-masing tim developer sesuai SOP pengerjaan proyek.

### 💻 Back-End (BE) Tasks

#### Penugasan: Nopal
- [/] **UC1**: Implementasi backend untuk semua modul UC1 *(Target: [Isi])*
- [ ] **UC3**: Penyesuaian backend UC3 (PKS dinamis & list 15 dokumen) *(Target: [Isi])*
- [ ] **UC6**: Implementasi backend untuk semua modul UC6 *(Target: [Isi])*
- [ ] **UC9**: Implementasi backend untuk modul UC9 (Management PRO & Suder) *(Target: [Isi])*
- [ ] **UC14**: Implementasi backend untuk semua modul UC14 *(Target: [Isi])*

#### Penugasan: Lee
- [ ] **UC2**: Implementasi backend untuk semua modul UC2 *(Target: [Isi])*
- [ ] **UC5**: Implementasi backend untuk semua modul UC5 *(Target: [Isi])*
- [ ] **UC7**: Implementasi backend untuk semua modul UC7 *(Target: [Isi])*
- [ ] **UC8**: Implementasi backend untuk semua modul UC8 (termasuk diskusi simplify tab poli) *(Target: [Isi])*
- [ ] **UC10**: Implementasi backend untuk semua modul UC10 *(Target: [Isi])*
- [ ] **UC9 (Selected Provider)**: Implementasi backend UC9 khusus modul Selected Provider *(Target: [Isi])*

---

### 🎨 Front-End (FE) Tasks

#### Penugasan: Raihan
- [ ] **UC1**: Implementasi UI/UX frontend untuk UC1 *(Target: [Isi])*
- [ ] **UC2**: Implementasi UI/UX frontend untuk UC2 *(Target: [Isi])*
- [ ] **UC3**: Integrasi daftar dokumen (penyajian ~15 dokumen) dan penonaktifan input nomor kontrak sementara *(Target: [Isi])*
- [ ] **UC5**: Implementasi UI/UX frontend untuk UC5 *(Target: [Isi])*
- [ ] **UC6**: Implementasi UI/UX frontend untuk UC6 *(Target: [Isi])*
- [ ] **UC7**: Implementasi UI/UX frontend untuk UC7 *(Target: [Isi])*
- [ ] **UC8**: Diskusi UX tab poli (>10 poli) bersama Lee dan Dika, serta implementasi hasil penyederhanaan tab *(Target: [Isi])*
- [ ] **UC9**: Implementasi UI/UX frontend untuk UC9 (Management PRO & Suder) *(Target: [Isi])*
- [ ] **UC9 (Selected Provider)**: Implementasi UI/UX frontend UC9 khusus modul Selected Provider *(Target: [Isi])*
- [ ] **UC10**: Implementasi UI/UX frontend untuk UC10 *(Target: [Isi])*
- [ ] **UC14**: Implementasi UI/UX frontend untuk UC14 *(Target: [Isi])*
