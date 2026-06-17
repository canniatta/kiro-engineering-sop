---
inclusion: fileMatch
fileMatchPattern: "**/*Controller*.cs"
---

# Standar API Design & Security

> [!NOTE]
> **Source of Truth**
>
> - API Review Checklist: #[[file:10-template-api-review-checklist.md]]
> - Rules 3, 10, 12, 21: #[[file:02-kiro-setup-and-configuration.md]] (section "Rules")

## URL Conventions

| Aspek | Aturan | Contoh |
|---|---|---|
| Resource naming | Plural nouns, lowercase | `/api/v1/orders`, `/api/v1/customers` |
| Hierarchy | Nested resources untuk relasi | `/api/v1/orders/{id}/items` |
| Casing | Lowercase dengan hyphens | `/api/v1/order-items` (bukan camelCase) |
| Versioning | URL segment | `/api/v{version}/{resource}` |
| Verbs | TIDAK di URL — gunakan HTTP method | `/api/v1/orders` bukan `/api/v1/getOrders` |

## HTTP Method Mapping

| Method | Operasi | Idempotent | Response Code (Success) |
|---|---|---|---|
| `GET` | Mendapatkan data | Ya | `200 OK` |
| `POST` | Membuat data baru | Tidak | `201 Created` |
| `PUT` | Update seluruh resource | Ya | `200 OK` |
| `PATCH` | Update sebagian resource | Tidak | `200 OK` |
| `DELETE` | Menghapus resource | Ya | `204 No Content` |

## API Response Wrapper

Semua API endpoint menggunakan format response yang konsisten:

```csharp
public class ApiResponse<T>
{
    public bool Success { get; init; }
    public string? Message { get; init; }
    public T? Data { get; init; }
    public IEnumerable<string>? Errors { get; init; }
}

// Penggunaan:
// Success: ApiResponse<OrderDto>.Success(data, "Order created")
// Failure: ApiResponse<OrderDto>.Fail("Validation failed", errors)
```

## Status Code

| Code | Deskripsi | Kapan Digunakan |
|---|---|---|
| `200 OK` | Success | GET, PUT, PATCH yang sukses mengembalikan data |
| `201 Created` | Resource dibuat | POST sukses — sertakan `Location` header |
| `204 No Content` | Success tanpa body | DELETE sukses |
| `400 Bad Request` | Client error | Input validation gagal (FluentValidation) |
| `401 Unauthorized` | Auth error | Token JWT tidak valid atau tidak terkirim |
| `403 Forbidden` | Permission error | User terautentikasi tapi tidak punya akses |
| `404 Not Found` | Resource missing | ID yang dicari tidak ditemukan |
| `409 Conflict` | State conflict | Duplikat resource, concurrency conflict |
| `422 Unprocessable` | Business rule violation | Request valid secara format tapi melanggar business logic |
| `500 Internal Error` | Server error | Unhandled exception — jangan ekspos detail ke client |

## Security Checklist

> [!IMPORTANT]
> Setiap controller harus memenuhi checklist security berikut.

- [ ] **JWT Verification** — Token valid, tidak expired, signature verified
- [ ] **RBAC/PBAC** — `[Authorize(Policy = "...")]` pada setiap endpoint yang protected
- [ ] **Input Validation** — FluentValidation untuk semua request DTOs
- [ ] **CORS** — Batasi origins ke domain frontend, jangan wildcard `*` di production
- [ ] **Rate Limiting** — Middleware `Microsoft.AspNetCore.RateLimiting` pada endpoint publik
- [ ] **No Sensitive Data in Response** — Jangan return password hash, internal IDs yang sensitif
- [ ] **SQL Injection Prevention** — Parameterized queries via EF Core / Dapper

## API Versioning

| Aspek | Aturan |
|---|---|
| Kapan bump version | Breaking change pada request/response contract |
| Support policy | N-1 (current + previous version) |
| Deprecation notice | Minimal 3 bulan sebelum sunset |
| Header info | `api-deprecated-versions` header pada response |

> [!WARNING]
> Menambah field optional ke response BUKAN breaking change. Menghapus field atau mengubah tipe ADALAH breaking change dan memerlukan versi baru.

## Controller Design Rules

```csharp
[ApiController]
[ApiVersion("1.0")]
[Route("api/v{version:apiVersion}/[controller]")]
[Produces("application/json")]
[Authorize]
public sealed class OrdersController : ControllerBase
{
    private readonly ISender _mediator;

    public OrdersController(ISender mediator)
    {
        _mediator = mediator;
    }

    [HttpPost]
    [ProducesResponseType(typeof(ApiResponse<OrderDto>), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> Create(
        [FromBody] CreateOrderCommand command,
        CancellationToken ct)
    {
        var result = await _mediator.Send(command, ct);
        return CreatedAtAction(nameof(GetById), new { id = result.Id }, result);
    }

    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(ApiResponse<OrderDto>), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetById(
        [FromRoute] Guid id,
        CancellationToken ct)
    {
        var result = await _mediator.Send(new GetOrderByIdQuery(id), ct);
        return result is null ? NotFound() : Ok(result);
    }
}
```

### Aturan Controller

| Aturan | Penjelasan |
|---|---|
| Thin controller | Tidak ada business logic — delegasi ke MediatR handler |
| `[ApiController]` | Enable automatic model validation dan binding source inference |
| `CancellationToken` | Propagasi ke setiap async call untuk graceful cancellation |
| `[ProducesResponseType]` | Dokumentasi Swagger untuk setiap kemungkinan response |
| `sealed` | Controller tidak di-inherit — prefer composition |
| Route template | `api/v{version:apiVersion}/[controller]` — konsisten untuk semua controllers |
