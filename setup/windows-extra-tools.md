# Windows Extra Tools Setup Guide

Supplementary CLI utilities and media tools for a Windows developer environment.
Companion to [windows-dev-setup.md](https://gist.github.com/karimdhafer52) — install that first.

> **Prerequisites:** PowerShell running as Administrator, winget available, core dev environment already set up.

---

## Table of Contents

1. [yt-dlp](#1-yt-dlp)
2. [FFmpeg (Gyan)](#2-ffmpeg-gyan)
3. [7-Zip](#3-7-zip)
4. [VLC Media Player](#4-vlc-media-player)
5. [ExifTool](#5-exiftool)

---

## 1. yt-dlp

**What it is:** A feature-rich command-line tool for downloading video and audio from YouTube and hundreds of other sites. Maintained fork of the now-inactive youtube-dl.

```powershell
winget install --id yt-dlp.yt-dlp -e --scope machine
```

**Expected output:**

```
Found yt-dlp [yt-dlp.yt-dlp] Version 2026.03.17
This package requires the following dependencies:
  - Packages
      DenoLand.Deno
      yt-dlp.FFmpeg

(1/2) Found Deno [DenoLand.Deno] Version 2.8.2
Downloading https://github.com/denoland/deno/releases/download/...
  ██████████████████████████████  39.6 MB / 39.6 MB
Successfully verified installer hash
Starting package install...
Command line alias added: "deno"
Path environment variable modified; restart your shell to use the new value.
Successfully installed

(2/2) Found FFmpeg for yt-dlp [yt-dlp.FFmpeg] Version N-124716-g054dffd133-20260531
Downloading https://github.com/yt-dlp/FFmpeg-Builds/releases/...
  ██████████████████████████████   211 MB /  211 MB
Successfully verified installer hash
Starting package install...
Command line alias added: "ffmpeg"
Command line alias added: "ffplay"
Command line alias added: "ffprobe"
Successfully installed

Downloading https://github.com/yt-dlp/yt-dlp/releases/...
  ██████████████████████████████  17.5 MB / 17.5 MB
Successfully verified installer hash
Starting package install...
Command line alias added: "yt-dlp"
Successfully installed
```

> yt-dlp pulls in two automatic dependencies: **Deno** (its runtime) and **yt-dlp.FFmpeg** (a yt-dlp-specific FFmpeg build used internally for merging audio and video streams). These are separate from the general-purpose Gyan FFmpeg below — no conflict.

**Basic usage:**

```powershell
yt-dlp <URL>                          # download best quality
yt-dlp -f bestaudio <URL>             # audio only
yt-dlp -o "%(title)s.%(ext)s" <URL>   # custom output filename
```

---

## 2. FFmpeg (Gyan)

**What it is:** The industry-standard multimedia processing toolkit — convert, trim, encode, stream, or inspect any audio/video format. The Gyan build is the most complete Windows distribution (includes all codecs).

> **Note:** yt-dlp installs its own FFmpeg build (`yt-dlp.FFmpeg`) for internal use. This is the **general-purpose** FFmpeg for your own use on the command line — both can coexist without conflict.

```powershell
winget install --id Gyan.FFmpeg -e --scope machine
```

**Expected output:**

```
Found FFmpeg [Gyan.FFmpeg] Version 8.1.1
Downloading https://github.com/GyanD/codexffmpeg/releases/...
  ██████████████████████████████   240 MB /  240 MB
Successfully verified installer hash
Starting package install...
Command line alias added: "ffmpeg"
Command line alias added: "ffplay"
Command line alias added: "ffprobe"
Successfully installed
```

**Basic usage:**

```powershell
ffmpeg -i input.mp4 output.mkv                            # convert format
ffmpeg -i input.mp4 -ss 00:01:00 -t 30 clip.mp4          # trim 30s starting at 1:00
ffmpeg -i input.mp4 -vn audio.mp3                         # extract audio
ffprobe input.mp4                                         # inspect file metadata
```

---

## 3. 7-Zip

**What it is:** Open-source file archiver with one of the highest compression ratios available. Handles `.7z`, `.zip`, `.tar`, `.gz`, `.rar`, and many more formats. Also adds shell integration (right-click menu).

```powershell
winget install --id 7zip.7zip -e --scope machine
```

**Expected output:**

```
Found 7-Zip [7zip.7zip] Version 26.01
Downloading https://7-zip.org/a/7z2601-x64.msi
  ██████████████████████████████  1.90 MB / 1.90 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

**Basic CLI usage:**

```powershell
7z a archive.7z .\folder\       # compress folder
7z x archive.7z -o.\output\     # extract to folder
7z l archive.7z                 # list contents
```

> 7-Zip also integrates into the Windows right-click context menu for GUI use.

---

## 4. VLC Media Player

**What it is:** The universal open-source media player. Plays virtually any format without needing additional codecs. Also useful as a lightweight streaming client.

```powershell
winget install --id VideoLAN.VLC -e --scope machine
```

**Expected output:**

```
Found VLC media player [VideoLAN.VLC] Version 3.0.23
Downloading https://download.videolan.org/pub/videolan/vlc/3.0.23/win64/vlc-3.0.23-win64.exe
  ██████████████████████████████  43.8 MB / 43.8 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

> This is a full GUI installation — identical to downloading from videolan.org. You get the complete VLC with file associations, right-click "Open with VLC", and full playlist support.

**Basic CLI usage:**

```powershell
vlc video.mkv                           # open file
vlc --intf dummy --play-and-exit video.mkv   # headless playback
```

---

## 5. ExifTool

**What it is:** The standard command-line tool for reading, writing, and editing metadata embedded in image, video, audio, and document files. Supports hundreds of formats including JPEG, PNG, MP4, PDF, and RAW camera formats.

```powershell
winget install --id OliverBetz.ExifTool -e --scope machine
```

**Expected output:**

```
Found ExifTool [OliverBetz.ExifTool] Version 13.59
Downloading https://oliverbetz.de/cms/files/Artikel/ExifTool-for-Windows/ExifTool_install_13.59_64.exe
  ██████████████████████████████  10.6 MB / 10.6 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

**Basic usage:**

```powershell
exiftool image.jpg                                          # read all metadata
exiftool -Author="Your Name" image.jpg                     # write a metadata field
exiftool -ext jpg C:\Photos\                               # read metadata from all JPGs in a folder
exiftool "-DateTimeOriginal>FileModifyDate" image.jpg      # sync file date to EXIF date
exiftool -csv C:\Photos\ > metadata.csv                    # export metadata to CSV
```

---

## Summary

| Tool          | winget ID             | PATH scope | Aliases registered                              |
| ------------- | --------------------- | ---------- | ----------------------------------------------- |
| yt-dlp        | `yt-dlp.yt-dlp`       | System     | `yt-dlp`, `deno`, `ffmpeg`, `ffplay`, `ffprobe` |
| FFmpeg (Gyan) | `Gyan.FFmpeg`         | System     | `ffmpeg`, `ffplay`, `ffprobe`                   |
| 7-Zip         | `7zip.7zip`           | System     | —                                               |
| VLC           | `VideoLAN.VLC`        | System     | —                                               |
| ExifTool      | `OliverBetz.ExifTool` | System     | —                                               |

> **Always open a new PowerShell window after each install** to ensure updated PATH entries are loaded.
