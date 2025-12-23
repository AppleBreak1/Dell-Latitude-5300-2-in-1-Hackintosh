# Dell Latitude 5300 2-in-1
<br>

<img width="600" src="https://github.com/user-attachments/assets/449ce9b6-2cf9-44c6-8945-351bef1eb913">

<img width="210" height="361" alt="idle power consumption" src="https://github.com/user-attachments/assets/2321d61b-235d-4ebf-8956-55c18a205b79" />

# Hardware Specification
```md
[CPU][Intel® Core™ i7-8665U]

[GPU][Intel® UHD Graphics 620]

[RAM][Micron 16GB 2666Mhz] x 2 (32GB)

[Display][13.3 FHD 1920 x 1080 IPS] (Chi Mei: CMN1382)

[Touchscreen][WCOM48E2] (Wacom)

[Wi-Fi/BT Card][BCM94360NG NGFF M.2] (Swapped from Intel® Dual Band Wireless AC 9560; extension cable required)

[SSD][SK Hynix PC 401 512GB] (Both macOS and Windows on the same drive)

[Audio][ALC295/ALC3254] (Layout-ID 77, Headphone jack requires ALCPlugFix to be functional)

[Pen][Dell Premium Active Pen PN579X]
```
Left side ports: 

  - USB-C (USB 3.1 Gen 2 10Gbps; DisplayPort (4K60FPS); Thunderbolt port may come as an option)
  - HDMI 1.4b (Supports up to 4K30FPS)
  - USB-A (USB 3.1 Gen 1 5Gbps)
  - Smart card reader

Right side ports

  - USB-A (USB 3.1 Gen 1 5Gbps)
  - microSD card reader (RTS525A PCIe)
  - Headset / Microphone (Universal Jack)
  - micro-SIM card (Optional)

# Working

- Restart/Shutdown

- Sleep(Idle/Lid close/Manual)

- Wake (Lid open/Power button/External USB or Bluetooth keyboard/mouse/trackpad when AC adapter is plugged in)

- Clamshell mode

- Trackpad/Touchscreen with gestures

- Keyboard (Volume+/-/Mute with Fn+F1/F2/F3, brightness +/- with Fn+F6/F7, keyboard backlight with Fn+F5)

- Pen PN579X: Limited functionality but pen hovering and top barrel button (right click) works.

- Display: 4K60FPS resolution via USB-C(DP-Alt-Mode) port / 4K30FPS via HDMI1.4b port / Dim display on battery

- Built-in Camera. (Though, much better image with using iPhone as webcam)

- Audio: Built-in speaker; Headphone jack requires ALCPlugFix to get sound

- Battery Status

- iMessage / Facetime 

- Airplay/Airdrop/Handoff/Continuity including iPhone as webcam (Requires natively supported Wi-Fi/BT card such as BCM94360NG)

- AirplaytoMac/Sidecar/Universal Control (Requires natively supported Wi-Fi/BT card such as BCM94360NG)

- Apple Music Lossless Audio ([unfairgva=4](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md); by spoofing board ID to iMacPro1,1(iGPU-less), lossless audio works in Apple Music; however, do note that spoofing board-ID to iMacPro1,1 on iGPU only system may break iGPU encoding/decoding functions and seemingly have issues with rps-control patch or Apple GuC firmware as the iGPU clock frequency and the power consumption can get stuck at max. This behavior is triggered when attempting video playback while having Apple Music play)
 
# Not Working

- DRM in Safari/Apple TV+ (Chrome browser will do)
- Fingerprint sensor
- Screen auto-rotation
- Wake via built-in trackpad/keyboard

# macOS

macOS: Big Sur ~ Ventura (Tested to work but older macOS like Sierra should also be possible with some spoofing)

  - macOS High Sierra - Catalina
    
    - SMBIOS -> MacBookPro15,2 (For High Sierra)
    - Set SecureBootModel -> Disabled
    - UEFI -> APFS -> MinDate and MinVersion -> -1
    - IGPU may require some spoofing in macOS High Sierra. Refer to [WhateverGreen Manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md) for supported ig-platform-id
 
  - macOS Big Sur ~ Ventura

    - Works without making any changes to the posted EFI.

  - macOS Sonoma

    - To install or update to Sonoma 14.4+, SecureBootModel needs to be disabled.
    - To fix black screen on internal display for Sonoma+, Injecting [enable-backlight-registers-alternative-fix](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#fix-the-3-minute-black-screen-issue-on-kblcfl-platforms-running-macos-134-or-later) property is necessary
    - For macOS Sonoma+, Broadcom Wi-Fi has to depend on [OCLP](https://github.com/dortania/OpenCore-Legacy-Patcher) patch to be functional.

# BIOS Settings

BIOS Revision: 1.32.0

SATA Operation -> AHCI

Lid Switch -> Enabled

Secure Boot Enable - > Disabled (Required)

Absolute® -> Disabled

SMM Security Mitigation -> Disabled

Intel AMT Capability -> Disabled

Intel Turbo boost -> Disabled for Windows 10 to reduce fan noise, sacrificing performance. (This option will be ignored in macOS however)

  ** Note that in order to boot macOS, only change required to make from its default setting is "Secure Boot Enable" **

# UEFI Variable Mod

The modification of these hidden BIOS settings are not required to boot macOS at all and can be ignored, but is done to enable features that are otherwise would not be available. 

To change below hidden BIOS settings, [ModGrubShell](https://github.com/datasone/grub-mod-setup_var/releases) is required

For Dell BIOS extraction to find offset, please refer to Dreamwhite's [guide](https://github.com/dreamwhite/bios-extraction-guide).


BIOS Version: 1.32.0


- Disable CFG Lock -> (offset 0x6ED)

       setup_var 0x6ED 0x0      (This will disable CFG Lock)
       setup_var 0x6ED 0x1      (Back to default)

- Set DVMT Pre-allocated to 64MB -> (offset 0xA10)

        setup_var 0xA10 0x2     (This will pre-allocate 64MB to DVMT)
        setup_var 0xA10 0x1     (Back to default)
  
- Enable USB Wake Support -> (offset 0x149A)

        setup_var 0x149A 0x1    (This will allow system to wake via USB peripheral devices(Keyboard/Mouse))
        setup_var 0x149A 0x0    (Back to default)

     <img width="1000" alt="385414764-d766416a-0fa6-447d-8931-173c06ec11fb" src="https://github.com/user-attachments/assets/b2b161bc-35fc-4163-9964-87a3e3e92569">




Note: 

  - [Why disable CFG Lock?](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#what-is-cfg-lock)
  - Once the DVMT Pre-Allocated is set to 64MB, make sure to remove framebuffer-stolenmem property from the config.plist as this will ensure 4K60FPS resolution option is enabled via (USB-C/DP-Alt-Mode). The HDMI port on this laptop will only support up to 4K30FPS with capable external monitor.
  - USB Wake support does not show up as an option in BIOS menu and there is only a checkbox option for "Wake on Dell USB-C dock". At [r/Dell](https://www.reddit.com/r/Dell/comments/mgipwl/dell_can_you_please_add_proper_enable_usb_wake/), there are some posts complaining about this issue. Nonetheless, the option is there, hidden. If this option is disabled as in default, the BIOS will remove power from all of the USB ports. This causes a problem with Bluetooth, connected USB drives, and more.
    
    Upon waking, it will take a while for Bluetooth to reconnect and with lid open wake, notice blutoothd process taking high CPU usage around 200% that the Bluetooth has to be toggled off/on to stop the high CPU usage. As for the connected external USB drives, of course there is going to be a "Disk not ejected properly" pop-up every time.

    By enabling USB Wake Support, all of these issues can be resolved given that there are no other problems causing these issues. However, as the description in BIOS(Although, the option is missing, ironically, there the description for it -_-) the USB Wake is functional only when the AC Power adapter is connected prior to standby mode.
    
# OpenCore Config

Mostly follow laptop, [Whiskey-Lake](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/coffee-lake.html) section of Dortania guide.

- ACPI

  ADD:

     - SSDT-AWAC.aml     (Disables AWAC clock and HPET; enables RTC to fix system clock)
     - SSDT-ALSD.aml     (Ambient Light sensor fix; works with SMCLightSensor kext; "Slightly dim the display on battery" feature becomes functional)
     - SSDT-ACOS.aml     (SSDT patch to allow Dell keyboard function keys to work, such as brightness +/-; use with BrightnessKeys.kext)
     - SSDT-Bridge.aml   (Injects missing IOReg names; cosmetic)
     - SSDT-EC-USBX.aml  (Fake embedded controller with USB power properties)
     - SSDT-GPI0.aml     (Ensures VoodooGPI0 is attached to GPI0 for trackpad and touchscreen to work)
     - SSDT-LPCB.aml     (Injects fake devices, ARTC, DMAC, FWHD, and PMCR; cosmetic)
     - SSDT-MCHC.aml     (Injects fake MCHC device)
     - SSDT-PLUG.aml     (Injects plugin type 1 to load XCPM for CPU PMGMT; No longer required beginning with Monterey 12.3)
     - SSDT-PNLF.aml     (Fixes backlight control -> brightness control)
     - SSDT-USBP.aml     (ACPI USB Port Mapping Table; correctly define USB connector type for each USB ports and disable unused ports at ACPI level using GUPC and TUPC method)
  
   Delete:

    - Drop OEM USB Table

- Quirks

    - FadtEnableReset -> Yes (Required to fix power button to function correctly (i.e putting system to sleep/fully wake system from sleep; not darkwake)    
      
      
- Device Properties

    - ALC295 audio layout-id <4D000000>
    - Intel UHD-620
      | Property 	| Data 	| Remarks 	|
      | :---: | :---: | :---: |
      |  AAPL,ig-platform-id | <00009B3E>   |   |
      |  device-id | <9B3E0000>	| |
      |  enable-backlight-registers-fix | <01000000> | Required to prevent black screen |
      |  enable-backlight-registers-alternative-fix | <01000000> | Required to prevent black screen (Sonoma+) |
      |  framebuffer-stolenmem | <00003001> | Remove this property if DVMT is set to 64MB (Modified in Grub shell) |
      |  framebuffer-patch-enable | <01000000> | Enables IGPU framebuffer patch 	|
      |  framebuffer-con1-enable    | <01000000> | For HDMI port patch |
      |  framebuffer-con1-busid   | <01000000> | BusID 1; for HDMI port patch |
      |  framebuffer-con1-type    | <00080000> | Connector type HDMI; for HDMI port patch |
      |  enable-backlight-smoother | <01000000> 	| Enable brightness control smoothing	|
      |  backlight-smoother-steps | <19000000> 	| |
      |  backlight-smoother-interval | <05000000> 	| |
      |  backlight-smoother-lowerbound | <BC020000 | Sets the built-in screen's minimum brightness level, preventing screen from going completely dark at its lowest |
      |  rps-control | <01000000> 	| Improve iGPU performance |
      |  igfxfw | <02000000> 	| Forces loading Apple GuC Firmware; either use rps-control or igfxfw |
      |  force-online | <01000000> 	| Use this property to workaround HDMI connected monitor not detected on coldplug and sleep resume thus requiring hotplug|


      Please refer to [WhateverGreen Manual](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md) for more detailed explanation.


- Kernel

  Add:

     - VirtualSMC and its plugins
     - SMCBatteryManager (Required for correct battery status/No additional battery patching needed; all are 8-bit)
     - SMCLightsensor (Ambient Light Sensor; works with SSDT-ALS0.aml)
     - SMCDellSensors (Needed for Dell CPU fan monitoring)
     - Lilu/AppleALC/WhateverGreen 
     - [CPUFriend](https://github.com/acidanthera/CPUFriend)/[CPUFriendDataProvider](https://github.com/corpnewt/CPUFriendFriend) (Configure CPU powermanagement)
     - [VoltageShift](https://github.com/sicreative/VoltageShift) (To disable Intel Turbo Boost, undervolt, and set Power Limit(PL1,PL2); the kext itself won't do anything unless the settings are configured at OS level; undervolting requires unlocking Overclock Lock in BIOS mod)
     - [RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader)/RealtekCardReaderFriend (For microSD card reader)
     - NVMeFix
     - VoodooPS2Controller/VoodooPS2Keyboard (For Keyboard)
     - [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C)/VoodooInput/VoodooI2CServices/VoodooGPIO/VoodooI2CHID (For Trackpad/Touchscreen)
     - [BrightnessKeys.kext](https://github.com/acidanthera/BrightnessKeys) (For Brightness keys to work; use with SSDT-ACOS.aml)
     - [HibernationFixup.kext](https://github.com/acidanthera/HibernationFixup) (Required if using standby(S4) with hibernatemode 3)

  Patch:

     - AppleRTC patch to disable RTC alarm wake scheduling. (Do not enable this patch if using hibernatemode 3)

  Quirks:
  
     - AppleXcpmCfgLock -> Set to No if CFG Lock is disabled. Otherwise, enable this quirk.
     - CustomSMBIOSGuid -> If dual booting to other OSes other than macOS, set this to yes along with setting UpdateSMBIOSMode to "Custom" in PlatformInfo section.

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

**Troubleshooting** (Hackintoshing & Refining)

  Display 

  -  Black screen on built-in display -> Injecting enable-backlight-registers-fix was required to resolve this.
  -  [3-minute Black screen](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#fix-the-3-minute-black-screen-issue-on-kblcfl-platforms-running-macos-134-or-later) on built-in display for Sonoma+ -> Injecting enable-backlight-registers-alternative-fix was required to resolve this.
  -  Black screen on built-in display when rotating or connecting/disconnecting to/from external display -> This was due to using custom resolution to enable HIDPI resolution. Workaround is to set display resolution back to its default in prior and scale the resolution afterwards.
  -  HDMI port connected display detection -> bus ID of the HDMI port(Index 1) needs to be patched to 0x01 and connector type to HDMI.
  -  HDMI port connected display not detected on coldplug and sleep resume -> Workaround is to inject force-online property. It will still take around 10 sec to come on with couple of flickers. Alternatively, you may use agdpmod=vit9696 for faster detection on boot but force-online property may still be necessary for detection on sleep resume.
  -  NO 4K resolution via external monitor -> Set DVMT Pre-allocated to 64MB using modGrubShell as this option is hidden in BIOS. (Make sure to remove framebuffer-stolenmem property)
  -  Auto-rotation feature does not work. To manually rotate the screen, press and hold option/alt key(Ventura) and go to Display setting in System Settings for rotation option to show up in display settings. Alternatively, use [displayplacer](https://github.com/jakehilborn/displayplacer). One can create multiple bash scripts for each screen resolution and simply change resolution with a click or a tap.

      - Do note that when screen is rotated, scaling does not work. It will only work in default resolution.  Rotating screen requires first setting the resolution to default then rotate; otherwise, black screen. Doing this from the System Settings is time consuming and inconvenient. This is where displayplacer tool comes in handy to makes it quite convenient and even more so when in tablet mode.

  Audio

  - ALC layout-id 77 works. Some other(incompatible) layout-ids can lead to kernel_task causing high CPU usage.
  - Unfortunately, the headphone jack on this laptop requires [AlcPlugFix](https://github.com/black-dragon74/ALCPlugFix-Swift) to be functional. (alc-verbs <01000000>, Node ID 0x19, Param 20, temporary disable SIP)
  - Boot-Chime not working in OpenCore boot menu -> Looking at the debug log, it's similar to issue described [here](https://github.com/acidanthera/bugtracker/issues/963). The solution is to upsample audio file to 48kHz. However, I usually don't like to use Boot-Chime as AudioDXE.efi driver causes a delay when the system posts and breaks audio in Windows (The fix for Windows can be found in OpenCore's Configuration.pdf). 

  Power Management

  - Inject CPUFriend.kext with configured CPUFriendDataProvider.kext.
  - [VoltageShift.kext](https://github.com/sicreative/VoltageShift) (This tool can be used to disable Intel Turbo Boost, undervolt, and set power limit to keep the temperature down. Features like BD PROCHOT and Turbo Ratio Limit may also be modified via Voltageshift as it can read and write MSR values.
    <br>
    
    My Voltageshift settings as reference
    ```md
    |        [CPU][-110mv]
    |  [CPU Cache][-110mv]
    |        [GPU][-70mv]
    | [TurboBoost][OFF]
    |        [PL1][10W]
    |        [PL2][10W]
    ```
    Disable Overclocking Lock -> VarOffset: 0x789)
    ```
    setup_var 0x789 0x0  (Disable)
    setup_var 0x789 0x1  (Back to default)
    ```
 - On default settings, the processor eventually downclocks and settles around 2.7 GHz average clock speed, 15W power consumption, and 80c temperature in longer period of use.

 - Setting Turbo Ratio Limit rather than disabling turbo boost

      - MSR for turbo ratio limit is 0x1ad

      - Read the values from 0x1ad and convert the resulting binary number to hex number

            ./voltageshift read 0x1ad

      - i7-8665U(4cores) as an example after converting the binary result to hex gives 
     
            0x2a2a2f30 (42 for 4 active cores, 42 for 3 active cores, 47 for 2 cores, 48 for 1 active core)

      - Replace the values and modify turbo ratio limit
     
                                                         // i7-8665U
            ./voltageshift write 0x1ad 0x1e1e1e1e    // limit to 30 for all number of active cores
            ./voltageshift write 0x1ad 0x24242424    // limit to 36 for all number of active cores
            ./voltageshift write 0x1ad 0x1e1e1e24    // limit to 30 for 2 to 4 active cores and limit to 36 for 1 active core
  
 - Power consumption can be further reduced by disabling SD card reader. When the microSD card reader is enabled, CPU package does not enter C-state deeper than C3 at idle. In order to achieve similar level of power consumption as Windows side where the CPU reaches C8 state at idle, SD card reader needs to be disabled. (Disabling touchscreen, camera, and any other unused devices at BIOS level may help as well)
 
 - Powermetrics Report

   <img width="801" height="214" alt="Powermetrics" src="https://github.com/user-attachments/assets/ff246cec-a226-43ca-8214-af86209eedcc" />

    
  Sleep/USB

  - Looking at the power event log, the system has been darkwaking every 1~2 hours due to all kinds of reasons -> The solution is to pmset to Hibernatemode 0, proximitywake 0, tcpkeepalive 0 and apply AppleRTC kernel patch.
  - The system will only wake via power button or by opening the lid, but not via external USB keyboard/mouse -> To wake via USB, USB Wake Support(only supported when AC adapter is plugged) option needs to be enabled and is to be done in alternative way as this option is not available in BIOS menu.
  - "Disk not ejected properly" pop-up notification with USB drive -> Enable USB Wake Support.
  - Bluetooth takes a while to reconnect upon waking -> Enable USB Wake Support.
  - bluetoothd process with high CPU usage on lid open wake -> Enable USB Wake Support.

    What if you are on battery power? Consider using [Bluesnooze](https://github.com/odlp/bluesnooze), [Blueutil](https://github.com/toy/blueutil) or manually turnoff Bluetooth.

Num Lock (Enable)
   
   - BIOS -> Keypad (Embedded) -> By Numlock
   - BIOS -> Numlock Enable -> Enable Numlock
   - Compile and inject -> [SSDT-NumLockSupport.dsl](https://github.com/acidanthera/VoodooPS2/blob/master/Docs/ACPI/SSDT-NumLockSupport.dsl)
   - Press fn+insert to enable numlock and use 7,8,9,0(/),U(4),I(5),O(6),P(*),J(1),K(2),L(3),;(-),M(0),>(.),/(+) keys as number pad.

[Standby](https://support.apple.com/en-us/101363) / Hibernatemode (Works)

   From pmset Man page
   
  <img width="566" alt="Screenshot 2024-11-19 at 6 44 19 PM" src="https://github.com/user-attachments/assets/5b7ce353-513f-48c0-91cb-fb36767bf205">
<img width="570" alt="Screenshot 2024-11-19 at 6 44 30 PM" src="https://github.com/user-attachments/assets/90495778-b186-44cf-9235-aec31b67ac60">


   
   - Hibernatemode 0

     - Completely disables hibernation

   - Hibernatemode 3  

      - pmset hibernatemode to 3
      - pmset standby to 1
      - Misc -> Boot -> Hibernatemode -> NVRAM
      - Misc -> Boot -> HibernateSkipsPicker -> Yes
      - Do not apply AppleRTC kernel patch if applied (patch to disable RTC wake scheduling)
      - Inject [HibernationFixup.kext](https://github.com/acidanthera/HibernationFixup)
      - Add boot-arg -> hbfx-ahbm=5 (Need this flag with value of at least 1 to put system in standby mode; refer to its [manual](https://github.com/acidanthera/HibernationFixup) for various configuration)
      - Set standbydelay time that suits your need in terminal (This sets the RTC alarm wake scheduling). The system will darkwake from normal sleep as set by standbydelay argument then decides whether to transition to standby mode. If transitions to standby, it saves current session to disk in var/vm/sleepimage and turns off some of the hardware systems to save power)

            Sudo pmset -a standbydelay 7200    (In seconds; This will put the system in standby mode after 2 hours of normal sleep)



        **Power event log after waking up from standby mode with hibernatemode 3**
        <img width="996" alt="H3" src="https://github.com/user-attachments/assets/1cbb0e84-555d-4e14-ab93-16815d803695">


        

          - 00:23:11 - The system initiates sleep upon lid closing and goes into normal sleep.
          - 02:23:33 - The system darkwakes after 2 hours(RTC alarm wake schedule set by standbydelay 7200) of normal sleep

                     (The battery drain during the normal sleep of 2 hour frame is around 3%)
            
          - 2:23:38 - The System transitions from normal sleep state to standby mode
          - 7:59:38 - The System wakes from standby after 5 hours due to user intervention

                     (The battery drain during 5 hours of standby is 0% )
   
   - Hibernatemode 25 (pmset standby to 1)

      Hibernatemode 25 will immediately put system to standby preserving battery life. 
     
      - pmset hibernatemode to 25
      - pmset standby to 1
      - Misc -> Boot -> Hibernatemode -> NVRAM
      - Misc -> Boot -> HibernateSkipsPicker -> Yes
    
          OC log during wake from standby
        
        <img width="456" alt="Screenshot 2024-11-19 at 6 33 24 PM" src="https://github.com/user-attachments/assets/9e723d11-d941-4eed-b3a2-a699af8f2fb1">
          


         **Power event log after waking up from standby mode with hibernatemode 25**
        <img width="946" alt="H25" src="https://github.com/user-attachments/assets/7bffe194-8c06-41c8-ab49-35ce1c796b39">

       

           - 11:41:15 - The system initiates idle sleep and goes into standby mode immediately.
           - 18:02:35 - The System wakes from standby after 6 hours due to user intervention

                  (The battery drain during 6 hours of standby is 0% )


      - Note that when in standby mode, the system could only wake via power button and it will continue from splash screen then straight into macOS if HibernateSkipsPicker is set to Yes
               
**Hardware**

CPU Fan

 - This laptop seems to have a fan curve mode where it won't start spinning until the CPU temp reaches above 70c and stops spinning once the temp cools down to around 60c (Optimized setting in Thermal Management). However, CPU fan can be triggered by temperature sensors of other components as well.
 - Has a single pipe heatsink -> Swapped with dual pipe heatsink from Latitude 5310 model(Had to bend one of the pipes a little bit). If I had known a difference it made, I wouldn't have bothered swapping. I think laptop cooling pad will probably do a better job.
 - The CPU fan can be controlled with MacsFanControl app which is very limited in terms of what you can control. The fan RPM can be adjusted from 3400 ~ 7000RPM at constant. Do note that this will only work if Fan Control Override is enabled via BIOS mod. This feature is not accessible from the BIOS menu.
   
Wi-Fi/Bluetooth

 - Comes with Intel Dual Band Wireless AC 9560 -> Swapped with BCM94360NG module to enjoy Airplay/Airdrop/continuity features. MHF4 extension cable was required as one(main) of the two antenna cables was a bit short to reach the main antenna port on BCM94360 module. Altenatively, you may utilize extra IPX MHF4 antenna and place it somewhere inside the laptop (It is very compact however).

 - BCM94360NG speed issue with macOS

   You will find discussion about this problem from Acidenthera bugtracker #[1532](https://github.com/acidanthera/bugtracker/issues/1532) with possible workaround
   
   On Windows, internet speed works fine as it should with link speed up to 866 Mbps. However, on macOS, speed becomes an issue. You'll notice that Tx rate is maxed at 434 Mbps with limited spatial stream to 1 (NSS=1) on cold boot. To gain its maximum capable speed, it seems that only workaround for now is to warm boot from Windows to macOS while having Broadcom Network Adapter driver version [7.35.295.2](https://www.catalog.update.microsoft.com/Search.aspx?q=Broadcom%20802.11n%20Network%20Adapter) (7/19/2015/Broadcom)  installed. Note that this will only work with this specific version of drive.

Battery 

 - N2K62 LCL (60 WHr, Long Cycle Life /1000 charge cycle) pack. At the time of testing, battery max capacity at 71.6% due to battery degradation.
 - In BIOS, battery is configured to be start charging when below 50% and stop charging when at 83%
 - Based on the testing with above setup and brightness level around 80%, the battery would last about 3 hrs when watching the 1080P 60FPS Youtube video. The CPU total package power consumption reporting during the video playback arounds 2.5W ~ 3.5W.
 - Disabling touchscreen and reducing brightness level may help with preserving battery life. 
   

WWAN slot

- Looking at the motherboard's [schematic diagram of Dell 5300](https://laptop-schematics.com/view/11853/), it does seem to support SSD via WWAN Slot. However, it did not work with B+M key 2242 M.2 (NVME/SATA)SSD.
      
# Geekbench 6


![385889795-65148185-2ac1-4d57-a411-68e065c5b668](https://github.com/user-attachments/assets/760971ca-7612-457b-a335-0501f2f4c8aa)



# Resources

- [Apple Inc](https://www.apple.com/) for MacOS.
- [Acidanthera Team](https://github.com/acidanthera) for OpenCore Bootloader, its documentation, and KEXTS.
- [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) for thorough explanation.  


Big thanks to Hackintosh community and those who contributed to make hackintosh possible from the beginning to the end!
