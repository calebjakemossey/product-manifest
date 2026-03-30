# Contributing

## Bumping a Package Version

1. Edit the relevant `manifest.repos` file(s) in `variants/<name>/` to update the `version` field
2. Open a pull request targeting `main`
3. CI will build and test the full workspace for each affected variant
4. Once CI passes, merge the PR

Only bump versions that have been tested and released in their own package repository. The manifest should always reference stable, tagged releases.

## Adding a New Hardware Variant

1. Create a new `variants/<name>/` directory
2. Add a `manifest.repos` file listing the packages and versions for the variant
3. Add a `config/` directory for variant-specific configuration
4. Add a `README.md` documenting the variant's fleet size, sensors, and release instructions
5. Open a PR - CI will automatically discover the new variant and include it in the build matrix

No workflow changes are needed. Variant discovery is automatic.

## Cutting a Product Release

All releases are tag-driven. There is no manual dispatch.

### Full Release (All Variants)

1. Ensure all desired version bumps have been merged to `main`
2. Tag the commit: `git tag v2.1.0`
3. Push the tag: `git push origin v2.1.0`
4. The release workflow builds deployment images for **all** variants and pushes them to GHCR

Images are tagged as `robot-software:<version>-<variant>` (e.g. `robot-software:v2.1.0-v1`).

### Selective Variant Release

To release only a single variant, append the variant name as a tag suffix:

```bash
git tag v2.1.0-v1 && git push origin v2.1.0-v1   # releases only v1
git tag v2.1.0-v2 && git push origin v2.1.0-v2   # releases only v2
```

The workflow parses the tag suffix to determine which variant to build. A tag without a suffix (e.g. `v2.1.0`) builds all variants.

### Re-pushing a Tag

Re-pushing a tag overwrites the existing image(s) in GHCR:

```bash
git tag -d v2.1.0 && git push origin :refs/tags/v2.1.0
git tag v2.1.0 && git push origin v2.1.0
```

## CI Validation

Every PR triggers the validation workflow, which:

1. Discovers all variants from `variants/*/manifest.repos`
2. For each variant: clones packages at the manifest-specified versions
3. Builds the full workspace with colcon
4. Runs all package tests
5. Publishes test results as check annotations

All variants must pass before merging.

## Asynchronous Development

Package teams develop and release independently. They do not need to know about hardware variants or this manifest. When a package publishes a new version, the product team bumps the manifest to include it.

This means:
- Package changes flow through the manifest, never the other way around
- The manifest is the single coordination point for what runs on each robot
- Version bumps are explicit, auditable, and reversible
