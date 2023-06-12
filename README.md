# Refitter Github Action
Generate a Refit client interface and contracts from an OpenAPI specifications document using [Refitter](https://github.com/christianhelle/refitter).

## Inputs

### `openapi-file`
The path to the OpenAPI document (both JSON and YAML are supported). Defaults to "openapi.json" (i.e. a file in the current directory called openapi.json). Paths that do not start with `/` are assumed to be relative to the root of the repository.

### `openapi-url`
The URL to load the OpenAPI document from. If set, `openapi-file` will be ignored.

### `namespace`
The default namespace used for the generated types (default: `GeneratedCode`)

### `use-api-response`
Return `Task<IApiResponse<T>>` instead of `Task<T>`

### `cancellation-tokens`
Use cancellation tokens

### `command-args`
Additional arguments to pass through to the [Refitter](https://github.com/christianhelle/refitter) CLI tool.

### `publish-artifacts`
Setting this will publish the generated code as C# files as build artifacts

### `client-sdk`
Setting this will package the generated code into a NuGet package and publish this as a build artifact. The following inputs are ***Required*** when `client-sdk` is enabled

- ### `client-sdk-version` - The version number used for the NuGet package (default: `1.0.${{ github.run_number }}`)
- ### `client-sdk-target-framework` - The target framework used in the generated Client SDK (default: `net6.0`)
- ### `client-sdk-package-id` - The value used as `<PackageId>` for package the generated code into a NuGet package
- ### `client-sdk-title` - The value used as `<Title>` for package the generated code into a NuGet package
- ### `client-sdk-root-namespace` - The value used as `<RootNamespace>` for package the generated code into - a NuGet package
- ### `client-sdk-assembly` - The value used as `<AssemblyName>` for package the generated code into a - NuGet package
- ### `client-sdk-authors` - The value used as `<Authors>` for package the generated code into a NuGet - package
- ### `client-sdk-company` - The value used as `<Company>` for package the generated code into a NuGet - package
- ### `client-sdk-product` - The value used as `<Product>` for package the generated code into a NuGet - package
- ### `client-sdk-description` - The value used as `<Description>` for package the generated code into a - NuGet package
- ### `client-sdk-license` - The value used as `<PackageLicenseExpression>` for package the generated code - into a NuGet package
- ### `client-sdk-project-url` - The value used as `<PackageProjectUrl>` and `<RepositoryUrl>` for package - the generated code into a NuGet package
- ### `client-sdk-repository-type` - The value used as `<RepositoryType>` for package the generated code into a NuGet package

## Outputs
No outputs are returned. The generated client is placed in the current directory and called **Output.cs** which contains both the Refit interface and the contract types used by the API


# Examples

### Using a File for OpenAPI specifications document

```yaml
jobs:
  smoke-test-url:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Use the action to generate a Refit client interface
      # This produces a file called Output.cs as a build artifact
      - uses: christianhelle/refitter-action@main
        name: Generate Refit Client
        with:        
          openapi-file: openapi.json
          namespace: ChristianHelle.Examples.Petstore.v3
      
      # Do something with the generated client (like include it in an existing project)
      - run: Get-Content Output.cs | Write-Host
        shell: pwsh
```

### Using a URL and producing a Client SDK NuGet package

```yaml
jobs:
  smoke-test-url-with-client-sdk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Use the action to generate a Refit client interface
      # This produces a NuGet package as a build artifact
      - uses: christianhelle/refitter-action@main
        name: Generate Refit Client SDK
        with:
          openapi-url: https://petstore3.swagger.io/api/v3/openapi.json
          namespace: ChristianHelle.Examples.Petstore.v3
          client-sdk: true
          client-sdk-version: 3.0.${{ github.run_number }}
          client-sdk-package-id: ChristianHelle.Examples.Petstore.v3
          client-sdk-title: ChristianHelle.Examples.Petstore.v3
          client-sdk-root-namespace: ChristianHelle.Examples.Petstore.v3
          client-sdk-assembly: ChristianHelle.Examples.Petstore.v3
          client-sdk-product: ChristianHelle.Examples.Petstore.v3
          client-sdk-authors: Christian Resma Helle
          client-sdk-company: Christian Resma Helle
          client-sdk-description: Example generated code using Refitter and the Swagger Petstore v3 example OpenAPI specifications
          client-sdk-project-url: https://github.com/christianhelle/refitter-action
```