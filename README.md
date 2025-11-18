# plasmactl-publish

A [Launchr](https://github.com/launchrctl/launchr) plugin for [Plasmactl](https://github.com/plasmash/plasmactl) that publishes platform artifacts to remote repositories.

## Overview

`plasmactl-publish` uploads packaged platform artifacts (created by `plasmactl-package`) to artifact repositories for distribution and deployment. It supports multiple backends including Nexus, GitHub Releases, and other artifact management systems.

## Features

- **Artifact Upload**: Publish tar.gz archives to remote repositories
- **Credential Management**: Secure credential storage via keyring
- **Multiple Backends**: Support for various artifact repository systems
- **Automatic Discovery**: Detects local vs. remote repository URLs
- **Version Tracking**: Uses git commit SHAs for artifact identification

## Usage

### Basic Publish

```bash
plasmactl publish
```

This uploads the artifact created by `plasmactl-package` to the configured artifact repository.

### With Credentials

```bash
plasmactl publish --username myuser --password mypass
```

Credentials can also be stored in the keyring (recommended):

```bash
plasmactl keyring:set https://repository.example.com
```

## Workflow

Typical artifact publishing pipeline:

```bash
# 1. Package the platform
plasmactl package

# 2. Publish to artifact repository
plasmactl publish

# 3. Deploy from published artifact
plasmactl ship dev platform.interaction.observability
```

## Artifact Naming

Published artifacts follow the naming convention:
```
{repo-name}-{commit-sha}-plasma-src.tar.gz
```

Example:
```
pla-plasma-abc123d-plasma-src.tar.gz
```

## Repository Configuration

### Current Support

The plugin currently supports Nexus-compatible repositories and is being extended to support:

- **GitHub Releases** - For open-source distributions
- **GitHub Packages** - For container images and artifacts
- **Nexus Repository** - For enterprise artifact management
- **Artifactory** - For JFrog Artifactory users
- **GitLab Packages** - For GitLab-based workflows

**Note**: Multi-backend support is under development. See [OPEN_SOURCE_PLAN.md](https://github.com/plasmash/pla-plasma/.claude/OPEN_SOURCE_PLAN.md) for details.

### Repository URL

The plugin attempts to use internal repository URLs when available, falling back to external URLs:

```
Internal: http://repositories.interaction.svc.{platform}:8081
External: https://repositories.{domain}/repository/{repo-name}-artifacts/
```

## Credential Storage

### Using Keyring (Recommended)

Store credentials securely:

```bash
plasmactl keyring:set https://repositories.example.com
```

When prompted, enter your username and password.

### Inline Credentials

Pass credentials directly (less secure):

```bash
plasmactl publish --username user --password pass
```

## Artifact Location

### Source (Local)

```
.compose/artifacts/{repo-name}-{commit-sha}-plasma-src.tar.gz
```

### Destination (Remote)

```
{repository-url}/repository/{repo-name}-artifacts/{repo-name}-{commit-sha}-plasma-src.tar.gz
```

## Error Handling

Common issues and solutions:

### "Artifact not found"
Run `plasmactl-package` first to create the artifact:
```bash
plasmactl package
plasmactl publish
```

### "Authentication failed"
Store valid credentials in keyring:
```bash
plasmactl keyring:set https://your-repository.com
```

### "Upload failed"
Check repository permissions and network connectivity.

## Integration

### With CI/CD

```yaml
# .gitlab-ci.yml or .github/workflows/publish.yml
publish:
  script:
    - plasmactl package
    - plasmactl publish
  only:
    - tags
```

### With Ship Command

```bash
# Publish and deploy in one workflow
plasmactl package
plasmactl publish
plasmactl ship dev platform.interaction.observability
```

## Best Practices

1. **Always package first**: Run `plasmactl package` before `plasmactl publish`
2. **Use keyring**: Store credentials securely, never commit them
3. **Verify upload**: Check the repository web UI after publishing
4. **Tag releases**: Use git tags for version tracking
5. **Automate**: Integrate into CI/CD pipelines

## Documentation

- [Plasmactl](https://github.com/plasmash/plasmactl) - Main CLI tool
- [Plasma Platform](https://plasma.sh) - Platform documentation
- [Open Source Plan](https://github.com/plasmash/pla-plasma/.claude/OPEN_SOURCE_PLAN.md) - Backend configuration roadmap

## License

Apache License 2.0
