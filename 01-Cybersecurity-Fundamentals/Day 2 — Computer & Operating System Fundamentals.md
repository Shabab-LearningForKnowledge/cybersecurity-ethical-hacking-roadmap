# 💻 Day 2 — Computer & Operating System Fundamentals

**Learning Path:** Cybersecurity & Ethical Hacking  
**Phase:** 1 — Fundamentals  
**Day:** 2  
**Level:** Beginner  
**Estimated Time:** 60–90 minutes

---

## 🎯 1. What Will You Learn Today?

Before learning how to test a system, you need to understand what a system actually is.

Today we will learn:

- What a computer system is
- CPU, RAM and Storage
- What an Operating System does
- Users and accounts
- Files and directories
- Processes
- Services
- Permissions
- Windows and Linux basics
- How to inspect a system
- Why these concepts matter in cybersecurity

> 💡 The goal of today's lesson is not to become a system administrator. The goal is to understand the system well enough to recognize how security weaknesses can occur.

---

## 🖥️ 2. What Is a Computer System?

A computer system is a combination of hardware and software that works together to perform tasks.

A simple way to understand it is:

**Hardware + Operating System + Applications + Data = Computer System**

### Hardware

Hardware is the physical part of a computer.

Examples:

- CPU
- RAM
- Hard Disk
- SSD
- Motherboard
- Keyboard
- Mouse
- Network Interface

### Software

Software is the set of programs that tell the hardware what to do.

Examples:

- Windows
- Linux
- macOS
- Web browsers
- Microsoft Office
- Mobile applications
- Security tools

---

## ⚙️ 3. Understanding the CPU

CPU stands for **Central Processing Unit**.

In simple words:

> **The CPU is responsible for executing instructions and performing calculations.**

For example, when you open an application, the CPU processes the instructions required to run it.

### Why Should a Cybersecurity Learner Care?

Applications, security tools, operating systems and other programs all need CPU resources to execute.

Understanding CPU usage can help you recognize:

- Resource-heavy processes
- Unusual processes
- Applications consuming excessive resources
- Possible system performance problems

You don't need to understand CPU architecture deeply yet.

For now, remember:

> **CPU = Executes instructions**

---

## 🧠 4. Understanding RAM

RAM stands for **Random Access Memory**.

In simple words:

> **RAM is temporary working memory used by the computer while programs are running.**

For example, when you open:

- A browser
- A text editor
- A virtual machine
- A security tool

they require RAM to operate.

### Important Point

RAM is different from permanent storage.

When the computer is turned off, the information stored in RAM is normally lost.

### Why Does RAM Matter in Cybersecurity?

Security tools, virtual machines and applications can consume significant amounts of memory.

Understanding RAM helps you understand:

- Application performance
- Running processes
- Virtual machines
- Resource consumption

Remember:

> **RAM = Temporary working memory**

---

## 💾 5. Understanding Storage

Storage is where information is stored for longer periods.

Examples:

- HDD
- SSD
- USB drive

Storage can contain:

- Operating system files
- Applications
- Documents
- Databases
- Logs
- Configuration files
- Credentials
- Other sensitive information

### Why Does Storage Matter in Cybersecurity?

Sensitive information stored on a system can become a security target.

For example:

**Application → Configuration File → Database Credentials**

If that configuration file is improperly protected, sensitive information could potentially be exposed.

This is why understanding files and permissions is important.

Remember:

> **Storage = Long-term data storage**

---

## 🧩 6. What Is an Operating System?

An Operating System, commonly called an **OS**, is software that manages the computer's hardware and provides an environment for applications to run.

Examples include:

- Windows
- Linux
- macOS
- Android
- iOS

### In Simple Words

> **The Operating System acts as a bridge between applications, users and computer hardware.**

For example, you open a browser.

The browser needs:

- CPU
- RAM
- Storage
- Network access
- Operating system services

The operating system helps manage these resources.

---

## 🔐 7. Why Is the Operating System Important in Cybersecurity?

A large part of cybersecurity involves understanding the operating system.

Security testers may need to understand:

- Users
- Processes
- Services
- Files
- Permissions
- Network configuration
- Installed software
- System configuration

If you don't understand the operating system, it becomes difficult to understand how a vulnerability actually affects the system.

> **You cannot effectively test a system that you don't understand.**

---

## 👤 8. Users and Accounts

Operating systems allow multiple users or accounts to interact with a system.

For example:

- Administrator
- Standard user
- Service account
- Guest account

Different users may have different permissions.

### Example

Imagine a computer with two accounts.

**Administrator**

Can install software and change important system settings.

**Standard User**

Has more limited permissions.

This separation is an important security concept.

### Why?

If every user had complete control over the system, compromising a low-privileged account could immediately provide much greater access.

Therefore:

> **Users should receive only the permissions they actually need.**

This concept is known as the **Principle of Least Privilege**.

---

## 📁 9. Files and Directories

A computer organizes information using files and directories.

Windows commonly uses paths such as:

**C:\Users\Student\Documents**

Linux commonly uses paths such as:

**/home/student/documents**

A file may contain:

- Text
- Configuration
- Application data
- Credentials
- Logs
- Source code

### Why Does This Matter?

During security testing, you may encounter files that contain sensitive information.

For example:

**config.txt**

could potentially contain application configuration.

Or:

**database.conf**

could potentially contain database connection information.

The important lesson is:

> **A file is not automatically sensitive or vulnerable. You must understand its contents, permissions and intended purpose.**

---

## 🔄 10. What Is a Process?

A **process** is a program that is currently running.

For example, when you open a browser, the operating system creates one or more processes to run it.

Other examples include:

- Web server process
- Database process
- System process
- Security software process
- User application process

### Why Are Processes Important?

During system analysis, you may want to understand:

- Which processes are running?
- Which user started them?
- What resources are they using?
- What software created them?
- Are any processes unexpected?

Understanding processes becomes very important when analyzing a potentially vulnerable or compromised system.

Remember:

> **Process = A running program**

---

## ⚙️ 11. What Is a Service?

A service is a program or system component designed to perform a function, often in the background.

Examples can include:

- Web services
- Database services
- Remote access services
- Update services
- Logging services

Some services start automatically when the operating system starts.

### Why Are Services Important in Security?

Every unnecessary or incorrectly configured service can potentially increase the system's attack surface.

For example:

**System → Running Service → Open Functionality → Potential Attack Surface**

This does not mean every service is vulnerable.

It means a security tester should understand:

> **What is running, why is it running, and is it required?**

---

## 🔑 12. Understanding Permissions

Permissions determine what a user or process is allowed to do.

Common permissions include:

- Read
- Write
- Execute

### Example

Suppose a user can:

**Read → Yes**

**Write → No**

**Execute → No**

That user may be able to view the file but not modify or execute it.

Another user may have greater privileges.

### Why Are Permissions Important?

Incorrect permissions can sometimes lead to security issues.

For example:

A sensitive configuration file should not normally be writable by every user on the system.

This is why permissions are an important part of security testing.

---

## 🪟 13. Windows vs Linux

Both Windows and Linux are operating systems, but they have different architectures, commands, tools and administration methods.

### Windows

Commonly used in:

- Enterprise environments
- Corporate desktops
- Servers
- Active Directory environments

### Linux

Commonly used in:

- Servers
- Cloud environments
- Web infrastructure
- Security testing environments
- Development environments

### Important Point

Do not think:

**Windows = Insecure**

or:

**Linux = Secure**

Security depends on many factors including:

- Configuration
- Software
- Permissions
- Updates
- Authentication
- Architecture
- Security controls
- User behavior

> **No operating system is automatically secure simply because of its name.**

---

## 🐧 14. Basic Linux Observation

If you have access to a Linux system, try the following commands.

### Identify the current user

whoami

This shows the user account currently being used.

### Identify the Operating System

uname -a

This displays information about the Linux system and kernel.

### List Files

ls

This displays files and directories in the current location.

### List Files With Additional Information

ls -la

This provides additional information, including permissions and hidden files.

### View Running Processes

ps

This displays processes associated with the current session.

These commands are not hacking commands.

They are basic system observation commands.

> **A security professional should be comfortable observing the environment before attempting to test it.**

## 🔍 15. Security Thinking Exercise

Imagine you have been authorized to assess a computer.

You discover:

- 12 user accounts
- 45 running processes
- 20 background services
- Several configuration files
- Multiple installed applications

Before testing anything, what would you want to understand?

Think about:

- Which users exist?
- Which user am I currently using?
- Which processes are running?
- Which services are running?
- Which applications are installed?
- Who has access to sensitive files?
- Which accounts have administrative privileges?
- Are unnecessary services enabled?

This is the beginning of system enumeration.

You will encounter this concept repeatedly in cybersecurity.

## 🧠 16. Think Like a Security Tester

A beginner might think:

> "I need to find a vulnerability immediately."

A better approach is:

> "First, I need to understand the system."

Ask:

- What operating system is being used?
- Who am I?
- What privileges do I have?
- What applications are installed?
- What processes are running?
- What services are available?
- What files exist?
- Who can access those files?

Only after understanding the environment should you start looking for weaknesses.

> **Reconnaissance and enumeration come before exploitation.**

---

## 🎯 17. Day 2 Practical Exercise

Perform the following exercise on **your own computer or an authorized lab system only**.

### Windows Learners

Run the following commands.

### Command 1 — Identify the Current User

```text
whoami
```

Record the result.

### Command 2 — Collect System Information

```text
systeminfo
```

Find:

- Operating System
- System Type
- Processor
- Installed RAM

### Command 3 — View Running Processes

```text
tasklist
```

Find any five running processes.

Record:

- Process name
- PID

---

### Linux Learners

Run the following commands.

### Command 1 — Identify the Current User

```text
whoami
```

Record the result.

### Command 2 — Identify the Operating System

```text
uname -a
```

Record the operating system information.

### Command 3 — List Files and Directories

```text
ls -la
```

Identify:

- Files
- Directories
- Permission information

### Command 4 — View Running Processes

```text
ps
```

Identify the processes displayed.

---

## 📝 18. Day 2 Assignment

Answer the following questions in your own words.

### Question 1

What is the difference between RAM and Storage?

### Question 2

What is an Operating System?

### Question 3

What is a process?

### Question 4

What is a service?

### Question 5

Why are permissions important?

### Question 6

What is the Principle of Least Privilege?

### Question 7

Why should a security tester understand the operating system before testing it?

### Question 8

What is the difference between a user and an administrator?

### Question 9

Why can unnecessary services increase the attack surface?

### Question 10

Why should we understand a system before trying to exploit it?

---

## 🧠 19. Day 2 Quick Quiz

Try to answer these questions before checking the answers.

### Question 1

Which component executes instructions?

A. RAM

B. CPU

C. Storage

D. Monitor

### Question 2

Which component provides temporary working memory?

A. CPU

B. SSD

C. RAM

D. Keyboard

### Question 3

What manages hardware resources and provides an environment for applications?

A. Browser

B. Operating System

C. Database

D. Firewall

### Question 4

What is a process?

A. A stored file

B. A running program

C. A user account

D. A network cable

### Question 5

Which of the following is a permission?

A. Read

B. Execute

C. Write

D. All of the above

### Question 6

What does the Principle of Least Privilege mean?

A. Give everyone administrator access

B. Give users only the access they need

C. Disable all user accounts

D. Give applications unlimited permissions

### Question 7

Why are services important to a security tester?

A. They can provide functionality that may increase the attack surface

B. They are always vulnerabilities

C. They are only used by hackers

D. They replace the operating system

### Question 8

Which command can show the current user on Windows and Linux?

A. whoami

B. tasklist

C. systeminfo

D. ls

---

## ✅ 20. Quiz Answers

<details>
<summary>Click here to reveal the answers</summary>

1. **B — CPU**

2. **C — RAM**

3. **B — Operating System**

4. **B — A running program**

5. **D — All of the above**

6. **B — Give users only the access they need**

7. **A — They can provide functionality that may increase the attack surface**

8. **A — whoami**

</details>

---

## 🔑 21. Key Takeaways

### CPU

Executes instructions.

### RAM

Provides temporary working memory.

### Storage

Stores information for longer periods.

### Operating System

Manages hardware and provides an environment for applications.

### User

An account used to interact with the system.

### Process

A running program.

### Service

A background program or system component that provides functionality.

### Permission

Defines what a user or process is allowed to do.

### Least Privilege

Give only the access that is required.

### System Enumeration

Collecting and understanding information about a system.

> **Before trying to exploit a system, understand the system.**

---

## 📌 Day 2 Completion Checklist

Before considering Day 2 complete, make sure you can explain:

- [ ] What a computer system is
- [ ] What CPU does
- [ ] What RAM does
- [ ] What storage does
- [ ] What an Operating System does
- [ ] What a user account is
- [ ] What a process is
- [ ] What a service is
- [ ] What permissions are
- [ ] What Least Privilege means
- [ ] Why system enumeration matters
- [ ] Why understanding a system comes before exploitation

---

> **Learn responsibly. Test ethically.**
