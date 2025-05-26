# What is a shell script and how would you create one?

**[Note: This is a work-in-progress draft.]**

## Quick Answer

**What is a shell script?** A text file containing shell commands that can be executed as a program to automate tasks.

**How to create one:**
1. Create a file with a text editor: `nano script.sh`
2. Add shebang line: `#!/bin/bash`
3. Write your commands
4. Make executable: `chmod +x script.sh`
5. Run it: `./script.sh`

Scripts can include variables, conditionals, loops, functions, and command substitution for powerful automation.

---

## What is a Shell Script?

A **shell script** is a text file containing commands that can be executed by a shell interpreter. Think of it as a recipe that automates tasks you'd normally type manually at the command line.

---

## Quick Start: Creating Your First Script

### The 5-Step Process

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `nano myscript.sh` | Create file with text editor |
| 2 | Add `#!/bin/bash` | Specify interpreter (shebang) |
| 3 | Write commands | Add your automation logic |
| 4 | `chmod +x myscript.sh` | Make executable |
| 5 | `./myscript.sh` | Run the script |

### Basic Example

```bash
#!/bin/bash
echo "Hello, World!"
echo "Current user: $USER"
echo "Current directory: $(pwd)"
```

---

## Essential Components

### Shebang Lines (Choose One)

| Shebang | Use Case | Portability |
|---------|----------|-------------|
| `#!/bin/bash` | Most features, common | Good |
| `#!/bin/sh` | POSIX compliant | Excellent |
| `#!/usr/bin/env bash` | Finds bash in PATH | Best |

### File Permissions

```bash
# Make executable
chmod +x script.sh

# Set specific permissions (rwxr-xr-x)
chmod 755 script.sh

# Check permissions
ls -l script.sh
```

---

## Core Scripting Concepts

### Variables & Data

#### Basic Variables
```bash
# Assignment (no spaces around =)
name="John"
count=42

# Usage
echo "Hello, $name"
echo "Count: ${count}"
```

#### Special Variables

| Variable | Contains |
|----------|----------|
| `$0` | Script name |
| `$1, $2, $3...` | Command-line arguments |
| `$#` | Number of arguments |
| `$@` | All arguments as separate words |
| `$?` | Exit code of last command |
| `$$` | Process ID of script |

#### Command Substitution
```bash
# Modern syntax (preferred)
current_date=$(date)
file_count=$(ls | wc -l)

# Legacy syntax
old_way=`date`
```

### Control Structures

#### Conditionals

```bash
# Basic if statement
if [ "$user" = "admin" ]; then
    echo "Welcome, administrator!"
elif [ "$user" = "guest" ]; then
    echo "Limited access granted"
else
    echo "Access denied"
fi

# Case statement
case "$choice" in
    1) echo "Option 1 selected" ;;
    2) echo "Option 2 selected" ;;
    *) echo "Invalid option" ;;
esac
```

#### Common Test Conditions

| Test | True When |
|------|-----------|
| `[ -f file ]` | File exists |
| `[ -d dir ]` | Directory exists |
| `[ -r file ]` | File is readable |
| `[ -w file ]` | File is writable |
| `[ -x file ]` | File is executable |
| `[ "$a" = "$b" ]` | Strings are equal |
| `[ "$a" -eq "$b" ]` | Numbers are equal |
| `[ "$a" -lt "$b" ]` | a is less than b |

#### Loops

```bash
# For loop with list
for item in apple banana cherry; do
    echo "Fruit: $item"
done

# For loop with range
for i in {1..5}; do
    echo "Number: $i"
done

# While loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    counter=$((counter + 1))
done
```

### Functions

```bash
# Define function
greet_user() {
    local name=$1
    echo "Hello, $name!"
    return 0
}

# Call function
greet_user "Alice"
```

---

## Input/Output Operations

### Reading User Input

```bash
# Simple input
echo "Enter your name:"
read username

# Input with prompt
read -p "Enter password: " -s password

# Input with timeout
read -t 10 -p "Quick! Enter something: " response
```

### Output Methods

| Command | Purpose | Example |
|---------|---------|---------|
| `echo` | Simple output | `echo "Hello"` |
| `printf` | Formatted output | `printf "%s: %d\n" "Count" 42` |

### Redirection

```bash
# Redirect output to file
echo "Log entry" > logfile.txt

# Append to file
echo "Another entry" >> logfile.txt

# Redirect errors
command 2> error.log

# Redirect both output and errors
command > output.log 2>&1
```

---

## Error Handling & Debugging

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1-255 | Various error conditions |

```bash
# Check if command succeeded
if grep "pattern" file.txt; then
    echo "Pattern found"
else
    echo "Pattern not found"
fi

# Set custom exit code
exit 1
```

### Debugging Techniques

| Method | Command | Purpose |
|--------|---------|---------|
| Verbose mode | `bash -x script.sh` | Show each command |
| Strict mode | `set -e` | Exit on any error |
| Undefined variables | `set -u` | Error on undefined vars |
| Debug in script | `set -x` | Enable debugging |

### Best Practices Checklist

- ✅ **Always quote variables**: `"$variable"` not `$variable`
- ✅ **Check command success**: Use `if` statements or `set -e`
- ✅ **Validate input**: Test user input and file existence
- ✅ **Use meaningful names**: `user_count` not `uc`
- ✅ **Add comments**: Explain complex logic
- ✅ **Handle errors gracefully**: Don't let scripts fail silently

---

## Common Script Patterns

### File Operations

```bash
# Check if file exists before processing
if [ -f "config.txt" ]; then
    source config.txt
else
    echo "Conf# What is a shell script and how would you create one?

## Quick Answer

**What is a shell script?** A text file containing shell commands that can be executed as a program to automate tasks.

**How to create one:**
1. Create a file with a text editor: `nano script.sh`
2. Add shebang line: `#!/bin/bash`
3. Write your commands
4. Make executable: `chmod +x script.sh`
5. Run it: `./script.sh`

Scripts can include variables, conditionals, loops, functions, and command substitution for powerful automation.

---

## What is a Shell Script?

A **shell script** is a text file containing commands that can be executed by a shell interpreter. Think of it as a recipe that automates tasks you'd normally type manually at the command line.

---

## Quick Start: Creating Your First Script

### The 5-Step Process

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `nano myscript.sh` | Create file with text editor |
| 2 | Add `#!/bin/bash` | Specify interpreter (shebang) |
| 3 | Write commands | Add your automation logic |
| 4 | `chmod +x myscript.sh` | Make executable |
| 5 | `./myscript.sh` | Run the script |

### Basic Example

```bash
#!/bin/bash
echo "Hello, World!"
echo "Current user: $USER"
echo "Current directory: $(pwd)"
```

---

## Essential Components

### Shebang Lines (Choose One)

| Shebang | Use Case | Portability |
|---------|----------|-------------|
| `#!/bin/bash` | Most features, common | Good |
| `#!/bin/sh` | POSIX compliant | Excellent |
| `#!/usr/bin/env bash` | Finds bash in PATH | Best |

### File Permissions

```bash
# Make executable
chmod +x script.sh

# Set specific permissions (rwxr-xr-x)
chmod 755 script.sh

# Check permissions
ls -l script.sh
```

---

## Core Scripting Concepts

### Variables & Data

#### Basic Variables
```bash
# Assignment (no spaces around =)
name="John"
count=42

# Usage
echo "Hello, $name"
echo "Count: ${count}"
```

#### Special Variables

| Variable | Contains |
|----------|----------|
| `$0` | Script name |
| `$1, $2, $3...` | Command-line arguments |
| `$#` | Number of arguments |
| `$@` | All arguments as separate words |
| `$?` | Exit code of last command |
| `$$` | Process ID of script |

#### Command Substitution
```bash
# Modern syntax (preferred)
current_date=$(date)
file_count=$(ls | wc -l)

# Legacy syntax
old_way=`date`
```

### Control Structures

#### Conditionals

```bash
# Basic if statement
if [ "$user" = "admin" ]; then
    echo "Welcome, administrator!"
elif [ "$user" = "guest" ]; then
    echo "Limited access granted"
else
    echo "Access denied"
fi

# Case statement
case "$choice" in
    1) echo "Option 1 selected" ;;
    2) echo "Option 2 selected" ;;
    *) echo "Invalid option" ;;
esac
```

#### Common Test Conditions

| Test | True When |
|------|-----------|
| `[ -f file ]` | File exists |
| `[ -d dir ]` | Directory exists |
| `[ -r file ]` | File is readable |
| `[ -w file ]` | File is writable |
| `[ -x file ]` | File is executable |
| `[ "$a" = "$b" ]` | Strings are equal |
| `[ "$a" -eq "$b" ]` | Numbers are equal |
| `[ "$a" -lt "$b" ]` | a is less than b |

#### Loops

```bash
# For loop with list
for item in apple banana cherry; do
    echo "Fruit: $item"
done

# For loop with range
for i in {1..5}; do
    echo "Number: $i"
done

# While loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    counter=$((counter + 1))
done
```

### Functions

```bash
# Define function
greet_user() {
    local name=$1
    echo "Hello, $name!"
    return 0
}

# Call function
greet_user "Alice"
```

---

## Input/Output Operations

### Reading User Input

```bash
# Simple input
echo "Enter your name:"
read username

# Input with prompt
read -p "Enter password: " -s password

# Input with timeout
read -t 10 -p "Quick! Enter something: " response
```

### Output Methods

| Command | Purpose | Example |
|---------|---------|---------|
| `echo` | Simple output | `echo "Hello"` |
| `printf` | Formatted output | `printf "%s: %d\n" "Count" 42` |

### Redirection

```bash
# Redirect output to file
echo "Log entry" > logfile.txt

# Append to file
echo "Another entry" >> logfile.txt

# Redirect errors
command 2> error.log

# Redirect both output and errors
command > output.log 2>&1
```

---

## Error Handling & Debugging

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1-255 | Various error conditions |

```bash
# Check if command succeeded
if grep "pattern" file.txt; then
    echo "Pattern found"
else
    echo "Pattern not found"
fi

# Set custom exit code
exit 1
```

### Debugging Techniques

| Method | Command | Purpose |
|--------|---------|---------|
| Verbose mode | `bash -x script.sh` | Show each command |
| Strict mode | `set -e` | Exit on any error |
| Undefined variables | `set -u` | Error on undefined vars |
| Debug in script | `set -x` | Enable debugging |

### Best Practices Checklist

- ✅ **Always quote variables**: `"$variable"` not `$variable`
- ✅ **Check command success**: Use `if` statements or `set -e`
- ✅ **Validate input**: Test user input and file existence
- ✅ **Use meaningful names**: `user_count` not `uc`
- ✅ **Add comments**: Explain complex logic
- ✅ **Handle errors gracefully**: Don't let scripts fail silently

---

## Common Script Patterns

### File Operations

```bash
# Check if file exists before processing
if [ -f "config.txt" ]; then
    source config.txt
else
    echo "Config file not found!"
    exit 1
fi

# Process multiple files
for file in *.txt; do
    if [ -f "$file" ]; then
        echo "Processing: $file"
        # Do something with file
    fi
done
```

### Menu System

```bash
show_menu() {
    echo "1. List files"
    echo "2. Show date"
    echo "3. Exit"
    read -p "Choose option: " choice
}

while true; do
    show_menu
    case $choice in
        1) ls -la ;;
        2) date ;;
        3) exit 0 ;;
        *) echo "Invalid option" ;;
    esac
done
```

### Backup Script Template

```bash
#!/bin/bash

# Configuration
SOURCE_DIR="/home/user/documents"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)

# Validation
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory doesn't exist"
    exit 1
fi

# Create backup
mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE_DIR"

if [ $? -eq 0 ]; then
    echo "Backup completed successfully"
else
    echo "Backup failed"
    exit 1
fi
```

---

## Advanced Features

### Process Management

```bash
# Run command in background
long_running_command &
pid=$!# What is a shell script and how would you create one?

## Quick Answer

**What is a shell script?** A text file containing shell commands that can be executed as a program to automate tasks.

**How to create one:**
1. Create a file with a text editor: `nano script.sh`
2. Add shebang line: `#!/bin/bash`
3. Write your commands
4. Make executable: `chmod +x script.sh`
5. Run it: `./script.sh`

Scripts can include variables, conditionals, loops, functions, and command substitution for powerful automation.

---

## What is a Shell Script?

A **shell script** is a text file containing commands that can be executed by a shell interpreter. Think of it as a recipe that automates tasks you'd normally type manually at the command line.

---

## Quick Start: Creating Your First Script

### The 5-Step Process

| Step | Command | Purpose |
|------|---------|---------|
| 1 | `nano myscript.sh` | Create file with text editor |
| 2 | Add `#!/bin/bash` | Specify interpreter (shebang) |
| 3 | Write commands | Add your automation logic |
| 4 | `chmod +x myscript.sh` | Make executable |
| 5 | `./myscript.sh` | Run the script |

### Basic Example

```bash
#!/bin/bash
echo "Hello, World!"
echo "Current user: $USER"
echo "Current directory: $(pwd)"
```

---

## Essential Components

### Shebang Lines (Choose One)

| Shebang | Use Case | Portability |
|---------|----------|-------------|
| `#!/bin/bash` | Most features, common | Good |
| `#!/bin/sh` | POSIX compliant | Excellent |
| `#!/usr/bin/env bash` | Finds bash in PATH | Best |

### File Permissions

```bash
# Make executable
chmod +x script.sh

# Set specific permissions (rwxr-xr-x)
chmod 755 script.sh

# Check permissions
ls -l script.sh
```

---

## Core Scripting Concepts

### Variables & Data

#### Basic Variables
```bash
# Assignment (no spaces around =)
name="John"
count=42

# Usage
echo "Hello, $name"
echo "Count: ${count}"
```

#### Special Variables

| Variable | Contains |
|----------|----------|
| `$0` | Script name |
| `$1, $2, $3...` | Command-line arguments |
| `$#` | Number of arguments |
| `$@` | All arguments as separate words |
| `$?` | Exit code of last command |
| `$$` | Process ID of script |

#### Command Substitution
```bash
# Modern syntax (preferred)
current_date=$(date)
file_count=$(ls | wc -l)

# Legacy syntax
old_way=`date`
```

### Control Structures

#### Conditionals

```bash
# Basic if statement
if [ "$user" = "admin" ]; then
    echo "Welcome, administrator!"
elif [ "$user" = "guest" ]; then
    echo "Limited access granted"
else
    echo "Access denied"
fi

# Case statement
case "$choice" in
    1) echo "Option 1 selected" ;;
    2) echo "Option 2 selected" ;;
    *) echo "Invalid option" ;;
esac
```

#### Common Test Conditions

| Test | True When |
|------|-----------|
| `[ -f file ]` | File exists |
| `[ -d dir ]` | Directory exists |
| `[ -r file ]` | File is readable |
| `[ -w file ]` | File is writable |
| `[ -x file ]` | File is executable |
| `[ "$a" = "$b" ]` | Strings are equal |
| `[ "$a" -eq "$b" ]` | Numbers are equal |
| `[ "$a" -lt "$b" ]` | a is less than b |

#### Loops

```bash
# For loop with list
for item in apple banana cherry; do
    echo "Fruit: $item"
done

# For loop with range
for i in {1..5}; do
    echo "Number: $i"
done

# While loop
counter=1
while [ $counter -le 5 ]; do
    echo "Count: $counter"
    counter=$((counter + 1))
done
```

### Functions

```bash
# Define function
greet_user() {
    local name=$1
    echo "Hello, $name!"
    return 0
}

# Call function
greet_user "Alice"
```

---

## Input/Output Operations

### Reading User Input

```bash
# Simple input
echo "Enter your name:"
read username

# Input with prompt
read -p "Enter password: " -s password

# Input with timeout
read -t 10 -p "Quick! Enter something: " response
```

### Output Methods

| Command | Purpose | Example |
|---------|---------|---------|
| `echo` | Simple output | `echo "Hello"` |
| `printf` | Formatted output | `printf "%s: %d\n" "Count" 42` |

### Redirection

```bash
# Redirect output to file
echo "Log entry" > logfile.txt

# Append to file
echo "Another entry" >> logfile.txt

# Redirect errors
command 2> error.log

# Redirect both output and errors
command > output.log 2>&1
```

---

## Error Handling & Debugging

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1-255 | Various error conditions |

```bash
# Check if command succeeded
if grep "pattern" file.txt; then
    echo "Pattern found"
else
    echo "Pattern not found"
fi

# Set custom exit code
exit 1
```

### Debugging Techniques

| Method | Command | Purpose |
|--------|---------|---------|
| Verbose mode | `bash -x script.sh` | Show each command |
| Strict mode | `set -e` | Exit on any error |
| Undefined variables | `set -u` | Error on undefined vars |
| Debug in script | `set -x` | Enable debugging |

### Best Practices Checklist

- ✅ **Always quote variables**: `"$variable"` not `$variable`
- ✅ **Check command success**: Use `if` statements or `set -e`
- ✅ **Validate input**: Test user input and file existence
- ✅ **Use meaningful names**: `user_count` not `uc`
- ✅ **Add comments**: Explain complex logic
- ✅ **Handle errors gracefully**: Don't let scripts fail silently

---

## Common Script Patterns

### File Operations

```bash
# Check if file exists before processing
if [ -f "config.txt" ]; then
    source config.txt
else
    echo "Config file not found!"
    exit 1
fi

# Process multiple files
for file in *.txt; do
    if [ -f "$file" ]; then
        echo "Processing: $file"
        # Do something with file
    fi
done
```

### Menu System

```bash
show_menu() {
    echo "1. List files"
    echo "2. Show date"
    echo "3. Exit"
    read -p "Choose option: " choice
}

while true; do
    show_menu
    case $choice in
        1) ls -la ;;
        2) date ;;
        3) exit 0 ;;
        *) echo "Invalid option" ;;
    esac
done
```

### Backup Script Template

```bash
#!/bin/bash

# Configuration
SOURCE_DIR="/home/user/documents"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)

# Validation
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory doesn't exist"
    exit 1
fi

# Create backup
mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE_DIR"

if [ $? -eq 0 ]; then
    echo "Backup completed successfully"
else
    echo "Backup failed"
    exit 1
fi
```

---

## Advanced Features

### Process Management

```bash
# Run command in background
long_running_command &
pid=$!

# Wait for background process
wait $pid

# Handle signals
trap 'echo "Script interrupted"; exit 1' INT TERM
```

### Array Handling

```bash
# Create array
fruits=("apple" "banana" "cherry")

# Access elements
echo "First fruit: ${fruits[0]}"
echo "All fruits: ${fruits[@]}"
echo "Number of fruits: ${#fruits[@]}"

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done
```

---

## Troubleshooting Guide

### Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| `Permission denied` | Not executable | `chmod +x script.sh` |
| `Command not found` | Wrong PATH or typo | Check spelling, use full path |
| `Syntax error` | Missing quotes/brackets | Check matching pairs |
| `Variable empty` | Typo in variable name | Use `set -u` to catch |

### Testing Your Scripts

```bash
# Syntax check without execution
bash -n script.sh

# Dry run with debugging
bash -x script.sh

# Test with different inputs
echo "test input" | ./script.sh
```

---

## Quick Reference

### Essential Commands for Scripts

| Task | Command |
|------|---------|
| Get script directory | `DIR="$(cd "$(dirname "$0")" && pwd)"` |
| Check if root user | `[ "$EUID" -eq 0 ]` |
| Generate random number | `$RANDOM` |
| Get current timestamp | `$(date +%s)` |
| Sleep for N seconds | `sleep 5` |
| Check internet connectivity | `ping -c 1 google.com >/dev/null 2>&1` |

### Script Template

```bash
#!/usr/bin/env bash

# Script: script_name.sh
# Purpose: Brief description
# Author: Your name
# Date: YYYY-MM-DD

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# Functions
usage() {
    echo "Usage: $SCRIPT_NAME [options]"
    echo "Options:"
    echo "  -h, --help    Show this help message"
    exit 0
}

main() {
    # Your main script logic here
    echo "Script execution completed"
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        *)
            echo "Unknown option: $1"
            exit 1
            ;;
    esac
    shift
done

# Run main function
main "$@"
```

# Wait for background process
wait $pid

# Handle signals
trap 'echo "Script interrupted"; exit 1' INT TERM
```

### Array Handling

```bash
# Create array
fruits=("apple" "banana" "cherry")

# Access elements
echo "First fruit: ${fruits[0]}"
echo "All fruits: ${fruits[@]}"
echo "Number of fruits: ${#fruits[@]}"

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done
```

---

## Troubleshooting Guide

### Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| `Permission denied` | Not executable | `chmod +x script.sh` |
| `Command not found` | Wrong PATH or typo | Check spelling, use full path |
| `Syntax error` | Missing quotes/brackets | Check matching pairs |
| `Variable empty` | Typo in variable name | Use `set -u` to catch |

### Testing Your Scripts

```bash
# Syntax check without execution
bash -n script.sh

# Dry run with debugging
bash -x script.sh

# Test with different inputs
echo "test input" | ./script.sh
```

---

## Quick Reference

### Essential Commands for Scripts

| Task | Command |
|------|---------|
| Get script directory | `DIR="$(cd "$(dirname "$0")" && pwd)"` |
| Check if root user | `[ "$EUID" -eq 0 ]` |
| Generate random number | `$RANDOM` |
| Get current timestamp | `$(date +%s)` |
| Sleep for N seconds | `sleep 5` |
| Check internet connectivity | `ping -c 1 google.com >/dev/null 2>&1` |

### Script Template

```bash
#!/usr/bin/env bash

# Script: script_name.sh
# Purpose: Brief description
# Author: Your name
# Date: YYYY-MM-DD

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# Functions
usage() {
    echo "Usage: $SCRIPT_NAME [options]"
    echo "Options:"
    echo "  -h, --help    Show this help message"
    exit 0
}

main() {
    # Your main script logic here
    echo "Script execution completed"
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        *)
            echo "Unknown option: $1"
            exit 1
            ;;
    esac
    shift
done

# Run main function
main "$@"
```ig file not found!"
    exit 1
fi

# Process multiple files
for file in *.txt; do
    if [ -f "$file" ]; then
        echo "Processing: $file"
        # Do something with file
    fi
done
```

### Menu System

```bash
show_menu() {
    echo "1. List files"
    echo "2. Show date"
    echo "3. Exit"
    read -p "Choose option: " choice
}

while true; do
    show_menu
    case $choice in
        1) ls -la ;;
        2) date ;;
        3) exit 0 ;;
        *) echo "Invalid option" ;;
    esac
done
```

### Backup Script Template

```bash
#!/bin/bash

# Configuration
SOURCE_DIR="/home/user/documents"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)

# Validation
if [ ! -d "$SOURCE_DIR" ]; then
    echo "Error: Source directory doesn't exist"
    exit 1
fi

# Create backup
mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE_DIR"

if [ $? -eq 0 ]; then
    echo "Backup completed successfully"
else
    echo "Backup failed"
    exit 1
fi
```

---

## Advanced Features

### Process Management

```bash
# Run command in background
long_running_command &
pid=$!

# Wait for background process
wait $pid

# Handle signals
trap 'echo "Script interrupted"; exit 1' INT TERM
```

### Array Handling

```bash
# Create array
fruits=("apple" "banana" "cherry")

# Access elements
echo "First fruit: ${fruits[0]}"
echo "All fruits: ${fruits[@]}"
echo "Number of fruits: ${#fruits[@]}"

# Loop through array
for fruit in "${fruits[@]}"; do
    echo "Fruit: $fruit"
done
```

---

## Troubleshooting Guide

### Common Issues

| Problem | Cause | Solution |
|---------|-------|----------|
| `Permission denied` | Not executable | `chmod +x script.sh` |
| `Command not found` | Wrong PATH or typo | Check spelling, use full path |
| `Syntax error` | Missing quotes/brackets | Check matching pairs |
| `Variable empty` | Typo in variable name | Use `set -u` to catch |

### Testing Your Scripts

```bash
# Syntax check without execution
bash -n script.sh

# Dry run with debugging
bash -x script.sh

# Test with different inputs
echo "test input" | ./script.sh
```

---

## Quick Reference

### Essential Commands for Scripts

| Task | Command |
|------|---------|
| Get script directory | `DIR="$(cd "$(dirname "$0")" && pwd)"` |
| Check if root user | `[ "$EUID" -eq 0 ]` |
| Generate random number | `$RANDOM` |
| Get current timestamp | `$(date +%s)` |
| Sleep for N seconds | `sleep 5` |
| Check internet connectivity | `ping -c 1 google.com >/dev/null 2>&1` |

### Script Template

```bash
#!/usr/bin/env bash

# Script: script_name.sh
# Purpose: Brief description
# Author: Your name
# Date: YYYY-MM-DD

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"

# Functions
usage() {
    echo "Usage: $SCRIPT_NAME [options]"
    echo "Options:"
    echo "  -h, --help    Show this help message"
    exit 0
}

main() {
    # Your main script logic here
    echo "Script execution completed"
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        *)
            echo "Unknown option: $1"
            exit 1
            ;;
    esac
    shift
done

# Run main function
main "$@"
```
