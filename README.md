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

## Outputs

| Name          | Description                                      |
| ------------- | ------------------------------------------------ |
| `new_version` | The new version that was applied to the package  |
| `upm_branch`  | The UPM branch that was created/updated          |

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
4. If a Documentation directory exists, it renames it to "Documentation~" as required by UPM guidelines
5. Applies versioning to the package based on the selected strategy
6. Creates a git tag for the new version
7. Pushes the UPM branch and tag to your repository
8. If the publish flag is set to true, it publishes the package to the specified registry

## Versioning Strategies

Upmizer supports three versioning strategies to manage your package versions:

### Semantic Versioning (Default)

**Strategy**: `semantic`

Automatically determines the version bump type by analyzing conventional commits since the last version.

**How it works:**
- Analyzes git commits since the last version tag
- Determines bump type based on [Conventional Commits](https://www.conventionalcommits.org/):
  - **BREAKING CHANGE** or `feat!:` → **major** bump (1.0.0 → 2.0.0)
  - `feat:` → **minor** bump (1.0.0 → 1.1.0)
  - `fix:`, `chore:`, `docs:`, etc. → **patch** bump (1.0.0 → 1.0.1)
- Updates `package.json` with the new version
- Creates a git tag (e.g., `v1.1.0`)

**Examples:**
```
Commits since v1.0.0:
- feat: add new feature       → Result: 1.1.0 (minor)
- fix: resolve bug            → Result: 1.0.1 (patch)
- feat!: breaking change      → Result: 2.0.0 (major)
```

**Best for:** Projects using conventional commits for automated semantic versioning.

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "semantic"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

### Tag-Based Versioning

**Strategy**: `tag`

Uses the latest git tag in your repository as the package version.

- Latest tag: `v2.1.0` → Package version: `2.1.0`
- Latest tag: `1.5.3` → Package version: `1.5.3`

**How it works:**
- Searches for the latest git tag matching semantic versioning pattern
- Strips the `v` prefix if present
- Uses that version for the package
- If no valid tag is found, uses the current version from `package.json`

**Best for:** Release workflows where you create git tags manually for releases.

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "tag"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

### Custom Versioning

**Strategy**: `custom`

Allows you to specify an exact version number.

**How it works:**
- Uses the version specified in the `version` input
- Validates the version format (must follow semver: `major.minor.patch`)
- Updates `package.json` with the specified version
- Creates a git tag with the version

**Best for:** Manual control over version numbers or complex versioning workflows.

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "custom"
    version: "2.0.0"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

**Important:** When using `custom` versioning, you must provide the `version` input with a valid semver version (e.g., `1.0.0`, `2.1.3`, `0.5.0-beta.1`).

### Version Validation

All versioning strategies validate that versions follow the semantic versioning specification:

- Valid: `1.0.0`, `2.3.5`, `0.1.0`, `1.0.0-beta.1`, `2.0.0+build.123`
- Invalid: `1.0`, `v1.0.0`, `1.0.0.0`, `latest`, `1.x.x`

If validation fails, the action will stop with a clear error message.

### Conventional Commits Guide

When using `versioning: semantic`, the action follows the [Conventional Commits](https://www.conventionalcommits.org/) specification:

**Format:** `<type>[optional scope][optional !]: <description>`

**Bump Types:**

| Commit Format | Version Bump | Example |
|---------------|--------------|---------|
| `feat!: description`<br>`feat: description`<br>`BREAKING CHANGE:` in body | **Major** (1.0.0 → 2.0.0) | `feat!: redesign API` |
| `feat: description` | **Minor** (1.0.0 → 1.1.0) | `feat: add user profiles` |
| `fix: description`<br>`chore: description`<br>etc. | **Patch** (1.0.0 → 1.0.1) | `fix: resolve null pointer` |

**Supported types:** `feat`, `fix`, `chore`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`

**Examples:**
```bash
git commit -m "feat: add dark mode support"          # → Minor bump
git commit -m "fix: resolve login issue"             # → Patch bump
git commit -m "feat!: change API authentication"     # → Major bump
git commit -m "chore: update dependencies"           # → Patch bump
```

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

### Example 3: Semantic Versioning (Conventional Commits)

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "semantic"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

This example demonstrates automatic semantic versioning. The action analyzes your commit history using conventional commits to determine the appropriate version bump:

- Commits with `feat!:` or `BREAKING CHANGE:` → major bump (2.0.0)
- Commits with `feat:` → minor bump (1.1.0)
- Commits with `fix:`, `chore:`, etc. → patch bump (1.0.1)

**Note:** Make sure your team uses [Conventional Commits](https://www.conventionalcommits.org/) format for this strategy to work optimally.

### Example 3b: Tag-Based Versioning

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "tag"
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

This example uses existing git tags as the version source. Perfect for release workflows where you manually create tags (e.g., `git tag v1.2.0`) before triggering the action.

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

### Example 5: Using Outputs

```yaml
- name: Create UPM Package
  id: upmizer
  uses: NoTaskStudios/upmizer@v1
  with:
    package_root: "Assets/MyPackage"
    versioning: "semantic"
    publish: true
    registry_url: "https://registry.npmjs.org/"
    registry_token: ${{ secrets.NPM_TOKEN }}
    github_token: ${{ secrets.GITHUB_TOKEN }}

- name: Display Results
  run: |
    echo "New version: ${{ steps.upmizer.outputs.new_version }}"
    echo "UPM branch: ${{ steps.upmizer.outputs.upm_branch }}"
    echo "Package published successfully!"
```

This example shows how to capture and use the action's outputs in subsequent workflow steps.

## Troubleshooting

### "package.json not found in UPM branch"

**Cause:** The specified `package_root` directory doesn't contain a `package.json` file.

**Solution:** Ensure your package root directory contains a valid Unity `package.json` file. See [Unity Package Layout](https://docs.unity3d.com/Manual/cus-layout.html) for details.

### "Invalid version format"

**Cause:** The version number doesn't follow semantic versioning format.

**Solution:** Ensure versions follow the `major.minor.patch` format (e.g., `1.0.0`, `2.3.5`). Pre-release and build metadata are supported (e.g., `1.0.0-beta.1`, `2.0.0+build.123`).

### "Custom version not provided or is default (0.0.0)"

**Cause:** Using `versioning: custom` without specifying a version, or specifying `0.0.0`.

**Solution:** When using custom versioning, always provide a valid version:

```yaml
versioning: "custom"
version: "1.2.3"  # Must be provided and != 0.0.0
```

### "No valid git tags found"

**Cause:** Using `versioning: tag` but no git tags exist in the repository.

**Solution:** The action will fall back to the current version in `package.json`. To use tag-based versioning, create a git tag first:

```bash
git tag v1.0.0
git push origin v1.0.0
```

### Permission Denied Errors

**Cause:** The GitHub token doesn't have sufficient permissions.

**Solution:** Ensure your workflow has the correct permissions:

```yaml
permissions:
  contents: write
  packages: write  # Only needed for publishing
```

### "No conventional commits found → defaulting to patch bump"

**Cause:** Using `versioning: semantic` but commits don't follow conventional commit format.

**Solution:** This is a warning, not an error. The action will default to patch bump. To get proper semantic versioning:

1. Start using conventional commits (see Conventional Commits Guide above)
2. Or switch to `versioning: tag` or `versioning: custom` if you prefer manual control

## Notes

- Make sure your package contains a valid `package.json` file with a `version` field
- The GitHub token requires write permissions to your repository
- All version numbers must follow semantic versioning format (`major.minor.patch`)
- Git tags are automatically created for each version (e.g., `v1.2.3`)
- The action validates all inputs and will fail fast with clear error messages if something is wrong
- Force push is used by default to update the UPM branch - ensure this is acceptable for your workflow
