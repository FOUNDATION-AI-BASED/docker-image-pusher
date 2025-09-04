# Docker Image Publisher

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Docker](https://img.shields.io/badge/docker-required-blue.svg)](https://www.docker.com/)

A powerful, user-friendly web-based tool for managing Docker images **exclusively on Ubuntu servers**. Build, manage, and publish Docker images through an intuitive web interface with no terminal commands required.

> ⚠️ **Important**: This application is designed specifically for Ubuntu Server environments and will not work on Windows, macOS, or other operating systems.

## 🚀 Features

### Core Functionality
- **Web-Based Interface**: Complete Docker image management through a modern web UI
- **No Terminal Required**: All operations handled through the web interface
- **Real-Time Updates**: Live progress tracking for build and push operations
- **Tutorial Mode**: Interactive guided experience for beginners
- **Advanced Mode**: Streamlined interface for experienced users

### Docker Operations
- **Image Management**: List, inspect, and delete Docker images
- **Image Building**: Build Docker images from Dockerfiles with real-time logs
- **Registry Publishing**: Push images to Docker Hub or private registries
- **Build History**: Track and review previous build operations
- **Multi-Architecture Support**: Build for different platforms and architectures

### System Features
- **Automatic Setup**: One-command installation with all dependencies
- **Service Management**: Systemd integration for reliable service management
- **Nginx Reverse Proxy**: Production-ready web server configuration
- **Security**: Dedicated user account and proper permission management
- **Monitoring**: Comprehensive logging and status monitoring

## 📋 Requirements

### System Requirements
- **Operating System**: Ubuntu Server 18.04 LTS or later (Ubuntu Desktop also supported)
- **Architecture**: x86_64 (amd64)
- **Memory**: Minimum 2GB RAM (4GB+ recommended)
- **Storage**: At least 10GB free space
- **Network**: Internet connection for package installation
- **Shell**: Bash shell environment (required for installation scripts)

> 🚫 **Not Compatible With**: Windows, macOS, CentOS, RHEL, or other Linux distributions

### Prerequisites
- **Docker**: Must be installed and running
- **Root Access**: Required for installation and service management
- **Ports**: Port 80 must be available (or configure alternative)

### Supported Docker Versions
- Docker Engine 19.03 or later
- Docker Compose 1.25 or later (optional)

## 🛠️ Installation

### Quick Start (Recommended)

The easiest way to install Docker Image Publisher is using the runner script:

```bash
# Download the project
git clone https://github.com/yourusername/docker-image-publisher.git
cd docker-image-publisher

# Make runner executable
chmod +x runner.sh

# Run interactive installer
sudo ./runner.sh
```

Select option `1) Install` from the menu and follow the prompts.

### Manual Installation

For more control over the installation process:

```bash
# Clone the repository
git clone https://github.com/yourusername/docker-image-publisher.git
cd docker-image-publisher

# Make scripts executable
chmod +x *.sh

# Run installation
sudo ./install.sh
```

### Installation Process

The installation script will:

1. **System Check**: Verify Ubuntu version and Docker installation
2. **Dependencies**: Install Python 3.8+, pip, nginx, and other requirements
3. **User Setup**: Create dedicated `docker-publisher` system user
4. **Application**: Set up application in `/opt/docker-image-publisher`
5. **Virtual Environment**: Create isolated Python environment
6. **Dependencies**: Install Python packages from `requirements.txt`
7. **Services**: Configure systemd service and nginx reverse proxy
8. **Firewall**: Configure UFW rules (if enabled)
9. **Startup**: Start services and enable auto-start

## 🎯 Usage

### Accessing the Web Interface

After installation, access the web interface at:

- **Local**: `http://localhost`
- **Network**: `http://your-server-ip`
- **Custom Port**: `http://your-server-ip:port` (if configured)

### First-Time Setup

1. **Choose Mode**: Select Tutorial or Advanced mode
2. **Docker Status**: Verify Docker connection
3. **Registry Setup**: Configure Docker Hub or private registry credentials
4. **Build Your First Image**: Follow the guided tutorial

### Tutorial Mode

Perfect for beginners or learning:

- **Step-by-Step Guidance**: Interactive tutorials for each operation
- **Explanations**: Detailed explanations of Docker concepts
- **Best Practices**: Learn Docker image building best practices
- **Safety Checks**: Warnings and confirmations for destructive operations

### Advanced Mode

Streamlined interface for experienced users:

- **Quick Access**: Direct access to all features
- **Batch Operations**: Perform multiple operations efficiently
- **Advanced Options**: Access to all Docker build parameters
- **Minimal Confirmations**: Faster workflow with fewer prompts

### Key Operations

#### Building Images
1. Navigate to "Build Image" tab
2. Select Dockerfile or upload one
3. Configure build parameters (tags, build args, etc.)
4. Monitor real-time build progress
5. Review build logs and results

#### Publishing Images
1. Go to "Publish Image" tab
2. Select image to publish
3. Configure registry and repository details
4. Add tags and metadata
5. Monitor upload progress

#### Managing Images
1. View all images in "Images" tab
2. Inspect image details and layers
3. Delete unused images
4. Export/import images

## 🔧 Management

### Service Management

Use the runner script for easy management:

```bash
# Interactive menu
sudo ./runner.sh

# Direct commands
sudo ./runner.sh start     # Start service
sudo ./runner.sh stop      # Stop service
sudo ./runner.sh restart   # Restart service
sudo ./runner.sh status    # Show status
sudo ./runner.sh logs      # View logs
```

### Manual Service Commands

```bash
# Service control
sudo systemctl start docker-image-publisher
sudo systemctl stop docker-image-publisher
sudo systemctl restart docker-image-publisher
sudo systemctl status docker-image-publisher

# View logs
sudo journalctl -u docker-image-publisher -f

# Enable/disable auto-start
sudo systemctl enable docker-image-publisher
sudo systemctl disable docker-image-publisher
```

### Configuration

#### Environment Configuration

Edit `/opt/docker-image-publisher/.env`:

```bash
# Application settings
FLASK_ENV=production
FLASK_DEBUG=false
SECRET_KEY=your-secret-key

# Server settings
HOST=0.0.0.0
PORT=5000

# Docker settings
DOCKER_HOST=unix:///var/run/docker.sock

# Logging
LOG_LEVEL=INFO
LOG_FILE=/var/log/docker-image-publisher/app.log
```

#### Nginx Configuration

Customize `/etc/nginx/sites-available/docker-image-publisher`:

```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    location /socket.io {
        proxy_pass http://127.0.0.1:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Updating

```bash
# Using runner script
sudo ./runner.sh update

# Manual update
sudo ./stop.sh
git pull origin main
sudo cp -r * /opt/docker-image-publisher/
sudo chown -R docker-publisher:docker-publisher /opt/docker-image-publisher
sudo -u docker-publisher /opt/docker-image-publisher/venv/bin/pip install -r /opt/docker-image-publisher/requirements.txt
sudo ./start.sh
```

### Backup and Restore

#### Backup

```bash
# Create backup directory
sudo mkdir -p /backup/docker-image-publisher

# Backup application
sudo tar -czf /backup/docker-image-publisher/app-$(date +%Y%m%d).tar.gz -C /opt docker-image-publisher

# Backup configuration
sudo cp /etc/nginx/sites-available/docker-image-publisher /backup/docker-image-publisher/
sudo cp /etc/systemd/system/docker-image-publisher.service /backup/docker-image-publisher/
```

#### Restore

```bash
# Stop service
sudo ./stop.sh

# Restore application
sudo tar -xzf /backup/docker-image-publisher/app-YYYYMMDD.tar.gz -C /opt

# Restore configuration
sudo cp /backup/docker-image-publisher/docker-image-publisher /etc/nginx/sites-available/
sudo cp /backup/docker-image-publisher/docker-image-publisher.service /etc/systemd/system/

# Reload and start
sudo systemctl daemon-reload
sudo ./start.sh
```

## 🔍 Troubleshooting

### Common Issues

#### Service Won't Start

```bash
# Check service status
sudo systemctl status docker-image-publisher

# Check logs
sudo journalctl -u docker-image-publisher -n 50

# Check Docker
sudo docker info

# Check permissions
sudo ls -la /opt/docker-image-publisher
```

#### Web Interface Not Accessible

```bash
# Check nginx status
sudo systemctl status nginx

# Check port binding
sudo netstat -tlnp | grep :80

# Check firewall
sudo ufw status

# Test local connection
curl http://localhost
```

#### Docker Operations Fail

```bash
# Check Docker daemon
sudo systemctl status docker

# Check Docker socket permissions
sudo ls -la /var/run/docker.sock

# Add user to docker group
sudo usermod -aG docker docker-publisher

# Restart service
sudo systemctl restart docker-image-publisher
```

#### Build Failures

1. **Check Dockerfile syntax**
2. **Verify base image availability**
3. **Check build context size**
4. **Review build logs in web interface**
5. **Ensure sufficient disk space**

### Log Locations

- **Application Logs**: `/var/log/docker-image-publisher/app.log`
- **System Logs**: `sudo journalctl -u docker-image-publisher`
- **Nginx Logs**: `/var/log/nginx/access.log`, `/var/log/nginx/error.log`
- **Docker Logs**: `sudo journalctl -u docker`

### Performance Tuning

#### For High-Volume Usage

```bash
# Increase worker processes
# Edit /opt/docker-image-publisher/.env
WORKERS=4
WORKER_CONNECTIONS=1000

# Optimize nginx
# Edit /etc/nginx/nginx.conf
worker_processes auto;
worker_connections 1024;

# Increase system limits
# Edit /etc/systemd/system/docker-image-publisher.service
[Service]
LimitNOFILE=65536
LimitNPROC=32768
```

## 🔒 Security

### Security Features

- **Dedicated User**: Runs under `docker-publisher` system user
- **Limited Permissions**: Minimal required system permissions
- **Secure Defaults**: Production-ready security configuration
- **Input Validation**: All user inputs are validated and sanitized
- **CSRF Protection**: Cross-site request forgery protection
- **Secure Headers**: Security headers in HTTP responses

### Security Best Practices

1. **Keep Updated**: Regularly update the application and dependencies
2. **Firewall**: Use UFW or iptables to restrict access
3. **HTTPS**: Configure SSL/TLS certificates for production
4. **Authentication**: Consider adding authentication for multi-user environments
5. **Network**: Run on private networks when possible
6. **Monitoring**: Monitor logs for suspicious activity

### Hardening

```bash
# Enable firewall
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Disable unnecessary services
sudo systemctl disable apache2  # if installed
sudo systemctl disable sendmail  # if installed

# Set up fail2ban (optional)
sudo apt install fail2ban
sudo systemctl enable fail2ban
```

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for details.

### Development Setup

```bash
# Clone repository
git clone https://github.com/yourusername/docker-image-publisher.git
cd docker-image-publisher

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Run development server
FLASK_ENV=development python app.py
```

### Running Tests

```bash
# Install test dependencies
pip install -r requirements-dev.txt

# Run tests
pytest tests/

# Run with coverage
pytest --cov=app tests/
```

## 🔧 Troubleshooting

### Common Issues

#### Runner Script Not Waiting for Input

**Problem**: When using the runner script for uninstallation, it doesn't wait for 'y' confirmation.

**Solution**: This has been fixed in the latest version. The runner script now properly handles terminal input/output. If you still experience issues:

```bash
# Option 1: Run uninstall script directly
sudo ./uninstall.sh

# Option 2: Force uninstall without confirmation
sudo FORCE_UNINSTALL=true ./uninstall.sh

# Option 3: Update to latest runner script
git pull origin main
chmod +x runner.sh
sudo ./runner.sh
```

#### Installation Fails on Non-Ubuntu Systems

**Problem**: Scripts fail when run on Windows, macOS, or other Linux distributions.

**Solution**: This application only works on Ubuntu Server/Desktop. Use a Ubuntu virtual machine or server.

#### Docker Not Found

**Problem**: Installation fails with "Docker not found" error.

**Solution**: Install Docker first:

```bash
# Install Docker on Ubuntu
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
# Log out and back in, then retry installation
```

#### Permission Denied Errors

**Problem**: Scripts fail with permission errors.

**Solution**: Ensure scripts are executable and run with sudo:

```bash
chmod +x *.sh
sudo ./runner.sh
```

#### Service Won't Start

**Problem**: Docker Image Publisher service fails to start.

**Solution**: Check logs and dependencies:

```bash
# Check service status
sudo systemctl status docker-image-publisher

# View logs
sudo journalctl -u docker-image-publisher -f

# Restart service
sudo systemctl restart docker-image-publisher
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Support

### Getting Help

- **Documentation**: Check this README and inline help
- **Issues**: Report bugs on [GitHub Issues](https://github.com/yourusername/docker-image-publisher/issues)
- **Discussions**: Join [GitHub Discussions](https://github.com/yourusername/docker-image-publisher/discussions)

### Reporting Issues

When reporting issues, please include:

1. **System Information**: Ubuntu version, Docker version
2. **Error Messages**: Complete error messages and stack traces
3. **Steps to Reproduce**: Detailed steps to reproduce the issue
4. **Logs**: Relevant log entries
5. **Configuration**: Any custom configuration changes

## 🗺️ Roadmap

### Planned Features

- [ ] **Multi-Registry Support**: Support for multiple Docker registries
- [ ] **Image Scanning**: Security vulnerability scanning
- [ ] **Build Caching**: Intelligent build caching for faster builds
- [ ] **User Management**: Multi-user support with role-based access
- [ ] **API Access**: REST API for programmatic access
- [ ] **Kubernetes Integration**: Deploy images directly to Kubernetes
- [ ] **Build Pipelines**: CI/CD pipeline integration
- [ ] **Image Analytics**: Usage analytics and optimization suggestions

### Version History

- **v1.0.0**: Initial release with core functionality
- **v1.1.0**: Tutorial mode and improved UI
- **v1.2.0**: Advanced build options and registry management

## 🙏 Acknowledgments

- **Flask**: Web framework
- **Docker**: Containerization platform
- **Socket.IO**: Real-time communication
- **Bootstrap**: UI framework
- **Ubuntu**: Operating system platform

---

**Made with ❤️ for the Docker community**

For more information, visit our [GitHub repository](https://github.com/yourusername/docker-image-publisher).