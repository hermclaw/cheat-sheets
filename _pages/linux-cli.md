---
title: "Linux CLI Cheat Sheet"
description: "Comprehensive Linux command-line reference covering file operations, text processing, permissions, networking, and system administration."
layout: default
---

## File Operations

### ls — List Directory

```bash
ls                                # Basic listing
ls -la                            # Long format, all files (including hidden)
ls -lh                            # Human-readable sizes
ls -lt                            # Sort by modification time
ls -lS                            # Sort by size
ls -R                             # Recursive
ls -1                             # One per line
ls --color=auto                   # Colored output
```

### cp — Copy

```bash
cp file.txt backup.txt            # Copy file
cp -r dir1/ dir2/                 # Copy directory recursively
cp -a src/ dst/                   # Archive mode (preserve permissions, timestamps)
cp -u src dst                     # Copy only if source is newer
cp -v file.txt backup.txt         # Verbose
```

### mv — Move / Rename

```bash
mv old.txt new.txt                # Rename
mv file.txt /path/                # Move
mv -v *.txt /path/                # Verbose move multiple files
mv -n src dst                     # No-clobber (don't overwrite)
```

### rm — Remove

```bash
rm file.txt                       # Remove file
rm -r dir/                        # Remove directory recursively
rm -f file.txt                    # Force (no prompt)
rm -rf dir/                       # Force remove directory
rm -i file.txt                    # Interactive (confirm each)
```

### ln — Links

```bash
ln target link                    # Hard link
ln -s target link                 # Symbolic link
ln -sf /new/target link           # Force overwrite symlink
ls -l link                        # View link target
readlink -f link                  # Resolve full path
```

### find — Find Files

```bash
find .                            # All files under current dir
find . -name "*.py"               # By name (pattern)
find . -iname "*.PY"              # Case-insensitive name
find . -type f                    # Files only
find . -type d                    # Directories only
find . -type l                    # Symlinks only
find . -size +100M                # Larger than 100 MB
find . -size -1k                  # Smaller than 1 KB
find . -mtime -7                  # Modified in last 7 days
find . -mtime +30                 # Modified more than 30 days ago
find . -perm 644                  # By permissions
find . -user alice                # By owner
find . -empty                     # Empty files/dirs
find . -name "*.log" -delete      # Find and delete
find . -name "*.py" -exec grep -l "TODO" {} \;
find . -name "*.py" | xargs grep "TODO"
```

### mkdir, touch, tree

```bash
mkdir dir                         # Create directory
mkdir -p a/b/c                    # Create with parents
mkdir -p project/{src,tests,docs} # Multiple directories
touch newfile.txt                 # Create empty file / update timestamp
tree -L 2                         # Directory tree (depth 2)
tree -a                           # Include hidden files
```

### File Info

```bash
file filename                     # File type detection
stat filename                     # Detailed metadata
wc -l file.txt                    # Line count
wc -w file.txt                    # Word count
wc -c file.txt                    # Byte count
head -n 20 file.txt               # First 20 lines
tail -n 20 file.txt               # Last 20 lines
tail -f /var/log/syslog           # Follow (live)
du -sh dir/                       # Directory size (human-readable)
du -sh *                          # Size of all items
du -h --max-depth=1               # One level deep
```

## Text Processing

### grep — Search Text

```bash
grep "pattern" file               # Basic search
grep -i "pattern" file            # Case-insensitive
grep -r "pattern" dir/            # Recursive
grep -n "pattern" file            # With line numbers
grep -c "pattern" file            # Count matches
grep -v "pattern" file            # Invert (non-matching)
grep -l "pattern" *.py            # Filenames only
grep -o "pattern" file            # Only matching parts
grep -E "a|b" file                # Extended regex
grep -P "\d{3}" file              # Perl regex
grep -A 3 -B 3 "pattern" file    # Context: 3 lines after/before
grep --include="*.py" "TODO" .    # Search specific file types
grep -rn "pattern" --exclude-dir=.git .
```

### sed — Stream Editor

```bash
sed 's/old/new/' file             # Replace first occurrence per line
sed 's/old/new/g' file            # Replace all occurrences
sed -i 's/old/new/g' file         # Edit in-place
sed -i.bak 's/old/new/g' file     # In-place with backup
sed '2,5d' file                   # Delete lines 2-5
sed -n '10,20p' file              # Print lines 10-20
sed '/pattern/d' file             # Delete matching lines
sed 's/^#//' file                 # Uncomment lines
sed '/^$/d' file                  # Delete blank lines
sed -i 's/\r$//' file             # Remove Windows line endings
sed 's/\(pattern\)/[\1]/g' file   # Capture groups: \1, \2...
echo "hello world" | sed 's/\w\+/[&]/g'  # [&] = whole match
```

### awk — Text Processing

```bash
awk '{print $1}' file             # Print first column
awk '{print $1, $3}' file         # Print columns 1 and 3
awk -F"," '{print $2}' file       # CSV (comma-delimited)
awk '{sum += $1} END {print sum}' file  # Sum column
awk 'NR > 1' file                 # Skip header line
awk 'NR == 10' file               # Print line 10
awk '/pattern/' file              # Lines matching pattern
awk '$3 > 100' file               # Where column 3 > 100
awk '{print NR, $0}' file         # Line numbers
awk 'BEGIN {print "start"} {count++} END {print count}' file
awk -F: '{print $1}' /etc/passwd  # Parse passwd
```

### Other Text Tools

```bash
cut -d"," -f1,3 file.csv          # Extract fields (delimiter, field)
cut -c1-10 file                   # Extract characters 1-10
sort file                         # Sort lines
sort -n file                      # Numeric sort
sort -r file                      # Reverse sort
sort -k2 -n file                  # Sort by 2nd field (numeric)
sort -u file                      # Sort and unique
sort -t"," -k2 -n file.csv        # Sort CSV by 2nd column
uniq                              # Remove adjacent duplicates
uniq -c                           # Count duplicates (use with sort)
uniq -d                           # Only duplicates
tr 'a-z' 'A-Z' < file            # Translate characters
tr -d '\n' < file                 # Delete characters
tr -s ' ' ' ' < file              # Squeeze repeats
paste file1 file2                 # Merge lines side by side
column -t -s"," file.csv          # Format as table
fmt -w 80 file                    # Reformat to 80 chars
```

### Pipelines

```bash
cat file | grep "error" | sort | uniq -c | sort -rn | head -10
find . -name "*.py" | xargs wc -l | tail -1
ps aux | grep python | awk '{print $2}' | xargs kill
```

## Permissions

### chmod — Change Mode

```bash
# Symbolic
chmod u+x script.sh               # Add execute for user
chmod g+w file.txt                # Add write for group
chmod o-r file.txt                # Remove read for others
chmod a+x script.sh               # Add execute for all
chmod ugo+rwx file                # Full permissions for all

# Numeric (octal)
chmod 755 script.sh               # rwxr-xr-x (standard script)
chmod 644 file.txt                # rw-r--r-- (standard file)
chmod 600 key.pem                 # rw------- (private)
chmod 700 dir/                    # rwx------ (private dir)
chmod -R 755 /var/www             # Recursive

# Special bits
chmod +s /path/to/dir             # Set SUID/SGID
chmod 1777 /tmp                   # Sticky bit (rwxrwxrwt)
```

### chown — Change Owner

```bash
chown user:group file             # Change owner and group
chown user file                   # Change owner only
chown :group file                 # Change group only
chown -R user:group dir/          # Recursive
```

### umask

```bash
umask                             # Show current mask
umask 022                         # Default: owner full, others read-only
umask 077                         # Private: owner only
```

### Permission Reference

- `r` = 4 (read), `w` = 2 (write), `x` = 1 (execute)
- `7` = rwx, `6` = rw-, `5` = r-x, `4` = r--, `0` = ---
- User / Group / Other

## Process Management

### ps — Process Status

```bash
ps aux                           # All processes (BSD syntax)
ps -ef                           # All processes (POSIX syntax)
ps aux | grep python             # Find specific process
ps -p <PID> -o pid,ppid,cmd      # Specific process details
ps -eo pid,ppid,cmd,%mem,%cpu    # Custom columns
ps --sort=-%mem | head -10       # Top 10 by memory
```

### top / htop

```bash
top                              # Interactive process viewer
top -d 1                         # Refresh every 1 second
top -p <PID>                     # Watch specific process
htop                             # Enhanced top (if installed)
htop -p <PID1>,<PID2>            # Specific processes
```

### kill — Signal Processes

```bash
kill <PID>                       # SIGTERM (15) — graceful
kill -9 <PID>                    # SIGKILL — force kill
kill -15 <PID>                   # Same as kill
kill -1 <PID>                    # SIGHUP — reload config
killall process_name             # Kill all by name
pkill -f "pattern"               # Kill matching processes
kill -0 <PID>                    # Check if process exists
```

### Background & Foreground

```bash
command &                        # Run in background
jobs                             # List background jobs
bg %1                            # Resume job in background
fg %1                            # Bring to foreground
Ctrl+Z                           # Suspend current job
nohup command &                  # Persist after shell exit
disown %1                        # Detach from shell
```

### System Resources

```bash
free -h                          # Memory usage
df -h                            # Disk space (human-readable)
df -i                            # Inode usage
du -sh *                         # Directory sizes
uptime                           # System uptime and load
cat /proc/cpuinfo                # CPU info
lscpu                            # CPU architecture
lsmem                            # Memory info
```

## Networking

### curl

```bash
curl https://example.com         # GET request
curl -o file.html URL            # Save to file
curl -O https://example.com/file # Save with remote filename
curl -L URL                      # Follow redirects
curl -I URL                      # Headers only (HEAD)
curl -s URL                      # Silent (no progress)
curl -v URL                      # Verbose
curl -X POST -d "key=value" URL  # POST form data
curl -X POST -H "Content-Type: application/json" \
     -d '{"key":"value"}' URL    # POST JSON
curl -H "Authorization: Bearer TOKEN" URL
curl -u user:pass URL            # Basic auth
curl -F "file=@local.txt" URL    # Upload file
curl -x http://proxy:8080 URL    # Via proxy
curl --connect-timeout 5 URL     # Timeout
```

### wget

```bash
wget https://example.com/file    # Download
wget -O output.html URL          # Rename output
wget -q URL                      # Quiet
wget -c URL                      # Resume interrupted download
wget --limit-rate=1m URL         # Throttle download
wget -r -l 2 URL                 # Recursive (depth 2)
wget -mirror URL                 # Mirror site
wget --no-check-certificate URL  # Skip SSL verify
```

### ssh

```bash
ssh user@host                    # Connect
ssh -p 2222 user@host            # Custom port
ssh -i key.pem user@host         # With identity file
ssh -v user@host                 # Verbose
ssh -L 8080:localhost:80 host    # Local port forwarding
ssh -R 3000:localhost:22 host    # Remote port forwarding
ssh -D 8080 host                 # SOCKS proxy
ssh-copy-id user@host            # Copy public key
ssh -o StrictHostKeyChecking=no user@host  # Skip host key check
```

### scp — Secure Copy

```bash
scp file.txt user@host:/path/    # Upload
scp user@host:/path/file.txt .   # Download
scp -r dir/ user@host:/path/     # Upload directory
scp -P 2222 file.txt user@host:/path/  # Custom port
scp -i key.pem file.txt user@host:/path/
```

### rsync

```bash
rsync -avz src/ user@host:dst/   # Sync (archive, compress, verbose)
rsync -avz --progress src/ dst/  # With progress
rsync -avz --delete src/ dst/    # Delete files in dst not in src
rsync -avz --dry-run src/ dst/   # Preview only
rsync -avz -e ssh src/ dst/      # Via SSH
rsync -avz --exclude="*.pyc" src/ dst/  # Exclude patterns
rsync -avz --partial --progress src/ dst/  # Resume partial
```

### Network Diagnostics

```bash
ss -tuln                         # Listening ports (TCP/UDP)
ss -tunp                         # All connections with process info
netstat -tuln                    # Legacy equivalent
netstat -anp | grep ESTABLISHED

ip addr show                     # Interface addresses
ip addr show eth0                # Specific interface
ip link show                     # Link status
ip route show                    # Routing table

ping host                        # Ping
ping -c 4 host                   # Count limited
traceroute host                  # Trace route
mtr host                         # My traceroute (better)
dig example.com                  # DNS lookup
nslookup example.com             # DNS lookup (simple)
host example.com                 # DNS lookup
whois example.com                # WHOIS info

nc -zv host port                 # Port check
nc -l 8080                       # Listen on port
```

## Disk Operations

```bash
df -h                            # Disk usage (human-readable)
df -h /                          # Specific filesystem
df -i                            # Inode usage
du -sh /path                     # Directory size
du -sh *                         # All items in dir
du -h --max-depth=1              # Summary one level
du -ah /path                     # All files individually
lsblk                            # Block devices
lsblk -f                         # With filesystem info
mount                            # Mounted filesystems
mount /dev/sdb1 /mnt             # Mount device
umount /mnt                      # Unmount
fdisk -l                         # Disk partitions
```

## Archiving

### tar

```bash
# Create
tar -czf archive.tar.gz file1 dir/     # Compressed (gzip)
tar -cjf archive.tar.bz2 file1 dir/    # Compressed (bzip2)
tar -cf archive.tar file1 dir/         # Uncompressed

# Extract
tar -xzf archive.tar.gz                # Extract (gzip)
tar -xzf archive.tar.gz -C /path/      # Extract to directory
tar -xzf archive.tar.gz file1          # Extract specific file
tar -tzf archive.tar.gz                # List contents

# Update
tar -uf archive.tar newfile             # Add file to uncompressed tar
```

### zip / unzip

```bash
zip archive.zip file1 file2 dir/        # Create
zip -r archive.zip dir/                 # Create from directory
zip -e archive.zip                      # Encrypt (prompts for password)
unzip archive.zip                       # Extract
unzip -l archive.zip                    # List contents
unzip -o archive.zip                    # Overwrite existing
unzip -d /path/ archive.zip             # Extract to directory
```

## Systemd

```bash
systemctl start service                  # Start service
systemctl stop service                   # Stop service
systemctl restart service                # Restart service
systemctl reload service                 # Reload config (no restart)
systemctl enable service                 # Enable on boot
systemctl disable service                # Disable on boot
systemctl status service                 # Check status
systemctl is-active service              # Check if running
systemctl is-enabled service             # Check if enabled
systemctl list-units --type=service      # List all services
systemctl list-units --failed            # Failed services
systemctl --failed                       # Same
journalctl -u service                    # Service logs
journalctl -u service -f                 # Follow logs
journalctl --since "1 hour ago"          # Recent logs
journalctl --since today                 # Today's logs
journalctl --disk-usage                  # Log disk usage
```

## Cron

### Syntax

```
# ┌────────── minute (0-59)
# │ ┌──────── hour (0-23)
# │ │ ┌────── day of month (1-31)
# │ │ │ ┌──── month (1-12)
# │ │ │ │ ┌── day of week (0-6, 0=Sunday)
# * * * * * command
```

### Examples

```bash
crontab -e                          # Edit crontab
crontab -l                          # List crontab
crontab -r                          # Remove crontab

# Schedule examples:
* * * * * /path/command             # Every minute
0 * * * * /path/command             # Every hour
0 */2 * * * /path/command           # Every 2 hours
0 0 * * * /path/command             # Daily at midnight
0 6 * * 1 /path/command             # Every Monday at 6 AM
0 0 1 * * /path/command             # 1st of every month
0 0 * * 0 /path/command             # Every Sunday at midnight
*/15 * * * * /path/command          # Every 15 minutes
0 9-17 * * 1-5 /path/command        # Weekdays, 9 AM - 5 PM

# Redirect output
0 0 * * * /path/command > /var/log/cron.log 2>&1
```

## Quick Reference

### Keyboard Shortcuts

- `Ctrl+C` — Interrupt (SIGINT)
- `Ctrl+D` — EOF / exit
- `Ctrl+Z` — Suspend
- `Ctrl+L` — Clear screen
- `Ctrl+A` — Beginning of line
- `Ctrl+E` — End of line
- `Ctrl+R` — Reverse search history
- `Ctrl+U` — Cut to beginning of line
- `Ctrl+K` — Cut to end of line
- `Ctrl+W` — Delete word before cursor
- `Alt+. ` — Insert last argument from previous command
- `!!` — Repeat last command
- `!$` — Last argument of previous command

### Useful One-Liners

```bash
# Find and replace in files
find . -name "*.txt" -exec sed -i 's/old/new/g' {} +

# Disk usage top 10
du -sh * | sort -rh | head -10

# Port in use
lsof -i :8080

# Count files by extension
find . -type f | sed 's/.*\.//' | sort | uniq -c | sort -rn

# Batch rename
for f in *.jpg; do mv "$f" "${f%.jpg}_backup.jpg"; done

# Watch command output
watch -n 1 'docker ps'
```
