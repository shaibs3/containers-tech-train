# Hands-on Session: Exploring Linux Namespaces

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process, user, and network namespaces.
- Use the `unshare` command to create and interact with namespaces.

---

### 1. Process Namespace

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
---

### 2. User Namespace

**Goal**:  
Create a new user namespace, where the user ID inside the new namespace is mapped to various users on the host.

#### Exercise 1:
1. Check the current UID and GID.
   ```bash
   id
   ```
   You should see output similar to `uid=1000(yourusername) gid=1000(yourgroup) groups=1000(yourgroup)`.

2. Create a new user namespace.
  ```bash
  sudo unshare --user --mount --map-root-user bash
  ```

    **Explanation**:
  - `--user`: Unshares the user namespace.
  - `--mount`: Unshares the mount namespace to ensure isolation.
  - `--map-root-user`: Maps your user ID (UID 1000) to the root user (UID 0) inside the namespace.
  - `bash`: Spawns a new shell inside the namespace.

3. Inside the new shell, check the current UID and GID.
  ```bash
  id
  ```
  You should see output similar to `uid=0(root) gid=0(root) groups=0(root)`.
4. Experiment with privileged operations.
  ```bash
  mkdir /test-directory
  ```
5. Open a new terminal and run-
   ```bash
    ls /test-directory
   ```
6. Verify that the directory exists on the host.

    **Question**:  
    Why did the new user namespace manage to write to the host filesystem?

7. Exit the namespace.
   ```bash 
   exit
   ```

#### Exercise 2:
1. Check the current UID and GID.
  ```bash
  id
  ```
2. Create a new user namespace:
  ```bash 
  unshare --user --map-user=0 bash
  ```

    **Explanation**:
  - `--user`: Unshares the user namespace.
  - `--map-user=0`: Maps your user ID (UID 1000) to the root user (UID 0) inside the namespace.
  - `bash`: Spawns a new shell inside the namespace.

3. Inside the new shell, check the current UID and GID:
  ```bash
  id
  ```  
  You should see output similar to `uid=0(root) gid=0(root) groups=0(root)`.
4. Experiment with privileged operations:
  ```bash
  mkdir /test-directory
  ```
5. Verify that the command fails.

    **Question**:  
Why did the command fail?

6. Exit the namespace.
   ```
   bash exit
   ```
---

### 3. Network Namespace

**Goal**:  
Isolate network interfaces, allowing for independent network configurations inside the namespace.

**Exercise**:
1. Create a new network namespace.
   ```bash
   sudo unshare --net --mount-proc /bin/bash
   ```

    **Explanation**:
   - `--net`: Creates a new network namespace.
   - `--mount-proc`: Mounts a new `/proc` filesystem inside the namespace.
   - `/bin/bash`: Starts a new shell inside the namespace.

2. Verify you are in the new network namespace.
   ```bash
   ip a
   ```  
   You should only see the `lo` interface.

3. Return to the host shell and create a veth pair:
   ```bash
   sudo ip link add veth-host type veth peer name veth-ns`
   ```

    **Explanation**:
   - `veth-host`: This will stay in the host network namespace.
   - `veth-ns`: This will be moved to the newly created namespace.

4. Return to the new network namespace and get the PID of the shell:
   ```bash
   echo $$
   ```

5. Return to the host shell and move `veth-ns` to the PID from step 4:
   ```bash
   sudo ip link set veth-ns netns <PID>
   ```

6. Configure the network interfaces on the host.
   ```bash
   sudo ip addr add 192.168.15.1/24 dev veth-host
   sudo ip link set veth-host up
   ```
7. Configure the network interfaces on the new network namespace.
   ```bash
     ip addr add 192.168.15.2/24 dev veth-ns
     ip link set veth-ns up
     ip link set lo up
   ```
8. Test network connectivity from the new namespace:
   ```bash
   ping 192.168.15.1
   ```

9. Connect the container to the internet.

    Enable IP forwarding.From the host, run-
    ```bash
    sudo sysctl -w net.ipv4.ip_forward=1
    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    ```
    From the new namespace, run-
    ```bash
    ip route add default via 192.168.15.1
    ```
10. Check connectivity from the new namespace:
    ```bash
    curl https://www.google.com
    ```

