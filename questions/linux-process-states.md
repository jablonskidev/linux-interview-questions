# What are the common process states in linux?

- [Answer](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#answer)
- [Background information](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#background-information)
  - [Process states table](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#process-states-table)
  - [How to check process states](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#how-to-check-process-states)
  - [Common modifiers](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#common-modifiers)
  - [Red flags to watch for](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#red-flags-to-watch-for)
- [Quiz](https://github.com/jablonskidev/linux-interview-questions/blob/main/questions/linux-process-states.md#quiz)

## Answer
Linux processes have six main states: Running (R), Sleeping (S/D), Stopped (T), Zombie (Z), and Idle (I).

## Background information

### Process states table

| State | Symbol | Description | When it occurs |
|-------|---------|-------------|----------------|
| **Running** | R | Currently executing or ready to run | Process is on CPU or in run queue |
| **Interruptible sleep** | S | Waiting for event, can be interrupted | Waiting for I/O, can receive signals |
| **Uninterruptible sleep** | D | Waiting for I/O, cannot be interrupted | Critical I/O operations |
| **Stopped** | T | Paused by signal or debugger | SIGSTOP received or being debugged |
| **Zombie** | Z | Finished but parent hasn't read exit status | Process died, waiting for cleanup |
| **Idle** | I | Kernel thread that's idle | Kernel threads with no work |

### How to check process states
- `ps aux`: View all processes (STAT column shows state)
- `top`: Real-time process viewer
- `htop`: Enhanced process viewer

### Common modifiers
- `+`: Foreground process
- `<`: High priority
- `N`: Low priority  
- `s`: Session leader

### Red flags to watch for
- **Too many Zombies (Z):** Parent processes not cleaning up children
- **Many Uninterruptible Sleep (D):** I/O bottlenecks or hardware issues

## Quiz

### Multiple-choice questions

1. Linux processes have this main main states:
- a. 4
- b. 5
- c. 6

2. The symboles for Interruptible Sleep and Uninterruptible Sleep are:
- a. S and Z
- b. I and S
- c. S and D

3. Too many Zombies indidates:
- a. Parent processes not cleaning up children
- b. I/O bottlenecks
- c. Hardware issues

### Answer key

- 1: c
- 2: c
- 3: a
