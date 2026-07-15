# Linux Cheat Sheet

> A comprehensive Linux reference for Task 1 - Foundation & Environment Setup. This guide supports cybersecurity beginners who need practical command fluency for virtual labs, reconnaissance, troubleshooting, and documentation.

## Table of Contents

- [Linux Basics](#linux-basics)
- [Filesystem](#filesystem)
- [Navigation](#navigation)
- [File and Directory Commands](#file-and-directory-commands)
- [Viewing and Searching Text](#viewing-and-searching-text)
- [Permissions](#permissions)
- [Networking](#networking)
- [Package Management](#package-management)
- [Processes](#processes)
- [System Monitoring](#system-monitoring)
- [Compression](#compression)
- [SSH](#ssh)
- [Users and Groups](#users-and-groups)
- [Examples](#examples)
- [Interview Questions](#interview-questions)
- [Tips](#tips)
- [Exercises](#exercises)

## Linux Basics

Linux is an open-source operating system kernel used by servers, cloud platforms, embedded systems, mobile devices, and cybersecurity distributions. In daily use, people often say "Linux" to describe a complete operating system distribution such as Kali, Debian, Ubuntu, Fedora, or Arch. A distribution combines the Linux kernel with system tools, package managers, shells, desktop environments, and applications.

For cybersecurity work, Linux is important because many security tools are built for Unix-like environments. Kali Linux, the attacker machine in this project, includes tools for reconnaissance, enumeration, exploitation research, password auditing, wireless testing, reverse engineering, and forensics. Even when a graphical desktop is available, the terminal remains the most efficient way to inspect network interfaces, run scans, automate tasks, and save repeatable evidence.

The shell is the command interpreter. The most common shell is Bash, although Zsh and Fish are also popular. A command generally follows this pattern:

```bash
command [options] [arguments]
```

Options modify behavior, arguments tell the command what to act on, and output is usually printed to the terminal. Commands can be chained through pipes, redirected to files, or combined in scripts. In a penetration testing lab, this allows a learner to move from manual inspection to repeatable workflows.

| Term | Meaning | Example |
|---|---|---|
| Kernel | Core of the operating system | Manages memory, processes, devices |
| Shell | Command interpreter | Bash |
| Terminal | Interface used to access a shell | Kali terminal |
| Distribution | Complete Linux operating system | Kali Linux |
| Package manager | Installs and updates software | apt |
| Root | Administrative account | UID 0 |

## Filesystem

Linux uses a hierarchical filesystem that begins at `/`, called the root directory. Everything is represented somewhere under this tree: system binaries, configuration files, user home directories, removable media, virtual device files, logs, and temporary files. Understanding this structure is critical because cybersecurity tasks frequently require reading logs, editing configuration, locating tool output, and protecting sensitive files.

| Directory | Purpose | Cybersecurity Relevance |
|---|---|---|
| `/` | Root of the filesystem | Starting point for all absolute paths |
| `/home` | User home directories | Stores user files and shell history |
| `/root` | Root user's home | Sensitive administrative workspace |
| `/etc` | System configuration | Network, service, user, and security configs |
| `/var` | Variable data | Logs, caches, web files, mail queues |
| `/tmp` | Temporary files | Common place for short-lived artifacts |
| `/usr/bin` | User commands | Standard executable programs |
| `/bin` | Essential commands | Basic tools needed during boot |
| `/sbin` | System binaries | Administrative utilities |
| `/dev` | Device files | Disks, terminals, random devices |
| `/proc` | Process/kernel info | Live process and system details |
| `/opt` | Optional software | Third-party tools |

Absolute paths begin at root, such as `/etc/hosts`. Relative paths begin from the current directory, such as `docs/Commands_Used.md`. The dot `.` means current directory and `..` means parent directory.

## Navigation

Navigation commands help you understand where you are and move through the filesystem. This is the first skill required before running tools or collecting evidence.

| Command | Purpose | Example |
|---|---|---|
| `pwd` | Print current directory | `pwd` |
| `ls` | List files | `ls -la` |
| `cd` | Change directory | `cd /etc` |
| `tree` | Display directory tree | `tree -L 2` |
| `clear` | Clear terminal screen | `clear` |

```bash
pwd
ls -la
cd /home/kali
cd ..
cd -
```

The `ls -la` command is especially useful. The `-l` option shows a long listing with permissions, ownership, size, and modification time. The `-a` option includes hidden files, whose names start with a dot. Hidden files often include shell profiles, tool configuration, SSH directories, and history files.

## File and Directory Commands

Creating, copying, moving, and removing files are daily Linux operations. Use these commands carefully because Linux will often do exactly what you ask, even when the result is destructive.

| Command | Purpose | Example |
|---|---|---|
| `touch` | Create an empty file or update timestamp | `touch notes.txt` |
| `mkdir` | Create directory | `mkdir reports` |
| `cp` | Copy files | `cp scan.txt backup.txt` |
| `mv` | Move or rename files | `mv old.txt new.txt` |
| `rm` | Remove files | `rm old.txt` |
| `rmdir` | Remove empty directory | `rmdir emptydir` |
| `file` | Identify file type | `file image.png` |
| `stat` | Show file metadata | `stat README.md` |

```bash
mkdir -p task1/evidence
touch task1/evidence/notes.txt
cp task1/evidence/notes.txt task1/evidence/notes-backup.txt
mv task1/evidence/notes-backup.txt task1/evidence/archive.txt
file screenshots/01-virtualbox.png
```

Avoid running recursive deletion commands until you have verified the path. A small typo can remove important files. In professional work, use backups, version control, and dry-run options where available.

## Viewing and Searching Text

Cybersecurity work produces text: logs, scan results, configuration files, scripts, and reports. Being able to view and search text quickly saves time.

| Command | Purpose | Example |
|---|---|---|
| `cat` | Print entire file | `cat /etc/hosts` |
| `less` | Page through a file | `less scan.txt` |
| `head` | Show first lines | `head -n 20 scan.txt` |
| `tail` | Show last lines | `tail -n 20 /var/log/syslog` |
| `grep` | Search text | `grep "open" scan.txt` |
| `wc` | Count lines, words, bytes | `wc -l scan.txt` |
| `sort` | Sort lines | `sort ports.txt` |
| `uniq` | Remove repeated adjacent lines | `sort ports.txt | uniq` |

```bash
grep -i "open" nmap-results.txt
tail -f /var/log/syslog
head -n 5 /etc/passwd
wc -w docs/Linux_CheatSheet.md
```

The `tail -f` command follows a log as it changes. This is useful when starting a service, reproducing an error, or watching authentication attempts in a lab.

## Permissions

Linux permissions control who can read, write, and execute files. Every file has an owner, a group, and permission bits for user, group, and others.

| Permission | Symbol | Numeric Value | Meaning for Files | Meaning for Directories |
|---|---:|---:|---|---|
| Read | r | 4 | View contents | List names |
| Write | w | 2 | Modify contents | Create or delete entries |
| Execute | x | 1 | Run as program | Enter directory |

Example long listing:

```text
-rwxr-xr-- 1 kali kali 2048 Jul 15 10:00 script.sh
```

This means the owner can read, write, and execute; the group can read and execute; others can read only.

Common permission commands:

| Command | Purpose | Example |
|---|---|---|
| `chmod` | Change permissions | `chmod 755 script.sh` |
| `chown` | Change owner | `sudo chown kali:kali file.txt` |
| `chgrp` | Change group | `chgrp analysts report.txt` |
| `umask` | Default permission mask | `umask` |

```bash
chmod 600 private-key.pem
chmod +x script.sh
sudo chown root:root /etc/example.conf
ls -l
```

Security note: private keys should usually be readable only by the owner. World-writable files in sensitive directories can create privilege escalation opportunities.

## Networking

Networking commands are central to Task 1. They reveal interface status, IP addresses, routes, DNS configuration, and connectivity.

| Command | Purpose | Example |
|---|---|---|
| `ip a` | Show interfaces and addresses | `ip a` |
| `ip route` | Show routing table | `ip route` |
| `ping` | Test reachability | `ping 192.168.56.102` |
| `ss` | Show sockets | `ss -tulpen` |
| `nmap` | Scan hosts and ports | `nmap 192.168.56.102` |
| `traceroute` | Show path to host | `traceroute 8.8.8.8` |
| `dig` | Query DNS | `dig example.com` |
| `curl` | Transfer data over protocols | `curl http://192.168.56.102` |

```bash
ip a
ip route
ping -c 4 192.168.56.102
ss -tulpen
nmap -sV 192.168.56.102
```

In this lab, `ip a` confirms the Kali and Metasploitable2 addresses. `ping` confirms basic connectivity. `nmap` identifies exposed services. These three steps create a clean progression from configuration to validation to reconnaissance.

## Package Management

Kali Linux is Debian-based and uses `apt` for package management. Package commands should be run carefully because they can change system state.

| Command | Purpose | Example |
|---|---|---|
| `sudo apt update` | Refresh package index | `sudo apt update` |
| `sudo apt upgrade` | Upgrade installed packages | `sudo apt upgrade` |
| `sudo apt install` | Install package | `sudo apt install nmap` |
| `sudo apt remove` | Remove package | `sudo apt remove package` |
| `apt search` | Search packages | `apt search wireshark` |
| `apt show` | Show package details | `apt show nmap` |

```bash
sudo apt update
sudo apt install nmap
apt show nmap
```

Security note: use official repositories where possible. Downloading random scripts or packages from unknown sources can compromise the lab machine.

## Processes

A process is a running program. Process management helps identify active tools, stop stuck programs, and understand system activity.

| Command | Purpose | Example |
|---|---|---|
| `ps` | Show processes | `ps aux` |
| `top` | Interactive process viewer | `top` |
| `htop` | Enhanced process viewer | `htop` |
| `kill` | Send signal to process | `kill 1234` |
| `pkill` | Kill by name | `pkill firefox` |
| `jobs` | Show shell jobs | `jobs` |
| `bg` | Resume job in background | `bg %1` |
| `fg` | Bring job to foreground | `fg %1` |

```bash
ps aux | grep nmap
top
kill -15 1234
```

Prefer graceful termination before forceful termination. Signal 15 asks a process to exit cleanly. Signal 9 forces termination and can leave files or state incomplete.

## System Monitoring

Monitoring commands help diagnose performance problems in virtual machines. Low RAM, full disks, or high CPU usage can make scans slow or unreliable.

| Command | Purpose | Example |
|---|---|---|
| `free -h` | Show memory usage | `free -h` |
| `df -h` | Show filesystem usage | `df -h` |
| `du -sh` | Show directory size | `du -sh screenshots` |
| `uptime` | Show load average | `uptime` |
| `dmesg` | Kernel messages | `sudo dmesg` |
| `journalctl` | Systemd logs | `journalctl -xe` |

```bash
free -h
df -h
du -sh docs
uptime
```

If a VM becomes slow, check memory and disk first. Cybersecurity tools can generate large outputs, and graphical desktops can consume substantial RAM.

## Compression

Compression and archiving are useful for saving reports, moving evidence, and packaging logs.

| Command | Purpose | Example |
|---|---|---|
| `tar` | Create or extract archives | `tar -czf evidence.tar.gz screenshots docs` |
| `gzip` | Compress a file | `gzip scan.txt` |
| `gunzip` | Decompress gzip file | `gunzip scan.txt.gz` |
| `zip` | Create zip archive | `zip -r task1.zip docs screenshots` |
| `unzip` | Extract zip archive | `unzip task1.zip` |

```bash
tar -czf task1-evidence.tar.gz docs screenshots
tar -xzf task1-evidence.tar.gz
zip -r Cybersecurity-Task1.zip README.md docs screenshots report
```

Before compressing evidence, make sure it does not contain secrets, tokens, private keys, or personal data that should not be shared.

## SSH

SSH provides encrypted remote shell access. In this task, SSH may appear as an open service on Metasploitable2 during Nmap scanning. Understanding SSH helps separate normal administrative access from risky exposure.

| Command | Purpose | Example |
|---|---|---|
| `ssh user@host` | Connect to remote host | `ssh kali@192.168.56.101` |
| `scp file user@host:/path` | Copy file over SSH | `scp notes.txt user@host:/tmp/` |
| `ssh-keygen` | Generate SSH keys | `ssh-keygen -t ed25519` |
| `ssh -v` | Verbose connection debug | `ssh -v user@host` |

```bash
ssh user@192.168.56.102
ssh-keygen -t ed25519 -C "lab-key"
chmod 600 ~/.ssh/id_ed25519
```

Security notes: disable password login where possible, use key-based authentication, restrict SSH by firewall rules, keep the server updated, and monitor authentication logs.

## Users and Groups

Linux uses users and groups to control access. Cybersecurity analysts should understand accounts because privilege boundaries, file ownership, and service permissions all depend on them.

| Command | Purpose | Example |
|---|---|---|
| `whoami` | Current user | `whoami` |
| `id` | User and group IDs | `id` |
| `who` | Logged-in users | `who` |
| `sudo` | Run command as another user | `sudo apt update` |
| `passwd` | Change password | `passwd` |
| `useradd` | Add user | `sudo useradd analyst` |
| `usermod` | Modify user | `sudo usermod -aG sudo analyst` |

```bash
whoami
id
sudo -l
cat /etc/passwd
cat /etc/group
```

The `sudo -l` command shows which commands the current user can run with elevated privileges. Misconfigured sudo permissions are a common security issue.

## Examples

### Verify Kali IP address

```bash
ip a
```

Look for the host-only interface and an address in the lab subnet, such as `192.168.56.101/24`.

### Verify target reachability

```bash
ping -c 4 192.168.56.102
```

Successful replies show that the two VMs can communicate. Packet loss may indicate the wrong adapter, wrong IP address, firewall behavior, or a powered-off target.

### Save Nmap output

```bash
nmap -sV -oN nmap-service-scan.txt 192.168.56.102
```

The `-oN` option writes normal output to a file. Saving output is better than relying only on screenshots because text can be searched, diffed, and attached to reports.

### Search scan output for open ports

```bash
grep "open" nmap-service-scan.txt
```

This quickly extracts the most important lines from a basic Nmap scan.

## Interview Questions

| Question | Strong Answer Direction |
|---|---|
| What is the difference between Linux and Kali Linux? | Linux is the kernel; Kali is a Debian-based security distribution built around that kernel. |
| What does `chmod 755` mean? | Owner can read/write/execute, group and others can read/execute. |
| Why is `/etc` important? | It stores system and service configuration files. |
| What command shows IP addresses? | `ip a` is the modern command. |
| What is the difference between `su` and `sudo`? | `su` switches users; `sudo` runs approved commands with elevated privileges. |
| Why should private keys use restrictive permissions? | Loose permissions can allow credential theft or SSH refusal. |
| What does a pipe do? | It sends output from one command as input to another command. |
| Why save scan results to files? | Files support evidence retention, review, search, and reporting. |
| What is the purpose of `grep`? | Search text using strings or regular expressions. |
| Why avoid running unknown scripts as root? | Root execution can compromise the entire system. |

## Tips

- Learn command help pages with `man command` and `command --help`.
- Use tab completion to reduce typing errors.
- Use command history with the up arrow and `history`.
- Keep lab notes while working; memory fades quickly after troubleshooting.
- Save scan output in text formats as well as screenshots.
- Prefer absolute paths in scripts when the working directory matters.
- Read error messages carefully; they often name the missing file, permission, or package.
- Use version control for documentation and scripts.
- Treat every destructive command as permanent until proven otherwise.
- Do not run scans outside networks you own or have permission to test.

## Exercises

1. Use `pwd`, `ls -la`, and `cd` to navigate from your home directory to `/etc` and back.
2. Create a directory named `lab-notes`, create a file inside it, and list it with long permissions.
3. Run `ip a` and identify the loopback interface and the host-only interface.
4. Ping your target VM and record packet loss.
5. Save a basic Nmap scan to a text file.
6. Use `grep` to extract open ports from the saved Nmap file.
7. Change a script permission with `chmod +x` and explain what changed.
8. Use `df -h` and `free -h` to check VM resources.
9. Create a compressed archive of your docs folder.
10. Write a short note explaining why Metasploitable2 should remain isolated.

## Quick Reference Table

| Task | Command |
|---|---|
| Current directory | `pwd` |
| List all files | `ls -la` |
| Change directory | `cd /path` |
| Show IP addresses | `ip a` |
| Show routes | `ip route` |
| Ping target | `ping -c 4 192.168.56.102` |
| Basic scan | `nmap 192.168.56.102` |
| Service scan | `nmap -sV 192.168.56.102` |
| Show sockets | `ss -tulpen` |
| Show memory | `free -h` |
| Show disk | `df -h` |
| Search text | `grep "text" file` |
| View logs | `journalctl -xe` |
| Archive folder | `tar -czf archive.tar.gz folder` |

Linux proficiency grows through repetition. For Task 1, the most important habit is to verify before assuming: verify the interface, verify the IP address, verify connectivity, verify the scan target, and verify the documentation evidence.


## Extended Linux Practice Notes

Linux skill is built by connecting commands to real operating system behavior. A command such as `ls` is not only a way to list files; it is a way to inspect permissions, ownership, hidden configuration, timestamps, and evidence organization. A command such as `ip a` is not only an address lookup; it confirms whether the kernel sees an interface, whether the interface is up, and whether the assigned address belongs to the expected subnet. This section adds deeper practice context for learners who want to move beyond memorizing commands.

### Shell Habits for Cybersecurity Work

The shell rewards careful habits. Before running a command that changes files, confirm the current directory with `pwd`. Before scanning an address, confirm that the address belongs to the lab target. Before copying output into a report, save the original command output. These habits reduce mistakes and make your work easier to defend during review.

| Habit | Why It Matters | Example |
|---|---|---|
| Confirm location | Prevents editing or deleting the wrong files | `pwd` |
| Use readable filenames | Keeps evidence understandable | `nmap-service-scan.txt` |
| Save command output | Preserves repeatable evidence | `nmap -oN scan.txt target` |
| Avoid running as root by default | Reduces accidental damage | Use `sudo` only when needed |
| Read help output | Prevents misuse of options | `nmap --help` |

### Redirection and Pipes

Redirection sends command output to files. Pipes send output from one command into another command. These features are essential for processing logs and scan output.

```bash
nmap -sV 192.168.56.102 > service-scan.txt
grep "open" service-scan.txt
cat service-scan.txt | wc -l
```

`>` overwrites a file, while `>>` appends to a file. Use append carefully so old and new evidence do not become mixed without timestamps.

### Environment Variables

Environment variables store values used by the shell and programs.

| Variable | Purpose |
|---|---|
| `HOME` | Current user's home directory |
| `PATH` | Directories searched for commands |
| `SHELL` | Current shell path |
| `USER` | Current username |
| `PWD` | Current working directory |

```bash
echo $HOME
echo $PATH
which nmap
```

If a command is installed but not found, the `PATH` may not include the directory containing that command.

### Logs and Evidence

Logs tell the story of system activity. On Linux systems that use systemd, `journalctl` is a central log viewer. Traditional logs also appear in `/var/log`.

| Log Location | What It May Contain |
|---|---|
| `/var/log/auth.log` | Authentication activity on Debian-based systems |
| `/var/log/syslog` | General system messages |
| `/var/log/kern.log` | Kernel messages |
| `/var/log/apache2/` | Apache web server logs |
| `~/.bash_history` | User shell command history |

In a professional report, logs and scan output should be handled as evidence. Keep original files unchanged, create working copies for analysis, and avoid editing raw output unless the report clearly states that output was sanitized.

### Text Processing Mini Reference

| Task | Command |
|---|---|
| Find open ports in scan output | `grep "open" scan.txt` |
| Count discovered lines | `grep "open" scan.txt | wc -l` |
| Show unique service names | `awk '{print $3}' scan.txt | sort | uniq` |
| Show first 20 lines | `head -n 20 scan.txt` |
| Show last 20 lines | `tail -n 20 scan.txt` |

Text processing is one reason Linux is popular in security work. Small commands can be combined into powerful workflows without writing full programs.

### Permission Scenarios

If a private SSH key has permissions such as `0644`, other users may be able to read it. The correct command is usually:

```bash
chmod 600 ~/.ssh/id_ed25519
```

If a script cannot run, it may lack execute permission:

```bash
chmod +x verify-lab.sh
./verify-lab.sh
```

If a file belongs to root and a normal user cannot edit it, ownership and privilege should be reviewed. Do not solve every permission problem by making files world-writable. That may fix the immediate error while creating a security weakness.

### Service Management

Many Linux distributions use systemd to manage services.

| Command | Purpose |
|---|---|
| `systemctl status ssh` | Check service status |
| `sudo systemctl start ssh` | Start a service |
| `sudo systemctl stop ssh` | Stop a service |
| `sudo systemctl enable ssh` | Start service at boot |
| `sudo systemctl disable ssh` | Prevent service from starting at boot |

For defenders, service management is part of hardening. Disable services that are not required. For analysts, service status can explain why a port is open or closed.

### More Interview Questions

| Question | Answer Direction |
|---|---|
| What is the difference between absolute and relative paths? | Absolute paths start at `/`; relative paths start at the current directory. |
| What is a hidden file in Linux? | A file whose name begins with a dot. |
| What does `sudo` do? | Runs a permitted command with elevated privileges. |
| Why is `/var/log` important? | It stores logs used for troubleshooting and investigations. |
| What is the purpose of `PATH`? | It tells the shell where to search for executables. |
| What is the risk of `chmod 777`? | It gives everyone read, write, and execute permissions. |
| How do you find listening ports locally? | Use `ss -tulpen` or similar tools. |
| Why use `less` instead of `cat` for large files? | `less` lets you page and search without flooding the terminal. |

### Additional Exercises

1. Create a file named `permissions-demo.txt`, inspect it with `ls -l`, change it to `600`, and explain who can read it.
2. Run `ip route` and identify whether the lab subnet is directly connected.
3. Save the output of `ss -tulpen` to a file and identify any listening services.
4. Use `grep` to search this cheat sheet for the word `Nmap`.
5. Create a tar archive of the `docs` folder, list its contents, and extract it into a temporary directory.
6. Compare `head`, `tail`, and `less` on the same file.
7. Use `man chmod` and identify the difference between symbolic and numeric permission modes.
8. Write a short paragraph explaining why root access should be limited.

The best Linux users are not people who memorize every command. They are people who know how to ask the system good questions, read the answer carefully, and make the smallest safe change needed to move forward.
