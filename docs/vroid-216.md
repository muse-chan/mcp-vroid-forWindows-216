# VRoid Studio 2.16.0 notes

This fork targets **VRoid Studio 2.16.0** on Windows. The driver code is
unchanged from the Windows backend it forked; this document records why
that is expected to work, what is different in 2.16.0, and what to verify
before doing real work.

## What changed in VRoid Studio 2.14.0 → 2.16.0

From pixiv's official update announcements:

* **v2.16.0** (2026-10-28)
  * New: hair shapes can now be saved in the Hairstyle editor.
  * New: a "Performance Monitor" option in Settings. When enabled, FPS,
    VRAM usage and RAM usage are drawn in the bottom-left corner of the
    editor while a model is being edited.
  * Fix: using the eraser with Alpha Lock enabled could draw black marks.
* No 2.15.x announcement appears in the Steam news feed, and no announced
  change in this range touches the editor's tab strip, the Parameters
  panel, or the Export-as-VRM flow.

## Why no code changes were needed

* The Windows backend identifies VRoid by its process
  (`VRoidStudio.exe`), never by a versioned window title, so a version
  bump does not break window discovery or the focus guard.
* The locating anchors are UI text read by OCR (`Export`, `Parameters`,
  the tab names, …) plus fractional positions measured on 2.14.0. None of
  the 2.14.0 → 2.16.0 changes rename or move those elements.
* Non-Steam installs were already supported: `MCP_VROID_EXE` points at
  the installed `VRoidStudio.exe`, which covers the official-site edition
  as well as the BOOTH edition.

## Editions

| Edition | How it is launched |
|---|---|
| Steam (appid `1486350`) | Through Steam, as before |
| Official site (standalone installer) | `MCP_VROID_EXE` = full path of the installed `VRoidStudio.exe` |
| BOOTH | `MCP_VROID_EXE`, as before |

## Verify before real work

The anchors are still *calibrated* on 2.14.0, so treat 2.16.0 support as
"expected to work, not yet verified" until this checklist has been run
once on the target machine:

1. Set VRoid Studio's UI language to **English**
   (kebab menu `⋮` → Settings → Language → English). The OCR anchors do
   not match a Japanese UI.
2. Leave **Performance Monitor off** (Settings). When it is on it draws
   over the bottom-left of the editor and can pollute captures and OCR.
3. Prefer a display scale of **100%**. Fractional anchors were calibrated
   at 2560×1440 (the export flow excepted); other resolutions may need
   re-measuring.
4. Run `uv run python scripts/smoke_test.py`, then call `vroid_status`.
5. On a **throwaway model**, run the acceptance phases in order:
   `create`, `edit`, `color`, `save`, `export10`
   (`uv run python scripts/windows_acceptance.py <phase>`).
   Confirm the exported file is a valid VRM 1.0.
6. Record the result in the README's *Verified environments* table.
7. If a phase fails, do not force it. Note which text or anchor was not
   found, re-measure following `docs/ui-map.md`, and record the new
   measurements with the environment they were taken in.

## 2.16.0-specific cautions

* The Hairstyle editor gained hair-shape saving UI in 2.16.0. The hair
  editor was already untested by this server; treat every hair-editor
  flow as unverified regardless of version.
* The pixiv sign-in / Premium UI (added in 2.14.0) is not used by any
  flow and needs no account.
