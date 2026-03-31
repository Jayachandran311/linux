# Linux — File System

## Filesystem Hierarchy Standard (FHS)

Linux uses a single unified directory tree starting from `/` (root). All drives, devices, and partitions are mounted somewhere in this tree.

```
/
├── bin/        → Essential user commands (ls, cp, bash)
├── sbin/       → System administration commands (fsck, ip, reboot)
├── etc/        → Configuration files (system-wide)
├── home/       → User home directories (/home/alice, /home/bob)
├── root/       → Home directory of root user
├── var/        → Variable data (logs, spools, cache)
│   ├── log/    → Log files
│   ├── www/    → Web server content
│   └── spool/  → Printing and mail queues
├── tmp/        → Temporary files (cleared on reboot)
├── usr/        → User programs and data (read-only)
│   ├── bin/    → Non-essential commands
│   ├── sbin/   → Non-essential admin commands
│   ├── lib/    → Libraries
│   └── local/  → Locally installed software
├── lib/        → Shared libraries (.so files)
├── dev/        → Device files (hard drives, terminals)
├── proc/       → Virtual filesystem — kernel and process info
├── sys/        → Virtual filesystem — kernel hardware info
├── mnt/        → Temporary mount points
├── media/      → Removable media (USB, CD)
├── opt/        → Optional/third-party software
├── run/        → Runtime data (PIDs, sockets)
└── boot/       → Boot loader files, kernel images
```

---

## Important Directories

### `/etc` — Configuration
```bash
/etc/passwd       # user account info
/etc/shadow       # encrypted passwords
/etc/group        # group definitions
/etc/hosts        # local hostname/IP mappings
/etc/resolv.conf  # DNS servers
/etc/hostname     # system hostname
/etc/fstab        # filesystem mount table
/etc/crontab      # system cron jobs
/etc/ssh/         # SSH configuration
/etc/nginx/       # nginx web server config
/etc/apt/         # apt package manager config
```

### `/proc` — Process and Kernel Info (virtual)
```bash
/proc/cpuinfo     # CPU information
/proc/meminfo     # memory usage
/proc/version     # kernel version
/proc/uptime      # system uptime
/proc/PID/        # info about process with that PID
/proc/PID/exe     # symlink to executable
/proc/PID/fd/     # open file descriptors
/proc/net/        # network stats
```

### `/var` — Variable Data
```bash
/var/log/syslog          # general system logs
/var/log/auth.log        # authentication logs
/var/log/nginx/          # nginx logs
/var/log/mysql/          # mysql logs
/var/spool/cron/         # user cron jobs
/var/www/html/           # default web root
```

---

## Paths

```bash
# Absolute path — starts from root /
/home/alice/documents/file.txt

# Relative path — relative to current directory
documents/file.txt       # from /home/alice/
./documents/file.txt     # same, explicit current dir
../bob/file.txt          # go up one, then to bob/

# Special path shortcuts
~                        # your home directory ($HOME)
~alice                   # alice's home directory
.                        # current directory
..                       # parent directory
-                        # previous directory (with cd)
```

---

## File Types

```bash
ls -la    # first character shows file type
```

| Symbol | Type |
|--------|------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device (terminal, keyboard) |
| `b` | Block device (hard drive, USB) |
| `p` | Named pipe (FIFO) |
| `s` | Socket |

```bash
# Identify file type
file /etc/passwd          # ASCII text
file /bin/ls              # ELF 64-bit LSB executable
file /dev/sda             # block special file
file /tmp/mysocket        # socket
```

---

## Symbolic Links vs Hard Links

```bash
# Symbolic link (symlink) — pointer to a path
ln -s /path/to/original linkname
ln -s /var/www/html /home/web   # create shortcut

# Hard link — another name for the same file data (inode)
ln original.txt hardlink.txt

# View links
ls -la              # → shows target of symlinks
readlink symlink    # shows what symlink points to
readlink -f symlink # shows resolved absolute path
```

| | Symbolic Link | Hard Link |
|--|--------------|-----------|
| Can link directories | Yes | No |
| Works across filesystems | Yes | No |
| Survives original deletion | No | Yes |
| Shown in `ls -la` | Yes (`->`) | No (same as file) |

---

## Disk and Storage

```bash
df -h              # disk free — show mounted filesystems + usage
df -h /home        # just the /home filesystem
du -sh /var/log    # disk usage of a directory
du -sh *           # size of each item in current dir
du -sh /* 2>/dev/null | sort -h   # largest directories

lsblk              # list block devices (drives, partitions)
blkid              # show UUIDs of partitions
fdisk -l           # partition table (requires root)
```

---

## Mounting Filesystems

```bash
# List currently mounted filesystems
mount
cat /proc/mounts

# Mount a partition
mount /dev/sdb1 /mnt/data

# Mount USB drive (auto-detected label)
mount /dev/sdc1 /media/usb

# Unmount
umount /mnt/data
umount /dev/sdb1

# Mount at boot — /etc/fstab
# device   mountpoint   fstype   options   dump   pass
/dev/sda1   /            ext4    defaults    0      1
UUID=xxx    /home        ext4    defaults    0      2
tmpfs       /tmp         tmpfs   defaults    0      0
```

---

## Key Takeaways

> - Linux has one unified tree from `/` — everything is mounted here
> - `/etc` = config, `/var/log` = logs, `/home` = users, `/proc` = kernel info
> - Absolute paths start with `/`; relative paths start from current directory
> - `~` is your home directory
> - `df -h` shows disk usage per filesystem; `du -sh dir/` shows directory size
> - Symlinks are pointers to paths; hard links are alternate names for the same data
