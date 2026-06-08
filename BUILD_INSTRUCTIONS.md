# Luo Manenos: Complete Build & Installation Guide

## Overview

**Luo Manenos** is a privacy-first gaming operating system built on a hardened Linux kernel with gaming optimizations. This guide provides step-by-step instructions to build, verify, and deploy Luo Manenos from source.

---

## System Requirements

### Build Environment
- **OS**: Linux (Ubuntu 20.04 LTS or later recommended)
- **CPU**: Multi-core processor (6+ cores recommended for faster builds)
- **RAM**: 8 GB minimum (16 GB recommended)
- **Disk Space**: 50 GB free space (for build artifacts and output)
- **Build Tools**: GCC, Make, Git, Wget, Perl

### Target System
- **Architecture**: x86_64 (AMD64)
- **Boot**: UEFI or BIOS compatible
- **RAM**: 2 GB minimum (4 GB recommended for gaming)
- **Storage**: 20 GB minimum (SSD recommended for performance)

---

## Prerequisites Installation

### Ubuntu/Debian Systems

```bash
sudo apt-get update
sudo apt-get install -y \
  build-essential \
  git \
  wget \
  curl \
  bc \
  cpio \
  unzip \
  rsync \
  libncurses-dev \
  flex \
  bison \
  perl \
  libssl-dev \
  libelf-dev \
  python3 \
  python3-dev
```

### Fedora/RHEL Systems

```bash
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y \
  git \
  wget \
  curl \
  bc \
  cpio \
  unzip \
  rsync \
  ncurses-devel \
  flex \
  bison \
  perl \
  openssl-devel \
  elfutils-libelf-devel \
  python3 \
  python3-devel
```

---

## Building Luo Manenos

### Step 1: Clone the Repository

```bash
git clone https://github.com/pilotjuen-hash/LUO-MANENOS.git
cd LUO-MANENOS
```

### Step 2: Download Buildroot

```bash
# Create build directory
mkdir -p buildroot/src
cd buildroot/src

# Download Buildroot 2024.02.12 (stable release)
wget https://buildroot.org/downloads/buildroot-2024.02.12.tar.gz
tar -xzf buildroot-2024.02.12.tar.gz
cd buildroot-2024.02.12
```

### Step 3: Configure Buildroot for Luo Manenos

```bash
# Copy Luo Manenos configuration
cp ../../configs/buildroot.config .config

# Launch configuration menu (optional - for customization)
make menuconfig
```

**Key Configuration Options:**
- **Target Architecture**: x86_64
- **Target Options**: x86-64 (generic)
- **Kernel**: Linux 6.1.44 (or latest stable)
- **Kernel Configuration**: Custom (with gaming and privacy patches)
- **Bootloader**: GRUB 2
- **Filesystem**: ext4 (default) or Btrfs (advanced)
- **System Configuration**: Hostname: `luo-manenos`

### Step 4: Apply Kernel Patches

```bash
# Navigate to kernel source
cd output/build/linux-6.1.44/

# Apply gaming optimization patches (Zen/Liquorix)
patch -p1 < ../../../patches/zen-gaming-patches.patch

# Apply privacy hardening patches (linux-hardened)
patch -p1 < ../../../patches/linux-hardened-patches.patch

# Return to buildroot directory
cd ../../../
```

### Step 5: Configure Kernel

```bash
# Launch kernel configuration menu
make linux-menuconfig

# Key kernel settings for Luo Manenos:
# - CONFIG_HZ=1000 (1000Hz tick rate for gaming responsiveness)
# - CONFIG_SCHED_PDS=y (PDS scheduler for superior gaming performance)
# - CONFIG_HARDENED_USERCOPY=y (Privacy hardening)
# - CONFIG_SMACK=y (Mandatory Access Control)
# - CONFIG_FORTIFY_SOURCE=y (Buffer overflow protection)
# - CONFIG_STACKPROTECTOR_STRONG=y (Stack canary protection)
```

### Step 6: Build the System

```bash
# Start the build process (this will take 1-3 hours depending on hardware)
make -j$(nproc)

# Monitor build progress
tail -f build.log
```

**Build Stages:**
1. Host tools compilation (gcc, binutils, etc.)
2. Cross-compiler setup
3. C library (glibc) compilation
4. Linux kernel compilation (with patches)
5. BusyBox and core utilities
6. Wayland display server
7. Root filesystem creation
8. Bootloader (GRUB) setup
9. Final image generation

### Step 7: Verify Build Output

```bash
# Check for generated images
ls -lh output/images/

# Expected output files:
# - bzImage (Linux kernel)
# - rootfs.ext4 (Root filesystem)
# - rootfs.tar (Filesystem archive)
# - grub.cfg (Bootloader configuration)
```

---

## Creating Bootable Media

### Method 1: USB Drive (Recommended for Testing)

```bash
# Identify USB device (e.g., /dev/sdb)
lsblk

# Write image to USB (replace sdX with your device)
sudo dd if=output/images/rootfs.ext4 of=/dev/sdX bs=4M status=progress
sudo sync

# Eject USB safely
sudo eject /dev/sdX
```

### Method 2: Virtual Machine (QEMU)

```bash
# Install QEMU
sudo apt-get install -y qemu-system-x86

# Create virtual disk
qemu-img create -f qcow2 luo-manenos.qcow2 20G

# Boot Luo Manenos in QEMU
qemu-system-x86_64 \
  -m 2048 \
  -smp 4 \
  -hda luo-manenos.qcow2 \
  -kernel output/images/bzImage \
  -append "root=/dev/sda1 rw" \
  -enable-kvm
```

### Method 3: Disk Image (Full Installation)

```bash
# Create disk image with partition table
dd if=/dev/zero of=luo-manenos.img bs=1M count=20480

# Create partition
parted -s luo-manenos.img mklabel gpt
parted -s luo-manenos.img mkpart primary ext4 1MiB 100%

# Mount and install
sudo losetup -f luo-manenos.img
sudo mkfs.ext4 /dev/loop0p1
sudo mount /dev/loop0p1 /mnt
sudo tar -xf output/images/rootfs.tar -C /mnt
sudo umount /mnt
```

---

## Installation on Physical Hardware

### Step 1: Boot from USB

1. Insert Luo Manenos USB drive into target system
2. Reboot and enter BIOS/UEFI (typically F2, DEL, or ESC during startup)
3. Set boot priority to USB device
4. Save and exit BIOS
5. System will boot into Luo Manenos live environment

### Step 2: Partition Target Disk

```bash
# List available disks
lsblk

# Create partitions (example for /dev/sda)
sudo fdisk /dev/sda
# Create EFI partition: +512M (type EF00)
# Create root partition: remaining space (type 8300)

# Format partitions
sudo mkfs.fat -F 32 /dev/sda1
sudo mkfs.ext4 /dev/sda2
```

### Step 3: Install Luo Manenos

```bash
# Mount partitions
sudo mount /dev/sda2 /mnt
sudo mkdir -p /mnt/boot/efi
sudo mount /dev/sda1 /mnt/boot/efi

# Copy filesystem
sudo tar -xf /path/to/rootfs.tar -C /mnt

# Install bootloader
sudo grub-install --target=x86_64-efi --efi-directory=/mnt/boot/efi --bootloader-id=luo-manenos /dev/sda

# Generate GRUB configuration
sudo grub-mkconfig -o /mnt/boot/grub/grub.cfg

# Unmount
sudo umount -R /mnt
```

### Step 4: First Boot

1. Remove USB drive
2. Reboot system
3. Select "Luo Manenos" from GRUB menu
4. System will boot into Luo Manenos

---

## Post-Installation Configuration

### Initial Setup

```bash
# Set hostname
sudo hostnamectl set-hostname luo-manenos

# Update system
sudo apt-get update && sudo apt-get upgrade -y

# Set timezone
sudo timedatectl set-timezone UTC
```

### Privacy Hardening

```bash
# Apply sysctl privacy tuning
sudo sysctl -w net.ipv4.conf.all.send_redirects=0
sudo sysctl -w net.ipv4.conf.default.send_redirects=0
sudo sysctl -w net.ipv4.icmp_echo_ignore_broadcasts=1
sudo sysctl -w net.ipv4.conf.all.log_martians=1
sudo sysctl -w kernel.unprivileged_userns_clone=0

# Make persistent
echo "net.ipv4.conf.all.send_redirects=0" | sudo tee -a /etc/sysctl.conf
```

### Gaming Setup

```bash
# Install gaming dependencies
sudo apt-get install -y \
  libgl1-mesa-glx \
  libvulkan1 \
  vulkan-tools \
  mesa-vulkan-drivers

# Install Proton/Wine for Windows games
sudo apt-get install -y wine winetricks

# Verify gaming performance
glxinfo | grep "OpenGL version"
vulkaninfo
```

### MicroG Integration (Privacy-Preserving Google Services)

```bash
# Clone MicroG repository
git clone https://github.com/microg/GmsCore.git
cd GmsCore

# Build MicroG
./gradlew build

# Install MicroG services
adb install app/build/outputs/apk/release/GmsCore-release.apk
```

---

## Verification & Security

### Verify Kernel Patches

```bash
# Check kernel version and patches
uname -a
cat /proc/cmdline

# Verify gaming optimizations
cat /proc/sys/kernel/sched_pds
cat /proc/sys/kernel/sched_migration_cost_ns

# Verify privacy hardening
cat /proc/sys/kernel/unprivileged_userns_clone
cat /proc/sys/net/ipv4/conf/all/log_martians
```

### Checksum Verification

```bash
# Verify downloaded images
sha256sum -c SHA256SUMS

# Expected checksums available at:
# https://github.com/pilotjuen-hash/LUO-MANENOS/releases
```

### GPG Signature Verification

```bash
# Import Luo Manenos GPG key
gpg --keyserver keyserver.ubuntu.com --recv-keys <KEY_ID>

# Verify signature
gpg --verify rootfs.ext4.sig rootfs.ext4
```

---

## Troubleshooting

### Build Fails with "Out of Memory"

```bash
# Reduce parallel jobs
make -j2  # Use 2 cores instead of all cores
```

### Kernel Patch Application Fails

```bash
# Check patch compatibility
patch -p1 --dry-run < patch-file.patch

# If dry-run succeeds, apply with verbose output
patch -p1 -v < patch-file.patch
```

### Boot Issues

```bash
# Boot with verbose output
# Add "debug" to kernel command line in GRUB menu

# Check system logs
journalctl -xe
dmesg | tail -50
```

### Performance Issues

```bash
# Check CPU frequency scaling
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# Set to performance mode
echo "performance" | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

---

## Distribution Channels

Luo Manenos is distributed freely through multiple channels:

### GitHub Releases
- **URL**: https://github.com/pilotjuen-hash/LUO-MANENOS/releases
- **Content**: Source code, build configurations, pre-built images
- **Verification**: SHA-256 checksums and GPG signatures provided

### Google Drive
- **URL**: [Luo Manenos Google Drive Folder]
- **Content**: Pre-built bootable images, documentation
- **Access**: Public, no login required

### BitTorrent/IPFS
- **Magnet Link**: [Available on GitHub Releases]
- **IPFS Hash**: [Available on GitHub Releases]
- **Benefits**: Decentralized distribution, peer-to-peer sharing

---

## Support & Community

- **GitHub Issues**: https://github.com/pilotjuen-hash/LUO-MANENOS/issues
- **Documentation**: https://luomanenos-kndgc6tb.manus.space
- **License**: GPLv2 (Kernel), Apache 2.0 (MicroG), LGPLv2+ (glibc)

---

## License

Luo Manenos is free and open-source software. All components are licensed under their respective open-source licenses:

- **Linux Kernel**: GPLv2
- **MicroG**: Apache 2.0
- **GNU C Library (glibc)**: LGPLv2+
- **BusyBox**: GPLv2
- **Buildroot**: GPLv2

---

## Contributing

We welcome contributions! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

For major changes, please open an issue first to discuss your proposal.

---

**Last Updated**: June 8, 2026  
**Version**: 1.0.0  
**Maintainer**: Luo Manenos Project Team
