# Hands-on Session: Exploring Network Namespace

## Objectives:
- Gain practical experience working with network namespaces.

---

### Network Namespace

**Exercise**:

#### 1. Create a new network namespace
1. Create the  namespace.
   ```bash
   sudo unshare --net --mount-proc bash
   ```
   **Explanation**:
    - `--net`: Creates a new network namespace.
    - `--mount-proc`: Mounts a new `/proc` filesystem inside the namespace.
    - `bash`: Starts a new shell inside the namespace.

2. List the network interfaces available in the new namespace.
   ```bash
   ip a
   ```  
   **Question** Do you see the same network interfaces as on the host?

#### 2. Create network connectivity between the host and the new namespace
1. **From the host shell** Create a veth pair:
   ```bash
   sudo ip link add veth-host type veth peer name veth-ns
   ```

   **Explanation**:
    - `veth-host`: This will stay in the host network namespace.
    - `veth-ns`: This will be moved to the newly created namespace.

2. **From the new network namesapce** Get the PID of the shell:
   ```bash
   echo $$
   ```

3. **From the host shell** Move `veth-ns` to the PID from step 4:
   ```bash
   sudo ip link set veth-ns netns <PID>
   ```
   **Question** Why do we need to use the PID to assign the interface to the namespace?

4. **From the host shell** Configure the network interfaces.
   ```bash
   sudo ip addr add 192.168.15.1/24 dev veth-host
   sudo ip link set veth-host up
   ```
5. **From the new network namesapce** Configure the network interfaces.
   ```bash
     # Rename the veth-ns interface to eth0
     ip link set veth-ns name eth0
     # Assign ip address
     ip addr add 192.168.15.2/24 dev eth0
     ip link set eth0 up
     ip link set lo up
   ```
- Test network connectivity from the new namespace:
   ```bash
   ping 192.168.15.1
   ```


<details>
  <summary>Bonus section. Connect the container to the internet</summary>

- Setup the host:
   - **From the host shell**:
      ```bash
      sudo sysctl -w net.ipv4.ip_forward=1
      sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
      ```
- Add default route in the new network namespace
   - **From the new network namesapce**:
      ```bash
      ip route add default via 192.168.15.1
     ```
2. **From the new network namesapce**Check connectivity to the internet:
    ```bash
    curl https://www.google.com
    ```
</details>

#### Cleanup

   ```bash
   exit
   sudo ip link delete veth-host
   # uncomment below if the bonus section was done
   #sudo sysctl -w net.ipv4.ip_forward=0
   #sudo iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
   ```


