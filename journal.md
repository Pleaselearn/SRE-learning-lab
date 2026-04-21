
# Day 1 - Linux Deep Dive

**What I did:**
- Used strace to see system calls of ls command
- Explored /proc filesystem
- Read /proc/meminfo - saw 8GB RAM, 1.2GB free
- Used lsof to see open file descriptors
- Used htop to see running processes

**What I learned:**
- Every program talks to kernel through system calls
- /proc is not real files - it's kernel state
- File descriptors: 0=stdin, 1=stdout, 2=stderr
- Everything in Linux is a file
- Every process has PID and PPid (parent)

**Command that surprised me:**
strace ls - I didn't know ls makes 50+ system calls just to list files

## Day 2 - Processes and Signals

**What I did:**
- Learned how fork() creates child processes
- Wrote a Python service that handles SIGTERM gracefully
- Tested SIGTERM vs SIGKILL with kill command

**What I learned:**
- fork() creates an exact copy of the parent process
- exec() replaces the child with a new program
- SIGTERM (kill -15) = graceful shutdown, service can clean up
- SIGKILL (kill -9) = instant death, no cleanup possible
- Always try SIGTERM first, only use SIGKILL if stuck

**Real world connection:**
- Kubernetes sends SIGTERM to stop pods gracefully
- If service doesn't respond in 30 seconds, Kubernetes sends SIGKILL
- Every production service must handle SIGTERM

**Command that mattered today:**
kill -15 <PID> → graceful
kill -9 <PID> → forceful

**Zombie processes:**
- Child finishes but parent doesn't call wait()
- Shows as Z/defunct in ps aux
- Fix: fix the code OR kill the parent
- In production: zombies consume PID slots, 
  eventually new processes can't start

## Day 3 - File Descriptors and lsof

**What I did:**
- Checked FD limit with ulimit -n (1024)
- Wrote a Python script that leaks file descriptors
- Watched it fail at 1021 FDs with "Too many open files"
- Script consumed 6GB RAM before getting killed
- Killed the leaking process with kill -9

**What I learned:**
- Every open file/socket/connection = 1 file descriptor
- Default limit per process = 1024
- FD leak = never closing files after opening
- Fix: always use "with open()" in Python
- lsof -p <PID> shows all open files for a process

**Commands learned:**
ulimit -n → check FD limit
lsof -p <PID> → list open files
lsof -p <PID> | wc -l → count open FDs
kill -9 $(pgrep -f <name>) → kill by name
free -h → check memory

**Real world connection:**
High traffic services need ulimit increased to 65536.
Set in systemd with LimitNOFILE=65536.

## Day 4 — strace

What I tried: strace curl, strace cat on missing file, attach to sleep process
What broke: sudo not configured, $! was empty when sleep finished
What I believed wrong: thought wsl --user would work inside WSL terminal
What the actual answer was: run wsl -u root from Windows PowerShell
Fix: passwd mohan from PowerShell root shell

Key learnings:
- execve is always the first syscall — loads the program
- openat = -1 ENOENT means file not found
- openat = 3 means success, 3 is the file descriptor
- EACCES = permission denied
- clock_nanosleep is what sleep blocks on
- strace -p attaches to a running process by PID
