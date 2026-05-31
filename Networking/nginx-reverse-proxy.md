# NGINX: Internal Port Forwarding & Reverse Proxy

## What is nginx

Nginx is a high-performance web server and reverse proxy, known for handling many concurrent connections with low memory overhead. Common uses include serving static files, terminating TLS, load balancing, and acting as a reverse proxy in front of application servers.

In this note, nginx is used purely as a reverse proxy. The goal is to route traffic from a public-facing port (e.g. 80) to a backend service running on a non-standard local port on the same machine, so end users hit a clean URL without specifying the port.

## Before you start

### Prerequisites

- nginx installed and running
- a backend service already listening on a local port
- sudo access

### Main configuration directory

- main config: `/etc/nginx/nginx.conf`
- drop-in site configs: `/etc/nginx/conf.d/*.conf`
- alternate drop-in directory: `/etc/nginx/sites-enabled/*`

The main `nginx.conf` includes both directories inside its `http {}` block. Any `.conf` file placed in `conf.d/` is auto-loaded on reload.

### Preparations

1. confirm the backend is reachable locally

   ```
   curl http://localhost:<backend-port>
   ```

2. pick a config filename — `<service>.conf`, lowercase-hyphenated

3. make sure no other site config already binds the public port you plan to use (check `conf.d/` and `sites-enabled/`, including the default site)

## steps

1. create the site config at `/etc/nginx/conf.d/<service>.conf`

   ```
   server {
       listen 80;
       listen [::]:80;

       location / {
           proxy_pass http://localhost:<backend-port>;

           proxy_http_version 1.1;
           proxy_set_header Host              $host;
           proxy_set_header X-Real-IP         $remote_addr;
           proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

2. test config syntax

   ```
   sudo nginx -t
   ```

3. reload nginx

   ```
   sudo systemctl reload nginx
   ```

4. verify

   ```
   curl http://<host>/
   ```

## key directives

- `listen 80` — public port nginx accepts traffic on
- `proxy_pass` — backend address and port to forward to
- `Host $host` — preserves original Host header so the backend sees the real domain
- `X-Real-IP` / `X-Forwarded-For` — passes the client IP through; without these, backend only sees `127.0.0.1`
- `X-Forwarded-Proto` — tells backend whether the original request was http or https
- `proxy_http_version 1.1` — required for keep-alive and websockets

## extras for file-server or streaming backends

For large uploads, long-lived connections, or streaming:

```
client_max_body_size 0;
proxy_buffering off;
proxy_request_buffering off;
proxy_redirect off;
proxy_set_header Connection "Keep-Alive";
```

## case study — proxying a local app from :3923 to :80

Backend running on `localhost:3923`. Goal: serve it on port 80 so users don't type the port in the URL.

`/etc/nginx/conf.d/myapp.conf`:

```
server {
    listen 80;
    listen [::]:80;

    location / {
        proxy_pass http://localhost:3923;
        proxy_http_version 1.1;
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Apply:

```
sudo nginx -t && sudo systemctl reload nginx
```

## troubleshooting

- `nginx -t` fails — check syntax; check for duplicate `listen 80` across configs
- 502 bad gateway — backend not running, or wrong port in `proxy_pass`
- connection refused on :80 — firewall blocking, or nginx not running
- backend sees wrong client IP — missing `X-Real-IP` / `X-Forwarded-For`
- conflicting default site — remove or disable `/etc/nginx/sites-enabled/default`

## file naming

Use `<service>.conf` (lowercase) in `/etc/nginx/conf.d/`. Files without the `.conf` extension are ignored by the default include.
