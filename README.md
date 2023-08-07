# Generate-Code
Generate a C# Refit client interface and contracts from an OpenAPI specifications document using [Refitter](https://github.com/christianhelle/refitter).

## Inputs

### `openapi-file`
The path to the OpenAPI document (both JSON and YAML are supported). Defaults to `"openapi.json"` (i.e. a file in the current directory called openapi.json). Paths that do not start with `/` are assumed to be relative to the root of the repository.

### `openapi-url`
The URL to load the OpenAPI document from. If set, `openapi-file` will be ignored.

### `namespace`
The default namespace used for the generated types (default: `GeneratedCode`)

### `use-api-response`
Return `Task<IApiResponse<T>>` instead of `Task<T>`

### `cancellation-tokens`
Use cancellation tokens

### `multiple-interfaces`
Generate a Refit interface for each endpoint. May be one of `ByEndpoint`, `ByTag`

### `command-args`
Additional arguments to pass through to the [Refitter](https://github.com/christianhelle/refitter) CLI tool.

### `publish-artifacts`
Setting this will publish the generated code as C# files as build artifacts

### `output-filename`
No outputs are returned. The generated client is placed in the current directory and using the `output-filename` (Default: **`Output.cs`**) value. The output file contains both the Refit interface and the contract types used by the API


# Examples

### From a File

```yaml
jobs:
  smoke-test-url:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Use the action to generate a Refit client interface from a file
      # This produces a file called Output.cs as a build artifact
      - uses: refitter/generate-code@v1
        name: Generate Refit Client
        with:        
          openapi-file: openapi.json
          namespace: ChristianHelle.Examples.Petstore.v3
      
      # Do something with the generated client (like include it in an existing project)
      - run: Get-Content Output.cs | Write-Host
        shell: pwsh
```

### From a URL

```yaml
jobs:
  smoke-test-url:
    name: From URL
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      # Use the action to generate a Refit client interface from a URL
      # This produces a file called OutputFromUrl.cs as a build artifact
      - uses: refitter/generate-code@v1
        name: Generate Refit Client
        with:
          openapi-url: https://petstore3.swagger.io/api/v3/openapi.json
          namespace: ChristianHelle.Examples.Petstore.v3
          command-args: --no-logging
          output-filename: OutputFromUrl.cs
      
      # Do something with the generated client (like include it in an existing project)
      - run: Get-Content OutputFromUrl.cs | Write-Host
        shell: pwsh
```