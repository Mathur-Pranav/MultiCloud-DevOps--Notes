## Concept

- **Linux Troubleshooting** is the systematic process of identifying, diagnosing, and resolving issues on Linux servers in real-time production environments, covering logs, performance, disk, services, permissions, hardware, and network problems. 
- A **DevOps engineer** must be capable of independent troubleshooting — even when L1/L2 support is unavailable — because unresolved issues escalate to senior engineers who are expected to fix them. 
- **System logs** are the primary source of truth when any issue occurs; the thumb rule is: *if something goes wrong, check the logs first*. 
- **Shell scripting** allows repetitive diagnostic commands to be automated into a single executable file, reducing manual effort and enabling scheduled execution via **cron jobs**. 
- **Service management** in Linux is handled via `systemctl`, which controls the lifecycle (start, stop, restart, status) of any installed service such as NGINX, SSH, Docker, Kubernetes, Ansible, etc. 
- **Graceful vs. Forceful process termination**: `kill -15` (SIGTERM) waits for the process to finish cleanly before stopping; `kill -9` (SIGKILL) forces immediate termination regardless of state. 
- **Swap memory** is virtual memory allocated from disk space, used when RAM is insufficient or to offload inactive memory pages — analogous to Windows' page file / hibernate feature. 

---

## What I Understood

### Google Cloud VM Creation and Configuration

- The session was conducted live on YouTube and used **Google Cloud (GCP)** as the hands-on environment; students were instructed to navigate to `console.cloud.google.com` and bookmark it as their cloud homepage. 
- A new VM named **"Day 6"** was created using the **Create Instance** workflow in Compute Engine > VM Instances. 
- **Real-time production server configuration** discussed: up to **16 cores, 16 GB RAM, 512 GB SSD** — though for critical workloads with thousands of users, 32 GB RAM is appropriate; large-scale storage is offloaded to third-party object storage rather than stored on VMs. 
- **Operating system choice**: Ubuntu **24.04 LTS** was selected over 26.04 because production servers predominantly still run on 22.04 or 24.04; a planned upgrade to 26.04 is a valid interview answer ("upgrade planned in next 3 months, discussion ongoing with client"). 
- **Auto-deletion feature**: Under Machine Configuration > VM Provisioning, a time limit can be set so the VM is automatically deleted or stopped after a defined period — useful for learning environments to avoid unexpected billing. 
- **CLI VM creation**: The `gcloud compute instances create` command was demonstrated in **Cloud Shell**, with parameters for name, zone, machine type, and project. Remembering four key lines of this command is sufficient for interview purposes. 
- **Equivalent instance type names across clouds** for 16 core / 16 GB RAM: 
    - **AWS**: `C6i.4xlarge` (C-series)
    - **GCP**: `N2-standard-16`
    - **Azure**: `Standard_F16s_v2`
- **IP types on a VM**: Internal IP = private IP used within the same VPC; External IP = public IP accessible over the internet. 
- **SSH connectivity options** in GCP: browser-based SSH, custom port, SSH key (public/private), Cloud Shell command, or third-party SSH client — all use port 22. 

---

### Log Checking — Scenario 1

- All system logs are stored under **`/var/log/`**; navigate there with `cd /var/log` and list contents with `ls`. 
- Key log files and their purposes: 
    - **auth.log** — authentication-related events (login attempts, sudo usage)
    - **kern.log** — kernel/OS-level messages, hardware-OS interface events
    - **dpkg.log** — package installation and removal history (package manager)
    - **dmesg** — boot-time messages, hardware detection during startup
    - **syslog / messages** — general system messages
    - **secure** — secure access logs
    - **fail.log** — failed login attempts
- **Commands for reading logs**: 
    - `more <filename>` — reads file page by page (slow, sequential)
    - `tail -10 <filename>` — shows last 10 lines only
    - `tail -f <filename>` — **follows** the file in real-time as new logs are written (`-f` = follow); used when a developer is running something and you need to monitor live output
    - `cat <filename> | grep error` — filters for specific keywords like `error`, `warning`, `failed`, `closed`, `exception`
- Application logs (e.g., Jenkins, NGINX) may also appear under `/var/log/<appname>/` — same commands apply, same concept. 
- **Logging tools** used in real-time environments: **Splunk** (most common), **Kibana**, **Dynatrace**, **Datadog** — these provide centralized, searchable log dashboards without manual CLI access. 

---

### Performance Troubleshooting — Scenario 2

- **Key commands for performance analysis**: 
    - `top` — real-time view of CPU usage, memory usage, load average, running tasks
    - `htop` — enhanced GUI-style version of top (must be installed: `apt install htop`); shows color-coded CPU/memory bars
    - `free -h` — memory usage in human-readable format
    - `df -h` — disk space usage per partition in human-readable format
    - `du` — disk usage of specific directories
    - `ps -ef` or `ps -aux` — list all running processes with details
    - `uptime` — system uptime and load average
    - `who` / `w` — who is currently logged in, how many users
- **Monitoring Shell Script** was built live, combining these commands: 
    - Created with `vi` editor; started with `#!/bin/bash` (shebang line)
    - Used `echo` statements as section headers for readability
    - Commands included: `free -h`, `df -h`, `du`, `uptime`
    - Saved and given execute permission with `chmod +x <filename>` (by default Linux does NOT grant execute permission for security reasons) 
    - Executed with `./scriptname.sh`
- **Production-level script enhancements** (generated with ChatGPT prompt): 
    - Added hostname and date/time for context
    - Colorized output — e.g., if disk usage exceeds **80% threshold**, display warning in red
    - Conditional logic: `if disk_usage > threshold → show warning`
    - Could be extended to **send email alerts** via SMTP configuration
- **Scheduling with cron jobs**: The monitoring script can be scheduled to run at regular intervals using `crontab`, enabling automated recurring health checks. 
- **Historical performance data**: For trend analysis (CPU/memory over weeks or months), data must be fed into a **continuous monitoring system** (e.g., Grafana, Datadog) rather than relying on real-time commands alone. 

---

### Disk Troubleshooting — Scenario 3

- **Windows disk tools**: Disk Defragmentation (built-in), `chkdsk` (Check Disk for errors), Disk Management (`compmgmt.msc`) for viewing partitions. 
- **Linux disk commands**: 
    - `df -h` — view partition sizes, used/free space; the root (`/`) partition is equivalent to Windows' C: drive
    - `lsblk` — list block devices; identify unconnected or unattempted disks
    - `smartctl` — tool from the `smartmontools` package; checks SSD/HDD health, temperature, error counts, and persistent device data 
- **Disk fragmentation** in Linux: Linux filesystems (ext4, etc.) handle fragmentation differently from Windows; Windows defragmentation compresses and reorders blocks; Linux rarely needs manual defragmentation. 
- **Block vs. Disk**: A disk contains blocks; blocks are the smallest units where data is physically stored; file systems manage how blocks are allocated. 
- **Partitions**: A single physical disk can be divided into multiple partitions (e.g., EFI, recovery, main OS partition); each partition appears as a separate mountpoint in Linux. 

---

### Service Troubleshooting — Scenario 4 (NGINX Example)

- **NGINX** was installed live: `apt-get update` → `apt install nginx` 
- Verified it was running by accessing the VM's external IP in a browser — default NGINX welcome page confirmed the service was active. 
- **Service management commands** (applicable to any service — NGINX, SSH, Docker, Kubernetes, Ansible, Tomcat, Apache, Java apps): 
    - `systemctl status nginx` — check if service is running, stopped, or failed; also shows the log path
    - `systemctl start nginx` — start the service
    - `systemctl stop nginx` — stop the service
    - `systemctl restart nginx` — restart
    - `systemctl enable nginx` — enable service to start on boot
    - `journalctl -u <service>` — view all logs for a specific service unit
- **Finding application logs**: When you run `systemctl status <service>`, the output shows the binary path and log location — this is the "smart answer" for finding service-specific logs in an interview. 
- **NGINX log location**: `/var/log/nginx/` — contains `access.log` and `error.log`; monitored in real-time with `tail -f access.log`. 
- **HTTP status codes** briefly discussed: 
    - `409` — Conflict (single server overloaded with too many requests)
    - `404` — Service/resource not found
    - `Connection refused` — service may have been killed (OOM kill, manual kill, crash)
- **Scaling solutions for overloaded servers**: Horizontal scaling (add more VMs) + **load balancing** (distribute traffic across VMs using AWS Elastic Load Balancer, GCP Load Balancer, or NGINX configured as a load balancer) + **High Availability (HA)** setup. 

---

### Permission Issues — Scenario 5

- **File permissions** in Linux are not automatically granted — by default, even the file owner cannot execute a newly created file (security design). 
- **CHMOD** (Change Mode) to grant permissions: 
    - `chmod +x <file>` — add execute permission
    - `chmod 744 <file>` — owner has full permissions (rwx), group and others have read-only
    - `chmod 777 <file>` — full permissions for everyone (use with caution)
- **CHOWN** (Change Ownership) — used to change file/directory ownership when permission issues relate to the wrong owner. 
- Checking permissions: `ls -l` — shows permission string, owner, group; executable files appear highlighted in green in many terminals. 

---

### Process Killing — Scenario 6

- Use `ps -ef` or `top` to find the **PID (Process ID)** of the problematic process. 
- **Kill signals**: 
    - `kill -15 <PID>` or plain `kill <PID>` — **graceful termination** (SIGTERM): waits for the process to complete its current task before stopping; preferred in production
    - `kill -9 <PID>` — **forceful termination** (SIGKILL): immediately kills the process regardless of state; use only when graceful kill fails or when the process is consuming resources abnormally without justification
    - `kill -11` — also mentioned as a signal option
- **Interview language tip**: Use the word **"graceful"** (not just "normal") when describing `kill -15` to sound professional. 
- **Thread dumps**: For Java-based applications consuming high CPU/memory, take a thread dump and hand it to the developer for analysis — DevOps engineers typically cannot fix application-level thread issues directly. 
- **When NOT to kill**: In production, never kill a process without approval or analysis; first check logs, analyze historical data, determine if the high usage is expected (e.g., end-of-day batch reports, monthly data extraction jobs). 

---

### Hardware Troubleshooting — Scenario 7

- **Common hardware issues in Linux**: 
    - **Kernel panic** — most common hardware-related error; often caused by OS/firmware mismatch after an update, or running an incompatible OS on old hardware
    - Disconnected cables or failed disk mounts
    - Fan issues, temperature problems
    - Driver/firmware mismatch (common in legacy systems)
- **Diagnostic commands**: 
    - `dmesg` — kernel ring buffer; shows hardware detection messages, boot errors, device connection/disconnection events
    - `lsblk` — list block devices; identifies unconnected or failed disks
    - `lscpu` — CPU information; can reveal multi-CPU configuration issues
    - `smartctl` — disk health and temperature (requires `smartmontools`)
- **Key principle**: Even in cloud environments, understanding hardware troubleshooting is essential for interviews — cloud abstracts hardware but interviewers still ask about it. 

---

### Network Troubleshooting — Brief Coverage

- **Telnet**: Used to verify port connectivity — `telnet <hostname> <port>` checks if a specific port on a destination is open and reachable. 
- **SSH service**: If SSH is not running, remote connections fail; check with `systemctl status sshd`; SSH configuration (allow/disallow users, keys) is managed in `/etc/ssh/sshd_config`. 
- **Wireshark / tcpdump**: Network packet capture tools — `tcpdump` captures packets via CLI, Wireshark analyzes them visually; used to diagnose network latency, packet drops, and timeout issues. 
- **SSH disconnection causes**: Most commonly network latency or packet drops (timeout); unlike Zoom which auto-reconnects, SSH shell sessions break and must be manually reconnected. 
- **Swap memory** (network context): Not network-related, but swap is virtual memory from disk — analogous to Windows hibernate/page file; useful when RAM is insufficient for active application data. 

---

### Weekend Challenge & Interview Prep

- **Bandit Game** (OverTheWire): A Linux command-line game with ~33–34 levels; students were challenged to complete as many levels as possible over the weekend and explain their solutions to the class. 
    - Level 0: SSH into the server using provided credentials (`bandit0` user, `bandit0` password) — login command was provided in Zoom chat
    - Completing and explaining levels builds real troubleshooting muscle memory
- **Resume points derived from this session**: 
    - Linux server configuration and troubleshooting in real-time with DevOps best practices
    - Installed and configured Ubuntu Server 24.04 on Google Cloud (console and CLI)
    - Performed user management, group permissions, secure access (public/private key, CHMOD)
    - Managed services: NGINX, SSH, and others
    - Built health check scripts (CPU, memory, disk, network, hardware monitoring)
    - Performed process analysis and termination; troubleshot disk and service issues
- **AI Ops preview**: A future class will automate all manual troubleshooting steps using AI Ops tools — the manual approach taught today is the foundation. 

---

## What I Didn't Fully Get

- **`journalctl -u <service>`** was mentioned briefly but not deeply demonstrated — this command streams the full log history of a specific systemd service and is extremely useful for debugging service failures. You can add `-f` to follow it live, or `--since "1 hour ago"` to filter by time.
- **`kill -11`** **(SIGSEGV)** was mentioned but not explained clearly — this is a segmentation fault signal, typically sent when a process accesses invalid memory. It is not commonly used to manually kill processes; it usually appears as an error signal from the OS itself.
- **OpenTelemetry** was briefly discussed at the end in the context of an SRE interview question — it is an observability framework that combines **metrics, logs, and traces** into a unified standard. Tools like Grafana, Prometheus, and Jaeger can consume OpenTelemetry data. It is particularly relevant for SRE/production support roles. 
- **VPC connectivity between two VMs** was raised as a question but deferred to Day 19 — the concept is that two VMs in different VPCs need **VPC peering** or a shared network configuration to communicate; this will be covered practically later. 
- **Horizontal vs. Vertical scaling use cases** were touched on but not fully resolved — vertical scaling (increasing CPU/RAM on the same VM) requires stopping the VM in GCP/AWS; VMware supports **hot-plugging** (adding RAM without stopping), but GCP/AWS use cold-plug only. 
- **`systemctl`** **vs.** **`journalctl`**: `systemctl` manages service state (start/stop/enable); `journalctl` reads logs from the systemd journal. Both are part of the `systemd` ecosystem and complement each other. 
- **Disk fragmentation in Linux** was glossed over — Linux filesystems like ext4 are designed to minimize fragmentation; tools like `e4defrag` exist but are rarely needed. The `smartctl` tool is more relevant for disk health monitoring.

---

## Might Show Up on the Exam

- **Log file location**: `/var/log/` is the standard directory for all system and application logs on Linux 
- **`tail -f`** **vs** **`tail -n`**: `-f` follows live updates; `-n` (or `-<number>`) shows last N lines — know the difference 
- **`chmod +x`** **is required** before executing any shell script — Linux does not grant execute permission by default 
- **Shebang line**: Every shell script must start with `#!/bin/bash` to specify the interpreter 
- **`systemctl status <service>`** reveals both service state AND the log path — useful in troubleshooting interviews 
- **`kill -9`** **= forceful (SIGKILL),** **`kill -15`** **= graceful (SIGTERM)** — use "graceful" terminology in interviews 
- **Swap memory** = virtual memory from disk; used when RAM is full or to offload cold pages — equivalent to Windows page file 
- **Instance type naming conventions** across clouds: AWS uses `C6i.4xlarge` style; GCP uses `N2-standard-16`; Azure uses `Standard_F16s_v2` 
- **`df -h`** = disk space by partition; **`free -h`** = RAM/swap usage; **`top`****/****`htop`** = live CPU/process view — all standard performance commands 
- **Telnet** checks port connectivity (`telnet <host> <port>`); SSH is for secure login — they serve different purposes 
- **Auto-delete VM setting** in GCP: Machine Configuration > VM Provisioning > Set time limit — VM must be stopped before this setting can be edited 
- **Single project rule**: Always create VMs within one GCP project; creating multiple projects leads to billing confusion and resource fragmentation 
- **NGINX** can act as: web server, load balancer, reverse proxy, streaming server, or messaging server — know which role it plays in your use case 
- **80% disk threshold** is the standard warning threshold for production monitoring scripts; alerts should trigger at or above this level

# Linux Troubleshooting — 30 Interview Questions & Answers + 20 Scenario-Based Q&A

> Based on the provided Linux Troubleshooting session notes. The questions and explanations stay focused on the commands, concepts, and troubleshooting flow covered in the source material.

---

## Part 1 — 30 Linux Interview Questions & Answers

### 1. What is Linux troubleshooting?

**Answer:**  
Linux troubleshooting is the systematic process of identifying, diagnosing, and resolving issues on Linux servers. It commonly includes checking logs, CPU and memory performance, disk usage, services, permissions, processes, hardware, and network connectivity.

**Explanation:**  
In a production environment, a DevOps engineer should be able to investigate an issue independently instead of immediately depending on L1/L2 support.

---

### 2. What is the first thing you should check when a Linux server has an issue?

**Answer:**  
Check the system logs first.

**Explanation:**  
Logs are one of the primary sources of truth when something goes wrong. The standard Linux log directory is:

```bash
/var/log/
```

You can inspect logs using commands such as `tail`, `cat`, `grep`, and `journalctl`.

---

### 3. Where are Linux system logs stored?

**Answer:**

```bash
/var/log/
```

**Explanation:**  
Common logs include:

- `auth.log` — authentication and sudo-related events
- `kern.log` — kernel-level messages
- `dpkg.log` — package installation/removal history
- `syslog` — general system messages
- `dmesg` — kernel and boot-time hardware messages
- NGINX logs — commonly under `/var/log/nginx/`

---

### 4. What is the difference between `tail -f` and `tail -n`?

**Answer:**  
`tail -f` continuously follows a log file as new lines are written. `tail -n` displays a specific number of last lines.

Example:

```bash
tail -f /var/log/syslog
tail -n 20 /var/log/syslog
```

**Explanation:**  
Use `tail -f` when monitoring a live application or service. Use `tail -n` when you only need recent log entries.

---

### 5. How do you search for errors inside a log file?

**Answer:**

```bash
cat /var/log/syslog | grep error
```

You can also search for terms such as:

```bash
grep failed /var/log/auth.log
grep warning /var/log/syslog
grep exception application.log
```

**Explanation:**  
Filtering logs helps reduce a large amount of output and focus on a specific problem.

---

### 6. What is `top` used for?

**Answer:**  
`top` provides a real-time view of CPU usage, memory usage, running processes, tasks, and load average.

```bash
top
```

**Explanation:**  
It is one of the first commands to use when a server appears slow or overloaded.

---

### 7. What is the difference between `top` and `htop`?

**Answer:**  
Both are used for real-time performance monitoring. `htop` provides a more interactive and user-friendly view.

```bash
htop
```

If it is not installed:

```bash
apt install htop
```

**Explanation:**  
`top` is commonly available by default, while `htop` may need to be installed.

---

### 8. What does `free -h` show?

**Answer:**

```bash
free -h
```

It shows RAM and swap memory usage in human-readable format.

**Explanation:**  
Use it when investigating memory-related issues, such as high memory utilization or insufficient RAM.

---

### 9. What does `df -h` show?

**Answer:**

```bash
df -h
```

It shows disk space usage by filesystem or partition in human-readable format.

**Explanation:**  
If an application is failing because the filesystem is full, `df -h` is one of the first commands to run.

---

### 10. What is the difference between `df` and `du`?

**Answer:**  
`df -h` shows filesystem/partition-level disk usage. `du` helps identify disk usage by directories or files.

Examples:

```bash
df -h
du
du -sh /var/log
```

**Explanation:**  
A useful troubleshooting flow is: use `df` to identify which filesystem is full, then use `du` to identify which directory is consuming the space.

---

### 11. What does `ps -ef` do?

**Answer:**

```bash
ps -ef
```

It lists running processes with detailed information, including process IDs.

**Explanation:**  
The PID is important when you need to investigate or terminate a problematic process.

---

### 12. What is `uptime` used for?

**Answer:**

```bash
uptime
```

It shows how long the server has been running and provides load-average information.

**Explanation:**  
It is a quick way to check server uptime and get an initial indication of system load.

---

### 13. What is swap memory?

**Answer:**  
Swap is virtual memory allocated from disk space. It can be used when RAM is insufficient or for inactive memory pages.

**Explanation:**  
It is similar conceptually to the Windows page file. Swap is slower than RAM because it uses disk storage.

---

### 14. What is a shell script and why is it useful in DevOps?

**Answer:**  
A shell script is an executable file containing shell commands. It allows repetitive commands to be automated.

Example:

```bash
#!/bin/bash

free -h
df -h
uptime
```

**Explanation:**  
Instead of manually running several health-check commands, a DevOps engineer can run one script. The script can also be scheduled using cron.

---

### 15. What is the shebang line?

**Answer:**  
The shebang specifies which interpreter should execute the script.

Example:

```bash
#!/bin/bash
```

**Explanation:**  
For Bash scripts, `#!/bin/bash` tells Linux to use Bash as the interpreter.

---

### 16. Why do we use `chmod +x` on a shell script?

**Answer:**

```bash
chmod +x script.sh
```

It grants execute permission to the script.

Then it can be executed using:

```bash
./script.sh
```

**Explanation:**  
Linux does not automatically grant execute permission to a newly created file.

---

### 17. What is `systemctl` used for?

**Answer:**  
`systemctl` manages services controlled by systemd.

Examples:

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
```

**Explanation:**  
The same concept applies to services such as NGINX, SSH, Docker, and other installed services.

---

### 18. What is the difference between `systemctl` and `journalctl`?

**Answer:**  
`systemctl` manages service state, while `journalctl` reads systemd journal logs.

Examples:

```bash
systemctl status nginx
journalctl -u nginx
```

**Explanation:**  
Use `systemctl` to understand whether a service is running or failed. Use `journalctl` to investigate the service's logs.

---

### 19. How do you check logs for a specific systemd service?

**Answer:**

```bash
journalctl -u nginx
```

To follow logs:

```bash
journalctl -u nginx -f
```

To check recent history:

```bash
journalctl -u nginx --since "1 hour ago"
```

**Explanation:**  
This is useful when a service starts and then fails, or when you need the exact error generated by systemd.

---

### 20. Where are NGINX logs stored?

**Answer:**

```bash
/var/log/nginx/
```

Common files include:

```bash
access.log
error.log
```

To monitor an error log live:

```bash
tail -f /var/log/nginx/error.log
```

**Explanation:**  
The access log shows requests, while the error log is especially useful for troubleshooting NGINX problems.

---

### 21. What is the difference between `kill -15` and `kill -9`?

**Answer:**

```bash
kill -15 <PID>
kill -9 <PID>
```

`kill -15` sends SIGTERM and requests graceful termination. `kill -9` sends SIGKILL and forcefully terminates the process.

**Explanation:**  
In production, prefer graceful termination first. Use SIGKILL only when the process does not terminate properly or requires immediate forceful termination.

---

### 22. How do you find the PID of a problematic process?

**Answer:**  
Use commands such as:

```bash
ps -ef
top
```

**Explanation:**  
Once the PID is identified, you can investigate the process and, if appropriate, terminate it with `kill`.

---

### 23. What is `lsblk` used for?

**Answer:**

```bash
lsblk
```

It lists block devices and helps identify disks and partitions.

**Explanation:**  
It is useful during disk troubleshooting when you need to understand which disks and partitions are attached to the system.

---

### 24. What is `smartctl` used for?

**Answer:**  
`smartctl` is used to check disk health and SMART information for supported HDDs/SSDs.

It is provided by the `smartmontools` package.

**Explanation:**  
It can help identify disk health problems, temperature information, and device error data.

---

### 25. What is `dmesg` used for?

**Answer:**

```bash
dmesg
```

It displays messages from the Linux kernel ring buffer.

**Explanation:**  
It is useful for investigating hardware detection, device connection/disconnection events, boot messages, and kernel-level errors.

---

### 26. What is `lscpu` used for?

**Answer:**

```bash
lscpu
```

It displays CPU architecture and CPU configuration information.

**Explanation:**  
It can help investigate CPU-related configuration problems, especially when working with physical or virtual systems.

---

### 27. How do you check network port connectivity from Linux?

**Answer:**

```bash
telnet <hostname> <port>
```

**Explanation:**  
Telnet can be used as a simple connectivity test to determine whether a destination port is reachable.

For example:

```bash
telnet example.com 80
```

---

### 28. What is `tcpdump` used for?

**Answer:**  
`tcpdump` captures network packets from the command line.

**Explanation:**  
It is useful for diagnosing network latency, packet drops, connectivity problems, and timeout issues. Wireshark provides a graphical way to analyze packet captures.

---

### 29. What is the difference between internal and external IP addresses on a cloud VM?

**Answer:**  
An internal IP is a private address used for communication inside the VPC/network. An external IP is a public address that can be reachable over the internet, depending on firewall and network configuration.

**Explanation:**  
For cloud troubleshooting, knowing whether traffic is internal or internet-facing is important.

---

### 30. What is horizontal scaling vs vertical scaling?

**Answer:**  
Vertical scaling means increasing resources on the same VM, such as CPU or RAM. Horizontal scaling means adding more VMs/instances.

**Explanation:**  
For an overloaded production application, horizontal scaling combined with load balancing can distribute traffic across multiple servers. Vertical scaling increases the capacity of an existing server.

---

# Part 2 — 20 Real-Time Scenario-Based Interview Questions & Answers

## Scenario 1 — Linux Server Is Very Slow

### Question
A production Linux server is very slow. What will you check first?

### Answer
Start with performance commands:

```bash
uptime
top
free -h
df -h
ps -ef
```

### Explanation
First identify whether the issue is CPU, memory, disk, load, or a particular process. Do not immediately restart or kill processes without analysis.

---

## Scenario 2 — CPU Usage Is Very High

### Question
CPU usage suddenly reaches 95–100%. How will you troubleshoot it?

### Answer

```bash
top
ps -ef
```

Identify which process is consuming CPU. Check its behavior and application logs before deciding whether action is required.

### Explanation
High CPU can be expected during batch jobs or heavy processing. In production, first determine whether the usage is legitimate before killing anything.

---

## Scenario 3 — Memory Is Almost Full

### Question
A server has very little free RAM. What will you check?

### Answer

```bash
free -h
top
```

Check memory-consuming processes and also inspect swap usage.

### Explanation
Swap can provide virtual memory when RAM is insufficient, but it uses disk and is slower than RAM. The goal is to understand why memory consumption increased.

---

## Scenario 4 — Disk Is 95% Full

### Question
Your monitoring shows that `/` is 95% full. What will you do?

### Answer

First check:

```bash
df -h
```

Then identify large directories:

```bash
du -sh /*
du -sh /var/*
```

### Explanation
`df` tells you which filesystem is full. `du` helps locate the directories consuming the space. Logs are one area worth investigating because application and system logs can grow over time.

---

## Scenario 5 — Application Cannot Write to Disk

### Question
An application says it cannot write files. What will you check?

### Answer

Check filesystem space:

```bash
df -h
```

Then inspect the relevant directory:

```bash
du -sh <directory>
```

Also check permissions:

```bash
ls -l <directory>
```

### Explanation
The problem could be disk exhaustion or incorrect permissions. Check both instead of assuming only one cause.

---

## Scenario 6 — NGINX Is Not Working

### Question
Users report that the NGINX application is unavailable. What commands will you run?

### Answer

```bash
systemctl status nginx
```

If required:

```bash
journalctl -u nginx
```

Then check NGINX logs:

```bash
tail -f /var/log/nginx/error.log
```

### Explanation
Start with service state, then inspect systemd and application logs. This provides evidence about why the service is unavailable.

---

## Scenario 7 — NGINX Service Is Failed

### Question
`systemctl status nginx` shows that NGINX has failed. What is your next step?

### Answer

Run:

```bash
journalctl -u nginx
```

and inspect:

```bash
tail -f /var/log/nginx/error.log
```

### Explanation
Do not blindly restart repeatedly. First identify the error. The logs may indicate configuration, resource, or startup problems.

---

## Scenario 8 — Shell Script Says Permission Denied

### Question
You created `monitor.sh`, but `./monitor.sh` returns permission denied. What will you do?

### Answer

```bash
chmod +x monitor.sh
./monitor.sh
```

### Explanation
A new file does not automatically receive execute permission. `chmod +x` adds execute permission.

---

## Scenario 9 — Service Does Not Start After Reboot

### Question
NGINX works now, but after reboot it does not start automatically. What will you check?

### Answer

```bash
systemctl status nginx
systemctl enable nginx
```

### Explanation
`enable` configures the service to start during boot. `start` only starts it for the current runtime.

---

## Scenario 10 — Process Is Consuming Too Many Resources

### Question
A process is consuming abnormal CPU and memory. How will you handle it in production?

### Answer

First identify the process:

```bash
top
ps -ef
```

Check its logs and determine whether the workload is expected. If termination is approved, try:

```bash
kill -15 <PID>
```

If graceful termination fails:

```bash
kill -9 <PID>
```

### Explanation
Use graceful termination first. Never kill a production process without analysis or appropriate approval.

---

## Scenario 11 — Java Application Has High CPU

### Question
A Java application is consuming high CPU. What can a DevOps engineer do?

### Answer
Identify the process and collect relevant evidence. For a Java application, a thread dump can be provided to the developer for application-level analysis.

### Explanation
A DevOps engineer can identify the resource issue and collect diagnostic information, while the developer may need to investigate the application thread behavior.

---

## Scenario 12 — SSH Is Not Working

### Question
You cannot SSH into a Linux VM. What will you check?

### Answer
Check whether the SSH service is running:

```bash
systemctl status sshd
```

Also verify network connectivity, port 22 reachability, and SSH configuration such as:

```bash
/etc/ssh/sshd_config
```

### Explanation
SSH failures can come from the service, network path, configuration, keys, or access rules.

---

## Scenario 13 — SSH Session Keeps Disconnecting

### Question
Your SSH session repeatedly disconnects. What is a possible cause?

### Answer
Network latency or packet drops can cause SSH sessions to break.

### Explanation
Use network troubleshooting tools such as `tcpdump` for packet-level investigation. Unlike applications that automatically reconnect, a broken SSH shell generally needs to be reconnected.

---

## Scenario 14 — Disk Is Detected as Unhealthy

### Question
How would you investigate a possible disk hardware problem?

### Answer

Use:

```bash
lsblk
dmesg
smartctl
```

### Explanation
`lsblk` shows block devices, `dmesg` can show kernel/device errors, and `smartctl` can provide disk health information.

---

## Scenario 15 — Hardware Device Suddenly Disappears

### Question
A disk was available earlier but is now missing. What would you check?

### Answer

```bash
lsblk
dmesg
```

### Explanation
`lsblk` confirms whether the device is visible to the OS. `dmesg` can reveal device disconnects, driver issues, or kernel-level hardware messages.

---

## Scenario 16 — Production Server Shows Kernel Panic

### Question
A production server reports a kernel panic. What area would you investigate?

### Answer
Investigate kernel, hardware, OS, firmware, and compatibility information.

Useful command:

```bash
dmesg
```

### Explanation
The session notes identify kernel panic as an important hardware/OS-related issue and mention OS/firmware mismatch and compatibility problems as possible areas to investigate.

---

## Scenario 17 — Need to Monitor Logs While Developer Tests

### Question
A developer is reproducing an issue and you need to watch logs live. What command will you use?

### Answer

```bash
tail -f <logfile>
```

For a systemd service:

```bash
journalctl -u <service> -f
```

### Explanation
Both commands allow real-time observation as new log entries are generated.

---

## Scenario 18 — Need a Daily Health Check

### Question
You manually run `free -h`, `df -h`, and `uptime every day`. How would you automate it?

### Answer
Create a Bash script:

```bash
#!/bin/bash

echo "Memory"
free -h

echo "Disk"
df -h

echo "Uptime"
uptime
```

Then make it executable:

```bash
chmod +x monitor.sh
```

Schedule it using cron.

### Explanation
Shell scripting reduces repetitive manual work, and cron can execute the health-check script automatically.

---

## Scenario 19 — Disk Usage Crosses 80%

### Question
You are creating a production monitoring script. What should happen when disk usage reaches the warning threshold?

### Answer
The script can check the disk percentage and display a warning when usage reaches or exceeds the configured threshold, such as 80%.

### Explanation
The session used 80% as a standard warning threshold example. The script can later be extended to send email alerts or integrate with monitoring systems.

---

## Scenario 20 — Production Server Is Overloaded

### Question
One production VM is receiving too much traffic. What architecture-level solution can you consider?

### Answer
Consider horizontal scaling with multiple VMs and a load balancer.

Example architecture:

```text
Users
  |
Load Balancer
  |
  +---- VM 1
  |
  +---- VM 2
  |
  +---- VM 3
```

### Explanation
A load balancer distributes traffic across multiple servers. This can improve capacity and support a highly available architecture.

---

# Quick Interview Revision

## Important Commands

```bash
# Logs
cd /var/log
tail -f /var/log/syslog
tail -n 20 /var/log/syslog
grep error /var/log/syslog

# Performance
top
htop
free -h
df -h
du
ps -ef
uptime
who
w

# Services
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
journalctl -u nginx
journalctl -u nginx -f

# Permissions
ls -l
chmod +x script.sh
chmod 744 script.sh
chown <user>:<group> <file>

# Processes
ps -ef
kill -15 <PID>
kill -9 <PID>

# Hardware / Disk
lsblk
dmesg
lscpu
smartctl

# Network
telnet <host> <port>
tcpdump
```

## Interview Thumb Rules

1. **Issue → Check logs first.**
2. **Slow server → `top`, `free -h`, `df -h`, `uptime`, `ps -ef`.**
3. **Disk full → `df -h`, then `du`.**
4. **Service problem → `systemctl status`, then `journalctl`.**
5. **Live log monitoring → `tail -f`.**
6. **Script execution permission → `chmod +x`.**
7. **Graceful process termination → `kill -15`.**
8. **Forceful termination → `kill -9`.**
9. **Disk/device issue → `lsblk`, `dmesg`, `smartctl`.**
10. **Port connectivity → `telnet <host> <port>`.**
11. **Network packet troubleshooting → `tcpdump` / Wireshark.**
12. **Repeated health checks → Shell script + cron.**
13. **Historical monitoring → continuous monitoring tools such as Grafana/Datadog.**
14. **Overloaded server → consider horizontal scaling + load balancing.**

---

## Source Coverage Note

This question bank is based on the uploaded Linux Troubleshooting session notes, covering logs, performance, disk troubleshooting, services, permissions, processes, hardware, networking, shell scripting, GCP VM configuration, and production troubleshooting practices.

