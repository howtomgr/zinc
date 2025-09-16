# zinc Installation Guide

zinc is a free and open-source search engine. Zinc provides lightweight alternative to Elasticsearch

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
  - RAM: 512MB minimum
  - Storage: 10GB for indices
  - Network: HTTP/REST API
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 4080 (default zinc port)
  - None
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

# Install zinc
sudo dnf install -y zinc

# Enable and start service
sudo systemctl enable --now zinc

# Configure firewall
sudo firewall-cmd --permanent --add-port=4080/tcp
sudo firewall-cmd --reload

# Verify installation
zinc --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install zinc
sudo apt install -y zinc

# Enable and start service
sudo systemctl enable --now zinc

# Configure firewall
sudo ufw allow 4080

# Verify installation
zinc --version
```

### Arch Linux

```bash
# Install zinc
sudo pacman -S zinc

# Enable and start service
sudo systemctl enable --now zinc

# Verify installation
zinc --version
```

### Alpine Linux

```bash
# Install zinc
apk add --no-cache zinc

# Enable and start service
rc-update add zinc default
rc-service zinc start

# Verify installation
zinc --version
```

### openSUSE/SLES

```bash
# Install zinc
sudo zypper install -y zinc

# Enable and start service
sudo systemctl enable --now zinc

# Configure firewall
sudo firewall-cmd --permanent --add-port=4080/tcp
sudo firewall-cmd --reload

# Verify installation
zinc --version
```

### macOS

```bash
# Using Homebrew
brew install zinc

# Start service
brew services start zinc

# Verify installation
zinc --version
```

### FreeBSD

```bash
# Using pkg
pkg install zinc

# Enable in rc.conf
echo 'zinc_enable="YES"' >> /etc/rc.conf

# Start service
service zinc start

# Verify installation
zinc --version
```

### Windows

```bash
# Using Chocolatey
choco install zinc

# Or using Scoop
scoop install zinc

# Verify installation
zinc --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/zinc

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
zinc --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable zinc

# Start service
sudo systemctl start zinc

# Stop service
sudo systemctl stop zinc

# Restart service
sudo systemctl restart zinc

# Check status
sudo systemctl status zinc

# View logs
sudo journalctl -u zinc -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add zinc default

# Start service
rc-service zinc start

# Stop service
rc-service zinc stop

# Restart service
rc-service zinc restart

# Check status
rc-service zinc status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'zinc_enable="YES"' >> /etc/rc.conf

# Start service
service zinc start

# Stop service
service zinc stop

# Restart service
service zinc restart

# Check status
service zinc status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start zinc
brew services stop zinc
brew services restart zinc

# Check status
brew services list | grep zinc
```

### Windows Service Manager

```powershell
# Start service
net start zinc

# Stop service
net stop zinc

# Using PowerShell
Start-Service zinc
Stop-Service zinc
Restart-Service zinc

# Check status
Get-Service zinc
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream zinc_backend {
    server 127.0.0.1:4080;
}

server {
    listen 80;
    server_name zinc.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name zinc.example.com;

    ssl_certificate /etc/ssl/certs/zinc.example.com.crt;
    ssl_certificate_key /etc/ssl/private/zinc.example.com.key;

    location / {
        proxy_pass http://zinc_backend;
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
    ServerName zinc.example.com
    Redirect permanent / https://zinc.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName zinc.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/zinc.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/zinc.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:4080/
    ProxyPassReverse / http://127.0.0.1:4080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend zinc_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/zinc.pem
    redirect scheme https if !{ ssl_fc }
    default_backend zinc_backend

backend zinc_backend
    balance roundrobin
    server zinc1 127.0.0.1:4080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R zinc:zinc /etc/zinc
sudo chmod 750 /etc/zinc

# Configure firewall
sudo firewall-cmd --permanent --add-port=4080/tcp
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
sudo systemctl status zinc

# View logs
sudo journalctl -u zinc -f

# Monitor resource usage
top -p $(pgrep zinc)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/zinc"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/zinc-backup-$DATE.tar.gz" /etc/zinc /var/lib/zinc

echo "Backup completed: $BACKUP_DIR/zinc-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop zinc

# Restore from backup
tar -xzf /backup/zinc/zinc-backup-*.tar.gz -C /

# Start service
sudo systemctl start zinc
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u zinc -n 100
sudo tail -f /var/log/zinc/zinc.log

# Check configuration
zinc --version

# Check permissions
ls -la /etc/zinc
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 4080

# Test connectivity
telnet localhost 4080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep zinc)

# Check disk I/O
iotop -p $(pgrep zinc)

# Check connections
ss -an | grep 4080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  zinc:
    image: zinc:latest
    ports:
      - "4080:4080"
    volumes:
      - ./config:/etc/zinc
      - ./data:/var/lib/zinc
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update zinc

# Debian/Ubuntu
sudo apt update && sudo apt upgrade zinc

# Arch Linux
sudo pacman -Syu zinc

# Alpine Linux
apk update && apk upgrade zinc

# openSUSE
sudo zypper update zinc

# FreeBSD
pkg update && pkg upgrade zinc

# Always backup before updates
tar -czf /backup/zinc-pre-update-$(date +%Y%m%d).tar.gz /etc/zinc

# Restart after updates
sudo systemctl restart zinc
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/zinc

# Clean old logs
find /var/log/zinc -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/zinc
```

## Additional Resources

- Official Documentation: https://docs.zinc.org/
- GitHub Repository: https://github.com/zinc/zinc
- Community Forum: https://forum.zinc.org/
- Best Practices Guide: https://docs.zinc.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
