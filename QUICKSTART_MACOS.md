# Firecrawl - macOS Quick Start Guide 🚀

Get Firecrawl running locally on your Mac in under 5 minutes with Docker!

## Prerequisites

- ✅ [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop) installed and running
- ✅ Git (pre-installed on macOS)

## Setup Steps

### 1. Clone the Repository

```bash
git clone https://github.com/firecrawl/firecrawl.git
cd firecrawl
```

### 2. Configure Environment

The project includes a pre-configured `.env` file at `apps/api/.env` that's already set up for Docker. The default configuration includes:

- PostgreSQL, Redis, and RabbitMQ running in Docker containers
- No authentication required (for local development)
- All services connected via Docker networking

**Optional**: If you want to use AI features (LLM extraction), add your OpenAI API key:

```bash
echo "OPENAI_API_KEY=sk-your-key-here" >> apps/api/.env
```

### 3. Start All Services

```bash
docker compose up -d
```

This command will:

- Build the API, Playwright service, and PostgreSQL images (first time only - takes ~5-10 min)
- Start Redis, PostgreSQL, RabbitMQ, Playwright, and the API server
- Configure all networking automatically

### 4. Verify Services are Running

```bash
docker compose ps
```

You should see all 5 services in "Up" status:

- `firecrawl-api-1` (port 3002)
- `firecrawl-redis-1`
- `firecrawl-nuq-postgres-1`
- `firecrawl-rabbitmq-1` (should show "healthy" status)
- `firecrawl-playwright-service-1`

### 5. Test the API

```bash
# Test scraping
curl -X POST http://localhost:3002/v1/scrape \
    -H 'Content-Type: application/json' \
    -d '{"url": "https://example.com"}'
```

You should get a JSON response with the scraped content in markdown format!

## Common Commands

### View Logs

```bash
# All services
docker compose logs -f

# Specific service
docker compose logs -f api
docker compose logs -f rabbitmq
```

### Stop Services

```bash
docker compose down
```

### Stop and Remove All Data

```bash
docker compose down -v
```

### Restart Services

```bash
docker compose restart
```

## API Endpoints

Once running, you can access:

- **API**: http://localhost:3002
- **Scrape**: `POST http://localhost:3002/v1/scrape`
- **Crawl**: `POST http://localhost:3002/v1/crawl`
- **RabbitMQ Management**: http://localhost:15672 (if exposed)

## Troubleshooting

### Port 3002 Already in Use

Change the port by editing `apps/api/.env`:

```bash
PORT=3003
```

Then restart: `docker compose down && docker compose up -d`

### Services Not Starting

Check logs for the specific service:

```bash
docker compose logs api
docker compose logs rabbitmq
```

### Out of Memory

Increase Docker Desktop's memory allocation:

- Open Docker Desktop → Settings → Resources → Memory
- Increase to 8GB or more
- Click "Apply & Restart"

### RabbitMQ Unhealthy

RabbitMQ can take 20-30 seconds to become healthy on first start. Wait a bit and check:

```bash
docker compose ps
```

## What's Running?

- **PostgreSQL** (nuq-postgres): Database for queue management and data storage
- **Redis**: Caching and rate limiting
- **RabbitMQ**: Message queue for asynchronous job processing
- **Playwright Service**: Headless browser for JavaScript-heavy pages
- **API Service**: Main Firecrawl API with workers for crawling and scraping

## Next Steps

- Read the [API Documentation](https://docs.firecrawl.dev)
- Check out [example scripts](./examples/)
- Try the [Python SDK](https://docs.firecrawl.dev/sdks/python) or [Node SDK](https://docs.firecrawl.dev/sdks/node)
- For detailed development setup, see [CONTRIBUTING.md](./CONTRIBUTING.md)

---

**Need help?** Join our [Discord](https://discord.com/invite/gSmWdAkdwd) or open an [issue](https://github.com/firecrawl/firecrawl/issues)
