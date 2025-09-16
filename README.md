# gotify Installation Guide

gotify is a free and open-source simple server for sending and receiving messages. Gotify provides self-hosted push notifications with a simple API, serving as a privacy-focused alternative to Firebase Cloud Messaging or Pushover

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 128MB minimum
  - Storage: 100MB for application
  - Network: HTTP/HTTPS and WebSocket
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default gotify port)
  - WebSocket support required
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install gotify
sudo dnf install -y gotify-server

# Enable and start service
sudo systemctl enable --now gotify

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
gotify-server version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install gotify
sudo apt install -y gotify-server

# Enable and start service
sudo systemctl enable --now gotify

# Configure firewall
sudo ufw allow 80

# Verify installation
gotify-server version
```

### Arch Linux

```bash
# Install gotify
sudo pacman -S gotify-server

# Enable and start service
sudo systemctl enable --now gotify

# Verify installation
gotify-server version
```

### Alpine Linux

```bash
# Install gotify
apk add --no-cache gotify-server

# Enable and start service
rc-update add gotify default
rc-service gotify start

# Verify installation
gotify-server version
```

### openSUSE/SLES

```bash
# Install gotify
sudo zypper install -y gotify-server

# Enable and start service
sudo systemctl enable --now gotify

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
gotify-server version
```

### macOS

```bash
# Using Homebrew
brew install gotify-server

# Start service
brew services start gotify-server

# Verify installation
gotify-server version
```

### FreeBSD

```bash
# Using pkg
pkg install gotify-server

# Enable in rc.conf
echo 'gotify_enable="YES"' >> /etc/rc.conf

# Start service
service gotify start

# Verify installation
gotify-server version
```

### Windows

```bash
# Using Chocolatey
choco install gotify-server

# Or using Scoop
scoop install gotify-server

# Verify installation
gotify-server version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/gotify-server

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
gotify-server version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable gotify

# Start service
sudo systemctl start gotify

# Stop service
sudo systemctl stop gotify

# Restart service
sudo systemctl restart gotify

# Check status
sudo systemctl status gotify

# View logs
sudo journalctl -u gotify -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add gotify default

# Start service
rc-service gotify start

# Stop service
rc-service gotify stop

# Restart service
rc-service gotify restart

# Check status
rc-service gotify status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'gotify_enable="YES"' >> /etc/rc.conf

# Start service
service gotify start

# Stop service
service gotify stop

# Restart service
service gotify restart

# Check status
service gotify status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start gotify-server
brew services stop gotify-server
brew services restart gotify-server

# Check status
brew services list | grep gotify-server
```

### Windows Service Manager

```powershell
# Start service
net start gotify

# Stop service
net stop gotify

# Using PowerShell
Start-Service gotify
Stop-Service gotify
Restart-Service gotify

# Check status
Get-Service gotify
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream gotify-server_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name gotify-server.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name gotify-server.example.com;

    ssl_certificate /etc/ssl/certs/gotify-server.example.com.crt;
    ssl_certificate_key /etc/ssl/private/gotify-server.example.com.key;

    location / {
        proxy_pass http://gotify-server_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName gotify-server.example.com
    Redirect permanent / https://gotify-server.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName gotify-server.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/gotify-server.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/gotify-server.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend gotify-server_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/gotify-server.pem
    redirect scheme https if !{ ssl_fc }
    default_backend gotify-server_backend

backend gotify-server_backend
    balance roundrobin
    server gotify-server1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R gotify-server:gotify-server /etc/gotify-server
sudo chmod 750 /etc/gotify-server

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status gotify

# View logs
sudo journalctl -u gotify -f

# Monitor resource usage
top -p $(pgrep gotify-server)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/gotify-server"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/gotify-server-backup-$DATE.tar.gz" /etc/gotify-server /var/lib/gotify-server

echo "Backup completed: $BACKUP_DIR/gotify-server-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop gotify

# Restore from backup
tar -xzf /backup/gotify-server/gotify-server-backup-*.tar.gz -C /

# Start service
sudo systemctl start gotify
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u gotify -n 100
sudo tail -f /var/log/gotify-server/gotify-server.log

# Check configuration
gotify-server version

# Check permissions
ls -la /etc/gotify-server
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep gotify-server)

# Check disk I/O
iotop -p $(pgrep gotify-server)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  gotify-server:
    image: gotify-server:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/gotify-server
      - ./data:/var/lib/gotify-server
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update gotify-server

# Debian/Ubuntu
sudo apt update && sudo apt upgrade gotify-server

# Arch Linux
sudo pacman -Syu gotify-server

# Alpine Linux
apk update && apk upgrade gotify-server

# openSUSE
sudo zypper update gotify-server

# FreeBSD
pkg update && pkg upgrade gotify-server

# Always backup before updates
tar -czf /backup/gotify-server-pre-update-$(date +%Y%m%d).tar.gz /etc/gotify-server

# Restart after updates
sudo systemctl restart gotify
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/gotify-server

# Clean old logs
find /var/log/gotify-server -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/gotify-server
```

## Additional Resources

- Official Documentation: https://docs.gotify-server.org/
- GitHub Repository: https://github.com/gotify-server/gotify-server
- Community Forum: https://forum.gotify-server.org/
- Best Practices Guide: https://docs.gotify-server.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
