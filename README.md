# CDP Base Spark Rapids Demos


## 1. Cluster Setup

## 2. Nvidia Drivers Setup

SSH into gpu node. In each GPU node, run:

# NVIDIA GPU Driver Setup for RHEL 9 GPU Workers

This procedure prepares a RHEL 9 x86_64 GPU worker for NVIDIA GPU workloads, including Spark RAPIDS.

The setup installs:

* Kernel development packages
* EPEL
* CodeReady Builder
* DKMS
* NVIDIA CUDA repository
* NVIDIA NVIDIA driver

After installation, the node is rebooted and validated using `nvidia-smi`.

## 1. Verify the operating system and kernel

Check the RHEL version:

```bash
cat /etc/redhat-release
```

Check the running kernel:

```bash
uname -r
```

Check the architecture:

```bash
uname -m
```

Expected architecture:

```text
x86_64
```

Check installed kernel packages:

```bash
rpm -qa | grep '^kernel'
```

## 2. Verify kernel development packages

List available `kernel-devel` versions:

```bash
sudo dnf list --showduplicates kernel-devel
```

List available `kernel-headers` versions:

```bash
sudo dnf list --showduplicates kernel-headers
```

Determine the running kernel:

```bash
uname -r
```

Install the matching development packages:

```bash
sudo dnf install -y kernel-devel-$(uname -r) kernel-headers-$(uname -r)
```

Verify:

```bash
rpm -q kernel-devel
```

```bash
rpm -q kernel-headers
```

The `kernel-devel` version should match the running kernel returned by:

```bash
uname -r
```

For example:

```text
5.14.0-687.41.1.el9_8
```

would correspond to:

```text
kernel-devel-5.14.0-687.41.1.el9_8
kernel-headers-5.14.0-687.41.1.el9_8
```

## 3. Verify enabled repositories

Check the enabled repositories:

```bash
sudo dnf repolist enabled
```

The system should have repositories similar to:

```text
cloudera-manager
rhel-9-appstream-rhui-rpms
rhel-9-baseos-rhui-rpms
rhui-client-config-server-9
```

The following message may appear on AWS RHEL systems using RHUI:

```text
Unable to read consumer identity

This system is not registered with an entitlement server.
```

This does not prevent the RHUI repositories from functioning.

## 4. Install EPEL

`epel-release` is not available from the standard RHUI repositories, so install it directly from Fedora:

```bash
sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```

Verify EPEL:

```bash
sudo dnf repolist | grep -i epel
```

Expected output should include:

```text
epel
epel-cisco-openh264
```

## 5. Enable CodeReady Builder

Check the CodeReady repositories:

```bash
sudo dnf repolist all | grep -i codeready
```

The following repository may initially be disabled:

```text
codeready-builder-for-rhel-9-rhui-rpms
```

Enable it:

```bash
sudo dnf config-manager --set-enabled codeready-builder-for-rhel-9-rhui-rpms
```

Verify:

```bash
sudo dnf repolist enabled | grep -i codeready
```

## 6. Refresh repositories

Clean expired repository metadata:

```bash
sudo dnf clean expire-cache
```

Rebuild the DNF cache:

```bash
sudo dnf makecache
```

## 7. Install DKMS

Install DKMS:

```bash
sudo dnf install -y dkms
```

Verify:

```bash
dkms --version
```

You can also verify the installed RPM:

```bash
rpm -q dkms
```

## 8. Add the NVIDIA CUDA repository

Add NVIDIA's CUDA repository for RHEL 9 x86_64:

```bash
sudo dnf config-manager --add-repo https://developer.download.nvidia.com/compute/cuda/repos/rhel9/x86_64/cuda-rhel9.repo
```

Verify that the repository was added:

```bash
sudo dnf repolist | grep -i cuda
```

## 9. Refresh NVIDIA repository metadata

Clean the repository metadata:

```bash
sudo dnf clean expire-cache
```

Rebuild the metadata cache:

```bash
sudo dnf makecache
```

Optionally verify that NVIDIA packages are available:

```bash
sudo dnf list available | grep -i nvidia
```

## 10. Install the NVIDIA driver

Reset the NVIDIA driver module:

```bash
sudo dnf module reset -y nvidia-driver
```

Install the NVIDIA open kernel module driver:

```bash
sudo dnf module install -y nvidia-driver:610-open
```

Verify NVIDIA packages:

```bash
rpm -qa | grep -i nvidia
```

Check DKMS:

```bash
dkms status
```

The driver is now installed, but the system needs to be rebooted before the NVIDIA kernel modules are fully active.

## 11. Reboot

Reboot the GPU worker:

```bash
sudo reboot
```

Reconnect to the worker after it comes back online.

## 12. Verify NVIDIA GPU

Run:

```bash
nvidia-smi
```

A successful result should show:

* NVIDIA GPU model
* NVIDIA driver version
* CUDA version
* GPU memory
* GPU utilization
* Running GPU processes

Verify that the NVIDIA kernel modules are loaded:

```bash
lsmod | grep nvidia
```

Verify that NVIDIA device files exist:

```bash
ls -l /dev/nvidia*
```

## Final verification

Run the following commands after the reboot:

```bash
uname -r
```

```bash
rpm -q kernel-devel
```

```bash
rpm -q kernel-headers
```

```bash
rpm -q dkms
```

```bash
dkms status
```

```bash
nvidia-smi
```

```bash
lsmod | grep nvidia
```

```bash
ls -l /dev/nvidia*
```

The most important final test is:

```bash
nvidia-smi
```
