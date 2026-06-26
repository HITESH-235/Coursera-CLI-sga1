# Question 3 – File System and Link Analysis [4 Marks]

## Scenario
A developer accidentally deleted a file and wants to understand how Linux links work. Create a file, hard link, and symbolic link and investigate their behavior.

---

## Commands Executed and Outputs

### 1. Create Original File

```bash
$ echo "This is the original file for link analysis experiment." > original_file.txt
```
**Explanation:** Created a text file with sample content to serve as the base for link experiments.

---

### 2. Create Hard Link

```bash
$ ln original_file.txt hard_link.txt
```
**Explanation:** Created a hard link that shares the same inode as the original file. Both names point to the same data blocks on disk.

---

### 3. Create Symbolic Link

```bash
$ ln -s original_file.txt symbolic_link.txt
```
**Explanation:** Created a symbolic link that stores the path "original_file.txt" as a reference. It's a separate file that acts as a shortcut.

---

### 4. Inode Numbers and File Listing

```bash
$ ls -lai
13428518 -rw-r--r-- 2 hit235 hit235   56 Jun 26 16:09 hard_link.txt
13428518 -rw-r--r-- 2 hit235 hit235   56 Jun 26 16:09 original_file.txt
13428519 lrwxrwxrwx 1 hit235 hit235   17 Jun 26 16:09 symbolic_link.txt -> original_file.txt
```
**Explanation:** The `-i` flag shows inode numbers. Hard link and original share inode 13428518 (link count = 2). The symbolic link has its own inode 13428519 and shows the target with `->`.

---

### 5. Metadata with stat

```bash
$ stat original_file.txt
  File: original_file.txt
  Size: 56    Blocks: 8    IO Block: 4096   regular file
  Device: 259,8   Inode: 13428518    Links: 2
  Access: (0644/-rw-r--r--)  Uid: (1000/hit235)  Gid: (1000/hit235)

$ stat hard_link.txt
  File: hard_link.txt
  Size: 56    Blocks: 8    IO Block: 4096   regular file
  Device: 259,8   Inode: 13428518    Links: 2
  Access: (0644/-rw-r--r--)  Uid: (1000/hit235)  Gid: (1000/hit235)

$ stat symbolic_link.txt
  File: symbolic_link.txt -> original_file.txt
  Size: 17    Blocks: 0    IO Block: 4096   symbolic link
  Device: 259,8   Inode: 13428519    Links: 1
  Access: (0777/lrwxrwxrwx)  Uid: (1000/hit235)  Gid: (1000/hit235)
```
**Explanation:** stat confirms that original and hard link have identical metadata (same inode, size, permissions). The symbolic link has a different inode, 17-byte size (length of path string), and 0 blocks (path stored inline in inode).

---

### 6. File Type Check

```bash
$ file original_file.txt hard_link.txt symbolic_link.txt
original_file.txt: ASCII text
hard_link.txt:     ASCII text
symbolic_link.txt: symbolic link to original_file.txt
```
**Explanation:** The `file` command correctly identifies both regular files and the symbolic link type.

---

### 7. Effect of Deleting the Original File

```bash
$ rm original_file.txt
```
**Explanation:** Deleted the original file to test link behavior.

```bash
$ cat hard_link.txt
This is the original file for link analysis experiment.
```
**Explanation:** ✅ Hard link STILL WORKS. The data persists because the inode's link count was decremented from 2 to 1, but the data blocks are not freed until the count reaches 0.

```bash
$ cat symbolic_link.txt
cat: symbolic_link.txt: No such file or directory
```
**Explanation:** ❌ Symbolic link is BROKEN. Since it stores the path to "original_file.txt" (which no longer exists), the kernel cannot resolve the target.

```bash
$ file hard_link.txt symbolic_link.txt
hard_link.txt:     ASCII text
symbolic_link.txt: broken symbolic link to original_file.txt
```
**Explanation:** Confirmed — the hard link remains valid ASCII text while the symbolic link is explicitly reported as "broken."

---

## Comparison: Hard Link vs Symbolic Link

| Property | Hard Link | Symbolic Link |
|----------|-----------|---------------|
| Inode | Same as original (13428518) | Different (13428519) |
| Link Count | Increments original's count | Has its own (always 1) |
| File Size | Same as original (56 bytes) | Size of path string (17 bytes) |
| After original deleted | ✅ Still works | ❌ Broken/dangling |
| Cross-filesystem | Not possible | Possible |
| Can link directories | Not allowed | Allowed |

---

## Summary of Findings

1. Hard links and the original file are indistinguishable — they share the same inode and data blocks.
2. Symbolic links are separate files that store a pathname string as a reference.
3. Deleting the original file preserves hard link data but breaks symbolic links.
4. Hard links cannot span filesystems; symbolic links can.
5. The `stat` and `file` commands are essential tools for investigating link behavior.

---

## Files in This Folder
- `Link_Analysis_Report.txt` – Detailed link analysis report
- `README.md` – This file (commands, outputs, and explanations)
- `hard_link.txt` – The surviving hard link
- `symbolic_link.txt` – The broken symbolic link (for demonstration)
