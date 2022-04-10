# Post Installation

## First glance

- [x] BT cannot open, Address: Null
  - Fixed: Use `IntelBluetoothFirmware.kext` and `BlueToolFixup.kext`

- [x] Wi-Fi 802.11ax no
- [x] GPU driver: Unusable while minimizing / Maximizing Windows; Can not recognize GPU vendor
  - Fixed: 1. Switch RX 6500 XT to RX 6600; 2. Add `DeviceProperties` PCI description 
- [x] NVMe SSD Trim: not found
  - Fixed: Use Trim Patch from Hackintools
- [x] No Audio device found
  - Fixed: Work well after solving GPU driver
- Monitor cannot sleep off (windows frezon, but require login after waking up)
  - Fixed: Work well after solving GPU driver
- [ ] System Integrity Protection: Disabled

## Bluetooth

With Monterey, Apple has completely rewritten the bluetooth stack. Add kernel `IntelBluetoothFirmware.kext` and `BlueToolFixup.kext` for intel ax200 under Monterey 

References:

- https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth
- https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases
- https://github.com/acidanthera/BrcmPatchRAM

## Testing Hardware Acceleration and Decoding

So before we can get started with fixing DRM, we need to make sure your hardware is working. The best way to check is by running [VDADecoderChecker](https://i.applelife.ru/2019/05/451893_10.12_VDADecoderChecker.zip):

My output:

```
Hardware acceleration is fully supported
```

## Setting up Boot-chime with AudioDxe

### Pre-required resources

- [AudioDxe](https://github.com/acidanthera/OpenCorePkg/releases) in both EFI/OC/Drivers and UEFI -> Drivers
- [Binary Resources](https://github.com/acidanthera/OcBinaryData)
  - Add the Resources folder to EFI/OC, just like we did with the OpenCore GUI section
  - For those running out of space, `OCEFIAudio_VoiceOver_Boot.wav` is all that's required for the Boot-Chime

### Settings up NVRAM

- NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82:
  - Set `SystemAudioVolume | Data | 0x46`

### Setting up UEFI -> Audio

- AudioCodec (Codec address of Audio controller)
  - To find ourselves:
    - Check [IORegistryExplorer](https://github.com/khronokernel/IORegistryClone/blob/master/ioreg-302.zip) -> HDEF -> AppleHDAController -> IOHDACodecDevice and see the IOHDACodecAddress property
- Audio Device (PciRoot of audio controller)
  - Run [gfxutil](https://github.com/acidanthera/gfxutil/releases) to find the path
  - `gfxutil -f HDEF` -> `PciRoot(0x0)/Pci(0x1F,0x3)`
- AudioOut:
  - Undone
- AudioSupport: set this to `True`
- PlayChime: set this to `Enabled` (We use `Auto` here)
