# 🐧 Day 6 — Linux Command Line & Basic Enumeration

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Cybersecurity Fundamentals  
**Day:** 6  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 1. What Will You Learn Today?

In Day 5, you learned the basics of Linux.

Today, you will become more comfortable with the Linux command line and learn how a security tester can manually collect information from a Linux system.

You will learn:

- How to search for files
- How to search inside files
- How to inspect files
- How to view system information
- How to check network information
- How to identify running processes
- How to inspect users and groups
- How to understand environment variables
- How to combine Linux commands
- What enumeration means
- How to think like a security tester

The goal is not to memorize commands.

The goal is to understand:

> **What information can I collect from a system, and why is that information useful?**

---

## 2. What Is the Linux Command Line?

The **command line** is an interface where you interact with the operating system by typing commands.

Instead of clicking through graphical menus, you can tell Linux what you want to do using commands.

For example:

```bash
pwd
```

asks Linux:

> Where am I currently located?

And:

```bash
whoami
```

asks:

> Which user am I?

The command line is extremely useful in cybersecurity because many security tools and system administration tasks are performed through it.

---

## 3. Understanding Command Structure

A Linux command can contain:

- Command
- Options
- Arguments

For example:

```bash
ls -l /etc
```

Here:

```text
ls      → command
-l      → option
/etc    → argument
```

### Another Example

```bash
cat notes.txt
```

Here:

```text
cat        → command
notes.txt  → argument
```

Understanding this structure will help you learn new commands more easily.

---

## 4. Getting Help

You do not need to memorize every Linux command.

Linux provides built-in help.

### `man`

The `man` command displays a manual page.

Example:

```bash
man ls
```

You can also use:

```bash
man cat
```

### `--help`

Many commands also support:

```bash
ls --help
```

This gives you a quick overview of available options.

### Security Mindset

A good security tester does not depend on memorizing everything.

Instead:

> **Know what you are trying to accomplish and know how to find the correct information.**

---

## 5. Viewing Files

Linux provides several commands for viewing files.

### `cat`

Displays the contents of a file:

```bash
cat notes.txt
```

### `less`

Useful for reading larger files:

```bash
less /var/log/syslog
```

You can move through the content without loading everything into your terminal at once.

### `head`

Shows the beginning of a file:

```bash
head notes.txt
```

### `tail`

Shows the end of a file:

```bash
tail notes.txt
```

These commands are especially useful when working with logs and configuration files.

---

## 6. Searching for Files

Sometimes you know what you are looking for, but you don't know where the file is located.

The `find` command can help.

Example:

```bash
find . -name "notes.txt"
```

This searches for `notes.txt` starting from the current directory.

You can search for files with a particular extension:

```bash
find . -name "*.txt"
```

### Why Is This Useful?

During authorized security testing, you may need to identify:

- Configuration files
- Scripts
- Application files
- Log files
- Backup files

Understanding how to locate files is an important Linux skill.

---

## 7. Searching Inside Files

Finding a file is only half the job.

Sometimes you need to search for specific information inside a file.

The `grep` command is commonly used for this.

Example:

```bash
grep "password" notes.txt
```

This searches for the word:

```text
password
```

inside `notes.txt`.

You can also search recursively:

```bash
grep -R "admin" .
```

This searches files inside the current directory and its subdirectories.

### Important

Searching for a word does not mean you have found a vulnerability.

It simply helps you locate information that may require further investigation.

---

## 8. Understanding Pipes

One of the most powerful features of the Linux command line is the **pipe**.

The pipe symbol is:

```text
|
```

It sends the output of one command to another command.

Example:

```bash
ps aux | grep ssh
```

Here:

```text
ps aux
```

lists processes.

The pipe sends that output to:

```text
grep ssh
```

which searches for lines containing:

```text
ssh
```

Think of it like:

```text
Command 1 → Output → Command 2
```

This allows you to combine simple commands to perform more useful tasks.

---

## 9. Understanding Redirection

Linux also allows command output to be redirected.

For example:

```bash
whoami > user.txt
```

Instead of displaying the result on the screen, the output is written to:

```text
user.txt
```

You can then view it:

```bash
cat user.txt
```

### Append Output

Use:

```bash
>>
```

to append information instead of replacing the existing content.

Example:

```bash
hostname >> system.txt
```

This is useful when creating notes during an assessment.

---

## 10. Checking System Information

Before assessing a Linux system, it can be useful to understand the basic environment.

### Hostname

```bash
hostname
```

### Kernel Information

```bash
uname -a
```

### Current User

```bash
whoami
```

### User and Group Information

```bash
id
```

### Current Directory

```bash
pwd
```

These commands provide basic information about the system and your current context.

---

## 11. Understanding Environment Variables

Environment variables contain information used by the operating system and applications.

You can display them with:

```bash
env
```

You can also use:

```bash
printenv
```

One important variable is:

```text
PATH
```

You can view it with:

```bash
echo $PATH
```

The `PATH` variable tells the shell where it should look for executable programs.

Example:

```text
/usr/local/bin:/usr/bin:/bin
```

### Security Perspective

Environment variables can sometimes contain useful configuration information.

However:

> Do not assume that every environment variable contains sensitive information.

Always verify what the value actually represents.

---

## 12. Understanding Users and Groups

Linux systems can contain multiple users and groups.

You can view information about the current user:

```bash
id
```

You can view the contents of the local user database:

```bash
cat /etc/passwd
```

A typical entry may look like:

```text
student:x:1000:1000:Student:/home/student:/bin/bash
```

You don't need to memorize every field yet.

The important idea is that Linux stores account information in system files.

You can also view group information:

```bash
cat /etc/group
```

### Security Perspective

During an authorized assessment, understanding users and groups can help identify:

- Available accounts
- Group memberships
- Service accounts
- Permission relationships

---

## 13. Checking Network Information

Linux can also provide information about its network configuration.

Use:

```bash
ip addr
```

to view network interfaces and IP addresses.

Use:

```bash
ip route
```

to view routing information.

You may see something similar to:

```text
default via 192.168.1.1
```

This indicates the default route through the gateway.

### Remember

Network information is sensitive in some environments.

Only collect and share it when you are authorized to do so.

---

## 14. Checking Listening Services

A service may listen for network connections on a particular port.

You can inspect listening network sockets using:

```bash
ss -tuln
```

This can help you understand which ports are listening locally.

For example, you may see:

```text
Local Address:Port
0.0.0.0:22
0.0.0.0:80
```

This suggests that services may be listening on ports:

```text
22
80
```

### Important

A listening port does **not automatically mean there is a vulnerability**.

It simply indicates that something is listening.

You need additional investigation to determine:

- What service is running
- What version it uses
- How it is configured
- Whether it has security weaknesses

---

## 15. Understanding Processes

You learned about processes in Day 5.

Today, let's use that knowledge for basic enumeration.

Run:

```bash
ps aux
```

Look at:

- Process owner
- Process ID
- CPU usage
- Memory usage
- Command being executed

You can search for a specific process:

```bash
ps aux | grep ssh
```

You can also observe processes dynamically:

```bash
top
```

### Security Perspective

During an authorized assessment, processes may reveal:

- Running applications
- Background services
- Application paths
- Users running processes

Again, information gathering is not automatically exploitation.

---

## 16. Think Like a Security Tester

Imagine you have been given an authorized Linux machine to assess.

You should not immediately start running random commands.

Start by asking questions.

### Question 1 — Who am I?

```bash
whoami
id
```

### Question 2 — Where am I?

```bash
pwd
ls
```

### Question 3 — What system am I using?

```bash
uname -a
hostname
```

### Question 4 — What network configuration exists?

```bash
ip addr
ip route
```

### Question 5 — What services are listening?

```bash
ss -tuln
```

### Question 6 — What processes are running?

```bash
ps aux
```

### Question 7 — What users and groups exist?

```bash
cat /etc/passwd
cat /etc/group
```

### Question 8 — Can I locate specific files?

```bash
find . -name "*.conf"
```

This is the beginning of a structured enumeration process.

The important skill is not:

> "I know 100 Linux commands."

The important skill is:

> "I know what information I need and how to collect it."

---

## 17. Day 6 Practical Exercise

Perform this exercise only on your own Linux machine or an authorized lab.

### Step 1 — Identify Yourself

Run:

```bash
whoami
id
```

Record:

- Username
- UID
- GID
- Groups

### Step 2 — Collect System Information

Run:

```bash
hostname
uname -a
```

Record the information you observe.

### Step 3 — Check Network Information

Run:

```bash
ip addr
ip route
```

Identify:

- Network interface
- Local IP address
- Default gateway

Do not publish your real network information publicly.

### Step 4 — Check Listening Ports

Run:

```bash
ss -tuln
```

Record the listening ports.

Do not assume that an open/listening port is automatically vulnerable.

### Step 5 — Check Processes

Run:

```bash
ps aux
```

Then try:

```bash
ps aux | grep ssh
```

Observe the difference.

### Step 6 — Practice Searching

Create a test directory:

```bash
mkdir enumeration-practice
cd enumeration-practice
```

Create three files:

```bash
touch notes.txt
touch users.txt
touch report.txt
```

Now search for them:

```bash
find . -name "*.txt"
```

---

## 18. Day 6 Assignment

Complete the following tasks on your own Linux system or authorized lab.

### Task 1 — System Information

Find:

1. Current username
2. User ID
3. Hostname
4. Kernel information

### Task 2 — Network Information

Find:

1. Network interface
2. Local IP address
3. Default gateway

### Task 3 — Listening Services

Use:

```bash
ss -tuln
```

Record the listening ports.

For each port, think about:

- What service could be using it?
- Why might the service be running?
- What additional information would you need before considering it a security concern?

### Task 4 — Process Investigation

Run:

```bash
ps aux
```

Choose one process and identify:

- Process ID
- User running it
- Command being executed

### Task 5 — Search Practice

Create five `.txt` files and use:

```bash
find
```

to locate them.

Then use:

```bash
grep
```

to search for a specific word inside one of the files.

### Task 6 — Explain

In your own words, explain:

> What is enumeration, and why is it important during a security assessment?

---

## 19. Day 6 Quick Quiz

### Question 1

Which command displays the current directory?

A. `ls`  
B. `pwd`  
C. `cd`  
D. `find`

### Question 2

Which command is commonly used to search for files?

A. `find`  
B. `cat`  
C. `echo`  
D. `whoami`

### Question 3

Which command searches for text inside files?

A. `grep`  
B. `pwd`  
C. `mkdir`  
D. `hostname`

### Question 4

What does the pipe symbol `|` do?

A. Deletes output  
B. Sends output from one command to another  
C. Changes the current directory  
D. Starts a process

### Question 5

Which command can display network interface information?

A. `ip addr`  
B. `ps`  
C. `chmod`  
D. `touch`

### Question 6

Which command can show listening network sockets?

A. `ss -tuln`  
B. `ls -l`  
C. `cat /etc/passwd`  
D. `pwd`

### Question 7

What does enumeration mean in cybersecurity?

A. Exploiting a vulnerability immediately  
B. Collecting and organizing information about a target  
C. Installing an operating system  
D. Deleting system files

### Question 8

Does a listening port automatically mean the system is vulnerable?

A. Yes  
B. No

---

## 20. Quiz Answers

1. **B — `pwd`**
2. **A — `find`**
3. **A — `grep`**
4. **B — Sends output from one command to another**
5. **A — `ip addr`**
6. **A — `ss -tuln`**
7. **B — Collecting and organizing information about a target**
8. **B — No**

---

## 21. Key Takeaways

Today you learned:

- How to use the Linux command line more effectively
- How to get help using `man` and `--help`
- How to view files
- How to search for files using `find`
- How to search inside files using `grep`
- How to use pipes
- How to redirect command output
- How to collect system information
- How to understand environment variables
- How to inspect users and groups
- How to inspect network configuration
- How to identify listening ports
- How to inspect running processes
- What enumeration means
- How to approach a Linux system methodically

### Remember

> **Enumeration is about collecting information before making assumptions.**

A good security tester asks:

```text
What do I know?
What don't I know?
What information can I safely collect?
Why is that information important?
What should I investigate next?
```

---

## ✅ Day 6 Completion Checklist

- [ ] I understand the Linux command line
- [ ] I know how to use `man` and `--help`
- [ ] I can view files
- [ ] I can search for files using `find`
- [ ] I can search inside files using `grep`
- [ ] I understand pipes
- [ ] I understand output redirection
- [ ] I can collect basic system information
- [ ] I understand environment variables
- [ ] I can inspect users and groups
- [ ] I can check network information
- [ ] I can identify listening ports
- [ ] I can inspect running processes
- [ ] I understand basic enumeration
- [ ] I completed the practical exercise
- [ ] I completed the assignment
- [ ] I completed the quiz

---

**Learn responsibly. Test ethically.**
