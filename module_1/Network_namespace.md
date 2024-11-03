# Hands-on Session: Exploring Linux Namespaces

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process, user, and network namespaces.
- Use the `unshare` command to create and interact with namespaces.

---

### Network Namespace

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

