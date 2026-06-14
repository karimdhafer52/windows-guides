# Windows System Maintenance Guide

A complete reference for keeping a Windows 10/11 system lean, fast, and secure. Covers
service tuning, startup cleanup, storage purging, and monthly maintenance routines.

> **Prerequisites:** Windows 10/11, PowerShell or Command Prompt running as Administrator.

---

## Table of Contents

1. [Pre-Optimization Safety Step](#1-pre-optimization-safety-step)
2. [Core Services Optimization](#2-core-services-optimization)
3. [Visual Performance Tweaks](#3-visual-performance-tweaks)
4. [Startup Apps Optimization](#4-startup-apps-optimization)
5. [Deep Storage Cleanup & Temp File Removal](#5-deep-storage-cleanup--temp-file-removal)
6. [Network Routing Refresh](#6-network-routing-refresh)
7. [Monthly Security Baseline](#7-monthly-security-baseline)

---

## 1. Pre-Optimization Safety Step

Before altering any service or system configuration, create a restore point so you can
roll back cleanly if something misbehaves.

1. Press the **Windows Key**, type **Create a restore point**, and select it.
2. Click **Create...**, name it `Before Optimization`, and click **Create**.

> Always create a restore point before changing service startup types or registry values.

---

## 2. Core Services Optimization

Windows runs several background services that are unnecessary on a personal laptop. Tuning
them frees up CPU cycles and RAM without affecting day-to-day use.

### How to Access the Services Manager

1. Press **Windows Key + R**, type `services.msc`, and press **Enter**.
2. Click the **Name** column header to sort alphabetically.
3. Double-click a service to open its properties, change **Startup type**, then click **Stop → Apply → OK**.

---

### Connected User Experiences and Telemetry

- **Recommended:** Disabled
- **Why:** Handles Microsoft's background telemetry and usage data collection — persistent
  network activity with no user benefit.
- **Note:** Disabling this does not affect Windows Update delivery.

---

### Windows Error Reporting Service

- **Recommended:** Manual
- **Why:** Stays at 0% idle. On _Manual_ it spins up briefly on a crash to log the dump
  file, then stops — the right balance between performance and debuggability.

---

### Xbox Services

Configure all four individually:

- `Xbox Accessory Management Service`
- `Xbox Live Auth Manager`
- `Xbox Live Game Save`
- `Xbox Live Networking Service`

- **Recommended:** Disabled (or Manual if you use Game Pass / Xbox controller via Bluetooth)
- **Why:** Xbox infrastructure services run in the background even after uninstalling the
  Xbox app. If you only use Steam or Epic Games with a non-Xbox controller, these are safe
  to disable entirely.

---

### BitLocker Drive Encryption Service

- **Recommended:** Manual
- **Why:** Dead weight if the drive is not encrypted. Setting to _Manual_ (not _Disabled_)
  ensures Windows Update can query it when needed without triggering update errors.

---

### Print Spooler

- **Recommended:** Disabled
- **Why:** Only needed if the laptop connects to a physical printer. If not, it runs for
  nothing.
- **Caution:** Disabling this breaks physical printing and some "Print to PDF" features in
  Office apps. If PDF export stops working after disabling, switch back to _Manual_.

---

### Windows Biometric Service

- **Recommended:** Disabled
- **Why:** Manages fingerprint readers and infrared face-unlock cameras. If the laptop
  lacks this hardware, the service wastes cycles searching for devices that don't exist.
- **Note:** This does **not** affect Windows Hello PIN login. PIN authentication is handled
  by TPM 2.0 / NGC and remains fully functional.

---

### SysMain (formerly Superfetch)

- **Recommended:** Manual (then Disabled after one stable week)
- **Why:** SysMain preloads frequently used apps into RAM — useful for mechanical hard
  drives (HDDs), but redundant on SSDs, which are already fast enough to negate the
  benefit. On an SSD it causes unnecessary disk writes ("thrashing") and aggressively fills
  RAM with stale cache.
- **Approach:** Set to _Manual_ first. If the system stays stable for a week, return here
  and switch to _Disabled_.

---

### Summary Table

| Service                                  | Recommended           | Notes                                           |
| :--------------------------------------- | :-------------------- | :---------------------------------------------- |
| Connected User Experiences and Telemetry | **Disabled**          | Telemetry — no user benefit                     |
| Windows Error Reporting Service          | **Manual**            | Activates only on crash                         |
| Xbox Accessory Management Service        | **Disabled**          | Keep **Manual** for Game Pass / Xbox controller |
| Xbox Live Auth Manager                   | **Disabled**          | ↑ same                                          |
| Xbox Live Game Save                      | **Disabled**          | ↑ same                                          |
| Xbox Live Networking Service             | **Disabled**          | ↑ same                                          |
| BitLocker Drive Encryption Service       | **Manual**            | Never _Disabled_ — breaks Windows Update checks |
| Print Spooler                            | **Disabled**          | Keep **Manual** if you print or export PDFs     |
| Windows Biometric Service                | **Disabled**          | Only if laptop has no fingerprint/face hardware |
| SysMain                                  | **Manual → Disabled** | Disable after one stable week on SSD            |

---

## 3. Visual Performance Tweaks

Windows applies UI animations that burden integrated GPUs and slow down laptops under load.
Switching to the performance-optimized visual mode routes maximum throughput to apps and games.

1. Press **Windows Key + R**, type `sysdm.cpl`, and click **OK**.
2. Select the **Advanced** tab.
3. Under _Performance_, click **Settings...**.
4. Select **Adjust for best performance**.
5. Click **Apply → OK**.

> This removes window shadows, animations, and transparency effects. The UI becomes
> minimal but all functionality remains intact. Reverse this at any time using the same panel.

---

## 4. Startup Apps Optimization

Many apps silently embed themselves into the boot sequence, increasing boot time and idle
RAM consumption.

1. Press **Ctrl + Shift + Esc** to open **Task Manager**.
2. Go to the **Startup apps** tab.
3. Review the list. Select non-essential entries (chat apps, game launchers, cloud sync
   utilities) and click **Disable**.

**Do not disable:**

- Audio/sound card controllers
- Touchpad gesture drivers
- GPU control panels (Intel, AMD, Nvidia)

These are hardware-dependent and disabling them can break system functionality.

---

## 5. Deep Storage Cleanup & Temp File Removal

Windows continuously writes prefetch data, temporary install files, and app crash logs.
Uninstalled apps often leave their cache folders behind permanently.

### Section A — Disk Cleanup Utility

1. Press **Windows Key + R**, type `cleanmgr`, and click **OK**.
2. Select the **C:** drive and click **OK**.
3. Check all boxes (Temporary Files, Delivery Optimization Files, Thumbnails, etc.).
4. Click **OK** and confirm.

For a deeper clean, click **Clean up system files** inside the same utility — this exposes
old Windows Update packages and the `Windows.old` folder from previous installs.

### Section B — Prefetch Data Cache

1. Press **Windows Key + R**, type `prefetch`, and press **Enter**.
2. Accept the UAC prompt.
3. Press **Ctrl + A** to select all, then **Shift + Delete** to permanently delete.
4. If a "file in use" warning appears, choose **Do this for all current items → Skip**.

### Section C — User Temp Cache

1. Press **Windows Key + R**, type `%temp%`, and press **Enter**.
2. Press **Ctrl + A**, then **Shift + Delete**. Skip locked files if prompted.

### Section D — System Temp Cache

1. Press **Windows Key + R**, type `%windir%\temp`, and press **Enter**.
2. Accept the UAC prompt.
3. Press **Ctrl + A**, then **Shift + Delete**. Skip locked files if prompted.

> **Storage warning:** Keep at least 25–30 GB free on the C: drive at all times. When the
> disk bar turns red in File Explorer, Windows cannot write virtual memory pages and
> performance degrades severely.

---

## 6. Network Routing Refresh

Stale DNS cache entries cause browser connection errors and lag spikes over time. This
clears the local DNS resolver without touching network adapter settings.

1. Open an **Elevated Command Prompt** (search `cmd` → right-click → Run as administrator).
2. Run:

```cmd
ipconfig /flushdns
```

3. Close the prompt when the success message appears.

For persistent network issues, see `windows-network-guide.md` for the full network stack
reset sequence.

---

## 7. Monthly Security Baseline

These two tasks take 10 minutes and prevent the majority of performance degradation from
accumulating over time.

### Windows Update

1. Open **Settings → Windows Update**.
2. Click **Check for updates** and install all pending patches.

> Windows often runs sluggishly when update installations are stalled or looping in the
> background. Keeping updates current prevents this.

### Full Malware Scan

1. Open **Windows Security** (shield icon in the system tray).
2. Go to **Virus & threat protection → Scan options**.
3. Select **Full scan** and click **Scan now**.

> A Full Scan reviews every file on the drive. It takes over an hour — run it when the
> laptop is idle or at the end of the day. Run it once a month.

---

**Restart the laptop after completing these steps to apply all changes cleanly.**
