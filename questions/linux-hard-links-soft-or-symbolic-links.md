# What is the difference between hard links and soft/symbolic links?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#background-information)
  - [Comparison](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#comparison)
  - [Use cases](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#use-cases)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-hard-links-soft-or-symbolic-links.md#quiz)

## Answer

Hard links create multiple names for the same file data, while symbolic links create pointers that reference a file's path.

## Background information

### Comparison

| Aspect | Hard links | Symbolic links |
|--------|------------|----------------|
| **Inode** | Same inode as original | Different inode |
| **Data storage** | Points to same file data | Stores path reference |
| **Deletion behavior** | File survives if original deleted | Breaks if original deleted |
| **Filesystem boundaries** | Can't cross filesystems | Can cross filesystems |
| **Directory support** | Can't link directories | Can link directories |
| **Performance** | Direct access | Slight overhead (path resolution) |
| **Visibility** | Looks like regular file | Shows as link with target path |

### Use cases

- **Hard links:** Use for backup/persistence
- **Symbolic links:** Use for shortcuts and cross-filesystem references

## Quiz

### Multiple-choice questions

1. Hard link can cross file systems:
- True
- False

2. Soft/symbolic links can link directories:
- True
- False

3. Use hard links for backups/persistence:
- True
- False

### Answer key

- 1. False
- 2. True
- 3. True 
