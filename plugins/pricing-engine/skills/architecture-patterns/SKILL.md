---
name: architecture-patterns
description: Provides critical architectural patterns for the pricing engine. Covers database operations, B2B/B2C dual processing, background jobs, error handling, and performance patterns. Use before implementing pricing engine features, modifying background jobs, or reviewing pricing engine code changes.
---

# Pricing Engine Architecture Patterns

This is a high-performance financial system processing hundreds of price change notifications per second with real money transactions. These patterns are critical.

## System Context

- **Throughput**: ~33 notifications/sec typical, 500-1000/sec capacity
- **Database Scale**: 93M+ rows in price history tables
- **Concurrency**: Multiple instances run simultaneously
- **Stakes**: Real money transactions - errors have financial impact

## Database Operations (CRITICAL)

### Bulk Operations
**MUST** use `QuickInsertService` with PostgreSQL COPY protocol for inserting multiple records.

```csharp
// CORRECT - Bulk insert
await quickInsertService.InsertAsync(items, cancellationToken);

// WRONG - Individual inserts in loop
foreach (var item in items)
    await dbContext.AddAsync(item);  // CRITICAL VIOLATION
```

### Index Awareness
New queries **MUST** consider indexes. Unindexed query on `price_history_items` (93M+ rows) = system degradation.

### Time-series Pattern
Append-only tables + `_latest` tables with triggers. Never update historical records.

## B2B/B2C Dual Processing (CRITICAL)

Most entities have `_b2b` variants. **If code touches B2C pricing, verify B2B is also handled.**

| B2C Table | B2B Table |
|-----------|-----------|
| `price_history_items` | `price_history_items_b2b` |
| `recommendations` | `recommendations_b2b` |
| `buy_box_history` | `buy_box_history_b2b` |

**Missing B2B handling = 50% of pricing logic broken.**

## Background Jobs (CRITICAL)

### Cancellation Tokens
**MUST** accept and propagate `CancellationToken` to all async operations.

```csharp
// CORRECT
public async Task ExecuteAsync(CancellationToken cancellationToken)
{
    await dbContext.SaveChangesAsync(cancellationToken);
}

// WRONG - Missing cancellation token causes shutdown hangs
public async Task ExecuteAsync()
{
    await dbContext.SaveChangesAsync();  // CRITICAL VIOLATION
}
```

### Distributed Locks
Jobs that shouldn't run concurrently **MUST** use `PostgresqlDistributedLock`.

### Configuration
Via `appsettings.json` under `Modules::{ModuleName}::BackgroundJobs`.

### Logging
**MUST** log start/completion/errors with context.

## Error Handling (CRITICAL)

```csharp
// CORRECT - Specific exception, logged and re-thrown
catch (DbUpdateException ex)
{
    logger.LogError(ex, "Failed to save price update for {Sku}", sku);
    throw;  // Re-throw for retry logic
}

// WRONG - Silent swallow
catch (Exception) { }  // CRITICAL VIOLATION in financial operations

// JSON parsing errors should be warnings, not crashes
catch (JsonException ex)
{
    logger.LogWarning(ex, "Invalid JSON in notification");
}
```

## Configuration (Security)

- **No hardcoded**: URLs, timeouts, batch sizes, credentials
- **Use**: `IOptions<T>` pattern
- **Secrets**: Via Docker mounts to `/app/secrets/`

## API/Endpoint Pattern

```
Modules/{Module}/Endpoints/Read/   - Read endpoints
Modules/{Module}/Endpoints/Write/  - Write endpoints
```

Contracts (request/response) in same file as endpoint. All I/O operations **MUST** be async.

## Frontend Patterns

- Container components load their own data via TanStack Query hooks
- **Never modify** `/components/ui/` (shadcn components)
- Assume millions of records - **always** provide filtering/pagination
- Debounce search inputs (300-500ms)

## Quick Checklist

Before any change:

- [ ] No performance anti-patterns (loops with SaveChanges, unbounded ToList)
- [ ] Indexes considered for new queries
- [ ] B2B variant handled if applicable
- [ ] Cancellation tokens propagated
- [ ] Error handling appropriate
- [ ] No security vulnerabilities
- [ ] Follows existing codebase patterns
