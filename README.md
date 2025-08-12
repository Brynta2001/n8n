# n8n Docker Compose Setup

A complete Docker Compose configuration for running n8n with ngrok tunneling for external webhook access.

## Overview

This setup provides:

- n8n workflow automation platform
- ngrok tunneling for secure external access
- Persistent data storage with Docker volumes
- Automatic service restart policies

## Prerequisites

- Docker and Docker Compose installed
- ngrok account and authentication token
- ngrok domain (static domain recommended for production)

## Quick Start

1. **Clone or download this repository**

   ```bash
   git clone <your-repo-url>
   cd n8n-compose
   ```

2. **Create environment variables**
   Create a `.env` file in the project root:

   ```env
   NGROK_AUTHTOKEN=your_ngrok_auth_token_here
   NGROK_DOMAIN=your-domain.ngrok.io
   ```

3. **Start the services**

   ```bash
   docker compose up -d
   ```

4. **Access n8n**

   - Local access: <http://localhost:5678>
   - External access: <https://your-domain.ngrok.io>

## Configuration

### Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `NGROK_AUTHTOKEN` | Your ngrok authentication token | `1234567890abcdef...` |
| `NGROK_DOMAIN` | Your ngrok domain | `my-n8n.ngrok.io` |

### n8n Configuration

The n8n service is configured with:

- **Port**: 5678 (mapped to host)
- **Protocol**: HTTPS (via ngrok)
- **Timezone**: America/Bogota
- **Webhook URL**: Uses the ngrok domain for external webhooks
- **Data persistence**: `/home/node/.n8n` mounted to `n8n_data` volume

### ngrok Configuration

- Tunnels n8n service on port 5678
- Uses static domain for consistent webhook URLs
- Depends on n8n service startup

## Usage

### Starting Services

```bash
# Start in detached mode
docker compose up -d

# Start with logs visible
docker compose up
```

### Stopping Services

```bash
# Stop services
docker compose down

# Stop and remove volumes (⚠️ deletes all n8n data)
docker compose down -v
```

### Viewing Logs

```bash
# View all logs
docker compose logs

# View specific service logs
docker compose logs n8n
docker compose logs ngrok

# Follow logs
docker compose logs -f
```

### Managing Data

n8n data is persisted in the `n8n_data` Docker volume:

```bash
# List volumes
docker volume ls

# Backup volume
docker run --rm -v n8n-compose_n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/n8n-backup.tar.gz /data

# Restore volume (⚠️ overwrites existing data)
docker run --rm -v n8n-compose_n8n_data:/data -v $(pwd):/backup alpine tar xzf /backup/n8n-backup.tar.gz -C /
```

## Troubleshooting

### Common Issues

1. **ngrok authentication fails**
   - Verify `NGROK_AUTHTOKEN` is correct
   - Check ngrok account status

2. **Domain not accessible**
   - Ensure `NGROK_DOMAIN` matches your ngrok domain
   - Check if domain is active in ngrok dashboard

3. **n8n data not persisting**
   - Verify volume mount is working: `docker volume inspect n8n-compose_n8n_data`

4. **Services won't start**
   - Check logs: `docker compose logs`
   - Verify environment variables: `docker compose config`

### Health Checks

```bash
# Check service status
docker compose ps

# Test n8n accessibility
curl -I http://localhost:5678

# Test ngrok tunnel
curl -I https://your-domain.ngrok.io
```

## Development

### Customizing Configuration

Edit `docker-compose.yml` to:

- Change ports
- Add environment variables
- Mount additional volumes
- Add more services

### Using Different n8n Version

```yaml
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n:1.x.x  # Specify version
```

## Security Considerations

- Keep ngrok auth token secure
- Use static domains for production
- Consider implementing authentication
- Regularly update Docker images
- Monitor access logs

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues related to:

- n8n: Visit [n8n documentation](https://docs.n8n.io/)
- ngrok: Visit [ngrok documentation](https://ngrok.com/docs)
- Docker: Visit [Docker documentation](https://docs.docker.com/)
