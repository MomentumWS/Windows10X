# Windows 10X 部署与 Win32 兼容性研究

[English](README.md) | **简体中文**

项目研究 Windows 10X 20279.1002 x64 能否在保留原有界面的前提下，从 WinPE 部署到普通 UEFI 电脑，并运行传统 Windows 桌面程序。

## 当前进度

| 研究方向 | 已验证 | 待验证 |
| --- | --- | --- |
| 镜像与启动 | 已核对原始存储池镜像的外层分区，并在重建的六分区 GPT 恢复副本中核对 EFI、MainOS、BSP 和 OSData 的启动绑定。修复后的 GPT 镜像已在 Hyper-V 第二代虚拟机进入桌面。 | 在空白盘上重复 WinPE 部署，验证实体机首次启动与重启。 |
| VMware 迁移 | 手工还原两个分区时出现加载器错误。另一次克隆盘测试进入内核后遇到 BAD_SYSTEM_CONFIG_INFO。 | 在固定镜像和虚拟控制器配置下捕获停止参数，确认原因。 |
| Win32 控制台与图形 | 来宾能运行内置 cmd 和 PowerShell。独立控制台样本启动时观察到代码完整性校验失败，直接启动预装浏览器的实验也遇到窗口 API 错误。 | 用独立样本分别验收控制台输出、GUI 可见窗口及交互，再验证安装与卸载。 |
| Argon / VAIL 容器 | 来宾有相关组件清单和服务。一次运行中容器管理服务已启动，枚举结果为空。 | 记录普通 EXE 通过容器启动的完整过程，并核对程序输出。 |

目前可复核的启动环境是 Hyper-V。实体机启动、通用桌面软件兼容和 MSI 安装仍是开放问题。

## 实验资料

- [镜像清单](evidence/images.json)列出原盘和恢复副本的文件长度、SHA-256 及分区布局。
- [启动与应用验证记录](docs/validation.zh-CN.md)说明哪份镜像进过桌面，以及每项实验还缺哪些证据。

## 公开的构建配置

2026-10-24 的 ImageApp 日志记录了使用下列 OEM 输入完成 AMD64 镜像构建。后续在修复 BCD 的工作盘上验证了 Hyper-V 桌面启动。

| 发布文件 | 构建时的文件名 | 用途 |
| --- | --- | --- |
| [OEMInput.xml](config/OEMInput.xml) | `OEMInput.generated.xml` | 指定 ModernPC、普通 GPT 和功能清单。原件保留了构建机的 D 盘路径和原始注释。 |
| [DeviceLayout.xml](config/DeviceLayout.xml) | `FlashGPT.DeviceLayout.xml` | 给出六个分区的顺序、文件系统和大小规则。 |
| [DriveLetterConfig.xml](config/DriveLetterConfig.xml) | `FlashGPT.DriveLetterConfig.xml` | 来宾系统将 MainOS 映射为 M:，Data 映射为 C:。WinPE 可另行分配盘符。 |

三份 XML 均保留历史文件的原始字节。[SHA-256 校验清单](config/SHA256SUMS)
