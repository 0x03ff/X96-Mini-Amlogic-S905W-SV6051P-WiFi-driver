# X96-Mini-Amlogic-S905W-SV6051P-WiFi-driver

Based on Rayhan Azad's [original project:SV6051-SV6051P-WiFi-driver-for-Amlogic-S905W-X96-Mini-with-DKMS-support](https://github.com/rayhanorigin/SV6051-SV6051P-WiFi-driver-for-Amlogic-S905W-X96-Mini-with-DKMS-support). Includes the critical SDIO timing fix (`-DSDIO_USE_SLOW_CLOCK`) with DKMS support and modern Linux kernel compatibility patches.

---

## Linux Kernel Compatibility (v6.12+)

In newer Linux kernel updates (such as 6.12+), internal system call parameters and API signatures changed, which caused the original network driver to fail to load.

### Key Fixes
1. **Timer Container Macro (`smac/init.c`):** Updated timer initialization and reference macros to align with modern kernel container standards.
2. **`ieee80211_hw` Config Signature Mismatch (`smac/dev.c`):** The `net/mac80211` subsystem updated the config callback function prototype by removing the redundant `int radio_idx` parameter.

---
## Prerequisite: Kernel Headers
Before you can run any build or DKMS commands, your system must have the matching Linux kernel headers installed.

Because a driver interacts directly with the core operating system, it cannot compile without knowing the exact source structures and macros of the specific kernel version currently running on your device `(for instance, linux-headers-6.12.117-ophub)`. If headers are missing, the compilation will fail immediately.

>[!warning]`Kernel Headers Requirement`
Make sure your device has the correct kernel header package installed via your package manager (e.g `apt install linux-headers-$(uname -r)`) before attempting to build the driver.

---

## Installation Procedure

Follow these steps directly in your terminal to build and install the driver using DKMS:

### 1. Clone the Repository
Clone the repository and enter the project directory:
```bash
git clone https://github.com/0x03ff/X96-Mini-Amlogic-S905W-SV6051P-WiFi-driver.git
cd X96-Mini-Amlogic-S905W-SV6051P-WiFi-driver
```

### 2. Copy Source Files to `/usr/src/`
DKMS requires the driver source tree to reside in `/usr/src/` under the module name and version format (`ssv6051-1.0`):
```bash
sudo mkdir -p /usr/src/ssv6051-1.0
sudo cp -r . /usr/src/ssv6051-1.0/
```

### 3. Register, Build, and Install via DKMS
Execute the DKMS commands to register, compile, and install the module for your active kernel:
```bash
sudo dkms remove -m ssv6051 -v 1.0 --all || true
sudo dkms add -m ssv6051 -v 1.0
sudo dkms build -m ssv6051 -v 1.0
sudo dkms install -m ssv6051 -v 1.0
```

### 4. Install Firmware (optional)
Copy the required Wi-Fi configuration and firmware files to the system directory:
```bash
sudo cp -r firmware/* /lib/firmware/
```

### 5. Load the Kernel Module (optional)
Update module dependencies and load the driver:
```bash
sudo depmod -a
sudo modprobe ssv6051
```
### 6. Verify Everything Correctly Active
1. Check if the module is loaded:
```bash
lsmod | grep ssv6051
```
2. Check your wireless interface:
```bash
ip link
```
---

## ⚠️ Warnings & Disclaimer
- **Testing:** This driver has not been exhaustively tested across all different wireless use cases and hardware revisions.
- **Kernel Stability:** Future Linux kernel updates may introduce breaking changes that could break this driver installation. No guarantees are made for future kernel versions.
- **Support:** Since I am continuously learning Linux systems programming, if you run into unique issues, searching community forums or consulting LLM tools is recommended for troubleshooting.