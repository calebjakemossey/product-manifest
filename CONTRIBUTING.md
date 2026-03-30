# Contributing

## Bumping a Package Version

1. Edit the relevant `.repos` file(s) in `variants/` to update the `version` field
2. Open a pull request targeting `main`
3. CI will build and test the full workspace for each affected variant
4. Once CI passes, merge the PR

Only bump versions that have been tested and released in their own package repository. The manifest should always reference stable, tagged releases.

## Adding a New Hardware Variant

1. Create a new `variants/<name>.repos` file listing the packages and versions for the variant
2. Create a `variants/<name>/config/` directory for variant-specific configuration
3. Open a PR - CI will automatically discover the new variant and include it in the build matrix

No workflow changes are needed. Variant discovery is automatic.

## Cutting a Product Release

1. Ensure all desired version bumps have been merged to `main`
2. Tag the commit: `git tag v<major>.<minor>.<patch>`
3. Push the tag: `git push origin v<major>.<minor>.<patch>`
4. The release workflow builds deployment images for each variant and pushes them to GHCR

Images are tagged as `robot-software:<version>-<variant>` (e.g. `robot-software:v1.2.0-v1`).

## CI Validation

Every PR triggers the validation workflow, which:

1. Discovers all variants from `variants/*.repos`
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
