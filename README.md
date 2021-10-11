# How to build a EFI for Intel Kaby Lake desktop CPUs

This is a guide for anyone want to install macOS on non-Apple hardware. This guide is based on [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).

- [EFI](https://github.com/Pangorin/EFI-Hackintosh/tree/main/EFI): Copy this into the USB boot drive

![Image spec])

## About my build
  - CPU: Intel Core i3-7100
  - Mainboard: ASRock H110M-DVS R2.0
  - RAM (or memory): G.SKILL Ripjaws V 8GB DDR4-2800
  - Storage (macOS): Samsung 850 EVO 120GB
  - Storage (Windows): Western Digital Green 120GB
  - Video card: Intel HD 630 (aka the iGPU)
  - Power Supply: Cooler Master Elite V3 PC400 400W

## Installation

How to make the USB boot drive:
  - Windows: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html)
  - macOS: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html) (this guide is mainly using for hackintosh the Mac which can't run the new macOS)
  - Linux: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html)

Prepare the base OpenCore system: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html).

Necessary files to boot: [click here](https://dortania.github.io/OpenCore-Install-Guide/ktext.html).

The drivers, kexts, and SSDTs I used are as follows:
  - Driver:
    * HfsPlus.efi
    * OpenRuntime.efi (included with OpenCore)
  
  - Kext:
    * Lilu.kext
    * WhateverGreen.kext
    * VirtualSMC.kext
      * SMCProcessor.kext
      * SMCSuperIO.kext
    * USBInjectAll.kext
    * RealtekRTL8111.kext
    * XHCI-unsupported.kext
    * USBMap.kext (IF YOU HAVEN'T MAP YOUR USB, DON'T USE THIS KEXT)
    * USBInjectAll (required when installing macOS)
  
  - SSDT:
    * SSDT-PLUG-DRTNIA.aml
    * SSDT-EC-USBX-DESKTOP.aml

## OpenCore Configuration

Follow the OpenCore Install Guide to [setup the config.plist](https://dortania.github.io/OpenCore-Install-Guide/config.plist/) and [configure for Intel Kaby Lake Desktop](https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html#starting-point).

## BIOS Setting (version P8.00)
- Disable
  * Fast Boot
  * Secure Boot
  * Serial/COM Port
  * Parallel Port
  * VT-d (can be enabled if you set DisableIoMapper to YES)
  * CSM
  * Intel Platform Trust
  * CFG Lock (MSR 0xE2 write protection)(This must be off, if you can't find the option then enable AppleXcpmCfgLock under Kernel -> Quirks. Your hack will not boot with CFG-Lock enabled)
- Enable
  * VT-x
  * Hyper-Threading
  * XHCI Hand-off
  * OS type: Windows 8.1/10 UEFI Mode
  * SATA Mode: AHCI

(We can leave any setting that we cannot found)

About the iGPU patching, if you have issue when booting into macOS Installer like black screen, no signal after verbose, make sure you add the igfxonln=1 in the boot-args and REMEMBER, macOS DOESN'T SUPPORT VGA PORT!
