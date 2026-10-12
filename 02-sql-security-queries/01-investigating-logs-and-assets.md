```markdown
# SQL Security Queries: Investigating Logs and Asset Management

## Objective
Utilize Structured Query Language (SQL) within a relational database management system (RDBMS) to investigate authentication logs, trace machine ownership, and audit organizational patch compliance.

## Scenario
Security incident investigations require analyzing millions of log entries quickly. By querying structured database records rather than scanning flat text files, analysts can isolate failed login attempts, trace compromised credentials, and identify unpatched operating systems across an organization's device inventory.

---

## Command & Clause Reference

| Clause / Command | Purpose | Security Context |
| :--- | :--- | :--- |
| `sudo mysql organization` | Connects to the organization database via MariaDB CLI | Opens direct shell access for database log inspection |
| `SELECT <cols>` | Specifies exact columns to return | Extracts only relevant evidence fields, reducing noise |
| `FROM <table>` | Defines the target table | Targets logs (`log_in_attempts`), inventory (`machines`), or personnel (`employees`) |
| `WHERE <condition>` | Filters records based on logical criteria | Isolates anomalies such as failed logins or out-of-date platforms |
| `AND` / `OR` / `NOT` | Combines or negates logical conditions | Chains forensic criteria (e.g., specific department AND unpatched OS) |
| `ORDER BY <col> DESC` | Sorts results sequentially | Arranges attack timelines in descending chronological order |
| `LIMIT <n>` | Caps output row count | Surfaces top findings (e.g., the 5 most recent events) |
| `\c` | Cancels the current multi-line SQL statement | Resets input buffer without executing erroneous syntax |

---

## Investigation Procedures & Queries

### 1. Hardware & OS Compliance Auditing
Identify workstations running legacy operating systems to verify configuration baselines and patch management:

```sql
-- Retrieve device identifiers and assigned operating systems
SELECT device_id, operating_system 
FROM machines;

-- Filter for all machines running Linux
SELECT * 
FROM machines 
WHERE operating_system = 'Linux';

-- Identify systems requiring OS X baseline security updates
SELECT * 
FROM machines 
WHERE operating_system = 'OS X';