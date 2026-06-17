# Logging & Observability Standard (.NET 8 & ReactJS)

Dokumen ini mendefinisikan standar implementasi logging, tracing, dan metrik (observabilitas) menggunakan **Serilog** dan **OpenTelemetry** pada backend .NET 8 serta monitoring terpadu untuk frontend ReactJS.

---

## 1. Serilog Structured Logging Configuration

### 1.1 `appsettings.json` Configuration
Konfigurasi terpusat Serilog dengan sink Elasticsearch dan File:

```json
{
  "Serilog": {
    "Using": [ "Serilog.Sinks.Console", "Serilog.Sinks.File", "Serilog.Sinks.Elasticsearch" ],
    "MinimumLevel": {
      "Default": "Information",
      "Override": {
        "Microsoft": "Warning",
        "Microsoft.Hosting.Lifetime": "Information",
        "System": "Warning"
      }
    },
    "WriteTo": [
      { "Name": "Console" },
      {
        "Name": "File",
        "Args": {
          "path": "Logs/log-.txt",
          "rollingInterval": "Day",
          "formatter": "Serilog.Formatting.Json.JsonFormatter, Serilog"
        }
      }
    ],
    "Enrich": [ "FromLogContext", "WithMachineName", "WithThreadId" ],
    "Properties": {
      "ApplicationName": "EnterpriseOrderAPI"
    }
  }
}
```

### 1.2 Program.cs Serilog Setup
```csharp
using Serilog;

var builder = WebApplication.CreateBuilder(args);

// Configure Serilog from configuration
Log.Logger = new LoggerConfiguration()
    .ReadFrom.Configuration(builder.Configuration)
    .CreateLogger();

builder.Host.UseSerilog();
```

---

## 2. Correlation ID Middleware (Distributed Tracing)

Untuk melacak siklus hidup request dari API Gateway hingga database query, setiap request harus memiliki `Correlation-Id` yang diteruskan melalui HTTP Header.

```csharp
using Microsoft.AspNetCore.Http;
using System.Threading.Tasks;

namespace App.Presentation.Middleware;

public class CorrelationIdMiddleware(RequestDelegate next)
{
    private const string CorrelationIdHeaderKey = "X-Correlation-ID";

    public async Task InvokeAsync(HttpContext context)
    {
        string? correlationId = null;

        if (context.Request.Headers.TryGetValue(CorrelationIdHeaderKey, out var headerValue))
        {
            correlationId = headerValue;
        }
        else
        {
            correlationId = Guid.NewGuid().ToString();
            context.Request.Headers.Append(CorrelationIdHeaderKey, correlationId);
        }

        // Add correlation ID to Response headers
        context.Response.OnStarting(() =>
        {
            context.Response.Headers[CorrelationIdHeaderKey] = correlationId;
            return Task.CompletedTask;
        });

        // Enrich Serilog logging context with this CorrelationId
        using (Serilog.Context.LogContext.PushProperty("CorrelationId", correlationId))
        {
            await next(context);
        }
    }
}
```

---

## 3. OpenTelemetry Integration

### 3.1 C# Setup for Traces and Metrics
Gunakan OpenTelemetry untuk tracing ke Jaeger atau Azure Monitor.

```csharp
using OpenTelemetry.Metrics;
using OpenTelemetry.Resources;
using OpenTelemetry.Trace;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddOpenTelemetry()
    .ConfigureResource(resource => resource.AddService("EnterpriseOrderAPI"))
    .WithTracing(tracing => tracing
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddEntityFrameworkCoreInstrumentation()
        .AddSqlClientInstrumentation(options => options.SetDbStatementForText = true)
        .AddJaegerExporter(options =>
        {
            options.AgentHost = builder.Configuration["Jaeger:Host"] ?? "localhost";
            options.AgentPort = int.Parse(builder.Configuration["Jaeger:Port"] ?? "6831");
        }))
    .WithMetrics(metrics => metrics
        .AddAspNetCoreInstrumentation()
        .AddRuntimeInstrumentation()
        .AddProcessInstrumentation()
        .AddPrometheusExporter());
```

---

## 4. Frontend ReactJS Logging Standard

Setiap exception di frontend ReactJS harus ditangkap menggunakan **Error Boundary** dan dikirim ke server backend atau tools monitoring eksternal (Sentry / Application Insights).

### 4.1 React Error Boundary Component
```tsx
import React, { Component, ErrorInfo, ReactNode } from "react";

interface Props {
  children: ReactNode;
}

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends Component<Props, State> {
  public state: State = {
    hasError: false
  };

  public static getDerivedStateFromError(_: Error): State {
    return { hasError: true };
  }

  public componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    // Log error to telemetry system (e.g., Sentry)
    console.error("Uncaught React Error:", error, errorInfo);
    
    // Example: API Call to log error to .NET Backend
    fetch("/api/v1/logs/frontend", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        message: error.message,
        stack: error.stack,
        componentStack: errorInfo.componentStack,
        url: window.location.href
      })
    }).catch(err => console.error("Logging failed:", err));
  }

  public render() {
    if (this.state.hasError) {
      return (
        <div className="flex flex-col items-center justify-center min-h-screen bg-gray-900 text-white p-4">
          <h1 className="text-2xl font-bold text-red-500 mb-2">Something went wrong.</h1>
          <p className="text-gray-400">Our engineering team has been notified. Please try reloading the page.</p>
          <button 
            onClick={() => window.location.reload()} 
            className="mt-4 px-4 py-2 bg-blue-600 hover:bg-blue-700 rounded transition"
          >
            Reload Page
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}
```

---

## 5. Health Checks & Alerting Rules

### 5.1 .NET Health Check Endpoint Configuration
Pasang health check untuk SQL Server dan Redis pada C#.

```csharp
builder.Services.AddHealthChecks()
    .AddSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")!, name: "sqlserver")
    .AddRedis(builder.Configuration["Redis:ConnectionString"]!, name: "redis");

// Map health check endpoint
var app = builder.Build();
app.MapHealthChecks("/health");
```
Untuk visualisasi, pasang `/health/ui` untuk melihat dashboard status ketersediaan dependency server.
