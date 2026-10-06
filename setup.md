# PaperKB

Open `paperkb.kicad_pro` in KiCad. The schematic and PCB are in this directory.

## Installed tools and libraries

Inventory checked on the original machine on 2026-10-05.

| Item | Version | Purpose / setup |
| --- | --- | --- |
| KiCad | 10.0.4 | Schematic and PCB editors, standard symbol/footprint libraries, and `kicad-cli`. Installed through the NixOS configuration at `/home/spacecat/nixos/module/home/app/kicad.nix`. |
| [Keyboard footprints placer (kbplacer)](https://github.com/adamws/kicad-kbplacer) | 0.19 | Keyboard key placement and routing plugin. Installed through KiCad's Plugin and Content Manager. |
| [marbastlib](https://github.com/ebastler/marbastlib) | 2026.03.22 | Keyboard symbols and footprints, including Gateron low-profile switches. Installed through KiCad's Plugin and Content Manager. |
| FallCTF custom footprint library | Local conversion | Contains `FPC-SMD_AFC01-S12FCA-00`, the AFC01-S12FCA-00 display connector (LCSC C262661). Register the `.pretty` directory with library nickname `FallCTF`. |
| [easyeda2kicad.py](https://github.com/uPesy/easyeda2kicad.py) | 1.0.1 | Tool used to convert the FallCTF footprint; commit `fff10a38619963d7cb1c57d779655a9ea4572e95`. Recorded in the conversion README; current installation was not checked. Only needed to regenerate the footprint. |

The installed PCM package inventory contains kbplacer and marbastlib. No additional plugins were found in the user plugin directories for KiCad 10.0.

## Copy the FallCTF footprint to the other machine

Run on the original machine after copying the PaperKB project:

```bash
scp -r \
  /home/spacecat/uiuc/pwny/fallctf-2026-badge/hardware/PCB/kicad/FallCTF.pretty \
  spacecat@172.27.63.119:~/projects/paperkb/paperkb/
```

On the destination, open **Preferences → Manage Footprint Libraries → Project Specific Libraries**, add the copied `FallCTF.pretty` directory, and use:

- Nickname: `FallCTF`
- Path: `${KIPRJMOD}/FallCTF.pretty`

Save the resulting project `fp-lib-table` alongside the design files and commit it with the footprint directory. The copy command does not register the library automatically.

The footprint has contacts 1–12 and mechanical mounting pads 13 and 14. No 3D model is bundled. Conversion notes and verification files are in `/home/spacecat/uiuc/pwny/fallctf-2026-badge/hardware/PCB/kicad/` on the original machine.

## Restore the keyboard library setup

Install kbplacer and marbastlib through KiCad's Plugin and Content Manager on the destination. KiCad settings and library tables reference library files; copying settings alone does not copy those files or install plugins.

The project uses `marbastlib-xp-gateron_lp:SW_KS33_1u`. The original machine has an extra global footprint library entry named `marbastlib-xp-gateron_lp`, pointing to:

```text
${KICAD10_3RD_PARTY}/footprints/com_github_ebastler_marbastlib/marbastlib-xp-gateron_lp.pretty
```

Add that nickname in **Manage Footprint Libraries** after installing marbastlib, or update the project's footprint assignments to the installed `PCM_marbastlib-xp-gateron_lp` nickname.

Some schematic assignments currently use `PCM_marbastlib-gateron_lp:SW_KS33_1u`, which does not match an entry in the original machine's footprint library table. Review those assignments and select the intended installed footprint before updating the PCB from the schematic.

## Original machine locations

- Settings and global library tables: `~/.config/kicad/10.0/`
- PCM plugin: `~/.local/share/kicad/10.0/3rdparty/plugins/com_github_adamws_kicad-kbplacer/`
- marbastlib footprints: `~/.local/share/kicad/10.0/3rdparty/footprints/com_github_ebastler_marbastlib/`
- marbastlib symbols: `~/.local/share/kicad/10.0/3rdparty/symbols/com_github_ebastler_marbastlib/`

Keep custom libraries in the project and use `${KIPRJMOD}` paths to make future transfers portable. Ignore local preferences (`*.kicad_prl`), lock files (`~*.lck`), automatic backups, `.history/`, and `kbplacer.log` in Git.
