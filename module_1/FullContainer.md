1. Run as root
   ```bash
   sudo su
   ```
2. Download the Alpine Linux miniroot filesystem
   ```bash
   cd /
   wget https://dl-cdn.alpinelinux.org/alpine/v3.18/releases/x86_64/alpine-minirootfs-3.18.4-x86_64.tar.gz
   ```
3. Create a directory to hold the root filesystem
   ```bash
   mkdir /alpine-rootfs
   ```

4. Extract the Alpine filesystem into the new directory
   ```bash
   tar -xzf alpine-minirootfs-3.18.4-x86_64.tar.gz -C /alpine-rootfs
   ```
5. Use `unshare` to create a new namespace environment and enter it
   ```bash
   sudo unshare --mount --uts --pid --fork --net --user --map-root-user --ipc --mount-proc /bin/bash
   ```
6. **Question** On which operating system are you running on now? find the linux cli command to find the operating system you are running on.

7. Inside the unshared environment, mount the root filesystem and use `chroot` to checnge the root filesystem
   ```bash
   mount --bind /alpine-rootfs alpine-rootfs
   chroot alpine-rootfs /bin/sh
   ```
8. **Question** On which operating system are you running on now? Use the same command you used in section 6.
9. **Question** What network devices do you see? Can you ping google.com? if not, why?
10. **Question** Is there any security concern in terms of user namespace in the new container?
11. **Question** If you were to create a second identical container by running the same commands, what major security issue may occur?




