# BIOS Tunning

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
- [x] OS type: UEFI Mode Others
- [x] DVMT Pre-Allocated(iGPU Memory): 64MB
- [x] SATA Mode: AHCI
