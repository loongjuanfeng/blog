---
title: "基本 Linux 运维"
date: 2026-07-19T15:54:07+08:00
draft: true
toc: true
tocBorder: true
---

也是租了很久的服务器了，也总是做“运维”（非严肃运维）相关的活，每次查都很费劲，于是总结出一些个人认为的 best practice.

默认是在 Ubuntu 24.04 LTS 上进行，没办法，Ubuntu 市占率实在是太高了。如果有选择谁不想用 Fedora 44 呢……

# Tmux

会把 scroll 映射到方向键，如果不更改配置的话想 scroll 只能 `Ctrl-B [`, 非常讨厌。

在 `~/.tmux.conf` 底部添加

```conf
set -g mouse on
```

能打开原生鼠标支持。（此后选择文本只能按着 `shift` 进行选中了）

对于 ESC 的拦截时间非常长，default 是 500ms.

```conf
# esc 拦截时间设为 10ms
set -sg escape-time 10
```

然后要么 reboot, 要么 `tmux source-file ~/.tmux.conf`.

（实际上用了 `zellij` 就没这么多事了）

# 关于无 `sudo` 的软件安装

自己 `~/.local/bin` 里面放一个 symlink, 二进制 stand-alone 或者 libs 什么的都丢进 `~/.local/share/` 里面。

我一般会安装的软件是 `fish`, `btop`, `clangd`, `helix`, `zellij`, `neovim`.

# 关于 `bash` 环境变量配置

```bash
export PATH="/my/custom/path:$PATH"
```

哦对了，`fish` 的 `PATH` 是这么改的
```fish
fish_add_path (pwd)
```

我不喜欢 `bash`, 也记不住这个语法。这玩意纯上个世纪的奇妙产物，行为也很奇怪。

# 文件解压
`.tar.[gz,xz]` 的文件都可以 `tar -xf <TAR FILE> -C <OUTPUT DIR>`.  
`.zip` 直接 `unzip <ZIP FILE> -d <OUTPUT DIR>`.

# 文件下载
`wget -nv -P <OUTPUT DIR> <URL>`（单线程）

# Git 相关
## `.gitignore`
支持简单的匹配，例如 `[]`, `*`, `**`.

## `.gitattributes`

常用范式为
```gitattributes
# scripts are text
scripts/* text eol=lf

# auto mode
* text=auto eol=lf

# binaries
bin/* binary

# diff mode for specific languages
src/**.cc diff=cxx
src/*.py diff=python
```

## git submodule
```shell
git submodule init
git submodule update

# or one-liner
git submodule update --init --recursive
```

# GitHub SSH key
```shell
ssh-keygen # enter enter enter ...
gh auth login --web --scopes admin:public_key
```

Dotfiles: https://github.com/loongjuanfeng/dotfiles





