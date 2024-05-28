# SOCKS5 via SSH on Windows for a specific user

Tunnel Internet traffic via any machine via SSH connection.

Choose direct or proxy connection by launching apps as different users.

## Principles

* SSH supports SOCKS5 proxy (and port forwarding both ways).
* Windows allows to configure proxy settings on the per-user basis.

## 1. `ssh alice@example.com -D 127.0.0.1:1080` to open SOCKS5 proxy

Optionally add `-N` to disable shell and wait for Ctrl+C.

Optionally add `-v` to show something. Otherwise there is no output.

```
ssh alice@example.com -v -N -D 127.0.0.1:1080
```

## 2. Prepare a `.pac` file - a simple JavaScipt script

Use proxy for all connections:
```
function FindProxyForURL(url, host) {
    return "SOCKS5 127.0.0.1:1080;";
}
```

Although, very sophisticated configurations are possible.

See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Proxy_servers_and_tunneling/Proxy_Auto-Configuration_PAC_file

See: https://learn.microsoft.com/en-us/troubleshoot/developer/browsers/connectivity-navigation/optimize-pac-performance

Authentication with passowrd is not supported.

## 3. Serve `.pac` locally via HTTP with IIS

* Open _Turn Windows features on or off_, select and install:
  * _Internet Information Services_
    * _Web Management Tools_
    * _World Wide Web services_
      * _Common HTTP features_
* Create a folder for serving static files via HTTP.
  * Grant _Read and Execute_ to `IUSR` and `IIS_IUSRS`. (401 if not done.)
  * Locate the `.pac` file in this folder.
* Open _Internet Information Services (IIS) Manager_:
  * Create a website:
    * In _Bindings..._, configure only `127.0.0.2`. (Or anyone from Internet can connect.)
    * Add MIME type `application/x-ns-proxy-autoconfig` for `.pac`. (404 if not done.)
    * Optionally, enable _Directory Browsing_. (403 on index page if not done.)
  * Disable stop the _Default Web Site_ or configure similar bindings. (For security too.)
  * Open http://127.0.0.2/ (configured in _Bindings..._) in browser.
    * Click the `.pac` file. It should be downloaded.
    * Copy the link address of the `.pac` file via context menu.

## 4. Configure proxy in the system for another user

Run as _Administrator_:
```
reg add "HKU\S-1-5-21-1938875136-3881193919-344594422-1002\Software\Microsoft\Windows\CurrentVersion\Internet Settings" /v AutoConfigURL /t REG_SZ /d "http://127.0.0.2/proxy.pac" /f
```

Where:
* `S-1-5-21-1938875136-3881193919-344594422-1002` is user's SID.
* `HKU\S-1-5-21-1938875136-3881193919-344594422-1002` is user's registry hive. (Each user sees their hive as `HKCU`.)

## 5. Open a browser as this user

Tested with Google Chrome and Firefox.

* Search for `Chrome` or `Firefox` in Start menu.
* Right click and `Open file location`.
* Hold <kbd>Ctrl</kbd>+<kbd>Shift</kbd> and right lick.
* Choose `Run as different user`. Type username and password of the user.
* Check you IP address.

## 6. Block browser connections in firewall

Block the browser completely in firewall. Connections to 127.0.0.0/8 bypass Windows Firewall anyway.

Windows Firewall supports rules based on application and user (local principal).

Consider blocking all outbound traffic by default and allowing only specific applications and users (local principals).