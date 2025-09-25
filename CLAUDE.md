# CLAUDE.md - AcreetionOS ARM64 Workspace Coordination

This file provides guidance to Claude Code when working with the AcreetionOS ARM64 port multi-repository workspace.

## Workspace Overview

**Repository**: `acreetionos-arm64/workspace` - Main coordination hub
**Architecture**: Multi-repository with Git submodules for specialized development areas
**Development Model**: Sustainable side project, 18-36 month timeline

## Multi-Repository Structure

### Core Repositories
- **`iso-builder/`** - Main archiso build system, ARM64 conversion work
- **`custom-packages/`** - AcreetionOS-specific components (branding, installer, etc.)
- **`arm64-toolchain/`** - Cross-compilation environment, QEMU setup
- **`package-builder/`** - ARM64 package compilation scripts and PKGBUILD files

### Hardware & Boot
- **`hardware-support/`** - Device trees, firmware, Raspberry Pi/Pine64 configs
- **`boot-systems/`** - U-Boot, ARM64 UEFI bootloader implementations

### Infrastructure
- **`testing-infrastructure/`** - QEMU automation, validation, CI/CD workflows
- **`upstream-sync/`** - GitLab CE integration, upstream coordination tools
- **`documentation/`** - Technical architecture, guides, proposals
- **`releases/`** - ISO artifacts, release management, distribution

## Development Workflow

### Working with Submodules
```bash
# Clone complete workspace
git clone --recursive git@github.com:acreetionos-arm64/workspace.git

# Update all submodules
git submodule update --remote

# Work on specific submodule
cd iso-builder/
# Make changes, commit, push to submodule repository
git add . && git commit -m "ARM64 conversion progress"
git push

# Update workspace to reference new submodule commit
cd ..
git add iso-builder/
git commit -m "Update iso-builder to latest ARM64 work"
git push
```

### Milestone-Based Development
Currently in **Milestone 0**: Infrastructure Setup

**Focus Areas by Milestone**:
- **M0**: Repository setup, content migration, toolchain preparation
- **M1**: ARM64 conversion, first ISO build attempts
- **M2**: Bootable system, hardware support
- **M3**: AcreetionOS identity, branding, installer
- **M4**: Production release preparation

### Dual Remote Strategy
- **Primary Development**: GitHub Organization `acreetionos-arm64`
- **Upstream Coordination**: GitLab CE mirrors for key repositories
- **Synchronization**: Automated workflows in `upstream-sync/` submodule

## Current Development Status

### Completed (Milestone 0)
- ✅ GitHub Organization `acreetionos-arm64` established
- ✅ Main workspace repository created with coordination structure
- ✅ Project planning and architecture documentation complete

### In Progress
- 🔄 Creating 10 focused submodule repositories
- 🔄 Content migration from current scattered directory structure
- 🔄 Git history preservation during migration

### Next Actions
1. Create all 10 submodule repositories under `acreetionos-arm64` organization
2. Migrate content from `acreetionOS_Arm_Project/` with git history preservation
3. Configure GitLab CE Group for upstream coordination
4. Set up ARM64 development environment in `arm64-toolchain/`

## Repository-Specific Guidance

### When Working on Build System (`iso-builder/`)
- Focus on ARM64 conversion: `arch="x86_64"` → `arch="aarch64"`
- Package list conversion: `packages.x86_64` → `packages.aarch64`
- Boot system adaptation for ARM64 hardware

### When Working on Custom Packages (`custom-packages/`)
- ARM64 compilation of AcreetionOS-specific components
- Calamares installer configuration for ARM64
- Branding and theme package adaptations

### When Working on Documentation (`documentation/`)
- Technical architecture documents
- Developer guides and contribution workflows
- Upstream coordination proposals and communication

## Project Philosophy

**Sustainable Development**: Work when motivated, quality over speed
**Learning Focus**: Linux distribution development education
**Upstream Respect**: Maintain attribution and collaboration opportunities
**Community Ready**: Professional structure for future contributors

## Storage Strategy

- **Git LFS**: Each repository gets ~1GB allowance (50-100GB total capacity)
- **Focused Storage**: Large files distributed across specialized repositories
- **Release Management**: ISOs and artifacts in dedicated `releases/` repository

## Success Metrics

- ARM64 ISO boots to Cinnamon desktop on Raspberry Pi 5
- System installer works for ARM64 installations
- All AcreetionOS branding and customizations functional
- Project serves as comprehensive learning experience

## Contact & Coordination

- **Lead Developer**: John Junkins ([@macjunkins](https://github.com/macjunkins))
- **Organization**: [`acreetionos-arm64`](https://github.com/acreetionos-arm64)
- **Timeline**: Flexible 18-36 month development cycle
- **Pressure Level**: Zero - sustainable side project approach