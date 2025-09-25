# AcreetionOS ARM64 Workspace

**Organization**: [`acreetionos-arm64`](https://github.com/acreetionos-arm64)
**Project Lead**: John Junkins ([@macjunkins](https://github.com/macjunkins))
**Status**: Milestone 0 - Infrastructure Setup
**Architecture Target**: ARM64/aarch64

## Overview

This is the main coordination workspace for the AcreetionOS ARM64 port project. This repository coordinates development across 10+ focused submodules, each handling specific aspects of the ARM64 port.

## Multi-Repository Architecture

```
acreetionos-arm64/workspace              # ← This repository (coordination)
├── iso-builder/                         # Git submodule - Main archiso build system
├── custom-packages/                     # Git submodule - AcreetionOS components
├── arm64-toolchain/                     # Git submodule - Cross-compilation tools
├── hardware-support/                    # Git submodule - Device-specific support
├── boot-systems/                        # Git submodule - ARM64 bootloaders
├── package-builder/                     # Git submodule - ARM64 package builds
├── testing-infrastructure/              # Git submodule - QEMU, validation
├── upstream-sync/                       # Git submodule - GitLab CE integration
├── documentation/                       # Git submodule - Technical docs
└── releases/                           # Git submodule - ISOs and artifacts
```

## Quick Start

```bash
# Clone with all submodules
git clone --recursive git@github.com:acreetionos-arm64/workspace.git

# Or clone and initialize submodules separately
git clone git@github.com:acreetionos-arm64/workspace.git
cd workspace
git submodule update --init --recursive

# Update all submodules to latest
git submodule update --remote
```

## Development Milestones

### ✅ Milestone 0: Infrastructure Setup (Current)
- [x] GitHub Organization `acreetionos-arm64` created
- [x] Main workspace repository established
- [ ] 10 focused submodule repositories created
- [ ] GitLab CE upstream coordination setup
- [ ] Content migration with git history preservation

### 📋 Milestone 1: Foundation (~40-60 hours)
- [ ] ARM64 cross-compilation environment
- [ ] Convert profiledef.sh and packages.x86_64 → packages.aarch64
- [ ] First ARM64 ISO build attempt

### 📋 Milestone 2: Bootable System (~60-80 hours)
- [ ] ARM64 bootloader implementation (U-Boot/UEFI)
- [ ] Hardware support (Raspberry Pi 4/5, Pine64)
- [ ] Desktop environment functional on ARM64

### 📋 Milestone 3: AcreetionOS Identity (~50-70 hours)
- [ ] Custom branding and themes for ARM64
- [ ] Calamares installer port
- [ ] Default applications and configurations

### 📋 Milestone 4: Production Ready (~30-40 hours)
- [ ] Package repository and signing
- [ ] Documentation and user guides
- [ ] Community testing and v1.0.0 release

## Project Philosophy

**Sustainable Side Project Development**
- 18-36 month realistic timeline (180-250 hours total)
- Work when motivated, no artificial deadlines
- Focus on learning Linux distribution development
- Build on solid upstream foundations (Arch Linux ARM)

## Technical Strategy

**Hybrid Package Approach**: 90% upstream ARM packages + 10% AcreetionOS customizations

**Dual Remote Strategy**:
- **GitHub** (Primary): Development, CI/CD, community collaboration
- **GitLab CE** (Secondary): Upstream coordination with AcreetionOS team

## Storage Benefits

- **GitHub Organization**: Unlimited public repositories
- **Git LFS per Repository**: ~50-100GB total capacity across all submodules
- **Focused Development**: Each submodule handles specific technical domain

## Contributing

This project welcomes contributions in:
- ARM64 architecture and hardware support
- Package maintenance and ARM64 compilation
- Multi-platform testing and validation
- Documentation and user experience

## Links

- **Technical Architecture**: [`acreetionos-arm64/documentation`](https://github.com/acreetionos-arm64/documentation) *(coming soon)*
- **Build System**: [`acreetionos-arm64/iso-builder`](https://github.com/acreetionos-arm64/iso-builder) *(coming soon)*
- **AcreetionOS Upstream**: [gitlab.acreetionos.org](https://gitlab.acreetionos.org)

---

*This project is a tribute to the excellent foundation created by the AcreetionOS team. Independent development ensures progress while maintaining respect and potential collaboration opportunities.*