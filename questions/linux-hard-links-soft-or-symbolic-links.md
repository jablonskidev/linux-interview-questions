# What is the difference between hard links and soft/symbolic links?

**[Note: This is a work-in-progress draft.]**

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#background-information)
  - [Hard links: shared data](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#hard-links-shared-data)
  - [Symbolic links: path pointers](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#symbolic-links-path-pointers)
  - [Creating links](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#creating-links)
  - [What happens when the original is deleted](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#what-happens-when-the-original-is-deleted)
  - [Quick comparison](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#quick-comparison)
  - [Choosing the right type](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#choosing-the-right-type)
  - [Common use cases](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#common-use-cases)
  - [Filesystem compatibility](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#filesystem-compatibility)
  - [Troubleshooting & security](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#troubleshooting--security)
  - [Container considerations](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#container-considerations)
  - [Advanced commands](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#advanced-commands)
  - [File operations through links](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#file-operations-through-links)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#quiz)

## Answer

- **Hard links**: Same file, multiple names, same filesystem only
- **Soft/symbolic links**: Shortcuts that can break, work across filesystems
- **Key difference**: Hard links survive if original is deleted, symbolic links don't

## Background information

### Hard links: shared data

```
Directory entries          Inode #123              Data blocks
┌─────────────────┐        ┌──────────┐           ┌─────────────┐
│ original.txt    │───────▶│ metadata │──────────▶│ "Hello      │
└─────────────────┘        │ size: 6  │           │ World"      │
┌─────────────────┐        │ perms    │           │             │
│ hardlink.txt    │───────▶│ links: 2 │           └─────────────┘
└─────────────────┘        └──────────┘
```

### Symbolic links: path pointers

```
Directory entries          Inodes                  Data blocks
┌─────────────────┐        ┌──────────┐           ┌─────────────┐
│ original.txt    │───────▶│ #123     │──────────▶│ "Hello      │
└─────────────────┘        │ metadata │           │ World"      │
┌─────────────────┐        └──────────┘           └─────────────┘
│ symlink.txt     │───────▶┌──────────┐
└─────────────────┘        │ #456     │──────────▶"original.txt"
                           │ type:sym │
                           └──────────┘
```

### Creating links

```bash
echo "test" > original.txt
ln original.txt hardlink.txt        # Hard link
ln -s original.txt symlink.txt      # Symbolic link
```

### What happens when the original is deleted

Hard links survive:

```
Before deletion:                After deletion:
original.txt ───┐              [deleted] ───┐
                ├──▶ inode #123              ├──▶ inode #123 ──▶ Data
hardlink.txt ───┘              hardlink.txt ─┘                    (still accessible)
```

Symbolic links break:

```
Before deletion:                After deletion:
original.txt ──▶ inode #123    [deleted] ──▶ [missing]
                      │
symlink.txt ──▶ "original.txt" ┘         symlink.txt ──▶ "original.txt" ──▶ ❌ BROKEN
```

### Quick comparison

| Feature | Hard links | Symbolic links |
|---------|------------|----------------|
| Command | `ln source link` | `ln -s source link` |
| Cross-filesystem | ❌ No | ✅ Yes |
| Link to directory | ❌ No | ✅ Yes |
| If original deleted | ✅ Still works | ❌ Breaks |
| Visual in `ls -l` | Looks identical | Shows → arrow |

### Choosing the right type

#### Use hard links when

- Files are on the same filesystem
- You need guaranteed access even if original is deleted
- Space efficiency is critical (backups, deduplication)

#### Use symbolic links when

- Linking across different filesystems
- Linking to directories
- Target location might change
- You want visible indication of the link

### Common use cases

#### Configuration management

```bash
# Switch between configurations
/etc/nginx/nginx.conf → nginx.conf.stable

# Deploy new config:
ln -sf /etc/nginx/nginx.conf.experimental /etc/nginx/nginx.conf
```

#### Application deployment

```bash
# Zero-downtime deployments
/var/www/app/current → releases/v2.0/

# Deploy new version:
ln -sfn /var/www/app/releases/v2.1 /var/www/app/current
```

#### Space-efficient backups with hard links

```
Backup Structure:
/backups/2025-05-20/file.txt ──┐
                               ├──▶ inode #123 ──▶ actual data
/backups/2025-05-21/file.txt ──┘
                            (unchanged files share same inode = no extra space)
```

### Filesystem compatibility

| Filesystem | Hard links | Symbolic links |
|------------|------------|----------------|
| ext4 | ✅ Full support | ✅ Full support |
| NTFS | ❌ Limited | ⚠️ Limited |
| NFS | ✅ Supported | ⚠️ Version-dependent |

### Troubleshooting & security

#### Finding links

```bash
# Find symbolic links
find /path -type l

# Find hard links (files with multiple links)
find /path -links +1 -type f

# Find broken symbolic links
find /path -type l -xtype l
```

#### Security best practices

- Avoid following symbolic links blindly in privileged scripts
- Use absolute paths when target location must be precisely controlled
- Be cautious with symbolic links in shared directories like /tmp

### Container considerations

- **Hard links**: Often used within layers for storage optimization
- **Symbolic links**: May break across container boundaries
- **Best practice**: Use relative symbolic links within mounted volumes

### Advanced commands

```bash
# Update existing symbolic link
ln -sf new_target existing_link

# Check inode numbers to identify hard links
ls -i

# Force overwrite when creating links
ln -sf source target
```

### File operations through links

```
original.txt ──┐
               ├──▶ inode #123 ──▶ Data: "Hello"
hardlink.txt ──┘

echo "World" >> hardlink.txt
Result: Both original.txt and hardlink.txt now contain "HelloWorld"

symlink.txt ──▶ "original.txt" ──▶ original.txt ──▶ Data
Editing through symlink.txt also modifies the original data
```

---

## Quiz

### Questions
 
### Answer key
