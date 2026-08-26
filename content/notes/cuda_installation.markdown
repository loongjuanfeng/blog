---
title: "在 Fedora 上安装 CUDA"
date: 2026-07-25T14:49:05+08:00
draft: true
toc: true
---

本文适用于 Fedora 43/44 系统。

# 准备工作
更新系统（Fedora 内核更新比较频繁）
```shell
sudo dnf upgrade --refresh
sudo reboot
```

安装必要软件包
```shell
sudo dnf install              \
  dnf5-plugins                \
  kernel-devel kernel-headers \
  gcc gcc-c++ make
```

添加 CUDA 源：
```shell
sudo dnf config-manager addrepo \
  --from-repofile=https://developer.download.nvidia.com/compute/cuda/repos/fedora44/x86_64/cuda-fedora44.repo
# CUDA also supports Fedora 43
# $ sudo dnf config-manager addrepo \
# ~   --from-repofile=https://developer.download.nvidia.com/compute/cuda/repos/fedora43/x86_64/cuda-fedora43.repo

sudo dnf clean expire-cache
```

# 安装 NVIDIA driver
微架构为 turing 或更新
```shell
sudo dnf install nvidia-open
```

微架构为 volta 或更旧
```shell
sudo dnf install cuda-drivers
```

# 安装 CUDA toolkit
```shell
sudo dnf install cuda-toolkit
```

# 配置 PATH 环境变量
in `~/.bashrc`:
```bash
export PATH="/usr/local/cuda/bin:$PATH"
```

# 验证安装
```shell
nvidia-smi
nvcc --version
```

# 参考资料

- [NVIDIA Driver Installation Guide - Fedora](https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/fedora.html)
- [CUDA Installation Guide for Linux - Fedora](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/#fedora-installation)
