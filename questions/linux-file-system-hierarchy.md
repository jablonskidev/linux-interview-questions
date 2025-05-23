# What is the Linux file system hierarchy and how is it organized?

**[Note: This is a work-in-progress draft.]**

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#background-information)
    - [Core directory structure](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#core-directory-structure)
    - [Standard directories: purpose and content](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#standard-directories-purpose-and-content)
    - [Core principles of Linux filesystem organization](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#core-principles-of-linux-filesystem-organization)
    - [File permissions and ownership](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#file-permissions-and-ownership)
    - [Security best practices](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#security-best-practices)
    - [Distribution variations](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#distribution-variations)
    - [Filesystem evolution trends](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#filesystem-evolution-trends)
    - [Common troubleshooting scenarios](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#common-troubleshooting-scenarios)
    - [Best practices](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#best-practices)
    - [Applying knowledge in different contexts](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#applying-knowledge-in-different-contexts)
- [Key takeaways](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#key-takeaways)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#quiz)
- [Next steps](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-system-hierarchy.md#next-steps)

## Answer

The Linux file system follows a hierarchical structure starting at the root directory (`/`) with standardized locations for different types of files:

- Essential command binaries are stored in `/bin` and `/sbin`, system configuration files in `/etc`, user data in `/home`, variable data like logs in `/var`, and most installed software in `/usr`.

- The system also includes special directories like `/boot` for kernel files, `/dev` for hardware device files, and virtual filesystems like `/proc` and `/sys` that expose kernel information.

This organization follows principles of separation of concerns, predictable locations, and security by design to create a logical and maintainable system structure.

## Background information

### Core directory structure
Linux follows the Filesystem Hierarchy Standard (FHS) with a hierarchical structure starting at the root (`/`):

```
/                   # Root directory
├── bin             # Essential user command binaries
├── sbin            # System administration binaries
├── etc             # Host-specific system configuration files
├── home            # User home directories
├── var             # Variable data files (logs, mail, databases)
├── usr             # Secondary hierarchy (programs, documentation)
│   ├── bin         # Non-essential command binaries
│   ├── lib         # Libraries for binaries in /usr/bin
│   └── local       # Locally installed software
├── tmp             # Temporary files (cleared on reboot)
├── boot            # Boot loader files and kernel
├── dev             # Device files representing hardware
├── proc            # Virtual filesystem for kernel and process info
├── sys             # Virtual filesystem for kernel objects
├── opt             # Optional application software packages
├── lib             # Essential shared libraries and kernel modules
└── mnt/media       # Mount points for removable media
```
### Standard directories: purpose and content

#### System operation directories

| Directory | Purpose |
|-----------|---------|
| `/bin` | Essential commands needed for system bootup and repair (bash, cp, ls) |
| `/sbin` | System administration commands primarily for root (fdisk, ifconfig) |
| `/lib` | Shared libraries needed by programs in `/bin` and `/sbin` |
| `/boot` | Files required to boot the system (kernel, initramfs, bootloader) |

#### Configuration and variable data

| Directory | Purpose |
|-----------|---------|
| `/etc` | System-wide configuration files in mostly text format |
| `/var` | Files that change during normal operation |
| `/var/log` | System and service logs |
| `/var/spool` | Print/mail queues, cron jobs |
| `/var/cache` | Application cache data |
| `/var/tmp` | Temporary files preserved between reboots |

#### User Data

| Directory | Purpose |
|-----------|---------|
| `/home` | Each user typically gets a subdirectory (`/home/username`) |
| `/root` | Home directory for the root user (not in `/home`) |

#### Application and programming directories

| Directory | Purpose |
|-----------|---------|
| `/usr` | Secondary hierarchy for read-only user data |
| `/usr/bin` | Most user commands |
| `/usr/lib` | Libraries for programs in `/usr/bin` |
| `/usr/local` | Locally compiled software |
| `/usr/share` | Architecture-independent data |
| `/opt` | Self-contained, add-on application packages |

#### Virtual and device filesystems

| Directory | Purpose |
|-----------|---------|
| `/dev` | Device files (representing hardware) |
| `/proc` | Virtual filesystem exposing process and kernel information |
| `/sys` | Virtual filesystem exposing kernel features and hardware information |

#### Temporary storage

| Directory | Purpose |
|-----------|---------|
| `/tmp` | Temporary files (often cleared on reboot) |
| `/run` | Runtime variable data since last boot (formerly `/var/run`) |

### Core principles of Linux filesystem organization

1. Hierarchical structure
- Everything exists under a single root (`/`)
- Directories contain related files grouped by function
- Path structure provides logical organization

2. Separation of concerns
- System files separated from user files
- Configuration separate from programs
- Static content separate from variable content
- Distribution packages separate from local additions

3. Predictable locations
- Standard locations for different types of files
- Applications can find resources without hard-coded paths
- Similar file types stored in similar locations

4. Security by design
- Permission structure follows functional requirements
- System directories protected from regular users
- Sensitive configuration protected with restricted permissions
- World-writable directories limited and controlled

5. Compatibility and evolution
- Backward compatibility maintained through transitions
- Legacy paths often preserved with symbolic links
- New standards implemented with minimal disruption

### File permissions and ownership

Basic permission structure:
- **Three permission types:** read (`r`), write (`w`), execute (`x`)
- **Three permission groups:** user/owner (`u`), group (`g`), others (`o`)
- **Example:** `rwxr-xr--` means full permissions for owner, read/execute for group, read-only for others.

Directory permissions:
- **Read:** List directory contents
- **Write:** Create, rename, or delete files within the directory
- **Execute:** Access files and subdirectories within the directory

Special permission bits:
- **Setuid (`chmod u+s`):** Run executable as file owner
- **Setgid (`chmod g+s`):** Run executable as file group; on directories, enforce group inheritance
- **Sticky bit (`chmod +t`):** On directories, prevent users from deleting files they don't own

### Security best practices

| Directory type | Recommended ownership | Recommended permissions | Security rationale |
|----------------|----------------------|--------------------------|-------------------|
| System binaries | `root:root` | `755` | Prevent unauthorized modification |
| Configuration files | `root:root` | `644` or `640` | Allow reading but restrict writing |
| Private data | `specific_user:group` | `700` or `750` | Restrict access to authorized users |
| Home directories | `user:user` | `750` or `700` | Protect user privacy |

### Distribution variations

| Distribution type | Examples | Characteristics |
|-------------------|----------|----------------|
| Enterprise distributions | RHEL, CentOS, SUSE | - Strict FHS adherence<br>- Heavy use of `/etc/sysconfig` for configuration<br>- Clear separation between vendor and local files |
| Community distributions | Debian, Ubuntu, Arch | - Quicker adoption of modern filesystem changes<br>- Greater use of `/etc/default` and `/etc/alternatives`<br>- More extensive use of symbolic links |
| Embedded distributions | Alpine, BusyBox | - Minimalist hierarchy optimized for size<br>- Combined functionality with fewer directories<br>- Aggressive use of linking for space efficiency |

### Filesystem evolution trends

Merged `/usr` movement:
- Consolidation of directories under `/usr`
- Symbolic links maintaining compatibility
- Benefits: simplifies system updates, enables read-only root

Container influence:
- Immutable system files
- Enhanced separation of configuration
- Read-only `/usr` with writable overlay
- Focus on isolation and reproducibility

Cloud-native adaptations:
- Ephemeral filesystem usage
- External persistence services
- Configuration through environment variables and injection
- Structured logging to external services

### Common troubleshooting scenarios

| Problem type | Command | Purpose |
|--------------|---------|---------|
| **Path-related problems** | `echo $PATH` | Check path variable |
|  | `which -a command_name` | Find command location |
|  | `strace -e open,stat command` | See what files a program tries to access |
| **Permission issues** | `ls -la /path/to/file` | Check permissions |
|  | `namei -l /path/to/file` | Check entire path permissions |
|  | `ps -eo user,group,comm \| grep program` | Check process ownership |
| **Disk Space problems** | `df -h` | Check disk space |
|  | `df -i` | Check inode usage |
|  | `du -sh /* \| sort -hr` | Find large directories |
|  | `lsof +L1` | Find deleted but open files |

### Best practices

System organization:
- Keep `/tmp` and `/var` on separate partitions from `/`
- Document non-standard filesystem structures
- Use package management for software installation when possible
- Follow distribution-specific configuration patterns
- Implement log rotation for system and application logs

Application placement:
- Use `/opt` for self-contained, vendor applications
- Use `/usr/local` for locally compiled software following FHS
- Use `~/.local` for user-specific applications
- Create dedicated users for server applications
- Respect filesystem organization standards when deploying software

Security:
- Apply principle of least privilege to file permissions
- Regularly audit permissions on sensitive files
- Use appropriate mount options (`noexec`, `nosuid`) for non-system partitions
- Document and maintain baseline permissions
- Implement filesystem integrity monitoring

Modern deployments:
- Separate configuration from application code
- Use environment-specific configuration strategies
- Treat infrastructure as code with version control
- Design for container and cloud compatibility
- Maintain clear separation between application and data

### Applying knowledge in different contexts

For system administrators:
- Understand directory purposes for proper system maintenance
- Follow filesystem security practices to protect systems
- Troubleshoot permission and path issues efficiently
- Plan partition schemes based on system usage patterns

For developers:
- Place application files in appropriate locations
- Follow system conventions for configuration files
- Understand permissions needed for application functionality
- Design with both traditional and containerized deployments in mind

For DevOps engineers:
- Bridge development and operations with consistent practices
- Implement infrastructure as code with proper file organization
- Build deployment pipelines respecting filesystem principles
- Design monitoring for filesystem health and security

## Key takeaways
- The Linux filesystem hierarchy follows logical organization principles designed for security, maintainability, and compatibility.
- Core directories serve specific functions with predictable content across distributions.
- Permission structures protect system integrity while enabling appropriate access.
- Modern trends emphasize immutability, separation of configuration, and containerization.
- Understanding the principles behind the hierarchy helps adapt to any Linux environment.

## Quiz

### Questions
 
### Answer key
## Next steps

See more [Linux interview questions](https://github.com/jablonskidev/linux-interview-questions/blob/main/README.md).
