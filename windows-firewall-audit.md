# Windows Firewall audit

## Examine configuration

```cmd
auditpol /get /category:"Object Access"
```
```
System audit policy
Category/Subcategory                      Setting
Object Access
  File System                             No Auditing
  Registry                                No Auditing
  Kernel Object                           No Auditing
  SAM                                     No Auditing
  Certification Services                  No Auditing
  Application Generated                   No Auditing
  Handle Manipulation                     No Auditing
  File Share                              No Auditing
  Filtering Platform Packet Drop          No Auditing
  Filtering Platform Connection           No Auditing
  Other Object Access Events              No Auditing
  Detailed File Share                     No Auditing
  Removable Storage                       No Auditing
  Central Policy Staging                  No Auditing
```

## Enable

```cmd
auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:enable /failure:enable
```
```
The command was successfully executed.
```

```cmd
auditpol /set /subcategory:"Filtering Platform Connection" /success:enable /failure:enable
```
```
The command was successfully executed.
```

## Disable

```cmd
auditpol /set /subcategory:"Filtering Platform Connection" /success:disable /failure:disable
```
```
The command was successfully executed.
```

```cmd
auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:disable /failure:disable
```
```
The command was successfully executed.
```

## View events in PowerShell with `Get-EventLog`

```powershell
Get-EventLog -LogName Security -After (Get-Date).AddMinutes(-1) -Source @("Microsoft-Windows-Security-Auditing") -InstanceId @(5150..5159)
```
```
   Index Time          EntryType   Source                 InstanceID Message
   ----- ----          ---------   ------                 ---------- -------
 5755787 Nov 26 00:14  FailureA... Microsoft-Windows...         5157 The Windows Filtering Platform has blocked a connection....
 5755786 Nov 26 00:14  FailureA... Microsoft-Windows...         5152 The Windows Filtering Platform has blocked a packet....
 5755785 Nov 26 00:14  FailureA... Microsoft-Windows...         5157 The Windows Filtering Platform has blocked a connection....
 5755784 Nov 26 00:14  FailureA... Microsoft-Windows...         5152 The Windows Filtering Platform has blocked a packet....
 5755783 Nov 26 00:14  FailureA... Microsoft-Windows...         5157 The Windows Filtering Platform has blocked a connection....
```

## View events in PowerShell with `Get-WinEvent`

```powershell
Get-WinEvent -FilterHashtable @{
  Logname='Security'
  ProviderName='Microsoft-Windows-Security-Auditing'
  ID=@(5150..5159)
  StartTime=(Get-Date).AddMinutes(-5)
}
```
```
   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
2023-11-26 0:21:02             5157 Information      The Windows Filtering Platform has blocked a connection....
2023-11-26 0:21:02             5152 Information      The Windows Filtering Platform has blocked a packet....
2023-11-26 0:21:02             5158 Information      The Windows Filtering Platform has permitted a bind to a local port....
2023-11-26 0:21:02             5157 Information      The Windows Filtering Platform has blocked a connection....
2023-11-26 0:21:02             5152 Information      The Windows Filtering Platform has blocked a packet....
```

## XML filter for Event Viewer

```xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and ( (EventID &gt;= 5150 and EventID &lt;= 5159) ) and TimeCreated[timediff(@SystemTime) &lt;= 300000]]]</Select>
  </Query>
</QueryList>
```

## View events in PowerShell with `Get-WinEvent` using XML filter

```powershell
Get-WinEvent -FilterXML @'
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and ( (EventID &gt;= 5150 and EventID &lt;= 5159) ) and TimeCreated[timediff(@SystemTime) &lt;= 300000]]]</Select>
  </Query>
</QueryList>
'@
```
```
   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
2023-11-26 0:16:22             5156 Information      The Windows Filtering Platform has permitted a connection....
2023-11-26 0:16:22             5156 Information      The Windows Filtering Platform has permitted a connection....
2023-11-26 0:16:21             5156 Information      The Windows Filtering Platform has permitted a connection....
2023-11-26 0:16:21             5156 Information      The Windows Filtering Platform has permitted a connection....
2023-11-26 0:16:21             5156 Information      The Windows Filtering Platform has permitted a connection....
```

## View programs that failed to connect

```powershell
filter Extend-WinEvent {
    $dom = [xml]$_.ToXml()
    foreach ($datum in $dom.Event.EventData.Data) {
        Add-Member -InputObject $_ NoteProperty $datum.Name $datum.'#text' -Force
    }
    $_
}

Get-WinEvent -FilterHashtable @{Logname='Security'; ID=5157; StartTime=(Get-Date).AddSeconds(-300)} |
    Extend-WinEvent |
    select DestAddress,DestPort,ProcessID,Application |
    sort * -Unique
```
```
DestAddress     DestPort ProcessID Application                                         
-----------     -------- --------- -----------                                         
20.190.177.147  443      2860      \device\harddiskvolume3\windows\system32\svchost.exe
20.190.177.148  443      2860      \device\harddiskvolume3\windows\system32\svchost.exe
20.190.177.149  443      2860      \device\harddiskvolume3\windows\system32\svchost.exe
```

## View programs with command line

```powershell
filter Extend-WinEvent {
    $dom = [xml]$_.ToXml()
    foreach ($datum in $dom.Event.EventData.Data) {
        Add-Member -InputObject $_ NoteProperty $datum.Name $datum.'#text' -Force
    }
    $_
}
filter Extend-Process {
    $processId = $_.ProcessId
    $application = $_.Application
    try {
        $object = [wmi]"Win32_Process.Handle=""$processId"""
        $applicationOrCommandLine = $object.CommandLine
    }
    catch {
        $applicationOrCommandLine = $application
    }
    Add-Member -InputObject $_ NoteProperty 'ApplicationOrCommandLine' $applicationOrCommandLine
    $_
}
Get-WinEvent -FilterHashtable @{Logname='Security'; ID=5157; StartTime=(Get-Date).AddSeconds(-300)} |
    Extend-WinEvent |
    Extend-Process |
    select DestAddress,DestPort,ProcessID,ApplicationOrCommandLine |
    sort * -Unique |
    ft -AutoSize
```
```
DestAddress     DestPort ProcessID ApplicationOrCommandLine                                                          
-----------     -------- --------- ------------------------                                                          
23.200.162.90   443      10232     "C:\Users\gsovetov\AppData\Local\Microsoft\BingWallpaperApp\BingWallpaperApp.exe" 
239.255.255.250 1900     3872      C:\WINDOWS\system32\svchost.exe -k LocalServiceAndNoImpersonation -p -s SSDPSRV   
88.221.17.175   443      9192      C:\WINDOWS\System32\svchost.exe -k netsvcs -p -s BITS                             
239.255.255.250 3702     3004      dashost.exe {bba43f3f-aec2-484a-9aafd54002107856}                                 
ff02::c         3702     3004      dashost.exe {bba43f3f-aec2-484a-9aafd54002107856}                                 
```