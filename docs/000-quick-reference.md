# Quick Reference - What This Playbook Stops AI From Doing

This card lists the **top rejection rules** from the Oqtane AI Playbook in plain language.

If AI-generated code violates any of these, **reject it immediately.**

---

## The 15 Hard Stops

### Authorization
1. **Never authorize by role name.**  
   `User.IsInRole("Admin")` is always wrong in module code.  
   Use Oqtane's permission APIs. Hard roles are configuration - not contracts.

2. **Never enforce authorization on the client.**  
   Hiding a button is UX. It is not security.  
   All enforcement belongs on the server.

---

### Service Boundaries
3. **Never put implementations in the Shared project.**  
   Shared contains interfaces, DTOs, enums, and constants - nothing else.  
   Business logic in Shared crosses a deployment boundary.

4. **Never reference Server from Client.**  
   The Client project must not take a direct project reference to Server.  
   Services that cross the boundary must have a Shared interface and two implementations.

---

### Migrations
5. **Never use standard EF Core `Migration` classes.**  
   Oqtane migrations must inherit `MultiDatabaseMigration`.  
   Standard EF migrations are incompatible and will silently fail.

6. **Never run migrations manually.**  
   Oqtane owns migration execution at startup.  
   `Update-Database` and on-demand migration code are invalid patterns.

7. **Never write raw SQL without database guards.**  
   New tables must use `EntityBuilders`.  
   Raw SQL assumes a specific provider and breaks multi-database support.

---

### Scheduled Jobs
8. **Never use `BackgroundService`, `IHostedService`, timers, or loops.**  
   Scheduled Jobs must inherit `HostedServiceBase`.  
   Everything else bypasses the framework's tenant-aware execution model.

9. **Never store static state between job executions.**  
   Jobs execute per-tenant and may be retried.  
   Static or singleton state leaks across tenants.

---

### Service Registration
10. **Never introduce `Program.cs`, `Startup.cs`, or `WebApplicationBuilder`.**  
    Modules do not own application startup.  
    All service registration must go through `IClientStartup` or `IServerStartup`.

---

### Logging
11. **Never use `ILogger<T>` in Oqtane server code.**  
    Oqtane server logging must use `ILogManager`.  
    `ILogger<T>` breaks tenant context and auditability.

12. **Never log validation failures.**  
    Validation failures are expected user behaviour - not errors.  
    Log operational failures, security violations, and state changes only.

---

### Error Handling
13. **Never use `AddModuleMessage` outside a top-level module component.**  
    It requires a valid `RenderModeBoundary` and will throw at runtime if called in services, repositories, background tasks, or child components that don't carry the boundary.

14. **Never silently swallow exceptions.**  
    Any caught exception must be logged and - where appropriate - surface a user-visible message.

---

### NuGet Dependencies
15. **Never add a NuGet package without handling runtime deployment.**  
    NuGet restore does not deploy assemblies to the Oqtane Server bin.  
    Every external dependency must be explicitly deployed and declared in the module `.nuspec`.

---

## Where to Find the Full Rules

| Topic | Playbook Chapter | Governance Rule |
|---|---|---|
| Authorization | `011-authorization.md` | `027x-authorization.md` |
| Service boundaries | `010-services.md` | `027x-structure-and-boundaries.md` |
| Migrations | `012-migrations.md` | `027x-migrations.md` |
| Scheduled Jobs | `013-scheduled-jobs.md` | `027x-scheduled-jobs.md` |
| Service registration | `018-service-registration.md` | `027x-startup.md` |
| Logging | `016-logging.md` | - |
| Error handling | `015-Error-Handling.md` | `027x-error-handling.md` |
| Packaging | `020-packaging-and-dependencies.md` | `027x-packaging-and-dependencies.md` |
| Client/server boundaries | `017-Client-Server-Responsibility-Boundaries.md` | `027x-execution-parity.md` |
| Caching | `035-caching.md` | `027x-caching.md` |

---

> This card is a summary only. Rules stated here are excerpts - always consult the full chapter and governance rule for context, examples, and edge cases.
