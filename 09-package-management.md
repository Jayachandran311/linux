# Linux — Package Management

## Overview

Different Linux distributions use different package managers:

| Distro Family | Package Manager | Config |
|--------------|----------------|--------|
| Ubuntu / Debian | `apt` | `/etc/apt/` |
| CentOS / RHEL / Fedora | `yum` / `dnf` | `/etc/yum.repos.d/` |
| Arch Linux | `pacman` | `/etc/pacman.conf` |
| openSUSE | `zypper` | — |

---

## APT — Debian / Ubuntu

### Updating and Upgrading

```bash
apt update                     # refresh package list (always run first)
apt upgrade                    # upgrade all installed packages
apt full-upgrade               # upgrade + handle dependency changes
apt dist-upgrade               # same as full-upgrade (older name)

# Combined (common workflow):
apt update && apt upgrade -y
```

### Installing and Removing

```bash
apt install nginx                        # install a package
apt install nginx curl git vim           # install multiple
apt install -y nginx                     # auto-confirm (no prompt)
apt install --no-install-recommends nginx  # skip recommended packages

apt remove nginx                         # remove package (keep config)
apt purge nginx                          # remove package + config files
apt autoremove                           # remove unused dependencies
apt clean                                # clear downloaded package cache
apt autoclean                            # clear only outdated cache

# Reinstall
apt install --reinstall nginx
```

### Searching and Inspecting

```bash
apt search nginx                   # search available packages
apt show nginx                     # detailed package info
apt list --installed               # list installed packages
apt list --installed | grep nginx  # check if specific package installed
apt list --upgradable              # packages with updates available

dpkg -l                            # list all installed packages (low-level)
dpkg -l nginx                      # check if nginx is installed
dpkg -L nginx                      # list files installed by package
dpkg -S /usr/bin/nginx             # which package owns this file?
```

### Package Sources (repositories)

```bash
cat /etc/apt/sources.list          # main repo list
ls /etc/apt/sources.list.d/        # additional repos

# Add a PPA (Ubuntu)
add-apt-repository ppa:nginx/stable
apt update

# Add repo manually (modern way)
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list
apt update
apt install docker-ce
```

---

## YUM / DNF — RHEL / CentOS / Fedora

`dnf` is the modern replacement for `yum` (same commands mostly):

```bash
dnf update                         # update all packages
dnf install nginx                  # install
dnf remove nginx                   # remove
dnf search nginx                   # search
dnf info nginx                     # package details
dnf list installed                 # list installed
dnf list available                 # list available
dnf check-update                   # check for updates
dnf autoremove                     # remove unused deps
dnf clean all                      # clear cache

# Groups
dnf grouplist                      # list package groups
dnf groupinstall "Development Tools"

# Repos
dnf repolist                       # list enabled repos
dnf config-manager --enable epel   # enable EPEL repo

# RPM (low-level)
rpm -qa                            # list all installed packages
rpm -qi nginx                      # info about installed package
rpm -ql nginx                      # list files in package
rpm -qf /usr/bin/nginx             # which package owns this file
```

---

## Pacman — Arch Linux

```bash
pacman -Syu                       # sync + update all packages
pacman -S nginx                   # install
pacman -R nginx                   # remove
pacman -Rs nginx                  # remove + unused deps
pacman -Ss nginx                  # search
pacman -Si nginx                  # package info
pacman -Q                         # list installed packages
pacman -Qe                        # list explicitly installed
pacman -Ql nginx                  # list package files
pacman -Qo /usr/bin/nginx         # which package owns file
pacman -Sc                        # clean cache
```

---

## Snap — Universal Packages

Runs in a sandbox, universal across distributions:

```bash
snap install code --classic        # install VS Code
snap install docker                # install Docker
snap list                          # list installed snaps
snap remove code                   # remove
snap refresh                       # update all snaps
snap find nginx                    # search
snap info nginx                    # package details
```

---

## Compiling from Source

When a package isn't in any repository:

```bash
# Download and extract source
wget https://example.com/myapp-1.0.tar.gz
tar -xzf myapp-1.0.tar.gz
cd myapp-1.0/

# Build tools (Ubuntu)
apt install build-essential        # gcc, make, etc.

# Standard build process (autotools)
./configure --prefix=/usr/local    # generates Makefile
make                               # compile
make install                       # install to /usr/local

# CMake-based projects
mkdir build && cd build
cmake .. -DCMAKE_INSTALL_PREFIX=/usr/local
make
make install
```

---

## Holding / Pinning Packages (prevent upgrade)

```bash
# apt (Ubuntu/Debian)
apt-mark hold nginx               # prevent nginx from being upgraded
apt-mark unhold nginx             # allow upgrades again
apt-mark showhold                 # list held packages

# dnf/yum
dnf install 'dnf-command(versionlock)'
dnf versionlock add nginx
dnf versionlock delete nginx
dnf versionlock list
```

---

## Useful Package Tricks

```bash
# Check which package provides a command
apt-file search ifconfig          # need: apt install apt-file
dpkg -S $(which nginx)

# List recently installed packages
grep " install " /var/log/dpkg.log | tail -20

# Show installed package size
dpkg-query --show --showformat='${Installed-Size}\t${Package}\n' | sort -n | tail -20

# Install specific version
apt install nginx=1.24.0-1ubuntu1

# Simulate install (dry run)
apt install --dry-run nginx
apt -s install nginx

# Download .deb without installing
apt download nginx

# Install local .deb file
dpkg -i mypackage.deb
apt install -f              # fix any broken dependencies
```

---

## Key Takeaways

> - Always run `apt update` before installing (`apt update && apt install package`)
> - `apt remove` keeps configs; `apt purge` removes configs too
> - `dpkg -S /path/to/file` finds which package owns a file
> - `apt-mark hold package` prevents a package from being upgraded
> - Use distribution repositories when possible — PPAs and manual repos need trust
