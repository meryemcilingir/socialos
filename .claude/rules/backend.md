---
paths:
  - "src/**/*.cs"
---

# Backend rules

- Endpoints: one route group per module under `/api/v1/<module>`. Every endpoint declares its authorization policy explicitly; anonymous endpoints are marked with `AllowAnonymous` on purpose.
- Authorization checks permissions (e.g. `posts.approve`), never role names.
- Pass `CancellationToken` through every async call. No `.Result`, `.Wait()` or `async void`.
- EF Core: one `DbContext` and one schema per module. Entity configuration in `IEntityTypeConfiguration<T>` classes. `AsNoTracking()` and projections for reads. No lazy loading.
- Logging: structured message templates (`"Published {PostTargetId}"`), never string interpolation in log calls.
- Configuration via strongly typed options validated on start (`ValidateOnStart`).
- HTTP clients for external platforms are typed clients with standard resilience; respect `Retry-After`.
