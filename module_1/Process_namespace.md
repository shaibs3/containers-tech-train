# Hands-on Session: Exploring Process Namespace

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process namespace.
- Use the `unshare` command to create and interact with namespaces.

---

### Process Namespace

#### 1. Create a new network namespace

1. View the processes running on the host.
   ```bash
   ps aux
   ```
2. Create a new process namespace.
   ```bash
   sudo unshare --fork --pid --mount-proc bash
   ```
   **Arguments Explanation**:
  - `--pid`: Creates a new process namespace.
  - `--fork`: Forks a new process to start the namespace.
  - `--mount-proc`: Remounts `/proc` to reflect the new namespace.
  - `bash`: Spawns a new shell process inside the namespace.

3. **Question** - What processes do you expect to see in the new namespace?
4. **Question** - What is the PID of the bash process in the new namespace?
5. **Question** - Are there any host processes listed in step 1 in the new namespace?

#### 2. Observe a new child process in the new namespace

1. Run a new process in the new process namespace.
   ```bash
   sleep 360 &
   ```
2. **Question** - What do you think will be the relationship between the bash process that created the namespace and the new process created in step 5?
   - To view the process tree and see the processes relationship type the following:
      ```bash
      pstree -tpln
      ```
3. **Question** - Is the process created in step 6 visible on the host? 
   - To answer the question, Open a new ubuntu terminal and type the following:
      ```bash
      pstree -tpln
      ```
#### Cleanup
1. type:
   ```bash
   exit
   ```