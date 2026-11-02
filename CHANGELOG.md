# Changelog

All notable changes to this project are documented here. Format loosely
follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions
follow [semantic versioning](https://semver.org/).

## [2.16.0 edition] — 2026-11-02

Fork of the Windows backend (muse-chan/mcp-vroid-forWindows-216) retargeted
to **VRoid Studio 2.16.0**.

### Changed

* The documented target VRoid version is now 2.16.0, for both the Steam
  edition and the official-site (non-Steam) edition launched through
  `MCP_VROID_EXE`. No driver code changes were needed: the Windows backend
  verifies the `VRoidStudio.exe` process rather than a versioned window
  title, and the official 2.14.0 → 2.16.0 changes (hair-shape saving in
  the Hairstyle editor, a Performance Monitor settings option, an eraser
  fix) do not move the tabs, parameter panels or export flow the anchors
  locate.
* Added `docs/vroid-216.md` and `docs/vroid-216.ja.md`: the 2.16.0 change
  summary, edition notes, and the verification checklist to run before
  real work.

### Fixed

* Windows Save As (`vroid_save_project` with a name) now clicks the blank
  part of the editor tab strip before sending Ctrl+Shift+S. On 2.16.0,
  raising the window alone did not give Unity keyboard focus, so the hotkey
  was dropped and the call failed with "Windows save dialog never appeared".
* VRM export now searches a wider horizontal band (0.30-0.70 of the width)
  of the VRM Settings modal for the format radio and the Name/Creators
  captions. The modal has a fixed pixel width, so in a 1920 px window its
  labels started left of the old band and export failed with
  "no VRM1.0 radio in the VRM Settings modal".

### Known limitations

* Anchors remain calibrated on 2.14.0; a 2.16.0 verification run
  (`scripts/windows_acceptance.py`) is still outstanding. The new
  hair-shape save UI in the 2.16.0 Hairstyle editor is untested, as is the
  hair editor in general.

## [Unreleased]

### Added

* **Windows 10/11 backend** (`mcp_vroid.driver.windows`): pywinauto/Win32
  input and window management, Pillow capture in physical pixels, a focus
  guard that accepts only the verified `VRoidStudio.exe` process and its
  owned dialogs, native Windows save dialogs and paths, and
  `%LOCALAPPDATA%\mcp-vroid` artifact directories. The same 18 MCP tools
  are exposed; `native/build.sh` is not needed on Windows.
* `MCP_VROID_TESSERACT` to point at the Tesseract executable.
* `MCP_VROID_EXE` to launch a non-Steam VRoid Studio (e.g. the BOOTH
  edition) instead of the Steam URL.
* `scripts/windows_acceptance.py` for attended end-to-end and DPI checks.

### Changed

* `vroid_screenshot(whole_screen=true)` on Windows captures the whole
  virtual screen (all monitors) instead of the primary monitor only.
* `vroid_release` on Windows returns `restored_window: null` instead of
  failing when Windows refuses to refocus the previous window.
* `pywin32` is now a declared Windows dependency.

### Known limitations

VRoid Studio's UI must be English. Except for the export flow, anchors
are still calibrated at 2560×1440; see the README's *Verified environments*.

## [0.1.0] — 2026-08-24

First public release.

### Added

* **MCP server** (`mcp-vroid`, stdio) exposing 18 tools over VRoid Studio:
  * lifecycle — `vroid_launch`, `vroid_status`, `vroid_release`
  * seeing — `vroid_screenshot`, `vroid_find_text`, `vroid_find_button`,
    `vroid_current_screen`
  * raw input — `vroid_click`, `vroid_drag`, `vroid_scroll`, `vroid_type`,
    `vroid_key`
  * flows — `vroid_new_character`, `vroid_open_tab`, `vroid_set_slider`,
    `vroid_set_color`, `vroid_export_vrm`, `vroid_save_project`
* **Driver engine** (`mcp_vroid.driver`): `grim` capture, tesseract OCR and
  OpenCV colour matching for locating, `hyprctl` for window management, and
  input through a `zwlr_virtual_pointer_unstable_v1` helper (pointer) plus
  X11 XTEST (keyboard and wheel).
* **`native/vpointer`** — a small C client for the Wayland virtual-pointer
  protocol, built by `native/build.sh`. Moves the real compositor cursor and
  needs no `/dev/uinput` access.
* **Session-environment recovery** so the server works when an MCP client
  launches it with a sanitised environment.
* **Focus guard** — every acting tool refuses to run unless VRoid Studio is
  the focused window; the idle screensaver is dismissed rather than typed
  into.
* **`vroid-driver` CLI** for driving the same engine from a shell.
* `scripts/smoke_test.py` — starts the server, lists tools, calls the
  read-only ones; never sends input.
* Documentation: README, [`docs/ui-map.md`](docs/ui-map.md).

### Known limitations

Calibrated against VRoid Studio 2.14.0 (English) at 2560×1440 / scale 1.25 on
Hyprland. See the README's *Limitations and brittleness* section.

[Unreleased]: https://github.com/yub4-arema/mcp-vroid-forWindows/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/nhodges/mcp-vroid/releases/tag/v0.1.0
