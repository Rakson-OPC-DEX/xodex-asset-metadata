# Asset Metadata CDN

This container serves versioned network and token icons from `logos/`. The versioned paths are cached for one year because released versions must be immutable.

```text
/v1/{network-code}/icon.{png,svg,gif}
/v1/{network-code}/{token-id}/icon.{png,svg,gif}
```

Examples from this repository:

```text
https://cdn.mydomain.com/v1/xode/icon.png
https://cdn.mydomain.com/v1/xode/1984/icon.png
https://cdn.mydomain.com/v1/assethub/icon.svg
```

## Docker deployment

The CDN container listens on internal port `80`. It does not publish server ports, so it can coexist with your existing reverse-proxy container, which must publish `80` and `443`.

1. Find the Docker network used by the existing reverse proxy:

   ```bash
   docker network ls
   ```

2. Copy `.env.example` to `.env` and set `PROXY_NETWORK` to that network name. If no shared network exists yet, create one:

   ```bash
   docker network create proxy
   ```

3. Start the CDN:

   ```bash
   docker compose up -d
   ```

### Local browser test

The production Compose file does not publish a host port. To test without a reverse proxy, use the local override:

```bash
docker compose -f docker-compose.yml -f docker-compose.local.yml up -d
```

Then open:

```text
http://localhost:8080/v1/xode/icon.png
```

The `127.0.0.1` binding restricts this test endpoint to the local machine. Do not use `docker-compose.local.yml` on the production server.

4. In the existing reverse proxy, create a host route:

   ```text
   Hostname: cdn.mydomain.com
   Upstream host: metadata-cdn
   Upstream port: 80
   TLS: enabled
   ```

The reverse-proxy container must also join `PROXY_NETWORK`, allowing it to resolve `metadata-cdn` by service name.

## Cloudflare

Create a proxied (orange-cloud) `A` record named `cdn` pointing at the server public IP. Set SSL/TLS mode to **Full (strict)** and add a cache rule matching `cdn.mydomain.com`: make responses eligible for cache and respect origin cache headers.

Test an origin route through the public hostname:

```bash
curl -I https://cdn.mydomain.com/v1/xode/icon.png
```

Expect `Cache-Control: public, max-age=31536000, immutable`. After Cloudflare caches the file, the response should include `CF-Cache-Status: HIT`.
