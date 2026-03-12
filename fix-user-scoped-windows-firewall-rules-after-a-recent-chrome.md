# Fix user-scoped Windows Firewall rules after a recent Chrome update

This is caused by Chrome's Network Service Sandbox,
which is the process running with a restricted token to improve security.
On the other hand, it limits the ability to filter network connections by a user.

    reg add HKLM\SOFTWARE\Policies\Google\Chrome /v NetworkServiceSandboxEnabled /t REG_DWORD /d 0 /f

Check that Chrome sees the setting (no restat needed):

* Visit [chrome://policy/](chrome://policy/)
* Click `Reload policies`
* See `NetworkServiceSandboxEnabled` appears with policy value `false`

Restart Chrome.