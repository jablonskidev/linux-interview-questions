# What is the difference between a daemon and a normal process?

**[Note: This is a work-in-progress draft.]**

## 🎯 Quick Answer

**Daemons** are background processes that run independently of user sessions and provide system services. **Normal processes** are typically interactive, user-initiated programs that depend on terminal sessions.

---

## 📊 Key Differences at a Glance

| Aspect | Daemon Process | Normal Process |
|--------|----------------|----------------|
| **Startup** | Boot time / System init | User command / Application launch |
| **Terminal** | No controlling terminal (?) | Has controlling terminal (tty) |
| **Parent** | Usually init (PID 1) | Shell or other user process |
| **Lifecycle** | Persistent until shutdown | Ends with user session |
| **Purpose** | System services | User tasks / Interactive programs |
| **Naming** | Often ends with "d" (sshd, httpd) | Varies (ls, vim, firefox) |
| **Privileges** | Often system/root | User permissions |

---

## 🔍 Process Identification

### How to Spot Daemons
```bash
# Look for processes with no TTY (shown as ?)
ps aux | grep "?"

# Check systemd services
systemctl list-units --type=service

# View process tree
pstree -p
```

### Common Daemon Examples
- **sshd** - SSH server daemon
- **httpd/nginx** - Web server daemons  
- **mysqld** - Database daemon
- **systemd** - Init system daemon
- **cron/crond** - Task scheduler daemon
- **rsyslog** - Logging daemon

---

## ⚙️ Technical Architecture

### Daemon Characteristics

#### **Process Detachment**
- Creates new session (`setsid()`)
- Becomes session leader
- No controlling terminal
- Closes standard file descriptors

#### **File Descriptor Handling**
```
stdin  (0) → /dev/null
stdout (1) → /dev/null or log file
stderr (2) → /dev/null or log file
```

#### **Working Directory**
- Usually changed to root (/) to avoid unmount issues
- Prevents holding references to user directories

### Normal Process Characteristics

#### **Terminal Association**
- Inherits controlling terminal from parent
- Can receive keyboard signals (Ctrl+C, Ctrl+Z)
- Standard I/O connected to terminal

#### **Process Groups**
- Belongs to foreground/background process group
- Subject to job control
- Terminated when shell exits

---

## 🚀 Lifecycle Management

### Daemon Startup Methods

#### **SystemD (Modern Linux)**
```bash
# Start service
sudo systemctl start servicename

# Enable at boot
sudo systemctl enable servicename

# Check status  
systemctl status servicename

# View logs
journalctl -u servicename
```

#### **SysV Init (Legacy)**
```bash
# Start service
sudo service servicename start

# Check status
sudo service servicename status

# Enable at boot
sudo chkconfig servicename on
```

#### **Manual Daemon Creation**
```bash
# Fork and detach process
nohup command &

# Use screen/tmux for persistence
screen -dmS daemon_name command
```

### Process Management Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ps aux` | List all processes | `ps aux \| grep daemon` |
| `pgrep` | Find process by name | `pgrep sshd` |
| `pkill` | Kill process by name | `pkill -f "python script.py"` |
| `killall` | Kill all named processes | `killall firefox` |
| `jobs` | Show background jobs | `jobs -l` |
| `nohup` | Run immune to hangups | `nohup ./script.sh &` |

---

## 📋 Service Management Across Distributions

### SystemD-based Systems
**Distributions:** Ubuntu 16+, CentOS 7+, Fedora, RHEL 7+, SUSE

```bash
# Service operations
systemctl start|stop|restart|reload service
systemctl enable|disable service
systemctl status service

# System state
systemctl list-units --type=service
systemctl list-unit-files --type=service
```

### SysV Init Systems  
**Distributions:** Older Ubuntu, CentOS 6, Debian 7

```bash
# Service operations
service servicename start|stop|restart|status
/etc/init.d/servicename start|stop|restart|status

# Boot configuration
chkconfig servicename on|off
update-rc.d servicename enable|disable
```

### OpenRC Systems
**Distributions:** Alpine Linux, Gentoo

```bash
# Service operations
rc-service servicename start|stop|restart|status
rc-update add servicename default
```

---

## 🔧 Troubleshooting Guide

### Common Issues & Solutions

#### **Service Won't Start**
1. **Check status and logs:**
   ```bash
   systemctl status servicename
   journalctl -u servicename -f
   ```

2. **Verify configuration:**
   ```bash
   # Test configuration syntax
   nginx -t
   apache2ctl configtest
   ```

3. **Check permissions and ownership:**
   ```bash
   ls -la /etc/servicename/
   ps aux | grep servicename
   ```

#### **High Resource Usage**
1. **Monitor resource consumption:**
   ```bash
   top -p $(pgrep servicename)
   htop -p $(pgrep servicename)
   ```

2. **Check open files:**
   ```bash
   lsof -p $(pgrep servicename)
   ```

3. **Analyze memory usage:**
   ```bash
   pmap $(pgrep servicename)
   ```

#### **Service Crashes/Restarts**
1. **Check system logs:**
   ```bash
   journalctl --since "1 hour ago" | grep servicename
   dmesg | grep servicename
   ```

2. **Review core dumps:**
   ```bash
   coredumpctl list servicename
   coredumpctl info servicename
   ```

---

## 📈 Monitoring & Performance

### Essential Monitoring Commands

#### **Real-time Process Monitoring**
```bash
# Interactive process viewer
htop

# Watch specific processes
watch -n 1 'ps aux | grep daemon_name'

# Monitor resource usage
iotop    # I/O usage
nethogs  # Network usage per process
```

#### **Service Health Checks**
```bash
# Check if service is running
systemctl is-active servicename

# Check if service is enabled
systemctl is-enabled servicename

# Get detailed service information
systemctl show servicename
```

### Log Analysis

#### **SystemD Logs (journald)**
```bash
# Follow service logs
journalctl -u servicename -f

# Show logs since boot
journalctl -u servicename -b

# Filter by time
journalctl -u servicename --since "2024-01-01"
```

#### **Traditional Syslog**
```bash
# Common log locations
tail -f /var/log/syslog
tail -f /var/log/messages
tail -f /var/log/daemon.log

# Service-specific logs
tail -f /var/log/nginx/error.log
tail -f /var/log/mysql/error.log
```

---

## 🛡️ Security Considerations

### Daemon Security Best Practices

#### **User and Permissions**
- Run with least privilege principle
- Use dedicated service users
- Avoid running as root when possible

#### **Configuration Security**
```bash
# Secure configuration files
chmod 600 /etc/servicename/config
chown service_user:service_group /etc/servicename/

# Check service user
ps aux | grep servicename
```

#### **Network Security**
```bash
# Check listening ports
netstat -tlnp | grep servicename
ss -tlnp | grep servicename

# Monitor network connections
lsof -i -P | grep servicename
```

---

## 🎛️ Advanced Topics

### Signal Handling

| Signal | Normal Process | Daemon Process |
|--------|----------------|----------------|
| **SIGTERM** | Terminates | Graceful shutdown |
| **SIGHUP** | Terminates | Reload config |
| **SIGINT** | Interrupts (Ctrl+C) | Usually ignored |
| **SIGKILL** | Force kill | Force kill |

### Process Supervision

#### **SystemD Supervision**
```ini
# /etc/systemd/system/myservice.service
[Unit]
Description=My Service
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=10
User=myuser
ExecStart=/usr/bin/myservice

[Install]
WantedBy=multi-user.target
```

#### **Manual Supervision**
```bash
# Simple restart loop
while true; do
    ./myservice
    echo "Service crashed, restarting..."
    sleep 5
done
```

---

## 📚 Quick Reference

### Must-Know Commands
```bash
# Process inspection
ps aux | head -20
pstree -p | head -20
top
htop

# Service management (SystemD)
systemctl status
systemctl list-units --type=service --state=running
journalctl -f

# Service management (SysV)
service --status-all
chkconfig --list

# Process control
kill -TERM <pid>
killall <process_name>
nohup <command> &
```

### Key Directories
- `/etc/systemd/system/` - SystemD service files
- `/etc/init.d/` - SysV init scripts  
- `/var/log/` - System and service logs
- `/run/` - Runtime data and PID files
- `/proc/` - Process information filesystem

---

## 🎯 Summary

**Daemons** are the backbone of Linux systems, providing essential services that run continuously in the background. Understanding the difference between daemons and normal processes is crucial for:

- **System Administration:** Managing services and troubleshooting issues
- **Performance Monitoring:** Identifying resource usage patterns  
- **Security:** Securing system services and monitoring access
- **Development:** Creating robust background services

Master these concepts to effectively manage any Linux system, regardless of distribution or init system.
