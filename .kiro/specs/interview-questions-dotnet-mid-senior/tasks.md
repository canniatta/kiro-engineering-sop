# Implementation Plan: Interview Questions .NET Mid-Senior

## Overview

Membuat dokumen `28-interview-questions-dotnet-mid-senior.md` yang berisi 95 pertanyaan interview untuk kandidat .NET Developer level Mid to Senior. Setiap pertanyaan harus disertai jawaban detail dengan poin-poin kunci, contoh kode, ekspektasi Mid vs Senior, follow-up questions, dan red/green flags.

## Tasks

- [x] 1. Tulis section Pendahuluan dokumen
  - Tujuan dokumen dan cara penggunaan
  - Target level kandidat (Mid vs Senior) dengan kriteria terukur
  - Tech stack yang relevan (.NET 8, C# 12, SQL Server 2022, EF Core 8.0)
  - Struktur dokumen dan navigasi kategori
  - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [x] 2. Tulis pertanyaan C# & .NET Fundamentals (18 pertanyaan)
  - [x] 2.1 Tulis Q-FUND-001: Value Type vs Reference Type
    - Level: Mid, Topik: Memory management, stack vs heap
    - Jawaban: Poin kunci perbedaan, contoh kode, behavior saat assignment
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.2 Tulis Q-FUND-002: Boxing dan Unboxing
    - Level: Mid, Topik: Performance, implicit/explicit conversion
    - Jawaban: Konsep boxing/unboxing, implikasi performa, contoh kode
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.3 Tulis Q-FUND-003: Generics dan Constraints
    - Level: Mid, Topik: Type safety, generic constraints, covariance/contravariance
    - Jawaban: Manfaat generics, constraint types, contoh implementasi
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.4 Tulis Q-FUND-004: Delegates dan Events
    - Level: Mid, Topik: Callback pattern, multicast delegates, event pattern
    - Jawaban: Perbedaan delegate vs event, usage pattern, contoh kode
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.5 Tulis Q-FUND-005: Async/Await dan Deadlock
    - Level: Both, Topik: Synchronization context, deadlock prevention
    - Jawaban: Penyebab deadlock, ConfigureAwait(false), best practices
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.6 Tulis Q-FUND-006: Task vs ValueTask
    - Level: Senior, Topik: Performance optimization, allocation reduction
    - Jawaban: Kapan pakai ValueTask, trade-offs, benchmark considerations
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.7 Tulis Q-FUND-007: LINQ Deferred Execution
    - Level: Mid, Topik: IEnumerable, IQueryable, execution timing
    - Jawaban: Deferred vs immediate execution, contoh perilaku, pitfalls
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.8 Tulis Q-FUND-008: LINQ Performance dan Optimization
    - Level: Senior, Topik: Query optimization, projection, materialization
    - Jawaban: Best practices LINQ, memory allocation, alternatives
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.9 Tulis Q-FUND-009: Extension Methods
    - Level: Mid, Topik: Syntax, use cases, namespace consideration
    - Jawaban: Cara kerja, best practices, contoh implementasi praktis
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.10 Tulis Q-FUND-010: Reflection dan Attributes
    - Level: Senior, Topik: Metadata inspection, dynamic invocation, custom attributes
    - Jawaban: Use cases reflection, performance cost, contoh custom attribute
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.11 Tulis Q-FUND-011: Garbage Collection di .NET
    - Level: Senior, Topik: Generations, LOH, GC modes
    - Jawaban: Cara kerja GC, generasi, large object heap, tuning
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.12 Tulis Q-FUND-012: IDisposable dan Using Statement
    - Level: Mid, Topik: Resource management, dispose pattern, finalizers
    - Jawaban: Kapan implement IDisposable, finalizer, safe handle
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.13 Tulis Q-FUND-013: Memory Management Best Practices
    - Level: Senior, Topik: Span<T>, Memory<T>, array pooling, stackalloc
    - Jawaban: Modern memory management techniques, contoh kode optimal
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.14 Tulis Q-FUND-014: Thread Safety dan Synchronization
    - Level: Both, Topik: Lock, Monitor, Mutex, Semaphore, concurrent collections
    - Jawaban: Thread synchronization primitives, best practices, race condition prevention
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.15 Tulis Q-FUND-015: CancellationToken dan Cooperative Cancellation
    - Level: Mid, Topik: Cancellation pattern, linked tokens, timeout handling
    - Jawaban: Cara kerja CancellationToken, best practices, contoh implementasi
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.16 Tulis Q-FUND-016: Record Types dan Pattern Matching
    - Level: Mid, Topik: C# 9+ features, immutable data, positional records
    - Jawaban: Keunggulan record, with expression, pattern matching examples
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.17 Tulis Q-FUND-017: Nullable Reference Types
    - Level: Mid, Topik: Null safety, compiler warnings, nullable annotations
    - Jawaban: Cara enable NRT, annotating APIs, suppress warnings correctly
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.18 Tulis Q-FUND-018: Source Generators
    - Level: Senior, Topik: Compile-time code generation, incremental generators
    - Jawaban: Cara kerja source generators, use cases, contoh implementasi
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [x] 3. Tulis pertanyaan Clean Architecture & Design Patterns (12 pertanyaan)
  - [x] 3.1 Tulis Q-ARCH-001: SOLID Principles Overview
    - Level: Mid, Topik: Kelima prinsip SOLID dengan contoh konkret
    - Jawaban: Penjelasan setiap prinsip, contoh pelanggaran dan perbaikan
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.2 Tulis Q-ARCH-002: Dependency Injection Deep Dive
    - Level: Mid, Topik: DI patterns, service lifetime, anti-patterns
    - Jawaban: Singleton/Scoped/Transient, constructor injection, property injection
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.3 Tulis Q-ARCH-003: Captive Dependency
    - Level: Senior, Topik: Service lifetime mismatch, detection, solutions
    - Jawaban: Definisi captive dependency, implikasi, factory pattern solution
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.4 Tulis Q-ARCH-004: Repository Pattern
    - Level: Mid, Topik: Data access abstraction, generic repository, unit of work
    - Jawaban: Tujuan repository, implementasi, kapan perlu/tidak perlu
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.5 Tulis Q-ARCH-005: CQRS Pattern
    - Level: Senior, Topik: Command Query Separation, MediatR, event sourcing
    - Jawaban: Konsep CQRS, implementasi dengan MediatR, trade-offs
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.6 Tulis Q-ARCH-006: MediatR dan Mediator Pattern
    - Level: Mid, Topik: Decoupling, pipeline behavior, request/response
    - Jawaban: Cara kerja MediatR, pipeline behaviors untuk cross-cutting concerns
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.7 Tulis Q-ARCH-007: Clean Architecture Layers
    - Level: Mid, Topik: Domain, Application, Infrastructure, Presentation
    - Jawaban: Tanggung jawab setiap layer, dependency direction, project structure
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.8 Tulis Q-ARCH-008: Domain-Driven Design Concepts
    - Level: Senior, Topik: Aggregate, Entity, Value Object, Domain Events
    - Jawaban: Konsep DDD, bounded context, implementasi di .NET
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.9 Tulis Q-ARCH-009: Strategy Pattern
    - Level: Mid, Topik: Behavioral pattern, runtime algorithm selection
    - Jawaban: Implementasi strategy pattern, dependency injection integration
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.10 Tulis Q-ARCH-010: Factory Pattern
    - Level: Mid, Topik: Object creation, factory method, abstract factory
    - Jawaban: Jenis factory pattern, kapan digunakan, contoh implementasi
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [ ] 3.11 Tulis Q-ARCH-011: Decorator Pattern
    - Level: Senior, Topik: Structural pattern, dynamic behavior addition
    - Jawaban: Use cases, implementasi dengan DI, contoh pipeline behavior
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [x] 3.12 Tulis Q-ARCH-012: Adapter dan Facade Pattern
    - Level: Mid, Topik: Integration patterns, third-party library wrapping
    - Jawaban: Perbedaan adapter vs facade, contoh integrasi external service
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

- [ ] 4. Tulis pertanyaan EF Core & Database (12 pertanyaan)
  - [ ] 4.1 Tulis Q-DATA-001: DbContext Lifecycle Management
    - Level: Mid, Topik: Scoped lifetime, pooling, disposal
    - Jawaban: Cara kerja DbContext, DI configuration, pooling optimization
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.2 Tulis Q-DATA-002: Change Tracking di EF Core
    - Level: Mid, Topik: Tracking vs No-Tracking, entity states
    - Jawaban: Entity states, AsNoTracking optimization, when to use what
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.3 Tulis Q-DATA-003: Eager vs Lazy vs Explicit Loading
    - Level: Mid, Topik: Navigation property loading strategies
    - Jawaban: Perbedaan tiga strategi, performance implications, contoh kode
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.4 Tulis Q-DATA-004: N+1 Query Problem
    - Level: Both, Topik: Query optimization, Include, ThenInclude
    - Jawaban: Identifikasi N+1, solusi dengan Include, cartesian explosion
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.5 Tulis Q-DATA-005: EF Core Migrations Strategy
    - Level: Mid, Topik: Migration workflow, production deployment, scripts
    - Jawaban: Best practices migration, idempotent scripts, rollback strategy
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.6 Tulis Q-DATA-006: Transaction Management
    - Level: Mid, Topik: DbContext transaction, distributed transaction, Unit of Work
    - Jawaban: BeginTransaction, SaveChanges, ambient transaction, isolation levels
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.7 Tulis Q-DATA-007: Raw SQL dan FromSqlRaw
    - Level: Mid, Topik: Raw query, stored procedure, SQL injection prevention
    - Jawaban: Kapan pakai raw SQL, parameterized query, interpolation vs parameter
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.8 Tulis Q-DATA-008: Complex Query dan Projection
    - Level: Senior, Topik: Select optimization, DTO projection, client evaluation
    - Jawaban: Projection best practices, Select vs Include, client-side evaluation pitfalls
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.9 Tulis Q-DATA-009: Concurrency Control
    - Level: Senior, Topik: Optimistic concurrency, row version, conflict resolution
    - Jawaban: ConcurrencyToken, RowVersion, handling DbUpdateConcurrencyException
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.10 Tulis Q-DATA-010: Index Strategy di EF Core
    - Level: Senior, Topik: Index configuration, composite index, included columns
    - Jawaban: HasIndex, index naming, SQL Server specific features
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.11 Tulis Q-DATA-011: Bulk Operations dan Performance
    - Level: Senior, Topik: Bulk insert, update, delete, batch size
    - Jawaban: AddRange optimization, EF Core 8 improvements, third-party libraries
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [ ] 4.12 Tulis Q-DATA-012: Database Connection Resilience
    - Level: Senior, Topik: Connection pooling, retry logic, circuit breaker
    - Jawaban: EnableRetryOnFailure, connection string optimization, health checks
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [ ] 5. Tulis pertanyaan API Design & REST (9 pertanyaan)
  - [ ] 5.1 Tulis Q-API-001: RESTful API Design Principles
    - Level: Mid, Topik: Resource naming, HTTP methods, statelessness
    - Jawaban: REST constraints, resource-oriented design, best practices
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.2 Tulis Q-API-002: HTTP Status Codes Usage
    - Level: Mid, Topik: Success codes, error codes, redirect codes
    - Jawaban: Kapan pakai 200/201/204, error response structure, 4xx vs 5xx
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.3 Tulis Q-API-003: API Versioning Strategies
    - Level: Mid, Topik: URL versioning, header versioning, query string
    - Jawaban: Perbandingan strategi, implementasi di .NET 8, backward compatibility
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.4 Tulis Q-API-004: Input Validation dengan FluentValidation
    - Level: Mid, Topik: Validation rules, custom validators, pipeline integration
    - Jawaban: Setup FluentValidation, validator composition, error response format
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.5 Tulis Q-API-005: Authentication dan Authorization di API
    - Level: Mid, Topik: JWT, OAuth 2.0, policy-based authorization
    - Jawaban: JWT validation flow, claims, policies, role-based access
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.6 Tulis Q-API-006: Rate Limiting dan Throttling
    - Level: Senior, Topik: Rate limiting middleware, token bucket, sliding window
    - Jawaban: Implementasi rate limiting, algorithm choice, distributed scenario
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.7 Tulis Q-API-007: API Documentation dengan OpenAPI/Swagger
    - Level: Mid, Topik: Swashbuckle, XML comments, response examples
    - Jawaban: Generate OpenAPI spec, document endpoints, UI customization
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.8 Tulis Q-API-008: Error Handling dan Problem Details
    - Level: Mid, Topik: ProblemDetails RFC 7807, global exception handling
    - Jawaban: Implementasi ProblemDetails, exception middleware, error codes
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 5.9 Tulis Q-API-009: Pagination, Filtering, dan Sorting
    - Level: Mid, Topik: Cursor vs offset pagination, query parameters, performance
    - Jawaban: Implementasi pagination, filtering strategy, HATEOAS consideration
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 6. Tulis pertanyaan Performance & Optimization (9 pertanyaan)
  - [ ] 6.1 Tulis Q-PERF-001: Caching Strategies
    - Level: Mid, Topik: In-memory cache, distributed cache, cache invalidation
    - Jawaban: IMemoryCache vs IDistributedCache, cache key design, expiration
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.2 Tulis Q-PERF-002: Async Optimization
    - Level: Mid, Topik: I/O bound vs CPU bound, parallel processing, WhenAll
    - Jawaban: Task.WhenAll vs sequential, ConfigureAwait, thread pool starvation
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.3 Tulis Q-PERF-003: Memory Profiling dan Diagnostics
    - Level: Senior, Topik: dotnet-counters, dotnet-dump, memory leaks
    - Jawaban: Tools untuk profiling, identifying leaks, GC debugging
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.4 Tulis Q-PERF-004: BenchmarkDotNet Usage
    - Level: Senior, Topik: Micro-benchmarking, allocation measurement, diagnoser
    - Jawaban: Setup BenchmarkDotNet, interpreting results, common pitfalls
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.5 Tulis Q-PERF-005: Garbage Collection Tuning
    - Level: Senior, Topik: Workstation vs Server GC, compacting, heap fragmentation
    - Jawaban: GC modes, configuration, when to tune, production considerations
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.6 Tulis Q-PERF-006: Object Pooling dan ArrayPool
    - Level: Senior, Topik: Allocation reduction, ArrayPool<T>, ObjectPool<T>
    - Jawaban: Kapan pakai pooling, implementasi manual vs Microsoft.Extensions.ObjectPool
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.7 Tulis Q-PERF-007: High-Performance Scenarios dengan Span<T>
    - Level: Senior, Topik: Memory<T>, Span<T>, stackalloc, slicing
    - Jawaban: Zero-allocation parsing, slicing, stack-only constraint
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.8 Tulis Q-PERF-008: API Response Optimization
    - Level: Mid, Topik: JSON serialization, compression, response caching
    - Jawaban: System.Text.Json optimization, response compression middleware
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ] 6.9 Tulis Q-PERF-009: Application Startup Performance
    - Level: Senior, Topik: AOT, trimming, ready-to-run, startup hooks
    - Jawaban: Startup optimization techniques, AOT considerations, Native AOT
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

- [ ] 7. Tulis pertanyaan Testing & Quality (9 pertanyaan)
  - [ ] 7.1 Tulis Q-TEST-001: Unit Testing Best Practices
    - Level: Mid, Topik: AAA pattern, test naming, one assertion per test
    - Jawaban: Arrange-Act-Assert, naming convention, test organization
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.2 Tulis Q-TEST-002: Mocking dengan NSubstitute
    - Level: Mid, Topik: Substitute creation, setup, verification
    - Jawaban: Membuat mock, setup return values, verify calls, argument matchers
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.3 Tulis Q-TEST-003: FluentAssertions untuk Readable Tests
    - Level: Mid, Topik: Assertion library, fluent syntax, custom assertions
    - Jawaban: Assertion syntax, collection assertions, exception assertions
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.4 Tulis Q-TEST-004: Test-Driven Development (TDD)
    - Level: Mid, Topik: Red-Green-Refactor, TDD benefits, challenges
    - Jawaban: TDD workflow, when TDD adds value, common mistakes
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.5 Tulis Q-TEST-005: Integration Testing dengan WebApplicationFactory
    - Level: Senior, Topik: Test server, database fixture, test containers
    - Jawaban: Setup integration tests, in-memory database, test isolation
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.6 Tulis Q-TEST-006: Test Coverage dan Quality Metrics
    - Level: Mid, Topik: Coverage tools, mutation testing, quality gates
    - Jawaban: Coverage measurement, coverlet, minimum coverage threshold
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.7 Tulis Q-TEST-007: Testing Async Code
    - Level: Mid, Topik: Async test pitfalls, timeout, cancellation
    - Jawaban: async void vs async Task, ConfigureAwait in tests, timeout handling
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.8 Tulis Q-TEST-008: Test Data Builders dan Fixtures
    - Level: Senior, Topik: Test data generation, builder pattern, AutoFixture
    - Jawaban: Builder pattern for test data, AutoFixture usage, fixture sharing
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ] 7.9 Tulis Q-TEST-009: Code Quality dan Static Analysis
    - Level: Mid, Topik: Roslyn analyzers, SonarQube, code metrics
    - Jawaban: Analyzer setup, custom rules, CI integration, technical debt tracking
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

- [ ] 8. Tulis pertanyaan Security (7 pertanyaan)
  - [ ] 8.1 Tulis Q-SEC-001: OWASP Top 10 untuk .NET
    - Level: Mid, Topik: Common vulnerabilities, mitigation strategies
    - Jawaban: Injection, XSS, broken auth, security misconfiguration in .NET context
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.2 Tulis Q-SEC-002: SQL Injection Prevention
    - Level: Mid, Topik: Parameterized queries, ORM safety, input validation
    - Jawaban: Parameterized query, EF Core protection, vulnerable patterns
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.3 Tulis Q-SEC-003: JWT Security Best Practices
    - Level: Mid, Topik: Token validation, expiration, refresh tokens, claims
    - Jawaban: JWT structure, validation parameters, refresh flow, secure storage
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.4 Tulis Q-SEC-004: Authentication vs Authorization
    - Level: Mid, Topik: Identity vs access control, policies, roles
    - Jawaban: Perbedaan konsep, ASP.NET Core Identity, policy-based auth
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.5 Tulis Q-SEC-005: Secure Configuration Management
    - Level: Senior, Topik: Secrets management, Azure Key Vault, environment variables
    - Jawaban: Storing secrets, configuration providers, production considerations
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.6 Tulis Q-SEC-006: HTTPS dan Certificate Management
    - Level: Mid, Topik: TLS, certificates, HSTS, redirect enforcement
    - Jawaban: HTTPS redirection, HSTS middleware, certificate validation
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

  - [ ] 8.7 Tulis Q-SEC-007: Input Validation dan Sanitization
    - Level: Mid, Topik: Model binding, validation attributes, XSS prevention
    - Jawaban: Data annotations, FluentValidation, HTML encoding, anti-XSS
    - _Requirements: 8.1, 8.2, 8.3, 8.4_

- [ ] 9. Tulis pertanyaan DevOps & Deployment (7 pertanyaan)
  - [ ] 9.1 Tulis Q-DEVOPS-001: Docker Containerization untuk .NET
    - Level: Mid, Topik: Dockerfile, multi-stage build, image optimization
    - Jawaban: Multi-stage Dockerfile, slim images, security best practices
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.2 Tulis Q-DEVOPS-002: CI/CD Pipeline dengan GitHub Actions/Azure DevOps
    - Level: Mid, Topik: Build pipeline, test automation, deployment stages
    - Jawaban: Pipeline structure, build/test/deploy, environment management
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.3 Tulis Q-DEVOPS-003: Configuration Management
    - Level: Mid, Topik: appsettings.json, environment variables, Azure App Config
    - Jawaban: Configuration providers, override hierarchy, production configuration
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.4 Tulis Q-DEVOPS-004: Logging dengan Serilog
    - Level: Mid, Topik: Structured logging, sinks, enrichers
    - Jawaban: Serilog setup, common sinks, correlation ID, log levels
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.5 Tulis Q-DEVOPS-005: Health Checks dan Monitoring
    - Level: Mid, Topik: Health check endpoints, readiness/liveness probes, monitoring
    - Jawaban: Health check implementation, custom checks, Application Insights
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.6 Tulis Q-DEVOPS-006: Blue-Green dan Canary Deployment
    - Level: Senior, Topik: Deployment strategies, zero-downtime, rollback
    - Jawaban: Strategy comparison, implementation considerations, traffic routing
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

  - [ ] 9.7 Tulis Q-DEVOPS-007: Container Orchestration Basics
    - Level: Senior, Topik: Kubernetes basics, scaling, service discovery
    - Jawaban: K8s concepts for .NET developers, deployments, services, ingress
    - _Requirements: 9.1, 9.2, 9.3, 9.4_

- [ ] 10. Tulis pertanyaan Soft Skills & Problem Solving (6 pertanyaan)
  - [ ] 10.1 Tulis Q-SOFT-001: Technical Decision Making
    - Level: Both, Topik: Trade-off analysis, ADR, decision communication
    - Jawaban: Framework untuk decision making, documenting decisions, stakeholder communication
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

  - [ ] 10.2 Tulis Q-SOFT-002: Mentoring dan Knowledge Sharing
    - Level: Senior, Topik: Mentoring approach, code review feedback, documentation
    - Jawaban: Mentoring strategies, effective feedback, growing team capabilities
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

  - [ ] 10.3 Tulis Q-SOFT-003: Handling Technical Debt
    - Level: Senior, Topik: Debt identification, prioritization, payback strategy
    - Jawaban: Identifying tech debt, business case for refactoring, incremental improvement
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

  - [ ] 10.4 Tulis Q-SOFT-004: Code Review Mindset
    - Level: Both, Topik: Constructive feedback, review efficiency, knowledge transfer
    - Jawaban: Review philosophy, giving feedback, receiving feedback, automation
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

  - [ ] 10.5 Tulis Q-SOFT-005: Conflict Resolution in Technical Decisions
    - Level: Senior, Topik: Disagreement handling, consensus building, escalation
    - Jawaban: Managing disagreements, data-driven decisions, team alignment
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

  - [ ] 10.6 Tulis Q-SOFT-006: Time Management dan Prioritization
    - Level: Both, Topik: Task prioritization, deadline management, scope negotiation
    - Jawaban: Prioritization frameworks, saying no, managing expectations
    - _Requirements: 10.1, 10.2, 10.3, 10.4_

- [ ] 11. Tulis pertanyaan Scenario-Based (6 pertanyaan)
  - [ ] 11.1 Tulis Q-SCEN-001: Production Incident Response
    - Level: Both, Topik: Debugging production, incident handling, RCA
    - Jawaban: Incident response process, debugging techniques, post-mortem
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

  - [ ] 11.2 Tulis Q-SCEN-002: Legacy Code Refactoring
    - Level: Senior, Topik: Refactoring strategy, testing, incremental migration
    - Jawaban: Strangler fig pattern, characterization tests, risk mitigation
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

  - [ ] 11.3 Tulis Q-SCEN-003: Architecture Decision for New Feature
    - Level: Senior, Topik: Requirements analysis, options evaluation, ADR
    - Jawaban: Decision framework, trade-off analysis, documentation
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

  - [ ] 11.4 Tulis Q-SCEN-004: Performance Troubleshooting
    - Level: Both, Topik: Performance bottleneck identification, profiling, optimization
    - Jawaban: Debugging slow requests, profiling tools, optimization strategy
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

  - [ ] 11.5 Tulis Q-SCEN-005: Scaling Application Architecture
    - Level: Senior, Topik: Scaling patterns, bottlenecks, distributed systems
    - Jawaban: Vertical vs horizontal scaling, caching strategy, database scaling
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

  - [ ] 11.6 Tulis Q-SCEN-006: Building New Microservice
    - Level: Senior, Topik: Service boundaries, communication, data ownership
    - Jawaban: Decomposition strategy, inter-service communication, eventual consistency
    - _Requirements: 11.1, 11.2, 11.3, 11.4_

- [ ] 12. Checkpoint - Pastikan semua pertanyaan terformat konsisten
  - Pastikan semua pertanyaan mengikuti format dari design document
  - Verifikasi jumlah pertanyaan per kategori sesuai requirements
  - Review konsistensi penamaan ID pertanyaan

- [ ] 13. Tulis section Scoring Rubrik
  - Overall assessment scale (1-5) dengan deskripsi per level
  - Category-specific rubrik untuk setiap kategori pertanyaan
  - Decision matrix untuk hire/reject decision
  - Contoh jawaban yang memenuhi kriteria Mid dan Senior
  - _Requirements: 12.1, 12.2, 12.3, 12.4_

- [ ] 14. Tulis section Tips untuk Interviewer
  - Cara mengajukan follow-up questions
  - Red flags yang perlu diwaspadai per kategori
  - Green flags yang dicari per kategori
  - Panduan durasi interview dan distribusi pertanyaan
  - Sumber referensi untuk persiapan lebih lanjut
  - _Requirements: 13.1, 13.2, 13.3, 13.4_

- [ ] 15. Tulis section Referensi
  - Link ke dokumentasi resmi Microsoft untuk .NET 8 dan C# 12
  - Referensi ke dokumen SOP terkait (Clean Architecture, Testing Strategy, Code Review)
  - Referensi ke best practices dan community resources
  - _Requirements: 14.1, 14.2, 14.3, 14.4_

- [ ] 16. Update 00-master-index.md dengan entry dokumen baru
  - Tambah entry "28 - Interview Questions .NET Mid-Senior" ke Table of Contents
  - Tambah ke kategori "Strategi Tim & Pengukuran Metrik"
  - Update changelog dengan versi dan tanggal
  - _Requirements: 15.1, 15.2, 15.3, 15.4, 15.5_


## Notes

- Setiap task pertanyaan (2.1 s/d 11.6) mengikuti format yang konsisten dari design document
- Jawaban detail mencakup: poin-poin kunci, contoh kode, ekspektasi Mid vs Senior, follow-up questions, dan red/green flags
- Total 95 pertanyaan terdistribusi di 10 kategori
- Tasks untuk pertanyaan dalam kategori yang sama dapat dikerjakan secara parallel
- Task 15 (update master-index) bergantung pada semua pertanyaan selesai ditulis
- Konvensi penulisan mengikuti aturan di `00-master-index.md` (Bahasa Indonesia, istilah teknis English)
- Line length maksimal 120 karakter
- Code blocks menggunakan fenced format dengan language tag (`csharp`, `sql`, `bash`)

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1"] },
    { "id": 1, "tasks": ["2.1", "2.2", "2.3", "2.4", "2.5", "2.6", "2.7", "2.8", "2.9", "2.10", "2.11", "2.12", "2.13", "2.14", "2.15", "2.16", "2.17", "2.18"] },
    { "id": 2, "tasks": ["3.1", "3.2", "3.3", "3.4", "3.5", "3.6", "3.7", "3.8", "3.9", "3.10", "3.11", "3.12"] },
    { "id": 3, "tasks": ["4.1", "4.2", "4.3", "4.4", "4.5", "4.6", "4.7", "4.8", "4.9", "4.10", "4.11", "4.12"] },
    { "id": 4, "tasks": ["5.1", "5.2", "5.3", "5.4", "5.5", "5.6", "5.7", "5.8", "5.9"] },
    { "id": 5, "tasks": ["6.1", "6.2", "6.3", "6.4", "6.5", "6.6", "6.7", "6.8", "6.9"] },
    { "id": 6, "tasks": ["7.1", "7.2", "7.3", "7.4", "7.5", "7.6", "7.7", "7.8", "7.9"] },
    { "id": 7, "tasks": ["8.1", "8.2", "8.3", "8.4", "8.5", "8.6", "8.7"] },
    { "id": 8, "tasks": ["9.1", "9.2", "9.3", "9.4", "9.5", "9.6", "9.7"] },
    { "id": 9, "tasks": ["10.1", "10.2", "10.3", "10.4", "10.5", "10.6"] },
    { "id": 10, "tasks": ["11.1", "11.2", "11.3", "11.4", "11.5", "11.6"] },
    { "id": 11, "tasks": [] },
    { "id": 12, "tasks": [] },
    { "id": 13, "tasks": [] }
  ]
}
```
