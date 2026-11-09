# OpenCore EFI – i7-6700K / ASUS Z170 / RTX 2080 SUPER

Bereinigte Sicherung der **OC_EFI**, frisch vom Desktop kopiert am **9. November 2026**. Dieses Repository dokumentiert einen konkreten macOS-Tahoe-Aufbau und soll als nachvollziehbarer Ausgangspunkt für ähnliche Hardware dienen.

**Aktueller Erfahrungsstand:** Grafik und Anmeldung funktionieren laut Rückmeldung des Besitzers. Beim ersten Login muss einmal der Bildschirm-Timeout abgewartet werden; danach lässt sich die Anmeldung normal bedienen. Die genaue Wartezeit und die Ursache sind noch nicht bestimmt. Das ist ein Workaround, kein behobener Fehler. Eine Messung oder ein Systembericht zur Metal-Beschleunigung liegt hier nicht vor.

**Vor dem Booten eigene SMBIOS-Kennungen eintragen.** Diese öffentliche EFI enthält absichtlich keine persönliche Seriennummer, MLB, SystemUUID oder ROM/MAC-Kennung. Sie ist deshalb keine unverändert startfertige Kopie und ersetzt kein privates Vollbackup.

## Hardware und Basis

| Komponente | Aufbau |
|---|---|
| Mainboard | ASUS MAXIMUS VIII RANGER, Intel Z170 |
| Prozessor | Intel Core i7-6700K, Skylake |
| Dedizierte Grafik | NVIDIA GeForce RTX 2080 SUPER, PCI-ID `10DE:1E81` |
| Integrierte Grafik | Intel HD 530; diese Konfiguration ist auf die RTX ausgerichtet |
| Monitor | Acer VG270U P, DisplayPort an der RTX |
| Netzwerk | Intel I219-V |
| WLAN / Bluetooth | Intel AX210 / Intel Bluetooth |
| OpenCore | 1.0.8 DEBUG |
| macOS | Tahoe; genaue aktuell installierte Buildnummer nicht erfasst |
| SMBIOS-Modell | `iMac20,1`; individuelle Kennungen müssen ergänzt werden |

Die Hardwareliste beschreibt das Zielsystem, keine Zusicherung, dass jede Funktion geprüft wurde. Insbesondere WLAN, Bluetooth, Audio, Sleep/Wake, alle USB-Ports und eine bestimmte Bildwiederholrate sind mit diesem Snapshot nicht vollständig verifiziert.

## Was enthalten ist

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

Es gibt keine macOS-Systemdateien, Recovery-DMGs, InstallAssistant-PKGs, NullMoth-Installationspakete, NVRAM-Dumps oder privaten Bootlogs im Repository. Die versteckten `.contentVisibility`-Dateien gehören zum Snapshot; beim Kopieren mit übernehmen.

## NVIDIA / NullMoth

Die EFI enthält die Boot-Einstellungen für einen **bereits separat installierten NullMoth-Treiber**. Durch Kopieren dieser EFI allein bekommt eine frische macOS-Installation keinen NVIDIA-Treiber.

NullMoth-Komponenten wie NVRM, NVAccel, NVRMFB und NVRMAGDC werden nach der [Herstelleranleitung](https://github.com/nullmoth/nvidia-macos-driver) in macOS installiert und über dessen Auxiliary Kernel Collection geladen. Sie werden hier nicht zusätzlich aus `EFI/OC/Kexts` injiziert. Die tatsächlich auf dem Zielsystem installierte NullMoth-Version wurde für diese Veröffentlichung nicht aus macOS ausgelesen.

Relevante Einstellungen dieses Snapshots:

| Einstellung | Wert / Zweck |
|---|---|
| `nvfb=1 nvaccel=1` | NullMoth-Framebuffer und Beschleuniger angefordert |
| `nvfbheads=1` | Ein Display-Ausgang; absichtlich der aktuelle OC_EFI-Stand |
| `-nvrmnoflip` | Copy-Pfad statt Zero-copy-Scan-out; Teil des Login-Workarounds, keine isoliert bewiesene Ursache |
| `nvrmsettle=10000` | Verzögerung für die Turing-Initialisierung |
| `-nvkmsnosmooth` | Im übernommenen Treiberprofil gesetzt |
| `gfxrst=4` | Aus dem getesteten Grafikprofil beibehalten |
| WhateverGreen | Vorhanden, aber deaktiviert |
| `Kernel/Block` | `com.apple.iokit.IONDRVSupport`, `Exclude` |
| `csr-active-config` | Data `430A0000` |
| `SecureBootModel` | `Disabled` |
| `ResizeAppleGpuBars` / `ResizeGpuBars` | `0` / `-1` |

Die AMFI-Argumente `amfi_get_out_of_my_way=0x1 amfi=0x80 ipc_control_port_options=0` sind ebenfalls übernommen. **SIP-/AMFI-Schutz ist hier für den experimentellen Treiber reduziert.** Das ist keine allgemein empfohlene macOS-Sicherheitskonfiguration.

### Login-Workaround

1. macOS über diese passend personalisierte EFI starten.
2. Wenn der Login zunächst keine Eingabe annimmt, einmal den Bildschirm-Timeout abwarten.
3. Danach den Bildschirm wieder aufwecken und anmelden.

Der Besitzer meldet, dass die Anmeldung danach normal funktioniert. Ob dieser Ablauf auf anderer Hardware hilft, ist ungetestet. Frühere Versuche zeigten auch einen schwarzen Bildschirm mit beweglichem Mauszeiger. Ein beweglicher Zeiger allein bestätigt keine funktionierende Metal-Darstellung.

### Auflösung

`UEFI/Output/Resolution` steht in dieser OC_EFI auf **`Max`**. Das betrifft die Boot-Konsole, nicht die macOS-Displayeinstellungen. Native 2560×1440, Bildwiederholrate und Metal-Unterstützung müssen in macOS separat geprüft werden. Es wird keine funktionierende 144-Hz-Ausgabe versprochen.

## Einrichtung auf ähnlicher Hardware

1. Eigene funktionierende EFI vollständig sichern und ein unabhängiges Rückfall-Bootmedium behalten.
2. Hardware und Firmware mit dieser Konfiguration vergleichen. Besonders ACPI-Pfade, CPU, USB-Controller und Grafik dürfen nicht blind übernommen werden. Die Tabellen sind `SSDT-PLUG-DRTNIA.aml` und `SSDT-EC-USBX-DESKTOP.aml`.
3. In `EFI/OC/config.plist` unter `PlatformInfo/Generic` eigene Werte für `SystemSerialNumber`, `MLB`, `SystemUUID` und `ROM` einsetzen. Das Modell `iMac20,1` ist bewusst erhalten. [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) und der [Dortania-Guide](https://dortania.github.io/OpenCore-Install-Guide/) erläutern die Einrichtung. Keine Kennungen aus fremden EFIs verwenden.
4. Mit **ocvalidate aus OpenCore 1.0.8** prüfen. Ein bestandener Strukturtest ist kein Hardware-Bootnachweis.
5. Zunächst von einer separaten Test-EFI booten. Diese RTX-Konfiguration ist für ein bereits mit NullMoth eingerichtetes macOS gedacht; sie ist kein universelles Recovery-/Installationsprofil. Insbesondere der IONDRVSupport-Block darf nicht unüberlegt in eine Installation ohne NVIDIA-Treiber übernommen werden.
6. Für WLAN wird bei `itlwm` üblicherweise [HeliPort](https://github.com/OpenIntelWireless/HeliPort) verwendet. Es sind keine WLAN-Zugangsdaten hinterlegt. Die tatsächliche Tahoe-Kompatibilität der Funktionen auf dem eigenen System prüfen.

BIOS-Ausgangspunkt für den NullMoth-Pfad ist UEFI mit CSM aus und Above 4G Decoding an. Der zuletzt bekannte BIOS-Stand war 3802; nach früheren BIOS-Resets wurden nicht alle aktuellen Werte erneut erfasst. Die bestehenden Booter-Speichereinstellungen wurden bewusst nicht durch ein generisches Skylake-Preset ersetzt.

## USB, Audio und Kexts

`USBToolBox` + `UTBDefault` ist eine **Übergangslösung, keine individuelle USB-Port-Map**. Eine eigene Map erstellen und danach UTBDefault ersetzen. `XhciPortLimit` ist deaktiviert. Ein funktionierender einzelner Anschluss beweist nicht, dass alle Ports oder internes Bluetooth korrekt zugeordnet sind.

AppleALC, SimpleHDA und WhateverGreen liegen als deaktivierte Bestandteile des Original-Snapshots bei. Nicht alle gleichzeitig aktivieren. Ein Audio-Layout-Eintrag allein belegt keine funktionierende Tonausgabe.

Die vollständige Liste mit Versionsnummern und Aktivierungszustand steht in [docs/components.json](docs/components.json). Quellen und Lizenzhinweise: [THIRD_PARTY.md](THIRD_PARTY.md).

## Datenschutz und Prüfumfang

- Frische Quelle: vom Benutzer ausdrücklich ausgewählte **OC_EFI**, nicht OC_RTX_TEST und nicht ein älteres Archiv.
- Vier persönliche SMBIOS-Felder geleert; ROM auf sechs Nullbytes gesetzt. Das ist ein Platzhalter, keine verwendbare eigene Kennung.
- `itlwm/IOKitPersonalities/itlwm/WiFiConfig` geleert; keine SSIDs oder Passwörter übernommen.
- Keine persönlichen Logs, Partitionstabellen, Geräte-Seriennummern oder Sicherungsarchive veröffentlicht.
- Abgesehen von diesen beiden plist-Dateien bleiben die EFI-Dateien bytegleich zur frischen Sicherung.
- Die ursprüngliche EFI auf der SSD wurde nicht verändert. Die persönliche Vollsicherung bleibt lokal.
- Die bereinigte plist besteht ocvalidate 1.0.8; ein Boot mit leeren Identitätsfeldern wurde nicht getestet und ist nicht das Ziel.

`SHA256SUMS` enthält Prüfsummen aller veröffentlichten EFI-Dateien. [docs/privacy-check.json](docs/privacy-check.json) dokumentiert die Bereinigung. Diese Prüfungen ersetzen keine Laufzeittests auf fremder Hardware.

## Lizenz und Beiträge

Die vorhandene MIT-Lizenz des Repositorys gilt für die eigenen Dokumentations- und Konfigurationsanteile, soweit diese vom Autor stammen. **Mitgelieferte Drittanbieter-Binärdateien behalten ihre jeweiligen Lizenzen**; die Repository-Lizenz lizenziert sie nicht neu. Siehe [THIRD_PARTY.md](THIRD_PARTY.md).

Bei Fehlerberichten bitte macOS-Build, Treiberversion, Anschluss und relevante bereinigte Logs nennen. Keine Seriennummern, UUIDs, MAC-Adressen oder WLAN-Zugangsdaten posten.
