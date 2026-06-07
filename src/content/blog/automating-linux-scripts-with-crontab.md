---

title: "Automating Linux Scripts with Crontab"
description: "A beginner-friendly explanation of how crontab helps automate Linux scripts and why scheduling tasks matters in cloud engineering."
pubDate: 2025-06-07
tags: ["Linux", "Crontab", "Bash", "Cloud Engineering", "Automation"]
heroImage: "/images/crontab-linkedin-post.jpg"
source: "Originally published on LinkedIn"
------------------------------------------

![Crontab learning post screenshot](/images/crontab-linkedin-post.jpg)

As I continue my cloud engineering journey at AltSchool Africa, I have been thinking more about automation.

One question I had was simple:

> After writing a script, do I always have to run it manually with `./memory_usage.sh`?

For example, if I write a script to check memory usage, do I need to manually execute it every time?

The answer I discovered is **no**.

This is where `crontab` becomes very useful.

## What is crontab?

`crontab` is a task scheduler in Unix and Linux systems. It allows you to automate repetitive tasks by setting them to run at specific times or intervals.

Instead of manually running a script every hour, every day, or every month, you can tell the system to run it automatically.

Crontab is useful for things like:

* Running backup scripts
* Cleaning up old log files
* Sending automated reports
* Running monitoring scripts
* Performing system maintenance
* Running custom Bash scripts
* Scheduling database maintenance
* Automating web application tasks

## How crontab works

A normal crontab schedule has five time fields followed by the command you want to run:

```bash
* * * * * command
```

Each asterisk represents a different part of the schedule:

```text
* * * * *
| | | | |
| | | | └── Day of the week: 0 - 7, where 0 and 7 mean Sunday
| | | └──── Month: 1 - 12
| | └────── Day of the month: 1 - 31
| └──────── Hour: 0 - 23
└────────── Minute: 0 - 59
```

## Example

Suppose I have a script located here:

```bash
/home/kaybee/memory_usage.sh
```

If I want the script to run on the 15th day of every month at exactly midnight, I can write:

```bash
0 0 15 * * /home/kaybee/memory_usage.sh
```

This means:

```text
0     → minute 0
0     → hour 0, midnight
15    → 15th day of the month
*     → every month
*     → any day of the week
```

So the script will run once every month on the 15th.

## Crontab shortcuts

Crontab also has special shortcuts that make scheduling easier:

```bash
@hourly   /home/kaybee/memory_usage.sh
@daily    /home/kaybee/memory_usage.sh
@weekly   /home/kaybee/memory_usage.sh
@monthly  /home/kaybee/memory_usage.sh
@yearly   /home/kaybee/memory_usage.sh
@reboot   /home/kaybee/memory_usage.sh
```

These mean:

* `@hourly` runs once every hour
* `@daily` or `@midnight` runs once every day at midnight
* `@weekly` runs once every week
* `@monthly` runs once every month
* `@yearly` or `@annually` runs once every year
* `@reboot` runs when the system starts

## Why this matters in cloud engineering

Automation is an important part of cloud engineering.

In cloud environments, engineers often need to manage servers, monitor systems, clean logs, run backups, and schedule maintenance tasks. Doing these things manually every time is not efficient.

Tools like `crontab` help improve consistency and reliability because tasks can run automatically at the right time.

For me, learning crontab helped me understand that cloud engineering is not just about launching servers. It is also about managing systems properly and making them more efficient through automation.

## What I learned

The biggest lesson for me was this:

> If a task is repetitive, it is a good candidate for automation.

Instead of manually running scripts again and again, I can schedule them and let the system handle the work.

As I continue learning at AltSchool Africa, I am gaining more hands-on experience with Linux, Bash scripting, and automation tools like crontab. These are small steps, but they are important foundations for becoming a better cloud engineer.
