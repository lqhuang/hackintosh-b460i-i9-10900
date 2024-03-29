# OpenCore config.plist

## ACPI

### Add

- SSDT-PLUG: Allows for native CPU power management on Haswell and newer
- SSDT-EC-USBX-DESKTOP: For Skylake desktops and newer and AMD CPU based systems
- SSDT-AWAC: This is the 300 series RTC patch (opens new window), required for
  all B460 and Z490 boards which prevent systems from booting macOS.
  - Here we use `SSDT-AWAC-DISABLE` from `OpenCorePkg`
- SSDT-RHUB: Needed to fix Root-device errors on Asus and potentially MSI
  boards.
- SSDT-SBUS-MCHC: Fixing AppleSMBus support in macOS, which mainly handles the
  System Management Bus. (Manually compiled required:
  https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus-methods/manual.html)

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

Device ID: 0x9BC5

```
0x9BC5 -> 00 00 9B C5

00 00 9B C5 -> C5 9B 00 00

C59B0000 = device-id
```

References:

1. [Intel iGPU Patching](https://dortania.github.io/OpenCore-Post-Install/gpu-patching/intel-patching/#device-id-explainer)
2. [Intel® Core™ i9-10900 Processor](https://ark.intel.com/content/www/us/en/ark/products/199328/intel-core-i910900-processor-20m-cache-up-to-5-20-ghz.html)

### PCI-E GPU

1. [macOS 12.3 更新後，導致部分 PCI-E GPU 用戶出現問題](https://www.imacpc.net/archives/4642)

### Audio

device-id

## Kernel

### Add

Must haves

- Lilu
  - A kext to patch many processes, required for AppleALC, WhateverGreen,
    VirtualSMC and many other kexts. Without Lilu, they will not work.
- VirtualSMC
  - Emulates the SMC chip found on real macs, without this macOS will not boot.

VirtualSMC Plugins

- SMCProcessor
  - Used for monitoring CPU temperature, doesn't work on AMD CPU based systems
- SMCSuperIO
  - Used for monitoring fan speed, doesn't work on AMD CPU based systems
- CPU Friend
  - Dynamic macOS CPU power management data injection
- [RadeonSensor](https://github.com/aluveitie/RadeonSensor)
  - Required to read the GPU temperature and requires Lilu
- [SMCRadeonGPU](https://github.com/aluveitie/RadeonSensor)
  - Can be used optionally to export GPU temperature to VirtualSMC for
    monitoring tools to read and requires VirtualSMC

Graphics

- WhateverGreen (Required)
  - Used for graphics patching, DRM fixes, board ID checks, framebuffer fixes,
    etc; all GPUs benefit from this kext.

Audio

- AppleALC
  - Used for AppleHDA patching, allowing support for the majority of on-board
    sound controllers

Ethernet

- IntelMausi
  - Required for the majority of Intel NICs
  - Intel's 82578, 82579, I217, I218 and I219 NICs are officially supported

WiFi and Bluetooth

- AirportBrcmFixup
  - An open source kernel extension providing a set of patches required for
    non-native Airport Broadcom Wi-Fi cards.
- BrcmFirmwareData
  - For BCM4360 bluethooth firmware
- BlueToolFixup
  - Fix bluethooth network stack for Monterey (macOS 12)

Extras

- NVMeFix
  - Used for fixing power management and initialization on non-Apple NVMe

### Quirks

- [ ] AppleXcpmCfgLock
  - Only needed when CFG-Lock can't be disabled in BIOS
  - Only applicable for Haswell and newer
- [x] DisableIoMapper
  - Needed to get around VT-D if either unable to disable in BIOS or needed for
    other operating systems
- [x] DisableLinkeditJettison
  - Allows Lilu and others to have more reliable performance without
    `keepsyms=1`
- [x] PanicNoKextDump
  - Allows for reading kernel panics logs when kernel panics occur
- [x] PowerTimeoutKernelPanic: YES
  - Helps fix kernel panics relating to power changes with Apple drivers in
    macOS Catalina, most notably with digital audio.
- SetApfsTrimTimeout: -1
  - Sets trim timeout in microseconds for APFS filesystems on SSDs, only
    applicable for macOS 10.14 and newer with problematic SSDs.

References:

1. [Fixing CFG Lock](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html)
2. [主機板解放 CFG LOCK 的教程(OC 篇)](https://www.imacpc.net/archives/1888)

## Misc

### Security

- ExposeSensitiveData:
  - `3` 将 OpenCore 的启动路径和版本储存进 NVRAM
  - `11` 在 `3` 的基础上添加主板 OEM 信息, `HWMonitorSMC2` 和 `NVMeFix` 需要主板
    OEM 信息才能正常工作
- SecureBootModel: Default
  - Apple 安全启动的机型。
  - Leave this as `Default` if running macOS Big Sur or newer.
  - 驱动程序缓存的列表可能不同，因此需要改变 `Add` 或 `Force` 内核驱动程序列表。
    比如，在这种情况下 `IO80211Family` 不能被注入。

## NVRAM

- 7C436110-AB2A-4BBB-A880-FE41995C9F82
  - `boot-args`:
    - `-v`: This enables verbose mode
    - `agdpmod=pikera`: Used for disabling board ID checks on Navi GPUs (RX 5000
      & 6000 series), without this you'll get a black screen. **Don't use if you
      don't have Navi** (ie. Polaris and Vega cards shouldn't use this)
  - `csr-active-config`: set to `00000000`
    - SIP enabled: `00000000`
    - macOS 10.13: `FF030000`
    - macOS 10.14/10.15: `FF070000`
    - macOS 11: `67080000`
    - macOS 12: `EF0F0000`

### Delete

- LegacyEnable: NO
  - Allows for NVRAM to be stored on nvram.plist, needed for systems without
    native NVRAM
- LegacyOverwrite: NO
  - Permits overwriting firmware variables from nvram.plist, only needed for
    systems without native NVRAM
- LegacySchema
  - Used for assigning NVRAM variables, used with LegacyEnable set to YES
- WriteFlash: YES
  - Enables writing to flash memory for all added variables.

## PlatformInfo

## UEFI

### Serial number

Choose correct and closest `SMBIOS` info depended on your hardware

| SMBIOS   | Hardware                                  |
| -------- | ----------------------------------------- |
| iMac20,1 | i7-10700K and lower(ie. 8 core and lower) |
| iMac20,2 | i9-10850K and higher(ie. 10 core)         |

Must required fields for iServices:

- `SystemProductName`
- `SystemSerialNumber`
- `MLB`
- `ROM` (mac address of NIC device)
- `SystemUUID`

**Note**: Reminder that you want either an invalid serial or valid serial
numbers but those not in use, you want to get a message back like: "Invalid
Serial" or "Purchase Date not Validated"

### Generic

- AdviseFeatures: NO
  - Used for when the EFI partition isn't first on the Windows drive
- MaxBIOSVersion: NO
  - Sets BIOS version to Max to avoid firmware updates in Big Sur+, mainly
    applicable for genuine Macs.
- ProcessorType: 0
  - Set to 0 for automatic type detection, however this value can be overridden
    if desired. See AppleSmBios.h
  - (opens new window) for possible values
- SpoofVendor: YES
  - Swaps vendor field for Acidanthera, generally not safe to use Apple as a
    vendor in most case
- SystemMemoryStatus: Auto
  - Sets whether memory is soldered or not in SMBIOS info, purely cosmetic and
    so we recommend `Auto`
- UpdateDataHub: YES
  - Update Data Hub fields
- UpdateNVRAM: YES
  - Update NVRAM fields
- UpdateSMBIOS: YES
  - Updates SMBIOS fields
- UpdateSMBIOSMode: Create
  - Replace the tables with newly allocated EfiReservedMemoryType, use `Custom`
    on Dell laptops requiring `CustomSMBIOSGuid` quirk
  - Setting to `Custom` with `CustomSMBIOSGuid` quirk enabled can also disable
    SMBIOS injection into "non-Apple" OSes however we do not endorse this method
    as it breaks Bootcamp compatibility. Use at your own risk

### APFS

- MinDate: 0
  - APFS 驱动的版本号基于其发布日期。使用默认数值。随着未来的更新，OpenCore 内置
    的默认数值也会不断更新。
- MinVersion: 0
  - APFS 驱动的版本号和 macOS 版本相关。使用默认数值。随着未来的更新，OpenCore
    内置的默认数值也会不断更新。

### Audio

- PlayChime: Auto
  - 开机时播放 Mac 特有的风铃的声音。

> 如果要让 Duang 和 VoiceOver 等其它音频功能工作, 需要额外下载语音资源包并放置于
> `ESP(分区)/EFI/OC/Resources/Audio` 文件夹下。
>
> 同时 `AudioDxe` 也需要安装在 Drivers 文件夹中并通过 config 注入

### Quirks

- [x] EnableVectorAcceleration
- [x] RequestBootVarRouting
