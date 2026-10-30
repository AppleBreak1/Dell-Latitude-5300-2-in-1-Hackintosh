<img width="1366" height="768" alt="Tahoe" src="https://github.com/user-attachments/assets/a7f01b7b-a48d-44c3-978a-b7eb3e648885" />

## Configuration Note

- EFI-Bcm-Wifi
  
  - System Integrity Protection fully enabled
  - Working BCM94360NG Wi-Fi without a root volume patch, courtesy of Laobamac's [SimpleBCM](https://github.com/laobamac/SimpleBCM)
  - Working Wi-Fi in macOS installer and recovery system.
  - Working onboard audio without a root volume patch, courtesy of Laobamac's [SimpleHDA](https://github.com/laobamac/SimpleHDA) 
  - Implements ACPI USB port mapping without dropping original USB table

- EFI-Intel-Wifi

  - System Integrity Protection fully enabled
  - Working Intel Wi-Fi with AirportItlwm without a root volume patch, courtesy of Laobamac's [AirportItlwm]([https://github.com/laobamac/SimpleBCM](https://github.com/laobamac/itlwm)
  - Working Wi-Fi in macOS installer and recovery system.
  - BlueToolFixup.kext is disabled by default. Enable it after a macOS update/installation for functional bluetooth.
  - Working onboard audio without a root volume patch, courtesy of Laobamac's [SimpleHDA](https://github.com/laobamac/SimpleHDA) 
  - Implements ACPI USB port mapping without dropping original USB table


