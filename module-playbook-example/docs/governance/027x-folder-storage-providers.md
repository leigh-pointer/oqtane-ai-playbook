# 027x - Folder Storage Providers (Version Aware)

## Purpose

This rule governs AI work that creates or changes Oqtane folder and file storage behavior. Framework version determines the supported extension point.

## Mandatory version check

Before generating storage code, inspect the target's `Oqtane.Shared.Constants.Version`, installed package version, and source when available. Do not infer the target version from a different checkout.

- **Before 10.3.0:** use only APIs present in that target. Do not reference `IStorageProvider`, `Folder.StorageProviderType`, or the 10.3 settings-control contract. Explain when a requested backend is not supported and propose upgrade or a separately approved version-specific design.
- **10.3.0+:** a new folder storage backend MUST use `IStorageProvider`. Module file access MUST continue through `IFileService`, `IFolderService`, and the built-in FileManager where appropriate.
- If the target version is unknown, AI MUST inspect evidence or ask before using version-specific APIs.

## Consumer vs provider boundary

Using file/folder content in a module is different from implementing a storage backend. The rule against duplicating file management does not prohibit a requested `IStorageProvider` on 10.3+; it prohibits bypassing Oqtane's file services/controllers or creating parallel upload/file APIs.

## Provider implementation (10.3+)

An `IStorageProvider` implementation MUST implement the full file and folder contract in `Oqtane.Shared/Interfaces/IStorageProvider.cs`, including streaming, exists/list/size, add/delete/copy/move and folder operations. It MUST handle cross-provider copy/move and define overwrite, missing-item, error, and stream ownership behavior.

Oqtane discovers and registers `IStorageProvider` implementations from loaded framework/module assemblies as scoped providers. AI MUST NOT add redundant provider registration. Any auxiliary service registration remains subject to the startup rules.

Provider choice is stored per folder using `Folder.StorageProviderType`; use the existing provider factory, folder API, and file/folder services. AI MUST NOT create a parallel registry, folder model, controller, or file service.

If provider settings are required, implement `IStorageProviderSettingsControl` in the Client assembly, expose its assembly-qualified type through `SettingControlType`, and read/write settings through Oqtane's folder settings APIs. Never expose credentials to client-visible settings or logs.

## Security and validation

Providers MUST preserve tenant/site isolation, constrain filesystem paths to an approved root, reject traversal, and maintain public/private access behavior. Authorization remains with Oqtane's file/folder services and controllers. Provider code MUST NOT create a public bypass for private files.

Required validation covers upload/download, list/size/exists, delete, folder CRUD, same-provider and cross-provider copy/move, synchronization/import flows where applicable, failures, and cross-site/tenant isolation.

## Legacy compatibility

`Folder.Type` / `FolderTypes` is the pre-10.3 model. Oqtane 10.3 deprecates it and migrates legacy Public/Private values to `StorageProviderType`. AI MUST NOT backport the new provider API by inventing compatibility types. Multi-version support requires version-appropriate code and packages validated against each framework target.
