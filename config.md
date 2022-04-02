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


