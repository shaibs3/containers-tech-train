# Hands-on Session: Exploring Linux Namespaces

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with network namespaces.
- Use the `unshare` command to create and interact with namespaces.

---

### Network Namespace

**Exercise**:

#### 1. Create a new network namespace
- Create the  namespace.
   ```bash
   sudo unshare --net --mount-proc /bin/bash
   ```
   **Explanation**:
    - `--net`: Creates a new network namespace.
    - `--mount-proc`: Mounts a new `/proc` filesystem inside the namespace.
    - `/bin/bash`: Starts a new shell inside the namespace.

- List the network interfaces available in the new namespace.
   ```bash
   ip a
   ```  
   **Question** Do you see the same network interfaces as on the host?

#### 2. Create network connectivity between the host and the new namespace
- Return to the host shell and create a veth pair:
   ```bash
   sudo ip link add veth-host type veth peer name veth-ns`
   ```

   **Explanation**:
    - `veth-host`: This will stay in the host network namespace.
    - `veth-ns`: This will be moved to the newly created namespace.

- Return to the new network namespace and get the PID of the shell:
   ```bash
   echo $$
   ```

- Return to the host shell and move `veth-ns` to the PID from step 4:
   ```bash
   sudo ip link set veth-ns netns <PID>
   ```
  **Question** Why do we need to use the PID to assign the interface to the namespace?

- Configure the network interfaces on the host.
   ```bash
   sudo ip addr add 192.168.15.1/24 dev veth-host
   sudo ip link set veth-host up
   ```
- Configure the network interfaces on the new network namespace.
   ```bash
     ip addr add 192.168.15.2/24 dev veth-ns
     ip link set veth-ns up
     ip link set lo up
   ```
- Test network connectivity from the new namespace:
   ```bash
   ping 192.168.15.1
   ```


<details>
  <summary>Bonus section. Connect the container to the internet</summary>

- Setup the host:
   - From the host:
      ```bash
      sudo sysctl -w net.ipv4.ip_forward=1
      sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
      ```
- Add default route in the new network namespace
   - From the new namespace:
      ```bash
      ip route add default via 192.168.15.1
     ```
2. Check connectivity from the new namespace:
    ```bash
    curl https://www.google.com
    ```

#### Cleanup

   ```bash
   exit
   sudo ip link delete veth-host
   # uncomment below if the bonus section was done
   #sudo sysctl -w net.ipv4.ip_forward=0
   #sudo iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
   ```


