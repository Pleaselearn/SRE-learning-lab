## Day 1 - Linux Deep Dive

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

