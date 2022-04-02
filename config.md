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

## DP

### iGPU

UHD630

Headless model

CML -> Comet Lake

Empty framebuffer (CML):

    0x9BC80003 (default)

### Audio

device-id

## UEFI

### Audio

> 如果要让 Duang 和 VoiceOver 等其它音频功能工作, 需要额外下载语音资源包并放置于 `ESP(分区)/EFI/OC/Resources/Audio` 文件夹下。
>
> 同时 `AudioDxe` 也需要安装在 Drivers 文件夹中并通过 config 注入

### Output

Ref:

https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md
https://github.com/acidanthera/AppleALC/wiki/Supported-codecs
