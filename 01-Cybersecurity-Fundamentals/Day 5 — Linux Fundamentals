# 🐧 Day 5 — Linux Fundamentals

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 5  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 1. What Will You Learn Today?

Linux is one of the most important operating systems in cybersecurity.

Today, you will learn:

- What Linux is
- Why Linux is important in cybersecurity
- Linux filesystem structure
- Files and directories
- Absolute and relative paths
- Basic Linux commands
- Users and groups
- File permissions
- Processes and services
- Environment variables
- Basic system information
- How a security tester observes a Linux system

The goal is to become comfortable with the Linux command line.

---

## 2. What Is Linux?

Linux is an open-source operating system based on the Linux kernel.

It is widely used in:

- Servers
- Cloud infrastructure
- Networking devices
- Security tools
- Web servers
- Containers
- Virtual machines
- Embedded systems

You may already have interacted with Linux without realizing it.

For example, many websites and applications run on Linux-based servers.

### Linux vs Windows

| Feature | Linux | Windows |
|---|---|---|
| Source | Open source | Proprietary |
| Command line | Very powerful | PowerShell / CMD |
| File system | `/` | `C:\` |
| Common servers | Very common | Common |
| Security tools | Widely used | Widely used |

Both operating systems are important.

For cybersecurity, however, Linux command-line skills are particularly useful.

---

## 3. Why Is Linux Important in Cybersecurity?

Many cybersecurity tools are designed to work from the Linux command line.

Examples include:

- Nmap
- Wireshark
- Burp Suite
- Nikto
- Metasploit
- Various enumeration and analysis tools

Security-focused distributions such as Kali Linux provide many security tools in one environment.

However:

> Knowing Linux is more important than simply knowing Kali Linux.

A security tester should understand what the commands are doing instead of blindly copying commands from the internet.

---

## 4. Understanding the Linux File System

Unlike Windows, Linux does not normally use drive letters such as `C:` or `D:`.

Linux starts from a single root directory:

```text
/
```

This is called the **root of the filesystem**.

A simplified Linux filesystem looks like this:

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── opt
├── tmp
├── usr
└── var
```

Some important directories:

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | User home directories |
| `/etc` | System configuration files |
| `/tmp` | Temporary files |
| `/var` | Variable data such as logs |
| `/usr` | Applications and system utilities |
| `/opt` | Optional/additional software |
| `/dev` | Device files |
| `/boot` | Files required for booting |

You don't need to memorize everything today.

Focus on understanding what these directories are used for.

---

## 5. Files and Directories

A **file** stores information.

A **directory** is used to organize files and other directories.

For example:

```text
/home/student/
├── notes.txt
├── report.pdf
└── projects/
    └── security/
```

Here:

- `student` is a directory
- `notes.txt` is a file
- `report.pdf` is a file
- `projects` is a directory
- `security` is a directory

Linux treats filenames as case-sensitive.

For example:

```text
Report.txt
report.txt
REPORT.txt
```

These can be three different filenames.

---

## 6. Understanding Paths

A path tells the system where a file or directory is located.

There are two important types of paths.

### Absolute Path

An absolute path starts from `/`.

Example:

```text
/home/student/notes.txt
```

It describes the complete location.

### Relative Path

A relative path starts from your current location.

Example:

```text
notes.txt
```

If the file exists in your current directory, this refers to that file.

### Important Symbols

| Symbol | Meaning |
|---|---|
| `/` | Root directory |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Current user's home directory |

Example:

```text
/home/student/projects
```

From `/home/student/projects`:

```text
.
```

means:

```text
/home/student/projects
```

And:

```text
..
```

means:

```text
/home/student
```

---

## 7. Basic Navigation Commands

Let's learn the commands used to move around the filesystem.

### `pwd`

`pwd` means **Print Working Directory**.

It shows your current location.

```bash
pwd
```

Example output:

```text
/home/student
```

### `ls`

`ls` lists files and directories.

```bash
ls
```

You can also use:

```bash
ls -l
```

The `-l` option provides more detailed information.

### `cd`

`cd` means **Change Directory**.

Example:

```bash
cd /tmp
```

Go back to the previous directory:

```bash
cd ..
```

Go to your home directory:

```bash
cd ~
```

---

## 8. Creating and Managing Files

You can create files and directories directly from the terminal.

### Create a Directory

```bash
mkdir practice
```

### Move Into the Directory

```bash
cd practice
```

### Create an Empty File

```bash
touch notes.txt
```

### View File Contents

```bash
cat notes.txt
```

### Add Text to a File

```bash
echo "Cybersecurity Practice" > notes.txt
```

Then:

```bash
cat notes.txt
```

You should see:

```text
Cybersecurity Practice
```

### Remove a File

```bash
rm notes.txt
```

Be careful with `rm`.

Unlike moving a file to a recycle bin, removing files from the command line can permanently delete them.

---

## 9. Copying and Moving Files

Two important commands are:

- `cp` — copy
- `mv` — move or rename

### Copy a File

```bash
cp file1.txt file2.txt
```

This creates a copy of `file1.txt`.

### Move a File

```bash
mv file1.txt /tmp/
```

### Rename a File

```bash
mv oldname.txt newname.txt
```

Understanding these commands is useful when working with scripts, logs, configuration files, and security tools.

---

## 10. Users and Groups

Linux is a multi-user operating system.

Different users can have different permissions.

For example:

```text
root
student
www-data
```

A user can also belong to one or more groups.

Groups make it easier to manage permissions for multiple users.

You can check the current user with:

```bash
whoami
```

You can view user and group information with:

```bash
id
```

Example:

```text
uid=1000(student) gid=1000(student) groups=1000(student)
```

### Why Does This Matter in Security?

Permissions determine what a user can access or modify.

A security tester should always understand:

> Who am I?

> What can I access?

> What can I modify?

> What privileges do I have?

---

## 11. Understanding Root

In Linux, **root** is the superuser account.

Root has very high privileges on the system.

For example, root may be able to:

- Modify system configuration
- Install software
- Access protected files
- Manage users
- Start or stop services

You may see commands using:

```bash
sudo
```

`sudo` allows an authorized user to execute a command with elevated privileges.

Example:

```bash
sudo apt update
```

Do not use `sudo` blindly.

Before running a command with elevated privileges, understand what the command does.

---

## 12. Understanding Linux File Permissions

Linux uses permissions to control access to files and directories.

A typical permission output may look like:

```text
-rwxr-xr--
```

These permissions are divided into three groups:

```text
Owner     Group     Others
rwx       r-x       r--
```

The letters mean:

| Permission | Meaning |
|---|---|
| `r` | Read |
| `w` | Write |
| `x` | Execute |

### Example

```text
-rwxr-xr--
```

Means:

- Owner: read, write, execute
- Group: read, execute
- Others: read

You can view permissions using:

```bash
ls -l
```

Example:

```text
-rw-r--r--  student student  25 notes.txt
```

Understanding permissions is extremely important in Linux security.

---

## 13. Changing Permissions

The `chmod` command is used to modify permissions.

For example:

```bash
chmod +x script.sh
```

This adds execute permission to the file.

You may also see numeric permissions such as:

```bash
chmod 755 script.sh
```

The numbers represent permissions:

| Number | Permission |
|---|---|
| `4` | Read |
| `2` | Write |
| `1` | Execute |

Therefore:

```text
7 = 4 + 2 + 1 = rwx
5 = 4 + 1 = r-x
```

So:

```bash
chmod 755 script.sh
```

generally represents:

```text
Owner  → rwx
Group  → r-x
Others → r-x
```

Don't worry if numeric permissions look confusing at first.

You will become comfortable with them through practice.

---

## 14. Understanding Processes

A **process** is a running instance of a program.

For example, when you open a program, the operating system creates a process for it.

You can view running processes with:

```bash
ps
```

For a more detailed view:

```bash
ps aux
```

You can also use:

```bash
top
```

to observe running processes and system activity.

### Security Perspective

Processes can help a security tester understand:

- What programs are running
- Which users started them
- What resources they use
- What applications are active

A process running with high privileges deserves particular attention during authorized security testing.

---

## 15. Understanding Services and System Information

A **service** is a program that runs in the background and provides a specific function.

Examples:

- SSH
- Web server
- Database server
- DNS service

On many Linux systems using `systemd`, you can check service status with:

```bash
systemctl status ssh
```

The exact service name may vary between systems.

### Basic System Information

You can use:

```bash
uname -a
```

to display kernel and system information.

You can check the hostname with:

```bash
hostname
```

You can check the current user with:

```bash
whoami
```

These commands provide useful information about the system you are working on.

---

## 16. Think Like a Security Tester

Now let's combine what you learned.

Imagine you have been given permission to assess a Linux machine.

Before testing anything, you want to understand the system.

You might ask:

### Identity

```bash
whoami
id
```

Who am I?

What groups am I part of?

### Location

```bash
pwd
ls
```

Where am I?

What files are available?

### System

```bash
uname -a
hostname
```

What operating system and system information can I observe?

### Processes

```bash
ps aux
```

What is running?

### Services

```bash
systemctl --type=service
```

What services are running?

### Permissions

```bash
ls -l
```

Who owns these files?

Who can read, write, or execute them?

This is the beginning of **Linux enumeration**.

> Enumeration means systematically collecting information about a target system.

Only perform enumeration on systems you own or are explicitly authorized to test.

---

## 17. Day 5 Practical Exercise

For this exercise, use your own Linux machine or an authorized Linux lab.

### Step 1 — Identify Yourself

Run:

```bash
whoami
```

Then:

```bash
id
```

Record:

- Current username
- User ID
- Group ID
- Groups

### Step 2 — Explore Your Location

Run:

```bash
pwd
ls
```

Then:

```bash
cd /tmp
pwd
```

Return to your home directory:

```bash
cd ~
```

### Step 3 — Create a Practice Directory

Run:

```bash
mkdir cyber-practice
cd cyber-practice
```

Create a file:

```bash
touch notes.txt
```

Check it:

```bash
ls -l
```

### Step 4 — Write Something to the File

Run:

```bash
echo "Linux Security Practice" > notes.txt
```

Then:

```bash
cat notes.txt
```

### Step 5 — Check Permissions

Run:

```bash
ls -l notes.txt
```

Identify:

- Owner
- Group
- Read permission
- Write permission
- Execute permission

### Step 6 — Observe the System

Run:

```bash
uname -a
hostname
ps
```

Write down what you learned about your own system.

---

## 18. Day 5 Assignment

Complete the following tasks on your own Linux system or authorized lab.

### Task 1

Create this directory structure:

```text
cyber-lab/
├── notes/
├── tools/
└── reports/
```

### Task 2

Inside `notes`, create:

```text
linux.txt
```

Add the following text:

```text
I am learning Linux for cybersecurity.
```

### Task 3

Use Linux commands to find:

1. Your current username
2. Your current directory
3. Your hostname
4. Your Linux kernel information
5. Running processes

### Task 4

Run:

```bash
ls -l
```

and explain what the permission section means.

### Task 5

In your own words, explain:

> Why are Linux users, groups, and permissions important for cybersecurity?

---

## 19. Day 5 Quick Quiz

### Question 1

What command shows your current directory?

A. `ls`  
B. `pwd`  
C. `cd`  
D. `whoami`

### Question 2

What does `/` represent in Linux?

A. Home directory  
B. Current directory  
C. Root of the filesystem  
D. Temporary directory

### Question 3

Which command shows the current user?

A. `whoami`  
B. `hostname`  
C. `pwd`  
D. `id`

### Question 4

What does `r` represent in file permissions?

A. Run  
B. Read  
C. Remove  
D. Root

### Question 5

Which command is commonly used to view running processes?

A. `mkdir`  
B. `ps`  
C. `touch`  
D. `chmod`

### Question 6

What does `sudo` generally provide?

A. Internet access  
B. File compression  
C. Authorized elevated privileges  
D. Network scanning

### Question 7

What is a process?

A. A directory  
B. A running instance of a program  
C. A user account  
D. A network cable

---

## 20. Quiz Answers

1. **B — `pwd`**
2. **C — Root of the filesystem**
3. **A — `whoami`**
4. **B — Read**
5. **B — `ps`**
6. **C — Authorized elevated privileges**
7. **B — A running instance of a program**

---

## 21. Key Takeaways

Today you learned:

- What Linux is
- Why Linux is important in cybersecurity
- Linux filesystem structure
- Files and directories
- Absolute and relative paths
- Basic navigation commands
- Creating and managing files
- Users and groups
- Root and `sudo`
- File permissions
- Processes
- Services
- Basic system information
- The beginning of Linux enumeration

### Remember

> **Don't just memorize Linux commands. Understand what information each command gives you and why that information matters.**

---

## ✅ Day 5 Completion Checklist

- [ ] I understand what Linux is
- [ ] I can navigate the Linux filesystem
- [ ] I understand files and directories
- [ ] I can use `pwd`, `ls`, and `cd`
- [ ] I can create and manage files
- [ ] I understand Linux users and groups
- [ ] I understand basic file permissions
- [ ] I understand what root means
- [ ] I understand what a process is
- [ ] I can collect basic information from a Linux system
- [ ] I completed the practical exercise
- [ ] I completed the assignment
- [ ] I completed the quiz

---

**Learn responsibly. Test ethically.**
