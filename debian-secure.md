# Securing a Debian Cloud Machine

> This guide outlines essential steps to secure a new Debian cloud machine to protect against common threats.

## Prerequisites

Before you begin, ensure you have:
- A freshly installed Debian cloud machine.
- Initial SSH access, typically as `root` or a `sudo` user.
- An active internet connection.

## Step 1: Update the System

Update and upgrade your system's packages to apply the latest security patches.

```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Create a New Sudo User

Avoid using the `root` user for daily tasks. Create a new standard user with `sudo` privileges.

1.  **Create the new user (replace `your_username`):**
    ```bash
    sudo adduser your_username
    ```
    Follow prompts to set a strong password.

2.  **Add the new user to the `sudo` group:**
    ```bash
    sudo usermod -aG sudo your_username
    ```

3.  **Verify sudo access:**
    Switch to the new user and test `sudo`.
    ```bash
    su - your_username
    sudo whoami
    ```
    Enter `your_username`'s password; output should be `root`.

## Step 3: Install and Configure SSH

Secure remote access by installing and configuring OpenSSH, prioritizing key authentication.

1.  **Install OpenSSH Server:**
    ```bash
    sudo apt install openssh-server -y
    ```

2.  **Enable and Start SSH Service:**
    ```bash
    sudo systemctl enable --now ssh
    ```

3.  **Harden SSH Configuration:**
    Edit `/etc/ssh/sshd_config`. **Backup the file first:**
    ```bash
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
    sudo vim /etc/ssh/sshd_config
    ```

    Make the following changes:

    *   **Disable Root Login**: (After securing main user `sudo` access).
        ```
        PermitRootLogin no
        ```

5.  **Restart SSH Service:**
    ```bash
    sudo systemctl restart sshd
    ```
    **IMPORTANT:** Test login *from a new terminal window* before closing your current session to prevent lockout.

## Step 4: Install and Configure Firewall (UFW)

Control network traffic with UFW (Uncomplicated Firewall).

1.  **Install UFW:**
    ```bash
    sudo apt install ufw -y
    ```

2.  **Set Default Policies:**
    Deny incoming, allow outgoing.
    ```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    ```

3.  **Allow SSH Connections:**
    Essential for remote access. Use `ssh` or your custom port (e.g., `sudo ufw allow 2222/tcp`).
    ```bash
    sudo ufw allow ssh
    ```

4.  **Enable Firewall:**
    **WARNING**: Enabling without allowing SSH will lock you out!
    ```bash
    sudo ufw enable
    ```
    Type `y` and Enter.

5.  **Verify Firewall Status:**
    ```bash
    sudo ufw status verbose
    ```
    Confirm `Status: active` and SSH is allowed.

## Step 5: Install Fail2ban

Fail2ban automatically bans IPs with suspicious login attempts by scanning log files.

1.  **Install Fail2ban:**
    ```bash
    sudo apt install fail2ban -y
    ```

2.  **Enable and Start Fail2ban Service:**
    ```bash
    sudo systemctl enable --now fail2ban
    ```

3.  **Verify Fail2ban Status (Optional):**
    ```bash
    sudo systemctl status fail2ban
    sudo fail2ban-client status
    ```
    Default configuration is often sufficient.

## Step 6: Setup Automatic Updates

Ensure security patches are applied automatically, reducing vulnerabilities.

1.  **Install Unattended Upgrades:**
    ```bash
    sudo apt install unattended-upgrades -y
    ```

2.  **Configure Unattended Upgrades:**
    At the interactive prompt, choose "Yes, automatically download and install stable updates."
    ```bash
    sudo dpkg-reconfigure unattended-upgrades
    ```

## Step 7: Install and Configure AppArmor

AppArmor enforces security profiles on applications, confining them to limited resources for proactive protection.

1.  **Install AppArmor and Utilities:**
    ```bash
    sudo apt install apparmor apparmor-utils -y
    ```

2.  **Enable and Start AppArmor Service:**
    ```bash
    sudo systemctl enable --now apparmor
    ```

3.  **Review and Enforce AppArmor Profiles:**
    Review and enable specific profiles for critical services, rather than blanket enforcing all.
    *   **Check existing profiles:**
        ```bash
        sudo apparmor_status
        ```
    *   **To enforce a specific profile** (e.g., for `nginx`):
        ```bash
        sudo aa-enforce /etc/apparmor.d/usr.sbin.nginx
        ```
        A blanket `sudo aa-enforce /etc/apparmor.d/*` might cause issues. Focus on important services.
