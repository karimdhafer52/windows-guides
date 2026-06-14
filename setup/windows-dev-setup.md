# Windows Developer Environment Setup Guide

A step-by-step guide to bootstrapping a complete developer toolchain on a fresh Windows installation. Covers Git, VS Code, WSL 2, Docker, Python, Node.js, Java (Temurin JDK), and TypeScript.

> **Prerequisites:** Windows 10/11, PowerShell running as Administrator, and an active internet connection.

---

## Table of Contents

1. [Git](#1-git)
2. [GitHub CLI](#2-github-cli)
3. [Visual Studio Code](#3-visual-studio-code)
4. [Global Git Configuration](#4-global-git-configuration)
5. [WSL 2 (Windows Subsystem for Linux)](#5-wsl-2)
6. [Docker Desktop](#6-docker-desktop)
7. [Python 3.14](#7-python-314)
8. [Node.js LTS](#8-nodejs-lts)
9. [Eclipse Temurin JDK 21](#9-eclipse-temurin-jdk-21)
10. [TypeScript & ts-node](#10-typescript--ts-node)
11. [Verify PATH Configuration](#11-verify-path-configuration)

---

## 1. Git

**What it is:** The standard distributed version control system. Required before anything else.

```powershell
winget install --id Git.Git -e --source winget
```

**Expected output:**

```
Found Git [Git.Git] Version 2.54.0
Downloading https://github.com/git-for-windows/git/releases/download/...
  ██████████████████████████████  62.1 MB / 62.1 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

> After installation, **open a new PowerShell window** to pick up the updated PATH.

---

## 2. GitHub CLI

**What it is:** Official command-line interface for GitHub. Lets you manage repos, PRs, issues, and Gists without leaving the terminal. Required before pushing anything to GitHub.

```powershell
winget install --id GitHub.cli -e
```

**Expected output:**

```
Found GitHub CLI [GitHub.cli] Version 2.x.x
Downloading https://github.com/cli/cli/releases/download/...
  ██████████████████████████████  xx.x MB / xx.x MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

Then authenticate with your GitHub account:

```powershell
gh auth login
```

The CLI walks you through an interactive prompt. Use the arrow keys to select each option:

```
? Where do you use GitHub? GitHub.com
? What is your preferred protocol for Git operations on this host? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Login with a web browser

! First copy your one-time code: XXXX-XXXX
Press Enter to open https://github.com/login/device in your browser...
✓ Authentication complete.
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as <your-github-username>
```

**Recommended answers:**

| Prompt                                         | Choice                     | Why                                             |
| ---------------------------------------------- | -------------------------- | ----------------------------------------------- |
| Where do you use GitHub?                       | `GitHub.com`               | Unless you're on GitHub Enterprise              |
| Protocol for Git operations                    | `HTTPS`                    | Works everywhere, no SSH key setup needed       |
| Authenticate Git with your GitHub credentials? | `Yes`                      | Lets `gh` also handle `git push/pull` auth      |
| How to authenticate                            | `Login with a web browser` | Most straightforward; generates a one-time code |

The browser flow gives you a one-time code (e.g. `A90D-074A`) — copy it, press Enter, paste it on the GitHub device page, and authorize. Authentication completes in the terminal automatically.

> Open a new PowerShell window after install before running `gh auth login`.

---

## 3. Visual Studio Code

**What it is:** The primary code editor. Installed machine-wide with context menu integration and PATH registration, without auto-launching after install.

```powershell
winget install --id Microsoft.VisualStudioCode -e --scope machine `
  --override '/verysilent /suppressmsgboxes /mergetasks="!runcode,addcontextmenufiles,addcontextmenufolders,associatewithfiles,addtopath"'
```

**Override flags explained:**

| Flag                    | Effect                                              |
| ----------------------- | --------------------------------------------------- |
| `/verysilent`           | Fully silent install, no UI                         |
| `/suppressmsgboxes`     | Suppresses any popup dialogs                        |
| `!runcode`              | Does **not** launch VS Code after install           |
| `addcontextmenufiles`   | Adds "Open with Code" to file right-click menu      |
| `addcontextmenufolders` | Adds "Open with Code" to folder right-click menu    |
| `associatewithfiles`    | Associates common text/code file types with VS Code |
| `addtopath`             | Registers the `code` command in PATH                |

**Expected output:**

```
Found Microsoft Visual Studio Code [Microsoft.VisualStudioCode] Version 1.123.0
Downloading ...
  ██████████████████████████████   174 MB /  174 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

> The winget msstore source will ask you to accept Terms of Transaction before downloading. Enter `y`.

---

## 4. Global Git Configuration

**Open a fresh PowerShell session** after installing Git and VS Code, then run:

```powershell
# Set default branch name for new repos
git config --global init.defaultBranch main

# Use VS Code as the commit message editor
git config --global core.editor "code --wait"

# Your identity (used in commit metadata)
git config --global user.name "Your Name"
git config --global user.email "your-handle@users.noreply.github.com"

# Use Windows Credential Manager for storing tokens
git config --global credential.helper manager
```

**Notes:**

- Using a `@users.noreply.github.com` address keeps your real email private on public commits. Enable it in GitHub under **Settings → Emails → Keep my email address private**.
- `credential.helper manager` = Git Credential Manager (ships with Git for Windows). Handles GitHub OAuth/PAT flows automatically.

---

## 5. WSL 2

**What it is:** Runs a real Linux kernel inside Windows. Required by Docker Desktop and useful for Linux-native tooling.

```powershell
wsl --install
```

**Expected output:**

```
Downloading: Windows Subsystem for Linux 2.7.3
Installing: Windows Subsystem for Linux 2.7.3
Windows Subsystem for Linux 2.7.3 has been installed.
Installing Windows optional component: VirtualMachinePlatform
...
The operation completed successfully.
The requested operation is successful. Changes will not be effective until the system is rebooted.
```

> **Reboot your machine** before continuing. WSL 2 requires enabling the `VirtualMachinePlatform` Windows feature, which takes effect only after restart.

After rebooting, verify WSL is up to date:

```powershell
wsl --update
# Expected: The most recent version of Windows Subsystem for Linux is already installed.
```

---

## 6. Docker Desktop

**What it is:** Containerization platform. Requires WSL 2 to be installed and the machine rebooted first.

```powershell
winget install --id Docker.DockerDesktop -e --source winget
```

**Expected output:**

```
Found Docker Desktop [Docker.DockerDesktop] Version 4.76.0
Downloading https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe
  ██████████████████████████████   639 MB /  639 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

> After install, launch Docker Desktop once from the Start menu and complete the onboarding. It will start its WSL 2 backend integration automatically.

---

## 7. Python 3.14

**What it is:** General-purpose scripting and tooling language.

```powershell
winget install --id Python.Python.3.14 -e
```

**Expected output:**

```
Found Python 3.14 [Python.Python.3.14] Version 3.14.5
Downloading https://www.python.org/ftp/python/3.14.5/python-3.14.5-amd64.exe
  ██████████████████████████████  28.9 MB / 28.9 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

The winget installer adds Python and its `Scripts\` folder to the **user PATH** automatically.

---

## 8. Node.js LTS

**What it is:** JavaScript runtime. Install the LTS (Long-Term Support) release for stability.

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

**Expected output:**

```
Found Node.js (LTS) [OpenJS.NodeJS.LTS] Version 24.16.0
Downloading https://nodejs.org/dist/v24.16.0/node-v24.16.0-x64.msi
  ██████████████████████████████  31.3 MB / 31.3 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

> Node.js is added to the **system PATH** (`C:\Program Files\nodejs\`), and npm global packages land in the user PATH under `AppData\Roaming\npm`.

---

## 9. Eclipse Temurin JDK 21

**What it is:** Production-grade OpenJDK distribution from the Eclipse Adoptium project. Version 21 is the current LTS.

> **Note:** The package ID `EclipseFoundation.Temurin.21.JDK` does **not** exist in winget. Use `EclipseAdoptium.Temurin.21.JDK`.

```powershell
# This will fail — wrong publisher prefix:
# winget install --id EclipseFoundation.Temurin.21.JDK -e
# No package found matching input criteria.

# Correct command:
winget install --id EclipseAdoptium.Temurin.21.JDK -e
```

**Expected output:**

```
Found Eclipse Temurin JDK with Hotspot 21 [EclipseAdoptium.Temurin.21.JDK] Version 21.0.11.10
Downloading .../OpenJDK21U-jdk_x64_windows_hotspot_21.0.11_10.msi
  ██████████████████████████████   171 MB /  171 MB
Successfully verified installer hash
Starting package install...
Successfully installed
```

The JDK `bin\` directory is added to the **system PATH**.

---

## 10. TypeScript & ts-node

**What it is:** TypeScript compiler (`tsc`) and `ts-node` for running `.ts` files directly without a build step.

First, allow PowerShell to run scripts (required to use globally installed npm binaries):

```powershell
Set-ExecutionPolicy RemoteSigned -Scope LocalMachine
# Answer [A] Yes to All at the prompt
```

Then install globally via npm:

```powershell
npm install -g typescript ts-node
```

**Expected output:**

```
added 20 packages in 4s
```

**Verify:**

```powershell
tsc --version    # e.g. Version 5.x.x
ts-node --version
```

---

## 11. Verify PATH Configuration

After all installs, open a **fresh PowerShell window** and run:

```powershell
Write-Host "`n=== SYSTEM PATHS ===" -ForegroundColor Cyan
[Environment]::GetEnvironmentVariable("Path", "Machine") -split ";" | Where-Object { $_ }

Write-Host "`n=== USER PATHS ===" -ForegroundColor Green
[Environment]::GetEnvironmentVariable("Path", "User") -split ";" | Where-Object { $_ }
```

**Expected system PATH entries:**

```
C:\Program Files\Eclipse Adoptium\jdk-21.0.11.10-hotspot\bin
C:\WINDOWS\system32
C:\WINDOWS
C:\WINDOWS\System32\Wbem
C:\WINDOWS\System32\WindowsPowerShell\v1.0\
C:\WINDOWS\System32\OpenSSH\
C:\Program Files\Git\cmd
C:\Program Files\Microsoft VS Code\bin
C:\Program Files\Docker\Docker\resources\bin
C:\Program Files\nodejs\
```

**Expected user PATH entries:**

```
C:\Users\<username>\AppData\Local\Programs\Python\Python314\Scripts\
C:\Users\<username>\AppData\Local\Programs\Python\Python314\
C:\Users\<username>\AppData\Local\Programs\Python\Launcher\
C:\Users\<username>\AppData\Local\Microsoft\WindowsApps
C:\Users\<username>\AppData\Roaming\npm
```

---

## Summary

| Tool                 | winget ID                        | PATH scope |
| -------------------- | -------------------------------- | ---------- |
| Git 2.54             | `Git.Git`                        | System     |
| GitHub CLI           | `GitHub.cli`                     | System     |
| VS Code 1.123        | `Microsoft.VisualStudioCode`     | System     |
| WSL 2                | built-in (`wsl --install`)       | —          |
| Docker Desktop 4.76  | `Docker.DockerDesktop`           | System     |
| Python 3.14          | `Python.Python.3.14`             | User       |
| Node.js LTS 24       | `OpenJS.NodeJS.LTS`              | System     |
| Temurin JDK 21       | `EclipseAdoptium.Temurin.21.JDK` | System     |
| TypeScript + ts-node | `npm install -g`                 | User (npm) |

> **Always open a new PowerShell window after each install** to ensure updated PATH entries are loaded.
