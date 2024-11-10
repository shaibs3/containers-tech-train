# Download the Alpine Linux miniroot filesystem
wget https://dl-cdn.alpinelinux.org/alpine/v3.18/releases/x86_64/alpine-minirootfs-3.18.4-x86_64.tar.gz

# Create a directory to hold the root filesystem
mkdir alpine-rootfs

# Extract the Alpine filesystem into the new directory
tar -xzf alpine-minirootfs-3.18.4-x86_64.tar.gz -C alpine-rootfs

# Use `unshare` to create a new namespace environment and enter it
sudo unshare --mount --uts --pid --fork --net --user --ipc --mount-proc /bin/bash

# Inside the unshared environment, mount the root filesystem and use `chroot`
mount --bind alpine-rootfs alpine-rootfs
chroot alpine-rootfs /bin/sh


