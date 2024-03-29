# Post Installation

## First glance

- [x] Bluetooth cannot open, Address: no found
  - Fixed: Use `IntelBluetoothFirmware.kext` and `BlueToolFixup.kext`
- [x] Wi-Fi 802.11ax no found
  - Fixed: Use `IntelBluetoothFirmware.kext` and `BlueToolFixup.kext`
- [x] GPU driver: Unusable while minimizing / Maximizing Windows; Can not
      recognize GPU vendor
  - Fixed: 1. Switch RX 6500 XT to RX 6600; 2. Add `DeviceProperties` PCI
    description
- [x] NVMe SSD Trim: not found
  - Fixed: Use Trim Patch from Hackintools
- [x] No Audio device found
  - Fixed: Work well after solving GPU issue
- [x] Monitor cannot sleep off (windows frezon, but require login after waking
      up)
  - Fixed: Work well after solving GPU issue
- [x] System Integrity Protection: Disabled
  - Fixed: adjust `csr-active-config` variable in NVRAM section

## (Enforced) Black screen after startup on Navi GPU

- Add `agdpmod=pikera` to boot args
- Try to remove `-v` in boot args, it may conflict to `agdpmod=pikera` (don't
  know why yet)
- ~~Append GPU configuration in DeviceProperties section~~

Reference:
<https://www.tonymacx86.com/threads/success-xfx-rx-6600-xt-graphics-card-in-monterey-12-2-1.319128/>

## (Enforced) Bluetooth

With Monterey, Apple has completely rewritten the bluetooth stack. Add kernel
`IntelBluetoothFirmware.kext` and `BlueToolFixup.kext` for intel ax200 under
Monterey

References:

- https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth
- https://github.com/OpenIntelWireless/IntelBluetoothFirmware/releases
- https://github.com/acidanthera/BrcmPatchRAM

## GPU patching and acceleration

### Testing Hardware Acceleration and Decoding

So before we can get started with fixing DRM, we need to make sure your hardware
is working. The best way to check is by running
[VDADecoderChecker](https://i.applelife.ru/2019/05/451893_10.12_VDADecoderChecker.zip):

My output:

```
Hardware acceleration is fully supported
```

### Test iGPU in headless mode

Refernces:

1. [iGPU in headless mode: How do I know if it's working?](https://www.reddit.com/r/hackintosh/comments/jkau4x/igpu_in_headless_mode_how_do_i_know_if_its_working/)

## (Optional) Setting up Boot-chime with AudioDxe

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

## (Enforced) Fixing RTC write issues

It's really a painful process :(

1. Add `RTCMemoryFixup.kexts`
2. Finding bad RTC region via `boot-arg`: `rtcfx_exclude=00-FF` in a binary
   search fashion
   - wish you a lucky dog!!!
   - **NOTE**: Reboot twice after updating protect regions is necessary to make
     sure updated value works well.
   - My exactly mached area is `58-59`
3. Add permanent RTC fixup blacklist to `NVRAM` -> `Add` ->
   `4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102` -> `rtc-blacklist`
   - add our bad RTC region as an array, so `rtcfx_exclude=85-86` will become
     `rtc-blacklist | Data | 8586`

Reference:

1. https://dortania.github.io/OpenCore-Post-Install/misc/rtc.html
2. https://github.com/acidanthera/RTCMemoryFixup
3. https://blog.xjn819.com/post/rtc-issues-related-to-oc.html

## (Optional) No Volume/Brightness control on external monitors

Oddly enough, macOS has locked down digital audio from having control. To bring
back some functionality, the app
[MonitorControl](https://github.com/the0neyouseek/MonitorControl/releases) has
done great work on improving support in macOS

## (Suggestive) System Integrity Protection (SIP)

Apple has provided numerous configuration options in the NVRAM variable
`csr-active-config` which can either be set in the macOS recovery environment or
with OpenCore's NVRAM section.

- `00000000` - SIP completely enabled (0x0).
- `03000000` - Disable kext signing (0x1) and filesystem protections (0x2).

## (Suggestive) Optimizing Power Management

So before we can fine tune power management to our liking, we need to first make
sure Apple's XCPM core is loaded. Note that this is supported only on **Haswell
and newer**.

Reference: <https://dortania.github.io/OpenCore-Post-Install/universal/pm.html>

### Enabling X86PlatformPlugin

To start, grab
[IORegistryExplorer](https://github.com/khronokernel/IORegistryClone/blob/master/ioreg-302.zip)
and look for `AppleACPICPU` to check it works well or not.

### Using CPU Friend

To start, we're gonna need a couple things:

- `X86PlatformPlugin` loaded
- CPUFriend
- [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)

Download `CPUFriendFriend` and Run `CPUFriendFriend.command`

#### LFM: Low Frequency Mode

When you first open up CPUFriendFriend, you'll be greeted with a prompt for
choosing your LFM value. This can be seen as the floor of your CPU, or the
lowest value it'll idle at. This value can greatly help with sleep functioning
correctly as macOS needs to be able to transition from S3(sleep) to S0(wake)
easily.

Note that the LFM value is simply the CPU's multiplier, so you'll need to trim
your value appropriately

Here we use `0A` as LFM Value for i9 10900 (28 \* 1000Mhz = 2.8GHz base
frequency)

#### EPP: Energy Performance Preference

Next up is the Energy Performance Preference, EPP. This tells macOS how fast to
turbo up the CPU to its full clock. 00 will tell macOS to let the CPU go as fast
as it can as quickly as it can while FF will tell macOS to take things slowly
and let the CPU ramp up over a much longer period of time. Depending on what
you're doing and the cooling on your machine, you may want to set something in
the middle. Below chart can help out a bit:

| EPP       | Speed               |
| :-------- | :------------------ |
| 0x00-0x3F | Max Performance     |
| 0x40-0x7F | Balance performance |
| 0x80-0xBF | Balance power       |
| 0xC0-0xFF | Max Power Saving    |

We set to `00` here for desktop platform (same to modern iMac).

#### Performance Bias

Perf Bias is a register on many modern Intel Processors which sets a policy
preference for performance vs energy savings. Perf Bias is a configurable dial
ranging from 0 (Performance) to 15 (Energy Savings). The processor utilizes perf
bias to help influence how the processor utilizes C and P states.

We use `01` for this value (same to modern iMac).

#### Cleanup

After compiled, some result files were provided:

- CPUFriendDataProvider.kext
- Mac-AF89B6D9451A490B.plist (Tunned X86PlatformPlugin configuration, has been
  embedded in `CPUFriendDataProvider.kext`)
- ssdt_data.aml
- ssdt_data.dsl

These could be used in your `config.plist`

If you do choose to use `ssdt_data.aml`, note that `SSDT-PLUG` is no longer
needed. However the setup for this SSDT is broken on HEDT platforms like X99 and
X299, so dortania highly recommend `SSDT-PLUG` with `CPUFriendDataProvider.kext`
instead.

## (Suggestive) Fixing SMBus support (SSDT-SBUS-MCHC)

We are lucky! Our ACPI path (`/PCI01@0/SBUS@1F,4`) of serial bus controller
(SMBus device) in mother board is totally the same to the default setup.

So we just use precompiled `SSDT-SBUS-MCHC.aml` file from OpenCore release
without custom and recompiling. Check references if you need to recompile.

References:

1. https://dortania.github.io/Getting-Started-With-ACPI/Universal/smbus-methods/manual.html
2. https://dortania.github.io/Getting-Started-With-ACPI/Manual/compile.html

## (Suggestive) Check sleep issue

To find which devices have affectd your suspend/resume process:

```
log show --last 7d | grep "Wake reason"
```

or

```
pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"
```

1. [GPRW/UPRW/LANC Instant Wake Patch](https://dortania.github.io/OpenCore-Post-Install/usb/misc/instant-wake.html)
2. [使用 OpenCore 引导黑苹果 - 3.7 睡眠即醒的相关问题](https://blog.xjn819.com/post/opencore-guide.html)

## (Enforced) USB Fixes

Why you need to remap USB port:

- macOS is very bad at guessing what kind of ports you have
- Some ports may run below their rated speed(3.1 ports running at 2.0)
- Bluetooth not working
- Certain services like Handoff may not work correctly
- Sleep may break
- Broken Hot-Plug

Do Intel USB mapping: Our hardware is quite newer, so
[renaming](https://dortania.github.io/OpenCore-Post-Install/usb/system-preparation.html#checking-what-renames-you-need)
is not necessary. Just jump to USB mapping section directly.

References:

1. https://dortania.github.io/OpenCore-Post-Install/usb
2. https://github.com/corpnewt/USBMap
3. https://apple.sqlsec.com/6-实用姿势/6-1.html

## (Suggestive) Fixing iMessage and other services with OpenCore

The following items will be created below and are required to use iServices:

- MLB
- ROM
- SystemProductName
- SystemSerialNumber
- SystemUUID

For `ROM`, use the MAC Address of the network interface, lowercase, and without
`:`.

References:

1. https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html

## (Suggestive) Fixing BCM4360 wireless driver

Since BCM4360 series wireless card is natively supported by macOS, we have to
block/remove `AirPortBrcm4360_Injector.kext` plugins after macOS 11+. The
simplest way is just delete it under plugins folder

> In 11+ class AirPortBrcm4360 has been completely removed. Using of injector
> kext with such class name and matched vendor-id:device-id blocks loading of
> original airport kext. To address this issue and keep compatibility with older
> systems injectors for AirPortBrcm4360 and AirPortBrcmNIC were removed from
> main Info.plist file. Instead, the two new kext injectors are deployed in
> PlugIns folder: AirPortBrcm4360_Injector.kext and
> AirPortBrcmNIC_Injector.kext. **You have to block (or remove)
> AirPortBrcm4360_Injector.kext in 11+.** In OpenCore you can specify MaxKernel
> 19.9.9 for AirPortBrcm4360_Injector.kext. In Clover you can have two different
> AirportBrcmFixup.kext, but in kext folder with version name 11 and 12
> AirportBrcmFixup.kext must not contain AirPortBrcm4360_Injector.kext. You
> don't need these injectors at all if your vendor-id:device-id is natively
> supported by AirPortBrcmNIC or AirPortBrcm4360 (your device-id is included
> into Info.plist in these kexts).

References:

1. https://github.com/acidanthera/AirportBrcmFixup
2. https://www.tonymacx86.com/threads/hackintosh-big-sur-wifi-problem-with-fenvi-t919-bcm94360cd.308591/
3. https://www.insanelymac.com/forum/topic/350364-airportbrcmfixupbcm94360hmb-maybe-broken-in-monterey-121-and-122/
4. https://blog.daliansky.net/BCM94360Z4-m.2-NGFF-interface-four-antenna-notebook_small-host-dedicated-black-Apple-wireless-network-card-driver-tutorial.html
