# Allow app access to a specific domain in Windows Firewall
```powershell
function Allow-DomainName {
    param (
        [string] [ValidateNotNullOrEmpty()] [Parameter(Mandatory)] $group,
        [string] [ValidateNotNullOrEmpty()] [Parameter(Mandatory)] [ValidateScript({Test-Path $_})] $app,
        [string] [ValidateNotNullOrEmpty()] [Parameter(Mandatory, ValueFromPipeline)] $domain
    )

    PROCESS {
        $addresses = (Resolve-DnsName $domain -Type A).IPAddress
        $filename = Split-Path $app -Leaf
        $name = "$group - $filename - $domain"
        $rule = New-NetFirewallRule -Name $name -DisplayName $name -Enabled True -Direction Outbound -Action Allow -RemoteAddress $addresses
        $rule.Group = $group
        Set-NetFirewallRule -InputObject $rule
        $rule
    }
}

$g = "0 Trust"

$d = @(
"dns.google",
"google.com"
)

Remove-NetFirewallRule -Group $g
$d | Allow-DomainName $g 'C:\Program Files\Oracle\VirtualBox\VirtualBoxVM.exe' | Format-Table Name,Direction,Action,Profile
```