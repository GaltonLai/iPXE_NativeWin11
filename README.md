# 🌐 把系统装进 GitHub：里面是 TinyCore Linux

> 通过 iPXE 从 GitHub 公开仓库直接引导 TinyCore Linux，实现“插上网线，全球启动”的轻量级云桌面。
>
> 怎么做到的？

---

## 📌 项目简介

这个项目怎么做到的？

本项目是这么做到的，将 **TinyCore Linux**（一个极简的 Linux 发行版）的核心文件（内核 `vmlinuz` 和初始内存盘 `core.gz`）托管到 GitHub 公开仓库，并利用 **iPXE** 网络引导技术，让任何能联网的电脑都能通过网络启动进入该系统。

**核心优势：**
- ✅ 无需本地硬盘，完全内存运行
- ✅ 客户端只需支持 PXE 或能启动 iPXE（U盘/ISO）
- ✅ 镜像文件极小（< 50 MB），完美适配 GitHub 100 MB 限制
- ✅ 全自动启动，无需手动输入命令（编译嵌入脚本后）

---

## 🧩 项目结构

```
.
├── vmlinuz          # TinyCore Linux 内核
├── core.gz          # TinyCore 初始内存盘（根文件系统）
├── boot.ipxe        # iPXE 启动脚本
├── wimboot          # (可选) 用于引导 Windows PE
└── README.md        # 项目说明
```

---

## 🚀 快速开始

### 客户端要求
- 一台支持 **PXE 网络启动** 或能通过 U 盘/ISO 启动 **iPXE** 的电脑
- 网络畅通，能访问 `raw.githubusercontent.com`（或国内镜像）

### 使用步骤

#### 1. 准备 iPXE 启动介质
- **方式 A（U盘）**：下载 [iPXE 官方 ISO](https://ipxe.org/download)，用 Rufus 写入 U 盘。
- **方式 B（编译嵌入脚本）**：将 `boot.ipxe` 嵌入 iPXE 固件，实现全自动启动。

#### 2. 在 iPXE 命令行执行
启动进入 iPXE 后，输入：
```ipxe
chain https://raw.githubusercontent.com/GaltonLai/iPXE_NativeWin11/main/boot.ipxe
```
系统将自动下载内核和 initrd，并启动 TinyCore Linux。

#### 3. 进入系统
启动后你会进入 TinyCore 的 BusyBox 命令行（`tc@box:~$`）。若需图形界面，可在命令行执行：
```bash
tce-load -wi Xvesa flwm Xprogs wbar
startx
```

---

## ⚙️ 工作原理

1. **iPXE** 通过 HTTP 从 GitHub Raw 链接下载 `boot.ipxe` 脚本。
2. 脚本使用 `kernel` 和 `initrd` 命令加载 `vmlinuz` 和 `core.gz`。
3. `boot` 命令启动 Linux 内核，内核解压 `core.gz` 作为根文件系统。
4. 系统完全运行在内存中，无需本地存储。

---

## 📜 许可证与致谢

- **TinyCore Linux**：基于 GPLv2 许可，源代码及版权信息见 [TinyCore 官网](http://tinycorelinux.net)。
- **iPXE**：基于 GPLv2 许可，源代码及版权信息见 [iPXE 官网](https://ipxe.org)。
- **本项目**：仅作为技术演示，所有第三方文件版权归原作者所有。使用本项目内容请遵守相关开源许可。

---

## ⚠️ 注意事项

- **网络要求**：客户端必须能访问 GitHub Raw 域名（国内可能需代理或替换为 Gitee 等镜像）。
- **内存需求**：TinyCore 运行约需 256 MB 内存，图形界面需更多。
- **UEFI 安全启动**：iPXE 可能被 Secure Boot 拦截，需在 BIOS 中关闭或使用签名的 iPXE。
- **GitHub 限制**：单个文件 < 100 MB，本项目所有文件均在此限制内。

---

## 🛠️ 自定义与扩展

- **更换发行版**：替换 `vmlinuz` 和 `initrd` 为其他 Linux 发行版的内核和 initramfs，并调整命令行参数。
- **添加持久化**：可通过 NFS 或 iSCSI 挂载远程存储，实现数据保存。
- **图形界面**：参考 TinyCore 文档，添加更多扩展包。

---

## 🤝 贡献与反馈

如果你有改进建议或遇到问题，欢迎提交 Issue 或 Pull Request。
