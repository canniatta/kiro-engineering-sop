# Prompts: Security & Code Review

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 5: #[[file:docs/03-prompt-library.md]] (Section "Security & Code Review Prompts")
> - Code review checklist: #[[file:docs/08-template-code-review-checklist.md]]
> - API review checklist: #[[file:docs/10-template-api-review-checklist.md]]
> - API Performance Review Checklist: #[[file:docs/10a-api-performance-review-checklist.md]]

---

## 1. API Security Review

**Kapan digunakan:** Sebelum deploy API baru ke production — mendeteksi celah keamanan OWASP pada .NET 8 controller atau endpoint.

```text
Review the following .NET 8 API Controller/Endpoint code for security vulnerabilities:
```csharp
[Paste C# Code Here]
```
Check for:
1. Mass Assignment (Overposting) vulnerabilities.
2. SQL Injection / NoSQL Injection.
3. Broken Object Level Authorization (BOLA / IDOR).
4. Cross-Site Scripting (XSS) / Input validation flaws.
5. Sensitive data exposure in logs or response payloads.
6. Rate limiting and CORS configurations.
Provide actionable fixes for any discovered issues.
```

> [!TIP]
> - Paste seluruh controller class termasuk DI constructor dan attribute routing
> - Sertakan DTO/request model yang digunakan endpoint tersebut
> - Sebutkan jika ada middleware custom (auth, validation) yang sudah aktif
> - Gunakan output sebagai input untuk code review — bukan pengganti review manual

> [!IMPORTANT]
> Security review dari AI adalah **layer pertama** deteksi. Untuk production deployment, tetap lakukan penetration testing dan review manual oleh security engineer.

---

## 2. React Performance Audit

**Kapan digunakan:** Mengaudit component React yang terasa lambat atau sering re-render tanpa alasan yang jelas.

```text
Audit this React component for performance bottlenecks:
```tsx
[Paste React/TypeScript Code Here]
```
Identify:
1. Unnecessary re-renders.
2. Expensive calculations that should be memoized using `useMemo`.
3. Functions passed as props that should use `useCallback`.
4. Opportunities for code-splitting / dynamic imports.
5. Virtualization needs if rendering large lists.
Provide the refactored code with explanations.
```

> [!TIP]
> - Paste component beserta parent component yang merender-nya (untuk konteks re-render propagation)
> - Sertakan informasi ukuran data yang di-render (misal: list 500+ items)
> - Sebutkan apakah component ini di-render di route utama atau lazy-loaded
> - Untuk list besar (>100 items), pertimbangkan `react-window` atau `@tanstack/react-virtual`

> [!WARNING]
> Jangan over-optimize. Premature optimization bisa menurunkan readability tanpa benefit nyata. Prioritaskan component yang terbukti lambat via React DevTools Profiler.

---

## 3. API Performance Review

**Kapan digunakan:** Mengaudit dan meninjau seluruh API Controller untuk mendeteksi bottleneck kinerja dan masalah alokasi memori sebelum rilis.

```text
Review seluruh API Controller.

Cari:
- N+1 query
- Multiple DbContext call
- Async issue
- Unnecessary LINQ
- Serialization issue
- Memory allocation issue

Sediakan temuan lengkap beserta baris kode yang bermasalah dan rekomendasi perbaikannya.
```

> [!TIP]
> - Tempelkan seluruh kelas Controller termasuk constructor Dependency Injection (DI).
> - Rujuk standar perbaikan di dokumen #[[file:docs/10a-api-performance-review-checklist.md]] sebagai acuan.
> - Mintalah AI untuk menyertakan perbandingan kode sebelum vs sesudah optimasi.

> [!IMPORTANT]
> Selalu pastikan hasil optimasi dari AI diuji melalui unit/integration testing untuk menjamin tidak ada perubahan behavior fungsional API.
