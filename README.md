# PetStore SDK Generation (Fern)

This repository contains a complete [Fern](https://buildwithfern.com) configuration that generates SDKs and a CLI from a single OpenAPI spec. Use it as a template to see how Fern turns an API definition into production-ready, published client libraries.

## What gets generated

Every push to `main` triggers GitHub Actions that call `fern generate` to produce:

| Output | Repository | Published to |
|--------|-----------|-------------|
| TypeScript SDK | [petstore-typescript-sdk](https://github.com/fern-demo/petstore-typescript-sdk) | npm (`@fern-api/example-typescript-sdk-petstore`) |
| Rust SDK | [petstore-rust-sdk](https://github.com/fern-demo/petstore-rust-sdk) | crates.io (`petstore`) |
| CLI | [petstore-cli](https://github.com/fern-demo/petstore-cli) | npm (`@fern-api/example-cli-petstore`) |

Additional generators for **Python**, **Go**, **Java**, **C#**, and **Ruby** are included in `generators.yml` as commented-out blocks — uncomment what you need.

## Repository structure

```
fern/
├── openapi.yml          # PetStore OpenAPI 3.0 spec (the source of truth)
├── generators.yml       # Fern generator config — defines which SDKs to generate
└── fern.config.json     # Fern project config (org + CLI version)

.github/workflows/
├── fern-check.yml       # Validates the spec on every PR
├── ts-sdk.yml           # Generates + publishes the TypeScript SDK
├── rust-sdk.yml         # Generates + publishes the Rust SDK
├── cli.yml              # Generates + publishes the CLI
├── python-sdk.yml       # Generates + publishes the Python SDK
├── go-sdk.yml           # Generates + publishes the Go SDK
├── java-sdk.yml         # Generates + publishes the Java SDK
├── csharp-sdk.yml       # Generates + publishes the C# SDK
└── ruby-sdk.yml         # Generates + publishes the Ruby SDK
```

## How it works

1. **Define your API** — Write or import an OpenAPI spec into `fern/openapi.yml`.
2. **Configure generators** — In `fern/generators.yml`, declare which SDKs and CLIs to generate, where to publish them, and which GitHub repos receive the generated code.
3. **Add a workflow** — Each generator group needs a GitHub Actions workflow that runs `fern generate --group <group-name>` on push to `main`. Workflow templates for every supported language are included in `.github/workflows/`.
4. **Push to `main`** — GitHub Actions run `fern generate`, which sends the spec to Fern's cloud, generates idiomatic client code, publishes to package registries, and opens a release PR on each SDK repository.

> **Note:** Generation requires a `FERN_TOKEN` tied to your [Fern](https://buildwithfern.com) organization. The token authenticates with Fern's generation service and controls access to paid features like auto-publishing, pagination, retries, and OAuth support.

## Adding a new language

To enable a new SDK (for example, Python):

1. **Uncomment the group** in `fern/generators.yml`:
   ```yaml
   python-sdk:
     generators:
       - name: fernapi/fern-python-sdk
         version: 5.14.8
         output:
           location: pypi
           package-name: petstore-sdk
         github:
           repository: your-org/petstore-python-sdk
           mode: release
         config:
           client:
             class_name: PetstoreClient
   ```
2. **Update the placeholders** — Set your `package-name`, `repository`, and `client` class name.
3. **Create the target GitHub repo** (e.g. `your-org/petstore-python-sdk`) and [install the Fern GitHub App](https://github.com/apps/fern-api) on it.
4. **Add `FERN_TOKEN`** — In your config repo's Settings → Secrets, add your Fern token. This is the only secret required — Fern handles registry authentication via OIDC.
5. **Push to `main`** — The matching workflow in `.github/workflows/python-sdk.yml` runs automatically.

## Use this as a template

1. **Fork this repo** (or copy the `fern/` folder into your own project).
2. Replace `fern/openapi.yml` with your own OpenAPI spec.
3. Update `generators.yml` — rename packages, point `github.repository` to your own repos, and uncomment the languages you want.
4. [Sign up for Fern](https://buildwithfern.com) and generate a token:
   ```bash
   npm install -g fern-api
   fern token
   ```
5. Add `FERN_TOKEN` as a GitHub Actions secret (Settings → Secrets → Actions). This is the only secret you need — Fern handles registry publishing via OIDC.
6. Push to `main` — your SDKs are generated and published automatically.

## Local development

Install the Fern CLI:

```bash
npm install -g fern-api
```

Validate the configuration:

```bash
fern check
```

Preview what would be generated (outputs to your local filesystem):

```bash
fern generate --group ts-sdk --local
```

## Learn more

- [Fern Docs — How SDKs work](https://buildwithfern.com/learn/sdks/overview/how-it-works)
- [Fern Docs — generators.yml reference](https://buildwithfern.com/learn/sdks/overview/generators-yml)
- [Fern Docs — Publishing to npm](https://buildwithfern.com/learn/sdks/generators/typescript/publishing)
- [Fern Docs — Publishing to PyPI](https://buildwithfern.com/learn/sdks/generators/python/publishing)
- [Fern Docs — Publishing as a Go module](https://buildwithfern.com/learn/sdks/generators/go/publishing)
- [Fern Docs — Publishing to Maven Central](https://buildwithfern.com/learn/sdks/generators/java/publishing)
- [Fern Docs — Self-hosted generation](https://buildwithfern.com/learn/sdks/deep-dives/self-hosted)
