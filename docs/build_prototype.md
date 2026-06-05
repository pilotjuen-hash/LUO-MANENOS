# Building the Luo Manenos Prototype

This document outlines the steps to build a minimal, bootable Luo Manenos prototype image using Buildroot. The prototype will be a command-line interface (CLI) based system, focused on demonstrating the core kernel modifications and a basic environment.

## Prerequisites

Before you begin, ensure your system has the necessary build tools. For Debian/Ubuntu-based systems, these can be installed with:

```bash
sudo apt-get update -y
sudo apt-get install -y build-essential git libncurses-dev rsync cpio unzip bc file wget flex bison libssl-dev qemu-system-x86 syslinux-utils
```

## Project Setup

1.  **Create Project Directory:**

    ```bash
mkdir -p /home/ubuntu/luo-manenos
cd /home/ubuntu/luo-manenos
    ```

2.  **Initialize Subdirectories:**

    ```bash
mkdir -p buildroot kernel-patches configs rootfs-overlay/etc scripts docs
    ```

3.  **Prepare Root Filesystem Overlay:**

    These files will be copied into the root filesystem of the built OS image.

    ```bash
printf 'Luo Manenos 0.1 (Privacy & Gaming)\n' > rootfs-overlay/etc/issue
printf '#!/bin/sh\necho "Welcome to Luo Manenos - Your Privacy, Your Games."\n' > rootfs-overlay/etc/profile
chmod +x rootfs-overlay/etc/profile
    ```

## Buildroot Integration

1.  **Download Buildroot:**

    ```bash
cd /home/ubuntu/luo-manenos
wget -q https://buildroot.org/downloads/buildroot-2024.02.12.tar.gz
tar -xzf buildroot-2024.02.12.tar.gz
mv buildroot-2024.02.12 buildroot/src
    ```

2.  **Configure Buildroot for Luo Manenos:**

    This step configures Buildroot to build an x86_64 image compatible with QEMU, includes BusyBox for basic utilities, and enables Dropbear for SSH access (for future experimental use). It also points to our custom root filesystem overlay.

    ```bash
cd /home/ubuntu/luo-manenos/buildroot/src
make qemu_x86_64_defconfig
./utils/config --set-str BR2_TARGET_GENERIC_HOSTNAME "luo-manenos"
./utils/config --set-str BR2_TARGET_GENERIC_ISSUE "Welcome to Luo Manenos 0.1"
./utils/config --set-str BR2_ROOTFS_OVERLAY "/home/ubuntu/luo-manenos/rootfs-overlay"
./utils/config --enable BR2_PACKAGE_BUSYBOX
./utils/config --enable BR2_PACKAGE_DROPBEAR
make olddefconfig
cp .config /home/ubuntu/luo-manenos/configs/luo_manenos_x86_64_buildroot.config
    ```

3.  **Apply Kernel Patches (Placeholder):**

    This is where custom kernel patches for gaming (e.g., Zen/Liquorix) and privacy (e.g., `linux-hardened` features) would be applied. For a prototype, these would typically be placed in `kernel-patches/` and referenced in the Buildroot configuration.

    *Example (conceptual, actual patch application depends on specific patches and Buildroot version):*

    ```bash
# cd /home/ubuntu/luo-manenos/buildroot/src
# echo "BR2_LINUX_KERNEL_PATCH = \"/home/ubuntu/luo-manenos/kernel-patches/gaming.patch /home/ubuntu/luo-manenos/kernel-patches/privacy.patch\"" >> .config
# make olddefconfig
    ```

## Building the Image

From the `buildroot/src` directory:

```bash
make
```

This command will download all necessary source code, compile the cross-compilation toolchain, the Linux kernel, BusyBox, and other selected packages, and finally create the root filesystem and a bootable image.

## Testing the Image

Once the build is complete, the bootable image (e.g., `bzImage` and `rootfs.ext2`) will be located in `output/images/` within the Buildroot source directory. You can test it using QEMU:

```bash
cd /home/ubuntu/luo-manenos/buildroot/src
qemu-system-x86_64 -M pc -kernel output/images/bzImage -append "root=/dev/sda console=ttyS0" -hda output/images/rootfs.ext2 -nographic
```

This will boot the Luo Manenos prototype in a QEMU virtual machine, displaying output on the console. You should see the custom welcome message and be presented with a login prompt (root with no password by default in Buildroot minimal images, or as configured).
