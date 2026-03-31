# Linux — Networking Commands

## Network Interface Management

### `ip` command (modern — prefer over ifconfig)

```bash
ip addr                       # show all interfaces and IPs
ip addr show eth0             # show specific interface
ip link                       # show link status
ip link set eth0 up           # bring interface up
ip link set eth0 down         # bring interface down

ip route                      # show routing table
ip route show                 # same
ip route add default via 192.168.1.1   # set default gateway
ip route add 10.0.0.0/24 via 192.168.1.1 dev eth0

ip neigh                      # ARP table (neighbor cache)
```

### `ifconfig` (older, classic)

```bash
ifconfig                      # show all interfaces
ifconfig eth0                 # show specific interface
ifconfig eth0 up
ifconfig eth0 down
ifconfig eth0 192.168.1.100 netmask 255.255.255.0
```

---

## Testing Connectivity

### `ping`

```bash
ping google.com               # continuous ping (Ctrl+C to stop)
ping -c 4 google.com          # send only 4 packets
ping -c 4 -i 0.5 google.com   # 0.5s interval between pings
ping -W 3 google.com          # timeout 3s per response
ping6 ::1                     # ICMP6 for IPv6
```

### `traceroute` / `tracepath`

```bash
traceroute google.com         # trace route (may need install)
tracepath google.com          # similar, no root needed
mtr google.com                # combines ping + traceroute (interactive)
```

---

## DNS Resolution

```bash
nslookup google.com             # basic DNS lookup
nslookup -type=MX gmail.com    # MX records
nslookup -type=NS google.com   # name servers

dig google.com                  # detailed DNS query
dig google.com A                # A records only
dig google.com MX               # mail exchange records
dig google.com AAAA             # IPv6 records
dig google.com ANY              # all records
dig @8.8.8.8 google.com        # query specific DNS server
dig +short google.com           # compact output (just the IPs)
dig -x 8.8.8.8                  # reverse DNS lookup

host google.com                 # quick lookup
host -t MX gmail.com            # MX records
```

---

## Port and Socket Info

### `ss` (modern, replaces netstat)

```bash
ss -tuln                     # listening TCP and UDP ports
ss -tulnp                    # with process name (root for all)
ss -an                       # all connections
ss -tnp                      # TCP connections with process
ss -unp                      # UDP with process
ss -s                        # summary statistics
ss -t state established      # established TCP connections

# Filter by port
ss -tnp | grep ':80'
ss -tnp | grep ':22'

# Check if port is open
ss -tuln | grep ':8080'
```

### `netstat` (older, may need `net-tools`)

```bash
netstat -tuln                 # listening ports
netstat -tulnp                # with process
netstat -an                   # all connections
netstat -rn                   # routing table
```

---

## HTTP Requests

### `curl`

```bash
curl https://example.com                          # basic GET
curl -o output.html https://example.com           # save to file
curl -O https://example.com/file.tar.gz          # save with original name
curl -L https://short.url/link                    # follow redirects
curl -I https://example.com                       # headers only (HEAD)
curl -v https://example.com                       # verbose output

# POST request
curl -X POST https://api.example.com/data \
    -H "Content-Type: application/json" \
    -d '{"name": "Alice", "role": "admin"}'

# With auth
curl -u username:password https://api.example.com
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com

# Download with progress
curl --progress-bar -O https://example.com/bigfile.iso

# Test if service is up
curl -sf https://example.com/health && echo "UP" || echo "DOWN"

# Set timeout
curl --max-time 10 https://example.com

# Silent (suppress progress)
curl -s https://api.example.com/data | python3 -m json.tool
```

### `wget`

```bash
wget https://example.com/file.tar.gz             # download file
wget -O filename.tar.gz https://example.com/...  # custom filename
wget -c https://example.com/bigfile.iso          # resume download
wget -r -l 2 https://example.com                 # recursive (mirror site)
wget --quiet https://example.com                  # suppress output
wget -b https://example.com/bigfile.iso          # background download
```

---

## SSH — Secure Shell

```bash
# Basic connection
ssh username@hostname
ssh alice@192.168.1.50
ssh alice@server.example.com -p 2222   # custom port

# Run command remotely
ssh alice@server "uptime"
ssh alice@server "df -h; free -h"

# SSH with key authentication
ssh-keygen -t ed25519 -C "alice@laptop"    # generate key pair
ssh-copy-id alice@server                   # copy public key to server
ssh -i ~/.ssh/custom_key alice@server      # use specific key

# SSH config file (~/.ssh/config)
# Host myserver
#     HostName 192.168.1.50
#     User alice
#     Port 22
#     IdentityFile ~/.ssh/id_ed25519
ssh myserver    # use config alias

# Copy files over SSH
scp file.txt alice@server:/home/alice/         # copy to server
scp alice@server:/home/alice/file.txt ./       # copy from server
scp -r directory/ alice@server:/home/alice/    # copy directory

# rsync over SSH (more efficient, incremental)
rsync -avz source/ alice@server:/destination/
rsync -avz --delete source/ alice@server:/destination/   # sync

# SSH tunneling
ssh -L 8080:localhost:80 alice@server    # local port forward
ssh -R 8080:localhost:80 alice@server    # remote port forward
ssh -D 1080 alice@server                 # SOCKS proxy
```

---

## Firewall (UFW / iptables / nftables)

### UFW (Uncomplicated Firewall — Ubuntu)

```bash
ufw status                          # show status and rules
ufw status verbose                  # detailed
ufw enable                          # turn on firewall
ufw disable                         # turn off

ufw allow 22                        # allow SSH
ufw allow 80                        # allow HTTP
ufw allow 443                       # allow HTTPS
ufw allow 22/tcp                    # explicit protocol
ufw allow from 192.168.1.0/24      # allow from subnet
ufw allow from 10.0.0.1 to any port 5432  # allow specific IP to postgres

ufw deny 23                         # block telnet
ufw delete allow 80                 # remove rule

ufw allow OpenSSH                   # by service name
ufw allow 'Nginx Full'              # allows both 80 and 443
```

---

## Network Utilities

```bash
whois google.com              # domain registration info
nmap -sn 192.168.1.0/24      # scan network for live hosts ("ping scan")
nmap -p 1-1000 192.168.1.50  # scan ports 1-1000 on a host
nmap -sV 192.168.1.50        # detect services/versions

hostname                      # show hostname
hostname -I                   # show all IP addresses
hostname -f                   # show FQDN

# Network interfaces
ip link show
cat /proc/net/if_inet6        # IPv6 interfaces
```

---

## Key Takeaways

> - `ip addr` shows network interfaces; `ip route` shows routing table
> - `ping` tests connectivity; `traceroute`/`mtr` traces the path
> - `dig` (or `nslookup`) for DNS resolution
> - `ss -tuln` shows listening ports (modern `netstat`)
> - `curl -sf URL` is great in scripts to check if a service is up
> - Use SSH keys (not passwords) for server access
> - `scp`/`rsync` for transferring files securely
