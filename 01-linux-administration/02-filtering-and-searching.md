# Linux Administration: Searching, Filtering, and Piping (`find`, `grep`, `|`)

## Objective

Demonstrate techniques for locating files, inspecting log contents, and chaining commands to isolate security indicators using `find`, `grep`, and Unix pipelines.

---

## Scenario

During incident response and log auditing, analysts must rapidly search file systems for indicator strings (e.g., malware hashes, error messages, user IDs) and locate modified files without opening directories manually.

---

## Core Command Reference

| Command / Flag | Purpose | Security Application |
| :--- | :--- | :--- |
| `grep <pattern> <file>` | Searches for matching text strings inside a file | Locating failed login events, specific IPs, or error codes in logs |
| `grep -i <pattern> <file>` | Case-insensitive string search | Catches variations in user inputs, signatures, or log outputs |
| `\|` (Pipeline) | Redirects `stdout` of one command into `stdin` of another | Chaining directory listings, filters, and word counters together |
| `find <path> -name "<pattern>"` | Recursively searches directory paths by case-sensitive name | Finding target config files, scripts, or suspicious file extensions |
| `find <path> -iname "<pattern>"` | Case-insensitive recursive name search | Locating files regardless of case obfuscation (e.g., `*Log*` vs `*log*`) |
| `find <path> -mtime -<days>` | Finds files modified within the last *N* days | Triage step: identifying recently added or altered files after an alert |
| `find <path> -mmin -<mins>` | Finds files modified within the last *N* minutes | Live containment: identifying newly dropped malware payloads |

---

## Practical Examples & Triage Workflows

### 1. Log Filtering with `grep`

Search authentication logs or update records for specific error events:

```bash
# Filter updates log specifically for operating system strings
grep OS updates.txt

# Search time logs for errors (case-insensitive)
grep -i "error" time_logs.txt
```

### 2. File System Hunting with `find`

Locate suspicious binaries, logs, or recently altered assets:

```bash
# Find all log files across the current system hierarchy
find / -name "*.log"

# Search case-insensitively for reports under /home
find /home -iname "*report*"

# Hunt for files modified within the last 24 hours (1 day)
find /var/log -mtime -1

# Detect binaries modified within the last 30 minutes
find /usr/bin -mmin -30
```

### 3. Pipeline Chaining (`|`)

Combine discovery and analysis tools for rapid telemetry filtering:

```bash
# Count total failed login attempts in an auth log
grep "Failed password" /var/log/auth.log | wc -l

# Search through running processes for an unauthorized daemon
ps aux | grep "netcat"

# Locate configuration files and filter for permission overrides
find /etc -name "*.conf" | grep "sudoers"
```