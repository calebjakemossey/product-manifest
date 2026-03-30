# Product Manifest

[![Validate](https://github.com/calebjakemossey/product-manifest/actions/workflows/validate.yaml/badge.svg)](https://github.com/calebjakemossey/product-manifest/actions/workflows/validate.yaml)

Coordinates product releases across packages, defines hardware variants, and produces deployment artefacts. This repository is the single source of truth for what software runs on each robot.

## Architecture

```mermaid
%%{init: {'theme': 'dark', 'flowchart': {'curve': 'linear'}}}%%
graph TB
    classDef primary fill:#2d5986,stroke:#4a90d9,stroke-width:1px,color:#e0e0e0,rx:8,ry:8
    classDef secondary fill:#1a3a5c,stroke:#3d7ab5,stroke-width:1px,color:#e0e0e0,rx:8,ry:8
    classDef accent fill:#2d7d46,stroke:#4caf50,stroke-width:1px,color:#e0e0e0,rx:8,ry:8
    classDef highlight fill:#8b5e3c,stroke:#d4956b,stroke-width:1px,color:#e0e0e0,rx:8,ry:8

    subgraph "Variant Manifests"
        V1[v1/manifest.repos<br/>LiDAR + RGB + supersonic]:::primary
        V2[v2/manifest.repos<br/>Depth camera + RGB]:::primary
    end

    subgraph "CI Validation (per-variant matrix)"
        B1[Build v1]:::secondary
        T1[Test v1]:::secondary
        B2[Build v2]:::secondary
        T2[Test v2]:::secondary
        INT[integration-test<br/>placeholder]:::highlight
    end

    subgraph "Tag-Driven Release"
        TAG[Tag v*]:::accent
        IMG1[robot-software:v*-v1]:::accent
        IMG2[robot-software:v*-v2]:::accent
        GHCR[GHCR]:::accent
    end

    V1 --> B1 --> T1 --> INT
    V2 --> B2 --> T2 --> INT
    TAG --> IMG1 --> GHCR
    TAG --> IMG2 --> GHCR
```

## How It Works

**Variant manifests** define which packages at which versions compose each hardware configuration. Each variant lives in its own directory under `variants/`, containing a `manifest.repos` file (a standard [vcstool](https://github.com/dirk-thomas/vcstool) manifest), a `config/` directory, and a `README.md`.

**Pull requests** that bump package versions trigger CI, which builds and tests the full workspace for every variant. The build matrix is discovered automatically from directories containing `manifest.repos` - adding a new variant directory is all that is needed to include it in validation.

**Tagging** the manifest triggers the release workflow, which parses the tag to determine what to build:

- `v2.1.0` - builds and pushes deployment images for **all** variants
- `v2.1.0-v1` - builds and pushes only the **v1** variant image
- `v2.1.0-v2` - builds and pushes only the **v2** variant image

A GitHub Release is created automatically with Docker pull commands for each variant built and a package versions table extracted from the manifests.

## Asynchronous Development

Teams develop and release packages independently in their own repositories. Each package has its own CI pipeline, versioning, and release cadence.

The manifest coordinates which versions compose each product release. A package team does not need to know about hardware variants - they simply publish versioned releases. The product team then updates the manifest to pull in tested, compatible versions.

This decoupling means:
- Package teams ship at their own pace
- The manifest is the single source of truth for what runs on each robot
- Version bumps are explicit, reviewable PRs

## Variant Structure

```
variants/
  v1/
    manifest.repos        # Package manifest for v1 hardware
    config/               # v1-specific configuration (model weights, calibration, launch profiles)
    README.md             # v1 variant documentation
  v2/
    manifest.repos        # Package manifest for v2 hardware
    config/               # v2-specific configuration
    README.md             # v2 variant documentation
```

Each variant is a self-contained directory. Adding a new variant means creating a new directory with a `manifest.repos`, `config/`, and `README.md` - the CI and release workflows discover it automatically.

## Cutting a Release

### Full Product Release (All Variants)

1. Open a PR bumping package versions in the relevant `manifest.repos` files
2. CI validates the build for all affected variants
3. Merge the PR
4. Tag the merge commit: `git tag v2.1.0 && git push origin v2.1.0`
5. The release workflow builds and pushes deployment images for **all** variants to GHCR

### Selective Variant Release

To release only a single variant, append the variant name as a tag suffix:

```bash
git tag v2.1.0-v1 && git push origin v2.1.0-v1   # releases only v1
git tag v2.1.0-v2 && git push origin v2.1.0-v2   # releases only v2
```

This is useful when a change only affects one hardware configuration and you want to avoid rebuilding unrelated variants.

### Re-pushing a Tag

If a release needs to be rebuilt (e.g. after a hotfix to the release workflow), delete and re-push the tag:

```bash
git tag -d v2.1.0 && git push origin :refs/tags/v2.1.0
git tag v2.1.0 && git push origin v2.1.0
```

This overwrites the existing image(s) in GHCR.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on bumping versions, adding variants, and cutting releases.
