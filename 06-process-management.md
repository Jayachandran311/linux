# Linux — Process Management

## What is a Process?

A **process** is a running instance of a program. Every process has:
- **PID** — Process ID (unique number)
- **PPID** — Parent Process ID
- **UID** — User that owns it
- **State** — Running, Sleeping, Stopped, Zombie

Process hierarchy: all processes descend from `init`/`systemd` (PID 1).

---

## Viewing Processes

### `ps` — Process Snapshot

```bash
ps               # your processes in current terminal
ps aux           # all processes (a=all users, u=user format, x=no tty)
ps aux | grep nginx           # find nginx processes
ps -ef           # full listing (PPID visible)
ps -u alice      # processes owned by alice
ps --sort=-%cpu  # sort by CPU usage
ps --sort=-%mem  # sort by memory usage
ps -p 1234       # info about specific PID

# BSD style (very common):
# a = show all users' processes
# u = user-oriented format
# x = include processes without a tty
```

### `top` — Dynamic Process Monitor

```bash
top              # live process view (updates every few seconds)

# Inside top:
# q      → quit
# k      → kill a process (enter PID)
# r      → renice (change priority)
# M      → sort by memory
# P      → sort by CPU
# 1      → show per-CPU stats
# u      → filter by user
# Shift+H → show threads
```

### `htop` — Enhanced interactive monitor (install separately)

```bash
apt install htop   # install
htop               # run

# Better UI: shows CPU bars, has mouse support, easier to kill/renice
```

---

## Process States

| State | Symbol | Meaning |
|-------|--------|---------|
| Running | `R` | Active on CPU or ready to run |
| Sleeping | `S` | Waiting for event (interruptible) |
| Disk sleep | `D` | Waiting for I/O (uninterruptible) |
| Stopped | `T` | Paused (Ctrl+Z) |
| Zombie | `Z` | Finished but parent hasn't read exit status |

---

## `kill` — Send Signals to Processes

```bash
kill PID              # send SIGTERM (15) — graceful shutdown
kill -9 PID           # send SIGKILL  — force kill (cannot be caught)
kill -TERM PID        # same as kill PID
kill -HUP PID         # SIGHUP — reload config (web servers, daemons)
kill -USR1 PID        # SIGUSR1 — user-defined signal
kill -0 PID           # check if PID exists (no signal sent)

killall nginx         # kill all processes named 'nginx'
killall -9 python3    # force-kill all python3 processes

pkill nginx           # kill by name pattern
pkill -u alice        # kill all of alice's processes
pkill -f "python script.py"  # kill by full command-line match
```

### Common Signals

| Signal | Number | Meaning |
|--------|--------|---------|
| `SIGHUP` | 1 | Hangup / reload config |
| `SIGINT` | 2 | Interrupt (Ctrl+C) |
| `SIGQUIT` | 3 | Quit (Ctrl+\) |
| `SIGKILL`| 9 | Force kill (cannot ignore) |
| `SIGTERM`| 15 | Graceful terminate (default kill) |
| `SIGSTOP`| 19 | Stop/pause (cannot ignore) |
| `SIGCONT`| 18 | Continue (after SIGSTOP) |

---

## Job Control

```bash
./long_script.sh &     # run in background
jobs                   # list background jobs
jobs -l                # with PIDs

fg %1                  # bring job 1 to foreground
bg %2                  # resume stopped job 2 in background

# Ctrl+Z               # suspend foreground process
# Ctrl+C               # kill foreground process

# Run and keep going after terminal closes:
nohup ./script.sh &
nohup ./script.sh > output.log 2>&1 &
disown $!              # detach from terminal
```

---

## Process Priority: `nice` and `renice`

Priority range: **-20** (highest) to **19** (lowest). Default: **0**.  
Only root can set negative (higher priority) values.

```bash
nice -n 10 ./backup.sh       # start with lower priority (nice = 10)
nice -n -5 ./critical.sh     # start with higher priority (root only)

renice 15 -p 1234            # lower priority of running process
renice -5 -p 1234            # raise priority (root only)
renice 10 -u alice           # change priority of all alice's processes
```

---

## `systemd` — Service Manager

Modern Linux uses `systemd` to manage services (daemons).

```bash
# Start / Stop / Restart
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx    # reload config without stopping

# Enable / Disable (auto-start on boot)
systemctl enable nginx
systemctl disable nginx
systemctl enable --now nginx   # enable AND start immediately

# Status
systemctl status nginx        # detailed status + recent logs
systemctl is-active nginx     # outputs: active / inactive
systemctl is-enabled nginx    # outputs: enabled / disabled
systemctl is-failed nginx

# List services
systemctl list-units --type=service
systemctl list-units --type=service --state=running
systemctl list-unit-files --type=service

# System control
systemctl reboot
systemctl poweroff
systemctl suspend

# Reload systemd after editing unit files
systemctl daemon-reload
```

---

## Viewing Service Logs with `journald`

```bash
journalctl                          # all logs
journalctl -u nginx                 # logs for nginx service
journalctl -u nginx -f              # follow (live)
journalctl -u nginx --since today
journalctl -u nginx --since "2024-03-01" --until "2024-03-31"
journalctl -n 50                    # last 50 lines
journalctl -n 50 -u nginx           # last 50 lines for nginx
journalctl -p err                   # errors only (levels: debug,info,notice,warning,err,crit,alert,emerg)
journalctl --disk-usage             # show log disk usage
journalctl --vacuum-size=500M       # limit journal to 500MB
```

---

## `cron` — Scheduled Tasks

```bash
crontab -l          # list your cron jobs
crontab -e          # edit your cron jobs
crontab -r          # remove all cron jobs
crontab -u alice -l # list alice's cron jobs (root)

# Cron format:
# ┌───────────── minute (0-59)
# │ ┌─────────── hour (0-23)
# │ │ ┌───────── day of month (1-31)
# │ │ │ ┌─────── month (1-12)
# │ │ │ │ ┌───── day of week (0-7, 0 and 7 are Sunday)
# │ │ │ │ │
# * * * * * command

# Examples:
* * * * *   /path/to/script.sh    # every minute
0 * * * *   /path/to/script.sh    # every hour
0 2 * * *   /path/to/backup.sh    # daily at 2:00 AM
30 4 * * 0  /path/to/weekly.sh    # every Sunday at 4:30 AM
0 0 1 * *   /path/to/monthly.sh   # first of every month at midnight
*/15 * * * * /path/to/check.sh    # every 15 minutes
0 9-18 * * 1-5 /path/to/job.sh   # every hour 9-18, Mon-Fri
```

### System-wide cron directories

```bash
/etc/cron.d/          # drop cron files here
/etc/cron.daily/      # scripts run daily
/etc/cron.hourly/     # scripts run hourly
/etc/cron.weekly/     # scripts run weekly
/etc/cron.monthly/    # scripts run monthly
```

---

## Practical Examples

```bash
# Find and kill a specific process
PID=$(pgrep -f "node server.js")
kill $PID

# Monitor a process until it dies
watch -n 1 "ps aux | grep myapp | grep -v grep"

# Start a process and monitor it
while true; do
    ./server &
    SERVER_PID=$!
    wait $SERVER_PID
    echo "Server crashed, restarting in 5s..."
    sleep 5
done
```

---

## Key Takeaways

> - `ps aux` shows all processes; `top` / `htop` for interactive monitoring
> - `kill PID` = graceful; `kill -9 PID` = force kill
> - `systemctl start/stop/restart/status service` manages services
> - `journalctl -u service -f` follows service logs live
> - `crontab -e` to schedule recurring tasks
> - `nice`/`renice` adjusts CPU priority; lower nice value = higher priority
