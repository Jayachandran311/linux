# Linux — File Permissions

## Understanding Permissions

Every file and directory has:
- **Owner** (user) — one user who owns the file
- **Group** — one group associated with the file
- **Others** — everyone else

Each has three permission bits: **read (r)**, **write (w)**, **execute (x)**

```bash
ls -la
# -rwxr-xr-- 1 alice devs  4096 Mar 30 10:00 script.sh
#  ─┬──┬──┬─
#   │  │  └── others: r--  (read only)
#   │  └───── group:  r-x  (read + execute)
#   └──────── owner:  rwx  (read + write + execute)
# First char: file type (- = file, d = dir, l = symlink)
```

---

## Permission Values

| Symbol | Octal | Meaning |
|--------|-------|---------|
| `r` | 4 | Read |
| `w` | 2 | Write |
| `x` | 1 | Execute |
| `-` | 0 | No permission |

Combinations:
```
rwx = 4+2+1 = 7
rw- = 4+2+0 = 6
r-x = 4+0+1 = 5
r-- = 4+0+0 = 4
-wx = 0+2+1 = 3
-w- = 0+2+0 = 2
--x = 0+0+1 = 1
--- = 0+0+0 = 0
```

Common permissions:
```
755 → rwxr-xr-x (owner: full, group+others: read+execute)
644 → rw-r--r-- (owner: read+write, group+others: read only)
700 → rwx------ (owner: full, group+others: nothing)
600 → rw------- (owner: read+write, group+others: nothing)
```

---

## `chmod` — Change Permissions

### Numeric (octal) mode

```bash
chmod 755 script.sh          # rwxr-xr-x
chmod 644 config.txt         # rw-r--r--
chmod 600 private.key        # rw------- (only owner can read)
chmod 777 shared/            # rwxrwxrwx (everyone has full — avoid!)
chmod -R 755 directory/      # recursive (all files in directory)
```

### Symbolic mode

```bash
# Format: [ugoa][+-=][rwx]
# u=user/owner, g=group, o=others, a=all

chmod u+x script.sh          # add execute for owner
chmod g-w file.txt           # remove write for group
chmod o+r file.txt           # add read for others
chmod a+x script.sh          # add execute for everyone

chmod u=rwx,g=rx,o=r file   # set exactly
chmod +x script.sh           # shorthand: add execute for all
chmod -x script.sh           # remove execute for all

# Recursive
chmod -R g+r directory/
chmod -R u+X directory/      # X = execute only if already executable or is directory
```

---

## `chown` — Change Owner

```bash
chown alice file.txt              # change owner to alice
chown alice:devs file.txt         # change owner AND group
chown :devs file.txt              # change group only (same as chgrp)
chown -R alice:devs project/      # recursive
chown alice: file.txt             # change owner + use alice's login group
```

---

## `chgrp` — Change Group

```bash
chgrp devs file.txt               # change group to devs
chgrp -R webteam /var/www/html    # recursive
```

---

## `umask` — Default Permissions

`umask` defines permissions **to subtract** from the default when creating new files/dirs.

```bash
umask           # show current umask (e.g., 0022)
umask 0027      # set new umask

# New file default base: 666 (rw-rw-rw-)
# New dir default base:  777 (rwxrwxrwx)

# With umask 022:
# File: 666 - 022 = 644 (rw-r--r--)
# Dir:  777 - 022 = 755 (rwxr-xr-x)

# With umask 027:
# File: 666 - 027 = 640 (rw-r-----)
# Dir:  777 - 027 = 750 (rwxr-x---)
```

Set persistent umask in `~/.bashrc` or `/etc/profile`.

---

## Special Permissions

### SUID (Set User ID) — `s` on owner execute bit

When set on an **executable**, it runs with the file **owner's** privileges (not the caller's). Used by programs like `passwd`.

```bash
chmod u+s /usr/bin/myprogram    # set SUID
chmod 4755 file                 # octal: 4 prefix

ls -la /usr/bin/passwd
# -rwsr-xr-x  root  root  ...   ← 's' in owner execute position
```

### SGID (Set Group ID) — `s` on group execute bit

- On executable: runs with **file group's** privileges
- On **directory**: new files inherit the directory's group (useful for shared workspaces)

```bash
chmod g+s /shared/directory     # set SGID
chmod 2755 file                 # octal: 2 prefix

ls -la /usr/bin/wall
# -rwxr-sr-x  root  tty  ...   ← 's' in group execute position
```

### Sticky Bit — `t` on others execute bit

On a directory, only the file **owner** (or root) can delete files — even if others have write access.
Used on `/tmp`.

```bash
chmod +t /shared/dir            # set sticky bit
chmod 1777 /tmp                 # octal: 1 prefix

ls -la /tmp
# drwxrwxrwt  ...   ← 't' in others execute position
```

### Find SUID/SGID files (security check)

```bash
find / -perm /4000 2>/dev/null   # SUID files
find / -perm /2000 2>/dev/null   # SGID files
find / -perm /6000 2>/dev/null   # both
```

---

## ACLs — Extended Access Control Lists

Standard permissions only support one owner and one group. ACLs add per-user/per-group rules.

```bash
# View ACLs
getfacl file.txt

# Add ACL entry
setfacl -m u:bob:rw file.txt       # give bob read+write
setfacl -m g:audit:r /var/log/     # give audit group read on dir
setfacl -R -m g:devs:rwx project/  # recursive

# Remove ACL entry
setfacl -x u:bob file.txt

# Remove all ACLs
setfacl -b file.txt
```

---

## Practical Permission Examples

```bash
# Web server files
chmod 755 /var/www/html/           # directory
chmod 644 /var/www/html/index.html  # HTML files

# SSH key permissions (strict — SSH enforces these)
chmod 700 ~/.ssh/
chmod 600 ~/.ssh/id_rsa            # private key
chmod 644 ~/.ssh/id_rsa.pub        # public key
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts

# Script files
chmod 755 /usr/local/bin/myscript  # executable by all

# Shared directory for a team
groupadd devteam
chown root:devteam /shared/
chmod 2775 /shared/    # SGID + group write
```

---

## Key Takeaways

> - `ls -la` shows permissions as `rwxrwxrwx` for owner/group/others
> - `chmod 755` = `rwxr-xr-x`; `chmod 644` = `rw-r--r--`
> - `chmod +x` makes a file executable
> - `chown user:group` changes both owner and group
> - SUID (`4xxx`) runs as file owner; SGID (`2xxx`) on dirs inherits group; Sticky bit (`1xxx`) prevents others from deleting
> - SSH keys must have `600` (private) and `644` (public) permissions
