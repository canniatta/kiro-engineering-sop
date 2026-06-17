# API Design and Security Review Checklist

Dokumen ini mendefinisikan standar API untuk RESTful endpoints yang dibangun menggunakan **.NET 8 Web API** dan diintegrasikan dengan **ReactJS Frontend Client**.

---

## 1. RESTful API Design Standards

### 1.1 HTTP Method & URL Conventions
* [ ] **Plural Nouns for Resources:** Gunakan kata benda jamak untuk mengidentifikasi resource.
  * *Bad:* `/api/order` atau `/api/getOrders`
  * *Good:* `/api/orders`
* [ ] **Proper HTTP Method Mapping:**
  * `GET` - Mendapatkan detail data (Idempotent, tidak memodifikasi state).
  * `POST` - Membuat data baru (Non-idempotent).
  * `PUT` - Memperbarui seluruh isi data/resource (Idempotent).
  * `PATCH` - Memperbarui sebagian kecil isi data/resource (Non-idempotent).
  * `DELETE` - Menghapus data/resource (Idempotent).
* [ ] **API Versioning:** Letakkan versi API pada URL segment.
  * `/api/v1/orders`

### 1.2 HTTP Status Code Standards
Gunakan status code yang sesuai secara konsisten untuk response REST API:

| Status Code | Deskripsi | Kapan Digunakan |
|-------------|-----------|-----------------|
| `200 OK` | Success | Operasi GET atau PUT/PATCH yang sukses mengembalikan data. |
| `210 Created` | Success Created | Operasi POST sukses membuat resource baru. |
| `204 No Content` | Success Empty | Operasi DELETE sukses, tidak ada data yang perlu dikembalikan. |
| `400 Bad Request` | Client Error | Validasi input gagal (FluentValidation invalid). |
| `401 Unauthorized` | Auth Error | Token JWT tidak valid, kedaluwarsa, atau tidak terkirim. |
| `403 Forbidden` | Perms Error | User terautentikasi tetapi tidak memiliki Role/Policy yang sesuai. |
| `404 Not Found` | Resource Missing | ID data yang dicari tidak ditemukan di database. |
| `500 Internal Error`| Server Error | Terjadi unhandled exception pada sisi code C#. |

---

## 2. API Security Checklist (OWASP Top 10 Aligned)

* [ ] **Authentication Token Verification:** Selalu pastikan token JWT diverifikasi, valid, dan masa aktifnya terbatas.
* [ ] **Authorization Enforcement (RBAC/PBAC):** Letakkan attribute `[Authorize]` di tingkat Controller atau Endpoint Minimal API. Hindari controller terbuka tanpa auth kecuali login/register.
* [ ] **Input Validation (Prevention of Injection & XSS):**
  * Gunakan FluentValidation di .NET 8 untuk memvalidasi panjang input, format email, dan karakter berbahaya.
  * Selalu gunakan model parameterized query (EF Core/Dapper) untuk mencegah SQL injection.
* [ ] **CORS Configuration:** Batasi Allowed Origins hanya ke URL domain ReactJS frontend Anda, dilarang menggunakan wildcard `*` di Production.
* [ ] **Rate Limiting:** Terapkan pembatasan request menggunakan middleware bawaan .NET 8 (`Microsoft.AspNetCore.RateLimiting`) pada endpoint publik.

---

## 3. Contoh Implementasi C# .NET 8 API

Berikut adalah struktur standard untuk Controller, DTO, dan FluentValidation yang aman dan clean:

### 3.1 DTO Definition (.NET 8 Primary Constructor)
```csharp
namespace App.Application.Dtos;

public record CreateOrderDto(
    Guid UserId,
    string CustomerEmail,
    List<OrderItemDto> Items
);

public record OrderItemDto(
    Guid ProductId,
    int Quantity
);
```

### 3.2 FluentValidation Schema
```csharp
using FluentValidation;
using App.Application.Dtos;

namespace App.Application.Validators;

public class CreateOrderDtoValidator : AbstractValidator<CreateOrderDto>
{
    public CreateOrderDtoValidator()
    {
        RuleFor(x => x.UserId)
            .NotEmpty().WithMessage("UserId is required.");

        RuleFor(x => x.CustomerEmail)
            .NotEmpty().WithMessage("Email address is required.")
            .EmailAddress().WithMessage("A valid email address is required.");

        RuleFor(x => x.Items)
            .NotEmpty().WithMessage("Order must contain at least one item.");

        RuleForEach(x => x.Items).ChildRules(item =>
        {
            item.RuleFor(i => i.ProductId).NotEmpty();
            item.RuleFor(i => i.Quantity).GreaterThan(0).WithMessage("Quantity must be greater than zero.");
        });
    }
}
```

### 3.3 Controller Implementation
```csharp
using Microsoft.AspNetCore.Mvc;
using App.Application.Dtos;
using App.Application.Validators;
using FluentValidation.Results;

namespace App.Presentation.Controllers;

[ApiController]
[Route("api/v1/[controller]")]
[Authorize] // Protected by default
public class OrdersController(ILogger<OrdersController> logger) : ControllerBase
{
    [HttpPost]
    [ProducesResponseType(StatusCodes.Status210Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> CreateOrder([FromBody] CreateOrderDto dto)
    {
        var validator = new CreateOrderDtoValidator();
        ValidationResult validationResult = await validator.ValidateAsync(dto);

        if (!validationResult.IsValid)
        {
            // RFC 7807 Problem Details Response
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Validation Error",
                Detail = string.Join("; ", validationResult.Errors.Select(e => e.ErrorMessage))
            });
        }

        logger.LogInformation("Creating order for user {UserId}", dto.UserId);
        
        // Simpan order di DB via Application layer (misal MediatR / Service)
        var newOrderId = Guid.NewGuid();

        return CreatedAtAction(nameof(GetOrderById), new { id = newOrderId }, new { OrderId = newOrderId });
    }

    [HttpGet("{id:guid}")]
    public IActionResult GetOrderById(Guid id)
    {
        // Mock get
        return Ok(new { Id = id, Status = "Pending" });
    }
}
```
