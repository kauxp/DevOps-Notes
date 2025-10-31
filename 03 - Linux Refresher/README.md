# Linux Refresher

Short summary:

A quick refresher on Linux fundamentals useful for DevOps work.

What you'll learn:

- Basic shell commands and file permissions
- Process management and systemd basics
- Package management and logs

Resources:

- Add links or notes here

Exercises:

- Add practice tasks (file permissions, package install)

Notes:

- Add instructor notes or references here

## Linux Class Notes

## AWS Fundamentals

### AWS Regions – The Global Layer

#### What is a Region?

An AWS Region is a geographically isolated area that contains multiple Availability Zones (AZs). Each region is designed to be independent, providing data sovereignty, fault tolerance, and low latency.

Example Regions:

- Region Name: US East (N. Virginia) — Code: `us-east-1` — Location: North America

Each region has its own set of resources, like EC2 instances, RDS databases, and S3 buckets.

### Availability Zones (AZs)

- Each Region has multiple Availability Zones (usually 2–6).
- An AZ is a physically separate datacenter (or group of datacenters) connected via low-latency links.

Example:

Region: `ap-south-1`

- `ap-south-1a`
- `ap-south-1b`
- `ap-south-1c`


### VPC (Virtual Private Cloud) Overview

Inside each Region, you create a VPC — an isolated virtual network for your AWS resources. You can create multiple VPCs in a region.

A VPC lets you define:

- IP address ranges (CIDR blocks)
- Subnets
- Route tables
- Internet Gateways
- Security groups / Network ACLs (NACLs)


### Subnets in AWS

#### What is a Subnet?

A Subnet is a smaller network segment inside your VPC. Each Subnet belongs to exactly one Availability Zone. Subnets divide the VPC into logical groups (e.g., public vs private). You can attach routing rules to control internet access.

#### Subnet Best Practices

Follow these guidelines for real projects:

1. Always use multiple AZs for redundancy.
2. Divide subnets into tiers (public, private, isolated).
3. Use NAT Gateways for private subnet internet access.
4. Reserve enough CIDR space for scaling.
5. Tag subnets clearly (e.g., `Env=Prod`, `Tier=Public`).
6. Use Route Tables and Security Groups to isolate traffic.


### AWS Fields that appear while creating EC2 instances

1. Name & Tags

   - Name: A tag where key = `Name` and value = whatever you choose. It helps you identify the instance later. Optional but strongly recommended.
   - Additional Tags: e.g., `Environment=Production`, `Role=WebServer` — useful for organization, cost-allocation, and automation.

2. Application & OS Images (AMI)

   - AMI: The Amazon Machine Image you choose (contains the OS and possibly applications/configuration).
   - Free Tier eligible: Select AMIs marked as Free Tier eligible if applicable.
   - Categories: Quick Start / Marketplace / Community AMIs.

3. Instance Type

   - Determines hardware configuration: vCPU, RAM, network performance. Examples: `t3.micro`, `m5.large`.
   - Choose based on workload (CPU-bound, memory-bound, general purpose).

4. Key Pair (Login)

   - Key pair name: Choose an existing key pair or create a new one for SSH/RDP access.
   - Without a key pair you may have no secure way to connect.

5. Network Settings

   - VPC / Subnet: Choose the VPC and subnet for the instance.
   - Auto-assign Public IP: Controls whether the instance receives a public IPv4 address at launch.
   - Security Group(s): Virtual firewall rules for inbound/outbound traffic.
   - Network interfaces / secondary interfaces: Optional additional NICs.
   - Subnet type (public/private) determines accessibility and routing.

6. Configure Storage

   - Root volume + additional volumes: AMI defines base/root volume; you can add more (EBS volumes) or use instance store volumes.
   - Volume Type: `gp3`, `gp2`, `io1`, `io2`, `st1`, etc. Different performance/cost/IOPS.
   - Size (GiB): Specify size of each volume.
   - IOPS / Throughput: For certain volume types you may specify IOPS and throughput.
   - Delete on termination: Decide whether the volume is deleted when the instance is terminated.
   - Encryption: Enable and choose a KMS key if required.
   - Device name: e.g., `/dev/xvda`, `/dev/sdb`.

7. Advanced Details

   - IAM instance profile: The IAM role associated with the instance (permissions to access AWS APIs).
   - Shutdown behavior: Stop vs terminate on OS shutdown.
   - Enable termination protection: Prevent accidental termination.
   - Monitoring: Detailed CloudWatch monitoring (1-minute metrics) vs default (5-minute metrics).
   - Placement group: Cluster/partition/ spread placement for network performance or fault isolation.
   - Tenancy: Shared vs dedicated hosts/instances.
   - User data: Script or cloud-init that runs at launch for bootstrapping.
   - Network performance options: ENA, SR-IOV, etc.
   - Metadata options / IMDS settings: Configure instance metadata service version and access restrictions.
   - Tag specifications at launch: Tags for instance and attached resources.


## Linux Fundamentals

This guide covers three essential sections:

- Basic Linux Commands — `mkdir`, `cd`, `touch`, etc.
- File Management Commands — `ls`, `find`, etc.
- Process Management Commands — `ps`, `top`, etc.

Each section includes command explanation, syntax, examples, lab exercises, and expected outcomes.

### What is Linux?

Linux is an open-source operating system widely used in servers, cloud systems, DevOps pipelines, and automation environments.

### Linux Architecture

#### What is the Linux Kernel?

- It manages CPU, memory, devices, and filesystems.
- It provides system calls so applications can interact with hardware safely.
- It controls process scheduling, networking, and security.

Think of it as the “brain” of Linux, running in the background and making everything else work.

#### What is the Shell?

The shell is a command-line interpreter that lets you interact with the Linux kernel. Common shells:

- `bash` (default in most systems)
- `zsh`, `sh`, `fish`, etc.

Why Learn Shell Commands?

- Faster automation
- Foundation for DevOps scripting
- Easier debugging and system navigation


### 1. Basic Linux Commands

These are the foundational commands that help users create, navigate, and manage files/directories.

Commands covered: `pwd`, `cd`, `mkdir`, `rmdir`, `touch`, `cat`, `echo`, `clear`, `history`.

#### Command: `pwd`

- Purpose: Displays the current working directory.
- Syntax:

```bash
pwd
```

- Example:

```bash
[user@linux ~]$ pwd
/home/user
```

#### Command: `cd`

- Purpose: Change the current directory.
- Syntax:

```bash
cd [directory_path]
```

- Examples:

```bash
cd /home/user/Documents    # Move to Documents folder
cd /tmp/                  # Move to /tmp
cd ..                     # Move to parent directory
cd ~                      # Move to home directory
```

#### Command: `mkdir`

- Purpose: Create new directories.
- Syntax:

```bash
mkdir [directory_name]
```

- Examples:

```bash
mkdir projects
mkdir -p /home/user/code/python   # Create nested directories
```

#### Command: `rmdir`

- Purpose: Remove empty directories.
- Example:

```bash
rmdir old_folder
```

#### Command: `touch`

- Purpose: Create empty files or update timestamps.
- Example:

```bash
touch file1.txt
```

#### Command: `cat`

- Purpose: Display or concatenate file content.
- Examples:

```bash
cat file1.txt            # View file contents
cat file1.txt file2.txt  # View both files
```

#### Command: `echo`

- Purpose: Print text or write text into files.
- Examples:

```bash
echo "Hello, Linux!"           # Print text
echo "Linux Lab" > lab.txt     # Write to file
```

#### Command: `history`

- Purpose: Show list of previously executed commands.
- Example:

```bash
history
```

##### Lab Exercise 1 – Basic Commands

a. Create a directory named `starting_point`.
b. Navigate inside it.
c. Create a file called `secret_lair.txt`.
d. Display the absolute path of your current location.
e. View the history of commands executed.

Expected Outcome:

Students can create, navigate, and manipulate files/directories.


### 2. File Management Commands

These commands let you list, find, copy, move, or remove files efficiently.

Commands covered: `ls`, `cp`, `mv`, `rm`, `find`, `locate`, `du`, `df`.

#### Command: `ls`

- Purpose: List directory contents.
- Examples:

```bash
ls        # Basic list
ls -l     # Long list (permissions, owners, etc.)
ls -a     # Include hidden files
ls -lh    # Human-readable format
```

#### Command: `cp`

- Purpose: Copy files or directories.
- Examples:

```bash
cp file1.txt /backup/
cp -r folder1/ /backup/   # Copy directory recursively
```

#### Command: `mv`

- Purpose: Move or rename files.
- Examples:

```bash
mv file1.txt /tmp/
mv oldname.txt newname.txt
```

#### Command: `rm`

- Purpose: Remove files or directories.
- Examples:

```bash
rm file1.txt
rm -r temp_folder
```

Use with caution — no recycle bin!

#### Command: `find`

- Purpose: Search for files and directories.
- Examples:

```bash
find /home/user -name "*.log"     # Find all .log files
find . -type f -size +50M          # Find files > 50MB
```

#### Command: `du` and `df`

- Purpose: Display disk usage and free space.
- Examples:

```bash
du -sh /var/log/    # Total size of directory
df -h               # Disk space usage overview
```

##### Lab Exercise 2 – File Management

a. Check if `delete_directory` exists and remove it.
b. Delete `delete.txt`.
c. Use `find` to locate all `.txt` files under `/home/user`.
d. Display disk usage of `/var/log/`.

Expected Outcome:

Students understand file management operations, safe deletion, and disk space checking.


### 3. Process Management Commands

Processes represent running programs. Understanding how to list, monitor, and control them is vital for DevOps and system admins.

Commands covered: `ps`, `top`, `htop`, `kill`, `nice`, `renice`, `jobs`, `bg`, `fg`.

#### Command: `ps`

- Purpose: Display currently running processes.
- Examples:

```bash
ps            # Current user’s processes
ps -ef        # All processes in system
ps aux | grep nginx   # Find specific process
```

#### Command: `top`

- Purpose: Real-time system and process monitoring.
- Usage:

```bash
top
```

Press `q` to quit, `k` to kill, `r` to renice.

#### Command: `htop` (if installed)

- Purpose: Enhanced version of `top` with color and interactivity.
- Usage:

```bash
htop
apt install htop -y
```

#### Command: `kill`

- Purpose: Terminate a process using its PID.
- Examples:

```bash
sleep 100 &
kill 1234        # Send SIGTERM
kill -9 1234     # Forcefully terminate (SIGKILL)
```

#### Command: `jobs`, `bg`, `fg`

- Purpose: Manage background/foreground jobs.
- Examples:

```bash
sleep 100 &
jobs             # List background jobs
fg %1            # Bring job 1 to foreground
```

##### Lab Exercise 3 – Process Management

a. List all running processes.
b. Identify the process ID of `sshd`.
c. Start a background job using `sleep 60`.
d. Bring it to foreground using `fg`.
e. Kill a specific process using its PID.

Expected Outcome:

Students understand process listing, management, and termination safely.


### Extended Concept Practice: Permissions and Ownership

Key Commands:

- `chmod`, `chown`, `chgrp`

Examples:

```bash
chmod 755 file.sh
chown user:group file.sh
```

##### Lab Exercise 4 – Advanced Challenge

Work with the hidden directory `secret_lair`:

1. Count subdirectories (“rooms”).
2. Identify which one is permission-restricted.
3. Find command to change ownership to `user`.
4. Write all answers to `/home/user/answer.txt`.
