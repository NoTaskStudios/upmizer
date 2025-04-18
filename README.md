# Upmizer GitHub Action

A GitHub Action that prepares your Unity package for UPM (Unity Package Manager) distribution by creating a dedicated UPM branch with the correct structure.

## Description

Upmizer automates the process of preparing Unity packages for distribution via the Unity Package Manager. It:

1. Creates a clean UPM branch containing only your package content
2. Correctly handles samples by moving them to the UPM-compliant "Samples~" directory
3. Pushes the UPM branch to your repository

## Inputs

| Name           | Description                                                      | Required | Default   |
| -------------- | ---------------------------------------------------------------- | -------- | --------- |
| `upm_branch`   | The name of the branch that will be created for UPM distribution | No       | `upm`     |
| `package_root` | Path to your package's root directory                            | Yes      | -         |
| `samples_root` | Path to your samples directory                                   | No       | `Samples` |
| `github_token` | GitHub authentication token with repo permissions                | Yes      | -         |

## Usage

```yaml
- uses: NoTaskStudios/upmizer@v1
  with:
    # You can specify a different branch name if needed
    # Default: upm
    upm_branch: "upm"

    # Required, path to your package's root directory
    package_root: "Assets/MyPackage"


    # Default: Samples
    samples_root: "Assets/MyPackage/Samples"

    # Required, GitHub token with repo permissions
    # You can use the default GITHUB_TOKEN secret provided by GitHub Actions
    github_token: ${{ secrets.GITHUB_TOKEN }}
```

## How It Works

1. The action extracts your package from the specified root directory using `git subtree`
2. It creates or updates the UPM branch with your package content
3. If a Samples directory exists, it renames it to "Samples~" as required by UPM guidelines
4. Finally, it pushes the UPM branch to your repository

## Notes

- Make sure your package contains a valid `package.json` file
- The GitHub token requires write permissions to your repository
