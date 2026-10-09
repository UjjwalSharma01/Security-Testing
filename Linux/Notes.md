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



## Locate — Finding Files

- `locate` → helps us find files **by name**
- Usage → `locate <filename>` → e.g. `locate test.txt`

> 💡 `locate` searches a pre-built database, so brand-new files may not show up until you refresh it → `sudo updatedb`


 to find the files contaning a particular pattern in name we can use `locate -all "ujjwal sharma"`
