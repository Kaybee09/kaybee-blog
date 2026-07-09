# Understanding systemd, Linux Services, and systemctl: My Learning Experience

## Introduction

One of the biggest changes in modern Linux systems is the adoption of **systemd** as the default init system. Before learning Linux administration, I thought services simply started automatically. I later discovered that systemd is responsible for starting, stopping, managing, and monitoring almost everything that runs after the Linux kernel boots.

Learning systemd helped me understand how Linux services actually work behind the scenes.

---

## What is systemd?

systemd is the initialization system that starts user-space services after the Linux kernel boots.

Its responsibilities include:

* Boot management
* Service management
* Dependency handling
* Logging
* Mount management
* Timers
* Resource control

---

## What is a Service?

A service is a background process (daemon) that performs tasks without user interaction.

Examples include:

* sshd
* nginx
* apache2
* docker
* cron

---

## Service Unit Files

systemd stores service definitions in unit files.

Example location:

```bash
/usr/lib/systemd/system/
```

or

```bash
/etc/systemd/system/
```

Example:

```ini
[Unit]
Description=My Demo Service

[Service]
ExecStart=/usr/bin/python3 /home/user/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Managing Services with systemctl

Start service

```bash
systemctl start nginx
```

Stop service

```bash
systemctl stop nginx
```

Restart

```bash
systemctl restart nginx
```

Enable during boot

```bash
systemctl enable nginx
```

Disable

```bash
systemctl disable nginx
```

Check status

```bash
systemctl status nginx
```

---

## Reloading Configuration

```bash
systemctl daemon-reload
```

This command tells systemd to reread modified unit files.

---

## Viewing Logs

systemd integrates with journald.

```bash
journalctl -u nginx
```

---

## Mistakes I Made

Initially, I confused **enable** with **start**.

I thought enabling a service would immediately run it, but it only configures the service to start during boot.

Another mistake was editing a service file without running:

```bash
systemctl daemon-reload
```

As a result, none of my changes took effect.

I also spent time troubleshooting a service that kept failing because the executable path in `ExecStart` was incorrect.

---

## Lessons I Learned

I learned to verify service files carefully before restarting services.

I also learned that checking logs with `journalctl` is often faster than guessing why a service failed.

Understanding dependencies also helped me appreciate why some services refuse to start until required services are already running.

---

## Conclusion

systemd is much more than a service manager. It controls how Linux boots, manages services, and provides centralized logging. Learning how to configure and troubleshoot systemd has become one of the most valuable Linux administration skills I have developed.
