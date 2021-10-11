# Hackintosh guide for Intel Kaby Lake desktop CPUs

This is a guide for anyone want to install macOS on non-Apple hardware. This guide is based on [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/).

- [EFI](https://github.com/Pangorin/EFI-Hackintosh/tree/main/EFI): Copy this into the USB boot drive (You should not copy my EFI for your build, unless you have the same specs as my build. My EFI is just a reference for other builds!)


![Image spec](https://user-images.githubusercontent.com/68218885/136784825-3b5b0e4c-83e0-4d81-b859-46cbb5d254fd.png)

## About my build
  - CPU: Intel Core i3-7100
  - Mainboard: ASRock H110M-DVS R2.0
  - RAM (or memory): G.SKILL Ripjaws V 8GB DDR4-2800
  - Storage (macOS): Samsung 850 EVO 120GB
  - Storage (Windows): Western Digital Green 120GB
  - Video card: Intel HD 630 (aka the iGPU)
  - Power Supply: Cooler Master Elite V3 PC400 400W

As noted above, if you want to use my EFI, you just care about the mainboard and the video card. If those 2 things match my build, you can use my EFI!

- If you want to use a graphics card (or check that if your graphics card will be supported on macOS), read this. [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
## Installation

How to make the USB boot drive:
  - Windows: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html)
  - macOS: [click here](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html) (this guide is mainly using for hackintosh the Mac which can't run the new macOS. Though, you can still using this method to make a boot drive for desktop build)
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
    * XHCI-unsupported.kext (only ASRock mainboard and specific mainboard need this kext, please read the guide to see if your mainboard need that)
    * USBMap.kext (IF YOU HAVEN'T MAP YOUR USB, DON'T USE THIS KEXT)
    * USBInjectAll (required when installing macOS)
  
  - SSDT:
    * SSDT-PLUG-DRTNIA.aml
    * SSDT-EC-USBX-DESKTOP.aml

## OpenCore Configuration

Follow the OpenCore Install Guide to [setup the config.plist](https://dortania.github.io/OpenCore-Install-Guide/config.plist/) and [configure for Intel Kaby Lake Desktop](https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html#starting-point).

> Note: If you have an unsupported GPU, you must patch the iGPU in order to have signal when booting into macOS, and make sure that you have to disable the unsupported GPU by SSDT or -wegnoegpu boot-args
> - About the iGPU patching, if you have issue when booting into macOS Installer like black screen, no monitor signal after verbose, make sure you add the `igfxonln=1` in the boot-args and REMEMBER, macOS DOESN'T SUPPORT VGA PORT!
> - If you have no HDMI audio, please find the `layout-id` that fits your build. 
> - Where to find my `layout-id`? [AppleALC Supported Codecs](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs).


| SMBIOS     | Description   |
| ---------- | --------------|
| `iMac18,1` | Used for computers utilizing the iGPU for displaying |
| `iMac18,3` | Used for computers using a dGPU for displaying, and an iGPU for computing tasks only |

Make sure you choose the right SMBIOS for your build, otherwise it can't be booting up with monitor signal!

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
