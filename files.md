# Files Added

A complete list of all files added or modified across the four repositories.

## ci-workflows

Shared CI/CD infrastructure: Docker images, reusable release workflow, and deliverable documents.

| File | Description |
|------|-------------|
| `Dockerfile` | CI image: `ros:humble` base with vcstool, colcon, rosdep, pytest |
| `Dockerfile.release` | Generic release image for package-level releases |
| `.github/workflows/build-ci-image.yaml` | Builds and pushes `ros-ci:humble` to GHCR when Dockerfile changes |
| `.github/workflows/release.yaml` | Reusable: builds a Docker image per repo tag, creates GitHub Release with pull instructions |
| `.github/CODEOWNERS` | Assigns repository owner as reviewer |
| `.github/pull_request_template.md` | PR template with checklist |
| `CONTRIBUTING.md` | How to modify workflows and test changes |
| `README.md` | Architecture documentation with mermaid diagrams |
| `overview.md` | Deliverable: solution overview, requirements coverage, architecture, E2E results, future improvements |
| `files.md` | Deliverable: this file |
| `access.md` | Deliverable: repository URLs, CI pipeline links, Docker image pull commands, demo PR links |

## assignment_example_pkg

ROS-independent Python utility library.

| File | Description |
|------|-------------|
| `.github/workflows/ci.yaml` | Self-contained CI: flake8 lint (gates) + pytest across Python 3.10/3.11/3.12 |
| `.github/CODEOWNERS` | Assigns repository owner as reviewer |
| `.github/pull_request_template.md` | PR template with Python-specific checklist |
| `CONTRIBUTING.md` | Development setup, testing, linting, PR workflow |
| `.pre-commit-config.yaml` | Pre-commit hooks: whitespace, line endings, YAML, flake8 |
| `README.md` | *(modified)* CI badge, system diagram, Docker instructions, Humble note |
| `requirements.txt` | *(modified)* Added pytest |

### Files modified for lint compliance

| File | Change |
|------|--------|
| `example_package/example.py` | Fixed flake8 errors (E302, E305, W605) |
| `example_package/example_runner.py` | Fixed flake8 errors (E302, E501) |
| `tests/test_example.py` | Fixed flake8 errors (E302) |

## assignment_example_ros_pkg

ROS2 workspace with nodes, services, and actions.

| File | Description |
|------|-------------|
| `.github/workflows/ci.yaml` | Self-contained CI: ament linters (gate) + colcon build/test in ros-ci:humble + report results on PR |
| `.github/workflows/release.yaml` | Caller to shared release.yaml: builds package image and creates GitHub Release per tag |
| `.github/CODEOWNERS` | Assigns repository owner as reviewer |
| `.github/pull_request_template.md` | PR template with ROS-specific checklist |
| `CONTRIBUTING.md` | Workspace setup with vcstool, colcon build/test, PR workflow |
| `.pre-commit-config.yaml` | Pre-commit hooks: whitespace, line endings, YAML, flake8 |
| `workspace.repos` | vcstool manifest pinning `assignment_example_pkg` to v1.0.0 |
| `LICENSE` | Apache 2.0 licence (required by ament_copyright) |
| `README.md` | *(modified)* CI badge, system diagram, Docker instructions, Humble note |

### Files modified for lint compliance

| File | Change |
|------|--------|
| `example_package_ros/example_package_ros/example_node.py` | Lint fixes + copyright header |
| `example_package_ros/example_package_ros/advanced_example_node.py` | Lint fixes + copyright header |
| `example_package_ros/launch/example_node.launch.py` | Lint fixes + copyright header |
| `example_package_ros/launch/advanced_example_node.launch.py` | Lint fixes + copyright header |
| `example_package_ros/test/test_example_node.py` | Lint fixes + copyright header |
| `example_package_ros/test/test_advanced_example_node.py` | Lint fixes + copyright header |
| `example_package_ros/setup.py` | Copyright header |
| `example_package_ros/README.md` | Iron to Humble throughout |

## product-manifest

Product release coordination: variant manifests, integration testing, deployment artefacts.

| File | Description |
|------|-------------|
| `variants/v1/manifest.repos` | v1 hardware manifest: package versions for LiDAR + RGB + supersonic |
| `variants/v1/config/README.md` | v1 variant configuration (placeholder for models, calibration, launch profiles) |
| `variants/v1/README.md` | v1 variant documentation: fleet size, sensors, usage |
| `variants/v2/manifest.repos` | v2 hardware manifest: package versions for depth camera + RGB |
| `variants/v2/config/README.md` | v2 variant configuration (placeholder for models, calibration, launch profiles) |
| `variants/v2/README.md` | v2 variant documentation: fleet size, sensors, usage |
| `Dockerfile.release` | Builds product image from variant manifest |
| `.github/workflows/validate.yaml` | PR CI: discovers variants, builds + tests per variant, integration placeholder |
| `.github/workflows/release.yaml` | Tag-driven: parses tag suffix to build all or single variant deployment images, pushes to GHCR, creates GitHub Release with bill of materials |
| `.github/CODEOWNERS` | Assigns repository owner as reviewer |
| `.github/pull_request_template.md` | PR template with variant checklist |
| `CONTRIBUTING.md` | How to bump versions, add variants, cut releases |
| `README.md` | Architecture, variant structure, release process |
