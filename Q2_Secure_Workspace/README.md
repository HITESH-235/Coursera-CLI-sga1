# Question 2 – Secure Project Workspace Setup [4 Marks]

## Scenario
A project team requires a secure workspace for storing project files and documentation. Create the workspace and configure appropriate permissions.

---

## Commands Executed and Outputs

### 1. Create Directory Structure

```bash
$ mkdir -p project_workspace/{docs,src,config,logs}
```
**Explanation:** Created a structured workspace with four subdirectories using brace expansion. The `-p` flag creates parent directories as needed without throwing errors.

```bash
$ find project_workspace -type d
project_workspace
project_workspace/docs
project_workspace/src
project_workspace/logs
project_workspace/config
```
**Explanation:** Verified the directory tree was created correctly. The `find` command with `-type d` recursively lists only directories.

---

### 2. Create Sample Project Files

```bash
$ echo "Project documentation" > project_workspace/docs/README.md
$ echo "Source code placeholder" > project_workspace/src/main.py
$ echo "Config placeholder" > project_workspace/config/settings.conf
$ echo "Log placeholder" > project_workspace/logs/app.log
```
**Explanation:** Created sample files in each subdirectory using output redirection (`>`). These files simulate real project artifacts for demonstrating permission management.

---

### 3. Permissions BEFORE Modification

```bash
$ ls -laR project_workspace
project_workspace:
drwxr-xr-x 6 hit235 hit235 4096 Jun 26 16:09 .
drwxr-xr-x 2 hit235 hit235 4096 Jun 26 16:09 config
drwxr-xr-x 2 hit235 hit235 4096 Jun 26 16:09 docs
drwxr-xr-x 2 hit235 hit235 4096 Jun 26 16:09 logs
drwxr-xr-x 2 hit235 hit235 4096 Jun 26 16:09 src

project_workspace/config:
-rw-r--r-- 1 hit235 hit235   19 Jun 26 16:09 settings.conf

project_workspace/docs:
-rw-r--r-- 1 hit235 hit235   22 Jun 26 16:09 README.md

project_workspace/logs:
-rw-r--r-- 1 hit235 hit235   16 Jun 26 16:09 app.log

project_workspace/src:
-rw-r--r-- 1 hit235 hit235   24 Jun 26 16:09 main.py
```
**Explanation:** Default permissions show directories at 755 and files at 644 (determined by umask 0022). All directories are world-readable and executable, which is too permissive for a secure workspace.

---

### 4. Modify Permissions

```bash
$ chmod 750 project_workspace        # Owner: rwx, Group: r-x, Others: ---
$ chmod 700 project_workspace/config # Owner-only access for sensitive config
$ chmod 640 project_workspace/config/settings.conf  # Owner: rw, Group: r
$ chmod 755 project_workspace/docs   # Public-readable documentation
$ chmod 644 project_workspace/docs/README.md        # Public-readable file
$ chmod 750 project_workspace/src    # Owner + Group access for source code
$ chmod 750 project_workspace/logs   # Owner + Group access for logs
$ chmod 640 project_workspace/logs/app.log           # Owner: rw, Group: r
```
**Explanation:** Applied the principle of least privilege. Config gets the most restrictive access (700), source and logs allow group access (750), and docs remains publicly readable (755).

---

### 5. Permissions AFTER Modification

```bash
$ ls -laR project_workspace
project_workspace:
drwxr-x--- 6 hit235 hit235 4096 Jun 26 16:09 .
drwx------ 2 hit235 hit235 4096 Jun 26 16:09 config
drwxr-xr-x 2 hit235 hit235 4096 Jun 26 16:09 docs
drwxr-x--- 2 hit235 hit235 4096 Jun 26 16:09 logs
drwxr-x--- 2 hit235 hit235 4096 Jun 26 16:09 src

project_workspace/config:
-rw-r----- 1 hit235 hit235   19 Jun 26 16:09 settings.conf

project_workspace/docs:
-rw-r--r-- 1 hit235 hit235   22 Jun 26 16:09 README.md

project_workspace/logs:
-rw-r----- 1 hit235 hit235   16 Jun 26 16:09 app.log

project_workspace/src:
-rw-r--r-- 1 hit235 hit235   24 Jun 26 16:09 main.py
```
**Explanation:** After modification, the config directory is restricted to owner-only (700), src and logs are accessible to owner and group (750), and docs remains world-readable. This creates a secure tiered access model.

---

### 6. Ownership Details

```bash
$ stat project_workspace
  File: project_workspace
  Size: 4096            Blocks: 8          IO Block: 4096   directory
  Device: 259,8   Inode: 13428507    Links: 6
  Access: (0750/drwxr-x---)  Uid: ( 1000/  hit235)   Gid: ( 1000/  hit235)
  Access: 2026-06-26 16:09:36.829878339 +0530
  Modify: 2026-06-26 16:09:36.793212609 +0530
  Change: 2026-06-26 16:09:36.809878850 +0530
  Birth:  2026-06-26 16:09:36.793212609 +0530
```
**Explanation:** The `stat` command shows detailed metadata including the inode, permission bits in both octal (0750) and symbolic format, and all four timestamps. The owner and group are both hit235 (UID/GID 1000).

---

### 7. Umask Value

```bash
$ umask
0022

$ umask -S
u=rwx,g=rx,o=rx
```
**Explanation:** The umask value of 0022 means new files default to 644 (666-022) and directories to 755 (777-022). The symbolic output confirms that the owner gets full permissions while group and others get read+execute.

---

## How Permissions Protect Project Data

| Directory | Permission | Octal | Who Can Access |
|-----------|-----------|-------|---------------|
| project_workspace | drwxr-x--- | 750 | Owner (full), Group (read/traverse) |
| config | drwx------ | 700 | Owner only |
| docs | drwxr-xr-x | 755 | Everyone (read) |
| src | drwxr-x--- | 750 | Owner (full), Group (read/traverse) |
| logs | drwxr-x--- | 750 | Owner (full), Group (read/traverse) |

The permission structure follows the **Principle of Least Privilege**: sensitive configuration data is accessible only to the owner, source code and logs are shared with the team (group), and documentation is open to all. This ensures that critical project data remains protected while enabling necessary collaboration.

---

## Files in This Folder
- `Project_Workspace_Report.txt` – Detailed workspace setup report
- `README.md` – This file (commands, outputs, and explanations)
- `project_workspace/` – The actual workspace created with configured permissions
