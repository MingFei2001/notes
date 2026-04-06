# Sudo: Superuser Do
> Privilege escalation utility for executing commands as another user (typically root), enforcing security policies via PAM.

`sudo` runs commands with elevated privileges, usually as root while maintaining audit trails and session accountability.
By default, it sanitizes the environment for security, omitting user variables unless configured otherwise.

To use `sudo`, a user must be granted permission—typically by being added to the `sudo` or `wheel` group, or explicitly configured in `/etc/sudoers`.
Misuse of `sudo` can compromise system integrity or security.
Only trusted users should be granted access.

## Using sudo
Use `sudo <command>` to run a single command as root. This limits the time spent with elevated privileges and reduces risk.

For example, on Debian-based systems:

```bash
apt update
sudo apt upgrade
```

`apt update` refreshes the package index from the distribution’s repositories. This operation only updates local metadata and does not modify the system, so it can be run without `sudo`.

`sudo apt upgrade` downloads and installs updated packages, replacing system binaries and libraries. Because this modifies the system, `sudo` is required as a security measure to prevent unauthorized changes.

Most commands that alter with system configuration or files will have the same measure in place to protect the system from malicious attempts.

## Switch user to root
For most cases, avoid opening a persistent root shell unless necessary. Use `sudo su` or `sudo -i` only when you need to run multiple root commands in sequence.

By default, `su` switching user to the root when invoke without argument.

To switch to root as a user with the right permission:
```bash
# Use sudo to start a root shell (inherits current environment).
sudo su

# Alternatively, use sudo -i for a full root login shell (loads root's environment).
sudo -i

# Type 'exit' or press Ctrl+D to leave the root shell.
```
**Note:** Each `sudo` command is logged but actions inside a root shell (`sudo su` or `sudo -i`) are not individually tracked.

## Password feedback
By default, password input in the sudo prompt is hidden; no characters are shown as you type. You can enable an option to display asterisks (`*`) for each character entered, providing visual feedback without revealing the actual password.

Edit the sudo configuration safely with `sudo visudo`. Find the line starting with `Default env_reset`.

```bash
# Add pwfeedback to the end of the line:
Default env_reset,pwfeedback

# Or add as a new line:
Default pwfeedback
```

This will show asterisks (`*`) as you type your password in the sudo prompt.

**WARNING:** This is not secure. Others can see the number of characters in your password, making brute-force attacks easier. Only enable this option if you understand the risks.
