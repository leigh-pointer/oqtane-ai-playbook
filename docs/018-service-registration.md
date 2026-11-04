# 018 - Service Registration and Module Startup

## Problem

Many AI tools and developers assume that all ASP.NET-based systems expose a
central application startup model (`Program.cs`, `Startup.cs`, or `WebApplicationBuilder`).

**Oqtane modules do not own application startup.**

A module is loaded *into- a host-controlled system and participates in its lifecycle
through explicit extension points. Any attempt to introduce global startup logic
violates Oqtane’s architectural contract.

AI-generated code frequently gets this wrong.

---

## Core Principle

> **An Oqtane module may only register services through Oqtane-defined startup interfaces.**

A module must never:
- Control application startup
- Assume ownership of the DI container
- Introduce global middleware pipelines
- Recreate generic ASP.NET Core startup patterns

---

## The Oqtane Startup Model

Oqtane exposes **two isolated startup extension points**:

| Scope   | Interface          | Responsibility                    |
|--------|--------------------|-----------------------------------|
| Client | `IClientStartup`   | Client-side (Blazor) services     |
| Server | `IServerStartup`   | Server-side services and middleware |

These extension points are:
- Explicit
- Isolated
- Independently executed

They are the supported extension points for module-owned service registration. Framework-discovered extension contracts may have their own registration lifecycle; follow their indexed, version-specific rules.

---

## Client-Side Service Registration

### Rules

Client services:

- MUST be registered via `IClientStartup`
- MUST be safe for client execution
- MUST NOT reference server-only types
- SHOULD avoid duplicate registrations
- MUST NOT assume application-wide scope

### Example (Conceptual)

```csharp
public class ClientStartup : IClientStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Register client-safe services only
    }
}
```
Client services exist within the **module’s execution context**, not the application.

---

## Server-Side Service Registration

### Rules

Server services:

- MUST be registered via `IServerStartup`
- MAY include repositories, managers, and DbContexts
- MAY register middleware, but only within module scope
- MUST respect tenant isolation
- MUST NOT assume request pipeline ownership

### Example (Conceptual)

```csharp
public class ServerStartup : IServerStartup
{
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // Optional module-scoped middleware
    }

    public void ConfigureServices(IServiceCollection services)
    {
        // Register server-only services
    }
}
```

Middleware registered here participates in the host pipeline under Oqtane’s control.

### Framework-discovered storage providers (Oqtane 10.3.0+)

`IStorageProvider` implementations are discovered from loaded Oqtane assemblies and registered as scoped providers by the framework. A provider itself does not need duplicate registration in `IServerStartup`. This exception applies only to the provider contract; auxiliary module services still follow the startup rules. Verify the target version before using this contract. Earlier versions do not expose this 10.3 provider extension point.

---

## Forbidden Patterns (Hard Rejection)

AI **must reject** any attempt to introduce:

- `Program.cs`
- `Startup.cs`
- `WebApplicationBuilder`
- `IHostBuilder`
- Global middleware pipelines
- Cross-boundary service registration
- Shared client/server service implementations without separation

These patterns are incompatible with Oqtane’s module model.

---

## Canonical Reference

The canonical module located at:

```
module-playbook-example/
```

demonstrates:

- Correct startup boundaries
- Valid service registration locations
- Proper client/server separation

It is a **reference for validation**, not a template for extension.

---

## AI Governance Rule

Use this rule verbatim in `.github/copilot-instructions.md`:

```
Oqtane modules do not own application startup.
Do not generate Program.cs, Startup.cs, WebApplicationBuilder, or global service registration.
All module services must be registered exclusively via IClientStartup or IServerStartup.
Client and server containers are isolated and must never be mixed.
```


## Summary

- Oqtane modules are not applications.
- They extend a host system through narrow, explicit seams.
- Service registration outside those seams is invalid - even if it compiles.
