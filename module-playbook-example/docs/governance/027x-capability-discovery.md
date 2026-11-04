# 027x - Capability Discovery (Non-Negotiable)

AI MUST exhaustively evaluate existing Oqtane and module capabilities before creating new functionality.

This rule exists to prevent duplication, architectural drift, and misalignment with the Oqtane framework.

---

## The Core Rule

> **Discover first.
> Reuse when possible.
> Create only when necessary.**

If functionality already exists, it MUST be used.

---

## Mandatory Discovery Order

If AI introduces new functionality without completing this discovery process, the output MUST be rejected.
Before introducing any new code, AI MUST evaluate:

### 1. Base Classes

-   `ModuleBase`
-   `ServerServiceBase`
-   Oqtane controller patterns

AI MUST check for:

-   Built-in behaviors
-   Existing lifecycle handling
-   Logging and messaging support
-   State integration

---

### 2. State and Shared Models

-   `ModuleState`
-   `PageState`
-   `SiteState`
-   Existing Models

State objects MUST be used as the source of truth for UI-related data.
AI MUST NOT introduce new state structures if equivalent data already exists.

---

### 3. Framework Services

AI MUST evaluate existing Oqtane services including:

-   Authorization and permissions (`IUserPermissions`, etc.)
-   Logging (`ILogManager`)
-   Navigation (`INavigationManager`)
-   Localization (`ILocalizationService`, `IStringLocalizer`)
-   File and storage handling (`IFileService`, `IFolderService`)

Existing services MUST be used instead of custom implementations.
AI MUST NOT reinvent file management, logging, or authorization under any circumstances.

**Storage version distinction:** `IFileService` / `IFolderService` are the module-facing file APIs. When a user explicitly requests a storage backend, AI MUST also check the target version and apply `027x-folder-storage-providers.md`: Oqtane 10.3.0+ provides `IStorageProvider`; earlier versions do not. The prohibition on parallel file systems does not prohibit this framework extension on supported versions.

---

### 4. Existing Module Patterns and Built-in Modules

AI MUST review the Oqtane ecosystem for existing solutions:

-   Boilerplate module template
-   Built-in core modules (e.g., FileManager, User Profile, Admin, HtmlText)
-   Established service and repository patterns

Patterns already used in the framework MUST be followed. 
AI MUST NOT build features that mirror the functionality of built-in Oqtane modules (such as building a custom file manager when Oqtane's FileManager already exists). If a feature requires file management, it MUST integrate with Oqtane's `IFileService` and `IFolderService` rather than recreating a file management UI or storage system.

---

### 5. Current Project Implementation

AI MUST inspect:

-   Registered services
-   Existing repositories
-   Existing domain models and entities
-   Previously implemented features

Duplicate implementations MUST NOT be introduced.

---

## Prohibited Behavior

AI MUST NOT:

-   Recreate functionality provided by base classes
-   Introduce duplicate state containers
-   Bypassing state objects by calling services directly from the client when state is available
-   Replace Oqtane services with custom abstractions
-   Create parallel logging, authorization, or navigation systems
-   Mirror or recreate the functionality of built-in Oqtane modules (e.g., FileManager, User Profile)
-   Duplicate DTOs or models that already exist
-   Introduce new patterns that bypass established framework behavior

---

## Required Justification for New Functionality

AI MUST NOT reinvent anything Oqtane can already do. If AI determines that new functionality is required because a true capability gap exists in the framework, it MUST:

-   Confirm that no existing Oqtane capability satisfies the requirement
-   Identify what was evaluated within the framework
-   Explain why reuse is not possible and detail the exact gap
-   **Report this gap explicitly to the user**, as it could be a candidate for a future Framework Pull Request (PR)
-   Align any new implementation with Oqtane patterns

Absence of explicit justification and reporting of the gap is considered a violation.

---

## Common Failure Patterns (Reject on Sight)

The following patterns indicate a violation:

-   Creating custom logging abstractions
-   Replacing `ModuleState`,`PageState`,`SiteState` with alternative state handling
-   Writing services that duplicate `ServerServiceBase`
-   Implementing custom authorization logic
-   Mirroring built-in module features (e.g., building a custom file manager instead of using `IFileService`/`IFolderService`)
-   Introducing duplicate DTOs in Shared
-   Ignoring existing service registrations

---

## Enforcement Outcome

If an existing capability satisfies the requirement:

-   AI MUST reuse it
-   AI MUST NOT extend it unnecessarily
-   AI MUST NOT wrap it without clear justification

---

## Relationship to Other Rules

-   Must be evaluated before runtime awareness to ensure correct environment evaluation
-   Supports canonical validation by enforcing framework alignment
-   Reinforces client and server responsibility boundaries
-   Protects data integrity by preventing parallel implementations

---

## Summary

-   Oqtane already provides most required functionality
-   Duplication is a governance failure, not an optimization
-   Discovery is mandatory, not optional

If AI creates before it discovers, the output MUST be rejected.