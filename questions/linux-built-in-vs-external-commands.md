# What is the difference between bash shell built-in commands and external commands?

**[Note: This is a work-in-progress draft.]**

## Answer

Built-ins are part of bash itself (like `cd`, `echo`), while externals are separate programs (like `ls`, `grep`).

## Background information

| Built-in commands | External commands |
|-------------------|-------------------|
| Part of bash shell | Separate programs in filesystem |
| Execute in same process | Spawn new process |
| Fast execution | Slower (process overhead) |
| Can modify shell environment | Can't modify parent shell |
| Examples: `cd`, `echo`, `pwd` | Examples: `ls`, `grep`, `find` |

Built-in commands are compiled directly into bash and run within the current shell process. This makes them faster and allows them to modify the shell's state directly, which is why `cd` can change your current directory.

External commands are separate executable files stored in directories like `/bin` or `/usr/bin`. The shell must fork a new process to run them, making them slightly slower but enabling more complex functionality.

The distinction matters because built-ins execute faster and can affect your shell's environment, while externals provide specialized functionality but run in isolation.

## Quiz

### Multiple-choice question

### Answer key
