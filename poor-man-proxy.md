# 🌍 Poor Man's Proxy 🚀

## 🔹 Overview

A simple guide to setting up an SSH tunnel as a SOCKS5 proxy to bypass geo-restrictions and encrypt your internet traffic. This covers Linux desktops and Android devices.

## Prerequisites

-   🖥️ A remote Linux server (VPS) with SSH access.
-   💻 A local Linux machine with `ssh` installed.
-   📱 An Android device with Termux installed.

## Linux Desktop Setup

### 1. Create the SSH Tunnel

Execute this command, replacing `user@your-vps-ip` with your SSH credentials:

```sh
ssh -D 1080 -q -C -N user@your-vps-ip
```

*   `-D 1080`: Creates a SOCKS5 proxy on local port `1080`.
*   `-q`: Suppresses non-error output.
*   `-C`: Enables compression.
*   `-N`: Establishes the tunnel without executing remote commands.

Keep this session running while using the proxy.

### 2. Configure Applications

#### Browsers

*   **Firefox**: Go to `Settings` → `Network Settings` → `Manual Proxy Configuration`. Set **SOCKS Host** to `127.0.0.1`, Port `1080`, choose **SOCKS v5**, and enable "Proxy DNS when using SOCKS v5."
*   **Chromium-based (Brave, Chrome)**: Launch with the flag:
    ```sh
    brave-browser --proxy-server="socks5://127.0.0.1:1080"
    ```

#### System-Wide (`proxychains`)

Install `proxychains`:

```sh
sudo apt install proxychains
```

Edit `/etc/proxychains.conf` and change the last line:

```sh
socks5 127.0.0.1 1080
```

Run commands through the proxy:

```sh
proxychains curl ifconfig.me
```

### 3. Automate the Proxy Tunnel (Optional)

For automatic startup, create a systemd user service.

Create the file:

```sh
mkdir -p ~/.config/systemd/user
nano ~/.config/systemd/user/poor-mans-proxy.service
```

Add this content (update `user@your-vps-ip`):

```ini
[Unit]
Description=Poor Man's Proxy
After=network.target

[Service]
ExecStart=/usr/bin/ssh -D 1080 -q -C -N user@your-vps-ip
Restart=always

[Install]
WantedBy=default.target
```

Enable and start the service:

```sh
systemctl --user enable poor-mans-proxy.service
systemctl --user start poor-mans-proxy.service
```

### 4. Verify the Proxy

Check your public IP before and after connecting:

```sh
curl ifconfig.me
```

The IP should match your remote server's IP.

---

## Android Setup (Termux + Firefox)

### 1. Install Termux and OpenSSH

1.  Get [Termux](https://f-droid.org/packages/com.termux/) from F-Droid.
2.  Update Termux:
    ```sh
    pkg update && pkg upgrade
    ```
3.  Install OpenSSH:
    ```sh
    pkg install openssh
    ```

### 2. Create the SSH Tunnel

In Termux, run (replace `user@your-vps-ip`):

```sh
ssh -D 1337 -q -C -N user@your-vps-ip
```

Keep Termux running.

### 3. Configure Firefox

1.  Open Firefox on Android.
2.  Go to `about:config`.
3.  Set `network.proxy.socks` to `127.0.0.1`.
4.  Set `network.proxy.socks_port` to `1337`.
5.  Set `network.proxy.socks_version` to `5`.
6.  Set `network.proxy.socks_remote_dns` to `true`.

### 4. Verify the Proxy

Visit [https://ifconfig.me](https://ifconfig.me) to confirm your IP matches the VPS.

---

## Troubleshooting

*   **Frequent SSH disconnects**: Add `-o ServerAliveInterval=60` to your SSH command.
*   **Port in use/blocked**: Ensure ports `1080` (Linux) or `1337` (Android) are available.
*   **Authentication**: For authenticated SOCKS5, use dedicated proxy managers.

---

## Conclusion

The Poor Man's Proxy provides a secure and straightforward way to manage your internet traffic via SSH tunneling.