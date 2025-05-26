# How do you schedule tasks in Linux?

**[Note: This is a work-in-progress draft.]**

## Short Answer

**How do you schedule tasks in Linux?**

Use `cron` for recurring tasks by editing your crontab with `crontab -e` and adding entries in the format: `minute hour day month weekday command`. For example, `0 2 * * * /path/to/backup.sh` runs a backup daily at 2 AM. For one-time future tasks, use `at`. Modern systems also support systemd timers for advanced scheduling.

---

## Quick Reference Card

| Tool | Use Case | Command | Best For |
|------|----------|---------|----------|
| **cron** | Recurring tasks | `crontab -e` | Regular maintenance, backups |
| **at** | One-time tasks | `echo "cmd" \| at time` | Future single execution |
| **systemd** | Advanced scheduling | `systemctl enable timer` | Complex dependencies |
| **anacron** | Catch-up tasks | Automatic | Systems with downtime |

---

## Cron - The Universal Scheduler

### Crontab Syntax Reference

```
┌───────────── minute (0 - 59)
│ ┌─────────── hour (0 - 23)
│ │ ┌───────── day of month (1 - 31)
│ │ │ ┌─────── month (1 - 12)
│ │ │ │ ┌───── day of week (0 - 7) (Sunday=0 or 7)
│ │ │ │ │
* * * * * command-to-execute
```

### Special Characters

| Symbol | Meaning | Example | Result |
|--------|---------|---------|---------|
| `*` | Any value | `* * * * *` | Every minute |
| `,` | List separator | `0,30 * * * *` | 0 and 30 minutes |
| `-` | Range | `1-5 9 * * *` | 9 AM, Mon-Fri |
| `/` | Step values | `*/15 * * * *` | Every 15 minutes |
| `@` | Keywords | `@daily` | Once per day |

### Predefined Keywords

| Keyword | Equivalent | When It Runs |
|---------|------------|--------------|
| `@yearly` | `0 0 1 1 *` | January 1st at midnight |
| `@monthly` | `0 0 1 * *` | 1st of every month |
| `@weekly` | `0 0 * * 0` | Every Sunday |
| `@daily` | `0 0 * * *` | Every day at midnight |
| `@hourly` | `0 * * * *` | Every hour |
| `@reboot` | N/A | At system startup |

### Common Cron Examples

```bash
# System Maintenance
0 2 * * *        /usr/local/bin/backup.sh           # Daily backup at 2 AM
0 4 * * 0        /usr/bin/updatedb                  # Update file database weekly
*/30 * * * *     /usr/local/bin/cleanup-temp.sh     # Clean temp files every 30 min

# Development Tasks
0 9 * * 1-5      /home/user/deploy.sh               # Deploy Mon-Fri at 9 AM
*/5 9-17 * * 1-5 /usr/local/bin/health-check.sh     # Health check business hours

# Log Management
0 0 * * *        /usr/sbin/logrotate /etc/logrotate.conf  # Rotate logs daily
```

### Crontab Management Commands

| Action | Command | Description |
|--------|---------|-------------|
| Edit | `crontab -e` | Open user's crontab in editor |
| List | `crontab -l` | Show current crontab |
| Remove | `crontab -r` | Delete entire crontab |
| Edit as root | `sudo crontab -e` | Edit system crontab |
| Edit for user | `sudo crontab -e -u username` | Edit another user's crontab |

---

## System Cron Directories

### Automatic Execution Folders

```
/etc/
├── cron.hourly/     ← Scripts run every hour
├── cron.daily/      ← Scripts run daily (usually 6:25 AM)
├── cron.weekly/     ← Scripts run weekly (Sunday 6:47 AM)
└── cron.monthly/    ← Scripts run monthly (1st day, 6:52 AM)
```

**Advantages:**
- ✅ No crontab syntax needed
- ✅ Automatic execution
- ✅ Easy script management

**Requirements:**
- Scripts must be executable (`chmod +x`)
- No file extensions (e.g., `backup` not `backup.sh`)
- Proper shebang line (`#!/bin/bash`)

---

## At Command - One-Time Scheduling

### Basic Usage

| Task | Command | Example |
|------|---------|---------|
| Schedule command | `echo "command" \| at time` | `echo "reboot" \| at 11:30 PM` |
| Schedule from file | `at time < script.sh` | `at 2:00 AM < backup.sh` |
| Interactive mode | `at time` | `at now + 2 hours` |

### Time Formats

```bash
# Absolute times
at 10:30 AM
at 2:30 PM tomorrow
at 11:30 PM Dec 25

# Relative times
at now + 2 hours
at now + 30 minutes
at now + 1 week
at now + 1 month

# Special formats
at teatime    # 4:00 PM
at noon       # 12:00 PM
at midnight   # 12:00 AM
```

### At Management

| Action | Command | Purpose |
|--------|---------|---------|
| View queue | `atq` | List scheduled jobs |
| Remove job | `atrm job_number` | Cancel specific job |
| View job details | `at -c job_number` | Show job commands |

---

## Systemd Timers - Modern Scheduling

### Timer Creation Process

1. **Create Service File** (`/etc/systemd/system/backup.service`)
```ini
[Unit]
Description=Daily Backup Service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
User=backup
```

2. **Create Timer File** (`/etc/systemd/system/backup.timer`)
```ini
[Unit]
Description=Daily Backup Timer
Requires=backup.service

[Timer]
OnCalendar=daily
Persistent=true
RandomizedDelaySec=300

[Install]
WantedBy=timers.target
```

3. **Enable and Start**
```bash
sudo systemctl daemon-reload
sudo systemctl enable backup.timer
sudo systemctl start backup.timer
```

### Timer Schedule Formats

| Schedule | OnCalendar Value | Description |
|----------|------------------|-------------|
| Every 15 minutes | `*:0/15` | 15-minute intervals |
| Business hours | `Mon..Fri 9..17:00` | Weekdays 9 AM - 5 PM |
| Specific time | `*-*-* 02:30:00` | 2:30 AM daily |
| Monthly | `*-*-01 00:00:00` | 1st of each month |

### Timer Management

| Action | Command | Purpose |
|--------|---------|---------|
| List timers | `systemctl list-timers` | Show all active timers |
| Timer status | `systemctl status timer.timer` | Check timer details |
| View logs | `journalctl -u service.service` | Check execution logs |

---

## Environment and Troubleshooting

### Cron Environment Issues

**Problem:** Commands work in terminal but fail in cron

**Common Causes:**

| Issue | Solution | Example |
|-------|----------|---------|
| Limited PATH | Use full paths | `/usr/bin/python3` not `python3` |
| Missing variables | Set in crontab | `PATH=/usr/local/bin:/usr/bin:/bin` |
| No HOME | Specify explicitly | `HOME=/home/user` |
| No display | Set if needed | `DISPLAY=:0` |

### Crontab Environment Template

```bash
# Set environment variables at top of crontab
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
SHELL=/bin/bash
MAILTO=admin@example.com

# Your cron jobs below
0 2 * * * /usr/local/bin/backup.sh
```

### Debugging Checklist

- [ ] **Test command manually** - Run the exact command from terminal
- [ ] **Check file permissions** - Ensure scripts are executable
- [ ] **Verify paths** - Use absolute paths for all commands
- [ ] **Check logs** - Look in `/var/log/cron` or `/var/log/syslog`
- [ ] **Test with minimal cron** - Start with simple command like `date > /tmp/test`
- [ ] **Verify cron service** - Ensure cron daemon is running

---

## Security and Best Practices

### Access Control

| File | Purpose | Format |
|------|---------|---------|
| `/etc/cron.allow` | Whitelist users | One username per line |
| `/etc/cron.deny` | Blacklist users | One username per line |

**Rule:** If `cron.allow` exists, only listed users can use cron. Otherwise, all users except those in `cron.deny` can use cron.

### Script Best Practices

```bash
#!/bin/bash
# Good cron script template

# Use full paths
PATH=/usr/local/bin:/usr/bin:/bin

# Set working directory
cd /home/user/scripts || exit 1

# Log execution
echo "$(date): Starting backup" >> /var/log/backup.log

# Your commands here with error handling
if /usr/local/bin/backup.sh; then
    echo "$(date): Backup completed successfully" >> /var/log/backup.log
else
    echo "$(date): Backup failed" >> /var/log/backup.log
    exit 1
fi
```

### Security Guidelines

- ✅ Use specific user accounts for different tasks
- ✅ Limit file permissions (`chmod 700` for scripts)
- ✅ Validate all input in scripts
- ✅ Use full paths to prevent PATH injection
- ✅ Log all activities for audit trails
- ❌ Never run unnecessary tasks as root
- ❌ Don't ignore error outputs
- ❌ Avoid hardcoded passwords in scripts

---

## Quick Troubleshooting Guide

### Common Problems and Solutions

| Problem | Symptoms | Solution |
|---------|----------|----------|
| **Job doesn't run** | No output, no logs | Check cron service: `systemctl status cron` |
| **Permission denied** | "Permission denied" in logs | Fix permissions: `chmod +x script.sh` |
| **Command not found** | "command not found" error | Use full path: `/usr/bin/command` |
| **Script runs but fails** | Unexpected behavior | Test script manually with same environment |
| **No email notifications** | Missing job reports | Configure mail system or redirect output |

### Log Locations by Distribution

| Distribution | Cron Logs | System Logs |
|--------------|-----------|-------------|
| **Ubuntu/Debian** | `/var/log/cron.log` | `/var/log/syslog` |
| **CentOS/RHEL** | `/var/log/cron` | `/var/log/messages` |
| **Arch Linux** | `journalctl -u cronie` | `journalctl` |
| **openSUSE** | `/var/log/cron` | `/var/log/messages` |

---

## Monitoring and Maintenance

### Health Check Script

```bash
#!/bin/bash
# Check if important cron jobs are running

LOGFILE="/var/log/cron-health.log"
DATE=$(date)

echo "$DATE: Checking cron jobs..." >> $LOGFILE

# Check if backup ran today
if grep -q "$(date +%Y-%m-%d)" /var/log/backup.log; then
    echo "$DATE: Backup job - OK" >> $LOGFILE
else
    echo "$DATE: Backup job - FAILED" >> $LOGFILE
    # Send alert
    mail -s "Backup job failed" admin@example.com < /dev/null
fi
```

### Regular Maintenance Tasks

| Frequency | Task | Command |
|-----------|------|---------|
| **Weekly** | Review cron logs | `grep CRON /var/log/syslog \| tail -50` |
| **Monthly** | Clean old logs | `find /var/log -name "*.log" -mtime +30 -delete` |
| **Quarterly** | Audit cron jobs | `crontab -l` and review necessity |
| **Yearly** | Update time references | Check for hardcoded dates in scripts |

---

## Summary

Task scheduling in Linux provides powerful automation capabilities through multiple complementary tools. Cron remains the universal standard for recurring tasks, while `at` handles one-time scheduling, systemd timers offer advanced features, and anacron ensures reliability on systems with irregular uptime.

**Key Takeaways:**
- Start with cron for most scheduling needs
- Use full paths and proper error handling in scripts
- Test commands manually before scheduling
- Monitor logs and implement health checks
- Follow security best practices for production systems

**Remember:** The best scheduled task is one that runs reliably, logs appropriately, and fails gracefully when problems occur.
