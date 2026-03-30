# v1 Hardware Variant

**Fleet:** 7 robots
**Sensors:** LiDAR, RGB camera, supersonic sensors

## Package Manifest

`manifest.repos` defines the package versions for this variant. Core packages are shared with other variants; v1-specific packages (e.g. LiDAR perception) would be listed here as the product grows.

## Configuration

The `config/` directory holds v1-specific:
- Model weights and parameters (e.g. LiDAR perception model)
- Sensor calibration data
- Launch profiles that start only v1-relevant nodes

## Releasing This Variant

To release only v1 (without rebuilding v2), use manual dispatch:
```
gh workflow run release.yaml --field version=v2.1.0 --field variants=v1
```

A full product release (tagging the manifest) builds all variants.
