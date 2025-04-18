# Contributing to Upmizer

Thank you for considering contributing to Upmizer! This document provides guidelines and instructions for contributing to this GitHub Action for Unity UPM package management.

## Code of Conduct

By participating in this project, you agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md). Please read it before contributing.

## How Can I Contribute?

### Reporting Bugs

Before submitting a bug report:

1. Check the [issue tracker](../../issues) to see if the problem has already been reported
2. Make sure you're using the latest version of the action

When submitting a bug report:

- Use a clear and descriptive title
- Describe the exact steps to reproduce the issue
- Provide specific examples (e.g., a link to your repository or a minimal test case)
- Include any error messages or logs
- Describe the behavior you expected

### Suggesting Features

Feature suggestions are tracked as GitHub issues:

1. Check if the feature has already been suggested
2. Provide a clear description of the feature and why it would be valuable
3. Include examples of how users would use the feature
4. Consider suggesting how to implement the feature

### Pull Requests

1. Fork the repo and create your branch from `main`
2. If you've added code that should be tested, add tests
3. Ensure your code follows the existing style
4. Update the documentation if needed
5. Submit your pull request

## Development Environment Setup

To set up your environment for developing Upmizer:

1. Fork and clone the repository
2. Make sure you have Git and Bash installed

### Testing Locally with Act

We use [Act](https://github.com/nektos/act) for local testing:

```bash
# Install Act
brew install act  # macOS with Homebrew
# or
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

# Run tests locally
act -j test
```

### Testing Environment Setup

Create a test Unity package structure to validate your changes:

```bash
# Create a test package structure
mkdir -p TestProject/Assets/TestPackage/Runtime
mkdir -p TestProject/Assets/TestPackage/Editor
mkdir -p TestProject/Assets/TestPackage/Samples
echo '{"name": "com.test.package"}' > TestProject/Assets/TestPackage/package.json

# Setup git repo for testing
cd TestProject
git init
git config user.name "Test User"
git config user.email "test@example.com"
git add .
git commit -m "Initial commit"

# Test the action
../path/to/your/action/script.sh
```

## Coding Style and Standards

- Follow the existing code style in the repository
- Keep shell scripts POSIX-compatible when possible
- Add comments to explain complex operations
- Use meaningful variable names
- Error handling should be robust

## Documentation

When contributing, please update the documentation accordingly:

- Update the README.md if you add or change features
- Document any new parameters in action.yml
- Add examples for significant new features

## Release Process

Maintainers follow this process for releases:

1. Update the version in appropriate files
2. Create and merge a release PR with the changes
3. Create a GitHub release with a tag matching the version
4. The GitHub Action will be available at the new tag

## Communication

- Use GitHub Issues for bug reports and feature requests
- For more complex discussions, open a discussion topic
- Significant changes should be first discussed in an issue

## License

By contributing to Upmizer, you agree that your contributions will be licensed under the project's [MIT License](../LICENSE).

Thank you for contributing to Upmizer!
