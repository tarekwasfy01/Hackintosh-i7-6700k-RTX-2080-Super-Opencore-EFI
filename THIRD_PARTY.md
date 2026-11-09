# Drittanbieter-Komponenten

Die Binärdateien stammen aus einem bestehenden EFI-Snapshot. Die Versionsangaben sind den enthaltenen Info.plists entnommen; sie sind keine Zusicherung, dass jede Datei aus einem unveränderten Upstream-Release stammt. Prüfsummen dieses Snapshots stehen in SHA256SUMS.

| Dateien | Upstream / Quellcode |
|---|---|
| OpenCore, BOOTx64, OpenRuntime, OpenHfsPlus, OpenShell, MmapDump | https://github.com/acidanthera/OpenCorePkg |
| Lilu | https://github.com/acidanthera/Lilu |
| VirtualSMC, SMCProcessor, SMCSuperIO | https://github.com/acidanthera/VirtualSMC |
| WhateverGreen (deaktiviert) | https://github.com/acidanthera/WhateverGreen |
| AppleALC (deaktiviert) | https://github.com/acidanthera/AppleALC |
| IntelMausi | https://github.com/acidanthera/IntelMausi |
| NVMeFix | https://github.com/acidanthera/NVMeFix |
| RestrictEvents | https://github.com/acidanthera/RestrictEvents |
| USBToolBox, UTBDefault | https://github.com/USBToolBox/kext |
| BlueToolFixup | https://github.com/acidanthera/BrcmPatchRAM |
| IntelBluetoothFirmware, IntelBTPatcher | https://github.com/OpenIntelWireless/IntelBluetoothFirmware |
| itlwm | https://github.com/OpenIntelWireless/itlwm |
| AMFIPass | https://github.com/dortania/OpenCore-Legacy-Patcher |
| SimpleHDA (deaktiviert) | Bundle-ID `cn.laobamac.SimpleHDA`; genaue Herkunft dieses bereits vorhandenen Builds nicht verifiziert |
| SSDT-PLUG-DRTNIA, SSDT-EC-USBX-DESKTOP | Dortania-Vorlagen / lokale Konfiguration; nicht als eigene allgemeingültige ACPI-Implementierung ausgegeben |

Die Upstream-Lizenztexte liegen, soweit zugeordnet, unter `LICENSES/`. Die bestehenden Copyright-Vermerke in den Bundles bleiben erhalten. Die MIT-Lizenz im Repository-Root überschreibt weder GPL/BSD-Lizenzen noch Firmware- oder sonstige Drittanbieterbedingungen. Upstream-Quellcode und Release-Historie sind über die Links erreichbar.

NullMoth-Treiber, Apple-Systemdateien und Installationspakete sind **nicht enthalten**. Für NullMoth gelten die Bedingungen des Herstellers: https://github.com/nullmoth/nvidia-macos-driver .
