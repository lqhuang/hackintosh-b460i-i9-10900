# Hackintosh EFI Config

## Setup

- mother board: Asus ROG Strix B460-I Gaming
- CPU: Intel(R) i9 10900 (Comet Lake)
- RAM: 2x16GB DDR4 2933Mhz Crucial Ballistix BL16G32C16U4W.16FE
- GPU: Asus Tuf 6500XT OC (AMD RDNA2 Navi 2x)
- SSD: WD Black SN770
- Ethernet: Intel(R) I219-V
- Wireless & BT: Intel(R) Wi-Fi AX200
- Audio: Realtek(R) ALC1220P / ALC S1220A

## System

- booter: OpenCore
- OS: macOS Monterey 12.0+

## BIOS Tunning

Disable:

- [x] Fast Boot
- [x] Secure Boot
  - Require to clear platform key (PK) in BIOS settings
- Serial/COM Port (no need)
- Parallel Port (no need)
- [x] VT-d
- [x] CSM
- Thunderbolt (no need)
  - For initial install, as Thunderbolt can cause issues if not setup correctly
- [x] Intel SGX
- [x] Intel Platform Trust Technology (PCH-FW -> PTT)
- CFG Lock (default in UNLOCK state for this board)
  - (MSR 0xE2 write protection) This must be off, if you can't find the option
    then enable `AppleXcpmCfgLock` under Kernel -> Quirks. Your hack will not
    boot with CFG-Lock enabled.

Enable:

- [x] VT-x (CPU Configuration -> Intel VMX)
- [x] Above 4G decoding (Advanced -> PCI Subsystem Configuration -> Above 4G
      decoding)
  - 2020+ BIOS Notes: When enabling Above4G, Resizable BAR Support may become an
    available on some Z490 and newer motherboards. Please ensure that Booter ->
    Quirks -> `ResizeAppleGpuBars` is set to 0 if this is enabled.
- [x] Hyper-Threading
- Execute Disable Bit (no found in BIOS settings)
- [x] EHCI/XHCI Hand-off (Advanced -> USB)
- [x] OS type: Windows 8.1/10 UEFI Mode
- [x] DVMT Pre-Allocated(iGPU Memory): 64MB
- [x] SATA Mode: AHCI

## Credits

- [OpenCore-Install-Guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html)
- [sqlsec/AsRock-Z490-Steel-Legend-i7-10700](https://github.com/sqlsec/AsRock-Z490-Steel-Legend-i7-10700)
- [LightFocus/ASUS-ROG-STRIX-B460I-Hackintosh](https://github.com/LightFocus/ASUS-ROG-STRIX-B460I-Hackintosh)
- [yqlbu/asus-b460i-strix-oc-efi](https://github.com/yqlbu/asus-b460i-strix-oc-efi)
