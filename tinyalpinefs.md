# Tiny Alpine FS

## Install copyparty system-wide

```bash
# alpine linux specific
sudo apk add pipx
pipx ensurepath
pipx install copyparty --global

# verify the path to the binary
which copyparty
```

*P/S: you can do a test run by running `copyparty` anywhere.*

## Setting up the openrc service

1. Create the service file at `/etc/init.d/copyparty`.

```bash
#!/sbin/openrc-run

name="copyparty"
description="Copyparty file server"

# change the following based on the binary path
command="/usr/local/bin/copyparty"
command_args="--chdir /srv/copyparty"
command_user="copyparty:copyparty"
command_background="yes"

pidfile="/run/copyparty.pid"

depend() {
    need net
    after firewall
}
```

```bash
# make the service executable
sudo chmod +x /etc/init.d/copyparty
```

2. Create the user and groups for the service

```bash
sudo adduser -S -D copyparty
sudo addgroup -S copyparty copyparty
```

3. Create the directory for the service

```bash
sudo mkdir /srv/copyparty
chown -R copyparty:copyparty /srv/copyparty
```

4. Enable and start the service

```bash
sudo rc-update add copyparty
sudo rc-service copyparty start
```

It should run without error. If any error occurs, tweak the command args in the service file and check the user's group and directory ownership is in the right place.

5. Test the service

Navigate to http://<ip-address>:3923 to use the service.

## Setting up reverse proxy to port 80

1. Install Nginx

```bash
sudo apk add nginx
```

2. Modify `/etc/nginx/http.d/default.conf`

```bash
server {
    listen 80;
    listen [::]:80;

    server_name _;   # wildcard for LAN use

    location / {
        proxy_pass         http://127.0.0.1:3923/;
        proxy_redirect     off;

        proxy_http_version 1.1;

        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        client_max_body_size 0;
        proxy_request_buffering off;
        proxy_buffering off;

        proxy_set_header Connection "Keep-Alive";
    }
}
```
3. Enable the nginx service

```bash
sudo rc-update add nginx default
sudo rc-service nginx start
```

4. Test the reserve proxy

Navigate to http://<ip-address> to verify the proxy is working.
