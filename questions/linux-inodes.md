# What are inodes in Linux filesystems?

**[Note: This is a work-in-progress draft.]**

## What Are Inodes?

**Inodes** store file metadata - not filenames or content. Each file has one inode with a unique number.

### What Inodes Contain

| Data | Purpose |
|------|---------|
| **File type** | Regular file, directory, link |
| **Permissions** | rwx for owner/group/other |
| **Size** | File size in bytes |
| **Timestamps** | Access, modify, change times |
| **Link count** | Number of hard links |
| **Block pointers** | Location of actual data |

### File Organization
```
filename.txt → inode#12345 → data blocks
backup.txt   ↗ (hard link shares same inode)
```

---

## Essential Commands

| Task | Command | Example |
|------|---------|---------|
| **Check inode usage** | `df -i` | Shows filesystem inode statistics |
| **Show inode numbers** | `ls -i` | `12345 document.txt` |
| **Get file details** | `stat filename` | Complete inode information |
| **Find by inode** | `find / -inum 12345` | Locate all hard links |
| **Create hard link** | `ln source target` | Multiple names, same inode |
| **Count files** | `find /path -type f \| wc -l` | Files consuming inodes |

### Command Examples

```bash
# Check inode usage across filesystems
$ df -i
Filesystem    Inodes  IUsed  IFree IUse% Mounted on
/dev/sda1     655360  45230  610130   7% /

# View inode numbers
$ ls -i
12345 document.txt
12346 script.sh

# Create and verify hard link
$ ln original.txt backup.txt
$ ls -i original.txt backup.txt
12345 original.txt
12345 backup.txt
```

### Hard Links
- Multiple filenames pointing to same inode
- Share all metadata and content
- Same filesystem only
- No directories allowed
- Link count shows number of references

---

## Troubleshooting

### Inode Exhaustion

**Problem:** "No space left on device" but `df -h` shows free space
**Cause:** Out of inodes, not disk space

```bash
# Diagnose the issue
$ df -h /tmp && df -i /tmp
/dev/sda1    10G  2.0G  7.5G  21% /tmp  ← Disk has space
/dev/sda1  655360 655360     0 100% /tmp  ← No inodes left!
```

### Find Problem Areas

| Task | Command |
|------|---------|
| **Directories with most files** | `find /tmp -type d -exec sh -c 'echo "{}:$(ls -1 "{}" 2>/dev/null \| wc -l)"' \\; \| sort -t: -k2 -nr` |
| **Count files in directory** | `find /var/log -type f \| wc -l` |
| **Large directories** | `ls -1 /path \| wc -l` |

### Quick Fixes

| Problem | Solution |
|---------|----------|
| **Too many temp files** | `find /tmp -type f -mtime +7 -delete` |
| **Zero-byte files** | `find /var/log -type f -size 0 -delete` |
| **Old log files** | `find /var/log -name "*.log.*" -mtime +30 -delete` |

### Monitoring Script

```bash
#!/bin/bash
# Alert when inode usage > 90%
df -i | awk 'NR>1 && $5+0 > 90 {
    print $6 " is " $5 " full (inodes)"
}'
```

---

## Quick Reference

### Core Concepts
- **1 inode per file** - stores metadata only
- **Hard links share inodes** - multiple names, same data  
- **Inode exhaustion** - can happen with free disk space
- **Fixed allocation** - most filesystems set inode count at format time

### Essential Commands
```bash
df -i              # Check inode usage
ls -i filename     # Show inode number
stat filename      # Full inode details  
ln file1 file2     # Create hard link
find /path -type f | wc -l  # Count files
```

### Troubleshooting Workflow
1. **Check:** `df -i` for inode usage
2. **Find:** Large directories with file counting
3. **Clean:** Remove old/temp files
4. **Monitor:** Regular inode usage checks

### Planning Guidelines
- **Many small files:** 1 inode per 4-8KB when formatting
- **Large files only:** 1 inode per 64KB+ when formatting  
- **Mixed workload:** Default 1 inode per 16KB

### Key Takeaways
✓ Inodes store file metadata separate from names and content  
✓ Monitor inode usage with `df -i` to prevent storage issues  
✓ Hard links enable multiple filenames for the same file data  
✓ Inode exhaustion causes "no space" errors despite free disk space  
✓ Regular cleanup prevents inode exhaustion in high-file-count areas
