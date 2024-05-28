# secedit - minimal example

Change local policies on Windows 11 Home and Windows 10 Home without third-party tools.

The GUI app `secpol.msc` is not included in in Home.

Powershell cmdlets are not included in Home either.

## Principle

Prepare a database file and apply settings from it to the system.

Type settings in a config file. Then convert it to a database file.

## Basic command sequence

### 1. Create a config file

A minimal config file (stored as `new2.inf`):
```
[Privilege Rights]
SeBatchLogonRight = *S-1-5-32-544,*S-1-5-32-551,*S-1-5-32-559,*S-1-5-21-1938875136-3881193919-344594422-1002
[Version]
signature="$CHICAGO$"
```

In this example, `,*S-1-5-21-1938875136-3881193919-344594422-1002` was added. The goal is to give the `SeBatchLogonRight` priviledge to a specific user.

If the `Version` section or the `signature` are removed, a database file is not created.

Get such file with all actual settings from the system:

```
secedit /export /cfg all-from-system.inf
```

### 2. Convert it to a database file

```
secedit /import /db new2.db /cfg new2.inf
```

### 3. Apply settings from the database to the system

```
secedit /configure /db new2.db
```

### 4. Validate system settings

```
secedit /export /cfg all-from-system-2.inf
fc /u all-from-system.inf all-from-system-2.inf
```

See the result of the comparison command.