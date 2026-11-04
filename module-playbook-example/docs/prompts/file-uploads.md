# AI Instruction: File Uploads & Media Management

**Instruction Context**: Load this file when the user asks to use files, upload media, or create a storage backend. For a backend extension, also load `docs/prompts/folder-storage-providers.md` and the indexed `027x-folder-storage-providers.md` rule.

## First identify the task and framework version

- For module features that consume files/folders, use Oqtane's `IFileService`, `IFolderService`, and built-in `FileManager` where appropriate.
- For a request to add a folder storage backend, follow the version-aware provider rule. `IStorageProvider` is available starting with Oqtane 10.3.0.
- Before 10.3.0, do not emit `IStorageProvider`, `StorageProviderType`, or its settings-control API. Use only the actual target version's folder APIs and explain if the requested backend is unavailable.
- Do not treat implementing `IStorageProvider` as permission to create a parallel file service or bypass Oqtane authorization.

## Oqtane File Management Rules

Standard ASP.NET Core `IFormFile` saving directly to `wwwroot` is prohibited. Module file interactions MUST use Oqtane's integrated file management system so framework tenant isolation, permissions, and file behavior remain in effect.

### 1. Use Core Services

For module media interactions, inject and use `IFileService` and `IFolderService`:

```csharp
[Inject] protected IFileService FileService { get; set; }
[Inject] protected IFolderService FolderService { get; set; }
```

These consumer APIs remain the correct path when a custom storage provider is installed.

### 2. Folder Resolution

Resolve or create the destination folder through `FolderService`. Files belong to folders, which belong to sites (tenants).

```csharp
var folder = await FolderService.GetFolderAsync(PageState.Site.SiteId, "MyModule/Uploads");
```

### 3. Uploading Files

Use the built-in `FileManager` when possible. When processing bytes manually through `InputFile` or an API, use `FileService.UploadFileAsync()` or the appropriate Oqtane file-service method. Never write module uploads directly to `wwwroot` or bypass `IFileService`.

### 4. File Output

Use the `FileUrl()` helper or Oqtane's standardized `/api/file/download/{id}` route. Do not build a separate download endpoint that skips framework authorization.

### 5. Storage Backend Extension (10.3.0+ only)

Only when explicitly asked to add a backend, implement the framework's `IStorageProvider` and follow `docs/prompts/folder-storage-providers.md`. The provider operates behind Oqtane's existing file/folder services. Do not implement provider code for an earlier target version; verify the version first.
