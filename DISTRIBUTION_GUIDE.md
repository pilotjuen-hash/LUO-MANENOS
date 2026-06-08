# Luo Manenos: Global Distribution & Verification Guide

## Distribution Overview

**Luo Manenos** is distributed freely across multiple channels to ensure global accessibility, redundancy, and decentralized availability. All distribution channels provide the same verified, secure software.

---

## Distribution Channels

### 1. GitHub Releases (Primary Distribution)

**URL**: https://github.com/pilotjuen-hash/LUO-MANENOS/releases

**Content Available**:
- Source code (complete repository)
- Pre-built bootable images (.img, .iso)
- Build configurations and patches
- Documentation and guides
- SHA-256 checksums
- GPG signatures

**Advantages**:
- Version control and release history
- Integrated issue tracking
- Community contributions
- Automatic backup and redundancy

**Download Instructions**:
```bash
# Clone repository
git clone https://github.com/pilotjuen-hash/LUO-MANENOS.git

# Download specific release
cd LUO-MANENOS
git checkout v1.0.0  # Replace with desired version

# Download pre-built images
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/luo-manenos-v1.0.0.img.gz
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/SHA256SUMS
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/SHA256SUMS.sig
```

---

### 2. Google Drive (Mirror Distribution)

**URL**: [Luo Manenos Public Google Drive Folder]

**Content Available**:
- Pre-built bootable images
- Installation guides
- Quick start documentation
- Frequently asked questions

**Advantages**:
- High-speed downloads (especially in regions with GitHub restrictions)
- No technical knowledge required for access
- Integrated preview capabilities
- Automatic versioning

**Download Instructions**:
```bash
# Access via web browser
# https://drive.google.com/drive/folders/[FOLDER_ID]

# Or use gdrive CLI tool
gdrive download [FILE_ID]

# Or use wget with Google Drive link
wget --no-check-certificate 'https://drive.google.com/uc?export=download&id=[FILE_ID]' -O filename
```

---

### 3. BitTorrent/IPFS (Decentralized Distribution)

**Magnet Link**: `magnet:?xt=urn:btih:[HASH]&dn=luo-manenos-v1.0.0.img.gz`

**IPFS Hash**: `QmXxxx...` (available on GitHub Releases)

**Content Available**:
- Complete bootable images
- Full source code
- All documentation

**Advantages**:
- Decentralized, peer-to-peer distribution
- No single point of failure
- Bandwidth efficient (shared among peers)
- Censorship resistant
- Permanent availability (IPFS)

**Download Instructions**:

**BitTorrent**:
```bash
# Using transmission-cli
transmission-cli 'magnet:?xt=urn:btih:[HASH]&dn=luo-manenos-v1.0.0.img.gz'

# Using qbittorrent
qbittorrent 'magnet:?xt=urn:btih:[HASH]&dn=luo-manenos-v1.0.0.img.gz'

# Or download .torrent file from GitHub Releases
transmission-cli luo-manenos-v1.0.0.torrent
```

**IPFS**:
```bash
# Install IPFS
wget https://dist.ipfs.io/go-ipfs/v0.20.0/go-ipfs_v0.20.0_linux-amd64.tar.gz
tar -xzf go-ipfs_v0.20.0_linux-amd64.tar.gz
sudo ./go-ipfs/install.sh

# Initialize IPFS
ipfs init

# Start IPFS daemon
ipfs daemon &

# Download from IPFS
ipfs get QmXxxx... -o luo-manenos-v1.0.0.img.gz
```

---

## Verification & Security

### SHA-256 Checksum Verification

**Why Verify?**
- Ensures file integrity (no corruption during download)
- Detects man-in-the-middle attacks
- Confirms authenticity of distribution

**Verification Process**:

```bash
# 1. Download the image and checksums
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/luo-manenos-v1.0.0.img.gz
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/SHA256SUMS

# 2. Verify checksum
sha256sum -c SHA256SUMS

# Expected output:
# luo-manenos-v1.0.0.img.gz: OK

# 3. If verification fails, re-download from alternative channel
# (GitHub, Google Drive, or BitTorrent)
```

**Manual Checksum Verification**:
```bash
# Calculate checksum of downloaded file
sha256sum luo-manenos-v1.0.0.img.gz

# Compare with published checksum from GitHub Releases
# Output should match exactly
```

---

### GPG Signature Verification

**Why Verify Signatures?**
- Confirms authenticity (file signed by Luo Manenos maintainers)
- Prevents tampering (cryptographic proof of origin)
- Establishes trust chain

**Verification Process**:

```bash
# 1. Import Luo Manenos GPG public key
gpg --keyserver keyserver.ubuntu.com --recv-keys 0x[KEY_ID]

# Alternative: Import from GitHub
curl https://github.com/pilotjuen-hash.gpg | gpg --import

# 2. Download signature file
wget https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/SHA256SUMS.sig

# 3. Verify signature
gpg --verify SHA256SUMS.sig SHA256SUMS

# Expected output:
# gpg: Signature made [DATE] using RSA key ID [KEY_ID]
# gpg: Good signature from "Luo Manenos Project <[EMAIL]>"
```

**Key Information**:
- **Key ID**: 0x[KEY_ID]
- **Key Fingerprint**: [FINGERPRINT]
- **Key Owner**: Luo Manenos Project
- **Key Server**: keyserver.ubuntu.com

---

### Complete Verification Workflow

```bash
#!/bin/bash
# Complete verification script for Luo Manenos downloads

set -e

VERSION="v1.0.0"
FILENAME="luo-manenos-${VERSION}.img.gz"
GITHUB_RELEASE="https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/${VERSION}"

echo "=== Luo Manenos Verification Script ==="
echo

# Step 1: Download files
echo "[1/4] Downloading files..."
wget -q "${GITHUB_RELEASE}/${FILENAME}"
wget -q "${GITHUB_RELEASE}/SHA256SUMS"
wget -q "${GITHUB_RELEASE}/SHA256SUMS.sig"
echo "✓ Files downloaded"
echo

# Step 2: Verify checksum
echo "[2/4] Verifying SHA-256 checksum..."
if sha256sum -c SHA256SUMS; then
    echo "✓ Checksum verification passed"
else
    echo "✗ Checksum verification FAILED"
    exit 1
fi
echo

# Step 3: Import GPG key
echo "[3/4] Importing GPG key..."
gpg --keyserver keyserver.ubuntu.com --recv-keys 0x[KEY_ID] 2>/dev/null || true
echo "✓ GPG key imported"
echo

# Step 4: Verify signature
echo "[4/4] Verifying GPG signature..."
if gpg --verify SHA256SUMS.sig SHA256SUMS; then
    echo "✓ GPG signature verification passed"
else
    echo "✗ GPG signature verification FAILED"
    exit 1
fi
echo

echo "=== All verifications passed! ==="
echo "File '${FILENAME}' is authentic and ready to use."
```

---

## Installation from Downloaded Image

### Decompress Image

```bash
# If downloaded as .gz
gunzip luo-manenos-v1.0.0.img.gz

# Result: luo-manenos-v1.0.0.img
```

### Write to USB Drive

```bash
# List available drives
lsblk

# Write image (replace sdX with your USB device)
sudo dd if=luo-manenos-v1.0.0.img of=/dev/sdX bs=4M status=progress
sudo sync

# Eject safely
sudo eject /dev/sdX
```

### Boot and Install

1. Insert USB drive into target system
2. Reboot and enter BIOS/UEFI
3. Set boot priority to USB device
4. Follow on-screen installation prompts
5. Reboot into Luo Manenos

---

## Troubleshooting Distribution Issues

### Download Interrupted

```bash
# Resume download using wget
wget -c https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/luo-manenos-v1.0.0.img.gz

# Or use aria2 for multi-threaded downloads
aria2c https://github.com/pilotjuen-hash/LUO-MANENOS/releases/download/v1.0.0/luo-manenos-v1.0.0.img.gz
```

### Checksum Mismatch

```bash
# 1. Verify downloaded file is complete
ls -lh luo-manenos-v1.0.0.img.gz

# 2. Re-download from alternative channel
# Try Google Drive or BitTorrent

# 3. Verify again
sha256sum -c SHA256SUMS
```

### GPG Key Not Found

```bash
# Try alternative key servers
gpg --keyserver pgp.mit.edu --recv-keys 0x[KEY_ID]
gpg --keyserver keys.openpgp.org --recv-keys 0x[KEY_ID]

# Or download key directly
curl https://github.com/pilotjuen-hash.gpg | gpg --import
```

### Slow Downloads

```bash
# Use BitTorrent for faster, distributed downloads
transmission-cli 'magnet:?xt=urn:btih:[HASH]'

# Or use IPFS for decentralized access
ipfs get QmXxxx...

# Or try Google Drive mirror
wget 'https://drive.google.com/uc?export=download&id=[FILE_ID]'
```

---

## Reporting Security Issues

If you discover a security vulnerability in Luo Manenos:

1. **Do NOT** post it publicly on GitHub Issues
2. **Email** security@luo-manenos.dev with:
   - Description of vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if available)

3. **Allow** 90 days for patch development and testing
4. **Coordinate** public disclosure after patch release

---

## License & Legal

All Luo Manenos distribution channels provide software under the following licenses:

- **Linux Kernel**: GPLv2
- **MicroG**: Apache 2.0
- **GNU C Library (glibc)**: LGPLv2+
- **BusyBox**: GPLv2
- **Buildroot**: GPLv2

**Free Access Policy**: Luo Manenos is completely free for personal, commercial, and educational use. No registration, licensing fees, or restrictions apply.

---

## Support

- **Documentation**: https://luomanenos-kndgc6tb.manus.space
- **GitHub Issues**: https://github.com/pilotjuen-hash/LUO-MANENOS/issues
- **Build Guide**: See BUILD_INSTRUCTIONS.md in repository

---

**Last Updated**: June 8, 2026  
**Version**: 1.0.0  
**Maintainer**: Luo Manenos Project Team
