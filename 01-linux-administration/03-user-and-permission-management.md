# Linux Administration: User, Group, and Permission Management

## Objective
Implement least-privilege access controls across Linux file systems, manage system user lifecycles, and configure role-based group assignments using CLI administration tools.

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
| `sudo usermod -L <user>` | Locks user account | Deactivates login capability during employee offboarding while preserving files |
| `sudo userdel -r <user>` | Deletes user and purges their home directory | Cleanly decommissions accounts without leaving orphaned user artifacts |
| `man <cmd>` / `whatis <cmd>` | Displays full manual or single-line description | Rapid CLI documentation verification during administrative tasks |
| `apropos -a <term1> <term2>` | Searches manual summaries for matching keywords | Discovers relevant administrative and security tools by task description |

---

## Permission String Architecture

```text
 d   r w x   r w x   r w x
[1] [2 3 4] [5 6 7] [8 9 10]
 |     |       |       |
 |     |       |       +---> Other (Everyone else on system)
 |     |       +-----------> Group permissions
 |     +-------------------> User (File owner) permissions
 +-------------------------> File type: 'd' = directory, '-' = regular file