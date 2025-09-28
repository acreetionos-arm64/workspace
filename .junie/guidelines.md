# Project Guidelines

# JUNIE.md - Custom Instruction Set for JetBrains AI (Junie)

This file provides the workspace-specific guidance Junie should follow when assisting in the AcreetionOS ARM64 multi-repository workspace. It is the Junie equivalent of CLAUDE.md for Claude CLI.

## Purpose
- Align Junie’s actions with the project’s established methodology, structure, and expectations.
- Ensure minimal, precise code changes with clear communication and WBS-driven execution.
- Maintain consistency across multi-repository development with Git submodules.

## Workspace Context
- Repository: acreetionos-arm64/workspace (coordination hub)
- Architecture: Multi-repo with Git submodules for specialized areas
- Development model: Waterfall with WBS numbering; sustainable, long-term side project (18–36 months)
- This project is a custom Arm64 distribution of AcreetionOS. A custom Linux distribution based on Arch Linux.

## Methodology and Governance
- Primary methodology: Waterfall; strictly respect dependencies and milestone order.
- WBS numbering format: M[milestone].[repository].[task]
    - Repository numbers: 1=iso-builder, 2=arm64-toolchain, 3=documentation, 4=custom-packages, 5=testing-infrastructure, 6=boot-systems, 7=hardware-support
- Issues: Internal development issues MUST use WBS numbering. Community issues are triaged and may be mapped to WBS.
- Milestones: M1 (Foundation & Infrastructure), M2 (Core ARM64 Implementation)

## Communication Protocol (Junie)
- Always keep the user informed using a concise analysis + an explicit plan.
- Use the following structure in conversations and PRs/commits:
    1) Findings/assumptions relevant to the change
    2) Proposed minimal plan
    3) Actions taken
    4) Verification steps/results
    5) Next actions
- Prefer high-signal summaries over verbose narration.
- Respect established decisions in this workspace; do not re-argue methodology or scope unless asked to.

## Operating Rules for Code Changes
- Minimal-change principle: implement only what is necessary to satisfy the issue.
- Do not introduce new technologies, frameworks, or broad refactors unless explicitly requested.
- Maintain compatibility with the multi-repo submodule structure.
- Follow existing conventions (filenames, directory layout, script patterns).
- Add or update documentation when behavior or workflows change.

## Git and Submodule Workflow
- When changing a submodule:
    - Commit to the submodule repo first.
    - Update submodule reference in the workspace repo.
    - Use descriptive commit messages tied to WBS (e.g., "M1.1.2: Update iso-builder aarch64 package list").
- Keep commits focused and logically atomic.

## File and Repository Conventions
- iso-builder/: archiso build system and ARM64 conversion
- custom-packages/: AcreetionOS-specific packages (branding, installer)
- arm64-toolchain/: cross-compilation and QEMU
- package-builder/: ARM64 package compilation scripts/PKGBUILDs
- hardware-support/: device trees, firmware configs
- boot-systems/: U-Boot and ARM64 UEFI bootloader implementations
- testing-infrastructure/: CI/CD, QEMU automation, validation
- upstream-sync/: GitHub/GitLab sync automation
- documentation/: architecture, guides, proposals
- releases/: ISO artifacts and distribution

## Issue Handling
- Start from the WBS issue on the critical path unless specifically told otherwise.
- Explicitly list dependencies and confirm none are violated.
- Record verification steps (repro, tests, build results). Automate where reasonable.

## Safety Checks Before Submitting Changes
- Build and run any relevant local validations (scripts, QEMU, lint) when available.
- Ensure submodule references are updated and consistent.
- Add or update README/docs for new scripts or workflows.
- Keep storage constraints in mind; use Git LFS appropriately per repo policies.

## Deliverables and Formatting
- Summaries should end with a clear status: done/blocked/needs-review.
- Use WBS numbers in commit messages and PR titles for internal work.
- For documentation changes, include a brief "Why" section.

## Example Commit Messages
- M1.1.1: Validate x86_64 baseline build scripts in iso-builder
- M1.2.1: Add initial aarch64 cross-toolchain bootstrap scripts
- M2.6.2: Implement U-Boot config for Raspberry Pi 5 in boot-systems

## How Junie Differs from Claude Here
- Junie operates inside JetBrains IDEs with project-aware tooling and should:
    - Prefer IDE search/navigation tools over shell grepping.
    - Make small, targeted edits and document them clearly.
    - Adhere to the Waterfall + WBS governance already established.

## Contact and Ownership
- Lead: John Junkins (@macjunkins)
- Org: https://github.com/acreetionos-arm64
- Pressure level: Low; quality, learning, and sustainability over speed.

## Quick Start for Junie in This Repo
- If mirroring Claude guidance: consult CLAUDE.md for contextual overlap; defer to JUNIE.md for Junie-specific rules.
- When in doubt, ask clarifying questions before making broad changes.

