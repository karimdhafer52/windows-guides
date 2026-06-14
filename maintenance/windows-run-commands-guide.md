# Windows Run Commands Reference Guide

The Windows Run dialog (`Win + R`) is the fastest keyboard-driven way to open core
administrative, diagnostic, and configuration utilities — bypassing nested Settings menus
entirely.

---

## Table of Contents

1. [How to Use the Run Dialog](#1-how-to-use-the-run-dialog)
2. [Core System Configuration & Logs](#2-core-system-configuration--logs)
3. [Hardware & Disk Diagnostics](#3-hardware--disk-diagnostics)
4. [Network & Security Shortcuts](#4-network--security-shortcuts)
5. [Control Panel Applets (.cpl)](#5-control-panel-applets-cpl)
6. [Advanced Administrative Utilities](#6-advanced-administrative-utilities)
7. [Hidden System Directory Shortcuts](#7-hidden-system-directory-shortcuts)

---

## 1. How to Use the Run Dialog

- **Open:** Press **Windows Key + R** simultaneously (or right-click the Start button → **Run**).
- **Execute:** Type the command and press **Enter** — no need to click OK.
- **Elevated launch:** Type the command, then press **Ctrl + Shift + Enter** to open it as
  Administrator (works for `cmd`, `powershell`, and `.msc` tools).

---

## 2. Core System Configuration & Logs

### System Configuration — `msconfig`

Troubleshoot boot issues, configure **Safe Boot** for diagnostic startups, or switch
between Normal and minimal Diagnostic startup states. The **Tools** tab contains a
curated directory of additional Windows diagnostics.

### Event Viewer — `eventvwr`

The definitive local OS log. When an app silently crashes, a driver fails, or a blue
screen occurs, navigate to **Windows Logs → Application** or **System** to find the exact
error code and description.

### Services Manager — `services.msc`

View, start, stop, or change the startup type of Windows background services and
third-party updaters. See `windows-maintenance-guide.md` for a full tuning reference.

### Registry Editor — `regedit`

Directly read and modify the Windows configuration database for advanced tweaks.

> **Warning:** Misconfiguring registry keys can break Windows. Always create a System
> Restore Point (`sysdm.cpl`) before making any changes here.

---

## 3. Hardware & Disk Diagnostics

### System Information — `msinfo32`

Read-only report of exact hardware specs: processor model, total/available RAM, BIOS
firmware version, and peripheral device assignments.

### Device Manager — `devmgmt.msc`

Update, roll back, or disable individual device drivers. Yellow warning triangles indicate
a component missing a functional driver — check here immediately after a fresh Windows
install.

### Disk Management — `diskmgmt.msc`

Format drives, resize partitions, change volume labels, or assign drive letters. Also used
to prepare space for a dual-boot OS installation.

### Disk Cleanup — `cleanmgr`

Launches the storage scanning utility. Click **Clean up system files** inside it to expose
deeper caches like old Windows Update packages and the `Windows.old` folder.

### DirectX Diagnostics — `dxdiag`

Displays GPU details, display refresh rate, and audio hardware configuration. Check the
**Notes** section on each tab for hardware-detected errors.

### Memory Diagnostic — `mdsched`

Schedules a hardware-level RAM scan on the next reboot. Use this if the system produces
random BSODs with memory-related error codes.

---

## 4. Network & Security Shortcuts

### Network Adapters — `ncpa.cpl`

Opens the network adapters panel directly. Right-click an adapter to change DNS servers,
assign a static IP, or reset the adapter.

### Windows Firewall — `firewall.cpl`

Check firewall profile states, manage per-app traffic permissions, or create custom
inbound/outbound rules.

### Command Prompt — `cmd`

Opens a standard command shell.

> **Pro tip:** Press **Ctrl + Shift + Enter** instead of **Enter** after typing `cmd` to
> open an Elevated Admin Command Prompt immediately — skipping the right-click step.

---

## 5. Control Panel Applets (.cpl)

The traditional Control Panel handles deep hardware configuration better than the modern
Settings app for many scenarios.

| Command        | Destination                | Best Used For                                               |
| :------------- | :------------------------- | :---------------------------------------------------------- |
| `control`      | Control Panel root         | Quick access to items not yet in Settings                   |
| `appwiz.cpl`   | Programs and Features      | Uninstall or **Repair** older installed software            |
| `sysdm.cpl`    | Advanced System Properties | Virtual memory, visual animations, restore points           |
| `powercfg.cpl` | Power Options              | Customize power profiles, sleep timers, battery save limits |
| `ncpa.cpl`     | Network Adapters           | DNS settings, static IP, adapter reset                      |
| `firewall.cpl` | Windows Firewall           | Firewall profile and rule management                        |
| `main.cpl`     | Mouse Properties           | Pointer speed, click latency, scroll line count             |

---

## 6. Advanced Administrative Utilities

### Local Group Policy Editor — `gpedit.msc`

Configure deep OS policies, security permissions, and environment rules.

> Available on **Windows Pro and Enterprise only** — not present on Windows Home.

### Task Scheduler — `taskschd.msc`

Manages all automated OS tasks, update sequences, and background data collections. If your
laptop slows down at predictable times of day, check here for scheduled tasks causing the
load.

### Optional Features — `optionalfeatures`

Toggle enterprise features on or off: Hyper-V virtualization, Windows Subsystem for Linux
(WSL), .NET frameworks, and other optional Windows components.

### Windows Version Info — `winver`

Displays a compact dialog showing your exact Windows version, build number, and edition
(Home, Pro, Enterprise). Useful for confirming update status.

---

## 7. Hidden System Directory Shortcuts

Windows maps shell shortcuts to deeply nested system folders — useful for reaching hidden
directories without enabling "Show hidden files" in Explorer.

### User AppData — `shell:appdata`

Opens `AppData\Roaming` — where most third-party apps store user profiles, save states,
and configuration files.

### Startup Folder — `shell:startup`

Opens the user startup directory. Any shortcut or executable placed here launches
automatically when Windows completes sign-in.

### System Startup Folder — `shell:common startup`

Same as above but for **all users** on the machine (requires elevation to modify).

---

**Mastering a handful of these shortcuts dramatically reduces time spent navigating nested menus.**
