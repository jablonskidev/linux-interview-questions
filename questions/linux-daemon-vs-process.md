# What is the difference between a daemon and a normal process?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-daemon-vs-process.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-daemon-vs-process.md#background-information)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-daemon-vs-process.md#quiz)

## Answer

A daemon is a background system process that runs continuously without user interaction, while a normal process runs in the foreground and is tied to user sessions.

## Background information

### Key differences

| Aspect | Daemon process | Normal process |
|--------|----------------|----------------|
| **Execution** | Background | Foreground |
| **Terminal** | No TTY (shows `?`) | Has controlling TTY |
| **Lifespan** | Boot to shutdown | Dies with session |
| **Control** | System managed | User controlled |
| **Signals** | No Ctrl+C/Ctrl+Z | Responds to keyboard |
| **Examples** | `sshd`, `httpd`, `mysqld` | `ls`, `vim`, `grep` |

Daemons:
- Detached from controlling terminal
- Adopted by init/systemd (PID 1)
- Close stdin/stdout/stderr or redirect to logs
- Change working directory to root (/)
- Create PID files to prevent duplicates

Normal processes:
- Inherit environment from parent
- Display output to terminal
- Terminate when parent dies or session ends

## Quiz

### Multiple-choice questions

1. Daemons run in the background for system services:
- a: True
- b: False

2. Normal processes are interactive:
- a: True
- b: False

3. Normal processes live from boot to shutdown:
- a: True
- b: False

### Answer key

- 1: a
- 2: a
- 3: b
