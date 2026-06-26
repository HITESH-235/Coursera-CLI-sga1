# Question 4 – File Access and I/O Investigation [4 Marks]

## Scenario
An application is experiencing logging issues. You have been assigned to investigate file access and I/O operations.

---

## Commands Executed and Outputs

### 1. Open Files Identified

```bash
$ ls -la /proc/$$/fd
lrwx------ 1 hit235 hit235 64 Jun 26 16:10 0 -> /dev/pts/1
lrwx------ 1 hit235 hit235 64 Jun 26 16:10 1 -> /dev/pts/1
lrwx------ 1 hit235 hit235 64 Jun 26 16:10 2 -> /dev/pts/1
lr-x------ 1 hit235 hit235 64 Jun 26 16:10 3 -> /opt/antigravity-ide/icudtl.dat
lrwx------ 1 hit235 hit235 64 Jun 26 16:10 5 -> 'socket:[5273872]'
lr-x------ 1 hit235 hit235 64 Jun 26 16:10 7 -> 'pipe:[5273874]'
l-wx------ 1 hit235 hit235 64 Jun 26 16:10 8 -> 'pipe:[5273874]'
lrwx------ 1 hit235 hit235 64 Jun 26 16:10 48 -> /dev/dri/renderD128
lr-x------ 1 hit235 hit235 64 Jun 26 16:10 66 -> anon_inode:inotify
l-wx------ 1 hit235 hit235 64 Jun 26 16:10 85 -> '.config/Antigravity IDE/logs/.../main.log'
```
**Explanation:** The `/proc/$$/fd` directory lists all open file descriptors for the current process. Each entry is a symlink showing the actual file/resource. The process has regular files, sockets, pipes, GPU devices, and inotify watches open — demonstrating Linux's "everything is a file" philosophy.

---

### 2. File Descriptor Observations

```
FD 0 (stdin)  -> /dev/pts/1  (pseudo-terminal input)
FD 1 (stdout) -> /dev/pts/1  (pseudo-terminal output)
FD 2 (stderr) -> /dev/pts/1  (pseudo-terminal error output)
```
**Explanation:** Every process inherits three standard file descriptors. FD 0 is for reading input, FD 1 for writing normal output, and FD 2 for writing errors. All are connected to the same pseudo-terminal (`/dev/pts/1`).

Other observed file descriptor types:
- **FD 3, 4, 11**: Read-only data files (application resources)
- **FD 5, 6, 46, 47**: Network sockets for IPC
- **FD 7 (read), 8 (write)**: A pipe pair for inter-process communication
- **FD 15–24**: Event poll/event fd for asynchronous I/O
- **FD 48**: GPU render device (`/dev/dri/renderD128`)
- **FD 66**: inotify for filesystem event monitoring
- **FD 85, 121**: Write-only log file descriptors

---

### 3. Output Redirection

```bash
$ echo "This is standard output" > stdout_test.txt
$ cat stdout_test.txt
This is standard output
```
**Explanation:** The `>` operator redirects stdout (FD 1) to a file. The output of `echo` is written to `stdout_test.txt` instead of the terminal. The file is created if it doesn't exist or truncated if it does.

---

### 4. Error Redirection

```bash
$ ls /nonexistent_directory 2> stderr_test.txt
Exit code: 2

$ cat stderr_test.txt
ls: cannot access '/nonexistent_directory': No such file or directory
```
**Explanation:** The `2>` operator redirects stderr (FD 2) to a file. The error message from the failed `ls` command is captured in `stderr_test.txt` instead of displaying on the terminal.

---

### 5. Combined Redirection

```bash
$ ls /home /nonexistent_path > combined_output.txt 2>&1
$ cat combined_output.txt
ls: cannot access '/nonexistent_path': No such file or directory
/home:
hit235
```
**Explanation:** The `2>&1` syntax redirects stderr to the same destination as stdout. Both the error (from the invalid path) and normal output (listing /home) are captured in a single file — essential for comprehensive logging.

---

### 6. Append Redirection

```bash
$ echo "First line" > append_test.txt
$ echo "Second line" >> append_test.txt
$ cat append_test.txt
First line
Second line
```
**Explanation:** The `>>` operator appends data to a file instead of overwriting it. This is critical for log files where data must accumulate over time without losing previous entries.

---

### 7. Resource Limits

```bash
$ ulimit -a
real-time non-blocking time  (microseconds, -R) 200000
core file size              (blocks, -c) unlimited
data seg size               (kbytes, -d) unlimited
file size                   (blocks, -f) unlimited
pending signals                     (-i) 125638
max locked memory           (kbytes, -l) 8192
max memory size             (kbytes, -m) unlimited
open files                          (-n) 524288
pipe size                (512 bytes, -p) 8
stack size                  (kbytes, -s) 8192
cpu time                   (seconds, -t) unlimited
max user processes                  (-u) 125638
virtual memory              (kbytes, -v) unlimited
file locks                          (-x) unlimited
```
**Explanation:** `ulimit -a` displays all per-process resource limits. Key limits include 524288 open files, 125638 max processes, 8 MB stack size, and unlimited virtual memory.

```bash
$ ulimit -n
524288
```
**Explanation:** The open file limit is 524288 (512K), which determines the maximum number of file descriptors a single process can have open simultaneously.

---

### 8. System-Wide File Statistics

```bash
$ cat /proc/sys/fs/file-nr
13077   0       9223372036854775807
```
**Explanation:** Shows system-wide FD stats: 13077 currently allocated, 0 free (unused field in modern kernels), and a maximum of ~9.2 × 10¹⁸ (effectively unlimited).

```bash
$ cat /proc/sys/fs/file-max
9223372036854775807
```
**Explanation:** The system-wide maximum FDs is set to the maximum 64-bit signed integer (2⁶³ - 1), meaning the kernel places no practical limit on total file descriptors.

---

## How Linux Manages File I/O

| Concept | Description |
|---------|-------------|
| File Descriptors | Small integers mapping to kernel file structures |
| VFS Layer | Uniform API for all filesystem types |
| I/O Redirection | Shell operators (>, >>, 2>, 2>&1) manipulate FDs |
| Everything is a File | Devices, sockets, pipes all use the same FD interface |
| Resource Limits | Per-process (ulimit) and system-wide (/proc/sys/fs) controls |

---

## Technical Observations

1. The process had dozens of open FDs including regular files, sockets, pipes, and devices — all managed through the unified file descriptor interface.
2. Output redirection (`>`) truncates files; append (`>>`) preserves existing content — crucial for log management.
3. Error redirection (`2>`) captures stderr separately, enabling proper error logging in applications.
4. The system supports 524288 open files per process and has 13077 FDs currently allocated system-wide.
5. The `/proc` filesystem is the primary tool for real-time investigation of process I/O and system resources.

---

## Files in This Folder
- `IO_Investigation_Report.txt` – Detailed I/O investigation report
- `README.md` – This file (commands, outputs, and explanations)
- `stdout_test.txt` – Output redirection test file
- `stderr_test.txt` – Error redirection test file
- `combined_output.txt` – Combined stdout+stderr redirection test
- `append_test.txt` – Append redirection test file
