# node-exporter Installation Guide

node-exporter is a free and open-source Prometheus exporter for hardware and OS metrics. Node Exporter exposes hardware and kernel-related metrics for *NIX systems, essential for system monitoring with Prometheus

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
  - Storage: 50MB for installation
  - Network: HTTP for metrics endpoint
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9100 (default node-exporter port)
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

# Install node-exporter
sudo dnf install -y prometheus-node-exporter

# Enable and start service
sudo systemctl enable --now node_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9100/tcp
sudo firewall-cmd --reload

# Verify installation
node_exporter --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install node-exporter
sudo apt install -y prometheus-node-exporter

# Enable and start service
sudo systemctl enable --now node_exporter

# Configure firewall
sudo ufw allow 9100

# Verify installation
node_exporter --version
```

### Arch Linux

```bash
# Install node-exporter
sudo pacman -S prometheus-node-exporter

# Enable and start service
sudo systemctl enable --now node_exporter

# Verify installation
node_exporter --version
```

### Alpine Linux

```bash
# Install node-exporter
apk add --no-cache prometheus-node-exporter

# Enable and start service
rc-update add node_exporter default
rc-service node_exporter start

# Verify installation
node_exporter --version
```

### openSUSE/SLES

```bash
# Install node-exporter
sudo zypper install -y prometheus-node-exporter

# Enable and start service
sudo systemctl enable --now node_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9100/tcp
sudo firewall-cmd --reload

# Verify installation
node_exporter --version
```

### macOS

```bash
# Using Homebrew
brew install prometheus-node-exporter

# Start service
brew services start prometheus-node-exporter

# Verify installation
node_exporter --version
```

### FreeBSD

```bash
# Using pkg
pkg install prometheus-node-exporter

# Enable in rc.conf
echo 'node_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service node_exporter start

# Verify installation
node_exporter --version
```

### Windows

```bash
# Using Chocolatey
choco install prometheus-node-exporter

# Or using Scoop
scoop install prometheus-node-exporter

# Verify installation
node_exporter --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/prometheus-node-exporter

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
node_exporter --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable node_exporter

# Start service
sudo systemctl start node_exporter

# Stop service
sudo systemctl stop node_exporter

# Restart service
sudo systemctl restart node_exporter

# Check status
sudo systemctl status node_exporter

# View logs
sudo journalctl -u node_exporter -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add node_exporter default

# Start service
rc-service node_exporter start

# Stop service
rc-service node_exporter stop

# Restart service
rc-service node_exporter restart

# Check status
rc-service node_exporter status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'node_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service node_exporter start

# Stop service
service node_exporter stop

# Restart service
service node_exporter restart

# Check status
service node_exporter status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start prometheus-node-exporter
brew services stop prometheus-node-exporter
brew services restart prometheus-node-exporter

# Check status
brew services list | grep prometheus-node-exporter
```

### Windows Service Manager

```powershell
# Start service
net start node_exporter

# Stop service
net stop node_exporter

# Using PowerShell
Start-Service node_exporter
Stop-Service node_exporter
Restart-Service node_exporter

# Check status
Get-Service node_exporter
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream prometheus-node-exporter_backend {
    server 127.0.0.1:9100;
}

server {
    listen 80;
    server_name prometheus-node-exporter.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name prometheus-node-exporter.example.com;

    ssl_certificate /etc/ssl/certs/prometheus-node-exporter.example.com.crt;
    ssl_certificate_key /etc/ssl/private/prometheus-node-exporter.example.com.key;

    location / {
        proxy_pass http://prometheus-node-exporter_backend;
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
    ServerName prometheus-node-exporter.example.com
    Redirect permanent / https://prometheus-node-exporter.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName prometheus-node-exporter.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/prometheus-node-exporter.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/prometheus-node-exporter.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9100/
    ProxyPassReverse / http://127.0.0.1:9100/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend prometheus-node-exporter_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/prometheus-node-exporter.pem
    redirect scheme https if !{ ssl_fc }
    default_backend prometheus-node-exporter_backend

backend prometheus-node-exporter_backend
    balance roundrobin
    server prometheus-node-exporter1 127.0.0.1:9100 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R prometheus-node-exporter:prometheus-node-exporter /etc/prometheus-node-exporter
sudo chmod 750 /etc/prometheus-node-exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9100/tcp
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
sudo systemctl status node_exporter

# View logs
sudo journalctl -u node_exporter -f

# Monitor resource usage
top -p $(pgrep prometheus-node-exporter)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/prometheus-node-exporter"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/prometheus-node-exporter-backup-$DATE.tar.gz" /etc/prometheus-node-exporter /var/lib/prometheus-node-exporter

echo "Backup completed: $BACKUP_DIR/prometheus-node-exporter-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop node_exporter

# Restore from backup
tar -xzf /backup/prometheus-node-exporter/prometheus-node-exporter-backup-*.tar.gz -C /

# Start service
sudo systemctl start node_exporter
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u node_exporter -n 100
sudo tail -f /var/log/prometheus-node-exporter/prometheus-node-exporter.log

# Check configuration
node_exporter --version

# Check permissions
ls -la /etc/prometheus-node-exporter
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9100

# Test connectivity
telnet localhost 9100

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep prometheus-node-exporter)

# Check disk I/O
iotop -p $(pgrep prometheus-node-exporter)

# Check connections
ss -an | grep 9100
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  prometheus-node-exporter:
    image: prometheus-node-exporter:latest
    ports:
      - "9100:9100"
    volumes:
      - ./config:/etc/prometheus-node-exporter
      - ./data:/var/lib/prometheus-node-exporter
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update prometheus-node-exporter

# Debian/Ubuntu
sudo apt update && sudo apt upgrade prometheus-node-exporter

# Arch Linux
sudo pacman -Syu prometheus-node-exporter

# Alpine Linux
apk update && apk upgrade prometheus-node-exporter

# openSUSE
sudo zypper update prometheus-node-exporter

# FreeBSD
pkg update && pkg upgrade prometheus-node-exporter

# Always backup before updates
tar -czf /backup/prometheus-node-exporter-pre-update-$(date +%Y%m%d).tar.gz /etc/prometheus-node-exporter

# Restart after updates
sudo systemctl restart node_exporter
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/prometheus-node-exporter

# Clean old logs
find /var/log/prometheus-node-exporter -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/prometheus-node-exporter
```

## Additional Resources

- Official Documentation: https://docs.prometheus-node-exporter.org/
- GitHub Repository: https://github.com/prometheus-node-exporter/prometheus-node-exporter
- Community Forum: https://forum.prometheus-node-exporter.org/
- Best Practices Guide: https://docs.prometheus-node-exporter.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
