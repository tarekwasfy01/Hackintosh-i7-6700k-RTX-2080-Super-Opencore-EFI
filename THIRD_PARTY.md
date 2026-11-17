# Third-party components

The binaries come from an existing EFI snapshot. Version numbers were read from the included Info.plist files; they do not guarantee that every file is an unmodified upstream release. Checksums for this snapshot are listed in SHA256SUMS.

| Files | Upstream / source code |
|---|---|
| OpenCore, BOOTx64, OpenRuntime, OpenHfsPlus, OpenShell, MmapDump | https://github.com/acidanthera/OpenCorePkg |
| Lilu | https://github.com/acidanthera/Lilu |
| VirtualSMC, SMCProcessor, SMCSuperIO | https://github.com/acidanthera/VirtualSMC |
| WhateverGreen (disabled) | https://github.com/acidanthera/WhateverGreen |
| AppleALC (disabled) | https://github.com/acidanthera/AppleALC |
| IntelMausi | https://github.com/acidanthera/IntelMausi |
| NVMeFix | https://github.com/acidanthera/NVMeFix |
| RestrictEvents | https://github.com/acidanthera/RestrictEvents |
| USBToolBox, UTBDefault | https://github.com/USBToolBox/kext |
| BlueToolFixup | https://github.com/acidanthera/BrcmPatchRAM |
| IntelBluetoothFirmware, IntelBTPatcher (disabled) | https://github.com/OpenIntelWireless/IntelBluetoothFirmware |
| ForbiddenItlwm 0.6.0-beta.1 (Tahoe build) | https://github.com/retropineaple/ForbiddenItlwm (GPL-2.0; license text in `LICENSES/ForbiddenItlwm-GPL-2.0.txt`) |
| BTLEAdvFixup 0.9.2 (Tahoe build) | https://github.com/retropineaple/ForbiddenItlwm/tree/main/BTLEAdvFixup (GPL-3.0-only; license text in `LICENSES/BTLEAdvFixup-GPL-3.0-only.txt`) |
| AMFIPass | https://github.com/dortania/OpenCore-Legacy-Patcher |
| SimpleHDA (disabled) | Bundle ID `cn.laobamac.SimpleHDA`; the exact origin of this existing build has not been verified |
| SSDT-PLUG-DRTNIA, SSDT-EC-USBX-DESKTOP | Dortania templates / local configuration; not presented as an original, universally applicable ACPI implementation |

Where identified, upstream license texts are included under `LICENSES/`. Existing copyright notices inside the bundles are preserved. The MIT license at the repository root does not override GPL/BSD licenses, firmware terms or other third-party conditions. ForbiddenItlwm bundles contain Intel firmware subject to Intel’s redistribution terms; see the upstream `iwlwifi-firmware-license` in the linked source repository. Upstream source code and release history are accessible through the links above.

NullMoth drivers, Apple system files and installation packages are **not included**. NullMoth is subject to its upstream terms: https://github.com/nullmoth/nvidia-macos-driver .
