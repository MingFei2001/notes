# Setting up Copyparty on Debian

## Copyparty

Install Copyparty using pipx in global mode:

```bash
sudo apt-get update
sudo apt-get install python3 pip 
pipx ensurepath
source ~/.bashrc
pipx install copyparty --global
```

Create the following service file:

```/etc/systemd/system/copyparty.service
[Unit]
Description=Copyparty File Server
After=network.target

[Service]
Type=simple
User=wwtadmin
Group=wwtadmin
WorkingDirectory=/home/user
ExecStart=/opt/pipx/venvs/copyparty/bin/copyparty --chdir /home/user -a user:password -v .::A,user:rw
Restart=on-failure
RestartSec=3
NoNewPrivileges=true
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

## Nginx

Install Nginx

```bash
sudo apt-get install nginx
```

Create the following configuration file:

```/etc/nginx/conf.d/copyparty.conf
server {
    listen 80;
    listen [::]:80;

    location / {
        proxy_pass         http://localhost:3923;

        proxy_redirect off;
        proxy_http_version 1.1;
        client_max_body_size 0;
        proxy_buffering off;
        proxy_request_buffering off;
        proxy_buffers 32 8k;
        proxy_buffer_size 16k;
        proxy_busy_buffers_size 24k;

        proxy_set_header   Connection        "Keep-Alive";
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
    }
}
```

Link it to the right location:

```bash
sudo ln -s /etc/nginx/conf.d/copyparty.conf /etc/nginx/sites-enabled/default
```

Enable Nginx Systemd Service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now nginx
```
