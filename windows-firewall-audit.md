# Windows Firewall audit

## Examine configuration

```cmd
C:\Windows\System32>auditpol /get /category:"Object Access"
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
C:\Windows\System32>auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:enable /failure:enable
The command was successfully executed.
```

```cmd
C:\Windows\System32>auditpol /set /subcategory:"Filtering Platform Connection" /success:enable /failure:enable
The command was successfully executed.
```

## Disable

```cmd
C:\Windows\security\database>auditpol /set /subcategory:"Filtering Platform Connection" /success:disable /failure:disable
The command was successfully executed.
```

```cmd
C:\Windows\security\database>auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:disable /failure:disable
The command was successfully executed.
```

## XML filter for Event Viewer

```xml
<QueryList>
  <Query Id="0" Path="Security">
    <Select Path="Security">*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and ( (EventID &gt;= 5150 and EventID &lt;= 5159) ) and TimeCreated[timediff(@SystemTime) &lt;= 300000]]]</Select>
  </Query>
</QueryList>
```