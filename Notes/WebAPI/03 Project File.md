# 03. Project File

## Key elements

1. **`<TargetFramework>`**: typically set to `net6.0` and indicates which .NET runtime and SDK the project targets.
2. **`<Nullable>`**: when set to `enable` turns on nullable reference types to help catch nullability issues at compile time.
3. **`<ItemGroup>`**: container for NuGet package references (for example, EF Core, Swashbuckle, logging libraries).

## Example .csproj

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net6.0</TargetFramework>
    <Nullable>enable</Nullable> <!-- Enables nullable reference types -->
  </PropertyGroup>

  <ItemGroup>
    <!-- NuGet packages -->
    <PackageReference Include="Microsoft.AspNetCore.Mvc.NewtonsoftJson" Version="6.0.1" />
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="6.0.1" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.1.4" />
  </ItemGroup>

</Project>
```

## How to upgrade an existing project to a new .NET version

1. Change the target framework: open the `.csproj` file and update the `<TargetFramework>` tag (for example, to `net6.0`).
2. Update dependencies: ensure NuGet packages are compatible with the new target and update versions where necessary (e.g., EF Core, Swashbuckle).
3. Upgrade the SDK: install or use the appropriate .NET SDK (e.g., .NET 6) on your development machine and CI.

---
