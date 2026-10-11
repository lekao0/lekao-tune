<p align="center">
  <img src="logo.png" width="110" alt="Lekao Booster logo">
</p>

<h1 align="center">Lekao System Booster</h1>

<p align="center">
  A safe, reversible Windows performance optimizer with a built-in hardware monitor and in-game FPS overlay.
  <br>
  <b>Python · CustomTkinter · Win32 API (ctypes) · C# / .NET · PowerShell</b>
</p>

---

## Overview

Lekao System Booster is a desktop app I built to tune Windows PCs for gaming and everyday use, based on 8+ years of hands-on PC repair and maintenance work.

Most "PC booster" tools change dozens of system settings with no way back. The core idea here is **safety first**: before any registry value or service is changed, its **original value is saved**, so a single click on *Revert* returns the PC to exactly how it was.

## Features

### Optimization (grouped by risk level)
- **System:** high-performance power plan, SSD TRIM check, drive optimization, hibernation off, foreground-app priority, multimedia scheduling, memory compression tuned to installed RAM
- **Visual:** lighter animations, menus without delay, no transparency, mouse acceleration off
- **Network:** Nagle's algorithm off for lower latency, DNS cache flush
- **Background noise:** telemetry, Game DVR, Cortana, Widgets, search indexing and notifications can be disabled individually

### Memory & cleanup
- Frees standby RAM and trims background processes using Windows privileges (`SeProfileSingleProcessPrivilege`) through `ctypes`
- Cleans temp folders (only items older than 24h), old Windows Update downloads, browser caches and component store (DISM)
- Native **Disk Cleanup** handlers called via COM, run in a separate worker process with live progress

### Startup manager
- Lists startup programs from the registry and Task Manager's approval keys, with on/off toggles

### Hardware monitor & game overlay
- Real-time CPU/GPU usage, clocks, temperatures, VRAM and RAM
- Temperatures come from a small **C# helper** I wrote on top of LibreHardwareMonitorLib, which streams sensor data to the Python app over stdin/stdout and pauses itself when not needed to save CPU
- GPU load read directly from Windows kernel graphics counters (D3DKMT) and PDH performance counters
- **FPS counter** using Intel's open-source PresentMon
- **Transparent click-through overlay** (RivaTuner style) with global hotkeys. It draws a window on top of the game and injects nothing into the game process, so it carries no anti-cheat risk.

### Safety & recovery
- Automatic **System Restore point** and registry export before changes
- Original-state file (`estado_original.json`) with every value the app touched
- *Revert* tab restores everything, or only selected items
- Detects and fixes settings from the previous version that caused instability

## How it works

```
┌───────────────────────────┐   stdin/stdout   ┌──────────────────────┐
│  Python app (UI + logic)  │ ◄──────────────► │ LekaoSensors.exe (C#)│
│  CustomTkinter, psutil,   │                  │ LibreHardwareMonitor │
│  winreg, ctypes, COM      │                  └──────────────────────┘
│                           │   subprocess     ┌──────────────────────┐
│  Steps: Reg / Service /   │ ───────────────► │ PowerShell, DISM,    │
│  Cmd / Func               │                  │ defrag, PresentMon   │
└────────────┬──────────────┘                  └──────────────────────┘
             │ saves before changing
             ▼
   LekaoBackups/estado_original.json  →  one-click Revert
```

- Every tweak is a typed **Step** (`Reg`, `Service`, `Cmd`, `Func`), so the same object knows how to apply itself, report status and be undone
- Long-running work runs in background threads with a queue feeding the UI, so the window never freezes
- Child processes are killed as a whole tree on timeout to avoid hangs
- Packaged as a single `.exe` with PyInstaller, and requests admin rights automatically (UAC)

## Screenshots

| Home: live hardware overview | Optimization |
|---|---|
| ![Home](screenshot-home.jpg) | ![Optimization](screenshot-optimization.jpg) |

| Memory | In-game overlay |
|---|---|
| ![Memory](screenshot-memory.jpg) | ![Overlay](screenshot-overlay.jpg) |

## Tech stack

| Area | Tools |
|---|---|
| Language | Python 3, C# (.NET Framework) |
| UI | CustomTkinter, Tkinter, Pillow |
| System access | `winreg`, `ctypes` (Win32, NT and D3DKMT APIs), COM, PowerShell |
| Monitoring | psutil, PDH counters, LibreHardwareMonitorLib, PresentMon |
| Packaging | PyInstaller |

## Source code

The source code is kept private. **Available upon request** for recruiters and technical reviewers.

## Author

**Alexandro Rodrigues** · IT professional & Python developer · Rio de Janeiro, Brazil
[LinkedIn](https://www.linkedin.com/in/alexandro-rodrigues-07802292/)
