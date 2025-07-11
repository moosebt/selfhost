# Traefik Reverse Proxy

This directory contains the Traefik reverse proxy configuration for the self-hosted services stack. Traefik acts as the main entry point for all web services, handling SSL termination, routing, and security.

## Overview

Traefik is configured as a Docker container that:
- Provides SSL/TLS termination with automatic Let's Encrypt certificates
- Routes traffic to various services based on hostnames
- Implements security headers and rate limiting
- Integrates with Cloudflare DNS for certificate validation
- Includes authentication via Authentik
- Provides a web dashboard for monitoring

## Directory Structure

```
vms/traefik/
├── compose.yaml          # Docker Compose configuration
├── .env.example          # Example environment variables
├── secrets/              # Secret files (create manually)
│   ├── CF_API_EMAIL.txt
│   └── CF_DNS_API_TOKEN.txt  # Cloudflare API token
└── config/
    └── traefik/
        ├── traefik.yaml  # Main Traefik configuration
        ├── acme/         # ACME certificate storage
        ├── certs/        # Additional certificates
        ├── logs/         # Traefik logs
        └── dynamic/      # Dynamic configuration files
            ├── middlewares/  # Middleware configurations
            ├── auth.yaml     # Authentication settings
            ├── security-headers.yaml
            └── [service].yaml # Service-specific configurations
```

## Prerequisites

1. **Docker and Docker Compose** installed
2. **Cloudflare account** with API token for DNS challenge
3. **Domain name** pointing to your server
4. **External proxy network** created: `docker network create proxy`

## Setup Instructions

### 1. Environment Configuration

Copy the example environment file and configure it:

```bash
cp .env.example .env
```

Edit `.env` with your specific values:
- `DOMAIN`: Your domain name (e.g., `example.com`)
- `CF_API_EMAIL`: Your Cloudflare account email
- `CF_DNS_API_TOKEN`: Your Cloudflare API token
- `HTTP_PORT`: HTTP port (default: 80)
- `HTTPS_PORT`: HTTPS port (default: 443)
- `SSH_PORT`: SSH port for WarpGate (default: 2222)

### 2. Secrets Setup

Create the secrets directory and files:

```bash
mkdir -p secrets
echo "your@email.com" > secrets/CF_API_EMAIL.txt
echo "your-cloudflare-api-token" > secrets/CF_DNS_API_TOKEN.txt
```

**Cloudflare API Permissions:**
- Zone:Zone:Read
- Zone:DNS:Edit

### 3. Certificate Storage

Create the ACME certificate storage file:

```bash
mkdir -p config/traefik/acme
touch config/traefik/acme/acme.json
chmod 600 config/traefik/acme/acme.json
```

### 4. Start Traefik

```bash
docker-compose up -d
```

## Configuration Details

### Main Configuration (`traefik.yaml`)

- **Entry Points**: HTTP (80), HTTPS (443), SSH (2222)
- **SSL/TLS**: Automatic certificate generation via Let's Encrypt
- **DNS Challenge**: Uses Cloudflare for domain validation
- **Security**: Trusted IP ranges for Cloudflare and local networks
- **Logging**: Error and access logs stored in `config/traefik/logs/`

### Dynamic Configuration

Service configurations are stored in `config/traefik/dynamic/`:

- **Service Configs**: Individual service routing rules
- **Middlewares**: Security, authentication, and routing middleware
- **Authentication**: Authentik integration for protected services

### Middlewares

Available middlewares in `config/traefik/dynamic/middlewares/`:

- `authentik.yaml`: Authentik authentication integration
- `security-headers.yaml`: Security headers for all requests
- `rate-limit.yaml`: Rate limiting configuration
- `crowdsec-bouncer.yaml`: CrowdSec security integration

## Services

The following services are configured for routing:

- **Media Services**: Sonarr, Radarr, Bazarr, Jellyseerr, Navidrome
- **Download Clients**: SABnzbd, qBittorrent
- **Applications**: BookStack, Paperless, Komodo, Beszel
- **Management**: Proxmox VE, Traefik Dashboard
- **Security**: WarpGate (SSH)
- **Communication**: Convos (chat)

## Access

### Service URLs

Services are accessible at: `https://service.yourdomain.com`

For example:
- Sonarr: `https://sonarr.yourdomain.com`
- Radarr: `https://radarr.yourdomain.com`
- Jellyseerr: `https://jellyseerr.yourdomain.com`

## Security Features

1. **SSL/TLS**: Automatic Let's Encrypt certificates
2. **Security Headers**: Comprehensive security headers on all requests
3. **Rate Limiting**: Protection against abuse
4. **Authentication**: Authentik integration for protected services
5. **CrowdSec**: Advanced threat protection
6. **Trusted IPs**: Only Cloudflare and local IPs are trusted

## Troubleshooting

### Common Issues

1. **Certificate Issues**: Check Cloudflare API token permissions
2. **Service Not Accessible**: Verify service is running and network is correct
3. **Authentication Problems**: Check Authentik configuration

## Network Requirements

Ensure the `proxy` network exists:
```bash
docker network create proxy
```