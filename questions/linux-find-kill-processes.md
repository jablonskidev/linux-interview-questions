# How do you find and kill processes in Linux?

**[Note: This is a work-in-progress draft.]**

## Quick Answer

**To find processes:** Use `ps aux` to list all processes, `pgrep firefox` to find by name, or `top` for interactive viewing.

**To kill processes:** Use `kill 1234` for graceful termination (try first), or `kill -9 1234` to force kill. You can also use `pkill firefox` to kill by name.

**Example:** `ps aux | grep firefox` finds Firefox processes, then `kill 1234` terminates process 1234 gracefully.

---

Managing processes is a fundamental skill every Linux user needs. Whether you're dealing with hung applications, resource-hungry programs, or need to clean up system resources, knowing how to find and terminate processes safely is essential.

## Quick Reference Card

| Task | Command | Example |
|------|---------|---------|
| List all processes | `ps aux` | `ps aux` |
| Find by name | `pgrep name` | `pgrep firefox` |
| Kill gracefully | `kill PID` | `kill 1234` |
| Force kill | `kill -9 PID` | `kill -9 1234` |
| Kill by name | `pkill name` | `pkill firefox` |
| Kill all instances | `killall name` | `killall chrome` |

---

## Understanding Linux Processes

A **process** is a running instance of a program. Every command you execute creates one or more processes with unique identifiers (PIDs).

**Key concepts:**
- **PID**: Process ID (unique identifier)
- **PPID**: Parent Process ID  
- **Child processes**: Created by parent processes
- **Process hierarchy**: Like a family tree where killing parents affects children

---

## Finding Processes

### Essential Commands

```bash
# List all processes
ps aux

# Find by name
pgrep firefox

# Interactive monitoring
top

# Find with details
ps aux | grep firefox
```

### Understanding ps aux Output

| Column | Meaning | Example |
|--------|---------|---------|
| USER | Process owner | `root`, `john` |
| PID | Process ID | `1234` |
| %CPU | CPU usage | `2.5` |
| %MEM | Memory usage | `1.8` |
| COMMAND | Program name | `firefox` |

### Advanced Filtering

```bash
# High CPU processes
ps aux --sort=-%cpu | head -5

# High memory processes  
ps aux --sort=-%mem | head -5

# Processes by user
ps aux | grep ^username

# Pattern matching
pgrep -f "partial_command"
```

---

## Process Signals

Before killing processes, understand the signals:

| Signal | Number | Effect | When to Use |
|--------|--------|--------|-------------|
| TERM | 15 | Graceful shutdown | **Try first** |
| KILL | 9 | Force termination | **Last resort** |
| INT | 2 | Interrupt (Ctrl+C) | Interactive stop |
| HUP | 1 | Reload config | Restart services |

### **Always follow this order:**
1. **SIGTERM (15)** - Let process clean up gracefully
2. **Wait 5-10 seconds**  
3. **SIGKILL (9)** - Force kill if needed

---

## Killing Processes

### By Process ID

```bash
# Graceful termination
kill 1234

# Force kill
kill -9 1234

# Kill multiple
kill 1234 5678 9012
```

### By Process Name

```bash
# Kill by name
pkill firefox

# Kill all instances
killall firefox

# Force kill all
killall -9 firefox

# Kill by user
pkill -u username
```

### Combined Commands

```bash
# Find and kill in one command
kill $(pgrep firefox)

# Kill all Chrome processes
pkill chrome
```

---

## Common Scenarios

| Problem | Solution | Command |
|---------|----------|---------|
| Hung application | Find PID, then graceful kill | `ps aux \| grep app` → `kill PID` |
| High CPU usage | Find top CPU process | `ps aux --sort=-%cpu \| head -5` |
| Memory hog | Find memory-heavy process | `ps aux --sort=-%mem \| head -5` |
| User session cleanup | Kill all user processes | `pkill -u username` |
| Won't respond | Try graceful, then force | `kill PID` → wait → `kill -9 PID` |

### Step-by-Step Example

```bash
# 1. Find the problematic process
ps aux | grep firefox
# Output: john 1234 15.2 8.1 firefox

# 2. Try graceful kill
kill 1234

# 3. Check if still running (wait 10 seconds)
ps aux | grep 1234

# 4. Force kill if necessary
kill -9 1234
```

---

## Safety Guidelines

### ❌ **Never Kill These Processes**

| Process | PID | Why Not |
|---------|-----|---------|
| systemd/init | 1 | System crashes |
| kthreadd | 2 | Kernel panic |
| Kernel threads | Low PIDs | System instability |
| sshd | Any | Lose remote connection |

### ✅ **Safe to Kill**
- User applications (browsers, editors, games)
- Your own background processes  
- Hung or unresponsive programs
- Processes you started intentionally

### 🔍 **Check Before Killing**

```bash
# Identify the process
ps aux | grep PID

# Check if it's a system process (kernel threads in brackets)
ps aux | grep "^\[.*\]"
```

---

## Troubleshooting

| Issue | Symptom | Solution |
|-------|---------|----------|
| Process won't die | Still shows after `kill -9` | Check if zombie: `ps aux \| grep defunct` |
| Permission denied | "Operation not permitted" | Use `sudo kill PID` |
| System unresponsive | High load after killing | Kill gradually, check `uptime` first |
| Can't find process | `pgrep` returns nothing | Check exact name: `ps aux \| grep partial_name` |

### Zombie Processes

If a process shows `<defunct>` or `Z` status after `kill -9`, it's already dead but waiting for parent cleanup:

```bash
# Find parent process
ps aux --forest | grep -B5 ZOMBIE_PID

# Kill parent instead
kill PARENT_PID
```

---

## Advanced Commands

```bash
# Kill process group
kill -TERM -GROUP_ID

# Kill all children of a process
pkill -P PARENT_PID

# Get detailed process info
cat /proc/PID/status

# Kill current session processes
kill -TERM 0
```

---

## Quick Troubleshooting Checklist

**When a process won't respond:**

- [ ] Find exact PID: `ps aux | grep name`
- [ ] Try graceful kill: `kill PID`  
- [ ] Wait 10 seconds
- [ ] Check if running: `ps aux | grep PID`
- [ ] Force kill: `kill -9 PID`
- [ ] If still showing, check for zombie process
- [ ] Consider killing parent or rebooting

**Safety checklist:**

- [ ] Never kill PID 1 (init/systemd)
- [ ] Check process owner before killing
- [ ] Understand what the process does
- [ ] Use graceful termination first
- [ ] Have an exit strategy (especially remote connections)

---

## Summary

**Essential commands to remember:**

```bash
ps aux                    # List all processes
pgrep firefox            # Find Firefox PID  
kill 1234                # Graceful kill
kill -9 1234            # Force kill
pkill firefox           # Kill by name
killall chrome          # Kill all Chrome instances
```

**Key principles:**
- **Always try graceful termination first** (`kill PID` before `kill -9 PID`)
- **Identify before you terminate** (know what you're killing)
- **Safety first** (never kill system-critical processes)
- **When in doubt, don't kill** (especially low PID numbers)

Master these fundamentals and you'll handle Linux process management confidently and safely!
