# What is the Linux file system hierarchy?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#background-information)
  - [Core system directories](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#core-system-directories)
  - [Configuration and variable data](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#configuration-and-variable-data)
  - [User and application areas](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#user-and-application-areas)
  - [Virtual file systems](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#virtual-file-systems)
  - [Mount points](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#mount-points)
  - [Design principles](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#design-principles)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#quiz)

The Linux file system hierarchy is a standardized tree structure defined by the Filesystem Hierarchy Standard (FHS) that starts from the root directory (/) and organizes all files and directories in a consistent manner across distributions.

It's organized by separating different types of data and functionality:
- Essential system files in /bin and /sbin
- Configuration files in /etc
- User data in /home
- Variable data like logs in /var
- Virtual kernel interfaces in /proc and /sys

This separation enables efficient system administration, security policies, and backup strategies by grouping files based on their purpose and how they change over time.

## Background information

### Core system directories

| Directory | Purpose | Key contents |
|-----------|---------|--------------|
| `/bin` | Essential user commands | ls, cp, mv, cat |
| `/sbin` | System administration commands | mount, umount, fsck |
| `/lib` `/lib64` | Shared system libraries | Libraries for /bin and /sbin programs |
| `/boot` | Boot system files | Kernel images, boot loader files |

### Configuration and variable data

| Directory | Purpose | Examples |
|-----------|---------|----------|
| `/etc` | System-wide configuration | Network settings, /etc/passwd, startup scripts |
| `/var` | Variable data (changes during operation) | /var/log (logs), /var/mail (mail), cache files |
| `/tmp` | Temporary files | May be cleared on reboot |

### User and application areas

| Directory | Purpose | Contents |
|-----------|---------|----------|
| `/home` | User home directories | /home/username for each user |
| `/root` | Root user's home | Separate from /home for security |
| `/usr` | User programs and shared data | /usr/bin, /usr/lib, /usr/share |
| `/opt` | Optional software packages | Third-party applications |

### Virtual file systems

These directories don't contain real files but provide kernel interfaces:

| Directory | Purpose | What you will find |
|-----------|---------|------------------|
| `/proc` | Process and kernel information | Running process data, system stats |
| `/sys` | Kernel objects and hardware info | Hardware attributes, kernel parameters |
| `/dev` | Device files | Hardware device interfaces |

### Mount points

| Directory | Purpose | Typical use |
|-----------|---------|-------------|
| `/mnt` | Temporary mount point | Manual file system mounting |
| `/media` | Removable media mount point | USB drives, CD-ROMs |

### Design principles

| Separation type | Examples | Benefit |
|-----------------|----------|---------|
| **Static vs. variable** | /etc (config) vs. /var (logs) | Targeted backup strategies |
| **System vs. user** | /bin (system) vs. /usr (user apps) | Security and access control |
| **Local vs. shareable** | /etc (machine-specific) vs. /usr (network-shareable) | Network file system efficiency |

## Quiz

### Multiple-choice questions

1. The file system tree structure starts at the:
- a. home directory
- b. root directory
- c. boot directory

2. Essential system files are in:
- a. bin
- b. sbin
- c. bin and sbin

3. dev is for:
- a. development
- b. devices
- c. devel

### Answer key

- 1: b
- 2: c
- 3: b
