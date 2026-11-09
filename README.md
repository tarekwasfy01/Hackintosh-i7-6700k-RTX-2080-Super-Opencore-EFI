# OpenCore EFI – i7-6700K / ASUS Z170 / RTX 2080 SUPER

Sanitized backup of **OC_EFI**, freshly copied from the desktop on **November 9, 2026**. This repository documents a specific macOS Tahoe setup and provides a reference for similar hardware.

**Current user-reported status:** Display output and login work. At the initial login screen, wait for the display to time out once; login then becomes responsive. The exact delay and underlying cause have not been determined. This is a workaround, not a resolved bug. No measurement or system report confirming Metal acceleration is included here.

**Generate and enter your own SMBIOS identifiers before booting.** This public EFI deliberately excludes the owner's serial number, MLB, SystemUUID and ROM/MAC identifier. It is not ready to boot unchanged and does not replace a complete private backup.

## Hardware and software

| Component | Configuration |
|---|---|
| Motherboard | ASUS MAXIMUS VIII RANGER, Intel Z170 |
| CPU | Intel Core i7-6700K, Skylake |
| Discrete GPU | NVIDIA GeForce RTX 2080 SUPER, PCI ID `10DE:1E81` |
| Integrated GPU | Intel HD 530; this configuration targets the RTX |
| Monitor | Acer VG270U P, connected to the RTX via DisplayPort |
| Ethernet | Intel I219-V |
| Wi-Fi / Bluetooth | Intel AX210 / Intel Bluetooth |
| OpenCore | 1.0.8 DEBUG |
| macOS | Tahoe; the exact currently installed build was not recorded |
| SMBIOS model | `iMac20,1`; individual identifiers must be supplied |

This list describes the target system, not a guarantee that every feature has been tested. Wi-Fi, Bluetooth, audio, sleep/wake, all USB ports and specific refresh rates have not been fully verified with this snapshot.

## Contents

```text
EFI/
  BOOT/BOOTx64.efi
  OC/
    OpenCore.efi
    config.plist
    ACPI/
    Drivers/
    Kexts/
    Tools/
docs/
  components.json
  privacy-check.json
SHA256SUMS
THIRD_PARTY.md
```

The repository does not contain macOS system files, recovery DMGs, InstallAssistant packages, NullMoth installation packages, NVRAM dumps or private boot logs. The hidden `.contentVisibility` files are part of the snapshot; include them when copying it.

## NVIDIA / NullMoth

The EFI contains boot settings for a **NullMoth driver that has already been installed separately**. Copying this EFI alone does not install an NVIDIA driver into a fresh macOS installation.

NullMoth components such as NVRM, NVAccel, NVRMFB and NVRMAGDC are installed inside macOS according to the [upstream instructions](https://github.com/nullmoth/nvidia-macos-driver) and loaded through its Auxiliary Kernel Collection. They are not also injected from `EFI/OC/Kexts` here. The NullMoth version actually installed on the target system was not read from macOS for this publication.

Relevant settings in this snapshot:

| Setting | Value / purpose |
|---|---|
| `nvfb=1 nvaccel=1` | Request the NullMoth framebuffer and accelerator |
| `nvfbheads=1` | One display head; deliberately preserves the current OC_EFI configuration |
| `-nvrmnoflip` | Copy path instead of zero-copy scan-out; part of the configuration used with the login workaround, not independently proven to cause it |
| `nvrmsettle=10000` | Delay for Turing initialization |
| `-nvkmsnosmooth` | Set in the inherited driver profile |
| `gfxrst=4` | Retained from the tested graphics profile |
| WhateverGreen | Included but disabled |
| `Kernel/Block` | `com.apple.iokit.IONDRVSupport`, `Exclude` |
| `csr-active-config` | Data `430A0000` |
| `SecureBootModel` | `Disabled` |
| `ResizeAppleGpuBars` / `ResizeGpuBars` | `0` / `-1` |

The AMFI arguments `amfi_get_out_of_my_way=0x1 amfi=0x80 ipc_control_port_options=0` are also retained. **SIP/AMFI protections are reduced for the experimental driver.** This is not a generally recommended macOS security configuration.

### Login workaround

1. Boot macOS using this EFI after supplying suitable personal identifiers and hardware settings.
2. If the login screen initially ignores input, wait for the display to time out once.
3. Wake the display and log in.

The owner reports that login works normally afterward. Whether this procedure helps on other hardware has not been tested. Earlier attempts also produced a black screen with a movable pointer. A movable pointer alone does not confirm working Metal rendering.

### Resolution

`UEFI/Output/Resolution` is set to **`Max`** in this OC_EFI. This affects the boot console, not the macOS display settings. Native 2560×1440 output, refresh rate and Metal support must be checked separately inside macOS. Working 144 Hz output is not promised.

## Setup on similar hardware

1. Back up your own working EFI completely and keep an independent fallback boot medium.
2. Compare your hardware and firmware with this configuration. In particular, do not blindly copy ACPI paths, CPU, USB controller or graphics settings. The included tables are `SSDT-PLUG-DRTNIA.aml` and `SSDT-EC-USBX-DESKTOP.aml`.
3. In `EFI/OC/config.plist`, enter your own `SystemSerialNumber`, `MLB`, `SystemUUID` and `ROM` under `PlatformInfo/Generic`. The model `iMac20,1` has deliberately been retained. See [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) and the [Dortania guide](https://dortania.github.io/OpenCore-Install-Guide/) for setup instructions. Do not reuse identifiers from someone else's EFI.
4. Validate with **ocvalidate from OpenCore 1.0.8**. Passing structural validation does not prove that the hardware will boot.
5. Start with a separate test EFI. This RTX configuration targets macOS with NullMoth already installed; it is not a universal recovery or installation profile. In particular, do not blindly carry the IONDRVSupport block into an installation without the NVIDIA driver.
6. Wi-Fi with `itlwm` typically uses [HeliPort](https://github.com/OpenIntelWireless/HeliPort). No Wi-Fi credentials are included. Check actual Tahoe compatibility and functionality on your own system.

The BIOS starting point for the NullMoth path is UEFI with CSM disabled and Above 4G Decoding enabled. The last known BIOS version was 3802; not all current settings were recorded again after earlier BIOS resets. The existing Booter memory settings were deliberately preserved instead of being replaced with a generic Skylake preset.

## USB, audio and kexts

`USBToolBox` + `UTBDefault` is a **temporary configuration, not a custom USB port map**. Create your own map and replace UTBDefault afterward. `XhciPortLimit` is disabled. One working port does not prove that all ports or internal Bluetooth are mapped correctly.

AppleALC, SimpleHDA and WhateverGreen are included as disabled components of the original snapshot. Do not enable everything at once. An audio layout entry alone does not establish that audio output works.

The complete list of versions and enabled states is in [docs/components.json](docs/components.json). Sources and licensing notes are in [THIRD_PARTY.md](THIRD_PARTY.md).

## Privacy and validation

- Fresh source: the owner explicitly selected **OC_EFI**, not OC_RTX_TEST or an older archive.
- Four personal SMBIOS fields were cleared; ROM was set to six zero bytes. This is a placeholder, not a usable personal identifier.
- `itlwm/IOKitPersonalities/itlwm/WiFiConfig` was cleared; no SSIDs or passwords were retained.
- No personal logs, partition tables, device serial numbers or backup archives were published.
- Except for these two plist files, the EFI files are byte-identical to the fresh backup.
- The original EFI on the SSD was not modified. The complete personal backup remains local.
- The sanitized plist passes ocvalidate 1.0.8. Booting with empty identity fields was not tested and is not the intended use.

`SHA256SUMS` contains checksums for all published EFI files. [docs/privacy-check.json](docs/privacy-check.json) records the sanitization. These checks do not replace runtime testing on other hardware.

## License and contributions

The repository's existing MIT license applies to the author's own documentation and configuration contributions. **Bundled third-party binaries retain their respective licenses**; the repository license does not relicense them. See [THIRD_PARTY.md](THIRD_PARTY.md).

When reporting a problem, include the macOS build, driver version, display connection and relevant sanitized logs. Do not post serial numbers, UUIDs, MAC addresses or Wi-Fi credentials.
