# Security Policy

## Reporting a Vulnerability

The Upmizer team takes security issues seriously. We appreciate your efforts to responsibly disclose your findings and will make every effort to acknowledge your contributions.

To report a security vulnerability, please use the GitHub Security Advisory ["Report a Vulnerability"](../../security/advisories/new) tab.

Please include:

- Description of the vulnerability
- Steps to reproduce the issue
- Potential impact of the vulnerability
- Any possible mitigations you've identified

## What to Expect

After you have submitted your report:

1. We will acknowledge receipt of your vulnerability report within 3 business days
2. We will assign a primary handler to investigate the issue
3. We will keep you informed of our progress throughout the process
4. We will notify you when the issue is fixed

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| 1.x.x   | :white_check_mark: |
| < 1.0.0 | :x:                |

Only the latest major version will receive security updates.

## Security Considerations

### GitHub Token Permissions

Upmizer requires GitHub token access to:

- Create and modify branches
- Push changes to your repository

We recommend using the default `GITHUB_TOKEN` provided by GitHub Actions which has limited scope and automatically expires.

### Local Testing

When testing Upmizer locally with act:

- Use a test token with minimal permissions
- Do not use your personal access token with unnecessary scopes
- Avoid exposing secrets in test repositories

## Best Practices for Users

When using Upmizer in your projects:

1. Always pin to a specific version (e.g., `NoTaskStudios/Upmizer@v1.0.0`) rather than a branch
2. Regularly update to the latest version to receive security fixes
3. Review changes in the action before updating in production workflows
4. Keep your GitHub Actions workflow permissions to the minimum required

## Disclosure Policy

- We follow a coordinated disclosure process
- Security issues will be announced via GitHub Security Advisories
- After fixes are available, advisories will be published with full details
- CVEs will be requested for significant vulnerabilities
