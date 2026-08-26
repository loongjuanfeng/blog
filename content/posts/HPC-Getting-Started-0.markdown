---
title: "HPC-Getting-Started #0"
date: 2026-07-25T15:29:19+08:00
draft: true
---

主要放一些杂项。

# 关于这个项目
HPC-Getting-Started 是我的 HPC 学习项目。目的是练习 C/C++ 编程，并行编程，CUDA 编程，以及基本的性能测试方法。

接下来会简单说明一下这个项目的结构，实验和结论；并且尝试提供一些方便的复现工具。

# 项目结构
```text
├── source/
│   ├── core/          # common static library and utilities
│   ├── mat_mul/       # matrix multiplication
│   ├── spmv_csr/      # sparse matrix-vector multiplication
│   ├── stencil_2d/    # 2-dimensional stencil
│   └── vec_add/       # vector addition
└── xmake.lua          # build script
```

```text
├── include/
│   ├── allocator.hh  # no-initialization allocator
│   ├── config.hh     # read config from toml file (using toml++)
│   ├── log.hh        # logging wrapper for spdlog
│   ├── report.hh     # unified report class
│   └── timer.hh      # simple timer
├── allocator.cc
├── allocator_test.cc
├── config.cc
├── config_test.cc
├── report_test.cc
└── timer_test.cc
```
