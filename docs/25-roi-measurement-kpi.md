# KPI & ROI Measurement Playbook (Kiro Integration)

Dokumen ini mendefinisikan kerangka kerja untuk mengukur produktivitas developer, performa rilis tim, serta menghitung Return on Investment (ROI) finansial dari adopsi asisten AI **Kiro** pada tim engineering.

---

## 1. DORA Metrics (DevOps Research and Assessment)

Kami mengukur efektivitas tim engineering berdasarkan 4 pilar metrik DORA utama:

| Metrik DORA | Definisi | Target Tim Elite | Cara Mengukurnya |
|-------------|----------|------------------|------------------|
| **Deployment Frequency** | Seberapa sering tim berhasil mendeploy code ke production. | Harian / Beberapa kali sehari | Jumlah build deployment sukses di pipeline CD Azure DevOps. |
| **Lead Time for Changes** | Waktu yang dibutuhkan dari commit pertama hingga masuk production. | < 1 Hari | Timestamp merge PR dikurangi timestamp commit pertama di branch tersebut. |
| **Mean Time to Restore (MTTR)**| Waktu yang dibutuhkan untuk memulihkan layanan dari kegagalan Production (SEV1/2). | < 1 Jam | Waktu insiden selesai dimitigasi dikurangi waktu deteksi alert insiden masuk. |
| **Change Failure Rate** | Persentase deployment ke production yang mengakibatkan kegagalan/hotfix. | < 15% | Jumlah hotfix/rollback deployment dibagi total deployment rilis bulanan. |

---

## 2. Kiro AI-Assistant ROI Framework

Dengan mengintegrasikan Kiro ke alur kerja harian, kami menargetkan efisiensi waktu kerja di berbagai aktivitas teknis:

### 2.1 Metrik Penghematan Waktu (Target Efisiensi)
* **Boilerplate & Project Setup:** Efisiensi **70%**. Kiro mereduksi pembuatan kelas-kelas DTO, Entity configurations, dan routing controller dari jam menjadi menit.
* **Unit Testing Writing:** Efisiensi **60%**. Kiro mempercepat penulisan test cases xUnit/Jest dari data input mock.
* **Refactoring Legacy Code:** Efisiensi **40%**. Membantu memahami struktur class lama dan menyusun ulang ke kode modern secara cepat.
* **API Documentation (Swagger/OpenAPI):** Efisiensi **80%**. Otomasi penulisan OpenAPI YAML spec dari model C#.

### 2.2 Rumus Perhitungan ROI Finansial (Contoh Kasus)

Untuk menghitung nilai pengembalian investasi lisensi Kiro:

$$\text{ROI} = \frac{\text{Nilai Penghematan Waktu (USD)} - \text{Biaya Lisensi Kiro (USD)}}{\text{Biaya Lisensi Kiro (USD)}} \times 100\%$$

#### Contoh Simulasi Tim (10 Developer):
* Biaya Rata-rata Developer per jam: **$25 / jam**.
* Waktu Kerja Developer per bulan: **160 jam**.
* Rata-rata Penghematan Waktu dengan Kiro: **20%** (setara **32 jam/bulan per developer**).
* **Total Penghematan Waktu Tim per Bulan:** $10 \text{ dev} \times 32 \text{ jam} = 320 \text{ jam/bulan}$.
* **Nilai Penghematan Uang (Cost Saved):** $320 \text{ jam} \times \$25 = \$8,000 / \text{bulan}$.
* **Biaya Lisensi Kiro (Misal):** $10 \text{ dev} \times \$30 = \$300 / \text{bulan}$.
* **Net Value Saved per Bulan:** $\$8,000 - \$300 = \$7,700 / \text{bulan}$.
* **Return on Investment (ROI):** 
  $$\text{ROI} = \frac{\$8,000 - \$300}{\$300} \times 100\% = 2566\%$$

---

## 3. Template Laporan Produktivitas Bulanan (KPI Report)

```markdown
# BULANAN ENGINEERING PRODUCTIVITY & Kiro ROI REPORT - [BULAN YYYY]

## 1. DORA Metrics Performance
* **Deployment Frequency:** [e.g., 22 Deployments]
* **Lead Time for Changes:** [e.g., 4.2 Hours (Average)]
* **MTTR:** [e.g., 28 Minutes]
* **Change Failure Rate:** [e.g., 4.5% (1 Failure out of 22)]

## 2. Kiro Adoption Metrics
* **Total Active Licenses:** [e.g., 10 / 10]
* **Estimated Hours Saved:** [e.g., 340 Hours (Tim Total)]
* **Most Used Prompt Categories:** Unit Testing (35%), Boilerplate (30%), Debugging (25%).

## 3. Kualitas & Defect Rate
* **Bugs Reported in Production:** [e.g., 2 Bugs (SEV3)]
* **Average Code Coverage Backend:** [e.g., 84.5% (+2.1% from last month)]
* **Average Code Coverage Frontend:** [e.g., 71.0%]

## 4. Key Learnings & Action Items
* *Pelajaran:* Penggunaan steering file `.kiro/rules` di repository utama sukses menurunkan tingkat revisi pada PR code review sebesar 15%.
* *Action Item:* Rilis Kiro steering file terstandarisasi untuk repositori Microservices bulan depan.
```
