# OpenCore config.plist

## ACPI

### Add

- SSDT-PLUG: Allows for native CPU power management on Haswell and newer
- SSDT-EC-USBX-DESKTOP: For Skylake desktops and newer and AMD CPU based systems
- SSDT-AWAC: This is the 300 series RTC patch (opens new window), required for all B460 and Z490 boards which prevent systems from booting macOS.
  - Here we use `SSDT-AWAC-DISABLE` from `OpenCorePkg`
- SSDT-RHUB: Needed to fix Root-device errors on Asus and potentially MSI boards.
- SSDT-SBUS-MCHC: Fixing AppleSMBus support in macOS, which mainly handles the System Management Bus. (Manually compiled required: https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus-methods/manual.html)

### Quirks

- [x] ResetLogoStatus

## Booter

### Quirks

| Quirk              | Value | Commoent                                                                                                                                                                  |
| :----------------- | :---: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ResizeAppleGpuBars |  -1   | If your firmware supports increasing GPU Bar sizes (ie Resizable Bar Support), set this to 0. Note: B460i + RX 6500XT are fine to SAM (AMD's Resizable GPU Bar)           |
| SetupVirtualMap    |  NO   | Fixes SetVirtualAddresses calls to virtual addresses, however broken due to Comet Lake's memory protections. ASUS, Gigabyte and AsRock boards will not boot with this on. |

## DeviceProperties

Ref:

1. [iGPU PCI config](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)
2. [ALC device-id](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs)

### iGPU

UHD630

Headless model

CML -> Comet Lake

Empty framebuffer (CML):

    0x9BC80003 (default)

### Audio

device-id

## Kernel

## Misc

### Security

- ExposeSensitiveData:
  - `3` 将 OpenCore 的启动路径和版本储存进 NVRAM
  - `11` 在 `3` 的基础上添加主板 OEM 信息, `HWMonitorSMC2` 和 `NVMeFix` 需要主板 OEM 信息才能正常工作
- SecureBootModel: Default
  - Apple 安全启动的机型。
  - Leave this as `Default` if running macOS Big Sur or newer.
  - 驱动程序缓存的列表可能不同，因此需要改变 `Add` 或 `Force` 内核驱动程序列表。比如，在这种情况下 `IO80211Family` 不能被注入。

## NVRAM

## UEFI

## PlatformInfo

### APFS

- MinDate: 0
  - APFS 驱动的版本号基于其发布日期。使用默认数值。随着未来的更新，OpenCore 内置的默认数值也会不断更新。
- MinVersion: 0
  - APFS 驱动的版本号和 macOS 版本相关。使用默认数值。随着未来的更新，OpenCore 内置的默认数值也会不断更新。

### Audio

- PlayChime: Auto
  - 开机时播放 Mac 特有的风铃的声音。

> 如果要让 Duang 和 VoiceOver 等其它音频功能工作, 需要额外下载语音资源包并放置于 `ESP(分区)/EFI/OC/Resources/Audio` 文件夹下。
>
> 同时 `AudioDxe` 也需要安装在 Drivers 文件夹中并通过 config 注入

### Quirks

- [x] EnableVectorAcceleration
- [x] RequestBootVarRouting
