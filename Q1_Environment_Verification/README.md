# Question 1 – Linux Environment Verification [4 Marks]

## Scenario
You have joined a company as a Linux support engineer. Before accessing company resources, you must verify your Linux environment and user account details.

---

## Commands Executed and Outputs

### 1. Username and Groups

```bash
$ whoami
hit235
```
**Explanation:** The `whoami` command confirms the current logged-in user is "hit235".

```bash
$ groups
hit235 input wheel
```
**Explanation:** The `groups` command shows that user hit235 belongs to three groups: the primary group "hit235", "input" (for input device access), and "wheel" (admin privileges on Arch Linux).

```bash
$ id
uid=1000(hit235) gid=1000(hit235) groups=1000(hit235),992(input),998(wheel)
```
**Explanation:** The `id` command provides detailed numeric UID/GID information. UID 1000 indicates the first non-root user account, and all group memberships are displayed with their numeric IDs.

---

### 2. Current Shell

```bash
$ echo $SHELL
/usr/bin/zsh
```
**Explanation:** The `$SHELL` variable reveals that the default login shell is Zsh (Z Shell), a feature-rich shell offering advanced auto-completion and customization.

---

### 3. Current Working Directory

```bash
$ pwd
/home/hit235/Documents/Bits_CLI/coursera-cli-sga-1
```
**Explanation:** The `pwd` command prints the full absolute path of the current working directory, confirming we are inside the lab assignment repository.

---

### 4. List of Files and Directories

```bash
$ ls -la
total 12
drwxr-xr-x 3 hit235 hit235 4096 Jun 26 16:06 .
drwxr-xr-x 3 hit235 hit235 4096 Jun 26 16:06 ..
drwxr-xr-x 6 hit235 hit235 4096 Jun 26 16:08 .git
```
**Explanation:** The `ls -la` command lists all files including hidden ones in long format. The workspace contains only the `.git` directory (Git version control metadata), indicating a freshly initialized repository.

---

### 5. System Information

```bash
$ uname -a
Linux carnitrix 6.18.35-1-lts #1 SMP PREEMPT_DYNAMIC Tue, 09 Jun 2026 11:41:56 +0000 x86_64 GNU/Linux
```
**Explanation:** The `uname -a` command shows full system details — hostname "carnitrix", kernel version 6.18.35-1-lts, x86_64 architecture, with SMP and PREEMPT_DYNAMIC support.

```bash
$ cat /etc/hostname
carnitrix
```
**Explanation:** The /etc/hostname file confirms the machine hostname is "carnitrix".

---

### 6. Network Connectivity Verification

```bash
$ ping -c 4 google.com
PING google.com (142.251.222.142) 56(84) bytes of data.
64 bytes from pnmaaa-az-in-f14.1e100.net (142.251.222.142): icmp_seq=1 ttl=118 time=10.7 ms
64 bytes from pnmaaa-az-in-f14.1e100.net (142.251.222.142): icmp_seq=2 ttl=118 time=18.9 ms
64 bytes from pnmaaa-az-in-f14.1e100.net (142.251.222.142): icmp_seq=3 ttl=118 time=19.2 ms
64 bytes from pnmaaa-az-in-f14.1e100.net (142.251.222.142): icmp_seq=4 ttl=118 time=19.1 ms

--- google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 10.696/16.981/19.210/3.629 ms
```
**Explanation:** The `ping` command sends 4 ICMP packets to google.com. All 4 were received with 0% packet loss and ~17ms average latency, confirming the system has working DNS resolution and internet connectivity.

---

## Screenshots

> Screenshots of the above command executions are saved in this folder.

---

## Observations (Summary)

1. The system runs Arch Linux with kernel 6.18.35-1-lts on x86_64 architecture, a modern 64-bit Linux environment.
2. User "hit235" (UID 1000) has administrative access via the "wheel" group, essential for system administration tasks.
3. The default shell is Zsh, providing enhanced productivity features compared to traditional Bash.
4. Network connectivity is fully operational with 0% packet loss, confirming DNS and internet access work correctly.
5. The workspace is a clean Git repository with standard Linux file permissions, ready for project work.

---

## Files in This Folder
- `Environment_Report.txt` – Detailed environment verification report
- `README.md` – This file (commands, outputs, and explanations)
