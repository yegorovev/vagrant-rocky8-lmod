# vagrant-rocky8-lmod

Vagrantbox is based on "bento/rockylinux-8"

## 1. Install Lmod

```bash
dnf -y install epel-release
dnf config-manager --set-enabled powertools
dnf -y install gcc
dnf -y install lua
dnf -y install lua5.1-bitop
dnf -y install lua-devel
dnf -y install lua-json
dnf -y install lua-lpeg
dnf -y install lua-posix
dnf -y install lua-term
dnf -y install tcl
dnf -y install tcl-devel
wget https://github.com/TACC/Lmod/archive/refs/tags/8.7.13.tar.gz
tar xzf 8.7.13.tar.gz
cd Lmod-8.7.13/
./configure --prefix=/opt/apps
make install
ln -s /opt/apps/lmod/lmod/init/profile /etc/profile.d/z00_lmod.sh
ln -s /opt/apps/lmod/lmod/init/cshrc   /etc/profile.d/z00_lmod.csh
```

## 2. Test python 3.10.8
### Before
```bash
[vagrant@localhost ~]$ python3
bash: python3: command not found
[vagrant@localhost ~]$ python
bash: python: command not found
[vagrant@localhost ~]$ sudo -i
[root@localhost ~]# python
bash: python: command not found
[root@localhost ~]# python3
bash: python3: command not found
[root@localhost ~]# dnf -y install python3
...
[vagrant@localhost ~]$ python3
Python 3.6.8 (default, Apr 12 2022, 06:55:39)
[GCC 8.5.0 20210514 (Red Hat 8.5.0-10)] on linux
Type "help", "copyright", "credits" or "license" for more information.
```
### Compile python 3.10.8 (root)

```bash
dnf -y install libffi-devel
dnf -y install openssl
dnf -y install openssl-devel
wget https://www.python.org/ftp/python/3.10.8/Python-3.10.8.tgz
tar xzf Python-3.10.8.tgz
mkdir /opt/apps/python3/3.10.8
cd Python-3.10.8
./configure --enable-optimizations --with-ensurepip=install --prefix=/opt/apps/python3/3.10.8
make
make install
mkdir -p /opt/apps/modulefiles/Linux/python3
vi /opt/apps/modulefiles/Linux/python3/3.10.8.lua
```
#### 3.10.8.lua
```lua
-- -*- lua -*-
version = "3.10.8"
prefix = "/opt/apps/python3/" .. version
whatis("Python Programming Language")
help([[
This module provides the Python 3 programming language. Version 3.10.8
]])
prepend_path("PATH",            prefix .. "/bin")
prepend_path("LD_LIBRARY_PATH", prefix .. "/lib")
prepend_path("MANPATH",         prefix .. "/share/man")
```

### After
```bash
[vagrant@localhost ~]$ python3
Python 3.6.8 (default, Apr 12 2022, 06:55:39)
[GCC 8.5.0 20210514 (Red Hat 8.5.0-10)] on linux
Type "help", "copyright", "credits" or "license" for more information.

[vagrant@localhost ~]$ ml load python3/3.10.8
[vagrant@localhost ~]$ python3
Python 3.10.8 (main, Oct 16 2022, 12:26:01) [GCC 8.5.0 20210514 (Red Hat 8.5.0-10)] on linux
Type "help", "copyright", "credits" or "license" for more information.

[vagrant@localhost ~]$ ml unload python3/3.10.8
[vagrant@localhost ~]$ python3
Python 3.6.8 (default, Apr 12 2022, 06:55:39)
[GCC 8.5.0 20210514 (Red Hat 8.5.0-10)] on linux
Type "help", "copyright", "credits" or "license" for more information.

```