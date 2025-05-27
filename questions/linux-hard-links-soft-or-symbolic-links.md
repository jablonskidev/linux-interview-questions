# What is the difference between hard links and soft/symbolic links?

**[Note: This is a work-in-progress draft.]**

## Answer

Hard links create multiple names for the same file data, while symbolic links create pointers that reference a file's path.

## Background information

### Comparison

| Aspect | Hard links | Symbolic links |
|--------|------------|----------------|
| **Inode** | Same inode as original | Different inode |
| **Data storage** | Points to same file data | Stores path reference |
| **Deletion behavior** | File survives if original deleted | Breaks if original deleted |
| **Filesystem boundaries** | Cannot cross filesystems | Can cross filesystems |
| **Directory support** | Cannot link directories | Can link directories |
| **Performance** | Direct access | Slight overhead (path resolution) |
| **Visibility** | Looks like regular file | Shows as link with target path |

### Use cases

- **Hard links:** Use for backup/persistence
- **Symbolic links:** Use for shortcuts and cross-filesystem references

## Quiz

### Multiple-choice questions

### Answer key
