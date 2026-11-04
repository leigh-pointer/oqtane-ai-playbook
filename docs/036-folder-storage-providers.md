# Oqtane Folder Storage Providers

## Purpose

This chapter explains how AI must choose the correct Oqtane folder and file storage pattern for the framework version in the actual target solution.

## Version gate (mandatory)

Before designing storage code, inspect the target framework version from `Oqtane.Shared.Constants.Version`, installed package versions, and the loaded framework source when available. Do not assume that the checked-out 10.3 source is the version used by every application.

| Target Oqtane version | Required approach |
|---|---|
| Earlier than 10.3.0 | Use only the folder/file APIs and `Folder.Type` behavior supported by that version. Do not generate `IStorageProvider`, `StorageProviderType`, or 10.3 settings-control code. If the requested backend is unsupported, explain that limitation and propose upgrading or a separately approved version-specific design. |
| 10.3.0 and later | For a new folder storage backend, implement the framework `IStorageProvider` extension point. Keep module file interactions on `IFileService` and `IFolderService`. |

If the version cannot be verified, stop before using version-specific APIs and ask for the target version or inspect the project/package/source evidence.

## Keep the two responsibilities distinct

- **Module consumes files/folders:** use `IFileService`, `IFolderService`, and the built-in `FileManager` where appropriate. Do not write uploads directly to `wwwroot` or bypass Oqtane file authorization.
- **Framework storage backend:** only when the request is to add a storage backend, implement `IStorageProvider` for Oqtane 10.3+. This is an Oqtane extension, not a parallel module file-management system.

The prohibition on recreating file management does not prohibit a user-requested `IStorageProvider` implementation. It does prohibit custom upload APIs or file-management abstractions that bypass Oqtane's services and controllers.

## Oqtane 10.3+ extension contract

`IStorageProvider` is defined in `Oqtane.Shared/Interfaces/IStorageProvider.cs`. An implementation supplies a display name, optional settings-control type, folder initialization, and the complete file and folder API. Implement and validate every interface member, including list/exists/size/stream, add/delete/copy/move, and folder exists/create/list/move/delete operations. Handle cross-provider copy/move explicitly; do not assume both folders use the same backend.

The framework discovers `IStorageProvider` implementations from loaded Oqtane assemblies and registers them as scoped services. Do not add redundant provider registration through startup. Register auxiliary services through the applicable Oqtane startup extension only when they are not discovered by the provider scan.

Providers are selected per folder through `Folder.StorageProviderType`; the framework exposes available provider metadata through the folder API. Use the framework's provider factory and `IFileService` / `IFolderService` pathways. Do not add a competing provider registry, database model, controller, or file service.

If settings are required, implement `IStorageProviderSettingsControl` in the client assembly and return its assembly-qualified type via `SettingControlType`. Load and save provider settings using the folder settings APIs. Keep secrets out of source, client-visible settings, and ordinary logs; use the host's secure configuration mechanism for credentials.

## Security and behavior requirements

Provider implementations must preserve Oqtane's site/tenant boundaries and leave authorization decisions to the framework's file/folder services and controllers. They must not expose private content through public URLs. Validate provider configuration and constrain filesystem paths to an intended root; reject traversal and unsafe names. Avoid storing credentials in `Folder.Path` or client settings.

Use asynchronous stream operations and define ownership/disposal behavior. Preserve correct overwrite, size, missing-file, and error semantics. Test upload, download, listing, delete, copy/move within one provider and across providers, folder operations, synchronization/import paths where supported, and isolation between sites/tenants.

## Legacy behavior

`FolderTypes` / `Folder.Type` is the pre-10.3 model. Oqtane 10.3 marks folder types obsolete and migrates legacy `Public` and `Private` values to `StorageProviderType`. Do not backport the new provider contract to earlier targets by inventing compatibility types. When supporting multiple framework versions, produce separate version-appropriate implementations and packages, and validate each against its actual framework.
