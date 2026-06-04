# 027x Caching Governance

## Purpose

This rule governs caching implementations within Oqtane modules. Oqtane 10.2.0 introduced `CacheManager` (wrapping FusionCache) to provide a multi-tenant safe abstraction over distributed and memory caching.

---

## 1. Version Gate (Runtime-Aware Governance)

The `CacheManager` abstraction is available in **Oqtane 10.2.0 or greater**.

Before generating caching logic, AI must verify the framework version.

If Version < 10.2.0:
- Standard `IMemoryCache` is permissible, but keys **must** be explicitly prefixed with the `SiteId` to prevent cross-tenant data bleeding.

If Version >= 10.2.0:
- Use `CacheManager` natively provided by the framework.
- `IMemoryCache` and `IDistributedCache` must not be used directly.

---

## 2. Allowed Cache Mechanism (10.2.0+)

Modules must strictly use the framework-provided `CacheManager` abstraction for caching needs. The framework handles standardizing cache key formats and supporting multi-tenancy.

**Reject if:**
- `IMemoryCache` is injected or used directly.
- `IDistributedCache` is injected or used directly.
- Static dictionaries, fields, or singletons are used to cache data in memory.
- Custom cache-aside logic is manually implemented using generic abstractions instead of `CacheManager`.

---

## 3. Cache Keys and Multi-Tenancy

Oqtane's `CacheManager` automatically handles key standardization and multi-tenant scoping if implemented through the framework abstractions. 

AI must explicitly rely on the `CacheManager` caching primitives instead of manually concatenating `TenantId` or `SiteId` into strings when interacting with the cache, unless the specific framework API explicitly requires it.

---

## 4. Cache Durations

When specifying cache durations, utilize `TimeSpan` parameters. If applicable, defer to configuration options exposed by the framework or module settings (e.g. `MemoryCacheDuration`, `DistributedCacheDuration`) rather than hardcoding arbitrary expiration values.

---

## 5. AI Enforcement Behavior

If the user requests caching logic, AI must:
1. Validate the Oqtane version.
2. Inject the framework `CacheManager` abstraction in server services if >= 10.2.0.
3. Refuse to use raw `IMemoryCache` or `IDistributedCache` on modern versions.
4. Refuse the creation of static state properties for caching purposes.
