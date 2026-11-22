# 启动与应用验证记录

[中文项目主页](../README.zh-CN.md) | [English validation record](validation.md)

## 哪一份镜像启动过

| 样本 | 观测方法 | 结果 |
| --- | --- | --- |
| 原始 Flash.vhdx | 历史只读磁盘分析 | 外层 GPT 有 BS_EFIESP 和 OSPool。外层 ESP 枚举到 EFI/Boot/BOOTX64.EFI。池内 MainOS 和 BCD 的完整映射仍待核实。 |
| FlashGPT-BCD-GPT 工作盘 | Hyper-V 第二代虚拟机启动 | 进入 10X 桌面，来宾中可运行 cmd 和 PowerShell 7.0.3。这是本项目已取得的桌面启动结果。 |
| FlashGPT-BCD-GPT-PreDebug.vhdx 恢复副本 | 只读检查 | 枚举到 EFIESP、MainOS、BSP、PreInstalled、OSData 和 Data。历史 BCD 检查确认默认加载项的 device 和 osdevice 均对应 MainOS，BSP 和 OSData 绑定与分区标识相符。 |

2026-11-22 重新读取 PreDebug 副本，文件长度为 3,795,845,120 字节，SHA-256 与清单一致，主备 GPT 校验通过。原盘一栏引用历史记录。工作盘经来宾写入后会增长，恢复副本的哈希仅对应清单中指定的文件。[镜像清单](../evidence/images.json)

## 哪个环节仍待验证

| 独立实验 | 当时看到什么 | 还缺什么证据 |
| --- | --- | --- |
| VMware 手工还原 EFIESP 和 MainOS | 恢复界面显示 winload.efi 和 0xc000000e。另一次 VMware 测试出现内核停止码 0x74。 | 同一份完整镜像、固定控制器配置下的停止参数和调用栈。两个现场应分别追踪。 |
| 启动独立控制台 EXE | 来宾事件 3033 记录 Win32Probe 未满足签名等级；另一控制台样本实验在创建映像节时观察到 0xc0000428。 | 在同一次启动中关联目标文件、策略事件、程序输出和退出码。 |
| 桌面窗口 | 直接运行预装浏览器的包内 EXE 时，注册消息窗口类返回 0x78。 | 由独立 Win32 GUI 样本创建可见窗口，验证交互与正常退出。 |
| Argon / VAIL | 来宾有 VailArgonCommon 包，CmService、hns 和 vmcompute 在一次查询时均为 Running，当次容器枚举为空。 | 启动普通 EXE 并将可观察的程序输出关联到具体的容器实例。 |
