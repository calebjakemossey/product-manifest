# v2 Hardware Variant

**Fleet:** 3 robots
**Sensors:** Depth camera, RGB camera

## Package Manifest

`manifest.repos` defines the package versions for this variant. Core packages are shared with other variants; v2-specific packages (e.g. depth camera perception) would be listed here as the product grows.

## Configuration

The `config/` directory holds v2-specific:
- Model weights and parameters (e.g. depth camera perception model)
- Sensor calibration data
- Launch profiles that start only v2-relevant nodes

## Releasing This Variant

To release only v2 (without rebuilding v1), use manual dispatch:
```
gh workflow run release.yaml --field version=v2.1.0 --field variants=v2
```

A full product release (tagging the manifest) builds all variants.
