# Product Manifest

Coordinates product releases across packages, defines hardware variants, and produces deployment artefacts.

## Architecture

```mermaid
%%{init: {'theme': 'dark', 'flowchart': {'curve': 'linear'}}}%%
flowchart LR
    subgraph Manifests
        V1[v1.repos]
        V2[v2.repos]
    end

    subgraph CI["CI Validation (per-variant build matrix)"]
        B1[Build v1]
        B2[Build v2]
        T1[Test v1]
        T2[Test v2]
    end

    subgraph Release
        TAG[Tag v*]
        IMG1[v1 Image]
        IMG2[v2 Image]
        GHCR[GHCR]
    end

    V1 --> B1 --> T1
    V2 --> B2 --> T2
    T1 --> TAG
    T2 --> TAG
    TAG --> IMG1 --> GHCR
    TAG --> IMG2 --> GHCR
```

## How It Works

**Variant manifests** define which packages at which versions compose each hardware configuration. Each `.repos` file in `variants/` is a standard [vcstool](https://github.com/dirk-thomas/vcstool) manifest listing Git repositories and their pinned versions.

**Pull requests** that bump package versions trigger CI, which builds and tests the full workspace for every variant. The build matrix is discovered automatically from the `.repos` files - adding a new variant file is all that is needed to include it in validation.

**Tagging** the manifest (e.g. `v1.0.0`) triggers the release workflow, which builds a Docker image for each variant and pushes it to GHCR. These images are the deployment artefacts consumed by the fleet.

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
  v1.repos              # Package manifest for v1 hardware
  v1/config/            # v1-specific configuration (model weights, calibration, launch profiles)
  v2.repos              # Package manifest for v2 hardware
  v2/config/            # v2-specific configuration
```

Each variant has:
- A `.repos` file listing packages and pinned versions
- A `config/` directory for variant-specific artefacts (model weights, sensor calibration, launch files)

## Cutting a Release

1. Open a PR bumping package versions in the relevant `.repos` files
2. CI validates the build for all affected variants
3. Merge the PR
4. Tag the merge commit: `git tag v1.2.0 && git push origin v1.2.0`
5. The release workflow builds and pushes deployment images to GHCR

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on bumping versions, adding variants, and cutting releases.
