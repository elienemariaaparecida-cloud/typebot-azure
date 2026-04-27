# Typebot Production Setup

This repository contains deployment infrastructure only: Docker Compose, Nginx proxy configuration, and environment examples.

The Typebot application itself is supplied from the official Docker images `baptistearno/typebot-builder` and `baptistearno/typebot-viewer`.

## Production Docker Compose

Use `docker-compose.prod.yml` to run production services.

### Required environment variables

Copy `.env.example` to `.env` and update values:

- `DATABASE_URL` - your PostgreSQL or compatible database connection string
- `DOMAIN` - public domain used by Typebot services
- `ENCRYPTION_SECRET` - a strong, random secret used for encryption
- `ADMIN_EMAIL` - admin user email for Typebot access

### Start services

```bash
cp .env.example .env
docker compose -f docker-compose.prod.yml up -d
```

### Service URLs

- Builder: `https://builder.<your-domain>`
- Viewer: `https://viewer.<your-domain>`

### Azure App Service

This repository is designed for Azure App Service Linux with Docker Compose.

- Use the file `docker-compose.yml` at the repository root for Azure deployment.
- Azure App Service should terminate HTTPS for you, so Nginx only speaks HTTP internally.
- Do not mount local TLS certificate files for Azure deployment.
- The public endpoint is served by the `nginx` container on port `80`.

To deploy via GitHub Actions, create an Azure Web App for Containers named `producao-brasil-app`, then configure the Azure service principal secrets in GitHub:

- `AZUREAPPSERVICE_CLIENTID_EA4BA96A9E874E00BDB8BDE00A7CB1C8`
- `AZUREAPPSERVICE_TENANTID_79BDFAB699FF4FC79033C1242573F3DC`
- `AZUREAPPSERVICE_SUBSCRIPTIONID_B895FB53765E47FC871B1812985E99C6`

Also configure the same runtime values as App Settings in Azure for:

- `DATABASE_URL`
- `DOMAIN`
- `ENCRYPTION_SECRET`
- `ADMIN_EMAIL`

> In production, terminate TLS at the edge (Azure Front Door, App Service managed certs, or an external proxy) and keep the app services internal.

## Notes

- For production, pin image tags or use image digests instead of `latest`.
- Store secrets securely in your deployment environment; do not commit `.env` to source control.
- If deploying to Azure, use Azure App Service for Containers or Azure Container Instances with this compose configuration.
