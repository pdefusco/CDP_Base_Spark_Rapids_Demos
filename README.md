# CDP Base Spark Rapids Demos


## 1. Cluster Setup

## 2. Nvidia Drivers Setup

#### 1. Kernel prerequisites
sudo dnf install -y kernel-devel-$(uname -r) kernel-headers-$(uname -r)

#### 2. EPEL -- direct RPM because epel-release isn't in RHUI
sudo dnf install -y \
  https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

#### 3. CodeReady Builder
sudo dnf config-manager \
  --set-enabled codeready-builder-for-rhel-9-rhui-rpms

#### 4. Refresh repositories
sudo dnf clean expire-cache
sudo dnf makecache

#### 5. Verify/install DKMS
sudo dnf install -y dkms

#### 6. NVIDIA CUDA repository
sudo dnf config-manager --add-repo \
  https://developer.download.nvidia.com/compute/cuda/repos/rhel9/x86_64/cuda-rhel9.repo

#### 7. Refresh NVIDIA repo metadata
sudo dnf clean expire-cache
sudo dnf makecache

#### 8. Install NVIDIA driver
sudo dnf module reset -y nvidia-driver
sudo dnf module install -y nvidia-driver:610-open

#### 9. Reboot
sudo reboot

#### 10. Test Nvidia SMI

nvidia-smi
