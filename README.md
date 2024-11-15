# Dell Latitude 5300 2-in-1
<br>

<img width="360" src="https://github.com/user-attachments/assets/449ce9b6-2cf9-44c6-8945-351bef1eb913">


# Hardware Specification

CPU : Intel® Core™ i7-8665U

GPU: Intel® UHD Graphics 620

RAM: Micron 16GB 2666Mhz x 2 (32GB)

Display: 13.3 FHD 1920 x 1080 IPS (Chi Mei: CMN1382)

Touchscreen: WCOM48E2 (Wacom) 

Wi-Fi/BT Card: BCM94360NG NGFF M.2 (Swapped from Intel® Dual Band Wireless AC 9560; However, it requires MHF4 cable extension)

SSD: SK Hynix PC 401 512GB (Both macOS and Windows on the same drive; M.2 SSD support via WWAN slot?)

Audio: ALC295/ALC3254 (Layout-ID 77, Headphone jack requires ALCPlugFix to be functional)

Left side ports: 

  - USB-C (USB 3.1 Gen 2 10Gbps; DisplayPort (4K60FPS); Thunderbolt port comes as an option)
  - HDMI 1.4b (Supports up to 4K30FPS)
  - USB-A (USB 3.1 Gen 1 5Gbps)
  - Smart card reader

Right side ports

  - USB-A (USB 3.1 Gen 1 5Gbps)
  - microSD card reader
  - Headset / Microphone (Universal Jack)
  - micro-SIM card (Optional)
  

# BIOS Settings

BIOS Revision: 1.32.0

SATA Operation: AHCI

Secure Boot Enable - > Disabled (Required)

Absolute® -> Disabled

SMM Security Mitigation -> Disabled

Intel AMT Capability -> Disabled

Intel Turbo boost -> Disabled for Windows 10 to reduce fan noise, sacrificing performance. (This option will be ignored in macOS however)

  ** Note that in order to boot macOS, only change required to make from its default setting is "Secure Boot Enable" **

# Grubshell BIOS Mod

These modifications in BIOS are not required to boot macOS at all and can be ignored, but is done to enable features that are otherwise would not be available. 

To change below BIOS settings, [ModGrubShell](https://github.com/datasone/grub-mod-setup_var/releases) is required

For Dell BIOS extraction to find offset, please refer to Dreamwhite's [guide](https://github.com/dreamwhite/bios-extraction-guide).


BIOS Version: 1.32.0


- Disable CFG Lock -> (offset 0x6ED)

       setup_var 0x6ED 0x0      (This will disable CFG Lock)
       setup_var 0x6ED 0x1      (Back to default)

- Set DVMT Pre-allocated to 64MB -> (offset 0xA10)

        setup_var 0xA10 0x2     (This will set DVMT to 64MB)
        setup_var 0xA10 0x1     (Back to default)
  
- Enable USB Wake Support -> (offset 0x149A)

        setup_var 0x149A 0x1    (This will allow system to wake via USB peripheral devices(Keyboard/Mouse))
        setup_var 0x149A 0x0    (Back to default)

     <img width="1000" alt="385414764-d766416a-0fa6-447d-8931-173c06ec11fb" src="https://github.com/user-attachments/assets/b2b161bc-35fc-4163-9964-87a3e3e92569">




Note: 

  - [Why disable CFG Lock?](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#what-is-cfg-lock)
  - Once the DVMT is set to 64MB, make sure to remove framebuffer-stolenmem property from the config.plist as this will ensure 4K60FPS resolution option is enabled via (USB-C/DP-Alt-Mode). The HDMI port on this laptop will only support up to 4K30FPS with capable external monitor.
  - USB Wake support does not show up as an option in BIOS and there is only a checkbox option for "Wake on Dell USB-C dock". At [r/Dell](https://www.reddit.com/r/Dell/comments/mgipwl/dell_can_you_please_add_proper_enable_usb_wake/), there are some posts complaining about this issue. Nonetheless, the option is there, hidden. If this option is disabled as in default, the BIOS will remove power from all of the USB ports. This causes a problem with Bluetooth, connected USB drives, and more.
    
    Upon waking, it will take a while for Bluetooth to reconnect and with lid open wake, notice blutoothd process taking high CPU usage around 200% that the Bluetooth has to be toggled off/on to stop the high CPU usage. As for the connected external USB drives, of course there is going to be a "Disk not ejected properly" pop-up every time.

    By enabling USB Wake Support, all of these issues can be resolved given that there are no other problems causing these issues. However, as the description in BIOS(Although, the option is missing, ironically, there the description for it -_-) the USB Wake is functional only when the AC Power adapter is connected prior to standby mode.
    

# Working

- Restart/Shutdown

- Sleep(Idle/Lid close/Manual)

- Wake (Lid open/Power button/External USB or Bluetooth keyboard/mouse/trackpad when AC adapter is plugged in)

- Clamshell mode

- Trackpad/Touchscreen with gestures

- Keyboard (Volume+/-/Mute with Fn+F1/F2/F3, brightness +/- with F6/F7, keyboard backlight with Fn+F5)

- Display: 4K60FPS resolution via USB-C(DP-Alt-Mode) port / 4K30FPS via HDMI1.4b port / Dim display on battery

- Built-in Camera. (Though, much better image with using iPhone as webcam)

- Audio: Built-in speaker; Headphone jack requires ALCPlugFix to get sound

- Battery Status

- iMessage / Facetime 

- Airplay/Airdrop/Handoff/Continuity including iPhone as webcam (Requires natively supported Wi-Fi/BT card such as BCM94360NG)

- AirplaytoMac/Sidecar/Universal Control (Requires natively supported Wi-Fi/BT card such as BCM94360NG)
  
 
# Not Working

- DRM in Safari/TV+ (Chrome browser will do)
- Fingerprint sensor
- Wake via built-in trackpad/keyboard

# OpenCore Config.plist

Mostly follow laptop, [Whiskey-Lake](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html) section of Dortania guide.

- ACPI

  ADD:

     - SSDT-AWAC.aml     (Disables AWAC clock and HPET; enables RTC to fix system clock)
     - SSDT-ALS0.aml     (Ambient Light sensor fix; works with SMCLightSensor kext; "Slightly dim the display on battery" feature becomes functional)
     - SSDT-Bridge.aml   (Injects missing IOReg names; cosmetic)
     - SSDT-DMAR.aml     (Modified DMAR table to prevent issues when enabling AppleVTD)
     - SSDT-EC-USBX.aml  (Fake embedded controller with USB power properties)
     - SSDT-GPI0.aml     (Ensures VoodooGPI0 is attached to GPI0 for Trackpad and touchscreen to work)
     - SSDT-LPCB.aml     (Injects fake devices, ARTC, DMAC, FWHD, and PMCR; cosmetic)
     - SSDT-MCHC.aml     (Injects fake MCHC device)
     - SSDT-PLUG.aml     (Injects plugin type 1 to load XCPM for CPU PMGMT; No longer required beginning with Monterey 12.3)
     - SSDT-PNLF.aml     (Fixes backlight control -> brightness control; sleep with lid closed while AC adapter is plugged in)
     - SSDT-USBP.aml     (ACPI USB Port Mapping Table; correctly define USB connector type for each USB ports and disable unused ports at ACPI level using GUPC and TUPC method)
  
   Delete:

    - Drop OEM DMAR Table 
    - Drop OEM USB Table
      
      
- Device Properties

    - ALC295 audio layout-id <4D000000>
    - Intel UHD-620
      | Property 	| Data 	| Remarks 	|
      | --- | --- | --- |
      |  AAPL,ig-platform-id | <00009B3E>   |   |
      |  device-id | <9B3E0000>	| |
      |  enable-backlight-registers-fix | <01000000> | Required to prevent Blackscreen |
      |  framebuffer-stolenmem | <00003001> | Remove this property if DVMT is set to 64MB (Modified in Grubshell) |
      |  framebuffer-patch-enable | <01000000> | Enables framebuffer patch 	|
      |  framebuffer-con0-enable    | <01000000> | 	|
      |  framebuffer-con0-alldata   | <00000800 02000000 98000000> | BusID 0; Pipe 8; LVDS 0002; Flags 0098 |
      |  framebuffer-con1-enable    | <01000000> | 	|
      |  framebuffer-con1-alldata   | <01010900 00080000 C7010000> | BusID 1; Pipe 9; HDMI 0008; Flags 01C7 |
      |  framebuffer-con2-enable    | <01000000> | 	|
      |  framebuffer-con2-alldata   | <01010A00 00040000 C7010000> | BusID 2; Pipe 10; DP 0004; Flags 01C7 |      
      |  enable-backlight-smoother | <01000000> 	| Brightness control smoothing	|
      |  backlight-smoother-steps | <19000000> 	| |
      |  backlight-smoother-interval | <05000000> 	| |
      |  rps-control | <01000000> 	| Improve iGPU performance |
      |  force-online | <01000000> 	| Use this property to workaround coldplug not working on HDMI Port |


- Kernel

  Add:

     - VirtualSMC and its plugins
     - SMCBatteryManager (Required for correct battery status/No battery patching needed)
     - SMCLightsensor (Ambient Light Sensor; works with SSDT-ALS0.aml)
     - SMCDellSensors (Needed for Dell CPU fan monitoring)
     - Lilu/AppleALC/WhateverGreen 
     - [CPUFriend](https://github.com/acidanthera/CPUFriend)/[CPUFriendDataProvider](https://github.com/corpnewt/CPUFriendFriend) (Configure CPU powermanagement)
     - [VoltageShift](https://github.com/sicreative/VoltageShift) (To disable Turbo Boost/Undervolt/Power Limit; the kext itself won't do anything unless settings are configured at OS level; Undervolting requires unlocking Overclock Lock in BIOS mod)
     - [RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader)/RealtekCardReaderFriend (For microSD card reader)
     - NVMeFix
     - VoodooPS2Controller/VoodooPS2Keyboard (For Keyboard)
     - [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)/VoodooInput/VoodooI2CServices/VoodooGPIO/VoodooI2CHID (For Trackpad/Touchscreen)

  Patch:

     - AppleRTC patch to disable RTC alarm wakes.

  Quirks:
  
     - AppleXcpmCfgLock -> Set to No if CFG Lock is disabled. Otherwise, enable this quirk.
     - CustomSMBIOSGuid -> If dual booting to other OSes other than macOS, set this to yes along with setting UpdateSMBIOSMode to "Custom" in PlatformInfo section.
     - DisableIoMapper -> No (If enabling AppleVTD)
     - DisableIoMapperMapping -> Yes (If enabling AppleVTD in Ventura+)

- Misc
  
   - Security -> ScanPolicy -> 0 (Set it to 2,687,747 if you wish to hide unnecessary entries)

   - SecureBootModel -> j213

- PlatformInfo -> Generic -> SystemProductName -> MacBookPro15,4
- PlatformInfo -> Generic -> UpdateSMBIOSMode -> Custom


# ACPI USB Port Mapping

- HS01/SS01 -> USB-C port 
- HS02/SS02 -> Left side USB-A port 
- HS03/SS03 -> Right side USB-A port 
- HS06 -> Webcam 
- HS08 -> Fingerprint Sensor / Smart Card Reader (Disabled)
- HS10 -> Bluetooth 

[Guide](https://github.com/AppleBreak1/Z490-Vision-D-Customac/tree/main/USB%20Ports)


# Misc

**Troubleshooting** (Hackintoshing)

  Display 

  -  Black screen on built-in display -> Inject enable-backlight-registers-fix was required to resolve this.
  -  Black screen on built-in display when rotating or connecting to external display -> This was due to using custom resolution to enable HIDPI resolution. Workaround is to set display resolution back to   its default in prior.
  -  HDMI port coldplug not working -> Workaround is to inject force-online property.
  -  NO 4K resolution via external monitor -> Set DVMT Pre-allocated to 64MB using modGrubshell as there is no option for it in BIOS. (Make sure to remove framebuffer-stolenmem property)
  -  Brightness control keymapping (F6/F7) -> Add scan code 40=65 and 41=66 in VoodooPS2Keyboard.kext-> info.plist -> Custom Profile -> Default -> Custom PS2 Map. There are alternative ways to remap Fn+F6/F7 key such as [Brightnesskeys.kext](https://github.com/acidanthera/BrightnessKeys) or [SSDT-BRT6.aml](https://osxlatitude.com/forums/topic/15661-acpi-patch-for-brightness-keys-on-dell-laptops/) which require patching _OSI, OSID and BRT6. For me, F6 and F7 will do.
  

  Audio

  - ALC layout-id 28 or 77 works. Some other(incompatible) layout-ids can lead to kernel_task causing high CPU usage.
  - Unfortunately, the headphone jack on this laptop requires [AlcPlugFix](https://github.com/black-dragon74/ALCPlugFix-Swift) to be functional. (alc-verbs <01000000>, Node ID 0x19, Param 20, temporary disable SIP)
  - Boot-Chime not working in OpenCore boot menu -> Looking at the debug log, it's similar to issue described [here](https://github.com/acidanthera/bugtracker/issues/963). The solution is to upsample audio file to 48kHz. However, I usually don't like to use Boot-Chime as AudioDXE.efi driver causes delay when posting. 

  CPU Powermanagement

  - Inject CPUFriend.kext with configured CPUFriendDataProvider.kext.
  - [VoltageShift.kext](https://github.com/sicreative/VoltageShift) to disable Intel Turbo Boost and keep the fan noise down.
    
  Sleep/USB

  - Looking at the power event log, the system has been waking up every 1~2 hours due to all kinds of reasons -> The solution is to pmset to Hibernatemode 0, proximitywake 0, tcpkeepalive 0 and apply AppleRTC kernel patch.
  - The system will only wake via power button or by opening the lid, but not via external USB keyboard/mouse -> To wake via USB, USB Wake Support(only supported when AC adapter is plugged) option needs to be enabled and is to be done in alternative way as this option is not available in BIOS mode.
  - "Disk not ejected properly" pop-up notification with USB drive -> Enable USB Wake Support.
  - Bluetooth takes a while to reconnect upon waking -> Enable USB Wake Support.
  - bluetoothd process with high CPU usage on lid open wake -> Enable USB Wake Support.

    What if you are on battery power? Consider using [Bluesnooze](https://github.com/odlp/bluesnooze), [Blueutil](https://github.com/toy/blueutil) or manually turnoff Bluetooth.
  
 

  
 
**Hardware**

CPU Fan

 - This laptop seems to have a fan curve mode where it won't start spinning until the temp reaches above 70c and stops spinning once the temp cools down to around 50c (Optimized setting in Thermal Management).
 - Has a single pipe heatsink -> Swapped with dual pipe heatsink from Latitude 5310 model(Had to bend one of the pipes a little bit). If I had known a difference it made, I wouldn't have bothered swapping. I think laptop cooling pad will probably do a better job.
 - The CPU fan can be controlled with MacsFanControl app which is very limited in terms of what you can control. The fan RPM can be adjusted from 3400 ~ 6000RPM at constant. Do note that this will only work if Fan Control Override is enabled via BIOS mod. This feature is not accessible from the BIOS menu.
   
Wi-Fi/Bluetooth

 - Comes with Intel Dual Band Wireless AC 9560 -> Swapped with BCM94360NG module to enjoy Airplay/Airdrop/continuity features. MHF4 extension cable was required as one(main) of the two antenna cables was too short to reach the main antenna port on BCM94360 module.
 

WWAN slot

- Looking at the motherboard's [schematic diagram of Dell 5300](https://laptop-schematics.com/view/11853/), it does seem to support SSD via WWAN Slot. However, it did not work with B+M key 2242 M.2 (NVME/SATA)SSD. What I hadn't try was enabling SATA ports in BIOS mod. All the SATA ports are disabled by default and not accessible in BIOS menu. Maybe enabling one of those SATA ports might work with M.2 SATA SSD. 

      
# Geekbench 6


![385889795-65148185-2ac1-4d57-a411-68e065c5b668](https://github.com/user-attachments/assets/760971ca-7612-457b-a335-0501f2f4c8aa)



# Resources

- [Apple Inc](https://www.apple.com/) for MacOS.
- [Acidanthera Team](https://github.com/acidanthera) for OpenCore Bootloader, its documentation, and KEXTS.
- [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) for thorough explanation.  


Big thanks to Hackintosh community and those who contributed to make hackintosh possible from the beginning to the end!






