# What are partitions in Linux?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-partitions.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-partitions.md#backround-information)
  - [Purpose](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-partitions.md#purpose)
  - [Common partition types](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-partitions.md#common-partition-types)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-partitions.md#quiz)

## Answer

Partitions divide storage devices into separate sections for better organization, security, and flexibility. Partitions are logical divisions of physical storage that create independent sections on the same device. Think of it like dividing a filing cabinet into separate drawers.

## Backround information

### Purpose

| Purpose | Benefit | Example |
|---------|---------|---------|
| **Organization** | Keep different data types separate | System files vs. personal files |
| **Multi-boot** | Run multiple operating systems | Linux + Windows on same drive |
| **File systems** | Use optimal formats for different needs | ext4 for general use, FAT32 for compatibility |
| **Security** | Isolate and protect critical data | Separate system from user data |
| **Performance** | Optimize for specific workloads | Fast storage for databases |

### Common partition types

Essential partitions:
- **Root (/):** Core system files and directories
- **Swap:** Virtual memory when RAM is full
- **Boot:** Files needed to start the system

Optional partitions:
- **Home (/home):** User accounts and personal files
- **Recovery:** System restoration tools
- **Data:** Shared files or applications

Start simple with basic partitions, then add complexity as needed for your specific use case.

## Quiz

### Multiple-choice questions

1. Partitions are logical divisions of physical storage that create independent sections on the same device:
- a: True
- b: False

2. Data is an essential partition:
- a: True
- b: False

### Answer key

- 1: a
- 2: b
