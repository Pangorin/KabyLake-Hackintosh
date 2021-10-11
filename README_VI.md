# Hướng dẫn hackintosh cho chip Intel Kaby Lake

Đây là bài viết dành cho những ai muốn cài hệ điều hành macOS lên những máy tính không phải do Apple sản xuất.

- [EFI](https://github.com/Pangorin/KabyLake-Hackintosh/tree/main/EFI): Copy thư mục này vào USB boot (Không khuyến khích copy nguyên xi EFI, vì mỗi máy sẽ sử dụng những linh kiện khác nhau, chỉ trùng CPU, trừ khi máy bạn có cấu hình gần y nguyên như máy của tôi. Và EFI này chỉ nên sử dụng để tham khảo!)

![Image spec](https://user-images.githubusercontent.com/68218885/136784825-3b5b0e4c-83e0-4d81-b859-46cbb5d254fd.png)

## Cấu hình máy của tôi
  - CPU: Intel Core i3-7100
  - Mainboard: ASRock H110M-DVS R2.0
  - RAM : G.SKILL Ripjaws V 8GB DDR4-2800
  - Ổ cứng (macOS): Samsung 850 EVO 120GB
  - Ổ cứng (Windows): Western Digital Green 120GB
  - Card màn hình: Intel HD 630 (aka the iGPU)
  - Nguồn: Cooler Master Elite V3 PC400 400W

Như đã nói ở trên, trừ khi bạn có cấu hình y hệt của tôi thì bạn mới nên copy y nguyên EFI của tôi, nhưng chúng ta chỉ cần quan tâm đến giống nhau ở Mainboard và card màn hình, nếu cả 2 thứ nêu trên đều giống nhau thì bạn cũng có thể sử dụng EFI của tôi.

- LƯU Ý: CHECK CARD MÀN HÌNH RỜI CỦA MÌNH CÓ ĐUỢC HỖ TRỢ TRÊN macOS KHÔNG TRƯỚC KHI CÀI! 
  - Câu hỏi: làm cách nào để biết card màn hình của tôi có được hỗ trợ hay không?
    - Trả lời: [Huớng dẫn mua GPU phù hợp](https://dortania.github.io/GPU-Buyers-Guide/) (thường thì card màn hình của AMD sẽ được hỗ trợ)

## Cách cài đặt 

Cách tạo USB để boot vào bộ cài macOS:
  - Windows: [nhấn vào đây](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/winblows-install.html)
  - macOS: [nhấn vào đây](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/mac-install.html) (guide này mục đích chính là để hackintosh những máy Mac đời cũ không được hỗ trợ lên macOS mới, nhưng vẫn có thể dùng để tạo bộ cài cho cả desktop)
  - Linux: [nhấn vào đây](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/linux-install.html)

Setup bộ cài: [nhấn vào đây](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html).

Những file cần thiết để boot: [nhấn vào đây](https://dortania.github.io/OpenCore-Install-Guide/ktext.html).

Những drivers, kexts, và SSDT tôi sử dụng:
  - Driver:
    * HfsPlus.efi
    * OpenRuntime.efi (đã đi kèm trong bộ cài OpenCore)
  
  - Kext:
    * Lilu.kext
    * WhateverGreen.kext
    * VirtualSMC.kext
      * SMCProcessor.kext
      * SMCSuperIO.kext
    * USBInjectAll.kext
    * RealtekRTL8111.kext
    * XHCI-unsupported.kext (chỉ những main ASRock và 1 số loại main khác dùng kext này, vui lòng đọc guide để biết main mình có cần hay không)
    * USBMap.kext (NẾU CHƯA MAP USB, XIIN VUI LÒNG XÓA FILE NÀY)
    * USBInjectAll (cần thiết khi cài đặt macOS)
  
  - SSDT:
    * SSDT-PLUG-DRTNIA.aml
    * SSDT-EC-USBX-DESKTOP.aml

## Tinh chỉnh bộ cài OpenCore 
Làm theo hướng dẫn trong guide này để [chuẩn bị file config.plist](https://dortania.github.io/OpenCore-Install-Guide/config.plist/) và [config cho desktop Intel Kaby Lake](https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html#starting-point).

> Lưu ý: trong trường hợp bạn đang sử dụng 1 chiếc card màn hình không được hỗ trợ trên macOS (thường là card màn hình của hãng nVidia), bạn cần phải patch iGPU để có thể xuất hình, và hãy chắc chắn rằng bạn đã disable card rời bằng SSDT hoặc boot-args -wegnoegpu
> - Về phần patch iGPU, nếu bạn gặp phải lỗi đen màn hình hay không có tín hiệu khi đang boot vào bộ cài macOS, hãy thêm boot-args `igfxonln=1` và điều quan trọng, macOS KHÔNG HỖ TRỢ CỔNG VGA!
> - Nếu bạn không có âm thanh khi xuất hình bằng cổng HDMI, hãy tìm `layout-id` phù hợp với máy bạn. "Tôi có thể tìm layout-id phù hợp ở đâu?" [AppleALC Supported Codecs](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs).

| SMBIOS     | Description   |
| ---------- | --------------|
| `iMac18,1` | Xuất hình bằng iGPU |
| `iMac18,3` | Card màn hình rời xuất hình, và iGPU chỉ chạy nền để xử lí dữ liệu cùng card rời |

Hãy chọn đúng SMBIOS cho máy mình, nếu không sẽ không có tín hiệu màn hình khi boot vào bộ cài!

## Thiết lập BIOS (version P8.00)
- Disable
  * Fast Boot
  * Secure Boot
  * Serial/COM Port
  * Parallel Port
  * VT-d (can be enabled if you set DisableIoMapper to YES)
  * CSM
  * Intel Platform Trust
  * CFG Lock 
- Enable
  * VT-x
  * Hyper-Threading
  * XHCI Hand-off
  * OS type: Windows 8.1/10 UEFI Mode
  * SATA Mode: AHCI

(những cài đặt nào trong BIOS mà không tìm thấy ở đây chúng ta có thể bỏ qua)
