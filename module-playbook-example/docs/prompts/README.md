# AI Prompt Library (Module-Scoped)

## Purpose

This folder contains **approved, reusable AI prompts** for this module.

Prompts exist to:

- Reduce ambiguity when working with AI tools
- Ensure governance rules are consistently applied
- Prevent accidental deviation from Oqtane and module constraints
- Make AI behavior predictable and repeatable

These prompts are **operational tools**, not documentation.

---

## Available Prompts

- `file-uploads.md`: Use Oqtane file/folder services for module uploads and media.
- `folder-storage-providers.md`: Implement a custom folder storage backend on Oqtane 10.3.0+ after verifying the target version.

- `authorization.md`: For authorization logic (controllers, APIs, UI).
- `concurrency.md`: For generating Repository Update/Delete methods properly.
- `create-module-instructions.md`: Scaffold rules for AI to create new modules.
- `data-integrity.md`: For importing/exporting data and external mapping.
- `datetime.md`: For handling date and time data safely.
- `diagnostics.md`: For diagnosing failures explicitly.
- `localization-opt-in.md`: To force canonical localization behavior.
- `migrations.md`: For safely generating structured explicit migrations.
- `multi-module-solution.md`: For structuring multiple Oqtane modules within a single solution.
- `scheduled-jobs.md`: For IHostedService/background jobs.
- `services.md`: For service boundary decisions and DI registrations.
- `ui-construction.md`: Core rules for any UI component creation.
- `ui-framework-installation-template.md`: For installing encapsulated packages.

---

## Why Prompts Are Module-Scoped

AI tools only respect **visible, local context**.

Therefore:

- Prompts **must live in the module repository**
- Prompts **must reflect this module�s governance rules**
- Prompts **may evolve independently per module**

The Oqtane AI Playbook defines *how prompts should be used*,

but **does not ship prompts itself**.

---

## Relationship to Governance

Prompts do **not override governance**.

All prompts must comply with:

- `.github/copilot-instructions.md`
- `docs/governance/027-rules-index.md`
- All applicable `027x-*` rule documents
- `docs/ai-decision-timeline.md`

If a prompt conflicts with a governance rule, **the rule wins**.

---

## When to Use a Prompt

Use a prompt when:

- Creating UI components
- Adding services or repositories
- Writing migrations
- Performing refactors
- Debugging complex issues
- Asking AI to reason about correctness or conformance

Prompts are especially important when:

- The request is non-trivial
- The outcome affects architecture
- The AI has previously made mistakes in this area

---

## Prompt Usage Pattern (Recommended)

Before issuing a prompt to AI:

1. Ensure governance files are visible in the solution
2. Refresh AI context if files were recently added or changed
3. Reference the prompt explicitly

Example:

> 
> 
> Use the canonical UI prompt from `docs/prompts/ui.md`.
> 
> Follow all applicable 027x governance rules.
> 
> Check the AI Decision Timeline before responding.
> 

---

## Prompt Structure (Recommended)

Each prompt file should contain:

- **Purpose** what this prompt is for
- **Required context** which rules apply
- **Explicit constraints** what AI must not do
- **Invocation example** how to use the prompt

Prompts should be:

- Short
- Explicit
- Deterministic
- Reusable

---

## Adding or Modifying Prompts

Prompts may be added or updated when:

- A repeated AI failure is observed
- A governance rule needs stronger reinforcement
- A workflow becomes common or error-prone

If a prompt reveals a **new invariant or repeated failure**,

consider recording it in `docs/ai-decision-timeline.md`.

---

## Important Reminder

Prompts improve consistency.

Governance enforces correctness.

If unsure:

- Prefer rejection over invention
- Prefer explicit prompts over vague requests
- Prefer governance over convenience

---

This prompt library exists to make AI a **trusted assistant**,

not an architectural decision-maker.