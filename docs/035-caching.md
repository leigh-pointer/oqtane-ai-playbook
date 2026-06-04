# 035 - Caching Governance

Oqtane 10.2.0 introduced `CacheManager` (wrapping FusionCache) to standardize caching across the framework and guarantee multi-tenant safety.

## The Problem

Using `IMemoryCache` or `IDistributedCache` directly in a multi-tenant environment risks cross-tenant data leakage if cache keys are not correctly namespaced with the `SiteId` or `TenantId`. Additionally, developers often reimplement cache-aside patterns, which introduces subtle bugs.

## The Solution

For Oqtane 10.2.0 and later, always use the framework-provided `CacheManager` (or equivalent framework caching abstraction) for caching in Oqtane modules. The framework automatically handles standardizing cache key formats, cache failsafes, and supporting multi-tenancy correctly.

## Rules

- Never use `IMemoryCache` or `IDistributedCache` directly.
- Never use static variables, dictionaries, or singleton collections for caching data.
- Ensure caching configuration utilizes framework standard parameters (e.g. `MemoryCacheDuration`, `DistributedCacheDuration`) where applicable.
- Let the framework dictate the cache expiration policy via `CacheManager`.
