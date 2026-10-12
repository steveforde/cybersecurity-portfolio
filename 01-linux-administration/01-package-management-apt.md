# Linux Administration: Software Management via APT

## Objective

Demonstrate installing, updating, inspecting, and purging software packages using Debian's Advanced Package Tool (`apt`) within an enterprise Linux environment.

---

## Scenario

As a security analyst, maintaining an accurate inventory of system utilities and ensuring binaries are sourced from verified repository mirrors is critical to vulnerability management and attack surface reduction.

---

## Command Reference

| Command | Purpose | Security Context |
| :--- | :--- | :--- |
| `sudo apt update` | Resynchronizes package index files from repositories | Prevents installing outdated binaries with known vulnerabilities (CVEs) |
| `sudo apt list --upgradable` | Lists packages with available patches | Audits what needs patching before applying system changes |
| `sudo apt upgrade -y` | Installs newest versions of all current packages | Core OS hardening practice |
| `sudo apt install -y <pkg>` | Installs the target package and required dependencies | Ensures only authorized tools are provisioned |
| `apt show <pkg>` | Displays package metadata, maintainer, and dependencies | Verification step to inspect binary origins before execution |
| `sudo apt remove <pkg>` | Removes binaries but retains configuration files | Clean decommissioning of unneeded services |
| `sudo apt purge -y <pkg>` | Removes the package and all associated configuration files | Leaves zero orphaned configuration files or potential backdoors |

---

## Lab Procedure & Verification

### 1. Update Package Index

Refresh local repository metadata against remote mirrors:

```bash
# Update repository package lists
sudo apt update

# Review pending security updates and upgrades
sudo apt list --upgradable
```

### 2. Inspect & Install Security Utilities

Verify package integrity, provenance, and install necessary analysis tools:

```bash
# Inspect package details before installation
apt show tcpdump

# Install the verified package
sudo apt install -y tcpdump
```

### 3. Decommission & Purge Software

Remove unused software to minimize the system attack surface:

```bash
# Remove package binaries
sudo apt remove tcpdump

# Purge residual configuration files
sudo apt purge -y tcpdump

# Automatically remove orphaned dependencies
sudo apt autoremove -y
```

---

## Key Security Takeaways

- **Attack Surface Reduction:** Regularly removing and purging unused utilities reduces the number of local privilege escalation targets.
- **Supply Chain Verification:** Inspecting package origins with `apt show` helps ensure packages come from official, signed repository mirrors.
- **Patch Hygiene:** Running regular package updates closes known vulnerabilities (CVEs) before they can be exploited.