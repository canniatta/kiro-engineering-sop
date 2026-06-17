---
inclusion: fileMatch
fileMatchPattern: "*{Kafka,kafka,Messaging,messaging,Publisher,Consumer}*"
---

# Standar Integrasi Apache Kafka

> [!NOTE]
> **Source of Truth**
>
> - Playbook Kafka: #[[file:docs/27-playbook-kafka-message-broker.md]]
> - Clean Architecture: #[[file:docs/12-template-clean-architecture-dotnet8.md]]

## Struktur Integrasi di Clean Architecture

Pastikan Anda mematuhi batas arsitektural (*architectural boundaries*) saat membuat atau memodifikasi kode Kafka:

| Layer | Tanggung Jawab | File Terkait |
|---|---|---|
| **Core Application** | Abstraksi interface produser, DTO event, dan CQRS Command/Handler | `IEventPublisher.cs`, `OrderCreatedEventDto.cs` |
| **Infrastructure** | Implementasi produser konkret (`Confluent.Kafka`), background service konsumen | `KafkaProducer.cs`, `KafkaConsumerBackgroundService.cs`, `KafkaSettings.cs` |
| **Presentation** | Memicu publikasi event secara asinkronus via API | `OrdersController.cs` |

---

## Aturan Penulisan Kode Kafka

### 1. Kafka Producer Rules

> [!IMPORTANT]
> Saat menulis Kafka Producer, pastikan menggunakan konfigurasi ketahanan data (*resilience*) tingkat tinggi:

* Gunakan **`Acks.All`** untuk menjamin data tersimpan aman di broker leader dan replica.
* Gunakan serialisasi bertipe data strongly-typed dengan **`System.Text.Json`** untuk payload JSON.
* Kembalikan hasil pengiriman asinkronus dengan meneruskan `CancellationToken` dari pemanggil API.

```csharp
// GOOD
public async Task PublishAsync<T>(string topic, T @event, CancellationToken cancellationToken)
{
    var payload = JsonSerializer.Serialize(@event);
    var message = new Message<string, string> { Key = Guid.NewGuid().ToString(), Value = payload };
    await _producer.ProduceAsync(topic, message, cancellationToken);
}
```

### 2. Kafka Consumer Background Service Rules

> [!WARNING]
> Untuk mencegah kegagalan pemrosesan pesan dan memastikan pengiriman *At-Least-Once*:

* Atur **`EnableAutoCommit = false`** pada konfigurasi konsumen.
* Lakukan commit offset secara manual dengan memanggil **`_consumer.Commit(consumeResult)`** hanya setelah MediatR handler selesai memproses pesan tanpa ada eksepsi (*exception*).
* Gunakan **`CreateScope()`** untuk men-resolve MediatR handler di dalam Background Service (karena Background Service bersifat Singleton, sedangkan dbContext dan handler biasanya Scoped).
* Gunakan polling periodik dengan timeout (misal `TimeSpan.FromMilliseconds(100)`) agar loop mendengarkan sinyal penghentian `stoppingToken` secara responsif.

```csharp
// GOOD
protected override async Task ExecuteAsync(CancellationToken stoppingToken)
{
    _consumer.Subscribe("topic-name");
    while (!stoppingToken.IsCancellationRequested)
    {
        var result = _consumer.Consume(TimeSpan.FromMilliseconds(100));
        if (result == null) continue;

        using (var scope = _serviceProvider.CreateScope())
        {
            var mediator = scope.ServiceProvider.GetRequiredService<IMediator>();
            var command = JsonSerializer.Deserialize<MyCommand>(result.Message.Value);
            await mediator.Send(command, stoppingToken);
        }
        _consumer.Commit(result);
    }
}
```

---

## Penanganan Error & Ketahanan Sistem

> [!CAUTION]
> Hindari pola penanganan error yang memblokir konsumen secara permanen (*poison pill*).

| Masalah | Solusi | Detail Implementasi |
|---|---|---|
| Kesalahan parsing JSON | Tangkap `JsonException` secara eksplisit | Catat log error, abaikan pesan rusak atau kirim ke DLQ, lalu jalankan commit offset agar loop lanjut ke pesan berikutnya. |
| Koneksi Database Putus | Tangkap Exception, jangan commit offset | Jangan panggil `Commit()`, biarkan background service mengulangi (*retry*) pembacaan pesan yang sama setelah jeda singkat. |
| Kegagalan Bisnis Handler | Kirim ke Dead Letter Queue (DLQ) | Jika retries habis, kirim pesan tersebut ke topic `.dead-letter` agar admin dapat menginvestigasi. |
