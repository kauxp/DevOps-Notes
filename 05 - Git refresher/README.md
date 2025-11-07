# Git refresher

Short summary:

Core Git concepts and workflows for collaboration.

What you'll learn:

- Commits, branches, merges, and rebases
- Working with remote repositories and pull requests
- Common workflows: feature branching, trunk-based

Resources:

- Add links or notes here

Exercises:

- Practice merges, rebases, resolving conflicts

Notes:

- Add instructor notes or references here

## Git - Day 1

## The Story of Git and GitHub — The Code Odyssey

### Chapter 1: The Chaos Before Version Control

In the bustling city of Codeville, a group of developers—Maya, Leo, and Sam—were building an amazing new app. Every day, they shared files over email: "Here’s app_final_v3_really_final_final.py." Soon, no one knew which file was the latest version. Sometimes, one developer’s fix overwrote another’s feature. It was chaos.

Then one day, an old engineer named Linus walked in and said: "You need Version Control — a way to track every change to your code, forever."

### Chapter 2: What is SCM?

SCM stands for Source Code Management — it helps teams collaborate on code safely.

Repository Types:

Type | Where the Code Lives | Example
--- | --- | ---
Local SCM | Only on your computer | RCS (old tools)
Remote SCM | Central server everyone connects to | Subversion (SVN)
Distributed SCM | Each developer has the full copy of history | Git

"Git," said Linus, "is distributed — everyone has a full history of the project. You can work even offline."

### Comparison of Repository Types

Aspect | Local Repositories | Remote Repositories | Distributed Repositories
--- | --- | --- | ---
Collaboration | No collaboration — only accessible to one user/machine | Enables team collaboration via a central server | Full collaboration — every user has a complete copy of the repo
Community & Ecosystem Support | Lacks ecosystem integration | Integrated with platforms like GitHub, GitLab, Bitbucket | Same as remote, plus offline flexibility
Storage Requirements | High disk usage (full history on one system) | Centralized storage | Each user stores full but manageable copies
Availability & Reliability | Reduced — data loss if local machine fails | Dependent on remote server uptime | Highly available — each copy acts as a backup
Performance | Very fast (local operations) | Slower — network latency | Fast for local operations; syncs when needed
Network Dependency | None — fully offline | Network required for all operations | Works offline; syncs when needed
Backup & Recovery | Manual backups required | Centralized backups needed | Built-in redundancy (each clone is a backup)

### Chapter 3: Installing Git — The Developer’s Toolkit

For CentOS/RedHat (yum):

```bash
sudo su
yum update
yum install git
```

For Debian/Ubuntu (apt):

```bash
apt update -y
apt install git
```

Verify installation:

```bash
git --version
```

### Chapter 4: Configuring Git — The Identity Scroll

Set identity:

```bash
git config --global user.name "Maya Codeheart"
git config --global user.email "maya@codeville.dev"
git config --global core.editor "vim"
```

### Chapter 5: SSH Setup

Generate SSH key:

```bash
ssh-keygen -t rsa -b 4096 -C "maya@codeville.dev"
```

Copy the public key and add to GitHub.

### Chapter 6: GitHub — The Kingdom of Collaboration

Create an account on [https://github.com](https://github.com) and create a repository (e.g., `demo-project`).

### Chapter 7: Starting the Project

```bash
mkdir demo
cd demo
ls -a
git init
ls -a
```

### Chapter 8: The Four Kingdoms of Git

Area | Also Called | Description | Example Command
--- | --- | --- | ---
Working Tree | Workspace | Where you edit files | `git status`
Staging Area | Index / cache | Prepare files for commit | `git add file.txt`
Local Repository | `.git` dir | Stores commits and history | `git commit`
Remote Repository | Origin | Hosted copy on GitHub | `git push`

### Chapter 9: Creating & Tracking Files

```bash
touch Abc.txt
touch Xyz.txt
git status
git add Abc.txt
git status
```

### Chapter 10: The First Commit

```bash
git commit -m "Learn git commit"
```

### Chapter 11: The Great Push — Connecting to GitHub

```bash
git remote add origin git@github.com:maya-codeheart/demo-project.git
git push origin main
```

### Chapter 12: Exploring & Comparing

- `git diff` — shows changes in working directory vs last commit.
- `git add .` — stages changes.
- `git diff --cached` — shows staged changes vs last commit.

### Chapter 13: The Scrolls of Knowledge

- `man git` — full manual.
- `git add -h` — quick help.

### Chapter 14: Cloning and Collaboration

```bash
git clone git@github.com:maya-codeheart/demo-project.git
```

### Chapter 15: The Circle of Git Life

Working Tree → `git add` → Staging Area → `git commit` → Local Repo → `git push` → Remote Repo → `git pull`

### Quick Recap: Git Adventure Map

Stage | Command | Purpose
--- | --- | ---
Install Git | `apt-get install git` | Install Git
Configure Identity | `git config --global user.name "Name"` | Set username/email
Start Project | `git init` | Create a new local repo
Track Files | `git add file.txt` | Stage files
Commit Changes | `git commit -m "msg"` | Save snapshot locally
Connect Remote | `git remote add origin <url>` | Link to GitHub
Push Changes | `git push origin main` | Upload commits
Clone Repo | `git clone <url>` | Copy an existing repo
Compare Changes | `git diff` `git diff --cached` | View differences
Help | `man git` `git -h` | Get documentation

### Git and GitHub — Practical Commands & Workflows

(Here we list common commands; keep for quick reference.)

#### Config & Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

#### Basic Workflow

```bash
mkdir demo
cd demo
git init
touch Abc.txt
git add Abc.txt
git commit -m "first commit"
```

#### Diff & Status

```bash
git status
git diff
git diff --staged
git log --oneline --graph --decorate --all
```

#### Branching & Switching

```bash
git branch
git branch feature/x
git switch feature/x
git switch -c feature/y
git branch -d feature/old
git branch -D feature/old
```

#### Merging & Rebasing

```bash
git merge feature/x
git merge --no-ff feature/x
git rebase master
git rebase -i HEAD~5
git rebase --abort
git rebase --continue
```

#### Remote Operations

```bash
git remote -v
git remote add origin git@github.com:user/repo.git
git fetch origin
git pull
git push origin feature/x
git push -u origin feature/x
git push --force-with-lease origin feature/x
git push --tags
```

#### Stashing

```bash
git stash
git stash push -m "WIP"
git stash list
git stash show -p stash@{0}
git stash apply stash@{0}
git stash pop
git stash drop stash@{0}
```

#### Undoing & Rewriting History

```bash
git revert <commit>
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
git reflog
git cherry-pick <commit>
```

#### Searching & Selecting Commits/Files

```bash
git grep "TODO"
git log --grep="fix memory leak"
git log -S"functionName"
```

#### Hooks

Explain hooks and show example of making pre-commit executable:

```bash
chmod +x .git/hooks/pre-commit
```

#### Ignore files & attributes

```bash
echo "node_modules/" >> .gitignore
echo "*.log" >> .gitignore
git status --ignored
git check-ignore -v path/to/file
```

#### Large files & LFS

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

#### Safety & Best Practices

```bash
git fetch --all --prune
git branch --merged
git branch --no-merged
git remote show origin
git config pull.rebase true
```

### Example Workflows

#### Feature Branch Workflow

```bash
git switch -c feature/awesome
# make changes
git add .
git commit -m "Implement awesome feature"
git push -u origin feature/awesome
```

#### Rebase workflow before merging

```bash
git fetch origin
git rebase origin/main
git push --force-with-lease origin feature/awesome
```

#### Merge into main

```bash
git checkout main
git pull origin main
git merge --no-ff feature/awesome
git push origin main
```

### Config Inspection

```bash
git config --list
git config --global user.name
```

I added the Git Day1 content to `05 - Git refresher/README.md`. Would you like me to create example scripts or a small practice repo inside the workspace? If so, tell me which items to create (e.g., a sample repo, .gitignore, pre-commit hook). Otherwise, next I can add diagrams or exercises. What would you like next?
