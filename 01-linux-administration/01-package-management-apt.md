# Linux Administration: Software Management via APT

## Objective
Demonstrate installing, updating, inspecting, and purging software packages using Debian's Advanced Package Tool (`apt`) within an enterprise Linux environment.

## Scenario
As a security analyst, maintaining an accurate inventory of system utilities and ensuring binaries are sourced from verified repository mirrors is critical to vulnerability management and attack surface reduction.

---

## Command Reference

| Command | Purpose | Security Context |
| :--- | :--- | :--- |
| `sudo apt update` | Resynchronizes package index files from repositories | Prevents installing outdated binaries with known vulnerabilities (CVEs) |
| `sudo apt list --upgradable` | Lists packages with available patches | Audits what needs patching before applying system changes |
| `sudo apt upgrade -y` | Installs newest versions of all current packages | Core OS hardening practice |
| `sudo apt install -y <pkg>` | Installs target package and required dependencies | Ensure only authorized tools are provisioned |
| `apt show <pkg>` | Displays package metadata, maintainer, and dependencies | Verification step to inspect binary origins before execution |
| `sudo apt remove <pkg>` | Removes binaries but retains configuration files | Clean decommissioning of unneeded services |
| `sudo apt purge -y <pkg>` | Removes package and all associated configuration files | Leaves zero orphaned configuration files or potential backdoors |

---

## Lab Procedure & Verification

### 1. Update Package Index
Refresh local repository metadata against remote mirrors:
```bash
sudo apt update