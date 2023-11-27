# NCSI - Windows Internet connection test

See: <https://learn.microsoft.com/en-us/windows-server/networking/ncsi/ncsi-overview>

See: <https://learn.microsoft.com/en-us/troubleshoot/windows-client/networking/internet-explorer-edge-open-connect-corporate-public-network>

See: <https://superuser.com/q/1650052/174311>

## Default settings in Registry

`Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NlaSvc\Parameters\Internet`

```ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NlaSvc\Parameters\Internet]
"ActiveDnsProbeContent"="131.107.255.255"
"ActiveDnsProbeContentV6"="fd3e:4f5a:5b81::1"
"ActiveDnsProbeHost"="dns.msftncsi.com"
"ActiveDnsProbeHostV6"="dns.msftncsi.com"
"ActiveWebProbeContent"="Microsoft Connect Test"
"ActiveWebProbeContentV6"="Microsoft Connect Test"
"ActiveWebProbeHost"="www.msftconnecttest.com"
"ActiveWebProbeHostV6"="ipv6.msftconnecttest.com"
"ActiveWebProbePath"="connecttest.txt"
"ActiveWebProbePathV6"="connecttest.txt"
"CaptivePortalTimer"=dword:00000000
"CaptivePortalTimerBackOffIncrementsInSeconds"=dword:00000005
"CaptivePortalTimerMaxInSeconds"=dword:0000001e
"EnableActiveProbing"=dword:00000001
"PassivePollPeriod"=dword:0000000f
"StaleThreshold"=dword:0000001e
"WebTimeout"=dword:00000023

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NlaSvc\Parameters\Internet\ManualProxies]
```

## Firewall rule

```powershell
(Get-NetFirewallRule | ? { $_.DisplayName -contains 'netprofm' })
```
```
Name                          : {F0114982-D442-4515-9F88-8173AF9A0C1D}
DisplayName                   : netprofm
Description                   :
DisplayGroup                  :
Group                         :
Enabled                       : False
Profile                       : Any
Platform                      : {}
Direction                     : Outbound
Action                        : Allow
EdgeTraversalPolicy           : Block
LooseSourceMapping            : False
LocalOnlyMapping              : False
Owner                         :
PrimaryStatus                 : OK
Status                        : The rule was parsed successfully from the store. (65536)
EnforcementStatus             : NotApplicable
PolicyStoreSource             : PersistentStore
PolicyStoreSourceType         : Local
RemoteDynamicKeywordAddresses : {}
PolicyAppId                   :
```
```powershell
(Get-NetFirewallRule | ? { $_.DisplayName -contains 'netprofm' }) | Get-NetFirewallServiceFilter
```
Service : netprofm
```
```powershell
(Get-NetFirewallRule | ? { $_.DisplayName -contains 'netprofm' }) | Get-NetFirewallApplicationFilter
```
```
Program : %SystemRoot%\System32\svchost.exe
Package :
```