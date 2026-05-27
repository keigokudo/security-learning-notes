# VulnHub Potato – Learning Notes

## Overview

This repository contains my learning notes from working through the VulnHub Potato machine.

The goal was not simply to complete the challenge, but to understand the reasoning process behind enumeration, authentication, privilege escalation, and Linux system behavior.

As a software engineer, I am using these exercises to strengthen my understanding of systems, networking, security fundamentals, and attack surfaces relevant to real-world application development and operations.

---

## Learning Objectives

- Understand a structured approach to penetration testing
- Practice Linux enumeration techniques
- Learn common authentication weaknesses
- Understand privilege escalation concepts
- Connect offensive techniques to defensive design principles

---

## Workflow

```text
Information Gathering
        ↓
Service Enumeration
        ↓
Initial Access
        ↓
Credential Discovery
        ↓
User Access
        ↓
Privilege Escalation
        ↓
Root Access
```

---

# Pre-study Topics

## sudoers file

**Purpose**

Defines which users can execute commands using sudo.

**Locations**

```
/etc/sudoers
/etc/sudoers.d/*
```

**Useful command**

```
sudo -l
```

**Things to check**

- NOPASSWD entries
- Commands executable as root
- Unexpected permissions

**Key question**

> Can an allowed command be abused to execute a shell?

---

## Netcat

**Purpose**

A networking utility often called a "Swiss Army knife".

**Common uses**

- Port communication
- Listening for connections
- File transfer
- Shell communication

**Examples**

Connect:

```
nc IP PORT
```

Listen:

```
nc -lvnp PORT
```

**Key idea**

> If quick network communication testing is needed, Netcat is often useful.

---

## Bind Shell vs Reverse Shell

### Bind Shell

Target system opens a listening port.

```text
Target → listening
Attacker → connects
```

### Reverse Shell

Attacker listens.

```text
Attacker → listening
Target → connects back
```

**Key idea**

> Reverse shells are often preferred because they work better through NAT and firewalls.

---

# Enumeration

## Netdiscover

**Purpose**

Discover hosts on a local network.

**Example**

```
sudo netdiscover
```

**Look for**

- IP addresses
- MAC addresses
- Virtual machine environments

---

## Nmap

**Purpose**

Identify open ports and services.

**Examples**

```
nmap -sV -sC TARGET_IP

nmap -A TARGET_IP

nmap -p- TARGET_IP
```

**Look for**

- Open ports
- Service versions
- FTP
- SSH
- HTTP services

**Key question**

> Can this service be accessed or abused?

---

## Gobuster

**Purpose**

Discover hidden files and directories.

**Example**

```
gobuster dir -u http://TARGET_IP -w WORDLIST
```

**Look for**

- /admin
- /backup
- /test
- /index.php.bak

**Key question**

> Did developers accidentally expose sensitive files?

---

# Initial Access

## Anonymous FTP

**Purpose**

Access FTP servers that allow anonymous login.

**Example**

```
ftp TARGET_IP PORT
```

Login:

```
Username: anonymous
```

**Look for**

- Configuration files
- Backups
- Credentials

---

## Directory Traversal

**Purpose**

Access files outside intended directories.

**Example**

```
../../../../../etc/passwd
```

**Key question**

> Can arbitrary files be read?

---

## SSH

**Purpose**

Remote login access.

**Example**

```
ssh username@TARGET_IP
```

Requirements:

- Username
- Password
- SSH key

---

# Authentication and Credential Discovery

## Burp Suite

**Purpose**

Intercept and inspect HTTP traffic.

**Common uses**

- Inspect requests
- Modify parameters
- Repeater testing
- Authentication analysis

**Look for**

- Cookies
- POST values
- Session information

**Key question**

> What happens if values are modified?

---

## John the Ripper

**Purpose**

Password hash cracking.

**Example**

```
john hash.txt --wordlist=rockyou.txt
```

Show results:

```
john --show hash.txt
```

**Key question**

> What type of hash is this?

---

# Privilege Escalation

## sudo -l

**Purpose**

List sudo permissions.

**Example**

```
sudo -l
```

**Most important thing**

```
NOPASSWD
```

**Key question**

> Can this executable be abused?

---

## /bin/nice

**Purpose**

Normally used to modify process priority.

In this challenge:

```text
sudo permission
      ↓
abuse possible
      ↓
shell execution
```

**Method**

Check GTFOBins:

https://gtfobins.github.io

**Key idea**

> If I do not recognize a command, investigate whether it can be abused.

---

# Main Lessons Learned

1. Enumeration comes first

   ```
   nmap
   gobuster
   ftp
   ```

2. Search for credentials

   ```
   config files
   backups
   passwords
   hashes
   ```

3. Gain access

   ```
   ssh
   ```

4. Check permissions

   ```
   sudo -l
   ```

5. Verify executable abuse opportunities

   ```
   GTFOBins
   ```

---

# Real-world Relevance

This exercise reinforced several security principles relevant to production systems:

- Follow least privilege principles
- Avoid unnecessary sudo permissions
- Do not expose backup files
- Secure authentication mechanisms
- Restrict sensitive information disclosure
- Security issues are often caused by misconfiguration rather than sophisticated exploits

---

**Educational use only**

This repository is intended for educational use within intentionally vulnerable environments such as VulnHub.
