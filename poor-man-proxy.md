# 🌍 Poor Man's Proxy 🚀

## 🔹 Overview

The "Poor Man's Proxy" is a simple and effective method to route traffic through an SSH tunnel, allowing you to bypass geo-blocking and secure your internet connection without setting up a full-fledged proxy server. This guide covers the setup on Linux & android.

## ✅ Prerequisites

- 🖥️ A remote Linux server (e.g., VPS) with SSH access.
- 💻 A local Linux machine (your computer) with `ssh` installed.
- 📱 An Android device with Termux installed.

## 🔧 Step 1: Connect to Your Remote Server

Use the following SSH command to create a SOCKS5 proxy on your local machine:

```sh
ssh -D 8080 -q -C -N user@your-vps-ip
```

### 🔍 Explanation:

- `-D 8080` → Creates a dynamic SOCKS5 proxy on port `8080`.
- `-q` → Suppresses non-error messages.
- `-C` → Enables compression for better performance.
- `-N` → Prevents executing commands (only establishes the tunnel).

Leave this SSH session **running** while using the proxy.

## 🌐 Step 2: Configure Your Applications to Use the Proxy

### 🦊 Browser (Firefox/Chromium-based Browsers)

- **Firefox**:

  1. Open Firefox → `Settings` → `Network Settings` → `Manual Proxy Configuration`
  2. Set **SOCKS Host** to `127.0.0.1`, Port `8080`
  3. Choose **SOCKS v5** and enable "Proxy DNS when using SOCKS v5"

- **Chromium-based Browsers (Brave, Chrome, etc.)**:

  ```sh
  brave-browser --proxy-server="socks5://127.0.0.1:8080"
  ```

  ```sh
  google-chrome --proxy-server="socks5://127.0.0.1:8080"
  ```

### 🛠️ System-Wide Proxy (Using `proxychains`)

To force all applications to use the SOCKS5 proxy, install **proxychains**:

```sh
sudo apt install proxychains
```

Edit the configuration file:

```sh
nano /etc/proxychains.conf
```

Modify the last line to:

```sh
socks5 127.0.0.1 8080
```

Run commands through the proxy:

```sh
proxychains curl ifconfig.me
```

## 🔄 Step 3: Automate the Proxy Tunnel

To avoid manually running the SSH command, create a systemd service:

```sh
mkdir -p ~/.config/systemd/user
nano ~/.config/systemd/user/poor-mans-proxy.service
```

Paste the following:

```ini
[Unit]
Description=Poor Man's Proxy
After=network.target

[Service]
ExecStart=/usr/bin/ssh -D 8080 -q -C -N user@your-vps-ip
Restart=always

[Install]
WantedBy=default.target
```

Enable and start the service:

```sh
systemctl --user enable poor-mans-proxy.service
systemctl --user start poor-mans-proxy.service
```

This will start the proxy automatically on login. 🎉

## ✅ Step 4: Verify the Proxy

Check your IP before and after connecting:

```sh
curl ifconfig.me
```

Make sure it reflects your **remote server's IP** when using the proxy. 🌍

---

## 📱 Using Poor Man's Proxy on Android (Termux + Firefox)

### 🛠️ Step 1: Install Termux and OpenSSH

1. Download and install [Termux](https://f-droid.org/packages/com.termux/) from F-Droid.
2. Open Termux and update packages:
   ```sh
   pkg update && pkg upgrade
   ```
3. Install OpenSSH:
   ```sh
   pkg install openssh
   ```

### 🔄 Step 2: Create the SSH Tunnel

Run the following command to establish the SOCKS5 proxy:

```sh
ssh -D 1337 -q -C -N user@your-vps-ip
```

Leave Termux running while using the proxy.

### 🌐 Step 3: Configure Firefox to Use the Proxy

1. Open **Firefox** on your Android device.
2. Enter `about:config` in the address bar.
3. Search for `network.proxy.socks` and set it to:
   ```
   127.0.0.1
   ```
4. Search for `network.proxy.socks_port` and set it to:
   ```
   1337
   ```
5. Enable **SOCKS v5** by setting `network.proxy.socks_version` to `5`.
6. Search for `network.proxy.socks_remote_dns` and set it to `true` (to resolve DNS through the proxy).

### ✅ Step 4: Verify the Proxy

Check your IP before and after enabling the proxy by visiting:

[https://ifconfig.me](https://ifconfig.me) or [https://whatismyipaddress.com](https://whatismyipaddress.com)

If your IP matches the VPS, the proxy is working! 🎉

## 🛠️ Troubleshooting

- ⚡ If SSH disconnects frequently, use the `-o ServerAliveInterval=60` flag:
  ```sh
  ssh -D 8080 -q -C -N -o ServerAliveInterval=60 user@your-vps-ip
  ```
- 🔄 Ensure port `8080` is not blocked on your local system.
- 🔒 Some applications may require **SOCKS5 with authentication**; in such cases, consider using a **SOCKS5 proxy manager** like `proxydroid` (for Android) or **Proxifier** (on GUI-based Linux systems).

---

## 🎯 Conclusion

The "Poor Man's Proxy" is a lightweight and powerful method for secure browsing, bypassing geo-restrictions, and encrypting your traffic. With SSH tunneling, you avoid the complexities of setting up a dedicated VPN or proxy server.

Enjoy secure and unrestricted browsing! 🚀🔓
