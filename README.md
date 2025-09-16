# blender Installation Guide

blender is a free and open-source 3D creation suite. Blender provides 3D creation suite

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
  - CPU: 4+ cores
  - RAM: 8GB minimum
  - Storage: 50GB for projects
  - Network: GUI application
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default blender port)
  - Network render
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

# Install blender
sudo dnf install -y blender

# Enable and start service
sudo systemctl enable --now blender

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
blender --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install blender
sudo apt install -y blender

# Enable and start service
sudo systemctl enable --now blender

# Configure firewall
sudo ufw allow N/A

# Verify installation
blender --version
```

### Arch Linux

```bash
# Install blender
sudo pacman -S blender

# Enable and start service
sudo systemctl enable --now blender

# Verify installation
blender --version
```

### Alpine Linux

```bash
# Install blender
apk add --no-cache blender

# Enable and start service
rc-update add blender default
rc-service blender start

# Verify installation
blender --version
```

### openSUSE/SLES

```bash
# Install blender
sudo zypper install -y blender

# Enable and start service
sudo systemctl enable --now blender

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
blender --version
```

### macOS

```bash
# Using Homebrew
brew install blender

# Start service
brew services start blender

# Verify installation
blender --version
```

### FreeBSD

```bash
# Using pkg
pkg install blender

# Enable in rc.conf
echo 'blender_enable="YES"' >> /etc/rc.conf

# Start service
service blender start

# Verify installation
blender --version
```

### Windows

```bash
# Using Chocolatey
choco install blender

# Or using Scoop
scoop install blender

# Verify installation
blender --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/blender

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
blender --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable blender

# Start service
sudo systemctl start blender

# Stop service
sudo systemctl stop blender

# Restart service
sudo systemctl restart blender

# Check status
sudo systemctl status blender

# View logs
sudo journalctl -u blender -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add blender default

# Start service
rc-service blender start

# Stop service
rc-service blender stop

# Restart service
rc-service blender restart

# Check status
rc-service blender status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'blender_enable="YES"' >> /etc/rc.conf

# Start service
service blender start

# Stop service
service blender stop

# Restart service
service blender restart

# Check status
service blender status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start blender
brew services stop blender
brew services restart blender

# Check status
brew services list | grep blender
```

### Windows Service Manager

```powershell
# Start service
net start blender

# Stop service
net stop blender

# Using PowerShell
Start-Service blender
Stop-Service blender
Restart-Service blender

# Check status
Get-Service blender
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream blender_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name blender.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name blender.example.com;

    ssl_certificate /etc/ssl/certs/blender.example.com.crt;
    ssl_certificate_key /etc/ssl/private/blender.example.com.key;

    location / {
        proxy_pass http://blender_backend;
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
    ServerName blender.example.com
    Redirect permanent / https://blender.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName blender.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/blender.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/blender.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend blender_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/blender.pem
    redirect scheme https if !{ ssl_fc }
    default_backend blender_backend

backend blender_backend
    balance roundrobin
    server blender1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R blender:blender /etc/blender
sudo chmod 750 /etc/blender

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
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
sudo systemctl status blender

# View logs
sudo journalctl -u blender -f

# Monitor resource usage
top -p $(pgrep blender)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/blender"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/blender-backup-$DATE.tar.gz" /etc/blender /var/lib/blender

echo "Backup completed: $BACKUP_DIR/blender-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop blender

# Restore from backup
tar -xzf /backup/blender/blender-backup-*.tar.gz -C /

# Start service
sudo systemctl start blender
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u blender -n 100
sudo tail -f /var/log/blender/blender.log

# Check configuration
blender --version

# Check permissions
ls -la /etc/blender
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep blender)

# Check disk I/O
iotop -p $(pgrep blender)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  blender:
    image: blender:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/blender
      - ./data:/var/lib/blender
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update blender

# Debian/Ubuntu
sudo apt update && sudo apt upgrade blender

# Arch Linux
sudo pacman -Syu blender

# Alpine Linux
apk update && apk upgrade blender

# openSUSE
sudo zypper update blender

# FreeBSD
pkg update && pkg upgrade blender

# Always backup before updates
tar -czf /backup/blender-pre-update-$(date +%Y%m%d).tar.gz /etc/blender

# Restart after updates
sudo systemctl restart blender
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/blender

# Clean old logs
find /var/log/blender -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/blender
```

## Additional Resources

- Official Documentation: https://docs.blender.org/
- GitHub Repository: https://github.com/blender/blender
- Community Forum: https://forum.blender.org/
- Best Practices Guide: https://docs.blender.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
