# Securing a Debian Cloud Machine

> This guide details securing a new Debian cloud machine.

## Step 1: Update the System and enable ssh

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

## Step 2: Install Fail2ban

```bash
sudo apt install fail2ban -y
sudo systemctl enable --now fail2ban
```

## Step 3: Install and Configure Firewall

```bash
sudo apt install ufw -y
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
```

## Step 4: Setup automatic updates

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
```

## Step 5: Install and Configure AppArmor

```bash
sudo apt install apparmor apparmor-utils -y
sudo systemctl enable --now apparmor
sudo aa-enforce /etc/apparmor.d/* -y
```

## Step 6: Disable root login

Uncomment the following line in `/etc/ssh/sshd_config`:

```bash
PermitRootLogin no
```

then restart sshd for the changes to take effect:

```bash
sudo systemctl restart sshd
```
