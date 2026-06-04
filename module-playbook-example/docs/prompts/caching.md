# AI Instruction: Caching Governance

**Instruction Context**: When the user requests to add caching, improve performance via cache, or store temporary data across requests, you must load and execute this file.

---

## Mandatory Context

Before responding, you MUST:

1. Read `docs/governance/027x-caching.md`
2. Verify Oqtane framework version using runtime awareness principles.

If the above file is missing or inaccessible, **STOP and refuse**.

---

## Caching Rules (Non-Negotiable)

You MUST:
- Use the framework-provided `CacheManager` abstraction if the Oqtane version is 10.2.0 or greater.
- Define cache durations using `TimeSpan`.
- Rely on framework APIs to guarantee multi-tenant cache isolation.

You MUST NOT:
- Use `IMemoryCache` or `IDistributedCache` directly in modern Oqtane modules.
- Use static fields, dictionaries, or singletons to hold state or cache data across requests.
- Implement custom background workers to invalidate cache entries when framework events or expiration policies exist.

---

## Expected Output
- Explicit injection of `CacheManager` in server-side services.
- Clean cache-aside patterns.
- Clear multi-tenant safety.

If uncertain about framework support, **REFUSE and explain why**.
