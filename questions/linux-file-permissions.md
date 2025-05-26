# What are file permissions in Linux and how do they work?

**[Note: This is a work-in-progress draft.]**

## Quick Answer

**Linux file permissions control who can read, write, or execute files using a three-tier system (owner/group/others) with three permission types (read/write/execute). They're displayed as symbols like `rwxr-xr--` or numbers like `754`. You modify them with `chmod`, change ownership with `chown`, and they include special permissions like SUID for enhanced security control.**

---

## What Are File Permissions?

File permissions are **security controls** that determine who can access, modify, or execute files and directories on Unix-like systems. They form the foundation of system security across all Linux distributions and Unix variants.

**Key Benefits:**
- Prevent unauthorized access
- Protect system files from modification  
- Control program execution
- Enable secure multi-user environments

---

## The Three Permission Types

| Permission | Symbol | Number | File Effect | Directory Effect |
|------------|--------|--------|-------------|------------------|
| **Read** | `r` | 4 | View file contents | List directory contents |
| **Write** | `w` | 2 | Modify file contents | Create/delete files in directory |
| **Execute** | `x` | 1 | Run file as program | Access/enter directory |

### Quick Examples:
- **File with `r` only:** Can read but not modify or run
- **Directory with `x` only:** Can enter but not list contents
- **Script with `rx`:** Can read and execute but not modify

---

## The Three Permission Categories

```
rwx | rwx | rwx
 ↓     ↓     ↓
Owner Group Others
```

### Who Gets What Access:

| Category | Description | Common Use Cases |
|----------|-------------|------------------|
| **Owner** | File creator or assigned user | Personal files, scripts |
| **Group** | Users in the file's group | Team projects, shared resources |
| **Others** | Everyone else on system | Public files, system binaries |

---

## Permission Representation Methods

### Symbolic Notation (Human-Readable)

```
-rwxr-xr--
│└┬┘└┬┘└┬┘
│ │  │  └── Others: read only
│ │  └───── Group: read + execute  
│ └──────── Owner: read + write + execute
└────────── File type indicator
```

**Common Patterns:**
- `rwxrwxrwx` = Full access for everyone
- `rwxr-xr-x` = Owner full, others read/execute
- `rw-rw-r--` = Read/write for owner/group, read-only for others
- `rwx------` = Owner-only access

### Numeric (Octal) Notation

| Binary | Decimal | Permissions |
|--------|---------|-------------|
| 000 | 0 | `---` (no access) |
| 001 | 1 | `--x` (execute only) |
| 010 | 2 | `-w-` (write only) |
| 011 | 3 | `-wx` (write + execute) |
| 100 | 4 | `r--` (read only) |
| 101 | 5 | `r-x` (read + execute) |
| 110 | 6 | `rw-` (read + write) |
| 111 | 7 | `rwx` (full access) |

### Most Common Numeric Combinations:

| Octal | Symbolic | Use Case |
|-------|----------|----------|
| `755` | `rwxr-xr-x` | **Executables, scripts** |
| `644` | `rw-r--r--` | **Regular files, documents** |
| `600` | `rw-------` | **Private files, config files** |
| `700` | `rwx------` | **Private directories** |
| `666` | `rw-rw-rw-` | **Shared writable files** |

---

## Converting Between Formats

### Symbolic to Numeric:
```
rwxr-xr-- = ?
rwx = 4+2+1 = 7
r-x = 4+0+1 = 5  
r-- = 4+0+0 = 4
Result: 754
```

### Quick Conversion Chart:
- `r--` = 4
- `-w-` = 2  
- `--x` = 1
- `rw-` = 6
- `r-x` = 5
- `-wx` = 3
- `rwx` = 7

---

## Special Permission Bits

Beyond basic permissions, there are three special bits that modify behavior:

| Bit | Symbol | Octal | Effect |
|-----|--------|-------|--------|
| **SUID** | `s` in owner execute | 4000 | Execute with file owner's privileges |
| **SGID** | `s` in group execute | 2000 | Execute with group privileges OR inherit group ownership |
| **Sticky** | `t` in others execute | 1000 | Only owner can delete files (shared directories) |

### Special Permission Examples:

```bash
# SUID on executable
-rwsr-xr-x  (4755)  # passwd command

# SGID on directory  
drwxr-sr-x  (2755)  # Shared project directory

# Sticky bit on directory
drwxrwxrwt  (1777)  # /tmp directory
```

**Security Note:** SUID and SGID can create security vulnerabilities if misused on writable files.

---

## Essential Permission Commands

### chmod - Change Permissions

**Symbolic Method:**
```bash
# Add permissions
chmod u+x file.sh          # Add execute for owner
chmod g+w document.txt     # Add write for group  
chmod o-r private.txt      # Remove read for others
chmod a+r public.txt       # Add read for all

# Set exact permissions
chmod u=rwx,g=rx,o=r file  # Owner:rwx, Group:rx, Others:r
```

**Numeric Method:**
```bash
chmod 755 script.sh        # rwxr-xr-x
chmod 644 document.txt     # rw-r--r--
chmod 600 private.key     # rw-------
chmod 700 private_dir/    # rwx------
```

### chown - Change Ownership

```bash
chown user:group file.txt     # Change both user and group
chown user file.txt          # Change user only
chown :group file.txt        # Change group only
chown -R user:group dir/     # Recursive for directories
```

### Other Useful Commands:

```bash
# View permissions
ls -l file.txt              # Long format with permissions
ls -la directory/           # Include hidden files

# Change group only
chgrp group file.txt        

# View current umask
umask                      

# Set umask (affects new file permissions)
umask 022                   # New files: 644, directories: 755
```

---

## Understanding umask

**umask** sets default permissions for new files and directories by **subtracting** from maximum permissions.

### How umask Works:

| Item | Maximum Perms | Common umask | Result | Symbolic |
|------|---------------|--------------|--------|----------|
| **Files** | 666 | 022 | 644 | `rw-r--r--` |
| **Directories** | 777 | 022 | 755 | `rwxr-xr-x` |

### Common umask Values:

- `022` = Group/others get read access only (most common)
- `077` = Owner-only access (most secure)  
- `002` = Group gets full access, others read-only

---

## Advanced Concepts

### Access Control Lists (ACLs)

For more granular permissions beyond the basic model:

```bash
# View ACLs
getfacl file.txt

# Set ACL for specific user
setfacl -m u:username:rw file.txt

# Remove ACL
setfacl -x u:username file.txt
```

### Directory vs File Behavior

| Permission | Files | Directories |
|------------|-------|-------------|
| **Read** | View contents | List directory contents |
| **Write** | Modify contents | Create/delete files inside |
| **Execute** | Run as program | Enter/traverse directory |

**Important:** You need `execute` permission on ALL parent directories to access a file!

---

## Security Best Practices

### ✅ Recommended Practices:

- **Principle of Least Privilege**: Give minimum required permissions
- **Regular Audits**: Check permissions on sensitive files
- **Careful with SUID/SGID**: Only use when absolutely necessary
- **Protect System Files**: Keep system binaries at 755 or more restrictive

### ❌ Common Security Mistakes:

- World-writable files (`chmod 777`)
- SUID on user-writable files
- Overly permissive directories containing sensitive data
- Ignoring special permission implications

### Quick Security Checklist:

```bash
# Find world-writable files (security risk)
find / -perm -002 -type f 2>/dev/null

# Find SUID files (potential attack vectors)  
find / -perm -4000 2>/dev/null

# Find SGID files
find / -perm -2000 2>/dev/null

# Find files with no owner (orphaned files)
find / -nouser -o -nogroup 2>/dev/null
```

---

## Real-World Examples

### Web Server Files:
```bash
# HTML files - readable by web server
chmod 644 *.html

# PHP scripts - readable/executable  
chmod 755 *.php

# Configuration files - owner only
chmod 600 config.php

# Upload directory - writable by web server
chmod 755 uploads/
```

### System Scripts:
```bash
# System executables
chmod 755 /usr/local/bin/myscript

# Service configuration  
chmod 644 /etc/myservice.conf

# Log files - append only for service
chmod 640 /var/log/myservice.log
```

### Personal Files:
```bash
# Private documents
chmod 600 ~/private_notes.txt

# Shared project directory
chmod 775 ~/shared_project/

# Executable scripts
chmod 755 ~/bin/myscript.sh
```

---

## Quick Reference Card

### Permission Quick Reference:
```
7 = rwx (read + write + execute)
6 = rw- (read + write)  
5 = r-x (read + execute)
4 = r-- (read only)
3 = -wx (write + execute)
2 = -w- (write only)
1 = --x (execute only)
0 = --- (no permissions)
```

### Most Used Commands:
```bash
chmod 755 file    # Make executable
chmod 644 file    # Regular file  
chmod 600 file    # Private file
ls -l            # View permissions
chown user file  # Change owner
umask 022        # Set default permissions
```

### Emergency Reset:
```bash
# Reset directory permissions recursively
find directory/ -type d -exec chmod 755 {} \;

# Reset file permissions recursively  
find directory/ -type f -exec chmod 644 {} \;
```

---

## Troubleshooting Common Issues

| Problem | Likely Cause | Solution |
|---------|--------------|----------|
| "Permission denied" when running script | Missing execute permission | `chmod +x script.sh` |
| Can't modify file as owner | File is read-only | `chmod u+w filename` |  
| Can't access directory | Missing execute on directory | `chmod +x directory/` |
| "Operation not permitted" | Not file owner or root | Use `sudo` or contact administrator |
| New files have wrong permissions | Incorrect umask | Check and adjust `umask` |

This guide covers the universal permission system that works across all Linux distributions and Unix-like systems. Master these concepts to effectively secure and manage any Unix-based system!
