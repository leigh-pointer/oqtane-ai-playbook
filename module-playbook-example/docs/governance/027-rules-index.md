# 027 - Governance Rule Index (Authoritative)

## Purpose

This document is the **authoritative index of enforceable AI governance rules** for this repository.

It defines **what rules exist**, not how they are implemented.

> If a rule is not indexed here, it does not exist and MUST NOT be enforced.

This prevents:

- Invented constraints
- Implicit best practices
- AI hallucinating architectural authority

---

## Governance Evaluation Order (Authoritative Precedence)

GitHub indexes files alphabetically.  
Governance enforcement does NOT follow alphabetical order.

The following hierarchy defines **mandatory evaluation order**:

1. **027x-capability-discovery.md**
2. **027x-runtime-awareness.md**
3. **027x-migrations.md**
4. **027x-packaging-and-dependencies.md**
5. **027x-scheduled-jobs.md**
6. **027x-sitetasks.md**
7. Remaining governance rules (as applicable)

### Why This Order Exists

Capability Discovery determines:

- What functionality already exists
- What must be reused instead of recreated
- Whether new code is required at all

Without capability discovery:

- AI will duplicate framework behavior
- Services and state will be recreated incorrectly
- Models may diverge from existing implementations

This makes all subsequent rules unreliable.

---

Runtime Awareness determines:

- Framework version
- Database baseline state
- Migration execution history
- Feature availability
- Packaging state

Without runtime validation, lower rules risk misapplication.

---

Therefore:

Capability Discovery MUST be evaluated first.

Runtime Awareness MUST be evaluated second.

If discovery or runtime validation changes applicability, dependent rules must adapt accordingly.

---

## How This Index Is Used

Before generating or modifying code, AI MUST:

1. Read this index
2. Apply Governance Evaluation Order
3. Identify relevant rule documents
4. Load and apply only those rules
5. Refuse changes that violate an indexed rule
6. Never enforce rules that are not listed here

If no applicable rule exists:

- The AI MUST NOT invent one
- The AI SHOULD propose a governance rule addition instead

---

## Canonical Reference Source

The **Oqtane Framework source*- is the canonical implementation reference.

This includes, but is not limited to:

- `Oqtane.Client`
- `Oqtane.Server`
- `Oqtane.Shared`
- Built-in modules such as HtmlText and Admin
- Boilerplate Template
`.\Oqtane.Framework\Oqtane.Server\wwwroot\Modules\Templates\External\`

Canonical behavior is derived from **existing framework implementations**, not copied reference modules.

AI must:

- Observe
- Compare
- Conform

AI must NOT:

- Reinterpret
- Abstract
- Generalize beyond what exists

---

# Indexed Governance Rules

## Core Governance & Validation

- **027x-canonical-validation.md**
Enforces validation against Oqtane framework patterns and prohibits invention.
- **027x-capability-discovery.md**
Mandatory rule requiring AI to discover and reuse existing Oqtane capabilities before creating new functionality. Prevents duplication of base class features, state handling, services, and framework behavior.
- **027x-ai-instruction-discovery.md**
Governs how the AI should discover instructions.
- **027x-ai-decision-timeline.md**
Governs when and how AI decisions are recorded as binding memory.

---

## Runtime Awareness (Top Priority)

- **027x-runtime-awareness.md**
Mandatory rule requiring AI to validate actual environment state before generation.

AI must never assume:

- Framework version
- Database baseline state
- Migration execution history
- Feature availability
- Packaging state

Classification: Mandatory

---

## Architecture & Infrastructure

- **027x-execution-parity.md**
Ensures identical functional behavior across Blazor Server and WebAssembly hosting modes.  
Business logic must reside exclusively in ServerService.  
- **027x-migrations.md**
Database Migration Governance.  
Defines module migration versioning strategy using Option B baseline model, ReleaseVersion semantics, RevisionNumber tracking, strict 8 digit naming, EntityBuilder immutability, table naming conventions, ModelBase audit requirements, and AI enforcement behavior.  
Mandatory for all schema changes.  
- **027x-packaging-and-dependencies.md**
Governs runtime behavior, deployment correctness, NuGet packaging rules, static web asset handling, and staticwebassets path alignment.  
- **027x-multi-module-solution-pattern.md**
Defines a standardized pattern for structuring **multiple Oqtane modules within a single solution**, enabling scalability, separation of concerns, and maintainable growth.
- **027x-scheduled-jobs.md**
Rules for Oqtane Scheduled Jobs using HostedServiceBase.  
- **027x-sitetasks.md**
Runtime Aware Governance for Oqtane Site Tasks in version 10.1 and later.  
AI must verify Oqtane version is 10.1 or greater before implementation.  
When a user requests a Scheduled Job but the workload is user initiated and asynchronous, AI must evaluate and suggest Site Tasks instead.  
Site Tasks must not be implemented in Oqtane versions earlier than 10.1.  
- **027x-javascript-usage.md**
JavaScript must not be introduced unless a Blazor based C# solution is demonstrably insufficient.  
Opt in, not default.  
- **027x-caching.md**
Governance for caching implementations. Requires use of `CacheManager` in Oqtane 10.2+ and forbids direct use of `IMemoryCache` to ensure multi-tenant safety.

---

## Data & Domain Layer (Authoritative State & Integrity)

- **027x-framework-data-first.md**
Mandatory pre-build audit rule. Before designing or building any model, repository, controller, or service for a stated data need, AI MUST audit what the Oqtane framework already provides. Covers Oqtane.Shared models, framework services, and existing API endpoints. Building custom data infrastructure for data Oqtane already owns is a governance violation.

- **027x-repositories.md**
Repository responsibilities, boundaries, and prohibited logic.  
- **027x-structure-and-boundaries.md**
Enforces service mediated access and prohibits controllers from accessing repositories directly.  
- **027x-module-portability.md**
Mandatory when IPortable is implemented.  
Requires full state export and import.  
- **027x-data-identity-remapping.md**
Mandatory when hierarchical data exists.  
All primary keys must be remapped during import.
- **027x-data-integrity-boundaries.md**
Defines non-negotiable rules for preserving data integrity across all layers.  
Prevents trust leakage from client to server, enforces canonical representations, and prohibits mutation outside authoritative boundaries.  
Applies to all data entering or leaving the system.  
- **027x-datetime-handling.md**
Mandatory DateTime governance.  
All DateTime values must be stored and processed in UTC.  
Localization is a client concern only.  
Prevents ambiguity, timezone drift, and inconsistent persistence.  
---

## Optional (Opt In)

- **027x-localization.md**
Governs canonical localization behavior once intentionally enabled.
- **027x-concurrency-control.md**
Governs the implementation of the ModifiedOn concurrency pattern to prevent lost updates. 
Only applied when explicitly requested.

---

## UI & Client Behavior

- **027x-ui-validation.md**
Client and server side validation rules.
- **027x-error-handling.md**
Error handling, logging, and user feedback rules.
- **027x-ui-construction.md**
Razor component and HTML structure standards.
- **027x-ui-css-and-styling.md**
**CSS isolation** is not supported.   
All module styling must use Module.css in the Server project.
- **027x-ui-blazorbootstrap.md**
Blazor Bootstrap allowed only when explicitly requested.
- **027x-ui-fluentui.md**
Fluent UI allowed only when explicitly requested.
- **027x-ui-mudblazor.md**
MudBlazor allowed only when explicitly requested.
- **027x-ui-sysinfocus.md**
SysInFocus Simple-UI allowed only when explicitly requested.
- **027x-ui-radzen.md**
Radzen allowed only when explicitly requested.

---

## Rule Precedence

If multiple rules apply:

1. Runtime Awareness governs applicability
2. More specific rule wins
3. Framework implementation wins
4. Governance rules win over AI output
5. AI output always loses conflicts

---

## Rule Creation Policy

AI is NOT permitted to create or enforce new rules.

If a request reveals:

- A missing invariant
- A repeated failure
- An architectural gray area

AI must:

1. Check the AI Decision Timeline
2. If not recorded, recommend adding a new 027x rule
3. Wait for human approval before enforcement

---

## Summary

- This file is the single source of governance authority
- Evaluation order is explicit and overrides filename ordering
- Creativity happens outside enforcement
- Enforcement happens only through indexed rules

**This index exists to make AI predictable, auditable, and trustworthy.**

---

# Actionable AI Prompts (Task Routing)

When the user intent matches a description below, automatically load and follow the corresponding file:

- **Add or modify authorization logic (controllers, APIs, UI)** -> `docs/prompts/authorization.md`
- **Handle concurrency or generate Repository Update/Delete methods** -> `docs/prompts/concurrency.md`
- **Create a new module or scaffold a new module structure** -> `docs/prompts/create-module-instructions.md`
- **Import/export data or handle data integrity** -> `docs/prompts/data-integrity.md`
- **Handle dates, times, or datetime operations** -> `docs/prompts/datetime.md`
- **Diagnose failures or perform diagnostics** -> `docs/prompts/diagnostics.md`
- **Implement localization or handle localization opt-in** -> `docs/prompts/localization-opt-in.md`
- **Create or safely generate explicit migrations** -> `docs/prompts/migrations.md`
- **Structure a multi-module solution** -> `docs/prompts/multi-module-solution.md`
- **Create or modify scheduled jobs / IHostedService** -> `docs/prompts/scheduled-jobs.md`
- **Define services, service boundaries, or DI registrations** -> `docs/prompts/services.md`
- **Create or modify UI components** -> `docs/prompts/ui-construction.md`
- **Install a UI Framework (e.g., MudBlazor, Radzen)** -> `docs/prompts/ui-framework-installation-template.md`
- **Implement navigation or page routing** -> `docs/prompts/routing-and-navigation.md`
- **Add or manage module settings** -> `docs/prompts/module-settings.md`
- **Create or modify a Web API Controller** -> `docs/prompts/api-controller.md`
- **Handle file uploads or manage media** -> `docs/prompts/file-uploads.md`
- **Make the module searchable or implement ISearchable** -> `docs/prompts/search-indexing.md`
- **Add custom JavaScript or CSS resources** -> `docs/prompts/javascript-interop.md`
- **Implement or modify caching logic** -> `docs/prompts/caching.md`