# Space ROS Documentation

This repository contains the sources for the Space ROS documentation which is hosted as: https://space-ros.github.io/docs
The sources from this repository are built by GitHub Actions.

## Quick Start with Pixi

This project uses [Pixi](https://pixi.sh) for dependency management and task running. To get started:

1. **Install Pixi** (if not already installed):
   Follow the installation instructions at https://pixi.sh

2. **Install dependencies**:
   ```bash
   pixi install
   ```

3. **Build documentation**:
   ```bash
   # Build single version
   pixi run html
   
   # Build multi-version (for deployment)
   pixi run multiversion
   ```

4. **Run tests**:
   ```bash
   pixi run test
   ```

5. **Serve locally** (optional):
   ```bash
   pixi run serve
   ```

## Available Commands

| Command | Description |
|---------|-------------|
| `pixi run html` | Build single version documentation |
| `pixi run multiversion` | Build multi-version documentation |
| `pixi run test` | Run doc8 linting tests |
| `pixi run clean` | Clean build directory |
| `pixi run serve` | Serve documentation locally |
| `pixi run ci` | Run CI workflow (test + build) |
| `pixi run deploy` | Run deployment workflow (test + multiversion) |



## Contributing to the documentation

Contributions to this site are most welcome.
Please see the [Contributing to ROS 2 Documentation](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing/Contributing-To-ROS-2-Documentation.html) page to learn more.
Most of the guidelines there apply but the branch structure is much simpler as Space ROS currently has only one supported distribution.

### Development Workflow

1. Fork and clone the repository
2. Install dependencies: `pixi install`
3. Make your changes to the documentation
4. Test your changes: `pixi run test`
5. Build and preview: `pixi run html && pixi run serve`
6. Submit a pull request



## Contributing to Space ROS

To contribute to the Space ROS and ROS 2 projects please refer to the [ROS 2 contributing guidelines](https://docs.ros.org/en/rolling/The-ROS2-Project/Contributing.html).
