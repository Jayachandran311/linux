# Linux — System Monitoring

## CPU and System Info

```bash
uname -a                  # kernel version, arch, hostname
uname -r                  # kernel version only
uname -m                  # architecture (x86_64, arm64)

cat /etc/os-release       # OS name and version
lsb_release -a            # distro info (Ubuntu/Debian)
hostnamectl               # hostname, OS, kernel (systemd)
```

### CPU Info

```bash
lscpu                     # CPU architecture, cores, threads, speed
cat /proc/cpuinfo         # raw CPU info (per-core details)
nproc                     # number of processing units available

# Get core count
nproc
grep -c ^processor /proc/cpuinfo
```

---

## Memory Monitoring

```bash
free -h                   # RAM and swap (human-readable)
free -m                   # in MB
free -s 2                 # refresh every 2 seconds

cat /proc/meminfo         # detailed memory info

# Memory breakdown
vmstat 1                  # virtual memory stats (update every 1s)
vmstat -s                 # summary

# Top memory-consuming processes
ps aux --sort=-%mem | head -10
```

Output of `free -h`:
```
              total   used   free   shared  buf/cache   available
Mem:           7.7G   2.1G   3.2G    234M      2.4G       5.1G
Swap:          2.0G    12M   1.9G
```

- **used** = actively used RAM
- **buf/cache** = used for caching (can be freed)
- **available** = actual available RAM (more useful than "free")

---

## Disk Monitoring

```bash
df -h                         # disk free (all mounted filesystems)
df -h /                       # root filesystem only
df -i                         # inode usage

# Directory sizes
du -sh /var/log               # total size of /var/log
du -sh *                      # size of each item in current dir
du -sh /* 2>/dev/null | sort -h   # sorted sizes under /
du -sh /var/log/* | sort -h       # sorted log directory sizes

# Find large files
find / -type f -size +100M 2>/dev/null
find /var/log -name "*.log" -size +50M
```

---

## CPU Load Monitoring

```bash
uptime                        # load averages (1m, 5m, 15m)
# 14:32:01 up 5 days, 2:10, 2 users, load average: 0.52, 0.48, 0.45

cat /proc/loadavg             # raw load data

# Load average meaning:
# On a single-core system: load > 1 means overloaded
# On 4-core system: load > 4 means overloaded
# Rule of thumb: load / cores should be < 1.0

top                           # real-time CPU usage
htop                          # better interactive monitor
mpstat 1                      # per-CPU stats (needs sysstat)
iostat 1                      # CPU + I/O stats
```

---

## I/O Monitoring

```bash
iostat                        # disk I/O stats and CPU
iostat -x 1                   # extended stats, 1s refresh
iostat -x -d sda              # specific disk

iotop                         # top-like I/O monitor (root needed, install separate)
iotop -o                      # only show processes doing I/O

# Disk latency and speed test
hdparm -t /dev/sda            # read speed test
dd if=/dev/zero of=/tmp/test bs=1M count=1000 oflag=direct   # write test
```

---

## Network Monitoring

```bash
ifstat                        # network interface statistics
iftop                         # top-like per-connection bandwidth
nethogs                       # per-process bandwidth

ss -s                         # socket summary statistics
ip -s link                    # RX/TX bytes per interface
cat /proc/net/dev              # raw network counters

# Watch bandwidth in real-time
watch -n 1 "ip -s link show eth0"

# Check established connections count
ss -t state established | wc -l
```

---

## System Logs

```bash
# systemd journal (modern)
journalctl                            # all logs
journalctl -f                         # follow (live)
journalctl -n 100                     # last 100 lines
journalctl -u nginx                   # logs for specific service
journalctl -u nginx -f                # follow nginx logs
journalctl --since "1 hour ago"
journalctl --since "2024-03-01" --until "2024-03-31"
journalctl -p err                     # errors only
journalctl -p "err..emerg"            # error and above
journalctl -b                         # this boot only
journalctl -b -1                      # previous boot logs
journalctl --disk-usage               # journal size on disk

# Traditional log files
tail -f /var/log/syslog               # general system log
tail -f /var/log/auth.log             # authentication events
tail -f /var/log/kern.log             # kernel messages
tail -f /var/log/nginx/access.log     # nginx access log
tail -f /var/log/nginx/error.log      # nginx errors

# dmesg — kernel ring buffer
dmesg                                 # all kernel messages
dmesg | tail                          # recent kernel messages
dmesg -T                              # with human-readable timestamps
dmesg -w                              # watch (follow)
dmesg --level=err,crit,alert,emerg    # errors only
```

---

## Monitoring Tools Overview

### `top` — Interactive Process Monitor

```
top - 14:32:01 up 5 days,  2:10,  2 users,  load average: 0.52, 0.48, 0.45
Tasks: 135 total,   1 running, 134 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.3 us,  1.2 sy,  0.0 ni, 92.8 id,  0.5 wa,  0.0 hi,  0.2 si
MiB Mem:   7892.5 total,   3241.1 free,   2187.3 used,   2464.1 buff/cache
MiB Swap:  2048.0 total,   2035.9 free,     12.1 used.   5234.1 avail Mem
```

Key fields:
- **us** = user CPU, **sy** = system CPU, **id** = idle, **wa** = I/O wait
- **wa** high = disk I/O bottleneck

### `watch` — Repeat a Command

```bash
watch -n 2 df -h              # show disk usage every 2 seconds
watch -n 1 "ss -tuln | wc -l" # count connections every second
watch -d free -h               # highlight changes (-d = diff)
```

---

## Performance Baselines

```bash
# Comprehensive performance snapshot
echo "=== CPU ===" && lscpu | grep -E "^CPU\(s\)|Model name|MHz"
echo "=== Memory ===" && free -h
echo "=== Disk ===" && df -h
echo "=== Load ===" && uptime
echo "=== Top Processes ===" && ps aux --sort=-%cpu | head -6
echo "=== Network ===" && ss -s
```

---

## Alerting with Scripts

```bash
#!/usr/bin/env bash
# Simple resource monitor

THRESHOLD=80

# CPU load
load=$(awk '{print $1}' /proc/loadavg)
cores=$(nproc)
load_pct=$(echo "$load $cores" | awk '{printf "%.0f", ($1/$2)*100}')
[ "$load_pct" -gt "$THRESHOLD" ] && echo "ALERT: CPU load ${load_pct}%"

# Memory
mem_pct=$(free | awk '/Mem:/ {printf "%.0f", ($3/$2)*100}')
[ "$mem_pct" -gt "$THRESHOLD" ] && echo "ALERT: Memory usage ${mem_pct}%"

# Disk
disk_pct=$(df / | awk 'NR==2 {print $5}' | tr -d '%')
[ "$disk_pct" -gt "$THRESHOLD" ] && echo "ALERT: Disk usage ${disk_pct}%"

echo "Health check complete"
```

---

## Key Takeaways

> - `free -h` for memory; `df -h` for disk; `uptime` for load averages
> - Load average / CPU cores < 1.0 = healthy
> - `journalctl -f -u nginx` to follow service logs in real-time
> - `dmesg -T` for kernel-level hardware/driver issues
> - `ps aux --sort=-%mem | head` for memory hogs
> - `iostat -x 1` for disk I/O bottlenecks
