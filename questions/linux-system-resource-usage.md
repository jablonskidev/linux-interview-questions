# How do you check system resource usage in Linux?

## Quick Answer

Use these essential commands to check Linux system resources:
- **`top`** or **`htop`** - Overall system view (CPU, memory, processes)
- **`free -h`** - Memory usage in human-readable format
- **`df -h`** - Disk space usage across filesystems
- **`iostat -x`** - Disk I/O performance statistics
- **`ss -tuln`** - Network connections and listening ports

For historical data, use **`sar`** from the sysstat package.

---

System resource monitoring is the backbone of effective Linux administration. Whether you're troubleshooting performance issues, planning capacity, maintaining system health, or detecting security incidents, knowing how to check CPU, memory, disk, and network usage is essential.

## Essential Monitoring Tools

| Tool | Purpose | Key Command | Best For |
|------|---------|-------------|----------|
| `top`/`htop` | Process & system overview | `htop`, `top -u user` | General monitoring, runaway processes |
| `free` | Memory usage | `free -h` | Memory pressure, swap usage |
| `df` | Disk space | `df -h` | Disk space, inode exhaustion |
| `iostat` | I/O performance | `iostat -x 1` | Disk bottlenecks, I/O wait |
| `iotop` | Per-process I/O | `iotop -o` | Process-level disk usage |
| `vmstat` | Virtual memory stats | `vmstat 2` | CPU, memory, I/O overview |
| `dstat` | Versatile system stats | `dstat -cdngy` | Comprehensive monitoring |
| `glances` | All-in-one monitor | `glances` | Web interface, remote monitoring |
| `ss`/`netstat` | Network connections | `ss -tuln` | Port monitoring, connections |
| `nethogs` | Per-process network | `nethogs` | Network usage by process |
| `sar` | Historical data | `sar -u`, `sar -r` | Trend analysis, capacity planning |

## Understanding Key Metrics

### **Memory Output (free -h)**
- **available** - Memory available for new processes (most important)
- **used** - Currently allocated memory
- **cached** - File system cache (can be freed if needed)
- **swap** - Virtual memory usage (high activity indicates memory pressure)

### **CPU Usage Types**
- **us (user)** - Application workload
- **sy (system)** - Kernel operations  
- **wa (iowait)** - Waiting for disk I/O (indicates storage bottlenecks)
- **st (steal)** - VM resource contention

### **Load Averages**
Load should stay below CPU core count. Load of 2.0 on dual-core = 200% utilization (overloaded).

## Performance Baselines by System Type

| System Type | Load | Memory | I/O Wait | Red Flags |
|-------------|------|--------|----------|-----------|
| **Web Server** | 0.5-2.0 | 60-80% | <5% | High network connections |
| **Database** | 1.0-4.0 | 80-95% | 5-15% | Excessive disk I/O |
| **File Server** | 0.2-1.0 | 40-60% | 10-30% | Network bottlenecks |
| **Development** | 0.1-1.5 | 30-70% | <10% | Unexpected resource spikes |

## Troubleshooting Workflow

**Step 1: System Overview** - `htop` or `glances` for immediate issues
**Step 2: Memory Check** - `free -h` for available memory and swap activity  
**Step 3: Disk Analysis** - `df -h` for space, `iostat -x` for I/O performance
**Step 4: Network Review** - `ss -tuln` for connections, `nethogs` for usage
**Step 5: Historical Context** - `sar` for trend analysis

## Common Bottlenecks & Solutions

| Issue | Symptoms | Key Indicators | Tools | Quick Fix |
|-------|----------|----------------|-------|-----------|
| **CPU** | Slow response | Load > cores, high us/sy | `top`, `htop` | Kill CPU-hungry processes |
| **Memory** | System sluggish | Low available, swap active | `free`, `vmstat` | Clear caches, kill memory hogs |
| **I/O** | Slow file ops | High iowait, util% | `iostat`, `iotop` | Check disk health, optimize FS |
| **Network** | Slow transfers | Dropped packets, timeouts | `ss`, `nethogs` | Check config, bandwidth limits |

## Security-Aware Monitoring

Watch for suspicious patterns:
- **CPU spikes** - Possible cryptomining, malware
- **Unusual network** - Data exfiltration, backdoors  
- **Abnormal I/O** - Log tampering, unauthorized access
- **Memory exhaustion** - Fork bombs, memory leaks

```bash
# Security monitoring commands
ss -tuln | grep -v '127.0.0.1'  # External connections
top -o %CPU | head -10          # Top CPU consumers
find /etc -type f -mtime -1     # Recent system changes
```

## Automation & Alerting

**Basic monitoring script:**
```bash
#!/bin/bash
LOG="/var/log/system-monitor.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

# Alert thresholds
LOAD=$(uptime | awk -F'load average:' '{print $2}' | awk '{print $1}' | sed 's/,//')
AVAIL=$(free | grep Mem | awk '{printf "%.0f", $7/$2 * 100.0}')

[ $(echo "$LOAD > 2.0" | bc -l) ] && echo "$DATE - HIGH LOAD: $LOAD" >> $LOG
[ "$AVAIL" -lt 10 ] && echo "$DATE - LOW MEMORY: ${AVAIL}%" >> $LOG
df -h | awk '$5 > 90 {print "'$DATE' - DISK FULL: " $6 " at " $5}' >> $LOG
```

**Enable historical collection:**
```bash
systemctl enable sysstat  # Auto-collect performance data
```

## The /proc Filesystem

Linux tools read from `/proc` virtual filesystem:
- `/proc/meminfo` - Memory stats (free, vmstat)
- `/proc/stat` - CPU stats (top, vmstat)  
- `/proc/loadavg` - Load averages
- `/proc/diskstats` - Disk I/O (iostat)
- `/proc/net/dev` - Network stats (ss, netstat)

## Quick Reference Commands

```bash
# System overview
htop                 # Interactive system monitor
glances              # Comprehensive web-enabled monitor
dstat                # Versatile real-time stats

# Specific checks  
free -h              # Memory usage
df -h                # Disk space
iostat -x 1          # I/O performance
iotop -o             # Active I/O processes
ss -tuln             # Network connections
nethogs              # Network usage by process

# Historical analysis
sar -u               # CPU usage trends
sar -r               # Memory usage trends  
sar -d               # Disk activity trends

# All-in-one check
watch 'uptime && free -h && df -h'
```

## Advanced Tips

**Combine tools effectively** - Use `glances` for overview, `iotop` for I/O details, `nethogs` for network analysis, and `sar` for historical context.

**Establish baselines** - Document normal ranges for your systems to detect anomalies quickly.

**Monitor proactively** - Set up automated alerts rather than waiting for problems to surface.

**Consider context** - A web server has different normal patterns than a database or development machine.

Start with the basics (`top`, `free`, `df`) then add specialized tools (`iotop`, `nethogs`, `dstat`) as your monitoring sophistication grows. Effective monitoring transforms system administration from reactive firefighting to proactive management.
