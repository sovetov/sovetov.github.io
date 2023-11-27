# WinRM &ndash; Configuration, Pitfalls and Examples

To catch all the potential issues,
I take two Windows 10 machines. WinRM was never set up on either.
All attempts and errors will be documented.

The server machine has Windows 10 Home on board.
Note that you cannot enable the built-in Administrator account on the Home edition.
There are even no "Local Users and Groups" in Computer Management.

The client laptop's OS is Windows 10 Enterprise.
It's the most powerful version of Windows.
But you don't even need to enable WinRM service on it to use it as a client.

See: <http://www.hurryupandwait.io/blog/understanding-and-troubleshooting-winrm-connection-and-authentication-a-thrill-seekers-guide-to-adventure>

See: <http://www.hurryupandwait.io/blog/safely-running-windows-automation-operations-that-typically-fail-over-winrm-or-powershell-remoting>

See: <https://powershell.one/>

# Configure Server Machine with `winrm quickconfig`

```cmd
winrm quickconfig
```
```
WinRM is not set up to receive requests on this machine.
The following changes must be made:

Start the WinRM service.
Set the WinRM service type to delayed auto start.

Make these changes [y/n]? y

Error: One or more update steps could not be completed.

Could not change the WinRM service type: Access is denied.
Could not start the WinRM service: Access is denied.
WSManFault
    Message The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".

Error number:  -2144108526 0x80338012
The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".
```

## Run cmd.exe as Administrator

Find "cmd" in Start Menu and Shift+Ctrl+Enter on it. Or Run as Administrator in the context menu.
```cmd
winrm quickconfig
```
```
WinRM is not set up to receive requests on this machine.
The following changes must be made:

Start the WinRM service.
Set the WinRM service type to delayed auto start.

Make these changes [y/n]? y

WinRM has been updated to receive requests.

WinRM service type changed successfully.
WinRM service started.
WSManFault
    Message
        ProviderFault
            WSManFault
                Message WinRM firewall exception will not work since one of the network connection types on this machine is set to Public. Change the network connection type to either Domain or Private and try again.

Error number:  -2144108183 0x80338169
WinRM firewall exception will not work since one of the network connection types on this machine is set to Public. Change the network connection type to either Domain or Private and try again.
```

## Make Adapters Public

You may also try running quickconfig with "-quiet"

(GUI images here)

Or in PowerShell:
```powershell
Get-NetConnectionProfile
```
```
Name             : ..........................
InterfaceAlias   : Wi-Fi
InterfaceIndex   : 3
NetworkCategory  : Private
IPv4Connectivity : Internet
IPv6Connectivity : NoTraffic

Name             : Local Area Connection 2
InterfaceAlias   : Local Area Connection
InterfaceIndex   : 17
NetworkCategory  : Public
IPv4Connectivity : LocalNetwork
IPv6Connectivity : NoTraffic
```


The following works only when PowerShell is running as administrator. (Same, search for "PowerShell" in the Start Menu and then Shift+Ctrl+Enter.)

```powershell
Set-NetConnectionProfile -InterfaceIndex 3 -NetworkCategory Private
```
```powershell
Set-NetConnectionProfile -InterfaceIndex 17 -NetworkCategory Private
```

## It Works!

```cmd
winrm quickconfig
```
```
WinRM service is already running on this machine.
WinRM is not set up to allow remote access to this machine for management.
The following changes must be made:

Enable the WinRM firewall exception.
Configure LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.

Make these changes [y/n]? y

WinRM has been updated for remote management.

WinRM firewall exception enabled.
Configured LocalAccountTokenFilterPolicy to grant administrative rights remotely to local users.
```


Works!

# Check WinRM from Client Laptop

Check from another machine. 127.0.0.1 is never checked by the firewall on Windows.

The easiest way to me is to take another Windows laptop connected to the same local network and run some command.

## Find out your IP address

```cmd
ipconfig
```
```
Windows IP Configuration


Unknown adapter Local Area Connection:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::4190:e313:1b60:7fb6%17
   IPv4 Address. . . . . . . . . . . : 10.8.0.10
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :

Ethernet adapter Ethernet 2:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter Local Area Connection* 9:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter Local Area Connection* 10:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :

Wireless LAN adapter Wi-Fi:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::d5cc:470c:4ad9:3dab%3
   IPv4 Address. . . . . . . . . . . : 192.168.1.138
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1

Ethernet adapter Bluetooth Network Connection:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . :
```

## Try First Time

Let's query WinRM client connection settings on your machine from the client laptop.
(We'll soon alter them on the client laptop.)
```cmd
winrm g winrm/config/client -r:192.168.1.138
```
```
WSManFault
    Message The WinRM client cannot process the request. If the authentication scheme is different from Kerberos, or if the client computer is not joined to a domain, then HTTPS transport must be used or the destination machine must be added to the TrustedHosts configuration setting. Use winrm.cmd to configure TrustedHosts. Note that computers in the TrustedHosts list might not be authenticated. You can get more information about that by running the following command: winrm help config.

Error number:  -2144108316 0x803380E4
The WinRM client cannot process the request. If the authentication scheme is different from Kerberos, or if the client computer is not joined to a domain, then HTTPS transport must be used or the destination machine must be added to the TrustedHosts configuration setting. Use winrm.cmd to configure TrustedHosts. Note that computers in the TrustedHosts list might not be authenticated. You can get more information about that by running the following command: winrm help config.
```

## Set Trusted Hosts on Client Laptop in the Registry (Easy Way)

Open `regedit`.
Navigate to `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Client`.
Add `trusted_hosts` String value (`REG_SZ`) with value `192.168.1.138`.

This don't require setting up WinRM.

## Set Trusted Hosts on Client Laptop with the `winrm` Command (Not Necessary)

Doing that in the registry is enough.

```cmd
winrm g winrm/config/client
```
```
WSManFault
    Message The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".

Error number:  -2144108526 0x80338012
The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig".
```

It doesn't work because WinRM service is not running. `winrm quickconfig` helps:
```cmd
winrm g winrm/config
```
```
WSManFault
    Message
        ProviderFault
            WSManFault
                Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

Even if you're is in the `BUILTIN\Administrators` group,
you cannot access the whole config object
because UAC removes administrator privilegies from the security context.

You can access the `client` part though:
```cmd
winrm g winrm/config/client
```
```
Client
    NetworkDelayms 5000
    URLPrefix wsman
    AllowUnencrypted false
    Auth
        Basic true
        Digest true
        Kerberos true
        Negotiate true
        Certificate true
        CredSSP false
    DefaultPorts
        HTTP 5985
        HTTPS 5986
    TrustedHosts  192.168.1.138
```

# Authentication

This is what you see with the incorrect password:
```cmd
winrm g winrm/config/client -r:192.168.1.138 -u:gsovetov -p:qwe
```
```
WSManFault
    Message Access is denied.
```

With the correct password:
```cmd
winrm g winrm/config/client -r:192.168.1.138 -u:gsovetov -p:PaSsW0Rd
```
```
Client
    NetworkDelayms 5000
    URLPrefix wsman
    AllowUnencrypted false
    Auth
        Basic true
        Digest true
        Kerberos true
        Negotiate true
        Certificate true
        CredSSP false
    DefaultPorts
        HTTP 5985
        HTTPS 5986
    TrustedHosts
```

Windows uses local credentials to connect to the remote machine
if you don't specify any:
```cmd
winrm g winrm/config/client -r:192.168.1.138
```
```
Client
    NetworkDelayms 5000
    URLPrefix wsman
    AllowUnencrypted false
    Auth
        Basic true
        Digest true
        Kerberos true
        Negotiate true
        Certificate true
        CredSSP false
    DefaultPorts
        HTTP 5985
        HTTPS 5986
    TrustedHosts
```

# Example: Get current time from the remote machine

Here you'll see how to discover and explore WMI classes.
Some pages or websites may go unavailable in future.
That's why I'll tell you what to search for.

- Search for `windows wmi current time` on the internet.
- You'll find: <https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-tasks--dates-and-times>.
- Look at the code. There's `Win32_OperatingSystem` WMI class.
- Search for it on the internet `"Win32_OperatingSystem"`. Quotes tell the search engine not to interpret the name as several words.
- You'll find: <https://docs.microsoft.com/en-us/windows/win32/cimwin32prov/win32-operatingsystem>.
- There are various data about the OS. Including the current time. We'll query it remotely.

A class is identified with its URL.
This class' URL is `http://schemas.microsoft.com/wbem/wsman/1/wmi/root/cimv2/Win32_OperatingSystem`.
(Find it in the class' documentation.)
Let's query it.

```cmd
winrm get http://schemas.microsoft.com/wbem/wsman/1/wmi/root/cimv2/Win32_OperatingSystem
```
```
Win32_OperatingSystem
    BootDevice \Device\HarddiskVolume1
    BuildNumber 19043
    ...
    LocalDateTime
        Datetime 2021-09-24T21:23:10.616+03:00
    ...
```

The `winrm` command supports namespace aliases.
An alias is replaced with its URL, which become the prefix of the full URL.
Refer to `winrm help aliases`.

All the following commands produce the same output:
```cmd
winrm get http://schemas.microsoft.com/wbem/wsman/1/wmi/root/cimv2/Win32_OperatingSystem
winrm get winrm/wmi/root/cimv2/Win32_OperatingSystem
winrm get wsman/wmi/root/cimv2/Win32_OperatingSystem
winrm get wmi/root/cimv2/Win32_OperatingSystem
winrm get wmicimv2/Win32_OperatingSystem
```

`get` can be abbreviated as `g`.
The following commands produce the same output:
```cmd
winrm get wmicimv2/Win32_OperatingSystem
winrm g wmicimv2/Win32_OperatingSystem
```

# Querying objects

The OS object is special - it's a singleton.

Most WMI classes may have multiple instances.
To list all of them, insted of `get`, use `enumerate`,
which can be contracted to `enum` or `e`.
The wanted object needs to be selected.

See more examples in `winrm help get` and `winrm help enumerate`.

Command that manipulated the config above used the `winrm` alias and queried the client config.
The following commands produce the same result:
```cmd
winrm g winrm/config/client
winrm g http://schemas.microsoft.com/wbem/wsman/1/config/client
```

# Reverse-engineering the protocol

Not for use in production!

WinRM is based on SOAP, which is based on HTTP(S).

Run it over HTTP and capture traffic with Wireshark, Network Monitor or Message Analyzer.
HTTP connections are called "unencrypted" in the WinRM terminology.
Allow unencrypted connections on the client and the server.

```cmd
winrm get wmicimv2/Win32_OperatingSystem -r:192.168.1.138 -un
```
```
WSManFault
    Message The WinRM client cannot process the request. Unencrypted traffic is currently disabled in the client configuration. Change the client configuration and try the request again.

Error number:  -2144108322 0x803380DE
The WinRM client cannot process the request. Unencrypted traffic is currently disabled in the client configuration. Change the client configuration and try the request again.
```

HTTP (unencrypted) is not allowed on the client laptop.

## Allow on the client in the registry

Key: `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Client`
Value name: `allow_unencrypted`
Value type: `DWORD` (boolean values are usually represented with this type)
Value data: 1

## Allow on the client with the `winrm` command

```cmd
winrm set winrm/config/client @{AllowUnencrypted="true"}
```
```
WSManFault
    Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

Administrative privilegies are required to modify the client config.
(Without the privilegies, you can only read it .)

Run `cmd.exe` as administrator and try again:
```cmd
winrm set winrm/config/client @{AllowUnencrypted="true"}
```
```
Client
    NetworkDelayms 5000
    URLPrefix wsman
    AllowUnencrypted true
    Auth
        Basic true
        Digest true
        Kerberos true
        Negotiate true
        Certificate true
        CredSSP false
    DefaultPorts
        HTTP 5985
        HTTPS 5986
    TrustedHosts 192.168.1.138
```

## Try again after unencrypted connections are allowed on the client side

```cmd
winrm get wmicimv2/Win32_OperatingSystem -r:192.168.1.138 -un
```
```
WSManFault
    Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

Unencrypted connections seem to be prohibited on the server side too.

## Allow unencrypted on the server side

```cmd
winrm set winrm/config/service @{AllowUnencrypted="true"}
```
```
WSManFault
    Message
        ProviderFault
            WSManFault
                Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

```cmd
winrm g winrm/config/service
```
```
WSManFault
    Message
        ProviderFault
            WSManFault
                Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

```cmd
winrm g winrm/config
```
```
WSManFault
    Message
        ProviderFault
            WSManFault
                Message Access is denied.

Error number:  -2147024891 0x80070005
Access is denied.
```

You need administrator privilegies to modify the service config, to read the service config or the whole config.

```cmd
winrm set winrm/config/service @{AllowUnencrypted="true"}
```
```
Service
    RootSDDL O:NSG:BAD:P(A;;GA;;;BA)(A;;GR;;;IU)S:P(AU;FA;GA;;;WD)(AU;SA;GXGW;;;WD)
    MaxConcurrentOperations 4294967295
    MaxConcurrentOperationsPerUser 1500
    EnumerationTimeoutms 240000
    MaxConnections 300
    MaxPacketRetrievalTimeSeconds 120
    AllowUnencrypted true
    Auth
        Basic false
        Kerberos true
        Negotiate true
        Certificate false
        CredSSP false
        CbtHardeningLevel Relaxed
    DefaultPorts
        HTTP 5985
        HTTPS 5986
    IPv4Filter *
    IPv6Filter *
    EnableCompatibilityHttpListener false
    EnableCompatibilityHttpsListener false
    CertificateThumbprint
    AllowRemoteAccess true
```

## Try with unencrypted connect allowed on the server

```cmd
winrm get wmicimv2/Win32_OperatingSystem -r:192.168.1.138 -un
```
```
Win32_OperatingSystem
    BootDevice \Device\HarddiskVolume1
    BuildNumber 19043
    ...
    WindowsDirectory C:\Windows
```

It works!

# Authenticating other clients

## Enable Basic authentication

You need administrator privilegies to modify the service config.
```cmd
winrm set winrm/config/service/auth @{Basic="true"}
```
