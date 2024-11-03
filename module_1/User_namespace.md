# Hands-on Session: Exploring Linux Namespaces

## Objectives:
- Understand how namespaces isolate different aspects of the system.
- Gain practical experience working with process, user, and network namespaces.
- Use the `unshare` command to create and interact with namespaces.

---

### User Namespace

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
2. Create a new user namespace.
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