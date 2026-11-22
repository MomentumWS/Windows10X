# Boot and application validation

[English overview](../README.md) | [中文验证记录](validation.zh-CN.md)

| Disk | Method | Result |
| --- | --- | --- |
| Original Flash.vhdx | Historical read-only inspection | The outer GPT contained BS_EFIESP and OSPool. The outer EFI partition contained EFI/Boot/BOOTX64.EFI. Mapping the inner MainOS and BCD remains open. |
| FlashGPT-BCD-GPT working disk | Generation 2 Hyper-V boot | The repaired disk reached the 10X desktop. The guest ran cmd and PowerShell 7.0.3. |
| FlashGPT-BCD-GPT-PreDebug.vhdx recovery copy | Read-only inspection | The six partitions matched the recorded layout. The archived BCD check found that the default loader’s device and osdevice referred to MainOS, with matching BSP and OSData bindings. |

On 2026-11-22 the recovery copy measured 3,795,845,120 bytes. Its SHA-256 matched the inventory and its primary and backup GPTs passed verification. A working disk changes as the guest writes to it. Compare file hashes for the specific copy under test. [Image inventory](../evidence/images.json)

## Application and migration tests

- **VMware.** Restoring just EFIESP and MainOS produced a loader screen naming winload.efi with status 0xc000000e. A separate VMware run reached BAD_SYSTEM_CONFIG_INFO (0x74). The cause of that stop remains undetermined.
- **Console EXEs.** Guest event 3033 recorded a signing-level failure for Win32Probe. Another console test recorded 0xc0000428 during image-section creation. Identifying the specific rule for that second test requires evidence tied to the same launch.
- **GUI.** Directly launching the executable inside the preinstalled browser package produced error 0x78 while registering a message-only window class. An independent GUI program still needs to demonstrate a visible window, input handling and a normal exit.
- **Argon / VAIL.** The guest contained a VailArgonCommon manifest. CmService, hns and vmcompute were running during one observation, while container enumeration returned an empty list. A successful Win32 launch through a named container has yet to be demonstrated.
