# DisMenu

Python rewrite of the legacy batch menu `BATCH/DisMenu.bat` to operate on Windows image files (WIM / ESD) using DISM and (optionally) wimlib.

## Table of Contents

1. Overview
2. Requirements
3. Quick Start
4. Operational Notes
5. Status Line Indicators
6. Settings & Persistence
7. Menu 19 Defaults
8. Console Window Positioning
9. Packaging (README + binaries)
10. wimlib-imagex Integration
11. Elevation Strategy (UAC)
12. Quick Guide: auto-py-to-exe
13. Console Colors (VT / colorama)
14. Temporary Directory Warnings
15. Menu Entries (Feature List)
16. Original Italian Text (Appendix)

## 1. Overview

DisMenu is an interactive console tool to list, mount, modify and export Windows image indexes from `.wim` / `.esd` files. It wraps common DISM operations and can optionally leverage `wimlib-imagex` for faster export/convert steps with progress feedback.

## 2. Requirements

- Windows
- Administrator privileges (the script attempts self‑elevation)
- DISM available in PATH (standard on Windows)
- (Optional) `wimlib-imagex.exe` in the same folder or in PATH for accelerated export/convert

## 3. Quick Start

Open an elevated PowerShell in the project folder and run:

```powershell
python .\DisMenu.py
```

If not elevated, the script relaunches with UAC.

## 4. Operational Notes

- Modification operations (enable/disable feature, add package/driver, component cleanup) are not allowed on `.ESD` images.
- During feature filtering DISM output is forced to English to ensure reliable text matching.
- If you pick `recovery` compression but the destination is `.wim`, it is transparently changed to `max`.
- Temporary mount directories are created under a random folder inside `%TEMP%` and auto‑unmounted.
- Error log: `%TEMP%/DisMenu_Errors.log`.
- Verbose (captured stdout/stderr) log: enable via menu 19 → `%TEMP%/DisMenu_Verbose.log`.
- Set a custom base mount directory via menu 18 (helpful if `%TEMP%` has low free space).
- Fast export/convert: if `wimlib-imagex` is available and backend is `wimlib` (or `auto` selects it) the tool uses it instead of DISM and shows a single‑line percentage progress.
- View recent logs via menu 17 (Error and Verbose).
- Backend + wimlib indicator: status line shows `[wimlib …]` next to `[ExportBackend: ...]`; if version detected (e.g. `1.14.x`) it is displayed, else the source (`local next to exe`, `system PATH`) or `missing`.
- Help entry: menu 20 opens this README.
- Utility: menu 21 opens the log folder (`%TEMP%`).
- Single-line progress bars: long DISM ops (Mount/Unmount, Add-Package/Driver, Cleanup-Image, Enable/Disable-Feature, DISM export, boot.wim operations) and wimlib show an updating line to avoid flooding the console. Toggle in menu 19.
- Informational spinner: for commands without reliable percentages (Get-Features, Get-WimInfo) a spinner is shown while output is captured. Toggle in menu 19.
- Menu 15 (Integrity check) uses `DISM /Cleanup-Image` with `CheckHealth` and `ScanHealth`.
- Temporary mount folders created in the session are tracked and removed robustly; on exit a cleanup is attempted. Force manual cleanup with menu 22 (reports freed space).

## 5. Status Line Indicators

The status line (below the main menu) summarizes current runtime settings:

- `[MountDirBase: ...]` — base folder for temporary mounts; `%TEMP%` if unset (set in menu 18)
- `[Verbose: on|off]` — detailed logging (menu 19)
- `[ExportBackend: auto|dism|wimlib]` — active export backend (menu 19)
- `[wimlib ...]` — `wimlib-imagex` state: version (e.g. `1.14.x`), origin (`local`, `PATH`), or `missing`

## 6. Settings & Persistence

The following options persist across sessions: base mount folder, verbose logging, export backend (`auto` / `dism` / `wimlib`), single‑line percentage bar, informational spinner, console tweaks (VT, QuickEdit, centering, restore position, AlwaysOnTop).

Configuration file locations (first existing wins):

1. Preferred: `%APPDATA%\DisMenu\settings.json`
2. Fallback (if `%APPDATA%` unavailable): `PYTHON/POSTINSTALL/config/settings.json`

Modify via menu entries:

- 18: Set base folder for temporary mounts (empty = `%TEMP%`).
- 19: Console options (VT, QuickEdit, center, restore position, AlwaysTop), verbose log, export backend, percentage bar mode (applies to wimlib + DISM operations exposing percent) and spinner (Enter = apply defaults below).

Reset to defaults: delete `settings.json` and restart.

## 7. Menu 19 Defaults (Blank Enter)

Pressing Enter without input in menu 19 applies:

- VT (Virtual Terminal): off — prompt: `on/off, Enter=off`.
- QuickEdit: on — prompt: `on/off, Enter=on`.
- Verbose log: on — prompt: `on/off, Enter=on` (a confirmation hint is shown).
- Center console at startup: on — prompt: `on/off, Enter=on`.
- Restore saved position at startup: off — prompt: `on/off, Enter=off`.
- Always on top: off — prompt: `on/off, Enter=off` (takes effect immediately).
- Center retries: Enter keeps current — prompt: `(Enter=keep)`.
- Delay between retries (ms): Enter keeps current — prompt: `(Enter=keep)`.
- Percentage progress bar (wimlib/DISM): `line` / `off`, Enter=`line` (single updating line; `off` hides it).
- Informational spinner (Get-Features, Get-WimInfo): on — prompt: `on/off, Enter=on`.
- Export backend: auto — prompt: `auto/dism/wimlib, Enter=auto`.

Hints are shown after toggling VT, QuickEdit, Verbose, Center and Restore.

## 8. Console Window Position & Behavior (Menu 19)

- Center console at startup: centers on the monitor containing the cursor (multi‑monitor aware). Default: on.
- Restore saved position at startup: if a saved position exists and this is on, it overrides centering. Default: off.
- Save current position as preferred: choose `S` inside menu 19 or press `S` in the main menu shortcut.
- Reliability tuning: configure `Center retries` (0–5) and `Delay between retries (ms)` (0–1000) for cases where the window repositions slowly.
- Config keys: `center_console` (bool), `restore_console_pos` (bool), `saved_console_pos` ({ x, y }), `center_retry` (int), `center_delay_ms` (int), `last_console_pos` (last computed), `always_on_top` (bool).
- Always on top: keeps the window top-most when enabled (default off).

## 9. Packaging (README Inclusion)

If you build an executable you can ship `README_dismenu.md` next to it so menu 20 opens it directly.

auto-py-to-exe:

- Add under "Additional Files": `README_dismenu.md;.`

PyInstaller:

```bash
--add-data "README_dismenu.md;."
```

## 10. wimlib-imagex Integration

Download Windows binaries from the official wimlib project site (search "wimlib downloads"). You need `wimlib-imagex.exe`.

To bundle without relying on PATH place `wimlib-imagex.exe` beside the executable or add it as an extra file:

- auto-py-to-exe: `wimlib-imagex.exe;.` in Additional Files
- PyInstaller: `--add-binary "wimlib-imagex.exe;."`
- One-file mode: extracted into `_MEIPASS`; code resolves path at runtime.

## 11. Elevation Strategy (UAC)

Launching unelevated triggers a self‑relaunch with UAC elevation.

Recommended: build a "Window Based" executable (no console) so only the elevated instance allocates a console. The code:

1. Hides any pre‑elevation console (if present)
2. Relaunches elevated
3. Allocates a fresh console in the elevated instance

When running as script (`python DisMenu.py`) or a Console based exe you will simply see the current console; the non‑elevated stub exits after spawning the elevated one.

## 12. Quick Guide: auto-py-to-exe (Window Based + Extra Files)

1. Install: `pip install auto-py-to-exe`
2. Launch: `auto-py-to-exe`
3. Script Location: choose `DisMenu.py`
4. Onefile/Onefolder: choose freely ("One Directory" recommended for clarity)
5. Console Window: "Window Based" (only elevated instance shows a console)
6. Additional Files:
   - `README_dismenu.md;.`
   - `wimlib-imagex.exe;.` (optional)
7. (Optional) Icon: `Python.ico` or custom
8. Click "Convert .py to .exe"
9. Run `dismenu.exe` (it will auto‑elevate). Status line will include `[wimlib: ...]`.

Notes:

- In "One File" mode extra files are extracted at runtime (supported via `_MEIPASS`). Using "One Directory" keeps them visible next to the exe.

## 13. Console Colors (VT & colorama)

Menu 19 lets you enable VT (Virtual Terminal) at startup. If `on`, ANSI sequences are used directly; if `off`, native console behavior is retained.

- Disabling QuickEdit prevents accidental pauses when clicking.
- Optional: install `colorama` for reliable ANSI→Win32 translation even with `VT: off`.

Install:

```bash
pip install colorama
```

## 14. Temporary Directory Warnings

In one‑file packaging (PyInstaller / auto-py-to-exe) you may sometimes see:

```
Failed to remove temporary directory: R:\TEMP\...
```

Usually harmless; it stems from cleanup of the extraction directory. The program sets a stable working dir (the exe folder) to reduce locks. Prefer "One Directory" if you want to avoid such transient messages entirely.

## 15. Menu Entries

- 1: List image indexes
- 2: Mount image (RW) and unmount
- 3: Mount image (RO) and unmount
- 4: Show current mounts
- 5: Clean orphaned mounts
- 6: List features (with filter)
- 7: Enable feature
- 8: Disable feature
- 9: Add package (CAB/MSU)
- 10: Add driver
- 11: Component cleanup
- 12: Add drivers to boot.wim (idx 2)
- 13: Remove drivers from boot.wim by folder
- 14: Export indexes to new WIM/ESD
- 15: Image integrity check
- 16: Convert ESD → WIM
- 17: Show recent logs
- 18: Settings: mount folder
- 19: Settings: console / verbose / backend
- 20: Help (README)
- 21: Open log folder
- 22: Purge temporary mount folders created this session (shows freed space)
- 23: Unmount an existing mount folder (if you left a mount from 2/3)
---
## License

Specify your license here (e.g. MIT). If not decided yet, add a `LICENSE` file later.

## Contributing

Issues and pull requests are welcome. Please include reproduction steps for bugs and indicate whether DISM, wimlib, or both are involved.

## Disclaimer

Use at your own risk. Modifying Windows images can produce unusable builds if improper combinations of features, packages or drivers are applied.

---
