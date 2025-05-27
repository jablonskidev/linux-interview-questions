# What are inodes in Linux filesystems?

**[Note: This is a work-in-progress draft.]**

## Answer

Inodes are data structures in Linux filesystems that store file metadata.

## Background information

Inodes contain:
- File permissions, ownership, and type
- File size and timestamps
- Pointers to where the actual data is stored
- Link count (how many names point to this file)

Key points:
- Every file gets exactly one inode with a unique number
- Filenames are separate from inodes and just point to inode numbers
- This separation enables hard links (multiple names for the same file)
- When you delete a file, you're removing the name, but the inode stays until no names point to it
- Moving files within the same filesystem is instant because you're just updating directory entries, not moving data blocks

## Quiz

### Multiple-choice questions

### Answer key
