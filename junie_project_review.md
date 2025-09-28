# AcreetionOS ARM64 Workspace — Deep Review and Action Plan

Date: 2025-09-27 13:02 (local)

This document consolidates the detailed review and recommendations provided during the current session. It focuses on enabling a first successful aarch64 (ARM64) boot using the iso-builder repository, improving build reliability and performance, and outlining CI and testing guidance. The scope also includes process, repository hygiene, and toolchain considerations across the multi-repo workspace.

---

## Executive summary

- The iso-builder is currently configured for x86_64. Minimal but critical edits are required to switch to aarch64, fix the mkarchiso invocation, and ensure correct UEFI ARM64 boot assets.
- For a fast path to first ARM64 boot: update `profiledef.sh` (arch, bootmodes, squashfs options), correct `mkarchiso.sh`, add `packages.aarch64`, and boot the generated ISO in QEMU with edk2/UEFI.
- Efficiency opportunities: avoid unconditional `work/` cleanup, enable caching, standardize environments (containers/devcontainers), and run builds on native ARM64 runners for 3–10x speedups.
- Security and supply chain: decide on pacman signature policy and document keyring bootstrap; avoid committing large binary-like overlays in `airootfs/` unless required.

---

## High-impact findings (iso-builder)

- Architecture set to x86_64
  - `profiledef.sh` has `arch="x86_64"`. For ARM64, set `arch="aarch64"`.
  - `airootfs_image_tool_options` uses `-Xbcj x86` which is x86-only. Remove for ARM.

- Boot modes are x86-centric
  - Current bootmodes include BIOS and x86_64 UEFI. For ARM64, use UEFI aarch64. If your archiso fork supports systemd-boot on aarch64, prefer it; otherwise use GRUB with `grubaa64.efi`.

- mkarchiso invocation is malformed
  - Current line mixes an `export` into the argument list and uses a wrong parallel flag.
  - Use `export PACMAN_OPTS="--overwrite *"` separately, and `-j "$(nproc)"`.

- Build script always nukes cache
  - `build.sh` deletes `./work` every run. Make cleanup optional to leverage incremental rebuilds during development.

- Packages lists tied to x86_64
  - Provide `packages.aarch64` (and `bootstrap_packages.aarch64` if needed) to match the new `arch`.

- Pacman repos and signing
  - `pacman.conf` references custom repos with `SigLevel = Optional`. Decide policy and document keyring bootstrap (especially if using Arch Linux ARM keyring and custom keys).

- Large `airootfs/` overlay
  - Keep overlays minimal—prefer package dependencies over committing large runtime trees to the repo.

---

## Required iso-builder edits for ARM64 enablement

1) profiledef.sh (minimal safe changes)

- Replace x86 with aarch64, remove x86 bcj, and set ARM64 UEFI boot mode. Diff-style summary:

```
- bootmodes=('bios.syslinux.mbr' 'bios.syslinux.eltorito'
-            'uefi-ia32.systemd-boot.esp' 'uefi-x64.systemd-boot.esp'
-            'uefi-ia32.systemd-boot.eltorito' 'uefi-x64.systemd-boot.eltorito')
- arch="x86_64"
- airootfs_image_tool_options=('-comp' 'xz' '-Xbcj' 'x86' '-b' '1M' '-Xdict-size' '1M')
+ bootmodes=('uefi-aarch64.systemd-boot.esp')
+ arch="aarch64"
+ airootfs_image_tool_options=('-comp' 'xz' '-b' '1M' '-Xdict-size' '1M')
```

Notes:
- If your archiso version doesn’t support `uefi-aarch64.systemd-boot.esp`, switch to GRUB for aarch64 and provide `EFI/BOOT/BOOTAA64.EFI` (grub) with a matching grub.cfg.

2) mkarchiso.sh (fix command)

Replace the current line with:

```bash
#!/usr/bin/env bash
set -euo pipefail
export PACMAN_OPTS="--overwrite *"
mkarchiso -L "AcreetionOS" -v -j "$(nproc)" -C ./pacman.conf -o ../ISO .
```

3) build.sh (optional cleanup)

Make cleanup opt-in via an environment variable:

```bash
#!/usr/bin/env bash
set -euo pipefail
: "${BUILD_CLEAN:=0}"
./refresh.sh -j
./mkarchiso.sh
if [[ "$BUILD_CLEAN" == "1" ]]; then
  sudo rm -rf ./work
fi
```

4) Packages lists for aarch64

Create `packages.aarch64` (and `bootstrap_packages.aarch64` if your profile expects it). Example starter list:

```
base
base-devel
linux-aarch64
linux-aarch64-headers
mkinitcpio
systemd
# If using GRUB for aarch64 instead of systemd-boot:
# grub
efibootmgr
btrfs-progs
xfsprogs
netctl
networkmanager
vim
openssh
curl
wget
pacman-contrib
```

Adjust names to match your repos (Arch Linux ARM vs custom mirrors).

5) EFI boot assets (systemd-boot path)

If relying on archiso to create loader entries, ensure kernel and initramfs names match. Example loader entry if you’re providing it yourself:

```
# efiboot/loader/entries/acreetionos.conf
title   AcreetionOS (ARM64 Live)
linux   /vmlinuz-linux-aarch64
initrd  /initramfs-linux-aarch64.img
options rd.luks=0 rd.md=0 rd.dm=0 root=live:LABEL=AcreetionOS copytoram=n
```

If you switch to GRUB on aarch64, ensure `EFI/BOOT/BOOTAA64.EFI` exists and `grub.cfg` points to the correct kernel/initramfs, searching by ISO label.

---

## Pacman configuration and keyring

- Keep `Architecture = auto`.
- For development, a transitional policy may be acceptable:
  - Official repos: `SigLevel = Required DatabaseOptional`
  - Custom repos (temporary): `SigLevel = Optional` until keys are in place
- Document keyring bootstrap in ISO overlay or first-boot script, e.g.:

```bash
pacman-key --init
pacman-key --populate archlinuxarm   # if using Arch Linux ARM keyring
pacman-key --lsign-key <YOUR_REPO_KEY_ID>
```

Tighten signatures before any public release.

---

## QEMU ARM64 smoke test (headless, serial)

Use edk2 UEFI firmware to validate the ISO boots to a usable target.

```bash
# Paths vary by distro; adjust accordingly
EFI_FW_CODE=/usr/share/edk2/aarch64/QEMU_EFI-pflash.raw
EFI_FW_VARS=/usr/share/edk2/aarch64/vars-template-pflash.raw
cp "$EFI_FW_VARS" ./EFI_VARS.fd

qemu-system-aarch64 \
  -machine virt \
  -cpu cortex-a72 \
  -m 4096 \
  -smp 4 \
  -bios "$EFI_FW_CODE" \
  -drive if=pflash,format=raw,file=./EFI_VARS.fd \
  -device virtio-gpu-pci \
  -device qemu-xhci \
  -device usb-tablet \
  -device virtio-net-pci,netdev=n0 \
  -netdev user,id=n0 \
  -display none -serial mon:stdio \
  -drive media=cdrom,if=virtio,file=../ISO/AcreetionOS-*.iso
```

Expected: systemd-boot (or GRUB) menu → kernel boots → reaches default target (<~60s). If no loader is found, verify ESP contains the correct `BOOTAA64.EFI` and loader config.

---

## Minimal GitHub Actions CI on ARM64 (sketch)

Run builds on ARM64 runners and in a containerized Arch ARM environment with caching:

```yaml
name: Build ARM64 ISO
on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build-arm64:
    runs-on: ubuntu-24.04-arm64
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: recursive
      - name: Prep pacman cache
        run: |
          sudo mkdir -p /var/cache/pacman/pkg
          sudo chown $USER: /var/cache/pacman/pkg
      - name: Setup Docker buildx
        uses: docker/setup-buildx-action@v3
      - name: Build in Arch ARM container
        run: |
          docker run --rm -t \
            --platform linux/arm64 \
            -v "$PWD":/ws \
            -v /var/cache/pacman/pkg:/var/cache/pacman/pkg \
            ghcr.io/<your-org>/archarm-base:latest \
            bash -lc '
              pacman -Syu --noconfirm archiso git coreutils findutils \
              && cd /ws/iso-builder \
              && ./build.sh
            '
      - name: Upload ISO artifact
        uses: actions/upload-artifact@v4
        with:
          name: acreetionos-arm64-iso
          path: iso-builder/../ISO/*.iso
```

Notes:
- Provide or build a minimal Arch Linux ARM container image and publish it (e.g., GHCR). Mount `/var/cache/pacman/pkg` for caching.
- Prefer self-hosted ARM64 runners or GitHub’s `ubuntu-24.04-arm64` for better performance.

---

## arm64-toolchain: interim setup snippet

Until your scripts are finalized, a portable environment helper improves developer experience:

```bash
# scripts/setup-cross-env.sh (example)
set -euo pipefail
export TARGET_ARCH=aarch64
export TARGET_TRIPLET=aarch64-linux-gnu

export CC=${TARGET_TRIPLET}-gcc
export CXX=${TARGET_TRIPLET}-g++
export AR=${TARGET_TRIPLET}-ar
export RANLIB=${TARGET_TRIPLET}-ranlib
export LD=${TARGET_TRIPLET}-ld
export STRIP=${TARGET_TRIPLET}-strip

: "${SYSROOT:=${HOME}/.acreetionos/sysroots/${TARGET_TRIPLET}}"
export SYSROOT

export PKG_CONFIG_PATH=${SYSROOT}/usr/lib/pkgconfig:${SYSROOT}/usr/share/pkgconfig
export PKG_CONFIG_LIBDIR=${PKG_CONFIG_PATH}
export CFLAGS="--sysroot=${SYSROOT} -O2 -pipe"
export CXXFLAGS="${CFLAGS}"
export LDFLAGS="--sysroot=${SYSROOT}"
export PATH="${SYSROOT}/usr/bin:${PATH}"

echo "Cross env configured for ${TARGET_TRIPLET} with SYSROOT=${SYSROOT}"
```

Also provide a CMake toolchain file in `configs/cmake/arm64-toolchain.cmake` for projects using CMake.

---

## Efficiency recommendations (build, CI, repo hygiene)

- Native ARM64 runners: Build on Apple Silicon/Linux ARM64 for 3–10x faster builds vs emulation.
- Caching: Enable `ccache/sccache`, persistent pacman cache, and container layer caching.
- Reproducible environments: Devcontainers or Docker images with pinned tool versions.
- Artifacts: Publish ISOs to Releases instead of storing in-repo; set retention policies.
- `airootfs/` overlay: Keep minimal; prefer packages over committing runtime trees.

---

## Risks and mitigations

- Submodule drift → Pin submodule SHAs; use scheduled bot PRs to bump and validate.
- Slow builds → Native ARM64 runners + aggressive caching + prebuilt base layers.
- Supply chain/signing → Establish keyring management, require signatures for releases.
- Large artifacts in repo → Use Releases; avoid LFS for frequently changing ISOs.

---

## Fast path checklist (today)

- [ ] Update `iso-builder/profiledef.sh` to `arch="aarch64"`, ARM64 bootmodes, and remove `-Xbcj x86`.
- [ ] Fix `iso-builder/mkarchiso.sh` and make `build.sh` cleanup optional.
- [ ] Create `packages.aarch64` with initial essentials.
- [ ] Build on an ARM64 host and boot ISO in QEMU with edk2.
- [ ] Confirm ESP contents and loader configuration (systemd-boot or GRUB) on aarch64.

---

## Notes on targets

A PC-style UEFI ARM64 ISO is ideal for QEMU and rare ARM64 PCs. SBCs (e.g., Raspberry Pi/Pine64) typically need board images (U-Boot + device tree + kernel) rather than an ISO. Consider a separate image builder for SBCs after the generic aarch64 ISO path is validated.

---

## Attribution

This review builds upon earlier high-level efficiency guidance and the contents of the `acreetionos-arm64` workspace and submodules visible in this session. It’s intended as a practical guide to achieve first-boot success on aarch64 and to improve reliability and speed across the project.
