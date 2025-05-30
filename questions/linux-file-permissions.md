# What are file permissions in Linux?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-permissions.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-permissions.md#background-information)
  - [Permission types](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-permissions.md#permission-types)
  - [User categories](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-permissions.md#user-categories)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-file-permissions.md#quiz)
## Answer

File permissions in Linux control who can read, write, or execute files and directories. They're a fundamental security feature that determines access rights for different users.

## Background information

### Permission types

There are three basic permission types:
- Read (r): View file contents or list directory contents
- Write (w): Modify file contents or create/delete files in a directory
- Execute (x): Run a file as a program or enter a directory

### User categories

Permissions are set for three categories of users:
- Owner: The user who owns the file
- Group: Users who belong to the file's group
- Other: All other users on the system

## Quiz

### Multiple-choice questions

1. File permissions control who can:
- a: Read, write, or execute files
- b: Read, write, or execute files and directories

2. The permission type for deleting files is:
- a: r
- b: w
- c: x

### Answer

- 1: b
- 2: w
