# Setting up Tiny Alpine FS with Copyparty and Nginx

This guide helps you set up a lightweight, self-hosted file server using `copyparty` on Alpine Linux. `copyparty` is a powerful and versatile web server perfect for sharing files, streaming media, and managing uploads/downloads. 

You'll learn how to get it running reliably as an automatic service using OpenRC. We'll also cover how you can *optionally* use Nginx to make your `copyparty` server accessible on standard web ports (like port 80), providing a more polished and secure setup.

## What You'll Learn

*   Installing `copyparty` system-wide using `pipx`.
*   Configuring and managing `copyparty` as an OpenRC service.
*   Setting up Nginx as a reverse proxy to access `copyparty` on standard HTTP port 80.

## Install copyparty system-wide

We'll use `pipx` to install `copyparty`. `pipx` is a tool that helps you install and run Python applications in isolated environments, preventing dependency conflicts with other Python projects on your system. This ensures `copyparty` and its dependencies are self-contained.

```bash
sudo apk add pipx
pipx ensurepath
pipx install copyparty --global

# verify the path to the binary
which copyparty
```

After `pipx ensurepath`, you may need to refresh your shell (e.g., `source ~/.profile`) for `copyparty` to be in your PATH.

*P.S.: You can test the installation by running `copyparty` from any directory.*

## Setting up the openrc service

Alpine Linux uses OpenRC to manage its background services. Making an OpenRC script for `copyparty` means it will start automatically when your system boots, be simple to control (start, stop, restart), and run securely using a dedicated user.

1.  Create the service file at `/etc/init.d/copyparty`.

    This script shows OpenRC how to run the `copyparty` service. Focus on `command` and `command_args` to ensure `copyparty` starts correctly and uses the right folder for files.

    ```bash
    #!/sbin/openrc-run

    name="copyparty"
    description="Copyparty file server"

    
    command="/usr/local/bin/copyparty"    # this can change based on the binary path
    command_args="--chdir /srv/copyparty" # This tells copyparty to serve files from /srv/copyparty
    command_user="copyparty:copyparty"    # Runs the service as the 'copyparty' user and group
    command_background="yes"              # Runs the service in the background

    pidfile="/run/copyparty.pid" # Stores the process ID, allowing OpenRC to track the service

    depend() {
        need net # Ensures networking is up before starting the service
        after firewall # Starts after any firewall services have initialized
    }
    ```

    ```bash
    # make the service executable
    sudo chmod +x /etc/init.d/copyparty
    ```

2.  Create the dedicated user and group for the service

    Create a `copyparty` system user and group, and run the service as that unprivileged user instead of root to reduce risk.

    ```bash
    sudo adduser -S -D copyparty # -S creates a system user, -D prevents password prompt
    sudo addgroup -S copyparty copyparty
    ```

3.  Create the directory for the service

    This directory (`/srv/copyparty`) will be the root directory from which `copyparty` serves files. It's important to set the ownership to the `copyparty` user and group so the service has the necessary permissions to read and write files within it.

    ```bash
    sudo mkdir /srv/copyparty
    chown -R copyparty:copyparty /srv/copyparty
    ```

4.  Enable and start the service

    ```bash
    sudo rc-update add copyparty # Adds the service to the default runlevel, ensuring it starts on boot
    sudo rc-service copyparty start # Starts the service immediately
    ```

    It should run without error. If any issues arise, review the `command_args` in the service file, and verify the user's group membership and directory ownership/permissions.

5.  Test the service

    Navigate to http://<ip-address>:3923 to use the service.

## Setting up reverse proxy to port 80

`copyparty` runs on port 3923 by default, but users expect websites on port 80 (and 443 for HTTPS). Use Nginx as a reverse proxy to listen on port 80 and forward requests to copyparty. This gives a single public address, hides the internal port, and makes adding HTTPS/certificates much easier.

1.  Install Nginx

    ```bash
    sudo apk add nginx
    ```

2.  Modify `/etc/nginx/http.d/default.conf`

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
3.  Enable the nginx service

    ```bash
    sudo rc-update add nginx default
    sudo rc-service nginx start
    ```

4.  Test the reverse proxy

    Navigate to http://<ip-address> to verify the proxy is working.
