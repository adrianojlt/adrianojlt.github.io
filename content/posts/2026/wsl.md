+++
title = 'Docker with WSL'
date = '2026-03-15'
draft = false
tags = ['devops']
#author = 'adriano'
header_image = "/images/wsl.png"
+++

## Using WSL and Docker for Remote Development

I own an HP ZBook with 64 Gb of RAM running Windows 11. With specs like that, it is practically begging to have Linux installed so I can go crazy testing docker containers. Having 64 Gb of memory gives me more than enough breathing room to run a massive stack of services without breaking a sweat.

There was just one catch: I actually need my Windows installation for me and family daily tasks. I do not want to switched entirely to Linux. That is where the **Windows Subsystem for Linux (WSL)** comes in. It allows me to keep my Windows environment while still playing around with Docker in a native-feeling Linux environment.

By pairing Docker Desktop with WSL, I can get exactly the setup I need. However, getting everything accessible via SSH from another computer requires a few extra steps. Here i will share those configuration:

### Install and Info
```Bash
# Enable WSL feature
wsl --install -d Ubuntu

# Check installed distros
wsl --list --verbose

# Set default WSL version
wsl --set-default-version <version#>

# Set default distribution
wsl --set-default <distribution>

# Terminate a distribution
wsl --terminate <distribution>

# Shutdown all WSL instances
wsl --shutdown

# Stop distribution   
wsl -t

# Update WSL
wsl --update

# Check WSL status
wsl --status
```

### WSL2 Memory limit configuration

By default, WSL2 uses **50% of host RAM** (~32GB on my machine). To use more:
 - Open File Explorer -> navigate to `%USERPROFILE%`
 - Create or edit the file: `.wslconfig`
 - Add the following:

```
[wsl2]
memory=48GB              # Max RAM for WSL2 (leave 16GB for Windows)
processors=12            # Optional: limit CPU cores
swap=4GB                 # Optional: reduce default 8GB swap
localhostForwarding=true # Required for SSH port forwarding
```

Restart WSL to apply changes:
```Bash
wsl --shutdown
wsl
```

Check memory usage:
```Bash
# Should show ~48GB total memory
free -h
```

Install Docker Desktop with WSL2 Integration

- Download Docker Desktop for Windows from docker.com
- During installation, enable **"Use WSL2 instead of Hyper-V"**
- After install, open Docker **Desktop -> Settings -> Resources -> WSL Integration**:
    - Enable integration with Ubuntu
    - Enable "Start Docker Desktop when you log in" (optional)
- Verify in WSL:

```Bash
docker --version
docker run hello-world
```

> 💡 Store projects in WSL filesystem (~/projects), NOT /mnt/c/. Performance is 10–20× faster for I/O-heavy operations.

### Enable Remote SSH Access to WSL2

By default, WSL2 is only accessible locally. Here's how to SSH into it from any machine on your LAN.
Install & Configure SSH Server in WSL. Execute the following in your WSL terminal:

```Bash
# Install OpenSSH server
sudo apt update && sudo apt install openssh-server -y

# Generate host keys (if missing)
sudo ssh-keygen -A

# Enable password authentication
sudo sed -i 's/#\?PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Set a password for your WSL user
passwd

# Start SSH service
sudo service ssh start

# Verify SSH is listening
ss -tuln | grep ':22'
# Should show: 127.0.0.1:22 or 0.0.0.0:22
```

Configure Windows Port Forwarding. Run in **PowerShell(Admin)**:

```powershell
# Forward Windows port 2222 → WSL port 22 (via localhost)
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=22 connectaddress=127.0.0.1

# Verify the rule
netsh interface portproxy show all
# Should show: 0.0.0.0:2222 → 127.0.0.1:22
```

> 💡 Why 127.0.0.1? This IP never changes, unlike WSL's dynamic VM IP. This makes the rule persistent across reboots.

Configure Windows Firewall. Also in **PowerShell(Admin)**:

```bash
ipconfig | findstr /i "IPv4"
# Note the IP (e.g., 192.168.1.2)
```

Connect from Another Machine. From your Mac, Linux, or another PC:
```bash
ssh -p 2222 your-wsl-username@192.168.1.2
# Example: ssh -p 2222 adriano@192.168.1.2
```

Set up SSH key authentication if you want to avoid password prompts:

```bash
# On client machine
ssh-keygen -t ed25519
ssh-copy-id -p 2222 adriano@192.168.1.2
```

### Keep WSL Running 24/7 (Prevent Auto-Shutdown)

WSL automatically shuts down when idle — which kills your SSH sessions. Here's how to keep it alive.

**Option A: Simple Background Process (Recommended)**

In WSL, run once:

```bash
nohup sleep infinity </dev/null >/dev/null 2>&1 &
echo $! > ~/.wsl_keepalive.pid
```

This keeps WSL running indefinitely. To make it persistent, add to `~/.profile`:
```bash
# Keep WSL alive in background
if [ -z "$WSL_KEEPALIVE_PID" ]; then
  nohup sleep infinity </dev/null >/dev/null 2>&1 &
  echo $! > ~/.wsl_keepalive.pid
  export WSL_KEEPALIVE_PID=$!
fi
```

**Option B: Windows Task Scheduler (Auto-start on Boot)**

Run in **PowerShell (Admin)**:
```powershell
$action = New-ScheduledTaskAction -Execute "wsl" -Argument "-u $env:USERNAME -- nohup sleep infinity >/dev/null 2>&1 &"
$trigger = New-ScheduledTaskTrigger -AtLogOn
$principal = New-ScheduledTaskPrincipal -UserId $env:USERNAME -LogonType Interactive
Register-ScheduledTask -TaskName "WSL-KeepAlive" -Action $action -Trigger $trigger -Principal $principal
```









