# What is the difference between a daemon and a normal process?

**[Note: This is a work-in-progress draft.]**

## Answer

A daemon is a background system process that runs continuously without user interaction, while a normal process runs in the foreground and is tied to user sessions.

## Background information

### Key differences

Daemons:
- Background
- Always running
- No user input
- System services
- Examples: `sshd`, `httpd`, `mysqld`

Normal processes:
- Foreground
- User-controlled
- Interactive
- Session-based
- Examples: `ls`, `vim`, `grep`

| Aspect | Daemon process | Normal process |
|--------|----------------|----------------|
| **Execution** | Background | Foreground |
| **Terminal** | No TTY (shows `?`) | Has controlling TTY |
| **Lifespan** | Boot to shutdown | Dies with session |
| **Control** | System managed | User controlled |
| **Signals** | No Ctrl+C/Ctrl+Z | Responds to keyboard |

### Technical details

Daemons:
- **Detached** from controlling terminal
- **Adopted** by init/systemd (PID 1)
- **Close** stdin/stdout/stderr or redirect to logs
- **Change** working directory to root (/)
- **Create** PID files to prevent duplicates

Normal processes:
- **Inherit** environment from parent
- **Display** output to terminal
- **Terminate** when parent dies or session ends

## Quiz

### Multiple-choice questions

### Answer key
