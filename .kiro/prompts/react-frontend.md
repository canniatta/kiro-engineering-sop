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

---

## 3. Zustand Store Generator

**Kapan digunakan:** Membuat store global Zustand baru menggunakan standardisasi TypeScript dan slices pattern.

```text
Create a Zustand store using TypeScript for [Store Name] (e.g., Auth, Cart).
The store should:
1. Use the slices pattern if there are multiple sub-domains of state.
2. Implement persist middleware if the state needs to be saved in localStorage.
3. Enforce strict type safety for state and action methods.
4. Avoid storing server state that should instead be managed by TanStack Query.
```

---

## 4. Axios API Client dengan JWT Refresh Interceptors

**Kapan digunakan:** Membuat instance Axios API client terkonfigurasi dengan penanganan request/response interceptors untuk JWT silent refresh token flow.

```text
Generate a configured Axios instance in TypeScript for ReactJS.
The client should:
1. Set baseURL using environment variables.
2. Include a request interceptor to attach JWT access token from Zustand store.
3. Include a response interceptor to catch 401 errors, call the refresh token endpoint, set the new tokens, and retry the original failed requests.
4. Properly propagate errors and clean up resources on failure.
```

---

## 5. IndexedDB Helper Generator (using idb library)

**Kapan digunakan:** Membuat database local persist menggunakan IndexedDB dan library `idb` untuk data offline.

```text
Create a helper module in TypeScript using the `idb` library to manage local storage in IndexedDB for [Store Name] (e.g., OfflineProducts).
The code should:
1. Initialize the database schema and object stores with indexes.
2. Implement async CRUD functions (get, getAll, put, delete) with transactions.
3. Wrap the database operations in a React custom hook for state synchronization.
```

---

## 6. Vitest + React Testing Library UI Test Generator

**Kapan digunakan:** Membuat berkas unit test fungsional komponen React menggunakan Vitest dan React Testing Library (RTL).

```text
Write a unit test file using Vitest and React Testing Library for component [Component Name].
The test should:
1. Follow the AAA (Arrange, Act, Assert) pattern.
2. Mock external hooks, router contexts, and API calls (using MSW if applicable).
3. Test user interactions (typing, clicking) using `@testing-library/user-event`.
4. Verify assertions for loading states, success states, and error handling.
```

---

## 7. Playwright E2E Test Generator

**Kapan digunakan:** Membuat skenario pengujian End-to-End (E2E) antarmuka pengguna menggunakan Playwright dan Page Object Model (POM).

```text
Write a Playwright E2E test in TypeScript for user flow [Flow Name] (e.g., checkout flow).
Please generate:
1. A Page Object Model class representing the pages involved.
2. A test spec file containing assertion checks for the user flow.
3. Best practices like element locating using role/text locators and waiting for network idle.
```

---

## 8. React Router DOM v6 Navigation Setup

**Kapan digunakan:** Membuat konfigurasi routing modern di React menggunakan React Router DOM v6 dengan lazy loading.

```text
Create a router configuration file using React Router DOM v6 `createBrowserRouter`.
The router should:
1. Configure public routes and layout wrappers.
2. Configure lazy-loaded components with `Suspense` and fallback loaders.
3. Configure a `ProtectedRoute` wrapper component to redirect unauthenticated users to `/login`.
4. Define nested route hierarchies and custom error boundary elements.
```
