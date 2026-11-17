# OpenCore EFI – i7-6700K / ASUS Z170 / RTX 2080 SUPER

Sanitized backup of the active **OC_RTX_TEST** EFI, freshly read back from the test EFI on **November 17, 2026**. This repository documents a specific macOS Tahoe setup and provides a reference for similar hardware.

**Current user-reported status (November 17, 2026):** macOS starts to the login screen; the owner reports working Intel AX210 Wi-Fi, Metal acceleration on the RTX 2080 SUPER, and multiple active displays on that GPU with this OC_RTX_TEST snapshot. AirDrop has not yet been tested. The exact Metal workload and display refresh rates were not recorded. The earlier login-screen timeout workaround is documented below.

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
| macOS | Tahoe; exact installed build not recorded (ForbiddenItlwm AirDrop support requires a specifically supported build; see below) |
| SMBIOS model | `iMac20,1`; individual identifiers must be supplied |

Wi-Fi, RTX Metal acceleration and multi-display output are user-reported as working with this snapshot. The exact Metal workload and refresh rates were not recorded. Bluetooth, AirDrop, audio, sleep/wake and every USB port have not been independently verified.

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

This OC_RTX_TEST EFI contains boot settings for a **NullMoth driver that has already been installed separately**. Copying this EFI alone does not install an NVIDIA driver into a fresh macOS installation.

NullMoth components such as NVRM, NVAccel, NVRMFB and NVRMAGDC are installed inside macOS according to the [upstream instructions](https://github.com/nullmoth/nvidia-macos-driver) and loaded through its Auxiliary Kernel Collection. They are not also injected from `EFI/OC/Kexts` here. The NullMoth version actually installed on the target system was not read from macOS for this publication.

Relevant settings in this snapshot:

| Setting | Value / purpose |
|---|---|
| `nvfb=1 nvaccel=1` | Request the NullMoth framebuffer and accelerator |
| `nvfbheads=4` | RTX profile configured for up to four display heads; user reports multiple displays working |
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

The owner reports that login works normally afterward. Whether this procedure helps on other hardware has not been tested. Earlier attempts on an older driver setup produced a black screen with a movable pointer; that symptom predates this current working configuration. The owner now reports Metal acceleration working with the RTX setup.

### Resolution

`UEFI/Output/Resolution` is set to **`Max`** in this OC_RTX_TEST. This affects the boot console, not the macOS display settings. The owner reports multiple displays working, but the exact resolution and refresh rate for each display were not recorded. The owner reports RTX Metal acceleration working; this repository does not include a benchmark or Metal system report. Working 144 Hz output is not promised.

## Setup on similar hardware

1. Back up your own working EFI completely and keep an independent fallback boot medium.
2. Compare your hardware and firmware with this configuration. In particular, do not blindly copy ACPI paths, CPU, USB controller or graphics settings. The included tables are `SSDT-PLUG-DRTNIA.aml` and `SSDT-EC-USBX-DESKTOP.aml`.
3. In `EFI/OC/config.plist`, enter your own `SystemSerialNumber`, `MLB`, `SystemUUID` and `ROM` under `PlatformInfo/Generic`. The model `iMac20,1` has deliberately been retained. See [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) and the [Dortania guide](https://dortania.github.io/OpenCore-Install-Guide/) for setup instructions. Do not reuse identifiers from someone else's EFI.
4. Validate with **ocvalidate from OpenCore 1.0.8**. Passing structural validation does not prove that the hardware will boot.
5. Start with a separate test EFI. This RTX configuration targets macOS with NullMoth already installed; it is not a universal recovery or installation profile. In particular, do not blindly carry the IONDRVSupport block into an installation without the NVIDIA driver.
6. Wi-Fi uses [ForbiddenItlwm](https://github.com/retropineaple/ForbiddenItlwm) for the Intel AX210. The user reports Wi-Fi working on this machine. AirDrop is experimental and has not yet been tested here; the upstream project enables AirDrop only on specifically verified builds such as Tahoe 26.7.1–26.7.3 (builds 25G241/25G332/25G342). On other builds it should fall back to ordinary Wi-Fi with AWDL/AirDrop disabled. This machine’s exact macOS build is not recorded. The Intel Bluetooth controller and Bluetooth-to-AirDrop path need separate runtime verification. No Wi-Fi credentials are included.

The BIOS starting point for the NullMoth path is UEFI with CSM disabled and Above 4G Decoding enabled. The last known BIOS version was 3802; not all current settings were recorded again after earlier BIOS resets. The existing Booter memory settings were deliberately preserved instead of being replaced with a generic Skylake preset.

## USB, audio and kexts

`USBToolBox` + `UTBDefault` is a **temporary configuration, not a custom USB port map**. Create your own map and replace UTBDefault afterward. `XhciPortLimit` is disabled. One working port does not prove that all ports or internal Bluetooth are mapped correctly.

AppleALC, SimpleHDA and WhateverGreen are included as disabled components of the original snapshot. Do not enable everything at once. An audio layout entry alone does not establish that audio output works.

The complete list of versions and enabled states is in [docs/components.json](docs/components.json). ForbiddenItlwm replaces `itlwm`; `IntelBTPatcher.kext` remains bundled but disabled because its hook conflicts with `BTLEAdvFixup.kext`. Sources and licensing notes are in [THIRD_PARTY.md](THIRD_PARTY.md).

## Privacy and validation

- Fresh source: the active **OC_RTX_TEST** was read back from its EFI volume on November 17, 2026.
- Four personal SMBIOS fields were cleared; ROM was set to six zero bytes. This is a placeholder, not a usable personal identifier.
- `itlwm/IOKitPersonalities/itlwm/WiFiConfig` was cleared; no SSIDs or passwords were retained.
- No personal logs, partition tables, device serial numbers or backup archives were published.
- SMBIOS identifiers were sanitized in the public config. The EFI files otherwise match the live test EFI snapshot, except that private identifiers were cleared and documentation/checksum files were regenerated.
- The original EFI on the SSD was not modified. The complete personal backup remains local.
- The sanitized plist passes ocvalidate 1.0.8. The live EFI (with the owner’s private identifiers) was the one used for the user-reported Wi-Fi, RTX Metal acceleration and multi-display operation; the public sanitized copy is not boot-ready until a user supplies unique SMBIOS identifiers.

`SHA256SUMS` contains checksums for all published EFI files. [docs/privacy-check.json](docs/privacy-check.json) records the sanitization. These checks do not replace runtime testing on other hardware.

## License and contributions

The repository's existing MIT license applies to the author's own documentation and configuration contributions. **Bundled third-party binaries retain their respective licenses**; the repository license does not relicense them. See [THIRD_PARTY.md](THIRD_PARTY.md).

When reporting a problem, include the macOS build, driver version, display connection and relevant sanitized logs. Do not post serial numbers, UUIDs, MAC addresses or Wi-Fi credentials.
