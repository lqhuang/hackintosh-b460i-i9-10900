# Post Installation

## First glance

- [x] Bluetooth cannot open, Address: no found
  - Fixed: Use `IntelBluetoothFirmware.kext` and `BlueToolFixup.kext`
- [x] Wi-Fi 802.11ax no found
- [x] GPU driver: Unusable while minimizing / Maximizing Windows; Can not
      recognize GPU vendor
  - Fixed: 1. Switch RX 6500 XT to RX 6600; 2. Add `DeviceProperties` PCI
    description
- [x] NVMe SSD Trim: not found
  - Fixed: Use Trim Patch from Hackintools
- [x] No Audio device found
  - Fixed: Work well after solving GPU driver
- [x] Monitor cannot sleep off (windows frezon, but require login after waking
      up)
  - Fixed: Work well after solving GPU driver
- [x] System Integrity Protection: Disabled
  - Fixed: adjust `csr-active-config` variable in NVRAM section

## Bluetooth

With Monterey, Apple has completely rewritten the bluetooth stack. Add kernel
`IntelBluetoothFirmware.kext` and `BlueToolFixup.kext` for intel ax200 under
Monterey

References:

- https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth
- https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases
- https://github.com/acidanthera/BrcmPatchRAM

## Testing Hardware Acceleration and Decoding

So before we can get started with fixing DRM, we need to make sure your hardware
is working. The best way to check is by running
[VDADecoderChecker](https://i.applelife.ru/2019/05/451893_10.12_VDADecoderChecker.zip):

My output:

```
Hardware acceleration is fully supported
```

## Setting up Boot-chime with AudioDxe

### Pre-required resources

- [AudioDxe](https://github.com/acidanthera/OpenCorePkg/releases) in both
  EFI/OC/Drivers and UEFI -> Drivers
- [Binary Resources](https://github.com/acidanthera/OcBinaryData)
  - Add the Resources folder to EFI/OC, just like we did with the OpenCore GUI
    section
  - For those running out of space, `OCEFIAudio_VoiceOver_Boot.wav` is all
    that's required for the Boot-Chime

### Settings up NVRAM

- NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82:
  - Set `SystemAudioVolume | Data | 0x46`

### Setting up UEFI -> Audio

- AudioCodec (Codec address of Audio controller)
  - To find ourselves:
    - Check
      [IORegistryExplorer](https://github.com/khronokernel/IORegistryClone/blob/master/ioreg-302.zip)
      -> HDEF -> AppleHDAController -> IOHDACodecDevice and see the
      IOHDACodecAddress property
- Audio Device (PciRoot of audio controller)
  - Run [gfxutil](https://github.com/acidanthera/gfxutil/releases) to find the
    path
  - `gfxutil -f HDEF` -> `PciRoot(0x0)/Pci(0x1F,0x3)`
- AudioOut:
  - Undone
- AudioSupport: set this to `True`
- PlayChime: set this to `Enabled` (We use `Auto` here)

## Fixing SMBus support (SSDT-SBUS-MCHC)

References:

1. https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus-methods/manual.html
2. https://dortania.github.io/Getting-Started-With-ACPI/Manual/compile.html

## Fixing RTC write issues

Reference:

https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html

## No Volume/Brightness control on external monitors

Oddly enough, macOS has locked down digital audio from having control. To bring
back some functionality, the app
[MonitorControl](https://github.com/the0neyouseek/MonitorControl/releases) has
done great work on improving support in macOS

## System Integrity Protection (SIP)

Apple has provided numerous configuration options in the NVRAM variable
`csr-active-config` which can either be set in the macOS recovery environment or
with OpenCore's NVRAM section.

- `00000000` - SIP completely enabled (0x0).
- `03000000` - Disable kext signing (0x1) and filesystem protections (0x2).
