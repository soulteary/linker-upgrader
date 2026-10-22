# 🚀 Linker Universal Program Upgrade System

A simple, secure, and configurable web-based program upgrade system that supports automatic deployment and service management for multiple file formats.

English | [中文](README_zhCN.md)

![](.github/banner.jpg)

## ✨ Features

- 🌐 **Web Interface**: User-friendly interface with drag-and-drop upload support
- 📦 **Multi-format Support**: `.tar.gz`, `.zip`, `.gz`, executable files
- 🔧 **Service Management**: Automatic stop/start of systemd services
- 💾 **Smart Backup**: Automatic backup of existing programs with rollback support
- 🔐 **Permission Management**: Automatic file and executable permission setup
- ⚙️ **Highly Configurable**: Support for configuration files, environment variables, and command-line parameters
- 🧹 **Auto Cleanup**: Scheduled cleanup of temporary files
- 📊 **Real-time Logging**: Detailed upgrade process logs display
- 🛡️ **Security Checks**: File type validation and size limits

## 🎯 Use Cases

- **Industrial Control System Upgrades**: Remote upgrade of industrial control programs
- **Edge Device Deployment**: Automatic updates for IoT device programs
- **Server Application Upgrades**: Hot updates for production environment applications
- **CI/CD Deployment**: Continuous integration/continuous deployment pipelines
- **Embedded Systems**: Embedded device program upgrades

## 📋 System Requirements

- **Operating System**: Linux (Ubuntu 18.04+, CentOS 7+, other distributions)
- **Go Version**: 1.27.3 or higher
- **System Permissions**: Recommended to run with root privileges (for service management)
- **System Tools**: `tar`, `unzip`, `systemctl` (optional)

## 🚀 Quick Start

### 1. Download and Installation

```bash
# Clone repository
git clone https://github.com/linker-bot/linker-upgrader.git
cd linker-upgrader

# Compile program
go build -o linker-upgrader main.go

# TBD
# Or download pre-compiled binary
wget https://github.com/linker-bot/linker-upgrader/releases/latest/download/linker-upgrader-linux-amd64
chmod +x linker-upgrader-linux-amd64
```

### 2. Generate Configuration File

```bash
# Generate default configuration file
./linker-upgrader -gen-config

# Configuration file will be saved to config.json
```

### 3. Start Service

```bash
# Start with default configuration
./linker-upgrader

# Start with specified port
./linker-upgrader -port 8080

# Start with specified configuration file
./linker-upgrader -config /path/to/config.json
```

### 4. Access Web Interface

Open browser and visit: `http://localhost:8080`

## ⚙️ Configuration Details

### Configuration File (config.json)

```json
{
  "upload_dir": "./uploads",                    // Upload temporary directory
  "target_dir": "/opt/myapp",                   // Target program directory  
  "backup_dir": "/opt/myapp/backup",            // Backup directory
  "service_name": "myapp",                      // systemd service name
  "port": ":8080",                             // Service port
  "max_file_size": 100,                        // Maximum file size (MB)
  "enable_backup": true,                       // Enable backup functionality
  "enable_service": true,                      // Enable service management
  "enable_cleanup": true,                      // Enable file cleanup
  "cleanup_interval": 1,                       // Cleanup interval (hours)
  "file_max_age": 24,                         // File retention time (hours)
  "dir_permission": "0755",                    // Directory permissions
  "file_permission": "0644",                   // File permissions
  "exec_permission": "0755",                   // Executable file permissions
  "title": "🚀 Linker - Program Upgrade System", // Page title
  "description": "Multi-format program upgrade system", // Page description
  "accept_types": [                           // Supported file types
    ".tar.gz", ".zip", ".gz",
    "application/x-executable",
    "application/octet-stream"
  ]
}
```

### Environment Variable Configuration

```bash
# Basic configuration
export TARGET_DIR="/opt/production"
export SERVICE_NAME="prod-service"
export PORT="9090"
export MAX_FILE_SIZE="200"

# Feature switches
export ENABLE_BACKUP="true"
export ENABLE_SERVICE="true"
export ENABLE_CLEANUP="false"

# Interface customization
export TITLE="Production Upgrade System"
```

### Command Line Parameters

```bash
./linker-upgrader -h
  -config string
        Configuration file path (default "./config.json")
  -gen-config
        Generate default configuration file and exit
  -port string
        Service port (overrides configuration file)
  -service string
        Service name (overrides configuration file)
  -target string
        Target directory (overrides configuration file)
```

## 🔄 Upgrade Process

The system automatically executes the following steps based on configuration:

1. **📤 File Upload**: Validate file type and size
2. **⏹️ Stop Service**: Gracefully stop the currently running service (optional)
3. **💾 Backup Program**: Backup existing program to backup directory (optional)
4. **📦 Extract and Deploy**: Automatically extract or copy based on file type
5. **🔐 Set Permissions**: Automatically set directory and file permissions
6. **▶️ Start Service**: Start service and verify status (optional)
7. **📊 Status Report**: Display detailed upgrade logs

## 🛠️ Advanced Usage

### Systemd Service Configuration

Create systemd service file `/etc/systemd/system/linker-upgrader.service`:

```ini
[Unit]
Description=Program Upgrade System
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/linker-upgrader
ExecStart=/opt/linker-upgrader/linker-upgrader -config /etc/linker-upgrader/config.json
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Enable service:

```bash
sudo systemctl enable linker-upgrader
sudo systemctl start linker-upgrader
```

### Docker Deployment

#### Using Pre-built Images

```bash
# Pull the latest version
docker pull ghcr.io/linker-bot/linker-upgrader:latest

# Run container
docker run -d -p 6110:6110 \
  --name linker-upgrader \
  -v /opt/myapp:/opt/myapp \
  -v /etc/linker-upgrader:/etc/linker-upgrader \
  -v ./config.json:/etc/linker-upgrader/config.json \
  ghcr.io/linker-bot/linker-upgrader:latest \
  -config /etc/linker-upgrader/config.json
```

#### Using Docker Compose

```yaml
services:
  linker-upgrader:
    image: ghcr.io/linker-bot/linker-upgrader:latest
    container_name: linker-upgrader
    ports:
      - "6110:6110"
    volumes:
      - /opt/myapp:/opt/myapp
      - ./config.json:/etc/linker-upgrader/config.json
    command: ["-config", "/etc/linker-upgrader/config.json"]
    restart: unless-stopped
```

### Nginx Reverse Proxy

```nginx
server {
    listen 80;
    server_name upgrade.example.com;
    
    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        
        # Support large file uploads
        client_max_body_size 200M;
        proxy_read_timeout 300s;
        proxy_send_timeout 300s;
    }
}
```

## 🔒 Security Considerations

- **Permission Management**: Recommended to run with minimal privilege principle
- **Network Security**: Use HTTPS and authentication in production environments
- **File Validation**: Verify file integrity and source before upload
- **Backup Strategy**: Regularly clean backup files to avoid disk space shortage
- **Log Monitoring**: Monitor upgrade logs to detect anomalies promptly

## 📚 API Documentation

### Web Interface

- `GET /` - Main page displaying upload form
- `POST /upload` - Handle file upload and program upgrade

### Response Format

Successful response displays a page containing:
- Upgrade status (success/failure)
- Detailed operation logs
- Current configuration information

## 🐛 Troubleshooting

### Common Issues

**Q: Service startup failed?**
```bash
# Check if port is occupied
netstat -tlnp | grep :8080

# Check permissions
ls -la linker-upgrader
```

**Q: File upload failed?**
```bash
# Check disk space
df -h

# Check upload directory permissions
ls -la uploads/
```

**Q: Service management failed?**
```bash
# Check systemd service status
systemctl status myapp

# Check user permissions
id
```

### Log Viewing

```bash
# View program logs
tail -f /var/log/linker-upgrader.log

# View systemd logs
journalctl -u linker-upgrader -f
```

## 🤝 Contributing Guidelines

We welcome all forms of contributions!

### Development Environment Setup

```bash
# Clone repository
git clone https://github.com/yourusername/linker-upgrader.git
cd linker-upgrader

# Install dependencies
go mod tidy

# Run tests
go test ./...

# Run program
go run main.go
```

### Code Standards

- Follow Go official code conventions
- Add appropriate comments and documentation
- Write unit tests
- Use `gofmt` to format code

## 📄 License

This project is licensed under the Apache v2 License - see the [LICENSE](LICENSE) file for details.

---

⭐ If this project helps you, please give us a Star!