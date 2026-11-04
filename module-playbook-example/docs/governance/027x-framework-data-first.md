# Oqtane Framework Data-First Rule (Enforced)

AI MUST audit what the Oqtane framework already provides for any data need
before designing or building models, repositories, controllers, or services.

Building custom data infrastructure for data that Oqtane already owns and
exposes is a governance violation.

---

## The Core Rule

> **Identify the data need.**
> **Check the framework first.**
> **Build only what does not already exist.**

If Oqtane already provides a model, service, or API for the data required,
that existing infrastructure MUST be used.

---

## Mandatory Pre-Build Audit

Before producing any model, repository, controller, or service for a stated
data need, AI MUST complete this audit in order.

### Step 1: Identify the Domain

Name the domain the data need belongs to.

Examples:

- Users and identity
- Roles and permissions
- Sites and tenants
- Pages and modules
- Files and folders
- Settings and configuration
- Notifications and alerts
- Scheduled jobs

---

### Step 2: Check Oqtane.Shared for Existing Models

AI MUST inspect `Oqtane.Shared` for existing models before declaring a
new model is required.

Known Oqtane framework models include but are not limited to:

| Domain | Oqtane Model |
|---|---|
| Users | `Oqtane.Models.User` |
| Roles | `Oqtane.Models.Role` |
| User Roles | `Oqtane.Models.UserRole` |
| Sites | `Oqtane.Models.Site` |
| Pages | `Oqtane.Models.Page` |
| Modules | `Oqtane.Models.Module` |
| Files | `Oqtane.Models.File` |
| Folders | `Oqtane.Models.Folder` |
| Settings | `Oqtane.Models.Setting` |
| Notifications | `Oqtane.Models.Notification` |
| Jobs | `Oqtane.Models.Job` |
| Languages | `Oqtane.Models.Language` |
| Profiles | `Oqtane.Models.Profile` |

If the required data maps to an existing model, that model MUST be used.
AI MUST NOT create a duplicate or shadow model for the same domain.

---

### Step 3: Check Oqtane Services for Existing Access

AI MUST inspect the Oqtane service layer before declaring a new service
is required.

Known Oqtane framework services include but are not limited to:

| Domain | Service Interface |
|---|---|
| Users | `IUserService` |
| Roles | `IRoleService` |
| User Roles | `IUserRoleService` |
| Sites | `ISiteService` |
| Pages | `IPageService` |
| Modules | `IModuleService` |
| Files | `IFileService` |
| Folders | `IFolderService` |
| Folder storage backend (Oqtane 10.3.0+) | `IStorageProvider` (version-gated; see `027x-folder-storage-providers.md`) |
| Settings | `ISettingService` |
| Notifications | `INotificationService` |
| Jobs | `IJobService` |
| Languages | `ILanguageService` |
| Profiles | `IProfileService` |

If the data can be retrieved or mutated through an existing Oqtane service,
that service MUST be used. AI MUST NOT create a parallel service for the
same domain.

---

### Step 4: Check the Oqtane API Surface

AI MUST verify whether an existing Oqtane controller already exposes the
required data over HTTP before declaring a new controller is required.

Oqtane ships controllers for all framework domains listed above.
These controllers follow the pattern:

`/api/{Domain}/{id}` or `/api/{Domain}?siteid={siteid}`

If an existing Oqtane API endpoint satisfies the data need, a new controller
MUST NOT be created.

---

### Step 5: Confirm the Gap Before Building

Only after completing Steps 1 through 4 may AI proceed to build new
data infrastructure.

Before building, AI MUST explicitly state:

- What domain data is required
- What was found in `Oqtane.Shared`
- What was found in the Oqtane service layer
- What was found in the Oqtane API surface
- Why none of the existing capabilities satisfy the requirement

Absence of this justification is a violation. The output MUST be rejected.

---

## Prohibited Behavior

AI MUST NOT:

- Create a `User` model when `Oqtane.Models.User` exists
- Create a `UserService` or `UserRepository` when `IUserService` exists
- Create a `/api/users` controller when Oqtane already exposes one
- Duplicate any framework model, service, or API without explicit justification
- Proceed directly to code generation when a data need is stated
- Treat a data need as a build task before the audit is complete

---

## Common Violation Patterns (Reject on Sight)

The following patterns indicate a violation of this rule:

- Generating a `Models/User.cs` in a module project for user data
- Creating a `UserRepository.cs` that queries the users table directly
- Writing a `UserController.cs` that duplicates Oqtane user endpoints
- Adding a `CustomUserService.cs` that wraps `IUserService` without purpose
- Building any data layer artifact before stating what the audit found

---

## When Custom Data Infrastructure Is Legitimate

Custom models, repositories, controllers, and services are legitimate only
when the data is:

- Owned by the module (not by Oqtane core)
- Not represented by any existing Oqtane model or service
- Not accessible through any existing Oqtane API

Module-specific entities such as calendar events, documents, or orders are
legitimate candidates for custom infrastructure.

References to Oqtane-owned entities (such as `UserId`, `SiteId`, `PageId`)
MUST use the framework types and services, not custom duplicates.

---

## Audit Checklist

An implementation is valid only if:

- The data domain was identified before any code was generated
- `Oqtane.Shared` was inspected for existing models
- The Oqtane service layer was inspected for existing services
- The Oqtane API surface was inspected for existing endpoints
- A gap was confirmed with explicit justification before building

If any check was skipped, **reject the output**.

---

## Relationship to Other Rules

- Extends `027x-capability-discovery.md` with specific enforcement for data needs
- Reinforces `027x-canonical-framework.md` by treating Oqtane data ownership as authoritative
- Supports `027x-data-integrity-boundaries.md` by preventing parallel data infrastructure
- Supports `027x-structure-and-boundaries.md` by preventing controller and service duplication

---

## Summary

- Oqtane owns core domain data. Modules do not re-own it.
- Discovering what exists is mandatory before any data layer is designed.
- Building before auditing is a governance failure, not a shortcut.

If AI designs or builds data infrastructure before completing the framework
data audit, the output MUST be rejected.
