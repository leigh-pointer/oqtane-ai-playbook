# AI Prompt: Create an Oqtane Folder Storage Provider

## Purpose

Use this prompt only when asked to add or modify a backend that stores Oqtane folder files.

## Required context

1. Read `docs/governance/027-rules-index.md` and `027x-folder-storage-providers.md`.
2. Verify the actual target Oqtane version from source/packages. Do not infer it from another checkout.
3. Inspect the target `IStorageProvider`, its default implementations, provider discovery, folder settings, and file/folder services before proposing code.

## Version constraints

- Oqtane 10.3.0+: implement `IStorageProvider` and preserve Oqtane's service/controller path for consumers.
- Earlier versions: do not use the 10.3 provider contract. Explain the version limitation and use only APIs proven to exist in that target.
- Unknown version: stop and ask or inspect evidence.

## Implementation requirements (10.3.0+)

- Implement every `IStorageProvider` file and folder operation.
- Reuse/subclass a framework provider only when its semantics fit the requested backend; otherwise implement the contract directly.
- Rely on Oqtane's loaded-assembly discovery. Do not manually register the provider again.
- If settings UI is needed, implement `IStorageProviderSettingsControl` in Client and set `SettingControlType` to its assembly-qualified component name. Persist settings with Oqtane folder settings APIs; do not expose secrets in Client or logs.
- Preserve site/tenant boundaries, authorization, private/public behavior, path safety, streaming, and cross-provider copy/move semantics.
- Do not add a duplicate file service, folder model, controller, provider registry, or direct-write module upload path.

## Validation checklist

Cover upload, download, exists, listing, size, overwrite, delete, folder create/list/move/delete, copy/move within and across providers, synchronization/import flows that apply, failure behavior, and isolation across sites/tenants. Report any framework behavior the provider cannot support.

## Invocation example

> Create a storage provider for the verified Oqtane 10.3+ target. Follow the folder-storage-provider prompt and indexed rule. Inspect the provider contract and discovery behavior first, then implement all required operations and validate tenant isolation and cross-provider moves.
