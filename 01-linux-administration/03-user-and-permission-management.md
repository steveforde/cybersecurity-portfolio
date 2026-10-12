# Linux Administration: User, Group, and Permission Management

## Objective

Implement least-privilege access controls across Linux file systems, manage system user lifecycles, and configure role-based group assignments using CLI administration tools.

---

## Scenario

As a security analyst, unauthorized access and privilege escalation represent significant security risks. Ensuring files have restrictive permissions, removing former employee accounts, and requiring administrative actions to execute through `sudo` rather than raw root logins are core system-hardening duties.

---

## Command Reference

| Command | Purpose | Security Context |
| :--- | :--- | :--- |
| `ls -la` | Lists all files with permissions in long format | Audits existing file permissions, ownership, and hidden dotfiles |
| `chmod <mode> <file>` | Changes read/write/execute permissions | Enforces the principle of least privilege on sensitive data |
| `sudo chown <user>:<group> <file>` | Modifies file owner and group affiliation | Restricts object ownership to authorized personnel and system groups |
| `sudo useradd -g <p_grp> -G <s_grp> <user>` | Creates a new user with defined group associations | Establishes role-based access for new employees |
| `sudo usermod -a -G <group> <user>` | Safely appends a supplemental group | Grants additional task privileges without wiping existing group memberships |
| `sudo usermod -L <user>` | Locks a user account | Deactivates login capability during employee offboarding while preserving files |
| `sudo userdel -r <user>` | Deletes a user and purges their home directory | Cleanly decommissions accounts without leaving orphaned user artifacts |
| `man <cmd>` / `whatis <cmd>` | Displays the full manual or a single-line description | Rapid CLI documentation verification during administrative tasks |
| `apropos -a <term1> <term2>` | Searches manual summaries for matching keywords | Discovers relevant administrative and security tools by task description |

---

## Permission String Architecture

```text
 d   r w x   r w x   r w x
[1] [2 3 4] [5 6 7] [8 9 10]
 |     |       |       |
 |     |       |       +---> Other (everyone else on the system)
 |     |       +-----------> Group permissions
 |     +-------------------> User (file owner) permissions
 +-------------------------> File type: 'd' = directory, '-' = regular file
```

| Symbol | Permission | Value | On a file | On a directory |
| :---: | :--- | :---: | :--- | :--- |
| `r` | Read | 4 | Read file contents | View directory listing |
| `w` | Write | 2 | Modify or overwrite the file | Create or delete files within the directory |
| `x` | Execute | 1 | Run the binary or script | Traverse into the directory (`cd`) |

---

## Lab Procedure & Verification

### 1. Hardening File Permissions (Least Privilege)

Audit file permissions on sensitive payroll data and revoke group read/write privileges:

```bash
# Check initial file permissions
ls -l bonuses.txt

# Revoke group read and write permissions
chmod g-rw bonuses.txt

# Enforce explicit permissions (Owner: read/write/execute, Group: read, Other: none)
chmod u=rwx,g=r,o= script.sh
```

### 2. File Ownership Reassignment

Transfer ownership of security policy documents:

```bash
# Set user owner to fgarcia
sudo chown fgarcia access.txt

# Set group owner to the security department
sudo chown :security access.txt

# Set both user and group simultaneously
sudo chown fgarcia:security access.txt
```

### 3. User Lifecycle Management

Provision, modify, and securely decommission employee accounts:

```bash
# Provision a new user with primary group 'security' and supplemental group 'admin'
sudo useradd -g security -G admin fgarcia

# Safely append the user to the marketing group without overwriting existing groups
sudo usermod -a -G marketing fgarcia

# Lock a departing employee's account immediately for incident response / auditing
sudo usermod -L fgarcia

# Decommission the employee account and purge the home directory
sudo userdel -r fgarcia
```