# Prompts: ReactJS Frontend Development

> [!NOTE]
> **Source of Truth**
>
> - Master library Section 3: #[[file:docs/03-prompt-library.md]] (Section "ReactJS Frontend Development Prompts")
> - Standar frontend: #[[file:docs/13-template-reactjs-frontend-standard.md]]

---

## 1. Form Integration dengan React Hook Form & Zod

**Kapan digunakan:** Membuat form kompleks dengan validasi sisi client menggunakan TypeScript, React Hook Form, dan Zod schema.

```text
Create a React functional component using TypeScript and Vite for [Nama Form].
The form needs to:
1. Use `react-hook-form` for state management and submission.
2. Use `@hookform/resolvers/zod` with a Zod schema to enforce validation rules.
3. Contain field validation (e.g., Email, required fields, minimum length, custom validation logic).
4. Integrate with TailwindCSS or custom CSS modules for modern styling.
5. Show clear validation error messages under each field and highlight invalid inputs.
6. Disable the submit button during submission state.
```

> [!TIP]
> - Ganti `[Nama Form]` dengan deskripsi spesifik (misal: "User Registration Form with address autocomplete")
> - Sebutkan field yang dibutuhkan beserta tipe data dan constraint validasi
> - Tambahkan requirement accessibility (aria labels, error announcements) jika form publik
> - Hasil form component bisa langsung diintegrasikan dengan custom hook TanStack Query (lihat prompt #2 di bawah)

---

## 2. Custom React Hook untuk API Integration (TanStack Query)

**Kapan digunakan:** Menghubungkan UI React dengan endpoint REST API .NET 8 menggunakan TanStack Query untuk CRUD operations dengan cache management.

```text
Write a custom React Hook using TypeScript and `@tanstack/react-query` to manage CRUD operations for resource [Nama Resource] (e.g., Products).
The hook should:
1. Define query keys correctly.
2. Implement `useQuery` for fetching list and detail.
3. Implement `useMutation` for Create, Update, and Delete actions.
4. Automatically invalidate the query cache on successful mutation (optimistic updates optional).
5. Implement error handling and loading indicators.
6. Call an Axios API client instance that includes Authorization headers.
```

> [!TIP]
> - Ganti `[Nama Resource]` dengan entity spesifik dan sebutkan endpoint API-nya
> - Tambahkan requirement pagination jika list endpoint mendukungnya
> - Sebutkan apakah perlu optimistic updates (untuk UX yang lebih responsif)
> - Hook ini biasanya di-pair dengan Zustand store untuk client-side state (filter, selection)

> [!IMPORTANT]
> Pastikan query keys mengikuti konvensi yang konsisten di seluruh aplikasi. Contoh pattern: `[resource, action, params]` → `['products', 'list', { page: 1 }]`.
