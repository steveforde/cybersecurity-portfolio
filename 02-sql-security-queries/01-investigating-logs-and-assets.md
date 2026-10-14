# SQL Security Queries: Investigating Logs and Asset Management

## Objective

Use Structured Query Language (SQL) within a relational database management system (RDBMS) to investigate authentication logs, trace machine ownership, correlate assets with user identities, and audit organizational patch compliance.

---

## Scenario

Security incident investigations require analyzing millions of log entries quickly. By querying structured database records rather than scanning flat text files, analysts can isolate failed login attempts, trace compromised credentials, identify unpatched operating systems across an organization's device inventory, and tie suspicious activity back to specific employees.

---

## Command & Clause Reference

| Clause / Command | Purpose | Security Context |
| :--- | :--- | :--- |
| `sudo mysql organization` | Connects to the organization database via the MariaDB CLI | Opens direct shell access for database log inspection |
| `SELECT <cols>` | Specifies exact columns to return | Extracts only relevant evidence fields, reducing noise |
| `FROM <table>` | Defines the target table | Targets logs (`log_in_attempts`), inventory (`machines`), or personnel (`employees`) |
| `WHERE <condition>` | Filters records based on logical criteria | Isolates anomalies such as failed logins or out-of-date platforms |
| `AND` / `OR` / `NOT` | Combines or negates logical conditions | Chains forensic criteria (e.g., specific department AND unpatched OS) |
| `ORDER BY <col> DESC` | Sorts results sequentially | Arranges attack timelines in descending chronological order |
| `LIMIT <n>` | Caps output row count | Surfaces top findings (e.g., the 5 most recent events) |
| `INNER JOIN ... ON` | Combines tables on a shared key, keeping only matches | Correlates users, devices, and log events across tables |
| `LEFT JOIN ... ON` | Keeps all rows from the left table, matching rows from the right | Surfaces orphan assets via `NULL` values |
| `\c` | Cancels the current multi-line SQL statement | Resets the input buffer without executing erroneous syntax |

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
```

### 2. Personnel & Device Access Auditing

Audit device assignments to detect unauthorized hardware and isolate staff by department:

```sql
-- Isolate personnel within the Marketing department
SELECT *
FROM employees
WHERE department = 'Marketing';

-- Cross-department audit (Marketing and Finance)
SELECT *
FROM employees
WHERE department = 'Marketing' OR department = 'Finance';

-- Trace specific machine allocations back to employees
SELECT *
FROM employees
WHERE device_id = 'device_01' OR device_id = 'device_02';
```

### 3. Authentication & Incident Triage

Investigate authentication attempts to uncover brute-force attacks and establish incident timelines:

```sql
-- Surface all failed authentication events (success = 0)
SELECT *
FROM log_in_attempts
WHERE success = 0;

-- Filter authentication activity on a target incident date
SELECT *
FROM log_in_attempts
WHERE login_date = '2022-05-09';

-- Reconstruct recent timeline by returning the 5 latest attempts
SELECT *
FROM log_in_attempts
ORDER BY login_time DESC
LIMIT 5;
```

### 4. Cross-Table Asset & Incident Correlation (SQL Joins)

Combine relational tables using common keys (`device_id`, `username`) to correlate machine vulnerabilities with user identities and trace suspicious logins back to specific employees:

```sql
-- Correlate employees with assigned machines to verify current hardware baselines
SELECT employees.username, employees.department, machines.device_id, machines.operating_system
FROM employees
INNER JOIN machines
  ON employees.device_id = machines.device_id;

-- Audit unassigned inventory: list all machines and identify unallocated assets (NULL values)
SELECT machines.device_id, machines.operating_system, employees.username
FROM machines
LEFT JOIN employees
  ON machines.device_id = employees.device_id;

-- Incident analysis: map failed login attempts to specific personnel and departments
SELECT log_in_attempts.event_id, log_in_attempts.login_date, log_in_attempts.login_time,
       employees.username, employees.department
FROM log_in_attempts
INNER JOIN employees
  ON log_in_attempts.username = employees.username
WHERE log_in_attempts.success = 0;
```

#### Join Type Reference for Security Audits

| Join Type | Records Returned | Security Investigation Use Case |
| :--- | :--- | :--- |
| `INNER JOIN` | Only matching records present in both tables | Correlating active users with assigned hardware or known login events |
| `LEFT JOIN` | All records from Table A, plus matching records from Table B | Identifying orphan assets (e.g., workstations without assigned owners) via `WHERE TableB.id IS NULL` |
| `FULL OUTER JOIN` | All records from both tables | Complete reconciliation to spot unmanaged hardware and employees missing baseline equipment |

> **Note:** MariaDB/MySQL does not support `FULL OUTER JOIN` natively. Emulate it by combining a `LEFT JOIN` and a `RIGHT JOIN` with `UNION`.

---

## Key Security Takeaways

- **Principle of Least Privilege:** Departmental queries allow auditing user roles to verify access grants match actual job duties.
- **Incident Reconstruction:** Chronological sorting (`ORDER BY login_time DESC`) enables analysts to build timelines around initial compromise vectors.
- **Asset Integrity:** Mapping employees to device IDs prevents unmonitored shadow IT from connecting to the internal corporate network.
- **Asset Ownership Verification:** Joining directory services (`employees`) with device inventory (`machines`) flags shadow IT, unassigned hardware, and rogue assets.
- **Accountable Incident Response:** Joining authentication logs (`log_in_attempts`) with user directories maps anonymous account abuse directly to real identities, offices, and departments.
- **Data Gap Analysis:** Using outer joins to detect `NULL` values highlights compliance blind spots, such as machines unlinked from asset tracking systems.