---
title: Install Python from source on Ubuntu
keywords: python, python3, python3.6, source distribution, ubuntu
description: How to install newest Python from source with complete set of dependencies.
---

# Install Python from source on Ubuntu

Basic way to build is download sources and run `./configure && make`.

There may emerge some problems and questions though:

- Python is built without some modules, e.g. tkinter and sqlite3.
- Where do you place source code?
- Do you need to run `make install`?

Python 3.6.1 will be used as an example.

## Dependencies for Tkinter, sqlite3 and others

```
sudo apt-get install tk8.6-dev  # For Tkinter. (tcl8.6-dev is dependency).
sudo apt-get install libsqlite3-dev  # For sqlite3.
```

There are another modules, e.g. dbm and gdbm, for which other dependencies must be installed. 

## Install to `/usr/local/`

Install to `/usr/local/` as prescribed at https://refspecs.linuxfoundation.org/FHS_3.0/fhs/ch04s09.html.

Don't nest it to avoid complicated directories structure.

Use same name of directory as archive: less commands, easier to find it later.

```
wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tar.xz
sudo tar -xvf Python-3.6.1.tar.xz -C /usr/local/
```

## Don't install if Python will be used from venv (virtual environment)

It's best practice to use virtual environment. (No third-party packages in Python `site-packages`, no version conflicts.)

Don't meddle with system Python interpreters, other software may rely on particular version of system Python. For everyday scripting, system Python is usually enough.

To create virtual environment, use absolute path to Python:

```
/usr/local/Python3.6.1/bin/python -m venv path/to/env
```

### Installing uWSGI and other binary packages

uWSGI installation into freshly created virtual environment may end up with following error:

```
gcc: error: /home/george/ll/env/lib/python3.6/config-3.6m/libpython3.6.a: No such file or directory
```

Root cause of this problem is unclear.

It can be solved by creating a symbolic to static Python lib:

```
mkdir -p /home/george/ll/env/lib/python3.6/config-3.6m
ln -s /usr/local/Python-3.6.1/libpython3.6m.a /home/george/ll/env/lib/python3.6/config-3.6m/libpython3.6.a
```
