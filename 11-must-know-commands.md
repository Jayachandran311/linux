# 5 Commands Every Linux User Should Know

These are modern, powerful CLI tools that dramatically improve your productivity in the terminal. Unlike the standard utilities, these need to be installed separately — but they're absolutely worth it.

---

## 1. `ncdu` — Disk Usage Analyzer

A terminal-based interactive tool that visualizes disk space usage. Far easier to navigate than `du`.

### Install
```bash
# Debian / Ubuntu
sudo apt install ncdu

# RHEL / CentOS / Fedora
sudo dnf install ncdu
```

### Usage
```bash
ncdu              # analyze current directory
ncdu /            # analyze entire filesystem
ncdu /var/log     # analyze specific directory
```

### Why it's better than `du`
| `du` | `ncdu` |
|------|--------|
| Raw numbers, hard to read | Interactive UI with sizes |
| No navigation | Arrow keys to drill into folders |
| No delete option | Press `d` to delete a file/folder |

### Key controls
| Key | Action |
|-----|--------|
| `↑ ↓` | Navigate |
| `Enter` | Open directory |
| `d` | Delete selected item |
| `q` | Quit |

---

## 2. `tldr` — Simplified Help Pages

Replaces long `man` pages with short, practical, example-based explanations. Perfect for quick command reminders.

### Install
```bash
# Via npm
npm install -g tldr

# Via pip
pip install tldr

# Debian / Ubuntu
sudo apt install tldr
```

### Usage
```bash
tldr tar          # show common tar examples
tldr rsync        # show common rsync examples
tldr curl         # show common curl examples
tldr --update     # update the local cache
```

### Example output for `tar`
```
tar

Archiving utility.
Extract: tar -xf archive.tar
Create:  tar -czf archive.tar.gz folder/
List:    tar -tf archive.tar
```

> **Tip:** Use `tldr` when you remember a command exists but forget the exact flags.

---

## 3. `rg` (ripgrep) — Fast Recursive Search

A super-fast search tool that works like `grep` but is significantly faster, recursive by default, and automatically respects `.gitignore` rules.

### Install
```bash
# Debian / Ubuntu
sudo apt install ripgrep

# RHEL / Fedora
sudo dnf install ripgrep

# macOS
brew install ripgrep
```

### Usage
```bash
rg "search_term"              # search in current directory recursively
rg "error" /var/log           # search in specific directory
rg -i "warning"               # case-insensitive search
rg -l "TODO"                  # list only filenames with matches
rg -n "function"              # show line numbers
rg "pattern" --type py        # search only Python files
rg -w "word"                  # whole word match only
```

### `rg` vs `grep`
| Feature | `grep -r` | `rg` |
|---------|-----------|------|
| Speed | Slower | 5–10× faster |
| `.gitignore` aware | No | Yes (automatic) |
| Recursive by default | No (`-r` needed) | Yes |
| Color output | Basic | Rich, readable |

---

## 4. `fzf` — Fuzzy Finder

An interactive, real-time fuzzy search tool for files, command history, processes, and more. Works as a standalone command or piped with other tools.

### Install
```bash
# Debian / Ubuntu
sudo apt install fzf

# From GitHub (latest)
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

### Usage
```bash
fzf                           # fuzzy search files in current directory
fzf --preview 'cat {}'        # preview file content while searching
history | fzf                 # fuzzy search through command history
ps aux | fzf                  # fuzzy search running processes
```

### Power combos
```bash
# Open a file in vim using fuzzy search
vim $(fzf)

# cd into a directory interactively
cd $(find . -type d | fzf)

# Kill a process interactively
kill $(ps aux | fzf | awk '{print $2}')
```

### Shell integration (after install)
| Shortcut | Action |
|----------|--------|
| `Ctrl+R` | Fuzzy search command history |
| `Ctrl+T` | Fuzzy search files in current directory |
| `Alt+C`  | Fuzzy cd into a subdirectory |

---

## 5. `bat` — Modern `cat` Replacement

Displays file contents with syntax highlighting, line numbers, and Git change indicators. A drop-in replacement for `cat`.

### Install
```bash
# Debian / Ubuntu
sudo apt install bat
# Note: on Ubuntu the binary may be called batcat
batcat filename.md

# RHEL / Fedora
sudo dnf install bat

# Create alias if needed
echo "alias bat='batcat'" >> ~/.bashrc
source ~/.bashrc
```

### Usage
```bash
bat file.py               # view with syntax highlighting
bat file1.txt file2.txt   # view multiple files
bat -n file.py            # show line numbers only (no other decorations)
bat -A file.txt           # show invisible characters
bat --style=plain file.md # plain output, no line numbers or borders
```

### `bat` vs `cat`
| Feature | `cat` | `bat` |
|---------|-------|-------|
| Syntax highlighting | No | Yes (auto-detected) |
| Line numbers | No | Yes |
| Git diff indicators | No | Yes |
| Pager built-in | No | Yes (for long files) |

### Alias tip
```bash
# Add to ~/.bashrc or ~/.zshrc
alias cat='bat --style=plain'   # use bat everywhere cat is used
```

---

## Quick Reference

| Tool | Install | What it replaces | Best for |
|------|---------|-----------------|----------|
| `ncdu` | `apt install ncdu` | `du` | Finding what's eating disk space |
| `tldr` | `npm install -g tldr` | `man` | Quick command examples |
| `rg` | `apt install ripgrep` | `grep -r` | Fast code/log search |
| `fzf` | `apt install fzf` | manual searching | Interactive file/history search |
| `bat` | `apt install bat` | `cat` | Reading files with highlighting |

---

## Install All at Once

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install -y ncdu ripgrep fzf bat

# Fedora / RHEL
sudo dnf install -y ncdu ripgrep fzf bat

# Add to .bashrc
echo "alias cat='bat --style=plain'" >> ~/.bashrc
source ~/.bashrc
```
