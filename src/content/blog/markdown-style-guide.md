---
title: 'Simple Linux System Health Monitor with Bash'
description: 'Here is a sample of some basic Markdown syntax that can be used when writing Markdown content in Astro.'
pubDate: 'Jun 01 2026'
##heroImage: '../../assets/blog-placeholder-1.jpg'
---

When you are learning Linux administration, one of the best ways to improve your skills is to build small tools that solve real problems.

A system administrator often needs to answer simple questions:

- How long has the server been running?
- Is the system under heavy load?
- How much memory is available?
- Is the disk becoming full?
- Who is currently logged in?

You could run a separate command for every question, but that quickly becomes repetitive. A better approach is to combine those commands into a small Bash script that gives you a quick overview of the system.

In this article, I will walk through a simple script called `monitor_snapshot.sh`. It collects useful information about a Linux machine and prints everything in a clean, readable report.

## What the Script Does

The script displays:

- The computer or server hostname
- The user running the script
- The system uptime
- CPU load averages
- RAM and swap usage
- Total disk usage
- Users currently logged in

This is not intended to replace full monitoring platforms such as Prometheus, Grafana, Nagios, or Zabbix. It is simply a lightweight tool for quick checks, troubleshooting, and Bash practice.

## The Complete Script

Create a file called `monitor_snapshot.sh` and add the following code:

```bash
#!/bin/bash

# monitor_snapshot.sh
# Prints a one-time system health snapshot.

HOSTNAME=$(hostname)
CURRENT_USER=$(whoami)
UPTIME_INFO=$(uptime -p)
LOAD_AVG=$(uptime | awk -F'load average:' '{print $2}' | xargs)
LOGGED_USERS=$(who | awk '{print $1}' | sort -u | xargs)

if [[ -z "$LOGGED_USERS" ]]; then
    LOGGED_USERS="No logged-in users found"
fi

echo "===================================="
echo " Linux Monitor Agent - Snapshot"
echo "===================================="
echo "Hostname:        $HOSTNAME"
echo "Current User:    $CURRENT_USER"
echo "Uptime:          $UPTIME_INFO"
echo "CPU Load Avg:    $LOAD_AVG"
echo

echo "RAM Usage:"
free -h

echo
echo "Disk Usage:"
df -h --total | grep -E 'Filesystem|total'

echo
echo "Logged-in Users:"
echo "$LOGGED_USERS"
```

Now let us follow on how each part works.

## Starting with the Shebang

```bash
#!/bin/bash
```

The first line tells Linux to run the script using the Bash shell.

This line is called a **shebang**. Without it, the operating system may not know which interpreter should be used to execute the file.

## Getting the Hostname

```bash
HOSTNAME=$(hostname)
```

The `hostname` command returns the name of the machine.

The result is stored in a variable called `HOSTNAME`. This is especially useful when you run the same monitoring script on several servers because the report immediately tells you which machine you are checking.

The `$()` syntax is called **command substitution**. Bash runs the command inside the brackets and stores its output in the variable.

## Finding the Current User

```bash
CURRENT_USER=$(whoami)
```

The `whoami` command displays the username of the person or account running the script.

This can help you confirm whether the script is being run by a normal user, an administrator, or the root account.

## Checking the System Uptime

```bash
UPTIME_INFO=$(uptime -p)
```

The `uptime` command tells us how long the system has been running.

The `-p` option makes the result easier to read. Instead of a long technical line, you may see something like:

```text
up 3 days, 5 hours, 20 minutes
```

Uptime is useful when investigating unexpected restarts. If a server was supposed to have been running for several weeks but the uptime shows only a few hours, it may have recently rebooted.

## Reading the CPU Load Average

```bash
LOAD_AVG=$(uptime | awk -F'load average:' '{print $2}' | xargs)
```

This line looks more complicated because it combines several commands using pipes.

First, `uptime` produces output similar to this:

```text
14:31:15 up 3 days, 2 users, load average: 0.10, 0.15, 0.12
```

The output is passed to `awk`, which separates the line at the words `load average:` and prints the second part.

```bash
awk -F'load average:' '{print $2}'
```

The result is then passed to `xargs`, which removes unnecessary spaces from the beginning and end.

The three load-average values represent the average system load over the last:

1. One minute
2. Five minutes
3. Fifteen minutes

These values should be interpreted in relation to the number of CPU cores.

For example, a load average of `4.00` may be normal for a busy four-core machine, but it could indicate a serious bottleneck on a single-core system.

## Checking Logged-In Users

```bash
LOGGED_USERS=$(who | awk '{print $1}' | sort -u | xargs)
```

The `who` command lists users with active login sessions.

Its output is passed through three additional commands:

```bash
awk '{print $1}'
```

This keeps only the username.

```bash
sort -u
```

This sorts the names and removes duplicates. A user may have several open sessions, but their name will appear only once.

Finally, `xargs` places the usernames on one line.

## Handling an Empty Result

```bash
if [[ -z "$LOGGED_USERS" ]]; then
    LOGGED_USERS="No logged-in users found"
fi
```

Sometimes the `who` command returns nothing because no interactive users are logged in.

The `-z` test checks whether the variable is empty. If it is, the script replaces the empty value with a clear message:

```text
No logged-in users found
```

This is a small detail, but it makes the final report easier to understand.

## Printing the Report

The next section creates the report heading and displays the collected information.

```bash
echo "===================================="
echo " Linux Monitor Agent - Snapshot"
echo "===================================="
echo "Hostname:        $HOSTNAME"
echo "Current User:    $CURRENT_USER"
echo "Uptime:          $UPTIME_INFO"
echo "CPU Load Avg:    $LOAD_AVG"
```

The spacing after each label helps keep the output aligned.

An empty `echo` command adds a blank line between sections:

```bash
echo
```

This makes the report more readable.

## Displaying Memory Usage

```bash
free -h
```

The `free` command displays information about the machine's memory and swap space.

The `-h` option means **human-readable**. It shows values using units such as megabytes and gigabytes instead of raw byte counts.

A typical result may look like this:

```text
               total        used        free      shared  buff/cache   available
Mem:            15Gi       4.2Gi       5.7Gi       310Mi       5.1Gi        10Gi
Swap:          2.0Gi          0B       2.0Gi
```

When reading this output, the `available` column is often more useful than the `free` column.

Linux uses unused memory for caching to improve performance. This means a low `free` value does not always indicate a problem.

## Displaying Disk Usage

```bash
df -h --total | grep -E 'Filesystem|total'
```

The `df` command reports disk-space usage.

The `-h` option displays values in a readable format, while `--total` adds a final line containing the combined total.

The output is then filtered with:

```bash
grep -E 'Filesystem|total'
```

This keeps only the heading and the total line.

The output may look like this:

```text
Filesystem      Size  Used Avail Use% Mounted on
total            80G   29G   47G  39% -
```

This gives you a quick summary of overall disk usage.

For a more detailed view of every mounted filesystem, you can run:

```bash
df -h
```

## Running the Script

After saving the file, make it executable:

```bash
chmod +x monitor_snapshot.sh
```

Then run it:

```bash
./monitor_snapshot.sh
```

You can also run it directly with Bash:

```bash
bash monitor_snapshot.sh
```

## Example Output

Your result may look similar to this:

```text
====================================
 Linux Monitor Agent - Snapshot
====================================
Hostname:        web-server-01
Current User:    ubuntu
Uptime:          up 3 days, 5 hours, 12 minutes
CPU Load Avg:    0.08, 0.12, 0.10

RAM Usage:
               total        used        free      shared  buff/cache   available
Mem:            7.7Gi       2.1Gi       3.4Gi       100Mi       2.2Gi       5.1Gi
Swap:           2.0Gi          0B       2.0Gi

Disk Usage:
Filesystem      Size  Used Avail Use% Mounted on
total            80G   29G   47G  39% -

Logged-in Users:
ubuntu admin
```

The exact values will depend on your machine, workload, storage, and active users.
