# Linux — Introduction

## What is Linux?

**Linux** is a free, open-source Unix-like operating system kernel created by **Linus Torvalds** in 1991. Combined with GNU tools, it forms a complete operating system used everywhere — servers, cloud, phones (Android), embedded devices, and supercomputers.

> **95%+ of the world's servers, 100% of the top 500 supercomputers, and all major cloud platforms run Linux.**

---

## Linux vs Unix vs GNU

| Term | What it means |
|------|--------------|
| **Unix** | Original OS developed at Bell Labs (1970s). Proprietary. Basis for all modern OSes. |
| **Linux** | Just the **kernel** — manages hardware, processes, memory |
| **GNU** | Free software tools (bash, gcc, ls, cp, etc.) |
| **GNU/Linux** | Full OS = Linux kernel + GNU tools + other software |
| **Distribution** | Package of Linux kernel + tools + package manager + desktop |

---

## Popular Linux Distributions

| Distro | Based on | Use case |
|--------|----------|----------|
| **Ubuntu** | Debian | Beginner-friendly, desktop & server |
| **Debian** | — | Stable base, many derivatives |
| **CentOS / Rocky / AlmaLinux** | RHEL | Enterprise servers |
| **RHEL** | — | Enterprise, paid support |
| **Fedora** | RHEL (upstream) | Cutting-edge, developers |
| **Arch Linux** | — | Rolling release, advanced users |
| **Kali Linux** | Debian | Penetration testing / security |
| **Alpine Linux** | — | Minimal, heavily used in Docker |
| **Amazon Linux** | RHEL | AWS cloud |

---

## Linux Architecture

```
┌──────────────────────────────┐
│       User Applications      │  (browsers, editors, scripts)
├──────────────────────────────┤
│       Shell / CLI / GUI      │  (bash, zsh, GNOME, KDE)
├──────────────────────────────┤
│       System Calls           │  (interface between apps and kernel)
├──────────────────────────────┤
│           Kernel             │  (core: process, memory, device mgmt)
├──────────────────────────────┤
│           Hardware           │  (CPU, RAM, Disk, Network)
└──────────────────────────────┘
```

The **kernel** manages:
- **Process management** — create, schedule, terminate processes
- **Memory management** — RAM allocation, virtual memory
- **File system** — read/write files on disk
- **Device drivers** — communicate with hardware
- **Network stack** — TCP/IP networking

---

## Getting Help

```bash
man ls           # manual page for ls
man -k keyword   # search manual pages by keyword
man 5 passwd     # section 5 of passwd (file formats)

ls --help        # most commands have --help
info ls          # GNU info docs (more detailed)

type ls          # show how ls is resolved (alias/builtin/file)
which ls         # show full path of ls binary
whereis ls       # show binary, man page, source locations
```

### `man` page sections

| Section | Content |
|---------|---------|
| 1 | User commands |
| 2 | System calls |
| 3 | C library functions |
| 4 | Device files |
| 5 | File formats |
| 6 | Games |
| 7 | Miscellaneous |
| 8 | System administration commands |

---

## The Terminal

The terminal (also called console, shell, or command line) is where you type commands.

```bash
# Prompt anatomy
alice@ubuntu:~$
# └─ username
#        └─── hostname
#              └── current directory (~ = home)
#                   └ $ = regular user (# = root)

# Shortcut keys
Ctrl+C       # cancel/kill current command
Ctrl+D       # end of input / logout
Ctrl+Z       # suspend current process
Ctrl+L       # clear screen (same as 'clear')
Ctrl+A       # go to beginning of line
Ctrl+E       # go to end of line
Ctrl+R       # search command history
Tab          # autocomplete
↑ / ↓        # navigate command history
```

---

## Essential First Commands

```bash
whoami           # show current username
id               # show UID, GID, and groups
hostname         # show system hostname
uname -a         # full system info (kernel, arch, etc.)

pwd              # print working directory
ls               # list files
cd ~             # go to home directory
cd /             # go to root of filesystem
cd ..            # go up one directory
cd -             # go to previous directory

date             # current date and time
cal              # calendar
uptime           # how long system has been running
w                # who is logged in and what they're doing
history          # list of recent commands
```

---

## Linux Philosophy

> - **Everything is a file** — devices, processes, and sockets are all accessed like files
> - **Small tools, each doing one thing well** — compose them with pipes
> - **Text is the universal interface** — configuration, logs, and data are plain text
> - **Open source** — you can read, modify, and distribute the code

---

## Key Takeaways

> - Linux = kernel; Ubuntu/CentOS/etc. = full distributions
> - Most servers run Linux — mastering it is essential for DevOps
> - Use `man command` and `command --help` for help
> - The terminal is your primary tool — get comfortable with it
