# Hackintosh EFI Config

## Hardware setup

- Mother board: Asus ROG Strix B460-I Gaming
- CPU: Intel(R) i9 10900 (Comet Lake)
- RAM: 2x16GB DDR4 2933Mhz Crucial Ballistix BL16G32C16U4W.16FE
- ~~GPU: Asus Tuf RX 6500 XT (AMD RDNA2 Navi 24)~~
- GPU: Sapphire RX 6600 (AMD RDNA2 Navi 23)
- SSD: WD Black SN770
- Ethernet: Intel(R) I219-V
- ~~Wireless & BT: Intel(R) Wi-Fi AX200~~
- Wireless & BT: Broadcom BCM4360
- Audio: Realtek(R) ALC1220P / ALC S1220A

Bad news:

Navi 24 (6400, 6500, 6500 XT) and Navi 22 (6700 XT) are not compatible in
Monterey!!

Available choices: Navi 23 (6600 and 6600 XT) and Navi 21 (6800, 6800 XT, 6900
XT)

Reference:
[Reddit - RX 6000 Series macOS Compatibility](https://www.reddit.com/r/hackintosh/comments/s357a3/rx_6000_series_macos_compatibility)

## System

- booter: OpenCore
- OS: macOS Monterey 12.0+

## My installation steps

1. [Tune BIOS settings](./bios.md)
2. [Custom OpenCore configuration](./config.md)
3. [Fixup in post installation](./post-installation.md)

Enjoy!

## Available features

- [x] macOS 12 Monterey
- [x] auto sleeping and normal wakeup
- [x] Wi-Fi & Bluethooth
- [x] AirDrop / Sidecar / Universal control
- [x] HEVC Decoder & Encoder
- [x] HiDPI for dual 4K monitors

## Performance tools

- [iStat Menus](https://bjango.com/mac/istatmenus)
- [Sensei](https://sensei.app)
- [Intel® Power Gadget](https://www.intel.com/content/www/us/en/developer/articles/tool/power-gadget.html)
- [VideoProc](https://www.videoproc.com/)
- [Geekbench 5](https://www.geekbench.com/)

## Credits

- [OpenCore-Install-Guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html)
- [sqlsec/AsRock-Z490-Steel-Legend-i7-10700](https://github.com/sqlsec/AsRock-Z490-Steel-Legend-i7-10700)
- [LightFocus/ASUS-ROG-STRIX-B460I-Hackintosh](https://github.com/LightFocus/ASUS-ROG-STRIX-B460I-Hackintosh)
- [yqlbu/asus-b460i-strix-oc-efi](https://github.com/yqlbu/asus-b460i-strix-oc-efi)
- [Hackintool 使用教程及插入姿势](https://blog.daliansky.net/Intel-FB-Patcher-tutorial-and-insertion-pose.html)
