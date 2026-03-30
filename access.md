# Accessing Hosted Solutions

## GitHub Repositories

All four repositories are public and can be viewed directly:

| Repository | URL | Purpose |
|-----------|-----|---------|
| ci-workflows | https://github.com/calebjakemossey/ci-workflows | Shared CI infrastructure (Docker images, package release workflow) |
| assignment_example_pkg | https://github.com/calebjakemossey/assignment_example_pkg | ROS-independent Python library |
| assignment_example_ros_pkg | https://github.com/calebjakemossey/assignment_example_ros_pkg | ROS2 workspace (nodes, services, actions) |
| product-manifest | https://github.com/calebjakemossey/product-manifest | Product release manifests and hardware variants |

## CI Pipelines

| Pipeline | URL |
|---------|-----|
| ci-workflows (image builds) | https://github.com/calebjakemossey/ci-workflows/actions |
| assignment_example_pkg (Python CI) | https://github.com/calebjakemossey/assignment_example_pkg/actions |
| assignment_example_ros_pkg (ROS CI) | https://github.com/calebjakemossey/assignment_example_ros_pkg/actions |
| product-manifest (variant validation + release) | https://github.com/calebjakemossey/product-manifest/actions |

## Demo PRs

| PR | What It Proves | Link |
|---|---|---|
| Deliberate failure | CI catches regressions - lint passes, build-and-test fails, merge is blocked | [PR #23](https://github.com/calebjakemossey/assignment_example_ros_pkg/pull/23) |
| Python CI happy path | Lint + test matrix (3.10/3.11/3.12) all pass, sequential flow | [PR #17](https://github.com/calebjakemossey/assignment_example_pkg/pull/17) |
| ROS CI happy path | Ament linters + colcon build/test + report-results all pass | [PR #25](https://github.com/calebjakemossey/assignment_example_ros_pkg/pull/25) |
| Manifest version bump | Both variants validated, integration-test passes | [PR #11](https://github.com/calebjakemossey/product-manifest/pull/11) |

## Releases

| Release | Link |
|---------|------|
| Product release v1.0.0 (all variants) | https://github.com/calebjakemossey/product-manifest/releases/tag/v1.0.0 |
| Selective variant release v1.0.1-v1 | https://github.com/calebjakemossey/product-manifest/releases/tag/v1.0.1-v1 |
| Package release v1.0.1 (ros_pkg) | https://github.com/calebjakemossey/assignment_example_ros_pkg/releases/tag/v1.0.1 |

## Docker Images (GHCR)

### CI Image: `ros-ci:humble`

Used by repo CI pipelines for building and testing ROS2 packages.

```bash
docker pull ghcr.io/calebjakemossey/ros-ci:humble
```

**Contents:** `ros:humble` base + vcstool, colcon-common-extensions, rosdep (initialised), pytest.

### Package Release Images

Individual repos produce package-level images when tagged. The shared release workflow builds the image and creates a GitHub Release with pull instructions.

```bash
# ROS2 workspace package
docker pull ghcr.io/calebjakemossey/assignment-example-ros-pkg:v1.0.0
docker pull ghcr.io/calebjakemossey/assignment-example-ros-pkg:v1.0.1
```

### Product Release Images: `robot-software`

Deployment images built per hardware variant when the product-manifest is tagged. Tags without a suffix (e.g. `v1.0.0`) build all variants; tags with a suffix (e.g. `v1.0.1-v1`) build only that variant.

```bash
# v1.0.0 release - both variants
docker pull ghcr.io/calebjakemossey/robot-software:v1.0.0-v1
docker pull ghcr.io/calebjakemossey/robot-software:v1.0.0-v2

# v1.0.1 release - v1 only (selective variant release)
docker pull ghcr.io/calebjakemossey/robot-software:v1.0.1-v1
```

### Verifying a Product Image

To confirm ROS2 packages are present in a product release image:

```bash
docker run --rm ghcr.io/calebjakemossey/robot-software:v1.0.0-v1 bash -c \
  "source /opt/ros/humble/setup.bash && source /ros_ws/install/local_setup.bash && ros2 pkg list | grep example"
```

Expected output:
```
example_package
example_package_msgs
example_package_ros
```

## GitHub Releases

Tag-driven releases automatically create GitHub Releases on the corresponding repository. Each release includes:

- Auto-generated release notes from merged PRs
- Docker image pull commands
- For product releases: a bill of materials listing package versions per variant

Releases can be viewed on each repository's Releases page:
- https://github.com/calebjakemossey/assignment_example_ros_pkg/releases
- https://github.com/calebjakemossey/product-manifest/releases

## Authentication

The repositories and CI logs are publicly accessible - no authentication required to view them.

To pull Docker images from GHCR:

```bash
# Public images can be pulled without auth
docker pull ghcr.io/calebjakemossey/ros-ci:humble

# If auth is needed:
echo $GITHUB_TOKEN | docker login ghcr.io -u YOUR_USERNAME --password-stdin
```
