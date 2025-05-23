# What are the common process states in linux?

**[Note: This is a work-in-progress draft.]**

## Answer

Process states in Linux track what a process is currently doing:

- **Running (R):** Actively executing or ready to run
- **Sleeping (S):** Waiting for an event (I/O, input, etc.)
- **Uninterruptible sleep (D):** Waiting and can't be interrupted by signals
- **Zombie (Z):** Finished executing but parent hasn't collected exit status
- **Stopped (T):** Suspended by signal or debugger
- **Traced (t):** Being debugged

You can see process states with ps aux (STAT column) or top. These states help the kernel schedule processes and help you troubleshoot system issues.

## Background information

### Process states overview

| State | Symbol | Description | When it occurs | Resource usage |
|-------|--------|-------------|----------------|----------------|
| **Running** | R | Executing or ready to execute | Active on CPU or waiting in run queue | High CPU |
| **Interruptible sleep** | S | Waiting for event, can be interrupted | File I/O, network data, user input | Minimal |
| **Uninterruptible sleep** | D | Waiting for critical operation | Disk I/O, kernel operations | Minimal but problematic if stuck |
| **Stopped** | T | Paused by signal | Debugging, job control (Ctrl+Z) | Memory only |
| **Zombie** | Z | Terminated but not cleaned up | Parent hasn't retrieved exit status | Process table entry only |

### Essential monitoring commands

### Quick process state check

```bash
# Show all processes with states
ps axo pid,ppid,state,pcpu,comm --sort=-pcpu

# Real-time monitoring
top -o %CPU

# Count processes by state
ps axo state | sort | uniq -c
```

#### Problem identification

```bash
# Find stuck processes (D-state)
ps axo pid,ppid,state,comm | grep " D "

# Find zombies and their parents
ps axo pid,ppid,state,comm | grep " Z "

# Monitor state changes
watch -n 5 'ps axo state | sort | uniq -c'
```

#### System analysis

```bash
# Comprehensive process info
ps aux

# Process tree with relationships
pstree -p

# Detailed process status
cat /proc/[pid]/status | grep -E "State|Pid|PPid"
```

### Signal management

| Signal | Number | Effect | Use case |
|--------|--------|--------|----------|
| TERM | 15 | Graceful termination | Normal process shutdown |
| KILL | 9 | Force termination | Unresponsive processes |
| STOP | 19 | Pause execution | Debugging, job control |
| CONT | 18 | Resume execution | Continue stopped process |
| HUP | 1 | Hangup/reload | Configuration reload |

```bash
# Signal commands
kill -TERM pid     # Graceful shutdown
kill -9 pid        # Force kill
kill -STOP pid     # Pause process
kill -CONT pid     # Resume process
pkill process_name # Kill by name
```

### Common problems & solutions

### Problem: processes stuck in D-state

**Symptoms**: Processes in uninterruptible sleep that won't terminate
**Causes**: Hardware issues, failed I/O operations, network filesystem problems
**Solutions**:

```bash
# Check I/O subsystem
iostat -x 1
dmesg | grep -i error

# Check filesystem health
fsck /dev/device

# Monitor hardware logs
journalctl -k | grep -i error
```

#### Problem: zombie accumulation

**Symptoms**: Growing number of Z-state processes
**Causes**: Parent processes not cleaning up children
**Solutions**:

```bash
# Find zombie parents
ps axo pid,ppid,state,comm | awk '$3=="Z" {print $2}' | sort | uniq -c

# Signal parents to clean up
kill -CHLD parent_pid

# Restart problematic services
systemctl restart service_name
```

####  Problem: high CPU contention

**Symptoms**: Many R-state processes, high load average
**Causes**: CPU-bound processes, insufficient resources
**Solutions**:

```bash
# Identify CPU hogs
ps axo pid,pcpu,comm --sort=-pcpu | head -10

# Monitor load
uptime && cat /proc/loadavg

# Analyze CPU usage
top -n 1 -b | head -15
```

### Monitoring best practices

#### Establish baselines

```bash
# Create normal state distribution
ps axo state | sort | uniq -c > baseline_states.txt

# Compare current to baseline
ps axo state | sort | uniq -c | diff baseline_states.txt -
```

#### Automated monitoring

```bash
# Simple state monitor script
#!/bin/bash
D_COUNT=$(ps axo state | grep -c D)
Z_COUNT=$(ps axo state | grep -c Z)
[ "$D_COUNT" -gt 5 ] && echo "WARNING: $D_COUNT D-state processes"
[ "$Z_COUNT" -gt 10 ] && echo "WARNING: $Z_COUNT zombies"
```

#### Container environments

```bash
# Monitor containerized processes
docker exec container_name ps aux
kubectl exec pod_name -- ps aux

# Container-aware monitoring
docker top container_name
docker stats container_name
```

#### Process state modifiers

When viewing process states in tools like `ps`, additional characters provide context:

| Modifier | Meaning |
|----------|---------|
| < | High priority |
| N | Low priority |
| L | Memory locked |
| s | Session leader |
| + | Foreground process |

### Key `/proc` files reference

| File | Purpose | Usage |
|------|---------|--------|
| `/proc/[pid]/status` | Human-readable process info | `cat /proc/1234/status` |
| `/proc/[pid]/stat` | Machine-readable stats | Raw process statistics |
| `/proc/loadavg` | System load | Current load averages |
| `/proc/meminfo` | Memory statistics | System memory usage |

### Quick troubleshooting workflow

1. **Identify the problem**

   ```bash
   ps axo state | sort | uniq -c
   ```

3. **Find problematic processes**

   ```bash
   ps axo pid,ppid,state,comm | grep -E " [DZ] "
   ```

5. **Analyze system health**

   ```bash
   uptime && iostat -x 1 3
   ```

7. **Take corrective action**
   
   - D-state: Check hardware/I/O
   - Z-state: Signal parent processes
   - High R-state: Identify CPU hogs

9. **Monitor resolution**

   ```bash
   watch -n 5 'ps axo state | sort | uniq -c'
   ```

### When to be concerned

- **D-state processes**: More than 2-3 stuck for >5 minutes
- **Zombies**: More than 10 accumulating over time  
- **Load average**: Consistently above number of CPU cores
- **Memory pressure**: Combined with unusual state distributions

## Quiz

### Questions

### Answer key
