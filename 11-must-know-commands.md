# 9 Powerful Linux Tools Every Beginner Should Know

These are modern command-line tools that make your Linux life much easier. They are not built into Linux by default — you need to install them. But once you do, you will wonder how you ever survived without them!

> **Think of it this way:** These tools are like upgraded versions of the basic Linux commands you already know.

---

## 1. `ncdu` — Disk Usage Analyzer

### What is it?
`ncdu` stands for **NCurses Disk Usage**. It shows you which files and folders are eating up your disk space — in a nice, interactive screen you can navigate with arrow keys.

> **Simple analogy:** Imagine your hard disk is a cupboard. `ncdu` opens the cupboard and shows you exactly which boxes are taking the most space, and you can open each box to see what's inside.

### Install
```bash
sudo apt install ncdu        # Ubuntu / Debian
sudo dnf install ncdu        # Fedora / RHEL
```

### Basic Usage
```bash
ncdu                         # analyze your current folder
ncdu /                       # analyze the entire system (root)
ncdu /home                   # analyze the home directory
ncdu /var/log                # check which log files are large
```

### What you see on screen
```
--- /home/student -----------------------------------------
  512.0 MiB [##########] /Downloads
  200.0 MiB [####      ] /Videos
   80.0 MiB [#         ] /Documents
    5.0 MiB [          ] /Desktop
```

### Key controls
| Key | Action |
|-----|--------|
| `↑` `↓` | Move up and down |
| `Enter` | Open a folder |
| `←` or `q` | Go back / Quit |
| `d` | Delete the selected item |

### Real-world use
```bash
# Your disk is full and you don't know why — run this:
ncdu /
# Navigate into the largest folder and find the culprit
```

---

## 2. `tldr` — Simplified Man Pages

### What is it?
`tldr` gives you **short, simple examples** of how to use any Linux command. Instead of reading a 10-page manual, you get 5–10 practical examples in seconds.

> **Simple analogy:** `man` pages are like reading a full textbook. `tldr` is like reading the cheat sheet your teacher gives before an exam.

### Install
```bash
sudo apt install tldr        # Ubuntu / Debian
npm install -g tldr          # via Node.js (any system)
pip install tldr             # via Python (any system)
```

### Basic Usage
```bash
tldr tar                     # how to use tar (compress/extract)
tldr curl                    # how to download files
tldr chmod                   # how to change file permissions
tldr ssh                     # how to connect to remote servers
tldr --update                # update the examples database
```

### Example output for `tar`
```
tar
Archiving utility.

- Create an archive from files:
  tar cf target.tar file1 file2 file3

- Extract an archive in the current directory:
  tar xf source.tar

- Create a compressed archive (gzip):
  tar czf target.tar.gz folder/

- List the contents of a tar file:
  tar tvf source.tar
```

### Why it's better than `man`
| `man tar` | `tldr tar` |
|-----------|------------|
| 500+ lines of text | 10–15 lines |
| Technical descriptions | Real examples you can copy |
| Hard for beginners | Perfect for beginners |

---

## 3. `rg` (ripgrep) — Fast File Search

### What is it?
`rg` searches for text **inside files**, recursively through all folders. It is much faster than the traditional `grep` command and automatically skips hidden/system files.

> **Simple analogy:** Imagine you have 1000 notebooks and you want to find every notebook that contains the word "photosynthesis". `grep` checks them one by one. `rg` checks all of them at the same time.

### Install
```bash
sudo apt install ripgrep     # Ubuntu / Debian
sudo dnf install ripgrep     # Fedora / RHEL
```

### Basic Usage
```bash
rg "hello"                   # find "hello" in all files here
rg "error" /var/log          # find "error" in log files
rg -i "warning"              # search case-insensitive
rg -l "TODO"                 # only show filenames, not the lines
rg -n "def main"             # show line numbers
rg "import" --type py        # search only in Python files
rg -w "cat"                  # match whole word only (not "catch")
```

### Example output
```bash
$ rg "hello" /home/student/projects

greetings.py
3: print("hello world")
10: message = "hello there"

notes.txt
5: hello, this is my note
```

### `rg` vs `grep` comparison
| Feature | `grep -r` | `rg` |
|---------|-----------|------|
| Speed | Normal | 5–10x faster |
| Skips `.git` folder | No | Yes (automatic) |
| Recursive by default | No (need `-r`) | Yes |
| Color output | Basic | Clear and readable |

---

## 4. `fzf` — Fuzzy Finder

### What is it?
`fzf` is an **interactive search tool**. You type a few letters and it instantly filters results — even if you don't type the exact word. You can use it to search files, command history, running processes, and more.

> **Simple analogy:** Google search lets you find things even if you spell something wrong or only remember part of the word. `fzf` does the same thing — but for your terminal.

### Install
```bash
sudo apt install fzf         # Ubuntu / Debian
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install               # install with shell integration
```

### Basic Usage
```bash
fzf                          # fuzzy search all files in current folder
ls | fzf                     # pick a file from ls output
history | fzf                # search your command history interactively
cat /etc/passwd | fzf        # search through any command's output
```

### Power combos (very useful!)
```bash
# Open any file in nano using fuzzy search
nano $(fzf)

# Jump into any folder interactively
cd $(find . -type d | fzf)

# Kill a running process by searching its name
kill $(ps aux | fzf | awk '{print $2}')
```

### Keyboard shortcuts (after installing with shell integration)
| Shortcut | What it does |
|----------|--------------|
| `Ctrl + R` | Search through your command history |
| `Ctrl + T` | Search files in the current directory |
| `Alt + C` | Jump into a directory using fuzzy search |
| `↑` `↓` | Move through results |
| `Enter` | Select the highlighted result |

---

## 5. `bat` — Modern `cat` Replacement

### What is it?
`bat` works just like `cat` (it prints file content to the screen), but it adds **syntax highlighting**, **line numbers**, and shows **Git changes** on the side. It makes reading code files much easier.

> **Simple analogy:** `cat` is like reading a plain black-and-white printout. `bat` is like reading a colour-coded printed version where keywords are highlighted, making it much easier to understand.

### Install
```bash
sudo apt install bat          # Ubuntu / Debian
# Note: on some Ubuntu versions, the command is 'batcat'
sudo dnf install bat          # Fedora / RHEL
```

### Basic Usage
```bash
bat file.py                  # view a Python file with colors
bat notes.md                 # view a markdown file
batcat file.py               # use this if 'bat' doesn't work on Ubuntu
bat -n file.py               # show line numbers only
bat -A file.txt              # show hidden/invisible characters
bat --style=plain file.txt   # plain view, no line numbers or borders
```

### Example — viewing a Python file
```
───────┬──────────────────────────────────
       │ File: hello.py
───────┼──────────────────────────────────
   1   │ # My first Python program
   2   │ name = input("Enter your name: ")
   3   │ print(f"Hello, {name}!")
───────┴──────────────────────────────────
```

### Make `bat` your default `cat`
```bash
# Add to your ~/.bashrc file
alias cat='bat --style=plain'

# Apply the change
source ~/.bashrc
```

### `bat` vs `cat`
| Feature | `cat` | `bat` |
|---------|-------|-------|
| Syntax highlighting | ❌ No | ✅ Yes |
| Line numbers | ❌ No | ✅ Yes |
| Git change indicator | ❌ No | ✅ Yes |
| Works with pipes | ✅ Yes | ✅ Yes |

---

## 6. `ranger` — Terminal File Manager

### What is it?
`ranger` is a **file manager that runs inside the terminal**. It shows three columns: your parent folder, your current folder, and a preview of the selected file. You navigate entirely with the keyboard.

> **Simple analogy:** Windows File Explorer lets you click through folders with a mouse. `ranger` lets you do the exact same thing — but completely with keyboard shortcuts, and it works over SSH on remote servers too.

### Install
```bash
sudo apt install ranger      # Ubuntu / Debian
sudo dnf install ranger      # Fedora / RHEL
```

### Start it
```bash
ranger                       # open ranger in current directory
ranger /var/log              # open ranger in a specific path
```

### What you see on screen
```
/home/student/projects  │ my-website/    │ index.html
Documents/              │ notes.txt       │ style.css
Downloads/              │ homework.pdf    │ script.js
projects/           ►   │ images/         │
Desktop/                │                 │ preview of index.html...
```

### Key controls
| Key | Action |
|-----|--------|
| `↑` `↓` | Move up and down |
| `→` or `Enter` | Open folder or file |
| `←` | Go back to parent folder |
| `Space` | Select/deselect a file |
| `yy` | Copy file |
| `dd` | Cut file |
| `pp` | Paste file |
| `dD` | Delete file |
| `q` | Quit |

### Why use ranger over normal `ls`/`cd`?
```bash
# Without ranger — lots of typing:
ls
cd projects
ls
cd my-website
ls
cat index.html

# With ranger — just arrow keys, instant file previews
ranger
```

---

## 7. `lazygit` — Git TUI

### What is it?
`lazygit` is a **visual Git interface in your terminal**. Instead of memorizing Git commands, you see all your changes, branches, and commit history on one screen and use simple keyboard shortcuts.

> **Simple analogy:** Git on the command line is like driving a car with only text instructions. `lazygit` gives you a dashboard with buttons — the same car, but much easier to control.

### Install
```bash
# Ubuntu / Debian
sudo add-apt-repository ppa:lazygit-team/release
sudo apt install lazygit

# Using Go
go install github.com/jesseduffield/lazygit@latest

# macOS
brew install lazygit
```

### Start it
```bash
cd my-project               # go into a git repository
lazygit                     # open the lazygit interface
```

### What you see on screen
```
Unstaged Files         │ Branches        │ Commit Log
─────────────────────  │ ─────────────── │ ──────────────────────
M  index.html          │ * main          │ abc123 Add login page
A  style.css           │   feature/login │ def456 Fix navbar bug
                       │                 │ ghi789 Initial commit
```

### Key controls
| Key | Action |
|-----|--------|
| `Tab` | Switch between panels |
| `Space` | Stage / unstage a file |
| `c` | Commit staged changes |
| `p` | Push to remote |
| `P` | Pull from remote |
| `b` | Create a new branch |
| `?` | Show all shortcuts |
| `q` | Quit |

### Common workflow
```
1. Open lazygit in your project folder
2. Press Space on each changed file to stage it
3. Press c to write your commit message
4. Press p to push to GitHub
```
All without typing a single git command!

---

## 8. `lazydocker` — Docker TUI

### What is it?
`lazydocker` is a **visual dashboard for Docker** that runs in your terminal. You can see all your containers, images, logs, and resource usage from one screen — no need to remember complex `docker` commands.

> **Simple analogy:** Docker on the command line is like controlling a factory by sending text messages. `lazydocker` gives you a control panel with live monitors for every machine in the factory.

### Install
```bash
# Using the install script
curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash

# Using Go
go install github.com/jesseduffield/lazydocker@latest

# macOS
brew install lazydocker
```

### Start it
```bash
lazydocker                  # open the dashboard
```

### What you see on screen
```
Containers              │ Logs                        │ Stats
──────────────────────  │ ──────────────────────────  │ ────────────
▶ web-app    Running    │ [web-app]                   │ CPU:  2.3%
  database   Running    │ Server started on port 3000 │ MEM: 128MB
  redis      Exited     │ Connected to database       │ NET: ↑1KB
                        │ GET /api/users 200 OK        │
```

### Key controls
| Key | Action |
|-----|--------|
| `↑` `↓` | Select container |
| `Tab` | Switch between panels |
| `Enter` | View details |
| `s` | Stop a container |
| `r` | Restart a container |
| `d` | Remove a container |
| `e` | Open a shell inside the container |
| `q` | Quit |

---

## 9. `glances` — System Monitor

### What is it?
`glances` is a **real-time system monitoring tool** that shows everything about your computer in one screen — CPU, RAM, disk, network, running processes, and more.

> **Simple analogy:** Your car has a dashboard that shows speed, fuel, temperature, etc. all at once. `glances` is the dashboard for your Linux computer — everything important, all in one view.

### Install
```bash
sudo apt install glances     # Ubuntu / Debian
sudo dnf install glances     # Fedora / RHEL
pip install glances          # via Python (any system)
```

### Basic Usage
```bash
glances                      # open the live dashboard
glances -w                   # run as a web server (view in browser)
glances --export csv         # export stats to a CSV file
```

### What you see on screen
```
CPU [|||||||    42%]    MEM [|||||      55%]    SWAP [|   10%]

NETWORK          Rx/s    Tx/s      DISK I/O    Read    Write
eth0             1 MB   500 KB    sda          2 MB    1 MB

PROCESSES
  PID  USER     CPU%  MEM%  NAME
 1234  student   5.2   2.1  chrome
  567  student   2.1   1.0  python3
   89  root      0.5   0.3  sshd
```

### Key controls while running
| Key | Action |
|-----|--------|
| `q` or `Esc` | Quit |
| `c` | Sort processes by CPU |
| `m` | Sort processes by memory |
| `n` | Show/hide network stats |
| `d` | Show/hide disk stats |
| `h` | Show help |

### Web mode (view from browser)
```bash
glances -w
# Then open: http://localhost:61208
# Useful when monitoring a remote server
```

---

## Quick Reference Table

| Tool | Replaces | Install | Best used for |
|------|----------|---------|---------------|
| `ncdu` | `du` | `apt install ncdu` | Finding large files on disk |
| `tldr` | `man` | `apt install tldr` | Quick command examples |
| `rg` | `grep -r` | `apt install ripgrep` | Searching text inside files |
| `fzf` | manual typing | `apt install fzf` | Interactive fuzzy search |
| `bat` | `cat` | `apt install bat` | Viewing files with colors |
| `ranger` | `ls` + `cd` | `apt install ranger` | Browsing files in terminal |
| `lazygit` | `git` commands | see above | Managing Git visually |
| `lazydocker` | `docker` commands | see above | Managing Docker visually |
| `glances` | `top` / `htop` | `apt install glances` | Monitoring system health |

---

## Install All Basic Tools at Once

```bash
# Ubuntu / Debian — installs all tools available in apt
sudo apt update && sudo apt install -y ncdu tldr ripgrep fzf bat ranger glances

# Fedora / RHEL
sudo dnf install -y ncdu ripgrep fzf bat ranger glances
```

> **Note:** `lazygit` and `lazydocker` need to be installed separately using their install scripts (see their sections above).

