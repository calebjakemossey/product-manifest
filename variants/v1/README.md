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

To release only v1 (without rebuilding v2), use a tag with the `-v1` suffix:
```bash
git tag v2.1.0-v1 && git push origin v2.1.0-v1
```

A full product release tag without a suffix (e.g. `v2.1.0`) builds all variants including this one.
