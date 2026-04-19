
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
