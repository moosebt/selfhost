# Self-Hosted Services Stack

A comprehensive collection of self-hosted services for home infrastructure, featuring authentication, reverse proxy, and various applications all managed through Docker Compose.

## Overview

This project provides a complete self-hosted infrastructure setup with:
- **Authentication**: Authentik for centralized identity management
- **Reverse Proxy**: Traefik with automatic SSL/TLS certificates
- **Media Services**: Complete media management stack
- **Security**: Integrated security features and monitoring

## Project Structure

```
selfhost/
├── authentication/          # Authentik identity provider
│   ├── compose.yaml        # Docker Compose configuration
│   └── README.md           # Setup and configuration guide
├── traefik/                # Traefik reverse proxy
│   ├── compose.yaml        # Docker Compose configuration
│   ├── config/             # Traefik configuration files
│   │   └── traefik/
│   │       ├── traefik.yaml    # Main configuration
│   │       ├── dynamic/        # Dynamic service configs
│   │       ├── acme/           # SSL certificate storage
│   │       └── logs/           # Traefik logs
│   ├── .env.example        # Environment variables template
│   └── README.md           # Setup and configuration guide
├── renovate.json           # Automated dependency updates
├── .gitignore              # Git ignore rules
└── README.md               # This file
```

## Services

### Core Infrastructure

- **Authentik** (`authentication/`): Centralized identity and access management
  - PostgreSQL database backend
  - Redis for caching
  - Web-based admin interface
  - SSO integration for all services

- **Traefik** (`traefik/`): Reverse proxy and load balancer
  - Automatic SSL/TLS certificate management via Let's Encrypt
  - Cloudflare DNS integration
  - Service discovery and routing
  - Security headers and rate limiting
  - CrowdSec threat protection

### Media Services (via Traefik)

- **Sonarr**: TV show management and automation
- **Radarr**: Movie management and automation  
- **Bazarr**: Subtitle management
- **Jellyseerr**: Media request management
- **Navidrome**: Music streaming server

### Download Services

- **SABnzbd**: Usenet download client
- **qBittorrent**: BitTorrent client

### Applications

- **BookStack**: Document and wiki management
- **Paperless**: Document scanning and archiving
- **Komodo**: Code editor and IDE
- **Beszel**: Communication platform

### Management & Security

- **Proxmox VE**: Virtual machine management
- **WarpGate**: SSH access management
- **WUD**: Update notifications and monitoring
- **Convos**: Chat and communication

## Quick Start

### Prerequisites

1. **Docker and Docker Compose** installed
2. **Domain name** pointing to your server
3. **Cloudflare account** (for DNS and SSL certificates)

### Initial Setup

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd selfhost
   ```

2. **Create required Docker networks**:
   ```bash
   docker network create proxy
   docker network create frontend
   docker network create backend
   ```

3. **Set up Traefik** (see `traefik/README.md` for detailed instructions):
   ```bash
   cd traefik
   cp .env.example .env
   # Edit .env with your domain and Cloudflare credentials
   mkdir -p secrets config/traefik/acme
   # Create secrets files
   docker-compose up -d
   ```

4. **Set up Authentik** (see `authentication/README.md` for detailed instructions):
   ```bash
   cd ../authentication
   mkdir -p secrets
   # Generate required secrets
   docker-compose up -d
   ```

## Configuration

### Environment Variables

Each service has its own `.env` file for configuration:
- `traefik/.env`: Domain, ports, Cloudflare credentials
- `authentication/.env`: Database and application settings

### Secrets Management

Sensitive data is managed through Docker secrets:
- API tokens and passwords stored in `secrets/` directories
- Automatically excluded from version control
- Mounted securely into containers

### Network Architecture

- **`proxy`**: External network for Traefik and web-accessible services
- **`frontend`**: Network for frontend services
- **`backend`**: Network for backend services and databases

## Security Features

- **SSL/TLS**: Automatic certificate generation and renewal
- **Authentication**: Centralized SSO via Authentik
- **Rate Limiting**: Protection against abuse
- **Security Headers**: Comprehensive security headers
- **Threat Protection**: CrowdSec integration
- **Trusted IPs**: Only Cloudflare and local networks trusted

## Maintenance

### Updates

The project uses [Renovate](https://renovatebot.com/) for automated dependency updates:
- Scheduled updates every Monday before 4am UTC
- Automatic merging of patch updates
- Manual review for minor and major updates
- Breaking changes labeled appropriately

### Monitoring

- **Traefik Dashboard**: Service health and routing status
- **Authentik Admin**: User and application management
- **Service Logs**: Individual service monitoring
- **WUD**: Update notifications

### Backup

Important data to backup:
- `traefik/config/traefik/acme/acme.json` (SSL certificates)
- `traefik/config/traefik/dynamic/` (service configurations)
- `authentication/secrets/` (authentication secrets)
- `.env` files (environment configurations)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
1. Check the individual service README files
2. Review the troubleshooting sections
3. Check service logs for errors
4. Open an issue on the repository

## Acknowledgments

- [Authentik](https://goauthentik.io/) for identity management
- [Traefik](https://traefik.io/) for reverse proxy
- [Renovate](https://renovatebot.com/) for dependency management
- All the open-source projects that make this stack possible