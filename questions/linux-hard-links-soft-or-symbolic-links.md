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

**1. What happens to a hard link when the original file is deleted?**
- a) The hard link becomes broken and unusable
- b) The hard link continues to work and provides access to the data
- c) The hard link is automatically deleted
- d) The hard link converts to a symbolic link

**2. Which command creates a symbolic link?**
- a) `ln original.txt symlink.txt`
- b) `ln -h original.txt symlink.txt`
- c) `ln -s original.txt symlink.txt`
- d) `symlink original.txt symlink.txt`

**3. Can hard links work across different filesystems?**
- a) Yes, always
- b) No, never
- c) Only on ext4 filesystems
- d) Only with administrator privileges

**4. What does a symbolic link actually contain?**
- a) A copy of the original file's data
- b) The inode number of the original file
- c) A path/reference to the original file
- d) Compressed version of the original file

**5. Which type of link can point to directories?**
- a) Hard links only
- b) Symbolic links only
- c) Both hard and symbolic links
- d) Neither type can link to directories

**6. In the output of `ls -l`, how can you identify a symbolic link?**
- a) It shows the same inode number as the original
- b) It displays an arrow (→) pointing to the target
- c) It appears in a different color only
- d) It has a special 's' permission bit

**7. What is the main advantage of using hard links for backups?**
- a) They work across different filesystems
- b) They can link to directories
- c) Unchanged files share the same inode, saving space
- d) They're easier to create than symbolic links

**8. Which find command locates broken symbolic links?**
- a) `find /path -type l -broken`
- b) `find /path -type l -xtype l`
- c) `find /path -links 0`
- d) `find /path -type s -broken`

**9. For zero-downtime application deployments, which type of link is typically preferred?**
- a) Hard links, because they're more reliable
- b) Symbolic links, because you can easily redirect to new versions
- c) Either type works equally well
- d) Neither, you should copy files instead

**10. What filesystem limitation affects hard links but not symbolic links?**
- a) File size restrictions
- b) Permission requirements
- c) Must be on the same filesystem
- d) Cannot be created by regular users

**11. When editing a file through a hard link, what happens to the original file?**
- a) The original file remains unchanged
- b) Both the original and hard link are modified (they're the same file)
- c) Only the hard link is modified
- d) The original file is automatically backed up

**12. Which command shows inode numbers to help identify hard links?**
- a) `ls -l`
- b) `ls -i`
- c) `ls -h`
- d) `ls -ln`

**13. In container environments, what's a best practice for symbolic links?**
- a) Always use absolute paths
- b) Avoid symbolic links entirely
- c) Use relative symbolic links within mounted volumes
- d) Convert all symbolic links to hard links

**14. What security concern exists with symbolic links in shared directories like /tmp?**
- a) They consume too much disk space
- b) They can be followed blindly by privileged scripts
- c) They're automatically deleted by the system
- d) They require root privileges to create

**15. If you have multiple hard links to a file, how many times is the actual data stored on disk?**
- a) Once for each hard link
- b) Twice (original + backup)
- c) Only once, shared by all hard links
- d) It depends on the filesystem type

### Answer Key

**1. b) The hard link continues to work and provides access to the data**  
*Explanation: Hard links point to the same inode as the original file. When the original is deleted, the inode and data remain as long as at least one hard link exists.*

**2. c) `ln -s original.txt symlink.txt`**  
*Explanation: The -s flag creates a symbolic (soft) link. Without -s, ln creates a hard link.*

**3. b) No, never**  
*Explanation: Hard links must be on the same filesystem because they reference inodes, which are filesystem-specific.*

**4. c) A path/reference to the original file**  
*Explanation: Symbolic links store the path to the target file, not the actual data or inode number.*

**5. b) Symbolic links only**  
*Explanation: Hard links cannot point to directories (with rare exceptions for special system use). Symbolic links can point to both files and directories.*

**6. b) It displays an arrow (→) pointing to the target**  
*Explanation: In ls -l output, symbolic links show as linkname → target, making them easily identifiable.*

**7. c) Unchanged files share the same inode, saving space**  
*Explanation: Hard links allow multiple backup snapshots to share the same physical data for unchanged files, dramatically reducing storage requirements.*

**8. b) `find /path -type l -xtype l`**  
*Explanation: This finds symbolic links (-type l) where the target doesn't exist (-xtype l), indicating broken links.*

**9. b) Symbolic links, because you can easily redirect to new versions**  
*Explanation: Symbolic links allow you to quickly point /current to a new release directory, enabling instant switches between versions.*

**10. c) Must be on the same filesystem**  
*Explanation: Hard links are restricted to the same filesystem, while symbolic links can cross filesystem boundaries.*

**11. b) Both the original and hard link are modified (they're the same file)**  
*Explanation: Hard links are different names for the same file/inode, so changes through any hard link affect all of them.*

**12. b) `ls -i`**  
*Explanation: The -i flag displays inode numbers. Files with the same inode number are hard links to each other.*

**13. c) Use relative symbolic links within mounted volumes**  
*Explanation: Relative paths within mounted volumes are more portable and less likely to break across container boundaries.*

**14. b) They can be followed blindly by privileged scripts**  
*Explanation: Malicious users could create symbolic links that point to sensitive files, potentially causing privileged scripts to access unintended locations.*

**15. c) Only once, shared by all hard links**  
*Explanation: All hard links to a file share the same inode and data blocks. The data is stored only once on disk regardless of how many hard links exist.*
