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

- Fast boot
- VT-d
- CSM
- Intel SGX

Enable:

- VT-x
- Hyper Threading
- OS type: other system

## Credits

- [OpenCore-Install-Guide]https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html
- [sqlsec/AsRock-Z490-Steel-Legend-i7-10700](https://github.com/sqlsec/AsRock-Z490-Steel-Legend-i7-10700)
