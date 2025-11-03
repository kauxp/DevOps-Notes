# Introduction to Shell scripting

Short summary:

Basics of writing shell scripts for automation and tooling.

What you'll learn:

- Bash syntax, variables, conditionals, loops
- Writing robust scripts and error handling
- Useful tooling: cron, systemd timers

Resources:

- Add links or notes here

Exercises:

- Create small automation scripts (backup, log rotation)

Notes:

- Add instructor notes or references here

## Shell scripting notes

---

## 1. Hello world shell script

```bash
#!/bin/bash
# This is a simple shell script that prints "Hello, World!" to the terminal
echo "Hello, World!"
```

### Hello world shell script explained

- `#!` (shebang / "sha-bang") — instructs the OS to use the interpreter at `/bin/bash` to run the script.
- The `echo` command prints text to stdout.

### Other Common Shebang Options

Shebang | Interpreter | Use Case
--- | --- | ---
`#!/bin/bash` | Bash shell | Most common; full-featured scripting shell.
`#!/bin/sh` | Basic shell | Faster, POSIX-compliant, but lacks some Bash features (on Ubuntu often dash).
`#!/usr/bin/env bash` | Find bash in $PATH | More portable across systems where Bash may not be in `/bin`.
`#!/bin/zsh` | Z shell | Used if you prefer Zsh scripting features.
`#!/usr/bin/python3` | Python interpreter | Used for Python scripts.
`#!/usr/bin/perl` | Perl interpreter | Used for Perl scripts.

---

## 2. Useful example scripts

### Display Current Date and Time (current_time.sh)

```bash
#!/bin/bash
echo "Current date and time: $(date)"
```

### Check Disk Usage (disk_usage.sh)

```bash
#!/bin/bash
echo "Disk usage report:"
df -h
```

### List Files in a Directory (list_files.sh)

```bash
#!/bin/bash
echo "Files in current directory:"
ls -l
```

### Check if a File Exists (file_check.sh)

```bash
#!/bin/bash
read -p "Enter filename: " file
if [ -f "$file" ]; then
  echo "File '$file' exists."
else
  echo "File '$file' does not exist."
fi
```

### Add Two Numbers (add_numbers.sh)

```bash
#!/bin/bash
read -p "Enter first number: " a
read -p "Enter second number: " b
sum=$((a + b))
echo "Sum: $sum"
```

### Print System Uptime (system_uptime.sh)

```bash
#!/bin/bash
echo "System has been up for:"
uptime -p
```

### Check User Login (check_user.sh)

```bash
#!/bin/bash
read -p "Enter username to check: " user
if id "$user" &>/dev/null; then
  echo "User '$user' exists."
else
  echo "User '$user' not found."
fi
```

### Backup a Directory (backup_dir.sh)

```bash
#!/bin/bash
read -p "Enter source directory: " src
read -p "Enter backup directory: " dest
tar -czf "$dest/backup_$(date +%F).tar.gz" "$src"
echo "Backup completed successfully!"
```

### Check Internet Connectivity (check_internet.sh)

```bash
#!/bin/bash
if ping -c 1 8.8.8.8 &>/dev/null; then
  echo "Internet is working."
else
  echo "No internet connection."
fi
```

### Find the Length of a String (string_length.sh)

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Length of string: ${#str}"
```

---

## Parameter Expansion and String Manipulation

- `${str}` — Parameter expansion. Returns the value of variable `str`.
- `${str^^}` — Convert to uppercase (Bash 4+).
- `${var^}` — Uppercase first character.
- `${var,}` — Lowercase first character.
- `${var,,}` — Lowercase all characters.

_Note: these features are Bash-specific and may not work in `/bin/sh`._

### Convert String to Uppercase (uppercase.sh)

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Uppercase: ${str^^}"
```

### Convert String to Lowercase (lowercase.sh)

```bash
#!/bin/bash
read -p "Enter a string: " str
echo "Lowercase: ${str,,}"
```

### Reverse a String (reverse_string.sh)

```bash
#!/bin/bash
read -p "Enter a string: " str
rev_str=$(echo "$str" | rev)
echo "Reversed string: $rev_str"
```

### Check if Two Strings are Equal (compare_strings.sh)

```bash
#!/bin/bash
read -p "Enter first string: " str1
read -p "Enter second string: " str2
if [[ "$str1" == "$str2" ]]; then
  echo "Strings are equal."
else
  echo "Strings are not equal."
fi
```

### Extract a Substring (substring_extract.sh)

```bash
#!/bin/bash
read -p "Enter a string: " str
read -p "Enter starting position: " pos
read -p "Enter length: " len
echo "Substring: ${str:$pos:$len}"
```

---

## /bin/sh vs /bin/bash

Feature | /bin/sh | /bin/bash
--- | --- | ---
Name | Bourne Shell (POSIX) | Bourne Again Shell (Bash)
Speed | Slightly faster (lighter) | Slightly slower (more features)
Portability | Very portable (exists on all UNIX systems) | Common but not guaranteed everywhere
Features | Basic, POSIX-compliant | Rich: arrays, functions, string ops, brace expansion, `[[ ]]`, `==`
Use case | Simple, portable scripts | Complex, interactive, feature-rich scripts

### Bash-only examples that fail in /bin/sh

- `${var^^}` (uppercase conversion) → `Bad substitution` in `/bin/sh`.
- Arrays (`colors=(...)`) → syntax error in `/bin/sh`.
- `[[ ... ]]` conditional → unsupported in `/bin/sh`.

Practical guideline: use `/bin/sh` for minimal, portable scripts and `/bin/bash` where Bash features are required.

---

## User creation script (non-standards)

```bash
#!/bin/bash
USERNAME="$1"
GROUPNAME="$2"
groupadd "$GROUPNAME"
useradd -m -s /bin/bash -g "$GROUPNAME" "$USERNAME"
# Add welcome message to .bashrc
echo "echo \"Welcome, $USERNAME!!\"" >> /home/$USERNAME/.bashrc
echo "Setup complete! When '$USERNAME' logs in, they'll see a welcome message."
```

## User creation script (standardized)

```bash
#!/bin/bash
# ===============================================
# Script Name: create_user.sh
# Description: Create a new Linux user and group,
# set home directory and shell.
# Usage: sudo ./create_user.sh <username> <groupname>
# ===============================================
set -e
if [[ $EUID -ne 0 ]]; then
  echo "This script must be run as root (use sudo)"
  exit 1
fi
if [[ $# -ne 2 ]]; then
  echo "Usage: $0 <username> <groupname>"
  exit 1
fi
USERNAME="$1"
GROUPNAME="$2"
if getent group "$GROUPNAME" > /dev/null; then
  echo " Group '$GROUPNAME' already exists."
else
  echo "Creating group '$GROUPNAME'..."
  groupadd "$GROUPNAME"
  echo " Group '$GROUPNAME' created."
fi
if id "$USERNAME" &>/dev/null; then
  echo " User '$USERNAME' already exists."
else
  echo " Creating user '$USERNAME'..."
  useradd -m -s /bin/bash -g "$GROUPNAME" "$USERNAME"
  echo " User '$USERNAME' created with home directory /home/$USERNAME"
fi
read -p "Do you want to set a password for $USERNAME? (y/n): " choice
if [[ "$choice" == "y" || "$choice" == "Y" ]]; then
  passwd "$USERNAME"
fi
echo " User setup completed successfully!"
```

---

## Assignment: Timestamped directories utility

### Objective

Create a reusable utility script and a main script that automate project setup by creating uniquely named directories with timestamps.

### utils.sh

```bash
#!/bin/bash
# utils.sh — Utility functions for project setup
create_timestamped_dir() {
  local project_name="$1"
  if [ -z "$project_name" ]; then
    echo "Error: Project name not provided."
    return 1
  fi
  local timestamp
  timestamp=$(date +%Y%m%d-%H%M%S)
  local dir_path="/tmp/${project_name}-${timestamp}"
  mkdir -p "$dir_path"
  echo "Directory created: $dir_path"
}
```

### setup_project.sh

```bash
#!/bin/bash
# setup_project.sh — Main script to set up a new project
source "$(dirname "$0")/utils.sh"
create_timestamped_dir "my-new-app"
```

---

## ACLs (Access Control Lists)

1. ACLs allow fine-grained permissions for multiple users/groups on the same file or directory.
2. Check if ACLs are enabled:

```bash
mount | grep acl
```

3. Viewing ACLs:

```bash
getfacl filename
```

4. Setting ACLs:

```bash
setfacl -m u:john:r-- project.txt
setfacl -m g:developers:rw- project.txt
```

5. Removing ACL entries:

```bash
setfacl -x u:john project.txt
setfacl -b project.txt  # remove all ACLs
```

6. Recursive and default ACLs:

```bash
setfacl -R -m u:john:rwX /var/www/
setfacl -d -m u:john:rwX /var/www/
```

7. Understanding mask:

```bash
setfacl -m m::r-- file.txt
```

8. Script to apply ACLs for a team:

```bash
#!/bin/bash
SHARE_DIR="/data/team"
USER_LIST="alice bob charlie"
for user in $USER_LIST; do
  setfacl -m u:$user:rwx $SHARE_DIR
done
```
