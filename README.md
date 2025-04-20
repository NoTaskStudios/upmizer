# Upmizer GitHub Action

[![Version](https://img.shields.io/github/v/tag/NoTaskStudios/Upmizer?label=version)](https://github.com/NoTaskStudios/Upmizer/tags)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A GitHub Action that prepares your Unity package for UPM (Unity Package Manager) distribution by creating a dedicated UPM branch with the correct structure.

## Description

Upmizer automates the process of preparing Unity packages for distribution via the Unity Package Manager. It:

1. Creates a clean UPM branch containing only your package content
2. Correctly handles samples by moving them to the UPM-compliant "Samples~" directory
3. Pushes the UPM branch to your repository

## Inputs

| Name             | Description                                                                | Required | Default                       |
| ---------------- | -------------------------------------------------------------------------- | -------- | ----------------------------- |
| `upm_branch`     | The name of the branch that will be created for UPM distribution           | No       | `upm`                         |
| `package_root`   | Path to your package's root directory                                      | Yes      | -                             |
| `samples_root`   | Path to your samples directory                                             | No       | `Samples`                     |
| `docs_root`      | Path to your documentation directory                                       | No       | `Documentation`               |
| `publish`        | Flag to allow publishing the package to registry like npm or github        | No       | `false`                       |
| `registry_url`   | The URL of the registry to publish the package to                          | No       | `https://registry.npmjs.org/` |
| `registry_token` | Token for authenticating with the registry ( not needed when github)       | Yes      | -                             |
| `github_token`   | GitHub authentication token with repo permissions                          | Yes      | -                             |
| `versioning`     | Strategy enum to enable versioning for the package [semantic, tag, custom] | Yes      | `semantic`                    |
| `version`        | The version of the package to publish when versioning is set to custom     | No       | -                             |

## Permissions

The action requires the following permissions to function correctly:

- `contents: write` - To create and push the UPM branch
- `packages: write` - To publish the package to the registry (if applicable)

## Usage

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    # You can specify a different branch name if needed
    # Default: upm
    upm_branch: "upm"

    # Required, path to your package's root directory
    package_root: "Assets/MyPackage"

    # Optional, path to your package's samples directory
    # Default: Samples
    samples_root: "CustomPathToSamples/Samples"

    # Optional, path to your package's documentation directory
    # Default: Documentation
    docs_root: "CustomPathToDocs/Documentation"

    # Optional, flag to allow publishing the package to registry like npm or github
    # Default: false
    publish: false

    # Optional, the URL of the registry to publish the package to
    # Default: https://registry.npmjs.org/
    registry_url: "https://registry.npmjs.org/"

    # Optional, token for authenticating with the registry (not needed when github)
    registry_token: ${{ secrets.REGISTRY_TOKEN }}

    # Optional, strategy enum to enable versioning for the package [semantic, tag, custom]
    # Default: semantic
    versioning: "semantic"

    # Optional, the version of the package to publish when versioning is set to custom
    version: "1.0.0"

    # Required, GitHub token with repo permissions
    # You can use the default GITHUB_TOKEN secret provided by GitHub Actions
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

## How It Works

1. The action extracts your package from the specified root directory using `git subtree`
2. It creates or updates the UPM branch with your package content
3. If a Samples directory exists, it renames it to "Samples~" as required by UPM guidelines
4. Finally, it pushes the UPM branch to your repository
5. If the publish flag is set to true, it publishes the package to the specified registry using the provided token
6. If versioning is enabled, it updates the package version in the `package.json` file according to the specified strategy (semantic, tag, or custom)
7. The action also handles versioning by updating the `package.json` file with the new version number

## Recommended Folder Structure

When developing Unity packages for UPM distribution, organizing your repository with the proper structure is essential. The following structure is recommended as it:

- Conforms to Unity's package layout standards
- Facilitates seamless UPM conversion
- Improves discoverability of package components
- Simplifies maintenance and versioning

```
<root>
  ├── README.md
  ├── Assets
  │   └── [YourPackageName]
  │         ├── package.json
  │         ├── Runtime
  │         │    ├── [YourPackageName].asmdef
  │         │    └── ...
  │         └── Samples
  │         │     ├── Sample 1
  │         │     ├── Sample 2
  │         │     ├── Sample 3
  │         │     └── ...
  │         └── Documentation
  │               ├── Doc 1.md
  │               ├── Doc 2.md
  │               ├── Doc 3.md
  │               └── ...
  ├── ...
```

## Examples

### Example 1: Basic Usage

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

This example demonstrates the basic usage of the Upmizer action. It creates a UPM branch from the specified package root directory and pushes it to the repository.

### Example 2: Publishing to NPM

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    publish: true
    registry_url: "https://registry.npmjs.org/"
    registry_token: ${{ secrets.NPM_TOKEN }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

This example shows how to publish the package to NPM after creating the UPM branch. The `publish` flag is set to `true`, and the registry URL and token are provided.

### Example 3: Versioning

```yaml
- uses: NoTaskStudios/upmizer@v1

    with:
      package_root: "Assets/MyPackage"
      versioning: "semantic"
      github_token: ${{ secrets.GITHUB_TOKEN }}
```

This example demonstrates how to enable versioning for the package. The `versioning` input is set to `semantic`, which will automatically update the package version based on semantic versioning rules.

### Example 4: Advanced Usage

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    # Package configuration options
    upm_branch: "upm"
    package_root: "Assets/MyPackage"
    samples_root: "CustomPathToSamples/Samples"
    docs_root: "CustomPathToDocs/Documentation"

    # Publishing to NPM
    publish: true
    registry_url: "https://registry.npmjs.org/"
    registry_token: ${{ secrets.NPM_TOKEN }}
    github_token: ${{ secrets.GITHUB_TOKEN }}

    # Custom versioning
    versioning: "custom"
    version: "1.0.1" # Updated version number
```

This example demonstrates advanced usage of the Upmizer action. It specifies custom paths for the samples and documentation directories, enables publishing to NPM, and sets a custom version number for the package.

## Notes

- Make sure your package contains a valid `package.json` file
- The GitHub token requires write permissions to your repository
