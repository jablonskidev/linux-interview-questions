# How would you troubleshoot network connectivity issues in Linux?

**[Note: This is a work-in-progress draft.]**

## Quick Answer: How to Troubleshoot Network Connectivity Issues in Linux

**Start with the basics and work systematically:** First check physical connections, then validate IP configuration with `ip addr` or `ifconfig`. Test basic connectivity with `ping` to local gateway and remote hosts. Use `traceroute` to identify routing problems. Check open ports with `netstat -tuln` or `ss -tuln`. Examine firewall rules with `iptables -L`. For deeper analysis, use `tcpdump` or `wireshark` to capture and analyze network traffic.

**Key diagnostic commands:** `ping`, `ip addr show`, `ss -tuln`, `dig`, `traceroute`, `iptables -L`, `tcpdump`

---

## Quick Reference: The 5-Minute Checklist

| Step | Command | What It Tests | Fix If Fails |
|------|---------|---------------|--------------|
| 1 | `ip link show` | Interface status | `sudo ip link set <interface> up` |
| 2 | `ip addr show` | IP configuration | Check DHCP/static config |
| 3 | `ping 127.0.0.1` | Local stack | System/driver issue |
| 4 | `ping <gateway>` | Local network | Check cables/switch |
| 5 | `ping 8.8.8.8` | Internet access | Check routing/firewall |
| 6 | `ping google.com` | DNS resolution | Check `/etc/resolv.conf` |

---

## The Systematic Approach: Bottom-Up Troubleshooting

```
Application Layer    ←  Test with curl, wget, telnet
    ↓
Transport Layer      ←  Check with ss, netstat  
    ↓
Network Layer        ←  Test with ping, traceroute
    ↓
Data Link Layer      ←  Verify with ip link, ethtool
    ↓
Physical Layer       ←  Check cables, hardware
```

---

## Phase 1: Initial Assessment

### Interface Status Check

**Modern Commands (Preferred):**
```bash
# Check all interfaces
ip link show

# Check specific interface
ip link show eth0

# Bring interface up
sudo ip link set eth0 up
```

**Traditional Commands:**
```bash
# Interface information
ifconfig

# Specific interface
ifconfig eth0

# Bring interface up
sudo ifconfig eth0 up
```

### IP Configuration Verification

| Command | Purpose | Key Info to Check |
|---------|---------|-------------------|
| `ip addr show` | Current IP settings | IP address, subnet mask, scope |
| `ip route show` | Routing table | Default gateway, routes |
| `ip neigh show` | ARP table | MAC address mappings |

**Expected Output Patterns:**
```bash
# Good interface status
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0

# Default route present
default via 192.168.1.1 dev eth0 proto dhcp metric 100
```

---

## Phase 2: Connectivity Testing

### The Ping Progression

| Test | Command | Success Means | Failure Indicates |
|------|---------|---------------|-------------------|
| **Loopback** | `ping 127.0.0.1` | TCP/IP stack works | System-level issue |
| **Gateway** | `ping <gateway_ip>` | Local network OK | Switch/cable problem |
| **External IP** | `ping 8.8.8.8` | Internet reachable | Routing/firewall issue |
| **Hostname** | `ping google.com` | DNS working | DNS configuration |

### Advanced Connectivity Tests

```bash
# Test specific ports
telnet google.com 80
nc -zv google.com 80

# Test with different packet sizes
ping -s 1472 8.8.8.8

# IPv6 testing
ping6 ::1                    # IPv6 loopback
ping6 2001:4860:4860::8888  # Google IPv6 DNS
```

---

## Phase 3: DNS Troubleshooting

### DNS Configuration Files

| File | Purpose | Key Settings |
|------|---------|--------------|
| `/etc/resolv.conf` | DNS servers | `nameserver 8.8.8.8` |
| `/etc/hosts` | Local resolution | `127.0.0.1 localhost` |
| `/etc/nsswitch.conf` | Resolution order | `hosts: files dns` |

### DNS Testing Commands

```bash
# Basic DNS lookup
nslookup google.com

# Detailed DNS query
dig google.com

# Test specific DNS server
dig @8.8.8.8 google.com

# Reverse DNS lookup
dig -x 8.8.8.8

# Check all DNS record types
dig google.com ANY
```

**Interpreting DNS Results:**
```bash
# Good dig output shows:
;; ANSWER SECTION:
google.com.     300     IN      A       172.217.164.78

# Bad output shows:
;; connection timed out; no servers could be reached
```

---

## Phase 4: Routing and Path Analysis

### Route Table Analysis

```bash
# View routing table
ip route show

# Add static route
sudo ip route add 192.168.2.0/24 via 192.168.1.1

# Delete route
sudo ip route del 192.168.2.0/24
```

### Path Tracing Tools

| Tool | Best For | Example Usage |
|------|----------|---------------|
| `traceroute` | One-time path check | `traceroute google.com` |
| `mtr` | Continuous monitoring | `mtr google.com` |
| `tracepath` | No root required | `tracepath google.com` |

**Reading Traceroute Output:**
```bash
traceroute to google.com (172.217.164.78), 30 hops max:
 1  192.168.1.1     1.234ms   1.123ms   1.456ms  ← Local gateway
 2  10.0.0.1        5.678ms   5.432ms   5.789ms  ← ISP router
 3  * * *                                        ← Blocked/timeout
```

---

## Phase 5: Service and Port Analysis

### Active Connections

**Modern Approach (ss command):**
```bash
# All TCP listening ports
ss -tuln

# All connections with process info
ss -tulpn

# Specific port
ss -tuln | grep :80
```

**Traditional Approach (netstat):**
```bash
# All listening ports
netstat -tuln

# With process information
netstat -tulpn

# Active connections
netstat -tan
```

### Socket States Reference

| State | Meaning | Action Needed |
|-------|---------|---------------|
| LISTEN | Port accepting connections | Normal for servers |
| ESTABLISHED | Active connection | Normal during use |
| TIME_WAIT | Connection closing | Normal, will clear |
| SYN_SENT | Attempting connection | May indicate blocking |

---

## Phase 6: Firewall Investigation

### Firewall Rule Examination

| Distribution | Command | Configuration |
|--------------|---------|---------------|
| **iptables** | `sudo iptables -L -n -v` | Rules-based |
| **UFW** (Ubuntu) | `sudo ufw status verbose` | `/etc/ufw/` |
| **firewalld** (RHEL/CentOS) | `sudo firewall-cmd --list-all` | Zones-based |

### Common Firewall Commands

```bash
# iptables
sudo iptables -L INPUT -n --line-numbers
sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT

# UFW
sudo ufw allow 22/tcp
sudo ufw deny from 192.168.1.0/24

# firewalld
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --reload
```

---

## Phase 7: Advanced Diagnostics

### Packet Capture

| Tool | Interface | Best For |
|------|-----------|----------|
| `tcpdump` | Command line | Quick captures, scripting |
| `wireshark` | GUI | Detailed analysis |
| `tshark` | Command line | Wireshark without GUI |

**Essential tcpdump Commands:**
```bash
# Capture on specific interface
sudo tcpdump -i eth0

# Save to file
sudo tcpdump -i eth0 -w capture.pcap

# Filter by host
sudo tcpdump -i eth0 host google.com

# Filter by port
sudo tcpdump -i eth0 port 80

# HTTP traffic only
sudo tcpdump -i eth0 -A port 80
```

### Network Statistics

```bash
# Interface statistics
cat /proc/net/dev

# Real-time monitoring
watch -n 1 'cat /proc/net/dev'

# Historical data (if sar available)
sar -n DEV 1 5
```

---

## Phase 8: System-Level Investigation

### Log Analysis

| Log Location | Contents | Search Commands |
|--------------|----------|-----------------|
| `journalctl -f` | Real-time system logs | `journalctl -u NetworkManager` |
| `/var/log/messages` | General system messages | `grep -i network /var/log/messages` |
| `dmesg` | Kernel ring buffer | `dmesg \| grep -i eth` |

### Hardware and Driver Checks

```bash
# Network hardware detection
lspci | grep -i network
lsusb | grep -i network

# Loaded network modules
lsmod | grep -E "(e1000|r8169|iwl)"

# Module information
modinfo e1000e

# Interface driver info
ethtool -i eth0
```

---

## Common Troubleshooting Scenarios

### Scenario 1: No Internet Access

| Step | Test | Expected Result | If Failed |
|------|------|-----------------|-----------|
| 1 | `ip addr show` | Interface has IP | Check DHCP/static config |
| 2 | `ping <gateway>` | Successful | Check physical connection |
| 3 | `ping 8.8.8.8` | Successful | Check DNS settings |
| 4 | `ping google.com` | Successful | Issue resolved |

### Scenario 2: Slow Network Performance

```bash
# Check interface errors
ip -s link show eth0

# Test bandwidth
iperf3 -c server_ip

# Check for duplex mismatches
ethtool eth0 | grep -E "(Speed|Duplex)"

# Monitor real-time traffic
iftop -i eth0
```

### Scenario 3: DNS Resolution Issues

| Problem | Symptom | Solution |
|---------|---------|----------|
| **No DNS servers** | `ping 8.8.8.8` works, `ping google.com` fails | Add to `/etc/resolv.conf` |
| **Wrong DNS servers** | Slow resolution | Change DNS servers |
| **DNS cache issues** | Stale records | Flush DNS cache |

---

## Emergency Network Recovery

### Quick Interface Reset

```bash
# Method 1: NetworkManager
sudo systemctl restart NetworkManager

# Method 2: Manual reset
sudo ip link set eth0 down
sudo ip link set eth0 up
sudo dhclient eth0

# Method 3: Full network restart
sudo systemctl restart networking  # Debian/Ubuntu
sudo systemctl restart network     # RHEL/CentOS
```

### Bypass Network Managers

```bash
# Manual IP configuration
sudo ip addr add 192.168.1.100/24 dev eth0
sudo ip route add default via 192.168.1.1
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
```

---

## Tool Quick Reference

### Essential Commands by Category

| Category | Tools | Purpose |
|----------|-------|---------|
| **Basic** | `ping`, `ip`, `ss` | Connectivity, config, connections |
| **DNS** | `dig`, `nslookup`, `host` | Name resolution testing |
| **Routing** | `traceroute`, `mtr`, `tracepath` | Path analysis |
| **Monitoring** | `tcpdump`, `iftop`, `nethogs` | Traffic analysis |
| **Debugging** | `strace`, `lsof`, `netstat` | Process/file analysis |

### Command Comparison: Old vs New

| Old Command | New Command | Notes |
|-------------|-------------|-------|
| `ifconfig` | `ip addr show` | ip command is more powerful |
| `route -n` | `ip route show` | Consistent ip command syntax |
| `arp -a` | `ip neigh show` | ARP table viewing |
| `netstat -rn` | `ip route show` | Routing table display |

---

## Best Practices and Tips

### Troubleshooting Methodology

1. **Start Simple** - Begin with basic connectivity before complex analysis
2. **Work Bottom-Up** - Test physical → network → transport → application layers
3. **Document Changes** - Keep track of configuration modifications
4. **Use Multiple Tools** - Confirm findings with different utilities
5. **Test Both Directions** - Verify connectivity from both client and server perspectives

### Common Mistakes to Avoid

- ❌ Changing multiple settings simultaneously
- ❌ Ignoring log files and error messages
- ❌ Not checking physical connections first
- ❌ Assuming DNS is working without testing
- ❌ Forgetting to check firewall rules

### Pro Tips

💡 **Always check the basics first** - Most network issues are simple misconfigurations

💡 **Use `mtr` for intermittent issues** - It combines ping and traceroute with statistics

💡 **Keep a network baseline** - Document working configurations for comparison

💡 **Learn your network topology** - Understanding your infrastructure helps isolate problems

💡 **Use packet capture sparingly** - Start with simpler tools before diving into tcpdump/Wireshark

---

## When to Escalate

Contact network administrators or consider hardware issues when:

- Multiple hosts experience the same problem simultaneously
- Physical layer indicators (link lights) show problems
- Switch/router logs indicate hardware failures
- Issues persist after systematic software troubleshooting
- Performance problems occur across multiple protocols

Remember: Network troubleshooting is a skill that improves with practice. Start with the systematic approach outlined here, and gradually build expertise with the advanced tools and techniques.
