# Luo Manenos Global Distribution and Hosting Strategy

To ensure Luo Manenos is available "for free all over the world," the project utilizes a multi-channel distribution strategy leveraging major public infrastructure.

## 1. Public GitHub Repository

The primary home for Luo Manenos source code and build automation is a **Public GitHub Repository**.

- **Repository URL:** `https://github.com/luomanenos/luomanenos` (Placeholder)
- **Contents:**
  - Buildroot configurations (`defconfig`).
  - Kernel patch sets for gaming and privacy.
  - Root filesystem overlays.
  - Documentation and legal notices.
- **GitHub Actions:** Automated builds to verify that every commit results in a bootable OS image.

## 2. Global Image Hosting (Google Services)

While the source code lives on GitHub, the compiled binary images (ISO, IMG, QCOW2) can be large. We leverage **Google Services** for global, high-speed distribution of these artifacts.

| Google Service | Usage in Luo Manenos | Benefit |
| --- | --- | --- |
| **Google Drive** | Primary download mirror for stable releases. | Easy to share publicly, high-speed downloads, and no cost for the project. |
| **Google Cloud Storage** | Backend storage for automated nightly builds. | Highly reliable, global availability, and can be integrated with a CDN. |
| **Google Cloud CDN** | Content Delivery Network for global image distribution. | Ensures low-latency downloads for users regardless of their geographic location. |

## 3. Alternative Free Distribution Channels

To maximize reach and ensure censorship resistance, Luo Manenos will also be available via:

- **Internet Archive:** For long-term preservation of every version.
- **BitTorrent / WebTorrent:** For peer-to-peer distribution, reducing the load on central servers and allowing users to help each other download the OS.
- **IPFS (InterPlanetary File System):** For decentralized, content-addressed storage and distribution.

## 4. Free Access Policy

Luo Manenos is committed to being free of charge for all users worldwide.
- No paywalls or mandatory "donations."
- No proprietary "pro" versions.
- All optimizations and features are included in the base open-source distribution.

## 5. Security and Verification

Every distributed image will be accompanied by:
- **SHA-256 Checksums:** To allow users to verify the integrity of their download.
- **GPG Signatures:** To verify that the image was officially released by the Luo Manenos project team.
