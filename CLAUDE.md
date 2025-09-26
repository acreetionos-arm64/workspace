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

### Work Breakdown Structure (WBS) & Waterfall Development
**Development Methodology**: Structured Waterfall approach with systematic WBS numbering

**Current Active Milestones**:
- **M1: Foundation & Infrastructure** (80-100 hours) - Due: Dec 31, 2025
- **M2: Core ARM64 Implementation** (120-160 hours) - Due: Mar 31, 2026

**WBS Numbering System**: `M[milestone].[repository].[task]`
- **Repository Numbers**: 1=iso-builder, 2=arm64-toolchain, 3=documentation, 4=custom-packages, 5=testing-infrastructure, 6=boot-systems, 7=hardware-support
- **Examples**: M1.1.1, M1.2.3, M2.6.1

**Issue Management Rules**:
- **Internal Development**: MUST follow WBS numbering system
- **Community Issues**: Will be triaged and either integrated into WBS or handled separately
- **Dependencies**: Strict adherence to documented issue dependencies
- **No Agile**: Sequential milestone completion, no sprint methodology

### Dual Remote Strategy
- **Primary Development**: GitHub Organization `acreetionos-arm64`
- **Upstream Coordination**: GitLab CE mirrors for key repositories
- **Synchronization**: Automated workflows in `upstream-sync/` submodule

## Current Development Status

### Completed (Infrastructure Setup)
- ✅ GitHub Organization `acreetionos-arm64` established
- ✅ Main workspace repository created with coordination structure
- ✅ **M1 & M2 Milestones**: 29 issues created with WBS numbering (M1: 16 issues, M2: 13 issues)
- ✅ **Project Board**: All issues organized in unified tracking system
- ✅ **WBS System**: Structured numbering implemented across all development work

### Current Milestone: M1 - Foundation & Infrastructure
**Ready to Begin**: M1.1.1 - Validate x86_64 build baseline (critical path starter)
**Parallel Research**: M1.3.x documentation tasks can run concurrently

### Next Critical Path Actions
1. **M1.1.1**: Validate x86_64 build baseline (0 dependencies)
2. **M1.2.1**: Setup ARM64 cross-compilation toolchain (foundation for all builds)
3. **M1.3.1-3**: ARM64 architecture, package ecosystem, and hardware research
4. **M1.1.2**: Analyze archiso build system internals (depends on M1.1.1)

## Issue Management & WBS Guidelines

### Creating New Issues
**Internal Development Issues**: MUST follow WBS format
```
Title: M[milestone].[repository].[task] - [Description]
Examples:
- M1.1.1 - Validate x86_64 build baseline
- M2.6.2 - Implement U-Boot configuration for target platforms
```

### Repository Number Reference
```
1 = iso-builder          (Main archiso build system)
2 = arm64-toolchain      (Cross-compilation environment)
3 = documentation        (Technical architecture, guides)
4 = custom-packages      (AcreetionOS-specific components)
5 = testing-infrastructure (CI/CD, validation)
6 = boot-systems         (U-Boot, UEFI bootloaders)
7 = hardware-support     (Device trees, firmware)
```

### Issue Labels Required
- **Repository**: `iso-builder`, `arm64-toolchain`, etc.
- **Priority**: `priority:critical`, `priority:important`
- **Complexity**: `complexity:low`, `complexity:medium`, `complexity:high`
- **Type**: `enhancement`, `bug`, `documentation`

### Community Issue Triage
- External issues lacking WBS numbering will be triaged
- May be integrated into existing WBS or handled as separate community requests
- Maintain clear separation between structured development and community contributions

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