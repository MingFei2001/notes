# Setting up Copyparty on Debian
A quick guide to install and configure Copyparty on a Debian system, including setting up a reverse proxy with Nginx.

## Base Software Stack

- OS: Debian
  - You can setup the system with separated `/home` partition during installation.
- File Browser: Copyparty
  - Support various advanced file server features like resume after pause download, user permission and WebDAV.
- Nginx
  - Reverse proxy for Copyparty, user can access Copyparty through IP address without knowing the port number.

## Installing Copyparty
We will be using pipx to install Copyparty as a global application.

1. **Install Dependencies:**
   ```bash
   sudo apt-get update
   sudo apt-get install python3 pip 
   ```

2. **Install pipx and Copyparty:**
   ```bash
   pipx ensurepath
   source ~/.bashrc
   pipx install copyparty --global
   ```

## Configuring Copyparty as a Systemd Service
Set up Copyparty to run as a systemd service for automatic startup and better process management.

1. **Create the Service File:**
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

  **Available Options**
  1. `--chdir /home/user`: Sets the working directory for Copyparty.
  2. `-a user:password`: Defines the username and password for authentication.
  5. `-v .::A,user` grants `user` full permission to current directory, equivalent to `rwmda`(read/write/move/delete/admin/dotfiles)
  6. `:rw` grants anonymous user read and write permissions, normal access but could not move and delete files.

2. **Enable the Service:**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable --now copyparty
   ```

## Setting Up Nginx as a Reverse Proxy
Configure Nginx to act as a reverse proxy for Copyparty, enabling better performance and additional features like HTTPS.

1. **Install Nginx:**
   ```bash
   sudo apt-get install nginx
   ```

2. **Create the Nginx Configuration File:**
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

3. **Link Configuration and Enable Nginx:**
   ```bash
   sudo ln -s /etc/nginx/conf.d/copyparty.conf /etc/nginx/sites-enabled/default
   sudo systemctl daemon-reload
   sudo systemctl enable --now nginx
   ```

## Testing the Setup
Verify that Copyparty and Nginx are running correctly and accessible from your browser.

1. Verify services:
   ```bash
   sudo systemctl status copyparty
   sudo systemctl status nginx
   ```

2. Access the service via browser: `http://<server-ip>`
   - you should be able to access Copyparty at `http://<server-ip>` without specifying the port.
   - login requires no username as it will automatically matches the password with the right user.

## Troubleshooting
Resolve common issues that may arise during the setup of Copyparty or Nginx.

- **Permissions:** Ensure user has the correct permissions for the Copyparty directory.

1. To restart Nginx:
```bash
  # If configuration has changed:
  sudo systemctl daemon-reload
  
  sudo systemctl restart nginx
  sudo systemctl status nginx
  ```

2. To restart Copyparty:
```bash
  # If configuration has changed:
  sudo systemctl daemon-reload
  
  sudo systemctl restart copyparty
  sudo systemctl status copyparty
  ```
