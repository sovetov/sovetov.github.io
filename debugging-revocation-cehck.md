# Debugging revocation check on Windows

## cURL fails with 0x80092013

```
curl -vvv --head https://github.com/
```
```
*   Trying 140.82.121.3:443...
* Connected to github.com (140.82.121.3) port 443 (#0)
* schannel: disabled automatic use of client certificate
* ALPN: offers http/1.1
* schannel: next InitializeSecurityContext failed: Unknown error (0x80092013) - The revocation function was unable to check revocation because the revocation server was offline.
* Closing connection 0
* schannel: shutting down SSL/TLS connection with github.com port 443
curl: (35) schannel: next InitializeSecurityContext failed: Unknown error (0x80092013) - The revocation function was unable to check revocation because the revocation server was offline.
```

## OpenSSL shows website certificate

Git comes with OpenSSL. Use it.

```
echo "" | "c:\Program Files\Git\usr\bin\openssl.exe" s_client -showcerts -servername github.com -connect github.com:443 > c.txt
```
```
depth=2 C = US, O = DigiCert Inc, OU = www.digicert.com, CN = DigiCert Global Root CA
verify return:1
depth=1 C = US, O = DigiCert Inc, CN = DigiCert TLS Hybrid ECC SHA384 2020 CA1
verify return:1
depth=0 C = US, ST = California, L = San Francisco, O = "GitHub, Inc.", CN = github.com
verify return:1
DONE
```

```
"c:\Program Files\Git\usr\bin\openssl.exe" x509 -in c.txt -text -noout
```
```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            0c:d0:a8:be:c6:32:cf:e6:45:ec:a0:a9:b0:84:fb:1c
        Signature Algorithm: ecdsa-with-SHA384
        Issuer: C = US, O = DigiCert Inc, CN = DigiCert TLS Hybrid ECC SHA384 2020 CA1
        Validity
            Not Before: Feb 14 00:00:00 2023 GMT
            Not After : Mar 14 23:59:59 2024 GMT
        Subject: C = US, ST = California, L = San Francisco, O = "GitHub, Inc.", CN = github.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:a3:a4:03:46:03:df:46:51:56:cb:c9:39:ab:22:
                    cd:e7:6c:59:96:7a:93:a0:fb:b9:40:1c:90:32:88:
                    36:c6:09:76:9c:50:f5:55:f7:76:5e:68:20:9c:ee:
                    22:ed:83:0c:15:30:10:41:44:5e:32:ac:90:a1:d5:
                    aa:f2:e5:43:b3
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Authority Key Identifier:
                keyid:0A:BC:08:29:17:8C:A5:39:6D:7A:0E:CE:33:C7:2E:B3:ED:FB:C3:7A

            X509v3 Subject Key Identifier:
                C7:07:27:78:85:F2:9D:33:C9:4C:5E:56:7D:5C:D6:8E:72:67:EB:DE
            X509v3 Subject Alternative Name:
                DNS:github.com, DNS:www.github.com
            X509v3 Key Usage: critical
                Digital Signature
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 CRL Distribution Points:

                Full Name:
                  URI:http://crl3.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crl

                Full Name:
                  URI:http://crl4.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crl

            X509v3 Certificate Policies:
                Policy: 2.23.140.1.2.2
                  CPS: http://www.digicert.com/CPS

            Authority Information Access:
                OCSP - URI:http://ocsp.digicert.com
                CA Issuers - URI:http://cacerts.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crt

            X509v3 Basic Constraints:
                CA:FALSE
            CT Precertificate SCTs:
                Signed Certificate Timestamp:
                    Version   : v1 (0x0)
                    Log ID    : EE:CD:D0:64:D5:DB:1A:CE:C5:5C:B7:9D:B4:CD:13:A2:
                                32:87:46:7C:BC:EC:DE:C3:51:48:59:46:71:1F:B5:9B
                    Timestamp : Feb 14 16:58:33.338 2023 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:46:02:21:00:E4:16:AE:D3:E2:2C:BA:82:9F:A9:79:
                                F2:4B:C6:94:52:ED:4D:E0:87:CC:50:CA:69:B1:B4:8F:
                                05:77:3A:94:EB:02:21:00:B5:9F:C3:F9:CB:0F:AD:D0:
                                60:F2:30:1B:71:05:72:12:0D:BD:65:1F:07:A9:9C:53:
                                4B:76:95:12:04:A6:BF:2E
                Signed Certificate Timestamp:
                    Version   : v1 (0x0)
                    Log ID    : 48:B0:E3:6B:DA:A6:47:34:0F:E5:6A:02:FA:9D:30:EB:
                                1C:52:01:CB:56:DD:2C:81:D9:BB:BF:AB:39:D8:84:73
                    Timestamp : Feb 14 16:58:33.387 2023 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:45:02:20:1E:3C:60:32:7E:20:51:F5:D6:E1:AF:7D:
                                4D:F5:97:C4:48:2E:46:57:6B:86:05:37:32:4F:25:04:
                                36:B1:F7:B7:02:21:00:FC:09:7E:C0:7C:03:83:26:36:
                                BD:A7:5B:EB:1D:13:59:F6:62:20:8E:6D:6F:B7:0D:31:
                                EB:DB:F5:11:EE:5B:D4
                Signed Certificate Timestamp:
                    Version   : v1 (0x0)
                    Log ID    : 3B:53:77:75:3E:2D:B9:80:4E:8B:30:5B:06:FE:40:3B:
                                67:D8:4F:C3:F4:C7:BD:00:0D:2D:72:6F:E1:FA:D4:17
                    Timestamp : Feb 14 16:58:33.402 2023 GMT
                    Extensions: none
                    Signature : ecdsa-with-SHA256
                                30:46:02:21:00:CC:E0:6B:F4:E6:74:FB:A3:92:67:21:
                                53:8B:2C:0D:EB:83:F2:B0:DD:05:2D:E2:D1:C8:BE:63:
                                98:4B:18:AC:36:02:21:00:EE:D2:3B:60:5A:23:08:29:
                                4E:82:33:47:4A:72:A5:16:2E:46:85:13:6D:DC:DA:25:
                                80:85:80:07:AA:B1:51:47
    Signature Algorithm: ecdsa-with-SHA384
         30:64:02:30:04:dc:0d:d4:de:34:99:0a:9c:1f:a8:e1:c1:76:
         5c:62:f4:04:a0:29:35:3e:c2:0d:2a:c3:71:6a:b5:f4:37:d4:
         ec:0b:60:57:71:87:43:25:36:4f:c7:c2:48:d1:49:68:02:30:
         56:d0:bc:c9:17:10:fb:cd:be:fe:2d:df:42:ba:c6:da:46:db:
         aa:a6:67:ee:8e:88:84:81:20:85:cc:96:35:a7:b2:26:11:d6:
         0c:99:9d:3c:c8:83:70:10:4b:0e:15:9b
```

## CRL Distribution Points

URLs from the certificate:
```
http://crl3.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crl
http://crl4.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crl
```

```
nslookup "crl3.digicert.com"
nslookup "crl4.digicert.com"
```

```
C:\Windows\System32>nslookup "crl3.digicert.com"
Server:  UnKnown
Address:  192.168.10.1

Non-authoritative answer:
Name:    fp2e7a.wpc.phicdn.net
Address:  192.229.221.95
Aliases:  crl3.digicert.com
          crl.edge.digicert.com
          fp2e7a.wpc.2be4.phicdn.net


C:\Windows\System32>nslookup "crl4.digicert.com"
Server:  UnKnown
Address:  192.168.10.1

Non-authoritative answer:
Name:    fp2e7a.wpc.phicdn.net
Address:  192.229.221.95
Aliases:  crl4.digicert.com
          crl.edge.digicert.com
          fp2e7a.wpc.2be4.phicdn.net
```

The IP address:
```
192.229.221.95
```

Try to get them with cURL:
```
curl -vvv --head http://crl4.digicert.com/DigiCertTLSHybridECCSHA3842020CA1-1.crl
```
```
*   Trying 192.229.221.95:80...
* Connected to crl4.digicert.com (192.229.221.95) port 80 (#0)
> HEAD /DigiCertTLSHybridECCSHA3842020CA1-1.crl HTTP/1.1
> Host: crl4.digicert.com
> User-Agent: curl/8.0.1
> Accept: */*
>
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< Accept-Ranges: bytes
Accept-Ranges: bytes
< Age: 5436
Age: 5436
< cache-control: max-age=7200, public
cache-control: max-age=7200, public
< Content-Type: application/pkix-crl
Content-Type: application/pkix-crl
< Date: Sun, 26 Nov 2023 20:11:59 GMT
Date: Sun, 26 Nov 2023 20:11:59 GMT
< Etag: "6562aa2f-28e3"
Etag: "6562aa2f-28e3"
< expires: Sun, 26 Nov 2023 22:11:59 GMT
expires: Sun, 26 Nov 2023 22:11:59 GMT
< last-modified: Sun, 26 Nov 2023 02:15:11 GMT
last-modified: Sun, 26 Nov 2023 02:15:11 GMT
< Server: ECAcc (frc/4CD7)
Server: ECAcc (frc/4CD7)
< X-Cache: HIT
X-Cache: HIT
< Content-Length: 10467
Content-Length: 10467

<
* Connection #0 to host crl4.digicert.com left intact
```

They are accessible.

## Potential cause: combination of firewall settings and how OS cryptography works

Perhaps, cURL is allowed in the firewall but something that queries CRL is not.

## Process id in Firewall log file `C:\Windows\System32\LogFiles\Firewall\pfirewall.log`

```
type "C:\Windows\System32\LogFiles\Firewall\pfirewall.log"
```
```
#Version: 1.5
#Software: Microsoft Windows Firewall
#Time Format: Local
#Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path pid

2023-11-26 22:27:57 DROP TCP 192.168.10.106 192.229.221.95 65457 80 0 - 0 0 0 - - - SEND 1360
2023-11-26 22:27:57 DROP TCP 192.168.10.106 192.229.221.95 65458 80 0 - 0 0 0 - - - SEND 1360
2023-11-26 22:27:57 DROP TCP 192.168.10.106 192.229.221.95 65459 80 0 - 0 0 0 - - - SEND 1360
2023-11-26 22:27:57 DROP TCP 192.168.10.106 192.229.221.95 65460 80 0 - 0 0 0 - - - SEND 1360
2023-11-26 22:27:57 DROP TCP 192.168.10.106 192.229.221.95 65461 80 0 - 0 0 0 - - - SEND 1360
```

## lsass.exe does the request, Firewall audit indicates

[Read up on Firewall audit]({% link windows-firewall-audit.md %}).

```powershell
C:\WINDOWS\system32> filter Extend-WinEvent {
    $dom = [xml]$_.ToXml()
    foreach ($datum in $dom.Event.EventData.Data) {
        Add-Member -InputObject $_ NoteProperty $datum.Name $datum.'#text' -Force
    }
    $_
}
```
```powershell
Get-WinEvent -FilterHashtable @{Logname='Security'; ID=@(5150..5159 ; 5030..5040); StartTime=(Get-Date).AddSeconds(-120)} |
    Extend-WinEvent | Extend-Process |
    select ID,Protocol,SourceAddress,SourcePort,DestAddress,DestPort,ProcessID,ApplicationOrCommandLine,Message |
    ft
```
```
  Id Protocol SourceAddress  SourcePort DestAddress    DestPort ProcessID ApplicationOrCommandLine
  -- -------- -------------  ---------- -----------    -------- --------- ------------------------
5152 6        140.82.121.4   443        192.168.10.106 65456    0
5157 6        192.168.10.106 65461      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5152 6        192.168.10.106 65461      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5158 6        0.0.0.0        65461                              1360      C:\WINDOWS\system32\lsass.exe
5157 6        192.168.10.106 65460      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5152 6        192.168.10.106 65460      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5158 6        0.0.0.0        65460                              1360      C:\WINDOWS\system32\lsass.exe
5157 6        192.168.10.106 65459      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5152 6        192.168.10.106 65459      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5158 6        0.0.0.0        65459                              1360      C:\WINDOWS\system32\lsass.exe
5157 6        192.168.10.106 65458      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5152 6        192.168.10.106 65458      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5158 6        0.0.0.0        65458                              1360      C:\WINDOWS\system32\lsass.exe
5157 6        192.168.10.106 65457      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5152 6        192.168.10.106 65457      192.229.221.95 80       1360      C:\WINDOWS\system32\lsass.exe
5158 6        0.0.0.0        65457                              1360      C:\WINDOWS\system32\lsass.exe
5156 6        192.168.10.106 65456      140.82.121.4   443      6244      \device\harddiskvolume3\windows\system32\curl.exe
5158 6        0.0.0.0        65456                              6244      \device\harddiskvolume3\windows\system32\curl.exe
5156 17       192.168.10.106 56966      192.168.10.1   53       2840      C:\WINDOWS\system32\svchost.exe -k NetworkService -p
5156 6        127.0.0.1      65455      127.0.0.1      65454    6244      \device\harddiskvolume3\windows\system32\curl.exe
5156 6        127.0.0.1      65455      127.0.0.1      65454    6244      \device\harddiskvolume3\windows\system32\curl.exe
5158 6        0.0.0.0        65455                              6244      \device\harddiskvolume3\windows\system32\curl.exe
5154 6        127.0.0.1      65454                              6244      \device\harddiskvolume3\windows\system32\curl.exe
5158 6        127.0.0.1      65454                              6244      \device\harddiskvolume3\windows\system32\curl.exe
5156 17       192.168.10.100 5353       224.0.0.251    5353     2840      C:\WINDOWS\system32\svchost.exe -k NetworkService -p
```

It is `C:\WINDOWS\system32\lsass.exe`

## Process monitor?

Process Monitor shows "TCP Disconnect" events to 192.229.221.95 from lsass.exe surrounded by many registry queries

## Wireshark?

Wireshark shows no traffic to 192.229.221.95

## Event log `Microsoft-Windows-CAPI2/Operational`

```
wevtutil sl Microsoft-Windows-CAPI2/Operational /e:true
```
```powershell
Get-WinEvent Microsoft-Windows-CAPI2/Operational
```
```
   ProviderName: Microsoft-Windows-CAPI2

TimeCreated                      Id LevelDisplayName Message
-----------                      -- ---------------- -------
2023-11-27 1:13:32               30 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               11 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               90 Information      For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               41 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               42 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               42 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               53 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               52 Information      For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               40 Information      For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               41 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               42 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               53 Error            For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               52 Information      For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               40 Information      For more details for this event, please refer to the "Details" section
2023-11-27 1:13:32               10 Information      For more details for this event, please refer to the "Details" section
```

Here is one of events with id 41 - a revocation result. (This is result of a request to another domain, `google.com`.)
```powershell
Get-WinEvent -FilterHashtable @{Logname='Microsoft-Windows-CAPI2/Operational'; ID=@(41)} -MaxEvents 1 | % { ([xml]$_.ToXml()).Save([Console]::Out) }
```
```xml
<?xml version="1.0" encoding="IBM437"?>
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
  <System>
    <Provider Name="Microsoft-Windows-CAPI2" Guid="{5bbca4a8-b209-48dc-a8c7-b23d3e5216fb}" />
    <EventID>41</EventID>
    <Version>0</Version>
    <Level>2</Level>
    <Task>41</Task>
    <Opcode>2</Opcode>
    <Keywords>0x4000000000000005</Keywords>
    <TimeCreated SystemTime="2023-11-27T02:07:57.5609376Z" />
    <EventRecordID>4204</EventRecordID>
    <Correlation ActivityID="{011a12e2-1f9d-0004-9413-1a019d1fda01}" />
    <Execution ProcessID="1360" ThreadID="4380" />
    <Channel>Microsoft-Windows-CAPI2/Operational</Channel>
    <Computer>DESKTOP-TEB080J</Computer>
    <Security UserID="S-1-5-21-1938875136-3881193919-344594422-1001" />
  </System>
  <UserData>
    <CertVerifyRevocation>
      <Certificate fileRef="4C0D178CF1307C3A6F9B8EB4830E5CBDED173E95.cer" subjectName="*.google.com" />
      <IssuerCertificate fileRef="1E7EF647CBA150281C60897257102878C4BD8CDC.cer" subjectName="GTS CA 1C3" />
      <Flags value="0" />
      <AdditionalParameters currentTime="2023-11-27T02:07:57.375Z" />
      <RevocationStatus index="0" error="80092013" reason="0" thirdPartyProviderUsed="C:\Windows\System32\cryptnet.dll" />
      <EventAuxInfo ProcessName="lsass.exe" impersonateToken="S-1-5-21-1938875136-3881193919-344594422-1001" />
      <CorrelationAuxInfo TaskId="{B3C609FC-D773-4517-A317-556F11C0B3C4}" SeqNumber="19" />
      <Result value="80092013">The revocation function was unable to check revocation because the revocation server was offline.</Result>
    </CertVerifyRevocation>
  </UserData>
</Event>
```

## Disable logging and audit

```powershell
Set-NetFireWallProfile -LogBlocked NotConfigured -LogAllowed NotConfigured
```
```
wevtutil sl Microsoft-Windows-CAPI2/Operational /e:false
auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:enable /failure:enable
auditpol /set /subcategory:"Filtering Platform Connection" /success:enable /failure:enable
```

## Solution

Add a Firewall rule for `lsass.exe` or `192.229.221.95`.