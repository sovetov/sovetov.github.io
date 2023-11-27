# Haskell on Windows, portable

Based on: <https://www.haskell.org/ghcup/install/>

Principles (most important first):
- Understand what is going on
- Verify where possible
- Portable (non-invasive)
- Automated where possible

## Work dir
`cd C:\Users\gsovetov\Documents\haskell`

## MSYS2

### Download and unpack

Download a self-extracting archive from <https://github.com/msys2/msys2-installer/releases>.

For example, <https://github.com/msys2/msys2-installer/releases/download/2023-10-26/msys2-base-x86_64-20231026.sfx.exe>.

Verify checksum:
```
certutil -hashfile msys2-base-x86_64-20231026.sfx.exe sha256
```

Extract by running executable `msys2-base-x86_64-20231026.sfx.exe`

### Add to `PATH`
```
set PATH=%PATH%;c:\Users\gsovetov\Documents\haskell\msys64\usr\bin
```
Or this error appear when running `pacman`:
```
x86_64-mingw64-ghcup-0.1.20.0.exe: pacman: createProcess: does not exist (No such file or directory)
```

### Run MSYS2 for the first time to let it configure itself
```
msys64\mingw64.exe
```
```
MSYS2 is starting for the first time. Executing the initial setup.
Copying skeleton files.
These files are for the users to personalise their msys2 experience.

They will never be overwritten nor automatically updated.

'./.bashrc' -> '/home/gsovetov/.bashrc'
'./.bash_logout' -> '/home/gsovetov/.bash_logout'
'./.bash_profile' -> '/home/gsovetov/.bash_profile'
'./.profile' -> '/home/gsovetov/.profile'
'C:\WINDOWS\system32\drivers\etc\hosts' -> '/etc/hosts'
'C:\WINDOWS\system32\drivers\etc\protocol' -> '/etc/protocols'
'C:\WINDOWS\system32\drivers\etc\services' -> '/etc/services'
'C:\WINDOWS\system32\drivers\etc\networks' -> '/etc/networks'
gpg: /etc/pacman.d/gnupg/trustdb.gpg: trustdb created
gpg: no ultimately trusted keys found
gpg: starting migration from earlier GnuPG versions
gpg: porting secret keys from '/etc/pacman.d/gnupg/secring.gpg' to gpg-agent
gpg: migration succeeded
==> Generating pacman master key. This may take some time.
gpg: Generating pacman keyring master key...
gpg: directory '/etc/pacman.d/gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/etc/pacman.d/gnupg/openpgp-revocs.d/081969A52947F4608F74818CA60300B7F4FDAA0F.rev'
gpg: Done
==> Updating trust database...
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
==> Appending keys from msys2.gpg...
==> Locally signing trusted keys in keyring...
  -> Locally signed 5 keys.
==> Importing owner trust values...
gpg: setting ownertrust to 4
gpg: setting ownertrust to 4
gpg: setting ownertrust to 4
gpg: setting ownertrust to 4
gpg: setting ownertrust to 4
==> Disabling revoked keys in keyring...
  -> Disabled 4 keys.
==> Updating trust database...
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   5  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: depth: 1  valid:   5  signed:   7  trust: 0-, 0q, 0n, 5m, 0f, 0u
gpg: depth: 2  valid:   4  signed:   2  trust: 4-, 0q, 0n, 0m, 0f, 0u
gpg: next trustdb check due at 2024-04-10
gpg: error retrieving 'alexey.pawlow@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: F40D263ECA25678A
gpg: error retrieving 'david.macek.0@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 790AE56A1D3CFDDC
gpg: error retrieving 'martellmalone@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: DA7EF2ABAEEA755C
gpg: error retrieving 'reiter.christoph@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 755B8182ACD22879
gpg: error retrieving 'icquinteiro@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 9F418C233E652008
gpg: error retrieving 'mingw.android@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: BBE514E53E0D0813
gpg: error retrieving 'alexpux@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 5F92EFC1A47D45A1
gpg: error retrieving 'david.macek.0@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 974C8BE49078F532
gpg: error retrieving 'reiter.christoph@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: FA11531AA0AA7F57
gpg: error retrieving 'me@martellmalone.com' via WKD: No name
gpg: error reading key: No name
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 794DCF97F93FC717
gpg: error retrieving 'martellmalone@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: D595C9AB2C51581E
gpg: error retrieving 'mingw.android@gmail.com' via WKD: Permission denied
gpg: error reading key: Permission denied
gpg: refreshing 1 key from hkps://keyserver.ubuntu.com
gpg: keyserver refresh failed: Permission denied
==> ERROR: Could not update key: 4DF3B7664CA56930
Initial setup complete. MSYS2 is now ready to use.

gsovetov@DESKTOP-TEB080J MINGW64 ~
$
```

Otherwise, these errors appear later:
```
warning: Public keyring not found; have you run 'pacman-key --init'?
error: clangarm64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: mingw32: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: mingw64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: ucrt64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: clang32: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: clang64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: msys: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
```
```
error: clangarm64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: mingw32: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: mingw64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: ucrt64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: clang32: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: clang64: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: msys: key "5F944B027F7FE2091985AA2EFA11531AA0AA7F57" is unknown
error: keyring is not writable
error: failed to synchronize all databases (invalid or corrupted database (PGP signature))
[ Error ] [GHCup-00841] Process "pacman" with arguments ["--noconfirm",
[ ...   ]                                  "-Syuu"] failed with exit code 1.
```

### Allow one mirror in Firewall

Or this error appears with running `pacman`:
```
error: failed to synchronize all databases (download library error)
```

## GHCup
    - Download from <https://downloads.haskell.org/~ghcup/>
    - For example, <https://downloads.haskell.org/~ghcup/0.1.20.0/x86_64-mingw64-ghcup-0.1.20.0.exe>
    - Or by the URL to the latest version <https://downloads.haskell.org/~ghcup/x86_64-mingw64-ghcup.exe>
## Environment variables from the official instruction
    - `set PATH=%PATH%;c:\Users\gsovetov\Documents\haskell\ghcup\bin`
    - `set GHCUP_INSTALL_BASE_PREFIX=c:\Users\gsovetov\Documents\haskell\ghcup`
    - `set CABAL_DIR=c:\Users\gsovetov\Documents\haskell\cabal`
    - `set GHCUP_MSYS2=c:\Users\gsovetov\Documents\haskell\msys64\msys2.exe`
## Commands from the official instruction
    - `x86_64-mingw64-ghcup-0.1.20.0.exe install ghc --set recommended`
    - `x86_64-mingw64-ghcup-0.1.20.0.exe install cabal latest`
    - `x86_64-mingw64-ghcup-0.1.20.0.exe install stack latest`
    - `x86_64-mingw64-ghcup-0.1.20.0.exe install hls latest`
    - `ghcup\bin\cabal.exe update`

## Configure Cabal

```
extra-include-dirs: C:\Users\gsovetov\Documents\haskell\msys64\mingw64\include
extra-lib-dirs: C:\Users\gsovetov\Documents\haskell\msys64\mingw64\lib
extra-prog-path: C:\Users\gsovetov\Documents\haskell\ghcup\bin, C:\Users\gsovetov\Documents\haskell\cabal\bin, C:\Users\gsovetov\Documents\haskell\msys64\mingw64\bin, C:\Users\gsovetov\Documents\haskell\msys64\usr\bin
```

## Uncomment `MSYS2_PATH_TYPE=inherit`

In `msys64/msys2.ini`, uncomment `MSYS2_PATH_TYPE=inherit`.

In `msys64/msys2_shell.cmd` uncomment (remove `rem`) `set MSYS2_PATH_TYPE=inherit`.

## Run `msys2_shell.cmd`

```
msys64/msys2_shell.cmd
```

Terminal (console) window appears.

## Create project stub with Cabal

```
cabal init
```

Answer many questions and get an empty project.