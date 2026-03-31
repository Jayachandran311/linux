# Linux — Basic Commands

## Navigation

```bash
pwd                  # Print Working Directory — where are you?
ls                   # list files
ls -l                # long format (permissions, owner, size, date)
ls -la               # include hidden files (starting with .)
ls -lh               # human-readable sizes (KB, MB)
ls -lt               # sort by modification time (newest first)
ls -lS               # sort by file size (largest first)
ls -R                # recursive listing

cd /etc              # go to /etc (absolute)
cd logs              # go to logs/ in current directory (relative)
cd ~                 # go home
cd -                 # go back to previous directory
cd ..                # go up one level
cd ../..             # go up two levels
cd /var/log/nginx    # go directly to nested path
```

---

## Viewing Files

```bash
cat file.txt              # print entire file
cat -n file.txt           # with line numbers
cat file1.txt file2.txt   # concatenate and print both

less file.txt             # pager — scroll up/down, press q to quit
more file.txt             # older pager — scroll down only
head file.txt             # first 10 lines
head -n 20 file.txt       # first 20 lines
tail file.txt             # last 10 lines
tail -n 50 file.txt       # last 50 lines
tail -f /var/log/syslog   # follow — live updates (great for logs)
tail -F /var/log/syslog   # follow and reopen if rotated

# Inside `less`:
# Space / PgDn   — page down
# b / PgUp       — page back
# /pattern       — search forward
# n / N          — next/prev match
# g              — go to top
# G              — go to bottom
# q              — quit
```

---

## Creating Files and Directories

```bash
touch newfile.txt          # create empty file (or update timestamp)
touch file1 file2 file3    # create multiple files

mkdir mydir                # create directory
mkdir -p a/b/c/d           # create nested (all parents)
mkdir -p project/{src,docs,tests,bin}   # create multiple subdirs

# Create files with content
echo "Hello" > file.txt
cat > config.txt << EOF
key=value
setting=true
EOF
```

---

## Copying Files

```bash
cp source.txt dest.txt           # copy file
cp source.txt /tmp/              # copy to directory (keeps filename)
cp -r srcdir/ destdir/           # copy directory recursively
cp -p file.txt backup/           # preserve permissions + timestamps
cp -u *.sh scripts/              # copy only if newer (update)
cp -i file.txt /tmp/             # interactive — ask before overwrite
cp -v file.txt /tmp/             # verbose — show what's being copied
```

---

## Moving and Renaming

```bash
mv file.txt newname.txt          # rename file
mv file.txt /tmp/                # move file to directory
mv *.log /var/log/archive/       # move multiple files
mv -i file.txt /tmp/             # ask before overwrite
mv -n file.txt /tmp/             # no overwrite (never replace existing)
mv -v file.txt /tmp/             # verbose

# Rename multiple files (bash loop)
for f in *.txt; do mv "$f" "${f%.txt}.md"; done
```

---

## Deleting Files

```bash
rm file.txt                  # delete file
rm -i file.txt               # interactive — confirm each file
rm -f file.txt               # force (no error if doesn't exist)
rm file1 file2 file3         # delete multiple
rm *.log                     # delete all .log files
rm -r directory/             # delete directory and all contents
rm -rf directory/            # force-delete (no prompts)

rmdir emptydir/              # delete empty directory only
```

> ⚠️ `rm -rf` is irreversible. Always double-check the path before running.

---

## Searching Files

### `find`

```bash
find / -name "hosts"                  # find by name (case sensitive)
find / -iname "hosts"                 # case-insensitive
find . -name "*.sh"                   # by extension
find . -type f -name "*.log"          # files only
find . -type d                        # directories only
find . -size +10M                     # larger than 10MB
find . -mtime -7                      # modified in last 7 days
find . -user alice                    # owned by alice
find . -perm 644                      # with exact permissions
find . -name "*.tmp" -delete          # delete found files
find . -name "*.sh" -exec chmod +x {} \;   # set executable

# find and process with xargs (faster)
find . -name "*.log" | xargs grep "ERROR"
find . -type f -name "*.py" | xargs wc -l
```

### `locate` (fast, uses database)

```bash
locate passwd                  # quick search by filename
locate -i passwd               # case-insensitive
sudo updatedb                  # update the database (run as root)
```

### `which` / `whereis` / `type`

```bash
which python3           # /usr/bin/python3
which -a python3        # all locations in PATH
whereis python3         # binary + man pages + source
type ls                 # ls is aliased to 'ls --color=auto'
command -v ls           # used in scripts to check if command exists
```

---

## File Information

```bash
file /bin/ls              # determine file type
stat file.txt             # full info: size, inode, timestamps, permissions
wc file.txt               # word count: lines, words, bytes
wc -l file.txt            # line count only
wc -w file.txt            # word count only
wc -c file.txt            # byte count
ls -lh file.txt           # size in human-readable
du -sh directory/         # directory size
```

---

## Comparing Files

```bash
diff file1.txt file2.txt            # line-by-line differences
diff -u file1.txt file2.txt         # unified format (like git diff)
diff -r dir1/ dir2/                 # compare directories
vimdiff file1.txt file2.txt         # visual side-by-side diff

cmp file1 file2              # byte-by-byte comparison (binary)
md5sum file.iso              # MD5 checksum
sha256sum file.tar.gz        # SHA256 checksum
```

---

## Text Search in Files

```bash
grep "error" logfile.log            # search in file
grep -i "error" logfile.log         # case-insensitive
grep -r "TODO" src/                 # recursive search in directory
grep -n "function" script.sh        # show line numbers
grep -l "pattern" *.py              # list files that match
grep -v "DEBUG" app.log             # exclude matching lines
grep -c "ERROR" app.log             # count matching lines
grep -A 3 "Exception" app.log       # show 3 lines AFTER each match
grep -B 2 "Exception" app.log       # show 2 lines BEFORE each match
grep -E "error|warning|critical" app.log  # regex with alternation
```

---

## Viewing Disk and System Info

```bash
df -h                    # disk free (all mounts)
df -h /                  # disk free on root partition
du -sh /var              # disk usage of /var
du -sh * | sort -h       # sorted size of current dir contents
free -h                  # RAM usage (human-readable)
lscpu                    # CPU info
uname -a                 # kernel version and architecture
cat /etc/os-release      # OS version
lsb_release -a           # distro info (Ubuntu/Debian)
```

---

## Keyboard Shortcuts in Terminal

| Shortcut | Action |
|---------|--------|
| `Ctrl+C` | Kill running command |
| `Ctrl+D` | EOF / log out |
| `Ctrl+Z` | Suspend process |
| `Ctrl+L` | Clear screen |
| `Ctrl+A` | Go to start of line |
| `Ctrl+E` | Go to end of line |
| `Ctrl+U` | Delete to start of line |
| `Ctrl+K` | Delete to end of line |
| `Ctrl+W` | Delete previous word |
| `Ctrl+R` | Search history |
| `Alt+.` | Paste last argument |
| `Tab` | Autocomplete |
| `!!` | Repeat last command |
| `!ls` | Repeat last command starting with `ls` |

---

## Key Takeaways

> - `ls -la` and `pwd` are your orientation commands
> - Use `less` instead of `cat` for large files
> - `find . -name "*.sh"` is powerful for locating files
> - `grep -r "pattern" dir/` searches all files in a directory
> - `Ctrl+R` to search command history is a huge time saver
> - `tail -f` is essential for monitoring live logs
