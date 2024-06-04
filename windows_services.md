# Disable Edge Startup boost

Open in Edge: edge://settings/?search=startup

See: https://aka.ms/EdgeStartupBoostFAQ

It constantly tries to connect Internet.

# Disable wlidsvc - Microsoft Account Sign-in Assistant

Connects to Internet (login.live.com) constantly.

Live id is not needed.

# ~~Disable netprofm - Network List Service~~

~~Disables Wi-Fi when it cannot connect to http://www.msftconnecttest.com/ , even if the firewall blocks it.~~

~~The network icon in the system tray must become normal (not offline).~~

Causes Explorer (Taskbar) to crash on startup.

Consider allowing this service in the Firewall.

# Disable utcsvc - Connected User Experiences and Telemetry

Attempts to access Internet (settings-win.data.microsoft.com).

# Remove apps

List installed apps:
```
Get-AppxProvisionedPackage -Online
```

Remove an app (example):
```
Remove-AppxProvisionedPackage -online -PackageName 'Microsoft.Xbox.TCUI_1.24.10001.0_neutral_~_8wekyb3d8bbwe'
```

* Redirect list of apps to a file
* Remove apps that should stay
* With Vim or another advanced text editor, transform lines to removal commands. Or use PowerShell scripting to run the removal command for each line.

Cannot be removed:
* `Microsoft.DesktopAppInstaller_2024.326.2005.0_neutral_~_8wekyb3d8bbwe`

Consider not to remove:
* `Microsoft.LanguageExperiencePackru-RU_22621.56.210.0_neutral__8wekyb3d8bbwe`
* `Microsoft.Paint_11.2304.33.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.RawImageExtension_2.3.611.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.SecHealthUI_1000.25873.9001.0_x64__8wekyb3d8bbwe`
* `Microsoft.UI.Xaml.2.4_2.42007.9001.0_x64__8wekyb3d8bbwe`
* `Microsoft.VCLibs.140.00_14.0.27323.0_x64__8wekyb3d8bbwe`
* `Microsoft.VP9VideoExtensions_1.1.451.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WebMediaExtensions_1.0.62931.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WebpImageExtension_1.0.62681.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.Windows.Photos_2023.11100.11002.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WindowsAlarms_2022.2306.23.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WindowsCalculator_2021.2401.0.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WindowsCamera_2022.2305.4.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WindowsNotepad_11.2401.26.0_neutral_~_8wekyb3d8bbwe`
* `Microsoft.WindowsTerminal_3001.19.10573.0_neutral_~_8wekyb3d8bbwe`
* `RealtekSemiconductorCorp.RealtekAudioControl_1.26.250.0_neutral_~_dt26b99r8h8gj`


# Uninstall AMD drivers

It constantly tries to connect Internet and search for updates.

# Uninstall Skype

From _Apps > Installed apps_.

# To do:

* Apply recommendations from https://learn.microsoft.com/en-us/windows/privacy/manage-connections-from-windows-operating-system-components-to-microsoft-services#bkmk-cortana
* Allow Internet Printer Protocol (port 631/tcp)
* Allow or disable SSDP for dashost.exe and chrome.exe
* Disable Chrome updates (done with Group Policies)

# See also

* https://learn.microsoft.com/en-us/windows/privacy/manage-connections-from-windows-operating-system-components-to-microsoft-services
* https://learn.microsoft.com/en-us/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10
* https://www.microsoft.com/en-us/download/details.aspx?id=55319
* https://habr.com/ru/companies/ruvds/articles/778466/
* https://github.com/henrypp/simplewall
