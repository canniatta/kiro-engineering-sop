# ⚙️ Kiro Setup & Configuration — Panduan Lengkap

> **Dokumen**: 02 - Kiro Setup and Configuration  
> **Versi**: 2.0.0 | **Terakhir Diperbarui**: 17 Juni 2026  
> **Target Pembaca**: Developer, Tech Lead  
> **Estimasi Waktu Setup**: 2-4 jam (fresh install) | 30-60 menit (update)

---

## 📌 Daftar Isi

1. [🔧 Instalasi dan Konfigurasi Kiro Step by Step](#instalasi-dan-konfigurasi-kiro-step-by-step)
2. [📜 Kiro Rules dan Steering Files](#kiro-rules-dan-steering-files)
3. [📁 Struktur Folder Project](#struktur-folder-project)
4. [🧩 VS Code Extension Stack](#vs-code-extension-stack)
5. [📄 Kiro Configuration Files](#kiro-configuration-files)
6. [🔀 Git Configuration](#git-configuration)
7. [💻 Environment Setup (Windows & macOS)](#environment-setup-windows--macos)
8. [🐳 Docker Development Environment](#docker-development-environment)
9. [🤝 Team-Wide Configuration Standardization](#team-wide-configuration-standardization)
10. [🎯 Custom Kiro Rules untuk .NET, ReactJS, SQL Server](#custom-kiro-rules-untuk-net-reactjs-sql-server)
11. [🔗 Integration dengan Existing IDE Settings](#integration-dengan-existing-ide-settings)
12. [🔧 Troubleshooting Common Setup Issues](#troubleshooting-common-setup-issues)
13. [⚡ Performance Optimization untuk Kiro](#performance-optimization-untuk-kiro)

---

## Instalasi dan Konfigurasi Kiro Step by Step

### Prerequisites

Sebelum mulai instalasi, pastikan software berikut sudah terinstall:

| Software | Minimum Version | Recommended | Download |
|----------|----------------|-------------|----------|
| Node.js | 18.x LTS | 20.x LTS | https://nodejs.org |
| .NET SDK | 8.0.x | 8.0.x (latest) | https://dot.net |
| SQL Server | 2019 | 2022 | https://www.microsoft.com/sql-server |
| Git | 2.40+ | 2.45+ | https://git-scm.com |
| Docker Desktop | 4.25+ | Latest | https://www.docker.com |
| VS Code | 1.85+ | Latest | https://code.visualstudio.com |

### Step 1: Download dan Install Kiro

> [!IMPORTANT]
> Kiro adalah standalone IDE berbasis VS Code. Download dari https://kiro.dev dan install sesuai platform kamu.

#### macOS

```bash
# Download Kiro dari official website
# Atau via Homebrew (jika tersedia)
brew install --cask kiro

# Verifikasi instalasi
kiro --version
```

#### Windows

```powershell
# Download installer dari https://kiro.dev
# Jalankan KiroSetup.exe
# Ikuti installation wizard
# Pilih: Add to PATH, Register as editor for supported files

# Verifikasi instalasi (PowerShell)
kiro --version
```

### Step 2: Login dan Aktivasi

```
1. Buka Kiro
2. Klik "Sign In" di pojok kiri bawah
3. Login menggunakan AWS account (Builder ID atau IAM Identity Center)
4. Pilih plan: Free tier atau Pro tier
5. Tunggu konfirmasi "Kiro is ready"
```

### Step 3: Initial Configuration

Buka Settings di Kiro (`Cmd+,` / `Ctrl+,`) dan configure:

```json
{
  "kiro.ai.model": "claude-sonnet",
  "kiro.ai.streaming": true,
  "kiro.specs.autoDetect": true,
  "kiro.hooks.enabled": true,
  "kiro.steering.enabled": true,
  "kiro.telemetry.enabled": false,
  "editor.fontSize": 14,
  "editor.tabSize": 4,
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "files.autoSave": "onFocusChange",
  "terminal.integrated.defaultProfile.osx": "zsh",
  "terminal.integrated.defaultProfile.windows": "PowerShell"
}
```

### Step 4: Verifikasi Instalasi

Buat file test untuk memastikan Kiro berfungsi:

```bash
# Buat folder test
mkdir kiro-test && cd kiro-test

# Init project
dotnet new webapi -n TestApi
cd TestApi

# Buka di Kiro
kiro .

# Test: Buka file Program.cs, tekan Ctrl+I (inline chat)
# Ketik: "Add a health check endpoint at /health"
# Jika Kiro merespons dengan code, setup berhasil!
```

### Checklist Verifikasi

- [ ] Kiro terinstall dan bisa dibuka
- [ ] Login berhasil (AWS account)
- [ ] Inline chat berfungsi (Ctrl+I / Cmd+I)
- [ ] Agentic chat panel muncul (sidebar)
- [ ] Spec creation berfungsi
- [ ] File steering terbaca
- [ ] Terminal integrated berfungsi

---

## Kiro Rules dan Steering Files

### Apa Itu Steering Files?

Steering files adalah markdown documents di folder `.kiro/steering/` yang memberikan **persistent context** kepada Kiro tentang project kita. Ini seperti "onboarding document" untuk AI yang memastikan output selalu konsisten dengan standar tim.

### Struktur Folder .kiro/

```
.kiro/
├── steering/                    # Panduan global untuk AI
│   ├── architecture.md          # Arsitektur dan patterns
│   ├── coding-standards.md      # Standar coding
│   ├── tech-stack.md            # Definisi tech stack
│   ├── testing-standards.md     # Standar testing
│   ├── api-conventions.md       # Konvensi API
│   ├── database-conventions.md  # Konvensi database
│   ├── react-conventions.md     # Konvensi React
│   ├── security-guidelines.md   # Panduan security
│   ├── error-handling.md        # Standar error handling
│   └── naming-conventions.md    # Konvensi penamaan
├── specs/                       # Feature specifications
│   └── [feature-name]/
│       ├── design.md            # Design document
│       ├── requirements.md      # Requirements
│       └── tasks.md             # Task breakdown
└── hooks/                       # Automated hooks
    ├── pre-commit.md            # Validasi sebelum commit
    └── post-save.md             # Aksi setelah save
```

### 25 Recommended Kiro Rules dengan Penjelasan

Berikut adalah 25 rules yang direkomendasikan untuk project .NET 8 + ReactJS + SQL Server:

---

#### Rule 1: Architecture Pattern

```markdown
# .kiro/steering/architecture.md

## Architecture Pattern
Project ini menggunakan **Clean Architecture** dengan layer:

1. **Domain** (innermost) — Entities, Value Objects, Domain Events, Interfaces
2. **Application** — Use Cases, DTOs, Validators, Mappings, CQRS Handlers
3. **Infrastructure** — EF Core, External Services, File System, Email
4. **Presentation** — API Controllers/Minimal API Endpoints, Middleware

### Rules:
- Domain layer TIDAK BOLEH depend pada layer luar
- Application layer HANYA depend pada Domain
- Infrastructure implements interfaces dari Domain/Application
- Presentation HANYA memanggil Application layer via MediatR
- Cross-cutting concerns (logging, caching) via behaviors/middleware
```

#### Rule 2: CQRS Pattern

```markdown
## CQRS with MediatR

### Commands
- Naming: `{Action}{Entity}Command` (e.g., `CreateOrderCommand`)
- Handler naming: `{Action}{Entity}CommandHandler`
- Return type: `Result<T>` untuk mutations
- Satu command handler per file

### Queries
- Naming: `Get{Entity/Entities}Query` (e.g., `GetOrdersQuery`)
- Handler naming: `Get{Entity/Entities}QueryHandler`
- Return type: `Result<T>` atau `Result<PaginatedList<T>>`
- Queries TIDAK BOLEH modify state
```

#### Rule 3: API Response Standard

```markdown
## API Response Wrapper

Semua API endpoint HARUS return `ApiResponse<T>`:

```csharp
public class ApiResponse<T>
{
    public bool Success { get; set; }
    public string Message { get; set; }
    public T? Data { get; set; }
    public List<string>? Errors { get; set; }
    public Dictionary<string, object>? Metadata { get; set; }
}
```

### Usage:
- Success: `ApiResponse<T>.Ok(data, "Operation successful")`
- Fail: `ApiResponse<T>.Fail("Validation failed", errors)`
- NotFound: `ApiResponse<T>.NotFound("Resource not found")`
```

#### Rule 4: Entity Base Class

```markdown
## Entity Conventions

Semua entities HARUS inherit dari `BaseEntity`:

```csharp
public abstract class BaseEntity
{
    public Guid Id { get; set; }
    public DateTime CreatedAt { get; set; }
    public string CreatedBy { get; set; }
    public DateTime? UpdatedAt { get; set; }
    public string? UpdatedBy { get; set; }
    public bool IsDeleted { get; set; }
}
```

### Rules:
- Soft delete by default (set `IsDeleted = true`)
- Audit fields (`CreatedAt`, `CreatedBy`, dll) di-set via `SaveChangesInterceptor`
- Id menggunakan `Guid`, BUKAN auto-increment integer
- Entity TIDAK BOLEH punya logic di constructor selain property init
```

#### Rule 5: Validation Standard

```markdown
## Validation dengan FluentValidation

### Rules:
- Setiap Request DTO HARUS punya Validator class
- Naming: `{RequestName}Validator`
- Register via `AddValidatorsFromAssemblyContaining<>`
- Validation dijalankan via MediatR Pipeline Behavior
- Custom error messages HARUS dalam bahasa yang sesuai (configurable)

### Contoh:
```csharp
public class CreateOrderCommandValidator : AbstractValidator<CreateOrderCommand>
{
    public CreateOrderCommandValidator()
    {
        RuleFor(x => x.CustomerId)
            .NotEmpty().WithMessage("Customer ID is required");
        
        RuleFor(x => x.Items)
            .NotEmpty().WithMessage("Order must have at least one item")
            .Must(items => items.Count <= 100)
            .WithMessage("Order cannot have more than 100 items");
        
        RuleForEach(x => x.Items).ChildRules(item =>
        {
            item.RuleFor(i => i.Quantity)
                .GreaterThan(0).WithMessage("Quantity must be positive");
            item.RuleFor(i => i.ProductId)
                .NotEmpty().WithMessage("Product ID is required");
        });
    }
}
```
```

#### Rule 6: EF Core Configuration

```markdown
## Entity Framework Core Conventions

### Rules:
- Gunakan Fluent API untuk configuration, BUKAN Data Annotations
- Satu configuration file per entity: `{EntityName}Configuration.cs`
- Implement `IEntityTypeConfiguration<T>`
- String properties HARUS punya `MaxLength`
- Decimal properties HARUS punya `Precision`
- Navigation properties HARUS di-configure explicit
- Global query filter untuk soft delete: `.HasQueryFilter(e => !e.IsDeleted)`

### Migration Naming:
- Format: `YYYYMMDDHHMMSS_{DescriptiveAction}`
- Contoh: `20260617120000_AddOrderTable`
- JANGAN edit migration yang sudah di-apply ke shared environment
```

#### Rule 7: Exception Handling

```markdown
## Global Exception Handling

### Rules:
- Gunakan `IExceptionHandler` (.NET 8) untuk global exception handling
- Custom exceptions harus inherit dari `BaseException`
- JANGAN throw generic `Exception` — gunakan specific types
- Log semua exceptions dengan correlation ID

### Exception Hierarchy:
```csharp
BaseException
├── NotFoundException          // 404
├── ValidationException        // 400
├── UnauthorizedException      // 401
├── ForbiddenException         // 403
├── ConflictException          // 409
├── BusinessRuleException      // 422
└── ExternalServiceException   // 502
```
```

#### Rule 8: Logging Standards

```markdown
## Logging dengan Serilog

### Rules:
- Inject `ILogger<T>`, BUKAN `ILogger`
- Gunakan structured logging (BUKAN string concatenation)
- JANGAN log sensitive data (passwords, tokens, PII)
- Setiap request HARUS punya CorrelationId

### Log Levels:
| Level | Usage |
|-------|-------|
| Trace | Detailed debugging, disabled in production |
| Debug | Development troubleshooting |
| Information | Business events (order created, user logged in) |
| Warning | Recoverable errors, degraded performance |
| Error | Unrecoverable errors with exception |
| Fatal | Application crash |

### Contoh:
```csharp
// ✅ BAIK: Structured logging
_logger.LogInformation("Order {OrderId} created for Customer {CustomerId} with {ItemCount} items",
    order.Id, order.CustomerId, order.Items.Count);

// ❌ BURUK: String concatenation
_logger.LogInformation($"Order {order.Id} created for customer {order.CustomerId}");
```
```

#### Rule 9: React Component Standards

```markdown
## React Component Conventions

### Rules:
- Functional components ONLY (no class components)
- TypeScript strict mode
- Props interface naming: `{ComponentName}Props`
- One component per file (with associated types)
- Use React.FC sparingly — prefer explicit return types
- Custom hooks prefix: `use{HookName}`

### File Structure per Component:
```
ComponentName/
├── ComponentName.tsx           # Main component
├── ComponentName.test.tsx      # Unit tests
├── ComponentName.module.css    # CSS Modules (or styled-components)
├── ComponentName.types.ts      # Types/interfaces (if complex)
└── index.ts                    # Barrel export
```

### State Management:
- Local state: `useState`, `useReducer`
- Server state: TanStack Query (React Query)
- Global client state: Zustand (BUKAN Redux kecuali ada alasan kuat)
- Form state: React Hook Form + Zod
```

#### Rule 10: API Endpoint Naming

```markdown
## REST API Naming Conventions

### URL Pattern:
```
/api/v{version}/{resource}
/api/v{version}/{resource}/{id}
/api/v{version}/{resource}/{id}/{sub-resource}
```

### HTTP Methods:
| Method | Usage | Response Code |
|--------|-------|---------------|
| GET | Retrieve resource(s) | 200 |
| POST | Create resource | 201 |
| PUT | Full update | 200 |
| PATCH | Partial update | 200 |
| DELETE | Remove resource (soft delete) | 204 |

### Naming Rules:
- Resources: plural nouns (`/api/v1/orders`, bukan `/api/v1/order`)
- Lowercase with hyphens (`/api/v1/order-items`, bukan `/api/v1/orderItems`)
- Query params untuk filtering: `?status=active&sort=createdAt:desc`
- Pagination: `?page=1&pageSize=20`
```

#### Rule 11: SQL Server Query Standards

```markdown
## SQL Server Conventions

### Query Rules:
- SELALU gunakan parameterized queries (prevent SQL injection)
- Gunakan `NOLOCK` hint HANYA untuk reporting queries yang toleran stale data
- Index semua foreign keys
- Gunakan `INCLUDE` pada indexes untuk covering queries
- Avoid `SELECT *` — SELALU specify columns

### Stored Procedure Naming:
- Format: `usp_{Module}_{Action}{Entity}`
- Contoh: `usp_Order_GetByCustomerId`

### Table Naming:
- PascalCase, singular: `Order`, `OrderItem`, `Customer`
- Junction tables: `{Entity1}{Entity2}`: `ProductCategory`
```

#### Rule 12: Authentication & Authorization

```markdown
## Auth Standards

### JWT Configuration:
- Access token expiry: 15 minutes
- Refresh token expiry: 7 days
- Token storage: httpOnly, Secure, SameSite=Strict cookies
- JANGAN simpan JWT di localStorage

### Authorization Pattern:
- Use Policy-based authorization
- Custom `IAuthorizationHandler` untuk complex logic
- Role hierarchy: SuperAdmin > Admin > Manager > User > Viewer

### API Protection:
- Semua endpoints `[Authorize]` by default
- Anonymous endpoints explicitly marked `[AllowAnonymous]`
- Rate limiting per user/IP
```

#### Rule 13: Testing Standards

```markdown
## Testing Conventions

### Unit Testing (.NET):
- Framework: xUnit
- Mocking: Moq
- Assertions: FluentAssertions
- Naming: `{MethodName}_Should{ExpectedBehavior}_When{Condition}`
- AAA pattern: Arrange, Act, Assert
- Minimum coverage: 80%

### Unit Testing (React):
- Framework: Vitest
- Component testing: React Testing Library
- Naming: `should {expected behavior} when {condition}`
- Test user behavior, NOT implementation details

### Integration Testing:
- Use `WebApplicationFactory<Program>` 
- Test database: SQL Server in Docker (TestContainers)
- Seed data via custom `IDataSeeder`
```

#### Rule 14: DTO and Mapping

```markdown
## DTO Conventions

### Naming:
- Request: `{Action}{Entity}Request` (e.g., `CreateOrderRequest`)
- Response: `{Entity}Response` (e.g., `OrderResponse`)
- Command: `{Action}{Entity}Command`
- Query: `Get{Entity/Entities}Query`

### Mapping:
- Use Mapster (atau AutoMapper) untuk entity-to-DTO mapping
- JANGAN expose entity langsung via API
- Config mapping di `MappingConfig` class per module

### Rules:
- DTOs TIDAK BOLEH punya business logic
- DTOs HARUS immutable (use record types when possible)
- Nested DTOs diperbolehkan tapi max 2 level deep
```

#### Rule 15: Error Message Standards

```markdown
## Error Messages

### Rules:
- Error messages HARUS user-friendly (bisa ditampilkan ke end-user)
- Technical details di log, BUKAN di response
- Include error code untuk frontend error handling
- Localization ready (resource files)

### Format:
```json
{
  "success": false,
  "message": "Unable to create order",
  "errors": [
    {
      "code": "ORD-001",
      "field": "customerId",
      "message": "Customer not found"
    }
  ]
}
```
```

#### Rule 16: Dependency Injection

```markdown
## DI Registration Conventions

### Rules:
- Register services di layer-specific extension methods
- Naming: `Add{LayerName}Services()` 
- Lifetime guidelines:
  | Type | Lifetime | Example |
  |------|----------|---------|
  | Stateless services | Scoped | Application services |
  | Factories | Transient | Service factories |
  | Configuration | Singleton | Cache, config objects |
  | DbContext | Scoped | EF Core context |

### Contoh:
```csharp
// Infrastructure/DependencyInjection.cs
public static IServiceCollection AddInfrastructureServices(
    this IServiceCollection services, 
    IConfiguration configuration)
{
    services.AddDbContext<AppDbContext>(options =>
        options.UseSqlServer(configuration.GetConnectionString("Default")));
    
    services.AddScoped<IUnitOfWork, UnitOfWork>();
    services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
    
    return services;
}
```
```

#### Rule 17: Git Commit Messages

```markdown
## Git Commit Convention

### Format: Conventional Commits
```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### Types:
| Type | Description |
|------|-------------|
| feat | New feature |
| fix | Bug fix |
| refactor | Code refactoring (no feature change) |
| test | Adding/updating tests |
| docs | Documentation changes |
| chore | Build, CI, tooling changes |
| perf | Performance improvement |
| style | Code style (formatting, semicolons) |

### Contoh:
```
feat(orders): add bulk order creation endpoint

- Implement POST /api/v1/orders/bulk
- Support up to 100 orders per request
- Add validation for duplicate PO numbers

Closes #1234
```
```

#### Rule 18: React Hook Conventions

```markdown
## React Hook Standards

### Custom Hooks:
- Prefix: `use`
- Location: `src/hooks/` (shared) atau `src/features/{feature}/hooks/`
- HARUS punya return type yang explicit
- HARUS punya unit test

### API Hooks (TanStack Query):
- Query hook: `use{Entity}Query`, `use{Entities}Query`
- Mutation hook: `use{Action}{Entity}Mutation`
- Query keys: centralized di `src/lib/queryKeys.ts`

### Contoh:
```typescript
// hooks/useOrdersQuery.ts
export function useOrdersQuery(params: OrderQueryParams) {
  return useQuery({
    queryKey: queryKeys.orders.list(params),
    queryFn: () => orderApi.getOrders(params),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
}

// hooks/useCreateOrderMutation.ts
export function useCreateOrderMutation() {
  const queryClient = useQueryClient();
  return useMutation({
    mutationFn: orderApi.createOrder,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.orders.all });
      toast.success('Order created successfully');
    },
  });
}
```
```

#### Rule 19: Performance Guidelines

```markdown
## Performance Standards

### Backend:
- API response time target: < 200ms (p95)
- Database query time: < 100ms (p95)
- Use pagination untuk list endpoints (max 100 items per page)
- Implement response caching untuk data yang jarang berubah
- Use `AsNoTracking()` untuk read-only queries

### Frontend:
- Largest Contentful Paint (LCP): < 2.5s
- Interaction to Next Paint (INP): < 200ms
- Bundle size budget: < 250KB (initial JS, gzipped)
- Lazy load routes dan heavy components
- Use `React.memo` dan `useMemo` secara strategis (bukan untuk semua)

### Database:
- Query plan review untuk queries yang dijalankan > 100x/hari
- Index maintenance schedule (weekly)
- Statistics update (daily)
```

#### Rule 20: Frontend State Management

```markdown
## State Management Decision Matrix

| State Type | Tool | Contoh |
|-----------|------|--------|
| Server state | TanStack Query | API data, cached responses |
| Client global | Zustand | Theme, user preferences, auth state |
| Client local | useState/useReducer | Form state, UI toggles |
| URL state | React Router (searchParams) | Filters, pagination, tabs |
| Form state | React Hook Form + Zod | Form data, validation |

### Rules:
- JANGAN duplicate server state ke client global state
- JANGAN gunakan context untuk frequently-updated state
- Zustand store max 1 store per feature domain
- Selalu define TypeScript types untuk state
```

#### Rule 21: API Versioning

```markdown
## API Versioning Strategy

### Rules:
- Versioning via URL path: `/api/v1/`, `/api/v2/`
- Minor changes (additive): TIDAK perlu version bump
- Breaking changes: WAJIB version bump
- Support N-1 version (current + satu versi sebelumnya)
- Deprecation notice minimal 3 bulan sebelum sunset

### Breaking Changes (perlu version bump):
- Remove endpoint
- Remove field dari response
- Change field type
- Change required/optional status
- Change validation rules yang lebih restrictive
```

#### Rule 22: Caching Strategy

```markdown
## Caching Conventions

### Layers:
1. **Response caching**: `[ResponseCache]` untuk GET endpoints
2. **In-memory caching**: `IMemoryCache` untuk frequently-accessed data
3. **Distributed caching**: Redis untuk multi-instance deployment
4. **Client caching**: TanStack Query staleTime/cacheTime

### Cache Key Naming:
- Format: `{entity}:{identifier}:{variant}`
- Contoh: `user:550e8400:profile`, `orders:page:1:size:20`

### Invalidation:
- Event-driven: invalidate saat data berubah
- Time-based: TTL sesuai data sensitivity
- JANGAN cache: authentication data, real-time data, PII
```

#### Rule 23: File Upload Standards

```markdown
## File Upload Conventions

### Rules:
- Max file size: 10MB (configurable per endpoint)
- Allowed types: whitelist (bukan blacklist)
- Storage: Azure Blob / AWS S3 (BUKAN filesystem lokal di production)
- Generate unique filename: `{guid}_{timestamp}.{ext}`
- Scan for malware sebelum accept (production)
- Return file URL/path, bukan file content dalam response

### Validation:
```csharp
// Validate by content, not just extension
var allowedSignatures = new Dictionary<string, byte[]>
{
    { ".pdf", new byte[] { 0x25, 0x50, 0x44, 0x46 } },
    { ".png", new byte[] { 0x89, 0x50, 0x4E, 0x47 } },
    { ".jpg", new byte[] { 0xFF, 0xD8, 0xFF } },
};
```
```

#### Rule 24: Background Job Standards

```markdown
## Background Job Conventions

### Framework: Hangfire atau Quartz.NET

### Job Naming:
- Class: `{Action}{Entity}Job`
- Contoh: `SendOrderConfirmationEmailJob`, `CleanupExpiredTokensJob`

### Rules:
- Jobs HARUS idempotent
- Jobs HARUS punya retry policy
- Jobs HARUS log start dan completion
- Long-running jobs HARUS support cancellation
- JANGAN inject scoped services langsung — buat scope dalam job

### Retry Policy:
| Attempt | Delay |
|---------|-------|
| 1 | 30 seconds |
| 2 | 2 minutes |
| 3 | 10 minutes |
| 4 | 1 hour |
| 5 (final) | 6 hours |
```

#### Rule 25: Code Documentation

```markdown
## Code Documentation Standards

### XML Documentation (.NET):
- WAJIB untuk semua public classes, methods, dan properties
- WAJIB untuk semua API endpoints
- Use `<summary>`, `<param>`, `<returns>`, `<exception>`

### JSDoc (React):
- WAJIB untuk exported functions dan components
- WAJIB untuk custom hooks
- Include `@param`, `@returns`, `@example`

### Rules:
- Document WHY, not WHAT (code should be self-explanatory for WHAT)
- Update docs saat code berubah
- Gunakan Kiro untuk generate initial documentation
```

---

## Struktur Folder Project

### Opsi A: Monorepo (Recommended untuk tim kecil-medium)

```
project-root/
├── .kiro/                          # Kiro configuration
│   ├── steering/                   # Steering files (lihat rules di atas)
│   ├── specs/                      # Feature specifications
│   └── hooks/                      # Automated hooks
│
├── src/
│   ├── backend/                    # .NET 8 Solution
│   │   ├── ProjectName.sln
│   │   ├── src/
│   │   │   ├── ProjectName.Domain/
│   │   │   │   ├── Entities/
│   │   │   │   ├── ValueObjects/
│   │   │   │   ├── Enums/
│   │   │   │   ├── Events/
│   │   │   │   ├── Exceptions/
│   │   │   │   └── Interfaces/
│   │   │   │       ├── Repositories/
│   │   │   │       └── Services/
│   │   │   │
│   │   │   ├── ProjectName.Application/
│   │   │   │   ├── Common/
│   │   │   │   │   ├── Behaviors/
│   │   │   │   │   ├── Mappings/
│   │   │   │   │   ├── Models/
│   │   │   │   │   └── Interfaces/
│   │   │   │   ├── Features/
│   │   │   │   │   ├── Orders/
│   │   │   │   │   │   ├── Commands/
│   │   │   │   │   │   │   ├── CreateOrder/
│   │   │   │   │   │   │   │   ├── CreateOrderCommand.cs
│   │   │   │   │   │   │   │   ├── CreateOrderCommandHandler.cs
│   │   │   │   │   │   │   │   └── CreateOrderCommandValidator.cs
│   │   │   │   │   │   │   └── UpdateOrder/
│   │   │   │   │   │   ├── Queries/
│   │   │   │   │   │   │   ├── GetOrders/
│   │   │   │   │   │   │   └── GetOrderById/
│   │   │   │   │   │   └── DTOs/
│   │   │   │   │   └── Products/
│   │   │   │   └── DependencyInjection.cs
│   │   │   │
│   │   │   ├── ProjectName.Infrastructure/
│   │   │   │   ├── Data/
│   │   │   │   │   ├── AppDbContext.cs
│   │   │   │   │   ├── Configurations/
│   │   │   │   │   ├── Migrations/
│   │   │   │   │   ├── Interceptors/
│   │   │   │   │   └── Seeders/
│   │   │   │   ├── Repositories/
│   │   │   │   ├── Services/
│   │   │   │   └── DependencyInjection.cs
│   │   │   │
│   │   │   └── ProjectName.Api/
│   │   │       ├── Controllers/           # atau Endpoints/ untuk minimal API
│   │   │       ├── Middleware/
│   │   │       ├── Filters/
│   │   │       ├── Extensions/
│   │   │       ├── Program.cs
│   │   │       └── appsettings.json
│   │   │
│   │   └── tests/
│   │       ├── ProjectName.UnitTests/
│   │       ├── ProjectName.IntegrationTests/
│   │       └── ProjectName.ArchTests/
│   │
│   └── frontend/                   # ReactJS Application
│       ├── package.json
│       ├── tsconfig.json
│       ├── vite.config.ts
│       ├── tailwind.config.ts
│       ├── public/
│       ├── src/
│       │   ├── app/
│       │   │   ├── App.tsx
│       │   │   ├── router.tsx
│       │   │   └── providers.tsx
│       │   ├── components/
│       │   │   ├── ui/              # Shared UI components (buttons, inputs)
│       │   │   ├── layout/          # Layout components (header, sidebar)
│       │   │   └── common/          # Shared business components
│       │   ├── features/
│       │   │   ├── orders/
│       │   │   │   ├── components/
│       │   │   │   ├── hooks/
│       │   │   │   ├── api/
│       │   │   │   ├── types/
│       │   │   │   └── utils/
│       │   │   └── products/
│       │   ├── hooks/               # Shared hooks
│       │   ├── lib/                 # Utilities, helpers, configs
│       │   │   ├── api-client.ts
│       │   │   ├── query-keys.ts
│       │   │   └── utils.ts
│       │   ├── styles/
│       │   │   └── globals.css
│       │   ├── types/               # Shared types
│       │   └── main.tsx
│       └── tests/
│           ├── setup.ts
│           └── __mocks__/
│
├── database/                       # SQL Server scripts
│   ├── migrations/                 # EF Core migrations (generated)
│   ├── scripts/
│   │   ├── seed-data/              # Seed data SQL scripts
│   │   ├── stored-procedures/      # Stored procedures
│   │   ├── views/                  # Database views
│   │   └── functions/              # Database functions
│   └── backups/                    # Backup scripts
│
├── docker/                         # Docker configuration
│   ├── docker-compose.yml          # Development docker-compose
│   ├── docker-compose.prod.yml     # Production docker-compose
│   ├── backend.Dockerfile
│   └── frontend.Dockerfile
│
├── docs/                           # Documentation
│   ├── api/                        # API documentation
│   ├── architecture/               # Architecture decisions
│   └── runbook/                    # Operations runbook
│
├── .github/                        # GitHub Actions
│   └── workflows/
│       ├── ci.yml
│       ├── cd-staging.yml
│       └── cd-production.yml
│
├── .gitignore
├── .editorconfig
├── README.md
└── Makefile                        # atau Taskfile.yml
```

### Opsi B: Multi-Repo (Recommended untuk tim besar / microservices)

```
# Repository 1: Backend API
backend-api/
├── .kiro/
│   └── steering/
│       ├── architecture.md
│       ├── coding-standards.md     # .NET specific
│       └── api-conventions.md
├── src/
│   ├── ProjectName.Domain/
│   ├── ProjectName.Application/
│   ├── ProjectName.Infrastructure/
│   └── ProjectName.Api/
├── tests/
├── docker/
└── .github/workflows/

# Repository 2: Frontend
frontend-app/
├── .kiro/
│   └── steering/
│       ├── react-conventions.md
│       ├── component-standards.md
│       └── state-management.md
├── src/
│   ├── app/
│   ├── components/
│   ├── features/
│   └── lib/
├── tests/
└── .github/workflows/

# Repository 3: Database
database-scripts/
├── .kiro/
│   └── steering/
│       └── database-conventions.md
├── migrations/
├── stored-procedures/
├── scripts/
└── .github/workflows/

# Repository 4: Shared/Common
shared-packages/
├── .kiro/
├── packages/
│   ├── api-contracts/     # Shared DTOs/types (NuGet + npm)
│   ├── common-utils/      # Shared utilities
│   └── design-system/     # Shared UI components
└── .github/workflows/
```

---

## VS Code Extension Stack

> [!NOTE]
> Kiro berbasis VS Code sehingga mendukung sebagian besar VS Code extensions. Berikut extensions yang **WAJIB** dipasang oleh seluruh tim.

### Kategori 1: Core Development (7 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 1 | C# Dev Kit | `ms-dotnettools.csdevkit` | IntelliSense, debugging, project management untuk .NET |
| 2 | C# | `ms-dotnettools.csharp` | Language support, OmniSharp, syntax highlighting |
| 3 | .NET Install Tool | `ms-dotnettools.vscode-dotnet-runtime` | .NET SDK management |
| 4 | ESLint | `dbaeumer.vscode-eslint` | JavaScript/TypeScript linting |
| 5 | Prettier | `esbenp.prettier-vscode` | Code formatting (JS/TS/CSS/JSON/MD) |
| 6 | TypeScript Importer | `pmneo.tsimporter` | Auto-import TypeScript modules |
| 7 | EditorConfig | `editorconfig.editorconfig` | Maintain consistent coding styles |

### Kategori 2: React & Frontend (6 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 8 | ES7+ React/Redux Snippets | `dsznajder.es7-react-js-snippets` | React snippet shortcuts |
| 9 | Tailwind CSS IntelliSense | `bradlc.vscode-tailwindcss` | Autocomplete untuk Tailwind classes |
| 10 | CSS Modules | `clinyong.vscode-css-modules` | CSS Modules autocomplete dan go-to-definition |
| 11 | Auto Rename Tag | `formulahendry.auto-rename-tag` | Auto rename paired HTML/JSX tags |
| 12 | Headwind | `heybourn.headwind` | Sort Tailwind CSS classes |
| 13 | vscode-styled-components | `styled-components.vscode-styled-components` | Syntax highlighting untuk styled-components |

### Kategori 3: Database & SQL (4 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 14 | SQL Server (mssql) | `ms-mssql.mssql` | Connect & query SQL Server |
| 15 | SQL Formatter | `adpyke.vscode-sql-formatter` | Format SQL queries |
| 16 | Database Client | `cweijan.vscode-database-client2` | GUI database management |
| 17 | SQLTools | `mtxr.sqltools` | Database management & query runner |

### Kategori 4: Testing (3 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 18 | .NET Core Test Explorer | `formulahendry.dotnet-test-explorer` | Discover & run .NET tests |
| 19 | Vitest | `vitest.explorer` | Run dan debug Vitest tests |
| 20 | Coverage Gutters | `ryanluker.vscode-coverage-gutters` | Display test coverage inline |

### Kategori 5: Git & Collaboration (4 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 21 | GitLens | `eamodio.gitlens` | Enhanced Git capabilities |
| 22 | Git Graph | `mhutchie.git-graph` | Visual Git history |
| 23 | Conventional Commits | `vivaxy.vscode-conventional-commits` | Conventional commit helper |
| 24 | GitHub Pull Requests | `github.vscode-pull-request-github` | PR management dalam editor |

### Kategori 6: Docker & DevOps (3 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 25 | Docker | `ms-azuretools.vscode-docker` | Docker file support & management |
| 26 | YAML | `redhat.vscode-yaml` | YAML language support (CI/CD files) |
| 27 | Remote - Containers | `ms-vscode-remote.remote-containers` | Dev containers |

### Kategori 7: Documentation & Productivity (6 extensions)

| # | Extension | ID | Fungsi |
|---|-----------|-----|--------|
| 28 | Markdown All in One | `yzhang.markdown-all-in-one` | Markdown editing tools |
| 29 | Mermaid Markdown | `bpruber.mermaid-markdown-syntax-highlighting` | Mermaid diagram syntax |
| 30 | REST Client | `humao.rest-client` | HTTP request testing inline |
| 31 | Todo Tree | `gruntfuggly.todo-tree` | Track TODO/FIXME comments |
| 32 | Error Lens | `usernamehw.errorlens` | Inline error/warning display |
| 33 | indent-rainbow | `oderwat.indent-rainbow` | Colorize indentation levels |

### Batch Install Script

```bash
# Install semua extensions sekaligus (jalankan di terminal Kiro)
# Core Development
kiro --install-extension ms-dotnettools.csdevkit
kiro --install-extension ms-dotnettools.csharp
kiro --install-extension ms-dotnettools.vscode-dotnet-runtime
kiro --install-extension dbaeumer.vscode-eslint
kiro --install-extension esbenp.prettier-vscode
kiro --install-extension pmneo.tsimporter
kiro --install-extension editorconfig.editorconfig

# React & Frontend
kiro --install-extension dsznajder.es7-react-js-snippets
kiro --install-extension bradlc.vscode-tailwindcss
kiro --install-extension clinyong.vscode-css-modules
kiro --install-extension formulahendry.auto-rename-tag
kiro --install-extension heybourn.headwind
kiro --install-extension styled-components.vscode-styled-components

# Database & SQL
kiro --install-extension ms-mssql.mssql
kiro --install-extension adpyke.vscode-sql-formatter
kiro --install-extension cweijan.vscode-database-client2
kiro --install-extension mtxr.sqltools

# Testing
kiro --install-extension formulahendry.dotnet-test-explorer
kiro --install-extension vitest.explorer
kiro --install-extension ryanluker.vscode-coverage-gutters

# Git & Collaboration
kiro --install-extension eamodio.gitlens
kiro --install-extension mhutchie.git-graph
kiro --install-extension vivaxy.vscode-conventional-commits
kiro --install-extension github.vscode-pull-request-github

# Docker & DevOps
kiro --install-extension ms-azuretools.vscode-docker
kiro --install-extension redhat.vscode-yaml
kiro --install-extension ms-vscode-remote.remote-containers

# Documentation & Productivity
kiro --install-extension yzhang.markdown-all-in-one
kiro --install-extension bpruber.mermaid-markdown-syntax-highlighting
kiro --install-extension humao.rest-client
kiro --install-extension gruntfuggly.todo-tree
kiro --install-extension usernamehw.errorlens
kiro --install-extension oderwat.indent-rainbow

echo "✅ All extensions installed!"
```

---

## Kiro Configuration Files

### File: `.kiro/steering/tech-stack.md`

```markdown
# Tech Stack Definition

## Backend
- Runtime: .NET 8 (LTS)
- Language: C# 12
- Web Framework: ASP.NET Core 8 Minimal API
- ORM: Entity Framework Core 8
- CQRS: MediatR 12
- Validation: FluentValidation 11
- Mapping: Mapster 7
- Logging: Serilog
- Authentication: JWT Bearer + ASP.NET Core Identity
- API Documentation: Swagger/OpenAPI (Swashbuckle)
- Background Jobs: Hangfire
- Caching: IMemoryCache + Redis (StackExchange.Redis)
- Health Checks: AspNetCore.Diagnostics.HealthChecks

## Frontend
- Framework: React 18
- Language: TypeScript 5
- Build Tool: Vite 5
- Styling: Tailwind CSS 3
- State Management: Zustand (client) + TanStack Query (server)
- Forms: React Hook Form + Zod
- Routing: React Router 6
- HTTP Client: Axios
- Component Library: shadcn/ui (Radix primitives)
- Testing: Vitest + React Testing Library

## Database
- RDBMS: SQL Server 2022
- ORM Migrations: EF Core Migrations
- Connection: Microsoft.Data.SqlClient

## Infrastructure
- Containerization: Docker + Docker Compose
- CI/CD: GitHub Actions
- Cloud: AWS (or Azure)
- Monitoring: Application Insights / CloudWatch
```

### File: `.editorconfig`

```ini
# EditorConfig - https://editorconfig.org
root = true

[*]
indent_style = space
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.{js,jsx,ts,tsx,json,css,scss,yml,yaml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false

[*.{csproj,props,targets}]
indent_size = 2

[*.cs]
# C# Coding Conventions
dotnet_sort_system_directives_first = true
csharp_new_line_before_open_brace = all
csharp_indent_case_contents = true
csharp_style_var_elsewhere = true:suggestion
csharp_style_expression_bodied_methods = when_on_single_line:suggestion
```

### File: `docker-compose.yml` (Development)

```yaml
version: '3.8'

services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: dev-sqlserver
    environment:
      ACCEPT_EULA: "Y"
      MSSQL_SA_PASSWORD: "YourStr0ngP@ssword!"
      MSSQL_PID: "Developer"
    ports:
      - "1433:1433"
    volumes:
      - sqlserver-data:/var/opt/mssql
    healthcheck:
      test: /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P "YourStr0ngP@ssword!" -C -Q "SELECT 1"
      interval: 10s
      timeout: 3s
      retries: 10

  redis:
    image: redis:7-alpine
    container_name: dev-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data

  seq:
    image: datalust/seq:latest
    container_name: dev-seq
    environment:
      ACCEPT_EULA: "Y"
    ports:
      - "5341:5341"
      - "8081:80"

  mailhog:
    image: mailhog/mailhog:latest
    container_name: dev-mailhog
    ports:
      - "1025:1025"
      - "8025:8025"

volumes:
  sqlserver-data:
  redis-data:
```

---

## Git Configuration

### File: `.gitignore`

```gitignore
# .NET
bin/
obj/
*.user
*.suo
*.cache
*.log
*.vs/
TestResults/
*.DotSettings.user

# Node/React
node_modules/
dist/
build/
.cache/
*.tsbuildinfo

# Environment
.env
.env.local
.env.*.local
appsettings.Development.json
appsettings.Local.json

# IDE
.idea/
.vscode/settings.json
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Docker
docker-compose.override.yml

# Coverage
coverage/
*.lcov
TestResults/

# Kiro (keep steering, ignore personal)
.kiro/personal/
```

### Git Hooks Setup (Husky + lint-staged untuk frontend)

```json
// package.json (frontend)
{
  "scripts": {
    "prepare": "husky install"
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,json,md}": [
      "prettier --write"
    ]
  }
}
```

### Branch Strategy

```mermaid
gitgraph
    commit id: "init"
    branch develop
    checkout develop
    commit id: "setup"
    branch feature/user-auth
    checkout feature/user-auth
    commit id: "feat: add login"
    commit id: "feat: add register"
    checkout develop
    merge feature/user-auth id: "merge: user-auth"
    branch release/1.0
    checkout release/1.0
    commit id: "chore: bump version"
    checkout main
    merge release/1.0 id: "release: v1.0" tag: "v1.0.0"
    checkout develop
    merge release/1.0
```

```
main           → Production-ready code
develop        → Integration branch
feature/*      → Feature development
bugfix/*       → Bug fixes
release/*      → Release preparation
hotfix/*       → Emergency production fixes
```

---

## Environment Setup (Windows & macOS)

### macOS Setup

```bash
# 1. Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Install core tools
brew install git node@20 dotnet-sdk
brew install --cask docker kiro visual-studio-code

# 3. Install SQL Server (via Docker)
docker pull mcr.microsoft.com/mssql/server:2022-latest

# 4. Install Azure Data Studio (SQL GUI)
brew install --cask azure-data-studio

# 5. Install global tools
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-outdated-tool
npm install -g pnpm

# 6. Verify
echo "Node: $(node --version)"
echo ".NET: $(dotnet --version)"
echo "Git: $(git --version)"
echo "Docker: $(docker --version)"
```

### Windows Setup

```powershell
# 1. Install Chocolatey
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# 2. Install core tools
choco install git nodejs-lts dotnet-8.0-sdk docker-desktop -y

# 3. Install Kiro
# Download from https://kiro.dev and run installer

# 4. Install SQL Server Developer Edition
choco install sql-server-2022 -y

# 5. Install SSMS
choco install sql-server-management-studio -y

# 6. Install global tools
dotnet tool install --global dotnet-ef
npm install -g pnpm

# 7. Verify
node --version
dotnet --version
git --version
docker --version
```

### Environment Variables

```bash
# .env.development (backend)
ASPNETCORE_ENVIRONMENT=Development
ConnectionStrings__Default=Server=localhost,1433;Database=ProjectNameDb;User Id=sa;Password=YourStr0ngP@ssword!;TrustServerCertificate=true
JwtSettings__Secret=your-256-bit-secret-key-for-development-only
JwtSettings__Issuer=ProjectName
JwtSettings__Audience=ProjectName
JwtSettings__ExpiryMinutes=15
Redis__ConnectionString=localhost:6379
Seq__ServerUrl=http://localhost:5341
Email__SmtpHost=localhost
Email__SmtpPort=1025
```

```bash
# .env.development (frontend)
VITE_API_BASE_URL=https://localhost:7001/api/v1
VITE_APP_NAME=ProjectName
VITE_APP_VERSION=1.0.0
```

---

## Docker Development Environment

### Quick Start

```bash
# Start semua infrastructure services
docker compose up -d

# Verify semua services running
docker compose ps

# Expected output:
# dev-sqlserver    running    0.0.0.0:1433->1433/tcp
# dev-redis        running    0.0.0.0:6379->6379/tcp
# dev-seq          running    0.0.0.0:5341->5341/tcp, 0.0.0.0:8081->80/tcp
# dev-mailhog      running    0.0.0.0:1025->1025/tcp, 0.0.0.0:8025->8025/tcp
```

### Service Access

| Service | URL/Connection | Fungsi |
|---------|---------------|--------|
| SQL Server | `localhost:1433` | Database |
| Redis | `localhost:6379` | Caching |
| Seq | `http://localhost:8081` | Log viewer |
| MailHog | `http://localhost:8025` | Email testing |
| Backend API | `https://localhost:7001` | .NET API |
| Frontend | `http://localhost:5173` | React app (Vite) |

### Backend Dockerfile (Development)

```dockerfile
# docker/backend.Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS dev

WORKDIR /app

# Copy solution and project files for restore
COPY src/backend/*.sln .
COPY src/backend/src/ProjectName.Domain/*.csproj ./src/ProjectName.Domain/
COPY src/backend/src/ProjectName.Application/*.csproj ./src/ProjectName.Application/
COPY src/backend/src/ProjectName.Infrastructure/*.csproj ./src/ProjectName.Infrastructure/
COPY src/backend/src/ProjectName.Api/*.csproj ./src/ProjectName.Api/

RUN dotnet restore

# Copy everything else
COPY src/backend/ .

# Run with hot reload
ENTRYPOINT ["dotnet", "watch", "run", "--project", "src/ProjectName.Api"]
```

---

## Team-Wide Configuration Standardization

### Shared Configuration Checklist

- [ ] .editorconfig committed ke repo (enforced by CI)
- [ ] .kiro/steering/ folder shared dan versioned
- [ ] ESLint config shared (.eslintrc.cjs)
- [ ] Prettier config shared (.prettierrc)
- [ ] TypeScript config shared (tsconfig.json)
- [ ] Docker compose files standardized
- [ ] Git hooks (Husky) setup documented
- [ ] VS Code extension list (extensions.json)
- [ ] Environment variable template (.env.example)

### File: `.vscode/extensions.json` (Recommended Extensions)

```json
{
  "recommendations": [
    "ms-dotnettools.csdevkit",
    "ms-dotnettools.csharp",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "bradlc.vscode-tailwindcss",
    "ms-mssql.mssql",
    "eamodio.gitlens",
    "ms-azuretools.vscode-docker",
    "yzhang.markdown-all-in-one",
    "vitest.explorer",
    "ryanluker.vscode-coverage-gutters",
    "usernamehw.errorlens"
  ]
}
```

### File: `.vscode/settings.json` (Shared Team Settings)

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "[csharp]": {
    "editor.defaultFormatter": "ms-dotnettools.csharp"
  },
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "explicit"
  },
  "typescript.preferences.importModuleSpecifier": "relative",
  "eslint.validate": ["javascript", "javascriptreact", "typescript", "typescriptreact"],
  "files.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/node_modules": true
  },
  "search.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/node_modules": true,
    "**/dist": true
  }
}
```

---

## Custom Kiro Rules untuk .NET, ReactJS, SQL Server

### .NET Specific Steering File

```markdown
# .kiro/steering/dotnet-rules.md

## .NET 8 Specific Rules

### Minimal API Preferred
- Gunakan Minimal API untuk new endpoints
- Group endpoints menggunakan `MapGroup`
- Use `TypedResults` untuk explicit return types

### Contoh Endpoint Pattern:
```csharp
public static class OrderEndpoints
{
    public static void MapOrderEndpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("/api/v1/orders")
            .WithTags("Orders")
            .RequireAuthorization();

        group.MapGet("/", GetOrders)
            .WithName("GetOrders")
            .Produces<ApiResponse<PaginatedList<OrderResponse>>>();

        group.MapGet("/{id:guid}", GetOrderById)
            .WithName("GetOrderById")
            .Produces<ApiResponse<OrderResponse>>()
            .ProducesProblem(404);

        group.MapPost("/", CreateOrder)
            .WithName("CreateOrder")
            .Produces<ApiResponse<OrderResponse>>(201)
            .ProducesValidationProblem();
    }

    private static async Task<IResult> GetOrders(
        [AsParameters] GetOrdersQuery query,
        ISender sender)
    {
        var result = await sender.Send(query);
        return TypedResults.Ok(ApiResponse<PaginatedList<OrderResponse>>.Ok(result));
    }
}
```

### C# 12 Features to Use:
- Primary constructors untuk DI
- Collection expressions
- `required` modifier untuk DTOs
- Raw string literals untuk multi-line strings
- Pattern matching (switch expressions)

### Contoh Primary Constructor:
```csharp
public class OrderService(
    IRepository<Order> orderRepository,
    IUnitOfWork unitOfWork,
    ILogger<OrderService> logger) : IOrderService
{
    public async Task<Order> GetByIdAsync(Guid id, CancellationToken ct = default)
    {
        logger.LogInformation("Getting order {OrderId}", id);
        return await orderRepository.GetByIdAsync(id, ct)
            ?? throw new NotFoundException(nameof(Order), id);
    }
}
```
```

### ReactJS Specific Steering File

```markdown
# .kiro/steering/react-rules.md

## React Conventions for This Project

### Component Pattern:
```tsx
import { type ComponentProps } from 'react';

interface OrderListProps {
  customerId?: string;
  onOrderSelect: (orderId: string) => void;
}

export function OrderList({ customerId, onOrderSelect }: OrderListProps) {
  const { data, isLoading, error } = useOrdersQuery({ customerId });

  if (isLoading) return <OrderListSkeleton />;
  if (error) return <ErrorDisplay error={error} />;
  if (!data?.items.length) return <EmptyState message="No orders found" />;

  return (
    <div className="space-y-4">
      {data.items.map((order) => (
        <OrderCard
          key={order.id}
          order={order}
          onClick={() => onOrderSelect(order.id)}
        />
      ))}
      <Pagination
        currentPage={data.currentPage}
        totalPages={data.totalPages}
      />
    </div>
  );
}
```

### API Client Pattern:
```typescript
// lib/api-client.ts
import axios from 'axios';

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  withCredentials: true,
  headers: { 'Content-Type': 'application/json' },
});

apiClient.interceptors.response.use(
  (response) => response.data,
  async (error) => {
    if (error.response?.status === 401) {
      // Handle token refresh
    }
    return Promise.reject(error);
  }
);
```

### Import Order (enforced by ESLint):
1. React imports
2. Third-party libraries
3. Internal modules (@/ alias)
4. Relative imports
5. Type imports
6. CSS/style imports
```

### SQL Server Specific Steering File

```markdown
# .kiro/steering/sql-rules.md

## SQL Server Conventions

### EF Core Configuration Pattern:
```csharp
public class OrderConfiguration : IEntityTypeConfiguration<Order>
{
    public void Configure(EntityTypeBuilder<Order> builder)
    {
        builder.ToTable("Orders");
        
        builder.HasKey(o => o.Id);
        
        builder.Property(o => o.OrderNumber)
            .IsRequired()
            .HasMaxLength(20);
        
        builder.Property(o => o.TotalAmount)
            .HasPrecision(18, 2);
        
        builder.HasOne(o => o.Customer)
            .WithMany(c => c.Orders)
            .HasForeignKey(o => o.CustomerId)
            .OnDelete(DeleteBehavior.Restrict);
        
        builder.HasMany(o => o.Items)
            .WithOne(i => i.Order)
            .HasForeignKey(i => i.OrderId)
            .OnDelete(DeleteBehavior.Cascade);
        
        builder.HasQueryFilter(o => !o.IsDeleted);
        
        builder.HasIndex(o => o.OrderNumber).IsUnique();
        builder.HasIndex(o => o.CustomerId);
        builder.HasIndex(o => o.CreatedAt);
    }
}
```

### Query Performance Rules:
- ALWAYS use `.AsNoTracking()` for read-only queries
- Use `.AsSplitQuery()` for queries with multiple collection includes
- Prefer projection (`.Select()`) over loading full entities
- Use `IQueryable` extension methods for reusable filters
```

---

## Integration dengan Existing IDE Settings

### Migrating dari VS Code ke Kiro

```bash
# Kiro otomatis import settings dari VS Code
# Jika tidak, manual copy:

# macOS
cp ~/Library/Application\ Support/Code/User/settings.json \
   ~/Library/Application\ Support/Kiro/User/settings.json

# Windows
copy "%APPDATA%\Code\User\settings.json" "%APPDATA%\Kiro\User\settings.json"
```

### Migrating dari Cursor/Windsurf Rules

```bash
# Dari Cursor (.cursorrules)
# Konversi ke format Kiro steering files:
# 1. Copy content dari .cursorrules
# 2. Buat file di .kiro/steering/coding-standards.md
# 3. Struktur ulang ke format markdown yang proper

# Dari Windsurf (.windsurfrules) — proses yang sama
```

---

## Troubleshooting Common Setup Issues

### Issue 1: Kiro Tidak Mendeteksi .NET SDK

```
Gejala: "No .NET SDK found" atau IntelliSense tidak bekerja
Solusi:
1. Verifikasi: dotnet --list-sdks
2. Pastikan PATH includes dotnet:
   macOS: export PATH="$PATH:/usr/local/share/dotnet"
   Windows: Verify System Environment Variables
3. Restart Kiro setelah install SDK
4. Buka Command Palette > ".NET: Restart Language Server"
```

### Issue 2: SQL Server Container Tidak Start

```
Gejala: "docker compose up" gagal untuk sqlserver
Solusi:
1. Cek Docker Desktop running
2. Cek minimum RAM allocation (2GB+)
3. Cek password complexity (harus memenuhi SQL Server policy)
4. Cek port 1433 tidak dipakai: lsof -i :1433
5. Reset: docker compose down -v && docker compose up -d
```

### Issue 3: Kiro AI Tidak Merespons

```
Gejala: Inline chat atau agentic chat tidak memberikan response
Solusi:
1. Cek internet connection
2. Cek login status (kiri bawah)
3. Re-login: Command Palette > "Kiro: Sign Out" > Sign In ulang
4. Cek apakah ada VPN/proxy yang blocking AWS endpoints
5. Cek Kiro Output panel untuk error messages
```

### Issue 4: Steering Files Tidak Terbaca

```
Gejala: AI output tidak mengikuti steering files
Solusi:
1. Pastikan folder structure: .kiro/steering/*.md
2. Pastikan setting: "kiro.steering.enabled": true
3. Cek file permissions (readable)
4. Restart Kiro
5. Verify: Command Palette > "Kiro: Show Active Steering Files"
```

### Issue 5: EF Core Migrations Gagal

```
Gejala: "dotnet ef migrations add" error
Solusi:
1. Pastikan dotnet-ef tool installed: dotnet tool list -g
2. Pastikan di folder yang benar (solution root)
3. Specify startup project: 
   dotnet ef migrations add MigrationName \
     --project src/ProjectName.Infrastructure \
     --startup-project src/ProjectName.Api
4. Cek connection string di appsettings.Development.json
```

### Issue 6: Frontend Build Errors setelah Clone

```
Gejala: pnpm install gagal atau build errors
Solusi:
1. Hapus node_modules dan lock file:
   rm -rf node_modules pnpm-lock.yaml
2. Clear pnpm cache: pnpm store prune
3. Install ulang: pnpm install
4. Cek Node version matches .nvmrc: nvm use
5. Cek pnpm-workspace.yaml jika menggunakan monorepo
```

### Issue 7: Hot Reload Tidak Bekerja

```
Backend (.NET):
1. Pastikan launch profile menggunakan "dotnet watch"
2. Cek: <Watch Include="**\*.cs" /> di project file

Frontend (Vite):
1. Cek vite.config.ts server configuration
2. Pastikan file watching limit tidak exceeded:
   macOS: ulimit -n 10240
   Linux: echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
```

---

## Performance Optimization untuk Kiro

### Tips untuk Kiro Berjalan Lebih Cepat

| Tip | Detail | Impact |
|-----|--------|--------|
| Exclude folders dari search | `files.exclude` dan `search.exclude` untuk bin, obj, node_modules | 🟢 High |
| Batasi file watchers | Set `files.watcherExclude` | 🟢 High |
| Gunakan steering files yang focused | Jangan terlalu banyak context yang tidak relevan | 🟡 Medium |
| Close unused tabs | Kiro considers open files as context | 🟡 Medium |
| Use `.kiroignore` | Mirip `.gitignore`, exclude files dari AI context | 🟢 High |
| Regular restart | Restart Kiro setiap 4-6 jam untuk clear memory | 🟡 Medium |
| SSD storage | Pastikan project di SSD, bukan HDD | 🟢 High |
| RAM | Minimum 16GB, recommended 32GB | 🟢 High |

### Recommended Settings untuk Performance

```json
{
  "files.watcherExclude": {
    "**/bin/**": true,
    "**/obj/**": true,
    "**/node_modules/**": true,
    "**/.git/objects/**": true,
    "**/dist/**": true,
    "**/coverage/**": true
  },
  "search.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/node_modules": true,
    "**/dist": true,
    "**/coverage": true,
    "**/*.min.js": true,
    "**/*.map": true
  },
  "files.exclude": {
    "**/bin": true,
    "**/obj": true,
    "**/.git": true
  },
  "editor.quickSuggestions": {
    "other": true,
    "comments": false,
    "strings": false
  }
}
```

### File: `.kiroignore`

```
# Files/folders that Kiro should NOT include in AI context
bin/
obj/
node_modules/
dist/
coverage/
*.min.js
*.map
*.lock
package-lock.json
pnpm-lock.yaml
Migrations/
*.Designer.cs
wwwroot/lib/
```

---

> [!TIP]
> Setelah setup selesai, lanjutkan ke **[03 - Prompt Library](./03-prompt-library.md)** untuk mempelajari 120+ prompt yang siap pakai untuk daily development.

---

*Lanjut ke dokumen berikutnya: [03 - Prompt Library](./03-prompt-library.md)*

---

*Dibuat dengan ❤️ oleh Engineering Team — Powered by Kiro AI*  
*Terakhir diperbarui: 17 Juni 2026 | Versi: 2.0.0*
