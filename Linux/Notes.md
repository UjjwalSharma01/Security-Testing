# Linux Notes

> Personal revision notes — learning Linux as a starting point for ethical hacking.

---

## Terminal

### Font size

- Increase → `ctrl + shift + (+)`
- Decrease → `ctrl + -`

### Clearing the terminal

- Use the `clear` command
- Or press `ctrl + l`

### Ending or pausing a process

- End the currently running process → `ctrl + c`
- Pause a process or task → `ctrl + z`
  - Resume it with `fg` (foreground) or `bg` (background)

### Autocomplete

- Press `tab` to autocomplete
- Press `tab` twice (double tab) to see all the possible matches for what you've typed so far

### Closing the terminal

- Close the current tab → `ctrl + shift + w`
- Close the whole window → `ctrl + shift + q`
- Or type `exit` / press `ctrl + d`
- ⚠️ `ctrl + shift + t` **opens a new tab** — don't mix it up with closing

---

## File Management and Manipulation

### Current directory — `pwd`

- `pwd` → **p**rint **w**orking **d**irectory (I call it *present working directory*)
- Shows the directory you are currently in

### Listing — `ls`

- `ls` → lists the directories and files present in the current directory

#### Other variations of `ls`

- `ls -l` → long format; lists the contents as a list/table for easy readability
- `ls -a` → shows hidden files as well (`a` = all; hidden files start with `.`)
- `ls -al` → both combined (long format + hidden files)
- `ls -lh` → **h**uman readable; file sizes in KB/MB instead of bytes (unlike `ls -l`)
- `ls -lR Desktop/` → shows the subdirectories as well
  - `R` = **recursive**
  - mention the folder you want to go recursive in (here `Desktop/`)

### Changing directory — `cd`

- `cd ..` → go to the **parent** directory
- `cd <directory name>` → go to a specific directory
- `cd ~` → go back to the **home** directory
- Use `/` to specify a directory that is not directly available in the current directory

> ❓ **My question:** can we only move *down* the hierarchy, or anywhere in the file system? How does Linux process this?
>
> 💡 **Answer:** anywhere. Linux reads the path one folder at a time, and where it *starts* depends on the path:
>
> - **Absolute path** → starts with `/` (root), works from anywhere → `cd /var/log`
> - **Relative path** → no leading `/`, starts from the current directory → `cd Desktop/projects`
> - `.` = current directory, `..` = parent → `cd ../..` goes up two levels


### How `cd` resolves a path

- `cd` does **not** search the file system, it resolves the path one component at a time
  - Starts from `/` (absolute path) or the current directory (relative path)
  - e.g. `cd Desktop/projects/hacking` → 3 lookups only, whether the system has 100 directories or millions
- Speed depends on **how many components are in the path**, not on how many directories exist
- `cd hacking` fails if `hacking` isn't directly inside the current directory → `No such file or directory`
- To actually *search* the file system, use `find` or `locate`

**Why it's fast**

- A directory is a special file that maps names to inodes
- Modern filesystems (ext4, XFS) store entries in a hash/tree structure, so lookups are quick
- Recent lookups are kept in memory (**dentry cache**)

**Limits**

- Full path → max **4096** characters
- Single file/folder name → max **255** characters
- Symbolic links → stops after about **40** hops (`Too many levels of symbolic links`)
### Info about a command — `whatis`

- `whatis <command>` → quick one-line description of a command or tool
- e.g. `whatis ls`

---

## File Commands

1. **Create a file** → `touch`
   - Creates an empty file → `touch test.txt`

2. **`echo`** → prints some text in the command line
   - The data can also be redirected into a file

3. **Redirecting** the data to a particular file or command → used majorly with `echo`
   - `echo "Ujjwal Sharma" > test.txt`
   - `>` overwrites the file, `>>` appends to it

4. **Display** the content of a file → `cat`
   - Used to print and concatenate the contents of a file → `cat test.txt`

5. **Redirect** the contents of a file with `cat`
   - If the destination file doesn't exist, it will be created
   - Pass the path of the file (relative or absolute) → `cat /etc/passwd > pass.txt`

---

## File and Directory Permissions

### The basics

**2 ways to handle permissions in Linux**

1. Symbolic mode format
2. Octal (or binary) mode format

**3 types of permissions**

- `r` → read
- `w` → write
- `x` → execute

### Reading permissions in the terminal

Permissions are divided into blocks of 3, e.g. `-rw-r--r--` → read it as `-` `rw-` `r--` `r--`

```
-   rw-   r--   r--
|    |     |     |
|    |     |     +-- others  (all other users in the system)
|    |     +-------- group
|    +-------------- owner
+------------------- file type  (- = file, d = directory)
```

- A directory starts with `d` → e.g. `drwxr-xr-x`

### Changing permissions — `chmod`

- `chmod` → changes the file mode bits (the permissions) of a file

#### Symbolic way

- Format → `chmod <who><operator><permissions> <file>`

**Who**

- `u` → user (the current user)
- `g` → group
- `o` → others
- `a` → all

**Operator**

- `=` → set exactly these permissions
- `+` → add permissions
- `-` → remove permissions

**Examples**

- `chmod u-rwx file.txt` → remove all permissions from the user
- `chmod go=rwx test.sh` → set `rwx` for group and others
- `chmod go-wx test.sh` → remove write and execute from group and others
- `chmod +x test.sh` → make a file executable

> 💡 Don't want to write all the permissions with `=` again and again? Use `+` and `-` to add or remove only what you need.

#### Octal way

- Permissions are denoted by numbers:
  - `r` = **4**
  - `w` = **2**
  - `x` = **1**
- For extra permissions, **add the values together** → `rw-` = 4+2 = **6**, `rwx` = 4+2+1 = **7**
- Give one number for each entity → **owner | group | others**

**Examples**

- `chmod 444 file.txt` → owner, group and others get **only read**
- `chmod 644 file.txt` → `rw-r--r--`
- `chmod 755 file.sh` → `rwxr-xr-x`

> 💡 To do anything recursively, just use the `-R` flag → e.g. `chmod -R 755 folder/`

---

## File and Directory Ownership

- `chown` → change the **owner** of a file
  - `chown root test.sh`
- `chgrp` → change the **group** of a file
  - `chgrp root test.sh`

> 💡 These usually need `sudo`. To change owner and group together → `chown user:group file`

---

## Grep and Piping

### `grep`
It is used for both the verbatim first is to Find the patterns like how really you will find the patterns is if you will give it a file It will cheque the contents of the file and print the line that you are looking for If you give it a forwarder it will cheque for the file name containing the content that you give it
- Prints the lines matching a particular pattern
- Also helps you find strings and patterns in a file
- Usage → `grep -r "dynamic" /etc/`
  - the location is specified at the end
  - `-r` → recursive; needed when the location is a directory
- Case-insensitive search → use the `-i` flag
  - `grep -ri "dynamic" /etc/`

> 💡 Unsure about a tool? Use `man <command>` to check how to use it (full manual — `whatis` is just a one-liner). Press `q` to exit.

### Piping `|`

- We can also pass some data to a command and then perform operations on it
- Example → `cat /etc/passwd | grep "Ujjwal"`
  - `cat` prints the file → the output is piped into `grep` → only the matching lines are shown



A pipe takes the output of the left command and feeds it as input to the right command. The flow is `command → command`.

- `ls -l | grep "txt"` shows only the `.txt` entries of a listing.
- `ps aux | grep ssh` checks whether an `ssh` process is running.
- `history | grep "nmap"` finds commands you ran earlier.
- `ip a | grep "inet"` shows only your IP lines.
- `cat /etc/passwd | grep "bash"` does the same as `grep "bash" /etc/passwd`, which is shorter and preferred when it's a single file.


**Symbols to remember**

- `|` → command → command
- `>` → command → file
- `>>` → command → file (append)



# `locate`

`locate` finds files **by name**, very fast. It doesn't scan the disk when you run it. It searches a **pre-built database** of file paths.

## 1. How it works

- A database of every file path on the system is built by `updatedb` (usually once a day, automatically).
- When you run `locate`, it searches that database, which is why it takes milliseconds.
- The catch is that the database can be **stale**. A file you created 5 minutes ago won't show up until the database is refreshed.

## 2. Basic usage

**Syntax:** `locate [options] pattern`

- `locate test.txt` lists every path containing `test.txt`
- `locate passwd` also matches `/etc/passwd`, `/usr/share/doc/passwd`, and so on

By default the pattern matches **anywhere in the full path**, not just the file name. So `locate nmap` also returns files inside folders named `nmap`.

## 3. Flags you'll use

| Flag | Meaning | Example |
|---|---|---|
| `-i` | ignore case | `locate -i readme` |
| `-c` | count matches instead of printing them | `locate -c ".conf"` |
| `-l N` | limit output to N results | `locate -l 5 passwd` |
| `-b` | match the **file name only**, not the folder path | `locate -b "nmap"` |
| `-e` | show only files that **still exist** (filters out deleted ones) | `locate -e test.txt` |
| `-r` | use a regex pattern | `locate -r "\.conf$"` |
| `-S` | show database statistics | `locate -S` |

For an **exact** file name match, use `-b` with a backslash: `locate -b "\passwd"`. This returns files named exactly `passwd` and ignores files like `passwd.bak`.

## 4. Wildcards

If the pattern contains wildcards (`*`, `?`, `[ ]`), quote it so the shell doesn't expand it first.

- `locate "*.conf"` finds every `.conf` file
- `locate "*.txt"` finds every `.txt` file
- `locate "/etc/*.conf"` finds `.conf` files under `/etc`

## 5. Updating the database

- `sudo updatedb` refreshes the database manually.
- Run it right after creating a file you want to find, or on a fresh install.
- If the command isn't found, install it with `sudo apt install plocate`. Older systems use `mlocate`.

Example:
```
touch newfile.txt
locate newfile.txt      # nothing found, database is stale
sudo updatedb
locate newfile.txt      # now it shows up
```

## 6. Combining with pipes and grep

This ties back to your grep and piping notes:

- `locate "*.conf" | grep "ssh"` narrows the results
- `locate nmap | grep "scripts"` finds files in the scripts folder
- `locate "*.txt" | wc -l` counts the results
- `locate "*.log" > logs.txt` saves the results to a file

## 7. `locate` vs `find`

| | `locate` | `find` |
|---|---|---|
| Speed | very fast | slower, scans the disk live |
| Freshness | can be stale | always current |
| Search by | name only | name, size, time, permissions, owner |
| Example | `locate test.txt` | `find / -name test.txt` |

Use `locate` for a quick lookup by name, and `find` when you need current results or advanced conditions.

## 8. Limitations

- It can't find files created after the last `updatedb`.
- It only shows files **you have permission to see**. Some paths may be hidden from a normal user.
- It can still list files that were **deleted** since the last update. Use `-e` to filter those out.
- Searching by name only means it can't search by size, date or permissions.

## 9. Where it helps in ethical hacking

- Find wordlists: `locate rockyou.txt`
- Find Nmap scripts: `locate "*.nse"`
- Find config files: `locate "*.conf" | grep "apache"`
- Find tools or payloads you've installed: `locate -i shell`
- Hunt for interesting files: `locate -i password`

## 10. Cheat summary

- `locate name` does a fast database search by name.
- `-i` (ignore case), `-b` (name only), `-e` (exists), `-c` (count) and `-l` (limit) are the flags to know.
- Quote your wildcards, like `locate "*.txt"`.
- `sudo updatedb` refreshes the database, and a stale database is the number one reason a file isn't found.
- Use `find` when you need live results or filters.

If you'd like, I can turn this into a short section for your `linux-notes.md`, shortened to match the style of your other notes.



## Locate — Finding Files

- `locate` → helps us find files **by name**, very fast
- It does **not** scan the disk when you run it → it searches a **pre-built database** of file paths
- Syntax → `locate [options] pattern`
- Example → `locate test.txt`

> 💡 The database can be **stale** — a file created 5 minutes ago won't show up until it is refreshed (see *Updating the database* below)

---

### How it works

- The database of all file paths is built by `updatedb` (usually once a day, automatically)
- `locate` only searches that database → that's why it takes milliseconds
- By default the pattern matches **anywhere in the full path**, not just the file name
  - so `locate nmap` also returns files that merely sit inside a folder called `nmap`

---

### Pretend database (used for all examples below)

```
/etc/passwd
/etc/passwd.bak
/home/kali/Desktop/nmap.txt
/home/kali/Documents/Report.txt
/home/kali/Documents/report.txt
/home/kali/nmap
/home/kali/nmap/notes.txt
/home/kali/nmap/scan.txt
/home/kali/oldfile.txt
/usr/share/nmap
/usr/share/nmap/scripts
/usr/share/nmap/scripts/http-title.nse
/var/log/auth.log
```

- Folders are entries too (`/home/kali/nmap` is a folder)

---

### Default search → whole path

```
$ locate nmap
/home/kali/Desktop/nmap.txt
/home/kali/nmap
/home/kali/nmap/notes.txt
/home/kali/nmap/scan.txt
/usr/share/nmap
/usr/share/nmap/scripts
/usr/share/nmap/scripts/http-title.nse
```

- `notes.txt` and `scan.txt` have nothing to do with nmap by name, but matched because their **folder** is called `nmap`

---

### Flags

#### `-b` → match the file name only

How a path splits:

```
/home/kali/nmap/notes.txt
└── folder part ─┘└ name ┘

default locate → searches the WHOLE string
locate -b      → searches only "notes.txt"
```

```
$ locate -b nmap
/home/kali/Desktop/nmap.txt
/home/kali/nmap
/usr/share/nmap
```

- Files *inside* the nmap folders are gone → only the last part of each path is checked
- Results dropped from 7 to 3

#### `-b` with `\` → exact name match

```
$ locate passwd
/etc/passwd
/etc/passwd.bak

$ locate -b '\passwd'
/etc/passwd
```

- Plain `locate` matches anything **containing** the word
- `-b '\passwd'` matches the name **exactly** → `passwd.bak` is gone
- Use **single quotes** so the shell doesn't swallow the backslash

#### `-i` → ignore case

```
$ locate report
/home/kali/Documents/report.txt

$ locate -i report
/home/kali/Documents/Report.txt
/home/kali/Documents/report.txt
```

- Without `-i`, the capital-R file stays invisible

#### `-c` → count instead of print

```
$ locate -c nmap
7

$ locate -c -b nmap
3
```

- Flags can be combined (second example)

#### `-l N` → limit the output

```
$ locate -l 3 nmap
/home/kali/Desktop/nmap.txt
/home/kali/nmap
/home/kali/nmap/notes.txt
```

- Stops after N results → handy when a search would print thousands of lines

#### `-e` → only files that still exist

```
$ rm /home/kali/oldfile.txt

$ locate oldfile
/home/kali/oldfile.txt          <- ghost entry, file is already gone

$ locate -e oldfile
                                <- nothing, -e checks the file really exists
```

- The database may still list files **deleted** after the last update → `-e` filters them out

#### `-r` → regex

```
$ locate -r "passwd$"
/etc/passwd

$ locate -r "\.txt$"
/home/kali/Desktop/nmap.txt
/home/kali/Documents/Report.txt
/home/kali/Documents/report.txt
/home/kali/nmap/notes.txt
/home/kali/nmap/scan.txt
/home/kali/oldfile.txt
```

- `$` → end of the path (same as in grep)
- `\.` → a literal dot, so only names ending in `.txt` match

#### `-S` → statistics

- Prints info about the database (number of files, size)

---

### Wildcards

- If the pattern has wildcards (`*`, `?`, `[ ]`) → **quote it** so the shell doesn't expand it first

```
$ locate "*.nse"
/usr/share/nmap/scripts/http-title.nse
```

- Other examples → `locate "*.conf"`, `locate "*.txt"`, `locate "/etc/*.conf"`

> ⚠️ **Common trap:** with wildcards, `locate` matches against the **entire path**, and every path starts with `/`

```
$ locate "report*"
                                <- nothing! the path starts with "/home/...", not "report"

$ locate -b "report*"
/home/kali/Documents/report.txt
```

- Fix → add `-b` so only the file name is checked

---

### Updating the database

- `sudo updatedb` → refreshes the database manually
- Run it right after creating a file you want to find, or on a fresh install
- If `locate` isn't found → `sudo apt install plocate` (older systems use `mlocate`)

```
$ touch newfile.txt
$ locate newfile.txt          <- nothing found, database is stale
$ sudo updatedb
$ locate newfile.txt          <- now it shows up
```

---

### Combining with pipes and grep

- `locate "*.conf" | grep "ssh"` → narrow the results
- `locate nmap | grep "scripts"` → only paths containing "scripts"
- `locate "*.txt" | wc -l` → count the results
- `locate "*.log" > logs.txt` → save the results to a file

---

### `locate` vs `find`

- `locate`
  - very fast (database lookup)
  - can be stale
  - searches by **name only**
  - e.g. `locate test.txt`
- `find`
  - slower (scans the disk live)
  - always current
  - can search by name, size, time, permissions, owner
  - e.g. `find / -name test.txt`

> 💡 Use `locate` for a quick lookup by name, `find` when you need live results or advanced conditions

---

### Limitations

- Can't find files created after the last `updatedb`
- Only shows files **you have permission to see**
- May list files **deleted** since the last update → use `-e`
- Searches by name only → no size, date or permission filters

---

### Where it helps in ethical hacking

- Find wordlists → `locate rockyou.txt`
- Find Nmap scripts → `locate "*.nse"`
- Find config files → `locate "*.conf" | grep "apache"`
- Find installed tools or payloads → `locate -i shell`
- Hunt for interesting files → `locate -i password`

---

### Quick summary

| You want | Use |
|---|---|
| Search by name, ignoring folder names | `-b` |
| Exact name only | `-b '\name'` |
| Ignore capital letters | `-i` |
| Just how many matches | `-c` |
| Only the first few | `-l 5` |
| Skip deleted/ghost files | `-e` |
| Pattern like "ends with" | `-r` |
| Refresh the database | `sudo updatedb` |

- Quote your wildcards → `locate "*.txt"`
- A stale database is the number one reason a file isn't found


## Enumerating Distribution & Kernel Information

- Usually one of the **first steps** once you have a shell on a machine
- Goal → find the exact OS + kernel version, to look for matching exploits
- The **kernel version** is the key detail for privilege escalation (old kernels → known CVEs)

---

### Kernel info — `uname`

- `uname -a` → **all** info at once (the go-to command)

```
$ uname -a
Linux kali 6.1.0-kali7-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.0-13 (2023-07-12) x86_64 GNU/Linux
```

| Flag | Meaning |
|---|---|
| `-a` | all information |
| `-r` | kernel release version (the important one) |
| `-m` | architecture (e.g. `x86_64`) |
| `-n` | hostname |
| `-v` | kernel build date/version |
| `-s` | kernel name |

> 💡 `uname -r` alone is usually enough to start exploit-hunting

---

### Distro info — the kernel version alone doesn't tell you this

- `cat /etc/os-release` → the **modern standard**, works on almost every distro
  - `PRETTY_NAME` line is the quickest to read
- `lsb_release -a` → another common way, but not installed everywhere
- `cat /etc/issue` → the pre-login banner, less reliable (can be outdated/customized)
- Older/specific files:
  - `/etc/debian_version` → Debian/Ubuntu
  - `/etc/redhat-release` → RedHat/CentOS/Fedora
  - `cat /etc/*-release` → wildcard, catches whichever exists

---

### `/proc/version` — live kernel info

```
$ cat /proc/version
Linux version 6.1.0-kali7-amd64 (devel@kali.org) (gcc-12) #1 SMP PREEMPT_DYNAMIC ...
```

- `/proc` is a **virtual** filesystem, generated live by the kernel (not real files on disk)
- Similar to `uname -a`, but also shows the **compiler** used to build the kernel

---

### `hostnamectl` — one-shot summary

```
$ hostnamectl
  Operating System: Kali GNU/Linux Rolling
            Kernel: Linux 6.1.0-kali7-amd64
      Architecture: x86-64
    Virtualization: kvm
```

- Gives OS + kernel + architecture + whether you're in a **VM**, all at once

---

### Piping it together (ties back to grep notes)

- `cat /etc/os-release | grep "PRETTY_NAME"` → just the readable name
- `uname -a | grep -o "x86_64\|i686"` → quick architecture check

---

### Why it matters for privilege escalation

- Once you know the exact kernel version → search `searchsploit <version>` or check Exploit-DB
- e.g. an old Ubuntu `4.4.0` kernel has known privesc exploits, a patched `6.x` kernel usually doesn't

> ⚠️ Always confirm the **exact** version — exploits are version-specific, wrong one can crash the service

---

### Cheat summary

| Goal | Command |
|---|---|
| Kernel version (quick) | `uname -r` |
| Kernel + arch + hostname | `uname -a` |
| Distro name (modern) | `cat /etc/os-release` |
| Distro name (classic) | `lsb_release -a` |
| Pre-login banner | `cat /etc/issue` |
| Live kernel + compiler info | `cat /proc/version` |
| Full system summary | `hostnamectl` |



## `find` — and OverTheWire Bandit

- `find` searches the file system **live**, right now (unlike `locate`, which searches a database)
- Slower, but always accurate — can search by **size, permission, time, owner, type**, and more
- This is exactly why OverTheWire's Bandit forces you to learn it

---

### Basic syntax

```
find <where to look> <what to look for> <what to do with it>
```

- `find /home -name "test.txt"` → search starting at `/home`
- `find .` → search starting at the current directory

---

### Searching by name

| Flag | Meaning |
|---|---|
| `-name` | exact match, case-sensitive |
| `-iname` | same, case-**insensitive** |

```
find / -iname "*.txt"
```

> 💡 Quote wildcards, same rule as `locate`

---

### Searching by type

- `-type f` → regular files only
- `-type d` → directories only
- `-type l` → symbolic links only

```
find /home -type d
```

---

### Searching by size

The **most-used flag** in Bandit's early levels — many tasks ask you to find "the only file of this size."

```
find / -size 1033c
```

| Suffix | Unit |
|---|---|
| `c` | bytes |
| `k` | KB |
| `M` | MB |
| `G` | GB |

Ranges:
- `+1033c` → greater than 1033 bytes
- `-1033c` → less than 1033 bytes
- `1033c` → exactly 1033 bytes

---

### Searching by permissions

```
find / -perm 644
```

- `-perm 644` → matches **exactly** that permission set
- `-perm -644` → matches files with **at least** those permissions

> 💡 Bandit often says *"find the file readable by you but not executable"* → this is the flag for that

---

### Searching by ownership

```
find / -user root
find / -group root
```

---

### Searching by modification time

- `-mtime -1` → modified in the last 1 day
- `-mtime +7` → modified more than 7 days ago
- `-mtime 5` → modified **exactly** 5 days ago

```bash
# Find files modified in the last 2 days
find . -mtime -2

# Find files older than 30 days (good for cleanup)
find . -mtime +30

# Find files modified exactly 5 days ago
find . -mtime 5
```

> 💡 Same `+` / `-` / exact logic as `-size` → `+` is "more than", `-` is "less than", plain number is "exactly"

---

### Combining conditions (AND by default)

```
find / -type f -size 1033c 2>/dev/null
```

Reads as: *"regular file, exactly 1033 bytes, hide permission errors."*

> ⚠️ `2>/dev/null` is essential on real systems and in Bandit — without it, your terminal fills with `Permission denied` noise

---

### Running a command on results — `-exec`

```
find / -name "*.txt" -exec cat {} \;
```

- `{}` → placeholder for each file found
- `\;` → ends the command (backslash escapes the semicolon from the shell)
- Runs `cat` on **every** matching file, one by one

---

### How this maps to Bandit

Bandit = beginner wargame, SSH access + one task per level, usually forcing exactly one new command. `find` shows up heavily around **Level 4–7**.

**Find the only human-readable file among decoys:**
```
find . -type f -exec file {} \; | grep "ASCII text"
```
*(`file` shows type of each file → piped into `grep` to keep only text files)*

**Find a file by exact size + owner + group:**
```
find / -size 1033c -user bandit7 -group bandit6 2>/dev/null
```
*(all 3 conditions AND together → narrows millions of files to one)*

**Find recently modified files (cron-job levels):**
```
find / -mmin -5 2>/dev/null
```

> 💡 **General Bandit pattern:** `find / <conditions> 2>/dev/null` — always hide errors, since you don't have root

---

### `find` vs `locate` (recap)

| | `locate` | `find` |
|---|---|---|
| Speed | fast (database) | slower (live scan) |
| Freshness | can be stale | always accurate |
| Searches by | name only | name, size, time, permission, owner, type |

Bandit specifically wants `find` — its challenges test conditions `locate` simply can't check.

---

### Cheat summary

| Goal | Command |
|---|---|
| By name | `find / -iname "file.txt"` |
| By type | `find / -type f` |
| By size | `find / -size 50c` |
| By owner | `find / -user bandit7` |
| By permission | `find / -perm 644` |
| By time | `find / -mtime -1` |
| Run a command on results | `find / -name "*.txt" -exec cat {} \;` |
| Hide errors (always do this) | add `2>/dev/null` |


## Output Redirection — `2>/dev/null`

> ⭐ **Why this matters:** almost every real-world or Bandit-style `find` / `grep` command on a shared system throws `Permission denied` errors. Without this, your terminal floods and hides the actual result you were looking for. This is one of the most-used pieces of syntax in enumeration.

---

### 1. Every command has 2 separate output channels

A command doesn't produce one stream of output — it produces **two**, kept completely separate by Linux (even though your terminal displays both mixed together):

| Channel | Name | Number | Carries |
|---|---|---|---|
| stdout | standard output | `1` | normal, successful output |
| stderr | standard error | `2` | error messages |

> 💡 They *look* like one stream on screen, but to Linux they are two different pipes — that's exactly why you can throw away errors without touching the real results.

---

### 2. Breaking down `2>/dev/null`

| Part | Meaning |
|---|---|
| `2` | select channel **2** → stderr (errors) |
| `>` | redirect — send this output elsewhere instead of printing it |
| `/dev/null` | a special "trash can" file that discards anything sent to it |

- Plain `>` with no number defaults to channel `1` (stdout) → so `command > file.txt` still shows errors on screen, since it never touched channel 2
- `/dev/null` isn't a real file with disk space — it's a device file that discards data instantly, nothing is stored

**Put together:** *"Take the errors (channel 2), and throw them into the void — show me only the real results."*

---

### 3. Example

```bash
find / -iname "*.conf" 2>/dev/null
```

Shows only the matching files, no `Permission denied` noise.

---

### 4. See the two channels separately (optional, to prove it to yourself)

```bash
find / -iname "*.conf" > results.txt 2>errors.txt
```

- `results.txt` → real matches (stdout)
- `errors.txt` → permission errors (stderr)

---

### 5. Bonus — merging both streams

```bash
find / -iname "*.conf" 2>&1
```

- `2>&1` → send channel 2 to wherever channel 1 is currently going
- Useful when you want errors **and** output captured together:

```bash
find / -iname "*.conf" > all_output.txt 2>&1
```

---

### Cheat summary

| Syntax | Meaning |
|---|---|
| `1` | stdout channel (normal output) |
| `2` | stderr channel (errors) |
| `>` | redirect to a file instead of screen |
| `2>/dev/null` | discard error messages only |
| `2>&1` | merge errors into the same place as normal output |

> ⭐ **Rule of thumb:** append `2>/dev/null` to any `find`, or similar recursive search run without root — it's standard practice, not optional.
