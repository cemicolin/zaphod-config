# Zaphod ZMK config

[ZMK](https://zmk.dev) firmware config for the Zaphod, a 34-key wireless
keyboard with a Sharp memory LCD, designed by Pete Johanson.

This is a personal fork of
[petejohanson/zaphod-config](https://github.com/petejohanson/zaphod-config).
The board definition, bongo cat and original keymap are Pete's work.

## What this fork adds

- **ZMK Studio:** edit the keymap live at <https://zmk.studio>, no rebuild.
- **Local builds:** one script builds the firmware in a container and can
  flash it.
- **Display fixes for ZMK v0.3:** a board-level Sharp LCD driver
  (`boards/arm/zaphod/zaphod_ls0xx.c`) that stops stray rows and garbled layer
  names, plus a correctly inverted theme and no scrollbars.
- **Bluetooth tuning:** more reliable reconnects and maximum radio power.
- **A board definition for newer ZMK** (Zephyr 4.x), ready for the next
  release.

## Layout

| What | Where |
| --- | --- |
| Keymap | `config/zaphod.keymap` (or edit live at <https://zmk.studio>) |
| Settings (Bluetooth, display widgets, Studio) | `config/zaphod.conf` |
| ZMK version | `config/west.yml` (`revision:`) |
| Board definition, ZMK v0.3 | `boards/arm/zaphod/` |
| Board definition, newer ZMK (Zephyr 4.x) | `boards/petejohanson/zaphod/` |

The keymap has seven layers: Base (QWERTY with home-row mods), Nav, BT Sync,
Num, Sym, Media and Fun.

`boards/shields/zaphod_lite/` comes from upstream unchanged. This fork doesn't
build or test it.

## Building

- **Locally on a Mac or Linux:** follow the [macOS setup guide](docs/setup-mac.md),
  then run `./build-local.sh` to get `firmware/zaphod.uf2`. Add `--flash` to
  copy it to the keyboard after you double-tap reset. It uses Apple container,
  OrbStack or Docker.
- **Locally on Windows:** `.\build-local.ps1` and the
  [Windows setup guide](docs/setup-windows.md) are included but **untested**.
- **GitHub Actions:** every push builds a `.uf2` you can download from the
  Actions tab.

## ZMK Studio locking is off

`CONFIG_ZMK_STUDIO_LOCKING=n` means the keymap needs no `&studio_unlock` key.
It also means any computer the keyboard is connected to can change the keymap
without a key press. To require an unlock, remove that line from
`config/zaphod.conf` and add a `&studio_unlock` binding to the keymap.

## Upgrading to a newer ZMK release

The board exists in two formats because ZMK v0.3 uses Zephyr 3.5 and newer
ZMK uses Zephyr 4.x, which changed how boards are defined. Both folders can't
exist together on newer ZMK, so upgrading is:

1. In `config/west.yml`, set `revision:` to the new release tag (e.g. `v0.4`).
2. In `build.yaml`, change `board: zaphod` to `board: zaphod//zmk`.
3. Delete `boards/arm/zaphod/`.
4. Run `./build-local.sh`, flash, and use it for a day. To go back, revert
   those three changes.

Your keymap, settings and anything saved with ZMK Studio carry over.

The newer-ZMK board has been built against ZMK `main` (Zephyr 4.1, LVGL 9),
but not yet tested on the hardware. It does not have the Sharp LCD driver from
the v0.3 board yet.

Keep display code changes in sync: the bongo cat and status screen code exists
in both board folders, ported to each version's graphics library (LVGL 8 vs 9).

## License

There is no repo-wide license file, here or upstream. Each source file carries
its own SPDX header: MIT for the board and widget code, Apache-2.0 for the
display driver, which is based on Zephyr's `ls0xx` driver.
