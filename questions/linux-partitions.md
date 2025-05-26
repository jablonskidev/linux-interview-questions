# What are partitions in Linux and what are they for?

**[Note: This is a work-in-progress draft.]**

## Quick Answer

**What are partitions?** Logical divisions of a storage device (like splitting a hard drive into separate sections).

**What are they for?** Organization, security, and reliability - keeping your operating system separate from user files, enabling different file systems, and preventing one full partition from crashing your entire system.

---

Partitions are logical divisions of storage devices that create separate areas for different types of data. This guide covers everything you need to know about Linux partitions across any distribution.

## What Are Partitions?

A partition divides a physical drive (HDD, SSD, USB) into independent sections, like dividing a warehouse into separate storage areas.

**Key Difference:**
- **Partitions** = Physical divisions of hardware
- **Directories** = Logical organization within file systems

## Partition Types & Tables

### Partition Types
| Type | Description | Limit | Use Case |
|------|-------------|-------|----------|
| **Primary** | Bootable main partitions | 4 per drive (MBR) | Operating systems |
| **Extended** | Container for logical partitions | 1 per drive (MBR) | When you need >4 partitions |
| **Logical** | Partitions within extended | Many | Additional storage |

### Partition Tables
| Feature | MBR (Legacy) | GPT (Modern) |
|---------|--------------|--------------|
| **Max Partitions** | 4 primary | 128+ |
| **Max Drive Size** | 2TB | 9.4 ZB |
| **Best For** | Older systems | Modern systems |

## Essential Linux Partitions

### Root Partition (/)
**Contains:** System files (`/bin`, `/etc`, `/lib`, `/usr`, `/var`)  
**Size:** 20-50GB minimum

### Swap Partition
**Purpose:** Virtual memory when RAM is full

| RAM Amount | Swap Size | Use Case |
|------------|-----------|----------|
| < 2GB | 2x RAM | Limited RAM systems |
| 2-8GB | = RAM | General desktop |
| 8-64GB | 0.5x RAM | High-memory systems |
| > 64GB | 4-8GB | Servers |

### Home Partition (/home)
**Benefits:**
- ✅ Preserves user data during OS reinstalls
- ✅ Separate backup strategies
- ✅ Different mount options

**Size:** Remaining available space

### Boot Partition (/boot)
**When needed:** Encrypted systems, old hardware, complex setups  
**Size:** 500MB-1GB

## File Systems

| File System | Strengths | Best For | Cross-Platform |
|-------------|-----------|----------|----------------|
| **ext4** | Stable, widely supported | General purpose | Linux only |
| **Btrfs** | Snapshots, compression | Advanced users | Linux only |
| **XFS** | High performance | Large files | Linux only |
| **FAT32** | Universal compatibility | USB drives | All platforms |
| **NTFS** | Windows native | Dual-boot | Limited on Linux |

## Common Partition Schemes

### Minimal Setup (Beginners)
```
├── Root (/) - ext4 - Remaining space
└── Swap - 4-8GB
```

### Standard Desktop
```
├── Root (/) - ext4 - 30-50GB
├── Home (/home) - ext4 - Remaining space
└── Swap - 4-8GB
```

### Size Planning

| Use Case | Root (/) | Home (/home) | Swap |
|----------|----------|--------------|------|
| **Basic Desktop** | 30GB | Remaining | = RAM |
| **Developer** | 50GB | Remaining | = RAM |
| **Server** | 20GB | 50GB | 0.5x RAM |

**Rule:** Leave 10-20% free space in each partition for overhead and growth.

## Partition Management Tools

### Command-Line Tools
| Tool | Best For | Key Features |
|------|----------|--------------|
| **fdisk** | MBR partitions | Simple, widely available |
| **gdisk** | GPT partitions | GPT-specific features |
| **parted** | Both MBR/GPT | Flexible, scriptable |

### Graphical Tools
- **GParted** - Most comprehensive, live CD available
- **KDE Partition Manager** - Well-integrated with KDE
- **GNOME Disks** - Simple, clean interface

### ⚠️ Safety First
1. **Backup important data**
2. **Test with live USB/CD**
3. **Have recovery media ready**

## Essential Commands

### Viewing Partitions
```bash
# List all devices in tree format
lsblk

# Show disk usage
df -h

# Show partition tables
sudo fdisk -l
```

### Example Output
```
NAME   SIZE TYPE MOUNTPOINT
sda    500G disk
├─sda1 512M part /boot/efi
├─sda2  30G part /
├─sda3 400G part /home
└─sda4   8G part [SWAP]
```

## Storage Strategies

### Single vs. Multiple Drives

| Setup | Pros | Cons |
|-------|------|------|
| **Single Drive** | Simple, cost-effective | Single failure point |
| **SSD + HDD** | Fast system + large storage | More complex |
| **Multiple SSDs** | High performance | Higher cost |

### SSD + HDD Combination
```
SSD: Root (/) + active Home files
HDD: Archives, media, backups
```

## Troubleshooting

### Full Partition Issues
**Symptoms:** "No space left" errors, system slowdown

**Solutions:**
- Find large files: `du -sh /* | sort -rh`
- Clear temp files in `/tmp`, `/var/tmp`
- Clean package cache (distro-specific)
- Remove old logs: `journalctl --vacuum-time=30d`

### Mount Problems
**Quick fixes:**
```bash
# Check current mounts
mount | grep /dev/sda

# Manual mount
sudo mount /dev/sda3 /home

# Check boot mounting config
cat /etc/fstab
```

## Best Practices & Optimization

### Security & Performance
| Practice | Benefit | Implementation |
|----------|---------|----------------|
| **Separate /home** | Protects user data | Dedicated partition |
| **SSD optimization** | Better performance | Enable TRIM: `fstrim -av` |
| **Regular monitoring** | Prevent issues | Check `df -h` regularly |

### When to Repartition
- ❌ **Avoid if possible** - Risk of data loss
- ✅ **Consider instead:** LVM, bind mounts, symbolic links
- ✅ **Valid reasons:** Major hardware upgrades, changing usage patterns

## Quick Decision Guide

| Your Situation | Recommended Scheme |
|----------------|-------------------|
| **Linux newcomer** | Root + Swap |
| **Dual-boot system** | Root + Home + Swap |
| **Development work** | Root + Home + Swap |
| **Home server** | Root + Home + Swap + separate /var |

## Key Takeaways

**Start Simple:** Root + Swap works for most beginners. Add complexity only when needed.

**Separate /home:** Protects user data during system changes and reinstalls.

**Plan Ahead:** Consider future storage needs but avoid over-engineering.

**Backup First:** Always backup before modifying partitions.

**Use Right Tools:** GParted for GUI work, command-line tools for scripting.

---

**Remember:** The best partition scheme meets your specific needs without unnecessary complexity. You can always evolve your approach as requirements change.
