# Linux — User Management

## User Accounts

Every user has:
- **Username** — login name
- **UID** — User ID (number). Root = 0, System = 1–999, Regular = 1000+
- **GID** — Primary Group ID
- **Home directory** — `/home/username` (or `/root` for root)
- **Shell** — default shell (`/bin/bash`, `/sbin/nologin`, etc.)

---

## `/etc/passwd` — User Database

```bash
cat /etc/passwd
# username:x:UID:GID:comment:home:shell

alice:x:1001:1001:Alice Smith:/home/alice:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
root:x:0:0:root:/root:/bin/bash
```

Fields:
1. Username
2. `x` = password in `/etc/shadow`
3. UID
4. GID (primary group)
5. GECOS comment (full name, etc.)
6. Home directory
7. Login shell (`/sbin/nologin` or `/bin/false` = no login)

---

## `/etc/shadow` — Password Hashes

```bash
sudo cat /etc/shadow
# username:hashed_password:lastchange:min:max:warn:inactive:expire:
```

Only readable by root. Contains hashed passwords (not plain text).

---

## `/etc/group` — Group Database

```bash
cat /etc/group
# groupname:x:GID:members

sudo:x:27:alice,bob
docker:x:999:alice
devteam:x:1100:alice,bob,charlie
```

---

## Adding Users

```bash
# Create user (interactive — sets home dir, creates group)
useradd -m -s /bin/bash alice
useradd -m -s /bin/bash -c "Alice Smith" -G sudo,docker alice

# Options
# -m          create home directory
# -M          don't create home directory
# -d path     specify home directory
# -s shell    specify shell
# -c comment  GECOS field (full name)
# -G groups   additional groups (comma-separated, no spaces)
# -u UID      specific UID
# -g GID      primary group
# -e date     account expiry (YYYY-MM-DD)
# -r          system user (UID < 1000, no home dir)

# Set password
passwd alice          # prompted to enter password

# Ubuntu/Debian convenience command (interactive):
adduser alice
```

---

## Modifying Users

```bash
usermod -aG docker alice        # add alice to docker group (-a = append!)
usermod -aG sudo,docker alice   # add to multiple groups
usermod -s /bin/zsh alice       # change shell
usermod -c "New Full Name" alice  # update comment
usermod -d /home/newdir alice   # change home directory
usermod -l newname alice        # rename username
usermod -L alice                # lock account (disable password login)
usermod -U alice                # unlock account
usermod -e 2025-12-31 alice     # set expiry date
```

> ⚠️ Use `-aG` (not `-G` alone) to **add** groups. `-G` without `-a` **replaces** all supplementary groups.

---

## Deleting Users

```bash
userdel alice               # delete user (keeps home dir)
userdel -r alice            # delete user AND home directory + mail spool
```

---

## Group Management

```bash
groupadd devteam              # create group
groupadd -g 1100 devteam      # with specific GID

groupmod -n newname devteam   # rename group
groupdel devteam              # delete group

# Add/remove user from group
usermod -aG devteam alice     # add alice to devteam
gpasswd -d alice devteam      # remove alice from devteam
gpasswd -a alice devteam      # add alice to devteam (alternative)
```

---

## Switching Users

```bash
su - alice              # switch to alice (- loads alice's environment)
su alice                # switch to alice (keeps current env)
su -                    # switch to root
sudo command            # run single command as root
sudo -i                 # interactive root shell
sudo -u alice command   # run as alice
sudo -l                 # list your sudo permissions
```

### Difference: `su` vs `su -`

```bash
su alice    # only switch user, keep current env (PWD, PATH, etc.)
su - alice  # full login: load alice's profile, go to her home
```

---

## `sudo` Configuration

```bash
# Edit sudoers safely
visudo       # opens /etc/sudoers with syntax checking

# Sudoers format:
# user/group  hosts = (run_as) commands

alice   ALL=(ALL:ALL) ALL          # alice can run anything
%sudo   ALL=(ALL:ALL) ALL          # any member of sudo group
alice   ALL=(ALL) NOPASSWD: ALL    # no password prompt
alice   ALL=(ALL) NOPASSWD: /usr/bin/apt, /bin/systemctl  # specific commands

# Allow a group member specific commands
%developers  ALL=(root) /bin/systemctl restart nginx
```

---

## Password Management

```bash
passwd                  # change your own password
passwd alice            # change alice's password (root only)
passwd -l alice         # lock account
passwd -u alice         # unlock account
passwd -e alice         # expire password (force change on next login)
passwd -d alice         # remove password (empty password)
passwd -S alice         # show password status

chage -l alice          # show password aging info
chage -M 90 alice       # set max password age (90 days)
chage -W 14 alice       # warn 14 days before expiry
chage -d 0 alice        # force password change at next login
```

---

## Viewing Current User Info

```bash
whoami              # current username
id                  # UID, GID, and all groups
id alice            # info for another user
groups              # your group memberships
groups alice        # alice's group memberships
w                   # who is logged in and what they're doing
who                 # who is logged in
last                # login history
last alice          # login history for alice
lastb               # failed login attempts (root only)
finger alice        # user info (if finger is installed)
```

---

## Practical Examples

### Create a new user for a service

```bash
# Create system user (no home, no login shell)
useradd -r -s /sbin/nologin -c "Nginx web server" www-data

# Create user with specific settings
useradd -m \
    -d /home/deploy \
    -s /bin/bash \
    -c "Deployment user" \
    -G docker,sudo \
    deploy

passwd deploy    # set password
```

### Add user to docker group (allow using docker without sudo)

```bash
usermod -aG docker $USER
newgrp docker    # apply group change without logging out
```

### Bulk create users from file

```bash
while IFS=',' read -r username fullname; do
    useradd -m -c "$fullname" "$username"
    echo "${username}:$(openssl rand -base64 12)" | chpasswd
    echo "Created: $username"
done < users.csv
```

---

## Key Takeaways

> - `useradd -m -s /bin/bash username` creates a user with home dir and bash shell
> - Always use `usermod -aG` (not `-G`) to add groups — bare `-G` replaces all groups
> - `/etc/passwd` stores user info; `/etc/shadow` stores password hashes
> - `sudo visudo` to safely edit sudoers
> - `su - alice` = full login switch; `su alice` = switch user only
> - System users (daemons) are created with `-r -s /sbin/nologin`
