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

To release only v2 (without rebuilding v1), use a tag with the `-v2` suffix:
```bash
git tag v2.1.0-v2 && git push origin v2.1.0-v2
```

A full product release tag without a suffix (e.g. `v2.1.0`) builds all variants including this one.
