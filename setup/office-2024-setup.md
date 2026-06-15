# Microsoft Office 2024 Setup Guide

Install Office 2024 for free and legally using official Microsoft tools — no cracks,
no unauthorized activators.

> **Prerequisites:** Windows 10/11, an active internet connection, and Command Prompt
> running as Administrator.

---

## Table of Contents

1. [How It Works](#1-how-it-works)
2. [Create the Installation Folder](#2-create-the-installation-folder)
3. [Download the Office Deployment Tool](#3-download-the-office-deployment-tool)
4. [Generate a Configuration File](#4-generate-a-configuration-file)
5. [Run the Installation](#5-run-the-installation)
6. [Verify the Installation](#6-verify-the-installation)

---

## 1. How It Works

Microsoft provides two official tools that together handle everything:

| Tool                                                                                           | Purpose                                                                               |
| :--------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------ |
| [Office Deployment Tool (ODT)](https://www.microsoft.com/en-us/download/details.aspx?id=49117) | The installer engine — a `setup.exe` that downloads and installs Office               |
| [Office Customization Tool (OCT)](https://config.office.com/deploymentsettings)                | A web UI that generates a `Configuration.xml` telling the ODT exactly what to install |

The XML file controls which apps get installed, which get excluded, which languages to
include, and which channel (volume license, subscription, etc.) to use.

---

## 2. Create the Installation Folder

Keep all installation files in one dedicated location.

Recommended path: `C:\tools\ODT`

```cmd
mkdir C:\tools\ODT
```

Or create it manually in File Explorer. All files downloaded in the next steps go here.

---

## 3. Download the Office Deployment Tool

1. Go to the [Office Deployment Tool download page](https://www.microsoft.com/en-us/download/details.aspx?id=49117) on Microsoft's site.
2. Click **Download** and save the `.exe` to your downloads folder.
3. Run the downloaded installer and point it at `C:\tools\ODT` as the extraction destination.

After extraction, the folder should contain `setup.exe`:

```
C:\tools\ODT\
└── setup.exe
```

---

## 4. Generate a Configuration File

1. Open the [Office Customization Tool](https://config.office.com/deploymentsettings) in your browser.
2. Configure your installation:
   - **Architecture:** 64-bit (matches most modern systems; choose 32-bit only if required)
   - **Product:** Office 2024 (or Office 365 if you have a subscription)
   - **Version:** Leave on latest unless you need a specific build
   - **Apps:** Toggle individual apps on or off — only install what you need
   - **Languages:** Add as many as needed (e.g. English, French, Arabic)

3. Click **Export** on the right side of the page. This generates a `Configuration.xml` file.
4. Copy the downloaded `Configuration.xml` into `C:\tools\ODT\`.

Your folder should now look like this:

```
C:\tools\ODT\
├── setup.exe
└── Configuration.xml
```

### Example Configuration

The XML below installs Word, Excel, and PowerPoint only, in English, French, and Arabic,
using the Office 2024 Volume License channel. All other apps (Outlook, Teams, OneNote,
etc.) are explicitly excluded.

> **Note:** The `ID` attribute and `PIDKEY` value are generated uniquely per export by the
> OCT — yours will differ from the ones shown here even if you configure the exact same
> options. This is expected and normal.

```xml
<Configuration ID="908e9d82-36c3-4529-9b6b-da332fdf726b">
  <Add OfficeClientEdition="64" Channel="PerpetualVL2024">
    <Product ID="ProPlus2024Volume" PIDKEY="XJ2XN-FW8RK-P4HMP-DKDBV-GCVGB">
      <Language ID="en-us" />
      <Language ID="fr-fr" />
      <Language ID="ar-sa" />
      <ExcludeApp ID="Access" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="OneNote" />
      <ExcludeApp ID="Outlook" />
      <ExcludeApp ID="Publisher" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <Property Name="SharedComputerLicensing" Value="0" />
  <Property Name="FORCEAPPSHUTDOWN" Value="FALSE" />
  <Property Name="DeviceBasedLicensing" Value="0" />
  <Property Name="SCLCacheOverride" Value="0" />
  <Property Name="AUTOACTIVATE" Value="1" />
  <Updates Enabled="TRUE" />
  <RemoveMSI />
  <AppSettings>
    <User Key="software\microsoft\office\16.0\excel\options" Name="defaultformat" Value="51" Type="REG_DWORD" App="excel16" Id="L_SaveExcelfilesas" />
    <User Key="software\microsoft\office\16.0\powerpoint\options" Name="defaultformat" Value="27" Type="REG_DWORD" App="ppt16" Id="L_SavePowerPointfilesas" />
    <User Key="software\microsoft\office\16.0\word\options" Name="defaultformat" Value="" Type="REG_SZ" App="word16" Id="L_SaveWordfilesas" />
  </AppSettings>
</Configuration>
```

**Key fields explained:**

| Field                  | Value                | Meaning                                                                   |
| :--------------------- | :------------------- | :------------------------------------------------------------------------ |
| `OfficeClientEdition`  | `64`                 | 64-bit installation                                                       |
| `Channel`              | `PerpetualVL2024`    | Office 2024 perpetual volume license channel                              |
| `Product ID`           | `ProPlus2024Volume`  | Office 2024 Professional Plus (volume)                                    |
| `PIDKEY`               | generated per export | Volume license key — unique to your OCT export                            |
| `AUTOACTIVATE`         | `1`                  | Activates automatically using the embedded key                            |
| `RemoveMSI`            | —                    | Removes any older MSI-based Office installs before proceeding             |
| `defaultformat` values | `51` / `27` / `""`   | Saves Excel as `.xlsx`, PowerPoint as `.pptx`, Word as `.docx` by default |

---

## 5. Run the Installation

Open **Command Prompt as Administrator** (search `cmd` → right-click → Run as administrator),
navigate to the ODT folder, and run the setup command:

```cmd
cd C:\tools\ODT
setup /configure Configuration.xml
```

The ODT will download Office directly from Microsoft's servers and install it using your
configuration. This takes a few minutes depending on your internet connection and the
number of apps and languages selected.

You can follow the progress in the Command Prompt output — it will return to the prompt
when complete.

---

## 6. Verify the Installation

1. Open **Microsoft Word** (or Excel, or PowerPoint) from the Start menu.
2. Confirm it opens without any activation prompts or error dialogs.
3. Go to **File → Account** inside any Office app to verify the product name and license
   status shown matches what you configured.

If the app opens cleanly and shows **Office 2024** under product information, the
installation is complete and properly licensed.

---

## Notes

- The ODT always downloads Office fresh from Microsoft's servers — no offline installer is
  involved unless you configure a local source explicitly in the XML.
- If you need to reinstall later, just re-run `setup /configure Configuration.xml` from
  the same folder. Keep the `C:\tools\ODT\` folder and your XML for this reason.
- To add or remove apps after installation, update the XML (add/remove `<ExcludeApp>`
  entries) and re-run the same command — the ODT handles delta updates cleanly.
- For reference, the [ODT documentation](https://learn.microsoft.com/en-us/deployoffice/overview-office-deployment-tool)
  on Microsoft Learn covers all available XML configuration options in full detail.
