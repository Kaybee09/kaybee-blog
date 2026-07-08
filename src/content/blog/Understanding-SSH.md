---
title: 'Understanding SSH and Hardening SSH'
description: 'My Learning Journey
pubDate: 'Jul 08 2025'
heroImage: '../../assets/blog-placeholder-3.jpg'
---

## Introduction

When I first started learning Linux administration, I quickly realized that almost every server is managed remotely. SSH (Secure Shell) provides encrypted remote access to Linux servers.

## What is SSH?

SSH is a secure network protocol used to remotely connect to servers, execute commands, transfer files (SCP/SFTP), and create encrypted tunnels.

Example:
```bash
ssh username@server-ip
```

## SSH Configuration

Main configuration file:
```text
/etc/ssh/sshd_config
```
Common settings:
```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
```
Restart service:
```bash
sudo systemctl restart sshd
```

## Hardening SSH
- Disable root login
- Use SSH keys
- Disable password authentication
- Limit login attempts
- Change default port (optional)
- Allow only approved users

## Commands
```bash
systemctl status sshd
sshd -t
ss -tulpn | grep ssh
```

## Mistakes I Made
- Edited `sshd_config` without validating it.
- Disabled passwords before confirming SSH key login worked.
- Forgot to back up the configuration file.

## What I Learned
- Always test configuration with `sshd -t`.
- Keep one SSH session open while testing another.
- Security should never be an afterthought.

## Conclusion
SSH is the foundation of Linux remote administration. Learning how to secure it has made me more confident in managing production servers.
