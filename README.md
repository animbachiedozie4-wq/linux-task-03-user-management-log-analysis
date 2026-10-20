# Linux Task 03 — User Management, Log Analysis & Permissions

## Overview
This repository documents the Linux Task 03 practical exercise covering:
- User setup and sudo access (`forensic01`)
- Creating and filtering mock system-event logs
- Pipes, redirection, `head`, `tail`, `cat`, and `less`
- File permissions and ownership (`chmod`, `chown`)
- Temporary user management (`guest1`, `guest2`)
- Command evidence and screenshots

> **Safety note:** Run these commands only in a Linux virtual machine/lab where you have administrative access. User-management commands change the VM's accounts. Do not run them on a production system. The log entries are mock data, not real system logs.

## Repository structure
```text
linux-task-03/
├── README.md
├── commands_used.txt
├── explanations.md
├── .gitignore
├── screenshots/
│   └── README.md
├── logs/
│   ├── sys_events.log
│   └── error_report.txt
├── evidence/
│   └── README.md
└── docs/
    └── README.md
```

## Prerequisites
- Linux VM (Ubuntu, Kali Linux, or another Debian-based distribution)
- A user account with `sudo` privileges
- Git installed on your Windows computer (Git Bash) and a GitHub account

## Lab workflow

### Part 1 — Create `forensic01`
Run these on the Linux VM from an account with sudo access:
```bash
sudo useradd -m -s /bin/bash forensic01
sudo usermod -aG sudo forensic01
sudo passwd forensic01
```
If the account already exists, do not rerun `useradd`; check it first with `id forensic01`.

Log in as the new user:
```bash
su - forensic01
```
Enter the password you set when prompted.

Create the log directory and sample log file:
```bash
mkdir -p ~/logs
cd ~/logs
cat > sys_events.log <<'EOF'
2026-10-14 08:00:01 INFO user01 logged in from 192.0.2.10
2026-10-14 08:01:12 WARNING user01 entered an invalid password
2026-10-14 08:02:03 ERROR authentication failed for user01
2026-10-14 08:03:45 INFO root started scheduled maintenance
2026-10-14 08:04:10 ERROR sudo authentication failure for user01
2026-10-14 08:05:22 WARNING disk usage reached 75 percent
2026-10-14 08:06:14 INFO service ssh restarted by root
2026-10-14 08:07:31 ERROR failed login attempt for guest1
2026-10-14 08:08:02 INFO user01 opened a session
2026-10-14 08:09:48 WARNING repeated login attempts detected
2026-10-14 08:10:17 ERROR permission denied while reading confidential.txt
2026-10-14 08:11:09 INFO root reviewed system logs
2026-10-14 08:12:33 WARNING account guest2 is temporary
2026-10-14 08:13:07 INFO user01 ran grep on sys_events.log
2026-10-14 08:14:56 ERROR service health check failed
2026-10-14 08:15:21 INFO root changed file ownership
2026-10-14 08:16:42 WARNING password policy check required
2026-10-14 08:17:18 ERROR access denied for user01
2026-10-14 08:18:39 INFO user01 saved error_report.txt
2026-10-14 08:19:50 WARNING review sudo group membership
EOF
```
This creates 20 lines of clearly identified mock events.

### Part 2 — Filter and review logs
From `~/logs`:
```bash
grep "ERROR" sys_events.log
grep -E "root|user01" sys_events.log
grep "ERROR" sys_events.log | less
grep "ERROR" sys_events.log > error_report.txt
head -n 5 sys_events.log
tail -n 5 sys_events.log
cat sys_events.log
```
Press `q` to exit `less`. Verify the report:
```bash
cat error_report.txt
wc -l sys_events.log error_report.txt
```

### Part 3 — Permissions and ownership
Run as `forensic01`:
```bash
touch confidential.txt
chmod 600 confidential.txt
ls -l confidential.txt
```
`600` means owner read/write, group no access, others no access. To make the ownership demonstration clear, add harmless sample text:
```bash
echo "Mock confidential lab note" > confidential.txt
cat confidential.txt
```
Then change ownership:
```bash
sudo chown root:root confidential.txt
ls -l confidential.txt
cat confidential.txt
echo "Attempt to append as forensic01" >> confidential.txt
```
Because the file is mode `600` and owned by root, `forensic01` should be denied access. The append command should fail; that is expected. Do not use `sudo` for the read/write test, because that would test root's access instead.

### Part 4 — User management
From an account with sudo privileges:
```bash
sudo useradd -m -s /bin/bash guest1
sudo useradd -m -s /bin/bash guest2
sudo usermod -aG sudo guest1
id guest1
id guest2
sudo userdel -r guest2
```
If a temporary account already exists, inspect it first with `id guest1` or `id guest2`. The `-r` option removes the deleted user's home directory and mail spool where applicable. Keep `guest1` if the exercise does not ask you to delete it.

## Submission checklist
- [ ] `commands_used.txt` contains the commands actually run
- [ ] `screenshots/` contains your real terminal screenshots
- [ ] `explanations.md` answers the required command and permissions questions
- [ ] `logs/sys_events.log` contains at least 20 mock lines
- [ ] `logs/error_report.txt` contains the actual grep output
- [ ] Repository is pushed to GitHub

## Capture evidence
Suggested screenshots (save PNG/JPG files in `screenshots/`):
1. `01-user-setup.png` — `id forensic01` and `getent passwd forensic01`
2. `02-mock-log.png` — `wc -l sys_events.log` and `head -n 5 sys_events.log`
3. `03-grep-error.png` — `grep "ERROR" sys_events.log`
4. `04-grep-root-user01.png` — `grep -E "root|user01" sys_events.log`
5. `05-head-tail.png` — first and last five lines
6. `06-permissions-before.png` — `ls -l confidential.txt` before `chown`
7. `07-ownership-after.png` — `ls -l confidential.txt` after `sudo chown root:root`
8. `08-access-denied.png` — attempted read/append as `forensic01`
9. `09-user-management.png` — `id guest1`, `id guest2` before deletion, and confirmation after deletion

**Important:** These are filenames to create yourself. No screenshots have been fabricated or included.
