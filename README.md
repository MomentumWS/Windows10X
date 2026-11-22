# Windows 10X deployment and Win32 compatibility

**English** | [简体中文](README.zh-CN.md)

This project investigates whether Windows 10X build 20279.1002 x64 can be deployed from WinPE to a UEFI PC with its original shell, and which traditional Windows applications can run on it.

**Boot milestone:** A rebuilt GPT disk reached the 10X desktop in a Generation 2 Hyper-V VM after its boot configuration was repaired. First boot and restart on physical hardware remain open tests.

## Build configuration

The ImageApp log dated 2026-10-24 records a completed AMD64 image build with the OEM input below. The files preserve their original bytes, including comments and build-machine paths.

| Published file | Build artifact name | What it records |
| --- | --- | --- |
| [OEMInput.xml](config/OEMInput.xml) | `OEMInput.generated.xml` | ModernPC product, plain GPT layout, selected features and references to feature manifests under the original build machine’s D: drive. |
| [DeviceLayout.xml](config/DeviceLayout.xml) | `FlashGPT.DeviceLayout.xml` | Order, filesystem and sizing rules for EFIESP, MainOS, BSP, PreInstalled, OSData and Data. |
| [DriveLetterConfig.xml](config/DriveLetterConfig.xml) | `FlashGPT.DriveLetterConfig.xml` | MainOS maps to M: and Data to C: in the guest. WinPE can assign different letters. |

To check a downloaded XML file against the archived build artifact, compare its SHA-256 digest with the [checksum list](config/SHA256SUMS).

## Research progress

| Area | Observed | Next test |
| --- | --- | --- |
| Boot | The original disk exposed an EFI partition and a storage pool. The six-partition recovery copy had matching BCD bindings for MainOS, BSP and OSData. | Deploy to a blank disk from WinPE and record first boot and restart on a physical UEFI PC. |
| VMware | One two-partition restore failed at the loader. A separate test reached kernel stop 0x74. | Capture the stop parameters and stack with a fixed disk image and storage controller. |
| Win32 applications | Built-in cmd and PowerShell ran. A standalone console test encountered code integrity validation. Launching the bundled browser executable directly encountered a window API error. | Verify an independent console program’s output and exit code, then a GUI window that accepts input. MSI installation remains unverified. |
| Argon / VAIL | Container components and running services were observed. One inventory query returned no containers. | Correlate an application’s output with a specific container instance. |

See the [validation record](docs/validation.md) for the evidence behind each result and the [image inventory](evidence/images.json) for image sizes, layouts and hashes.
