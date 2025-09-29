# Dismenu (Python)

Python replica of the Batch Menu Batch/Dismenu.bat 'for WIM/ESD operations with Dism.

## Requirements

- Windows
- execution as an administrator (the script tries the elevation automatically)
- Dism available in the path (pre -installed on Windows)

## Start

Perform as an administrator:

`` `Powershell
# from the folder C: \ Sourcecode \ Python \ Postinstall
Python. \ Dismenu.py
`` ``

## Operational Notes

- Edit operations (ENABLE/Disable Feature, Add-Package/Driver, Cleanup) are not allowed on .SD files.
- During the filtering of feature, the Dism output is forced to English for reliable research.
- If you choose compression `recovery` but the destination is` .wim`, is automatically converted into `Max`.
- Temporary Mounts are created in a random folder inside `` Temp%`and automatically disassembled.
- In case of errors, check the log: `` `` `` `` `` `` `` `othi%%/
- Activate the detailed log (verbose) from menu 19 to save full strudouts/strors in `` `` temp%/dysmenu_verbose.gog`.
- Sets the base folder for the Mount from menu 18 (useful if `` `temp%%` has no sufficient space).
- For fast exports/converts, if `` Wimlib-IMAGEX` is installed and selections Backend `Wimlib` (or leave` Auto`), will be used instead of Dism; With Wimlib you will also see a percentage advance bar.
- Show the latest logs directly from menu 15 (Error Log and Verbose Log).
- Wimlib indicator and backnd: in the state line see `[wimlib ...]` next to `[exportback: ...]`. If available, it shows the version (e.g. `1.14.x`); Otherwise it indicates the source (e.g. `local (next to the executable)` or `path of system ') or` absentee`.
- Aid item: from menu 20 you can open this Readme.
- Utility: from menu 21 you can open the log folder (%temp%).
-Percentage bars on a single line: long Dism operations (Mount/UNMOUNT, Add-Package/Driver, Cleanup-IMAGE, ENABLE/Disable-Features, Export with Dism, Boot.Wim operations) and Wimlib show the percentage advancement on a single line not to "dirty" the console; You can deactivate the bars from menu 19.
-Information spinner on a single line: for information controls without reliable percentages (e.g. Get-Feutures, Get-Wiminfo) a spinner is shown while the output is captured in full; The spinner is optional and can be deactivated from menu 19.
- Menu 15 (Integrity control) uses Dism with `/Cleanup-IMAGE` for` checkhealth and `scanhealth` options.
- Temporary Mount folders: all Mount's folders created during the session are traced and removed in a robust way; At the end, the program tries to clean up automatically. You can also force cleaning from the new menu 22, which shows a report with the total recovered space.

### state line and indicators

Under the list of items, the program shows a state line that summarizes the current settings:

- [Mountdirbase: ...] - Basic Folder of Temporary Mounts; `` `Temp%%` if not set (settable from menu 18)
- [verbose: on/off] - Enable/disable detailed logging on files (menu 19)
- [Exportback: Auto/Dism/Wimlib] - Active Backand for Export/Convert (Menu 19)
- [Wimlib ...]- State of Wimlib-IMAGEX: if available it shows the version (e.g. `1.14.x`); Alternatively indicates the source (`local or` path`); If absent, `` absent.

## settings and persistence

The settings of the app (Mount base folder, verbose log on/off, export backand: `` auto`/`` `wimlib`, percentage bar on a row and information spinner) are persistent among the sessions.

- Configuration file path:
  - Favorite: `` ARO APPDDATA%\ Dismenu \ Settings.json`
  - Fallback (if `` APPDDATA%`` it is not available): `Python/Postinstall/Conference/settings.json`
- Change the settings from the menu:
  - 18: Set of the basic folder for temporary Mounts (empty = use `` `` `` `` `` `` `` `)
  - 19: Set console options (VT, quickedit, centering, restoration position, alowystop), verbose log, export backand, percentage bar mode (applies to Wimlib and for the Dism operations that expose percentages) and the spinner for information controls (empty sending applies the defaults indicated below).
- Reset to the default settings: Delete the `settings.json` indicated above and reopen the script.

### Default Menu 19 (empty sending)

In Menu 19, if you simply press the prompt, these defaults are applied:

- VT (virtual terminal): off - prompt: "on/off, send = off".
- Quickedit (Anti-Pouse on Click): On- Prompt: "On/off, send = on".
- verbose log: on - prompt: "on/off, send = on"; A state hint is shown after setting.
- Cent Window to startup: On - prompt: "On/off, send = on".
- Restore position saved at startup: off - prompt: "on/off, send = off".
- Always in the foreground (Alwaystop): Off - prompt: "On/off, send = off"; The effect is immediate.
- Central attempts: End keeps the current value - prompt: "(send = keep)".
- delay between attempts (ms): send man
