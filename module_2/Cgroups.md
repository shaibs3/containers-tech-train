# Hands-on Session: Exploring Container Filesystems

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process, user, and network namespaces.
- Use the `unshare` command to create and interact with namespaces.

---

### Creating a Cgroup and assign it to a process

**Goal**:  
Isolate processes to create an independent view of the process tree.

**Exercise**:
1. Create a cgroup:
   ```bash
   sudo mkdir /sys/fs/cgroup/cpu/my_cgroup
   ```
2. Get the current shell PID:
   ```bash
   export CURRENT_PROCESS=`echo $$`
   ```
3. Add the current process to my_cgroup:
   ```bash
   sudo sh -c "echo ${CURRENT_PROCESS} > /sys/fs/cgroup/cpu/my_cgroup/cgroup.procs"
   ```
4. Verify the current process has been added to my_cgroup:
   ```bash
   cat /sys/fs/cgroup/cpu/my_cgroup/cgroup.procs
   ```
### 2. Limiting CPU usage
Goal:
Limit CPU usage for a process to demonstrate cgroup resource control.

**Exercise**:

1. Set the CPU quota (e.g., limit to 20% of a CPU core):
    ```bash
    sudo sh -c "echo 20000 > /sys/fs/cgroup/cpu/my_cgroup/cpu.cfs_quota_us"
    sudo sh -c "echo 100000 > /sys/fs/cgroup/cpu/my_cgroup/cpu.cfs_period_us"
    ```
2. Run a CPU-intensive task:
    ```bash
    stress --cpu 1 --timeout 360 &
    ```
3. While the task is running, check the CPU usage of the task and verify it is about 20%:
    ```bash
    top -p $(ps --ppid $(pgrep -f stress) | awk '{print $1}' | tail -n 1)
    ```
### 3. Limiting Memory usage
Goal:
Limit CPU usage for a process to demonstrate cgroup resource control.

**Exercise**:

1. Create a new cgroup for memory:
    ```bash
    sudo mkdir /sys/fs/cgroup/memory/my_memory_cgroup
    ```
2. Set a memory limit:
    ```bash
    sudo sh -c "echo 100M > /sys/fs/cgroup/memory/my_memory_cgroup/memory.limit_in_bytes"
    ```
3. Add the current process to the memory cgroup:
    ```bash
    sudo sh -c "echo ${CURRENT_PROCESS} > /sys/fs/cgroup/memory/my_memory_cgroup/cgroup.procs"
    ```
4. Run a memory stress test and verify the program runs successfully:
    ```bash
    stress --vm 1 --vm-bytes 100M --timeout 2
    ```
5. Run a memory stress test and verify the program fails:
    ```bash
    stress --vm 1 --vm-bytes 300M --timeout 2
    ```
