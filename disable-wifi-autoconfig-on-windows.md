# Disble Wi-Fi autoconfig on Windows

```
netsh.exe wlan set autoconfig no "Wi-Fi"
```

## Diagosing

EventViewer:

1. EventViewer. Fold everything first! Then `View > Show Analytics and Debug logs`.
2. `Applications and Services Logs > Microsoft > Windows > WLAN-AutoConfig > Diagnostics`.
3. Search for an event: `Limited Connectivity Recovery Type: None Event: Considered Data: 0x5`.

## Editing registry does not help

Setting this does not help: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WcmSvc\wifinetworkmanager\config\AutoConnectAllowedOEM`
