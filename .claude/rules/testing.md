---
paths:
  - "tests/**/*"
---

# Testing rules

- Integration tests share one PostgreSQL container per test collection and reset data between tests; they must not depend on execution order.
- Arrange data through the public API or explicit builders, not by reaching into another module's internals.
- External platforms: use WireMock.Net or the PlatformSimulator; never call real platform APIs from CI.
- Time-dependent logic takes `TimeProvider`; tests use a fake time provider instead of `Thread.Sleep`.
- Assert on behaviour visible to a user or caller (HTTP status, response body, persisted state), not on private implementation details.
