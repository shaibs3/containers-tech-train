# Hands-on Session: Exploring Linux Namespaces

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process namespace.
- Use the `unshare` command to create and interact with namespaces.

---

### Process Namespace

**Goal**:  
Isolate processes to create an independent view of the process tree.

**Exercise**:
1. View the processes running on the host.
   ```bash
   ps aux
   ```
2. Create a new process namespace.
   ```bash
   sudo unshare --fork --pid --mount-proc bash
   ```
**Explanation**:
- `--pid`: Creates a new process namespace.
- `--fork`: Forks a new process to start the namespace.
- `--mount-proc`: Remounts `/proc` to reflect the new namespace.
3. View the running processes on the new namespace.
   ```bash
   ps aux
   ```
4. Verify that the bash process has PID (Process ID) 1.
5. Verify that no host processes listed in step 1 are visible.
6. Run a new process in the new process namespace.
   ```bash
   sleep 360 &
   ```
7. Verify the new sleep process is a child process of the bash process.
   ```bash
   pstree -tpln
   ```
8. Open a new terminal and run -
   ```bash
   pstree -tpln
   ```
9. Verify that you see both bash and sleep processes in the original process namespace.
10. Exit the new namespace.
   ```bash
   exit
   ```