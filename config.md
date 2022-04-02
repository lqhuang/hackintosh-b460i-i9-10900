# OpenCore config.plist

## ACPI

Must required:

- SSDT-PLUG: Allows for native CPU power management on Haswell and newer
- SSDT-EC-USBX-DESKTOP: For Skylake desktops and newer and AMD CPU based systems
- SSDT-AWAC: This is the 300 series RTC patch (opens new window), required for all B460 and Z490 boards which prevent systems from booting macOS.
  - Here we use `SSDT-AWAC-DISABLE` from `OpenCorePkg`
- SSDT-RHUB: Needed to fix Root-device errors on Asus and potentially MSI boards.
- SSDT-SBUS-MCHC: Fixing AppleSMBus support in macOS, which mainly handles the System Management Bus. (Manually compiled required: https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus-methods/manual.html)
