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

## 3. EasyBuild

1. Installing EasyBuild with EasyBuild.

See [Installing EasyBuild with EasyBuild](https://docs.easybuild.io/en/latest/Installation.html#installing-easybuild-with-easybuild "Installing EasyBuild with EasyBuild")

2. Use python 3.9.15

python 3.10 does not work.

*The Tcl-based or Lmod implementations of environment modules do their default sorting differently. The former will normally sort in the lexicographic order, while Lmod follows an approach that is closer to Python’s construct LooseVersion way of ordering. Such aspects may make a big difference, if you have installed both versions 1.9.0 and 1.15.2, with respect to what is the version being loaded by default.*
```bash
eb --install-latest-eb-release --prefix /opt/apps/easybuild
...
Badabum!
sanity check command eb --version exited with code 1 (output: ERROR: No compatible 'python' command found via $PATH (EasyBuild requires Python 2.6+ or 3.5+)
```

### Install python 3.9.15 module
See the previous section. Replace 3.10.8 to 3.9.15

### Step 1: Installing EasyBuild into a temporary location
```bash
ml load python3/3.9.15
export EB_TMPDIR=/tmp/$USER/eb_tmp
python3 -m pip install --ignore-installed --prefix $EB_TMPDIR easybuild
export PATH=$EB_TMPDIR/bin:$PATH
export PYTHONPATH=$(/bin/ls -rtd -1 $EB_TMPDIR/lib*/python*/site-packages | tail -1):$PYTHONPATH
export EB_PYTHON=python3
```

### Step 2: Using EasyBuild to install EasyBuild
```bash
eb --install-latest-eb-release --prefix /opt/apps/easybuild
```

### Step 3: Loading the EasyBuild module
```bash
module use /opt/apps/easybuild/modules/all
ml avail
ml load EasyBuild
eb --version
This is EasyBuild 4.6.1 (framework: 4.6.1, easyblocks: 4.6.1) on host localhost.localdomain.
```
