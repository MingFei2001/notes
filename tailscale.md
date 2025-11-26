# Tailscale
> Pretty much all you need to start using Tailscale on Linux.

## Installation

Go to [the official website](https://tailscale.com/download) and follow the instructions for your Linux distribution. A common one-liner for installation is:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

## Initial Setup

After installation, run the following command to connect your device to your Tailscale network for the first time:

```bash
sudo tailscale up
```
Follow the link to authenticate your device in your web browser.

### Basic Connection Commands

Here are some basic commands to manage your Tailscale connection after the initial setup:

*   **Check status:** See the current connection status and connected devices.
    ```bash
    tailscale status
    ```
*   **Disconnect:** Stop the Tailscale service and disconnect from the network.
    ```bash
    sudo tailscale down
    ```
*   **Reconnect:** Bring the Tailscale service back up and reconnect to the network.
    ```bash
    sudo tailscale up
    ```
*   **Reset configuration:** Reset all local Tailscale configurations to their default values (e.g., advertised routes, exit node settings). This does not log you out.
    ```bash
    sudo tailscale set --reset
    ```
*   **Logout:** Disconnect and completely remove the device from your tailnet, invalidating its authentication key. You will need to re-authenticate if you wish to rejoin.
    ```bash
    sudo tailscale logout
    ```
*   **Open Web UI:** Start a local web interface to manage your Tailscale settings.
    ```bash
    tailscale web
    ```
*   **Set Operator Permissions:** Grant the current non-root user permission to manage Tailscale without `sudo`.
    ```bash
    sudo tailscale set --operator $USER
    ```

## Advanced Configuration

This section covers setting up your Tailscale client for specific roles and common options.

### Subnet Router (Operator)

To configure your Linux machine as a Subnet Router, allowing other Tailscale devices to access your local network, you can use the `--advertise-routes` flag with `tailscale up`. Replace `192.168.1.0/24` with your actual subnet(s).

```bash
sudo tailscale up --advertise-routes=192.168.1.0/24
```

After running this command, you'll need to enable the advertised routes in your [Tailscale admin console](https://login.tailscale.com/admin/settings/network) under "Subnet routes".

For details on enabling IP forwarding, which might be necessary for a subnet router, refer to the [Tailscale documentation on Subnet Routers](https://tailscale.com/kb/1075/subnet-routers).

### Exit Node (Operator)

To set up your Linux machine as an Exit Node, allowing other Tailscale devices to route all their internet traffic through it, use the `--advertise-exit-node` flag.

First, advertise it as an exit node:
```bash
sudo tailscale up --advertise-exit-node
```

Then, you can use it from another device by specifying its name or IP:
```bash
tailscale up --exit-node <your-linux-machine-name>
```

You'll also need to enable the exit node in your [Tailscale admin console](https://login.tailscale.com/admin/settings/network) under "Exit nodes".
