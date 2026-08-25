# CDP Base Spark Rapids Demos


## 1. Cluster Setup

## 2. Nvidia Drivers Setup

SSH into gpu node. In each GPU node, run:

# ============================================================
# NVIDIA DRIVER INSTALLATION - RHEL 9 GPU NODE
# ============================================================
# Prerequisite:
#   - Node is running kernel 5.14.0-687.41.1.el9_8.x86_64
#   - NVIDIA GPU is physically attached to the node
#
# ============================================================
# 1. CONFIRM RUNNING KERNEL
# ============================================================

uname -r

# Expected:
# 5.14.0-687.41.1.el9_8.x86_64


# ============================================================
# 2. VERIFY MATCHING KERNEL DEVELOPMENT PACKAGES
# ============================================================

rpm -q kernel-devel-$(uname -r)
rpm -q kernel-headers-$(uname -r)

# Both commands should return:
# 5.14.0-687.41.1.el9_8.x86_64


# ============================================================
# 3. INSTALL EPEL DIRECTLY
# ============================================================
# epel-release is not available through the RHEL RHUI repos,
# so install the EPEL release RPM directly from Fedora.

sudo dnf install -y \
  https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

# Verify EPEL:

sudo dnf repolist | grep -i epel

# Expected:
# epel
# epel-cisco-openh264


# ============================================================
# 4. ENABLE CODEREADY BUILDER
# ============================================================

# First verify that the repository exists:

sudo dnf repolist all | grep -i codeready

# Expected to include:
# codeready-builder-for-rhel-9-rhui-rpms

# Enable it:

sudo dnf config-manager \
  --set-enabled codeready-builder-for-rhel-9-rhui-rpms

# Verify that it is enabled:

sudo dnf repolist enabled | grep -i codeready

# Expected:
# codeready-builder-for-rhel-9-rhui-rpms


# ============================================================
# 5. REFRESH REPOSITORY METADATA
# ============================================================

sudo dnf clean expire-cache
sudo dnf makecache


# ============================================================
# 6. INSTALL DKMS
# ============================================================

sudo dnf install -y dkms

# Verify:

dkms --version


# ============================================================
# 7. ADD NVIDIA CUDA REPOSITORY
# ============================================================

sudo dnf config-manager \
  --add-repo \
  https://developer.download.nvidia.com/compute/cuda/repos/rhel9/x86_64/cuda-rhel9.repo


# ============================================================
# 8. REFRESH NVIDIA REPOSITORY METADATA
# ============================================================

sudo dnf clean expire-cache
sudo dnf makecache


# ============================================================
# 9. RESET NVIDIA DRIVER MODULE
# ============================================================

sudo dnf module reset -y nvidia-driver


# ============================================================
# 10. INSTALL NVIDIA 610 OPEN KERNEL MODULE DRIVER
# ============================================================
# This is the driver stream used successfully on GPU worker 01.

sudo dnf module install -y nvidia-driver:610-open


# ============================================================
# 11. REBOOT
# ============================================================

sudo reboot


# ============================================================
# 12. VERIFY AFTER REBOOT
# ============================================================
# SSH back into the node after it comes back online.

# Verify running kernel:

uname -r

# Expected:
# 5.14.0-687.41.1.el9_8.x86_64

# Verify NVIDIA driver and GPU:

nvidia-smi

# Expected to see approximately:
#
# NVIDIA-SMI 610.57.04
# KMD Version: 610.57.04
# CUDA UMD Version: 13.3
#
# GPU:
# NVIDIA A10G
#
# GPU Memory:
# approximately 23028 MiB
